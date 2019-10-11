---
title: Projekt-akusztikai Unreal és Wwise Integration
titlesuffix: Azure Cognitive Services
description: Ez a cikk ismerteti, hogyan integrálható a projekthez tartozó Wwise-beépülő modulok a projektbe.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243056"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projekt-akusztikai Unreal és Wwise Integration
Ez a cikk azt ismerteti, hogyan integrálható a projekt akusztikai beépülő modulja a meglévő Unreal és Wwise game projektbe.

A szoftverre vonatkozó követelmények:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 +
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1
* [Wwise beépülő modul az Unreal-hez](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  
  Ha a Wwise SDK közvetlen integrációját használja az Wwise Unreal beépülő modul helyett, tekintse meg a Project Acoustics Unreal beépülő modulját, és állítsa be a Wwise API-hívásokat.

Ha a Project Acoustics-t a Wwise-től eltérő hangmotorral kívánja használni, a [projekt akusztikai vitafórumában](https://github.com/microsoft/ProjectAcoustics/issues)végezze el a javító kérést. A Project akusztikai Unreal beépülő modullal lekérdezheti az akusztikai adatait, és API-hívásokat indíthat a motorjának.

## <a name="download-project-acoustics"></a>Projekt akusztikai letöltése
Ha még nem tette meg, töltse le a [Project Acoustics Unreal and Wwise beépülőmodul-csomagot](https://www.microsoft.com/download/details.aspx?id=58090) .

A csomag tartalmazza az Unreal Engine beépülő modult és a Wwise keverő beépülő modulját. Az Unreal beépülő modul szerkesztői és futásidejű integrációt biztosít. A játékmenet során a projekt akusztikai Unreal beépülő modulja kiszámítja a paramétereket, például az egyes képkockákhoz tartozó minden egyes játék objektumnál az elzáródást. Ezek a paraméterek Wwise API-hívásokra vannak lefordítva.

## <a name="integration-steps"></a>Integrációs lépések

A következő lépésekkel telepítheti a csomagot, és telepítheti azt a játékba.

### <a name="install-the-project-acoustics-mixer-plug-in"></a>A Project akusztikai keverő beépülő moduljának telepítése
1. Nyissa meg a Wwise-indítót. A **beépülő modulok** lap **új beépülő modulok telepítése**területén válassza a **Hozzáadás a címtárból**lehetőséget.

    ![Beépülő modul telepítése a Wwise-Indítóban](media/wwise-install-new-plugin.png)

1. Válassza ki a letöltési csomagban található *AcousticsWwisePlugin\ProjectAcoustics* könyvtárat. Tartalmazza a Wwise keverő beépülő modulját.

   A Wwise telepíti a beépülő modult. A Project akusztikai elemeknek szerepelniük kell a Wwise telepített beépülő moduljainak listáján.  
![A Wwise telepített beépülő modulok listája a Project akusztikus telepítése után](media/unreal-integration-post-mixer-plugin-install.png)

### <a name="dedeploy-wwise-into-your-game"></a>Wwise detelepítése a játékba
Még akkor is telepítse újra a Wwise a játékba, ha már integrálta a Wwise-t. Ez a lépés integrálja a projekt akusztikai Wwise beépülő modulját.

   > [!NOTE]
   > **Motor beépülő modul:** Ha egy Unreal C++ -projektben Wwise van telepítve, hagyja ki ezt a lépést. Ha a rendszer a motor beépülő moduljának részeként telepíti, például azért, mert az Unreal projekt csak terv, a Wwise üzembe helyezése összetettebb. Hozzon létre egy üres C++ , üres, Unreal-projektet. Ha megnyílik az Unreal Editor, akkor a Wwise üzembe helyezéséhez kövesse a többi eljárást a dummy projektbe. Ezután másolja ki a telepített Wwise beépülő modult.
 
1. A Wwise-Indítóban válassza az **Unreal Engine (az Unreal Engine** ) fület. Válassza a **legutóbbi Unreal Engine-projektek** melletti "hamburger" (ikon) menüt, majd válassza a **Tallózás a projekthez**lehetőséget. Nyissa meg a játék Unreal Project *. Project* fájlt.

    ![Az Wwise Launcher Unreal lapja](media/wwise-unreal-tab.png)

1. Válassza a **Wwise integrálása a projektben** vagy **a Wwise módosítása a projektben**lehetőséget. Ez a lépés integrálja a Wwise bináris fájljait a projektbe, beleértve a projekt akusztikai keverő beépülő modulját.

   > [!NOTE]
   > **Motor beépülő modul:** Ha a Wwise motorként használja, és a korábban leírtak szerint hozta létre a próbabábu-projektet, másolja a Wwise telepített mappát: *[DummyUProject] \Plugins\Wwise*. Illessze be a *[UESource] \Engine\Plugins\Wwise*. *[DummyUProject]* az üres Unreal C++ projekt elérési útja, a *[UESource]* pedig az Unreal Engine-források telepítésének helye. A mappa másolása után törölheti a dummy-projektet.

### <a name="add-the-project-acoustics-unreal-plug-in-to-your-game"></a>Adja hozzá a projekt akusztikai Unreal beépülő modulját a játékhoz
 
1. Másolja a *Unreal\ProjectAcoustics* mappát a beépülőmodul-csomagban. Hozzon létre egy új mappát *[UProjectDir] \Plugins\ProjectAcoustics*, ahol a *[UProjectDir]* a játék Project mappája, amely tartalmazza a *. uproject* fájlt.

   > [!NOTE]
   > **Motor beépülő**modul: Ha Wwise-t használ, akkor az Unreal Engine beépülő modullal kell használnia a Project akusztikat is. A korábban hivatkozott cél könyvtára helyett használja a *[UESource] \Engine\Plugins\ProjectAcoustics*.

1. Ellenőrizze, hogy megjelenik-e a *Wwise* mappa a *ProjectAcoustics* mappa mellett. A Wwise beépülő modult, valamint a korábban üzembe helyezett keverő beépülő modul bináris fájljait tartalmazza.

### <a name="extend-wwise-unreal-plug-in-functionality"></a>A Wwise Unreal beépülő modul funkcióinak kiterjesztése
A projekt akusztikai Unreal beépülő modulja a Wwise Unreal beépülő modul API-ban elérhető további viselkedést igényli [ezen irányelvek](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)alapján. Mellékeltünk egy batch-fájlt a javítási eljárás automatizálásához.

* A *Plugins\ProjectAcoustics\Resources*-on belül futtassa a *PatchWwise. bat fájlt*. Az alábbi példában szereplő ábra a AcousticsGame minta projektjét használja.

    ![Egy Windows Intéző ablak, amely a patch Wwise kiemelve](media/patch-wwise-script.png)

* Ha nincs telepítve a DirectX SDK: az Ön által használt Wwise függően előfordulhat, hogy megjegyzésbe kell tennie a `DXSDK_DIR` *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*tartalmazó sort:

    ![A "DXSDK" nevű Kódszerkesztő megjegyzése](media/directx-sdk-comment.png)

* Ha a Visual Studio 2019 segítségével fordítja le a fordítást: a Wwise-mel való összekapcsolási hiba megoldásához manuálisan módosítsa az alapértelmezett `VSVersion` értéket a *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* -ben a **vc150**értékre:

    ![A "VSVersion" nevű Kódszerkesztő "vc150" értékre változott](media/vsversion-comment.png)

### <a name="build-the-game-and-check-that-python-is-enabled"></a>Hozza létre a játékot, és győződjön meg arról, hogy a Python engedélyezve van

1. Fordítsa le a játékát, és győződjön meg arról, hogy megfelelően épít. Ha nem épít, a folytatás előtt tekintse meg figyelmesen az előző lépéseket.

1. Nyissa meg a projektet az Unreal Editorban.

    > [!NOTE]
    > **Motor beépülő modul:** Ha ProjectAcoustics-t használ, akkor győződjön meg arról is, hogy engedélyezve van a beépített beépülő modulok alatt.

    Ekkor egy új mód jelenik meg, amely azt jelzi, hogy a projekt akusztikai integrációja megtörtént.

    ![Az akusztikai mód teljes az Unreal-ben](media/acoustics-mode-full.png)

1. Győződjön meg arról, hogy az Unreal-hez készült Python beépülő modul engedélyezve van, hogy a szerkesztő-integráció megfelelően működjön.

    ![Az Unreal Editorban engedélyezve van a Python-bővítmények](media/ensure-python.png)

### <a name="set-up-your-wwise-project-to-use-project-acoustics"></a>A Wwise-projekt beállítása a Project akusztika használatára

Egy példa Wwise projekt szerepel a minták letöltésében. Javasoljuk, hogy tekintse meg ezeket az utasításokat. A cikk későbbi részében található képernyőképek ebből a projektből származnak.

#### <a name="bus-setup"></a>Busz beállítása
A projekt akusztikai Unreal beépülő modulja egy olyan buszon keresi a társított keverő beépülő modult, amelynek a pontos neve `Project Acoustics Bus`. Hozzon létre egy új, azonos nevű hangbuszt. A keverő beépülő modul különböző konfigurációkban működhet. Most azonban feltételezzük, hogy csak reverb-feldolgozásra lesz felhasználva. Ez a busz a vegyes reverb-jeleket fogja használni az összes olyan forráshoz, amely akusztikai tevékenységet használ. Bármilyen busz keverési struktúrában kombinálható. Itt látható egy példa a minta letöltésében szereplő Wwise-minta projektből.

![A Project akusztikus buszt bemutató Wwise-buszok](media/acoustics-bus.png)

1. Állítsa be a csatorna konfigurációját a buszon a *1,0*, *2,0*, *4,0*, *5,1*vagy *7,1*értékre. Bármely más beállítás nem fog kimenetet adni a buszon.

    ![A Project akusztikai busz csatornájának konfigurációs beállításai](media/acoustics-bus-channel-config.png)

1. Lépjen be a Project akusztikai busz adataiba, és ellenőrizze, hogy látható-e a **keverő beépülő modul** lapja.

    ![Wwise a Project akusztikai busz számára engedélyezett keverő beépülő modul lapján](media/mixer-tab-enable.png)

1. Lépjen a **keverő beépülő** modul lapra, és adja hozzá a Project akusztikai keverő beépülő modult a buszhoz.

    ![A Project akusztikai keverő beépülő modul Wwise-buszhoz való hozzáadásának ábrája](media/add-mixer-plugin.png)

#### <a name="actor-mixer-hierarchy-setup"></a>Actor-mixer hierarchia beállítása
A legjobb teljesítmény érdekében a Project Acoustics a hangalapú digitális jelfeldolgozást egyszerre minden forrásra alkalmazza. Így a beépülő modulnak keverő beépülő modulként kell működnie. A Wwise a kimeneti buszon csatlakoztatni kell a keverő beépülő modulokat, bár a kimeneti busz általában a száraz kimeneti jeleket végzi. A Project akusztika megköveteli, hogy a száraz jelet Aux-buszokon keresztül irányítsa a rendszer, míg a nedves jelet a `Project Acoustics Bus` értékre kell vinni. A következő folyamat támogatja a fokozatos áttelepítést ebbe a jelerősségbe.

Tegyük fel, hogy van egy meglévő projektje, amely egy Actor-mixer hierarchiát tartalmaz, amely a legfelső szinten *lépteket*, *fegyvereket*és másokat tartalmaz. Mindegyikhez tartozik egy megfelelő kimeneti busz a száraz kombinációhoz. Tegyük fel, hogy át kívánja telepíteni az akusztikai használatot. Először hozzon létre egy megfelelő Aux-buszt, hogy a kimeneti buszt tartalmazó száraz submix vigye. Például a következő képen a "Dry" előtagot használtuk a buszok rendszerezéséhez, bár a pontos név nem fontos. A nyomdokain lévő buszon megjelenő mérőszámok vagy hatások továbbra is ugyanúgy működnek.

![Ajánlott Wwise Dry mix-telepítő](media/wwise-dry-mix-setup.png)

Ezután módosítsa a következő lépésként a busz kimeneti struktúráját a nyomdokain lévő színész-keverőhöz a következőképpen: a *Project akusztikai busz* a **kimeneti buszként**van beállítva, és a *Dry_Footsteps* felhasználó által definiált Aux-buszként van beállítva.

![Ajánlott Wwise Actors keverő busz beállítása](media/actor-mixer-bus-settings.png)

Most már minden lépés akusztikai kezelést és kimenetet eredményez a projekt akusztikai Buszján. A száraz jelet a rendszer a Dry_Footsteps-on keresztül irányítja át, a szokásos módon.

A Project akusztika csak olyan hangok esetében érvényes, amelyeken 3D-s hely van a világon. A [Wwise dokumentációjában](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)a helymeghatározási tulajdonságokat az ábrán látható módon kell beállítani. Ha szükséges, a **3D spatialization** beállítása lehet *pozíció* vagy *pozíció + tájolás* .

![Ajánlott Wwise-szereplők pozicionálási beállításai](media/wwise-positioning.png)

A **kimeneti busz** nem állítható be más buszra, amely összekeveri a *Project akusztikai busszal*. A Wwise ezt a követelményt a keverő beépülő moduljaira kényszeríti.

Ha azt szeretné, hogy a színész-keverő hierarchiában gyermek ne használjon akusztikai tényezőket, a "szülő felülbírálása" lehetőséggel választhatja ki.

Ha játék által definiált vagy felhasználó által definiált küldést használ a játékba tartozó bármely színész-keverőhöz, kapcsolja ki őket a keverőben, és ne alkalmazza a reverb szolgáltatást kétszer.

#### <a name="spatialization"></a>Spatialization
A Project akusztikai Wwise keverő beépülő modulja alapértelmezés szerint a következőt alkalmazza, így a Wwise a pásztázó spatialization hajtja végre. Ha a Project Acoustics szolgáltatást használja az alapértelmezett reverb-konfigurációban, a száraz kombinációban bármilyen csatorna-konfigurációt és spatialization metódust használhat. Így szinte bármilyen spatializer keverheti és egyeztetheti a projekt akusztikai reverb használatával. A lehetőségek közé tartozik a [Ambisonics-alapú binaurális spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) és a [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
A Project akusztika egy opcionális spatializer tartalmaz, amely támogatja az objektumon alapuló, nagy felbontású HRTF renderelést és pásztázást is. Jelölje be a **spatialization elvégzése** jelölőnégyzetet a keverő beépülő modul beállításainál, és válassza a *HRTF* vagy a *pásztázás*lehetőséget. Emellett tiltsa le a felhasználó által definiált Aux-küldést az összes száraz buszra, hogy ne spatializing kétszer a projekt akusztikai keverő beépülő modul és a Wwise. A spatialization mód valós időben nem módosítható, mert a hangbank újragenerálása szükséges. Indítsa újra az Unreal-t, majd a soundbanks újbóli létrehozása előtt válassza a lejátszás elemet a keverő beépülő modul konfigurációjának módosításainak integrálásához, például a **spatialization elvégzése** jelölőnégyzetet.

![Wwise-keverő beépülő modul spatialization beállításai](media/mixer-spatial-settings.png)

Sajnos más objektum-alapú spatializer beépülő modulok jelenleg nem támogatottak. A rendszer keverő beépülő modulokként implementálja őket, és a Wwise nem engedélyezi több keverő beépülő modul hozzárendelését egyetlen Actor-mixerhez.  

### <a name="audio-setup-in-unreal"></a>Hang beállítása az Unreal-ben
1. Először is sütni kell a játék szintjét egy akusztikai eszköz létrehozásához, amely a *Content\Acoustics*kerül. Forduljon az [Unreal Bake oktatóanyaghoz](unreal-baking.md). A mintavételi csomagban néhány előre elsütött szint is szerepel.

1. Hozzon létre egy akusztikai helyet a saját jelenetében. Csak egy szinten hozzon létre egy ilyen résztvevőt, mert az a teljes szint akusztikai értékeit képviseli.

    ![Akusztikai terület színész létrehozása az Unreal Editorban](media/create-acoustics-space.png)

1. Rendelje hozzá a kisült akusztikai adategységet az akusztikai terület Actors-adattárolóhelyéhez. A jelenet most már akusztikai!

    ![Akusztikai eszközök hozzárendelése az Unreal Editorban](media/acoustics-asset-assign.png)

1. Adjon hozzá üres szereplőt. Konfigurálja az alábbiak szerint.

    ![Az Unreal Editor az akusztikus összetevők használatát mutatja be egy üres színészben](media/acoustics-component-usage.png)

       
    <sup>1</sup> akusztikai hangösszetevő hozzáadása a szereplőhöz. Ez az összetevő hozzáadja a Project akusztikai funkcióit a Wwise audio összetevőhöz.
        
    <sup>2</sup> a **play on Start** Box alapértelmezés szerint ki van választva. Ez a beállítás egy társított Wwise eseményt indít el a szint indításakor.</li>
         
    <sup>3</sup> az **akusztikai paraméterek megjelenítése** jelölőnégyzet bejelölésével kinyomtathatja a forrásról a képernyőn megjelenő hibakeresési információkat.

    ![Az Unreal Editor akusztikai paneljén a hangforráson engedélyezve vannak a hibakeresési értékek](media/debug-values.png)

    <sup>4</sup> rendeljen egy Wwise eseményt a szokásos Wwise-munkafolyamathoz.
       
    <sup>5</sup> ellenőrizze, hogy ki van-e kapcsolva a **térbeli hang használata** . Ha egy adott hangösszetevőhöz a Project Acoustics szolgáltatást használja, akkor nem használhatja egyszerre a Wwise térbeli hangmotorját akusztikai eszközökhöz.</li>
       
Készen is van. Lépjen a helyszínre, és fedezze fel az akusztikai hatásokat!

## <a name="next-steps"></a>Következő lépések
* Próbálja ki a [Project Acoustics Unreal/Wwise design oktatóanyagát](unreal-workflow.md).
* Ismerje meg [, hogyan végezheti](unreal-baking.md) el a játékbeli jelenetek kisütését.
