---
title: Az oktatóanyagban adatokat másol a VHD-k a felügyelt lemezek az Azure Data Box nehéz |} A Microsoft Docs
description: 'Útmutató: adatok másolása VHD-ből a helyszíni virtuális gépek számítási feladatait az Azure Data Box (nagy erőforrásigényű)'
services: databox
author: alkohli
ms.service: databox
ms.subservice: Heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 8065d29c0cb984244178d49fe8c8c5aa853ee682
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595762"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Oktatóanyag: Használati adatok mezőbe (nagy erőforrásigényű) az adatok importálása a felügyelt lemezek az Azure-ban

Ez az oktatóanyag leírja, hogyan használhatja az Azure Data Box nehéz a helyszíni virtuális merevlemezek migrálása az Azure-ban felügyelt lemezekre. A VHD-fájlokat a helyszíni virtuális gépek lapblobként kerülnek át a Data Box (nagy erőforrásigényű), és feltöltése az Azure-ba, felügyelt lemezeket. Ezek a felügyelt lemezek is, majd Azure virtuális gépekhez lehet csatolni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozhat a Data Box (nagy erőforrásigényű)
> * Másolja az adatokat a Data Box (nagy erőforrásigényű)


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ön teljesítette a [oktatóanyag: Állítsa be az Azure Data Box nehéz](data-box-heavy-deploy-set-up.md).
2. A Data Box nehéz kapott, és a rendelés állapota a portálon **kézbesítések**.
3. Nagy sebességű hálózat csatlakozik. A leggyorsabb másolási megbízhatóbbak két 40-GbE kapcsolatot (egy csomópontonkénti), amellyel párhuzamosan. Ha nincs elérhető kapcsolat 40-GbE, javasoljuk, hogy legalább két 10-GbE kapcsolatot (egy csomópontonkénti). 
4. Áttekintette a:

    - Támogatott [felügyelt lemezek méretei az Azure objektum méretbeli korlátokat](data-box-heavy-limits.md#azure-object-size-limits).
    - [Bevezetés az Azure-ba, felügyelt lemezek](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Csatlakozhat a Data Box (nagy erőforrásigényű)

A megadott erőforrás-csoportok alapján, Data Box (nagy erőforrásigényű) hoz társított erőforrás csoportonkénti száma csomópontonként egy megosztást. Például ha `mydbmdrg1` és `mydbmdrg2` jöttek létre, amikor a rendelés helyez, a következő megosztások jön létre:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Belül minden megosztás a következő három mappák jönnek létre, amelyek megfelelnek a tárfiókban lévő tárolók.

- Prémium SSD
- Standard HDD
- Standard SSD

Az alábbi táblázat a Data Box nehéz a jeleníti meg az UNC elérési útvonalat a megosztásokat.
 
|        Kapcsolat protokollja           |             A megosztás UNC elérési útja                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Alapján, hogy használják az SMB vagy NFS Data Box nehéz megosztásokhoz csatlakozni, csatlakozni más lépéseket kell végrehajtania.

> [!NOTE]
> - REST-en keresztül csatlakozó a funkció nem támogatott.
> - Ismételje meg a Data Box (nagy erőforrásigényű), a második csomópontra történő csatlakozáshoz connect utasításokat.

### <a name="connect-to-data-box-heavy-via-smb"></a>Csatlakozhat a Data Box (nagy erőforrásigényű) az SMB-n keresztül

Ha egy Windows Server-állomás számítógépen használja, kövesse az alábbi lépéseket a Data Box (nagy erőforrásigényű) való kapcsolódáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson a **hitelesítő adatainak lekérése** a hozzáférési hitelesítő adatokat lekérni az erőforráscsoporthoz társított megosztásokhoz. A hozzáférési hitelesítő adatokat is beszerezheti a **eszközadatok** az Azure Portalon.

    > [!NOTE]
    > Felügyelt lemezek minden megosztás hitelesítő adatai azonosak.

    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. A a megosztás hozzáférés és másolás adatok párbeszédpanel, másolási a **felhasználónév** és a **jelszó** a megosztáshoz. Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Az erőforrás társított megosztás eléréséhez (*mydbmdrg1* az alábbi példában) a gazdagép számítógépről, nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Az UNC-megosztási elérési utak ebben a példában a következők:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>\<ShareName>`. Kattintson a **OK** , nyissa meg a Fájlkezelőt.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Meg kell jelennie a következő precreated egyes megosztáson belüli mappákhoz.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Csatlakozhat a Data Box nehéz NFS-n keresztül

Ha egy Linuxos gazdagép számítógépet használ, a következő lépésekkel konfigurálja az eszközt az NFS-ügyfelek hozzáférésének engedélyezéséhez.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre.

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót.

3. Az NFS-ügyfél telepítése után használja a következő parancsot az NFS-megosztások csatlakoztatására az eszközön:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan megosztáshoz való csatlakozáshoz az NFS-n keresztül a Data Box vagy a Data Box (nagy erőforrásigényű). A Data Box vagy a Data Box nehéz eszköz IP-cím `169.254.250.200`, a megosztás `mydbmdrg1_MDisk` van csatlakoztatva a ubuntuVM, csatlakoztatási pont alatt `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Másolja az adatokat a Data Box (nagy erőforrásigényű)

Után az kiszolgáló csatlakozott, a következő lépés az adatok másolása. A VHD-fájl, lapblob másolódik az előkészítési tárfiókból. A lapblob ezután alakítani egy felügyelt lemezt és egy erőforráscsoport áthelyezése.

Mielőtt elkezdené az adatok másolását, tekintse át az alábbiakat:

- Minden esetben másolja a VHD-k egyik precreated mappát. Másolja a VHD-ken kívüli ezeket a mappákat vagy egy mappában, amelyet Ön hozott létre, ha a virtuális merevlemezeket lapblobként Azure Storage-fiókhoz lesz feltöltve, és nem felügyelt lemezek.
- Csak a rögzített méretű virtuális merevlemezeket is feltölthetők a felügyelt lemez gyors létrehozásához. A VHDX-fájlok vagy a virtuális dinamikus és különbséglemezek nem támogatottak.
- Használhat egy felügyelt lemezt egy megadott nevű csak egy erőforráscsoportot a precreated mappák között. Ez azt jelenti, hogy egyedi nevük legyen a VHD-k a precreated mappák feltöltött kell. Győződjön meg arról, hogy a megadott név nem egyezik meg a olyan már meglévő felügyelt lemezről egy erőforráscsoportban.
- Tekintse át a felügyelt lemez korlátok [Azure objektum méretbeli korlátokat](data-box-heavy-limits.md#azure-object-size-limits).

Attól függően, hogy csatlakozik az SMB vagy NFS-n keresztül használhatja:

- [Adatok másolása az SMB-n keresztül](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Adatok másolása az NFS-n keresztül](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Várjon, amíg befejeződik a másolási feladatokat. Győződjön meg arról, hogy a másolási feladatokat végzett hiba nélkül, mielőtt továbblép a következő lépéssel.

![Nincsenek hibák, a ** csatlakozás és másolás ** lap](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Ha a másolási folyamat közben hibák, töltse le a naplók a **csatlakozás és másolás** lapot.

- Ha egy fájlt, amely nem 512 bájt igazítva másolja, a fájl lapblob a átmeneti tárfiók nem feltöltve. Látni fogja a naplókban hiba. Távolítsa el a fájlt, és másolja át a fájlt, amely pedig 512 bájt igazítva.

- Ha vhdx-fájlt (ezek a fájlok nem támogatottak) egy hosszú névvel rendelkező másolja, látni fogja a naplókban hiba.

    ![Hiba történt a naplók a ** csatlakozás és másolás ** lap](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Javítsa ki a hibákat, mielőtt továbblép a következő lépéssel.

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

A másolási feladat befejeződése után nyissa meg **szállításra való**.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett az Azure Data Box nehéz témakörök például:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozhat a Data Box (nagy erőforrásigényű)
> * Másolja az adatokat a Data Box (nagy erőforrásigényű)


Folytassa a következő oktatóanyaggal, megtudhatja, hogyan tehetnek a Data Box nehéz a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box (nagy erőforrásigényű) a Microsoft szállításra](./data-box-heavy-deploy-picked-up.md)

