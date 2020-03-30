---
title: Node.js modulok kal való együttműködés
description: Ismerje meg, hogyan dolgozhat a Node.js modulokkal az Azure App Service vagy a Cloud Services használata esetén.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70309273"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>A Node.js modulok használata az Azure alkalmazásokkal
Ez a dokumentum útmutatást nyújt a Node.js modulok azure-beli üzemeltetett alkalmazásokkal való használatával kapcsolatban. Útmutatást nyújt annak biztosításához, hogy az alkalmazás egy modul egy adott verzióját használja, valamint natív modulokat használjon az Azure-ral.

Ha már ismeri a Node.js modulok, **a package.json** és **az npm-shrinkwrap.json** fájlok használatát, az alábbi információk röviden összefoglalják a cikkben tárgyalt információkat:

* Az Azure App Service megérti **a package.json** és **az npm-shrinkwrap.json** fájlokat, és modulokat telepíthet a fájlok bejegyzései alapján.

* Az Azure Cloud Services elvárja, hogy az összes modul telepítve legyen a fejlesztői környezetben, és a **\_csomópontmodulok könyvtára** szerepelni fog a központi telepítési csomag részeként. Lehetőség van a modulok telepítésének engedélyezésére **a package.json** vagy **az npm-shrinkwrap.json** fájlok használatával a Cloud Services szolgáltatásban; ez a konfiguráció azonban a Cloud Service-projektek által használt alapértelmezett parancsfájlok testreszabását igényli. A környezet konfigurálásának például az [Npm-telepítés futtatásával az Azure Indítási feladatcímű témakörben található a csomópontmodulok telepítésének elkerülése érdekében.](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Az Azure virtuális gépek nem ismertetik ebben a cikkben, mivel a virtuális gép üzembe helyezési élménye a virtuális gép által üzemeltetett operációs rendszer függ.
> 
> 

## <a name="nodejs-modules"></a>Node.js modulok
A modulok betölthető JavaScript-csomagok, amelyek az alkalmazás speciális funkcióit biztosítják. A modulok általában az **npm** parancssori eszközzel vannak telepítve, azonban néhány modul (például a http modul) a Node.js alapcsomag részeként szolgál.

A modulok telepítésekor **a\_** rendszer az alkalmazáskönyvtár szerkezetének gyökérkönyvtárában tárolja őket. A **csomópontmodulok\_** könyvtárában lévő minden modul saját könyvtárat tart fenn, amely minden olyan modult tartalmaz, amelytől függ, és ez a viselkedés minden modulesetében ismétlődik a függőségi láncban. Ez a környezet lehetővé teszi, hogy minden telepített modul saját verziókövetelményekkel rendelkezik a modulokhoz, amelyektől függ, azonban meglehetősen nagy könyvtárstruktúrát eredményezhet.

A **\_csomópontmodulok** könyvtárának az alkalmazás részeként történő üzembe helyezése növeli a központi telepítés méretét a **package.json** vagy **az npm-shrinkwrap.json** fájl használatával összehasonlítva; ugyanakkor garantálja, hogy a gyártássorán használt modulok verziói megegyeznek a fejlesztéssorán használt modulokkal.

### <a name="native-modules"></a>Natív modulok
Míg a legtöbb modul egyszerűen egyszerű szöveges JavaScript fájlokat, egyes modulok platform-specifikus bináris képeket. Ezek a modulok fordítása telepítéskor történik, általában python és csomópont-gyp használatával. Mivel az Azure Cloud Services az alkalmazás részeként üzembe helyezett **csomópontmodulok\_** mappájára támaszkodik, a telepített modulok részét képező bármely natív modulnak felhőszolgáltatásban kell működnie, amennyiben az telepítve van és le van fordítva egy Windows fejlesztői rendszeren.

Az Azure App Service nem támogatja az összes natív modult, és előfordulhat, hogy sikertelen, ha modulokat állít össze speciális előfeltételekkel. Míg néhány népszerű modulok, mint a MongoDB opcionális natív függőségek és a munka finom nélkülük, két kerülő megoldás sikeresnek bizonyult szinte minden natív modulok ma elérhető:

* **Futtassa az npm telepítést** olyan Windows-gépen, amelyen telepítve van a natív modul összes előfeltétele. Ezután telepítse a létrehozott **\_csomópontmodulok mappát** az alkalmazás részeként az Azure App Service-be.

  * A fordítás előtt ellenőrizze, hogy a helyi Node.js telepítés megfelelő architektúrával rendelkezik-e, és a verzió a lehető legközelebb van-e az Azure-ban használt verzióhoz (az aktuális értékek ellenőrizhetők futásidőben a **properties process.arch** és **process.version címen).**

* Az Azure App Service beállítható egyéni bash- vagy rendszerhéj-parancsfájlok végrehajtására az üzembe helyezés során, így lehetősége van egyéni parancsok végrehajtására és az **npm-telepítés** futtatásának pontos konfigurálására. Az adott környezet konfigurálását bemutató videót az [Egyéni webhelytelepítési parancsfájlok kuduval című](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/)témakörben találja.

### <a name="using-a-packagejson-file"></a>Package.json fájl használata

A **package.json** fájl segítségével megadhatja az alkalmazás által igényelt legfelső szintű függőségeket, hogy a gazdaplatform telepíthesse a függőségeket, és nem kellene a **\_csomópontmodulok mappáját** a központi telepítés részeként megadnia. Az alkalmazás telepítése után az **npm telepítési** parancs a **package.json** fájl elemzésére és az összes felsorolt függőség telepítésére szolgál.

A fejlesztés során a **--save**, **--save-dev**vagy **--save-optional** paramétereket használhatja a modulok telepítésekor, hogy automatikusan hozzáadjon egy bejegyzést a modulhoz a **package.json** fájlhoz. További információ: [npm-install](https://docs.npmjs.com/cli/install).

A **package.json** fájl egyik lehetséges problémája, hogy csak a legfelső szintű függőségek verzióját adja meg. Minden telepített modul megadhatja a modulok verzióját, amelytől függ, és így lehetséges, hogy a fejlesztéshez használttól eltérő függőségi lánccal végződhet.

> [!NOTE]
> Az Azure App Service-be való üzembe helyezéskor, ha a <b>package.json</b> fájl natív modulra hivatkozik, előfordulhat, hogy az alkalmazás Git használatával történő közzétételekor az alábbi példához hasonló hibaüzenet jelenik meg:
> 
> npm ERR! module-name@0.6.0telepítés: "node-gyp konfigurálja a buildet"
> 
> npm ERR! 'cmd "/c" "csomópont-gyp konfigurálás build"" nem sikerült 1-gyp
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Npm-shrinkwrap.json fájl használata
Az **npm-shrinkwrap.json** fájl a **package.json** fájl modulverziós korlátozásainak kezelésére tett kísérlet. Míg a **package.json** fájl csak a legfelső szintű modulok verzióit tartalmazza, az **npm-shrinkwrap.json** fájl tartalmazza a teljes modulfüggőségi lánc verziókövetelményeit.

Ha az alkalmazás készen áll az éles környezetre, zárolhatja a verziókövetelményeket, és létrehozhat egy **npm-shrinkwrap.json** fájlt az **npm shrinkwrap** parancs használatával. Ez a parancs a **\_csomópontmodulok** mappájában jelenleg telepített verziókat fogja használni, és ezeket a verziókat az **npm-shrinkwrap.json** fájlba rögzíti. Miután az alkalmazást telepítette az üzemeltetési környezetbe, az **npm telepítési** parancs az **npm-shrinkwrap.json** fájl elemzésére és az összes felsorolt függőség telepítésére szolgál. További információ: [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Az Azure App Service-be való üzembe helyezéskor, ha az <b>npm-shrinkwrap.json</b> fájl natív modulra hivatkozik, az alkalmazás Git használatával történő közzétételekor a következő példához hasonló hibaüzenet jelenhet meg:
> 
> npm ERR! module-name@0.6.0telepítés: "node-gyp konfigurálja a buildet"
> 
> npm ERR! 'cmd "/c" "csomópont-gyp konfigurálás build"" nem sikerült 1-gyp
> 
> 

## <a name="next-steps"></a>További lépések
Most, hogy már tisztában van azzal, hogyan használhatja a Node.js modulokat az Azure-ral, ismerje meg, hogyan [adhatja meg a Node.js verziót,](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md) [hogyan hozhat létre és helyezhet üzembe egy Node.js webalkalmazást,](app-service/app-service-web-get-started-nodejs.md)valamint hogyan [használhatja a Mac és Linux Azure parancssori felületét.](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/)

További információk: [Node.js fejlesztői központ](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
