---
title: 'Oktatóanyag: adatok másolása az Azure Data Box nehéz NFS-n keresztül |} A Microsoft Docs'
description: Ismerje meg, hogyan másolhat adatokat az Azure Data Box nehéz NFS-n keresztül
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 4361cee3d07408c3abb5031d2ab18c15c92c5e0a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595748"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Oktatóanyag: Adatok másolása az Azure Data Box nehéz NFS-n keresztül

Ez az oktatóanyag leírja a csatlakozás és adatok másolása a helyi webes felhasználói Felületét, hogy az Azure Data Box nehéz a gazdaszámítógépet az.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozhat a Data Box (nagy erőforrásigényű)
> * Másolja az adatokat a Data Box (nagy erőforrásigényű)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejeződött a [oktatóanyag: Állítsa be az Azure Data Box nehéz](data-box-heavy-deploy-set-up.md).
2. A Data Box nehéz kapott, és a rendelés állapota a portálon **kézbesítések**.
3. Másolja át a Data Box nehéz kívánt adatokat a gazdaszámítógép rendelkezik. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-heavy-system-requirements.md) kell futnia;
    - egy nagy sebességű hálózathoz kell csatlakoznia. A leggyorsabb másolási megbízhatóbbak két 40-GbE kapcsolatot (egy csomópontonkénti), amellyel párhuzamosan. Ha nincs elérhető kapcsolat 40-GbE, javasoljuk, hogy legalább két 10-GbE kapcsolatot (egy csomópontonkénti). 

## <a name="connect-to-data-box-heavy"></a>Csatlakozhat a Data Box (nagy erőforrásigényű)

A kiválasztott tárfiók alapján a Data Box nehéz hoz létre, akár:
- Három megosztás minden társított tárfiókhoz, GPv1-hez és GPv2-höz.
- Egy prémium szintű Storage-megosztást.
- Blob storage-fiók egy megosztást.

Az eszköz a csomópontok mindkét ezeken a megosztásokon jönnek létre.

A block blob és az oldal blob megosztások:
- Az első szintű entitások tárolói.
- Második szintű entitások blobok legyenek.

A fájlmegosztások az Azure Files számára:
- Az első szintű entitások megosztások.
- Második szintű entitásokat a fájlok.

Az alábbi táblázat az UNC elérési utat a megosztásokat, ahol az adatok feltöltése a Data Box (nagy erőforrásigényű) és az Azure Storage elérési út URL. Az Azure Storage elérési út utolsó URL-cím származtatható megosztás UNC elérési útját.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Az Azure Block blobs | <li>Megosztás UNC elérési útja: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-lapblobok  | <li>Megosztás UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Megosztás UNC elérési útja: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Az Azure Storage URL-címe: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Ha egy Linuxos gazdagép számítógépet használ, a következő lépésekkel konfigurálja az eszközt az NFS-ügyfelek hozzáférésének engedélyezéséhez.

1. Adja meg azon ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz. A helyi webes felületen lépjen a **Connect and copy** (Kapcsolódás és másolás) lapra. Az **NFS settings** (NFS-beállítások) pontban kattintson az **NFS client access** (NFS-ügyfélhozzáférés) lehetőségre. 

    ![NFS-ügyfélhozzáférés konfigurálása 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Adja meg az NFS-ügynök IP-címét, és kattintson az **Add** (Hozzáadás) gombra. Ezt a lépést megismételve további NFS-ügyfeleket is konfigurálhat. Kattintson az **OK** gombra.

    ![NFS-ügyfélhozzáférés konfigurálása 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Győződjön meg arról, hogy a Linux gazdagépen az NFS-ügyfél [támogatott verziója](data-box-system-requirements.md) van telepítve. Használja a Linux-disztribúciónak megfelelő verziót. 

3. Az NFS-ügyfél telepítését követően az alábbi paranccsal csatlakoztathatja a Data Box-eszközön található NFS-megosztást:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Az alábbi példa bemutatja, hogyan megosztáshoz való csatlakozáshoz az NFS-n keresztül a Data Box (nagy erőforrásigényű). A Data Box (nagy erőforrásigényű) IP-cím `10.161.23.130`, a megosztás `Mystoracct_Blob` van csatlakoztatva a ubuntuVM, csatlakoztatási pont alatt `/home/databoxheavyubuntuhost/databoxheavy`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    A Mac-ügyfelek esetében a következő hozzáadása egy további lehetőség lesz szüksége: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. Blokkblob típusú a mappában létrehozott, és a blob megosztások lap egy tárolóban, amelyhez data nahrávají blobként jelöli. Nem lehet másolni a fájlokat közvetlenül a *legfelső szintű* mappát a storage-fiókban.

## <a name="copy-data-to-data-box-heavy"></a>Másolja az adatokat a Data Box (nagy erőforrásigényű)

Miután csatlakozott a Data Box nehéz megosztásokra, a következő lépés az adatok másolása. Mielőtt elkezdené az adatok másolását, tekintse át az alábbiakat:

- Győződjön meg arról, hogy az adatokat a helyes adatformátumnak megfelelő megosztásokba másolja. A blokkblobadatokat például másolja a blokkbloboknak fenntartott megosztásba. Másolja a VHD-k lapblobok. Ha az adatok formátuma nem egyezik a megfelelő megosztástípussal, akkor egy későbbi lépés során az Azure-ba történő adatfeltöltés sikertelen lesz.
-  Adatok másolása során biztosítása érdekében, hogy megfelel-e az adatok mérete a méretbeli korlátokat ismertetett a [az Azure storage és a Data Box nehéz korlátok](data-box-heavy-limits.md). 
- Ha a Data Box nehéz kívül más alkalmazás egyidejűleg fel az adatokat, amelyek feltöltése folyamatban van a Data Box nehéz, majd emiatt feltöltési feladat hibák és az adatok sérülését.
- Azt javasoljuk, hogy ne használjon egyidejűleg SMB-t és NFS-t az Azure-ban, illetve ne másolja ugyanazokat az adatokat ugyanarra a célhelyre. Ilyen esetekben a végeredmény nem garantálható.
- **Mindig hozzon létre egy mappát azokhoz a fájlokhoz, amelyeket másolni szeretne a megosztás alatt, majd másolja a fájlokat a létrehozott mappába**. Blokkblob típusú a mappában létrehozott, és a blob megosztások lap egy tárolóban, amelyhez data nahrávají blobként jelöli. Nem lehet másolni a fájlokat közvetlenül a *legfelső szintű* mappát a storage-fiókban.
- Ha a fürtjét a kis-és nagybetűket könyvtár- és keresztneveket az NFS-megosztások az NFS a Data Box (nagy erőforrásigényű): 
    - Az esetben a rendszer megőrzi a nevében.
    - A fájlok és nagybetűk nincsenek megkülönböztetve.
    
    Például, ha a Másolás `SampleFile.txt` és `Samplefile.Txt`, az eset megőrzi a nevét, ha az eszközre másolni, de a második fájl felülírja az elsőt, mivel ezek számítanak ugyanazt a fájlt.


Linux rendszerű gazdagép esetében használjon egy, a Robocopyhoz hasonló másolási segédprogramot. Ilyen például az [rsync](https://rsync.samba.org/), a [FreeFileSync](https://www.freefilesync.org/), a [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) vagy az [Ultracopier](https://ultracopier.first-world.info/).  

A `cp` parancs az egyik legjobb választás a könyvtárak másolására. A parancs használatáról [a cp tájékoztató oldalain](http://man7.org/linux/man-pages/man1/cp.1.html) talál további információt.

Amennyiben az rsyncet használja többszálas másoláshoz, a következő irányelveket kell betartania:

 - Telepítse a **CIFS Utils** vagy az **NFS Utils** csomagot, attól függően, hogy a Linux-ügyfél milyen fájlrendszert használ.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Telepítse az **Rsyncet** és a **Parallelt** (a Linux elosztott verziójától függően).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Hozzon létre egy csatlakozási pontot.

    `sudo mkdir /mnt/databoxheavy`

 - Csatlakoztassa a kötetet.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Tükrözze a mappa könyvtárstruktúráját.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Másolja át a fájlokat. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     A j a párhuzamos folyamatok számát jelöli, X = párhuzamos példányok száma

     Azt javasoljuk, hogy kezdetben 16 párhuzamos példánnyal dolgozzon, és az elérhető erőforrásoknak megfelelően növelje a szálak számát.

> [!IMPORTANT]
> A következő Linux fájltípusok nem támogatottak: szimbolikus hivatkozásokat, karakter fájlok, blokk-fájlok, sockets és csövek. Az ilyen során fellépő hibák eredményez a **szállításra való** . lépés.

Nyissa meg a célmappát a másolt fájlok megtekintéséhez és ellenőrzéséhez. Ha hibába ütközik a másolási folyamat során, töltse le a hibafájlokat a hibaelhárításhoz. További információkért lásd: [adatmásolás a Data Box nehéz során hiba történt a naplók megtekintéséhez](data-box-logs.md#view-error-log-during-data-copy). Adatok másolása során hibák részletes listájáért lásd: [Data Box nehéz hibaelhárítása problémák](data-box-troubleshoot.md).

Az adatok integritásának biztosítása érdekében az ellenőrzőösszeg kiszámítására beágyazva, az adatok másolása közben kerül sor. A másolás befejezése után ellenőrizze, hogy mekkora a felhasznált és a szabad tárhely az eszközén.
    
   ![A szabad és a felhasznált tárhely ellenőrzése az irányítópulton](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerkedett az Azure Data Box nehéz témakörök például:

> [!div class="checklist"]
> * Előfeltételek
> * Csatlakozhat a Data Box (nagy erőforrásigényű)
> * Másolja az adatokat a Data Box (nagy erőforrásigényű)


Folytassa a következő oktatóanyaggal, megtudhatja, hogyan tehetnek a Data Box elküldje a Microsoftnak.

> [!div class="nextstepaction"]
> [Az Azure Data Box (nagy erőforrásigényű) a Microsoft szállításra](./data-box-heavy-deploy-picked-up.md)

