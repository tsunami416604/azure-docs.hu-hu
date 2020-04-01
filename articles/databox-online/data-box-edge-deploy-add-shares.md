---
title: Oktatóanyag az azure data box edge-del való megosztásokra való adatátvitelhez | Microsoft dokumentumok
description: Megtudhatja, hogyan adhat hozzá megosztásokat a Data Box Edge-eszközön, és hogyan csatlakozhat hozzájuk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 3b1988656e2c15515e121df3ee71e31ce7edd750
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79212949"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Oktatóanyag: Adatok átvitele az Azure Data Box Edge segítségével

Ez az oktatóanyag bemutatja, hogyan adhat hozzá és csatlakozhat megosztásokhoz a Data Box Edge-eszközön. Miután hozzáadta a megosztásokat, a Data Box Edge adatokat továbbíthat az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás a megosztáshoz

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztásokat ad hozzá a Data Box Edge-hez, győződjön meg arról, hogy:

- Telepítette a fizikai eszközt az Azure Data Box Edge telepítése című témakörben leírtak [szerint.](data-box-edge-deploy-install.md)

- Aktiválta a fizikai eszközt a [Csatlakozás, beállítás és az Azure Data Box Edge aktiválása szerint.](data-box-edge-deploy-connect-setup-activate.md)


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához tegye a következő eljárást:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a Data Box Edge erőforrást, majd nyissa meg az **Áttekintés lehetőséget.** A készüléknek online állapotban kell lennie.

   ![Eszköz online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Válassza a **+ Megosztás hozzáadása** lehetőséget az eszköz parancssávján.

   ![Megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. A **Megosztás hozzáadása** ablaktáblán tegye a következő eljárást:

    a. A **Név** mezőben adjon meg egyedi nevet a megosztásnak.  
    A megosztásnévnek csak kisbetűi, számai és kötőjelei lehetnek. 3 és 63 karakter közötti nek kell lennie, és betűvel vagy számmal kell kezdődnie. A kötőjeleket betűnek vagy számnak kell megelőznie és követnie.
    
    b. Válassza ki a megosztás **típusát**.  
    A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos.  
    Attól függően, hogy az SMB vagy az NFS megosztást választja, a többi lehetőség kissé eltér. 

    c. Adjon meg egy tárfiókot, ahol a megosztás lesz lakhelye.

      > [!IMPORTANT]
      > Győződjön meg arról, hogy az Azure Storage-fiók, amely használata nem rendelkezik a nemmódosíthatósági szabályzatok, ha egy Azure Stack Edge vagy Data Box Gateway eszközzel használja. További információt a [Blob Storage-alapú megváltoztathatatlansági házirendek beállítása és kezelése című témakörben talál.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)

    d. A **Storage szolgáltatás** legördülő listájában válassza a **Blokkblob**, **Lapblob**vagy **Fájlok**lehetőséget.  
    A kiválasztott szolgáltatás típusa attól függ, hogy milyen formátumban szeretné használni az adatokat az Azure-ban. Ebben a példában, mivel blokkblobként szeretnénk tárolni az adatokat az Azure-ban, a **Blokkblob**lehetőséget választjuk. Ha a **Lapblob**lehetőséget választja, győződjön meg arról, hogy az adatok 512 bájthoz vannak igazítva. A VHDX például mindig 512 bájtos igazítású.

    e. Hozzon létre egy új blob-tárolót, vagy használjon egy meglévőt a legördülő listából. Blob-tároló létrehozásakor adja meg a tároló nevét. Ha egy tároló még nem létezik, az újonnan létrehozott megosztási névvel a tárfiókban jön létre.

    f. Attól függően, hogy SMB-megosztást vagy NFS-megosztást hozott-e létre, tegye az alábbi lépések egyikét:

    * **SMB-megosztás**: A **Minden jogosultság helyi felhasználója**csoportban válassza **az Új létrehozása vagy** a **Meglévő használata**lehetőséget. Ha új helyi felhasználót hoz létre, adjon meg egy felhasználónevet és egy jelszót, majd erősítse meg a jelszót. Ez a művelet engedélyeket rendel a helyi felhasználóhoz. A megosztásszintű engedélyek módosítása jelenleg nem támogatott.

        Ha bejelöli a **Csak olvasási műveletek engedélyezése jelölőnégyzetet** ehhez a megosztási adatokhoz, megadhatja az írásvédett felhasználókat.

        ![SMB-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)

    * **NFS-megosztás**: Adja meg a megosztáshoz hozzáférő engedélyezett ügyfelek IP-címét.

        ![NFS-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)

4. A megosztás létrehozásához válassza a **Létrehozás** gombot.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás a megadott beállításokkal létrejött, a **Megosztások** csempe frissül, hogy tükrözze az új megosztást.
    

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az utolsó lépésben létrehozott megosztáshoz. Attól függően, hogy SMB-vel vagy NFS-megosztáson van-e, a lépések eltérőek lehetnek.

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Data Box Edge eszközhöz csatlakoztatott Windows Server-ügyfélen a következő parancsokkal csatlakozzon egy SMB-megosztáshoz:


1. A parancsablakba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Amikor a rendszer erre kéri, adja meg a megosztás jelszavát.  
   Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. A billentyűzeten válassza a Windows + R lehetőséget.

4. A **Futtatás** ablakban `\\<device IP address>`adja meg a t, majd kattintson az **OK gombra.**  
   Megnyílik a Fájlkezelő. Most már meg kell tudnia tekinteni a létrehozott megosztásokat mappákként. A Fájlkezelőben kattintson duplán egy megosztásra (mappára) a tartalom megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

A Data Box Edge eszközhöz csatlakoztatott Linux-ügyfélen végezze el a következő eljárást:

1. Győződjön meg arról, hogy az ügyféln telepítve van az NFSv4 ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítése után csatlakoztassa a Data Box Edge eszközén létrehozott NFS-megosztást a következő paranccsal:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Használata `sync` opció szereléskor részvények javítja az átviteli sebesség a nagy fájlokat.
    > A megosztás csatlakoztatása előtt győződjön meg arról, hogy a helyi számítógépen csatlakoztatási pontként használó könyvtárak már létrejöttek. Ezek a könyvtárak nem tartalmazhatnak fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Data Box Edge-eszközön található megosztáshoz. Az eszköz IP-címe: `10.10.10.60`. A `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik. A megosztás csatlakoztatási pontja: `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> A következő kikötések vonatkoznak erre a kiadásra:
> - Miután létrehozott egy fájlt a megosztásban, a fájl átnevezése nem támogatott. 
> - Ha töröl egy fájlt egy megosztásból, azzal nem törli a bejegyzést a tárfiókból.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következő Data Box Edge témaköröket ismerte meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

Ha meg szeretné tudni, hogyan alakíthatja át az adatokat a Data Box Edge használatával, továbbkell lépnie a következő oktatóanyagra:

> [!div class="nextstepaction"]
> [Adatok átalakítása a Data Box Edge segítségével](./data-box-edge-deploy-configure-compute.md)


