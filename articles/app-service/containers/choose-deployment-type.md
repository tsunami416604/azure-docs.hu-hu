---
title: Egyéni lemezkép, többtárolós vagy beépített rendszerkép üzembe helyezése – Azure App Service | Microsoft Docs
description: Döntés az egyéni Docker-tárolók üzembe helyezése, a többtárolós és a beépített alkalmazás-keretrendszer a Linux rendszeren való App Service
keywords: Azure app Service, Web App, Linux, OSS
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: ae28b185aa44ca22d59204826036435a10c64e91
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066792"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Egyéni rendszerkép, több tároló vagy beépített platform-rendszerkép?

A Linuxon futó [app Service](app-service-linux-intro.md) három különböző elérési utat biztosít az alkalmazás webes közzétételéhez:

- **Egyéni rendszerkép központi telepítése**: "Dockerize" az alkalmazás egy olyan Docker-rendszerképbe, amely tartalmazza az összes fájlt és függőséget egy használatra kész csomagban.
- **Több tárolós telepítés**: A "Dockerize" az alkalmazás több tárolón keresztül egy Docker-összeállítási konfigurációs fájl használatával.
- **Alkalmazás üzembe helyezése beépített platform rendszerképpel**: Beépített platform-rendszerképeink közös webalkalmazás-futtatókörnyezeteket és függőségeket tartalmaznak, például a Node és a PHP-t. A [Azure app Service üzembe](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) helyezési módszerek bármelyikével üzembe helyezheti az alkalmazást a webalkalmazás tárolójában, majd egy beépített platform-lemezkép használatával futtathatja azt.

## <a name="which-method-is-right-for-your-app"></a>Melyik módszer a legmegfelelőbb az alkalmazáshoz? 

A megfontolandó elsődleges tényezők a következők:

- A **Docker elérhetősége a fejlesztési**munkafolyamatban: Az egyéni rendszerkép-fejlesztés a Docker-fejlesztési munkafolyamat alapszintű ismeretét igényli. Az egyéni rendszerképeknek egy webalkalmazásba történő üzembe helyezéséhez az egyéni rendszerkép közzétételét kell megadnia egy adattár-gazdagéphez, például a Docker hub-hoz. Ha ismeri a Docker-t, és hozzá tud adni Docker-feladatokat a Build-munkafolyamathoz, vagy ha már közzétette az alkalmazást Docker-rendszerképként, az egyéni rendszerkép szinte minden bizonnyal a legjobb választás.
- **Többrétegű architektúra**: Helyezzen üzembe több tárolót, például egy webalkalmazási réteget és egy API-réteget a képességek elkülönítéséhez több tároló használatával. 
- **Alkalmazás teljesítménye**: Növelje a többtárolós alkalmazások teljesítményét egy olyan gyorsítótár-réteggel, mint például a Redis. A többszörös tároló lehetőség kiválasztásával érheti el ezt.
- **Egyedi futásidejű követelmények**: A beépített platform rendszerképeket úgy tervezték, hogy megfeleljenek a legtöbb webalkalmazás igényeinek, de a testreszabhatóság korlátozott. Előfordulhat, hogy az alkalmazás egyedi függőségekkel vagy más futásidejű követelményekkel rendelkezik, amelyek meghaladják a beépített lemezképek képességeit.
- **Felépítési követelmények**: A [folyamatos üzembe helyezéssel](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)az Azure-ban közvetlenül a forráskódból hozhatja létre és futtathatja az alkalmazást. Nincs szükség külső felépítési vagy közzétételi folyamatra. Azonban a [kudu](https://github.com/projectkudu/kudu/wiki) üzembe helyezési motoron belül a build-eszközök testreszabhatóság és rendelkezésre állása is korlátozott. Az alkalmazása növelheti a kudu képességeit, mivel azok függőségei vagy az egyéni Build logikára vonatkozó követelmények szerint növekszik.
- **Lemez olvasási/írási követelményei**: Az összes webalkalmazás a webes tartalmak tárolási kötetét osztja ki. Az Azure Storage által támogatott kötet az alkalmazás fájlrendszerében van csatlakoztatva `/home` . A tároló fájlrendszerében lévő fájlokkal ellentétben a tartalom kötetében lévő fájlok az alkalmazás összes méretezési példányán elérhetők, és a módosítások az alkalmazások újraindításakor is megmaradnak. A tartalom kötetének késése azonban nagyobb és változó, mint a helyi tároló fájlrendszerének késése, és a hozzáférés hatással lehet a platformok frissítései, a nem tervezett állásidő és a hálózati kapcsolati problémákra. Azok az alkalmazások, amelyeknek a tartalomhoz való nehezen olvasható hozzáférésre van szükségük, az egyéni rendszerkép központi telepítése is hasznos lehet, amely a tartalom kötetén található fájlokat a képfájlrendszerben helyezi el.
- **Erőforrás-használat létrehozása**: Ha egy alkalmazás a forrásból lett üzembe helyezve, a kudu által futtatott telepítési parancsfájlok ugyanazt a App Service tervezze meg a számítási és tárolási erőforrásokat a futó alkalmazásként. A nagyméretű alkalmazások telepítése több erőforrást vagy időt is igénybe vehet. A sok üzembe helyezési munkafolyamatok nagy mennyiségű lemezes tevékenységet hoznak az alkalmazás tartalmának kötetén, amely nincs optimalizálva ilyen tevékenységre. Az egyéni rendszerkép egyetlen csomagban biztosítja az alkalmazás összes fájlját és függőségét az Azure-ba, és nincs szükség további fájlátviteli vagy üzembe helyezési műveletekre.
- **Gyors iteráció szükséges**: Dockerizing egy alkalmazáshoz további Build lépések szükségesek. A módosítások érvénybe léptetéséhez le kell küldenie az új rendszerképet egy adattárba az egyes frissítésekkel. Ezeket a frissítéseket ezután az Azure-környezetbe kell húzni. Ha a beépített tárolók egyike megfelel az alkalmazás igényeinek, a forrásról való üzembe helyezés gyorsabb fejlesztési munkafolyamatot eredményezhet.

## <a name="next-steps"></a>További lépések

Egyéni tároló:
* [Egyéni tároló futtatása](quickstart-docker-go.md)

Több tároló:
* [Többtárolós alkalmazás létrehozása](quickstart-multi-container.md)

A következő cikkek bemutatják, hogyan kezdheti meg a Linuxon futó App Servicet a beépített platform rendszerképpel:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](quickstart-php.md)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)