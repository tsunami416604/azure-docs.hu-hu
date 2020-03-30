---
title: Mobilalkalmazásai üzembe helyezésének és kiadásának automatizálása a Visual Studio App Center és az Azure-szolgáltatások segítségével
description: Ismerje meg azokat a szolgáltatásokat, például az App Centert, amelyek segítenek a mobilalkalmazások folyamatos kézbesítési folyamatának beállításában.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235354"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatizálja a mobilalkalmazások telepítését és kiadását a folyamatos kézbesítési szolgáltatásokkal

Fejlesztőkként kódot ír, és becsekkolja a kódtárházba, de előfordulhat, hogy a tárházba ellenőrzött véglegesítések nem mindig konzisztensek. Ha több fejlesztő dolgozik ugyanazon a projekten, problémák merülhetnek fel az integrációval. A csapatok olyan helyzetekbe ütközhetnek, ahol a dolgok nem működnek, a hibák felhalmozódnak, és a projektfejlesztés késik. A fejlesztőknek meg kell várniuk, amíg a teljes szoftverkód megépül és teszteli, hogy ellenőrizzék a hibákat, ami lassúvá és kevésbé ismétlődővé teszi a folyamatot.

A folyamatos kézbesítéssel automatizálhatja a mobilalkalmazások telepítését és kiadását. Nem számít, hogy az alkalmazást tesztelők vagy vállalati alkalmazottak egy csoportjának (bétateszteléshez) vagy egy alkalmazásáruháznak (éles környezetben) osztja-e el. A folyamatos kézbesítés csökkenti a telepítést, és ösztönzi a gyors ismétléseket. Az új módosításokat folyamatosan is kiadhatja ügyfeleinek.

## <a name="distribute-application-binaries-to-beta-testers"></a>Alkalmazásbináris fájlok terjesztése bétatesztelőknek
A mobilalkalmazás bétatesztelése az alkalmazásfejlesztési folyamat egyik legfontosabb lépése. Segít megtalálni a hibákat és problémákat az alkalmazásban a korai. A visszajelzés javítja az alkalmazás minőségét, amikor éles használatra készül.

Az alábbi szolgáltatások segítségével folyamatos kézbesítési folyamatot engedélyezanek a mobilalkalmazásokban.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[Az App Center Distribute](/appcenter/distribution/) egy olyan eszköz, amellyel a fejlesztők gyorsan felszabadíthatják a buildeket az eszközökre. A teljes telepítési portál lal az App Center Distribute hatékony megoldást kínál a bétaverziós tesztelők disztribúciójára. Ez is egy kényelmes alternatívája a forgalmazás nyilvános app tárolja. A fejlesztők még tovább automatizálhatják terjesztési munkafolyamatukat az App Center Build és a nyilvános alkalmazásáruház-integrációk segítségével.

**A legfontosabb jellemzők**
- Az alkalmazást terjesztheti a bétatesztelőknek és a felhasználóknak, és győződjön meg arról, hogy az összes tesztelő az alkalmazás legújabb verzióján van.
- Értesítse a tesztelőket az új kiadásokról anélkül, hogy a tesztelők újra átmenne a letöltési folyamaton.
- Az alkalmazás különböző verzióihoz való terjesztési csoportok kezelése.
- Terjesztés az üzletekben: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [megkezdése](/appcenter/distribution/stores/intune)
- Platformtámogatás az iOS, Android, macOS, tvOS, Xamarin, React Native, Unity és Cordova rendszerhez.
- Automatikusan regisztrálja az iOS-eszközöket a létesítési profilba.

**Referencia**
- [Regisztráció a Visual Studio App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Ismerkedés az App Center Distribute alkalmazással](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Az Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) egy teljes körűen kiemelt folyamatos integrációs (CI) és folyamatos kézbesítési (CD) szolgáltatás, amely együttműködik a kívánt Git-szolgáltatóval. Az Azure Pipelines a legtöbb főbb felhőszolgáltatások, például az Azure-szolgáltatások üzembe helyezhető. A kódot a GitHubon, a GitHub Enterprise Serveren, a GitLaben, a Bitbucket Cloudon vagy az Azure Repos-on kezdheti. Ezután automatizálhatja a kód összeállítását, tesztelését és telepítését a Microsoft Azure, a Google Cloud Platform vagy az Amazon Web Services (AWS) szolgáltatásba.

**A legfontosabb jellemzők**
- **Egyszerűsített feladatalapú felület a CI-kiszolgáló beállításához:** Hozzon létre egy CI-kiszolgálót natív (Android, iOS és Windows) és platformfüggetlen (Xamarin, Cordova és React Native) mobilalkalmazásokhoz is.
- **Bármilyen nyelv, platform és felhő:** Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android és iOS alkalmazások at készíthet, tesztelhet és helyezhet üzembe. Párhuzamosan futtassa Linuxon, macOS-en és Windows rendszeren. Üzembe helyezheti az olyan felhőszolgáltatókat, mint az Azure, az AWS és a Google Cloud Platform. Mobilalkalmazások terjesztése bétacsatornákon és alkalmazásáruházakon keresztül.
- **Natív tárolótámogatás:** Hozzon létre új tárolókat könnyedén, és tolja őket bármely rendszerleíró adatbázisba. Telepítse a tárolókat független állomásokra vagy Kubernetes-re.
- **Speciális munkafolyamatok és funkciók:** Egyszerűen hozhat létre építési láncokat és többfázisú buildeket. Támogatást kaphat a YAML-hez, a tesztelési integrációhoz, a kiadási kapukhoz, a jelentéskészítéshez és egyebekhez.
- **Bővíthető:** A közösség által készített build-, tesztelési és üzembe helyezési feladatok széles skáláját használhatja, amelyek több száz bővítményt tartalmaznak a Slacktől a SonarCloudig. Akár más CI-rendszerekből is telepíthet, például a Jenkins-ből. A webhorgok és a REST API-k segítségével integrálható.
- **Ingyenes felhőalapú buildek:** Ezek a buildek nyilvános és magántárolók számára érhetők el.
- **Más felhőszolgáltatóknak történő telepítés támogatása:** A szállítók közé tartozik az AWS és a Google Cloud Platform.

**Referencia**
- [Ismerkedés az Azure Pipelines útmutatójával](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Bevezetés az Azure DevOps használatába](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Az alkalmazás terjesztése közvetlenül az App Store-k között
Miután az alkalmazás készen áll az éles használatra, és azt szeretné, hogy nyilvánosan kell használni, azt el kell küldeni az alkalmazás-áruházak, ahol az ügyfelek által letölthető. Az alkalmazás terjesztését többféleképpen is eloszthatja közvetlenül az alkalmazásáruházakban. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Az [App Center Distribute](/appcenter/distribution/stores/)segítségével közvetlenül közzéteheti mobilalkalmazásait az alkalmazásáruházakban. Miután az alkalmazás készen áll a felhasználók általi letöltésre, az alkalmazás bináris fájljait közvetlenül a Visual Studio App Center portálról teheti közzé. 

Közvetlenül terjesztheti a következőket:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Áruház](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Az Apple által kifejlesztett és karbantartott alkalmazásboltban a felhasználók böngészhetnek és letölthetik az iOS, MacOS, WatchOS és tvOS eszközökre kifejlesztett alkalmazásokat. A fejlesztőknek be kell küldeniük iOS-alkalmazásaikat az Apple App Store-ba nyilvános használatra.

### <a name="google-play"></a>Google Play

A Google Play az Android operációs rendszer hivatalos alkalmazásboltja, ahol a felhasználók böngészhetnek és letölthetik az Android-eszközökre kifejlesztett alkalmazásokat, amelyeket a Google-on keresztül tesznek közzé.

### <a name="intune"></a>Intune

[A Microsoft Intune](/intune/app-management) egy felhőalapú szolgáltatás a vállalati mobilitás-felügyeleti térben, amely lehetővé teszi, hogy a munkaerő hatékonyan dolgozhasson, miközben a vállalati adatok védelme alatt marad. Az Intune-nal a következőkre nyílik lehetősége:
- Kezelheti azokat a mobileszközöket és számítógépeket, amelyeket a munkaerő a vállalati adatok eléréséhez használ.
- Kezelheti a munkaerő által használt mobilalkalmazásokat.
- Védje meg a vállalati adatokat azáltal, hogy szabályozza, hogy a munkaerő hogyan fér hozzá és osztja meg azokat.
- Győződjön meg arról, hogy az eszközök és alkalmazások megfelelnek a vállalati biztonsági követelményeknek.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Frissítések telepítése közvetlenül a felhasználók eszközeire

### <a name="codepush"></a>Kódküldéses
Az App [CentercodePush](/appcenter/distribution/codepush/) segítségével az Apache Cordova és a React Native fejlesztők közvetlenül telepíthetik a mobilalkalmazás-frissítéseket a felhasználók eszközeire. Központi adattárként működik, amelyen a fejlesztők közzétehetnek bizonyos frissítéseket, például a JavaScript, a HTML, a CSS és a képmódosításokat. Ezután az alkalmazások lekérdezhetik a frissítéseket a tárházból a megadott ügyfél SDK-k használatával. Ily módon egy determinisztikusabb és közvetlenebb elköteleződési modellel rendelkezhet a felhasználókkal, miközben megoldja a hibákat, vagy kisebb funkciókat ad hozzá. Nem kell újraépítenie a bináris fájlt, vagy továbbterjesztenia azt a nyilvános alkalmazásáruházakon keresztül.

**A legfontosabb jellemzők**
- A Cordova és a React natív fejlesztők a mobilalkalmazás-frissítéseket közvetlenül telepíthetik a felhasználók eszközeire anélkül, hogy egy áruházban közzétették volna őket.
- Hasznos a hibák kijavításához, vagy olyan kis funkciók hozzáadásához és eltávolításához, amelyek nem igénylik a bináris újraépítését és a megfelelő üzleteken keresztüli újraterjesztését.

**Referencia**
- [Regisztráció a Visual Studio App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [A CodePush első lépései az App Centerben](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)