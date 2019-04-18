---
title: Adatokat importálhat VHD-k és Microsoft Azure Data Box-felügyelt lemezek másolása |} A Microsoft Docs
description: Ismerje meg, hogyan másolhat adatokat VHD-ből a helyszíni virtuális gépek számítási feladataihoz az Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
ms.openlocfilehash: ec2013a793f766221a66912d6de9d8da8b8106dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282559"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Oktatóanyag: Ezen adatok mezőbe az adatok importálása a felügyelt lemezek az Azure-ban

Ez az oktatóanyag leírja, hogyan használhatja az Azure Data Box helyi VHD-k áttelepítése az Azure-ban felügyelt lemezeket. A VHD-fájlokat a helyszíni virtuális gépek lesz másolva a Data Box lapblobként és feltöltése az Azure-ba, felügyelt lemezeket. Ezek a felügyelt lemezek is, majd Azure virtuális gépekhez lehet csatolni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Ön teljesítette a [oktatóanyag: Állítsa be az Azure Data Box](data-box-deploy-set-up.md).
2. A Data Box kapott, és a rendelés állapota a portálon **kézbesítések**.
3. Nagy sebességű hálózat csatlakozik. Határozottan javasoljuk, hogy legalább 10 GbE sebességű kapcsolattal rendelkezzen. 10-GbE kapcsolatot nem érhető el, ha 1-GbE adatok hivatkozás használata, de a másolási sebességek érintett.
4. Áttekintette a:

    - Támogatott [felügyelt lemezek méretei az Azure objektum méretbeli korlátokat](data-box-limits.md#azure-object-size-limits).
    - [Bevezetés az Azure-ba, felügyelt lemezek](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box"></a>Csatlakozás a Data Boxhoz

A megadott erőforrás-csoportok alapján, a Data Box egy megosztást minden társított erőforráscsoportot hoz létre. Például ha `mydbmdrg1` és `mydbmdrg2` jöttek létre, amikor a rendelés helyez, a következő megosztások jön létre:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Belül minden megosztás a következő három mappák jönnek létre, amelyek megfelelnek a tárfiókban lévő tárolók.

- Prémium SSD
- Standard HDD
- Standard SSD

Az alábbi táblázat mutatja a Data Box az UNC elérési útvonalat a megosztásokat.
 
|        Kapcsolat protokollja           |             A megosztás UNC elérési útja                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Alapján, hogy használják az SMB vagy NFS szeretne csatlakozni a Data Box-megosztásokat, csatlakozni más lépéseket kell végrehajtania.

> [!NOTE]
> REST-en keresztül csatlakozó a funkció nem támogatott.

### <a name="connect-to-data-box-via-smb"></a>Csatlakozás a Data Boxba SMB-n keresztül

Ha a gazdagép Windows Server számítógépet használ, kövesse az alábbi lépéseket a Data Box csatlakozni.

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


### <a name="connect-to-data-box-via-nfs"></a>Csatlakozhat a Data Box NFS-n keresztül

Amennyiben Linux rendszerű gazdagépet használ, a következő módon konfigurálhatja a Data Boxot, hogy hozzáférést biztosítson az NFS-ügyelek számára.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre.

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót.

3. Az NFS-ügyfél telepítését követően az alábbi paranccsal csatlakoztathatja a Data Box-eszközön található NFS-megosztást:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan kell NFS-en keresztüli kapcsolódni egy Data Box-megosztáshoz. A Data Box-eszköz IP-címe `169.254.250.200`, a megosztás (`mydbmdrg1_MDisk`) az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a következő: `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Adatok másolása a Data Boxra

Után az kiszolgáló csatlakozott, a következő lépés az adatok másolása. A VHD-fájl, lapblob másolódik az előkészítési tárfiókból. A lapblob ezután alakítani egy felügyelt lemezt és egy erőforráscsoport áthelyezése.

Mielőtt elkezdené az adatok másolását, tekintse át az alábbiakat:

- Minden esetben másolja a VHD-k egyik precreated mappát. Másolja a VHD-ken kívüli ezeket a mappákat vagy egy mappában, amelyet Ön hozott létre, ha a virtuális merevlemezeket lapblobként Azure Storage-fiókhoz lesz feltöltve, és nem felügyelt lemezek.
- Csak a rögzített méretű virtuális merevlemezeket is feltölthetők a felügyelt lemez gyors létrehozásához. A VHDX-fájlok vagy a virtuális dinamikus és különbséglemezek nem támogatottak.
- Használhat egy felügyelt lemezt egy megadott nevű csak egy erőforráscsoportot a precreated mappák között. Ez azt jelenti, hogy egyedi nevük legyen a VHD-k a precreated mappák feltöltött kell. Győződjön meg arról, hogy a megadott név nem egyezik meg a olyan már meglévő felügyelt lemezről egy erőforráscsoportban.
- Tekintse át a felügyelt lemez korlátok [Azure objektum méretbeli korlátokat](data-box-limits.md#azure-object-size-limits).

Attól függően, hogy csatlakozik az SMB vagy NFS-n keresztül használhatja:

- [Adatok másolása az SMB-n keresztül](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Adatok másolása az NFS-n keresztül](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

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

Ebben az oktatóanyagban az Azure Data Box témaköréből ismerhette meg a következőket:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozás a Data Boxhoz
> * Adatok másolása a Data Boxra


Folytassa a következő oktatóanyaggal, megtudhatja, hogyan tehetnek a Data Box elküldje a Microsoftnak.

> [!div class="nextstepaction"]
> [Azure Data Box elküldése a Microsoftnak](./data-box-deploy-picked-up.md)

