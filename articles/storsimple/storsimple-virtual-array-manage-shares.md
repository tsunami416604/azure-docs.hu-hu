---
title: A StorSimple Virtual Array megosztások kezelése |} A Microsoft Docs
description: A StorSimple-Eszközkezelő ismerteti, és azt ismerteti, hogyan használhatja a StorSimple Virtual Array megosztások kezelésére.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 82a6cdb6c9a39a0d196049a7ba662681ea06b36a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092429"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>A StorSimple Virtual Array-megosztások kezelése a StorSimple-Eszközkezelő szolgáltatás segítségével

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan használhatja a StorSimple-Eszközkezelő szolgáltatás létrehozása és kezelése a StorSimple Virtual Array-megosztások.

A StorSimple-Eszközkezelő szolgáltatásban egy bővítmény az Azure Portalon, amely lehetővé teszi, hogy a StorSimple megoldás egy közös webes felületről kezelheti. Megosztások és kötetek felügyelhető a StorSimple-Eszközkezelő szolgáltatás segítségével megtekintése és kezelése az eszközök, riasztások megtekintése, biztonsági mentési házirendek kezelése és a biztonságimásolat-katalógus kezelése.

## <a name="share-types"></a>Megosztás típusa

A StorSimple-megosztások lehet:

* **A helyileg rögzített**: Ezeken a megosztásokon adatai mindig a tömb bekapcsolva marad, és nem kerülnek a felhőbe.
* **A rétegzett**: Ezek az adatok a felhőben is kerülnek. Rétegzett megosztás létrehozásakor a tárhely körülbelül 10 % a helyi rétegen van kiépítve, és 90 %-a terület a felhőben van kiépítve. Például ha Ön egy 1 TB-os-megosztást, 100 GB-os helyi területet lenne tartalmazhat, és a felhőben használni kívánt 900 GB amikor az adat szintet. Ez pedig azt jelenti, hogy ha kifogy a helyi terület az eszközön, nem használhatók a rétegzett megosztás (mivel a helyi rétegen szükséges 10 %-os nem érhető el).

### <a name="provisioned-capacity"></a>Kiosztott kapacitást

Tekintse meg a következő táblázat tartalmazza az egyes fájlmegosztás maximális kiosztott kapacitást.

| **Korlátazonosító** | **Korlát** |
| --- | --- |
| A rétegzett megosztás legkisebb mérete |500 GB |
| A rétegzett megosztás maximális mérete |20 TB |
| Egy helyileg rögzített megosztási minimális mérete |50 GB |
| Egy helyileg rögzített fájlmegosztás maximális mérete |2 TB |

## <a name="the-shares-blade"></a>A megosztások panel

A **megosztások** menü az a StorSimple szolgáltatás összefoglalás panelén a megadott StorSimple-tömb storage-megosztásokat listáját jeleníti meg, és lehetővé teszi, hogy azok kezelését.

![Megosztások panel](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Egy megosztás attribútumok áll:

* **A névnek a megosztását** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a megosztást.
* **Állapot** – lehet online vagy offline állapotban van. Ha egy megosztást offline állapotban van, a megosztás felhasználók nem fognak tudni azt elérni.
* **Típus** – azt jelzi, hogy a megosztás **rétegzett** (alapértelmezés) vagy **helyileg rögzített**.
* **Kapacitás** – Megadja, mint a korábban megszokott a megosztáson tárolt teljes adatmennyiség használt adatok mennyisége.
* **Leírás** – egy választható beállítás, amely segít a megosztás leírása.
* **Engedélyek** – a megosztás Windows Exploreren keresztül felügyelhető az NTFS-engedéllyel.
* **Biztonsági mentési** – abban az esetben a StorSimple virtuális tömb minden megosztás automatikusan engedélyezettek a biztonsági mentés.

![Megosztások részletei](./media/storsimple-virtual-array-manage-shares/share-details.png)

Ebben az oktatóanyagban az utasítások segítségével a következő feladatokat:

* Megosztás hozzáadása
* Egy megosztás módosítása
* Egy megosztást offline állapotba helyezése
* Megosztás törlése

## <a name="add-a-share"></a>Megosztás hozzáadása

1. A StorSimple szolgáltatás összefoglaló panelén kattintson **+ Hozzáadás megosztás** a parancssávon. Megnyílik a **Hozzáadás megosztás** panelen.

    ![Megosztás hozzáadása](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Az a **Hozzáadás megosztás** panelen tegye a következőket:
   
   1. Az a **megosztásnév** mezőben adjon meg egy egyedi nevet a megosztáshoz. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.

   2. Egy nem kötelező **leírás** a megosztáshoz. A leírás segítségével azonosíthatja a fájlmegosztás-tulajdonosok.

   3. Az a **típus** legördülő listában, adja meg, hogy hozzon létre egy **rétegzett** vagy **helyileg rögzített** megosztani. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterhelésekhez, válassza ki a **megosztás a helyileg rögzített**. Minden más adathoz válasszon **rétegzett** megosztani.

   4. Az a **kapacitás** mezőben adja meg a fájlmegosztás méretét. Rétegzett megosztás 500 GB-os és a 20 TB között kell lennie, és a egy rétegzett megosztás 50 GB-os és a 2 TB között kell lennie.

   5. Az a **teljes körű engedélyekkel alapértelmezett beállítása** mezőben az engedélyek hozzárendelése a felhasználó vagy a csoport, amely a megosztás fér hozzá. Adja meg a felhasználó vagy a felhasználói csoport nevét _john@contoso.com_ formátumban. Azt javasoljuk, hogy egy felhasználói csoportot (és nem egyetlen felhasználó) használatával engedélyezze a rendszergazdai jogosultsággal a ezeket a megosztásokat. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.
3. Amikor végzett a megosztás konfigurálásával, kattintson a **létrehozás**. A megadott beállításokkal jön létre egy megosztást, és megjelenik egy értesítés. Alapértelmezés szerint biztonsági mentés a megosztás engedélyezve lesz.
4. Győződjön meg arról, hogy a fájlmegosztás sikeresen létrejött, nyissa meg a **megosztások** panelen. A felsorolt megosztást kell megjelennie.
   
    ![Fájlmegosztás létrehozása sikeres](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Egy megosztás módosítása

Egy megosztás módosításával meg kell változtatnia a megosztás leírása. Más megosztás tulajdonságai nem módosíthatók, a megosztás létrehozása után.

#### <a name="to-modify-a-share"></a>Megosztás módosítása

1. Az a **megosztások** beállítása a StorSimple szolgáltatás összefoglalás panelén, válassza ki a virtuális tömb, amelyen szeretné, hogy módosítsa a megosztás található.
2. **Válassza ki** aktuális leírás megtekintése és módosítása a megosztást.
3. A módosítások mentéséhez kattintson a **mentése** parancssávon. A megadott beállítások lesznek alkalmazva, és megjelenik egy értesítés.
   
    ![ Megosztás szerkesztése](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Egy megosztást offline állapotba helyezése

Szükség lehet egy megosztást offline állapotba, ha azt tervezi, hogy módosítsa vagy törölje azt. Ha egy megosztást offline állapotban, akkor nem érhető olvasási és írási hozzáférése. Szüksége lesz a megosztás offline módban is a gazdagépen, valamint az eszközön.

#### <a name="to-take-a-share-offline"></a>Egy megosztást offline állapotba

1. Győződjön meg arról, hogy a szóban forgó megosztás nem használja, offline állapotba helyezése előtt.
2. A megosztás offline a tömb végre a következő lépések végrehajtásával:
   
    1. Az a **megosztások** beállítása a StorSimple szolgáltatás összefoglalás panelén, válassza ki a virtuális tömb, amelyen szeretné, hogy offline állapotba állítani a megosztáson található.

    2. **Válassza ki** a megosztást, majd kattintson **...**  (vagy kattintson a jobb gombbal a sor) és a helyi menüből válassza ki a **offline állapotba**.
     
        ![A kapcsolat nélküli megosztás](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Tekintse át az adatokat a a **offline állapotba** panelen, majd erősítse meg a művelet az elfogadást. Kattintson a **offline állapotba** a megosztás offline állapotba. Megjelenik egy értesítés, amely a folyamatban lévő művelet.

    4. Győződjön meg arról, hogy a megosztás sikerült offline állapotba kerül, lépjen a **megosztások** panelen. Megtekintheti az állapotát, a megosztás esetén offline.

## <a name="delete-a-share"></a>Megosztás törlése

> [!IMPORTANT]
> Törölheti a megosztás csak akkor, ha a kapcsolat nélküli üzemmódban.


Hajtsa végre az alábbi lépések végrehajtásával törli a megosztást.

#### <a name="to-delete-a-share"></a>Megosztás törlése

1. Az a **megosztások** beállítása a StorSimple szolgáltatás összefoglalás panelén, válassza ki a virtuális tömb, amelyiken a törölni kívánt megosztás található.
2. **Válassza ki** a megosztást, majd kattintson **...**  (vagy kattintson a jobb gombbal a sor) és a helyi menüből válassza ki a **törlése**.
   
    ![Megosztás törlése](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. A törölni kívánt megosztás állapotának ellenőrzéséhez. Ha a törölni kívánt megosztás nem offline állapotban van, a hálózatról, először. Kövesse a [egy megosztást offline állapotba](#take-a-share-offline).
4. Amikor a rendszer megerősítést kér a a **törlése** panelen fogadja el a megerősítési, és kattintson a **törlése**. A fájlmegosztás mostantól törlődik, és a **megosztások** panel megosztások a virtuális tömbön belüli frissített listáját jeleníti meg.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [egy StorSimple megosztás klónozása](storsimple-virtual-array-clone.md).

