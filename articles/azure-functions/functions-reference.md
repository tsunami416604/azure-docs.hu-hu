---
title: Útmutató az Azure Functions fejlesztéséhez
description: Ismerje meg az Azure Functions fogalmait és technikáit, amelyek a függvények azure-ban, az összes programozási nyelven és kötésben történő fejlesztéséhez szükségesek.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7dd7ef3c4833fb9ffa3781f06faba4f40cd40cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276620"
---
# <a name="azure-functions-developers-guide"></a>Útmutató az Azure Functions fejlesztőinek
Az Azure Functionsben bizonyos függvények néhány alapvető műszaki koncepciót és összetevőt osztanak meg, függetlenül a használt nyelvtől vagy kötéstől. Mielőtt beleugranál egy adott nyelvre vagy kötésre vonatkozó tanulási részletekbe, mindenképpen olvasd el ezt az áttekintést, amely mindegyikre vonatkozik.

Ez a cikk feltételezi, hogy már elolvasta az [Azure Functions áttekintését.](functions-overview.md)

## <a name="function-code"></a>Függvénykód
A *függvény* az Azure Functions elsődleges koncepciója. A függvény két fontos darabot tartalmaz - a kódot, amely et különböző nyelveken lehet írni, és néhány konfigurációt, a function.json fájlt. A lefordított nyelvek esetében ez a konfigurációs fájl automatikusan létrejön a kódban lévő jegyzetekből. A parancsfájlnyelvek hez a konfigurációs fájlt saját kezűleg kell megadnia.

A function.json fájl határozza meg a függvény eseményindítóját, kötéseit és egyéb konfigurációs beállításait. Minden függvényhez egy, és csakis egy trigger tartozik. A futásidejű használja ezt a konfigurációs fájlt, hogy meghatározza az eseményeket a figyeléshez, és hogyan adatokat továbbíthat, és adatokat afüggvény-végrehajtás. Az alábbi példa function.json fájl.

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

További információ: [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

A `bindings` tulajdonság, ahol konfigurálja az eseményindítók és a kötések. Minden kötés megoszt néhány gyakori beállítást és néhány olyan beállítást, amelyek egy adott kötéstípusra jellemzőek. Minden kötéshez a következő beállítások szükségesek:

| Tulajdonság | Értékek/típusok | Megjegyzések |
| --- | --- | --- |
| `type` |sztring |Kötés típusa. Például: `queueTrigger`. |
| `direction` |"be", "ki" |Azt jelzi, hogy a kötés adatok fogadására vagy a függvényből történő adatküldésre szolgál.Indicates whether the binding is for receiving data into the function or sending data from the function. |
| `name` |sztring |A függvényben lévő kötött adatokhoz használt név. A C#esetében ez egy argumentumnév; a JavaScript, ez a kulcs egy kulcs / érték lista. |

## <a name="function-app"></a>Függvényalkalmazás
Egy függvényalkalmazás egy végrehajtási környezetet biztosít az Azure-ban, amelyben a függvények futnak. Mint ilyen, ez az egység a telepítési és felügyeleti funkcióit. A függvényalkalmazás egy vagy több egyedi függvényből áll, amelyek felügyeltek, üzembe helyezve és együtt vannak méretezve. A függvényalkalmazás összes függvénye ugyanazt az árképzési tervet, telepítési módszert és futásidejű verziót használja. Gondoljon úgy egy függvényalkalmazásra, mint a funkciók rendszerezésére és együttes kezelésére. További információ: [Függvényalkalmazás kezelése.](functions-how-to-use-azure-function-app-settings.md) 

> [!NOTE]
> A függvényalkalmazás összes függvényének ugyanazon a nyelven kell szerződnie. Az Azure Functions futásidejű [korábbi verzióiban](functions-versions.md) ez nem volt szükséges.

## <a name="folder-structure"></a>Mappastruktúra
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A fenti a Függvényalkalmazás alapértelmezett (és ajánlott) mappastruktúrája. Ha módosítani szeretné egy függvény kódjának fájlhelyét, `scriptFile` módosítsa a _function.json_ fájl szakaszát. Azt is javasoljuk, hogy [csomagüzembe helyezése](deployment-zip-push.md) a projekt üzembe helyezéséhez a függvényalkalmazás az Azure-ban. Meglévő eszközöket is használhat, például [folyamatos integrációt és üzembe helyezést,](functions-continuous-deployment.md) valamint az Azure DevOps-t.

> [!NOTE]
> Ha manuálisan telepít egy csomagot, győződjön meg arról, hogy a `wwwroot` _host.json_ fájlt és a függvénymappákat közvetlenül a mappába telepíti. Ne foglalja `wwwroot` bele a mappát a központi telepítésbe. Ellenkező esetben, akkor `wwwroot\wwwroot` a végén a mappákat.

#### <a name="use-local-tools-and-publishing"></a>Helyi eszközök és közzététel használata
A függvényalkalmazások számos eszközzel is létrehozhatók és közzétehetők, például [a Visual Studio,](./functions-develop-vs.md) [a Visual Studio Code,](functions-create-first-function-vs-code.md) [az IntelliJ](./functions-create-maven-intellij.md), az [Eclipse](./functions-create-maven-eclipse.md)és az Azure Functions core [eszközök segítségével.](./functions-develop-local.md) További információ: [Az Azure Functions helyi kódolása és tesztelése című témakörben talál.](./functions-develop-local.md)

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a>Függvények szerkesztése az Azure Portalon
Az Azure Portalbeépített Functions szerkesztő lehetővé teszi a kód és a *function.json* fájl közvetlen üle- és inline frissítését. Ez csak kis változtatásokhoz vagy koncepcióigazolásokhoz ajánlott - a legjobb gyakorlat egy helyi fejlesztési eszköz, például a VS Code használata.

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Ha több eseményindító esemény gyorsabban történik, mint ahogy egy egyszálas függvény futásidejű feldolgozni őket, a futásidejű meghívása a függvény többször párhuzamosan.  Ha egy függvényalkalmazás a [Fogyasztás hosting csomagot](functions-scale.md#how-the-consumption-and-premium-plans-work)használja, a függvényalkalmazás automatikusan horizontálisan kiméretezhető.  A függvényalkalmazás minden példánya, függetlenül attól, hogy az alkalmazás a fogyasztási üzemeltetési csomagon vagy egy rendszeres [App Service-üzemeltetési csomagon](../app-service/overview-hosting-plans.md)fut, több szál használatával párhuzamosan feldolgozhatja az egyidejű függvénymeghívásokat.  Az egyes függvényalkalmazás-példányokban az egyidejű függvényberendelések maximális száma a használt eseményindító típusától, valamint a függvényalkalmazás on belüli egyéb függvények által használt erőforrásoktól függ.

## <a name="functions-runtime-versioning"></a>Függvények futásidejű verziószámozása

A Functions futásidejű verziót az `FUNCTIONS_EXTENSION_VERSION` alkalmazásbeállítással konfigurálhatja. Például a "~3" érték azt jelzi, hogy a Függvényalkalmazás a 3.x-et fogja használni főverzióként. A függvényalkalmazások minden új alverzióra frissülnek, amint azok megjelennek. További információ, többek között a függvényalkalmazás pontos verziójának megtekintéséről az [Azure Functions futásidejű verzióinak célzása](set-runtime-version.md)című témakörben talál.

## <a name="repositories"></a>Adattárak
Az Azure Functions kódja nyílt forráskódú és a GitHub-adattárakban tárolódik:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Azure Functions gazdagépe](https://github.com/Azure/azure-functions-host/)
* [Az Azure Functions portál](https://github.com/azure/azure-functions-ux)
* [Azure Functions-sablonok](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Kötések
Az alábbiakban az összes támogatott kötést táblázatban olvashatja.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Problémái vannak a kötésekből származó hibákkal? Tekintse át az [Azure Functions kötési hibakódok dokumentációját.](functions-bindings-error-pages.md)

## <a name="reporting-issues"></a>Jelentési problémák
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Az Azure Functions aktiválódik és kötéseket köt](functions-triggers-bindings.md)
* [Az Azure Functions helyi kódolása és tesztelése](./functions-develop-local.md)
* [Gyakorlati tanácsok az Azure Functionshez](functions-best-practices.md)
* [Azure Functions C# fejlesztői útmutató](functions-dotnet-class-library.md)
* [Az Azure Functions Node.js – fejlesztői útmutató](functions-reference-node.md)
