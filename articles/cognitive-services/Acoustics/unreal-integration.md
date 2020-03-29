---
title: Project Acoustics Unreal és Wwise integráció
titlesuffix: Azure Cognitive Services
description: Ez a cikk a Project Acoustics Unreal és a Wwise beépülő modulok projektbe való integrálását ismerteti.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: e57212a3002390754aaebc5f2aa9ffb10af230a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243056"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Project Acoustics Unreal és Wwise integráció
Ez a cikk bemutatja, hogyan integrálhatja a Project Acoustics beépülő modulcsomagot a meglévő Unreal és Wwise játékprojektbe.

Szoftverkövetelmények:
* [Unreal motor](https://www.unrealengine.com/) 4,20+
* [Audiokinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1
* [Wwise plug-in az Unreal](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Ha a Wwise Unreal beépülő modul helyett a Wwise SDK közvetlen integrációját használja, olvassa el a Project Acoustics Unreal beépülő modult, és állítsa be a Wwise API-hívásokat.

Ha a Project Acoustics programot a Wwise-tól eltérő hangmotorral szeretné használni, tegyen meg egy javítási kérelmet a [Project Acoustics vitafórumában.](https://github.com/microsoft/ProjectAcoustics/issues) A Project Acoustics Unreal beépülő modul segítségével lekérdezheti az akusztikai adatokat, és API-hívásokat kezdeményezhet a motorhoz.

## <a name="download-project-acoustics"></a>Project Akusztika letöltése
Töltse le a [Project Acoustics Unreal és a Wwise beépülő modulcsomagot,](https://www.microsoft.com/download/details.aspx?id=58090) ha még nem tette meg.

Egy Unreal Engine plug-in és egy Wwise keverő plug-in tartalmazza a csomagot. Az Unreal beépülő modul szerkesztő- és futásidejű integrációt biztosít. A játék során a Project Acoustics Unreal beépülő modul olyan paramétereket számít ki, mint például az egyes játékobjektumok elzáródása az egyes képkockákhoz. Ezek a paraméterek wwise API-hívásokra vannak lefordítva.

## <a name="integration-steps"></a>Integrációs lépések

A csomag telepítéséhez és a játékban való üzembe helyezéséhez kövesse az alábbi lépéseket.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>A Project Acoustics mixer beépülő modul telepítése
1. Nyissa meg a Wwise indítót. A **Beépülő modulok** lap **Új beépülő modulok telepítése csoportjában**válassza a **Hozzáadás a címtárból**lehetőséget.

    ![Beépülő modul telepítése a Wwise indítójára](media/wwise-install-new-plugin.png)

1. Válassza ki a letöltési csomagban található *AcousticsWwisePlugin\ProjectAcoustics* könyvtárat. Ez tartalmazza a Wwise mixer plug-in csomagot.

   A Wwise telepíti a beépülő modult. A Project Acoustics programnak szerepelnie kell a Wwise telepített beépülő moduljainak listáján.  
![A Wwise telepített beépülő modulok listája a Project Acoustics telepítése után](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>A Wwise dedeploy bekerüljön a játékba
Akkor is helyezze át a Wwise-t a játékba, ha már integrálta a Wwise-t. Ez a lépés integrálja a Project Acoustics Wwise beépülő modult.

   > [!NOTE]
   > **Motor beépülő modul:** Ha a Wwise játékbeépülő modulként van telepítve egy Unreal C++ projektben, hagyja ki ezt a lépést. Ha inkább motorbeépülő modulként van telepítve, például azért, mert az Unreal projekt csak Blueprint, a Wwise telepítése a keverő beépülő modulunkkal bonyolultabb. Hozzon létre egy üres Unreal C++ projektet. Zárja be az Unreal szerkesztőt, ha megnyílik, és kövesse a fennmaradó eljárást a Wwise próbabábu-projektbe való telepítéséhez. Ezután másolja ki az üzembe helyezett Wwise beépülő modult.
 
1. A Wwise launcherban válassza az **Browse for project** **Unreal Engine (Unreal Engine)** **Recent Unreal Engine Projects** fület. Nyissa meg a játék Unreal projekt *.project* fájlját.

    ![A Wwise launcher Unreal fül](media/wwise-unreal-tab.png)

1. Válassza **a Wwise integrálása a Projektbe** lehetőséget, vagy **a Wwise módosítása a Projectprogramban**lehetőséget. Ez a lépés a Wwise bináris fájljait integrálja a projektbe, beleértve a Project Acoustics mixer beépülő modult is.

   > [!NOTE]
   > **Motor beépülő modul:** Ha a Wwise-t motorbeépülő modulként használja, és a korábban leírtak szerint hozta létre a próbabábu-projektet, másolja a W8 által telepített mappát: *[DummyUProject]\Plugins\Wwise*. Illessze be *az [UESource]\Engine\Plugins\Wwise könyvtárba.* *[DummyUProject]* az üres Unreal C ++ projekt elérési útja, és *az [UESource]* az, ahol az Unreal Engine források vannak telepítve. A mappa másolása után törölheti a próbabábu-projektet.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>A Project Acoustics Unreal beépülő modul hozzáadása a játékhoz
 
1. Másolja *a* beépülő modul ba. Hozzon létre egy új mappát *[UProjectDir]\Plugins\ProjectAcoustics*, ahol *a [UProjectDir]* a játék projektmappája, amely tartalmazza az *.uproject* fájlt.

   > [!NOTE]
   > **Motorbeépülő modul**: Ha a Wwise-t motorbeépülő modulként használja, akkor a Project Acoustics-t is unreal motorbeépülő modulként kell használnia. A korábban említett célkönyvtár helyett használja *az [UESource]\Motor\Plugins\ProjectAcoustics parancsot.*

1. Ellenőrizze, hogy a *ProjectAcoustics* mappa mellett megjelenik-e egy *Wwise* mappa. Ez tartalmazza a Wwise plug-in együtt bináris a mixer plug-in, hogy a korábban telepített.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>A Wwise Unreal plug-in funkcióinak bővítése
A Project Acoustics Unreal plug-in további viselkedést igényel, amelyet a Wwise Unreal plug-in API-ból [adnak ki ezen irányelvek szerint.](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html) A javítási eljárás automatizálásához egy kötegfájlt is mellékeltünk.

* A *Plugins\ProjectAcoustics\Resources mappán*belül futtassa *a PatchWwise.bat parancsot.* A következő példakép az AcousticsGame mintaprojektünket használja.

    ![A Windows Intéző ablaka a javításhoz wwise kiemelve a parancsfájllal](media/patch-wwise-script.png)

* Ha nincs telepítve a DirectX SDK: A használt Wwise verziójától függően előfordulhat, hogy megjegyzést kell `DXSDK_DIR` fűznie az *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs.* sorához:

    ![A kód szerkesztő bemutatás ' DXSDK' magyarázat ki](media/directx-sdk-comment.png)

* Ha a Visual Studio 2019 használatával fordít: A Wwise összekapcsolási `VSVersion` hibájának megkerüléséhez manuálisan módosítsa az alapértelmezett értéket az *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs fájlban* **vc150-re:**

    ![A "VSVersion" -ot megjelenítő kódszerkesztő "vc150"-re változott.](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Építsd meg a játékot, és ellenőrizd, hogy a Python engedélyezve van-e

1. Fordítsa össze a játékot, és győződjön meg arról, hogy megfelelően épül fel. Ha nem épül fel, a folytatás előtt alaposan ellenőrizze az előző lépéseket.

1. Nyissa meg a projektet az Unreal Szerkesztőben.

    > [!NOTE]
    > **Motor beépülő modul:** Ha a ProjectAcoustics motorbeépülő modult használja, győződjön meg arról is, hogy engedélyezve van a "beépített" beépülő modulok ban.

    Ekkor megjelenik egy új mód, amely azt jelzi, hogy a Project Acoustics integrálva van.

    ![Az akusztikai mód tele van Unreal-ban](media/acoustics-mode-full.png)

1. Győződjön meg arról, hogy az Unreal Python beépülő modulja engedélyezve van-e, hogy a szerkesztő integrációja megfelelően működjön.

    ![A Python-bővítmények engedélyezve vannak az Unreal szerkesztőben](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>A Wwise projekt beállítása a Project Acoustics használatára

Egy példa Wwise projekt szerepel a minták letöltése. Javasoljuk, hogy tekintse meg ezeket az utasításokat. A cikk későbbi részében található képernyőképek ebből a projektből származnak.

#### <a name="bus-setup"></a>Busz beállítása
A Project Acoustics Unreal plug-in a pontos nevű buszon keresi a `Project Acoustics Bus`kapcsolódó keverőbeépülő modult. Hozzon létre egy új audio busz, amely ugyanazt a nevet. A keverő beépülő modul különböző konfigurációkban működhet. De most, azt feltételezzük, hogy ez lesz csak a zengetés feldolgozás. Ez a busz az akusztikát használó összes forrás vegyes zengetési jelét fogja hordozni. Bármilyen buszkeverő szerkezetbe keveredhet. Egy példa jelenik meg itt a Wwise minta projekt, amely szerepel a minta letöltés.

![Wwise buszok bemutató Project Acoustics Bus](media/acoustics-bus.png)

1. Állítsa a busz csatornakonfigurációját *1.0*, *2.0*, *4.0*, *5.1*vagy *7.1-re*. Minden más beállítás nem eredményez kimenetet a buszon.

    ![Csatornakonfigurációs beállítások a Project Acoustics Bus alkalmazáshoz](media/acoustics-bus-channel-config.png)

1. Lépjen be a Project Acoustics Bus részleteibe, és győződjön meg arról, hogy láthatja a **Mixer beépülő modul** lapot.

    ![Wwise with the Mixer Plug-in tab for the Project Acoustics Bus enabled](media/mixer-tab-enable.png)

1. Nyissa meg a **Mixer beépülő modul** lapot, és adja hozzá a projekt akusztikai keverőbe bővítményt a buszhoz.

    ![A Project Acoustics Mixer beépülő modul wwise buszhoz való hozzáadásának diagramja](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Aktor-keverő hierarchia beállítása
A legjobb teljesítmény érdekében a Project Acoustics egyszerre alkalmazza az összes forrásra a digitális jelfeldolgozást. Tehát, a plug-in kell működnie, mint egy keverő plug-in. A Wwise megköveteli, hogy a keverő plug-inek a kimeneti buszon legyenek, bár a kimeneti busz általában a száraz kimeneti jelet hordozza. A Project Acoustics megköveteli, hogy a száraz jel aux buszokon keresztül `Project Acoustics Bus`haladjon, míg a nedves jelet a . A következő folyamat támogatja a fokozatos migrációt erre a jeláramra.

Tegyük fel, hogy van egy meglévő projektje, amely nek van egy aktorkeverő hierarchiája, amely lépéseket, *fegyvereket*és *másokat*tartalmaz a legfelső szinten. Mindegyiknek van egy megfelelő kimeneti busza a száraz keverékhez. Tegyük fel, hogy a kuszakusz használatához lépéseket szeretne áttelepíteni. Először is, hozzon létre egy megfelelő aux busz, hogy készítsen a száraz almix, hogy a gyermek a nyomában kimeneti busz. Például az alábbi képen egy "Száraz" előtagot használtunk a buszok rendszerezéséhez, bár a pontos név nem fontos. Minden méter vagy hatása, hogy volt a nyomában busz továbbra is működik, mint korábban.

![Ajánlott Wwise száraz keverék beállítás](media/wwise-dry-mix-setup.png)

Ezután módosítsa a Lépések aktakeverő buszkimeneti struktúráját az alábbiak szerint: a *Project Acoustics Bus* **beállítása kimeneti busz,** *Dry_Footsteps* pedig felhasználó által definiált aux buszként.

![Ajánlott Wwise aktor keverő busz beállítása](media/actor-mixer-bus-settings.png)

Most minden lépés akusztikai kezelést kap, és a Project Acoustics Bus-on adja ki a zengetést. A száraz jel Dry_Footsteps keresztül vezet, és a szokásos módon térbelivé.

A Project Acoustics csak azokra a hangokra vonatkozik, amelyek a világon 3D-s helyszínnek vannak kitéve. A [Wwise dokumentációját](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)követően a pozicionálási tulajdonságokat az ábrán látható módon kell beállítani. A **3D térbeliítés beállítás** lehet *pozíció* vagy pozíció *+ tájolás,* ha szükséges.

![Ajánlott Wwise Actor helymeghatározási beállítások](media/wwise-positioning.png)

A **Kimeneti busz** nem állítható be egy másik buszra, amely a *Project Acoustics Bus-szal*keveredik. Wwise előírja ezt a követelményt a mixer plug-inek.

Ha azt szeretné, hogy egy gyermek a kitágan akta-keverő hierarchia, hogy ne használja az akusztika, használhatja a "felülírja szülő" rajta, hogy kiválassza azt.

Ha a játék által definiált vagy felhasználó által definiált küldést használ a játék bármely aktakeverőjében, kapcsolja ki őket az aktorkeverőn, hogy elkerülje a zengetés kétszeri alkalmazását.

#### <a name="spatialization"></a>Térbeliítés
A Project Acoustics Wwise keverő beépülő modul alapértelmezés szerint konvolúciós zengetést alkalmaz, így a Wwise a térbeliítést pásztázta. Ha ebben az alapértelmezett csak zengetésű konfigurációban használja a Project Akusztika programot, a száraz keverésen bármilyen csatornakonfigurációt és térbeliítési módszert használhat. Így szinte bármilyen területirevonatkozót keverhet és párosíthat a Project Acoustics zengetéssel. A lehetőségek közé [Ambisonics-alapú binaurális spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) és [a Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
A Project Acoustics egy opcionális spatializert is tartalmaz, amely támogatja az objektumalapú, nagy felbontású HRTF-renderelést és pásztázást. Jelölje be a **Térterület-kezelés végrehajtása** jelölőnégyzetet a keverő beépülő modulbeállításaiközött, és válasszon a *HRTF* vagy *a Pásztázás*lehetőség közül. Is, tiltsa le a felhasználó által definiált aux küld az összes száraz buszok elkerülése térbeli kétszer a Project Acoustics mixer plug-in és wwise. A térbeliítési mód nem módosítható valós időben, mert hangbank-regenerálást igényel. Indítsa újra az Unreal programot, majd a lejátszás kiválasztása előtt indítsa újra a hangbankokat a keverőbeépülő modul konfigurációs módosításainak integrálásához, például a **Térterület-kezelés végrehajtása** jelölőnégyzetbe.

![Wwise Mixer beépülő modul térbeliítési beállításai](media/mixer-spatial-settings.png)

Sajnos más objektumalapú spatializer beépülő modulok jelenleg nem támogatottak. Keverőbeépülő modulként vannak megvalósítva, és a Wwise nem engedélyezi, hogy több keverőbevalót lehessen hozzárendelni egyetlen aktakeverőhöz.  

### <a name="audio-setup-in-unreal"></a>Audio beállítás az Unrealban
1. Először is meg kell sütni a játék szintjén, hogy készítsen egy akusztikai eszköz, amely kerül a *Content\Acoustics*. Konzultáljon az [Unreal Bake bemutató](unreal-baking.md). Néhány előre sütött szint szerepel a mintacsomagban.

1. Hozzon létre egy akusztikai tér színész a jelenetet. Csak hozzon létre egy ilyen szereplők egy szinten, mert képviseli az akusztika az egész szinten.

    ![Akusztikus űrszínész létrehozása az Unreal szerkesztőjében](media/create-acoustics-space.png)

1. Rendelje hozzá a sült akusztikai adateszközt az Acoustics space actor Akusztikai adattárolóhoz. A jelenetednek most már van akusztikája!

    ![Akusztikai eszközhozzárendelés az Unreal szerkesztőjében](media/acoustics-asset-assign.png)

1. Adjon hozzá egy üres akta. Állítsa be az alábbiak szerint.

    ![Az Unreal szerkesztője az Acoustics komponenshasználatát mutatja egy üres aktorban](media/acoustics-component-usage.png)

       
    <sup>1</sup> Adjon hozzá egy Akusztikai audioösszetevőt az aktorhoz. Ez az összetevő hozzáadja a Project Acoustics funkciót a Wwise audio összetevőhöz.
        
    <sup>2</sup> A **Lejátszás a kezdőképernyőn** mező alapértelmezés szerint be van jelölve. Ez a beállítás egy társított Wwise-eseményt indít el a szint indításakor.</li>
         
    <sup>3</sup> Az **Akusztikai paraméterek megjelenítése** jelölőnégyzet segítségével nyomtassa ki a képernyőn a forrásra vonatkozó hibakeresési információkat.

    ![Az Unreal szerkesztő Akusztika panel a hangforráson engedélyezve van a hibakeresési értékeken](media/debug-values.png)

    <sup>4</sup> Wwise esemény hozzárendelése a szokásos Wwise munkafolyamatszerint.
       
    <sup>5</sup> Győződjön meg arról, hogy a **Térbeli hang használata** ki van kapcsolva. Ha egy adott hangösszetevőhöz project akusztikai eszközt használ, nem használhatja egyszerre a Wwise Spatial Audio motorját akusztikához.</li>
       
Készen is van. Mozogj a jelenet körül, és fedezze fel az akusztikus hatásokat!

## <a name="next-steps"></a>További lépések
* Próbálja ki a [Project Acoustics Unreal/Wwise Design tutorial](unreal-workflow.md).
* [Megtanulják, hogyan kell csinálni süt](unreal-baking.md) a játék jelenetek.
