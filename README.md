# Meesterproef

## Bug registratie
Samen met [Vienna](github.com/viennam) heb ik in opdracht van het Van Gogh Museum een bugregistratiesysteem gemaakt. Dit systeem bestaat uit twee onderdelen, een "voorkant" waar de baliemedewerkers bug in kunnen voeren, en een "dashboard" waar de verzamelde data inzichtelijk wordt gemaakt.

## Code
In overleg hebben we besloten de code in een private repository te bewaren. Wij kunnen docenten als collaborators toevoegen.

## Persoonlijk aandeel


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
