---
title: Az alkalmazások életciklusának automatizálása Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint a App Center, amelyek segítségével folyamatos buildet és integrációt állíthat be a mobil alkalmazásaihoz.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795548"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Az alkalmazások életciklusának automatizálása folyamatos létrehozással és integrációval

Fejlesztőként kódot írhat, és bejelöli a kódot a adattárba, de előfordulhat, hogy a tárházba bejelölt véglegesítek nem mindig konzisztensek. Ha több fejlesztő dolgozik ugyanazon a projekten, az integrációval kapcsolatos problémák merülhetnek fel, és a csapat olyan helyzetekben fut, ahol a dolgok nem működnek, a hibák megtartják a felhalmozás idejét, és a projekt fejlesztése késleltetve lesz. A fejlesztőknek meg kell várniuk, amíg a teljes programkódot felépítik és tesztelték, hogy ellenőrizze a hibákat, így a folyamat lassú és kevésbé ismétlődővé válik. 

A **folyamatos kiépítéssel és integrációval**a fejlesztők leegyszerűsítik a kód kiépítését és tesztelését azáltal, hogy nem csak a forráskód-tárház módosításait végzik el, hanem teszteket és ellenőrzéseket is végeznek a Build-környezetbe, így mindig futnak tesztek a kódban. A forráskódon végrehajtott összes módosítás folyamatosan jön létre, amikor véglegesítik a tárházat. A CI-kiszolgáló minden bejelentkezéskor érvényesíti és végrehajtja a fejlesztő által létrehozott összes tesztet. Ha a tesztek nem haladnak át, a rendszer visszaküldi a kódot a további változtatásokhoz. Ez lehetővé teszi, hogy a fejlesztő ne szakítsa meg a létrehozott buildeket, valamint hogy ne futtassa az összes tesztet helyileg a számítógépén, ami viszont növeli a fejlesztői hatékonyságot. 

## <a name="key-benefits"></a>Főbb előnyök
- Folyamat **automatikus** létrehozása, tesztelése és üzembe helyezése.
- **Észlelheti a hibákat és kijavíthatja a problémákat** a gyorsabb kiadási díjak biztosítása érdekében.
- Gyakrabban **véglegesítheti a kódot** , és gyorsan hozhat létre alkalmazásokat.
- A kód gyors módosításának **rugalmassága** problémák nélkül.
- A **gyorsabb piacra kerülési idő** gondoskodik arról, hogy csak a jó minőségű kód legyen elérhető.
- A **kis kód megváltozik** , mivel lehetővé teszi a kis kódrészletek egyszerre történő integrálását.
- A **csapat átláthatóságának és elszámoltathatóságának növelése** lehetővé teszi, hogy a **folyamatos visszajelzések** ne csak az ügyfelektől, hanem a csapattól is legyenek.

A következő szolgáltatások segítségével engedélyezheti a folyamatos integrációs folyamatot a Mobile apps szolgáltatásban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Build](/appcenter/build/) szolgáltatással olyan natív és platformfüggetlen alkalmazásokat hozhat létre, amelyeket a csapata a biztonságos felhőalapú infrastruktúra használatával használ. A tárházat könnyedén összekapcsolhatja App Center és a felhőben is megkezdheti az alkalmazás létrehozását, anélkül, hogy aggódnia kellene a Build-kiszolgálók helyileg, bonyolult konfigurációjában és a munkatársa gépén, de nem az Ön számára felépített kód beállításával.

A App Center szolgáltatások hozzáadott ereje révén tovább automatizálhatja a munkafolyamatot. Az automatikus kiadás a tesztelési és a nyilvános alkalmazás-áruházakra épít App Center terjesztéssel, illetve automatizált felhasználói felületi tesztek futtatásával több ezer valódi eszközön és operációsrendszer-konfiguráción a felhőben App Center teszteléssel.

**Főbb funkciók**
- Percek alatt **állíthatja be a folyamatos integrációt** , és gyakrabban és gyorsabban hozhat létre alkalmazásokat.
- Integrálhatja a **GitHub, a BitBucket, az Azure DevOps és a GitLab**.
- **Gyors és biztonságos buildek** felügyelt, felhőben üzemeltetett gépeken.
- **Lehetővé teheti a buildek indítását** , és ellenőrizheti, hogy az alkalmazás a valós idejű iOS-és Android-eszközökön épül-e fel.
- **Natív és platformfüggetlen támogatás** – iOS, Android, MacOS, Windows, Xamarin, natív reagálás.
- A buildeket a klónozás utáni, a Build előtti és utáni parancsfájlok hozzáadásával **szabhatja testre** .

**Hivatkozik**
- [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a App Center Buildtel](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure-beli folyamatok
 Az Azure DevOps-ben elérhető [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)egy teljes funkcionalitású, folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás, amely az Ön által előnyben részesített git-szolgáltatóval működik, és a legtöbb jelentős felhőalapú szolgáltatásban, például az Azure-ban is üzembe helyezhető. A kód a GitHubon, a GitHub Enterprise Server, a GitLab, a bitbucket Cloud vagy az Azure Repos használatával kezdődhet. Ezután automatizálhatja a kód kiépítését, tesztelését és üzembe helyezését Microsoft Azure, Google Cloud Platform vagy Amazon Web Services.

**Főbb funkciók**
- Egyszerűsített, a Microsoft és a nem Microsoft (node. js, Java) alapú kiszolgálókon kívül a CI-kiszolgáló natív (Android, iOS és Windows rendszerű) és platformfüggetlen (Xamarin, Cordova és Native) mobil alkalmazásaihoz való beállítására szolgáló egyszerűsített feladat-alapú felület technológiák.
- A Node. js, a Python, a Java, a PHP, a Ruby, a go, a C/C++, C#a, az Android és az iOS alkalmazásokat **bármilyen nyelven, platformon és felhőben** kiépítheti, tesztelheti és üzembe helyezheti. Párhuzamosan futtathatja alkalmazásait Linux, macOS és Windows rendszereken. Felhőbeli szolgáltatók, például az Azure, az AWS és a GCP üzembe helyezése. Mobil alkalmazások terjesztése a bétaverziós csatornákon és az alkalmazás-áruházakon keresztül.
- **Natív tároló támogatása** – új tárolók létrehozása könnyedén és bármely beállításjegyzékbe leküldhető. Tárolók üzembe helyezése független gazdagépekre vagy Kubernetes.
- **Speciális munkafolyamatok** – egyszerű Build láncolása és többfázisú buildek. Támogatja a YAML, a tesztelési integrációt, a kiadási kapukat, a jelentéskészítést és egyebeket.
- **Bővíthető** – a Közösség által készített kiépítési, tesztelési és üzembe helyezési feladatok széles körét használhatja – több száz bővítményt a slacktől a SonarCloud. Akár más CI-rendszerekből, például a Jenkinsből is üzembe helyezhet, és a webhookok és a REST API-k segítségével integrálhat
- **Ingyenes, felhőben üzemeltetett buildek** nyilvános és privát adattárakhoz.
- **Támogatja a más felhőalapú gyártók** , például a Amazon Web Services, Google Cloud platform stb. üzembe helyezését.

**Hivatkozik**
- [Ismerkedés az Azure-folyamatok útmutatójának használatába](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Ismerkedés az Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Rövid útmutatók](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Az alkalmazás létrehozásához válassza a megfelelő szolgáltatást, és kövesse a [app Center Build](/appcenter/build/choose-between-services)és az Azure-folyamatok összevetését ismertető cikket.
