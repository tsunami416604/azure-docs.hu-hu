---
title: Az Azure Functions tesztelése
description: Automatikus tesztek létrehozása c# függvényhez a Visual Studio-ban és a JavaScript függvényhez a VS-kódban
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: a37fd886e1bc70226b2e54750540dfcb79ee5973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768877"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Kódtesztelési stratégiák az Azure Functions szolgáltatásban

Ez a cikk bemutatja, hogyan hozhat létre automatikus teszteket az Azure Functionshez. 

Az összes kód tesztelése ajánlott, azonban a legjobb eredményeket kaphatja egy függvény logikájának becsomagolásával és a függvényen kívüli tesztek létrehozásával. Az absztrakt logika korlátozza a függvény kódsorait, és lehetővé teszi, hogy a függvény kizárólagosan felelős legyen más osztályok vagy modulok hívásáért. Ez a cikk azonban bemutatja, hogyan hozhat létre automatikus teszteket egy HTTP és időzítő által aktivált függvények ellen.

Az alábbi tartalom két különböző szakaszra van osztva, amelyek különböző nyelveket és környezeteket céloznak meg. Megtanulhatja, hogyan építsen teszteket:

- [C# a Visual Studio-ban xUnit-mal](#c-in-visual-studio)
- [JavaScript vs kód jest](#javascript-in-vs-code)

A mintatár elérhető a [GitHubon.](https://github.com/Azure-Samples/azure-functions-tests)

## <a name="c-in-visual-studio"></a>C# a Visual Studióban
A következő példa bemutatja, hogyan hozhat létre C# függvényalkalmazást a Visual Studióban, és hogyan futtathat és futtathat [teszteket az xUnit segítségével.](https://xunit.github.io)

![Az Azure-függvények tesztelése C# funkcióval a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Telepítés

A környezet beállításához hozzon létre egy függvényt és tesztelje az alkalmazást. A következő lépések segítségével létrehozhatja a tesztek támogatásához szükséges alkalmazásokat és funkciókat:

1. [Új Functions alkalmazás létrehozása](./functions-create-first-azure-function.md) és *annak elnevezése*
2. [Hozzon létre egy HTTP-függvényt a sablonból,](./functions-create-first-azure-function.md) és nevezze el *httpTrigger néven.*
3. [Hozzon létre egy időzítő függvényt a sablonból,](./functions-create-scheduled-function.md) és nevezze el *TimerTrigger*.
4. [Hozzon létre egy xUnit tesztalkalmazást](https://xunit.github.io/docs/getting-started-dotnet-core) a Visual Studióban a **Fájl > Új > Project > Visual C# elemre kattintva, > .NET Core > xUnit Test Project** és a Name it *Functions.Test*. 
5. Hivatkozás hozzáadása a tesztalkalmazásból a [Microsoft.AspNetCore.Mvc alkalmazáshoz](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/) a NuGet használatával
6. [Hivatkozzon a *Functions* alkalmazás](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) *Functions.Test* alkalmazás.

### <a name="create-test-classes"></a>Tesztosztályok létrehozása

Most, hogy az alkalmazások létre, létrehozhatja az automatizált tesztek futtatásához használt osztályokat.

Minden függvény az [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) egy példányát veszi igénybe az üzenetnaplózás kezeléséhez. Egyes tesztek vagy nem naplózzák az üzeneteket, vagy nem törődnek a naplózás megvalósításának módjával. Más teszteknek ki kell értékelniük a naplózott üzeneteket annak megállapításához, hogy a teszt áthalad-e.

Az `ListLogger` osztály megvalósítja a `ILogger` felületet, és a teszt során kiértékelésre szánt üzenetek belső listáját tartalmazza.

**Kattintson** a jobb gombbal a *Functions.Test* alkalmazásra, és válassza **a Hozzáadás > osztályt,** nevezze el **NullScope.cs,** és írja be a következő kódot:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Ezután kattintson a **jobb gombbal** a *Functions.Test* alkalmazásra, és válassza **a > osztály hozzáadása**parancsot, adja meg **ListLogger.cs,** és írja be a következő kódot:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

Az `ListLogger` osztály a következő tagokat valósítja meg a `ILogger` felület által szerződtetett módon:

- **BeginScope**: A hatókörök környezetet adnak a naplózáshoz. Ebben az esetben a teszt csak az `NullScope` osztály statikus példányára mutat, hogy lehetővé tegye a teszt működését.

- **IsEnabled**: A `false` megadott alapértelmezett érték.

- **Log**: Ez a `formatter` módszer a megadott függvényt használja az `Logs` üzenet formázásához, majd hozzáadja az eredményül kapott szöveget a gyűjteményhez.

A `Logs` gyűjtemény a `List<string>` konstruktor egy példánya, és inicializálva van.

Ezután kattintson a **jobb gombbal** a *Functions.Test* alkalmazásra, és válassza **a Hozzáadás > osztályt**, nevezze el **LoggerTypes.cs,** és írja be a következő kódot:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
Ez a számbavétel határozza meg a tesztek által használt naplózó típusát. 

Ezután kattintson a **jobb gombbal** a *Functions.Test* alkalmazásra, és válassza **a Hozzáadás > osztályt**, nevezze el **TestFactory.cs,** és írja be a következő kódot:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
Az `TestFactory` osztály a következő tagokat valósítja meg:

- **Adatok**: Ez a tulajdonság a mintaadatok [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) gyűjteményét adja vissza. A kulcsérték-párok olyan értékeket jelölnek, amelyek et egy lekérdezési karakterláncba adnak át.

- **CreateDictionary**: Ez a módszer argumentumként fogad el `Dictionary` egy kulcs-érték párt, és egy újat ad vissza, `QueryCollection` amely et a lekérdezési karakterlánc okainak ábrázolására használnak.

- **CreateHttpRequest**: Ez a módszer létrehoz egy HTTP-kérelmet, amelyet a megadott lekérdezési karakterlánc-paraméterekkel inicializálva.

- **CreateLogger**: A naplózó típusa alapján ez a módszer a teszteléshez használt naplózó osztályt adja vissza. A `ListLogger` nyomon követi a teszten értékelésre rendelkezésre álló naplózott üzeneteket.

Ezután kattintson a **jobb gombbal** a *Functions.Test* alkalmazásra, és válassza **a Hozzáadás > osztályt**, adja meg **FunctionsTests.cs,** és írja be a következő kódot:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Az ebbe az osztályba tartozó tagok a következők:

- **Http_trigger_should_return_known_string**: Ez a teszt létrehoz egy `name=Bill` kérelmet a http-függvény lekérdezési karakterláncának értékeivel, és ellenőrzi, hogy a várt választ adja-e vissza.

- **Http_trigger_should_return_string_from_member_data**: Ez a teszt xUnit attribútumokat használ a HTTP-függvény mintaadatainak megadására.

- **Timer_should_log_message**: Ez a `ListLogger` teszt létrehoz egy példányt, és átadja azt egy időzítő függvényeknek. A függvény futtatása után a napló ellenőrzi, hogy a várt üzenet jelen van-e.

Ha a tesztekben szeretné elérni az alkalmazásbeállításokat, használhatja a [System.Environment.GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Tesztek futtatása

A tesztek futtatásához keresse meg a **Tesztkezelőt,** és kattintson az **Összes futtatása**gombra.

![Az Azure-függvények tesztelése C# funkcióval a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Hibakeresési tesztek

A tesztek hibakereséséhez állítson be egy töréspontot egy teszten, keresse meg a **Tesztkezelőt,** és kattintson **a Futtatás > A hibakeresés utolsó futtatása parancsra.**

## <a name="javascript-in-vs-code"></a>JavaScript vs kódban

A következő példa bemutatja, hogyan hozhat létre JavaScript függvényalkalmazást a VS Code alkalmazásban, és hogyan futtathatod és [tesztelthet jest](https://jestjs.io)segítségével. Ez az eljárás a [VS Code Functions bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) használja az Azure Functions létrehozásához.

![Az Azure-függvények tesztelése JavaScripttel VS-kódban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Telepítés

A környezet beállításához inicializáljon egy új Node.js alkalmazást egy üres mappában a futva. `npm init`

```bash
npm init -y
```
Ezután telepítse a Jest-et a következő parancs futtatásával:

```bash
npm i jest
```
Most frissítse _a package.json-t,_ hogy lecserélje a meglévő tesztparancsot a következő paranccsal:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Tesztmodulok létrehozása
A projekt inicializálása után létrehozhatja az automatikus tesztek futtatásához használt modulokat. Kezdje azzal, hogy létrehoz egy *tesztelés* nevű új mappát a támogatási modulok tárolására.

A *tesztelési* mappában adjon hozzá egy új fájlt, nevezze el **defaultContext.js**, és adja hozzá a következő kódot:

```javascript
module.exports = {
    log: jest.fn()
};
```
Ez a modul kigúnyolja a *napló* függvényt, hogy képviselje az alapértelmezett végrehajtási környezetben.

Ezután adjon hozzá egy új fájlt, nevezze el **defaultTimer.js**, és adja hozzá a következő kódot:

```javascript
module.exports = {
    IsPastDue: false
};
```

Ez a modul `IsPastDue` valósítja meg a tulajdonság állni, mint egy hamis időzítő példány. Időzítő konfigurációk, mint a NCRONTAB kifejezések nem szükséges itt, mint a teszt hám egyszerűen hívja a funkciót közvetlenül, hogy teszteljék az eredményt.

Ezután a VS Code Functions kiterjesztéssel [hozzon létre egy új JavaScript HTTP függvényt,](/azure/javascript/tutorial-vscode-serverless-node-01) és nevezze el *HttpTrigger néven.* A függvény létrehozása után adjon hozzá egy új fájlt ugyanabba a mappába, amelynek neve **index.test.js**, és adja hozzá a következő kódot:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
A sablon HTTP-függvénye egy "Hello" karakterláncot ad vissza, amely a lekérdezési karakterláncban megadott névvel van összefűzve. Ez a teszt létrehoz egy hamis példányt a kérelem, és átadja azt a HTTP-függvény. A teszt ellenőrzi, hogy a *naplómetódus* t egyszer hívják-e meg, és a visszaadott szöveg "Hello Bill" lesz.

Ezután a VS Code Functions kiterjesztéssel hozzon létre egy új JavaScript időzítő funkciót, és nevezze el *TimerTrigger*néven. A függvény létrehozása után adjon hozzá egy új fájlt ugyanabba a mappába, amelynek neve **index.test.js**, és adja hozzá a következő kódot:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
A sablon időzítő funkciója naplózza a függvény törzsének végén lévő üzenetet. Ez a teszt biztosítja, hogy a *napló* függvény tava.

### <a name="run-tests"></a>Tesztek futtatása
A tesztek futtatásához nyomja le a CTRL + `npm test`~ **billentyűkombinációt** a parancsablak megnyitásához, és futtassa a következőket:

```bash
npm test
```

![Az Azure-függvények tesztelése JavaScripttel VS-kódban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Hibakeresési tesztek

A tesztek hibakereséséhez adja hozzá a következő konfigurációt a *launch.json* fájlhoz:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Ezután állítson be egy töréspontot a tesztben, és nyomja le **az F5 billentyűt.**

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan írhat automatikus teszteket a funkcióihoz, folytassa az alábbi erőforrásokkal:
- [Nem HTTP által aktivált függvény manuális futtatása](./functions-manually-run-non-http.md)
- [Az Azure Functions hibakezelése](./functions-bindings-error-pages.md)
- [Az Azure Függvény eseményrácsának helyi hibakeresési eseménye](./functions-debug-event-grid-trigger-local.md)
