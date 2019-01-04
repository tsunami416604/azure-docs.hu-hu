---
title: Az Azure Functions tesztelése
description: Hozzon létre automatizált teszteket egy C# függvény a Visual Studio és a JavaScript-függvény a VS Code-ban
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra tesztelése
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: cshoe
ms.openlocfilehash: 19a5dee53bee20438098d1aaeb773ebf08f252d4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993450"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>A kódot tesztelés az Azure Functions stratégiák

Ez a cikk bemutatja, hogyan hozhat létre automatizált teszteket az Azure Functions szolgáltatáshoz. 

Az összes kódot tesztelés ajánlott, azonban előfordulhat, hogy kap a legjobb eredmények elérése érdekében egy függvény logikai lezárása, és hozzon létre teszteket kívül a függvényt. Logikai azonnal paltformfüggetlen korlátozza egy függvény sornyi kódot, és lehetővé teszi, hogy a függvény, amely más osztályok vagy a modulokat hívó vonatkozó. Ebben a cikkben azonban bemutatja, hogyan hozhat létre automatizált teszteket egy HTTP- és időzítő által aktivált függvény ellen.

A következő tartalmától azt, hogy több különböző nyelvet és a környezetek két különböző szakaszokra van osztva. A vizsgálatok alatt hozhat létre további:

- [C#a Visual Studióban az xUnit](#c-in-visual-studio)
- [A VS Code-Jest JavaScript](#javascript-in-vs-code)

A minta tárház érhető el az [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C#a Visual Studióban
Az alábbi példa bemutatja, hogyan hozhat létre egy C# Függvényalkalmazásnak a Visual Studióban, és futtassa, és teszteli a [xUnit](https://xunit.github.io).

![Az Azure Functions tesztelése C# a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Beállítás

Állítsa be a környezetet, hozzon létre egy függvényt, és alkalmazás teszteléséhez. A következő lépések segítségével hozhat létre az alkalmazások és funkciók a tesztek támogatásához szükséges:

1. [Hozzon létre egy új Functions-alkalmazás](./functions-create-first-azure-function.md) , és nevezze el *funkciók*
2. [Egy HTTP-függvény létrehozása sablonból](./functions-create-first-azure-function.md) , és nevezze el *HttpTrigger*.
3. [Időzítő függvény létrehozása sablonból](./functions-create-scheduled-function.md) , és nevezze el *TimerTrigger*.
4. [Hozzon létre egy xUnit tesztalkalmazás](https://xunit.github.io/docs/getting-started-dotnet-core) a Visual Studióban kattintson **fájl > Új > Projekt > Visual C# > .NET Core > xUnit tesztelő projektet** , és nevezze el *Functions.Test*. 
5. Egy mutató hivatkozásokat tudjon felvenni az alkalmazás tesztelése a Nuget segítségével [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/) és [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referencia a *funkciók* alkalmazás](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) a *Functions.Test* alkalmazást.

### <a name="create-test-classes"></a>Teszt osztályok létrehozása

Most, hogy az alkalmazások jönnek létre, az automatikus tesztek futtatásához használt osztályok hozhat létre.

Minden függvény vesz igénybe egy példányát [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) nahrávání zprávy kezelésére. Néhány teszt nem üzenetek naplózása vagy rendelkezik hogyan van megvalósítva a naplózás nincs miatt aggódnának. Más teszteket kell értékelnie a meghatározásához, hogy egy tesztet, továbbítja a naplózott üzeneteket.

A `ListLogger` osztály hivatott megvalósítása a `ILogger` felületet, és tartsa egy teszt során értékelésre üzeneteket belső listájában.

**Kattintson a jobb gombbal** a a *Functions.Test* alkalmazás, és válassza ki **Hozzáadás > osztály**, adja neki **ListLogger.cs** , és adja meg a következő kódot:

```csharp
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions.Internal;
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

A `ListLogger` osztály által szerződésben vállalt módon valósítja meg az alábbi tagokat az `ILogger` felületen:

- **BeginScope**: Hatókörök hozzáadása a naplózási környezetben. Ebben az esetben a vizsgálat csak pontokat a statikus példányhoz a [NullScope](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.abstractions.internal.nullscope) osztály, hogy a függvény tesztelése.

- **IsEnabled**: Alapértelmezés szerint `false` van megadva.

- **Napló**: Ezt a módszert használja a megadott `formatter` függvényt az üzenet formázásához és az eredményül kapott szöveg hozzáadja a `Logs` gyűjtemény.

A `Logs` gyűjteménye egy példányát `List<string>` és a konstruktorban inicializálva van.

Ezután **kattintson a jobb gombbal** a a *Functions.Test* alkalmazás, és válassza **Hozzáadás > osztály**, adja neki **LoggerTypes.cs** , és adja meg a a következő kódot:

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

Ezután **kattintson a jobb gombbal** a a *Functions.Test* alkalmazás, és válassza **Hozzáadás > osztály**, adja neki **TestFactory.cs** , és adja meg a a következő kódot:

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
A `TestFactory` osztálya határozza meg a következő tagok:

- **Adatok**: Ez a tulajdonság adja vissza egy [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) mintaadatok gyűjteménye. A kulcs-érték párral egy lekérdezési karakterláncot az átadott értékeket jelölik.

- **CreateDictionary**: Ez a módszer egy kulcs/érték pár fogadja argumentumként, és adja vissza egy új `Dictionary` létrehozásához használt `QueryCollection` a lekérdezési karakterlánc értékeit jelölik.

- **CreateHttpRequest**: Ez a módszer létrehoz egy HTTP-kérelem inicializálása a megadott lekérdezési karakterlánc paraméterei.

- **CreateLogger**: Naplózó típusa alapján, ezzel a módszerrel a teszteléshez használt naplózó osztály adja vissza. A `ListLogger` nyomon követi a naplózott üzenetek tesztekben kiértékelésére.

Ezután **kattintson a jobb gombbal** a a *Functions.Test* alkalmazás, és válassza **Hozzáadás > osztály**, adja neki **FunctionsTests.cs** , és adja meg a a következő kódot:

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

- **Http_trigger_should_return_known_string**: Ez a vizsgálat kérést hoz létre a lekérdezés-karakterlánc értékét `name=Bill` egy HTTP-függvényt, és ellenőrzi, hogy a várt választ adja vissza.

- **Http_trigger_should_return_string_from_member_data**: Ez a vizsgálat xUnit attribútumok mintaadatokat a HTTP-függvényt használ.

- **Timer_should_log_message**: Ez a vizsgálat létrehoz egy példányt a `ListLogger` , és átadja egy időzítő függvények. Miután a függvény fut, majd a napló be van jelölve annak érdekében, hogy jelen a várt üzenet.

### <a name="run-tests"></a>Tesztek futtatása

A tesztek futtatását, nyissa meg a **teszt Explorer** kattintson **futtathatja az összes**.

![Az Azure Functions tesztelése C# a Visual Studióban](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Tesztek hibakeresése

A teszteket végezni, állítson be egy töréspontot a vizsgálatot, keresse meg a **Explorer tesztelése** kattintson **futtassa > utolsó futtassa hibakeresés**.

## <a name="javascript-in-vs-code"></a>JavaScript a VS Code-ban

Az alábbi példa bemutatja, hogyan hozzon létre egy JavaScript-függvény alkalmazást a VS Code-ban, és futtassa, és teszteli a [Jest](https://jestjs.io). Ez az eljárás használja a [VS Code funkciók bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) létrehozása az Azure Functions.

![JavaScript az Azure Functions tesztelése a VS Code-ban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Beállítás

Állítsa be a környezetet, inicializálni egy új Node.js-alkalmazás az üres mappa futtatásával `npm init`.

```bash
npm init -y
```
Ezután telepítse Jest a következő parancs futtatásával:

```bash
npm i jest
```
Most frissítse _package.json_ lecseréli a meglévő teszt parancs a következő parancsot:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Teszt modulok létrehozása
A modulok automatikus vizsgálatok futtatásához használt inicializálása a projekttel hozhat létre. Először hozzon létre egy új mappát *tesztelés* a támogatási modulok tárolásához.

Az a *tesztelés* mappa adjon hozzá egy új fájlt, adja neki **defaultContext.js**, és adja hozzá a következő kódot:

```javascript
module.exports = {
    log: jest.fn()
};
```
Ez a modul mocks a *log* függvény, amely jelöli az alapértelmezett végrehajtási környezetet.

Ezután adjon hozzá egy új fájlt, adja neki **defaultTimer.js**, és adja hozzá a következő kódot:

```javascript
module.exports = {
    isPastDue: false
};
```
A modul végrehajtja a `isPastDue` passzív tulajdonság értéke hamis időzítő példányként.

Ezután használhatja a VS Code funkciók bővítmény [hozzon létre egy új JavaScript HTTP függvényt](https://code.visualstudio.com/tutorials/functions-extension/getting-started) , és nevezze el *HttpTrigger*. A függvény létrehozása után adjon hozzá egy új fájlt ugyanabba a mappába nevű **index.test.js**, és adja hozzá a következő kódot:

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
A HTTP-függvényt a sablonból a "Hello", a lekérdezési karakterláncban megadott névvel összefűzött karakterláncot ad vissza. Ez a vizsgálat egy kérelem egy hamis példányt hoz létre, és továbbítja azt a HTTP-függvényt. A teszt ellenőrzi, hogy a *log* módszert hívja meg egyszer, és a visszaadott szövegben egyenlő "Hello Bill".

Ezután a VS Code funkciók bővítmény használatával hozzon létre egy új JavaScript-időzítő függvény *TimerTrigger*. A függvény létrehozása után adjon hozzá egy új fájlt ugyanabba a mappába nevű **index.test.js**, és adja hozzá a következő kódot:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
Az időzítő függvény a sablonból a függvény törzséhez végén egy üzenet naplózza. Ez a vizsgálat biztosítja a *log* függvény neve után.

### <a name="run-tests"></a>Tesztek futtatása
A tesztek futtatásához nyomja le az ENTER **CTRL + ~** nyissa meg a parancsablakot, és futtassa a `npm test`:

```bash
npm test
```

![JavaScript az Azure Functions tesztelése a VS Code-ban](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Tesztek hibakeresése

A teszteket végezni, adja hozzá a következő konfigurációt a *launch.json* fájlt:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Ezután állítson be egy töréspontot a teszt- és nyomja le az **F5**.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan írhat az Ön függvényeinek automatizált teszteket, folytassa a ezeket az erőforrásokat:
- [Egy nem HTTP által aktivált függvény manuális futtatása](./functions-manually-run-non-http.md)
- [Az Azure Functions hibakezelés](./functions-bindings-error-pages.md)
- [Azure-függvény Event Grid eseményindító helyi hibakeresés](./functions-debug-event-grid-trigger-local.md)
