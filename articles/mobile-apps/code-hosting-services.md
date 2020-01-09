---
title: A felhőben üzemeltetheti a Mobile Application-forráskódot a GitHub és az Azure DevOps
description: Ismerkedjen meg a Microsoft-szolgáltatásokkal a felhőben a mobil alkalmazás kódjának üzemeltetéséhez szükséges szolgáltatásokkal.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8662b9f77614339ff514fa4fcf97dc1ee8fc7417
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454488"
---
# <a name="cloud-hosted-source-code-management-services"></a>Felhőben üzemeltetett forráskód-kezelési szolgáltatások
Ha olyan fejlesztői csapattal rendelkezik, amelyeken több csapattag is dolgozik ugyanazon a kódon, akkor megfelelő helyet kell találnia a kód üzemeltetéséhez. A felhőben tárolt adatok tárolása és központi tárháza lehetővé teszi, hogy mindenki feltöltse, szerkessze és kezelhesse a kódrészleteket. Emellett más fejlesztőket is kezelhetnek a projektekben. A kód könnyen elérhető, függetlenül attól, hogy hol van, amíg van internetkapcsolata.

A felhőalapú üzemeltetés sokkal egyszerűbb, mint a helyszíni lehetőségek. Kevesebb hardverkonfiguráció szükséges, és lehetővé teszi, hogy a szervezetek agilis módon végezzék el a megvalósítási folyamatot.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>A forráskód üzemeltetésének előnyei a felhőben
- **Központosított Felhőbeli tárolás:** Adatait bárhonnan megtekintheti és kezelheti.
- **Jobb együttműködés és tisztább kód:** A csapatokon belüli kódok nyomon követése és a projektek kezelése a kiváló szoftverek folyamatos teljesítésének biztosítása érdekében.
- **Könnyebben részt vehet:** Egyszerűen járulhat hozzá a projektekhez.
- **Gyorsabb kiadási ciklus:** Gyorsabban dolgozhat a csapatokban, és egyszerűen hozzájárulhat projektjeihez.
- **Csökkentse a költségeket:** Ne aggódjon a saját hardverek, kiszolgálók, VPN stb. fenntartásával.

Használja az alábbi szolgáltatásokat az alkalmazásadatok felhőben történő üzemeltetéséhez.

## <a name="github"></a>GitHub
A [GitHub](https://github.com/) egy nyílt forráskódú tárház-üzemeltetési szolgáltatás, amely különböző programozási nyelveken üzemeltet forráskód-projekteket. A GitHub nyomon követi az egyes iterációk különböző módosításait.

**Fő funkciók**
- A Code-hosting használatával egyetlen helyen tarthatja meg az összes kódot. A magán-, nyilvános és nyílt forráskódú adattárak mindegyike rendelkezik olyan eszközökkel, amelyek segítséget nyújtanak a gazdagép, a verzió és a kibocsátási kód megadásához.
- Tekintse át a kódot, és használja a beépített felülvizsgálati eszközöket a kódok áttekintéséhez a csapat folyamatainak alapvető részéről:
    - Az ágak elleni védelem, a javaslatok módosítása és a kérelmek felülvizsgálata.
    - Direktszínek, megjegyzések a kontextusban, és egyértelmű visszajelzéseket kaphat.
- A korai koordinációt és a GitHub projektmenedzsment eszközeivel folytatott munkát:
    - Tekintse meg a projekt nagy képét.
    - A GitHubon belül közvetlenül a kód melletti munkalapokat használjon.
    - Húzza a kártyákat a problémák hozzárendeléséhez vagy a csapattagok lekéréséhez.
    - Mérföldkövek beállítása a folyamat rendszerezéséhez és nyomon követéséhez.
    - Írási megjegyzésekkel rögzíthet olyan ötleteket, amelyek hasznosak lehetnek, de nem tartoznak hozzá egy adott probléma vagy pull-kérelemhez.
- Egyszerűen felderítheti és kiválaszthatja a megfelelő eszközöket a jobb kommunikáció és automatizálás érdekében, ha alkalmazásokat vásárol a [GitHub piactérről](https://github.com/marketplace).
- Csapatok kezelése és növelése a használatával: 
    - A csoportok és hozzáférési engedélyek rendszerezését segítő felhasználói szerepkörök.
    - A témákkal és a csapatokkal kapcsolatos beszélgetések megbeszéléséhez
    - Közösségi irányelvek az új csapattagok gyors beállításához egy fiókkal.
- Böngésszen a népszerű projektek között, és kövesse őket.
- A hálózat és az iparág többi részének megismerése.

**Hivatkozik**
- [GitHubon](https://github.com/)
- [GitHub-útmutatók](https://guides.github.com/)
- [GitHub közösségi fórum](https://github.community/)
- [GitHub piactér](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
Az [Azure DevOps](https://azure.microsoft.com/services/devops/) a verziókövetés lehetőségeiként támogatja az [Azure Repos](https://azure.microsoft.com/services/devops/repos/) és a [Team Foundation verziókövetés (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) használatát. Korlátlan számú ingyenes privát tárházat tartalmaz, amelyek a kiváló minőségű kódok biztosítására, a speciális Fájlkezelés, a kódok keresésére és a fiókirodák házirendjeire vonatkoznak. Az Azure Repos kiválóan használható olyan kis projektekhez és nagy szervezetekhez, amelyek natív Azure Active Directory támogatást és speciális szabályzatokat igényelnek.
    
**Fő funkciók**
- Korlátlan, felhőben üzemeltetett git-forráskódot használhat a nyilvános és privát repók számára:
    - Támogatás kérése bármely git-ügyfélhez.
    - Webes hookok és API-integráció használata.
- Indítsa el a következő buildet egy tárház-lekéréses kérelemből:
    - Együttműködés a jobb kódok kiépítéséhez a menetes megbeszélések és az egyes változtatások folyamatos integrációja segítségével.
    - Folyamatos integráció/folyamatos teljesítés (CI/CD) beállítása a buildek, tesztek és központi telepítések automatikus elindításához minden befejezett lekéréses kérelemmel. Használhatja az Azure-folyamatokat és az eszközöket.
    - A kód minőségének biztosítása a fiókirodai házirendekkel.
- Kezelje a központosított verziókövetés kezelését a Team Foundation verziókövetés szolgáltatásával, amely tartalmazza a kód áttekintését.
- A kóddal a Xcode, az Eclipse, a IntelliJ, a Android Studio, a Visual Studio, a Visual Studio Code és más használatával csatlakozhat.
- Hatékony szemantikai kód keresésének használata.
- A nagyvállalati használatra kész funkciók előnyeit élvezheti. Az Azure DevOps natív integrációja Azure Active Directoryekkel, ami leegyszerűsíti a kódokhoz való hozzáférés felügyeletének folyamatát.
- A kódok minőségének biztosítása olyan ág-házirendekkel, mint például a kódok minimális száma, a sikeres buildek követelményei és a git Merge-stratégiák betartatása.
- Kedvenc fejlesztési környezetének összekapcsolásával hozzáférhet a repóhoz, és kezelheti a munkavégzést.

**Hivatkozik**
- [Ismerkedés az Azure Repostel](https://azure.microsoft.com/services/devops/repos/) 
- [Az Azure Repos dokumentációja](/azure/devops/repos/?view=azure-devops)