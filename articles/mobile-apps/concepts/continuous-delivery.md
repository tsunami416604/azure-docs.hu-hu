---
title: A Visual Studio App Center és az Azure-szolgáltatásokkal automatizálhatja mobil alkalmazásainak üzembe helyezését és kiadását
description: Ismerje meg az olyan szolgáltatásokat, mint például a App Center, amelyek segítenek beállítani a folyamatos szállítási folyamatot a mobileszközök számára.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 1fef01ed4db7252043a8adee478b22ef2417e21f
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450891"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>A mobil alkalmazások üzembe helyezésének és kiadásának automatizálása folyamatos kézbesítési szolgáltatásokkal

A fejlesztőknek kódot kell írnia, és ellenőriznie kell a kódot a tárházban, de előfordulhat, hogy az adattárban bejelölt véglegesítek nem mindig konzisztensek. Ha több fejlesztő dolgozik ugyanazon a projekten, a problémák az integrációval is találkozhatnak. Előfordulhat, hogy a csapatok olyan helyzetekben futnak, ahol a dolgok nem működnek, a felhalmozott hibák és a projektek fejlesztése késleltetve lesz. A fejlesztőknek meg kell várniuk a teljes programkód felépítését és tesztelését, hogy ellenőrizze a hibákat, így a folyamat lassú és kevésbé ismétlődővé válik.

A folyamatos teljesítéssel automatizálhatja a mobileszközök üzembe helyezését és kiadását. Nem számít, hogy az alkalmazást tesztelő vagy vállalati alkalmazottak (bétaverziós tesztelés) vagy egy alkalmazás-áruház (éles környezetben) egy csoportjára terjeszti-e. A folyamatos teljesítés kevésbé kockázatos üzembe helyezéseket tesz lehetővé, és gyors ismétléseket ösztönöz. Új módosításokat is kiszabadíthat ügyfeleinek folyamatos módon.

## <a name="distribute-application-binaries-to-beta-testers"></a>Alkalmazás bináris fájljainak terjesztése a bétaverziós tesztelők számára
A Mobile Application bétaverziójának tesztelése az alkalmazás-fejlesztési folyamat egyik kritikus lépése. Az alkalmazás korai szakaszában segít megtalálni a hibákat és problémákat. A visszajelzés javítja az alkalmazás minőségét, ha éles használatra készen áll.

A következő szolgáltatások használatával engedélyezheti a folyamatos kézbesítési folyamatot a Mobile apps szolgáltatásban.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Distribute](/appcenter/distribution/) egy olyan eszköz, amellyel a fejlesztők gyorsan kiszabadítják az eszközökre épülő buildeket. A teljes telepítési portál felülettel a App Center Distribution egy hatékony megoldás a Beta app Tester-disztribúcióhoz. Emellett a nyilvános alkalmazás-áruházakon keresztül történő terjesztés is kényelmes alternatíva. A fejlesztők még tovább automatizálják a terjesztési munkafolyamatot App Center Build és a nyilvános alkalmazás-áruház integrálásával.

**A legfontosabb jellemzők**
- Terjessze az alkalmazást a bétaverziós tesztelőkre és a felhasználókra, és győződjön meg arról, hogy az összes tesztelő az alkalmazás legújabb verziójával rendelkezik.
- Értesítse a tesztelőket az új kiadásokról anélkül, hogy tesztelő a letöltési folyamaton át nem halad.
- Terjesztési csoportok kezelése az alkalmazás különböző verzióihoz.
- Terjesztés a boltokban: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [megkezdése](/appcenter/distribution/stores/intune)
- Az iOS, az Android, a macOS, a tvOS, a Xamarin, a Native, az Unity és a Cordova platform támogatását veheti igénybe.
- IOS-eszközök automatikus regisztrálása a létesítési profilban.

**Referencia**
- [Regisztráció Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a App Center terjesztéssel](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

Az [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) egy teljes körűen Kiemelt folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás, amely az Ön által preferált git-szolgáltatóval működik. Az Azure-folyamatok a legjelentősebb felhőalapú szolgáltatásokhoz, például az Azure-szolgáltatásokhoz is üzembe helyezhetők. A kód a GitHubon, a GitHub Enterprise Server, a GitLab, a bitbucket Cloud vagy az Azure Repos használatával kezdődhet. Ezután automatizálhatja a kód kiépítését, tesztelését és üzembe helyezését Microsoft Azure, Google Cloud Platform vagy Amazon Web Services (AWS) használatával.

**A legfontosabb jellemzők**
- **Egyszerűsített, feladaton alapuló felület a CI-kiszolgáló beállításához:** Hozzon létre egy CI-kiszolgálót natív (Android, iOS és Windows) és platformfüggetlen (Xamarin, Cordova és Native) Mobile-alkalmazásokhoz.
- **Bármilyen nyelv, platform és felhő:** A Node. js, a Python, a Java, a PHP, a Ruby, a go, a C/C++, a C#, az Android és az iOS rendszerű alkalmazások létrehozása, tesztelése és üzembe helyezése. Párhuzamosan futtatható Linux, macOS és Windows rendszeren. Üzembe helyezhet olyan felhőalapú szolgáltatókat, mint például az Azure, az AWS és a Google Cloud Platform. Mobil alkalmazások terjesztése a bétaverziós csatornákon és az alkalmazás-áruházakon keresztül.
- **Natív tároló támogatása:** Egyszerűen hozhat létre új tárolókat, és leküldheti azokat bármely beállításjegyzékbe. Tárolók üzembe helyezése független gazdagépekre vagy Kubernetes.
- **Speciális munkafolyamatok és funkciók:** Egyszerűen létrehozhat Build láncok és többfázisú buildek. Támogatás a YAML, a tesztelési integráció, a kiadási kapuk, a jelentéskészítés és más szolgáltatásokhoz.
- **Bővíthető:** A Közösség által készített felépítési, tesztelési és üzembe helyezési feladatok széles skáláját használhatja, amely több száz bővítményt tartalmaz a Slackből a SonarCloud. Akár más CI-rendszerekből is üzembe helyezhetők, például a Jenkins-től. A webhookok és a REST API-k segítségével integrálhat.
- **Ingyenes felhőben üzemeltetett buildek:** Ezek a buildek nyilvános és privát adattárakhoz is elérhetők.
- **Más felhőalapú szállítókba való központi telepítés támogatása:** A szállítók közé tartozik az AWS és a Google Cloud Platform.

**Referencia**
- [Ismerkedés az Azure-folyamatok útmutatójának használatába](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Bevezetés az Azure DevOps használatába](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Az alkalmazás terjesztése közvetlenül az App Store-ban
Miután az alkalmazás készen áll az éles használatra, és szeretné nyilvánosan használni, el kell küldeni az alkalmazás-áruházakba, ahol az ügyfelek letölthetik. Az alkalmazások közvetlenül az alkalmazás-áruházakba terjeszthetők. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center terjesztéssel](/appcenter/distribution/stores/)közvetlenül is közzéteheti a mobil alkalmazásait az alkalmazás-áruházakban. Miután az alkalmazást a felhasználók letölthetik, közvetlenül a Visual Studio App Center-portálról teheti közzé az alkalmazás bináris fájljait. 

Közvetlenül a következőre terjeszthető:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Áruház](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Az Apple által fejlesztett és karbantartott App Store-ban a felhasználók megkereshetik és tölthetik le az iOS-, MacOS-, WatchOS-és tvOS-eszközökhöz kifejlesztett alkalmazásokat. A fejlesztőknek be kell nyújtaniuk iOS-alkalmazásaikat az Apple App Store-ba nyilvános használatra.

### <a name="google-play"></a>Google Play

A Google Play az Android operációs rendszerhez készült hivatalos App Store áruház, ahol a felhasználók böngészhetik és tölthetik le a Google-on keresztül közzétett Android-eszközökhöz kifejlesztett alkalmazásokat.

### <a name="intune"></a>Intune

A [Microsoft Intune](/intune/app-management) egy felhőalapú szolgáltatás a nagyvállalati mobilitási felügyeleti területen, amely lehetővé teszi, hogy a munkaerő hatékony legyen, miközben a vállalati adatvédelmet is megtarthatja. Az Intune-nal a következőkre nyílik lehetősége:
- Kezelheti a munkaerő által a vállalati adatszolgáltatásokhoz használt mobileszközök és számítógépek adatait.
- Kezelheti a munkaerő által használt mobil alkalmazásokat.
- Gondoskodjon a vállalati adatok biztonságáról azáltal, hogy szabályozza a munkatársak hozzáférését és megosztását.
- Győződjön meg arról, hogy az eszközök és alkalmazások megfelelnek a vállalat biztonsági követelményeinek.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Frissítések telepítése közvetlenül a felhasználói eszközökre

### <a name="codepush"></a>CodePush
Az Apache Cordova és a App Center [CodePush](/appcenter/distribution/codepush/) használatával a natív fejlesztők közvetlenül a felhasználói eszközökre telepíthetik a Mobile Application-frissítéseket. Központi tárházként funkcionál, melyben a fejlesztők bizonyos frissítéseket tehetnek közzé, például JavaScript, HTML, CSS és képek módosításait. Ezután az alkalmazások lekérhetik a frissítéseket a tárházból a megadott ügyféloldali SDK-k használatával. Így több determinisztikus és közvetlen bevonási modellt is használhat a felhasználók számára a hibák kezelése vagy a kisebb funkciók hozzáadása során. Nem szükséges egy bináris Újraépítés vagy a nyilvános alkalmazás-áruházakon keresztüli terjesztése.

**A legfontosabb jellemzők**
- A Cordova és a natív fejlesztők reagálnak a mobileszközök frissítéseinek közvetlen üzembe helyezésére a felhasználói eszközökön anélkül, hogy az áruházban adnák.
- Hasznos lehet a hibák kijavítására, illetve olyan kis funkciók hozzáadására és eltávolítására, amelyek nem igénylik a bináris elemek újraépítését és a megfelelő tárolók használatával történő terjesztését.

**Referencia**
- [Regisztráció Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a CodePush szolgáltatással App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)