---
title: StorSimple virtual array megosztások kezelése | Microsoft dokumentumok
description: Ismerteti a StorSimple Eszközkezelő, és elmagyarázza, hogyan használhatja a storSimple virtuális tömb megosztások kezelésére.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116866"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Megosztások kezelése a StorSimple-eszközkezelő szolgáltatással a StorSimple Virtual Arrayben

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan használhatja a StorSimple Eszközkezelő szolgáltatást a StorSimple virtuális tömb megosztások létrehozásához és kezeléséhez.

A StorSimple Device Manager szolgáltatás egy bővítmény az Azure Portalon, amely lehetővé teszi a StorSimple-megoldás egyetlen webes felületről történő kezelését. A megosztások és a kötetek kezelése mellett a StorSimple Eszközkezelő szolgáltatással megtekintheti és kezelheti az eszközöket, megtekintheti a riasztásokat, kezelheti a biztonsági mentési házirendeket, és kezelheti a biztonsági mentési katalógust.

## <a name="share-types"></a>Megosztástípusok

StorSimple részvények lehetnek:

* **Helyileg rögzített:** Ezekben a megosztásokban lévő adatok mindig a tömbön maradnak, és nem ömlenek ki a felhőbe.
* **Rétegzett:** Az ezekben a megosztásokban lévő adatok kiömlehetnek a felhőbe. Többszintű megosztás létrehozásakor a terület körülbelül 10 %-a a helyi szinten van kiépítve, és a terület 90 %-a a felhőben van kiépítve. Például ha 1 TB-os megosztást létesített, 100 GB-os hely a helyi térben, és 900 GB-ot használna a felhőben, amikor az adatrétegek. Ez viszont azt jelenti, hogy ha elfogy az összes helyi terület az eszközön, nem lehet kiépíteni egy rétegzett megosztás (mert a 10 %-os szükséges a helyi réteg nem lesz elérhető).

### <a name="provisioned-capacity"></a>Kiépített kapacitás

Az egyes megosztástípusok maximális kiosztott kapacitását az alábbi táblázatban tájékírja.

| **Korlátazonosító** | **Korlát** |
| --- | --- |
| A többszintű megosztás minimális mérete |500 GB |
| Többszintű megosztás maximális mérete |20 TB |
| Helyileg rögzített megosztás minimális mérete |50 GB |
| Helyileg rögzített megosztás maximális mérete |2 TB |

## <a name="the-shares-blade"></a>A Részvények panel

A StorSimple szolgáltatás összefoglaló **panelmegosztások** menüje megjeleníti az adott StorSimple tömb tárolási megosztásainak listáját, és lehetővé teszi azok kezelését.

![Részvények penge](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

A megosztás attribútumok sorozatából áll:

* **Megosztási név** – Egyedi nek és a megosztás azonosításának segít.
* **Állapot** – Lehet online vagy offline. Ha egy megosztás offline állapotban van, a megosztás felhasználói nem férhetnek hozzá.
* **Típus** – Azt jelzi, hogy a megosztás **rétegzett** (alapértelmezett) vagy **helyileg rögzített.**
* **Kapacitás** – megadja a használt adatok mennyiségét a megosztáson tárolható adatok teljes mennyiségéhez képest.
* **Leírás** – Nem kötelező beállítás, amely segít a megosztás leírásában.
* **Engedélyek** – A Windows Intézőn keresztül kezelhető megosztás NTFS-engedélyei.
* **Biztonsági mentés** – A StorSimple virtuális tömb esetén az összes megosztás automatikusan engedélyezve van a biztonsági mentéshez.

![Megosztások részletei](./media/storsimple-virtual-array-manage-shares/share-details.png)

Az oktatóanyag utasításaival hajtsa végre a következő feladatokat:

* Megosztás hozzáadása
* Megosztás módosítása
* Megosztás használata kapcsolat nélküli módban
* Megosztás törlése

## <a name="add-a-share"></a>Megosztás hozzáadása

1. A StorSimple szolgáltatás összefoglaló panelen kattintson a **+ Megosztás hozzáadása** a parancssávról elemre. Ez megnyitja a **Megosztás hozzáadása** panelt.

    ![Megosztás hozzáadása](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. A **Megosztás hozzáadása** panelen tegye a következőket:
   
   1. A **Név megosztása** mezőben adjon meg egy egyedi nevet a megosztásnak. A névnek 3–127 karakterből legtartalmazott karakterláncnak kell lennie.

   2. A megosztás nem kötelező **leírása.** A leírás segít azonosítani a részvénytulajdonosokat.

   3. A **Típus** legördülő listában adja meg, hogy **rétegzett** vagy **helyileg rögzített megosztást** szeretne-e létrehozni. A helyi garanciákat, alacsony késéseket és nagyobb teljesítményt igénylő számítási feladatok esetében válassza a **Helyileg rögzített megosztás lehetőséget.** Az összes többi adat esetében válassza **a Rétegzett** megosztás lehetőséget.

   4. A **Kapacitás** mezőben adja meg a megosztás méretét. A rétegzett megosztásnak 500 GB és 20 TB között kell lennie, és a helyileg rögzített megosztásnak 50 GB és 2 TB között kell lennie.

   5. A **Beállítás alapértelmezett teljes engedélyek beállítása** mezőben rendelje hozzá az engedélyeket a felhasználóhoz vagy a megosztást elérő csoporthoz. Adja meg a felhasználó vagy a _john@contoso.com_ felhasználói csoport nevét formátumban. Azt javasoljuk, hogy egy felhasználói csoport (nem pedig egy felhasználó), hogy rendszergazdai jogosultságokat elérni ezeket a megosztásokat. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.
3. Ha befejezte a megosztás konfigurálását, kattintson a **Létrehozás gombra.** A megosztás a megadott beállításokkal jön létre, és megjelenik egy értesítés. Alapértelmezés szerint a biztonsági mentés engedélyezve lesz a megosztáshoz.
4. A megosztás sikeres létrehozásának ellenőrzéséhez lépjen a **Megosztások** panelre. Meg kell jelennie a megosztás felsorolt.
   
    ![Megosztás létrehozása siker](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Megosztás módosítása

Módosítsa a megosztást, ha módosítania kell a megosztás leírását. A megosztás létrehozása után más megosztási tulajdonságok nem módosíthatók.

#### <a name="to-modify-a-share"></a>Megosztás módosítása

1. A StorSimple szolgáltatás összefoglaló **panelmegosztási** beállításából válassza ki azt a virtuális tömböt, amelyen a módosítani kívánt megosztás található.
2. **Válassza ki** a megosztást az aktuális leírás megtekintéséhez és módosításához.
3. Mentse a módosításokat a **Mentés** parancssávra kattintva. A megadott beállítások at alkalmazni, és megjelenik egy értesítést.
   
    ![ Megosztás szerkesztése](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Megosztás használata kapcsolat nélküli módban

Előfordulhat, hogy offline állapotba kell helyeznie egy megosztást, amikor módosítani vagy törölni szeretné. Ha egy megosztás offline állapotban van, nem érhető el írási és írási hozzáféréssel. A megosztást offline állapotba kell helyeznie a gazdagépen és az eszközön is.

#### <a name="to-take-a-share-offline"></a>Megosztás offline állapotba helyezése

1. Mielőtt offline állapotba hozná, győződjön meg arról, hogy a kérdéses rész nincs használatban.
2. A tömb megosztását a következő lépések végrehajtásával vegye igénybe a tömbön:
   
    1. A StorSimple szolgáltatás összefoglaló **panelmegosztási** beállításából válassza ki azt a virtuális tömböt, amelyen az offline kívánt megosztás takar.

    2. **Válassza ki** a megosztást, és kattintson **a ...** gombra (felváltva kattintson a jobb gombbal ebben a sorban), és a helyi menüben válassza a Kapcsolat nélküli **mód kiválasztását**.
     
        ![Kapcsolat nélküli megosztás](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Tekintse át az offline **módba vétel** panelen található információkat, és erősítse meg, hogy elfogadja a műveletet. A megosztás offline állapotba hozásához kattintson a **Kapcsolat nélküli mód** ra. A folyamatban lévő műveletről értesítést fog látni.

    4. Annak ellenőrzéséhez, hogy a megosztás sikeresen offline állapotba került-e, lépjen a **Megosztások** panelre. A megosztás állapotát offline állapotban kell látnia.

## <a name="delete-a-share"></a>Megosztás törlése

> [!IMPORTANT]
> A megosztást csak akkor törölheti, ha az offline állapotban van.


A megosztás törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-a-share"></a>Megosztás törlése

1. A StorSimple szolgáltatás összefoglaló **panelmegosztási** beállításából válassza ki azt a virtuális tömböt, amelyen a törölni kívánt megosztás található.
2. **Jelölje ki** a megosztást, és kattintson **a ...** gombra (felváltva kattintson a jobb gombbal ebben a sorban), és a helyi menüben válassza a **Törlés parancsot.**
   
    ![Megosztás törlése](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Ellenőrizze a törölni kívánt megosztás állapotát. Ha a törölni kívánt megosztás nem offline állapotban van, először tegye offline állapotba. Kövesse a [Megosztás offline állapotba helyezése](#take-a-share-offline)című részben leírt lépéseket.
4. Amikor megerősítést kér a **Törlés** panelen, fogadja el a megerősítést, és kattintson a **Törlés gombra.** A megosztás most törlődik, és a **Megosztások** panelen megjelenik a virtuális tömbben lévő megosztások frissített listája.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [klónozhat egy StorSimple megosztást.](storsimple-virtual-array-clone.md)

