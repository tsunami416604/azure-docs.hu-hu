---
title: Az alkalmazások életciklusának automatizálása a Visual Studio App Center és az Azure-szolgáltatások segítségével
description: Ismerje meg azokat a szolgáltatásokat, például az App Centert, amelyek segítenek a folyamatos építés és integráció beállításában a mobilalkalmazásokszámára.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240927"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatizálja alkalmazásai életciklusát a folyamatos építés és integráció révén

Fejlesztőkként kódot ír, és becsekkolja a kódtárházba, de előfordulhat, hogy a tárházba ellenőrzött véglegesítések nem mindig konzisztensek. Ha több fejlesztő dolgozik ugyanazon a projekten, problémák merülhetnek fel az integrációval. A csapatok olyan helyzetekbe ütközhetnek, ahol a dolgok nem működnek, a hibák felhalmozódnak, és a projektfejlesztés késik. A fejlesztőknek meg kell várniuk, amíg a teljes szoftverkód megépül és teszteli, hogy ellenőrizzék a hibákat, ami lassúvá és kevésbé ismétlődővé teszi a folyamatot. 

A folyamatos építés és integráció révén a fejlesztők egyszerűsíthetik a buildeket, és tesztelhetik a kódjukat azáltal, hogy lekötelezik a módosításokat a forráskód tárházán, és teszteket és ellenőrzéseket helyeznek el a buildkörnyezetben. Ily módon, ők mindig fut tesztek ellen a kódot. A forráskód minden módosítása folyamatosan épül, amikor véglegesítés történik a tárházban. Minden bejelentkezéssel a folyamatos integrációs (CI) kiszolgáló érvényesíti és végrehajtja a fejlesztő által létrehozott teszteket. Ha a tesztek nem felelnek meg, a rendszer visszaküldi a kódot további módosításokra. Ily módon a fejlesztők nem törik meg a létrehozott buildeket. Nem kell az összes tesztet helyileg futtatniuk a számítógépükön, ami növeli a fejlesztők termelékenységét. 

## <a name="key-benefits"></a>Főbb előnyök
- Automatizálhatja a folyamatok buildjeit, tesztjeit és üzembe helyezését.
- A gyorsabb kiadási arány biztosítása érdekében korán észlelheti a hibákat, és kijavíthatja a problémákat.
- Gyakrabban véglegesítse a kódot, és gyorsan hozzon létre alkalmazásokat.
- Rugalmasan módosíthatja a kódot problémák nélkül.
- Nyereség gyorsabb idő-to-market, hogy csak a jó minőségű kódot teszi végig.
- A kis kódmódosítások hatékonyabb átolását, mivel a kis kóddarabok egyszerre vannak integrálva.
- Növelje a csapat átláthatóságát és elszámoltathatóságát, hogy folyamatos visszajelzést kapjon ügyfeleitől és csapatától.

Az alábbi szolgáltatások segítségével folyamatos integrációs folyamatot engedélyezanek a mobilalkalmazásokban.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[Az App Center Build](/appcenter/build/) segítségével olyan natív és platformfüggetlen alkalmazásokat hozhat létre, amelyeken a csapata egy biztonságos felhőalapú infrastruktúra használatával dolgozik. A tárház könnyedén csatlakoztatható a Visual Studio App Center alkalmazásban, és minden véglegesítéskor elkezdheti az alkalmazást a felhőben építeni. Nem kell aggódnia a buildkiszolgálók helyi, bonyolult konfigurációk és a munkatárs gépére épülő kód konfigurálása miatt, de a tiéd miatt nem.

A Visual Studio App Center szolgáltatásainak nagyobb teljesítményével tovább automatizálhatja a munkafolyamatot. Az App Center Distribute segítségével automatikusan felszabadíthatja a teszteket és a nyilvános alkalmazásáruházakat. Az App Center-teszttel automatikus felhasználói felületi teszteket is futtathat a felhőben lévő több ezer valódi eszköz- és operációsrendszer-konfiguráción.

**A legfontosabb jellemzők**
- Állítsa be a folyamatos integrációt percek alatt, és hozzon létre alkalmazásokat gyakrabban és gyorsabban.
- Integrálható a GitHub, a BitBucket, az Azure DevOps és a GitLab szolgáltatással.
- Gyors és biztonságos buildeket hozhat létre felügyelt, felhőalapú gépeken.
- Engedélyezze a buildek indítását a teszt elindításához, és ellenőrizze, hogy az alkalmazás valós iOS- és Android-eszközökön épül-e fel.
- Natív és platformfüggetlen támogatás az iOS, Android, macOS, Windows, Xamarin és React Native rendszerhez.
- Testreszabhatja a buildeket a klónozás utáni, az előre megépítendő és a létrehozás utáni parancsfájlok hozzáadásával.

**Referencia**
- [Regisztráció a Visual Studio App Center alkalmazásközpontjában](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Az App Center Build – első lépések](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Az Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), az Azure DevOps szolgáltatás a teljes körű folyamatos integrációés folyamatos kézbesítés (CD) szolgáltatás, amely együttműködik a kívánt Git-szolgáltatóval. Üzembe helyezheti a legtöbb főbb felhőszolgáltatások, amely magában foglalja az Azure-t. A kódot a GitHubon, a GitHub Enterprise Serveren, a GitLaben, a Bitbucket Cloudon vagy az Azure Repos-on kezdheti. Ezután automatizálhatja a kód összeállítását, tesztelését és telepítését a Microsoft Azure, a Google Cloud Platform vagy az Amazon Web Services (AWS) szolgáltatásba.

**A legfontosabb jellemzők**
- **Egyszerűsített feladatalapú felület a CI-kiszolgáló beállításához:** A Microsoft és a nem Microsoft (Node.js, Java) alapú kiszolgálótechnológiák mellett hozzon létre ci-kiszolgálót natív (Android, iOS és Windows) és platformfüggetlen (Xamarin, Cordova és React Native) mobilalkalmazásokhoz is.
- **Bármilyen nyelv, platform és felhő:** Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android és iOS alkalmazások at hozhat létre, tesztelhet és helyezhet üzembe. Párhuzamosan futtassa Linuxon, macOS-en és Windows rendszeren. Üzembe helyezheti az olyan felhőszolgáltatókat, mint az Azure, az AWS és a Google Cloud Platform. Mobilalkalmazások terjesztése bétacsatornákon és alkalmazásáruházakon keresztül.
- **Natív tárolótámogatás:** Hozzon létre új tárolókat könnyedén, és tolja őket bármely rendszerleíró adatbázisba. Telepítse a tárolókat független állomásokra vagy Kubernetes-re.
- **Speciális munkafolyamatok:** Egyszerűen hozhat létre építési láncokat és többfázisú buildeket. Támogatást kaphat a YAML-hez, a tesztelési integrációhoz, a kiadási kapukhoz, a jelentéskészítéshez és egyebekhez.
- **Bővíthető:** A közösség által készített build-, tesztelési és üzembe helyezési feladatok széles skáláját használhatja, amelyek több száz bővítményt tartalmaznak a Slacktől a SonarCloudig. Akár más CI-rendszerekből is telepíthet, például a Jenkins-ből. A webhorgok és a REST API-k segítségével integrálható.
- **Ingyenes felhőalapú buildek:** Ezek a buildek nyilvános és magántárolók számára érhetők el.
- **Más felhőszolgáltatóknak történő telepítés támogatása:** A szállítók közé tartozik az AWS és a Google Cloud Platform.

**Referencia**
- [Ismerkedés az Azure Pipelines útmutatójával](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Bevezetés az Azure DevOps használatába](https://app.vsaex.visualstudio.com/signup/) 
- [Rövid útmutatók](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Az alkalmazásbuildek megfelelő szolgáltatásának kiválasztásához tekintse meg az App Center Build és az [Azure Pipelines összehasonlításáról szóló cikket.](/appcenter/build/choose-between-services)
