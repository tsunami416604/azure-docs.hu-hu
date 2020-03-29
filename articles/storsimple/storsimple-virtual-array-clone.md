---
title: StorSimple virtual array biztonsági mentés klónozása | Microsoft dokumentumok
description: Ismerje meg, hogyan klónozhat egy biztonsági másolatot, és hogyan állíthatja helyre a storSimple virtuális tömbfájlfájlját.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580856"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>Klónozás a StorSimple virtuális tömb biztonsági másolatából

## <a name="overview"></a>Áttekintés

Ez a cikk részletesen ismerteti, hogyan klónozhat egy biztonsági mentési készletet a megosztások vagy kötetek a Microsoft Azure StorSimple virtuális tömb. A klónozott biztonsági másolat a törölt vagy elveszett fájlok helyreállítására szolgál. A cikk részletes lépéseket is tartalmaz egy elemszintű helyreállítás végrehajtásához a StorSimple virtuális tömb fájlkiszolgálóként konfigurálva.

## <a name="clone-shares-from-a-backup-set"></a>Megosztások klónozása biztonságimásolat-készletből

**Mielőtt megpróbálna klónozni a megosztásokat, győződjön meg arról, hogy elegendő hely áll rendelkezésre az eszközön a művelet végrehajtásához.** Ha egy biztonsági másolatból szeretne klónozni, az [Azure Portalon](https://portal.azure.com/)hajtsa végre a következő lépéseket.

#### <a name="to-clone-a-share"></a>Megosztás klónozása

1. Keresse meg az **Eszközök** panelt. Jelölje ki, kattintson az eszközre, majd a **Megosztások gombra.** Jelölje ki a klónozni kívánt megosztást, kattintson a jobb gombbal a megosztásra a helyi menü meghívásához. Válassza a **Klónozás**lehetőséget.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. A **Klónozás** panelen kattintson a **Biztonsági mentés > kijelölése gombra,** majd tegye a következőket: 
   
   a.    Az eszköz biztonsági másolatának szűrése az időtartomány alapján. Az Elmúlt **7 nap**, **az Elmúlt 30 nap**és az Elmúlt **év**közül választhat.
   
   b.    A megjelenített szűrt biztonsági másolatok listájában válassza ki a klónozni kívánt biztonsági másolatot.
   
   c.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. A **Klónozás** panelen kattintson a **Célbeállítások** elemre, majd tegye a következőket:
   
   a.    Adja meg a megosztás nevét. A megosztási névnek 3-127 karaktert kell tartalmaznia.
   
   b.    Adja meg a klónozott megosztás leírását.
   
   c.    Nem módosíthatja a visszaállítani kívánt megosztás típusát. A rétegzett megosztás oka rétegzett, helyileg rögzített megosztás pedig helyileg rögzítettként van klónozva.
   
   d.    A kapacitás egyenlő a klónozáshoz szükséges megosztás méretével.
   
   e.    Rendelje hozzá a rendszergazdákat ehhez a megosztáshoz. A megosztási tulajdonságokat a Fájlkezelőn keresztül módosíthatja a klónozás befejezése után.
   
   f.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. Klónozási feladat indításához kattintson a **Klónozás** gombra. A feladat befejezése után elindul a klónozási művelet, és értesítést kap. A klónozás folyamatának figyeléséhez lépjen a **Feladatok** panelre, és kattintson a feladatra a feladat részleteinek megtekintéséhez.
5. A klón sikeres létrehozása után lépjen vissza a **Megosztások** panelre az eszközön.
6. Most már megtekintheti az új klónozott megosztást az eszköz megosztásainak listájában. A rétegzett megosztások rétegzett ként klónoznak, a helyileg rögzített megosztás pedig helyileg rögzített megosztásként.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>Kötetek klónozása biztonságimásolat-készletből

Egy biztonsági másolatból klónozni, az Azure Portalon, a megosztás klónozása kor hajtsa végre a hozzá hasonló lépéseket. A klónozási művelet klónozza a biztonsági mentést egy új kötetre ugyanazon a virtuális eszközön; nem klónozhat másik eszközre.

#### <a name="to-clone-a-volume"></a>Kötet klónozása

1. Keresse meg az **Eszközök** panelt. Jelölje ki, kattintson az eszközre, majd a **Kötetek parancsra.** Jelölje ki a klónozni kívánt kötetet, kattintson a jobb gombbal a kötetre a helyi menü meghívásához. Válassza a **Klónozás**lehetőséget.
   
   ![Kötet klónozása](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. A **Klónozás** panelen kattintson a **Biztonsági másolat** gombra, majd tegye a következőket: 
   
   a.    Az eszköz biztonsági másolatának szűrése az időtartomány alapján. Az Elmúlt **7 nap**, **az Elmúlt 30 nap**és az Elmúlt **év**közül választhat. 
   
   b.    A megjelenített szűrt biztonsági másolatok listájában válassza ki a klónozni kívánt biztonsági másolatot.
   
   c.    Kattintson az **OK** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. A **Klónozás** panelen kattintson a **Célhangerő-beállítások** elemre, majd tegye a következőket::
   
   a. Az eszköz neve automatikusan kitöltődik.
   
   b. Adja meg a **klónozott kötet**nevét. A kötet nevének 3–127 karaktert kell tartalmaznia.
   
   c. A kötet típusa automatikusan az eredeti kötetre van beállítva. A rétegzett kötetek többrétegű ként klónozva vannak, és helyileg rögzített kötet et rögzítettként.
   
   d. A **Csatlakoztatott állomások esetében**kattintson a **Kijelölés gombra.**
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. A **Csatlakoztatott állomások** panelen válasszon egy meglévő ACR-ből, vagy adjon hozzá egy új ACR-t. Új ACR hozzáadásához meg kell adnia egy ACR nevet és a gazdaIQN-t. Kattintson a **Kiválasztás** gombra.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. Kattintson **a Klónozás** gombra a klónozási feladat elindításához.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. A klónozási feladat létrehozása után megkezdődik a klónozás. A klón létrehozása után megjelenik a Kötetek panelen az eszközön. Vegye figyelembe, hogy a rétegzett kötetek rétegzett ként klónozva vannak, és a helyileg rögzített kötethelyileg rögzített kötetként klónozva lesz.
   
   ![Biztonsági másolat klónozása](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. Miután a kötet online megjelenik a kötetek listáján, a kötet használható. Az iSCSI-kezdeményező gazdagépen frissítse a célok listáját az iSCSI-kezdeményező tulajdonságai ablakban. A klónozott kötet nevét tartalmazó új célnak "inaktívként" kell megjelennie az állapotoszlopban.
8. Jelölje ki a célt, és kattintson a **Csatlakozás gombra.** Miután a kezdeményező csatlakozott a célhoz, az állapotnak Csatlakoztatott állapotra kell **váltania.**
9. A **Lemezkezelés** ablakban a csatlakoztatott kötetek az alábbi ábrán látható módon jelennek meg. Kattintson a jobb gombbal a felderített kötetre (kattintson a lemez nevére), majd kattintson az **Online** elemre.

> [!IMPORTANT]
> Ha egy kötetet vagy megosztást próbál klónozni egy biztonságimásolat-készletből, ha a klónozási feladat sikertelen, a portálon továbbra is létrejöhet egy célkötet vagy megosztás. Fontos, hogy törölje ezt a célkötetet vagy megosztást a portálon az ebből az elemből eredő jövőbeli problémák minimalizálása érdekében.
> 
> 

## <a name="item-level-recovery-ilr"></a>Elemszintű helyreállítás (ILR)

Ez a kiadás bemutatja az elemszintű helyreállítást (ILR) egy fájlkiszolgálóként konfigurált StorSimple virtuális tömbön. A szolgáltatás lehetővé teszi, hogy részletes helyreállítása fájlok és mappák egy felhő biztonsági másolatot az összes megosztása a StorSimple eszközön. A törölt fájlokat önkiszolgáló modell segítségével lekérheti a legutóbbi biztonsági mentésekből.

Minden megosztáshoz van egy *.backups* mappa, amely a legutóbbi biztonsági másolatokat tartalmazza. A kívánt biztonsági másolathoz navigálhat, átmásolhatja a megfelelő fájlokat és mappákat a biztonsági másolatból, és visszaállíthatja őket. Ez a szolgáltatás kiküszöböli a rendszergazdák hívásait a fájlok biztonsági másolatból történő visszaállításához.

1. Az ILR végrehajtásakor megtekintheti a biztonsági másolatokat a Fájlkezelőn keresztül. Kattintson arra a megosztásra, amelynek biztonsági mentését meg szeretné tekinteni. Az összes biztonsági másolatot tároló megosztás alatt létrehozott *.backups* mappa jelenik meg. Bontsa ki a *.backups mappát* a biztonsági mentések megtekintéséhez. A mappában a teljes biztonsági másolat hierarchia robbantott nézete látható. Ez a nézet igény szerint jön létre, és általában csak néhány másodpercet vesz igénybe a létrehozás.
   
   Az utolsó öt biztonsági másolat ily módon jelenik meg, és egy elemszintű helyreállítás hozható végre. Az öt legutóbbi biztonsági mentések tartalmazzák mind az alapértelmezett ütemezett és a manuális biztonsági mentések.
   
   * **Ütemezett biztonsági mentések,** &gt;amelyeket Eszköznévként &lt;neveznek el DailySchedule-YYYYMMDD-HHMMSS-UTC néven.
   * **Kézi biztonsági mentések** neve Ad-hoc-YYYYMMDD-HHMMSS-UTC.
     
     ![](./media/storsimple-virtual-array-clone/image14.png)

2. Azonosítsa a törölt fájl legújabb verzióját tartalmazó biztonsági másolatot. Bár a mappanév az előző esetekmindegyikében tartalmaz egy UTC-időbélyeget, a mappa létrehozásának időpontja a biztonsági mentés indításakor az eszköz tényleges ideje. A mappa időbélyege segítségével keresse meg és azonosítsa a biztonsági másolatokat.

3. Keresse meg a visszaállítani kívánt mappát vagy fájlt az előző lépésben azonosított biztonsági másolatban. Megjegyzés: csak azokat a fájlokat vagy mappákat tekintheti meg, amelyekhez engedéllyel rendelkezik. Ha bizonyos fájlokhoz vagy mappákhoz nem tud hozzáférni, forduljon egy megosztási rendszergazdához. A rendszergazda a Fájlkezelő segítségével szerkesztheti a megosztási engedélyeket, és hozzáférést biztosíthat az adott fájlhoz vagy mappához. Ajánlott eljárás, hogy a megosztási rendszergazda egyetlen felhasználó helyett felhasználói csoport.

4. Másolja a fájlt vagy a mappát a Megfelelő megosztásra a StorSimple fájlkiszolgálón.

## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről a helyi webes felhasználói felület használatával.](storsimple-ova-web-ui-admin.md)

