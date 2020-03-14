---
title: Oktatóanyag az adatok megosztásokhoz való továbbításához Azure Data Box Edge használatával | Microsoft Docs
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79212949"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge"></a>Oktatóanyag: adatok átvitele Azure Data Box Edge

Ez az oktatóanyag azt ismerteti, hogyan lehet hozzáadni és csatlakozni a Data Box Edge eszközön lévő megosztásokhoz. A megosztások hozzáadása után Data Box Edge az adatok átvitelét az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás a megosztáshoz

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené a megosztások hozzáadását a Data Box Edgehoz, győződjön meg a következőket:

- Telepítette a fizikai eszközt a következő témakörben leírtak szerint: [Install Azure Data Box Edge](data-box-edge-deploy-install.md).

- Aktiválta a fizikai eszközt a következő témakörben leírtak szerint: [Azure Data Box Edge csatlakoztatása, beállítása és aktiválása](data-box-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő eljárást:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Data Box Edge erőforrást, majd lépjen az **Áttekintés**elemre. Az eszköznek online állapotban kell lennie.

   ![Eszköz online](./media/data-box-edge-deploy-add-shares/device-online-1.png)

2. Válassza a **+ megosztás hozzáadása** lehetőséget az eszköz parancssáv-sávján.

   ![Megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/select-add-share-1.png)

3. A **megosztás hozzáadása** panelen hajtsa végre a következő eljárást:

    a. A **név** mezőben adjon meg egy egyedi nevet a megosztáshoz.  
    A megosztás neve csak kisbetűket, számokat és kötőjeleket tartalmazhat. 3 – 63 karakter hosszúnak kell lennie, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt betűnek vagy számnak kell lennie, és utána kell következnie.
    
    b. Válassza ki a megosztás **típusát**.  
    A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos.  
    Attól függően, hogy az SMB-vagy NFS-megosztásokat választja, a többi lehetőség némileg eltérő lehet. 

    c. Adja meg azt a Storage-fiókot, amelyben a megosztás található.

      > [!IMPORTANT]
      > Győződjön meg arról, hogy a használt Azure Storage-fiók nem rendelkezik módosíthatatlansági-házirendekkel, ha Azure Stack peremhálózati vagy Data Box Gateway eszközzel használja. További információ: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

    d. A **tárolási szolgáltatás** legördülő listában válassza a blob, **oldal blob**vagy **fájlok** **letiltása**lehetőséget.  
    A kiválasztott szolgáltatás típusa attól függ, hogy milyen formátumban szeretné használni az Azure-ban az adatok használatát. Ebben a példában, mert az adatblokk-blobokat az Azure-ban szeretnénk tárolni, válassza a **blob letiltása**lehetőséget. Ha kijelöli az **oldal blobot**, ügyeljen arra, hogy az adatai 512 bájtra legyenek igazítva. A VHDX például mindig 512 bájtos igazítású.

    e. Hozzon létre egy új BLOB-tárolót, vagy használjon egy meglévőt a legördülő listából. BLOB-tároló létrehozásakor adja meg a tároló nevét. Ha egy tároló még nem létezik, a rendszer létrehozza a Storage-fiókban az újonnan létrehozott megosztási névvel.

    f. Attól függően, hogy létrehozott-e SMB-megosztást vagy NFS-megosztást, hajtsa végre a következő lépések egyikét:

    * **SMB-megosztás**: **az összes jogosultság helyi felhasználó**területen válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. Ha új helyi felhasználót hoz létre, adjon meg egy felhasználónevet és egy jelszót, majd erősítse meg a jelszót. Ez a művelet engedélyeket rendel a helyi felhasználóhoz. A megosztási szintű engedélyek módosítása jelenleg nem támogatott.

        Ha bejelöli a **csak olvasási műveletek engedélyezése** jelölőnégyzetet ehhez a megosztási adatokhoz, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.

        ![SMB-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)

    * **NFS-megosztás**: adja meg a megosztást elérő engedélyezett ügyfelek IP-címeit.

        ![NFS-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)

4. A megosztás létrehozásához válassza a **Létrehozás** lehetőséget.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** csempe frissül, hogy tükrözze az új megosztást.
    

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az utolsó lépésben létrehozott megosztáshoz. Attól függően, hogy van-e SMB vagy NFS-megosztás, a lépések változhatnak.

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Data Box Edge eszközhöz csatlakoztatott Windows Server-ügyfélen a következő parancsok megadásával csatlakozhat egy SMB-megosztáshoz:


1. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Ha a rendszer erre kéri, adja meg a megosztás jelszavát.  
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

4. A **Futtatás** ablakban adja meg a `\\<device IP address>`, majd kattintson az **OK gombra**.  
   Megnyílik a fájlkezelő. Ekkor meg kell tudnia tekinteni a mappákként létrehozott megosztásokat. A Fájlkezelőben kattintson duplán egy megosztásra (mappára) a tartalom megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

A Data Box Edge eszközhöz csatlakoztatott Linux-ügyfélen hajtsa végre a következő eljárást:

1. Győződjön meg arról, hogy az ügyfélen telepítve van a Nfsv4 névleképezője-ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítése után csatlakoztassa a Data Box Edge eszközön létrehozott NFS-megosztást a következő parancs használatával:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > `sync` lehetőség használata, ha a csatlakoztatási megosztások növelik a nagy méretű fájlok átvitelének sebességét.
    > A megosztás csatlakoztatása előtt győződjön meg arról, hogy a helyi számítógépen csatolásiként szolgáló könyvtárak már létre lettek hozva. Ezek a címtárak nem tartalmazhatnak fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Data Box Edge-eszközön található megosztáshoz. Az eszköz IP-címe: `10.10.10.60`. A `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik. A megosztás csatlakoztatási pontja: `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> A következő kikötések alkalmazhatók erre a kiadásra:
> - Miután létrehozta a fájlt a megosztásban, a fájl átnevezése nem támogatott. 
> - Egy fájl megosztásból való törlése nem törli a bejegyzést a Storage-fiókban.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következő Data Box Edge témaköröket:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

Ha szeretné megtudni, hogyan alakíthatja át adatait Data Box Edge használatával, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Adatok átalakítása a Data Box Edge segítségével](./data-box-edge-deploy-configure-compute.md)


