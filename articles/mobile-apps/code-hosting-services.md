---
title: A githubés az Azure DevOps segítségével üzemeltetheti a mobilalkalmazás forráskódját a felhőben
description: Ismerje meg a mobilalkalmazás-kód felhőbeli üzemeltetéséhez nyújtott szolgáltatásokat a Microsoft-szolgáltatásokkal.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240966"
---
# <a name="cloud-hosted-source-code-management-services"></a>Felhőalapú forráskód-kezelési szolgáltatások
Ha fejlesztői csapatai több csapattaggal rendelkeznek, akik ugyanazon a kódbázison dolgoznak, meg kell találnia a kód megfelelő helyét. Az adatok tárolása a felhőben, és egy központi tárház lehetővé teszi, hogy mindenki feltöltheti, szerkesztheti és kezelheti a kódfájlokat. Ők is kölcsönhatásba léphet nek más fejlesztők a projektek. A kód könnyen hozzáférhető, függetlenül attól, hogy hol van, mindaddig, amíg van internet kapcsolat.

Cloud hosting sokkal könnyebb, mint a helyszíni lehetőségeket. Kevesebb hardverkonfigurációt igényel, és lehetővé teszi a szervezetek számára, hogy a megvalósítási folyamatot agilisabb módon hajtsák végre.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>A felhőbeli forráskód üzemeltetésének előnyei
- **Központi felhőalapú tárolás:** Bárhonnan megtekintheti és kezelheti adatait.
- **Jobb együttműködés és tisztább kód:** Nyomon követheti a csapatokon belüli kódot, és projekteket kezelhet a kiváló szoftverek folyamatos szállításának biztosítása érdekében.
- **Könnyebb bekapcsolódni:** Könnyedén hozzájárulhat a projektekhez.
- **Gyorsabb kiadási ciklus:** Dolgozzon gyorsabban a csapataiban, és egyszerűen járuljon hozzá a projektekhez.
- **Csökkentse a költségeket:** Ne aggódjon a saját hardver, szerverek, VPN és így tovább karbantartása miatt.

Az alábbi szolgáltatások használatával az alkalmazásadatok a felhőben.

## <a name="github"></a>GitHub
[A GitHub](https://github.com/) egy nyílt forráskódú tárház hosting szolgáltatás, amely különböző programozási nyelveken üzemelteti a forráskód-projekteket. A GitHub nyomon követi az összes iterációban végrehajtott különböző módosításokat.

**A legfontosabb jellemzők**
- Használja kód hosting tartani az összes kódot egy helyen. A privát, a nyilvános és a nyílt forráskódú adattárak mind olyan eszközökkel vannak felszerelve, amelyek segítenek a kód üzemeltetésében, verziójában és kiadásában.
- Tekintse át a kódot, és használja a beépített felülvizsgálati eszközöket, hogy a kódellenőrzés bármely csapat folyamatának lényeges része legyen:
    - Védje az ágakat, javasoljon módosításokat, és kérjen felülvizsgálatot.
    - Spot különbségek, megjegyzést összefüggésben, és kap egyértelmű visszajelzést.
- Koordináljon korán, maradjon összehangoltan, és még több munkát végeza GitHub projektkezelő eszközeivel:
    - Tekintse meg a projekt teljes képét.
    - Olyan feladattáblákat használhat, amelyek közvetlenül a GitHubon belüli kód mellett találhatók.
    - Húzza a kártyákat a problémák hozzárendeléséhez vagy a csapattagok lekéréses kérelmekhez.
    - Mérföldkövek et állíthat be a folyamat rendszerezéséhez és nyomon követéséhez.
    - Jegyzeteket írhat olyan ötletek rögzítéséhez, amelyek hasznosak lehetnek, de nem tartoznak egy adott problémához vagy lekéréses kérelemhez.
- A [GitHub Piactéralkalmazásainak](https://github.com/marketplace)megvásárlásával könnyedén felfedezheti és kiválaszthatja a jobb kommunikációhoz és automatizáláshoz szükséges eszközöket.
- Csapatok kezelése és termesztése a következők használatával: 
    - Felhasználói szerepkörök a csoportok rendszerezéséhez és az engedélyek eléréséhez.
    - A vitaszálas eszközökkel a témákés a csapat fókuszában tarthatja a beszélgetéseket.
    - Közösségi irányelvek az új csapattagok fiókkal való gyors beállításához.
- Böngésszen és csillag népszerű projektek követni őket.
- hálózat és tanulni másoktól az iparban.

**Referencia**
- [Github](https://github.com/)
- [GitHub-útmutatók](https://guides.github.com/)
- [GitHub közösségi fórum](https://github.community/)
- [GitHub piactér](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Az Azure DevOps](https://azure.microsoft.com/services/devops/) támogatja az [Azure Repos](https://azure.microsoft.com/services/devops/repos/) és [a Team Foundation Version Control (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) forrásvezérlési lehetőségeket. Korlátlan ingyenes privát tárolókkal rendelkezik, amelyek együttműködésen alapuló kódellenőrzéssel, fejlett fájlkezeléssel, kódkereséssel és fiókházirendekkel rendelkeznek a kiváló minőségű kód biztosítása érdekében. Az Azure Repos nagy a kis projektek és a nagy szervezetek, amelyek natív Azure Active Directory-támogatás és a speciális szabályzatok.
    
**A legfontosabb jellemzők**
- Korlátlan felhőben üzemeltetett Git-forráskód-tárházat használjon nyilvános és privát adattáraihoz:
    - Szerezzen támogatást bármely Git-ügyfélhez.
    - Használjon webhorgokat és API-integrációt.
- Indítsa el a következő buildet egy repo lekéréses kérelemből:
    - Együttműködés, hogy jobb kódot építsen ki a szálas vitafórum és a folyamatos integráció használatával minden egyes módosításhoz.
    - Állítsa be a folyamatos integrációt/folyamatos kézbesítést (CI/CD) úgy, hogy minden befejezett lekéréses kérelemmel automatikusan elindítsa a buildeket, teszteket és üzembe helyezéseket. Használhatja az Azure-folyamatok vagy az eszközök.
    - A kódminőséget ágszabályzatokkal védheti.
- A központosított verziókövetés karbantartása a Team Foundation verzióvezérléssel, amely kódellenőrzést is tartalmaz.
- Csatlakozzon a kódhoz az Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code és más eszközök használatával.
- Használjon hatékony szemantikai kódkeresést.
- Profitálhat a nagyvállalati használatra kész funkciókelőnyeiből. Az Azure DevOps natív integrációval rendelkezik az Azure Active Directoryval, amely leegyszerűsíti a kódtárolókhoz való hozzáférés kezelését.
- A kódminőség biztosítása az elágazási szabályzatokkal, például a kódfelülvizsgálatok minimális számával, a sikeres buildek követelményeivel és a Git-egyesítési stratégiák végrehajtásával.
- Csatlakoztassa kedvenc fejlesztői környezetét az adattrakók eléréséhez és a munka kezeléséhez.

**Referencia**
- [Ismerkedés az Azure Repos-okkal](https://azure.microsoft.com/services/devops/repos/) 
- [Az Azure Repos dokumentációja](/azure/devops/repos/?view=azure-devops)