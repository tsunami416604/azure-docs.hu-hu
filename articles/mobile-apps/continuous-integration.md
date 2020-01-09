---
title: Az alkalmazások életciklusának automatizálása Visual Studio App Center és Azure-szolgáltatásokkal
description: Ismerkedjen meg az olyan szolgáltatásokkal, mint a App Center, amelyek segítenek beállítani a mobil alkalmazásai folyamatos összeállítását és integrálását.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 9b0a9e10a88836ce83e636db20180c3692ab4429
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453187"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Az alkalmazások életciklusának automatizálása folyamatos létrehozással és integrációval

A fejlesztőknek kódot kell írnia, és ellenőriznie kell a kódot a tárházban, de előfordulhat, hogy az adattárban bejelölt véglegesítek nem mindig konzisztensek. Ha több fejlesztő dolgozik ugyanazon a projekten, a problémák az integrációval is találkozhatnak. Előfordulhat, hogy a csapatok olyan helyzetekben futnak, ahol a dolgok nem működnek, a felhalmozott hibák és a projektek fejlesztése késleltetve lesz. A fejlesztőknek meg kell várniuk a teljes programkód felépítését és tesztelését, hogy ellenőrizze a hibákat, így a folyamat lassú és kevésbé ismétlődővé válik. 

A folyamatos kiépítéssel és integrációval a fejlesztők leegyszerűsítik a kód összeállítását és tesztelését azáltal, hogy elvégzik a forráskód-tárház módosításait, és teszteket és ellenőrzéseket végeznek a Build-környezetbe. Így mindig tesztek futnak a kódban. A forráskódon végrehajtott összes módosítás folyamatosan készül, amikor véglegesítik a tárházat. A folyamatos integrációs (CI) kiszolgáló minden bejelentkezéskor érvényesíti és végrehajtja a fejlesztő által létrehozott összes tesztet. Ha a tesztek nem haladnak át, a rendszer visszaküldi a kódot a további változtatásokhoz. Így a fejlesztők nem bontják le a létrehozott buildeket. Emellett nem kell az összes tesztet helyileg futtatni a számítógépükön, ami növeli a fejlesztői hatékonyságot. 

## <a name="key-benefits"></a>Főbb előnyök
- Automatizálhatja a folyamatok felépítését, tesztelését és üzembe helyezését.
- Észlelheti a hibákat és kijavíthatja a problémákat a gyorsabb kiadási díjak biztosítása érdekében.
- Gyakrabban véglegesítheti a kódot, és gyorsan hozhat létre alkalmazásokat.
- A kód gyors megváltozása a hibák nélkül.
- Gyorsabban juthat el a piacra, hogy csak a jó minőségű kód legyen elérhető.
- A kisméretű kódok hatékonyabb módosítása, mivel a kis kódrészletek egyszerre vannak integrálva.
- Növelje a csapat átláthatóságát és elszámoltathatóságát, hogy folyamatos visszajelzést kapjon ügyfelei és csapata számára.

A következő szolgáltatások használatával engedélyezheti a folyamatos integrációs folyamatot a Mobile apps szolgáltatásban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Build](/appcenter/build/) segítségével natív és platformfüggetlen alkalmazásokat hozhat létre, amelyeket a csapata biztonságos felhőalapú infrastruktúrával használ. A tárházat könnyedén összekapcsolhatja Visual Studio App Centerban, és megkezdheti a felhőben felépíteni az alkalmazást minden egyes véglegesítés során. Nem kell aggódnia a Build-kiszolgálók helyi, bonyolult konfigurációinak és a munkatársainak, de nem a saját számítógépére épülő kód konfigurálásával kapcsolatban.

A Visual Studio App Center szolgáltatások hozzáadott ereje révén tovább automatizálhatja a munkafolyamatot. A buildeket automatikusan kibocsáthatja a tesztelők és a nyilvános alkalmazások tárolói számára a App Center Distribute használatával. Az automatizált felhasználói felületi teszteket több ezer valódi eszközön és operációsrendszer-konfiguráción is futtathatja a felhőben App Center teszteléssel.

**Fő funkciók**
- Percek alatt állíthatja be a folyamatos integrációt, és gyakrabban és gyorsabban hozhat létre alkalmazásokat.
- Integrálhatja a GitHub, a BitBucket, az Azure DevOps és a GitLab.
- Gyors és biztonságos buildek létrehozása felügyelt, felhőben üzemeltetett gépeken.
- A buildek lehetővé teszik a tesztek indítását, valamint annak ellenőrzését, hogy az alkalmazás a valós idejű iOS-és Android-eszközökön épül-e fel.
- Natív és platformfüggetlen támogatás az iOS, az Android, a macOS, a Windows, a Xamarin és a natív reagáláshoz.
- A buildeket a klónozás utáni, a Build előtti és utáni parancsfájlok hozzáadásával szabhatja testre.

**Hivatkozik**
- [Regisztráció Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a App Center Buildtel](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure-beli folyamatok
 Az Azure DevOps-ben üzemelő [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)egy teljes körűen elérhető folyamatos integrációs és folyamatos teljesítési (CD) szolgáltatás, amely az Ön által preferált git-szolgáltatóval működik. Üzembe helyezhető a legtöbb nagy felhőalapú szolgáltatásban, beleértve az Azure-t is. A kód a GitHubon, a GitHub Enterprise Server, a GitLab, a bitbucket Cloud vagy az Azure Repos használatával kezdődhet. Ezután automatizálhatja a kód kiépítését, tesztelését és üzembe helyezését Microsoft Azure, Google Cloud Platform vagy Amazon Web Services (AWS) használatával.

**Fő funkciók**
- **Egyszerűsített, feladaton alapuló felület a CI-kiszolgáló beállításához:** A Microsoft és a nem Microsoft (node. js, Java) alapú kiszolgálói technológiák mellett hozzon létre egy CI-kiszolgálót a natív (Android, iOS és Windows rendszerű) és platformfüggetlen (Xamarin, Cordova és Native) mobil alkalmazásokhoz.
- **Bármilyen nyelv, platform és felhő:** Node. js, Python, Java, php, Ruby, go, C/C++, C#, Android és iOS rendszerű alkalmazások létrehozása, tesztelése és üzembe helyezése. Párhuzamosan futtathatja alkalmazásait Linux, macOS és Windows rendszereken. Üzembe helyezhet olyan felhőalapú szolgáltatókat, mint például az Azure, az AWS és a Google Cloud Platform. Mobil alkalmazások terjesztése a bétaverziós csatornákon és az alkalmazás-áruházakon keresztül.
- **Natív tároló támogatása:** Egyszerűen hozhat létre új tárolókat, és leküldheti azokat bármely beállításjegyzékbe. Tárolók üzembe helyezése független gazdagépekre vagy Kubernetes.
- **Speciális munkafolyamatok:** Egyszerűen létrehozhat Build láncok és többfázisú buildek. Támogatás a YAML, a tesztelési integráció, a kiadási kapuk, a jelentéskészítés és más szolgáltatásokhoz.
- **Bővíthető:** A Közösség által készített felépítési, tesztelési és üzembe helyezési feladatok széles skáláját használhatja, amely több száz bővítményt tartalmaz a Slackből a SonarCloud. Akár más CI-rendszerekből is üzembe helyezhetők, például a Jenkins-től. A webhookok és a REST API-k segítségével integrálhat.
- **Ingyenes felhőben üzemeltetett buildek:** Ezek a buildek nyilvános és privát adattárakhoz is elérhetők.
- **Más felhőalapú szállítókba való központi telepítés támogatása:** A szállítók közé tartozik az AWS és a Google Cloud Platform.

**Hivatkozik**
- [Ismerkedés az Azure-folyamatok útmutatójának használatába](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Ismerkedés az Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Rövid útmutatók](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Az alkalmazás által létrehozott megfelelő szolgáltatás kiválasztásához tekintse meg a [app Center Build](/appcenter/build/choose-between-services)és az Azure-folyamatok összevetését ismertető cikket.
