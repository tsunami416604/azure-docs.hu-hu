---
title: "A StorSimple virtuális tömb megosztások kezelése |} Microsoft Docs"
description: "A StorSimple Device Manager és ismerteti a StorSimple virtuális tömb-megosztások kezelése segítségével."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: e5c62689de36baa175001f5f4f70d87568876ef0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>A StorSimple Device Manager szolgáltatással a StorSimple virtuális tömb-megosztások kezelése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti, hogyan a StorSimple Device Manager szolgáltatás létrehozását és kezelését a StorSimple virtuális tömb megosztásokat.

A StorSimple Device Manager szolgáltatás nem egy bővítmény, az Azure portálon, amely lehetővé teszi a StorSimple megoldásban egy egyetlen webes felhasználói felületen keresztüli kezelését. Megosztások és a kötetek kezelése, mellett a StorSimple Device Manager szolgáltatás segítségével megtekintheti és eszközök kezeléséhez, megtekintheti a riasztásokat, biztonsági mentési házirendek kezelése és a biztonságimásolat-katalógus kezelése.

## <a name="share-types"></a>Megosztás típusok

StorSimple megosztások lehet:

* **Helyileg rögzített**: ezek az adatok mindig a tömbben marad, és nem kerülnek a felhőbe.
* **Rétegzett**: ezek az adatok kerülnek a felhőbe is. Amikor létrehoz egy rétegzett megosztást, körülbelül 10 %-a a terület ki van építve a helyi rétegen, és 90 %-tér ki van építve a felhőben. Például ha kiépített egy 1 TB-os megosztást, 100 GB kellene lennie, a helyi terület és 900 GB használni a felhőben során az adatok szinteket. Ez viszont azt jelenti, hogy ha elfogy a helyi terület az eszközön, nem használhatók a rétegzett megosztás (mert a helyi rétegen szükséges 10 % csak akkor érhető el).

### <a name="provisioned-capacity"></a>Kiosztott kapacitást

Tekintse meg a következő táblázat az egyes megosztás maximális kiosztott kapacitást.

| **Korlát azonosítója** | **Korlát** |
| --- | --- |
| A rétegzett megosztás minimális mérete |500 GB |
| A rétegzett megosztás maximális mérete |20 TB |
| Egy helyileg rögzített megosztás minimális mérete |50 GB |
| Egy helyileg rögzített megosztás maximális mérete |2 TB |

## <a name="the-shares-blade"></a>A megosztások panel

A **megosztások** menü a StorSimple szolgáltatás összefoglaló panelen a tároló megosztások megjeleníti egy adott StorSimple-tömb, és kezelheti azokat.

![Megosztások panel](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

A megosztás sorozatából attribútumok:

* **Megosztási név** – egy leíró nevet, amely egyedinek kell lennie, és segít azonosítani a megosztáshoz.
* **Állapot** – lehet online vagy offline állapotú. Ha egy megosztást offline állapotban, a megosztás felhasználók nem fognak tudni azt elérni.
* **Típus** – azt jelzi, hogy a megosztás **rétegzett** (alapértelmezés) vagy **helyileg rögzített**.
* **Kapacitás** – Megadja azt az időt a megosztáson tárolt adatok teljes mennyisége képest használt adatokat.
* **Leírás** – választható beállítás, amely segít a megosztás leírása.
* **Engedélyek** -a megosztásra, a Windows Explorer használatával kezelheti az NTFS-engedélyeket.
* **Biztonsági mentési** – abban az esetben a StorSimple virtuális tömb minden megosztás automatikusan engedélyezve vannak a biztonsági mentéshez.

![Megosztások részletei](./media/storsimple-virtual-array-manage-shares/share-details.png)

Kövesse az utasításokat ebben az oktatóanyagban a következő feladatokat:

* Megosztás hozzáadása
* Egy megosztás módosítása
* Egy kapcsolat nélküli üzemmódra állítása
* Megosztás törlése

## <a name="add-a-share"></a>Megosztás hozzáadása

1. A StorSimple szolgáltatás összefoglaló paneljén kattintson **+ Hozzáadás megosztás** a parancssávon. Ezzel megnyílik a **Hozzáadás megosztás** panelen.

    ![Adja hozzá a megosztás](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Az a **Hozzáadás megosztás** panelen tegye a következőket:
   
    1. Az a **megosztásnevet** mezőben adjon meg egy egyedi nevet a megosztáshoz. A név 3 – 127 karaktert tartalmazó karakterláncnak kell lennie.

    2. Egy nem kötelező **leírás** a megosztáshoz. A leírás segít azonosítani a fájlmegosztás-tulajdonosok.

    3. Az a **típus** legördülő listában, adja meg, hogy hozzon létre egy **rétegzett** vagy **helyileg rögzített** megosztani. Helyi garanciákat, kis késleltetést és magasabb teljesítményt igénylő munkaterheléseknél válasszon **helyileg rögzített megosztás**. Minden más adathoz válasszon **rétegzett** megosztani.

    4. Az a **kapacitás** mezőben adja meg a fájlmegosztás méretét. A rétegzett megosztás 500 GB és 20 TB között kell lennie, és egy helyileg rögzített megosztás 50 GB-os és a 2 TB közé kell esnie.

    5. Az a **értékre alapértelmezett teljes körű engedélyekkel** mezőben, az engedélyek hozzárendelése a felhasználó vagy a csoportot, amelyhez ez a megosztás fér hozzá. Adja meg a felhasználó vagy a felhasználói csoport nevét  _john@contoso.com_  formátumban. Azt javasoljuk, hogy egy felhasználói csoport (helyett egy-egy felhasználóhoz) használatával biztosíthatja a rendszergazdai jogosultsággal a megosztást. Miután hozzárendelt itt az engedélyeket, majd segítségével Fájlkezelőben módosítani ezeket az engedélyeket.
3. Amikor elkészült, a megosztás konfigurálására, kattintson a **létrehozása**. A megosztás jön létre a megadott beállításokat, és megjelenik egy értesítés. Alapértelmezés szerint biztonsági mentés a megosztás engedélyezve lesz.
4. Győződjön meg arról, hogy a fájlmegosztás sikeresen létrejött, keresse fel a **megosztások** panelen. Meg kell jelenniük a felsorolt megosztást.
   
    ![Fájlmegosztás létrehozása sikerült](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Egy megosztás módosítása

Egy megosztás módosítása, ha módosítania kell a megosztás leírása. Nincs más tulajdonságok a megosztás létrehozása után módosítható.

#### <a name="to-modify-a-share"></a>Megosztás módosítása

1. Az a **megosztások** a StorSimple szolgáltatás összefoglaló panelen beállításnál válassza a virtuális tömb kívánja módosíthatja a megosztás helyezkedik el.
2. **Válassza ki** aktuális leírás megtekintése és módosítása a megosztáshoz.
3. A módosítások mentéséhez kattintson a **mentése** parancssávon. A megadott beállítások lesznek alkalmazva, és megjelenik egy értesítés.
   
    ![ Megosztás módosítása](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Egy kapcsolat nélküli üzemmódra állítása

Szükség lehet egy offline állapotba, ha azt tervezi, hogy módosítsa vagy törölje azt. Ha egy megosztást offline állapotban, nincs olvasási és írási hozzáférése érhető el. Szüksége lesz a megosztás nélküli érvénybe az állomáson, valamint az eszközön.

#### <a name="to-take-a-share-offline"></a>A megosztás offline állapotba

1. Győződjön meg arról, hogy a szóban forgó megosztás nem offline állapotba helyezése előtt használatban van.
2. A megosztás végrehajtása a hálózatról a tömb az alábbi lépések elvégzésével:
   
    1. Az a **megosztások** beállítása a StorSimple szolgáltatás összefoglaló panelen, jelölje ki a virtuális tömb, amelyen a megosztás kívánja, hogy a kapcsolat nélküli üzemmódra található.

    2. **Válassza ki** a megosztást, majd kattintson **...**  (felváltva kattintson a jobb gombbal a sorhoz), és válassza ki a helyi menü **offline állapotba**.
     
        ![Kapcsolat nélküli megosztás](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Tekintse át az adatokat a **offline állapotba** panel és elfogadta a műveletet. Kattintson a **offline állapotba** offline állapotba a megosztáshoz. Megjelenik egy értesítés, amely a folyamatban lévő művelet.

    4. Győződjön meg arról, hogy a megosztás sikeresen offline állapotba kerül, keresse fel a **megosztások** panelen. A megosztás állapotát, a kapcsolat nélküli kell megjelennie.

## <a name="delete-a-share"></a>Megosztás törlése

> [!IMPORTANT]
> Törölheti a megosztás csak akkor, ha offline állapotban.


Az alábbi lépésekkel törli a megosztást.

#### <a name="to-delete-a-share"></a>A megosztás törlése

1. Az a **megosztások** a StorSimple szolgáltatás összefoglaló panelen beállításnál válassza a virtuális tömb törli a megosztást helyezkedik el.
2. **Válassza ki** a megosztást, majd kattintson **...**  (felváltva kattintson a jobb gombbal a sorhoz), és válassza ki a helyi menü **törlése**.
   
    ![Megosztás törlése](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Törli a megosztást állapotának ellenőrzéséhez. Ha törli a megosztást nem offline állapotban, offline állapotba először. Kövesse a [offline állapotba a megosztás](#take-a-share-offline).
4. Ha a megerősítést kér a **törlése** panelen fogadja el a megerősítési, majd kattintson **törlése**. Program törli a megosztást és a **megosztások** panel megosztások a virtuális tömbön belüli frissített listáját jeleníti meg.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [klónozza a StorSimple megosztás](storsimple-virtual-array-clone.md).

