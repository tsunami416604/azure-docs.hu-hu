---
title: Node. js-modulok használata
description: Megtudhatja, hogyan dolgozhat a Node. js-modulokkal Azure App Service vagy Cloud Services használatakor.
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
ms.openlocfilehash: 8621dde43ff38cee15feb1d30b6ea80ab681c855
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594780"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>A Node.js modulok használata az Azure alkalmazásokkal
Ez a dokumentum útmutatást nyújt a Node. js-modulok használatáról az Azure-ban üzemeltetett alkalmazásokkal. Útmutatást nyújt annak biztosításához, hogy az alkalmazás a modul egy adott verzióját használja, valamint natív modulokat használjon az Azure-ban.

Ha már ismeri a Node. js-modulok, a **Package. JSON** és a **NPM-Shrinkwrap. JSON** fájlok használatát, a következő információk gyors összefoglalást biztosítanak a cikkben tárgyalt témakörökből:

* A Azure App Service a **Package. JSON** és a **NPM-Shrinkwrap. JSON** fájlokat értelmezi, és a fájlok bejegyzései alapján is telepíthet modulokat.

* Az Azure Cloud Services a fejlesztői környezetre telepítendő összes modult, valamint a központi telepítési csomag részét képező **csomópont\_-modulok** könyvtárat várja. Lehetővé teheti a modulok telepítésének támogatását a Cloud Services **Package. JSON** vagy **NPM-Shrinkwrap. JSON** fájlok használatával. Ehhez a konfigurációhoz azonban a Cloud Service-projektek által használt alapértelmezett parancsfájlok testreszabására van szükség. A környezet konfigurálásának példáját lásd: az [Azure indítási feladata a NPM-telepítés futtatásához a Node-modulok üzembe helyezésének elkerülése](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) érdekében

> [!NOTE]
> Az Azure Virtual Machines ebben a cikkben nem tárgyaljuk, mivel a virtuális gépek üzembe helyezési élménye függ a virtuális gép által üzemeltetett operációs rendszertől.
> 
> 

## <a name="nodejs-modules"></a>Node. js-modulok
A modulok olyan betölthető JavaScript-csomagok, amelyek konkrét funkciókat biztosítanak az alkalmazás számára. A modulokat általában a **NPM** parancssori eszközzel kell telepíteni, azonban egyes modulok (például a HTTP-modul) a Core Node. js-csomag részeként vannak megadva.

A modulok telepítésekor a rendszer a Node modules ( **csomópont\_-modulok** ) könyvtárban tárolja azokat az alkalmazási könyvtár struktúrájának gyökerében. A **\_csomópont moduljainak** könyvtára minden modulja saját könyvtárat tart fenn, amely az általa függő modulokat tartalmazza, és ez a viselkedés minden modul esetében ismétlődik, egészen a függőségi láncig. Ez a környezet lehetővé teszi, hogy a telepített modulok saját verzióra vonatkozó követelményekkel rendelkezzenek a modulokra vonatkozóan, amelyektől függnek, azonban nagy méretű címtár-struktúrát eredményezhet.

Ha az alkalmazás részeként telepíti a **Node\_** modules könyvtárat, az egy **Package. JSON** vagy **NPM-Shrinkwrap. JSON** fájl használatával összehasonlítva növeli az üzemelő példány méretét. azonban garantálja, hogy az éles környezetben használt modulok verziói ugyanazok, mint a fejlesztés során használt modulok.

### <a name="native-modules"></a>Natív modulok
Habár a legtöbb modul egyszerűen egyszerű szöveges JavaScript-fájlok, egyes modulok platform-specifikus bináris lemezképek. Ezek a modulok a telepítéskor, általában a Python és a Node-GYP használatával vannak lefordítva. Mivel az Azure Cloud Services az alkalmazás részeként üzembe helyezett **Node\_** modules mappa használatára támaszkodik, a telepített modulok részét képező natív moduloknak működniük kell egy felhőalapú szolgáltatásban, feltéve, hogy a telepítése és lefordítása egy Windows-fejlesztési rendszeren történt.

A Azure App Service nem támogatja az összes natív modult, és a modulok adott előfeltételekkel való fordításakor sikertelen lehet. Míg egyes népszerű modulok, például a MongoDB opcionális natív függőségekkel rendelkeznek, és ezek nélkül is működnek, két megkerülő megoldás sikeresnek bizonyult szinte minden jelenleg elérhető natív modul esetében:

* Futtassa a **NPM-telepítést** olyan Windows rendszerű gépen, amelyen telepítve van az összes natív modul előfeltétele. Ezután telepítse az alkalmazás részeként a létrehozott **Node\_** modules mappát Azure app Service.

  * A fordítás előtt győződjön meg róla, hogy a helyi Node. js-telepítésnek megfelelő architektúrával rendelkezik, és a verzió a lehető leghamarabb elérhető az Azure-ban (az aktuális értékek a tulajdonságok **folyamat. Arch** és **Process. Version**).

* A Azure App Service konfigurálható egyéni bash-vagy rendszerhéj-parancsfájlok futtatására az üzembe helyezés során, így egyéni parancsok hajthatók végre, és pontosan konfigurálhatók a **NPM telepítésének** módja. A környezet konfigurálását bemutató videó: [Egyéni webhely-telepítési parancsfájlok a kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Package. JSON fájl használata

A **Package. JSON** fájl segítségével megadhatja az alkalmazás által igényelt legfelső szintű függőségeket, hogy az üzemeltetési platform telepítse a függőségeket, ahelyett, hogy a telepítés részeként fel kellene vennie a **Node\_** modules mappát. Az alkalmazás telepítése után a **NPM install** paranccsal elemezheti a **Package. JSON** fájlt, és telepítheti a felsorolt függőségeket.

A fejlesztés során a **--Save**, a **--Save-dev**vagy a **--Save-optional** paramétereket használhatja a modulok telepítésekor, ha a modulhoz bejegyzést szeretne hozzáadni a **Package. JSON** fájlhoz. További információ: [NPM-install](https://docs.npmjs.com/cli/install).

A **Package. JSON** fájl egyik lehetséges problémája, hogy csak a legfelső szintű függőségek verzióját adja meg. Az egyes telepített modulok esetleg nem határozzák meg, hogy milyen verziójú modulokra van szükség, és így lehetséges, hogy a fejlesztés során használttól eltérő függőségi láncot használ.

> [!NOTE]
> Ha a Azure App Service telepítésekor a <b>Package. JSON</b> fájl egy natív modulra hivatkozik, az alkalmazás a git használatával történő közzétételekor az alábbi példához hasonló hibaüzenet jelenhet meg:
> 
> NPM ERR! module-name@0.6.0telepítés: "Node-GYP configure build"
> 
> NPM ERR! a "CMD"/c "" Node-GYP configure build "" nem sikerült 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>NPM-Shrinkwrap. JSON fájl használata
A **NPM-Shrinkwrap. JSON** fájl megkísérli a **Package. JSON** fájl modul-verziószámozási korlátainak megoldását. Míg a **Package. JSON** fájl csak a legfelső szintű modulok verzióit tartalmazza, a **NPM-Shrinkwrap. JSON** fájl tartalmazza a teljes modul függőségi láncának verziószámát.

Ha az alkalmazás készen áll az éles környezetben való használatra, zárolhatja a verzióra vonatkozó követelményeket, és létrehozhat egy **NPM-Shrinkwrap. JSON** fájlt a **NPM Shrinkwrap** parancs használatával. Ez a parancs a **Node\_** modules mappában jelenleg telepített verziókat fogja használni, és ezeket a verziókat a **NPM-Shrinkwrap. JSON** fájlba rögzíti. Miután az alkalmazást telepítette az üzemeltetési környezetbe, a **NPM install** parancs használatával elemezheti a **NPM-Shrinkwrap. JSON** fájlt, és telepítheti a felsorolt függőségeket. További információ: [NPM-Shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ha a Azure App Service telepítésekor a <b>NPM-Shrinkwrap. JSON</b> fájl egy natív modulra hivatkozik, az alkalmazás a git használatával történő közzétételekor az alábbi példához hasonló hibaüzenet jelenhet meg:
> 
> NPM ERR! module-name@0.6.0telepítés: "Node-GYP configure build"
> 
> NPM ERR! a "CMD"/c "" Node-GYP configure build "" nem sikerült 1
> 
> 

## <a name="next-steps"></a>További lépések
Most, hogy megértette, hogyan használhatja a Node. js-modulokat az Azure-ban, megtudhatja, hogyan [adhatja meg a](https://github.com/squillace/nodejs-microservice)Node. js-verziót, hogyan [építhet ki és helyezhet üzembe egy Node. js-webalkalmazást](app-service/app-service-web-get-started-nodejs.md), és [hogyan használhatja az Azure parancssori felületet Mac és Linux rendszereken](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

További információk: [Node.js fejlesztői központ](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
