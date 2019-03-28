---
title: Az Azure Data Box Edge fájlmegosztás-kezelést |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure Data Box Edge-megosztások kezelése az Azure portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: e85e006a54fcb4bb677932b3e1ff9fa79352dba9
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519833"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Az Azure Data Box Edge-megosztások kezelése az Azure portal használatával

Ez a cikk ismerteti, hogyan kezelheti az Azure Data Box Edge-megosztások. Az Azure Data Box Edge az Azure Portalon kezelheti, vagy keresztül a helyi webes felhasználói Felületét. Az Azure portal használatával adja hozzá, törlés, frissítés, megosztások vagy a megosztások társított tárfiók tárolási kulcs szinkronizálása.

## <a name="about-shares"></a>Információk a megosztásokról

Adatok átviteléhez az Azure-ba, szüksége az Azure Data Box Edge megosztások létrehozásához. A megosztásokat, adja hozzá a Data Box Edge-eszközön helyi megosztásokból vagy -megosztásokkal, az adatok felhőbe küldése is lehet.

 - **Helyi megosztásokból**: Használja ezeket a megosztásokat, ha azt szeretné, hogy helyileg az eszközön feldolgozni az adatokat.
 - **Megosztások**: Ha azt szeretné, hogy az eszközön lévő adatokat lehet leküldeni a felhőben a storage-fiók automatikusan, használja ezeket a megosztásokat. Felhőszolgáltatásokkal függvények, mint például **frissítése** és **tárfiókkulcsok szinkronizálása** a megosztásokat a alkalmazni.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Megosztás törlése
> * Megosztások frissítése
> * Tárkulcs szinkronizálása


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő lépéseket az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **átjáró > megosztások**. Válassza ki **+ Hozzáadás megosztás** a parancssávon.

    ![Válassza a Megosztás hozzáadása](media/data-box-edge-manage-shares/add-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válassza ki a megosztás **típusát**. A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

4. Adja meg azt a **tárfiókot**, ahol a megosztás található. A rendszer a tárfiókban létrehoz egy tárolót a megosztás nevével, ha még nem létezik ilyen. Ha létezik, akkor a rendszer a meglévő tárolót használja.

5. A legördülő listából válassza ki a **társzolgáltatás** blokkblob, lapblob vagy fájlokat. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Például ez esetben az adatokat az Azure-ban blokkblobként tárolni kívánt, ezért kiválasztjuk **Blokkblob**. Ha a **Lapblob**, gondoskodnia kell arról, hogy az adatok igazítva 512 bájt. Használat **lapblob** VHD vagy VHDX, amelyek mindig igazítva 512 bájt.

6. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
    - **SMB-megosztás létrehozásakor** – A **Teljes körű jogosultsággal rendelkező helyi felhasználó** mezőben válasszon az **Új létrehozása** és a **Meglévő használata** lehetőségek közül. Ha új helyi felhasználót hoz létre, adja meg a **felhasználónevet** és a **jelszót**, majd erősítse meg a jelszót. Ezzel hozzárendeli az engedélyeket a helyi felhasználóhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.

        ![SMB-megosztás hozzáadása](media/data-box-edge-manage-shares/add-smb-share.png)

        Ha a megosztott adatokhoz bejelöli a Csak az olvasási műveletek engedélyezése lehetőséget, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
    - **NFS-megosztás létrehozásakor** – Meg kell adnia azon **engedélyezett ügyfelek IP-címeit**, akik hozzáférhetnek a megosztáshoz.

        ![NFS-megosztás hozzáadása](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Az Edge-modulok számítási egyszerűen éri el a megosztásokat, használja a helyi csatlakoztatási pont. Válassza ki **megosztás használatára az Edge számítási** úgy, hogy a megosztás létrehozása után automatikusan van csatlakoztatva. Ha ezt a lehetőséget választja, az Edge-modul is használhatja a számítási a helyi csatlakoztatási ponttal.

8. A megosztás létrehozásához kattintson a **Létrehozás** parancsra. A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást.

## <a name="add-a-local-share"></a>Egy helyi megosztás hozzáadása

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **átjáró > megosztások**. Válassza ki **+ Hozzáadás megosztás** a parancssávon.

    ![Válassza a Megosztás hozzáadása](media/data-box-edge-manage-shares/add-local-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válassza ki a megosztás **típusát**. A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

4. Az Edge-modulok számítási egyszerűen éri el a megosztásokat, használja a helyi csatlakoztatási pont. Válassza ki **megosztás használatára az Edge számítási** , hogy az Edge-modul a számítási használhatja a helyi csatlakoztatási ponttal.

5. Válassza ki **konfigurálása, Edge helyi megosztásokból**. Az adatok a helyi megosztásokból helyileg marad az eszközön. Helyileg ezeket az adatokat tud feldolgozni.

6. Az a **jogosultság az összes helyi felhasználó** mezőben válassza ki a **új létrehozása** vagy **meglévő**.

7. Kattintson a **Létrehozás** gombra. 

    ![Helyi megosztás létrehozása](media/data-box-edge-manage-shares/add-local-share-2.png)

    Megjelenik egy értesítés, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást.

    ![Frissítések megosztások panel megjelenítése](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Válassza ki a megosztást a helyi csatlakoztatási pont az Edge számítási a megosztás modulok megtekintéséhez.

    ![Helyi megosztás részleteinek megtekintése](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>A fájlmegosztás csatlakoztatása

Ha egy megosztást a Data Box Edge-eszközön beállított számítási előtt hozta létre, szüksége lesz a megosztás csatlakoztatásához. Az alábbi lépéseket a megosztás csatlakoztatásához.


1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **átjáró > megosztások**. A megosztások listájában jelölje ki a megosztást szeretné csatlakoztatni. A **használt számítási erőforrások** oszlopban jelennek meg lesz, mint az állapot **letiltott** a kijelölt megosztás.

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/select-share-mount.png)

2. Válassza ki **csatlakoztatási**.

    ![Válassza ki a csatlakoztatási](media/data-box-edge-manage-shares/select-mount.png)

3. Amikor a rendszer megerősítést kér, válassza ki a **Igen**. Ez lesz a megosztás csatlakoztatásához.

    ![Csatlakoztatási megerősítése](media/data-box-edge-manage-shares/confirm-mount.png)

4. Miután a fájlmegosztás csatlakoztatva van, lépjen a megosztások. Láthatja, hogy a **használt számítási erőforrások** az oszlopban látható a megosztás állapotjelentése **engedélyezve**.

    ![Fájlmegosztás csatlakoztatva](media/data-box-edge-manage-shares/share-mounted.png)

5. Válassza ki újra a megosztást a megosztás a helyi csatlakoztatási pont megtekintése. Edge számítási modul használja a helyi csatlakoztatási pont a megosztáshoz.

    ![A megosztás helyi csatlakoztatási pont](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>A fájlmegosztás leválasztása

Kövesse az alábbi lépéseket az Azure Portalon a fájlmegosztás leválasztásához.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrás, és folytassa a **átjáró > megosztások**.

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/select-share-unmount.png)

2. A megosztások listájában jelölje ki a leválasztani kívánt megosztást. Győződjön meg arról, hogy a megosztást, válassza le nem kapcsolt modulok által használt szeretné. Ha egy modul használja a megosztást, majd megjelenik a megfelelő modullal kapcsolatos problémák. Válassza ki **leválasztása**.

    ![Válassza a leválasztás](media/data-box-edge-manage-shares/select-unmount.png)

3. Amikor a rendszer megerősítést kér, válassza ki a **Igen**. Ez a megosztás fogja válassza le.

    ![Győződjön meg róla leválasztása](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Miután a megosztás nem csatlakoztatott, nyissa meg a megosztások. Láthatja, hogy **használt számítási erőforrások** az oszlopban látható a megosztás állapotjelentése **letiltott**.

    ![Megosztás leválasztani](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Megosztás törlése

Megosztás törléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. A megosztások listájában jelölje ki a törölni kívánt megosztást, majd kattintson rá.

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/delete-share-1.png)

2. Kattintson a **Törlés** gombra.

    ![Kattintson a Törlés gombra](media/data-box-edge-manage-shares/delete-share-2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![Törlés megerősítése](media/data-box-edge-manage-shares/delete-share-3.png)

A törlés megosztások frissítések listája.


## <a name="refresh-shares"></a>Megosztások frissítése

A frissítési funkció lehetővé teszi egy fájlmegosztás tartalmának frissítése. A megosztások frissítésekor a rendszer keresést indít a legutóbbi frissítés óta a felhőbe feltöltött Azure-objektumok (például blobok és fájlok) azonosítására. Ezeket a további fájlokat majd letöltődnek az eszközön a megosztás tartalmának frissítéséhez.

> [!IMPORTANT]
> - Helyi megosztásokból nem frissíthetők.
> - Engedélyek és hozzáférés-vezérlési listák (ACL) nem maradnak a frissítési művelet között. 

Megosztás frissítéséhez hajtsa végre a következő lépéseket az Azure Portalon.

1.  Az Azure Portalon lépjen a **Megosztások** területre. Jelölje ki a frissíteni kívánt megosztást, majd kattintson rá.

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/refresh-share-1.png)

2.  Kattintson a **Frissítés** gombra. 

    ![Kattintson a Frissítés gombra](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.  Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. A rendszer ekkor egy feladatot indít a helyszíni megosztás tartalmának frissítésére.

    ![Frissítés megerősítése](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.  Amíg folyamatban van a frissítés, a Frissítés lehetőség kiszürkítve jelenik meg a helyi menüben. A frissítési feladat állapotát a feladatértesítésre kattintva tekintheti meg.

5.  A frissítés végrehajtásához szükséges idő az Azure-tárolón és az eszközön található fájlok számától függ. A frissítés sikeres befejezése után frissül a megosztás időbélyege. Ha a frissítés csak részben sikeres, maga a művelet akkor is sikeresnek tekintendő, és az időbélyeg frissülni fog. A frissítés hibanaplókat is frissülnek.

    ![Frissített időbélyeg](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Hiba esetén a rendszer riasztást ad ki. A riasztás részletei között szerepel a hiba oka és a hibaelhárítási javaslat. A riasztás továbbá tartalmaz egy fájlra mutató hivatkozást is, amelyre rákattintva megtekintheti a teljes hibajegyzéket, azon fájlok listájával együtt, amelyek esetében a frissítési vagy törlési művelet meghiúsult.


## <a name="sync-storage-keys"></a>Tárkulcsok szinkronizálása

Ha a tárfiók kulcsai le lettek cserélve, akkor szinkronizálni kell a tárelérési kulcsokat. A szinkronizálással biztosítható, hogy az eszköz tárfiókkulcsai naprakészek legyenek.

A tárelérési kulcsok szinkronizálásához hajtsa végre a következő lépéseket az Azure Portalon.

1. Lépjen az erőforrás **Áttekintés** területére. A megosztások listájában válassza ki a szinkronizálni kívánt tárfiókhoz tartozó megosztást, majd kattintson rá.

    ![Válassza ki a megfelelő tárfiók megosztása](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Kattintson a **Tárkulcsok szinkronizálása** elemre. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Válassza ki a storage-kulcs szinkronizálása](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

>[!NOTE]
> Ezt a műveletet csak egyszer kell elvégezni az egyes tárfiókok esetében, nem kell megismételni minden egyes, ugyanazon tárfiókhoz tartozó megosztásnál.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](data-box-edge-manage-users.md).
