---
title: Az Azure Functions kidolgozásához |} A Microsoft Docs
description: Az Azure Functions fogalmakat és technikákat elsajátítani, amely a functions az Azure-ban minden programozási nyelveket és kötések fejlesztéshez szükséges.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Fejlesztői útmutató az azure functions, függvények, eseményfeldolgozás, webhookok, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 5214a59b6a1aa27c80759c5af3d91ad4711de660
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39343977"
---
# <a name="azure-functions-developers-guide"></a>Az Azure Functions fejlesztői útmutatója
Az Azure Functions, a speciális szoftverfrissítési funkciókat ossza meg néhány alapvető technikai kapcsolatos fogalmakról és összetevőkről, függetlenül a nyelvet, vagy a kötés használja. Mielőtt megkezdi a munkát egy adott nyelven vagy a kötési adatait tanuló, mindenképpen olvassa végig az áttekintés, amely mindegyik vonatkozik.

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions áttekintő](functions-overview.md) , és ismeri a [WebJobs SDK fő fogalmaira, például JobHost futásidejű, eseményindítók és kötések](https://github.com/Azure/azure-webjobs-sdk/wiki). Az Azure Functions a WebJobs SDK alapul. 

## <a name="function-code"></a>Függvénykód
A *függvény* az elsődleges fogalom, az Azure Functions szolgáltatásban. Kódot írni a függvény a választott nyelven, és mentse a kódot és a konfigurációs fájlok ugyanabban a mappában. A konfiguráció elnevezése `function.json`, amely JSON-konfigurációs adatokat tartalmazza. Különböző nyelveket támogatja, és mindegyikhez egy optimalizáltuk, hogy működnek a legjobban az adott nyelv némileg eltérő tapasztalattal rendelkezik. 

A function.json fájlt határozza meg, a függvény kötéseket és egyéb konfigurációs beállításokkal. A modul használja ezt a fájlt határozza meg az események figyelésére, és adja át az adatokat, és adatokat adja vissza a függvény végrehajtási. Az alábbiakban látható egy példa function.json fájlt.

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

Állítsa be a `disabled` tulajdonságot `true` megakadályozza, hogy a függvény végrehajtása.

A `bindings` tulajdonság, eseményindítók és kötések is konfigurálhatja. Minden egyes kötés fájlmegosztások néhány általános beállításokat és bizonyos beállítások, amelyek egy adott típusú kötés jellemző. Minden kötelező a következő beállítások szükségesek:

| Tulajdonság | Értékek/típusa | Megjegyzések |
| --- | --- | --- |
| `type` |sztring |Typ vazby. Például: `queueTrigger`. |
| `direction` |"a" "out" |Azt jelzi, hogy a kötést adatfogadásra a függvénybe vagy adatok küldése a függvénytől. |
| `name` |sztring |A kötött adatokhoz a függvényben használt neve. C# Ez az argumentumot; a JavaScript esetén a kulcsot a kulcs/érték listáját. |

## <a name="function-app"></a>Függvényalkalmazás
Függvényalkalmazás áll egy vagy több különálló függvények, amelyek együttesen kezeli az Azure App Service-ben. A függvényalkalmazás összes ossza meg az azonos díjszabással, a folyamatos üzembe helyezés és a futtatókörnyezet-verzió. Több nyelven írt Functions összes oszthatnak meg ugyanaz a függvényalkalmazás. Függvényalkalmazás felfoghatók arra, hogy rendszerezése és a függvények együttesen kezelése. 

## <a name="runtime-script-host-and-web-host"></a>Futásidejű (parancsfájlfuttató és webes gazdagép)
A runtime vagy a parancsfájlfuttató, nem a mögöttes WebJobs SDK-állomás, amelyek az eseményeket figyeli, gyűjti össze és adatokat küld és végső soron futtatja a kódot. 

HTTP-eseményindítók megkönnyítése érdekében, nincs célja, hogy a parancsfájlfuttató éles forgatókönyvekben elé Dőljön webes gazdagépet. Segít elkülöníteni a parancsfájlfuttató elejéről végén kellene két gazdagép-gazdagép kezelheti a webes forgalom.

## <a name="folder-structure"></a>Gyökérmappa-szerkezetében
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Amikor létrehozása egy függvényalkalmazáshoz az Azure App Service-ben történő üzembe helyezéshez a funkciók egy projektet, a helykódot, a gyökérmappa-szerkezetében is kezelheti. Használhatja a meglévő eszközökkel, mint például a folyamatos integrációt és üzembe helyezést, vagy egyéni üzembehelyezési szkriptek minderre transpilation kód vagy idő csomagtelepítés üzembe helyezése.

> [!NOTE]
> Ügyeljen arra, hogy üzembe helyezése a `host.json` fájlt, és mappák közvetlenül a függvény a `wwwroot` mappát. Nem tartalmazzák a `wwwroot` mappát a központi telepítések. Ellenkező esetben Ön végül `wwwroot\wwwroot` mappákat. 
> 
> 

## <a id="fileupdate"></a> Függvények alkalmazás fájl frissítése
A függvény-szerkesztő, az Azure portal beépített lehetővé teszi, hogy frissítheti a *function.json* fájl- és a kódfájl egy függvényhez. Fel-vagy egyéb fájlok például frissítése *package.json* vagy *project.json* vagy a függőségek kell használjon más telepítési módszert.

Függvény alkalmazások beépített App Service-ben, tehát a [standard web Apps szolgáltatásban elérhető telepítési lehetőségek](../app-service/app-service-deploy-local-git.md) alkalmazások esetében is elérhetők. Az alábbiakban néhány módszert használhat, fel-vagy alkalmazásfájlok függvény frissítése. 

#### <a name="to-use-app-service-editor"></a>Az App Service Editor használata
1. Az Azure Functions portálon kattintson a **platformfunkciók**.
2. Az a **FEJLESZTŐESZKÖZÖK** területén kattintson **az App Service Editor**.   
   Miután az App Service Editor betöltődött, látni fog a *host.json* fájl- és függvény appájában *wwwroot*. 
5. Nyissa meg a fájlok szerkeszthetők, vagy húzza a fejlesztői gépről a fájlok feltöltéséhez.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>A függvényalkalmazás SCM (Kudu) végpont használata
1. Navigáljon a: `https://<function_app_name>.scm.azurewebsites.net`.
2. Kattintson a **konzol Debug > CMD**.
3. Navigáljon a `D:\home\site\wwwroot\` frissíteni *host.json* vagy `D:\home\site\wwwroot\<function_name>` egy függvény fájlok frissítéséhez.
4. És húzza a megfelelő mappába, a fájl rács feltölteni kívánt fájl. A fájl rács, ahol vethetők el egy fájl két területen találhatók. A *.zip* fájlokat, megjelenik egy a címkével ellátott "húzza ide, és csomagolja ki." Minden olyan fájltípus esetében dobja el a fájlt rácsban, de a "csomagolja ki" mezőben kívül.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Folyamatos üzembe helyezés használata
Kövesse a témakör a [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Több riasztást kiváltó események bekövetkezésekor gyorsabb, mint egy egyszálas függvény futtatókörnyezetét azokat fel tudná dolgozni, a futásidejű alkalmazhatja a függvény párhuzamosan több alkalommal.  Ha nem használja a függvényalkalmazást a [Használatalapú szolgáltatási csomag](functions-scale.md#how-the-consumption-plan-works), a függvényalkalmazás automatikusan sikerült horizontálisan.  Minden példánya a függvényalkalmazást, hogy az alkalmazás fut-e a használat üzemeltetési csomag vagy egy rendszeres [App Service szolgáltatási csomagjában](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), előfordulhat, hogy egyidejű függvény meghívásához használatával több szál párhuzamos feldolgozásához.  Maximális száma minden egyes függvény alkalmazáspéldány egyidejű függvény meghívásához használt triggert, valamint egyéb funkciók belül a függvényalkalmazás által használt erőforrások típusától függően változik.

## <a name="functions-runtime-versioning"></a>Functions runtime verziószámozás

Konfigurálhatja a Functions runtime használatával verziója a `FUNCTIONS_EXTENSION_VERSION` alkalmazásbeállítást. Például a "~ 1" érték azt jelzi, hogy a Függvényalkalmazás 1 fogja használni a főverzió. Függvényalkalmazások frissül az egyes új alverzió vannak. További információk, beleértve a függvényalkalmazás pontos verziójának megtekintése: [bemutatásához az Azure Functions runtime verziók](set-runtime-version.md).

## <a name="repositories"></a>Adattárak
Azure Functions a kód nyílt forráskódú és GitHub-adattárak tárolja:

* [Az Azure Functions runtime](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Az Azure Functions portálon](https://github.com/projectkudu/AzureFunctionsPortal)
* [Az Azure Functions-sablonjairól](https://github.com/Azure/azure-webjobs-sdk-templates/)
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

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions C# – fejlesztői referencia](functions-reference-csharp.md)
* [Az Azure Functions F # – fejlesztői referencia](functions-reference-fsharp.md)
* [Az Azure Functions NodeJS fejlesztői segédanyagai](functions-reference-node.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Az Azure Functions: Az utazás](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) az Azure App Service blogján. Hogyan fejlesztette ki az Azure Functions előzményeit.

