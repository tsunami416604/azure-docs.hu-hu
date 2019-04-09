---
title: Projekt Akusztika Unreal és Wwise integrációja
titlesuffix: Azure Cognitive Services
description: Ebben az útmutatóban a projektbe a projekt Akusztika Unreal és Wwise beépülő modulok integrációs ismerteti.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: c6baa9f8330338c1e5fdc9ee0b5a8cc8b344e871
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006487"
---
# <a name="project-acoustics-unreal-and-wwise-integration"></a>Projekt Akusztika Unreal és Wwise integrációja
Ez az útmutató részletes integráció lépései Project Akusztika beépülő modul csomag a projektbe meglévő Unreal és Wwise játék biztosít. 

Szoftverkövetelmények:
* [Unreal Engine](https://www.unrealengine.com/) 4.20 vagy 4.21.
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.\*
* [Unreal Wwise beépülő modulja](https://www.audiokinetic.com/library/?source=UE4&id=index.html)
  * Egy Wwise SDK való közvetlen integráció a Wwise Unreal beépülő modulok használata helyett használja, tekintse meg a projekt Akusztika Unreal beépülő modul, és a Wwise API-hívások módosíthatja.

Ha szeretné, hogy egy hang motor eltérő Wwise projekt Akusztika használata, lépjen kapcsolatba velünk az a [projekt Akusztika fórumok](https://social.msdn.microsoft.com/Forums/en-US/home?forum=projectacoustics). A projekt Akusztika Unreal beépülő modul segítségével Akusztika adatokat kérdezhet le, és végezze el a motor API-hívások.

## <a name="download-project-acoustics"></a>Töltse le a projekt Akusztika
Ha még nem tette, töltse le a [projekt Akusztika Unreal & Wwise beépülő modul csomag](https://www.microsoft.com/download/details.aspx?id=58090)). 

Bővítettük az Unreal Engine beépülő modul és a egy Wwise mixer beépülő modult a csomagban. Az Unreal beépülő modul szerkesztő és a futtatókörnyezet integrációt biztosít. Során hiba lép a projekt Akusztika Unreal beépülő modul minden egyes keret kiszámítja paraméterek például hangelnyelés minden játék objektum esetén. Ezek a paraméterek Wwise API-hívások értékekké lesznek átalakítva.

## <a name="review-integration-steps"></a>Tekintse át az integráció lépései

Ezek a csomag telepítéséhez, és üzembe helyezni a játék fő lépésből áll.
1. A projekt Akusztika Wwise mixer beépülő modul telepítése
2. (Újra) üzembe helyezése a játék Wwise. Ebben a lépésben a mixer beépülő modul a játék projektbe propagálása zajlik.
3. A projekt Akusztika Unreal beépülő modul hozzáadása a játék
4. A Wwise Unreal beépülő modul funkciójának bővítése
5. Készítsen játékot, és ellenőrizze a Python engedélyezve van
6. Projekt Akusztika használandó-Wwise projekt beállítása
7. Hang beállítása az Unreal

## <a name="1-install-the-project-acoustics-mixer-plugin"></a>1. A projekt Akusztika mixer beépülő modul telepítése
* Nyissa meg Wwise indítója, majd a **beépülő modulok** lap **új beépülő modulok telepítése**, jelölje be **könyvtár hozzáadása a**. 

    ![Képernyőkép a beépülő modul telepítése Wwise indítója](media/wwise-install-new-plugin.png)

* Válassza ki a `AcousticsWwisePlugin\ProjectAcoustics` könyvtárban található a letöltött csomagot. A Wwise mixer beépülő csomag tartalmazza.

* Wwise telepíti a beépülő modult. Projekt Akusztika most meg kell jelennie Wwise telepített beépülő modulok listájában.
![Képernyőkép a Wwise telepített beépülő modulok listában projekt Akusztika telepítése után](media/unreal-integration-post-mixer-plugin-install.png)

## <a name="2-redeploy-wwise-into-your-game"></a>2. (Újra) üzembe helyezése a Wwise játékát
Wwise újratelepítés játékát, még akkor is, ha Ön már integrált Wwise. Ezzel felveszi a projekt Akusztika Wwise beépülő modult.

* **Motor beépülő modult:** Ha Wwise Unreal C++-projektben a játék beépülő modul telepítve van, hagyja ki ezt a lépést. Ha telepítve van as-motor beépülő modult, helyette a példány mert Unreal projektjéhez tervezet csak, Wwise telepítési a mixer beépülő modul használatával összetettebb. Hozzon létre egy helyőrző, üres Unreal C++-projektet, és zárja be, ha megnyílik a szerkesztő Unreal hajtsa végre a fennmaradó Wwise helyezze üzembe a helyőrző projekt. Ezután másolja ki a központilag telepített Wwise beépülő modult.
 
* Wwise indítója, kattintson a **Unreal Engine** lapfülre, majd kattintson a Tovább gombra a hamburger menü **legutóbbi Unreal Engine projektek** válassza **keresse meg a projekt**. Nyissa meg a játék Unreal projektet `.uproject` fájlt.

    ![Képernyőkép a Wwise indítója a Unreal lap](media/wwise-unreal-tab.png)

* Kattintson a **integrálása Wwise projektben** vagy **-projektben módosítsa Wwise**. Ezt a lépést (újra) integrálható Wwise bináris fájlokat a projektbe, most többek között a projekt Akusztika mixer beépülő modult.

* **Motor beépülő modult:** Ha Wwise as-motor beépülő modult használja, és létrehozta a helyőrző projektet a fenti, másolja a következő mappát: telepített Wwise: `[DummyUProject]\Plugins\Wwise` , és illessze be keresztül `[UESource]\Engine\Plugins\Wwise`. `[DummyUProject]` üres Unreal C++ projekt elérési útját, és `[UESource]` van, ahol az Unreal Engine források telepítve van. Miután elkészült másolja, törölheti az üres projekt.

## <a name="3-add-the-project-acoustics-unreal-plugin-to-your-game"></a>3. A projekt Akusztika Unreal beépülő modul hozzáadása a játék
 
* Másolás a `Unreal\ProjectAcoustics` a beépülő modul mappájához csomagot, és hozzon létre egy új mappát `[UProjectDir]\Plugins\ProjectAcoustics`, ahol `UProjectDir` a játék projektmappa tartalmazó van a `.uproject` fájlt.
  * **Motor beépülő modul**: Wwise as-motor beépülő modul használata, az Unreal engine beépülő modult is projekt Akusztika célszerű használni. A fenti célkönyvtárban duplikátum helyett használja: `[UESource]\Engine\Plugins\ProjectAcoustics`.

* Győződjön meg arról hogy egy `Wwise` mappa mellett a `ProjectAcoustics` mappát. A Wwise beépülő modul és a mixer beépülő modult (re-) központilag a fenti 2. lépésben bináris fájljait tartalmazza.

## <a name="4-extend-wwises-unreal-plugin-functionality"></a>4. A Wwise Unreal beépülő modul funkciójának bővítése
* A projekt Akusztika Unreal beépülő modult a szükséges további működés a Wwise Unreal / beépülő modul API a kitett [ezeket az irányelveket](https://www.audiokinetic.com/library/?source=UE4&id=using__initialsetup.html). Automatizálhatja a javítási eljárás fájlt egy kötegfájlban bővítettük. 
* Belül `Plugins\ProjectAcoustics\Resources`futtassa `PatchWwise.bat`. A példában az alábbi képen a AcousticsGame mintaprojektet használ.

    ![Képernyőfelvétel a Windows Explorer ablak kiemelése megadott javítás Wwise parancsfájl segítségével](media/patch-wwise-script.png)

* Ha nem rendelkezik a DirectX SDK telepítve van, tegye megjegyzésbe a DXSDK_DIR tartalmazó sort kell `[UProject]\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`

    ![Kódszerkesztő megjegyzésként DXSDK bemutató képernyőkép](media/directx-sdk-comment.png)

## <a name="5-build-game-and-check-python-is-enabled"></a>5. Készítsen játékot, és ellenőrizze a Python engedélyezve van

* Fordítsa le a játék, és győződjön meg arról, hogy megfelelően létrehozza-e. Ellenkező esetben ellenőrizze a folytatás előtt figyelmesen az előző lépésekben. 
* Nyissa meg a projekt az Unreal-szerkesztőt. 
* **Motor beépülő modult:** Ha ProjectAcoustics motor beépülő modult használ, is győződjön meg arról, hogy engedélyezve van, "beépített" beépülő modulok listájában.
* Megjelenik egy új üzemmódot, amely azt jelzi, hogy a projekt Akusztika beépített összetevője.

    ![Képernyőkép az Unreal Akusztika mód teljes megjelenítése](media/acoustics-mode-full.png)

* Győződjön meg róla, az Unreal engedélyezve van a Python beépülő modul. Ez azért szükséges a szerkesztő integrációjának megfelelő működéséhez.

    ![Képernyőkép a Python-bővítmények Unreal szerkesztőben engedélyezése](media/ensure-python.png)

## <a name="6-wwise-project-setup"></a>6. Wwise projekt beállítása

Egy példaprojekt Wwise a minták letöltés részét képezi. Azt javasoljuk, hogy megnézi együtt ezeket az utasításokat. Az alábbi képernyőképek a projekt megnyílik.

### <a name="bus-setup"></a>Busz beállítása
* A projekt Akusztika Unreal beépülő modul megkeresi a buszon ezt a társított mixer beépülő modul ***pontos*** name: `Project Acoustics Bus`. Hozzon létre egy új hang bus ezt a nevet. A mixer beépülő modul különböző konfigurációkat is működik, de egyelőre feltételezzük, hogy csak visszhang feldolgozási használandó. A busz képviselik, a vegyes visszhang jel Akusztika használó összes adatforrás esetében. Azt kombinálhatja felső be bármely bus keverése struktúra, példa az alábbi származik a Wwise mintaprojektet a minta letöltés részét képezi.

    ![Projekt Akusztika Bus megjelenítő Képernyőkép az Wwise buszok](media/acoustics-bus.png)

* A csatorna konfigurálása a buszon kell állítani az egyik: `1.0, 2.0, 4.0, 5.1 or 7.1`. Egyéb konfigurációk nincs kimenet a buszon eredményez.

    ![Képernyőfelvétel a projekt Akusztika Bus csatorna konfigurációs lehetőségek](media/acoustics-bus-channel-config.png)

* Lépjen be a projekt Akusztika busz-részleteit, és győződjön meg arról, láthatja a Mixer beépülő modul lapon

    ![Képernyőkép a Wwise: hogyan kell a projekt Akusztika Bus a Mixer beépülő modul lap engedélyezése](media/mixer-tab-enable.png)

* Ezután nyissa meg a Mixer beépülő modul lapot, és a projekt Akusztika mixer-beépülő modul hozzáadása a busz

    ![A projekt Akusztika Mixer beépülő modul hozzáadása megjelenítő Screenshow, Wwise bus](media/add-mixer-plugin.png)

### <a name="actor-mixer-hierarchy-setup"></a>Actor-mixer hierarchia beállításai
* Teljesítménybeli megfontolások miatt az projekt Akusztika érvényes hang DSP összes adatforrás egy időben. Ehhez a beépülő modul, mint a mixer beépülő modul működéséhez. Wwise igényel a mixer beépülő modulok, a kimeneti buszon kell, ha a kimeneti bus általában végzi a száraz kimenő jel. Projekt Akusztika igényel a száraz jel lehet áthalad aux buszok, amíg folyik a nedves jel a `Project Acoustics Bus`. A következő folyamat támogatja a fokozatos áttelepítés jel ehhez a folyamathoz.

* Egy meglévő projekt tegyük az aktor-mixer hierarchiát tartalmazó Nyomdokába, fegyverek és egyéb, a legfelső szintű vannak. Mindegyik rendelkezik a megfelelő kimeneti buszhoz annak száraz vegyesen. Tegyük fel, Akusztika használandó Nyomdokába áttelepíteni kívánt segítségével. Először hozzon létre egy megfelelő aux bus számára, saját száraz submix, amely a Nyomdokába kimeneti bus gyermeke. Például használtunk "Száraz" előtagot az alábbi képen rendszerezéséhez ezeket, bár a pontos nevét nem fontos. Bármely fogyasztásmérőit, vagy a Nyomdokába buszon kellett hatások továbbra is működik mint korábban.

    ![Ajánlott Wwise száraz Mix-telepítő képernyőképe](media/wwise-dry-mix-setup.png)

* Majd módosítsa a busz kimeneti struktúra a Nyomdokába aktor Mixer a következőképpen, a projekt Akusztika busszal kimeneti busz és Dry_Footsteps beállításának állítja be a felhasználó által definiált aux busz.

    ![Ajánlott Wwise Aktor Mixer Bus telepítő képernyőképe](media/actor-mixer-bus-settings.png)

* Most már minden nyomdokába Akusztika kezelés lekérése, és azok a projekt Akusztika buszon visszhang kimeneti. A száraz jel Dry_Footsteps keresztül irányítja át, és a szokásos módon spatialized.

* Projekt Akusztika csak vonatkozik, amelyek egy 3D helyre a világ hangok. A következő [Wwise dokumentáció](https://blog.audiokinetic.com/out-with-the-old-in-with-the-new-positioning-revamped-in-wwise-2018.1/), a Tulajdonságok elhelyezéséhez állítson be látható módon. A "3D Spatialization" beállítás lehet "Pozíciója" vagy "Pozíció + tájolás" igény szerint.

    ![Képernyőkép a ajánlott Wwise Aktor elhelyezés beállításai](media/wwise-positioning.png)

* A kimeneti Bus beállítása néhány egyéb buszhoz csatlakozik, amely be felső eredményét **projekt Akusztika Bus** nem fog működni. Wwise ezt a követelményt a mixer beépülő modulok ír elő.

* Ha azt szeretné, hogy a gyermek nem használandó Akusztika Nyomdokába actor-mixer hierarchiában, mindig segítségével "szülő felülbírálások", kikapcsolhatja azt.

* Az aktor a játék mixer bármely visszhang használni kívánt játék -, vagy felhasználó által definiált küld, ha kapcsolja azokat az aktor-mixer visszhang kétszer alkalmazása elkerülése érdekében.

### <a name="spatialization"></a>Spatialization
Alapértelmezés szerint a projekt Akusztika Wwise mixer beépülő modul érvényes konvolúció visszhang, elhagyása Wwise Pásztázó spatialization tennie. Ha ezt az alapértelmezett csak visszhang konfigurálást projekt Akusztika használ, Ön ingyenesen használható minden olyan csatorna konfigurációja és spatialization metódus a saját száraz műveletei lehetővé teszi, hogy keverheti a projekt Akusztika visszhang szinte bármilyen spatializer. A lehetőségek a következők [binaural spatializers Ambisonics-alapú](https://www.audiokinetic.com/products/ambisonics-in-wwise/) és [Windows Sonic](https://docs.microsoft.com/windows/desktop/CoreAudio/spatial-sound).
 
Projekt Akusztika tartalmaz egy választható spatializer, amely támogatja a nagy felbontású HRTF renderelési objektum alapú, és e pásztázáshoz. Jelölje be a "Végrehajtása Spatialization" jelölőnégyzetet a mixer beépülő modul beállításai, és választhat HRTF vagy Pásztázó, és tiltsa le a felhasználó által definiált külső küld beállítása fent a száraz buszok spatializing kétszer, mind a projekt Akusztika mixer beépülő modul és Wwise elkerülése érdekében. A spatialization mód nem módosítható valós időben, mert egy hang bank újragenerálása van szükség. Indítsa újra az Unreal, majd soundbanks újragenerálása előtt szerezze meg a mixer beépülő modul konfigurációs módosítások végrehajtása Spatialization jelölőnégyzet például a Play áruházból.

![A Wwise Mixer beépülő modul Spatialization képernyőkép beállításai](media/mixer-spatial-settings.png)

Sajnos azonban más spatializer objektum-alapú beépülő modulok nem támogatott jelenleg, valamint a mixer beépülő modulok, végrehajtásuk Wwise jelenleg nem engedélyezi több mixer beépülő modulok egyetlen aktor mixer rendelt.  

## <a name="7-audio-setup-in-unreal"></a>7. Hang beállítása az Unreal
* Először kell előállításához egy Akusztika eszköz, amely kerülnek a játék szint os `Content\Acoustics`. Tekintse át a [Unreal os oktatóanyag](unreal-baking.md) , és itt folytatódik. Néhány előre létrehozott szint a minta-csomagban található.
* Hozzon létre egy Akusztika terület szereplő a jelenet. Csak hozzon létre egyet, ezek az aktorok egy szint, ez azt jelenti, hogy a teljes szint a Akusztika. 

    ![Akusztika terület aktor létrehozását bemutató képernyőkép Unreal szerkesztő](media/create-acoustics-space.png)

* Most rendelje hozzá a létrehozott akusztikai adategységhez a Akusztika adatok bővítőhely a Akusztika terület aktor a. A jelenethez Akusztika most már rendelkezik!

    ![Képernyőkép az Unreal szerkesztő s howing Akusztika eszköz-hozzárendelés](media/acoustics-asset-assign.png)

* Most adjon hozzá egy üres szereplő, és tegye a következőket:

    ![Egy üres szereplő Akusztika összetevő használati megjelenítő Képernyőkép az Unreal szerkesztő](media/acoustics-component-usage.png)

1. Egy Akusztika Hang összetevőt az aktor felé. Ez az összetevő terjeszti ki a Wwise hang összetevő funkciókkal projekt Akusztika.
2. A lejátszás kezdő Box aktiválják a szintű indítási vonatkozó Wwise esemény alapértelmezés szerint be van jelölve.
3. A Akusztika paraméterek megjelenítése jelölőnégyzet segítségével nyomtassa ki a képernyőn megjelenő hibakeresési információit a forrás.
    ![Képernyőkép az Unreal szerkesztő Akusztika panel a megbízható forrás hibakeresési értékekkel engedélyezve](media/debug-values.png)
4. Rendelje hozzá egy Wwise esemény a szokásos Wwise munkafolyamatonként
5. Győződjön meg arról, hogy a térbeli hang ki van kapcsolva. Jelenleg Ha projekt Akusztika használ az egy adott hang összetevő nem egyszerre használható Wwise a térbeli hang motor Akusztika.

Készen is van. Navigálás a jelenet, és Fedezze fel a akusztikai hatások!

## <a name="next-steps"></a>További lépések
* [Tervezési](unreal-workflow.md) projekt Akusztika az Unreal/Wwise oktatóanyag
* [Ismerje meg, hogyan teheti a kódfejlesztést](unreal-baking.md) a játék jelenetek számára 
