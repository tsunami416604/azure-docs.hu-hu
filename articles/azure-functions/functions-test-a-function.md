---
title: Az Azure Functions tesztelése
description: Hozzon létre automatizált teszteket egy C# függvény a Visual Studio és a JavaScript-függvény a VS Code-ban
author: craigshoemaker
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: c60cd631e703f929eaae56138a2acd3687121924
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226577"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>A kódot tesztelés az Azure Functions stratégiák

Ez a cikk bemutatja, hogyan hozhat létre automatizált teszteket az Azure Functions szolgáltatáshoz. 

Az összes kódot tesztelés ajánlott, azonban előfordulhat, hogy kap a legjobb eredmények elérése érdekében egy függvény logikai lezárása, és hozzon létre teszteket kívül a függvényt. Logikai azonnal paltformfüggetlen korlátozza egy függvény sornyi kódot, és lehetővé teszi, hogy a függvény, amely más osztályok vagy a modulokat hívó vonatkozó. Ebben a cikkben azonban bemutatja, hogyan hozhat létre automatizált teszteket egy HTTP- és időzítő által aktivált függvény ellen.

A következő tartalmától azt, hogy több különböző nyelvet és a környezetek két különböző szakaszokra van osztva. A vizsgálatok alatt hozhat létre további:

- [C#a Visual Studióban és a xUnit](#c-in-visual-studio)
- [JavaScript a VS Code-ban a Jesttel](#javascript-in-vs-code)

A minta tárház a [githubon](https://github.com/Azure-Samples/azure-functions-tests)érhető el.

## <a name="c-in-visual-studio"></a>C#a Visual Studióban
Az alábbi példa azt ismerteti, hogyan hozhat C# létre egy Function alkalmazást a Visual Studióban, és hogyan futtathat és tesztelheti a [xUnit](https://xunit.github.io).

![Az Azure Functions tesztelése C# a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Telepítés

Állítsa be a környezetet, hozzon létre egy függvényt, és alkalmazás teszteléséhez. A következő lépések segítségével hozhat létre az alkalmazások és funkciók a tesztek támogatásához szükséges:

1. [Hozzon létre egy új functions-alkalmazást](./functions-create-first-azure-function.md) , és nevezze el a *függvényeket*
2. [Hozzon létre egy http-függvényt a sablonból](./functions-create-first-azure-function.md) , és nevezze el *HttpTrigger*.
3. [Hozzon létre egy időzítő függvényt a sablonból](./functions-create-scheduled-function.md) , és nevezze el *TimerTrigger*.
4. [Hozzon létre egy xUnit-tesztelési alkalmazást](https://xunit.github.io/docs/getting-started-dotnet-core) a Visual Studióban. ehhez kattintson a **fájl > új > projekt > Visual C# > .net Core > xUnit test Project** elemre, és nevezze el a *functions. test*parancsot. 
5. A Nuget használata a [Microsoft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/) tesztelési alkalmazásból származó hivatkozások hozzáadásához
6. [Hivatkozzon a *functions* alkalmazásra](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) a *functions. test* alkalmazásban.

### <a name="create-test-classes"></a>Teszt osztályok létrehozása

Most, hogy az alkalmazások jönnek létre, az automatikus tesztek futtatásához használt osztályok hozhat létre.

A függvények a [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) egy példányát veszik fel az üzenetek naplózásának kezelésére. Néhány teszt nem üzenetek naplózása vagy rendelkezik hogyan van megvalósítva a naplózás nincs miatt aggódnának. Más teszteket kell értékelnie a meghatározásához, hogy egy tesztet, továbbítja a naplózott üzeneteket.

A `ListLogger` osztály a `ILogger` felület megvalósítását és az üzenetek belső listájának megtartását jelenti a tesztek során kiértékeléshez.

**Kattintson a jobb gombbal** a *functions. test* alkalmazásra, majd válassza a **> osztály hozzáadása**lehetőséget, nevezze el **NullScope.cs** , és adja meg a következő kódot:

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

Ezután **kattintson a jobb gombbal** a *functions. test* alkalmazásra, és válassza a **Hozzáadás > osztály**lehetőséget, nevezze el **ListLogger.cs** , és adja meg a következő kódot:

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

A `ListLogger` osztály a következő tagokat valósítja meg, mint a `ILogger` Interface:

- **BeginScope**: a hatókörök kontextust adhatnak a naplózáshoz. Ebben az esetben a teszt csak a `NullScope` osztály statikus példányára mutat, hogy lehetővé tegye a teszt működését.

- **IsEnabled**: `false` alapértelmezett értéke van megadva.

- **Napló**: Ez a metódus a megadott `formatter` függvényt használja az üzenet formázásához, majd hozzáadja az eredményül kapott szöveget a `Logs` gyűjteményhez.

A `Logs` gyűjtemény `List<string>` példánya, és a konstruktorban van inicializálva.

Ezután **kattintson a jobb gombbal** a *functions. test* alkalmazásra, és válassza a **Hozzáadás > osztály**lehetőséget, nevezze el **LoggerTypes.cs** , és adja meg a következő kódot:

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
Ez az enumerálás határozza meg a tesztek által használt naplózó. 

Ezután **kattintson a jobb gombbal** a *functions. test* alkalmazásra, és válassza a **Hozzáadás > osztály**lehetőséget, nevezze el **TestFactory.cs** , és adja meg a következő kódot:

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
A `TestFactory` osztály a következő tagokat valósítja meg:

- **Adatok**: Ez a tulajdonság a mintaadatok [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) gyűjteményét adja vissza. A kulcs-érték párral egy lekérdezési karakterláncot az átadott értékeket jelölik.

- **CreateDictionary**: Ez a metódus fogadja a kulcs/érték párokat argumentumként, és új `Dictionary` ad vissza, amelyet a rendszer a lekérdezési karakterlánc értékeit jelölő `QueryCollection` létrehozásához használ.

- **CreateHttpRequest**: Ez a METÓDUS egy HTTP-kérést hoz létre, amely a megadott lekérdezési karakterlánc-paraméterekkel inicializálva van.

- **CreateLogger**: a naplózó típus alapján ez a metódus a teszteléshez használt naplózó osztályt adja vissza. A `ListLogger` nyomon követi a tesztek kipróbálásához elérhető naplózott üzeneteket.

Ezután **kattintson a jobb gombbal** a *functions. test* alkalmazásra, és válassza a **Hozzáadás > osztály**lehetőséget, nevezze el **FunctionsTests.cs** , és adja meg a következő kódot:

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
Ez az osztály megvalósított tagjai:

- **Http_trigger_should_return_known_string**: Ez a teszt a lekérdezési karakterlánc `name=Bill` értékeit tartalmazó kérelmet hoz létre egy http-függvényhez, és ellenőrzi, hogy a rendszer visszaadja-e a várt választ.

- **Http_trigger_should_return_string_from_member_data**: Ez a teszt xUnit-attribútumokat használ a http-függvényhez tartozó mintaadatok biztosításához.

- **Timer_should_log_message**: Ez a teszt létrehoz egy `ListLogger` egy példányát, és átadja egy időzítő függvénynek. Miután a függvény fut, majd a napló be van jelölve annak érdekében, hogy jelen a várt üzenet.

Ha a tesztek során szeretné elérni az alkalmazás beállításait, használhatja a [System. environment. GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Tesztek futtatása

A tesztek futtatásához navigáljon a **tesztelési tallózóhoz** , és kattintson az **összes futtatása**parancsra.

![Az Azure Functions tesztelése C# a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Tesztek hibakeresése

A tesztek hibakereséséhez állítson be egy töréspontot egy teszten, navigáljon a **tesztelési tallózóhoz** , és kattintson a **Futtatás > hibakeresés utolsó futtatása**elemre.

## <a name="javascript-in-vs-code"></a>JavaScript a VS Code-ban

Az alábbi példa azt ismerteti, hogyan hozható létre JavaScript-függvény alkalmazás a VS Code-ban, és hogyan futtatható és tesztelhető a [Jest](https://jestjs.io)használatával. Ez az eljárás a [vs Code functions bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) használja Azure functions létrehozásához.

![JavaScript az Azure Functions tesztelése a VS Code-ban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Telepítés

A környezet beállításához inicializáljon egy új Node. js-alkalmazást egy üres mappában a `npm init`futtatásával.

```bash
npm init -y
```
Ezután telepítse Jest a következő parancs futtatásával:

```bash
npm i jest
```
Most frissítse a _Package. JSON_ fájlt, és cserélje le a meglévő teszt parancsot a következő parancsra:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Teszt modulok létrehozása
A modulok automatikus vizsgálatok futtatásához használt inicializálása a projekttel hozhat létre. Először hozzon létre egy *tesztelés* nevű új mappát a támogatási modulok tárolásához.

A *tesztelési* mappában adjon hozzá egy új fájlt, nevezze el **defaultContext. js**néven, és adja hozzá a következő kódot:

```javascript
module.exports = {
    log: jest.fn()
};
```
Ez a modul kigúnyolja a *log* függvényt, amely az alapértelmezett végrehajtási környezetet jelöli.

Ezután adjon hozzá egy új fájlt, nevezze el **defaultTimer. js**néven, és adja hozzá a következő kódot:

```javascript
module.exports = {
    IsPastDue: false
};
```

Ez a modul a `IsPastDue` tulajdonságot implementálja hamis időzítő példányként. Az időzítő konfigurációk, például a NCRONTAB-kifejezések nem szükségesek itt, mivel a teszt-hám egyszerűen hívja meg a függvényt közvetlenül az eredmény teszteléséhez.

Ezután a VS Code functions bővítmény használatával [hozzon létre egy új JavaScript http-függvényt](/azure/javascript/tutorial-vscode-serverless-node-01) , és nevezze el *HttpTrigger*. A függvény létrehozása után adjon hozzá egy új fájlt az **index. test. js**nevű mappában, és adja hozzá a következő kódot:

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
A HTTP-függvényt a sablonból a "Hello", a lekérdezési karakterláncban megadott névvel összefűzött karakterláncot ad vissza. Ez a vizsgálat egy kérelem egy hamis példányt hoz létre, és továbbítja azt a HTTP-függvényt. A teszt ellenőrzi, hogy a rendszer egyszer hívja-e a *log* metódust, és a visszaadott szöveg "Hello Bill".

Ezután a VS Code functions bővítmény használatával hozzon létre egy új JavaScript-időzítő függvényt, és nevezze el *TimerTrigger*. A függvény létrehozása után adjon hozzá egy új fájlt az **index. test. js**nevű mappában, és adja hozzá a következő kódot:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Az időzítő függvény a sablonból a függvény törzséhez végén egy üzenet naplózza. Ez a teszt azt biztosítja, hogy a *log* függvényt egyszer kell meghívni.

### <a name="run-tests"></a>Tesztek futtatása
A tesztek futtatásához nyomja le a **CTRL + ~** billentyűkombinációt a parancssori ablak megnyitásához, majd futtassa `npm test`parancsot:

```bash
npm test
```

![JavaScript az Azure Functions tesztelése a VS Code-ban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Tesztek hibakeresése

A tesztek hibakereséséhez adja hozzá a következő konfigurációt a *Launch. JSON* fájlhoz:

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

Ezután állítson be egy töréspontot a tesztben, és nyomja le az **F5**billentyűt.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan írhat az Ön függvényeinek automatizált teszteket, folytassa a ezeket az erőforrásokat:
- [Nem HTTP-triggert futtató függvény manuális futtatása](./functions-manually-run-non-http.md)
- [Azure Functions hibakezelés](./functions-bindings-error-pages.md)
- [Az Azure Function Event Grid helyi hibakeresést indít](./functions-debug-event-grid-trigger-local.md)
