---
title: "Az Azure Functions kidolgozásához |} Microsoft Docs"
description: "További tudnivalók az Azure Functions fogalmakat és módszereket is, hogy funkciók Azure, az összes programozási nyelveket és kötések kifejlesztésére lesz szükség."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Fejlesztői útmutató, azure funkciók, Funkciók, Eseményfeldolgozási, webhookokkal, a dinamikus számítási, a kiszolgáló nélküli architektúrája"
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/12/2017
ms.author: tdykstra
ms.openlocfilehash: 53ba5eaf3272746bd107efbcbae4b5d5889a197f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-functions-developers-guide"></a>Az Azure Functions fejlesztői útmutatója
Az Azure Functions adott funkciókhoz ossza meg néhány alapvető technikai kulcsfogalmak és összetevők, függetlenül a nyelvet, vagy a kötés használja. Ahhoz, hogy belevágjon tanulási egy adott nyelven vagy a kötési adatait, mindenképpen olvassa végig az áttekintés, amely az összes vonatkozik.

Ez a cikk feltételezi, hogy Ön már elolvasta a [Azure Functions áttekintése](functions-overview.md) és ismeri a [WebJobs SDK fogalmakat, például a JobHost futásidejű, eseményindítók és kötések](https://github.com/Azure/azure-webjobs-sdk/wiki). Az Azure Functions a WebJobs SDK alapul. 

## <a name="function-code"></a>Funkciókódot
A *függvény* az Azure Functions elsődleges fogalom. Írhat kódot a funkció az Ön által választott nyelven, és mentse a kódot és konfigurációs fájljait ugyanabban a mappában. A konfiguráció elnevezése `function.json`, amely tartalmazza a JSON-konfigurációs adatok. Különböző nyelveket támogatja, és minden nincs optimalizálva az adott nyelv némileg más élményt. 

A function.json fájl határozza meg, a függvénykötés és egyéb konfigurációs beállításokkal. A futtatókörnyezet ezt a fájlt határozza meg a figyelendő események és adja át az adatokat, és vissza adatokat, a függvény végrehajtása használja. A következő egy példa function.json fájl.

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

Állítsa be a `disabled` tulajdonságot `true` megakadályozhatja, hogy a függvény a végrehajtás alatt.

A `bindings` tulajdonság értéke, ahol konfigurálhatja az eseményindítók és kötések is. Minden kötésnek osztja meg néhány általános beállítások és az egyes beállítások, amelyek bizonyos típusú kötés. Minden kötésnek szükséges a következő beállításokat:

| Tulajdonság | Értékek/típusok | Megjegyzések |
| --- | --- | --- |
| `type` |karakterlánc |Kötés típusa. Például: `queueTrigger`. |
| `direction` |"in" "out" |Azt jelzi, hogy a kötés adatfogadásra a függvénynek vagy adatokat küld a függvény. |
| `name` |karakterlánc |A függvény a kötött adatok használt név. C# ez pedig egy argumentum neve; a JavaScript esetén a kulcsot a kulcs/érték listáját. |

## <a name="function-app"></a>Függvényalkalmazás
Egy vagy több egyéni függvények felügyelete együtt, amelyet az Azure App Service egy függvény alkalmazást magában foglalja. Összes függvény alkalmazásban funkció ossza meg az árképzési csomagot, a folyamatos üzembe helyezés és a futásidejű verzióját. Több nyelven írt funkciók összes megoszthatja függvény ugyanahhoz az alkalmazáshoz. Egy függvény app gondol rendszerezését és a funkciók együttesen kezelését is. 

## <a name="runtime-script-host-and-web-host"></a>Futásidejű (script host és webkiszolgáló)
A runtime vagy a parancsfájlfuttató, nem az alapul szolgáló WebJobs SDK-állomás eseményeket figyeli, összegyűjti és adatokat küld, és végső soron futtatja a kódot. 

HTTP-eseményindítók megkönnyítésére is van olyan webes gazdagépet, amely arra tervezték, hogy a parancsfájlfuttató éles forgatókönyvekben elé elhelyezkedik. Két állomás rendelkező segít elkülöníteni a parancsfájlfuttató elölről fejezze be a webkiszolgáló által kezelt forgalom.

## <a name="folder-structure"></a>Mappaszerkezet
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Amikor létrehozása a projekt funkciók telepítéséhez függvény alkalmazásokhoz az Azure App Service-ben, a helykódot, a gyökérmappa-szerkezetében is kezelheti. Meglévő eszközök, például a folyamatos integrációt és telepítést, vagy egyéni telepítési parancsfájl ennek transpilation kód vagy deploy idő csomag telepítése.

> [!NOTE]
> Ügyeljen arra, hogy központi telepítése a `host.json` fájlt, és mappák közvetlenül működéséhez a `wwwroot` mappát. Nem tartalmaznak a `wwwroot` mappába a központi telepítés. Ellenkező esetben, végül `wwwroot\wwwroot` mappák. 
> 
> 

## <a id="fileupdate"></a>Függvények app fájl frissítése
A függvény szerkesztő beépítve az Azure-portál lehetővé teszi, hogy frissítse a *function.json* fájl- és a kódfájl függvény esetében. Töltse fel, vagy más, mint frissítését *package.json* vagy *project.json* vagy függőségek, akkor használjon más telepítési módszert.

Függvény alkalmazások beépített App Service, így minden a [szokásos webes alkalmazásokra mutató elérhető telepítési lehetőségeket](../app-service/app-service-deploy-local-git.md) függvény alkalmazások esetében is elérhetők. Az alábbiakban néhány módszer feltöltése vagy függvény alkalmazást frissítőfájlok használható. 

#### <a name="to-use-app-service-editor"></a>App Service-szerkesztő segítségével
1. Az Azure Functions portálon kattintson **Platform funkciói**.
2. Az a **FEJLESZTŐESZKÖZÖK** kattintson **App Service-szerkesztő**.   
   App Service-szerkesztő betöltése után megjelenik a *host.json* fájl- és függvény mappáit *wwwroot*. 
5. Nyissa meg a fájlok szerkeszthetők, vagy húzza és eltávolítása a következő fájlok feltöltése a fejlesztési számítógépén.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>A függvény app SCM (Kudu) végpont használata
1. Nyissa meg: `https://<function_app_name>.scm.azurewebsites.net`.
2. Kattintson a **konzol Debug > CMD**.
3. Navigáljon a `D:\home\site\wwwroot\` frissítése *host.json* vagy `D:\home\site\wwwroot\<function_name>` egy függvény fájlok frissítéséhez.
4. Fogd és vidd egy fájlt szeretne feltölteni a fájlt rácsban a megfelelő mappába. Nincsenek a két területen a fájl rácsban, ha egy fájl elvetné. A *.zip* fájlok, megjelenik egy címkével ellátott "húzzon ide, és csomagolja ki." Minden olyan fájltípus esetében dobja el a fájl rácsban, de a "csomagolja ki" mezőben kívül.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="to-use-continuous-deployment"></a>Folyamatos üzembe helyezés használata
Kövesse az utasításokat a következő témakör [folyamatos üzembe helyezés az Azure Functions](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Párhuzamos végrehajtás
Több eseményindító események bekövetkezésekor gyorsabb, mint az egyszálas függvény futtatókörnyezettel is dolgozza fel őket, a futtatókörnyezet alkalmazhatja a függvény párhuzamosan több alkalommal.  Ha egy függvény alkalmazás használja a [üzemeltetési terv fogyasztás](functions-scale.md#how-the-consumption-plan-works), a függvény alkalmazás automatikusan sikerült kiterjesztése.  Függvény alkalmazás egyes példányainak hogy az alkalmazás fut-e a felhasználásra vonatkozó üzemeltetési terv vagy szokványos [az alkalmazásszolgáltatási csomag üzemeltetési](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), előfordulhat, hogy feldolgozni egyidejű függvény meghívásához párhuzamosan több szálat használ.  Maximális száma párhuzamos függvény meghívásához minden függvény alkalmazáspéldány használt eseményindítót, valamint egyéb funkciók, a függvény alkalmazásban által használt erőforrások típusú függ.

## <a name="functions-runtime-versioning"></a>Funkciók futásidejű versioning

Konfigurálhatja a funkciók futásidejű használatával verzióját a `FUNCTIONS_EXTENSION_VERSION` Alkalmazásbeállítás. Például a "~ 1" érték azt jelzi, hogy a függvény App 1 fog használni a fő verziószáma. Függvény alkalmazások verzióra, hogy minden új kisebb vannak. További információ, beleértve a függvény app pontos verziójának megtekintése: [bemutatásához az Azure Functions futásidejű verziók](set-runtime-version.md).

## <a name="repositories"></a>Adattárak
A kód az Azure Functions nyílt forráskódú, és a GitHub-adattárak tárolja:

* [Az Azure Functions futtatókörnyezettel](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Az Azure Functions portálra](https://github.com/projectkudu/AzureFunctionsPortal)
* [Az Azure Functions sablonokkal](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Azure WebJobs SDK Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Kötések
Ez az összes támogatott kötések tábla.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Jelentéskészítési problémák
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>További lépések
További információkért lásd a következőket:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions C# fejlesztői leírás](functions-reference-csharp.md)
* [Az Azure Functions F # fejlesztői leírás](functions-reference-fsharp.md)
* [Az Azure Functions NodeJS fejlesztői leírás](functions-reference-node.md)
* [Az Azure Functions eseményindítók és kötések](functions-triggers-bindings.md)
* [Az Azure Functions: Az út](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) az Azure App Service csapatának blogjában. Hogyan jött létre az Azure Functions előzményeit.

