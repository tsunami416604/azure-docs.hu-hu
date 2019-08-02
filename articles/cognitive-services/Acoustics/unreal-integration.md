---
title: Projekt-akusztikai Unreal és Wwise Integration
titlesuffix: Azure Cognitive Services
description: Ez az útmutató leírja, hogyan integrálható a projekt a Wwise beépülő modult a projektbe.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 5511dd6b9a7d77c0988a94fef747a30d25bb4fc3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706625"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projekt-akusztikai Unreal és Wwise Integration
Ez a útmutató a projekt akusztikai beépülő moduljának részletes integrációs lépéseit tartalmazza a meglévő Unreal és Wwise game projektben. 

A szoftverre vonatkozó követelmények:
* [Unreal Engine](https://www.unrealengine.com/) 4,20 vagy 4,21
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018,1.\*
* [Wwise beépülő modul az Unreal-hez](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Ha a Wwise SDK közvetlen integrációját használja a Wwise Unreal beépülő modul használata helyett, tekintse át a Project Acoustics Unreal beépülő modulját, és állítsa be a Wwise API-hívásokat.

Ha a Project Acoustics szolgáltatást nem a Wwise, hanem egy hangmotorral szeretné használni, a [projekt akusztikai vitafórumán](https://github.com/microsoft/ProjectAcoustics/issues)készítsen javító kérést. A Project Acoustics Unreal beépülő modullal lekérdezheti az akusztikai adatait, majd API-hívásokat végezhet a motorján.

## <a name="download-project-acoustics"></a>Projekt akusztikai letöltése
Ha még nem tette meg, töltse le a [Project Acoustics Unreal & Wwise plugin-csomagot](https://www.microsoft.com/download/details.aspx?id=58090)). 

Egy Unreal Engine beépülő modult és egy Wwise keverő beépülő modult tartalmazott a csomagban. Az Unreal beépülő modul szerkesztői és futásidejű integrációt biztosít. A játékmenet során a projekt akusztikai Unreal beépülő modulja kiszámítja a paramétereket, például az egyes képkockákhoz tartozó minden egyes játék objektumnál az elzáródást. Ezek a paraméterek Wwise API-hívásokra vannak lefordítva.

## <a name="review-integration-steps"></a>Integrációs lépések áttekintése

Ezek a főbb lépések a csomag telepítésére és a játékba való üzembe helyezésére vonatkoznak.
1. A Project akusztikai Wwise keverő beépülő moduljának telepítése
2. (Újra) telepítse a Wwise a játékba. Ez a lépés a keverő beépülő modult propagálja a játék projektbe.
3. Adja hozzá a Project akusztikai Unreal beépülő modulját a játékhoz
4. A Wwise Unreal beépülő modul funkcióinak kiterjesztése
5. Játék létrehozása és a Python engedélyezése
6. A Wwise-projekt beállítása a Project akusztika használatára
7. Hang beállítása az Unreal-ben

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. A Project akusztikai keverő beépülő moduljának telepítése
* Nyissa meg a Wwise-indítót, majd a **plugins** ( **új beépülő modulok telepítése**) lapon válassza a **Hozzáadás a címtárból**lehetőséget. 

    ![Képernyőkép: beépülő modul telepítése a Wwise-Indítóban](media/wwise-install-new-plugin.png)

* Válassza ki `AcousticsWwisePlugin\ProjectAcoustics` a letöltött csomag részét képező könyvtárat. Tartalmazza a Wwise keverő beépülő modulját.

* A Wwise telepíti a beépülő modult. A Project akusztika ekkor megjelenik a Wwise telepített plugins listájában.
![Képernyőkép a Wwise telepített beépülő modulok listájáról a Project akusztikai telepítése után](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Újra) telepítse a Wwise a játékba
Telepítse újra a Wwise a játékba még akkor is, ha már integrálva van a Wwise. Ezzel felveszi a projekt akusztikai Wwise beépülő modulját.

* **Motor beépülő modul:** Ha egy Unreal C++ -projektben Wwise van telepítve, ugorja át ezt a lépést. Ha a modult egy motor beépülő modulként telepíti, például azért, mert az Unreal-projekt csak terv, a Wwise üzembe helyezése összetettebb. Hozzon létre egy cumit C++ , üres Unreal-projektet, majd az Unreal Editor megnyitása után a fennmaradó eljárást követve telepítse a Wwise ebbe a dummy-projektbe. Ezután másolja ki a telepített Wwise beépülő modult.
 
* A Wwise-Indítóban kattintson az **Unreal Engine (irreális motor** ) fülre, majd a **legutóbbi Unreal Engine-projektek** melletti hamburger menüre, és válassza a **Tallózás a projekthez**lehetőséget. Nyissa meg a játék Unreal `.uproject` Project-fájlját.

    ![A Wwise Launcher Unreal lapjának képernyőképe](media/wwise-unreal-tab.png)

* Ezután kattintson **a Wwise integrálása a projektben** vagy **a Wwise módosítása a projektben**elemre. Ez a lépés (újra) integrálja a Wwise bináris fájljait a projektbe, mostantól tartalmazza a Project akusztikai keverő beépülő modulját.

* **Motor beépülő modul:** Ha Wwise használ, és a fentiek szerint hozta létre a dummy-projektet, másolja `[DummyUProject]\Plugins\Wwise` `[UESource]\Engine\Plugins\Wwise`ki a Wwise telepített mappát, és illessze be a vágólapra. `[DummyUProject]`az üres Unreal C++ projekt elérési útja, `[UESource]` és az a hely, ahol az Unreal Engine-források telepítve vannak. Ha elkészült a másolással, törölheti a dummy-projektet.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. Adja hozzá a Project akusztikai Unreal beépülő modulját a játékhoz
 
* Másolja a `Unreal\ProjectAcoustics` mappát a beépülő modul csomagjába, és hozzon `[UProjectDir]\Plugins\ProjectAcoustics`létre egy `UProjectDir` új mappát, ahol a a játék Project `.uproject` mappája, amely tartalmazza a fájlt.
  * **Motor beépülő modul**: Ha a Wwise-t motor-beépülő modulként használja, a Project Acoustics-et is használja Unreal Engine beépülő modulként. A fenti cél könyvtár helyett használja a következőket: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Erősítse meg, hogy `Wwise` a `ProjectAcoustics` mappa mellett megjelenik egy mappa. Tartalmazza a Wwise beépülő modult, valamint bináris fájlokat a keverő beépülő modulhoz, amelyet a fenti 2. lépésben üzembe helyezett (újra).

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. A Wwise Unreal beépülő modul funkcióinak kiterjesztése
* A Project akusztikai Unreal beépülő moduljának további viselkedést kell kitennie a Wwise Unreal plugin API-n belül [ezen irányelvek](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html)alapján. Mellékeltünk egy batch-fájlt a javítási eljárás automatizálásához. 
* Belül `Plugins\ProjectAcoustics\Resources`futtassa a `PatchWwise.bat`parancsot. Az alábbi ábra a AcousticsGame minta projektjét használja.

    ![Képernyőkép – a Windows Intéző ablakának kiemelése a megadott szkripttel a patch Wwise](media/patch-wwise-script.png)

* Ha nem telepítette a DirectX SDK-t, jegyezze fel a DXSDK_DIR tartalmazó sort a következőben:`[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Képernyőkép a Kódszerkesztő DXSDK](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Játék létrehozása és a Python engedélyezése

* Fordítsa le a játékát, és győződjön meg arról, hogy megfelelően épít. Ellenkező esetben a továbblépés előtt gondosan tekintse át az előző lépéseket. 
* Nyissa meg a projektet az Unreal Editorban. 
* **Motor beépülő modul:** Ha a ProjectAcoustics as Engine beépülő modult használja, győződjön meg arról is, hogy engedélyezve van a "beépített" beépülő modulok listájában.
* Meg kell jelennie egy új üzemmódnak, amely azt jelzi, hogy a Project Acoustics integrálva van.

    ![Képernyőkép a teljes akusztikus üzemmódról](media/acoustics-mode-full.png)

* Győződjön meg arról, hogy az Unreal-hez készült Python beépülő modul engedélyezve van. Ez a szerkesztő integrációjának megfelelő működéséhez szükséges.

    ![Képernyőkép a Python-bővítmények engedélyezéséről az Unreal Editorban](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Wwise projekt beállítása

Egy példa Wwise projekt szerepel a minták letöltésében. Javasoljuk, hogy tekintse át ezeket az utasításokat. Az alábbi Képernyőképek a projektből származnak.

### <a name="bus-setup"></a>Busz beállítása
* A Project akusztikai Unreal beépülő modulja a társított keverő beépülő modult fogja keresni egy buszon a ***pontos*** névvel: `Project Acoustics Bus`. Hozzon létre egy új hangbuszt ezzel a névvel. A keverő beépülő modul különböző konfigurációkban működhet, de most feltételezzük, hogy csak a reverb-feldolgozást fogja használni. Ez a busz a vegyes reverb-jeleket fogja használni az összes olyan forráshoz, amely akusztikai tevékenységet használ. Bármilyen buszos keverő struktúrában összekeverhető, például alább látható, amely a minta letöltésében szereplő Wwise-minta projektből származik.

    ![Képernyőkép a Wwise-buszokról, amelyek a Project akusztikai buszt mutatják](media/acoustics-bus.png)

* A buszon a csatorna konfigurációját az alábbiak egyikére kell beállítani: `1.0, 2.0, 4.0, 5.1 or 7.1`. Más konfigurációk nem eredményeznek kimenetet a buszon.

    ![Képernyőfelvétel a Channel konfigurációs beállításairól a Project akusztikai buszhoz](media/acoustics-bus-channel-config.png)

* Most nyissa meg a projekt akusztikai buszjának adatait, és ellenőrizze, hogy látható-e a keverő beépülő modul lapja

    ![Képernyőkép a Wwise, amely bemutatja, hogyan engedélyezheti a Project akusztikai buszának keverő beépülő modul lapját](media/mixer-tab-enable.png)

* Ezután lépjen a keverő beépülő modul lapra, és adja hozzá a Project akusztikai keverő beépülő modult a buszhoz

    ![A Wwise-busz screenshow, amely bemutatja, hogyan adhat hozzá a Project akusztikai keverő beépülő modult](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Actor-mixer hierarchia beállítása
* A Project Acoustics a következő teljesítményű eszközökön alkalmazza a hangalapú DSP-ket minden forrásra egyszerre. Ehhez a beépülő modulnak keverő beépülő modulként kell működnie. A Wwise a kimeneti buszon kell lennie, bár a kimeneti busz általában a száraz kimeneti jeleket végzi. A Project akusztikai funkció megköveteli, hogy a száraz jeleket az AUX-buszokon keresztül irányítsa a rendszer, `Project Acoustics Bus`miközben a nedves jelet hajtja végre. A következő folyamat támogatja a fokozatos áttelepítést ebbe a jelerősségbe.

* Tegyük fel, hogy rendelkezik egy meglévő projekttel, amely a legfelső szinten található lépteket, fegyvereket és másokat tartalmazó Actor-keverő hierarchia. Mindegyikhez megfelelő kimeneti busz tartozik a száraz kombinációhoz. Tegyük fel, hogy az akusztikai használathoz szeretné áttelepíteni a Nyomdokaikat. Először hozzon létre egy megfelelő Aux-buszt, amely a kimeneti busz kimenetének gyermeke, a száraz submix hajtja végre. Például az alábbi képen egy "száraz" előtagot használtunk a rendszerezéshez, bár a pontos név nem fontos. A lépteit-buszon már megjelenő mérőszámok vagy hatások továbbra is ugyanúgy működnek.

    ![Az ajánlott Wwise Dry mix-telepítő képernyőképe](media/wwise-dry-mix-setup.png)

* Ezután módosítsa a következő lépésként a Bus kimeneti struktúráját a nyomdokain lévő színész-keverőhöz, a Project akusztikai busz beállítása kimeneti Buszként, a Dry_Footsteps pedig felhasználó által definiált Aux-buszként van beállítva.

    ![A Wwise Actors-busz telepítésének képernyőképe](media/actor-mixer-bus-settings.png)

* Most már minden lépés akusztikai kezelést és kimenetet eredményez a projekt akusztikai Buszján. A száraz jelet a rendszer a Dry_Footsteps-on keresztül irányítja át, a szokásos módon.

* A Project akusztika csak olyan hangok esetében érvényes, amelyeken 3D-s hely van a világon. A [Wwise dokumentációjában](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/)a helymeghatározási tulajdonságokat az ábrán látható módon kell beállítani. A "3D spatialization" beállítás igény szerint "position" vagy "position + orientáció" lehet.

    ![A Wwise-szereplők ajánlott pozicionálási beállításainak képernyőképe](media/wwise-positioning.png)

* A kimeneti busz más buszra állítása, amely összekeveri a **Project akusztikai buszt** , nem fog működni. A Wwise ezt a követelményt a keverő beépülő moduloknál írja elő.

* Ha azt szeretné, hogy az Actors-mixer hierarchia ne használjon akusztikai célokat, a "szülő felülbírálása" lehetőséggel bármikor kiválaszthatja.

* Ha játék-vagy felhasználó által definiált küldést használ az reverb számára a játékban lévő összes keverőhöz, kapcsolja ki a hangvételt a hangvételi művelethez, hogy ne alkalmazza a reverb használatát kétszer.

### <a name="spatialization"></a>Spatialization
Alapértelmezés szerint a Project akusztikai Wwise keverő beépülő modulja a következőt alkalmazza, így a Wwise a pásztázó spatialization. Ha a Project akusztika ebben az alapértelmezett reverb-konfigurációban van használatban, szabadon használhatja a csatorna konfigurációját és a spatialization metódust a száraz kombinációban, így szinte bármilyen spatializer keverheti és egyeztetheti a Project Acoustics "reverb" használatával. A lehetőségek közé tartozik a [Ambisonics-alapú binaurális spatializers](https://www.audiokinetic.com/products/ambisonics-in-wwise/) és a [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
A Project akusztika egy opcionális spatializer tartalmaz, amely támogatja az objektum-alapú nagy felbontású HRTF-renderelést és a pásztázást is. Jelölje be a "spatialization végrehajtása" jelölőnégyzetet a keverő beépülő modul beállításainál, és válassza a HRTF vagy a pásztázás lehetőséget, és tiltsa le a fent megadott, felhasználó által definiált Aux-küldést az összes száraz buszok számára, hogy elkerülje a spatializing kétszer, mind a Project akusztikai keverő beépülő modullal, mind a Wwise. A spatialization mód nem módosítható valós időben, mert egy hangbank újragenerálása szükséges. Újra kell indítania az Unreal-t, majd újra létre kell hoznia a soundbanks, mielőtt a Play (lejátszás) gombra kattint a keverő beépülő modul konfigurációs módosításaiban, például az "spatialization végrehajtása" jelölőnégyzet

![Képernyőfelvétel a Wwise mixer beépülő modul spatialization beállításairól](media/mixer-spatial-settings.png)

Sajnos a más Object-alapú spatializer beépülő modulok jelenleg nem támogatottak, mert a keverő beépülő modulként vannak implementálva, és a Wwise jelenleg nem engedélyezi több keverő beépülő modul hozzárendelését egyetlen szereplőhöz.  

## <a name="7-audio-setup-in-unreal"></a>7. Hang beállítása az Unreal-ben
* Először is meg kell sütni a játék szintjét egy akusztikai eszköz létrehozásához, amely a következő helyen található: `Content\Acoustics`. Tekintse meg az [Unreal sütni oktatóanyagot](unreal-baking.md) , és folytassa itt. A mintavételi csomagban néhány előre elsütött szint is szerepel.
* Hozzon létre egy akusztikai helyet a saját jelenetében. Csak egy olyan szinten hozzon létre egy ilyen résztvevőt, amely a teljes szint akusztikai értékeit képviseli. 

    ![Képernyőkép az Unreal editorról, amely az akusztikai terület színészének létrehozását mutatja be](media/create-acoustics-space.png)

* Most rendelje hozzá a sült akusztikai adategységet az akusztikai adattárolóhoz az akusztikai terület színészén. A jelenet most már akusztikai!

    ![Képernyőkép az Unreal Editor s howing akusztikai eszköz-hozzárendeléséről](media/acoustics-asset-assign.png)

* Most adjon hozzá egy üres színészet, és tegye a következőket:

    ![Képernyőkép az Unreal editorról, amely az akusztikus összetevők használatát mutatja be egy üres színészben](media/acoustics-component-usage.png)

1. Akusztikai hangösszetevő hozzáadása a szereplőhöz. Ez az összetevő kiterjeszti a Wwise audio összetevőjét a Project Acoustics funkcióival.
2. A play on Start Box alapértelmezés szerint be van jelölve, amely a társított Wwise eseményt aktiválja a szint indításakor.
3. Használja az akusztikai paraméterek megjelenítése jelölőnégyzetet a képernyőn a forrással kapcsolatos hibakeresési információk kinyomtatásához.
    ![Képernyőkép az Unreal Editor akusztikai paneljéről a hangforráson, amelyen engedélyezve vannak a hibakeresési értékek](media/debug-values.png)
4. Wwise-esemény kiosztása a szokásos Wwise-munkafolyamatban
5. Győződjön meg arról, hogy a térbeli hang használata ki van kapcsolva. Ebben az esetben, ha a Project Acoustics szolgáltatást egy adott hangösszetevőhöz használja, akkor nem használhatja egyszerre a Wwise térbeli hangmotorját akusztikai eszközökre.

Készen is van. Lépjen a helyszínre, és fedezze fel az akusztikai hatásokat!

## <a name="next-steps"></a>További lépések
* [Tervezési](unreal-workflow.md) oktatóanyag a Project Acoustics szolgáltatáshoz az Unreal/Wwise
* [Ismerje meg, hogyan végezheti](unreal-baking.md) el a játékbeli jelenetek kisütését 
