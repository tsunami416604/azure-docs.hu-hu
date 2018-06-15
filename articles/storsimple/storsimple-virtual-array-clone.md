---
title: Klónozza a StorSimple virtuális tömb biztonsági |} Microsoft Docs
description: Megtudhatja, hogyan klónozza a biztonsági mentés és a fájl helyreállítása a StorSimple virtuális tömbjét.
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
ms.openlocfilehash: 768c9a1c906999f4690c9c8f7d075743ab1678ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875990"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>A StorSimple virtuális tömb biztonsági másolatából klónozása

## <a name="overview"></a>Áttekintés

A cikk ismerteti részletesen a megosztásokat vagy a Microsoft Azure StorSimple virtuális tömb kötet biztonságimásolat-készletből klónozását. A klónozott biztonsági másolatot a törölt vagy elveszett fájl helyreállításához szolgál. A cikk az elemszintű helyreállítást végrehajtani a StorSimple virtuális tömb fájlkiszolgálóként konfigurált részletes lépéseket is.

## <a name="clone-shares-from-a-backup-set"></a>Klónozás megosztások biztonságimásolat-készletből

**Előtt klónozza a megosztásokat, győződjön meg arról, hogy rendelkezik-e elegendő lemezterület a művelet végrehajtásához az eszközön.** Egy biztonsági másolatból, a Klónozás a [Azure-portálon](https://portal.azure.com/), hajtsa végre az alábbi lépéseket.

#### <a name="to-clone-a-share"></a>A megosztás klónozása

1. Keresse meg a **eszközök** panelen. Válassza ki, és kattintson az eszköz majd **megosztások**. Válassza ki a használni kívánt klónozza, kattintson a jobb gombbal a helyi menü meghívni a megosztás megosztást. Válassza ki **Klónozás**.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. Az a **Klónozás** panelen kattintson **biztonsági mentés > Válassza ki** és tegye a következőket: 
   
   a.    Az eszköz időtartománya alapján biztonsági szűréséhez. Választhat **elmúlt 7 napban**, **az elmúlt 30 napban**, és **múltbeli évre**.
   
   b.    A szűrt biztonsági mentések jelenik meg, jelölje ki a biztonsági mentés, a Klónozás.
   
   c.    Kattintson az **OK** gombra.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. Az a **Klónozás** panelen kattintson a **cél beállításai** és tegye a következőket:
   
   a.    Adja meg a fájlmegosztás nevét. A megosztás neve 3-127 karaktert tartalmazhat.
   
   b.    Megadhat egy leírást a klónozott megosztás.
   
   c.    A megosztás állítja vissza a típusa nem módosítható. A rétegzett megosztás egy rétegzett és egy helyileg rögzített megosztást, a helyileg rögzített klónozták.
   
   d.    A kapacitás értéke megegyezik a megosztást, a rendszer a klónozott méretét.
   
   e.    A rendszergazdák ezen a megosztáson hozzárendelése. Lesz a Klónozás befejezése után a Fájlkezelőben keresztül megosztás tulajdonságainak módosítása.
   
   f.    Kattintson az **OK** gombra.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Kattintson a **Klónozás** a Klónozás feladat elindításához. A feladat befejezése után a Klónozás elindul, és értesítést kap. A Klónozás előrehaladásának figyeléséhez, keresse fel a **feladatok** panel a feladat feladat részleteinek megtekintéséhez kattintson.
5. A Klónozás sikeres létrehozását követően lépjen vissza a **megosztások** panel az eszközön.
6. Az eszközön a megosztások listájában most már megtekintheti az új klónozott megosztást. A rétegzett megosztás klónozták, rétegzett, valamint a helyileg rögzített helyileg rögzített megosztásként.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>A biztonságimásolat-készletből kötetek klónozása

Klónozásához egy biztonsági másolatból, az Azure portálon, hogy a hasonló lépéseket végre, ha egy megosztást a Klónozás. A Klónozás klónozásával készíti a biztonsági mentés egy új kötetre, az adott virtuális eszközön; egy másik eszközre nem klónozhat.

#### <a name="to-clone-a-volume"></a>Kötet klónozása

1. Keresse meg a **eszközök** panelen. Válassza ki, és kattintson az eszköz majd **kötetek**. A kötet, amelyet a klón kiválasztása gombbal a kötetre meghívni a helyi menüből. Válassza ki **Klónozás**.
   
   ![Kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. Az a **Klónozás** panelen kattintson a **biztonsági mentés** és tegye a következőket: 
   
   a.    Az eszköz időtartománya alapján biztonsági szűréséhez. Választhat **elmúlt 7 napban**, **az elmúlt 30 napban**, és **múltbeli évre**. 
   
   b.    A szűrt biztonsági mentések jelenik meg, jelölje ki a biztonsági mentés, a Klónozás.
   
   c.    Kattintson az **OK** gombra.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. A a **Klónozás** panelen kattintson **céloz kötet beállítások** majd hajtsa végre a következő::
   
   a. Az eszköz neve automatikusan feltöltődik értékkel.
   
   b. Kötet nevezze el a **kötet klónozása**. A kötet neve 3 – 127 karaktert kell tartalmaznia.
   
   c. A kötet típusa automatikusan beállítja az eredeti köteten. A rétegzett kötetek klónozták, rétegzett, és egy helyileg rögzített kötetet, helyileg van rögzítve.
   
   d. Az a **csatlakozó állomások**, kattintson a **válasszon**.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. Az a **csatlakozó állomások** panelen válassza ki a egy meglévő ACR, vagy adja hozzá egy új ACR. Adja hozzá egy új ACR, szüksége lesz egy ACR felhasználónév és a gazdagép IQN. Kattintson a **Kiválasztás** gombra.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kattintson a **Klónozás** egy klónozott feladat elindítása.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. A Klónozás feladat létrehozása után a Klónozás elindul. A Klónozás létrehozása után megjelenik a a kötetek panelről az eszközön. Vegye figyelembe, hogy a rétegzett kötetek klónozhatók-e, mivel a rétegzett, és egy helyileg rögzített kötet klónozták egy helyileg rögzített kötet.
   
   ![A biztonsági mentés klónozása](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Ha a kötet megjelenik a kötetek online, lemez használható. Az iSCSI-kezdeményező gazdagép, a frissítés az iSCSI-kezdeményező tulajdonságai ablakban tárolók listája. Egy új cél klónozott kötet nevét tartalmazó, "inactive" állapot oszlopa alatt kell megjelennie.
8. Jelölje ki a cél, és kattintson a **Connect**. Miután a kezdeményező csatlakozik-e a cél, a állapotra kell módosulnia **csatlakoztatva**.
9. Az a **Lemezkezelés** ablakban a csatlakoztatott kötetek a az alábbi ábrán látható módon jelenik meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.

> [!IMPORTANT]
> Egy kötet vagy egy biztonsági másolatból megosztás klónozása közben, ha a Klónozás feladat sikertelen lesz, állítsa a célkötetet, vagy megosztást a portálon is létrejön. Fontos, hogy törli a célkötet, vagy megosztás minimalizálása érdekében ez az elem jövőbeli kérdéseket a portálon.
> 
> 

## <a name="item-level-recovery-ilr"></a>Elemszintű helyreállítás (ILR)

Ebben a kiadásban az elemszintű helyreállítás (ILR) egy StorSimple virtuális tömb fájlkiszolgálóként konfigurált vezet be. A funkció lehetővé teszi a fájlok és mappák részletes helyreállításával próbálkozik a StorSimple eszközön a megosztások felhőalapú biztonsági másolatából. A törölt fájlok kérhetnek le legutóbbi biztonsági mentés egy önkiszolgáló modell használatával.

Minden fájlmegosztás tartozik egy *.backups* a legutóbbi biztonsági másolatokat tartalmazó mappát. Keresse meg a kívánt biztonsági mentés, másolja a kapcsolódó fájlokat és mappákat a biztonsági másolat, és állítsa vissza őket. Így a rendszergazdák hívások nem a fájlok visszaállítása biztonsági másolatból.

1. Az Elemszintű hajt végre, ha a biztonsági mentések Fájlkezelőben keresztül tekintheti meg. Kattintson az adott megosztásra, nézze meg a biztonsági mentését kívánja. Látni fogja a *.backups* mappa alatt a megosztást, a biztonsági másolatokat tároló hozott létre. Bontsa ki a *.backups* mappa a biztonsági mentések megtekintéséhez. A mappa a teljes biztonsági mentés hierarchia robbantott nézetét jeleníti meg. Ez a nézet igény jön létre, és általában a csak egy néhány másodperc létrehozása alatt.
   
   Az utolsó öt biztonsági mentés ily módon jelennek meg, és használható az elemszintű helyreállítás végrehajtásához. Az alapértelmezett ütemezett, mind a manuális biztonsági mentések közé tartoznak az öt legutóbbi biztonsági mentés
   
   * **Ütemezett biztonsági mentések** megnevezett &lt;eszköznév&gt;DailySchedule-ÉÉÉÉHHNN-ÓÓPPMM-UTC.
   * **Manuális biztonsági mentések** nevű Ad-hoc-ÉÉÉÉHHNN-ÓÓPPMM-UTC formátumban.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. A biztonsági mentés a törölt fájl legújabb verzióját tartalmazó azonosításához. Bár a mappa neve minden az előző esetben UTC időbélyeggel tartalmaz, a, ahol a mappa létrehozásának ideje a tényleges eszköz idő, amikor a biztonsági mentés elindítása. A mappa időbélyeg használatával megtalálását és azonosítását a biztonsági mentéseket.

3. Keresse meg a mappa vagy a fájlt, amely elvégzi a visszaállítást a biztonsági mentéshez, amely az előző lépésben azonosított. Csak megtekintheti a fájlokat és mappákat, engedélyekkel rendelkezik a megjegyzés. Ha egyes fájlok és mappák nem férhet hozzá, kérjen meg egy megosztást rendszergazdát. A rendszergazda a fájlkezelő segítségével szerkesztheti a megosztási engedélyeket és hozzáférést biztosít az adott fájl vagy mappa. Ajánlott eljárás, hogy a megosztás rendszergazda-e a felhasználói csoport helyett egy-egy felhasználóhoz.

4. Másolja a megfelelő fájlkiszolgáló megosztott helyén a StorSimple a fájl vagy mappa.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a [felügyelete a StorSimple virtuális tömb, a helyi webes felhasználói felületen](storsimple-ova-web-ui-admin.md).

