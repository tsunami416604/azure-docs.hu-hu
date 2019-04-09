---
title: 'Oktatóanyag: Fordítás, szintetizálásához és elemzés – Translator Text API egy Flask-alkalmazás létrehozása'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Flask-alapú webalkalmazás, amely az Azure Cognitive Services használatával szöveg lefordítása, vélemények elemzése, és szintetizálásához lefordított szöveget beszéddé fog létrehozni. A fókusz a Python-kód és a Flask-útvonalakat, amelyek lehetővé teszik az alkalmazás be van kapcsolva. A Microsoft nem sok időt, amely az alkalmazás szabályozza a Javascript, de a megvizsgálhatja, hogy az összes fájlt biztosít.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 69e6797e91fc645e3bd3e3b300cea6852a662214
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007387"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Oktatóanyag: Készíthet olyan Flask-alkalmazás az Azure Cognitive Services szolgáltatással

Ebben az oktatóanyagban egy Flask-webalkalmazás, amely az Azure Cognitive Services használatával szöveg lefordítása, vélemények elemzése, és szintetizálásához lefordított szöveget beszéddé fog létrehozni. A fókusz a Python-kód és a Flask-útvonalakat, amelyek lehetővé teszik az alkalmazás, azonban segítünk Önnek a HTML és Javascript, amelyek együttesen kéri le az alkalmazást. Ha problémákat tapasztal tudassa velünk, hogy az alábbi visszajelzés gomb használatával.

Itt van ez az oktatóanyag bemutatja:

> [!div class="checklist"]
> * Az Azure-előfizetési kulcsok beolvasása
> * A fejlesztési környezet beállítása és a függőségek telepítése
> * Flask-alkalmazás létrehozása
> * Szöveg lefordítása a Translator Text API használatával
> * Bemeneti szövegek és a fordítások pozitív vagy negatív vélemények elemzése, Szövegelemzés használata
> * Beszédszolgáltatások használata a lefordított szöveg átalakítása szintetizált
> * A Flask-alkalmazás helyileg történő futtatása

> [!TIP]
> Ha szeretne, ugorjon előre, és minden a kód egyszerre, a teljes minta együtt build utasításokat is [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Mit jelent a Flask?

Flask webes alkalmazások létrehozásához egy microframework. Ez azt jelenti, hogy Flask biztosítja az eszközöket, könyvtárakat és technológiákat, amelyek lehetővé teszik, hogy egy webalkalmazást hozhat létre. Ez a webes alkalmazás egyes weboldalak, blog, wiki vagy egy naptár webes alkalmazás vagy egy kereskedelmi webhelyen például érdemi go lehet.

Azoknak, akik mélyebb szintű megismerése után ez az oktatóanyag Íme néhány hasznos hivatkozásokat:

* [Flask-dokumentáció](http://flask.pocoo.org/)
* [Flask - próbabábuk a Flask útmutató kezdőknek](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Előfeltételek

Tekintse át a szoftver- és előfizetés kulcsokat, amely ebben az oktatóanyagban szüksége lesz.

* [Python 3.5.2-es verzióját vagy újabb](https://www.python.org/downloads/)
* [Git-eszközök](https://git-scm.com/downloads)
* Egy IDE-szerkesztőben vagy szövegszerkesztőben, például [Visual Studio Code](https://code.visualstudio.com/) vagy [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) vagy [Firefox](https://www.mozilla.org/firefox)
* A **Szövegfordító** előfizetési kulcs (vegye figyelembe, hogy nem szükséges, válasszon ki egy régiót.)
* A **Szövegelemzés** az előfizetési kulcs a **USA nyugati RÉGIÓJA** régióban.
* A **beszédszolgáltatások** az előfizetési kulcs a **USA nyugati RÉGIÓJA** régióban.

## <a name="create-an-account-and-subscribe-to-resources"></a>Hozzon létre egy fiókot, és feliratkozhat a erőforrások

Ahogy korábban említettük fog ebben az oktatóanyagban három előfizetési kulcs szükséges. Ez azt jelenti, hogy szeretne-e létrehozni az Azure-fiókjában található erőforráshoz:
* Fordítói szöveg
* Szövegelemzés
* Beszédszolgáltatások

Használat [egy Cognitive Services-fiók létrehozása az Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) az erőforrások létrehozásának részletes útmutatóját.

> [!IMPORTANT]
> A jelen oktatóanyag esetében hozzon létre az erőforrások az USA nyugati régiójában. Ha használ egy másik régióban, szüksége lesz a kiindulási URL-cím a Python-fájlok minden módosítása.

## <a name="set-up-your-dev-environment"></a>A fejlesztési környezet beállítása

A Flask-webalkalmazás fejlesztése, előtt szüksége lesz egy működő könyvtárba, a projekt létrehozásához és néhány Python-csomagok telepítéséhez.

### <a name="create-a-working-directory"></a>Hozzon létre egy működő könyvtárba

1. Nyissa meg a parancssort (Windows) vagy a Terminálszolgáltatások (macOS/Linux). Ezután hozzon létre egy működő könyvtárba, és alkönyvtárai a projekthez:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Módosítsa a projekt munkakönyvtár:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Hozzon létre, és aktiválja a virtuális környezetet `virtualenv`

Hozzon létre egy virtuális környezethez, a Flask alkalmazást a `virtualenv`. Virtuális környezet használatával biztosítja, hogy bárhonnan tiszta környezettel.

1. Futtassa ezt a parancsot a virtuális környezet létrehozása a munkakönyvtárban: **macOS és Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Azt már explicit módon deklarálni, hogy a virtuális környezet a Python 3 kell használnia. Ez biztosítja, hogy több Python telepítése a felhasználók miként használják a megfelelő verziót.

   **Windows CMD / Bash-Windows:**
   ```
   virtualenv venv
   ```
   Egyszerűség, hogy a virtuális környezet venv van elnevezési.

2. A platform, és felület függően változhat a parancsokat a virtuális környezet aktiválása:   

   | Platform | Shell | Parancs |
   |----------|-------|---------|
   | macOS/Linux | a bash/zsh | `source venv/bin/activate` |
   | Windows | A bash | `source venv/Scripts/activate` |
   | | Parancssor | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   Ez a parancs futtatása után a parancssor vagy a terminál-munkamenetben kell tartalmazni a `venv`.

3. Ez a parancssor vagy a Terminálszolgáltatások beírásával is bármikor a munkamenet inaktiválása: `deactivate`.

> [!NOTE]
> Python hozhat létre és kezelhet virtuális környezetek széles körű dokumentációval, akkor tekintse meg [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Telepítse a kérelmek

Kérelmek egy népszerű modul, amely a HTTP 1.1-es kérelmek küldésére szolgál. Hiba esetén nem kell manuálisan adja hozzá a lekérdezési karakterláncok a URL-címekhez, vagy a POST data űrlap kódolása.

1. Kérelmek telepítéséhez futtassa:

   ```
   pip install requests
   ```

> [!NOTE]
> Ha szeretne további információ a kérelmeket, lásd: [kérelmeket: HTTP-t az emberek](http://docs.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Telepítse és konfigurálja a Flask

A következő Flask telepíteni kell. Flask végzi el a WebApp útválasztását, és lehetővé teszi számunkra, amelyek az előfizetési kulcsok a végfelhasználó a kiszolgálók közötti hívásokat.

1. Flask telepítéséhez futtassa:
   ```
   pip install Flask
   ```
   Most ellenőrizze Flask lett telepítve. Futtassa:
   ```
   flask --version
   ```
   A verzió kell-e nyomtatni a terminálon. Bármi más azt jelenti, hogy probléma merült fel.

2. A Flask alkalmazást futtatni, választhatja a flask vagy a Python -m kapcsolót használja a flask segítségével. Mielőtt úgy teheti meg, hogy meg kell adnia a terminálban melyik alkalmazás együttműködik a exportálásával a `FLASK_APP` környezeti változót:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>A Flask-alkalmazás létrehozása

Ebben a szakaszban fog által visszaadott HTML-fájl, amikor a felhasználók, nyomja le az alkalmazás gyökérkönyvtárában lecsupaszított Flask alkalmazás létrehozása. Nem túl sok időt próbál válasszon egymástól a kódot, visszatérünk később frissíteni ezt a fájlt.

### <a name="what-is-a-flask-route"></a>Mi az a Flask útvonalat?

Szánjunk néhány percet beszélni "[útvonalak](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Útválasztás URL-cím kötést létrehozni egy adott funkciót szolgál. Flask útvonal decorator adott URL-címek függvények regisztrálásához használja. Például amikor egy felhasználó megnyitja a legfelső szintű (`/`), a web app `index.html` jelenik meg.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Vessünk egy pillantást a kezdőlap kalapácsos egy további példa.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Ez a kód biztosítja, hogy amikor a felhasználók a `http://your-web-app.com/about` , amely a `about.html` fájl jelenik meg.

Bár ezek a minták bemutatják, hogyan jelennek meg a html-lapok egy felhasználó, útvonalak is használható az API-k hívása egy gomb megnyomásakor, vagy tetszőleges számú műveletet a kezdőlap elhagyni nélkül. Láthatja a működés fordítás, hangulatát és beszédszintézishez útvonalai létrehozásakor.

### <a name="get-started"></a>Bevezetés

1. Az IDE-ben nyissa meg a projektet, majd hozzon létre egy fájlt `app.py` a munkakönyvtárban gyökerében. Ezután másolja a kódot `app.py` , és mentse:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   A kódblokk arra utasítja az alkalmazás megjelenítendő `index.html` minden alkalommal, amikor egy felhasználó megnyitja a webalkalmazás gyökérmappájában (`/`).

2. Következő lépésként hozzon létre az előtérbeli webes alkalmazás. Hozzon létre egy fájlt `index.html` a a `templates` könyvtár. Ezután másolja a kódot `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Most tesztelheti a Flask-alkalmazás. A terminálból futtassa:

   ```
   flask run
   ```

4. Nyisson meg egy böngészőt, és keresse meg a megadott URL-cím. Az egyoldalas alkalmazást kell megjelennie. Nyomja meg **Ctrl + c** az alkalmazás leállítása.

## <a name="translate-text"></a>Szöveg lefordítása

Most, hogy van egy ötlete egy egyszerű Flask-alkalmazás működését, tekintsük:

* A Translator Text API meghívása, és adott vissza választ bizonyos Python írása
* A Python-kód meghívásához Flask útvonal létrehozása
* Frissítse a HTML szövegbevitel és a egy nyelvválasztó fordítását egy adott területre, és lefordítja a gomb
* A Javascript, amely lehetővé teszi a felhasználók számára a HTML-ből a Flask-alkalmazás telepítésébe való írása

### <a name="call-the-translator-text-api"></a>A Translator Text API meghívása

Először is szüksége, írjon egy függvényt a Translator Text API meghívásához. Ez a függvény két argumentumot vesz igénybe: `text_input` és `language_output`. Ez a függvény nevezzük, amikor a felhasználó megnyomja a fordítás gombra az alkalmazásban. A terület a HTML-ként legyen elküldve, a `text_input`, és a nyelv kiválasztása érték a HTML-ként legyen elküldve, `language_output`.

1. Először hozzon létre egy fájlt nevű `translate.py` a munkakönyvtárban gyökerében.
2. Ezután adja hozzá a kódot `translate.py`. Ez a függvény két argumentumot: `text_input` és `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'

   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Adja hozzá a Translator Text előfizetési kulcsot, és mentse.

### <a name="add-a-route-to-apppy"></a>Az útvonal hozzáadása `app.py`

Ezután szüksége lesz egy útvonal létrehozása a Flask-alkalmazást, amely meghívja a `translate.py`. Ez az útvonal minden alkalommal, amikor a felhasználó megnyomja a fordítás gombra az alkalmazás neve.

Az alkalmazás fogadja el az útvonal fog `POST` kérelmeket. Ennek oka az, a függvény azt várja, a fordítandó szöveg és a egy kimeneti a fordítás nyelvét.

Flask segítségével elemezheti és kezelheti az egyes kérések segédfüggvények biztosít. A megadott kód `get_json()` adja vissza az adatokat a `POST` kérés JSON-fájlként. Majd `data['text']` és `data['to']`, a szöveg- és kimeneti nyelvi érték lett átadva a `get_translation()` elérhető függvény `translate.py`. Az utolsó lépése a válaszban visszaadandó JSON-t, mivel ezek az adatok megjelenítése a webalkalmazásban kell.

A következő szakaszokban lesz ismételje ezt az eljárást, amikor vélemények elemzése és a speech összefoglaló útvonalakat hoz létre.

1. Nyissa meg `app.py` , és keresse meg az importálási utasítást tetején `app.py` , és adja hozzá a következő sort:

   ```python
   import translate
   ```
   Mostantól a Flask-alkalmazást is a webhelyen keresztül elérhető metódus `translate.py`.

2. Másolja ezt a kódot, végén `app.py` , és mentse:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>frissítés `index.html`

Most, hogy a szöveget, és a egy útvonalat a Flask-alkalmazást hívja, a függvény, a következő lépés az HTML-jét az alkalmazás használatának megkezdése. A HTML-kódot az alábbi néhány dolgot:

* Itt egy szövegterület, ahol a felhasználók is bemeneti a lefordítandó szöveg.
* A nyelvválasztó tartalmazza.
* Tartalmazza a felismert nyelv és a fordítás során visszaadott megbízhatósági pontszámok megjelenítéséhez a HTML-elemek.
* Itt egy csak olvasható text területre, ahol a fordítási kimenet jelenik meg.
* Magában foglalja a helyőrzők vélemények elemzése és a speech összefoglaló kódot fog hozzáadni kívánt ezt a fájlt az oktatóanyag későbbi részében.

Frissítsük `index.html`.

1. Nyissa meg `index.html` , és keresse meg a hozzászólások követésének kódot:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Cserélje le a kód megjegyzéseket a HTML-blokkban:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

A következő lépés az egyes Javascript írása. Ez az a híd között a HTML- és a Flask-útvonal.

### <a name="create-mainjs"></a>Hozzon létre `main.js`  

A `main.js` fájl a híd a HTML- és a Flask útvonal között. Az alkalmazás fogja használni a jQuery Ajax és XMLHttpRequest kombinációját jelennek meg a tartalmat, és győződjön meg arról, hogy `POST` a Flask útvonalak kérelmeket.

Az alábbi kódot, a tartalom a HTML-ből a Flask-útvonal irányuló kérelem létrehozására használható. Pontosabban, a szöveg és a nyelvválasztó tartalmát rendelt változók, és a kérelemben továbbít `translate-text`.

Ezután a kód végighalad a választ, és frissíti a HTML-kódot a fordítást, felismert nyelv és megbízhatósági pontszám.

1. Ide használatával, hozzon létre egy fájlt `main.js` a a `static/scripts` könyvtár.
2. Másolja be ezt a kódot `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Fordítási tesztelése

Most tesztelje fordítási az alkalmazásban.

```
flask run
```

Keresse meg a megadott kiszolgáló címét. Szöveg típusú bemeneti terület, válasszon ki egy nyelvet, és nyomja le a fordítja le. Egy fordítási szerezheti be. Ha nem működik, ellenőrizze, hogy hozzáadta az előfizetési kulcs.

> [!TIP]
> Ha a módosítások nem jelenik meg, vagy az alkalmazás nem működik a módon a várt, próbálja meg a gyorsítótár kiürítése, vagy privát/inkognitó ablak megnyitása.

Nyomja meg **CTRL + c** kill az alkalmazást, majd lépjen a következő szakaszban.

## <a name="analyze-sentiment"></a>Vélemények elemzése

A [Text Analytics API](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) segítségével hajtsa végre a hangulatelemzés, kinyerheti a kulcskifejezéseket szövegből vagy a Forrásnyelv felismerése. Ez az alkalmazás fogunk hangulatelemzés használatával határozza meg, ha a megadott szöveg pozitív, semleges vagy negatív. Az API 0 és 1 közötti numerikus pontszámokat jelenít meg. Az 1-hez közeli pontszámok pozitív véleményt jeleznek, míg a 0-hoz közeliek negatívat.

Ebben a szakaszban fog ehhez néhány dolgot:

* Hangulatelemzés végez, és a egy választ adja vissza a szövegelemzési API meghívására néhány Python írása
* A Python-kód meghívásához Flask útvonal létrehozása
* Frissítse a HTML-kódot egy adott területre a véleménypontszámokat és a egy gombot, amellyel az elemzéseket végezhet
* A Javascript, amely lehetővé teszi a felhasználók számára a HTML-ből a Flask-alkalmazás telepítésébe való írása

### <a name="call-the-text-analytics-api"></a>Szövegelemzési API hívása

Most írjon egy függvényt a szövegelemzési API meghívására. Ez a függvény vesz igénybe a négy argumentum: `input_text`, `input_language`, `output_text`, és `output_language`. Ez a függvény nevezzük, amikor a felhasználó megnyomja a futtatási sentiment analysis gombra az alkalmazásban. Az a szöveg terület és nyelvi választó, valamint az észlelt nyelvi és a fordítás kimeneti a felhasználó által megadott adatok minden kérelmet kapott. A válasz objektum tartalmazza a forrás és a fordítás véleménypontszámokat. A következő szakaszban fog írni, a válasz elemzéséhez, és használhatja az alkalmazás Javascript. Egyelőre koncentráljunk a hívást a szövegelemzési API-t.

1. Hozzon létre egy fájlt nevű `sentiment.py` a munkakönyvtárban gyökerében.
2. Ezután adja hozzá a kódot `sentiment.py`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Adja hozzá a Szövegelemzés előfizetéshez kulcsot, és mentse.

### <a name="add-a-route-to-apppy"></a>Az útvonal hozzáadása `app.py`

Hozzunk létre egy útvonalat a Flask-alkalmazást, amely meghívja `sentiment.py`. Ez az útvonal minden alkalommal, amikor a felhasználó megnyomja a futtatási sentiment analysis gombra az alkalmazás neve. Például az útvonal a fordítás, fogadja el ezt az útvonalat fogja `POST` kér, mivel a függvény argumentumot vár.

1. Nyissa meg `app.py` , és keresse meg az importálási utasítást tetején `app.py` , és frissítheti:

   ```python
   import translate, sentiment
   ```
   Mostantól a Flask-alkalmazást is a webhelyen keresztül elérhető metódus `sentiment.py`.

2. Másolja ezt a kódot, végén `app.py` , és mentse:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>frissítés `index.html`

Most, hogy egy függvényt, hangulatelemzés és a egy útvonal hívja, a Flask-alkalmazás futtatását, a következő lépés az írása a HTML-jét az alkalmazás elindításához. A HTML-kódot az alábbi néhány dolgot:

* Hangulatelemzés futtatása az alkalmazás ad hozzá egy gombot
* Egy elem, amely bemutatja, hangulatát pontozási hozzáadása
* Hozzáadja a véleménypontszámokat megjeleníthető elem

1. Nyissa meg `index.html` , és keresse meg a hozzászólások követésének kódot:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Cserélje le a kód megjegyzéseket a HTML-blokkban:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>frissítés `main.js`

Az alábbi kódot, a tartalom a HTML-ből a Flask-útvonal irányuló kérelem létrehozására használható. Pontosabban, a szöveg és a nyelvválasztó tartalmát rendelt változók, és a kérelemben továbbít a `sentiment-analysis` útvonalat.

Ezután a kód végighalad a választ, és frissíti a HTML-kódot a véleménypontszámokat.

1. Ide használatával, hozzon létre egy fájlt `main.js` a a `static` könyvtár.

2. Másolja be ezt a kódot `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Hangulatelemzés tesztelése

Most tesztelje hangulatelemzés az alkalmazásban.

```
flask run
```

Keresse meg a megadott kiszolgáló címét. Szöveg típusú bemeneti terület, válasszon ki egy nyelvet, és nyomja le a fordítja le. Egy fordítási szerezheti be. Ezután kattintson a Futtatás sentiment analysis gombra. Két pontszámok kell megjelennie. Ha nem működik, ellenőrizze, hogy hozzáadta az előfizetési kulcs.

> [!TIP]
> Ha a módosítások nem jelenik meg, vagy az alkalmazás nem működik a módon a várt, próbálja meg a gyorsítótár kiürítése, vagy privát/inkognitó ablak megnyitása.

Nyomja meg **CTRL + c** kill az alkalmazást, majd lépjen a következő szakaszban.

## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszéddé

A [szöveg-hang transzformációs API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) lehetővé teszi, hogy az alkalmazást, hogy a szöveg átalakítása beszéddé természetes emberszerű szintetizált. A szolgáltatás támogatja a standard szintű, Neurális és egyéni beszédhangot. A mintaalkalmazás az elérhető hangok néhány teljes listáját, tekintse meg [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

Ebben a szakaszban fog ehhez néhány dolgot:

* Írási néhány Python átalakítandó szöveg-hang transzformációs szöveg-hang transzformációs API-val
* A Python-kód meghívásához Flask útvonal létrehozása
* Az átalakítandó szöveg-hang transzformációs, és a egy elem számára audiotartalmak lejátszás gomb a HTML-kód frissítése
* A Javascript, amely lehetővé teszi a felhasználók számára a Flask-alkalmazás telepítésébe való írása

### <a name="call-the-text-to-speech-api"></a>Az szöveg-hang transzformációs API meghívása

Ideje lefuttatni egy függvény használatával az szöveg-hang transzformációs. Ez a függvény két argumentumot vesz igénybe: `input_text` és `voice_font`. Ez a függvény nevezzük, amikor a felhasználó megnyomja a konvertálás szöveg-hang transzformációs gombra az alkalmazásban. `input_text` a lefordítandó szöveg-, a hívás által visszaadott fordítási kimeneti `voice_font` az értéket a hangalapú betűtípus választó a HTML-formátumban van.

1. Hozzon létre egy fájlt nevű `synthesize.py` a munkakönyvtárban gyökerében.

2. Ezután adja hozzá a kódot `synthesize.py`.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Adja hozzá az beszédszolgáltatások előfizetési kulcs, és mentse.

### <a name="add-a-route-to-apppy"></a>Az útvonal hozzáadása `app.py`

Hozzunk létre egy útvonalat a Flask-alkalmazást, amely meghívja `synthesize.py`. Ez az útvonal minden alkalommal, amikor a felhasználó megnyomja a konvertálás szöveg-hang transzformációs gombra az alkalmazás neve. Például a fordítás és hangulatelemzés útvonalait, fogadja el ezt az útvonalat fogja `POST` kér, mivel a függvény két argumentumot vár: szintetizálásához szöveg és a hangtípusú lejátszás céljából.

1. Nyissa meg `app.py` , és keresse meg az importálási utasítást tetején `app.py` , és frissítheti:

   ```python
   import translate, sentiment, synthesize
   ```
   Mostantól a Flask-alkalmazást is a webhelyen keresztül elérhető metódus `synthesize.py`.

2. Másolja ezt a kódot, végén `app.py` , és mentse:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>frissítés `index.html`

Most, hogy egy függvény használatával az szöveg-beszéd átalakítás, és a egy útvonalat a Flask-alkalmazást hívja, a, a következő lépés az írása a HTML-jét az alkalmazás elindításához. A HTML-kódot az alábbi néhány dolgot:

* Számos hang legördülő menü
* Ad hozzá egy gombot, amellyel a szöveg-beszéd átalakítás
* Hozzáad egy hang elemet, amely a szintetizált lejátszására szolgál

1. Nyissa meg `index.html` , és keresse meg a hozzászólások követésének kódot:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Cserélje le a kód megjegyzéseket a HTML-blokkban:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Ezután keresse meg a hozzászólások követésének kódot:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Cserélje le a kód megjegyzéseket a HTML-blokkban:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Győződjön meg arról, hogy mentse a munkáját.

### <a name="update-mainjs"></a>frissítés `main.js`

Az alábbi kódot, a tartalom a HTML-ből a Flask-útvonal irányuló kérelem létrehozására használható. Pontosabban, a fordítás a hangtípusú rendelt változók és a kérelemben továbbít a `text-to-speech` útvonalat.

Ezután a kód végighalad a választ, és frissíti a HTML-kódot a véleménypontszámokat.

1. Ide használatával, hozzon létre egy fájlt `main.js` a a `static` könyvtár.
2. Másolja be ezt a kódot `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Már majdnem kész. A legutolsó dolog, meg fogjuk végrehajtani néhány kódot a `main.js` egy fordítási kiválasztott nyelven alapuló hangtípusú ki automatikusan. Adja hozzá a kódblokkot, amellyel `main.js`:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Az alkalmazás tesztelése

Most tesztelheti a beszédszintézishez az alkalmazásban.

```
flask run
```

Keresse meg a megadott kiszolgáló címét. Szöveg típusú bemeneti terület, válasszon ki egy nyelvet, és nyomja le a fordítja le. Egy fordítási szerezheti be. Ezután válassza egy hang, majd nyomja meg a szöveg-hang transzformációs convert gombot. a fordítás lejátszásának szintetizált szerint. Ha nem működik, ellenőrizze, hogy hozzáadta az előfizetési kulcs.

> [!TIP]
> Ha a módosítások nem jelenik meg, vagy az alkalmazás nem működik a módon a várt, próbálja meg a gyorsítótár kiürítése, vagy privát/inkognitó ablak megnyitása.

Ennyi az egész, rendelkezik egy működő alkalmazást, amely végrehajtja a fordítások, hangulatát és szintetizált elemzi. Nyomja meg **CTRL + c** az alkalmazás leállítása. Ügyeljen arra, hogy a többi [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>A forrás-kód kérése

A projekt forráskódja elérhető a [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>További lépések

* [A Translator Text API referenciája](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [A Text Analytics API referenciája](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Szöveget beszéddé átalakító API-referencia](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
