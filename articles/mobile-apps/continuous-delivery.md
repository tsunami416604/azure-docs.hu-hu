---
title: A Visual Studio App Center és az Azure-szolgáltatásokkal automatizálhatja mobil alkalmazásainak üzembe helyezését és kiadását
description: Ismerje meg az olyan szolgáltatásokat, mint például a App Center, amelyek segítenek beállítani a folyamatos szállítási folyamatot a mobileszközök számára.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795600"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>A mobil alkalmazások üzembe helyezésének és kiadásának automatizálása folyamatos kézbesítési szolgáltatásokkal

Fejlesztőként kódot írhat, és bejelöli a kódot a adattárba, de előfordulhat, hogy a tárházban bejelölt véglegesítek nem mindig konzisztensek. Ha több fejlesztő dolgozik ugyanazon a projekten, az integrációval kapcsolatos problémák merülhetnek fel, és a csapat olyan helyzetekben fut, ahol a dolgok nem működnek, a hibák megtartják a felhalmozás idejét, és a projekt fejlesztése késleltetve lesz. A fejlesztőknek meg kell várniuk a teljes programkód felépítését és tesztelését, hogy ellenőrizze a hibákat, így a folyamat lassú és kevésbé ismétlődővé válik.

A **folyamatos teljesítéssel**automatizálhatja a mobileszközök üzembe helyezését és kiadását, függetlenül attól, hogy az alkalmazást tesztelő vagy vállalati alkalmazottak (bétaverziós tesztelés) vagy az alkalmazás-áruház (éles környezetben) egy csoportjára terjeszti. Kevésbé kockázatos üzembe helyezést tesz lehetővé, gyors ismétléseket ösztönöz, és lehetővé teszi, hogy folyamatosan új módosításokat szabadítson fel ügyfeleinek.

## <a name="distribute-application-binaries-to-beta-testers"></a>Alkalmazás bináris fájljainak terjesztése a bétaverziós tesztelők számára
A Mobile Application bétaverziójának tesztelése az alkalmazás-fejlesztési folyamat egyik kritikus lépése. Segít az alkalmazás korai szakaszában található hibák és problémák keresésében, valamint az alkalmazások minőségének javításán alapuló visszajelzéseket az éles környezethez való használatra.

A következő szolgáltatások segítségével engedélyezheti a folyamatos kézbesítési folyamatot a Mobile apps szolgáltatásban.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Distribute](/appcenter/distribution/) egy olyan eszköz, amellyel a fejlesztők gyorsan kioszthatják a végfelhasználói eszközökre épülő buildeket. A teljes telepítési portál felülettel a terjesztés nem csak egy hatékony megoldás a Beta app Tester Distribution szolgáltatáshoz, hanem a nyilvános alkalmazás-Áruházakon keresztül történő terjesztéshez is kényelmes alternatíva. A fejlesztők még tovább automatizálják a terjesztési munkafolyamatot App Center Build és a nyilvános alkalmazás-áruház integrálásával.

**Főbb funkciók**
- **Terjessze az alkalmazást a bétaverziós tesztelőkre és a felhasználókra** , és győződjön meg arról, hogy az összes tesztelő az alkalmazás legújabb verziójával rendelkezik.
- **Értesítse a tesztelőket az új kiadásokról anélkül,** hogy tesztelő a letöltési folyamaton át nem halad.
- **Terjesztési csoportok kezelése** az alkalmazás különböző verzióihoz.
- **Tárolók eloszlása** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **Platform-támogatás** – iOS, Android, MacOS, TvOS, Xamarin, inaktív, Unity, Cordova.
- IOS-eszközök automatikus regisztrálása a létesítési profilban.

**Hivatkozik**
- [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a App Center terjesztéssel](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure-beli folyamatok

Az [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) egy teljes körűen Kiemelt folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás, amely az Ön által előnyben részesített git-szolgáltatóval működik, és üzembe helyezhető a legtöbb nagy felhőalapú szolgáltatásban, beleértve az Azure-szolgáltatásokat is. A kód a GitHubon, a GitHub Enterprise Server, a GitLab, a bitbucket Cloud vagy az Azure Repos használatával kezdődhet. Ezután automatizálhatja a kód kiépítését, tesztelését és üzembe helyezését Microsoft Azure, Google Cloud Platform vagy Amazon Web Services.

**Főbb funkciók**
- Egyszerűsített, feladaton alapuló élmény a CI-kiszolgáló **natív (Android, iOS és Windows rendszerű) és platformfüggetlen (Xamarin, Cordova és Native) mobil alkalmazások**számára történő beállításához.
- A Node. js, a Python, a Java, a PHP, a Ruby, a go, a C/C++, C#a, az Android és az iOS-alkalmazások **bármely nyelve, platformja és Felhőbeli** létrehozása, tesztelése és üzembe helyezése. Párhuzamosan futtathatja alkalmazásait Linux, macOS és Windows rendszereken. Felhőbeli szolgáltatók, például az Azure, az AWS és a GCP üzembe helyezése. Mobil alkalmazások terjesztése a bétaverziós csatornákon és az alkalmazás-áruházakon keresztül.
- **Natív tároló támogatása** – új tárolók létrehozása könnyedén és bármely beállításjegyzékbe leküldhető. Tárolók üzembe helyezése független gazdagépekre vagy Kubernetes.
- **Speciális munkafolyamatok és funkciók** – egyszerű Build-láncolás és többfázisú buildek. Támogatja a YAML, a tesztelési integrációt, a kiadási kapukat, a jelentéskészítést és egyebeket.
- **Bővíthető** – a Közösség által készített kiépítési, tesztelési és üzembe helyezési feladatok széles körét használhatja – több száz bővítményt a slacktől a SonarCloud. Akár más CI-rendszerekből is üzembe helyezhetők, például a Jenkins-től. A webhookok és a REST API-k segítenek az integrálásban
- **Ingyenes, felhőben üzemeltetett buildek** nyilvános és privát adattárakhoz.
- **Támogatja a más felhőalapú szállítók** , például az AWS, a GCP stb. üzembe helyezését.

**Hivatkozik**
- [Ismerkedés az Azure-folyamatok útmutatójának használatába](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Ismerkedés az Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Az alkalmazás terjesztése közvetlenül az App Store-ban
Ha az alkalmazás készen áll az éles használatra, és azt szeretné nyilvánosan használni, el kell küldenie az alkalmazás-áruházakba, ahol az ügyfelek letölthetik azokat. Az alkalmazások közvetlenül az alkalmazás-áruházakba terjeszthetők. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
A [app Center Distribution](/appcenter/distribution/stores/) Service lehetővé teszi a Mobile-alkalmazások közvetlen közzétételét az alkalmazás-áruházakban. Ha az alkalmazás készen áll a végfelhasználók számára történő letöltésre, közvetlenül a App Center-portálon teheti közzé az alkalmazás bináris fájljait.  

Közvetlenül a következőre terjeszthető:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Áruház](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Az Apple által fejlesztett és karbantartott App Store lehetővé teszi a felhasználók számára az iOS-, MacOS-, WatchOS-és tvOS-eszközökhöz kifejlesztett alkalmazások tallózását és letöltését. A fejlesztőknek be kell nyújtaniuk iOS-alkalmazásaikat az Apple Store-ba nyilvános használatra.

### <a name="google-play"></a>Google Play

A Google Play az Android operációs rendszerhez készült hivatalos App Store áruház, amely lehetővé teszi a felhasználók számára az Android-eszközökhöz fejlesztett és a Google-on közzétett alkalmazások tallózását és letöltését.

### <a name="intune"></a>Intune

A [Microsoft Intune](/intune/app-management) egy felhőalapú szolgáltatás a nagyvállalati mobilitási felügyeletben ("az"), amely lehetővé teszi, hogy a munkaerő hatékony legyen, miközben a vállalati adatvédelmet tartja. Az Intune-nal lehetősége van 
- Kezelheti a munkaerő által a vállalati adatszolgáltatásokhoz használt mobileszközök és számítógépek adatait.
- Kezelheti a munkaerő által használt mobil alkalmazásokat.
- Gondoskodjon a vállalati adatok biztonságáról azáltal, hogy szabályozza a munkatársak hozzáférését és megosztását.
- Győződjön meg arról, hogy az eszközök és alkalmazások megfelelnek a vállalat biztonsági követelményeinek.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Frissítések telepítése közvetlenül a felhasználói eszközökre

### <a name="codepush"></a>CodePush
A App Center [CodePush](/appcenter/distribution/codepush/) szolgáltatása lehetővé teszi az Apache Cordova használatát, és a natív fejlesztőket reagálva közvetlenül a felhasználói eszközökre telepítheti a mobil alkalmazások frissítéseit. Központi tárházként működik, amelyet a fejlesztők bizonyos frissítések közzétételére használhatnak (például JS, HTML, CSS és képváltozások). Ezután az alkalmazások lekérhetik a frissítéseket a tárházból a megadott ügyféloldali SDK-k használatával. Ez lehetővé teszi, hogy a végfelhasználók számára nagyobb determinisztikus és közvetlen bevonási modellt biztosítson, miközben a hibákat kezeli vagy kisebb funkciókkal bővíti, anélkül, hogy egy bináris fájlt kellene újraépítenie, vagy újra el kellene terjesztenie a nyilvános alkalmazások tárolói között.

**Főbb funkciók**
- Lehetővé teszi a Cordova és a natív fejlesztők számára a mobileszközök frissítéseinek közvetlen üzembe helyezését a felhasználói eszközökön anélkül, hogy tárolón kellene kihelyezni őket.
- Hasznos lehet a hibák javításához, illetve olyan kis funkciók hozzáadásához vagy eltávolításához, amelyek nem igénylik a bináris elemek újraépítését és a megfelelő tárolók közötti újraterjesztést

**Hivatkozik**
- [Regisztráció App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés a kód leküldésével App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)