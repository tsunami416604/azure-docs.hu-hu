---
title: "Node.js modulok használata"
description: "Útmutató az Azure App Service és a Felhőszolgáltatások használata a Node.js modulok használata."
services: 
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: 
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 76679ea0ff2c1e88d1923488717a245351437165
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="using-nodejs-modules-with-azure-applications"></a>A Node.js modulok használata az Azure alkalmazásokkal
Ez a dokumentum útmutatást nyújt a Node.js modulok használata az Azure-platformon futó alkalmazások. beleértve annak biztosítását, hogy az alkalmazás a modul egy adott verzióját használja, továbbá a natív modulok az Azure-ral való használatát.

Ha már ismeri a Node.js modulok használata **package.json** és **npm-shrinkwrap.json** fájlok, ez a cikk ismertet gyors összegzését tartalmazza a következő információkat:

* Az Azure App Service megértette **package.json** és **npm-shrinkwrap.json** fájlok, illetve az ezen fájlokban található bejegyzések alapuló modult telepítheti.

* Azure Cloud Services várt kell telepíteni a fejlesztési környezet összes modul és a **csomópont\_modulok** directory a központi telepítési csomag része. Lehetséges modulokkal telepítése támogatásának engedélyezése **package.json** vagy **npm-shrinkwrap.json** fájlokat a Felhőszolgáltatások; azonban ez a konfiguráció szükséges az alapértelmezett testreszabása Cloud Service projektek által használt parancsfájlok. Példa bemutatja, hogyan konfigurálhatja az ebben a környezetben, lásd: [Azure indítási feladat npm-telepítés elkerülése érdekében a csomópont-modulok telepítése](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Az Azure virtuális gépek nem ismertetése a cikkben, mivel a telepítési élményt nyújt a virtuális gépen az operációs rendszer a virtuális gép által üzemeltetett függ.
> 
> 

## <a name="nodejs-modules"></a>A node.js modulok
A modulokra betölthető JavaScript-csomagok, amelyek bizonyos funkciók biztosítanak az alkalmazáshoz. Modulok általában segítségével telepíthetők a **npm** parancssori eszközzel, azonban néhány modulokat (például a http-modulja) Node.js core csomag részeként biztosított.

Amikor modulok telepítve vannak, tárolásuk a **csomópont\_modulok** könyvtárhoz, az alkalmazás könyvtárstruktúrát gyökerében. Minden modul belül a **csomópont\_modulok** directory kezeli a saját **csomópont\_modulok** függ, és ez a viselkedés ismétlődik a modulokat tartalmazó könyvtárat minden modul egészen a függőségi láncból le. Ebben a környezetben lehetővé teszi, hogy a telepítés után a saját verziójával kapcsolatos követelmények a modulok függ, azonban igen nagy könyvtárstruktúrát eredményezhet modulokhoz.

Központi telepítése a **csomópont\_modulok** könyvtárába, az alkalmazás részei a központi telepítés használatával mérete növekszik a **package.json** vagy  **npm-shrinkwrap.json** azonban azt garantálja, hogy az éles környezetben használt modulok verziói ugyanazok, mint a modulok használják a fejlesztési; fájlt.

### <a name="native-modules"></a>A natív modulok
A legtöbb modulokra egyszerűen csak egyszerű szöveges JavaScript-fájlt, miközben néhány modulokra platformspecifikus bináris képek. Ezek a modulok összeállítása telepítésekor, általában a Python és a csomópont-gyp. Mivel Azure Felhőszolgáltatások támaszkodnak az **csomópont\_modulok** mindaddig, amíg telepítve lett az alkalmazás, minden natív modul, a telepített modulok részét képező részeként telepített mappa működnek felhőszolgáltatásban és a Windows fejlesztői rendszeren lefordított.

Az Azure App Service nem támogatja a natív modulok, és meghiúsulhat, ha a konkrét Előfeltételek modulok fordítása. Néhány népszerű modulokat, például a MongoDB választható natív függőségekkel rendelkeznek, és ezeket nem működnek, két lehetséges megoldások napjainkban szinte minden elérhető natív modul sikeres bizonyult:

* Futtatás **npm telepítése** egy Windows-gépen, amelyen a natív modul minden előfeltétel telepítve van. Ezután telepítse a létrehozott **csomópont\_modulok** mappa az Azure App Service alkalmazás részeként.

  * Előtt fordítása, ellenőrizze, hogy a helyi Node.js telepítési rendelkezik a megfelelő architektúra, és a lehető egy Azure-ban használt verziója (az aktuális értékek ellenőrizhetők tulajdonságokból Runtime **process.arch** és **process.version**).

* Az Azure App Service beállítható úgy, hogy a telepítés során egyéni bash vagy rendszerhéj-parancsfájlok végrehajtása felkínálva egyéni parancsokat hajthat végre, és pontosan állítania **npm telepítése** futtatták. A videó bemutatja, hogyan konfigurálja, hogy a környezetet, lásd: [egyéni webhely üzembe helyezési parancsfájlok a Kudu].

### <a name="using-a-packagejson-file"></a>A package.json fájl használata

A **package.json** fájl egy módszerre, amellyel az alkalmazás által igényelt, a gazdaplatform telepíthetik a függőségek ahelyett, hogy nincs szükség a következők legfelső szintű függőségek a **csomópont\_ modulok** mappába a központi telepítésének részeként. Miután az alkalmazás telepítve van, a **npm telepítése** parancs segítségével elemezni a **package.json** fájlt, és telepítse a felsorolt függőségei.

Fejlesztési, használhatja a **--mentése**, **--mentés-fejlesztői**, vagy **--mentés választható** paraméterek, a modul bejegyzése hozzáadása a moduloktelepítésekor**package.json** automatikusan fájlt. További információkért lásd: [npm-telepítés](https://docs.npmjs.com/cli/install).

Egy potenciális problémát, amelynek a **package.json** fájl csak meghatározza a felső szintű függőségek verzió. Minden telepített modulokban előfordulhat, hogy, vagy attól függ, a modulok verziója nem adható meg, és így, akkor előfordulhat, hogy akkor is szükségessé tehet, mint egy fejlesztési szerepel egy másik függőségi láncból.

> [!NOTE]
> Ha Azure App Service-ben való üzembe helyezés esetén a <b>package.json</b> fájl hivatkozik a natív modult az alábbi példához hasonló hiba jelenhet meg, a Git használatával alkalmazás közzététele során:
> 
> npm hiba! module-name@0.6.0telepítés: "csomópont-gyp konfigurálása build"
> 
> npm hiba! "cmd"/ c""csomópont-gyp konfigurálása build"" sikertelen 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Npm-shrinkwrap.json fájl használatával
A **npm-shrinkwrap.json** fájl modul versioning vonatkozó korlátozások elhárítása kísérlet a **package.json** fájlt. Amíg a **package.json** fájl csak a legfelső szintű modulok-verziók szerepelnek a **npm-shrinkwrap.json** fájl tartalmazza a teljes modul függőségi láncból verzió követelményei.

Amikor az alkalmazás készen áll a termelési, verzióra vonatkozó követelmények zárolását, és hozzon létre egy **npm-shrinkwrap.json** fájl használatával a **npm shrinkwrap** parancsot. Ez a parancs fogja használni a jelenleg telepített verzió a **csomópont\_modulok** mappa, és jegyezze fel az ezen verziói a **npm-shrinkwrap.json** fájlt. Az alkalmazás az üzemeltetési környezetbe telepítése után a **npm telepítése** parancs segítségével elemezni a **npm-shrinkwrap.json** fájlt, és telepítse a felsorolt függőségei. További információkért lásd: [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ha Azure App Service-ben való üzembe helyezés esetén a <b>npm-shrinkwrap.json</b> fájl hivatkozik a natív modult az alábbi példához hasonló hiba jelenhet meg, a Git használatával alkalmazás közzététele során:
> 
> npm hiba! module-name@0.6.0telepítés: "csomópont-gyp konfigurálása build"
> 
> npm hiba! "cmd"/ c""csomópont-gyp konfigurálása build"" sikertelen 1
> 
> 

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte a Node.js modulok használata Azure, megtudhatja, hogyan [a Node.js verzió megadása], [és üzembe egy Node.js-webalkalmazás](app-service/app-service-web-get-started-nodejs.md), és [az Azure parancssori használata Felület Mac és Linux].

További információk: [Node.js fejlesztői központ](/nodejs/azure/).

[a Node.js verzió megadása]: nodejs-specify-node-version-azure-apps.md
[az Azure parancssori használata Felület Mac és Linux]:cli-install-nodejs.md
[egyéni webhely üzembe helyezési parancsfájlok a Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
