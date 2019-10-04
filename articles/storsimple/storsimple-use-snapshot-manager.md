---
title: StorSimple Snapshot Manager felhasználói felületén |} A Microsoft Docs
description: A StorSimple Snapshot Manager felhasználói felületét ismerteti és bemutatja, hogyan használható a biztonsági mentési feladatok és a biztonságimásolat-katalógus kezelése.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.custom: ''
ms.openlocfilehash: 46225e5a332e035e4d1cc256e71c4b5d8686fd47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845273"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Felhasználói felület használata a StorSimple Snapshot Manager biztonsági mentési feladatok és a biztonságimásolat-katalógus kezelése

## <a name="overview"></a>Áttekintés
A StorSimple Snapshot Managerrel rendelkezik az intuitív felhasználói felület, amellyel igénybe vehet, és a biztonsági másolatok kezelése. Ez az oktatóanyag bevezetést nyújt a felhasználói felület a, és azt ismerteti, hogyan használhatja az egyes összetevők. Tekintse meg a részletes leírást a StorSimple Snapshot Manager [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konzol leírása
A felhasználói felület megtekintéséhez kattintson a StorSimple Snapshot Manager ikonra az asztalon. A konzolablakban megjelenik, az alábbi ábrán látható módon.

![A StorSimple Snapshot Manager ablaktáblái](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

A konzolablakban öt fő elemmel rendelkezik. Az egyes elemek teljes leírását a megfelelő hivatkozásra kattintva.

* [A menüsávon](#menu-bar) 
* [Az eszköztáron](#tool-bar) 
* [Hatókör panel](#scope-pane) 
* [Eredmények ablaktábla](#results-pane) 
* [Műveletek panel](#actions-pane) 

Ezenkívül a StorSimple Snapshot Manager támogatja-e [billentyűzettel végzett navigáció és számos parancsikont](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konzol kisegítő lehetőségek
A StorSimple Snapshot Manager felhasználói felülete támogatja az akadálymentességi funkciók a Windows operációs rendszer és a Microsoft Management Console (MMC), valamint az egyes a StorSimple Snapshot Manager-specifikus billentyűparancsok által biztosított. 

* A Windows kisegítő lehetőségeket nyújtó szolgáltatásai leírását, Ugrás [billentyűparancsok for Windows](https://support.microsoft.com/kb/126449). 
* Lépjen az MMC akadálymentességi funkciók leírását [kisegítő lehetőségek az MMC 3.0](https://technet.microsoft.com/library/cc766075.aspx)
* A StorSimple Snapshot Manager kisegítő lehetőségeket nyújtó szolgáltatásai leírását, Ugrás [billentyűzettel végzett navigáció és parancsikonok](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>A menüsávon
A menüsorban a konzol ablakának tetején található [fájl](#file-menu), [művelet](#action-menu), [nézet](#view-menu), [Kedvencek](#favorites-menu), [ablak](#window-menu), és [súgó](#help-menu) menün kellene végiglépkednie.

Az elérhető parancsok listájának megtekintéséhez a menüt a menüsávon valamelyik elemre kattintva. A következő példa bemutatja a **nézet** menüt a menüsávon kiválasztott.

![Kijelölt nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Fájl menü
A **fájl** menü Microsoft Management Console (MMC) szokásos parancsokat tartalmaz.

#### <a name="menu-access"></a>Menüpont
Megtekintéséhez a **fájl** menüben kattintson a **fájl** a menüsávon. A következő menü jelenik meg.

![A StorSimple Snapshot Manager fájl menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek a **fájl** menü.

| Menüelem | Leírás |
|:--- |:--- |
| Új |Kattintson a **új** hozhat létre egy új konzol, a StorSimple Snapshot Manager-alapú. |
| Open program |Kattintson a **nyissa meg a** meglévő konzol megnyitásához. |
| Mentés |Kattintson a **mentése** menteni a jelenlegi konzolt. |
| Mentés másként |Kattintson a **Mentés másként** , hozzon létre egy új, átnevezett példányt a jelenlegi konzolt. Használja a **Mentés másként** testreszabása egy nézetben, és mentheti későbbi lekéréséhez. Például létrehozhatja a StorSimple Snapshot Manager beépülő modulok, amelyek az adott kiszolgálóra mutasson. |
| Hozzáadása beépülő modul |Kattintson a **beépülő modul hozzáadása/eltávolítása** hozzáadása vagy eltávolítása a beépülő modulok és a csomópontok rendezése a **hatókör** ablaktáblán. További információért ugorjon [hozzáadása, eltávolítása, és a rendezés beépülő modulok és bővítmények az MMC 3.0](https://technet.microsoft.com/library/cc722035.aspx). |
| Beállítások |Kattintson a **beállítások** szeretné módosítani a konzol ikonjára, adja meg a felhasználói hozzáférés és az engedélyeket, vagy konzol fájltörléssel rendelkezésre álló lemezterület növelése érdekében. |
| Fájl elérési utak listája |Kattintson az újbóli megnyitásához és a egy fájlt, amely a legutóbb megnyitott számozott listájában szereplő útvonalhoz. |
| Kilépés |Kattintson a **kilépési** gombra kattintva zárja be a **fájl** menü. |

### <a name="action-menu"></a>Művelet menü
Használja a **művelet** menüre kattintva kiválaszthatja a rendelkezésre álló műveletek. Adja meg a kiválasztott függenek az Ön számára elérhető elemeket a **hatókör** ablaktábla vagy **eredmények** ablaktáblán.

#### <a name="menu-access"></a>Menüpont
Megtekintéséhez a **művelet** menü, tegye a következők egyikét:

* Kattintson a jobb gombbal egy elemet a **hatókör** ablaktábla vagy **eredmények** ablaktáblán.
* Válasszon ki egy elemet a a **hatókör** ablaktábla vagy **eredmények** ablaktáblán, és kattintson **művelet** a menüsávon. 

Például, ha bejelöli a legfelső szintű csomópontját a **hatókör** ablaktáblán, majd kattintson a jobb gombbal vagy kattintson **művelet** a menüsávon, megjelenik a következő menü.

![A StorSimple Snapshot Manager művelet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

A **műveletek** ablaktáblán (a konzol jobb) tartalmazza a azonos műveleteket, mint a **művelet** menü. Ezenkívül a **műveletek** panel tartalmazza a **nézet** menüpontok, amelyek lehetővé teszik, hogy az egyéni nézet létrehozása a **eredmények** ablaktáblán.

![Nézet menü a Műveletek panel megnyitása](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menü leírása
Az alábbi táblázat a StorSimple Snapshot Manager műveletek betűrendbe szedett listáját tartalmazza. 

* A **művelet** oszlop megjeleníti a csomópontok és az eredmények hajthat végre műveleteket. 
* A **navigációs** oszlop tárgyalja a megfelelő **művelet** menü, hogy kiválaszthatja a műveletet. Egyes műveletek jelennek meg több **művelet** menün kellene végiglépkednie. Ezek a műveletek esetében válasszon ki egy **navigációs** felsorolása lehetőségét. 
* A **leírás** oszlop ismerteti, hogyan lehet az egyes műveletek használatát a **művelet** menüben vagy a műveletek ablaktáblán, és bemutatja, mire használható.

> [!NOTE]
> A **műveletek** ablaktáblán és **művelet** menük tartalmazhat további beállítások, például **nézet**, **itt új ablakban**,  **Frissítés**, **lista exportálása**, és **súgó**. Ezek a lehetőségek érhetők el az MMC részeként, és a nem konkrétan a StorSimple Snapshot Manager. A táblázat leírása az alábbi lehetőségek közül tartalmaz.
> 
> 

| Műveletek | Navigáció | Leírás |
|:--- |:--- |:--- |
| Hitelesítés |Kattintson a **eszközök** csomópontot, és kattintson a jobb gombbal egy eszközt az a **eredmények** ablaktáblán. |Kattintson a **hitelesítés** , az eszköz beállított jelszót. |
| Klónozás |Bontsa ki a **biztonságimásolat-katalógus**, bontsa ki a **Felhőbeli pillanatképek**, egy dátummal ellátott biztonsági mentés gombra, és válassza a kötet a **eredmények** ablaktáblán. |Kattintson a **Klónozás** másolatot készít egy felhőbeli pillanatképet, és azt, hogy az Ön által kijelölt helyen tárolja. |
| Eszközök konfigurálása |Kattintson a jobb gombbal a **eszközök** csomópont. |Kattintson a **eszközök konfigurálása** konfigurálása egy egyetlen vagy több eszközön, a Windows-gazdagéphez való csatlakozáshoz. |
| Biztonsági mentési szabályzat létrehozása |Tegye a következők valamelyikét:<ul><li>Kattintson a jobb gombbal **biztonsági mentési házirendek**.</li><li>Kattintson a gombra, vagy bontsa ki a **kötet csoportok**, és kattintson a jobb gombbal egy kötetcsoport.</li><li>Kattintson a gombra, vagy bontsa ki a **biztonságimásolat-katalógus**, és kattintson a jobb gombbal egy kötetcsoport.</li></ul> |Kattintson a **biztonsági mentési szabályzat létrehozása** kötet csoport ütemezett biztonsági másolatának konfigurálásához. |
| Kötet-csoport létrehozása |Tegye a következők valamelyikét:<ul><li>Kattintson a **kötetek** csomópontot, és kattintson a jobb gombbal a kötet a **eredmények** ablaktáblán.</li><li>Kattintson a jobb gombbal a **kötet csoportok** csomópont.</li></ul> |Kattintson a **kötet-csoport létrehozása** Ha köteteket szeretne hozzárendelni egy kötetcsoport. |
| Törlés |Kattintson egy csomópont- vagy eredmény (Ez az elem megjelenik számos **művelet** menük és **műveletek** ablaktáblái.) |Kattintson a **törlése** törli a node vagy a kiválasztott eredményt. Amikor a megerősítő párbeszédpanelen megjelenik, győződjön meg arról, vagy visszavonja a törlést. |
| Részletek |Kattintson a **eszközök** csomópontot, és kattintson a jobb gombbal egy eszközt az a **eredmények** ablaktáblán. |Kattintson a **részletek** az eszköz konfigurációs részletek megtekintéséhez. |
| Szerkesztés |Kattintson a **biztonsági mentési házirendek**, és kattintson a jobb gombbal a házirend a **eredmények** ablaktáblán. |Kattintson a **szerkesztése** kötet csoport számára a biztonsági mentési ütemezés módosítása. |
| Lista exportálása |Kattintson bármelyik csomópontját vagy eredmény (Ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblái.) |Kattintson a **lista exportálása** listáját egy vesszővel tagolt (CSV) fájl mentéséhez. Egy táblázat alkalmazásba elemzéshez majd importálja ezt a fájlt. |
| Súgó |Kattintson bármelyik csomópontját vagy eredménye. (Ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblái.) |Kattintson a **súgó** egy külön böngészőablakot az online súgó megnyitásához. |
| Itt új ablakban |Kattintson bármelyik csomópontját vagy eredmény (Ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblái.) |Kattintson a **itt új ablakban** egy új StorSimple Snapshot Manager ablak megnyitásához. |
| Frissítés |Kattintson bármelyik csomópontját vagy eredmény (Ez az elem megjelenik az összes **művelet** menük és **műveletek** ablaktáblái.) |Kattintson a **frissítése** frissíteni az aktuálisan megjelenített a StorSimple Snapshot Manager ablak. |
| Eszköz frissítése |Kattintson a **eszközök** csomópontot, és kattintson a jobb gombbal egy eszközt az a **eredmények** ablaktáblán. |Kattintson a **frissítése eszköz** szinkronizálni egy csatlakoztatott eszköz StorSimple Snapshot Manager alkalmazással. |
| Eszközök frissítése |Kattintson a jobb gombbal a **eszközök** csomópont. |Kattintson a **frissítési eszközök** csatlakoztatott eszközök listájának szinkronizálása a StorSimple Snapshot Manager alkalmazással. |
| Kötetek ismételt vizsgálata |Kattintson a jobb gombbal a **kötetek** csomópont. |Kattintson a **ismételt vizsgálat kötetek** megjelenő kötetek listáját a **eredmények** ablaktáblán. |
| Visszaállítás |Bontsa ki a **biztonságimásolat-katalógus**, bontsa ki a egy kötetcsoport, **helyi pillanatképeket** vagy **Felhőbeli pillanatképek**, és kattintson a jobb gombbal a biztonsági mentés. |Kattintson a **visszaállítása** cserélje le az aktuális csoport adatainak a kijelölt biztonsági másolat adataival. |
| Biztonsági mentés |Tegye a következők valamelyikét:<ul><li>Bontsa ki a **kötet csoportok**, és kattintson a jobb gombbal egy kötetcsoport.</li><li>Bontsa ki a **biztonságimásolat-katalógus**, és kattintson a jobb gombbal egy kötetcsoport.</li></ul> |Kattintson a **igénybe biztonsági mentési** azonnal a biztonsági mentési feladat elindul. |
| Váltógomb import megjelenítése |Kattintson a jobb gombbal a legfelső szintű csomópontját a **hatókör** panelen (a **a StorSimple Snapshot Manager** a példákban csomópont). |Kattintson a **váltógomb import megjelenített** megjelenítése vagy elrejtése a kötet csoportokat és az azokhoz kapcsolódó biztonsági mentési, a StorSimple-Eszközkezelő szolgáltatás irányítópultján korábban importált. |

### <a name="view-menu"></a>Nézet menü
Használja a **nézet** menü egyéni nézetének létrehozása a **eredmények** ablak tartalmát. A **nézet** menü tartalmaz **oszlopok hozzáadása és eltávolítása** és **Testreszabás** beállítások.

#### <a name="menu-access"></a>Menüpont
Elérheti a **megtekintése** sávon, vagy a menüjéből a **műveletek** ablaktáblán.

![A StorSimple Snapshot Manager Nézet menü](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek a **nézet** menü.

| Menüelem | Leírás |
|:--- |:--- |
| Oszlopok hozzáadása és eltávolítása |Kattintson a **oszlopok hozzáadása és eltávolítása** hozzáadása vagy eltávolítása oszlopai a **eredmények** ablaktáblán. |
| Testreszabás |Kattintson a **Testreszabás** megjelenítése vagy elrejtése a StorSimple Snapshot Manager-konzol ablakának elemeit. |

### <a name="favorites-menu"></a>Kedvencek menü
Használja a **Kedvencek** menü hozzáadása, eltávolítása és rendezése a lapmegtekintések és a gyakran használt feladatokat. 

#### <a name="menu-access"></a>Menüpont
Elérheti a **Kedvencek** menüt a menüsávon.

![A StorSimple Snapshot Manager Kedvencek menü](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek a **Kedvencek** menü.

| Menüelem | Leírás |
|:--- |:--- |
| Hozzáadás a Kedvencekhez |Kattintson a **Hozzáadás a Kedvencekhez** , az aktuális nézet felvétele a Kedvencek listájához. |
| Kedvencek rendezése |Kattintson a **Kedvencek rendezése** rendszerezheti a Kedvencek mappa tartalmát. |

### <a name="window-menu"></a>Ablak menü
Használja a **ablak** hozzáadása és átrendezheti a StorSimple Snapshot Manager-konzol windows menü.

#### <a name="menu-access"></a>Menüpont
Elérheti a **ablak** menüt a menüsávon.

![A StorSimple Snapshot Manager ablak menü](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

A számozott listát a menü alján látható a jelenleg windows nyissa meg. Kattintson az ablak állapotba hozza az előtérbe a listában lévő bármelyik ablakban. 

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek ablak menüben.

| Menüelem | Leírás |
|:--- |:--- |
| Új ablakban |Kattintson a **új ablakban** , nyisson meg egy új konzolablakot (felül a meglévő ablakban). |
| Lépcsőzetes elrendezés |Kattintson a **Cascade** egymásra épülő stílus a konzol megnyitása ablakok megjelenítése. |
| Egymás alatt |Kattintson a **csempe vízszintesen** megjelenítéséhez a konzol megnyitása a windows egy csempe (vagy a rács) formátumban. |
| Ikonok elrendezése |Ha több konzol windows megnyitásához és az asztal elszórtan minimalizálása érdekében őket, és kattintson a **ikonok elrendezése** rendezze őket egy vízszintes sorban, a képernyő alján. |

### <a name="help-menu"></a>Súgó menü
Használja a **súgó** menüben elérhető online a StorSimple Snapshot Manager és az MMC súgójának megtekintéséhez. A rendszeren jelenleg telepített MMC és a StorSimple Snapshot Manager szoftver verziójával kapcsolatos információkat is megtekintheti. 

Elérheti a **súgó** menüt a menüsávon. A StorSimple Snapshot Manager súgó-témaköröket is elérheti a **műveletek** ablaktáblán.

![A StorSimple Snapshot Manager Súgó menüjében](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menü leírása
A következő táblázat ismerteti a megjelenő elemek a Súgó menüben.

| Menüelem | Leírás |
|:--- |:--- |
| A StorSimple Snapshot Manager súgó |Kattintson a **a StorSimple Snapshot Manager súgó** külön ablakban a StorSimple Snapshot Manager súgó megnyitásához. |
| Súgótémakörök |Kattintson a **Súgó-témaköröket** külön ablakban MMC online súgó megnyitásához. |
| TechCenter webhelyen |Kattintson a **TechCenter webhelyen** , külön ablakban nyissa meg a Microsoft TechNet Tech Center kezdőlapján. |
| Tudnivalók a Microsoft Management Console |Kattintson a **kapcsolatos Microsoft Management Console** megtekintheti, mely a Microsoft Management Console verziója telepítve van a rendszeren. |
| A StorSimple Snapshot Manager névjegye |Kattintson a **információ a StorSimple Snapshot Manager** megtekintéséhez a beépülő modul melyik verziója van telepítve a rendszeren. |

## <a name="tool-bar"></a>Az eszköztáron
Az eszköztár menü alatt található navigációs és feladat-ikonokat tartalmaz. Minden ikon egy adott feladat egy parancsikont.

### <a name="icon-descriptions"></a>Ikon leírása
A következő táblázat ismerteti az ikonok jelennek meg, az eszköztáron. 

| Ikon | Leírás |
|:--- |:--- |
| ![Balra mutató nyíl](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Kattintson a balra mutató nyíl ikonra az előző lapra való visszatéréshez. |
| ![Jobbra mutató nyíl](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Kattintson a jobbra mutató nyílra kattintva nyissa meg a következő lapra (Ha a nyíl szürke, a művelet nem érhető el). |
| ![Ikon mentése](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |A felfelé ikonra kattintva nyissa meg a konzolfán egy szinttel feljebb (a **hatókör** ablaktáblán). |
| ![A konzolfán megjelenítése/elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Kattintson a megjelenítése/elrejtése konzol fa ikonra megjelenítése vagy elrejtése a **hatókör** ablaktáblán. |
| ![Lista exportálása](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Kattintson az Exportálás ikonjára, Ön által megadott CSV-fájlba exportálni. |
| ![Súgó ikonra](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Kattintson a Súgó ikonra nyisson meg egy MMC online súgó-témakörhöz. |
| ![Műveletek ablaktábla megjelenítése/elrejtése](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Kattintson a megjelenítése/elrejtése **műveletek** ablaktábla ikon megjelenítése vagy elrejtése a **műveletek** ablaktáblán. |

## <a name="scope-pane"></a>Hatókör panel
A **hatókör** ablaktábla a StorSimple Snapshot Manager felhasználói felületén a bal szélső ablaktáblán. A konzolon (vagy csomópont) fa tartalmaz, és a fő navigációs mechanizmus a StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Hatókör ablaktábla struktúra
A **hatókör** panelen található tartalmazza kattintható objektumok (csomópontok) fastruktúrában vannak rendezve. 

![Hatókör panel](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Kibontása vagy összecsukása egy csomópontot, kattintson a csomópont neve melletti nyilat ábrázoló ikonra.
* Az állapot vagy a csomópont tartalmát megtekintéséhez kattintson a csomópont nevét. Az információ jelenik meg a **eredmények** ablaktáblán. 

A **hatókör** panelen a következő csomópontokat tartalmazza: 

* [Eszközök](#devices-node) 
* [Kötetek csomópont](#volumes-node) 
* [Kötet-csoportok csomópont](#volume-groups-node) 
* [Biztonsági mentési szabályzatok csomópontban](#backup-policies-node) 
* [Biztonsági mentési Eseménykatalógus csomópontjában](#backup-catalog-node) 
* [Feladatok csomópont](#jobs-node) 

### <a name="scope-pane-tasks"></a>Hatókör ablaktábla feladatok
Használhatja a **hatókör** ablaktáblán egy adott csomóponton művelet végrehajtásához. Válassza ki a feladatot, tegye a következők egyikét:

* Kattintson a jobb gombbal a csomópontra, és válassza ki a feladat lehetőséget a megjelenő menüből.
* Kattintson a csomópontot, majd a **művelet** a menüsávon. A megjelenő menüben válassza ki a feladatot.
* Kattintson a csomópontra, és válassza a művelet a **műveletek** ablaktáblán.

Válasszon ki egy csomópontot, és -feladat megtekintéséhez használja az alábbi módszereket, ha csak az ezen a csomóponton végrehajtott műveletek jelennek meg.

### <a name="devices-node"></a>Eszközök
A **eszközök** csomópont a StorSimple eszköz és a StorSimple virtuális eszközre, amely kapcsolódik a StorSimple Snapshot Manager jelöli. Válassza ki az ehhez a csomóponthoz csatlakozás és eszközök konfigurálása, és a társított köteteket, a kötetek csoportokat és a meglévő biztonsági másolatok importálása. Több eszköz is csatlakoztathatók egy önálló gazdagépre.

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **eszközök**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **eszközök** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Konfigurált eszközök listájának megtekintéséhez kattintson **eszközök** a a **hatókör** ablaktáblán. Eszközök, minden egyes eszköz adatai együtt jelenik meg a **eredmények** ablaktáblán.

### <a name="volumes-node"></a>Kötetek csomópont
A **kötetek** csomópont jelöli, amelyek megfelelnek a meghajtók által a gazdagép, beleértve azokat az iSCSI és a egy eszközön keresztül felderített keresztül felderített csatlakoztatott a kötetekre. Ezt a csomópontot használva megtekintheti az elérhető kötetek listáját és az egyéni kötetek hozzárendelése kötet csoportokhoz.

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **kötetek**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **kötetek** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Kötetek listájának megtekintéséhez kattintson **kötetek** a a **hatókör** ablaktáblán. Kötetek, minden olyan kötetre vonatkozó információval listája jelenik meg a **eredmények** ablaktáblán.

### <a name="volume-groups-node"></a>Kötet-csoportok csomópont
Kötet csoportok olyan más néven konzisztencia csoportok. Minden kötet csoport az alkalmazással kapcsolatos kötetek egy készletet, hogy az alkalmazás a konzisztencia biztosítása a biztonsági mentési műveletek során. Használja a **kötet csoportok** csomópont konfigurálhatja ezeket a csoportokat, és interaktív biztonsági másolatok készítése, vagy hozzon létre biztonsági mentési ütemtervet. 

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **kötet csoportok**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **kötet csoportok** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Kötet csoportok listájának megtekintéséhez kattintson **kötet csoportok** a a **hatókör** ablaktáblán. A kötet csoportok együtt minden kötet csoportra vonatkozó adatok listája jelenik meg a **eredmények** ablaktáblán.

### <a name="backup-policies-node"></a>Biztonsági mentési szabályzatok csomópontban
Biztonsági mentési házirendek nincsenek feladat ütemezése a helyi és felhőbeli pillanatképeket. Használja a **biztonsági mentési szabályzatok** adja meg, milyen gyakran a biztonsági másolat, és mennyi ideig biztonsági mentést a csomópontot meg kell őrizni. 

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **biztonsági mentési házirendek**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **biztonsági mentési házirendek** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* A biztonsági mentési szabályzatok listájában kattintson **biztonsági mentési szabályzatok** a a **hatókör** ablaktáblán. A biztonsági mentési házirendeket, és minden szabályzat adatainak listája jelenik meg a **eredmények** ablaktáblán.

> [!NOTE]
> Legfeljebb 64 biztonsági másolat őrizheti meg.


### <a name="backup-catalog-node"></a>Biztonsági mentési Eseménykatalógus csomópontjában
A **biztonságimásolat-katalógus** a csomópont tartalmazza az Azure StorSimple-kötetek helyszíni és a telephelyen kívüli biztonsági másolatainak listáját. Ez a csomópont kötetcsoport szerint van rendezve, és minden kötettároló csoport a következő helyi pillanatképeivel külön szerkezeteket tartalmaz (a **helyi pillanatkép**s csomópont) és a felhőbeli pillanatképek (a **Felhőbeli pillanatképek** csomópont). Ha ki van bontva, minden kötettároló csoport összes sikeres biztonsági mentés interaktív módon vagy a beállított szabályzat által végrehajtott sorolja fel.

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **biztonságimásolat-katalógus**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **biztonságimásolat-katalógus** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Biztonságimásolat-pillanatképek listájának megtekintéséhez kattintson **biztonságimásolat-katalógus** a a **hatókör** ablaktáblán. A pillanatképek együtt minden pillanatkép információ listája jelenik meg a **eredmények** ablaktáblán.

### <a name="local-snapshots-node"></a>A pillanatképek helyi csomópont
A **helyi pillanatképeket** csomópont helyi pillanatképei számára egy adott kötet csoport sorolja fel. A csomópont alatt található a **biztonságimásolat-katalógus** csomópontja a **hatókör** ablaktáblán. Helyi pillanatképei kötet adatait az Azure StorSimple-eszközön tárolt időponthoz példányban. Általában ilyen típusú biztonsági mentés létrehozott is, és gyorsan vissza. Helyi pillanatképet is használhat, mint egy helyi biztonsági másolat.

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **helyi pillanatképeket**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **helyi pillanatképeket** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* A helyi pillanatképeket listájának megtekintéséhez kattintson **helyi pillanatképeket** a a **hatókör** ablaktáblán. A pillanatképek együtt minden pillanatkép információ listája jelenik meg a **eredmények** ablaktáblán.

### <a name="cloud-snapshots-node"></a>Felhőbeli pillanatképek csomópont
A **Felhőbeli pillanatképek** csomópont megjeleníti egy adott kötet csoport felhőbeli pillanatképeket. A csomópont alatt található a **biztonságimásolat-katalógus** csomópontja a **hatókör** ablaktáblán. Felhőbeli pillanatképek másolatai időponthoz kötet adatait, amely a felhőben vannak tárolva. Felhőbeli pillanatkép megegyezik egy másik, külső helyszínen lévő tárolórendszer replikált pillanatképet. Felhőbeli pillanatképek különösen hasznosak a vész-helyreállítási helyzetekben.

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **Felhőbeli pillanatképek**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **Felhőbeli pillanatképek** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Felhőbeli pillanatképek listájának megtekintéséhez kattintson **Felhőbeli pillanatképek** a a **hatókör** ablaktáblán. A pillanatképek együtt minden pillanatkép információ listája jelenik meg a **eredmények** ablaktáblán.

### <a name="jobs-node"></a>Feladatok csomópont
A **feladatok** csomópont ütemezett, a futó és a nemrégiben befejezett biztonsági mentési feladatok kapcsolatos információkat tartalmaz. 

* Bontsa ki a csomópontot, kattintson a nyíl ikon melletti **feladatok**.
* A menüben elérhető műveletek megtekintéséhez kattintson a jobb gombbal a **feladatok** csomópont, vagy kattintson a jobb gombbal a csomópontokon, hogy a kibontott nézetben jelennek meg.
* Ütemezett feladatok listájának megtekintéséhez bontsa ki a **feladatok** csomópontban, és kattintson **ütemezett**. A korábban konfigurált feladatok és minden feladat információi listája jelenik meg a **eredmények** ablaktáblán. 
* Nemrégiben befejezett feladatok listájának megtekintéséhez bontsa ki a **feladatok** csomópontban, és kattintson **az elmúlt 24 órából**. Már az elmúlt 24 órában befejezett feladatok listája jelenik meg a **eredmények** ablaktáblán. A **eredmények** panelen az egyes befejezett feladat adatait is tartalmazza.
* Egy éppen futó feladatok listájának megtekintéséhez, bontsa ki a **feladatok** csomópontban, és kattintson **futó**. A jelenleg futó feladatok és minden feladat információi listája jelenik meg a **eredmények** ablaktáblán.

## <a name="results-pane"></a>Eredmények ablaktábla
A **eredmények** ablaktábla a középső ablaktáblán a StorSimple Snapshot Manager felhasználói felületén. Listák és állapotával kapcsolatos részletes információk a kiválasztott csomópontot tartalmaz az **hatókör** ablaktáblán.

### <a name="example"></a>Példa
Az alábbi példában látható, kattintson a **kötet csoportok** csomópontja a **hatókör** ablaktáblán. A **eredmények** ablaktábla megjeleníti az egyes csoportok adatait kötet csoportok listája.

![Eredmények ablaktábla](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Beállíthatja, hogy a megjelenő részleteket a **eredmények** ablaktáblán: kattintson a jobb gombbal a **hatókör** ablaktáblán kattintson a **nézet**, és kattintson a **oszlopok hozzáadása és eltávolítása** .

## <a name="actions-pane"></a>Műveletek panel
A **műveletek** ablaktáblát a jobb oldali ablaktáblán a StorSimple Snapshot Manager felhasználói felületén is. A csomóponton, a nézet vagy a kiválasztott adatok elvégezhető műveletek menü tartalmaz a **hatókör** ablaktábla vagy **eredmények** ablaktáblán. A **műveletek** ablaktábla, ugyanazokat a parancsokat tartalmaz a **művelet** eleme elérhető menüket a **hatókör** ablaktáblán és **eredmények** panel. Minden művelet leírását lásd a táblázatot Itt a **művelet** menü szakaszban.

### <a name="examples"></a>Példák
A következő példában látható a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, és válassza **ütemezett**. A **műveletek** ablaktáblán megjelennek azok a rendelkezésre álló műveletek esetében a **ütemezett** csomópont.

![Példa a műveletek ablaktáblán ütemezett feladatok](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

További beállítások, a megjelenítéséhez a **hatókör** ablaktáblán bontsa ki a **feladatok** csomópontot, kattintson a **ütemezett**, majd egy ütemezett feladatot a a **eredmények** panel. A **műveletek** ablaktábla megjeleníti a rendelkezésre álló műveletek az ütemezett feladat a következő példában látható módon.

![Példa a műveletek ablaktáblán Feladatműveletek](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Billentyűzetnavigációt és billentyűparancsok
A StorSimple Snapshot Manager lehetővé teszi, hogy az akadálymentességi funkciók a Windows operációs rendszer és a Microsoft Management Console (MMC). Bizonyos billentyűzetet funkcióinak és arra, hogy adott a StorSimple Snapshot Manager, a következő szakaszokban ismertetett módon is tartalmaz.

* [Navigációs billentyűk](#keyboard-navigation-keys) 
* [A gyorsbillentyűk menüsoron](#menu-bar-shortcut-keys) 
* [Hatókör ablaktábla billentyűparancsok](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Navigációs billentyűk
A következő táblázat ismerteti a kulcsokat, amely a StorSimple Snapshot Manager felhasználói felület segítségével. 

| Navigációs kulcs | Műveletek |
|:--- |:--- |
| Lefelé nyíl |Használja a le nyílbillentyűvel függőleges áthelyezése a következő elem menü vagy ablaktáblán. |
| Enter |Nyomja le az Enter billentyűt a művelet befejeződik, és folytathatja a következő lépéssel. Például lenyomja az Enter billentyűt **tovább**, **OK**, vagy **létrehozás**, és folytassa a varázsló következő lépésére. |
| Esc |Nyomja le az Esc billentyűt a menü bezárásához, vagy szakítsa meg és a egy oldal bezárásához. |
| F1 |Nyomja le az F1 billentyűt a jelenleg aktív ablak egy Súgó-témakör megtekintése. |
| F5 |Nyomja le az F5 billentyűt a csomópont frissítése. |
| F6 |Nyomja le az F6 billentyűt válthatnak a **hatókör** ablaktábla a **eredmények** ablaktáblán. |
| F10 |A F10 billentyűvel nyissa meg a menüsávon. |
| Bal nyílbillentyű |A bal oldali billentyűvel történő áthelyezéséhez vízszintesen sáv menüpont az előző beállítás. Amikor az előző elemmel át a menüsávon, a művelet (környezet) menüt az előző elem jelenik meg. |
| Jobb nyílbillentyű |A jobbra mutató nyílra kulcsa segítségével vízszintesen át az egyik sáv. menüelem a Tovább gombra. A következő elem áthelyezése a menüsávon, a művelet (környezet) menüt az új elem jelenik meg. |
| TAB billentyű |A Tab billentyű használatával helyezze át a következő panelen a konzolon vagy a következő kiválasztása vagy a beviteli mezőben lapon. |
| Fel nyílbillentyű |A felfelé mutató nyíl kulcs használatával Ugrás az előző elemmel függőleges menü vagy ablaktáblán. |

### <a name="menu-bar-shortcut-keys"></a>A gyorsbillentyűk menüsoron
A következő táblázat ismerteti a billentyűparancsokat a menüsávon. Után lenyomja a billentyűparancsok, és megnyílik a menü, használhatja a menüben a gyorsbillentyűk (az aláhúzott kulcsok a menüben). A menüsávon kapcsolatos további információkért látogasson el [menüsoron](#menu-bar).

| Helyi | Eredmény | Billentyűparancs menü | Eredmény |
|:--- |:--- |:--- |:--- |
| ALT+F |Megnyílik a **fájl** menü. |N |Megnyílik egy új konzol-példányt. |
|  |O |Megnyílik a **felügyeleti eszközök** lapot. | |
|  |S |A StorSimple Snapshot Manager-konzol menti. | |
|  |A |Megnyílik a **Mentés másként** lapot. | |
|  |M |Megnyílik a **beépülő modul hozzáadása/eltávolítása** lapot. | |
|  |P |Megnyílik a **beállítások** lapot. | |
|  |H |Online súgó megnyitása | |
| ALT + A |Megnyílik a **művelet** menü. |I |Az importálás megjelenítési beállítás be- és kikapcsolása. |
|  |W |Megnyílik egy új StorSimple Snapshot Manager-konzolt. | |
|  |F |A StorSimple Snapshot Manager-konzol frissítések. | |
|  |L |Megnyílik a **lista exportálása** lapot. | |
|  |H |Online súgó megnyitása | |
| ALT+V |Megnyílik a **nézet** menü. |A |Megnyílik a **oszlopok hozzáadása és eltávolítása** lapot. |
|  |U |Megnyílik a **nézet testreszabása** lapot. | |
| ALT + O |Megnyílik a **Kedvencek** menü. |A |Megnyílik a **Hozzáadás a Kedvencekhez** lapot. |
|  |O |Megnyílik a **Kedvencek rendezése** lapot. | |
| ALT + W |Megnyílik a **ablak** menü. |N |A StorSimple Snapshot Manager egy másik ablak megnyitása. |
|  |C |Minden megnyitott konzolablakok egymásra épülő stílus jeleníti meg. | |
|  |T |Minden megnyitott konzolablakok rács mintát jeleníti meg. | |
|  |I |Egy vízszintes sorban, a képernyő alján Ikonok elrendezése. | |
| ALT + H |Megnyílik a **súgó** menü. |H |Online súgó megnyitása |
|  |T |A Microsoft TechNet Tech Center weboldal nyílik meg. | |
|  |A |Megnyílik a **kapcsolatos Microsoft Management Console** lapot. | |

### <a name="scope-pane-shortcut-keys"></a>Hatókör ablaktábla billentyűparancsok
Az alábbi táblázatok bemutatják a helyi billentyűkombinációk minden egyes csomópontjához a **hatókör** ablaktáblán. 

* [Eszközök csomópont billentyűparancsok](#devices-node-shortcut-keys)
* [Kötetek csomópontra billentyűparancsok](#volumes-node-shortcut-keys)
* [Kötet-csoportok csomópont billentyűparancsok](#volume-groups-node-shortcut-keys)
* [Biztonsági mentési szabályzatok csomópont billentyűparancsok](#backup-policies-node-shortcut-keys)
* [Biztonsági mentési katalógus csomópont billentyűparancsok](#backup-catalog-node-shortcut-keys)
* [Feladatok csomópont billentyűparancsok](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Eszközök csomópont billentyűparancsok
| Helyi menü | Eredmény |
|:--- |:--- |
| C |Megnyílik a **eszközök konfigurálása** lapot. |
| D |Eszközök és eszközadatok listájának frissítése. |
| V |Megnyílik a **nézet** menü. |
| W |Megnyílik egy új StorSimple Snapshot Manager-konzolt összpontosít a **részletek** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyílik a **lista exportálása** lapot. |
| H |Online súgó megnyitása |

#### <a name="volumes-node-shortcut-keys"></a>Kötetek csomópontra billentyűparancsok
| Helyi menü | Eredmény |
|:--- |:--- |
| V |Frissíti a kötetek listáját. |
| V (kétszer lenyomás) |Megnyílik a **nézet** menü. |
| W |Megnyílik egy új StorSimple Snapshot Manager-konzolt összpontosít a **kötetek** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyílik a **lista exportálása** lapot. |
| H |Online súgó megnyitása |

#### <a name="volume-groups-node-shortcut-keys"></a>Kötet-csoportok csomópont billentyűparancsok
| Helyi menü | Eredmény |
|:--- |:--- |
| G |Megnyílik a **hozzon létre egy Kötetcsoport** lapot. |
| V |Megnyílik a **nézet** menü. |
| W |Megnyílik egy új StorSimple Snapshot Manager-konzolt összpontosít a **kötet csoportok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyílik a **lista exportálása** lapot. |
| H |Online súgó megnyitása |

#### <a name="backup-policies-node-shortcut-keys"></a>Biztonsági mentési szabályzatok csomópont billentyűparancsok
| Helyi menü | Eredmény |
|:--- |:--- |
| B |Megnyílik a **hozzon létre egy házirendet** lapot. |
| V |Megnyílik a **nézet** menü. |
| W |Megnyílik egy új StorSimple Snapshot Manager-konzolt összpontosít a **kötet csoportok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyílik a ** lista exportálása ** lapon. |
| H |Online súgó megnyitása |

#### <a name="backup-catalog-node-shortcut-keys"></a>Biztonsági mentési katalógus csomópont billentyűparancsok
| Helyi menü | Eredmény |
|:--- |:--- |
| W |Megnyílik egy új StorSimple Snapshot Manager-konzolt összpontosít a **kötet csoportok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| H |Online súgó megnyitása |

#### <a name="jobs-node-shortcut-keys"></a>Feladatok csomópont billentyűparancsok
| Helyi menü | Eredmény |
|:--- |:--- |
| V |Megnyílik a **nézet** menü. |
| W |Megnyílik egy új StorSimple Snapshot Manager-konzolt összpontosít a **feladatok** csomópont. |
| F |A StorSimple Snapshot Manager-konzol frissítések. |
| L |Megnyílik a **lista exportálása** lapot. |
| H |Online súgó megnyitása |

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [a StorSimple Snapshot Manager használatával csatlakozhat, és eszközök kezelése](storsimple-snapshot-manager-manage-devices.md).

