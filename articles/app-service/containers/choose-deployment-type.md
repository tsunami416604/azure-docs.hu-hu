---
title: Az Azure App Service üzemelő Linux - egyéni lemezképet, több tároló vagy beépített platformlemezképet?  | Microsoft Docs
description: Az App Service Linux rendszeren egyéni Docker-tároló központi telepítés, több tároló és a beépített alkalmazási keretrendszer között kiválasztásáról
keywords: az Azure app service, webalkalmazás, linux, oss
services: app-service
documentationCenter: ''
authors: msangapu
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.openlocfilehash: 012f78fc07f237e8ed532246c81a3c86bb6ab4ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Kép: egyéni, több tároló vagy beépített platformlemezképet?

[App Service Linux](app-service-linux-intro.md) három különböző elérési útját a közzétett webes alkalmazás első kínál:

- **Kép: egyéni központi telepítés**: "Dockerize" az alkalmazás, amely tartalmazza az összes fájlt és egy futtatásra kész csomag függőségeit Docker lemezképpel.
- **Több tároló telepítési**: "Dockerize" az alkalmazás a Docker Compose vagy Kubernetes konfigurációs fájl használatával több tároló között. További információkért lásd: [több tároló alkalmazás](#multi-container-apps-supportability).
- **A beépített platformlemezkép alkalmazástelepítés**: A beépített platform lemezképek tartalmazzák a közös web app futtatókörnyezetek és függőségei, mint például a csomópont és a PHP. A használatára a [Azure App Service-telepítési módszerek](../app-service-deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) az alkalmazás üzembe helyezése a webalkalmazás-tároló, majd egy beépített platformlemezképet futtatni.

##<a name="which-method-is-right-for-your-app"></a>Melyik módszert részesíti helyes az alkalmazáshoz? 

Az elsődleges tényezőket kell figyelembe venni a következők:

- **A fejlesztési munkafolyamat Docker rendelkezésre állásának**: egyéni lemezkép fejlesztési a Docker fejlesztési munkafolyamat alapszintű ismerete szükséges. Egy egyéni lemezkép telepítéséhez szükséges az egyéni lemezképet, például a Docker Hub tárház gazdagéphez közzétételét. Ha ismeri a Docker és Docker feladatok adhat hozzá a build munkafolyamat, vagy a már közzétett egy Docker-lemezképet, az alkalmazás, egyéni lemezkép szinte biztosan nem a legjobb választás.
- **Többrétegű architektúra**: például egy webes alkalmazás réteg és egy API réteget képességek külön több tároló használatával több tároló üzembe helyezése. 
- **Az alkalmazások teljesítményének**: a több tároló alkalmazásokhoz, például a Redis gyorsítótár réteg használatával teljesítményének növelése érdekében. Ennek eléréséhez több tároló választása.
- **Egyedi futásidejű követelmények**: A beépített platform képek legtöbb webalkalmazások igényeinek, de a korlátozott, a testreszabhatóság miatt. Előfordulhat, hogy az alkalmazás egyedi függőségekkel vagy más, mint mi a beépített képek képesek futásidejű követelményekkel.
- **Build követelmények**: A [folyamatos üzembe helyezés](../app-service-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), letölthető az alkalmazás működik, és az Azure közvetlenül forráskódot. Nincsenek külső build vagy kiadvány folyamat szükség. Van azonban a megadott korlát a testreszabhatóság miatt és build tools belül rendelkezésre állását a [Kudu](https://github.com/projectkudu/kudu/wiki) telepítési motor. Az alkalmazás előfordulhat, hogy elszaporodó Kudu tartozó képességek, függőségeit vagy egyéni build logika követelményei növekedésével.
- **Lemez olvasási/írási követelmények**: minden webalkalmazások tárolókötetet foglal le a webes tartalomhoz. Ezt a kötetet, Azure Storage által támogatott csatlakoztatva van a `/home` a az alkalmazás fájlrendszer. Ellentétben a tároló fájlrendszer fájlokat a tartalom lévő fájlok elérhetők minden méretezési példányára egy alkalmazás, és alkalmazás újraindításainál módosítások megmaradnak. Azonban a tartalom kötet lemez késleltetése nagyobb és több változót, mint a helyi tároló fájlrendszer és a hozzáférés a várakozási is negatív hatással lehet a hálózati kapcsolat hibái, ügyfélfelügyeletiplatform-frissítésekre és nem tervezett leállás. Tartalomfájlok nagy mennyiségű olvasási hozzáférést igénylő alkalmazások előnyös lehet az egyéni lemezképet a központi telepítési, a lemezkép fájlrendszer ahelyett, hogy a tartalom köteten lévő fájlok helyezi.
- **Erőforrás-használat Build**: egy alkalmazás telepítésekor forrásból származó, a központi telepítési parancsfájlok futtatása a Kudu használatával az azonos App Service-csomag számítási és tárolási erőforrásokat a futó alkalmazást. Nagy az alkalmazások telepítésének további erőforrások és kívánatosnál nagyobb idő lehet használni. Számos központi telepítési munkafolyamatai különösen a app tartalom köteten, amely nincs optimalizálva az ilyen tevékenység nehéz lemezműveletek hoz létre. Egyéni lemezkép kézbesíti az alkalmazást a fájlok és függőségek összes Azure nincs szükség további fájlok átvitele esetén vagy a telepítési műveletek a csomagban.
- **Gyors közelítés kell**: egy alkalmazás Dockerizing további build lépést igényel. A módosítások érvénybe lépéséhez az új lemezképet kell leküldése minden frissítés egy-tárházat. Ezek a frissítések vannak majd le kell az Azure környezetbe. Ha egy beépített tárolót felel meg az alkalmazás igényeinek, forrásból telepítése kínálhat gyorsabb fejlesztés munkafolyamat.

## <a name="multi-container-apps-supportability"></a>Több tároló alkalmazások támogatási lehetőségek

### <a name="supported-docker-compose-configuration-options"></a>Támogatott Docker Compose konfigurációs beállítások
- a parancs
- belépési pont
- environment
- Kép
- Portok
- Indítsa újra a
- Szolgáltatások
- Kötetek

### <a name="unsupported-docker-compose-configuration-options"></a>Nem támogatott Docker Compose konfigurációs beállítások
- Build (nem engedélyezett.)
- depends_on (figyelmen kívül hagyva)
- hálózatok (figyelmen kívül hagyva)
- a titkos kulcsokat (figyelmen kívül hagyva)
- 80-as és a 8080-as (figyelmen kívül hagyva) eltérő port

> [!NOTE]
> Nem kifejezetten feltüntettük az egyéb beállításokat is figyelmen kívül hagyja a nyilvános előzetes verziójához.

### <a name="supported-kubernetes-configuration-options"></a>Támogatott Kubernetes konfigurációs beállítások
- argumentum
- a parancs
- tárolók
- Kép
- név
- Portok
- egyedi

> [!NOTE]
>Bármely más Kubernetes lehetőség nincs kifejezetten feltüntettük nyilvános előzetes verziójában nem támogatott.
>