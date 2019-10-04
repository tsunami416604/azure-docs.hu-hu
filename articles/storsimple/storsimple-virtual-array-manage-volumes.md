---
title: A StorSimple Virtual Array kötetek kezelése |} A Microsoft Docs
description: A StorSimple-Eszközkezelő ismerteti, és azt ismerteti, hogyan használhatja a StorSimple Virtual Array köteteket kezelésére.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125797"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Kezelése a StorSimple Virtual Array köteteket használata a StorSimple-Eszközkezelő szolgáltatás

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti, hogyan hozhat létre és kezelhet a StorSimple Virtual Array található kötetek a StorSimple-Eszközkezelő szolgáltatás.

A StorSimple-Eszközkezelő szolgáltatásban egy bővítmény az Azure Portalon, amely lehetővé teszi, hogy a StorSimple megoldás egy közös webes felületről kezelheti. Megosztások és kötetek felügyelhető a StorSimple-Eszközkezelő szolgáltatás használatával megtekintése és kezelése az eszközök, megtekintheti a riasztásokat, és megtekintése és kezelése a biztonsági mentési szabályzatok és a biztonságimásolat-katalógust.

## <a name="volume-types"></a>Kötettípusok

A StorSimple-kötetek lehetnek:

* **A helyileg rögzített**: Ezeken a köteteken lévő adatok mindig a tömb bekapcsolva marad, és nem kerülnek a felhőbe.
* **A rétegzett**: Ezeken a köteteken lévő adatok a felhőben is kerülnek. Ha rétegzett kötetet hoz létre, a tárhely körülbelül 10 % a helyi rétegen van kiépítve, és 90 %-a terület a felhőben van kiépítve. Például ha Ön egy 1 TB-os kötetet, 100 GB-os helyi területet lenne tartalmazhat, és a felhőben használni kívánt 900 GB amikor az adat szintet. Ez pedig azt jelenti, hogy ha kifogy a helyi terület az eszközön, nem használhatók a rétegzett kötetek (mivel a helyi rétegen szükséges 10 %-os nem érhető el).

### <a name="provisioned-capacity"></a>Kiosztott kapacitást
Tekintse meg a következő táblázat tartalmazza az egyes mennyiségi maximális kiosztott kapacitást.

| **Korlátazonosító**                                       | **Korlát**     |
|------------------------------------------------------------|---------------|
| A rétegzett kötetek minimális mérete                            | 500 GB        |
| A rétegzett kötetek maximális mérete                            | 5 TB          |
| Helyileg rögzített kötet minimális mérete                    | 50 GB         |
| Egy helyileg rögzített kötet maximális mérete                    | 500 GB        |

## <a name="the-volumes-blade"></a>A kötetek panel
A **kötetek** menü az a StorSimple szolgáltatás összefoglalás panelén tároló kötetek listáját jeleníti meg a megadott StorSimple-tömb, és lehetővé teszi, hogy azok kezelését.

![Kötetek panel](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Egy kötet attribútumainak áll:

* **Kötet neve** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a kötetet.
* **Állapot** – lehet online vagy offline állapotban van. Ha a kötet offline állapotban van, akkor sem, melyek hozzáférhetnek a kötetet használja kezdeményezőktől (kiszolgálóktól) számára látható.
* **Típus** – azt jelzi, hogy a kötet **rétegzett** (alapértelmezés) vagy **helyileg rögzített**.
* **Kapacitás** – Megadja, mint a korábban megszokott a kezdeményező (kiszolgáló) által tárolható adatok teljes mennyisége használt adatok mennyisége.
* **Biztonsági mentési** – abban az esetben a StorSimple Virtual Array az összes kötet automatikusan engedélyezettek a biztonsági mentés.
* **Csatlakoztatott gazdagépek** – Itt adhatja meg, melyek hozzáférhetnek a kötet kezdeményezőktől (kiszolgálóktól).

![Kötetek részletei](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Ebben az oktatóanyagban az utasítások segítségével a következő feladatokat:

* Kötet hozzáadása
* Kötet módosítása
* A kötet offline állapotba helyezése
* Kötet törlése

## <a name="add-a-volume"></a>Kötet hozzáadása

1. A StorSimple szolgáltatás összefoglaló panelén kattintson **+ kötet hozzáadása** a parancssávon. Megnyílik a **kötet hozzáadása** panelen.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Az a **kötet hozzáadása** panelen tegye a következőket:
   
   * Az a **kötet neve** mezőben adjon meg egy egyedi nevet a kötet. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.
   * Az a **típus** legördülő listában, adja meg, hogy hozzon létre egy **rétegzett** vagy **helyileg rögzített** kötet. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterhelésekhez, válassza ki a **helyileg rögzített kötet**. Minden más adathoz válasszon **rétegzett** kötet.
   * Az a **kapacitás** mezőben adja meg a kötet méretét. A rétegzett kötetek 500 GB-os és 5 TB között kell lennie, és a egy helyileg rögzített kötet 50 GB és 500 GB között kell lennie.
   * * Kattintson a **csatlakoztatott gazdagépek**, jelölje be egy hozzáférés-vezérlési rekord (ACR) az iSCSI-kezdeményező csatlakozhat a kötet, és kattintson a kívánt megfelelő **kiválasztása**.
3. Új csatlakoztatott gazdagép hozzáadásához kattintson **új hozzáadása**, adjon meg egy nevet a gazdagép és a hozzá tartozó iSCSI minősített nevét (IQN), és kattintson **Hozzáadás**.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Amikor befejezte a kötethez konfigurálása, kattintson a **létrehozás**. Egy kötet a megadott beállításokkal jön létre, és megjelenik egy értesítés, a sikeres létrehozás azonos. Alapértelmezés szerint engedélyezve lesz biztonsági másolat a kötethez.
5. Győződjön meg arról, hogy a kötet sikeresen létrejött, nyissa meg a **kötetek** panelen. A kötet felsorolt kell megjelennie.
   
    ![Kötet létrehozása sikeres](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Kötet módosítása

A kötet módosításával meg kell változtatnia a gazdagépeket, amelyek elérni a kötetet. Az egyéb attribútumai a kötet nem lehet módosítani, a kötet létrehozása után.

#### <a name="to-modify-a-volume"></a>Kötet módosítása

1. Az a **kötetek** beállítása a StorSimple szolgáltatás összefoglalás panelén, válassza ki a virtuális tömb, amelyen szeretné, hogy módosítsa a köteten található.
2. **Válassza ki** a kötetet, majd kattintson **csatlakoztatott gazdagépek** a jelenleg csatlakoztatott állomás megtekintéséhez és módosításához, egy másik kiszolgálóra.
   
    ![Kötet módosítása](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. A módosítások mentéséhez kattintson a **mentése** parancssávon. A megadott beállítások lesznek alkalmazva, és megjelenik egy értesítés.

## <a name="take-a-volume-offline"></a>A kötet offline állapotba helyezése

Szükség lehet egy kötetet offline állapotba, ha azt tervezi, hogy módosítsa vagy törölje azt. Ha egy kötetet offline állapotban, akkor nem érhető olvasási és írási hozzáférése. Szüksége lesz a kötetet offline állapotrúra érvénybe a gazdagépen, valamint az eszközön.

#### <a name="to-take-a-volume-offline"></a>A kötet offline állapotba

1. Győződjön meg arról, hogy az adott kötet nem használja, offline állapotba helyezése előtt.
2. Állítsa offline állapotba a gazdagépen első. Ezzel elkerülhető, hogy a köteten adatsérülés lehetséges kockázatát. Tekintse meg az utasításokat a gazda operációs rendszer megadott lépéseket.
3. Miután a gazdagép a kötet offline állapotban, végre a kötet offline a tömb a következő lépések végrehajtásával:
   
   * Az a **kötetek** beállítása a StorSimple szolgáltatás összefoglalás panelén, válassza ki a virtuális tömb, amelyen szeretné, hogy offline állapotba állítani a köteten található.
   * **Válassza ki** a kötetet, majd kattintson **...**  (vagy kattintson a jobb gombbal a sor) és a helyi menüből válassza ki a **offline állapotba**.
     
        ![A kötet offline állapotban van](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Tekintse át az adatokat a a **offline állapotba** panelen, majd erősítse meg a művelet az elfogadást. Kattintson a **offline állapotba** a kötet offline állapotba. Megjelenik egy értesítés, amely a folyamatban lévő művelet.
   * Győződjön meg arról, hogy a kötet sikerült offline állapotba kerül, lépjen a **kötetek** panelen. Megtekintheti az állapotát, a kötet offline állapotban van.
     
       ![A kötet offline megerősítése](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> A kötet törölheti csak akkor, ha a kapcsolat nélküli üzemmódban.
> 
> 

A következő lépéseket egy kötet törlése.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. Az a **kötetek** beállítása a StorSimple szolgáltatás összefoglalás panelén, válassza ki a virtuális tömb, amelyen szeretné, hogy törli a köteten található.
2. **Válassza ki** a kötetet, majd kattintson **...**  (vagy kattintson a jobb gombbal a sor) és a helyi menüből válassza ki a **törlése**.
   
    ![Kötet törlése](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. A törölni kívánt kötet állapotának ellenőrzéséhez. Ha törölni szeretné a kötet nem offline állapotban van, offline állapotba először leírt lépések végrehajtásával [egy kötetet offline állapotba](#take-a-volume-offline).
4. Amikor a rendszer megerősítést kér a a **törlése** panelen fogadja el a megerősítési, és kattintson a **törlése**. A kötet most törlődik, és a **kötetek** panelen jelennek meg a virtuális tömbön belüli kötetek frissített listáját.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [StorSimple-kötet klónozása](storsimple-virtual-array-clone.md).

