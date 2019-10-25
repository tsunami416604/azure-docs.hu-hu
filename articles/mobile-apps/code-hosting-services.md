---
title: A felhőben üzemeltetheti a Mobile Application-forráskódot a GitHub és az Azure DevOps
description: Ismerkedjen meg a Microsoft-szolgáltatásokkal a felhőben a mobil alkalmazás kódjának üzemeltetéséhez szükséges szolgáltatásokkal.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795626"
---
# <a name="cloud-hosted-source-code-management-services"></a>Felhőben üzemeltetett forráskód-kezelési szolgáltatások
Ha olyan fejlesztői csapatokkal rendelkezik, amelyeken több csapattag is dolgozik ugyanazon a kódon, akkor megfelelő helyet kell találnia a kód üzemeltetéséhez. A felhőben tárolt adatok tárolása és a központosított tárház használata lehetővé teszi, hogy mindenki feltöltse, szerkessze és kezelje a programkódokat, más fejlesztőkkel együttműködve a projektekben, és lehetővé teszi, hogy a kód könnyen elérhető legyen, függetlenül attól, hogy Ön internetkapcsolattal rendelkezik.

A Felhőbeli üzemeltetés sokkal egyszerűbb, mint a helyszíni lehetőségek, mivel kevesebb hardverkonfiguráció szükséges, és lehetővé teszi a szervezetek számára, hogy a megvalósítási folyamatot agilis módon végezzék el.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>A forráskód üzemeltetésének előnyei a felhőben
- **Központosított felhőalapú tárolás** az adatok bárhonnan történő megtekintéséhez és kezeléséhez.
- Lehetővé teszi a csapatokon belüli **jobb együttműködés és tisztább kód** használatát a kódok nyomon követéséhez és a projektek kezeléséhez a kiváló szoftverek folyamatos teljesítésének biztosítása érdekében.
- Vegyen **részt** , és megkönnyíti a projektekhez való **hozzájárulást** .
- **Gyorsabb kiadási ciklus** , amellyel a csapata gyorsabban dolgozhat, és **egyszerűen hozzájárulhat** a projektekhez.
- **Csökkentse a költségeket** anélkül, hogy aggódnia kellene a saját hardver-, kiszolgáló-, VPN-és egyéb fenntartásával.

Használja az alábbi szolgáltatásokat az alkalmazásadatok felhőben történő üzemeltetéséhez.

## <a name="github"></a>GitHub
A [GitHub](https://github.com/) egy nyílt forráskódú tárház-üzemeltetési szolgáltatás, amely különböző programozási nyelveken üzemeltet forráskód-projekteket, és nyomon követi az egyes iterációk különböző módosításait.

**Főbb funkciók**
- A **kód üzemeltetése** az összes kóddal egy helyen. Magán-, nyilvános vagy nyílt forráskódú, az összes tárház olyan eszközzel rendelkezik, amely segítséget nyújt a gazdagép, a verzió és a kibocsátási kód megadásához.
- A **kód zökkenőmentes áttekintése** és a beépített felülvizsgálati eszközök lehetővé teszik a kódok áttekintését az egyes csapatok folyamatainak alapvető részeiről.
    - Az ágak elleni védelem, a javaslatok módosítása és a kérelmek felülvizsgálata.
    - Tekintse meg a kontextusban található különbséget és megjegyzést, és küldjön egyértelmű visszajelzést.
- A korai koordinációt és a GitHub **projektmenedzsment eszközeivel**folytatott munkát.
    - Tekintse meg a projekt nagy képét.
    - A GitHubon belül közvetlenül a kód melletti **munkalapok** .
    - A **kártyák húzásával** kioszthatja a problémákat, és lekérheti a csoporttagok számára a lekéréses kérelmeket.
    - **Mérföldkövek** a folyamat rendszerezéséhez és nyomon követéséhez.
    - **Megjegyzések** olyan ötletek rögzítéséhez, amelyek hasznosak lehetnek, de nem tartoznak hozzá egy adott probléma vagy lekéréses kérelemhez.
- Egyszerűen felderítheti és kiválaszthatja a megfelelő eszközöket a jobb kommunikáció és automatizálás érdekében, ha alkalmazásokat vásárol a **[GitHub piactérről](https://github.com/marketplace)** .
- A **Team Management** szolgáltatással egyszerűen kezelhet és növelheti a csapatokat **, a** **felhasználói szerepköröket** a csapatok és a hozzáférési engedélyek rendszerezéséhez új csapat tagjai egy fiókkal.
- **Böngésszen** **a népszerű projektek között, és** kövesse őket.
- A hálózat és az iparág többi részének **megismerése** .

**Hivatkozik**
- [GitHub](https://github.com/)
- [GitHub-útmutatók](https://guides.github.com/)
- [GitHub közösségi fórum](https://github.community/)
- [GitHub piactér](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
Az [Azure DevOps](https://azure.microsoft.com/services/devops/) a verziókövetés lehetőségeiként támogatja az [Azure Repos](https://azure.microsoft.com/services/devops/repos/) és a [Team Foundation verziókövetés (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) használatát. Korlátlan számú ingyenes privát tárházat tartalmaz, amelyek a kiváló minőségű kódok biztosítására, a speciális Fájlkezelés, a kódok keresésére és a fiókirodák házirendjeire vonatkoznak. Az Azure Repos kiválóan használható kis projektekhez, valamint olyan nagy szervezetekhez, amelyeknek natív HRE-támogatásra és speciális szabályzatokra van szükségük.
    
**Főbb funkciók**
- **Korlátlan számú felhőben üzemeltetett git-forráskód** a nyilvános és a privát repók számára
    - A git-ügyfelek támogatása.
    - Webhookok és API-integráció.
- **Indítsa el a következő buildet** egy Repos pull-kérelemből
    - Együttműködés a jobb kódok kiépítéséhez a menetes megbeszélések és az egyes változtatások folyamatos integrációja segítségével.
    - Az Azure Pipelines vagy saját eszközei használatával is beállíthat folyamatos integrációt/folyamatos készregyártást (CI/CD), így automatikusan elindíthatja a buildelést, a tesztelést és az üzembe helyezést minden egyes teljesült lekéréses kérelemmel.
    - A kód minőségének biztosítása a fiókirodai házirendekkel.
- **Központosított verziókövetés a Team Foundation verziószám-vezérlővel**, beleértve a kódok áttekintését.
- **Kapcsolódjon a kódhoz a** Xcode, az Eclipse, a IntelliJ, a Android Studio, a Visual Studio, a Visual Studio Code és más használatával.
- **Hatékony szemantikai kód keresése**.
- A **vállalat készen áll** a Azure Active Directory (ad) való natív integrációval, és egyszerűbbé teszi a kódokhoz való hozzáférés felügyeletének folyamatát.
- A **kódok minőségének biztosítása** olyan ág-házirendekkel, mint például a kódok minimális száma, sikeres buildek megkövetelése és git-egyesítési stratégiák érvényesítése.
- **Kedvenc fejlesztési környezetének összekapcsolásával hozzáférhet a** repóhoz, és kezelheti a munkavégzést.

**Hivatkozik**
- [Ismerkedés az Azure Repostel](https://azure.microsoft.com/services/devops/repos/) 
- [Az Azure Repos dokumentációja](/azure/devops/repos/?view=azure-devops)