---
title: StorSimple Snapshot Manager felhasználói felület | Microsoft dokumentumok
description: Ez a témakör a StorSimple Snapshot Manager felhasználói felületét ismerteti, és ismerteti, hogyan használható a biztonsági mentési feladatok és a biztonsági mentési katalógus kezeléséhez.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.custom: ''
ms.openlocfilehash: 56771d2e62289485017f34c6a9ab26e1d53610ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933969"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>A StorSimple Snapshot Manager felhasználói felületének használata a biztonsági mentési feladatok és a biztonságimásolat-katalógus kezeléséhez

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager egy intuitív felhasználói felület, amely segítségével a biztonsági mentések. Ez az oktatóanyag bemutatja a felhasználói felületet, majd elmagyarázza, hogyan kell használni az egyes összetevőket. A StorSimple Snapshot Manager részletes leírását a [Mi a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>A konzol leírása
A felhasználói felület megtekintéséhez kattintson a StorSimple Snapshot Manager ikonra az asztalon. Megjelenik a konzolablak, ahogy az az alábbi ábrán látható.

![StorSimple Snapshot Manager ablaktáblák](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A konzolablak öt fő elemet tartalmaz. Kattintson a megfelelő linkre az egyes elemek teljes leírásához.

* [Menüsor](#menu-bar) 
* [Eszköztár](#tool-bar) 
* [Hatókör ablaktábla](#scope-pane) 
* [Eredmények ablaktábla](#results-pane) 
* [Műveletek ablaktábla](#actions-pane) 

Ezenkívül a StorSimple Snapshot Manager támogatja a [billentyűzetnavigációt és számos parancsikont.](#keyboard-navigation-and-shortcuts)

### <a name="console-accessibility"></a>A konzol kisegítő lehetőségei
A StorSimple Snapshot Manager felhasználói felület támogatja a Windows operációs rendszer és a Microsoft Management Console (MMC) által biztosított kisegítő lehetőségeket, valamint néhány StorSimple Snapshot Manager-specifikus billentyűparancsot. 

* A Windows kisegítő lehetőségeinek leírását a [Windows billentyűparancsai](https://support.microsoft.com/kb/126449)című témakörben található. 
* Az MMC kisegítő lehetőségeinek leírását az [MMC 3.0 kisegítő lehetőségei című](https://technet.microsoft.com/library/cc766075.aspx) témakörben található.
* A StorSimple Snapshot Manager kisegítő lehetőségeinek leírását a [Billentyűzetnavigáció és billentyűparancsok](#keyboard-navigation-and-shortcuts)című témakörben olvashatja.

## <a name="menu-bar"></a>Menüsor
A konzolablak tetején található menüsor [fájl,](#file-menu) [művelet](#action-menu), [nézet,](#view-menu) [kedvencek](#favorites-menu), [ablak](#window-menu)és [súgómenüt](#help-menu) tartalmaz.

Kattintson a menüsor bármelyik elemére a menüben elérhető parancsok listájának megtekintéséhez. A következő példa a menüsoron kijelölt **Nézet** menüt mutatja be.

![Kijelölt nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Fájl menü
A **Fájl** menü szabványos Microsoft Management Console (MMC) parancsokat tartalmaz.

#### <a name="menu-access"></a>Menühozzáférés
A **Fájl** menü megtekintéséhez kattintson a menüsor **Fájl parancsára.** Megjelenik a következő menü.

![StorSimple Snapshot Manager fájl menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat a **Fájl** menüben megjelenő elemeket ismerteti.

| Menüpont | Leírás |
|:--- |:--- |
| Új |Kattintson **az Új** gombra egy új konzol létrehozásához a StorSimple Snapshot Manager alapján. |
| Megoldatlan |Meglévő konzol megnyitásához kattintson a **Megnyitás** gombra. |
| Mentés |Az aktuális konzol mentéséhez kattintson a **Mentés** gombra. |
| Mentés másként |Kattintson a **Mentés másként** gombra az aktuális konzol új, átnevezett példányának létrehozásához. A **Mentés másként** beállítással testreszabhatja a nézetet, és mentheti későbbi visszakeresésre. Létrehozhat például storSimple Snapshot Manager beépülő modulokat, amelyek adott kiszolgálókra mutatnak. |
| Beépülő modul hozzáadása/eltávolítása |A Beépülő modulok hozzáadásához vagy eltávolításához, valamint a **Hatókör** ablaktábla csomópontjainak rendszerezéséhez kattintson a **Beépülő modul hozzáadása/eltávolítása** lehetőségre. További információt az [MMC 3.0 beépülő modulok és bővítmények hozzáadása, eltávolítása és rendezése](https://technet.microsoft.com/library/cc722035.aspx)című részben talál. |
| Beállítások |Kattintson a **Beállítások** gombra a konzol ikonjának módosításához, adja meg a felhasználói hozzáférési módokat és engedélyeket, vagy törölje a konzolfájlokat a rendelkezésre álló lemezterület növelése érdekében. |
| Fájlelérési utak listája |Kattintson egy elérési útra a számozott listában a nemrég megnyitott fájl újbóli megnyitásához. |
| Kilépés |A Fájl menü bezárásához kattintson a **Kilépés** **gombra.** |

### <a name="action-menu"></a>Művelet menü
A **Művelet** menüben választhat az elérhető műveletek közül. A rendelkezésre álló elemek a **Hatókör** vagy az **Eredmények** ablaktáblán megadott kijelöléstől függenek.

#### <a name="menu-access"></a>Menühozzáférés
A **Művelet** menü megtekintéséhez tegye az alábbiak egyikét:

* Kattintson a jobb gombbal egy elemre a **Hatókör** ablaktáblán vagy az **Eredmények** ablaktáblán.
* Jelöljön ki egy elemet a **Hatókör** vagy az **Eredmények** ablaktáblán, majd kattintson a menüsor **Művelet parancsára.** 

Ha például a Felső csomópontot jelöli a **Hatókör** ablaktáblában, majd a menüsorban a jobb gombbal vagy a **Művelet** parancsra kattint, a következő menü jelenik meg.

![StorSimple Snapshot Manager művelet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

A **Műveletek** ablaktábla (a konzol jobb oldalán) ugyanazokat a műveleteket tartalmazza, mint a **Művelet** menü. Ezenkívül a **Műveletek** ablaktábla tartalmazza a **Nézet** menübeállításokat, amelyek lehetővé teszik az **Eredmények** ablaktábla egyéni nézetének létrehozását.

![Műveletek ablaktábla megnyitott Nézet menüvel](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat a StorSimple Snapshot Manager-műveletek betűrendes listáját tartalmazza. 

* A **Művelet** oszlop felsorolja a csomópontokon és eredményeken végrehajtható műveleteket. 
* A **Navigáció** oszlop bemutatja, hogyan jeleníthető meg a megfelelő **Művelet** menü, hogy ki tudja választani a műveletet. Egyes műveletek több **Művelet** menüben jelennek meg. Ezekhez a műveletekhez válasszon ki egy **navigációs** lehetőséget a listajeles listából. 
* A **Leírás** oszlop ismerteti, hogyan használhatók a **Művelet** menü vagy a Műveletek ablaktábla egyes műveletei, és ismerteti, hogy mit csinál.

> [!NOTE]
> A **Műveletek** ablaktábla és **a Művelet** menü további beállításokat tartalmaz, például a **Nézet,** **az Új ablak innen,** **a Frissítés,** **a Lista exportálása**és **a Súgó**. Ezek a beállítások az MMC részeként érhetők el, és nem a StorSimple Snapshot Manager re vonatkozóan érhetők el. A táblázat tartalmazza ezeknek a beállításoknak a leírását.
> 
> 

| Műveletek | Navigáció | Leírás |
|:--- |:--- |:--- |
| Hitelesítés |Kattintson az **Eszközök** csomópontra, és kattintson a jobb gombbal egy eszközre az **Eredmények** ablaktáblán. |Kattintson a **Hitelesítés gombra** az eszközhöz beállított jelszó megadásához. |
| Klónozás |Bontsa ki **a Biztonsági másolat katalógusát,** bontsa ki a **Felhőbeli pillanatképek csomópontot,** kattintson egy dátummal ellátott biztonsági másolatra, majd jelöljön ki egy kötetet az **Eredmények** ablaktáblán. |Kattintson **a Klónozás** gombra a felhőbeli pillanatkép másolatának létrehozásához és a kijelölt helyen való tárolásához. |
| Eszköz konfigurálása |Kattintson a jobb gombbal az **Eszközök** csomópontra. |Kattintson **az Eszköz konfigurálása** elemre egy eszköz vagy több eszköz windowsos állomáshoz való csatlakozásához való konfigurálásához. |
| Biztonsági másolat házirendjének létrehozása |Tegye a következők egyikét:<ul><li>Kattintson a jobb gombbal **a Biztonsági másolat házirendjei elemre.**</li><li>Kattintson vagy bontsa ki **a Kötetcsoportok csomópontot,** majd kattintson a jobb gombbal egy kötetcsoportra.</li><li>Kattintson vagy bontsa ki **a Katalógus biztonsági másolatkészítését,** majd kattintson a jobb gombbal egy kötetcsoportra.</li></ul> |Kattintson **a Biztonsági másolat készítése házirendre** egy kötetcsoport ütemezett biztonsági mentésének konfigurálásához. |
| Kötetcsoport létrehozása |Tegye a következők egyikét:<ul><li>Kattintson a **Kötetek** csomópontra, majd kattintson a jobb gombbal egy kötetre az **Eredmények** ablaktáblán.</li><li>Kattintson a jobb gombbal a **Kötetcsoportok** csomópontra.</li></ul> |A **Kötetcsoport létrehozása gombra** kattintva köteteket rendelhet egy kötetcsoporthoz. |
| Törlés |Kattintson egy csomópontra vagy eredményre (Ez az elem számos **Művelet** menüben és **Művelet** ablaktáblán jelenik meg.) |A **Törlés gombra** kattintva törölheti a kijelölt csomópontot vagy eredményt. Amikor megjelenik a megerősítést kérő párbeszédpanel, erősítse meg vagy szakítsa meg a törlést. |
| Részletek |Kattintson az **Eszközök** csomópontra, majd kattintson a jobb gombbal egy eszközre az **Eredmények** ablaktáblán. |Kattintson a **Részletek** gombra az eszköz konfigurációs részleteinek megtekintéséhez. |
| Szerkesztés |Kattintson a **Biztonsági másolat házirendek parancsára,** majd kattintson a jobb gombbal egy házirendre az **Eredmények** ablaktáblán. |Kattintson a **Szerkesztés** gombra a kötetcsoport biztonsági mentési ütemezésének módosításához. |
| Lista exportálása |Kattintson bármelyik csomópontra vagy eredményre (Ez az elem az összes **Művelet** menüben és **Művelet** ablaktáblán megjelenik.) |Kattintson a **Lista exportálása** gombra, ha egy listát vesszővel tagolt értékfájlba (CSV) szeretne menteni. Ezután ezt a fájlt importálhatja egy táblázatkezelő alkalmazásba elemzésre. |
| Súgó |Kattintson bármelyik csomópontra vagy eredményre. (Ez az elem az összes **Művelet** menüben és **Művelet** ablaktáblán megjelenik.) |Kattintson a **Súgó** gombra, ha az online súgót külön böngészőablakban szeretné megnyitni. |
| Új ablak innen |Kattintson bármelyik csomópontra vagy eredményre (Ez az elem az összes **Művelet** menüben és **Művelet** ablaktáblán megjelenik.) |Kattintson **az Új ablak innen** gombra egy új StorSimple Snapshot Manager ablak megnyitásához. |
| Frissítés |Kattintson bármelyik csomópontra vagy eredményre (Ez az elem az összes **Művelet** menüben és **Művelet** ablaktáblán megjelenik.) |Kattintson a **Frissítés** gombra az aktuálisan megjelenített StorSimple Snapshot Manager ablak frissítéséhez. |
| Eszköz frissítése |Kattintson az **Eszközök** csomópontra, és kattintson a jobb gombbal egy eszközre az **Eredmények** ablaktáblán. |Kattintson **az Eszköz frissítése** gombra egy adott csatlakoztatott eszköz szinkronizálásához a StorSimple Snapshot Manager rel. |
| Eszközök frissítése |Kattintson a jobb gombbal az **Eszközök** csomópontra. |Kattintson **az Eszközök frissítése** gombra a csatlakoztatott eszközök listájának szinkronizálásához a StorSimple Snapshot Manager segítségével. |
| Kötetek újrabeszekveretése |Kattintson a jobb gombbal a **Kötetek** csomópontra. |Kattintson a **Kötetek újravizsgálata** elemre az **Eredmények** ablaktáblán megjelenő kötetek listájának frissítéséhez. |
| Visszaállítás |Bontsa ki **a Biztonsági másolat katalógusát,** bontsa ki a kötetcsoportot, **bontsa** ki a Helyi pillanatképek vagy **a Felhőbeli pillanatképek csomópontot,** majd kattintson a jobb gombbal egy biztonsági másolatra. |Kattintson a **Visszaállítás gombra,** ha le szeretné cserélni az aktuális kötetcsoport adatait a kijelölt biztonsági másolatból származó adatokkal. |
| Biztonsági mentés készítése |Tegye a következők egyikét:<ul><li>Bontsa ki **a Kötetcsoportok**csomópontot, majd kattintson a jobb gombbal egy kötetcsoportra.</li><li>Bontsa ki **a Biztonsági másolat katalógusát**, majd kattintson a jobb gombbal egy kötetcsoportra.</li></ul> |Kattintson **a Biztonsági mentés folytatása** gombra a biztonsági mentési feladat azonnali elindításához. |
| Import megjelenítése |Kattintson a jobb gombbal a felső csomópontra a **Hatókör** ablaktáblában (a példákban a **StorSimple Snapshot Manager** csomópont). |Kattintson **az Importálás megjelenítése váltása** gombra a StorSimple Device Manager szolgáltatás irányítópultjáról importált kötetcsoportok és kapcsolódó biztonsági mentések megjelenítéséhez vagy elrejtéséhez. |

### <a name="view-menu"></a>Nézet menü
A **Nézet** menüben egyéni nézetet hozhat létre az **Eredmények** ablaktábla tartalmáról. A **Nézet** menü **tartalmazza az Oszlopok hozzáadása/eltávolítása és** a **Testreszabás** beállításokat.

#### <a name="menu-access"></a>Menühozzáférés
A **menüsor nézet** menüjét vagy a **Műveletek** ablaktáblát érheti el.

![StorSimple Snapshot Manager nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat a **Nézet** menüben megjelenő elemeket ismerteti.

| Menüpont | Leírás |
|:--- |:--- |
| Oszlopok hozzáadása/eltávolítása |Kattintson **az Oszlopok hozzáadása/eltávolítása** gombra az Oszlopok hozzáadásához vagy eltávolításához az **Eredmények** ablaktáblán. |
| Testreszabás |Kattintson a **Testreszabás gombra** az elemek megjelenítéséhez vagy elrejtéséhez a StorSimple Snapshot Manager konzolablakában. |

### <a name="favorites-menu"></a>Kedvencek menü
A **Kedvencek** menüben a gyakran használt oldalnézeteket és feladatokat veheti fel, távolíthatja el és rendezheti. 

#### <a name="menu-access"></a>Menühozzáférés
A menüsor **Kedvencek** menüjét is elérheti.

![StorSimple Snapshot Manager Kedvencek menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat a Kedvencek menüben megjelenő **elemeket** ismerteti.

| Menüpont | Leírás |
|:--- |:--- |
| Hozzáadás a kedvencekhez |Kattintson **a Hozzáadás a kedvencekhez gombra,** ha hozzá szeretné adni az aktuális nézetet a kedvencek listájához. |
| Kedvencek rendszerezése |A Kedvencek mappa tartalmának rendszerezéséhez kattintson a **Kedvencek** rendszerezése gombra. |

### <a name="window-menu"></a>Ablak menü
Az **Ablak** menüsegítségével hozzáadhatja és átrendezheti a StorSimple Snapshot Manager konzolablakait.

#### <a name="menu-access"></a>Menühozzáférés
A menüsor **Ablak** menüjét elérheti.

![StorSimple Snapshot Manager ablak menü](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A menü alján található számozott lista az aktuálisan megnyitott ablakokat jeleníti meg. Kattintson a lista bármelyik ablakára, hogy az ablak az előtérbe kerüljön. 

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat az Ablak menüben megjelenő elemeket ismerteti.

| Menüpont | Leírás |
|:--- |:--- |
| Új ablak |Új konzolablak megnyitásához kattintson az **Új ablak** gombra (a meglévő ablakon kívül). |
| Cascade |A **Kaszkádolás** gombra kattintva a megnyitott konzolablakok kaszkádolt stílusban jelenjenek meg. |
| Mozaik vízszintesen |Kattintson **a Vízszintes mozaik gombra** a megnyitott konzolablakok mozaikformátumban (vagy rács) formátumban való megjelenítéséhez. |
| Ikonok elrendezése |Ha több konzolablak van megnyitva és szétszórva az asztalon, minimalizálja őket, majd kattintson az **Ikonok elrendezése** gombra, hogy vízszintes sorba rendezhesse őket a képernyő alján. |

### <a name="help-menu"></a>Súgó menü
A **Súgó** menüben megtekintheti a StorSimple Snapshot Manager és az MMC elérhető online súgóját. A rendszerre jelenleg telepített MMC és StorSimple Snapshot Manager szoftververziókkal kapcsolatos információkat is megtekintheti. 

A menüsor **Súgó** menüjét elérheti. A StorSimple Snapshot Manager súgótémaköreit a **Műveletek** ablaktáblából is elérheti.

![StorSimple Snapshot Manager súgómenü](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat a Súgó menüben megjelenő elemeket ismerteti.

| Menüpont | Leírás |
|:--- |:--- |
| Súgó a StorSimple Snapshot Manager |Kattintson **a Súgó a StorSimple Snapshot Manager** megnyitásához StorSimple Snapshot Manager súgó egy külön ablakban. |
| Súgó témakörei |Kattintson **a Súgótémakörök gombra** az MMC online súgójának külön ablakban való megnyitásához. |
| A TechCenter webhelye |Kattintson a **TechCenter webhely elemre** a Microsoft TechNet Tech Center kezdőlapjának külön ablakban való megnyitásához. |
| A Microsoft Management Console |Kattintson **a Microsoft Management Console–szóra** a Microsoft Management Console melyik verziójának a rendszerre való telepítéséhez. |
| A StorSimple Snapshot Manager-ről |Kattintson **a StorSimple Snapshot Manager–** re annak megtekintéséhez, hogy a beépülő modul melyik verziója van telepítve a rendszerre. |

## <a name="tool-bar"></a>Eszköztár
A menüsor alatt található eszköztár navigációs és feladatikonokat tartalmaz. Minden ikon egy adott feladatparancsikon.

### <a name="icon-descriptions"></a>Ikon leírása
Az alábbi táblázat az eszköztáron megjelenő ikonokat ismerteti. 

| Ikon | Leírás |
|:--- |:--- |
| ![Balra nyíl](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Az előző oldalra való visszatéréshez kattintson a bal nyílikonra. |
| ![Jobb nyílbillentyű](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kattintson a jobbra mutató nyílra a következő oldalra való ugráshoz (ha a nyíl szürke, a művelet nem érhető el). |
| ![Fel ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |A felgombra kattintva egy szinttel feljebb lépakonzolon (a **Hatókör** ablaktáblán). |
| ![Konzolfa megjelenítése/elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kattintson a konzolfa megjelenítése/elrejtése ikonra a **Hatókör** ablaktábla megjelenítéséhez vagy elrejtéséhez. |
| ![Lista exportálása](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Kattintson az exportálási lista ikonra, ha egy listát a megadott CSV-fájlba szeretne exportálni. |
| ![Súgó ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kattintson a súgó ikonra egy online MMC súgótémakör megnyitásához. |
| ![Műveletek ablaktábla megjelenítése/elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |A **Műveletek** ablaktábla megjelenítése/elrejtése ikonra kattintva megjelenhet vagy elrejthet a **Műveletek** ablaktáblát. |

## <a name="scope-pane"></a>Hatókör ablaktábla
A **Hatókör** ablaktábla a StorSimple Snapshot Manager felhasználói felületének bal szélső ablaktáblája. Ez tartalmazza a konzol (vagy csomópont) fa, és az elsődleges navigációs mechanizmus a StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Hatókör ablaktábla szerkezete
A **Hatókör** ablaktábla fastruktúrába rendezett, kattintható objektumok (csomópontok) sorozatát tartalmazza. 

![Hatókör ablaktábla](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Csomópont kibontásához vagy összecsukásához kattintson a csomópont neve melletti nyílikonra.
* Egy csomópont állapotának vagy tartalmának megtekintéséhez kattintson a csomópont nevére. Az információ megjelenik az **Eredmények** ablaktáblán. 

A **Hatókör** ablaktábla a következő csomópontokat tartalmazza: 

* [Eszközcsomópont](#devices-node) 
* [Kötetek csomópontja](#volumes-node) 
* [Kötetcsoportok csomópontja](#volume-groups-node) 
* [Biztonsági mentési házirendek csomópontja](#backup-policies-node) 
* [Biztonsági másolat katalóguscsomópontja](#backup-catalog-node) 
* [Feladat csomópont](#jobs-node) 

### <a name="scope-pane-tasks"></a>Hatókör ablaktábla-feladatok
A **Hatókör** ablaktábla segítségével végrehajthat egy műveletet egy adott csomóponton. Feladat kijelöléséhez tegye az alábbiak egyikét:

* Kattintson a jobb gombbal a csomópontra, majd válassza ki a feladatot a megjelenő menüből.
* Kattintson a csomópontra, majd a menüsor **Művelet parancsára.** Jelölje ki a feladatot a megjelenő menüből.
* Kattintson a csomópontra, majd jelölje ki a műveletet a **Műveletek** ablaktáblán.

Amikor kijelöl egy csomópontot, és a következő módszerek bármelyikével látja a feladatlistát, csak az adott csomóponton végrehajtható műveletek jelennek meg.

### <a name="devices-node"></a>Eszközcsomópont
Az **eszközök** csomópont a StorSimple-eszközök és a StorSimple virtuális eszközök, amelyek a StorSimple Snapshot Manager csatlakozik. Válassza ezt a csomópontot egy eszköz csatlakoztatásához és konfigurálásához, valamint a hozzá tartozó kötetek, kötetcsoportok és meglévő biztonsági másolatok importálásához. Egyetlen állomáshoz több eszköz is csatlakoztatható.

* A csomópont kibontásához kattintson az **Eszközök**gomb melletti nyílikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal az **Eszközök** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* A konfigurált eszközök listájának megtekintéséhez kattintson az **Eszközök** elemre a **Hatókör** ablaktáblában. Az eszközök listája az egyes eszközökre vonatkozó információkkal együtt megjelenik az **Eredmények** ablaktáblán.

### <a name="volumes-node"></a>Kötetek csomópontja
A **Kötetek** csomópont azokat a meghajtókat jelöli, amelyek megfelelnek az állomás által csatlakoztatott köteteknek, beleértve az iSCSI-n keresztül és az eszközön keresztül felderített meghajtókat is. Ezzel a csomóponttal megtekintheti a rendelkezésre álló kötetek listáját, és egyes köteteket rendelhet kötetcsoportokhoz.

* A csomópont kibontásához kattintson a **Kötetek**gomb melletti nyílikonra.
* A rendelkezésre álló műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **Kötetek** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* A kötetek listájának megtekintéséhez kattintson a **Hatókör** ablaktábla **Kötetek** gombjára. A kötetek listája az egyes kötetekkel kapcsolatos információkkal együtt megjelenik az **Eredmények** ablaktáblán.

### <a name="volume-groups-node"></a>Kötetcsoportok csomópontja
A kötetcsoportokkkkkat konzisztenciacsoportoknak is nevezik. Minden kötetcsoport alkalmazásokkal kapcsolatos kötetek készlete, amely segít az alkalmazások konzisztenciájának biztosításában a biztonsági mentési műveletek során. A **Kötetcsoportok** csomópont segítségével konfigurálhatja ezeket a csoportokat, és interaktív biztonsági mentéseket készíthet, vagy biztonsági mentési ütemezéseket hozhat létre. 

* A csomópont kibontásához kattintson a **Hangerőcsoportok**gomb melletti nyílikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **Kötetcsoportok** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* A kötetcsoportok listájának megtekintéséhez kattintson a **Hatókör** ablaktábla **Kötetcsoportok** gombjára. A kötetcsoportok listája az egyes kötetcsoportokra vonatkozó információkkal együtt megjelenik az **Eredmények** ablaktáblán.

### <a name="backup-policies-node"></a>Biztonsági mentési házirendek csomópontja
A biztonsági mentési szabályzatok a helyi és a felhőbeli pillanatképek feladatütemezései. A **Biztonsági másolat házirendek** csomópontsegítségével megadhatja, hogy milyen gyakran jön létre biztonsági másolat, és mennyi ideig őrizze meg a biztonsági másolatot. 

* A csomópont kibontásához kattintson a **Biztonsági másolat házirendek**elem melletti nyílikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **Biztonsági másolat házirendek** csomópontjára, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* A biztonsági mentési házirendek listájának megtekintéséhez kattintson a **Hatókör** ablaktábla **Biztonsági mentési házirendjei** elemre. A biztonsági mentési házirendek listája az egyes házirendekkel kapcsolatos információkkal együtt megjelenik az **Eredmények** ablaktáblán.

> [!NOTE]
> Legfeljebb 64 biztonsági mentést őrizhet meg.


### <a name="backup-catalog-node"></a>Biztonsági másolat katalóguscsomópontja
A **biztonsági másolat katalógus** csomópont tartalmazza az Azure StorSimple-kötetek helyszíni és külső biztonsági mentéseinek listáját. Ez a csomópont kötetcsoport szerint van rendezve, és minden kötetcsoport-tároló külön struktúrákat tartalmaz a helyi pillanatképekhez (a **helyi pillanatkép**csomópont) és a felhőbeli pillanatképekhez (a **felhőbeli pillanatképek** csomóponthoz). Kibontáskor minden kötetcsoport-tároló felsorolja az interaktívan vagy egy konfigurált házirend által készített sikeres biztonsági mentéseket.

* A csomópont kibontásához kattintson a **Katalógus biztonsági másolata**ikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **Biztonsági másolat katalógus** csomópontjára, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* A biztonsági mentési pillanatképek listájának megtekintéséhez kattintson a **Hatókör** ablaktábla **Katalógus biztonsági mentése** elemre. A pillanatképek listája az egyes pillanatképekre vonatkozó információkkal együtt megjelenik az **Eredmények** ablaktáblán.

### <a name="local-snapshots-node"></a>Helyi pillanatképek csomópont
A **Helyi pillanatképek** csomópont egy adott kötetcsoport helyi pillanatképeit sorolja fel. A csomópont a **Biztonsági másolat katalógus** csomópontja alatt található a **Hatókör** ablaktáblában. A helyi pillanatképek az Azure StorSimple-eszközön tárolt kötetadatok időponthoz ért időpontjai. Az ilyen típusú biztonsági mentés általában gyorsan létrehozható és visszaállítható. A helyi pillanatképet úgy is használhatja, mint egy helyi biztonsági másolat.

* A csomópont kibontásához kattintson a **Helyi pillanatképek**gomb melletti nyílikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **Helyi pillanatképek** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* A helyi pillanatképek listájának megtekintéséhez kattintson a **Hatókör** ablaktábla **Helyi pillanatképek** gombjára. A pillanatképek listája az egyes pillanatképekre vonatkozó információkkal együtt megjelenik az **Eredmények** ablaktáblán.

### <a name="cloud-snapshots-node"></a>Felhőbeli pillanatképek csomópont
A **felhőbeli pillanatképek** csomópont egy adott kötetcsoport felhőbeli pillanatképeit sorolja fel. A csomópont a **Biztonsági másolat katalógus** csomópontja alatt található a **Hatókör** ablaktáblában. A felhőbeli pillanatképek a felhőben tárolt kötetadatok időponthoz szerzett másolatai. A felhőbeli pillanatkép egy másik, telephelyen kívüli tárolórendszeren replikált pillanatképnek felel meg. A felhőbeli pillanatképek különösen hasznosak a vész-helyreállítási forgatókönyvekben.

* A csomópont kibontásához kattintson a **Felhőbeli pillanatképek**elem melletti nyílikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **Felhőbeli pillanatképek** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* A felhőbeli pillanatképek listájának megtekintéséhez kattintson a **Hatókör** ablaktábla **Felhőbeli pillanatképek** elemre. A pillanatképek listája az egyes pillanatképekre vonatkozó információkkal együtt megjelenik az **Eredmények** ablaktáblán.

### <a name="jobs-node"></a>Feladat csomópont
A **Feladatok** csomópont információkat tartalmaz az ütemezett, futó és a legutóbb befejezett biztonsági mentési feladatokról. 

* A csomópont kibontásához kattintson a **Feladatok**gomb melletti nyílikonra.
* Az elérhető műveletek menüjének megtekintéséhez kattintson a jobb gombbal a **Feladatok** csomópontra, vagy kattintson a jobb gombbal a kibontott nézetben megjelenő csomópontok bármelyikére.
* Az ütemezett feladatok listájának megtekintéséhez bontsa ki a **Feladatok** csomópontot, majd kattintson az **Ütemezett gombra.** A korábban konfigurált feladatok listája és az egyes feladatokra vonatkozó információk megjelennek az **Eredmények** ablaktáblán. 
* A legutóbb befejezett feladatok listájának megtekintéséhez bontsa ki a **Feladatok** csomópontot, majd kattintson az **Utolsó 24 óra parancsra.** Az elmúlt 24 órában befejezett feladatok listája megjelenik az **Eredmények** ablaktáblán. **Az Eredmények** ablaktábla az egyes befejezett feladatokkal kapcsolatos információkat is tartalmaz.
* Az éppen futó feladatok listájának megtekintéséhez bontsa ki a **Feladat** csomópontot, majd kattintson a **Futás**gombra. Az eredmények ablaktáblán megjelenik az éppen **Results** futó feladatok és az egyes feladatokra vonatkozó információk listája.

## <a name="results-pane"></a>Eredmények ablaktábla
Az **Eredmények** ablaktábla a StorSimple Snapshot Manager felhasználói felületének középső ablaktáblája. A **Hatókör** ablaktáblában kijelölt csomópont listáit és részletes állapotadatait tartalmazza.

### <a name="example"></a>Példa
A következő példa megtekintéséhez kattintson a **Kötetcsoportok** csomópontra a **Hatókör** ablaktáblában. **Az Eredmények** ablaktábla megjeleníti a kötetcsoportok listáját az egyes csoportok részleteivel.

![Eredmények ablaktábla](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Az Eredmények ablaktáblán látható részleteket **beállíthatja:** kattintson a jobb gombbal egy csomópontra a **Hatókör** ablaktáblában, kattintson a **Nézet**parancsra, majd kattintson az **Oszlopok hozzáadása/eltávolítása parancsra.**

## <a name="actions-pane"></a>Műveletek ablaktábla
A **Műveletek** ablaktábla a StorSimple Snapshot Manager felhasználói felületének jobb oldali ablaktáblája. A **Hatókör** vagy az **Eredmények** ablaktáblán kijelölt csomóponton, nézetben vagy adaton végrehajtható műveletek menüjét tartalmazza. A **Műveletek** ablaktábla ugyanazokat a parancsokat tartalmazza, mint a **Hatókör** és az **Eredmények** ablaktáblán az elemekhez elérhető **Művelet** menük. Az egyes műveletek leírását a **Művelet** menü szakaszának táblázatában található.

### <a name="examples"></a>Példák
A következő példa megtekintéséhez a **Hatókör** ablaktáblán bontsa ki a **Feladatok** csomópontot, és kattintson az **Ütemezett gombra.** A **Műveletek** ablaktábla az **ütemezett** csomóponthoz rendelkezésre álló műveleteket jeleníti meg.

![Példa a Műveletek ablaktábla ütemezett feladateire](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

További beállítások megtekintéséhez a **Hatókör** ablaktáblában bontsa ki a **Feladatok** csomópontot, kattintson az **Ütemezett**elemre, majd az **Eredmények** ablaktáblán kattintson egy ütemezett feladatra. A **Műveletek** ablaktábla az ütemezett feladathoz rendelkezésre álló műveleteket jeleníti meg, ahogy az a következő példában látható.

![Példa a Műveletek ablaktábla feladatműveletekre](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Billentyűzetnavigáció és billentyűparancsok
A StorSimple Snapshot Manager lehetővé teszi a Windows operációs rendszer és a Microsoft Management Console (MMC) kisegítő szolgáltatásait. Néhány billentyűzetnavigációs funkciót és parancsikont is tartalmaz, amelyek a StorSimple Snapshot Managerre jellemzőek, a következő szakaszokban leírtak szerint.

* [Billentyűzet navigációs billentyűi](#keyboard-navigation-keys) 
* [Menüsor helyi billentyűparancsai](#menu-bar-shortcut-keys) 
* [Hatókör ablaktábla billentyűparancsai](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Billentyűzet navigációs billentyűi
Az alábbi táblázat azokat a kulcsokat ismerteti, amelyekkel navigálhat a StorSimple Snapshot Manager felhasználói felületén. 

| Navigációs gomb | Műveletek |
|:--- |:--- |
| Le nyílbillentyű |A lefelé mutató nyílbillentyűvel függőlegesen léphet a menü vagy ablaktábla következő elemére. |
| Enter |Az Enter billentyű lenyomása egy művelet végrehajtásához, majd folytassa a következő lépéssel. Az Enter billentyű lenyomásával például a **Tovább**, **az OK**vagy a **Létrehozás**lehetőséget, majd ugrás a varázsló következő lépésére. |
| Esc |Az Esc billentyű lenyomásával zárjon be egy menüt, vagy szakítson meg és zárjon be egy lapot. |
| F1 |Az F1 billentyűvel tekintse meg az aktuálisan aktív ablak súgótémakörét. |
| F5 |A csomópont frissítéséhez nyomja le az F5 billentyűt. |
| F6 |Az F6 billentyű lenyomása a **Hatókör** ablaktábláról az **Eredmények** ablaktáblára való áthelyezéshez. |
| F10 |Az F10 billentyű lenyomásához lépjen a menüsorra. |
| Balra nyíl |A bal nyílbillentyűvel vízszintesen mozoghat a menüsor beállításából az előző opcióra. Amikor a menüsor előző elemére lép, megjelenik az előző elem művelet (vagy helyi) menüje. |
| Jobbra nyíl |A jobb nyílbillentyűvel vízszintesen mozoghat az egyik menüsorból a másikba. Amikor a menüsor következő elemére lép, megjelenik az új elem művelet (vagy helyi) menüje. |
| Tabulátorbillentyű |A Tab billentyűvel lépjen a konzol következő ablaktáblájára, illetve a lap következő kijelölésére vagy szövegmezőjére. |
| Fel felé mutató nyílbillentyű |A felfelé mutató nyílbillentyűvel függőlegesen léphet a menü vagy ablaktábla előző elemére. |

### <a name="menu-bar-shortcut-keys"></a>Menüsor helyi billentyűparancsai
Az alábbi táblázat a menüsor billentyűkombinációit ismerteti. Miután megnyomta a billentyűparancsokat, és megnyílik a menü, használhatja a menü billentyűparancsait (a menü aláhúzott billentyűit). A menüsorról a Menüsor ról a [Menüsor elemre](#menu-bar)talál.

| Parancsikont | Eredmény | Menü helyi gombja | Eredmény |
|:--- |:--- |:--- |:--- |
| ALT+F |Megnyitja a **Fájl menüt.** |N |Új konzolpéldány megnyitása. |
|  |O |Megnyitja a **Felügyeleti eszközök** lapot. | |
|  |S |Menti a StorSimple Snapshot Manager konzolt. | |
|  |A |Megnyitja a **Mentés másként** lapot. | |
|  |M |Megnyitja a **Beépülő modul hozzáadása/eltávolítása** lapot. | |
|  |P |Megnyitja a **Beállítások** lapot. | |
|  |H |Megnyitja az online súgót. | |
| ALT+A |Megnyitja a **Művelet menüt.** |I |Be- és kikapcsolja az importálási megjelenítési beállítást. |
|  |W |Megnyit egy új StorSimple Snapshot Manager konzolt. | |
|  |F |Frissíti a StorSimple Snapshot Manager konzolt. | |
|  |L |Megnyitja a **Lista exportálása** lapot. | |
|  |H |Megnyitja az online súgót. | |
| ALT+V |Megnyitja a **Nézet menüt.** |A |Megnyitja az **Oszlopok hozzáadása/eltávolítása** lapot. |
|  |U |Megnyitja a **Nézet testreszabása** lapot. | |
| ALT+O |Megnyitja a **Kedvencek menüt.** |A |Megnyitja a **Hozzáadás a kedvencekhez** lapot. |
|  |O |Megnyitja a **Kedvencek rendszerezése** lapot. | |
| ALT+W |Megnyitja az **Ablak menüt.** |N |Megnyit egy másik StorSimple Snapshot Manager ablakot. |
|  |C# |Az összes megnyitott konzolablakmegjelenítése kaszkádolt stílusban. | |
|  |T |Az összes megnyitott konzolablakot rácsos mintázatban jeleníti meg. | |
|  |I |Az ikonokat vízszintes sorba rendezi a képernyő alján. | |
| ALT+H |Megnyitja a **Súgó menüt.** |H |Megnyitja az online súgót. |
|  |T |Megnyitja a Microsoft TechNet Tech Center weblapját. | |
|  |A |Megnyitja a **Microsoft Management Console – abenyit( Be: Microsoft Management Console)** lapot. | |

### <a name="scope-pane-shortcut-keys"></a>Hatókör ablaktábla billentyűparancsai
Az alábbi táblázatok a **Hatókör** ablaktábla egyes csomópontjainak billentyűparancs-kombinációit mutatják be. 

* [Eszközök csomópont jának billentyűparancsai](#devices-node-shortcut-keys)
* [Kötetek csomópontjának billentyűparancsai](#volumes-node-shortcut-keys)
* [Kötetcsoportok csomópont billentyűparancsai](#volume-groups-node-shortcut-keys)
* [Biztonsági másolat házirendjei csomópontjának billentyűparancsai](#backup-policies-node-shortcut-keys)
* [Katalóguscsomópont-billentyűparancsok biztonsági másolatának készítése](#backup-catalog-node-shortcut-keys)
* [Feladatok csomópont billentyűparancsai](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Eszközök csomópont jának billentyűparancsai
| Menü helyi útja | Eredmény |
|:--- |:--- |
| C# |Megnyitja az **Eszköz konfigurálása** lapot. |
| D |Frissíti az eszközök és az eszközök adatainak listáját. |
| V |Megnyitja a **Nézet menüt.** |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt, amely a **Részletek** csomópontra összpontosít. |
| F |Frissíti a StorSimple Snapshot Manager konzolt. |
| L |Megnyitja a **Lista exportálása** lapot. |
| H |Megnyitja az online súgót. |

#### <a name="volumes-node-shortcut-keys"></a>Kötetek csomópontjának billentyűparancsai
| Menü helyi útja | Eredmény |
|:--- |:--- |
| V |Frissíti a kötetek listáját. |
| V (nyomja meg kétszer) |Megnyitja a **Nézet menüt.** |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt, amely a **Kötetek** csomópontra összpontosít. |
| F |Frissíti a StorSimple Snapshot Manager konzolt. |
| L |Megnyitja a **Lista exportálása** lapot. |
| H |Megnyitja az online súgót. |

#### <a name="volume-groups-node-shortcut-keys"></a>Kötetcsoportok csomópont billentyűparancsai
| Menü helyi útja | Eredmény |
|:--- |:--- |
| G |Megnyitja a **Kötetcsoport létrehozása** lapot. |
| V |Megnyitja a **Nézet menüt.** |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt, amely a **Kötetcsoportok** csomópontra összpontosít. |
| F |Frissíti a StorSimple Snapshot Manager konzolt. |
| L |Megnyitja a **Lista exportálása** lapot. |
| H |Megnyitja az online súgót. |

#### <a name="backup-policies-node-shortcut-keys"></a>Biztonsági másolat házirendjei csomópontjának billentyűparancsai
| Menü helyi útja | Eredmény |
|:--- |:--- |
| B |Megnyitja a **Házirend létrehozása** lapot. |
| V |Megnyitja a **Nézet menüt.** |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt, amely a **Kötetcsoportok** csomópontra összpontosít. |
| F |Frissíti a StorSimple Snapshot Manager konzolt. |
| L |Megnyitja a **Export List **lapot. |
| H |Megnyitja az online súgót. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Katalóguscsomópont-billentyűparancsok biztonsági másolatának készítése
| Menü helyi útja | Eredmény |
|:--- |:--- |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt, amely a **Kötetcsoportok** csomópontra összpontosít. |
| F |Frissíti a StorSimple Snapshot Manager konzolt. |
| H |Megnyitja az online súgót. |

#### <a name="jobs-node-shortcut-keys"></a>Feladatok csomópont billentyűparancsai
| Menü helyi útja | Eredmény |
|:--- |:--- |
| V |Megnyitja a **Nézet menüt.** |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt, amely a **Feladatok** csomópontra összpontosít. |
| F |Frissíti a StorSimple Snapshot Manager konzolt. |
| L |Megnyitja a **Lista exportálása** lapot. |
| H |Online súgó megnyitása |

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezelheti a StorSimple snapshot kezelőt a StorSimple megoldás felügyeletéhez.](storsimple-snapshot-manager-admin.md)
* Ismerje meg, hogyan [csatlakozhat a StorSimple Snapshot Manager eszközhöz és kezelhetőeszközökhöz.](storsimple-snapshot-manager-manage-devices.md)

