---
title: Az Azure Functions tesztelése |} A Microsoft Docs
description: Az Azure functions tesztelése a Postman, a cURL és a Node.js használatával.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: az Azure functions, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra tesztelése
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a57d5f8d857a8cfcdc81e86650466aec740f41e3
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286809"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>A kódot tesztelés az Azure Functions stratégiák

Ez a témakör bemutatja a különböző módszereket teszteléséhez szükséges funkciókat, beleértve a következő általános megközelítéseket használatával:

+ HTTP-alapú eszközök, például a cURL, a Postman és a web-alapú eseményindítók még egy webes böngésző
+ Az Azure Storage Explorer, az Azure Storage-alapú eseményindítók tesztelése
+ Az Azure Functions portálon a tesztelési lapot
+ Időzítő által aktivált függvény
+ Alkalmazás- vagy keretrendszer tesztelése

A tesztelési módszer használata egy HTTP által aktivált függvény, amely bemeneti keresztül vagy a lekérdezési sztring paramétereként, vagy a kérelem törzsében. Ez a függvény az első szakaszban az Azure portal használatával hoz létre.

## <a name="create-a-simple-function-for-testing-using-the-azure-portal"></a>Hozzon létre egy egyszerű függvény tesztelése az Azure portal használatával
A legtöbb ebben az oktatóanyagban akkor használható, ha létrehoz egy függvényt HttpTrigger JavaScript-függvény sablon kis mértékben módosított verzióját használjuk. Ha egy függvény létrehozása segítségre van szüksége, tekintse át ezt [oktatóanyag](functions-create-first-azure-function.md). Válassza ki a **HttpTrigger - JavaScript** sablon létrehozásakor a teszt függvényt a [Azure Portal].

Az alapértelmezett függvénysablon alapvetően ad vissza a nevét, a kérelem szövegtörzséből vagy a lekérdezési karakterlánc paramétereként, a "hello world" függvény `name=<your name>`.  Frissítjük a kódot is lehetővé teszi, hogy a név és a egy címet, a kérelem törzsében szereplő JSON-tartalmak. A függvény ezután a ezeket újra az ügyfelet, ha elérhető az ad.   

Frissítse a függvény a következő kódra, amely a tesztelési használjuk:

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Az eszközök a függvény tesztelése
Az Azure Portalon kívül nincsenek különböző eszközöket, amelyek segítségével a tesztelési funkciók elindítani. Ezek közé tartozik a tesztelési eszközök (felületen mind a parancsot. sor), az Azure Storage-hozzáférés eszközökkel és még egy egyszerű webes böngésző HTTP.

### <a name="test-with-a-browser"></a>Tesztelés böngészővel
A webböngészőben az eseményindító függvények a HTTP Protokollon keresztül egyszerű módszert. Használhat egy böngészőben a GET-kérésekhez egy szervezet adattartalma nem igénylő, és a használata csak lekérdezési karakterlánc paraméterei.

A korábban meghatározott függvény teszteléséhez, másolja a **függvény URL-címének** a portálról. Rendelkezik a következő formátumot követi:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Fűzze hozzá a `name` a lekérdezési karakterlánc paramétert. Egy tényleges nevét használja a `<Enter a name here>` helyőrző.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Illessze be az URL-címet a böngészőben, és hogy válasz érkezik az alábbihoz hasonló.

![Képernyőkép – a Chrome böngészőlapon teszt válasz](./media/functions-test-a-function/browser-test.png)

Ebben a példában a Chrome böngészőben, amely becsomagolja a visszaadott karakterláncban XML formátumban. Más böngészők csak karakterlánc értéket jeleníti meg.

A portálon **naplók** ablakban kimenete az alábbihoz hasonló be van jelentkezve a függvény végrehajtása:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Tesztelése a postman használatával
Az ajánlott eszköz teszteléséhez a függvények a legtöbb Postman, amely integrálható a Chrome böngészőben. Postman telepítése: [első Postman](https://www.getpostman.com/). Postman segítségével szabályozhatja, számos további attribútumok HTTP-kérés.

> [!TIP]
> A vizsgálati eszköz, amely a leginkább teljesebb HTTP Protokollt használja. Az alábbiakban néhány más Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Emelt hozzáférési szintű munkaállomás](https://luckymarmot.com/paw)  
>
>

A kérelem törzsében a függvény tesztelése a Postmanben:

1. Indítsa el a Postmant, a **alkalmazások** gombra a Chrome böngészőben ablak bal felső sarkában.
2. Másolás a **függvény URL-címének**, és illessze be a Postman. Ez magában foglalja a hozzáférési kód lekérdezési karakterlánc paramétereként.
3. Módosítsa a HTTP-metódus **POST**.
4. Kattintson a **törzs** > **nyers**, és adjon hozzá egy JSON-kérés törzsének a következőhöz hasonló:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Kattintson a **küldése**.

Az alábbi képen látható, ez az oktatóanyag az egyszerű echo függvény példa tesztelése.

![Képernyőkép a Postman felhasználói felület](./media/functions-test-a-function/postman-test.png)

A portálon **naplók** ablakban kimenete az alábbihoz hasonló be van jelentkezve a függvény végrehajtása:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>A parancssorból a cURL tesztelése
Általában amikor tesztelt szoftvert, nem elengedhetetlen keresse ki az esetleges további a parancssor segítségével az alkalmazás hibakeresését. Ez nem eltér a functions tesztelése. Vegye figyelembe, hogy a cURL Linux-alapú rendszereken alapértelmezés szerint elérhető. A Windows, akkor először le kell töltenie, és telepítse a [cURL eszköz](https://curl.haxx.se/).

A függvény, amely a korábban meghatározott teszteléséhez, másolja a **függvény URL-Címének** a portálról. Rendelkezik a következő formátumot követi:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Ez az a függvényt aktiváló URL-CÍMÉT. Tesztelje a cURL-parancs segítségével a parancssorból, hogy egy GET (`-G` vagy `--get`) függvény kérelmet:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Ebben a példában van szükség a lekérdezési sztring paramétereként, amely adatok argumentumként átadhatók (`-d`) az a cURL-parancsot:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Futtassa a parancsot, és a függvény a következő kimenet jelenik meg a parancssorban:

![Kimenet képernyőképe a parancssor használatával](./media/functions-test-a-function/curl-test.png)

A portálon **naplók** ablakban kimenete az alábbihoz hasonló be van jelentkezve a függvény végrehajtása:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Storage Explorer használatával tesztelje a blob eseményindító
Blob eseményindító függvény használatával tesztelheti [Azure Storage Explorer](http://storageexplorer.com/).

1. Az a [Azure Portal] a függvényalkalmazás létrehozása a C#, F # vagy JavaScript blob eseményindító függvény. Állítsa be a elérési útjának figyelése a blobtároló nevét. Példa:

        files
2. Kattintson a **+** gombra, válassza ki vagy hozzon létre a használni kívánt tárfiókot. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. Hozzon létre egy szövegfájlt az alábbi szövegre, és mentse azt:

        A text file for blob trigger function testing.
4. Futtatás [Azure Storage Explorer](http://storageexplorer.com/), és csatlakozzon a figyelt storage-fiókban a blob-tároló.
5. Kattintson a **feltöltése** a szöveges fájlt feltölteni.

    ![Képernyőkép a Storage Explorerben](./media/functions-test-a-function/azure-storage-explorer-test.png)

Az alapértelmezett blob eseményindító függvény kód a blob, a naplók feldolgozását jelentések:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Egy függvény belül függvények tesztelése
A portál a célja, hogy HTTP tesztelése az Azure Functions és az időzítő által aktivált függvények. Tesztelt egyéb funkciók aktiválásához funkciók is létrehozhat.

### <a name="test-with-the-functions-portal-run-button"></a>A portál Futtatás gombjával funkciók tesztelése
A portál biztosít egy **futtatása** gombot, amely segítségével hajtsa végre egy korlátozott tesztelése. Kéréstörzs gomb használatával biztosíthat, de nem adja meg a lekérdezési karakterlánc paraméterei, illetve nem frissíthető a kérelemfejlécek.

A korábban létrehozott hozzáadva a következőhöz hasonló JSON-karakterláncot HTTP által aktivált függvény tesztelése a **kérelem törzse** mező. Kattintson a **futtatása** gombra.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

A portálon **naplók** ablakban kimenete az alábbihoz hasonló be van jelentkezve a függvény végrehajtása:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Időzítő eseményindító tesztelése
Bizonyos függvények a korábban említett eszközei megfelelően nem tesztelhető. Vegyük példaként, amely akkor fut, amikor egy üzenet van érkező üzenetsor eseményindító függvény [Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md). Mindig is írhat kódot a dobja el egy üzenetet az üzenetsorba, és a egy példát a konzol-projektben a cikk későbbi részében. Van azonban használhat, amely teszteli a függvényeket közvetlenül egy másik módszere.  

Egy időzítő indítófeltételt üzenetsor konfigurálva használhat kimeneti kötést. Időzítő eseményindító kódot majd írhat a teszt üzeneteket az üzenetsorba. Ez a szakasz egy példán keresztül mutatja be.

További részletes információ a kötések használata az Azure Functions: a [Azure Functions fejlesztői segédanyagai](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Tesztelési üzenetsor eseményindító létrehozása
Ez a megközelítés bemutatása érdekében először létrehozzuk, tesztelni szeretnénk a várólisták nevű üzenetsor eseményindító függvény `queue-newusers`. Ez a függvény nevét és címét, a Queue storage egy új felhasználó érkező információk dolgozza fel.

> [!NOTE]
> Ha egy másik várólistához nevet használ, ellenőrizze, hogy használt név megfelel-e a [elnevezési üzenetsorok és metaadatok](https://msdn.microsoft.com/library/dd179349.aspx) szabályokat. Ellenkező esetben a rendszer hibaüzenetet küld.
>
>

1. Az a [Azure Portal] a függvényalkalmazást, kattintson a **új függvény** > **QueueTrigger - C#**.
2. Adja meg a várólista függvény által figyelendő az üzenetsor neve:

        queue-newusers
3. Kattintson a **+** gombra, válassza ki vagy hozzon létre a használni kívánt tárfiókot. Ezt követően kattintson a **Create** (Létrehozás) gombra.
4. Hagyja nyitva, a portál böngészőablakot, hogy meg tudja figyelni a naplóbejegyzéseket az alapértelmezett várólista függvény sablonkódját a.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Az üzenetsorban lévő üzenet eldobni időzítő eseményindító létrehozása
1. Nyissa meg a [Azure Portal] egy új böngészőablakban, és keresse meg a függvényalkalmazást.
2. Kattintson a **új függvény** > **TimerTrigger – C#**. Adjon meg egy cron-kifejezés, milyen gyakran teszteli, az üzenetsor-függvény időzítő kódot beállítani. Ezt követően kattintson a **Create** (Létrehozás) gombra. Ha azt szeretné, hogy a teszt 30 másodpercenként futtatásához, a következőt használhatja [CRON-kifejezés](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Kattintson a **integráció** lap az új időzítő eseményindító.
4. A **kimeneti**, kattintson a **+ új kimenet**. Kattintson a **várólista** és **kiválasztása**.
5. Megjegyzés: a nevet használja a **várólista üzenetobjektum**. Ezzel az időzítő függvény kódban.

        myQueue
6. Adja meg a várólista nevét, ahová az üzenet elküldésekor:

        queue-newusers
7. Kattintson a **+** gombra kattintva válassza ki a tárfiókot, az üzenetsor eseményindító a korábban használt. Ezután kattintson a **Save** (Mentés) gombra.
8. Kattintson a **Develop** az időzítő eseményindító fülre.
9. A C# időzítő függvény esetében a következő kódot használhatja, mindaddig, amíg a várólista üzenet objektum névvel korábban bemutatott használt. Ezután kattintson a **Save** (Mentés) gombra.

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

Ezen a ponton a C# időzítő függvény végrehajtása 30 másodpercenként, ha például cron-kifejezésként használt. A naplók az időzítő függvény minden egyes végrehajtása jelentheti:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

A várólista függvény a böngésző ablakában látható minden üzenet feldolgozása folyamatban:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>A kód egy függvény tesztelése
Előfordulhat, hogy szeretne létrehozni egy külső alkalmazás vagy keretrendszer, a függvény teszteléséhez.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>A kód egy HTTP által aktivált függvény tesztelése: Node.js
Node.js-alkalmazások segítségével HTTP-kérést, a függvény teszteléséhez hajtsa végre.
Feltétlenül állítson be:

* A `host` a kérelem beállítások függvény app-gazdagépre.
* A függvény neve az a `path`.
* A hozzáférési kódot (`<your code>`) az a `path`.

Példa:

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


Kimenet:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

A portálon **naplók** ablakban kimenete az alábbihoz hasonló be van jelentkezve a függvény végrehajtása:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Tesztelje a kódot az üzenetsor eseményindító függvény: C# #
Azt korábban említettük, hogy egy üzenetsor eseményindító tesztelheti az üzenetsorban lévő üzenet eldobni kód használatával. Az alábbi példakód C#-kódban megjelenő alapul a [Azure Queue storage használatának első lépései](../storage/queues/storage-dotnet-how-to-use-queues.md) oktatóanyag. A kód más nyelven is a hivatkozás érhető el.

Ez a kód egy konzolalkalmazást a teszteléséhez tegye a következőket:

* [A tárolási kapcsolati karakterlánc konfigurálása az app.config fájlban](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Adja át a `name` és `address` az alkalmazás paraméterekként. Például: `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Ez a kód fogadja el a nevét és címét, egy új felhasználó parancssori argumentumként futtatás ideje alatt.)

A példában a C#-kódot:

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

A várólista függvény a böngésző ablakában látható minden üzenet feldolgozása folyamatban:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure Portal]: https://portal.azure.com
