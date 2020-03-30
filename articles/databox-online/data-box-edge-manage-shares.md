---
title: Azure Data Box Edge megosztáskezelés | Microsoft dokumentumok
description: Bemutatja, hogyan használhatja az Azure Portal on az Azure Data Box Edge megosztások kezelésére.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: b25409c63806e203bd841b0373543b7cc2b96d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79212943"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>Az Azure Data Box Edge megosztásának kezeléséhez használja az Azure Portalportálon lévő megosztásokat

Ez a cikk ismerteti, hogyan kezelheti a megosztások az Azure Data Box Edge. Az Azure Data Box Edge az Azure Portalon keresztül vagy a helyi webes felhasználói felületen keresztül kezelhető. Az Azure Portal használatával hozzáadhat, törölhet, frissíthet megosztásokat, vagy szinkronizálhatja a megosztásokkal társított tárfiókot.

## <a name="about-shares"></a>Információk a megosztásokról

Az Azure-ba történő adatátvitelhez megosztásokat kell létrehoznia az Azure Data Box Edge-en. A Data Box Edge-eszközön hozzáadott megosztások lehetnek helyi megosztások vagy megosztások, amelyek adatokat afelhőbe szeretne leadni.

 - **Helyi megosztások**: Használja ezeket a megosztásokat, ha azt szeretné, hogy az adatok helyileg fellegyenek dolgozva az eszközön.
 - **Megosztások:** Használja ezeket a megosztásokat, ha azt szeretné, hogy az eszközadatok automatikusan leküldéses a tárfiókba a felhőben. Az összes felhőfüggvény, például a **Refresh** és **a Sync tárolókulcsok** vonatkoznak a megosztásokra.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Megosztás törlése
> * Megosztások frissítése
> * Tárkulcs szinkronizálása


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő lépéseket az Azure Portalon.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **a Gateway > megosztások.** Válassza a **+ Megosztás hozzáadása** lehetőséget a parancssávon.

    ![Megosztás hozzáadása kiválasztása](media/data-box-edge-manage-shares/add-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válassza ki a megosztás **típusát**. A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

4. Adja meg azt a **tárfiókot**, ahol a megosztás található. A rendszer a tárfiókban létrehoz egy tárolót a megosztás nevével, ha még nem létezik ilyen. Ha létezik, akkor a rendszer a meglévő tárolót használja.

5. A legördülő listából válassza ki a **Storage szolgáltatást** blokkblobból, lapblobból vagy fájlokból. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adatok blokkblobként legyenek elrendelve az Azure-ban, ezért a **Blokkblob**lehetőséget választjuk. Ha a Lapblob lehetőséget **választja,** gondoskodnia kell arról, hogy az adatok 512 bájthoz igazítva legyenek. Használja **a lapblobot** a virtuális merevlemezek hez, vagy a VHDX-et, amelyek mindig 512 bájtigazítással vannak elrendezve.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy az Azure Storage-fiók, amely használata nem rendelkezik a nemmódosíthatósági szabályzatok, ha egy Azure Stack Edge vagy Data Box Gateway eszközzel használja. További információt a [Blob Storage-alapú megváltoztathatatlansági házirendek beállítása és kezelése című témakörben talál.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)

6. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
    - **SMB-megosztás létrehozásakor** – A **Teljes körű jogosultsággal rendelkező helyi felhasználó** mezőben válasszon az **Új létrehozása** és a **Meglévő használata** lehetőségek közül. Ha új helyi felhasználót hoz létre, adja meg a **felhasználónevet** és a **jelszót**, majd erősítse meg a jelszót. Ezzel hozzárendeli az engedélyeket a helyi felhasználóhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.

        ![SMB-megosztás hozzáadása](media/data-box-edge-manage-shares/add-smb-share.png)

        Ha a megosztott adatokhoz bejelöli a Csak az olvasási műveletek engedélyezése lehetőséget, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
    - **NFS-megosztás létrehozásakor** – Meg kell adnia azon **engedélyezett ügyfelek IP-címeit**, akik hozzáférhetnek a megosztáshoz.

        ![NFS-megosztás hozzáadása](media/data-box-edge-manage-shares/add-nfs-share.png)

7. A megosztások edge számítási modulok egyszerű eléréséhez használja a helyi csatlakoztatási pontot. **Válassza a Megosztás használata az Edge-számítással** lehetőséget, hogy a megosztás létrehozása után automatikusan csatlakoztatva legyen. Ha ez a beállítás be van jelölve, az Edge modul is használhatja a számítást a helyi csatlakoztatási ponttal.

8. A megosztás létrehozásához kattintson a **Létrehozás** parancsra. A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást.

## <a name="add-a-local-share"></a>Helyi megosztás hozzáadása

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **a Gateway > megosztások.** Válassza a **+ Megosztás hozzáadása** lehetőséget a parancssávon.

    ![Megosztás hozzáadása kiválasztása](media/data-box-edge-manage-shares/add-local-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válassza ki a megosztás **típusát**. A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

4. A megosztások edge számítási modulok egyszerű eléréséhez használja a helyi csatlakoztatási pontot. **Válassza a Megosztás használata edge-számítással** lehetőséget, hogy az Edge modul használhassa a számítást a helyi csatlakoztatási ponttal.

5. Válassza **a Beállítás edge helyi megosztásként lehetőséget.** A helyi megosztások adatai helyileg maradnak az eszközön. Ezeket az adatokat helyileg is feldolgozhatja.

6. A **Minden jogosultság helyi felhasználója** mezőben válasszon az **Új létrehozása** vagy **a Meglévő használata lehetőséget.**

7. Kattintson a **Létrehozás** gombra. 

    ![Helyi megosztás létrehozása](media/data-box-edge-manage-shares/add-local-share-2.png)

    Megjelenik egy értesítés arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást.

    ![Frissítések megtekintése Megosztások panel](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Válassza ki a megosztást az Edge számítási moduljainak helyi csatlakoztatási pontjának megtekintéséhez.

    ![Helyi megosztás részleteinek megtekintése](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Megosztás csatlakoztatása

Ha létrehozott egy megosztást, mielőtt konfigurálta a számítást a Data Box Edge eszközön, csatlakoztatnia kell a megosztást. A megosztás csatlakoztatása a következő lépésekkel egészül ki.


1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **a Gateway > megosztások.** A megosztások listájából válassza ki a csatlakoztatni kívánt megosztást. A **Használt számítási oszlop** ban a kijelölt megosztás **letiltott** állapota jelenik meg.

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/select-share-mount.png)

2. Válassza **a Mount**lehetőséget.

    ![Csatlakoztatás kiválasztása](media/data-box-edge-manage-shares/select-mount.png)

3. Amikor megerősítést kér, válassza az **Igen**lehetőséget. Ez felmászik a megosztásra.

    ![Csatlakoztatás megerősítése](media/data-box-edge-manage-shares/confirm-mount.png)

4. A megosztás csatlakoztatása után lépjen a megosztások listájára. Látni fogja, hogy a **Használt számítási oszlopban** a megosztás állapota **Engedélyezve .**

    ![Megosztás csatlakoztatva](media/data-box-edge-manage-shares/share-mounted.png)

5. A megosztás helyi csatlakoztatási pontjának megtekintéséhez ismét válassza ki a megosztást. Az Edge számítási modul ezt a helyi csatlakoztatási pontot használja a megosztáshoz.

    ![Helyi csatlakoztatási pont a megosztáshoz](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Megosztás leválasztása

Az alábbi lépéseket az Azure Portalon egy megosztás leválasztásához.

1. Az Azure Portalon nyissa meg a Data Box Edge erőforrást, majd nyissa meg **a Gateway > megosztások.**

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/select-share-unmount.png)

2. A megosztások listájából válassza ki a leválasztani kívánt megosztást. Győződjön meg arról, hogy a leválasztani kívánt megosztást egyetlen modul sem használja. Ha a megosztást egy modul használja, akkor a megfelelő modullal kapcsolatos problémák jelennek meg. Válassza **a Leválasztás lehetőséget.**

    ![Leválasztás kijelölése](media/data-box-edge-manage-shares/select-unmount.png)

3. Amikor megerősítést kér, válassza az **Igen**lehetőséget. Ezzel leválik a megosztás.

    ![Leválasztás megerősítése](media/data-box-edge-manage-shares/confirm-unmount.png)

4. Miután a megosztást lenem oldotta, lépjen a megosztások listájára. Látni fogja, hogy **a Használt számítási** oszlopban a megosztás állapota **Letiltva**állapot.

    ![Nem csatlakoztatott megosztás](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Megosztás törlése

Megosztás törléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. A megosztások listájában jelölje ki a törölni kívánt megosztást, majd kattintson rá.

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/delete-share-1.png)

2. Kattintson a **Törlés** gombra.

    ![Kattintson a Törlés gombra](media/data-box-edge-manage-shares/delete-share-2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![Törlés megerősítése](media/data-box-edge-manage-shares/delete-share-3.png)

A megosztások listája a törlésnek megfelelően frissül.


## <a name="refresh-shares"></a>Megosztások frissítése

A frissítési funkció lehetővé teszi egy megosztás tartalmának frissítését. A megosztások frissítésekor a rendszer keresést indít a legutóbbi frissítés óta a felhőbe feltöltött Azure-objektumok (például blobok és fájlok) azonosítására. Ezek a további fájlok ezután letöltődnek, hogy frissítsék a megosztás tartalmát az eszközön.

> [!IMPORTANT]
> - A helyi megosztások nem frissíthetők.
> - Az engedélyek és a hozzáférés-vezérlési listák (ACL-k) nem őrződnek meg a frissítési művelet során. 

Megosztás frissítéséhez hajtsa végre a következő lépéseket az Azure Portalon.

1.   Az Azure Portalon lépjen a **Megosztások** területre. Jelölje ki a frissíteni kívánt megosztást, majd kattintson rá.

    ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/refresh-share-1.png)

2.   Kattintson a **Frissítés** gombra. 

    ![Kattintson a Frissítés gombra](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3.   Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. A rendszer ekkor egy feladatot indít a helyszíni megosztás tartalmának frissítésére.

    ![Frissítés megerősítése](media/data-box-edge-manage-shares/refresh-share-3.png)
 
4.   Amíg folyamatban van a frissítés, a Frissítés lehetőség kiszürkítve jelenik meg a helyi menüben. A frissítési feladat állapotát a feladatértesítésre kattintva tekintheti meg.

5.   A frissítés végrehajtásához szükséges idő az Azure-tárolón és az eszközön található fájlok számától függ. A frissítés sikeres befejezése után frissül a megosztás időbélyege. Ha a frissítés csak részben sikeres, maga a művelet akkor is sikeresnek tekintendő, és az időbélyeg frissülni fog. A frissítési hibanaplók is frissülnek.

    ![Frissített időbélyeg](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Hiba esetén a rendszer riasztást ad ki. A riasztás részletei között szerepel a hiba oka és a hibaelhárítási javaslat. A riasztás továbbá tartalmaz egy fájlra mutató hivatkozást is, amelyre rákattintva megtekintheti a teljes hibajegyzéket, azon fájlok listájával együtt, amelyek esetében a frissítési vagy törlési művelet meghiúsult.


## <a name="sync-storage-keys"></a>Tárkulcsok szinkronizálása

Ha a tárfiók kulcsai le lettek cserélve, akkor szinkronizálni kell a tárelérési kulcsokat. A szinkronizálással biztosítható, hogy az eszköz tárfiókkulcsai naprakészek legyenek.

A tárelérési kulcsok szinkronizálásához hajtsa végre a következő lépéseket az Azure Portalon.

1. Lépjen az erőforrás **Áttekintés** területére. A megosztások listájában válassza ki a szinkronizálni kívánt tárfiókhoz tartozó megosztást, majd kattintson rá.

    ![Megosztás kiválasztása a megfelelő tárfiókkal](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Kattintson a **Tárkulcsok szinkronizálása** elemre. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Tárolókulcs kiválasztása](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

>[!NOTE]
> Ezt a műveletet csak egyszer kell elvégezni az egyes tárfiókok esetében, nem kell megismételni minden egyes, ugyanazon tárfiókhoz tartozó megosztásnál.


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](data-box-edge-manage-users.md).
