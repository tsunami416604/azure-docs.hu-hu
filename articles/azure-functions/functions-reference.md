---
title: Útmutató a Azure Functions fejlesztéséhez | Microsoft Docs
description: Ismerkedjen meg az Azure-ban a függvények fejlesztéséhez szükséges Azure Functions fogalmakkal és technikákkal az összes programozási nyelv és kötés között.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Fejlesztői útmutató, Azure functions, functions, Event Processing, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 2715aeae5d10241703fa876557d1edcea1723874
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69874959"
---
# <a name="azure-functions-developers-guide"></a>Azure Functions fejlesztői útmutató
Azure Functions az egyes függvények a használt nyelvtől vagy kötéstől függetlenül megosztanak néhány alapvető műszaki fogalmat és összetevőt. Mielőtt beolvassa az adott nyelvre vagy kötésre vonatkozó tanulási adatokat, olvassa el ezt az áttekintést, amely az összesre vonatkozik.

Ez a cikk azt feltételezi, hogy már elolvasta a [Azure functions áttekintését](functions-overview.md).

## <a name="function-code"></a>Függvény kódja
A *függvény* a Azure functions elsődleges fogalma. A függvény két fontos darabot tartalmaz: a kódot, amely különböző nyelveken, valamint néhány konfigurációban, a function. JSON fájlban is megírható. A lefordított nyelvek esetében ez a konfigurációs fájl automatikusan létrejön a kódban szereplő jegyzetekből. A programozási nyelvek esetében saját magának kell megadnia a konfigurációs fájlt.

A function. JSON fájl határozza meg a függvény triggerét, kötéseit és egyéb konfigurációs beállításait. Minden függvénynek van egy és csak egy triggere. A futtatókörnyezet ezt a konfigurációs fájlt használja a figyelni kívánt események meghatározásához, valamint az adatoknak a függvény végrehajtásának és az adatok visszaadásának módját. A következő példa egy function. JSON fájlt mutat be.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

További Példákért lásd: [function. JSON wiki oldal](https://github.com/Azure/azure-functions-host/wiki/function.json).

A `bindings` tulajdonság az eseményindítók és kötések konfigurálására szolgál. Az egyes kötések néhány gyakori beállítást és néhány olyan beállítást is megosztanak, amelyek egy adott típusú kötésre vonatkoznak. Minden kötéshez a következő beállítások szükségesek:

| Tulajdonság | Értékek/típusok | Megjegyzések |
| --- | --- | --- |
| `type` |Karakterlánc |Kötés típusa Például: `queueTrigger`. |
| `direction` |"in", "out" |Azt jelzi, hogy a kötés az adatoknak a függvénybe való fogadására vagy adatok küldésére szolgál. |
| `name` |Karakterlánc |A függvényben a kötött adathoz használt név. Esetében C#ez egy argumentum neve; JavaScript esetén a kulcs/érték lista kulcsa. |

## <a name="function-app"></a>Függvényalkalmazás
A Function alkalmazás végrehajtási környezetet biztosít az Azure-ban, amelyben a függvények futnak. A Function alkalmazás egy vagy több olyan önálló függvényből áll, amelyek kezelése, üzembe helyezése és méretezése együtt történik. A functions-alkalmazás összes funkciója ugyanazzal az árképzési csomaggal, a folyamatos üzembe helyezéssel és a futásidejű verzióval rendelkezik. Úgy képzelheti el, hogy egy Function-alkalmazás a függvények rendszerezésére és közös kezelésére szolgál. 

> [!NOTE]
> A Function app összes függvényét ugyanabba a nyelvre kell létrehozni. A Azure Functions futtatókörnyezet [korábbi verzióiban](functions-versions.md) ez nem volt szükséges.

## <a name="folder-structure"></a>Mappa szerkezete
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A fenti az alapértelmezett (és ajánlott) mappa szerkezete egy Function alkalmazáshoz. Ha módosítani szeretné egy függvény kódjának helyét, módosítsa `scriptFile` a _function. JSON_ fájl szakaszát. Javasoljuk továbbá, hogy a [csomag központi](deployment-zip-push.md) telepítését a projekt Azure-beli Function-alkalmazásba történő üzembe helyezéséhez használja. Meglévő eszközöket is használhat, például a [folyamatos integrációt és üzembe helyezést](functions-continuous-deployment.md) , valamint az Azure-DevOps.

> [!NOTE]
> Ha manuálisan telepíti a csomagot, ügyeljen arra, hogy a _gazdagép. JSON_ fájlját és a függvény mappáit közvetlenül `wwwroot` a mappába telepítse. Ne adja meg a `wwwroot` mappát a központi telepítések között. Ellenkező esetben a `wwwroot\wwwroot` mappákkal végződik.

#### <a name="use-local-tools-and-publishing"></a>Helyi eszközök használata és közzététel
A Function apps különböző eszközökkel, például a [Visual Studióval](./functions-develop-vs.md), a [Visual Studio Code](functions-create-first-function-vs-code.md), a [IntelliJ](./functions-create-maven-intellij.md), az [Eclipse](./functions-create-maven-eclipse.md)és a [Azure functions Core Tools](./functions-develop-local.md)használatával hozhatók létre és tehetők közzé. További információ: [code and test Azure functions helyileg](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a>Függvények szerkesztése a Azure Portalban
A Azure Portal beépített functions-szerkesztő lehetővé teszi a kód és a *function. JSON* fájl közvetlen beépítését. Ez csak kis-és nagymértékű módosítások esetén ajánlott, a legjobb megoldás az, ha egy helyi fejlesztési eszközt használ, például a VS Code-ot.

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha több kiváltó esemény is gyorsabb, mint egy egyszálas függvény futtatókörnyezete, akkor a futtatókörnyezet párhuzamosan többször is meghívhatja a függvényt.  Ha egy Function alkalmazás használja a használati [üzemeltetési csomagot](functions-scale.md#how-the-consumption-and-premium-plans-work), akkor a Function alkalmazás automatikusan felskálázásra kerül.  A Function alkalmazás minden példánya, függetlenül attól, hogy az alkalmazás fut-e a használati üzemeltetési csomagon vagy egy normál [app Service üzemeltetési csomagon](../app-service/overview-hosting-plans.md), az egyidejű függvények párhuzamos használatát több szálon is feldolgozhatja.  Az egyes functions-példányokban az egyidejű függvények maximális száma a használt trigger típusától, valamint a Function alkalmazásban a más függvények által használt erőforrásoktól függően változhat.

## <a name="functions-runtime-versioning"></a>Függvények futtatókörnyezetének verziószámozása

A functions futtatókörnyezet verzióját az `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítások használatával állíthatja be. A "~ 2" érték például azt jelzi, hogy a függvényalkalmazás a főverzióként 2. x-et fog használni. A Function apps minden új alverzióra frissül, amint azok megjelentek. További információt, többek között a Function app pontos verziójának megtekintését lásd: [Azure functions futtatókörnyezet verzióinak](set-runtime-version.md)megcélzása.

## <a name="repositories"></a>Tárházak
A Azure Functions kódja nyílt forráskódú, és a GitHub-adattárakban tárolódik:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions gazdagép](https://github.com/Azure/azure-functions-host/)
* [Azure Functions portál](https://github.com/azure/azure-functions-ux)
* [Azure Functions sablonok](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Kötések
Itt látható az összes támogatott kötés táblázata.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Problémák léptek fel a kötésekkel kapcsolatos hibákkal kapcsolatban? Tekintse át a [Azure functions kötési hibakódok](functions-bindings-error-pages.md) dokumentációját.

## <a name="reporting-issues"></a>Jelentéskészítési problémák
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* [Az Azure Functions helyi kódolása és tesztelése](./functions-develop-local.md)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Azure Functions C# fejlesztői dokumentáció](functions-dotnet-class-library.md)
* [Azure Functions NodeJS fejlesztői referenciája](functions-reference-node.md)
