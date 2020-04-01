---
title: 'Oktatóanyag: Másolás virtuális merevlemezekről felügyelt lemezekre'
titleSuffix: Azure Data Box Heavy
description: Megtudhatja, hogyan másolhat adatokat virtuális gépekről a helyszíni virtuálisgép-számítási feladatokból az Azure Data Box Heavy szolgáltatásba
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77471329"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Oktatóanyag: A Data Box Heavy használatával importáltadatokat felügyelt lemezként az Azure-ban

Ez az oktatóanyag ismerteti, hogyan használhatja az Azure Data Box Heavy-t a helyszíni virtuális merevlemezek azure-beli felügyelt lemezekre való áttelepítéséhez. A helyszíni virtuális gépek virtuális merevlemezek a Data Box Heavy-ba lapblobként kerülnek másolásra, és felügyelt lemezekként kerülnek feltöltésre az Azure-ba. Ezek a felügyelt lemezek ezután csatlakoztathatók az Azure virtuális gépekhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejezte az [oktatóanyagot: Az Azure Data Box Heavy beállítása.](data-box-heavy-deploy-set-up.md)
2. Megkapta a Data Box Heavyt, és a portálon a megrendelés **Kézbesítve** állapotú.
3. Nagy sebességű hálózathoz csatlakozik. A legnagyobb másolási sebesség érdekében két 40 GbE sebességű kapcsolat (csomópontonként egy) használható egyidejűleg. Ha nem rendelkezik 40 GbE sebességű kapcsolattal, javasoljuk, hogy legalább két 10 GbE sebességű kapcsolattal (csomópontonként eggyel) rendelkezzen. 
4. Áttekintette a következőket:

    - Támogatott [felügyelt lemezméretek az Azure-objektumok méretkorlátaiban.](data-box-heavy-limits.md#azure-object-size-limits)
    - [Bevezetés az Azure által felügyelt lemezekbe.](/azure/virtual-machines/windows/managed-disks-overview) 

## <a name="connect-to-data-box-heavy"></a>Csatlakozás a Data Box Heavyhez

A megadott erőforráscsoportok alapján a Data Box Heavy csomópontonként egy megosztást hoz létre minden társított erőforráscsoporthoz. Ha például `mydbmdrg1` `mydbmdrg2` a rendelés leadásakor létrehozták és létrehozták, a következő megosztások jönnek létre:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Az egyes megosztásokon belül a következő három mappa jön létre, amelyek megfelelnek a tárfiókban lévő tárolóknak.

- Prémium SSD
- Standard HDD
- Standard SSD

Az alábbi táblázat a Data Box Heavy megosztások UNC elérési útjait mutatja be.
 
|        Csatlakozási protokoll           |             UNC elérési út a megosztáshoz                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Attól függően, hogy SMB vagy NFS használatával csatlakozik-e a Data Box Heavy megosztásokhoz, a csatlakozás lépései eltérőek.

> [!NOTE]
> - A REST-en keresztüli csatlakozás nem támogatott ehhez a funkcióhoz.
> - Ismételje meg a csatlakozási utasításokat a Data Box Heavy második csomópontjának csatlakozásához.

### <a name="connect-to-data-box-heavy-via-smb"></a>Csatlakozás a Data Box Heavy-hoz SMB-n keresztül

Ha Windows Server rendszerű gazdagépet használ, kövesse az alábbi lépéseket a Data Box Heavyhez történő csatlakozáshoz.

1. Az első lépés a hitelesítés elvégzése, majd a munkamenet elindítása. Lépjen a **Connect and copy** (Kapcsolódás és másolás) elemre. Kattintson **a Hitelesítő adatok beszerezése** a hozzáférési hitelesítő adatok at a megosztások társított erőforráscsoport. A hozzáférési hitelesítő adatokat az Azure Portalon az **eszköz részleteiből** is lejuthat.

    > [!NOTE]
    > A felügyelt lemezek összes megosztásának hitelesítő adatai azonosak.

    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Az Access megosztási és másolási adatai párbeszédpanelen másolja a **megosztás felhasználónevét** és **jelszavát.** Kattintson az **OK** gombra.
    
    ![Megosztások hitelesítő adatainak beszerzése 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Az erőforráshoz társított megosztások (a következő példában a*mydbmdrg1)* eléréséhez nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ebben a példában az UNC megosztási elérési útjai a következők:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Ha a rendszer kéri, adja meg a megosztás jelszavát. A következő példa bemutatja, hogyan kell csatlakozni egy megosztáshoz a fenti parancs használatával.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>\<ShareName>`. Kattintson az **OK** gombra a Fájlkezelő megnyitásához.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Most a következő előre létrehozott mappákat kell látnia az egyes megosztásokon belül.
    
    ![Kapcsolódás a megosztáshoz a Fájlkezelővel 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Csatlakozás a Data Box Heavy-hoz NFS-en keresztül

Ha Linux-gazdaszámítógépet használ, hajtsa végre az alábbi lépéseket az eszköz nfs-ügyfelekhez való hozzáférés engedélyezéséhez való konfigurálásához.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre.

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót.

3. Az NFS-ügyfél telepítése után a következő paranccsal csatlakoztassa az NFS-megosztást az eszközre:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    A következő példa bemutatja, hogyan csatlakozhat NFS-en keresztül egy Data Box vagy Data Box Heavy megosztáshoz. A Data Box vagy a `169.254.250.200`Data Box `mydbmdrg1_MDisk` Heavy eszköz IP-je , a `/home/databoxubuntuhost/databox`megosztás az ubuntuVM-re van szerelve, a csatlakoztatási pont .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Adatok másolása a Data Box Heavyre

Miután csatlakozott az adatkiszolgálóhoz, a következő lépés az adatok másolása. A VHD-fájl lapblobként kerül az átmeneti tárfiókba. A lapblob ezután felügyelt lemezre konvertálódik, és egy erőforráscsoportba kerül.

Az adatok másolásának megkezdése előtt tekintse át az alábbi szempontokat:

- Mindig másolja át a VHD-kat az egyik előre létrehozott mappába. Ha a virtuális merevlemezek ezeken a mappákon kívülre, vagy egy létrehozott mappában, a Virtuális merevlemezek lesz nek feltöltve az Azure Storage-fiók ba lapblobok, és nem felügyelt lemezek.
- Csak a rögzített VHD-ket lehet feltölteni felügyelt lemezek létrehozásához. A VHDX-fájlok vagy a dinamikus és eltérő virtuális merevlemezek nem támogatottak.
- Az összes előre létrehozott mappában csak egy adott nevű felügyelt lemez lehet egy erőforráscsoportban. Ez azt is jelenti, hogy az előre létrehozott mappákba feltöltött VHD-knak egyedi névvel kell rendelkezniük. Győződjön meg arról, hogy a megadott név nem egyezik meg egy erőforráscsoport meglévő felügyelt lemez nevével sem.
- Tekintse át a felügyelt lemezkorlátokat az [Azure-objektumok méretkorlátaiban.](data-box-heavy-limits.md#azure-object-size-limits)

Attól függően, hogy SMB-n vagy NFS-en keresztül csatlakozik-e, a következőket használhatja:

- [Adatok másolása SMB-n keresztül](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Adatok másolása NFS-en keresztül](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Várja meg, amíg a másolási feladatok befejeződnek. Győződjön meg arról, hogy a másolási feladatok hiba nélkül befejeződtek, mielőtt a következő lépésre lépne.

![Nincs hiba a **Csatlakozás és másolás** oldalon](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Ha a másolási folyamat során hibák lépnek fel, töltse le a naplókat a **Csatlakozás és másolás** lapról.

- Ha nem 512 bájtos fájlt másolt, a fájl nem kerül feltöltésre lapblobként az átmeneti tárfiókba. Hibaüzenet jelenik meg a naplókban. Távolítsa el a fájlt, és másolja az 512 bájtos fájligazítást.

- Ha hosszú névvel másolt egy VHDX-et (ezek a fájlok nem támogatottak), hibaüzenet jelenik meg a naplókban.

    ![Hiba a **Connect and copy** oldalon található naplókban](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    A következő lépés előtt oldja meg a hibákat.

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Miután a másolási feladat befejeződött, akkor megy **a felkészülés a hajó**.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Heavyvel kapcsolatos témakörökkel ismerkedett meg, például a következőkkel:

> [!div class="checklist"]
> * Előfeltételek áttekintése
> * Csatlakozás a Data Box Heavyhez
> * Adatok másolása a Data Box Heavyre


Folytassa a következő oktatóanyaggal, amelyben megismerheti, hogyan küldheti vissza a Data Box Heavyt a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box Heavy visszaküldése a Microsoftnak](./data-box-heavy-deploy-picked-up.md)

