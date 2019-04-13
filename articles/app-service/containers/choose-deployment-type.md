---
title: Egyéni rendszerkép többtárolós vagy beépített rendszerkép – az Azure App Service üzembe helyezése |} A Microsoft Docs
description: A Linuxon futó App Service egyéni Docker-tárolók üzembe helyezése, a többtárolós és a egy beépített alkalmazás-keretrendszer közötti kiválasztásáról
keywords: az Azure app service, webalkalmazás, linux, oss
services: app-service
documentationCenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c8a700bcd2780ef7b0c7ad1fbb513d4b4febffcb
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549320"
---
# <a name="custom-image-multi-container-or-built-in-platform-image"></a>Egyéni rendszerkép, többtárolós vagy beépített platformlemezkép?

[A linuxon futó App Service](app-service-linux-intro.md) kínál a közzétett webes alkalmazásai három különböző elérési utak:

- **Egyéni lemezkép telepítésének**: "Dockerize" az alkalmazás be egy Docker-rendszerképet, amely az összes, a fájlok és a egy futtatásra kész csomag függőségeit tartalmazza.
- **Több tároló üzembe helyezési**: "Dockerize" az alkalmazást egy Docker-Compose- vagy Kubernetes konfigurációs fájl használatával több tárolón.
- **Az alkalmazás üzembe helyezése a platform beépített rendszerképpel rendelkező**: A beépített platform általános webes alkalmazás futtatókörnyezetek és függőségei, mint például a Node és a PHP tartalmazzák. Használjon a [Azure App Service-alapú üzembe helyezési módszer](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) helyezze üzembe az alkalmazást a webalkalmazás-tárolóba, és beépített platformlemezkép használatával futtassa.

## <a name="which-method-is-right-for-your-app"></a>Módszer a legmegfelelőbb az alkalmazást? 

Megfontolandó legfontosabb tényezők a következők:

- **A Docker a fejlesztési munkafolyamat rendelkezésre állási**: Egyéni rendszerkép fejlesztési a Docker-fejlesztési munkafolyamat alapszintű ismerete szükséges. Egy webalkalmazás, egyéni lemezkép telepítéséhez van szükség, például a Docker Hub adattárban gazdagépre az egyéni rendszerkép közzététele. Ha ismeri a Docker és a Docker-feladatokat adhat hozzá a build-munkafolyamatot, vagy már közzéteszi az alkalmazást egy Docker-rendszerképet, egyéni rendszerkép szinte biztosan a legjobb választás.
- **Többrétegű architektúra**: Például egy webes alkalmazás réteg és a egy API-réteget külön képességek használatával többtárolós több tároló üzembe helyezése. 
- **Alkalmazások teljesítményének**: Például a Redis cache réteget használó többtárolós alkalmazás teljesítményének növelése érdekében. Válassza ki a többtárolós ennek eléréséhez.
- **Egyedi futtatókörnyezet használatának követelményei**: A beépített platformlemezképek tervezték, hogy a legtöbb webes alkalmazások igényeihez, de a testreszabhatóság korlátozott. Előfordulhat, hogy az alkalmazás egyedi függőségekkel vagy más, amelyek túllépik a beépített rendszerképek Mik képes a futtatókörnyezet használatának követelményei.
- **Build követelmények**: A [folyamatos üzembe helyezés](../deploy-continuous-deployment.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), megtekintheti az alkalmazás és az Azure-ban közvetlenül a kódból származzanak. Nincsenek külső build és a közzétételi folyamat nem szükséges. Azonban korlátozva van a testreszabhatóság és belül nástroje sestavení Pro rendelkezésre állását a [Kudu](https://github.com/projectkudu/kudu/wiki) üzembe helyezési motorban. Az alkalmazás lehetséges, hogy elszaporodó Kudu a képességek, a függőségek vagy egyéni hozhat létre logikai követelményei növekedésével.
- **Lemez írási/olvasási követelmények**: Web Apps-alkalmazások az összes webes tartalmak vannak lefoglalva a tárolási köteten. Az Azure-tárhely, a kötet csatlakoztatva van `/home` a fájlrendszer az alkalmazást. Ellentétben a fájlok a tároló fájlrendszer a tartalom köteten lévő fájlok elérhetők egy alkalmazás összes méretezési csoport példánya között, és módosítások megmaradnak az alkalmazás-újraindítások között. Azonban a tartalom kötet a lemez késése értéke magasabb, és további változó, mint a helyi tároló fájlrendszer és a hozzáférés a késés is hatással lehet ügyfélfelügyeletiplatform-frissítésekre, a nem tervezett üzemkimaradások, és a hálózati problémák léptek fel. Tartalomfájlok (nagy erőforrásigényű) csak olvasható hozzáférést igénylő alkalmazások előnyösebb lehet egyéni rendszerképet a központi telepítési fájljait elhelyezi az lemezkép fájlrendszer helyett a tartalom köteten található.
- **Erőforrás-használat Build**: Ha egy alkalmazást a forrásból, a központi telepítési parancsfájlok futtatása Kudu használatával az azonos App Service-csomag számítási és tárolási erőforrások a futó alkalmazás. Nagy méretű alkalmazások telepítésének felhasználható további erőforrásokat, vagy a kívánt időt. Különösen számos üzembe helyezési munkafolyamatok létrehozása (nagy erőforrásigényű) lemez tevékenység a alkalmazás tartalom köteten, amely nincs optimalizálva az ilyen tevékenység. Egyéni rendszerkép kínál az alkalmazás fájljait és függőségek összes az Azure-bA és nincs szükség további fájlátvitel vagy a telepítési műveletek egyetlen csomagban.
- **Gyors ismétlését szükséges**: Alkalmazás dockerizing build további lépéseket igényel. A módosítások érvénybe léptetéséhez, meg kell az új rendszerkép leküldése egy adattár az egyes frissítések. Ezek a frissítések majd lekért vannak az Azure-környezethez. Ha az alkalmazás igényeinek megfelel egy beépített tárolót, üzembe helyezése a forrás kínálhatják gyorsabb fejlesztést munkafolyamat.
