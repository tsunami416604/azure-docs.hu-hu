---
title: Kötetek kezelése a StorSimple virtuális tömbön | Microsoft dokumentumok
description: Ismerteti a StorSimple Eszközkezelő, és elmagyarázza, hogyan kell használni a storSimple virtuális tömb kötetek kezelésére.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: a233a9deb58a7c1abc87a622a4f1f2581ee2e477
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62125797"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Kötetek kezelése a StorSimple-eszközkezelő szolgáltatással a StorSimple Virtual Arrayben

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan használhatja a StorSimple Eszközkezelő szolgáltatást kötetek létrehozásához és kezeléséhez a StorSimple virtuális tömbön.

A StorSimple Device Manager szolgáltatás egy bővítmény az Azure Portalon, amely lehetővé teszi a StorSimple-megoldás egyetlen webes felületről történő kezelését. A megosztások és kötetek kezelése mellett a StorSimple Eszközkezelő szolgáltatással megtekintheti és kezelheti az eszközöket, megtekintheti a riasztásokat, valamint megtekintheti és kezelheti a biztonsági mentési házirendeket és a biztonsági mentési katalógust.

## <a name="volume-types"></a>Kötettípusok

A StorSimple kötetek a következők lehetnek:

* **Helyileg rögzített:** Az ezekben a kötetekben lévő adatok mindig a tömbön maradnak, és nem ömlenek ki a felhőbe.
* **Rétegzett:** Az ezekben a kötetekben lévő adatok kiömlehetnek a felhőbe. Többszintű kötet létrehozásakor a terület körülbelül 10 %-a a helyi szinten van kiépítve, és a terület 90 %-a a felhőben van kiépítve. Például ha egy 1 TB-os kötetet, 100 GB-os a helyi térben található, és 900 GB-ot kell használni a felhőben, amikor az adatrétegek. Ez viszont azt jelenti, hogy ha elfogy az összes helyi terület az eszközön, nem lehet kiépíteni egy rétegzett kötet (mert a 10 %-os szükséges a helyi réteg nem lesz elérhető).

### <a name="provisioned-capacity"></a>Kiépített kapacitás
Az egyes kötettípusok maximális kiosztott kapacitását az alábbi táblázatban tájékírja.

| **Korlátazonosító**                                       | **Korlát**     |
|------------------------------------------------------------|---------------|
| A többszintű kötet minimális mérete                            | 500 GB        |
| Többszintű kötet maximális mérete                            | 5 TB          |
| A helyileg rögzített kötet legkisebb mérete                    | 50 GB         |
| Helyileg rögzített kötet maximális mérete                    | 500 GB        |

## <a name="the-volumes-blade"></a>A Kötetek panel
A StorSimple szolgáltatás összefoglaló **panelkötetek** menüje megjeleníti a tárolókötetek listáját egy adott StorSimple tömb, és lehetővé teszi azok kezelését.

![Kötetek panel](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

A kötet attribútumok sorozatából áll:

* **Kötet neve** – Egyedi nek és a kötet azonosításának segít.
* **Állapot** – Lehet online vagy offline. Ha egy kötet offline állapotban van, akkor az nem látható azon kezdeményezői (kiszolgálók) számára, amelyek számára engedélyezett a kötet használata.
* **Típus** – Azt jelzi, hogy a kötet **rétegzett** (alapértelmezett) vagy **helyileg rögzített.**
* **Kapacitás** – megadja a felhasznált adatok mennyiségét a kezdeményező (kiszolgáló) által tárolható teljes adatmennyiséghez képest.
* **Biztonsági mentés** – A StorSimple virtuális tömb esetén az összes kötet automatikusan engedélyezve van a biztonsági mentéshez.
* **Csatlakoztatott állomások** – Megadja azokat a kezdeményezőket (kiszolgálókat), amelyek hozzáférhetnek a kötethez.

![Kötetek részletei](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Az oktatóanyag utasításaival hajtsa végre a következő feladatokat:

* Kötet hozzáadása
* Kötet módosítása
* Kötet kapcsolat nélküli üzemmódba váltása
* Kötet törlése

## <a name="add-a-volume"></a>Kötet hozzáadása

1. A StorSimple szolgáltatás összefoglaló panelen kattintson a **+ Hangerő hozzáadása** a parancssávról lehetőségre. Ezzel megnyitja a **Kötet hozzáadása** panelt.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. A **Kötet hozzáadása** panelen tegye a következőket:
   
   * A **Kötet neve** mezőben adja meg a kötet egyedi nevét. A névnek 3–127 karakterből legtartalmazott karakterláncnak kell lennie.
   * A **Típus** legördülő listában adja meg, hogy **rétegzett** vagy **helyileg rögzített kötetet** szeretne-e létrehozni. A helyi garanciákat, alacsony késéseket és nagyobb teljesítményt igénylő számítási feladatok esetében válassza a **Helyileg rögzített kötet lehetőséget.** Az összes többi adat esetében válassza **a Rétegzett** kötet lehetőséget.
   * A **Kapacitás** mezőben adja meg a kötet méretét. A rétegzett kötetnek 500 GB és 5 TB között kell lennie, és a helyileg rögzített kötetnek 50 GB és 500 GB között kell lennie.
   * * Kattintson **a Csatlakoztatott állomások**elemre, jelöljön ki egy, a kötethez csatlakozni kívánt iSCSI-kezdeményezőnek megfelelő hozzáférés-vezérlési rekordot (ACR), majd kattintson a Kijelölés **gombra.**
3. Új csatlakoztatott állomás hozzáadásához kattintson az **Új hozzáadása**gombra, adja meg az állomás és az iSCSI minősített neve (IQN) nevét, majd kattintson a **Hozzáadás**gombra.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Ha befejezte a kötet konfigurálását, kattintson a **Létrehozás gombra.** A kötet jön létre a megadott beállításokat, és megjelenik egy értesítést a sikeres létrehozása ugyanaz. Alapértelmezés szerint a biztonsági mentés engedélyezve lesz a kötethez.
5. A kötet sikeres létrehozásának ellenőrzéséhez lépjen a **Kötetek** panelre. Meg kell jelennie a listában.
   
    ![A kötet sikeres](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Kötet módosítása

Módosítsa a kötetet, ha módosítania kell a kötetet elérő állomásokat. A kötet többi attribútuma nem módosítható a kötet létrehozása után.

#### <a name="to-modify-a-volume"></a>Kötet módosítása

1. A StorSimple szolgáltatás összefoglaló **panelkötetek** beállításából válassza ki azt a virtuális tömböt, amelyen a módosítani kívánt kötet található.
2. **Jelölje ki** a kötetet, és kattintson a **Csatlakoztatott állomások** elemre az aktuálisan csatlakoztatott állomás megtekintéséhez és egy másik kiszolgálóra való módosításához.
   
    ![Hangerő szerkesztése](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Mentse a módosításokat a **Mentés** parancssávra kattintva. A megadott beállítások at alkalmazni, és megjelenik egy értesítést.

## <a name="take-a-volume-offline"></a>Kötet kapcsolat nélküli üzemmódba váltása

Előfordulhat, hogy egy kötetet offline állapotba kell helyeznie, amikor módosítani vagy törölni szeretné. Ha egy kötet offline állapotban van, nem érhető el írási és írási hozzáféréssel. A kötetet offline állapotba kell helyeznie a gazdagépen és az eszközön is.

#### <a name="to-take-a-volume-offline"></a>Kötet kapcsolat nélküli üzemmódba váltása

1. Mielőtt offline állapotba hozná, győződjön meg arról, hogy a kérdéses kötet nincs használatban.
2. Először a kötetet állítsa offline állapotba a gazdagépen. Ez kiküszöböli a kötet adatsérülésének lehetséges kockázatát. A konkrét lépéseket a gazdaoperációs rendszerre vonatkozó utasításokban találja.
3. Miután az állomáson lévő kötet offline állapotba került, a következő lépések végrehajtásával tegye offline állapotba a kötetet a tömbön:
   
   * A StorSimple szolgáltatás összefoglaló **panelkötetek** beállításából válassza ki azt a virtuális tömböt, amelyen az offline állapotba szeretne helyezni kívánt kötetet.
   * **Jelölje ki** a hangerőt, és kattintson **a ...** (felváltva a jobb gombbal ebben a sorban), és a helyi menüben válassza a Kapcsolat nélküli **mód kiválasztását**.
     
        ![Kapcsolat nélküli kötet](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Tekintse át az offline **módba vétel** panelen található információkat, és erősítse meg, hogy elfogadja a műveletet. A kötet offline állapotba hozásához kattintson a **Kapcsolat nélküli mód** elemre. A folyamatban lévő műveletről értesítést fog látni.
   * Annak ellenőrzéséhez, hogy a kötet offline állapotba került-e, lépjen a **Kötetek** panelre. A kötet állapotát offline állapotban kell látnia.
     
       ![Kapcsolat nélküli kötet megerősítése](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> A kötetet csak akkor törölheti, ha az offline állapotban van.
> 
> 

Kötet törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. A StorSimple szolgáltatás összefoglaló **panelkötetek** beállításából válassza ki azt a virtuális tömböt, amelyen a törölni kívánt kötet található.
2. **Jelölje ki** a hangerőt, és kattintson **a ...** gombra (felváltva kattintson a jobb gombbal ebben a sorban), és a helyi menüben válassza a **Törlés parancsot.**
   
    ![Kötet törlése](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Ellenőrizze a törölni kívánt kötet állapotát. Ha a törölni kívánt kötet nem offline állapotú, először tegye offline állapotba a [Kötet kapcsolat nélküli üzemmódba helyezése](#take-a-volume-offline)című rész lépéseit.
4. Amikor megerősítést kér a **Törlés** panelen, fogadja el a megerősítést, és kattintson a **Törlés gombra.** A kötet most törlődik, és a **Kötetek** panelen megjelenik a virtuális tömbben lévő kötetek frissített listája.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [klónozhat egy StorSimple kötetet.](storsimple-virtual-array-clone.md)

