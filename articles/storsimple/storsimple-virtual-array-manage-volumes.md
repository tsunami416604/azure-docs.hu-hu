---
title: A StorSimple virtuális tömb kötetek kezelése |} Microsoft Docs
description: A StorSimple Device Manager és ismerteti a StorSimple virtuális tömb kötetek kezelése segítségével.
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
ms.openlocfilehash: a507bf1866952cb79fa6334fed80c88cd207cd0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876053"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>StorSimple az Eszközkezelő szolgáltatást a StorSimple virtuális tömbben kötetek kezelése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti, hogyan a StorSimple Device Manager szolgáltatás létrehozását és kezelését a StorSimple virtuális tömb köteteket.

A StorSimple Device Manager szolgáltatás nem egy bővítmény, az Azure portálon, amely lehetővé teszi a StorSimple megoldásban egy egyetlen webes felhasználói felületen keresztüli kezelését. Megosztások és a kötetek kezelése, mellett segítségével a StorSimple Device Manager szolgáltatás és eszközök kezeléséhez, megtekintheti a riasztásokat, és megtekintheti és megtekintéséhez és kezeléséhez biztonsági mentési házirendek a biztonságimásolat-katalógus.

## <a name="volume-types"></a>Kötet típusok

StorSimple-köteteket lehet:

* **Helyileg rögzített**: ezek a kötetek adatainak mindig a tömbben marad, és nem kerülnek a felhőbe.
* **Rétegzett**: ezeken a köteteken lévő adatok kerülnek a felhőbe is. Ha rétegzett kötetet hoz létre, körülbelül 10 %-a a terület a helyi rétegen ki van építve, és 90 %-tér ki van építve a felhőben. Például ha 1 TB-os kötet létesített, 100 GB kellene lennie, a helyi terület és 900 GB használni a felhőben során az adatok szinteket. Ez viszont azt jelenti, hogy ha elfogy a helyi terület az eszközön, nem használhatók a rétegzett kötetek (mert a helyi rétegen szükséges 10 % csak akkor érhető el).

### <a name="provisioned-capacity"></a>Kiosztott kapacitást
Tekintse meg a következő táblázat az egyes mennyiségi maximális kiosztott kapacitást.

| **Korlát azonosítója**                                       | **Korlát**     |
|------------------------------------------------------------|---------------|
| A rétegzett kötetek minimális mérete                            | 500 GB        |
| A rétegzett kötetek maximális mérete                            | 5 TB          |
| Egy helyileg rögzített kötet minimális mérete                    | 50 GB         |
| Egy helyileg rögzített kötet maximális mérete                    | 500 GB        |

## <a name="the-volumes-blade"></a>A kötetek panel
A **kötetek** a StorSimple szolgáltatás összefoglaló panelen menü tároló kötetek listáját jeleníti meg egy adott StorSimple-tömb, és kezelheti azokat.

![Kötetek panel](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

A kötet attribútumainak állnak:

* **Kötet neve** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a kötetet.
* **Állapot** – lehet online vagy offline állapotú. Ha egy kötet offline állapotban, nincs látható kezdeményezőktől (kiszolgálóktól), amelyek hozzáférhetnek a kötetet használja.
* **Típus** – azt jelzi, hogy a kötet **rétegzett** (alapértelmezés) vagy **helyileg rögzített**.
* **Kapacitás** – Megadja azt az időt a kezdeményező (kiszolgáló) tárolt adatok teljes mennyisége képest használt adatokat.
* **Biztonsági mentési** – abban az esetben a StorSimple virtuális tömb összes kötet automatikusan engedélyezve vannak a biztonsági mentéshez.
* **Kapcsolódó állomások** – Itt adhatja meg, amelyek számára engedélyezett a kötet elérésének kezdeményezőktől (kiszolgálóktól).

![Kötetek részletei](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Kövesse az utasításokat ebben az oktatóanyagban a következő feladatokat:

* Kötet hozzáadása
* A kötet módosítása
* A kötet offline állapotba helyezése
* Kötet törlése

## <a name="add-a-volume"></a>Kötet hozzáadása

1. A StorSimple szolgáltatás összefoglaló paneljén kattintson **+ kötet hozzáadása** a parancssávon. Ezzel megnyílik a **kötet hozzáadása** panelen.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Az a **kötet hozzáadása** panelen tegye a következőket:
   
   * Az a **kötetneve** mezőben adjon meg egy egyedi nevet a kötethez. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.
   * Az a **típus** legördülő listában, adja meg, hogy hozzon létre egy **rétegzett** vagy **helyileg rögzített** kötet. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **helyileg rögzített kötet**. Minden más adathoz válasszon **rétegzett** kötet.
   * Az a **kapacitás** mezőben adja meg a kötet méretét. A rétegzett kötetek 500 GB és 5 TB között kell lennie, és egy helyileg rögzített kötet 50 GB-os és 500 GB között kell lennie.
   * * Kattintson a **csatlakozó állomások**, jelölje be egy hozzáférés-vezérlési rekordot (ACR) az iSCSI-kezdeményezőt, amelyet szeretne csatlakozni a kötet, és kattintson a megfelelő **válasszon**.
3. Új csatlakoztatott gazdagép hozzáadásához kattintson **új hozzáadása**, adjon meg egy nevet a gazdagép és az iSCSI minősített nevét (IQN), és kattintson **Hozzáadás**.
   
    ![Kötet hozzáadása](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Amikor elkészült, a kötet konfigurálása, kattintson a **létrehozása**. A kötet a megadott beállításokkal jön létre, és megjelenik egy értesítés azonos sikeres létrehozása. Alapértelmezés szerint biztonsági mentés a kötet engedélyezve lesz.
5. Győződjön meg arról, hogy a kötet sikeresen létrejött, keresse fel a **kötetek** panelen. Meg kell jelenniük a felsorolt kötetet.
   
    ![Kötet létrehozása sikeres](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>A kötet módosítása

A kötet módosításával módosítania kell a gazdagépek, amelyek a kötethez. Egy kötet más attribútumait nem lehet módosítani, ha a kötet létrejött.

#### <a name="to-modify-a-volume"></a>A kötet módosítása

1. Az a **kötetek** beállítása a StorSimple szolgáltatás összefoglaló panelen, jelölje ki a virtuális tömb, amelyen kívánja módosíthatja a köteten található.
2. **Válassza ki** a kötetet, majd kattintson **csatlakozó állomások** a jelenleg csatlakozó állomás megtekintéséhez és módosításához azt egy másik kiszolgálóra.
   
    ![Kötet szerkesztése](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. A módosítások mentéséhez kattintson a **mentése** parancssávon. A megadott beállítások lesznek alkalmazva, és megjelenik egy értesítés.

## <a name="take-a-volume-offline"></a>A kötet offline állapotba helyezése

Szükség lehet egy kötet offline állapotba, ha azt tervezi, hogy módosítsa vagy törölje azt. Ha egy kötet offline állapotban, nincs olvasási és írási hozzáférése érhető el. Szüksége lesz a kötet offline érvénybe az állomáson, valamint az eszközön.

#### <a name="to-take-a-volume-offline"></a>A kötet offline állapotba

1. Győződjön meg arról, hogy az adott kötet nem offline állapotba helyezése előtt használatban van.
2. Igénybe offline állapotba a kötet a gazdagépen első. Ezzel a megoldással a köteten adatsérülés lehetséges kockázatát. Tekintse meg az utasításokat a gazda operációs rendszer megadott lépéseket.
3. Miután a gazdagép a kötet offline állapotban, igénybe a kötet a kapcsolat nélküli tömbben az alábbi lépések elvégzésével:
   
   * Az a **kötetek** beállítása a StorSimple szolgáltatás összefoglaló panelen, jelölje ki a virtuális tömb, amelyen a kívánja, hogy a kapcsolat nélküli üzemmódra köteten található.
   * **Válassza ki** a kötetet, majd kattintson **...**  (felváltva kattintson a jobb gombbal a sorhoz), és válassza ki a helyi menü **offline állapotba**.
     
        ![Kapcsolat nélküli kötet](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Tekintse át az adatokat a **offline állapotba** panel és elfogadta a műveletet. Kattintson a **offline állapotba** a kötet offline állapotba. Megjelenik egy értesítés, amely a folyamatban lévő művelet.
   * Győződjön meg arról, hogy a kötet sikeresen offline állapotba kerül, keresse fel a **kötetek** panelen. A kötet állapotát, a kapcsolat nélküli kell megjelennie.
     
       ![Kapcsolat nélküli kötet megerősítése](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Kötet törlése

> [!IMPORTANT]
> Törölheti a kötet csak akkor, ha offline állapotban.
> 
> 

Az alábbi lépésekkel kötet törlése.

#### <a name="to-delete-a-volume"></a>Kötet törlése

1. Az a **kötetek** beállítása a StorSimple szolgáltatás összefoglaló panelen, jelölje ki a virtuális tömb, amelyiken az, hogy törölni kívánja a köteten található.
2. **Válassza ki** a kötetet, majd kattintson **...**  (felváltva kattintson a jobb gombbal a sorhoz), és válassza ki a helyi menü **törlése**.
   
    ![Kötet törlése](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. A törölni kívánt kötet állapotának ellenőrzéséhez. Ha törli a kötet nem offline állapotban, offline állapotba először lépéseit [kötet offline állapotba](#take-a-volume-offline).
4. Ha a megerősítést kér a **törlése** panelen fogadja el a megerősítési, majd kattintson **törlése**. Program törli a kötet és a **kötetek** panelen megjelenik a virtuális tömbön belüli kötetek frissített listáját.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [klónozza a StorSimple-kötet](storsimple-virtual-array-clone.md).

