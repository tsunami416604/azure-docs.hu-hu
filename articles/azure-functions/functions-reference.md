---
title: Az Azure Functions kidolgozásához |} A Microsoft Docs
description: Az Azure Functions fogalmakat és technikákat elsajátítani, amely a functions az Azure-ban minden programozási nyelveket és kötések fejlesztéshez szükséges.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Fejlesztői útmutató az azure functions, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 01fce9813299465767bdaed7f3c2939813cfab87
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56339486"
---
# <a name="azure-functions-developers-guide"></a>Az Azure Functions fejlesztői útmutatója
Az Azure Functions, a speciális szoftverfrissítési funkciókat ossza meg néhány alapvető technikai kapcsolatos fogalmakról és összetevőkről, függetlenül a nyelvet, vagy a kötés használja. Mielőtt megkezdi a munkát egy adott nyelven vagy a kötési adatait tanuló, mindenképpen olvassa végig az áttekintés, amely mindegyik vonatkozik.

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions áttekintő](functions-overview.md).

## <a name="function-code"></a>Függvénykód
A *függvény* az elsődleges fogalom, az Azure Functions szolgáltatásban. Egy függvény két fontos darab - kódban, amely számos nyelven írhatók, és néhány konfiguráció, a function.json fájlt tartalmazza. Lefordított nyelveken a konfigurációs fájl jön létre automatikusan a jegyzetek a kódban. Programozási nyelvek meg kell adnia a konfigurációs fájlt saját maga.

A function.json fájlt határozza meg, a függvény eseményindító, kötéseit és egyéb olyan konfigurációs beállításoknak. Minden függvény csak egy eseményindító tartozik. A futtatókörnyezet használ, a konfigurációs fájlt határozza meg az események figyelésére, és adja át az adatokat és a egy függvény végrehajtását származó adatokat adja vissza. Az alábbiakban látható egy példa function.json fájlt.

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

A `bindings` tulajdonság, eseményindítók és kötések is konfigurálhatja. Minden egyes kötés oszt meg néhány gyakori beállítások és az egyes beállítások egy adott típusú kötés jellemző. Minden kötelező a következő beállítások szükségesek:

| Tulajdonság | Értékek/típusa | Megjegyzések |
| --- | --- | --- |
| `type` |sztring |Typ vazby. Például: `queueTrigger`. |
| `direction` |"a" "out" |Azt jelzi, hogy a kötést adatfogadásra a függvénybe vagy adatok küldése a függvénytől. |
| `name` |sztring |A kötött adatokhoz a függvényben használt neve. C# Ez az argumentumot; a JavaScript esetén a kulcsot a kulcs/érték listáját. |

## <a name="function-app"></a>Függvényalkalmazás
A függvényalkalmazás szolgáltat-végrehajtási környezet az Azure-ban, amelyben a függvények futnak. Függvényalkalmazás áll egy vagy több különálló függvények által kezelt, telepített, és együtt van ellátva. A függvényalkalmazás összes ossza meg az azonos díjszabással, a folyamatos üzembe helyezés és a futtatókörnyezet-verzió. Függvényalkalmazás felfoghatók arra, hogy rendszerezése és a függvények együttesen kezelése. 

> [!NOTE]
> A függvényalkalmazás a függvények ugyanazt a nyelvet kell hozhatóak létre. A [korábbi verziók](functions-versions.md) az Azure Functions Runtime nem volt szükség.

## <a name="folder-structure"></a>gyökérmappa-szerkezetében
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A fenti az alapértelmezett (és javasolt) mappastruktúra egy függvényalkalmazáshoz. Ha a fájl helyét a függvénykód módosítani kívánja, módosítsa a `scriptFile` szakaszában a _function.json_ fájlt. Azt is javasoljuk [központi telepítési csomag](deployment-zip-push.md) projekt telepítése az Azure-ban a függvényalkalmazáshoz. Használhatja például a meglévő eszközökkel is [folyamatos integrációs és üzembe helyezési](functions-continuous-deployment.md) és az Azure DevOps.

> [!NOTE]
> Manuálisan üzembe egy csomagot, ügyeljen arra, hogy üzembe helyezése a _host.json_ fájlt, és mappák közvetlenül a függvény a `wwwroot` mappát. Nem tartalmazzák a `wwwroot` mappát a központi telepítések. Ellenkező esetben Ön végül `wwwroot\wwwroot` mappákat.

#### <a name="use-local-tools-and-publishing"></a>Helyi eszközök és a közzététel használata
Függvényalkalmazások hozhat létre, és közzétette a különböző eszközök, többek között ad [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md), és a [az Azure Functions Core Tools](./functions-develop-local.md). További információkért lásd: [kódolás és tesztelés az Azure Functions helyi](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Az Azure Portalon funkciók szerkesztése
A Functions szerkesztőjében, az Azure portal beépített lehetővé teszi a kód frissítése és a *function.json* közvetlenül a beágyazott fájl. Ez csak a kisebb változtatásokat vagy megvalósíthatósági próbák menete ajánlott – ajánlott eljárás, hogy egy helyi fejlesztői eszköz, például a VS Code használata.

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Több riasztást kiváltó események bekövetkezésekor gyorsabb, mint egy egyszálas függvény futtatókörnyezetét azokat fel tudná dolgozni, a futásidejű alkalmazhatja a függvény párhuzamosan több alkalommal.  Ha nem használja a függvényalkalmazást a [Használatalapú szolgáltatási csomag](functions-scale.md#how-the-consumption-plan-works), a függvényalkalmazás automatikusan sikerült horizontálisan.  Minden példánya a függvényalkalmazást, hogy az alkalmazás fut-e a használat üzemeltetési csomag vagy egy rendszeres [App Service szolgáltatási csomagjában](../app-service/overview-hosting-plans.md), előfordulhat, hogy egyidejű függvény meghívásához használatával több szál párhuzamos feldolgozásához.  Maximális száma minden egyes függvény alkalmazáspéldány egyidejű függvény meghívásához használt triggert, valamint egyéb funkciók belül a függvényalkalmazás által használt erőforrások típusától függően változik.

## <a name="functions-runtime-versioning"></a>Functions runtime verziószámozás

Konfigurálhatja a Functions runtime használatával verziója a `FUNCTIONS_EXTENSION_VERSION` alkalmazásbeállítást. Például a "~ 2" érték azt jelzi, hogy a Függvényalkalmazás 2.x fogja használni a főverzió. Függvényalkalmazások frissül az egyes új alverzió vannak. További információk, beleértve a függvényalkalmazás pontos verziójának megtekintése: [bemutatásához az Azure Functions runtime verziók](set-runtime-version.md).

## <a name="repositories"></a>Adattárak
Azure Functions a kód nyílt forráskódú és GitHub-adattárak tárolja:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Az Azure Functions-gazdagép](https://github.com/Azure/azure-functions-host/)
* [Az Azure Functions portálon](https://github.com/azure/azure-functions-ux)
* [Az Azure Functions-sablonjairól](https://github.com/azure/azure-functions-templates)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Kötések
Itt látható az összes támogatott kötések tábláját.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

A kötések érkező hibákkal problémái vannak? Tekintse át a [Azure Functions kötés hibakódok](functions-bindings-error-pages.md) dokumentációját.

## <a name="reporting-issues"></a>Jelentéskészítési problémái
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Az Azure Functions helyi kódolása és tesztelése](./functions-develop-local.md)
* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions C# – fejlesztői referencia](functions-reference-csharp.md)
* [Az Azure Functions NodeJS fejlesztői segédanyagai](functions-reference-node.md)
