---
title: Node.js modulok használata
description: Ismerje meg, hogyan dolgozhat a Node.js modulok használata az Azure App Service és a Cloud Services esetén.
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 045250f0b0f97cbefe05b36f1c8d4480244a172d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575847"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>A Node.js modulok használata az Azure alkalmazásokkal
Ez a dokumentum útmutatást nyújt a Node.js modulok használata az Azure-ban üzemeltetett alkalmazások. Útmutatást annak biztosítása, hogy az alkalmazás egy modul egy adott verzióját használja-e, valamint a natív modulok használata az Azure-ral.

Ha már jól ismerik a Node.js-modulok **package.json** és **npm-shrinkwrap.json** fájlok, a cikkben leírtak szerint gyors összegzést tartalmaz a következő információkat:

* Az Azure App Service Luis alapján megérti **package.json** és **npm-shrinkwrap.json** fájlokat, és telepítheti ezeket a fájlokat a bejegyzések alapján történő modulok.

* Az Azure Cloud Services vár kell telepíteni a fejlesztési környezet összes modult, és a **csomópont\_modulok** könyvtárat a központi telepítési csomag része. Lehetséges használatával modulok telepítése támogatásának engedélyezése **package.json** vagy **npm-shrinkwrap.json** fájlokat a Cloud Services; Ez a konfiguráció azonban megköveteli, az alapértelmezett testreszabása Cloud Service projektek által használt parancsfájlok. Ebben a környezetben konfigurálása egy példa: [Azure indítási feladat futtatásához npm install elkerülése érdekében a node-modulok telepítése](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Mivel a központi telepítési élmény, a virtuális gép függ az operációs rendszer, a virtuális gép által üzemeltetett Azure-beli virtuális gépek nem tárgyalja ebben a cikkben.
> 
> 

## <a name="nodejs-modules"></a>A node.js modulok
Modulokra oszlopszinten JavaScript-csomagok, amelyek az alkalmazás bizonyos funkciókat biztosítanak. Modulok általában használatával telepíthetők a **npm** parancssori eszközzel, azonban a core Node.js-csomag részeként biztosított egyes modulok (például a http-modulja).

Amikor modulok telepítve vannak, akkor vannak tárolva a **csomópont\_modulok** könyvtárat az alkalmazás könyvtárstruktúrát gyökérmappájában. Minden modul belül a **csomópont\_modulok** directory kezeli a saját könyvtár, amely tartalmazza a szükséges modulok attól függ, és minden modulhoz egészen le a függőségi lánc megismétli ezt a viselkedést. Ebben a környezetben lehetővé teszi, hogy minden egyes modul telepítve van, a saját verziója követelmények vonatkoznak a modulok attól függ, azonban meglehetősen nagy könyvtárstruktúrát eredményezhet.

Üzembe helyezése a **csomópont\_modulok** könyvtárat, az alkalmazás részeként a központi telepítés használatával képest méretét is megnöveli a **package.json** vagy  **az npm-shrinkwrap.json** azonban azt garantálja, hogy az éles környezetben használt modulok verziói ugyanazok, mint a fejlesztésre használt modulok; fájlt.

### <a name="native-modules"></a>Natív modulok
A legtöbb modul egyszerűen egyszerű szöveges JavaScript-fájlok, amelyek bizonyos modulokra platformspecifikus bináris lemezképek. Ezek a modulok összeállítása telepítésekor, általában a Python és a csomópont-gyp használatával. Mivel az Azure Cloud Services támaszkodnak az **csomópont\_modulok** mappa alatt az alkalmazás, minden natív modul, a telepített modulok részét részeként üzembe helyezett felhőszolgáltatásban működnie kell, amíg telepítve lett és a egy Windows fejlesztési rendszeren lefordított.

Az Azure App Service nem támogatja az összes natív modulokat, és konkrét előfeltételeket rendelkező modulok fordítása sikertelen lehet. Bár egyes népszerű modulok, például a MongoDB választható natív függőségekkel rendelkeznek, és anélkül, hogy azok megfelelően működnek, két lehetséges megoldások sikeresnek elérhető szinte minden natív modulok még ma:

* Futtatás **npm telepítése** egy Windows-gépen, amelyen a natív modul az összes előfeltétel telepítve van. Ezután helyezze üzembe a létrehozott **csomópont\_modulok** mappában található, mint az alkalmazás az Azure App Service része.

  * A fordítás, előtt ellenőrizze, hogy a helyi Node.js telepítése rendelkezik a megfelelő architektúra és a lehető egy Azure-ban használt verziója (az aktuális értékek ellenőrizhetők tulajdonságokból Runtime **process.arch** és **process.version**).

* Az Azure App Service beállítható úgy, hogy üzembe helyezés során az egyéni bash- vagy shell-szkript végrehajtása így egyéni parancsokat hajthat végre, és pontosan állítania **npm telepítése** futtatja. Videó: hogyan kell konfigurálni abban a környezetben, lásd: [a kudu használatával egyéni webhely-üzembehelyezési szkriptek](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Egy package.json fájllal

A **package.json** fájl egy módja az alkalmazás igényel, a üzemeltetési platformot telepíthetik a függőségeket, nem pedig határozhat meg, hogy tartalmazza a legfelső szintű függőségek megadása a **csomópont\_ modulok** mappát a központi telepítésének részeként. Az alkalmazás üzembe helyezését követően a **npm telepítése** parancs segítségével elemezni a **package.json** fájlt, és telepítse a felsorolt összes függőséget.

A fejlesztés során használhat a **--mentése**, **--save-fejlesztési**, vagy **– mentés választható** paramétereket adjon hozzá egy bejegyzést a modul a -moduloktelepítésesorán**package.json** automatikusan fájlt. További információkért lásd: [npm-telepítés](https://docs.npmjs.com/cli/install).

Egy lehetséges problémájára a **package.json** fájl, csak adja meg a verziót a legfelső szintű függőségekhez. Minden telepített modul is, vagy attól függ, a modulok verziója nem adható meg, és így lehetséges, hogy, előfordulhat, hogy végül egy eltérő függőségi lánc mint egy fejlesztési használatban.

> [!NOTE]
> Ha az Azure App Service-ben való üzembe helyezés esetén a <b>package.json</b> fájlt láthatja az alábbi példához hasonló hiba, amikor az alkalmazás a Git használatával tesz közzé a natív modulok hivatkozik:
> 
> npm hiba! module-name@0.6.0 telepítése: "node-gyp konfigurálása build"
> 
> npm hiba! "cmd"/ c""csomópont-gyp configure build"" 1. nem sikerült
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Az npm-shrinkwrap.json fájl használatával
A **npm-shrinkwrap.json** fájl, oldja meg a modul versioning korlátozásai tett kísérlet a **package.json** fájlt. Bár a **package.json** fájl csak a legfelső szintű modulok verzióit tartalmazza a **npm-shrinkwrap.json** fájl tartalmazza a teljes modul függőségi lánc verzió követelményeinek.

Amikor az alkalmazás készen áll a termelési, verziókövetelményekért zárolását, és hozzon létre egy **npm-shrinkwrap.json** fájl használatával a **npm shrinkwrap** parancsot. Ezzel a paranccsal a jelenleg telepített verziókat fogja használni a **csomópont\_modulok** mappát, és jegyezze fel ezeket a verziókat, a **npm-shrinkwrap.json** fájlt. Az alkalmazás, a üzemeltetési környezet telepítését követően a **npm telepítése** parancs segítségével elemezni a **npm-shrinkwrap.json** fájlt, és telepítse a felsorolt összes függőséget. További információkért lásd: [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ha az Azure App Service-ben való üzembe helyezés esetén a <b>npm-shrinkwrap.json</b> fájlt láthatja az alábbi példához hasonló hiba, amikor az alkalmazás a Git használatával tesz közzé a natív modulok hivatkozik:
> 
> npm hiba! module-name@0.6.0 telepítése: "node-gyp konfigurálása build"
> 
> npm hiba! "cmd"/ c""csomópont-gyp configure build"" 1. nem sikerült
> 
> 

## <a name="next-steps"></a>További lépések
Most, hogy megismerkedett a Node.js modulok használata az Azure-ral, megtudhatja, hogyan [adja meg a Node.js-verzió](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [létrehozása és üzembe helyezése a Node.js-webalkalmazás](app-service/app-service-web-get-started-nodejs.md), és [használata az Azure parancssori Felület Macre és Linuxra készült](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

További információk: [Node.js fejlesztői központ](/nodejs/azure/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
