# Meesterproef

## Bug registratie
Samen met [Vienna](github.com/viennam) heb ik in opdracht van het Van Gogh Museum een bugregistratiesysteem gemaakt. Dit systeem bestaat uit twee onderdelen, een "voorkant" waar de baliemedewerkers bug in kunnen voeren, en een "dashboard" waar de verzamelde data inzichtelijk wordt gemaakt.

## Code
In overleg hebben we besloten de code in een private repository te bewaren. Wij kunnen docenten als collaborators toevoegen.

## Proces
Ik heb mijn proces bijgehouden in mijn procesboek. Tot en met week 3 hebben Vienna en ik dagelijks opgeschreven wat we gedaan hebben. Dit logboek is hier te vinden: [logboek](https://docs.google.com/document/d/1BLkQk_bOpa17CeITQ_SNrY3ZHIs7yYbW8BiBdiQcFkE/edit?usp=sharing)

## Leerdoelen
### Mongodb
Ik had als leerdoel om te leren werken met MongoDB. Dit is in zoverre gelukt dat wij al onze data in MongoDB opslaan. Ik heb geleerd hoe ik data op kan halen en kan opslaan/updaten. Er is nog veel te leren maar ik begrijp het in de basis en kan comfortable verder leren. Ik heb ook geleerd hoe je met mLab makkelijk een MongoDB database kan hosten. Op deze manier kan ik makkelijk een node project met database deployen via bijvoorbeeld heroku.

Voorbeeld MongoDB query:

```
collection.replaceOne(query, obj, {upsert: true}, (err, res) => {
  resolve(res)
  if (err) throw err
})
```

### APIs
Mijn tweede leerdoel was om te leren hoe je zelf APIs maakt met Node Express. Uiteindelijk heb ik meer dan 10 verschillende APIs gemaakt die we allemaal gebruiken. Ik heb geëxperimenteerd met het maken van flexibele APIs die voor verschillende doelen gebruikt kunnen worden. 

Het volgende snippet is een API die het aantal results telt in de Collection uit de query. De key en value worden gebruikt voor het maken van een mongoDB query.

```
function getCount(coll, key, value) {
  let query
  if (key) {
    query = `{ "${key}": "${value}" }`
    query = JSON.parse(query)
  } else {
    query = {}
  }
  return new Promise((resolve, reject) => {
    MongoClient.connect(
      MONGO_URL,
      (err, client) => {
        // Client returned
        let db = client.db("TEST")
        let collection = db.collection(coll)
        collection.find(query).count((err, result) => {
          resolve(result)
        })
      }
    )
  })
}
app.get("/api/count/", (req, res) => {
  getCount(req.query.coll, req.query.key, req.query.value).then((result) => {
    res.json(result)
  })
})
```
### NPM scripts
Voor dit project heb ik mijn oude Gulp boilerplate vervangen met NPM scripts. De build die ik nu heb werkt goed en ik heb het gevoel dat ik beter begrijp hoe de packages die ik gebruikte achter de schermen werken. Het script dat ik heb gemaakt ziet er zo uit:

```
"scripts": {
    "start": "npm run prod",
    "prod": "npm run prebuild && npm run prejs && npm run jsProd & npm run cssProd && node server.js",
    "prebuild": "rm -rf static/* && mkdir -p static",
    "build": "npm run sync & npm run js & npm run css & npm run nodemon",
    "sync": "browser-sync start --proxy 'localhost:8001' --port 7777 --files 'static' 'views' --no-open",
    "prejs": "cd static && mkdir -p js",
    "js": "watchify src/scripts/main.js -o static/js/bundle.js -t [ babelify --presets [ env ] ]",
    "jsProd": "browserify src/scripts/main.js -o static/js/bundle.js -t [ babelify --presets [ env ] ]",
    "css": "postcss src/styles/main.css -o static/css/main.css -w",
    "cssProd": "postcss src/styles/main.css -o static/css/main.css",
    "nodemon": "nodemon --inspect server.js --ignore src/ --ignore static/"
  }
```

### Object literal
Mijn laatste leerdoel was het Object Literal Pattern in Javascript. Ik heb dit al eerder gebruikt maar ik wilde hier graag meer mee oefenen. We hebben het pattern bij het hele project toegepast bij de frontend code. Ik krijg steeds meer grip op hoe het werkt, hoewel ik soms nog niet lang genoeg stil sta bij het bedenken van een goede structuur.

```
notification = {
  show: function () {
    this.elements.wrapper.classList.remove("hidden")
    this.elements.wrapper.classList.remove("invisible")
    this.animate(true)
  },
  hide: function () {
    this.elements.wrapper.classList.add("hidden")
  },
  invisible: function() {
    this.elements.wrapper.classList.add("invisible")
    this.elements.wrapper.classList.remove("slideIn")
  }
}
```
## Persoonlijk aandeel

### Tooling
Omdat mijn leerdoel NPM scripts was, heb ik de tooling van dit project gedaan. Het uiteindelijk script staat hierboven. Ik heb hierover ook een artikel geschreven onder het mom van weekly nerd. Zie [Transition to npm scripts](https://codepen.io/hackshackshacks/post/transition-to-npm-scripts).

### CSS
Ik heb het grootste deel van de CSS voor dit project geschreven. Deze taak heb ik van te voren niet per se op me genomen maar zo is het gelopen. De CSS heb ik voor het eerst m.b.v PostCSS geschreven. PostCSS biedt de mogelijkheid om moderne CSS te gebruiken. De code wordt omgeschreven tot CSS die voor alle browsers leesbaar is. Met behulp van een postCSS config bestand heb ik voor mij bekende "Sass" functionaliteit toegevoegd.

Bij het schrijven van CSS maak ik ook gebruik van de BEM methode. Zie [BEM](http://getbem.com/).

Omdat de browser-stack van dit project erg modern was heb ik besloten om zoveel mogelijk [CSS-grid](https://css-tricks.com/snippets/css/complete-guide-grid/) te gebruiken. Zie ook [mijn artikel over grid](https://codepen.io/hackshackshacks/post/everything-grid).

Voor de leuk hebben we in het design en in de CSS alleen CSS colornames gebruikt voor kleuren. De uitdaging was om te kijken of we een mooi ontwerp konden maken met alleen deze kleuren.

```
  --c-primary: orange;
  --c-secondary: SteelBlue;

  --c-text: darkslategrey;
  --c-text-light: lightsteelblue;

  --c-background: aliceblue;
  --c-offset: white;

  --c-quickfix: dodgerblue;
  --c-reset: orange;
  --c-defect: red;

  --v-valid: limegreen;
  --v-invalid: red;
```

### APIs en Database
Ik heb het op me genomen om een verbinding met de database te maken en de data op te halen. In heb Node Express APIs gemaakt om deze data aan de frontend te serveren. We doen calls naar deze APIs vanuit de frontend. Dit zijn de queries de ik geschreven heb:

```
queries: {
    all: "/api/bugs",
    recent: "/api/bugs/recent",
    lastFive: (device) => { return `/api/reports/device/?device=${device}&limit=5`},
    devices: (device) => { return `/api/devices/?device=${device}`},
    total: "/api/total",
    create: (details) => { return `/api/bugs/create/?category=${details.category}&title=${details.title}&description=${details.description}&staff=${details.staff}&deviceRelevant=${details.deviceRelevant}`},
    reports: (page) => {return `/api/reports/?page=${page}`},
    topReports: (amount) => {return `/api/reports/top/${amount}`},
    topDevices: (amount) => {return `/api/devices/top/${amount}`},
    setReport: (title, device, desk) => {return `/api/reports/create/?title=${title}&device=${device}&desk=${desk}`},
    deleteReport: (id) => {return `/api/reports/delete/${id}`},
    addSales: (details) => {return `/api/sales/add/?desk=${details.desk}&amount=${details.amount}&date=${details.date}`},
    getSales: (startDate, endDate) => { return `/api/sales/?startDate=${startDate}&endDate=${endDate}`},
    getCount: (coll, key, value) => { 
      if (key) {
        return `/api/count/?coll=${coll}&key=${key}&value=${value}`
      } else {
        return `/api/count/?coll=${coll}`
      }
    },
    reportCount: (startDate, endDate) => { return `/api/reports/count/?startDate=${startDate}&endDate=${endDate}`},
    deviceCount: (startDate, endDate) => { return `/api/devices/count/?startDate=${startDate}&endDate=${endDate}`}
  }
  ```

### Momentjs
Omdat we op veel plekken in de interface "meaningful" datums nodig hadden heb ik de momentjs library toegepast. Op deze manier kunnen we in plaats van bijvoorbeeld "29-06-2018" "Vandaag" laten zien.

Zie bijvoorbeeld deze helper functie:

```
isToday: function (date) {
    if (moment(date).format("MMM DD") === moment().format("MMM DD")) {
      return true
    } else {
      return false
    }
  }
```

## Vakken
Voor dit project heb ik uiteindelijk minder vakken toe kunnen passen dan ik had gewild. Real time data ben ik helaas niet meer aan toe gekomen.

### CSS to the rescue
Bij het schrijven van de CSS heb ik zoveel mogelijk moderne technieken toegepast. Zo is het grootste deel van de layout met behulp van `display: grid` gedaan.

### Web App From Scratch
Bij Web App From Scratch hebben we gewerkt met APIs en het Object literal Pattern. Deze heb ik bij dit project veel toegepast en ook veel ervaring mee opgedaan.

### Web Design
We hebben bij het maken van het design veel stilgestaan bij principes die we geleerd hebben bij Web Design. Omdat het bij de interface voor de baliemedewerkers zo belangrijk is dat het proces snel is, moest de focus en hierarchy "on point" zijn. Dit is volgens mij goed gelukt en dat blijkt ook uit de testresultaten.

### Performance matters
Om de performance van onze app te verbeteren doen we veel berekeningen "server side". Er zitten in ons project een aantal hele zware processen die erg lang zouden duren als het "client side" zou moeten. Het voorbeeld hieronder doet een call voor alle Registraties en sorteert maakt vervolgens een Array van bugs op volgorde van hoe vaak die voorkomen.

```
getAllData("reports").then((result) => {
    let bugs = groupBugs(result, "bug_title")
    let sorted = []
    for (var bug in bugs) {
      sorted.push({title: bug, count: bugs[bug].length})
    }
    let sortedCount = sorted.length
    sorted.sort(function(a, b) {
      return a.count - b.count
    })
```


