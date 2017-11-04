---
title: "StorSimple Snapshot Manager felhasználói felületen |} Microsoft Docs"
description: "A StorSimple Snapshot Manager felhasználói felület és annak segítségével kezelheti a biztonsági mentési feladatok és a biztonságimásolat-katalógus ismerteti."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: 
ms.openlocfilehash: b48c507e38eb7cadff56259f617e336e4efe5708
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Használja a StorSimple Snapshot Manager felhasználói felület biztonsági mentési feladatok és a biztonságimásolat-katalógus kezelése

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Manager rendelkezik egy egyszerűen elsajátítható felhasználói felületet, amelyekkel igénybe vehet, és biztonsági mentéseit. Ez az oktatóanyag bemutatja azokat a felhasználói felület, és majd ismerteti, hogyan összetevői. További részletes ismertetését a StorSimple Snapshot Manager: [Mi az StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konzol leírása
A felhasználói felület megtekintéséhez kattintson a StorSimple Snapshot Manager ikonra az asztalon. A konzolablakban jelenik meg, a következő ábrán látható módon.

![StorSimple Snapshot Manager ablaktáblák](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A konzolablakban öt fő elemmel rendelkezik. Az egyes elemek részletes ismertetése a megfelelő hivatkozásra kattintva.

* [Menüsáv](#menu-bar) 
* [Eszköztár](#tool-bar) 
* [Hatókör ablaktábla](#scope-pane) 
* [Eredmények ablaktábla](#results-pane) 
* [Műveletek panel](#actions-pane) 

Ezen kívül a StorSimple Snapshot Manager támogatja [navigációs és számos parancsikont billentyűzet](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konzol kisegítő lehetőségek
A StorSimple Snapshot Manager felhasználói felülete támogatja a Windows operációs rendszer és a Microsoft Management Console (MMC), valamint az egyes StorSimple Snapshot Manager-specifikus billentyűparancsok által biztosított kisegítő lehetőségeit. 

* A Windows kisegítő lehetőségei leírását itt [azok a billentyűparancsok Windows](https://support.microsoft.com/kb/126449). 
* Az MMC kisegítő lehetőségeket nyújtó szolgáltatásai leírását itt [kisegítő lehetőségek az MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* A StorSimple Snapshot Manager kisegítő lehetőségei leírását itt [navigációs és parancsikon billentyűzet](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Menüsáv
A menüsorban a konzol ablakának tetején található [fájl](#file-menu), [művelet](#action-menu), [nézet](#view-menu), [Kedvencek](#favorites-menu), [ablak](#window-menu), és [súgó](#help-menu) menün kellene végiglépkednie.

Kattintson bármelyik elemre kattintva megtekintheti a rendelkezésre álló parancsok listáját a menüt menüsávjában. Az alábbi példa azt mutatja meg a **nézet** menüsorban kijelölt menü.

![Kijelölt nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Fájl menü
A **fájl** menü szabványos Microsoft Management Console (MMC) parancsokat tartalmaz.

#### <a name="menu-access"></a>Menüpont
Megtekintéséhez a **fájl** menüben kattintson a **fájl** menüsávjában. A következő menü jelenik meg.

![StorSimple Snapshot Manager fájl menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek a **fájl** menü.

| Menüpont | Leírás |
|:--- |:--- |
| új |Kattintson a **új** egy új konzolt, a StorSimple Snapshot Manager alapú létrehozásához. |
| Nyílt |Kattintson a **nyissa meg a** meglévő konzol megnyitásához. |
| Mentés |Kattintson a **mentése** menteni a jelenlegi konzolt. |
| Mentés másként |Kattintson a **Mentés másként** a jelenlegi konzolt egy új, átnevezett példányának létrehozásakor. Használja a **Mentés másként** lehetőség, hogy a nézetek testreszabásának, és mentse a későbbi beolvasásához. Például létrehozhatja a StorSimple Snapshot Manager beépülő modulok, amelyek adott kiszolgálók. |
| Hozzáadása beépülő modul |Kattintson a **beépülő modul hozzáadása/eltávolítása** hozzáadása vagy eltávolítása a beépülő modulok és a csomópontok rendezése a **hatókör** ablaktáblán. További információkért látogasson el [hozzáadása, eltávolítása, és rendezés beépülő modulok és bővítmények az MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Beállítások |Kattintson a **beállítások** a konzol ikon módosításához adja meg a felhasználói hozzáférési mód és az engedélyek, vagy növelje a szabad lemezterület konzol fájltörléssel. |
| Fájl elérési utak listája |Kattintson a számozott lista nyissa meg újra a közelmúltban megnyitott fájl elérési útját. |
| Kilépés |Kattintson a **kilépési** bezárásához a **fájl** menü. |

### <a name="action-menu"></a>Művelet menü
Használja a **művelet** menüre kattintva kiválaszthatja az elérhető műveletek. A beállítástól függ a rendelkezésre álló elemek a **hatókör** ablaktáblán vagy **eredmények** ablaktáblán.

#### <a name="menu-access"></a>Menüpont
Megtekintéséhez a **művelet** menü, tegye a következők valamelyikét:

* Kattintson a jobb gombbal egy elemére a **hatókör** ablaktáblán vagy **eredmények** ablaktáblán.
* Jelöljön ki egy elemet a a **hatókör** ablaktáblán vagy **eredmények** ablaktáblán, és kattintson **művelet** menüsávjában. 

Például, ha a legfelső csomópontra a **hatókör** ablaktáblán, majd kattintson a jobb gombbal, illetve kattintson **művelet** a menüsávon a következő menü jelenik meg.

![StorSimple Snapshot Manager művelet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

A **műveletek** ablaktáblán (a konzol jobb) tartalmazza a azonos műveleteket, mint a **művelet** menü. Emellett a **műveletek** ablaktábla tartalmaz a **nézet** menü elemeit, amelyek lehetővé teszik, hogy az egyéni nézet létrehozása az **eredmények** ablaktáblán.

![Nyissa meg a Műveletek panel a Nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menü leírása
A következő táblázat a StorSimple Snapshot Manager műveletek betűrendbe szedett listáját tartalmazza. 

* A **művelet** oszlopban található csomópontokon és eredmények hajthat végre műveleteket. 
* A **navigációs** oszlop tárgyalja a megfelelő **művelet** menü, hogy kiválaszthatja, hogy a művelet. Egyes műveletek jelennek meg több **művelet** menün kellene végiglépkednie. Ezek a műveletek végrehajtásához, válasszon ki egy **navigációs** felsorolásjeles listaelemet. 
* A **leírás** oszlop egyes műveletek során a módját ismerteti a **művelet** menü vagy a Műveletek ablaktáblában, és elmagyarázza, hogyan kezeli.

> [!NOTE]
> A **műveletek** ablaktáblán és **művelet** menük tartalmazhat további beállításokat, például **nézet**, **új ablak innen**, **frissítése**, **lista exportálása**, és **súgó**. Ezek a beállítások az MMC részeként elérhető, és nem adott a StorSimple Snapshot Manager. A táblázat ezek a beállítások leírását tartalmazza.
> 
> 

| Műveletek | Navigációs | Leírás |
|:--- |:--- |:--- |
| Hitelesítés |Kattintson a **eszközök** csomópont, és kattintson a jobb gombbal egy eszközt a **eredmények** ablaktáblán. |Kattintson a **hitelesítés** meg kell adnia a jelszót az eszközön konfigurált. |
| Klónozás |Bontsa ki a **biztonságimásolat-katalógus**, bontsa ki a **felhőalapú pillanatfelvételek**, egy dátummal biztonsági mentés gombra, majd válassza ki a kötet a **eredmények** ablaktáblán. |Kattintson a **Klónozás** másolatot készít egy felhő-pillanatfelvételt és a megadott helyen tárolja. |
| Egy eszköz konfigurálása |Kattintson a jobb gombbal a **eszközök** csomópont. |Kattintson a **eszköz konfigurálása** konfigurálása egy egyetlen vagy több eszközön, a Windows gazdagéphez való csatlakozásra. |
| A biztonsági mentési házirend létrehozása |A következők közül:<ul><li>Kattintson a jobb gombbal **biztonsági mentési házirendek**.</li><li>Kattintson a gombra, vagy bontsa ki a **kötet csoportok**, és kattintson a jobb gombbal egy kötet csoportot.</li><li>Kattintson a gombra, vagy bontsa ki a **biztonságimásolat-katalógus**, majd kattintson a jobb gombbal egy kötet csoportot.</li></ul> |Kattintson a **biztonsági mentési házirend létrehozása** ütemezett biztonsági mentés egy kötet csoport konfigurálása. |
| Kötet csoport létrehozása |A következők közül:<ul><li>Kattintson a **kötetek** csomópontot, majd kattintson a jobb gombbal a kötet a **eredmények** ablaktáblán.</li><li>Kattintson a jobb gombbal a **kötet csoportok** csomópont.</li></ul> |Kattintson a **kötet csoport létrehozása** kötetek hozzárendelése egy kötet csoporthoz. |
| Törlés |Kattintson a csomópont vagy az eredmény (Ez az elem megjelenő számos **művelet** menük és **műveletek** ablaktáblák.) |Kattintson a **törlése** törli a csomópont vagy a kiválasztott eredménye. Amikor a megerősítő párbeszédpanelen megjelenik, erősítse meg, vagy visszavonja a törlést. |
| Részletek |Kattintson a **eszközök** csomópont, és kattintson a jobb gombbal egy eszközt a **eredmények** ablaktáblán. |Kattintson a **részletek** egy eszköz számára a konfigurációs részletek megtekintéséhez. |
| Szerkesztés |Kattintson a **biztonsági mentési házirendek**, és kattintson a jobb gombbal a házirend a **eredmények** ablaktáblán. |Kattintson a **szerkesztése** egy kötet csoport biztonsági mentési ütemezés módosítása. |
| Lista exportálása |Kattintson bármelyik csomópontját vagy az eredmény (Ez az elem jelenik meg az összes **művelet** menük és **műveletek** ablaktáblák.) |Kattintson a **lista exportálása** listáját egy vesszővel tagolt (CSV) fájl mentéséhez. Egy táblázat alkalmazásba elemzéshez majd importálja ezt a fájlt. |
| Súgó |Kattintson bármelyik csomópontját vagy eredménye. (Ez az elem jelenik meg az összes **művelet** menük és **műveletek** ablaktáblák.) |Kattintson a **súgó** egy külön böngészőablakban online súgó megnyitásához. |
| Itt új ablak |Kattintson bármelyik csomópontját vagy az eredmény (Ez az elem jelenik meg az összes **művelet** menük és **műveletek** ablaktáblák.) |Kattintson a **új ablak innen** egy új StorSimple Snapshot Manager ablak megnyitásához. |
| Frissítés |Kattintson bármelyik csomópontját vagy az eredmény (Ez az elem jelenik meg az összes **művelet** menük és **műveletek** ablaktáblák.) |Kattintson a **frissítése** frissíteni az éppen megjelenített StorSimple Snapshot Manager ablak. |
| Eszköz frissítése |Kattintson a **eszközök** csomópont, és kattintson a jobb gombbal egy eszközt a **eredmények** ablaktáblán. |Kattintson a **frissítése eszköz** egy adott csatlakoztatott eszköz StorSimple Snapshot Manager szinkronizálása. |
| Eszközök frissítése |Kattintson a jobb gombbal a **eszközök** csomópont. |Kattintson a **frissítési eszközök** szinkronizálni a csatlakoztatott eszközök listája a StorSimple Snapshot Manager. |
| Kötetek újraellenőrzése |Kattintson a jobb gombbal a **kötetek** csomópont. |Kattintson a **ismételt vizsgálat kötetek** megjelenő kötetek listáját a **eredmények** ablaktáblán. |
| Visszaállítás |Bontsa ki a **biztonságimásolat-katalógus**, bontsa ki a kötet csoport, **helyi pillanatképeket** vagy **felhőalapú pillanatfelvételek**, majd kattintson a jobb gombbal a biztonsági mentés. |Kattintson a **visszaállítása** lecseréli az aktuális kötet csoport adatokat a kijelölt biztonsági adatait. |
| Biztonsági mentés igénybe |A következők közül:<ul><li>Bontsa ki a **kötet csoportok**, majd kattintson a jobb gombbal egy kötet csoportot.</li><li>Bontsa ki a **biztonságimásolat-katalógus**, majd kattintson a jobb gombbal egy kötet csoportot.</li></ul> |Kattintson a **érvénybe biztonsági mentési** egy biztonsági mentési feladata azonnal elindul. |
| Váltás importálja megjelenítése |Kattintson a jobb gombbal a legfelső csomópontra a **hatókör** ablaktáblán (a **StorSimple Snapshot Manager** csomópont a példákban). |Kattintson a **váltása importálja megjelenítési** vagy a kötet csoportok és a StorSimple Device Manager szolgáltatás irányítópultról importált társított biztonsági másolatok elrejtéséhez. |

### <a name="view-menu"></a>Nézet menü
Használja a **nézet** menü az egyéni nézet létrehozása az **eredmények** ablaktábla tartalmát. A **nézet** menü tartalmaz **oszlopok hozzáadása/eltávolítása** és **Testreszabás** beállítások.

#### <a name="menu-access"></a>Menüpont
Érheti el a **nézet** menüjéből vagy a menüsoron a **műveletek** ablaktáblán.

![StorSimple Snapshot Manager Nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek a **nézet** menü.

| Menüpont | Leírás |
|:--- |:--- |
| Oszlopok hozzáadása/eltávolítása |Kattintson a **oszlopok hozzáadása/eltávolítása** hozzáadásához vagy eltávolításához oszlop szerepel a **eredmények** ablaktáblán. |
| Testreszabás |Kattintson a **Testreszabás** megjelenítendő vagy elrejtendő elemeket a StorSimple Snapshot Manager console ablakban. |

### <a name="favorites-menu"></a>A Kedvencek menü
Használja a **Kedvencek** menüből, hogy hozzáadása, eltávolítása és rendszerezése Lapmegtekintések és feladatokat, amelyek gyakran használják. 

#### <a name="menu-access"></a>Menüpont
Elérheti a **Kedvencek** az egérrel a menüsoron menüjéből.

![StorSimple Snapshot Manager Kedvencek menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek a **Kedvencek** menü.

| Menüpont | Leírás |
|:--- |:--- |
| Hozzáadása a Kedvencekhez |Kattintson a **hozzáadása a Kedvencekhez** az aktuális nézet hozzáadása a Kedvencek listájához. |
| Kedvencek rendezése |Kattintson a **Kedvencek rendezése** rendszerezésére a Kedvencek mappa tartalmát. |

### <a name="window-menu"></a>Ablak menü
Használja a **ablak** menü hozzáadásához és a StorSimple Snapshot Manager konzol windows átrendezését.

#### <a name="menu-access"></a>Menüpont
Érheti el a **ablak** az egérrel a menüsoron menüjéből.

![StorSimple Snapshot Manager ablak menü](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A menü alján a számozott lista mutatja azokat, nyissa meg a jelenleg használt windows. Kattintson a lista a az ablak vonja az előtérben található ablak. 

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat ablak menüben megjelenő elemeket ismerteti.

| Menüpont | Leírás |
|:--- |:--- |
| Új ablak |Kattintson a **új ablak** való Nyisson új konzolablakot (mellett a meglévő ablakban). |
| Lépcsőzetes elrendezés |Kattintson a **Cascade** megjelenítéséhez nyissa meg a konzolt windows kaszkádolt Style. |
| Egymás alatt |Kattintson a **csempe vízszintesen** megjelenítéséhez nyissa meg a konzolt windows csempe (vagy a rács) formátumban. |
| Ikonok elrendezése |Ha több konzol windows megnyitása és minimalizálása érdekében őket, és kattintson az asztal Elszórva **ikonok elrendezése** sorrendbe olyan vízszintes sorok esetén a képernyő alján. |

### <a name="help-menu"></a>Súgó menü
Használja a **súgó** menüjéből érhető el az MMC és a StorSimple Snapshot Manager online súgójának megtekintéséhez. A rendszeren jelenleg telepített MMC és a StorSimple Snapshot Manager szoftver verziójával kapcsolatos információkat is megtekintheti. 

Érheti el a **súgó** az egérrel a menüsoron menüjéből. Emellett a StorSimple Snapshot Manager súgó-témaköröket a **műveletek** ablaktáblán.

![StorSimple Snapshot Manager Súgó menü](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat a Súgó menüben megjelenő elemeket ismerteti.

| Menüpont | Leírás |
|:--- |:--- |
| A StorSimple Snapshot Manager súgó |Kattintson a **a StorSimple Snapshot Manager súgó** külön ablakban StorSimple Snapshot Manager súgó megnyitásához. |
| Súgótémakörök |Kattintson a **Súgó-témaköröket** külön ablakban MMC online súgó megnyitásához. |
| TechCenter webhelyen |Kattintson a **TechCenter webhely** megnyitni a Microsoft TechNet technológia Center kezdőlap külön ablakban. |
| A Microsoft Management Console kapcsolatos |Kattintson a **kapcsolatban a Microsoft Management Console** megtekintéséhez, mely a Microsoft Management Console verziója telepítve van a rendszeren. |
| A StorSimple Snapshot Manager névjegye |Kattintson a **kapcsolatos StorSimple Snapshot Manager** megtekintéséhez a beépülő modul melyik verziója telepítve van a rendszeren. |

## <a name="tool-bar"></a>Eszköztár
Az eszköztáron az egérrel a menüsoron alatt található navigációs és a feladat ikonokat tartalmaz. Minden ikon egy adott feladat parancsikon.

### <a name="icon-descriptions"></a>Ikon leírása
A következő táblázat ismerteti a megjelenő ikonok az eszköztáron. 

| Ikon | Leírás |
|:--- |:--- |
| ![Bal nyílbillentyű](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kattintson a balra mutató nyíl ikonra az előző laphoz való visszatéréshez. |
| ![Jobb nyílbillentyű](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |A jobbra mutató nyílra kattintva nyissa meg a következő oldalra (Ha a nyíl szürke, a művelet nem érhető el). |
| ![Másolatot ikon](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |A naprakész ikonra kattintva nyissa meg a konzolfán egy szinttel feljebb (a **hatókör** ablaktáblán). |
| ![Konzolfa megjelenítése/elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kattintson a megjelenítése/elrejtése konzol fa ikonra megjelenítése vagy elrejtése a **hatókör** ablaktáblán. |
| ![Lista exportálása](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Kattintson az Exportálás ikonjára, amely a megadott CSV-fájlba exportálni. |
| ![Súgó ikonra](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kattintson a Súgó ikonra nyisson meg egy MMC online súgó-témakörhöz. |
| ![Műveletek panel megjelenítése vagy elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kattintson a megjelenítése/elrejtése **műveletek** ablaktábla ikon megjelenítése vagy elrejtése a **műveletek** ablaktáblán. |

## <a name="scope-pane"></a>Hatókör ablaktábla
A **hatókör** ablaktábla a StorSimple Snapshot Manager felhasználói felületén a bal oldali ablaktáblán. A konzol (vagy a csomópont) fa tartalmaz, és az elsődleges navigációs mechanizmus a StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Hatókör ablaktábla struktúra
A **hatókör** ablaktáblában található tartalmazza kattintható objektumok (csomópontok) vannak rendszerezve fastruktúrában. 

![Hatókör ablaktábla](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Bontsa ki, vagy a csomópont összecsukása, kattintson a nyíl ikonra a csomópont neve mellett.
* Az állapot vagy a csomópont tartalmát megtekintéséhez kattintson a csomópont neve. Az információ jelenik meg a **eredmények** ablaktáblán. 

A **hatókör** panelen a következő csomópontokat tartalmazza: 

* [Eszköz csomópont alatt](#devices-node) 
* [Kötetek csomópont](#volumes-node) 
* [Kötet csoportok csomópont](#volume-groups-node) 
* [Biztonsági mentési házirendek csomópont](#backup-policies-node) 
* [Biztonsági mentési katalógus csomópont](#backup-catalog-node) 
* [Feladatok csomópont](#jobs-node) 

### <a name="scope-pane-tasks"></a>Hatókör ablaktábla feladatok
Használhatja a **hatókör** ablaktáblán egy adott csomóponton művelet elvégzéséhez. Válasszon egy feladatot, tegye a következők egyikét:

* Kattintson a jobb gombbal a csomópontra, majd válassza ki a feladatot a menüpontot.
* Kattintson a csomópontra, és kattintson a **művelet** menüsávjában. A megjelenő menüben válassza ki a feladatot.
* Kattintson a csomópontra, majd válassza ki a művelet a **műveletek** ablaktáblán.

Válasszon ki egy csomópontot, és a feladat listájának megtekintéséhez használja az alábbi módszereket, csak a ezen a csomóponton végrehajtható műveletek jelennek meg.

### <a name="devices-node"></a>Eszköz csomópont alatt
A **eszközök** csomópont a StorSimple eszköz és a StorSimple virtuális eszköz StorSimple Snapshot Manager csatlakozó jelöli. Jelölje ki azt a csomópontot, valamint eszköz beállítása, és importálja a társított kötetek, a kötetek csoportok és a meglévő biztonsági másolatok. Több eszköz egyetlen állomás is csatlakoztatható.

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **eszközök**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **eszközök** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Konfigurált eszközök listájának megtekintéséhez kattintson **eszközök** a a **hatókör** ablaktáblán. A listán szereplő eszközök, és minden eszköz, adatai megjelennek a **eredmények** ablaktáblán.

### <a name="volumes-node"></a>Kötetek csomópont
A **kötetek** csomópont jelöli, amelyek megfelelnek a meghajtókat, az állomás, köztük az iSCSI, és egy eszközön keresztül felderített keresztül felderített csatlakoztatott kötetek. Ezt a csomópontot használva megtekintheti az elérhető kötetek listáját, és rendelje hozzá az egyes kötetek kötet csoportok.

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **kötetek**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **kötetek** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Kötetek listájának megtekintéséhez kattintson **kötetek** a a **hatókör** ablaktáblán. A köteteket, és minden olyan kötetre, információ listája jelenik meg a **eredmények** ablaktáblán.

### <a name="volume-groups-node"></a>Kötet csoportok csomópont
Kötet csoportok olyan néven is ismert konzisztencia csoportok. Minden kötet csoport az alkalmazással kapcsolatos kötetek készlete, amely alkalmazás konzisztencia biztosítja a biztonsági mentési művelet során. Használja a **kötet csoportok** csomópont konfigurálhatja ezeket a csoportokat, és interaktív biztonsági másolatok készítése, vagy hozzon létre biztonsági mentési ütemterveket. 

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **kötet csoportok**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **kötet csoportok** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Kötet csoportok listájának megtekintéséhez kattintson **kötet csoportok** a a **hatókör** ablaktáblán. A kötet csoportok minden kötet csoportra vonatkozó információval listája jelenik meg a **eredmények** ablaktáblán.

### <a name="backup-policies-node"></a>Biztonsági mentési házirendek csomópont
Biztonsági mentési házirendek nincsenek feladatok ütemezését a helyi és felhőalapú pillanatfelvételek. Használja a **biztonsági mentési házirendek** adja meg, milyen gyakran egy biztonsági másolat készítése során, és mennyi ideig biztonsági-csomópontot meg kell őrizni. 

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **biztonsági mentési házirendek**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **biztonsági mentési házirendek** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* A biztonsági mentési házirendek listájának megtekintéséhez kattintson **biztonsági mentési házirendek** a a **hatókör** ablaktáblán. A biztonsági mentési házirendek, és minden házirendekkel kapcsolatos információ listája jelenik meg a **eredmények** ablaktáblán.

> [!NOTE]
> Legfeljebb 64 biztonsági másolatok továbbra is.


### <a name="backup-catalog-node"></a>Biztonsági mentési katalógus csomópont
A **biztonságimásolat-katalógus** csomópont helyszíni és a helyszínen biztonsági mentést Azure StorSimple-köteteket tartalmaz. Ez a csomópont kötet csoport szerint van rendezve, és minden egyes csoport kötettároló a pillanatképek helyi külön szerkezeteket tartalmaz (a **helyi pillanatfelvétel**s csomópont) és felhőalapú pillanatfelvételek (a **felhőalapú pillanatfelvételek** csomópont). Ha kibontva, minden egyes csoport kötettároló összes a sikeres biztonsági mentések interaktív módon, vagy a beállított házirend által végrehajtott sorolja fel.

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **biztonságimásolat-katalógus**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **biztonságimásolat-katalógus** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Biztonsági mentési pillanatképek listájának megtekintéséhez kattintson **biztonságimásolat-katalógus** a a **hatókör** ablaktáblán. A pillanatképek minden pillanatkép kapcsolatos információval listája jelenik meg a **eredmények** ablaktáblán.

### <a name="local-snapshots-node"></a>A pillanatképek helyi csomópont
A **helyi pillanatképeket** csomópont egy adott kötet csoport helyi pillanatképeket sorolja fel. A csomópont alatt található a **biztonságimásolat-katalógus** csomópontja a **hatókör** ablaktáblán. A pillanatképek helyi másolatai időpontban kötet az Azure StorSimple eszközön tárolt adatokat. Általában ilyen típusú biztonsági mentést hozható létre és gyorsan visszaállítva. Egy helyi pillanatfelvétel a helyi biztonsági másolat ugyanúgy használhatja.

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **helyi pillanatképeket**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **helyi pillanatképeket** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* A pillanatképek helyi listájának megtekintéséhez kattintson **helyi pillanatképeket** a a **hatókör** ablaktáblán. A pillanatképek minden pillanatkép kapcsolatos információval listája jelenik meg a **eredmények** ablaktáblán.

### <a name="cloud-snapshots-node"></a>Felhőalapú pillanatfelvételek csomópont
A **felhőalapú pillanatfelvételek** csomópont felhőalapú pillanatfelvételek egy adott kötet csoport sorolja fel. A csomópont alatt található a **biztonságimásolat-katalógus** csomópontja a **hatókör** ablaktáblán. Felhőalapú pillanatfelvételek másolatai időpontban kötet adatait, amely a felhőben vannak tárolva. Egy felhőalapú pillanatfelvétel megegyezik egy másik, a telephelytől távoli tárolórendszer replikálva pillanatképet. Felhőalapú pillanatfelvételek különösen hasznosak a vész-helyreállítási eljárással.

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **felhőalapú pillanatfelvételek**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **felhőalapú pillanatfelvételek** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Felhőalapú pillanatfelvételek listájának megtekintéséhez kattintson **felhőalapú pillanatfelvételek** a a **hatókör** ablaktáblán. A pillanatképek minden pillanatkép kapcsolatos információval listája jelenik meg a **eredmények** ablaktáblán.

### <a name="jobs-node"></a>Feladatok csomópont
A **feladatok** csomópont ütemezett, futó, és nemrég befejeződött a biztonsági mentési feladatok adatait tartalmazza. 

* Bontsa ki a csomópontot, kattintson a nyíl ikonra a **feladatok**.
* Rendelkezésre álló műveletek menüjének megjelenítéséhez kattintson a jobb gombbal a **feladatok** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Ütemezett feladatok listájának megtekintéséhez bontsa ki a **feladatok** csomópontra, majd **ütemezett**. A korábban konfigurált feladatok és minden feladat információ listája jelenik meg a **eredmények** ablaktáblán. 
* Nemrég befejeződött feladatok listájának megtekintéséhez bontsa ki a **feladatok** csomópontra, majd **utolsó 24 óra**. Az elmúlt 24 órában elvégzett feladatok listája jelenik meg a **eredmények** ablaktáblán. A **eredmények** ablaktáblán minden befejezett feladat adatait is tartalmazza.
* Az éppen futó feladatok listáját megtekintéséhez bontsa ki a **feladatok** csomópontra, majd **futtató**. A jelenleg futó feladatok és minden feladat információ listája jelenik meg a **eredmények** ablaktáblán.

## <a name="results-pane"></a>Eredmények ablaktábla
A **eredmények** ablaktábla a középső ablaktáblán a StorSimple Snapshot Manager felhasználói felületén. Listák és a kijelölt csomópont részletes állapotinformációkat tartalmaz a **hatókör** ablaktáblán.

### <a name="example"></a>Példa
Az alábbi példa megtekintéséhez kattintson a **kötet csoportok** csomópontja a **hatókör** ablaktáblán. A **eredmények** ablaktáblán jelennek meg minden egyes csoport részleteivel kötet csoportok listája.

![Eredmények ablaktábla](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Beállíthatja, hogy a megjelenő részleteket a **eredmények** ablaktáblában: kattintson a jobb gombbal a **hatókör** ablaktáblán kattintson a **nézet**, és kattintson a **oszlopok hozzáadása/eltávolítása**.

## <a name="actions-pane"></a>Műveletek panel
A **műveletek** ablaktáblán a jobb oldali ablaktáblán, a StorSimple Snapshot Manager felhasználói felületén. A csomópont, a nézet vagy a kiválasztott adatok végezhető műveletek menü tartalmaz a **hatókör** ablaktáblán vagy **eredmények** ablaktáblán. A **műveletek** ablaktábla tartalmaz ugyanazokat a parancsokat, mint a **művelet** menük elérhető elemek a **hatókör** ablaktáblán és **eredmények** ablaktáblán. Minden művelet leírását lásd a táblázatot Itt a **művelet** menü szakasz.

### <a name="examples"></a>Példák
Az alábbi példában látható a **hatókör** ablaktáblában bontsa ki a **feladatok** csomópontot, és kattintson **ütemezett**. A **műveletek** ablaktáblán megjelennek az elérhető műveletek a **ütemezett** csomópont.

![Példa a műveletek ablaktáblán ütemezett feladatok](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

További beállítások megtekintéséhez a a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, kattintson a **ütemezett**, majd egy ütemezett feladatot a a **eredmények** ablaktáblán. A **műveletek** ablaktáblán jelennek meg a rendelkezésre álló műveletek az ütemezett feladat a következő példában látható módon.

![Példa a műveletek ablaktáblán Feladatműveletek](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Billentyűparancsokkal és parancsikonok
StorSimple Snapshot Manager lehetővé teszi, hogy a Windows operációs rendszer és a Microsoft Management Console (MMC) kisegítő lehetőségeit. Néhány billentyűzet navigációs szolgáltatások és arra, hogy adott a StorSimple Snapshot Manager, a következő szakaszokban ismertetett módon is tartalmaz.

* [Navigációs billentyűk](#keyboard-navigation-keys) 
* [A gyorsbillentyűk menüsoron](#menu-bar-shortcut-keys) 
* [Hatókör ablaktábla billentyűparancsok](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigációs billentyűk
A következő táblázat ismerteti a kulcsokhoz, a StorSimple Snapshot Manager felhasználói felület segítségével. 

| Navigációs kulcs | Műveletek |
|:--- |:--- |
| Le nyílbillentyű |A lefelé mutató nyíl kulcs segítségével függőleges áthelyezése a következő elem menü vagy ablaktáblán. |
| Adja meg |Nyomja le az Enter billentyűt a művelet befejeződik, majd folytassa a következő. Például lenyomja az Enter billentyűt **következő**, **OK**, vagy **létrehozása**, és folytassa a következő lépés a varázslóban a. |
| ESC |Nyomja meg az Esc billentyűt menü bezárásához, vagy szakítsa meg, és zárja be a lapot. |
| F1 |Nyomja le az F1 billentyűt a jelenleg aktív ablak súgótémakör megtekintéséhez. |
| F5 |Egy csomópont frissítéséhez nyomja meg az F5 billentyűt. |
| F6 |Nyomja le az F6 billentyűt áthelyezése a **hatókör** ablaktábla a **eredmények** ablaktáblán. |
| F10 |Nyomja le az egérrel a menüsoron Ugrás F10 billentyűt. |
| Bal nyílbillentyű |A bal oldali billentyűvel történő áthelyezéséhez vízszintesen sáv menüpont az előző beállítás. Amikor az előző elemet a menüsoron helyezi át, az előző elemhez tartozó művelet (vagy a környezet) menüben jelenik meg. |
| Jobb nyílbillentyű |A jobbra mutató nyílra billentyűvel áthelyezése vízszintesen egy menüpont sáv a Tovább gombra. A következő elem a menüsoron helyezi át, az új elem művelet (vagy a környezet) menüben jelenik meg. |
| TAB billentyűt |A Tab billentyűvel helyezze át a következő ablaktáblára, a konzolon vagy a következő kijelölés vagy szöveges mezőbe lapon. |
| Fel nyílbillentyű |Az aktuális nyílbillentyűk segítségével Ugrás az előző elem függőleges menü vagy ablaktáblán. |

### <a name="menu-bar-shortcut-keys"></a>A gyorsbillentyűk menüsoron
A következő táblázat ismerteti az egérrel a menüsoron helyi billentyűkombinációk. A gyorsbillentyűk nyomja le az ENTER, és a menü megnyitása után menü billentyűparancsok (az a menü aláhúzott kulcsok) is használhatja. Az egérrel a menüsoron kapcsolatos további információkért látogasson el [menüsáv](#menu-bar).

| Helyi | eredménye | Menü billentyűparancs | eredménye |
|:--- |:--- |:--- |:--- |
| ALT + F |Megnyitja a **fájl** menü. |N |Megnyílik egy új konzolt példányt. |
|  |O |Megnyitja a **felügyeleti eszközök** lap. | |
|  |S |Menti a StorSimple Snapshot Manager konzolon. | |
|  |A |Megnyitja a **Mentés másként** lap. | |
|  |M |Megnyitja a **beépülő modul hozzáadása/eltávolítása** lap. | |
|  |P |Megnyitja a **beállítások** lap. | |
|  |H |Online súgójának megnyitása. | |
| ALT + A |Megnyitja a **művelet** menü. |I |Az importálás megjelenítési beállítás be- és kikapcsolása. |
|  |W |Megnyit egy új StorSimple Snapshot Manager konzolt. | |
|  |F |A StorSimple Snapshot Manager-konzol frissítések. | |
|  |L |Megnyitja a **lista exportálása** lap. | |
|  |H |Online súgójának megnyitása. | |
| ALT + V |Megnyitja a **nézet** menü. |A |Megnyitja a **oszlopok hozzáadása/eltávolítása** lap. |
|  |U |Megnyitja a **nézet testreszabása** lap. | |
| ALT + O |Megnyitja a **Kedvencek** menü. |A |Megnyitja a **hozzáadása a Kedvencekhez** lap. |
|  |O |Megnyitja a **Kedvencek rendezése** lap. | |
| ALT + W |Megnyitja a **ablak** menü. |N |StorSimple Snapshot Manager egy másik ablak megnyitása. |
|  |C# |Nyissa meg a konzolt az összes windows kaszkádolt stílus jeleníti meg. | |
|  |T |A rács minta minden megnyitott konzol windows jeleníti meg. | |
|  |I |A vízszintes sorban a képernyő alján Ikonok elrendezése. | |
| ALT + H |Megnyitja a **súgó** menü. |H |Online súgójának megnyitása. |
|  |T |Megnyitja a Microsoft TechNet technológia Center weblapot. | |
|  |A |Megnyitja a **kapcsolatban a Microsoft Management Console** lap. | |

### <a name="scope-pane-shortcut-keys"></a>Hatókör ablaktábla billentyűparancsok
Az alábbi táblázatok bemutatják a helyi minden egyes csomópontján billentyűkombinációinak a **hatókör** ablaktáblán. 

* [Eszközök csomópont billentyűparancsok](#devices-node-shortcut-keys)
* [Kötetek csomópont billentyűparancsok](#volumes-node-shortcut-keys)
* [Kötet csoportok csomópont billentyűparancsok](#volume-groups-node-shortcut-keys)
* [Biztonsági mentési házirendek csomópont billentyűparancsok](#backup-policies-node-shortcut-keys)
* [Biztonsági mentési katalógus csomópont billentyűparancsok](#backup-catalog-node-shortcut-keys)
* [Feladatok csomópont billentyűparancsok](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Eszközök csomópont billentyűparancsok
| Helyi menü | eredménye |
|:--- |:--- |
| C# |Megnyitja a **eszköz konfigurálása** lap. |
| D |Eszközök és eszközadatok listájának frissítése. |
| V |Megnyitja a **nézet** menü. |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt arra irányul, hogy a **részletek** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyitja a **lista exportálása** lap. |
| H |Online súgójának megnyitása. |

#### <a name="volumes-node-shortcut-keys"></a>Kötetek csomópont billentyűparancsok
| Helyi menü | eredménye |
|:--- |:--- |
| V |A kötetek listájának frissítése. |
| V (nyomja le az ENTER kétszer) |Megnyitja a **nézet** menü. |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt arra irányul, hogy a **kötetek** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyitja a **lista exportálása** lap. |
| H |Online súgójának megnyitása. |

#### <a name="volume-groups-node-shortcut-keys"></a>Kötet csoportok csomópont billentyűparancsok
| Helyi menü | eredménye |
|:--- |:--- |
| G |Megnyitja a **hozzon létre egy kötet csoportot** lap. |
| V |Megnyitja a **nézet** menü. |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt arra irányul, hogy a **kötet csoportok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyitja a **lista exportálása** lap. |
| H |Online súgójának megnyitása. |

#### <a name="backup-policies-node-shortcut-keys"></a>Biztonsági mentési házirendek csomópont billentyűparancsok
| Helyi menü | eredménye |
|:--- |:--- |
| B |Megnyitja a **házirend létrehozása** lap. |
| V |Megnyitja a **nézet** menü. |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt arra irányul, hogy a **kötet csoportok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyitja a ** lista exportálása ** lap. |
| H |Online súgójának megnyitása. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Biztonsági mentési katalógus csomópont billentyűparancsok
| Helyi menü | eredménye |
|:--- |:--- |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt arra irányul, hogy a **kötet csoportok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| H |Online súgójának megnyitása. |

#### <a name="jobs-node-shortcut-keys"></a>Feladatok csomópont billentyűparancsok
| Helyi menü | eredménye |
|:--- |:--- |
| V |Megnyitja a **nézet** menü. |
| W |Megnyit egy új StorSimple Snapshot Manager konzolt arra irányul, hogy a **feladatok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyitja a **lista exportálása** lap. |
| H |Online súgójának megnyitása |

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével csatlakozhat és eszközöket kezelheti a](storsimple-snapshot-manager-manage-devices.md).

