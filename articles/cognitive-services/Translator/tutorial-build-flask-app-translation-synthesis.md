---
title: 'Oktatóanyag: Flask alkalmazás létrehozása a szöveg lefordításához, szintetizálásához és elemzéséhez - Translator Text API'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy Flask-alapú webalkalmazást hozhat létre a szöveg lefordításához, a hangulat elemzéséhez és a lefordított szöveg beszédmé történő szintetizálásához.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: b41b68725b6747cbada13a9acc321724b3f89d67
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118575"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Oktatóanyag: Flask alkalmazás létrehozása az Azure Cognitive Services szolgáltatással

Ebben az oktatóanyagban egy Flask webalkalmazást hozhat létre, amely az Azure Cognitive Services segítségével lefordítja a szöveget, elemzi a véleményeket, és beszédté szintetizálja a lefordított szöveget. A hangsúly a Python-kód és a Flask útvonalak, amelyek lehetővé teszik a kérelmet, azonban segítünk ki a HTML és Javascript, hogy húzza az alkalmazást együtt. Ha bármilyen problémába ütközik, tudassa velünk az alábbi visszajelzés gombot.

Itt van, amit ez a bemutató kiterjed:

> [!div class="checklist"]
> * Azure-előfizetési kulcsok beszereznie
> * A fejlesztői környezet beállítása és függőségek telepítése
> * Lombik alkalmazás létrehozása
> * Szöveg fordítása a Fordító szöveg API-jával
> * Szövegelemzés használata a bemeneti szöveg és a fordítás pozitív/negatív hangulatának elemzésére
> * Lefordított szöveg konvertálása a Beszédszolgáltatások szolgáltatással
> * A Flask alkalmazás helyi futtatása

> [!TIP]
> Ha előre szeretne ugrani, és egyszerre szeretné látni az összes kódot, a teljes minta, valamint a buildutasítások elérhetők a [GitHubon.](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)

## <a name="what-is-flask"></a>Mi az a Flask?

A Flask egy mikrokeret webes alkalmazások létrehozásához. Ez azt jelenti, hogy a Flask olyan eszközöket, könyvtárakat és technológiákat biztosít, amelyek lehetővé teszik egy webes alkalmazás készítését. Ez a webes alkalmazás lehet néhány weboldal, egy blog, egy wiki, vagy megy olyan lényegi, mint egy web-alapú naptár alkalmazás vagy egy kereskedelmi honlapon.

Azoknak, akik szeretnék, hogy mély merülés után ez a bemutató itt van néhány hasznos link:

* [Lombik dokumentáció](http://flask.pocoo.org/)
* [Lombik kezdőknek - A kezdő útmutató a lombik](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Előfeltételek

Tekintsük át az oktatóanyaghoz szükséges szoftver- és előfizetési kulcsokat.

* [Python 3.5.2 vagy újabb](https://www.python.org/downloads/)
* [Git eszközök](https://git-scm.com/downloads)
* IDE- vagy szövegszerkesztő, például [Visual Studio-kód](https://code.visualstudio.com/) vagy [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) vagy [Firefox](https://www.mozilla.org/firefox)
* **Fordítói szöveg** előfizetési kulcs (Ne feledje, hogy nem kell régiót kijelölnie.)
* **Text** Analytics-előfizetési kulcs az USA **nyugati régiójában.**
* **A Speech Services** előfizetési kulcs az USA nyugati **régiójában.**

## <a name="create-an-account-and-subscribe-to-resources"></a>Fiók létrehozása és előfizetés az erőforrásokra

Mint korábban említettük, három előfizetési kulcsra lesz szüksége ehhez az oktatóanyaghoz. Ez azt jelenti, hogy létre kell hoznia egy erőforrást az Azure-fiókján belül:
* Fordítói szöveg
* Szövegelemzés
* Beszédszolgáltatások

A [Cognitive Services-fiók létrehozása az Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lépésről lépésre erőforrások létrehozásához.

> [!IMPORTANT]
> Ehhez az oktatóanyaghoz hozza létre az erőforrásokat az USA nyugati régiójában. Ha egy másik régiót használ, módosítania kell az alap URL-címet az egyes Python-fájlokban.

## <a name="set-up-your-dev-environment"></a>A fejlesztési környezet beállítása

A Flask webalkalmazás létrehozása előtt létre kell hoznia egy működő könyvtárat a projekthez, és telepítenie kell néhány Python-csomagot.

### <a name="create-a-working-directory"></a>Munkakönyvtár létrehozása

1. Nyissa meg a parancssort (Windows) vagy a terminált (macOS/Linux). Ezután hozzon létre egy munkakönyvtárat és alkönyvtárakat a projekthez:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Váltás a projekt munkakönyvtárára:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Hozza létre és aktiválja virtuális környezetét`virtualenv`

Hozzunk létre egy virtuális környezetet a `virtualenv`Flask alkalmazás segítségével. A virtuális környezet használata biztosítja, hogy tiszta környezetben dolgozni.

1. A munkakönyvtárban futtassa ezt a parancsot virtuális környezet létrehozásához: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Explicit módon bejelentettük, hogy a virtuális környezetnek python 3-at kell használnia. Ez biztosítja, hogy a több Python-telepítéssel rendelkező felhasználók a megfelelő verziót használják.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   Annak érdekében, hogy a dolgok egyszerű, mi elnevezése a virtuális környezet venv.

2. A virtuális környezet aktiválására vonatkozó parancsok a platformtól/rendszerhéjtól függően változnak:   

   | Platform | Felület | Parancs |
   |----------|-------|---------|
   | macOS/Linux | bash/zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Parancssor | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   A parancs futtatása után a parancssort vagy `venv`a terminálmunkamenetet a alkalmazással kell előkezelni.

3. A munkamenetet bármikor inaktiválhatja, ha beírja ezt a `deactivate`parancssorba vagy a terminálba: .

> [!NOTE]
> A Python kiterjedt dokumentációval rendelkezik a virtuális környezetek létrehozásához és kezeléséhez, [lásd: virtualenv.](https://virtualenv.pypa.io/en/latest/)

### <a name="install-requests"></a>Kérelmek telepítése

A kérelmek egy népszerű modul, amely a HTTP 1.1-es kérelmek küldésére szolgál. Nem kell manuálisan lekérdezési karakterláncokat hozzáadni az URL-címekhez, vagy űrlapkódolást kell létrehoznia a POST-adatokhoz.

1. A kérelmek telepítéséhez futtassa a következőket:

   ```
   pip install requests
   ```

> [!NOTE]
> Ha többet szeretne megtudni a kérésekről, olvassa el [a Kérések: HTTP for Humans (Http for Humans) témakört.](https://2.python-requests.org/en/master/)

### <a name="install-and-configure-flask"></a>Flask telepítése és konfigurálása

Ezután telepítenikell a Flaskát. A Flask kezeli a webalkalmazás útválasztását, és lehetővé teszi számunkra, hogy olyan kiszolgáló-szerver hívásokat bonyolítsunk le, amelyek elrejtik az előfizetési kulcsokat a végfelhasználó elől.

1. A Flask telepítéséhez futtassa a következőket:
   ```
   pip install Flask
   ```
   Nézzük meg, hogy flaska volt-e telepítve. Futtassa a következőt:
   ```
   flask --version
   ```
   A változatot a terminálra kell nyomtatni. Bármi más azt jelenti, hogy valami rosszul sült el.

2. A Flask alkalmazás futtatásához használhatja a flaska parancsot vagy a Python -m kapcsolóját a Flask-kal. Ehhez meg kell mondania a terminálnak, hogy melyik alkalmazással dolgozzon a `FLASK_APP` környezeti változó exportálásával:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>A Flask alkalmazás létrehozása

Ebben a szakaszban egy barebones Flask alkalmazást fog létrehozni, amely egy HTML-fájlt ad vissza, amikor a felhasználók az alkalmazás gyökerét érintik. Ne töltsön túl sok időt a kód szétszedésével, később visszajövünk, hogy frissítsük ezt a fájlt.

### <a name="what-is-a-flask-route"></a>Mi az a Flask útvonal?

Vegyünk egy percet, hogy beszéljünk["útvonalak](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Az Útválasztás egy URL-cím egy adott függvényhez való kötésére szolgál. A lombik útvonaldekorátorok segítségével regisztrálja a funkciókat az egyes URL-ekre. Ha például egy felhasználó a webalkalmazás gyökérre`/`( `index.html` ) navigál, a rendszer megjeleníti.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Vessünk egy pillantást még egy példa a kalapács ez otthon.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Ez a kód biztosítja, hogy `http://your-web-app.com/about` amikor `about.html` a felhasználó a fájl megjelenítéséhez navigál.

Bár ezek a minták azt mutatják, hogyan lehet html oldalakat megjeleníteni egy felhasználó számára, az útvonalak segítségével API-kat is meg lehet hívni, ha megnyom egy gombot, vagy tetszőleges számú műveletet kell végrehajtania anélkül, hogy el kellene navigálnia a kezdőlapról. Ezt működés közben fogja látni, amikor fordítási, hangulati és beszédszintézisi útvonalakat hoz létre.

### <a name="get-started"></a>Bevezetés

1. Nyissa meg a projektet az IDE-ben, majd hozzon létre egy nevet a munkakönyvtár gyökerében megnevezett `app.py` fájlt. Ezután másolja ezt `app.py` a kódot, és mentse:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Ez a kódblokk arra `index.html` utasítja az alkalmazást, hogy mindig`/`jelenjen meg, amikor a felhasználó a webalkalmazás ( gyökerere navigál).

2. Ezután hozzuk létre a webalkalmazás előtér-kiszolgálóját. Hozzon létre `index.html` egy `templates` névvel ellátott fájlt a címtárban. Ezután másolja `templates/index.html`ezt a kódot a programba.

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

3. Teszteljük a Flask alkalmazást. A terminálról futtassa:

   ```
   flask run
   ```

4. Nyisson meg egy böngészőt, és keresse meg a megadott URL-címet. Meg kell jelennie az egyoldalas alkalmazásnak. Nyomja **le a Ctrl + c** billentyűkombinációt az alkalmazás kiiktatásához.

## <a name="translate-text"></a>Szöveg lefordítása

Most, hogy van egy ötlete, hogyan működik egy egyszerű Flask app, nézzük:

* Írjon néhány Pythont a Translator Text API hívásához, és adjon vissza választ
* Hozzon létre egy Flask útvonalat a Python-kód hívásához
* A HTML frissítése szövegbeviteli és fordítási területtel, nyelvválasztóval és fordítási gombbal
* Írj Javascript, amely lehetővé teszi a felhasználók számára, hogy kölcsönhatásba lépnek a Flask app a HTML

### <a name="call-the-translator-text-api"></a>Hívja meg a fordító szöveges API-t

Az első dolog, amit meg kell tennie, hogy írjon egy függvényt a Translator Text API hívásához. Ez a függvény két `text_input` argumentumot vesz igénybe: és `language_output`a . Ezt a funkciót akkor hívják meg, amikor a felhasználó megnyomja a fordítás gombot az alkalmazásban. A HTML szövegterülete a `text_input`, és a HTML nyelvválasztó értéke a `language_output`.

1. Kezdjük azzal, hogy létrehoz `translate.py` egy fájlt, amelyet a munkakönyvtár gyökerébe hívnak.
2. Ezután adja hozzá `translate.py`ezt a kódot a hoz. Ez a függvény két `text_input` `language_output`argumentumot vesz igénybe: és a .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
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
           'Ocp-Apim-Subscription-Region': 'location',
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
3. Adja hozzá a Fordító szöveg előfizetési kulcsát, és mentse.

### <a name="add-a-route-to-apppy"></a>Útvonal hozzáadása`app.py`

Ezután létre kell hoznia egy útvonalat a `translate.py`Flask alkalmazásban, amely meghívja a . Ez az útvonal minden alkalommal meglesz hívva, amikor a felhasználó megnyomja a fordítás gombot az alkalmazásban.

Ehhez az alkalmazáshoz az útvonal `POST` fogadja a kéréseket. Ennek az az oka, hogy a függvény elvárja, hogy a szöveg lefordítsa, és egy kimeneti nyelvet a fordításhoz.

A Lombik segítő funkciókat biztosít az egyes kérések elemzéséhez és kezeléséhez. A megadott kódban `get_json()` a `POST` kérelemből származó adatokat JSON-ként adja vissza. Ezután `data['text']` `data['to']`a és a használatával a `get_translation()` szöveg- `translate.py`és kimeneti nyelvi értékek et a program ból elérhető függvénynek adja át. Az utolsó lépés az, hogy a választ JSON-ként adja vissza, mivel ezeket az adatokat meg kell jelenítenie a webalkalmazásban.

A következő szakaszokban ismételje meg ezt a folyamatot, amikor útvonalakat hoz létre a hangulatelemzéshez és a beszédszintézishez.

1. Nyissa `app.py` meg és keresse meg `app.py` az importálási utasítást a következő sor tetején, és adja hozzá a következő sort:

   ```python
   import translate
   ```
   Most a Flask app használhatja `translate.py`a rendelkezésre álló módszer.

2. Másolja a kódot a `app.py` végére, és mentse:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>A `index.html` frissítése

Most, hogy van egy funkciója a szöveg fordításához, és egy útvonal a Flask alkalmazásban, hogy meghívja, a következő lépés az, hogy elkezdi az alkalmazás HTML-fájljának kiépítését. Az alábbi HTML néhány dolgot tesz:

* Olyan szövegterületet biztosít, ahol a felhasználók bevihetik a fordítandó szöveget.
* Tartalmaz egy nyelvválasztót.
* Html-elemeket tartalmaz az észlelt nyelv és a fordítás során visszaadott megbízhatósági pontszámok megjelenítéséhez.
* Írásvédett szövegterületet biztosít, ahol a fordítási kimenet megjelenik.
* Helyőrzőket tartalmaz a hangulatelemzéshez és a beszédszintézis kódjához, amelyet az oktatóanyag későbbi részében adhat hozzá a fájlhoz.

Frissítsük `index.html`.

1. Nyissa `index.html` meg és keresse meg a következő kódmegjegyzéseket:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Cserélje le a kódmegjegyzéseket erre a HTML-blokkra:
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

A következő lépés az, hogy írjon néhány Javascript. Ez a híd a HTML és a Flask útvonal között.

### <a name="create-mainjs"></a>Létrehozása`main.js`  

A `main.js` fájl a html és flask a híd útvonala között. Az alkalmazás a jQuery, az Ajax és az XMLHttpRequest `POST` kombinációját fogja használni a tartalom megjelenítéséhez, és kéréseket intéz a Flask útvonalakra.

Az alábbi kódban a HTML-ből származó tartalom a Flask útvonalra vonatkozó kérés összeállítására szolgál. Pontosabban a szövegterület tartalma és a nyelvválasztó változókhoz van rendelve, majd a `translate-text`kérésben továbbadják a programnak.

A kód ezután végighalad a válaszon, és frissíti a HTML-t a fordítással, az észlelt nyelvvel és a megbízhatósági pontszámmal.

1. Az IDE-ből hozzon `main.js` létre `static/scripts` egy nevet a könyvtárban megnevezett fájlt.
2. Másolja a `static/scripts/main.js`kódot a következőbe:
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

### <a name="test-translation"></a>Teszt fordítása

Teszteljük a fordítást az alkalmazásban.

```
flask run
```

Keresse meg a megadott kiszolgálócímet. Írja be a szöveget a beviteli területre, válasszon ki egy nyelvet, és nyomja le a fordítást. Meg kellene szerezned egy fordítást. Ha nem működik, győződjön meg arról, hogy hozzáadta az előfizetési kulcsot.

> [!TIP]
> Ha az elvégzett módosítások nem jelennek meg, vagy az alkalmazás nem a várt módon működik, próbálja meg törölni a gyorsítótárat, vagy nyisson meg egy privát/inkognitóablakot.

Nyomja **le a CTRL + c** billentyűkombinációt az alkalmazás kiiktatásához, majd a következő szakaszba.

## <a name="analyze-sentiment"></a>Vélemények elemzése

A [Text Analytics API-t](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) hangulatelemzésre, kulcskifejezések szövegből való kinyerésére vagy a forrásnyelv észlelésére használhatja. Ebben az alkalmazásban hangulatelemzéssel határozzuk meg, hogy a megadott szöveg pozitív, semleges vagy negatív. Az API egy 0 és 1 közötti számértéket ad vissza. Az 1-hez közeli pontszámok pozitív véleményt, a 0-hoz közeli pontszámok pedig negatív hangulatot jeleznek.

Ebben a részben néhány dolgot fog tenni:

* Írjon egy Pythont a Text Analytics API hívásához a hangulatelemzés elvégzéséhez és a válasz visszaadásához
* Hozzon létre egy Flask útvonalat a Python-kód hívásához
* A HTML frissítése a véleménypontszámok területével és egy gombbal az elemzés elvégzéséhez
* Írj Javascript, amely lehetővé teszi a felhasználók számára, hogy kölcsönhatásba lépnek a Flask app a HTML

### <a name="call-the-text-analytics-api"></a>Szövegelemzési API hívása

Írjunk egy függvényt a Text Analytics API hívásához. Ez a függvény négy `input_text`argumentumot vesz igénybe: , `input_language`, `output_text`, és `output_language`. Ezt a funkciót akkor hívják meg, amikor a felhasználó megnyomja a hangulatelemzés futtatása gombot az alkalmazásban. A felhasználó által a szövegterületről és a nyelvi választóból megadott adatok, valamint az észlelt nyelvi és fordítási kimenet minden kéréshez rendelkezésre állnak. A válaszobjektum tartalmazza a forrás és a fordítás hangulatpontszámait. A következő szakaszokban javascriptet fog írni a válasz elemzéséhez és az alkalmazásban való használatához. Most koncentráljunk a Text Analytics API hívására.

1. Hozzunk létre egy `sentiment.py` fájlt hívott a gyökér a munkakönyvtár.
2. Ezután adja hozzá `sentiment.py`ezt a kódot a hoz.
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
3. Adja hozzá a Text Analytics-előfizetési kulcsot, és mentse.

### <a name="add-a-route-to-apppy"></a>Útvonal hozzáadása`app.py`

Hozzunk létre egy útvonalat a Flask alkalmazásban, amely a . `sentiment.py` Ez az útvonal minden alkalommal meglesz hívva, amikor a felhasználó megnyomja a hangulatelemzés futtatása gombot az alkalmazásban. A fordítási útvonalhoz hasonlóan ez `POST` az útvonal is fogadja a kéréseket, mivel a függvény argumentumokat vár.

1. Nyissa `app.py` meg és keresse meg `app.py` az importálási nyilatkozatot a tetején, és frissítse azt:

   ```python
   import translate, sentiment
   ```
   Most a Flask app használhatja `sentiment.py`a rendelkezésre álló módszer.

2. Másolja a kódot a `app.py` végére, és mentse:
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

### <a name="update-indexhtml"></a>A `index.html` frissítése

Most, hogy van egy funkciója a hangulatelemzés futtatásához, és egy útvonal a Flask alkalmazásban, hogy meghívja, a következő lépés az, hogy elkezdi írni az alkalmazás HTML-t. Az alábbi HTML néhány dolgot tesz:

* Gomb hozzáadása az alkalmazáshoz hangulatelemzés futtatásához
* Hozzáad egy elemet, amely megmagyarázza a véleménypontozást
* Elem hozzáadása a véleménypontszámok megjelenítéséhez

1. Nyissa `index.html` meg és keresse meg a következő kódmegjegyzéseket:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Cserélje le a kódmegjegyzéseket erre a HTML-blokkra:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>A `main.js` frissítése

Az alábbi kódban a HTML-ből származó tartalom a Flask útvonalra vonatkozó kérés összeállítására szolgál. Pontosabban a szövegterület tartalma és a nyelvválasztó változókhoz van rendelve, majd a `sentiment-analysis` kérelemben átadható az útvonalhoz.

A kód ezután végighalad a válaszon, és frissíti a HTML-t a hangulatpontszámokkal.

1. Az IDE-ből hozzon `main.js` létre `static` egy nevet a könyvtárban megnevezett fájlt.

2. Másolja a `static/scripts/main.js`kódot a következőbe:
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

Teszteljük az érzelmek elemzését az alkalmazásban.

```
flask run
```

Keresse meg a megadott kiszolgálócímet. Írja be a szöveget a beviteli területre, válasszon ki egy nyelvet, és nyomja le a fordítást. Meg kellene szerezned egy fordítást. Ezután nyomja meg a véleményelemzés futtatása gombot. Látnod kellene két pontot. Ha nem működik, győződjön meg arról, hogy hozzáadta az előfizetési kulcsot.

> [!TIP]
> Ha az elvégzett módosítások nem jelennek meg, vagy az alkalmazás nem a várt módon működik, próbálja meg törölni a gyorsítótárat, vagy nyisson meg egy privát/inkognitóablakot.

Nyomja **le a CTRL + c** billentyűkombinációt az alkalmazás kiiktatásához, majd a következő szakaszba.

## <a name="convert-text-to-speech"></a>Szöveg átalakítása beszéddé

A [text-to-speech API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) lehetővé teszi, hogy az alkalmazás a szöveget természetes, emberszerű szintetizált beszédté alakítsa. A szolgáltatás támogatja a szabványos, neurális és egyéni hangokat. Mintaalkalmazásunk néhány elérhető hangot használ, a teljes listához lásd a [támogatott nyelveket.](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)

Ebben a részben néhány dolgot fog tenni:

* Írjon egy Pythont a szövegfelolvasás hozadékához a szövegfelolvasó API-val
* Hozzon létre egy Flask útvonalat a Python-kód hívásához
* A HTML frissítése egy gombbal szövegfelolvasásési elem konvertálásához, valamint egy hanglejátszáshoz
* Javascript írása, amely lehetővé teszi a felhasználók számára, hogy kommunikáljanak a Flask alkalmazással

### <a name="call-the-text-to-speech-api"></a>A text-to-speech API hívása

Írjunk egy függvényt a szövegfelolvasás átalakításához. Ez a függvény két `input_text` argumentumot vesz igénybe: és `voice_font`a . Ezt a funkciót akkor hívják meg, amikor a felhasználó megnyomja a szövegfelolvasás konvertálása gombot az alkalmazásban. `input_text`a fordítási kimenet által visszaadott `voice_font` hívás lefordítani szöveget, az érték a hang font választó a HTML.

1. Hozzunk létre egy `synthesize.py` fájlt hívott a gyökér a munkakönyvtár.

2. Ezután adja hozzá `synthesize.py`ezt a kódot a hoz.
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
3. Adja hozzá a Beszédszolgáltatások-előfizetési kulcsot, és mentse.

### <a name="add-a-route-to-apppy"></a>Útvonal hozzáadása`app.py`

Hozzunk létre egy útvonalat a Flask alkalmazásban, amely a . `synthesize.py` Ez az útvonal minden alkalommal meglesz hívva, amikor a felhasználó megnyomja a szövegfelolvasás takarásgombját az alkalmazásban. A fordítási és hangulatelemzési útvonalakhoz hasonlóan `POST` ez az útvonal is fogadja a kéréseket, mivel a függvény két argumentumot vár: a szintetizandó szöveget és a lejátszás hangbetűtípusát.

1. Nyissa `app.py` meg és keresse meg `app.py` az importálási nyilatkozatot a tetején, és frissítse azt:

   ```python
   import translate, sentiment, synthesize
   ```
   Most a Flask app használhatja `synthesize.py`a rendelkezésre álló módszer.

2. Másolja a kódot a `app.py` végére, és mentse:

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

### <a name="update-indexhtml"></a>A `index.html` frissítése

Most, hogy van egy funkciója a szövegfelolvasás konvertálásához, és egy útvonal a Flask alkalmazásban, hogy meghívja, a következő lépés az, hogy elkezdi írni az alkalmazás HTML-kódot. Az alábbi HTML néhány dolgot tesz:

* Legördülő menüt biztosít a hangkijelöléshez
* Gomb hozzáadása szövegből történő átalakításhoz
* Hangelemet ad hozzá, amely a szintetizált beszéd lejátszására szolgál

1. Nyissa `index.html` meg és keresse meg a következő kódmegjegyzéseket:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Cserélje le a kódmegjegyzéseket erre a HTML-blokkra:
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

3. Ezután keresse meg ezeket a kódmegjegyzéseket:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Cserélje le a kódmegjegyzéseket erre a HTML-blokkra:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Ügyeljen arra, hogy mentse a munkáját.

### <a name="update-mainjs"></a>A `main.js` frissítése

Az alábbi kódban a HTML-ből származó tartalom a Flask útvonalra vonatkozó kérés összeállítására szolgál. Pontosabban a fordítás és a hang betűtípus a változókhoz van rendelve, majd a kérelemben átadható az `text-to-speech` útvonalra.

A kód ezután végighalad a válaszon, és frissíti a HTML-t a hangulatpontszámokkal.

1. Az IDE-ből hozzon `main.js` létre `static` egy nevet a könyvtárban megnevezett fájlt.
2. Másolja a `static/scripts/main.js`kódot a következőbe:
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
3. Már majdnem kész. Az utolsó dolog, amit fogsz csinálni, `main.js` hogy adjunk hozzá egy kódot, hogy automatikusan válassza ki a hang betűtípus alapján a kiválasztott nyelvet a fordításra. Adja hozzá ezt `main.js`a kódblokkot a következőhöz:
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

Teszteljük a beszédszintetizátort az alkalmazásban.

```
flask run
```

Keresse meg a megadott kiszolgálócímet. Írja be a szöveget a beviteli területre, válasszon ki egy nyelvet, és nyomja le a fordítást. Meg kellene szerezned egy fordítást. Ezután jelöljön ki egy hangot, majd nyomja meg a szövegfelolvasás gombját. a fordítást szintetizált beszédként kell lejátszani. Ha nem működik, győződjön meg arról, hogy hozzáadta az előfizetési kulcsot.

> [!TIP]
> Ha az elvégzett módosítások nem jelennek meg, vagy az alkalmazás nem a várt módon működik, próbálja meg törölni a gyorsítótárat, vagy nyisson meg egy privát/inkognitóablakot.

Ez az, van egy működő alkalmazás, amely elvégzi a fordításokat, elemzi a hangulat, és szintetizált beszéd. Nyomja **le a CTRL + c** billentyűkombinációt az alkalmazás kiiktatásához. Ügyeljen arra, hogy tekintse meg a többi [Azure Cognitive Services.](https://docs.microsoft.com/azure/cognitive-services/)

## <a name="get-the-source-code"></a>A forráskód lekérése

A projekt forráskódja elérhető a [GitHubon.](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial)

## <a name="next-steps"></a>További lépések

* [Translator Text API-referencia](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Text Analytics API-referencia](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Szövegfelolvasó API-hivatkozások](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
