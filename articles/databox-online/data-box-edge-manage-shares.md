---
title: Azure Data Box Edge megosztások kezelése | Microsoft Docs
description: Ismerteti, hogyan használható a Azure Portal a megosztások kezeléséhez a Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 4877b136f197f226f142f5ad5eb4035c63c07d7a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80887010"
---
# <a name="use-the-azure-portal-to-manage-shares-on-your-azure-data-box-edge"></a>A Azure Portal használatával kezelheti a Azure Data Box Edge megosztásait

Ez a cikk ismerteti, hogyan kezelheti a megosztásokat a Azure Data Box Edgeon. A Azure Data Box Edge a Azure Portal vagy a helyi webes felületen keresztül felügyelheti. A megosztásokhoz társított Storage-fiókhoz használja a Azure Portal a megosztások hozzáadásához, törléséhez, frissítéséhez vagy a Storage-kulcs szinkronizálásához.

## <a name="about-shares"></a>Információk a megosztásokról

Az adatok Azure-ba történő átviteléhez megosztásokat kell létrehoznia a Azure Data Box Edgeon. A Data Box Edge eszközön hozzáadott megosztások lehetnek helyi megosztások vagy megosztások, amelyek az adott felhőbe küldenek adattovábbítást.

 - **Helyi megosztások**: akkor használja ezeket a megosztásokat, amikor azt szeretné, hogy az eszközön helyileg dolgozzák fel az adatfeldolgozást.
 - **Megosztások**: akkor használja ezeket a megosztásokat, ha azt szeretné, hogy az eszköz adatai automatikusan a felhőben legyenek leküldve a Storage-fiókba. Az összes Felhőbeli funkció, például a **frissítési** és **szinkronizálási tárolási kulcsok** a megosztásokra vonatkoznak.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Megosztás hozzáadása
> * Megosztás törlése
> * Megosztások frissítése
> * Tárkulcs szinkronizálása

## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő lépéseket az Azure Portalon.

1. A Azure Portal nyissa meg a Data Box Edge-erőforrást, és válassza az **átjáró > megosztások**lehetőséget. Válassza a **+ megosztás hozzáadása** parancsot a parancssorban.

    ![Válassza a megosztás hozzáadása elemet.](media/data-box-edge-manage-shares/add-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válassza ki a megosztás **típusát**. A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

4. Adja meg azt a **tárfiókot**, ahol a megosztás található. A rendszer a tárfiókban létrehoz egy tárolót a megosztás nevével, ha még nem létezik ilyen. Ha létezik, akkor a rendszer a meglévő tárolót használja.

5. A legördülő listából válassza ki a **Storage szolgáltatást** a blob, az oldal blob vagy a Files blokkban. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adat az Azure-ban blokkolja a blobokat, ezért a **blob blokkolása**lehetőséget választjuk. Az **oldal blobjának**kiválasztásakor győződjön meg arról, hogy az adatai 512 bájtra vannak igazítva. Az **oldal blobjának** használata VHD-k vagy VHDX esetén, amelyek mindig 512 bájtra vannak igazítva.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a használt Azure Storage-fiók nem rendelkezik módosíthatatlansági-házirendekkel, ha Azure Stack peremhálózati vagy Data Box Gateway eszközzel használja. További információ: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

6. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
   - **SMB-megosztás létrehozásakor** – A **Teljes körű jogosultsággal rendelkező helyi felhasználó** mezőben válasszon az **Új létrehozása** és a **Meglévő használata** lehetőségek közül. Ha új helyi felhasználót hoz létre, adja meg a **felhasználónevet** és a **jelszót**, majd erősítse meg a jelszót. Ezzel hozzárendeli az engedélyeket a helyi felhasználóhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.

      ![SMB-megosztás hozzáadása](media/data-box-edge-manage-shares/add-smb-share.png)

        Ha a megosztott adatokhoz bejelöli a Csak az olvasási műveletek engedélyezése lehetőséget, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
   - **NFS-megosztás létrehozásakor** – Meg kell adnia azon **engedélyezett ügyfelek IP-címeit**, akik hozzáférhetnek a megosztáshoz.

      ![NFS-megosztás hozzáadása](media/data-box-edge-manage-shares/add-nfs-share.png)

7. Az Edge számítási modulok megosztásának egyszerű eléréséhez használja a helyi csatlakoztatási pontot. Válassza **a megosztás Edge-számítással** lehetőséget, hogy a megosztás automatikusan a létrehozás után legyen csatlakoztatva. Ha ez a beállítás be van jelölve, a peremhálózati modul a helyi csatlakoztatási ponttal is használhatja a számítást.

8. A megosztás létrehozásához kattintson a **Létrehozás** parancsra. A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást.

## <a name="add-a-local-share"></a>Helyi megosztás hozzáadása

1. A Azure Portal nyissa meg a Data Box Edge-erőforrást, és válassza az **átjáró > megosztások**lehetőséget. Válassza a **+ megosztás hozzáadása** parancsot a parancssorban.

   ![Válassza a megosztás hozzáadása elemet.](media/data-box-edge-manage-shares/add-local-share-1.png)

2. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak.
    
    A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.

3. Válassza ki a megosztás **típusát**. A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

4. Az Edge számítási modulok megosztásának egyszerű eléréséhez használja a helyi csatlakoztatási pontot. Válassza **a megosztás Edge-számítással** lehetőséget, hogy a peremhálózati modul a helyi csatlakoztatási ponttal tudja használni a számítást.

5. Válassza **a konfigurálás helyi megosztásként**lehetőséget. A helyi megosztásokban lévő információk helyileg maradnak az eszközön. Ezeket az adatfolyamatokat helyileg is feldolgozhatja.

6. A **minden jogosultság helyi felhasználó** mezőben válassza az **új létrehozása** lehetőséget, vagy **használja a meglévőt**.

7. Kattintson a **Létrehozás** gombra. 

   ![Helyi megosztás létrehozása](media/data-box-edge-manage-shares/add-local-share-2.png)

    Megjelenik egy értesítés arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást.

   ![Frissítések megosztásának megtekintése panel](media/data-box-edge-manage-shares/add-local-share-3.png)
    
    Válassza ki a megosztást a megosztás peremhálózati számítási moduljainak helyi csatlakoztatási pont megtekintéséhez.

   ![Helyi megosztás részleteinek megtekintése](media/data-box-edge-manage-shares/add-local-share-4.png)

## <a name="mount-a-share"></a>Megosztás csatlakoztatása

Ha létrehozott egy megosztást, mielőtt beállította a számítást a Data Box Edge eszközön, csatlakoztatnia kell a megosztást. A megosztás csatlakoztatásához hajtsa végre az alábbi lépéseket.

1. A Azure Portal nyissa meg a Data Box Edge-erőforrást, és válassza az **átjáró > megosztások**lehetőséget. A megosztások listájában válassza ki a csatlakoztatni kívánt megosztást. A **számítási** oszlophoz használt érték a kiválasztott megosztásnál **letiltott** állapotot jeleníti meg.

   ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/select-share-mount.png)

2. Válassza a **Csatlakoztatás**lehetőséget.

   ![Csatlakoztatás kiválasztása](media/data-box-edge-manage-shares/select-mount.png)

3. Ha a rendszer megerősítést kér, válassza az **Igen**lehetőséget. Ekkor a rendszer csatlakoztatja a megosztást.

   ![Csatlakoztatás megerősítése](media/data-box-edge-manage-shares/confirm-mount.png)

4. A megosztás csatlakoztatása után lépjen a megosztások listájára. Láthatja, hogy a **számítási** oszlophoz használt érték azt jelzi, hogy a megosztás állapota **engedélyezett**.

   ![Csatlakoztatott megosztás](media/data-box-edge-manage-shares/share-mounted.png)

5. A megosztás helyi csatlakoztatási pont megtekintéséhez válassza újra a megosztást. Az Edge számítási modul ezt a helyi csatlakoztatási pont használja a megosztáshoz.

   ![A megosztás helyi csatlakoztatási pont](media/data-box-edge-manage-shares/share-mountpoint.png)

## <a name="unmount-a-share"></a>Megosztás leválasztása

A megosztás leválasztásához hajtsa végre az alábbi lépéseket a Azure Portalben.

1. A Azure Portal nyissa meg a Data Box Edge-erőforrást, és válassza az **átjáró > megosztások**lehetőséget.

   ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/select-share-unmount.png)

2. A megosztások listájában válassza ki a leválasztani kívánt megosztást. Meg szeretné győződni arról, hogy a leválasztott megosztást egyetlen modul sem használja. Ha a megosztást egy modul használja, akkor a rendszer a megfelelő modullal kapcsolatos problémákat fogja látni. Válassza a **Leválasztás**lehetőséget.

   ![Válassza a leválasztás lehetőséget](media/data-box-edge-manage-shares/select-unmount.png)

3. Ha a rendszer megerősítést kér, válassza az **Igen**lehetőséget. Ezzel leválasztja a megosztást.

   ![Leválasztás megerősítése](media/data-box-edge-manage-shares/confirm-unmount.png)

4. A megosztás leválasztása után lépjen a megosztások listájára. Láthatja, hogy a számítási oszlophoz **használt** érték a megosztás állapotát **letiltottként**jeleníti meg.

   ![Megosztás leválasztva](media/data-box-edge-manage-shares/share-unmounted.png)

## <a name="delete-a-share"></a>Megosztás törlése

Megosztás törléséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. A megosztások listájában jelölje ki a törölni kívánt megosztást, majd kattintson rá.

   ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/delete-share-1.png)

2. Kattintson a **Törlés** gombra.

   ![Kattintson a Törlés gombra](media/data-box-edge-manage-shares/delete-share-2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

   ![Törlés megerősítése](media/data-box-edge-manage-shares/delete-share-3.png)

A megosztások listájának frissítése, hogy tükrözze a törlést.

## <a name="refresh-shares"></a>Megosztások frissítése

A frissítési funkció lehetővé teszi egy megosztás tartalmának frissítését. A megosztások frissítésekor a rendszer keresést indít a legutóbbi frissítés óta a felhőbe feltöltött Azure-objektumok (például blobok és fájlok) azonosítására. A rendszer ezután letölti ezeket a további fájlokat az eszközön található megosztás tartalmának frissítéséhez.

> [!IMPORTANT]
>
> - A helyi megosztások nem frissíthetők.
> - Az engedélyek és hozzáférés-vezérlési listák (ACL-ek) nem őrződnek meg a frissítési művelet során. 

Megosztás frissítéséhez hajtsa végre a következő lépéseket az Azure Portalon.

1. Az Azure Portalon lépjen a **Megosztások** területre. Jelölje ki a frissíteni kívánt megosztást, majd kattintson rá.

   ![Jelölje ki a megosztást](media/data-box-edge-manage-shares/refresh-share-1.png)

2. Kattintson a **Frissítés** gombra.

   ![Kattintson a Frissítés gombra](media/data-box-edge-manage-shares/refresh-share-2.png)
 
3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. A rendszer ekkor egy feladatot indít a helyszíni megosztás tartalmának frissítésére.

   ![Frissítés megerősítése](media/data-box-edge-manage-shares/refresh-share-3.png)

4. Amíg folyamatban van a frissítés, a Frissítés lehetőség kiszürkítve jelenik meg a helyi menüben. A frissítési feladat állapotát a feladatértesítésre kattintva tekintheti meg.

5. A frissítés végrehajtásához szükséges idő az Azure-tárolón és az eszközön található fájlok számától függ. A frissítés sikeres befejezése után frissül a megosztás időbélyege. Ha a frissítés csak részben sikeres, maga a művelet akkor is sikeresnek tekintendő, és az időbélyeg frissülni fog. A frissítési naplók frissítése is megtörtént.

   ![Frissített időbélyeg](media/data-box-edge-manage-shares/refresh-share-4.png)
 
Hiba esetén a rendszer riasztást ad ki. A riasztás részletei között szerepel a hiba oka és a hibaelhárítási javaslat. A riasztás továbbá tartalmaz egy fájlra mutató hivatkozást is, amelyre rákattintva megtekintheti a teljes hibajegyzéket, azon fájlok listájával együtt, amelyek esetében a frissítési vagy törlési művelet meghiúsult.

## <a name="sync-storage-keys"></a>Tárkulcsok szinkronizálása

Ha a tárfiók kulcsai le lettek cserélve, akkor szinkronizálni kell a tárelérési kulcsokat. A szinkronizálással biztosítható, hogy az eszköz tárfiókkulcsai naprakészek legyenek.

A tárelérési kulcsok szinkronizálásához hajtsa végre a következő lépéseket az Azure Portalon.

1. Lépjen az erőforrás **Áttekintés** területére. A megosztások listájában válassza ki a szinkronizálni kívánt tárfiókhoz tartozó megosztást, majd kattintson rá.

    ![Megosztás kiválasztása a megfelelő Storage-fiókkal](media/data-box-edge-manage-shares/sync-storage-key-1.png)

2. Kattintson a **Tárkulcsok szinkronizálása** elemre. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

     ![Válassza ki a Storage-kulcs szinkronizálása lehetőséget](media/data-box-edge-manage-shares/sync-storage-key-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

>[!NOTE]
> Ezt a műveletet csak egyszer kell elvégezni az egyes tárfiókok esetében, nem kell megismételni minden egyes, ugyanazon tárfiókhoz tartozó megosztásnál.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [kezelheti a felhasználókat az Azure Portalon keresztül](data-box-edge-manage-users.md).
