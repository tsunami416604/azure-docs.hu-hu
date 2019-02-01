---
title: A StorSimple Virtual Array biztonsági másolat klónozása |} A Microsoft Docs
description: 'Útmutató: biztonsági másolat klónozása, és a egy fájlt a StorSimple Virtual Array helyreállítása.'
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: feffbb634af62d70a840febcf2a04afb7bdeeddd
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55496790"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klónozza a StorSimple virtuális tömb egy biztonsági másolatból

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti részletesen biztonságimásolat-készletből a részvények vagy a Microsoft Azure StorSimple Virtual Array kötet klónozásához. A klónozott biztonsági másolat segítségével visszaállít egy törölt vagy elveszett fájlt. A cikk emellett tartalmaz részletes lépéseket a fájlkiszolgálóként konfigurált a StorSimple Virtual Array az elemszintű helyreállítás végrehajtásához.

## <a name="clone-shares-from-a-backup-set"></a>Klónozott megosztás biztonságimásolat-készlet

**Mielőtt megosztások klónozza, győződjön meg arról, hogy rendelkezik-e elegendő hely a művelet végrehajtásához az eszközön.** Az egy biztonsági másolatból, klónozásához a [az Azure portal](https://portal.azure.com/), hajtsa végre az alábbi lépéseket.

#### <a name="to-clone-a-share"></a>Megosztás klónozása

1. Keresse meg a **eszközök** panelen. Válassza ki, és válassza ki az eszközt, és kattintson **megosztások**. Válassza ki a megosztást, amelyet szeretne klónozza, kattintson a jobb gombbal a megosztás meghívni a helyi menüből. Válassza ki **Klónozás**.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Az a **Klónozás** panelen kattintson a **biztonsági mentés > válassza** majd tegye a következőket: 
   
   a.    Ezen az eszközön az időtartomány alapján biztonsági szűréséhez. Választhat **elmúlt 7 nap**, **az elmúlt 30 napban**, és **elmúlt év**.
   
   b.    A szűrt biztonsági mentések jelenik meg a listában válassza ki a klónozni kívánt biztonsági másolat.
   
   c.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Az a **Klónozás** panelen kattintson a **cél beállításai** majd tegye a következőket:
   
   a.    Adja meg a fájlmegosztás nevét. A megosztás neve 3 – 127 karaktert tartalmazhat.
   
   b.    Megadhat egy leírást, a klónozott megosztás.
   
   c.    Vissza a megosztás típusa nem módosítható. A többszintű és a egy helyileg rögzített megosztást, a helyileg rögzített rétegzett megosztás klónozták.
   
   d.    A kapacitás, a Klónozás a rendszer a megosztás méretével, megegyező van beállítva.
   
   e.    Rendelje hozzá a rendszergazdák ezen a megosztáson. A fájlkezelő segítségével a megosztás tulajdonságainak módosítása a Klónozás befejeződése után lehet.
   
   f.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Kattintson a **Klónozás** a Klónozás feladat elindításához. A feladat befejezése után a Klónozás elindul, és értesítést kap. A klón előrehaladásának figyeléséhez, nyissa meg a **feladatok** panel és a feladat feladat részleteinek megtekintéséhez kattintson.
5. A Klónozás sikeres létrehozása után lépjen vissza a **megosztások** panel az eszközön.
6. Most már megtekintheti az új klónozott megosztás a megosztások listájában az eszközön. A rétegzett megosztás klónozva van, mivel a rétegzett és a egy helyileg rögzített megosztási helyileg rögzített megosztásként.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Klónozott kötet a biztonságimásolat-készlet

Klónozásához egy biztonsági másolatból, az Azure Portalon, akkor hajtsa végre a hasonló lépéseket, ha egy megosztást a Klónozás. A Klónozás klónozza az azonos virtuális eszközön; egy új kötetet a biztonsági mentés nem klónozható, egy másik eszközre.

#### <a name="to-clone-a-volume"></a>A kötet klónozása

1. Keresse meg a **eszközök** panelen. Válassza ki, és válassza ki az eszközt, és kattintson **kötetek**. Válassza ki a kötetet, amelyet szeretne klónozza, kattintson a jobb gombbal a kötet meghívni a helyi menüből. Válassza ki **Klónozás**.
   
   ![Kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Az a **Klónozás** panelen kattintson a **Backup** majd tegye a következőket: 
   
   a.    Ezen az eszközön az időtartomány alapján biztonsági szűréséhez. Választhat **elmúlt 7 nap**, **az elmúlt 30 napban**, és **elmúlt év**. 
   
   b.    A szűrt biztonsági mentések jelenik meg a listában válassza ki a klónozni kívánt biztonsági másolat.
   
   c.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. Az a **Klónozás** panelen kattintson a **kötetbeállításait cél** majd tegye a következőket::
   
   a. Az eszköz nevét a rendszer automatikusan kitölti.
   
   b. Adja meg a kötet nevét a **kötet klónozása**. A kötet neve 3 – 127 karaktert kell tartalmaznia.
   
   c. A kötet típusának értéke automatikusan az eredeti kötet. A rétegzett kötetek klónozva van, mivel a rétegzett, és a egy helyileg rögzített kötetet, helyileg rögzített.
   
   d. Az a **csatlakoztatott gazdagépek**, kattintson a **kiválasztása**.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Az a **csatlakoztatott gazdagépek** panelen válasszon ki egy meglévő ACR-ből, vagy adjon hozzá egy új ACR. Adjon hozzá egy új ACR, szüksége lesz egy ACR-név és a gazdagép IQN-t. Kattintson a **Kiválasztás** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kattintson a **Klónozás** egy klónozott feladat elindításához.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. A Klónozás feladat létrehozását követően a Klónozás elindul. A klón létrehozása után a kötetek az eszközön panelen jelenik meg. Vegye figyelembe, hogy a rétegzett kötetek klónozva van, mivel a rétegzett és a egy helyileg rögzített kötet gyors helyi kötetté klónozták.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. A kötet online kötetek listájában jelenik meg, ha a kötet érhető el használatra. Az iSCSI-kezdeményező gazdagépen az iSCSI-kezdeményező tulajdonságai ablakban a tárolók listájának frissítéséhez. Klónozott kötet nevét tartalmazó új cél kell megjelennie, "inactive" Állapot oszlop alatt.
8. Jelölje ki a cél, és kattintson a **Connect**. A cél a kezdeményező csatlakoztatása után az állapotra kell módosulnia **csatlakoztatva**.
9. Az a **Lemezkezelés** ablakban a csatlakoztatott kötetek a az alábbi ábrán látható módon jelenik meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.

> [!IMPORTANT]
> Amikor megpróbálja a Klónozás lehetőségét, a kötet vagy a biztonságimásolat-készlet, megosztás, ha a Klónozási művelet sikertelen, cél kötetként vagy megosztásként továbbra is lehet létrehozni a portálon. Fontos, hogy törli a célkötet vagy megosztása a portálon az elemből származó kapcsolatos jövőbeli problémák minimalizálása érdekében.
> 
> 

## <a name="item-level-recovery-ilr"></a>Elemszintű helyreállítás (ILR)

Ebben a kiadásban bevezeti a StorSimple Virtual Array fájlkiszolgálóként konfigurált elemszintű helyreállítást (ILR). A funkció lehetővé teszi, hogy a fájlok és mappák részletes helyreállítási felhő biztonsági másolatából a StorSimple eszközön a megosztásokat. Törölt fájlok a legújabb biztonsági mentések önkiszolgáló modell segítségével kérheti le.

Minden megosztás rendelkezik egy *.backups* mappát, amely tartalmazza a legújabb biztonsági mentések. Keresse meg a kívánt biztonsági mentés, megfelelő fájlok és mappák biztonsági másolatát, és állíthatja vissza őket. Így nem hívások a rendszergazdák számára a fájlok biztonsági másolatokból való visszaállítással.

1. A ILR végrehajtásakor a biztonsági mentések Fájlkezelővel is megtekintheti. Kattintson az adott megosztás, tekintse meg a biztonsági mentés a kívánt. Látni fogja a *.backups* , amely tárolja az összes biztonsági mentés a megosztásban létrehozott mappára. Bontsa ki a *.backups* mappa a biztonsági másolatok megtekintéséhez. A mappa a a teljes biztonsági mentés hierarchia robbantott nézetét mutatja be. Ez a nézet jön létre igény szerinti, és általában eltart csak néhány másodperc alatt létrejön.
   
   Az utolsó öt biztonsági másolatok ilyen módon jelenik meg, és használható az elemszintű helyreállítás végrehajtásához. Az öt legújabb biztonsági mentések ütemezve az alapértelmezett, mind a manuális biztonsági mentések közé tartozik.
   
   * **Ütemezett biztonsági mentések** mivel &lt;eszköznév&gt;DailySchedule-ÉÉÉÉHHNN-HHMMSS-(UTC).
   * **Manuális biztonsági mentések** nevű Ad-hoc-ÉÉÉÉHHNN-HHMMSS – UTC formátumban.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Azonosíthatja a biztonsági mentés, amely tartalmazza a legújabb verzióját a törölt fájlt. A mappa neve tartalmaz egy UTC-időbélyeg minden az előző esetben, ha a, amelyen a mappa létrehozásának ideje a tényleges eszközidőt, amikor a biztonsági mentés használatába. A mappa időbélyeg használatával keresse meg, és azonosíthatja a biztonsági másolatok.

3. Keresse meg a mappát, vagy szeretné visszaállítani a biztonsági mentéshez az előző lépésben azonosított fájl. A megjegyzés csak megtekintheti a fájlokat és mappákat, jogosultság szükséges. Ha egyes fájlok vagy mappák nem férhet hozzá, kérjen meg egy fájlmegosztás rendszergazdát. A rendszergazda a fájlkezelő segítségével szerkesztheti a megosztási engedélyeket, és hozzáférést biztosít az adott fájl vagy mappa. Fontos, hogy-e a megosztás rendszergazda felhasználói csoport egy adott felhasználó helyett ajánlott.

4. A fájl vagy mappa másolja a megfelelő megosztási a StorSimple fájlkiszolgálón.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [felügyelheti a helyi webes felhasználói Felületet a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

