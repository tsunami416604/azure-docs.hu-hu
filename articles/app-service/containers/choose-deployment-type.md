---
title: A Linux-tárolók üzembe helyezési lehetőségei
description: Döntse el az egyéni Docker-tárolók üzembe helyezését, a többtárolós és a Linuxon App Service beépített alkalmazási keretrendszerét.
keywords: Azure app Service, Web App, Linux, OSS
author: msangapu-msft
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c4025e393c196fb98455bac26e94e5f252840b9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74687515"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Egyéni rendszerkép, több tároló vagy beépített platform-rendszerkép?

A [Linuxon futó app Service](app-service-linux-intro.md) három különböző elérési utat biztosít az alkalmazás webes közzétételéhez:

- **Egyéni rendszerkép központi telepítése**: "Dockerize" az alkalmazás egy olyan Docker-rendszerképbe, amely az összes fájlt és függőséget tartalmazza egy használatra kész csomagban.
- **Többtárolós telepítés**: "Dockerize" alkalmazása több tárolón a Docker-összeállítás konfigurációs fájljának használatával.
- **Alkalmazás üzembe helyezése beépített platform-lemezképpel**: a beépített platform-rendszerképek közös webalkalmazás-futtatókörnyezeteket és függőségeket tartalmaznak, például a Node és a PHP-t. A [Azure app Service üzembe](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) helyezési módszerek bármelyikével üzembe helyezheti az alkalmazást a webalkalmazás tárolójában, majd egy beépített platform-lemezkép használatával futtathatja azt.

## <a name="which-method-is-right-for-your-app"></a>Melyik módszer a legmegfelelőbb az alkalmazáshoz? 

A megfontolandó elsődleges tényezők a következők:

- A **Docker rendelkezésre állása a fejlesztési munkafolyamatban**: az egyéni rendszerkép-fejlesztés a Docker fejlesztési munkafolyamatának alapszintű ismeretét igényli. Az egyéni rendszerképeknek egy webalkalmazásba történő üzembe helyezéséhez az egyéni rendszerkép közzétételét kell megadnia egy adattár-gazdagéphez, például a Docker hub-hoz. Ha ismeri a Docker-t, és hozzá tud adni Docker-feladatokat a Build-munkafolyamathoz, vagy ha már közzétette az alkalmazást Docker-rendszerképként, az egyéni rendszerkép szinte minden bizonnyal a legjobb választás.
- **Többrétegű architektúra**: helyezzen üzembe több tárolót, például egy webalkalmazási réteget és egy API-réteget a képességek elkülönítéséhez több tároló használatával. 
- **Alkalmazás teljesítménye**: növelje a többtárolós alkalmazás teljesítményét egy gyorsítótár-réteg, például a Redis használatával. A többszörös tároló lehetőség kiválasztásával érheti el ezt.
- **Egyedi futtatókörnyezeti követelmények**: a beépített platform rendszerképeket úgy tervezték, hogy megfeleljenek a legtöbb webalkalmazás igényeinek, de a testreszabhatóság korlátozott. Előfordulhat, hogy az alkalmazás egyedi függőségekkel vagy más futásidejű követelményekkel rendelkezik, amelyek meghaladják a beépített lemezképek képességeit.
- **Felépítési követelmények**: a [folyamatos üzembe helyezéssel](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)az alkalmazás az Azure-ban közvetlenül a forráskódból szerezhető be és fut. Nincs szükség külső felépítési vagy közzétételi folyamatra. Azonban a [kudu](https://github.com/projectkudu/kudu/wiki) üzembe helyezési motoron belül a build-eszközök testreszabhatóság és rendelkezésre állása is korlátozott. Az alkalmazása növelheti a kudu képességeit, mivel azok függőségei vagy az egyéni Build logikára vonatkozó követelmények szerint növekszik.
- **Lemezes olvasási/írási követelmények**: az összes webalkalmazáshoz tárolási kötet van lefoglalva a webes tartalomhoz. Az Azure Storage által támogatott kötet az `/home` alkalmazás fájlrendszerében van csatlakoztatva. A tároló fájlrendszerében lévő fájlokkal ellentétben a tartalom kötetében lévő fájlok az alkalmazás összes méretezési példányán elérhetők, és a módosítások az alkalmazások újraindításakor is megmaradnak. A tartalom kötetének késése azonban nagyobb és változó, mint a helyi tároló fájlrendszerének késése, és a hozzáférés hatással lehet a platformok frissítései, a nem tervezett állásidő és a hálózati kapcsolati problémákra. Azok az alkalmazások, amelyeknek a tartalomhoz való nehezen olvasható hozzáférésre van szükségük, az egyéni rendszerkép központi telepítése is hasznos lehet, amely a tartalom kötetén található fájlokat a képfájlrendszerben helyezi el.
- **Erőforrás-használat létrehozása**: Ha egy alkalmazás a forrásból lett üzembe helyezve, a kudu által futtatott telepítési parancsfájlok ugyanazt a app Service tervet használják a számítási és tárolási erőforrások futtatásához. A nagyméretű alkalmazások telepítése több erőforrást vagy időt is igénybe vehet. A sok üzembe helyezési munkafolyamatok nagy mennyiségű lemezes tevékenységet hoznak az alkalmazás tartalmának kötetén, amely nincs optimalizálva ilyen tevékenységre. Az egyéni rendszerkép egyetlen csomagban biztosítja az alkalmazás összes fájlját és függőségét az Azure-ba, és nincs szükség további fájlátviteli vagy üzembe helyezési műveletekre.
- **Gyors iteráció szükséges**: az alkalmazások Dockerizing további Build lépések szükségesek. A módosítások érvénybe léptetéséhez le kell küldenie az új rendszerképet egy adattárba az egyes frissítésekkel. Ezeket a frissítéseket ezután az Azure-környezetbe kell húzni. Ha a beépített tárolók egyike megfelel az alkalmazás igényeinek, a forrásról való üzembe helyezés gyorsabb fejlesztési munkafolyamatot eredményezhet.

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