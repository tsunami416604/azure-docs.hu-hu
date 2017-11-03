---
title: "Az Azure Functions tesztelése |} Microsoft Docs"
description: "Az Azure functions tesztelése Postman, a cURL és a Node.js segítségével."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure funkciók, Funkciók, esemény feldolgozása, webhookokkal, dinamikus számítási, kiszolgáló nélküli architektúra tesztelése"
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41796a8cdde0756e5157ba276463a56b07679d04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>A kód az Azure Functions tesztelése kapcsolatos olyan stratégiák

Ebben a témakörben bemutatjuk a különböző módszereket funkciókkal, beleértve a következő általános módszerek használatával teszteléséhez:

+ HTTP-alapú eszközök, például cURL Postman vagy webes eseményindítók még egy webes böngésző
+ Az Azure Tártallózó, Azure Storage-alapú eseményindítók tesztelése
+ Az Azure Functions portálon teszt lap
+ Időzítő-eseményindítóval aktivált függvény
+ Alkalmazás vagy a keretrendszer tesztelése

A tesztelési módszerek használják az egy HTTP funkció, amely a bemeneti egy lekérdezési karakterlánc paramétert vagy a kérelem törzsében keresztül fogadja. Ez a függvény az első szakaszban hoz létre.

## <a name="create-a-function-for-testing"></a>Tesztelési függvény létrehozása
Ez az oktatóanyag a legtöbb, a függvény létrehozása esetén érhető el HttpTrigger JavaScript-függvény sablon kis mértékben módosított verzióját használjuk. Ha a függvény létrehozásához segítségre van szüksége, tekintse meg a [oktatóanyag](functions-create-first-azure-function.md). Válassza ki a **HttpTrigger - JavaScript** sablon teszt funkció létrehozásakor a [Azure-portálon].

Az alapértelmezett függvény sablon alapjában echók vissza a nevét, a kérelem törzsében vagy lekérdezési karakterlánc-paraméter, a "hello world" függvény `name=<your name>`.  Azt is lehetővé teszi, hogy adja meg a nevet és egy címet a kérelem törzsében szereplő JSON-tartalomként a kód frissítése. Ezután a függvény echók ezek vissza az ügyfélnek, ha elérhető.   

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

## <a name="test-a-function-with-tools"></a>Függvény eszközök és tesztelése
Az Azure-portálon kívül vannak különböző eszközök, amelyek használatával indul el, a funkciók vizsgálatához. Ezek közé tartozik a HTTP-eszközök (Felhasználóifelület-alapú, mind parancs sor), Azure Storage access eszközök és még egy egyszerű webböngésző tesztelése.

### <a name="test-with-a-browser"></a>Egy böngésző tesztelése
A webböngésző egyszerű módja a eseményindító funkciók HTTP Protokollon keresztül. A GET-kérésekhez, amelyekhez nem szükséges a szervezet hasznos adatok között használható egy böngészőt, és használata csak lekérdezési karakterlánc-paraméterek.

Az ellenőrzéshez a korábban meghatározott függvény másolni a **függvény URL-cím** a portálról. Rendelkezik a következő formában:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Hozzáfűzése a `name` a lekérdezési karakterlánc paramétert. Egy tényleges nevét használja a `<Enter a name here>` helyőrző.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Illessze be az URL-CÍMÉT a böngészőbe, és szerezheti be a válasz a következőhöz hasonló.

![Képernyőfelvétel, Chrome böngésző lapon teszt válasz](./media/functions-test-a-function/browser-test.png)

Ebben a példában a Chrome böngészőben becsomagolja a visszaadott karakterlánc az XML-Fájlban. A más böngészőkkel csak a karakterlánc értéket jeleníti meg.

A portál **naplók** ablakban kimenet az alábbihoz hasonló naplózott, a függvény végrehajtása:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>A Postman tesztelése
A funkciók a legtöbb teszteléséhez ajánlott eszköze Postman, amely integrálható a Chrome böngészőben. Postman telepítéséhez tekintse át [beolvasása Postman](https://www.getpostman.com/). Postman segítségével szabályozhatja, HTTP-kérések számos további attribútumokat.

> [!TIP]
> A vizsgálati eszköz, amely ismeri a Feladatkezelő HTTP használata. Az alábbiakban néhány más Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  
>
>

A függvény egy kérelemtörzset tesztelése a Postman:

1. Indítsa el a Postman a **alkalmazások** gomb a böngészőablakba bal felső sarkában.
2. Másolás a **függvény URL-cím**, majd illessze be a Postman. Ez magában foglalja a hozzáférési kód lekérdezési karakterláncot.
3. Módosítsa a HTTP-metódus **POST**.
4. Kattintson a **törzs** > **nyers**, és adja hozzá a következőhöz hasonló JSON kérelemtörzset:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Kattintson a **küldése**.

A következő kép bemutatja, hogy az oktatóanyag az egyszerű echo függvény példa tesztelése.

![Képernyőfelvétel a Postman felhasználói felülete](./media/functions-test-a-function/postman-test.png)

A portál **naplók** ablakban kimenet az alábbihoz hasonló naplózott, a függvény végrehajtása:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Tesztelje a cURL a parancssorból
Gyakran amikor tesztelést szoftver, nincs szükség minden további, mint a parancssor segítségével az alkalmazás hibakeresését kereséséhez. Ez nem eltér a functions tesztelése. Ne feledje, hogy a cURL alapértelmezés szerint a Linux-alapú rendszerekhez. A Windows, először le kell töltenie és telepítse a [cURL eszköz](https://curl.haxx.se/).

A függvény, amely korábban meghatározott teszteléséhez másolja a **függvény URL-cím** a portálról. Rendelkezik a következő formában:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Ez a indítására, a függvény az URL-cím. Használjon a cURL-parancsot a parancssorból egy GET végrehajtásához (`-G` vagy `--get`) függvény kérelmet:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Ebben a példában a lekérdezési karakterlánc paraméterként, amelyek adatként átadhatók igényel (`-d`) található a cURL-parancsot:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Futtassa a parancsot, és a függvény a következő kimenet jelenik meg a parancssorban:

![Képernyőkép a parancssori kimenet](./media/functions-test-a-function/curl-test.png)

A portál **naplók** ablakban kimenet az alábbihoz hasonló naplózott, a függvény végrehajtása:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Egy blob eseményindító tesztelése Tártallózó használatával
Egy blob funkció segítségével tesztelheti [Azure Tártallózó](http://storageexplorer.com/).

1. Az a [Azure-portálon] függvény alkalmazás, hozzon létre egy C#, F # vagy JavaScript blob eseményindító függvényt. A blob-tároló neve a figyelheti a elérési útjának beállítása. Példa:

        files
2. Kattintson a  **+**  gombra, válassza ki vagy hozzon létre a használni kívánt tárfiókot. Ezt követően kattintson a **Create** (Létrehozás) gombra.
3. A következő szöveggel hozzon létre egy szövegfájlt, és mentse azt:

        A text file for blob trigger function testing.
4. Futtatás [Azure Tártallózó](http://storageexplorer.com/), és kapcsolódjon a figyelt tárfiók a blob tároló.
5. Kattintson a **feltöltése** a szöveges fájl feltöltéséhez.

    ![Képernyőkép a Tártallózó alkalmazással](./media/functions-test-a-function/azure-storage-explorer-test.png)

Az alapértelmezett blob eseményindító függvény kód a naplókban lévő blob feldolgozása jelentések:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Egy függvény funkciók tesztelése
A portál az célja, hogy lehetővé teszik, hogy HTTP tesztelése az Azure Functions és indított időzítő funkciók. Egyéb, a tesztelni kívánt funkciót kiváltásához funkciók is létrehozhat.

### <a name="test-with-the-functions-portal-run-button"></a>A funkciók portál Futtatás gombra a teszt
A portál biztosít egy **futtatása** gombra, hogy használhatják-bizonyos korlátozott tesztelése. A gombra kattintva megadhatja egy kérelemtörzset, de nem adja meg a lekérdezési karakterlánc paramétereket, illetve nem frissíthető a kérelemfejlécekben.

A HTTP-eseményindító a következőhöz hasonló JSON karakterláncnak hozzáadásával korábban létrehozott függvény tesztelése a **Request body** mező. Kattintson a **futtatása** gombra.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

A portál **naplók** ablakban kimenet az alábbihoz hasonló naplózott, a függvény végrehajtása:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Az időzítő eseményindító tesztelése
Egyes funkciók nem megfelelően tesztelni a korábban említett eszközök. Tegyük fel, amely akkor fut, amikor egy üzenet megszakad a várólista eseményindító függvény [Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md). Mindig írhat kódot a dobja el az üzenetet a várólistán, és példákat a konzol projektben áll a cikk későbbi részében. Van azonban egy másik módszert alkalmaz is használhat, amely közvetlenül tesztek funkciók.  

Egy időzítő indítófeltételt konfigurált üzenetsorokat használhatja kimeneti kötése. Hogy időzítő aktiváló kód majd írhat a tesztüzenetet a várakozási sorba. Ez a szakasz egy példán keresztül bemutatja, hogyan.

További információt az Azure Functions kötések használatával, lásd: a [Azure Functions fejlesztői segédanyagai](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>A létrehozott várólista tesztelése
Bemutatják, ezt a módszert használja, azt először létre kell hoznia egy várólista funkció, amely teszteléséhez nevű várólista szeretnénk `queue-newusers`. Ez a funkció be egy új felhasználó a Queue storage eldobott nevét és címét adatokat dolgozza fel.

> [!NOTE]
> Ha egy másik várólistához nevet használja, győződjön meg arról, hogy a használt név megfelel-e a [elnevezési üzenetsorok és metaadatok](https://msdn.microsoft.com/library/dd179349.aspx) szabályok. Ellenkező esetben hibaüzenetet kap.
>
>

1. Az a [Azure-portálon] függvény alkalmazás, kattintson a **új függvény** > **QueueTrigger - C#**.
2. Adja meg a várólista nevét a várólista függvény által figyelt:

        queue-newusers
3. Kattintson a  **+**  gombra, válassza ki vagy hozzon létre a használni kívánt tárfiókot. Ezt követően kattintson a **Create** (Létrehozás) gombra.
4. Hagyja megnyitva, a portál böngészőablakot, figyelheti a naplóbejegyzéseket, az alapértelmezett várólista függvény sablon kódot.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Hozzon létre egy időzítő indítófeltételt üzenetet a várólistában lévő elvetése érdekében
1. Nyissa meg a [Azure-portálon] egy új böngészőablakban, és keresse meg a függvény alkalmazást.
2. Kattintson a **új függvény** > **TimerTrigger - C#**. Adja meg a beállítása, hogy milyen gyakran az időzítő kód teszteli a várólista függvény cron-kifejezést. Ezt követően kattintson a **Create** (Létrehozás) gombra. Ha a vizsgálatot, 30 másodperces futtatásához, a következő használható [CRON-kifejezés](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Kattintson a **integráció** lap az új időzítő eseményindító.
4. A **kimeneti**, kattintson a **+ új kimeneti**. Kattintson a **várólista** és **válasszon**.
5. Megjegyzés: a nevet használja a **várólista message objektumot**. Ezzel a időzítő függvény kódban.

        myQueue
6. Adja meg a várólista nevét, ahol a hibaüzenet:

        queue-newusers
7. Kattintson a  **+**  gombra kattintva válassza ki a korábban használt a várólista eseményindító tárfiókot. Ezután kattintson a **Save** (Mentés) gombra.
8. Kattintson a **Develop** az időzítő eseményindító fülre.
9. A következő kódot használhatja a C# időzítő függvény, mindaddig, amíg a várólista üzenet objektum néven korábban bemutatott használta. Ezután kattintson a **Save** (Mentés) gombra.

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

Ezen a ponton a C# időzítő végrehajtja 30 másodpercenként, ha követte a példa cron-kifejezés. A naplókat az időzítő jelentést minden egyes végrehajtása:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

A várólista függvény böngészőablakban minden egyes üzenet feldolgozás alatt látható:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Egy függvény kóddal tesztelése
Szükség lehet egy külső alkalmazás vagy keretrendszer, a funkciók ellenőrzéséhez hozzon létre.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Egy HTTP-eseményindító függvény kóddal tesztelése: Node.js
A Node.js-alkalmazás segítségével hajtható végre egy HTTP-kérést a függvény tesztelése.
Feltétlenül állítson be:

* A `host` a kérelem-beállításokban, a függvény app gazdagépre.
* A függvény nevét a `path`.
* A hozzáférési kódját (`<your code>`) található a `path`.

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

A portál **naplók** ablakban kimenet az alábbihoz hasonló naplózott, a függvény végrehajtása:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>A várólista funkció kóddal tesztelése: C# #
Azt a korábban említett, hogy várólista eseményindító eldobásához egy üzenetet a várólistában lévő kód segítségével tesztelheti. Az alábbi példakód C#-kódban szerepel a alapul a [Ismerkedés az Azure Queue storage](../storage/queues/storage-dotnet-how-to-use-queues.md) oktatóanyag. Kód más nyelveken érhető el az adott hivatkozáson.

Ez a kód egy konzolalkalmazás teszteléséhez a következőket kell tennie:

* [A tárolási kapcsolati karakterlánc konfigurálása az app.config fájlban](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Adjon át egy `name` és `address` az alkalmazás paraméterekként. Például: `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Ez a kód fogad el nevét és címét egy új felhasználó parancssori argumentumok futásidőben.)

C# példakódot:

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

A várólista függvény böngészőablakban minden egyes üzenet feldolgozás alatt látható:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[Azure-portálon]: https://portal.azure.com
