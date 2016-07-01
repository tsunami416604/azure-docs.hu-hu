<properties
    pageTitle="Node.js API-alkalmazás az Azure App Service platformon | Microsoft Azure"
    description="Megtudhatja, hogyan hozhat létre Node.js RESTful API-t, és hogyan telepítheti egy Azure App Service platformon futó API-alkalmazásba."
    services="app-service\api"
    documentationCenter="node"
    authors="bradygaster"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="node"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="bradygaster"/>

# Node.js RESTful API buildjének elkészítése és telepítése Azure-ban futó API-alkalmazásba

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy egyszerű [Node.js](http://nodejs.org) API-t, és hogyan telepítheti egy [API-alkalmazásba](app-service-api-apps-why-best-platform.md) az [Azure App Service](../app-service/app-service-value-prop-what-is.md) platformon a [Git](http://git-scm.com) használatával. Bármilyen, Node.js futtatására alkalmas operációs rendszert használhat, és mindent parancssori eszközökkel, például a cmd.exe vagy a bash használatával kell végrehajtania.

## Előfeltételek

1. Microsoft Azure-fiók ([itt nyithat ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/))
1. Telepített [Node.js](http://nodejs.org) (ez a példa azt feltételezi, hogy a Node.js 4.2.2 verzióval rendelkezik)
2. Telepített [Git](https://git-scm.com/)
1. [GitHub](https://github.com/)-fiók

Habár az App Service számos módon lehetővé teszi a kód API-alkalmazásba való telepítését, ez az oktatóanyag a Git módszert mutatja be, és feltételezi a Git használatának alapszintű ismeretét. Más központi telepítési módszerekkel kapcsolatos további információkért lásd: [Alkalmazás telepítése az Azure App Service platformra](../app-service-web/web-sites-deploy.md).

## A mintakód letöltése

1. Nyisson meg egy parancssori felület, amely képes a Node.js és Git-parancsok futtatására.

1. Lépjen egy olyan mappába, amelyet a helyi Git-tárházhoz használhat, és klónozza a [mintakódot tartalmazó GitHub-tárházat](https://github.com/Azure-Samples/app-service-api-node-contact-list).

        git clone https://github.com/Azure-Samples/app-service-api-node-contact-list.git

    A minta API két végpontot nyújt: a `/contacts` Get kérése nevek és e-mail címek listáját adja vissza JSON formátumban, míg a `/contacts/{id}` csak a kiválasztott névjegyet.

## Node.js kód automatikus generálása Swagger-metaadatok alapján

A [Swagger](http://swagger.io/) egy olyan fájlformátum, amely RESTful API leírására szolgáló metaadatokat tárol. Az Azure App Service [beépített támogatást nyújt a Swagger-metaadatokhoz](app-service-api-metadata.md). Az oktatóanyagnak ez a fejezete az API-fejlesztésnek azt a folyamatát modellezi, amelynek során először létrehozzuk a Swagger-metaadatokat, majd ezek segítségével automatikusan generáljuk az API kiszolgálói kódját. 

>[AZURE.NOTE] Kihagyhatja ezt a szakaszt, ha nem szeretné megtanulni, hogyan kell Swagger-metaadatfájlból Node.js-kódot generálni. Ha csak telepíteni szeretné a példakódot egy új API-alkalmazásba, ugorjon közvetlenül az [API-alkalmazás létrehozása az Azure platformon](#createapiapp) című szakaszra.

### A Swaggerize telepítése és elindítása

1. A következő parancsok futtatásával telepítse globálisan a **yo** és a **generator-swaggerize** NPM modult.

        npm install -g yo
        npm install -g generator-swaggerize

    A Swaggerize egy olyan eszköz, amely kiszolgálói kódot generál egy Swagger-metaadatfájllal leírt API-hoz. A használt Swagger-fájl neve *api.json*, és a klónozott tárház *start* mappájában található.

2. Lépjen a *start* mappába, majd futtassa a `yo swaggerize` parancsot. A Swaggerize kérdéseket tesz fel Önnek.  A **what to call this project** (mi legyen a projekt neve) kérdésre a „contactlist” választ, a **path to swagger document** (swagger-dokumentum elérési útvonala) kérdésre az „api.json” választ, az **Express, Hapi, or Restify** (Express, Hapi vagy Restify) kérdésre pedig az „express” választ írja be.

        yo swaggerize

    ![Swaggerize parancssor](media/app-service-api-nodejs-api-app/swaggerize-command-line.png)
    
    **Megjegyzés:**: Ha ebben a lépésben hibát észlel, a következő lépésben megtudhatja, hogyan oldhatja meg a problémát.

    A Swaggerize létrehoz egy alkalmazásmappát, generátorokat és konfigurációs fájlokat, és előállít egy **package.json** fájlt. Az „expressz nézet” motor előállítja a Swagger súgóoldalt.  

3. Ha a `swaggerize` parancs „unexpected token” (nem várt token) vagy „invalid escape sequence” (érvénytelen escape-szekvencia) hibával tér vissza, a generált *package.json* fájl szerkesztésével javíthatja ki a hibát. A `regenerate` sorban (a `scripts` alatt) az *api.json* előtti perjelet írja át fordított perjelre, hogy a sor az alábbi példához hasonló legyen:

        "regenerate": "yo swaggerize --only=handlers,models,tests --framework express --apiPath config/api.json"

1. Keresse meg a generált kódot tartalmazó mappát (ebben az esetben a *ContactList* almappa).

1. Futtassa az `npm install` parancsot.
    
        npm install
        
2. Telepítse a **jsonpath** NPM modult. 

        npm install --save jsonpath
        
    ![Jsonpath telepítése](media/app-service-api-nodejs-api-app/jsonpath-install.png)

1. Telepítse a **swaggerize-ui** NPM modult. 

        npm install --save swaggerize-ui
        
    ![Swaggerize Ui telepítése](media/app-service-api-nodejs-api-app/swaggerize-ui-install.png)

### A generált kód testreszabása

1. Másolja a **lib** mappát a **start** mappából a generáló által létrehozott **ContactList** mappába. 

1. Cserélje a **handlers/contacts.js** fájlban lévő kódot az alábbira. 

    Ez a kód a **lib/contactRepository.js** által kiszolgált **lib/contacts.json** fájlban lévő JSON-adatokat használja. Az új contacts.js kód válaszol az összes névjegyet lekérdező HTTP-kérésekre, és JSON-adatként adja vissza őket. 

        'use strict';
        
        var repository = require('../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.all())
            }
        };

1. Cserélje a **handlers/contacts/{id}.js** fájlban lévő kódot az alábbira. 

        'use strict';

        var repository = require('../../lib/contactRepository');
        
        module.exports = {
            get: function contacts_get(req, res) {
                res.json(repository.get(req.params['id']));
            }    
        };

1. Cserélje a **server.js** fájlban lévő kódot az alábbira. 

    A server.js fájl változtatásaira megjegyzésekkel hívjuk fel a figyelmet. 

        'use strict';

        var port = process.env.PORT || 8000; // first change

        var http = require('http');
        var express = require('express');
        var bodyParser = require('body-parser');
        var swaggerize = require('swaggerize-express');
        var swaggerUi = require('swaggerize-ui'); // second change
        var path = require('path');

        var app = express();

        var server = http.createServer(app);

        app.use(bodyParser.json());

        app.use(swaggerize({
            api: path.resolve('./config/api.json'), // third change
            handlers: path.resolve('./handlers'),
            docspath: '/swagger' // fourth change
        }));

        // change four
        app.use('/docs', swaggerUi({
          docs: '/swagger'  
        }));

        server.listen(port, function () { // fifth and final change
        });

### Tesztelés helyileg futó API-val

1. Aktiválja a kiszolgálót a Node.js parancssori végrehajtható fájllal. 

        node server.js

1. Ha megnyitja a **http://localhost:8000/contacts** címet, láthatja a névjegylista JSON-kimenetét (vagy letöltheti, böngészőtől függően). 

    ![Az összes névjegy Api-hívás](media/app-service-api-nodejs-api-app/all-contacts-api-call.png)

1. Ha megnyitja a **http://localhost:8000/contacts/2** címet, az azonosítóhoz tartozó névjegyet láthatja.

    ![Adott névjegy Api-hívás](media/app-service-api-nodejs-api-app/specific-contact-api-call.png)

1. A Swagger JSON-adatokat a **/swagger** végpont biztosítja:

    ![Contacts Swagger Json](media/app-service-api-nodejs-api-app/contacts-swagger-json.png)

1. A Swagger felhasználói felületet a **/docs** végpont biztosítja. A Swagger felhasználói felületén gazdag HTML-ügyfélfunkciókkal tesztelheti az API-t.

    ![Swagger Ui](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a> Új API-alkalmazás létrehozása

Ebben a szakaszban használhatja az Azure portál segítségével új API-alkalmazást hozhat létre az Azure-ban. Ez az API-alkalmazás azokat a számítási erőforrásokat reprezentálja, amelyeket az Azure az Ön kódjának futtatásához biztosít. A későbbi szakaszokban a kódot az új API-alkalmazásba fogja telepíteni.

1. Keresse fel az [Azure portált](https://portal.azure.com/). 

1. Kattintson az **Új > Web + mobil > API-alkalmazás** elemre. 

    ![Új API-alkalmazás a portálon](media/app-service-api-nodejs-api-app/new-api-app-portal.png)

4. Az **Alkalmazásnév** mezőbe írjon be egy olyan nevet, amely egyedi az *azurewebsites.net* tartományban, például NodejsAPIApp, plusz egy szám, amellyel egyedivé teszi a nevet. 

    Ha például a név `NodejsAPIApp`, az URL-cím `nodejsapiapp.azurewebsites.net`.

    Ha olyan nevet ad meg, amelyet valaki más már használ, piros felkiáltójel jelenik meg a jobb oldalon.

6. Az **Erőforráscsoport** legördülő menüben kattintson az **Új** elemre, majd az **Új erőforráscsoport neve** mezőbe írja be a „NodejsAPIAppGroup” nevet, vagy amit szeretne. 

    Az [erőforráscsoport](../azure-portal/resource-group-portal.md) olyan Azure-erőforrások gyűjteménye, mint az API-alkalmazások, adatbázisok és virtuális gépek. Ebben az oktatóanyagban érdemes új erőforráscsoportot létrehozni, mivel így később egyetlen művelettel törölheti az oktatóanyaghoz létrehozott összes Azure-erőforrást.

4. Kattintson az **App Service-csomag/Hely**, majd az **Új létrehozása** elemre.

    ![App Service-csomag létrehozása](./media/app-service-api-nodejs-api-app/newappserviceplan.png)

    A következő lépésekben létrehozzuk az új erőforráscsoporthoz tartozó App Service-csomagot. Az App Service-csomag határozza meg azokat a számítási erőforrásokat, amelyek segítségével az API-alkalmazás fut. Ha például az ingyenes csomagot választja, az API-alkalmazás megosztott virtuális gépeken fog futni, míg egyes fizetős csomagokban az alkalmazás dedikált virtuális gépeken fut. Az App Service-csomagokkal kapcsolatban további információkért lásd a következő cikket: [App Service plans overview](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) (App Service-csomagok áttekintése).

5. Az **App Service-csomag** panelen írja be a „NodejsAPIAppPlan” nevet, vagy amit szeretne.

5. A **Location** (Hely) legördülő menüben válassza ki az Önhöz legközelebbi helyet.

    Ez a beállítás azt határozza meg, hogy melyik Azure-adatközpontban fog futni az alkalmazás. Ebben az oktatóanyagban nincs különbség, bármelyik régiót is választja. Azonban éles alkalmazások esetén az azt elérő ügyfelekhez lehető legközelebbi kiszolgálót célszerű választani a [késés](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090) minimalizálása érdekében.

5. Kattintson az **Tarifacsomag > Az összes megtekintése > F1 Ingyenes** lehetőségre.

    Ehhez az oktatóanyaghoz az ingyenes szint is megfelelő teljesítményt nyújt.

    ![Az Ingyenes tarifacsomag kiválasztása](./media/app-service-api-nodejs-api-app/selectfreetier.png)

6. Az **App Service-csomag** panelen kattintson az **OK** gombra.

7. Az **API-alkalmazást** panelen kattintson a **Létrehozás** gombra.

## Az új API-alkalmazás Git-telepítésének beállítása

Úgy tudja telepíteni a kódot az API-alkalmazásba, hogy a véglegesítéseket beküldi az Azure App Service-ben található Git-tárházba. Az oktatóanyagnak ebben a részében létrehozzuk az Azure-ban a telepítéshez szükséges hitelesítő adatokat és Git-tárházat.  

1. Az API-alkalmazás létrehozása után kattintson az **App Services > {API-alkalmazás}** lehetőségre a portál kezdőlapján. 

    A portál megjeleníti az **API-alkalmazás** és a **Beállítások** panelt.

    ![A portál API-alkalmazás és Beállítások panelje](media/app-service-api-nodejs-api-app/portalapiappblade.png)

1. A **Beállítások** panelen görgessen le a **Közzététel** szakaszhoz, és kattintson a **Telepítési hitelesítő adatok** lehetőségre.
 
3. Az **Üzembe helyezési hitelesítő adatok beállítása** panelen írjon be egy felhasználónevet és egy jelszavat, majd kattintson a **Mentés** gombra.

    Ezeket a hitelesítő adatokat fogja használni a Node.js-kódnak az API-alkalmazásban való közzétételéhez. 

    ![Telepítési hitelesítő adatok,](media/app-service-api-nodejs-api-app/deployment-credentials.png)

1. Az **Üzembe helyezési hitelesítő adatok beállítása** panelen kattintson a **Központi telepítés forrása > Forrás választása > Helyi git-tárház** lehetőségre, majd az **OK** gombra.

    ![Git-tárház létrehozása](media/app-service-api-nodejs-api-app/create-git-repo.png)

1. A Git-tárház létrehozása után a panel az aktív telepítéseket mutatja. Mivel a tárház új, ezért nincs aktív központi telepítés a listában. 

    ![Nincs aktív telepítés](media/app-service-api-nodejs-api-app/no-active-deployments.png)

1. Másolja ki a Git-tárház URL-címét. Ezt az új API-alkalmazás panelének **Alapvető szolgáltatások** részében találhatja meg. Az **Essentials** szakaszban látható a **Git clone URL-cím** elem. Ha erre az URL-címre mutat, a jobb oldalon egy ikon jelenik meg, amelyre kattintva a vágólapra másolhatja az URL-t. Kattintson erre az ikonra az URL vágólapra másolásához.

    ![A Git URL-cím másolása a portálról](media/app-service-api-nodejs-api-app/get-the-git-url-from-the-portal.png)

    **Megjegyzés:**: a Git clone URL-címre szüksége lesz a következő szakaszban, ezért addig mentse valahova.

Most, hogy már van API-alkalmazása és hozzá tartozó Git-tárháza, a tárházba való beküldéssel telepíthet kódot az API-alkalmazásba. 

## Az API-kód telepítése az Azure-ba

Ebben a szakaszban egy helyi Git-tárházat hozunk létre, amely tartalmazza az API kiszolgálóoldali kódját, majd a kódot ebből a tárházból az Azure-ban lévő, korábban létrehozott tárházba küldjük.

1. Másolja a `ContactList` mappát egy olyan helyre, amelyet az új helyi Git-tárházhoz használhat. Ha elvégezte az oktatóanyag első részét, másolja a `ContactList` mappát a `start` mappából, ellenkező esetben másolja a `ContactList` mappát a `end` mappából.

1. A választott parancssori eszközben lépjen be az új mappába, majd hajtsa végre a következő parancsot egy új helyi Git-tárház létrehozásához. 

        git init

     ![Új helyi Git-tárház](media/app-service-api-nodejs-api-app/new-local-git-repo.png)

1. A következő parancs végrehajtásával adjon hozzá egy távoli Git-tárházat az API-alkalmazás tárházához. 

        git remote add azure YOUR_GIT_CLONE_URL_HERE

    **Megjegyzés**: A „YOUR_GIT_CLONE_URL_HERE” karakterlánc helyére a saját, korábban kimásolt Git clone URL-jét írja. 

1. A következő parancsok futtatásával hozzon létre egy véglegesítést, amely tartalmazza az összes kódot. 

        git add .
        git commit -m "initial revision"

    ![A Git commit parancs kimenete](media/app-service-api-nodejs-api-app/git-commit-output.png)

1. A parancs végrehajtásával küldje be a kódot az Azure-ba. Amikor a rendszer kéri a jelszót, adja meg az Azure portálon korábban létrehozott jelszót.

        git push azure master

    Ezzel elindítja az API-alkalmazásban való telepítést.  

1. A böngészőben lépjen vissza az API-alkalmazás **Példányok** paneljére, ahol láthatja, hogy a telepítés folyamatban van. 

    ![Telepítés folyamatban](media/app-service-api-nodejs-api-app/deployment-happening.png)

    Eközben a parancssori felületen a folyamat közben látható a telepítés állapota. 

    ![Node Js telepítés folyamatban](media/app-service-api-nodejs-api-app/node-js-deployment-happening.png)

    A telepítés befejeződése után a **Példányok** panelen látható, hogy a kódváltozások sikeresen bekerültek az API-alkalmazásba. 

## Tesztelés Azure-ban futó API-val
 
3. Másolja ki az **URL-címet** az API-alkalmazás paneljének **Alapvető szolgáltatások** szakaszából. 

    ![A telepítés befejeződött](media/app-service-api-nodejs-api-app/deployment-completed.png)

1. REST API-ügyfél, például a Postman vagy a Fiddler (vagy a böngésző) használatával adja meg a névjegyek API-hívás URL-címét, ami az API-alkalmazás `/contacts` végpontja. Az URL a következő lesz: `https://{your API app name}.azurewebsites.net/contacts`

    Amikor GET kérést küld erre  a végpontra, az API-alkalmazás JSON-kimenetét fogja megkapni.

    ![Postman Api-elérés](media/app-service-api-nodejs-api-app/postman-hitting-api.png)

2. Egy böngészőben nyissa meg a `/docs` végpontot, és próbálja ki az Azure-ban futó Swagger felhasználói felületet.

Ön beállította a változtatások folyamatos üzembe helyezését, így most már egyszerűen, a véglegesítéseknek az Azure Git-tárházba való beküldésével hajthat végre kódmódosításokat, és telepítheti őket.

## Következő lépések

Ön sikeresen létrehozott egy API-alkalmazást, és Node.js API-kódot telepített rá. A következő oktatóanyag azt mutatja be, hogyan [használhat API-alkalmazásokat JavaScript-ügyfelekkel a CORS segítségével](app-service-api-cors-consume-javascript.md).



<!--HONumber=Jun16_HO2--->


