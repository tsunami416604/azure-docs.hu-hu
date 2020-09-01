---
title: Oktatóanyag az adatok megosztásokhoz való továbbításához Azure Stack Edge GPU-val | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá és csatlakozhat a megosztásokhoz Azure Stack Edge GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 4d7453ba34a7bc1dd26d0201f604c9028974c1a2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268927"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-gpu"></a>Oktatóanyag: adatok átvitele megosztásokon keresztül Azure Stack Edge GPU-val

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez az oktatóanyag ismerteti, hogyan adhat hozzá és csatlakozhat a Azure Stack Edge-eszközön található megosztásokhoz. A megosztások hozzáadása után Azure Stack Edge adatátvitelt hajthat végre az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Megosztás hozzáadása
> * Csatlakozás a megosztáshoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené a megosztások hozzáadását Azure Stack Edge-hez, ügyeljen rá, hogy:

* Telepítette a fizikai eszközt a [Azure stack Edge telepítése](azure-stack-edge-gpu-deploy-install.md)című témakörben leírtak szerint.

* Aktiválta a fizikai eszközt az [Azure stack Edge aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.

## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő eljárást:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Azure stack Edge-erőforrást, majd lépjen az **Áttekintés**elemre. Az eszköznek online állapotban kell lennie.

   ![Eszköz online](./media/azure-stack-edge-j-series-deploy-add-shares/device-online-1.png)

2. Válassza a **+ megosztás hozzáadása** lehetőséget az eszköz parancssáv-sávján.

   ![Megosztás hozzáadása](./media/azure-stack-edge-j-series-deploy-add-shares/select-add-share-1.png)

3. A **megosztás hozzáadása** panelen hajtsa végre a következő eljárást:

    a. A **név** mezőben adjon meg egy egyedi nevet a megosztáshoz.  
    A megosztás neve csak betűket, számokat és kötőjeleket tartalmazhat. 3 – 63 karakter hosszúnak kell lennie, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt betűnek vagy számnak kell lennie, és utána kell következnie.
    
    b. Válassza ki a megosztás **típusát**.  
    A típus **SMB** vagy **NFS** lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos.  
    Attól függően, hogy az SMB-vagy NFS-megosztásokat választja, a többi lehetőség némileg eltérő lehet. 

    c. Adja meg azt a Storage-fiókot, amelyben a megosztás található.

    d. A **tárolási szolgáltatás** legördülő listában válassza a blob, **oldal blob**vagy **fájlok** **letiltása**lehetőséget.  
    A kiválasztott szolgáltatás típusa attól függ, hogy milyen formátumban szeretné használni az Azure-ban az adatok használatát. Ebben a példában, mert az adatblokk-blobokat az Azure-ban szeretnénk tárolni, válassza a **blob letiltása**lehetőséget. Ha kijelöli az **oldal blobot**, ügyeljen arra, hogy az adatai 512 bájtra legyenek igazítva. A VHDX például mindig 512 bájtos igazítású.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a használt Azure Storage-fiók nem rendelkezik módosíthatatlansági-házirendekkel, ha Azure Stack peremhálózati vagy Data Box Gateway eszközzel használja. További információ: [módosíthatatlansági-szabályzatok beállítása és kezelése a blob Storage-hoz](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage).

    e. Hozzon létre egy új BLOB-tárolót, vagy használjon egy meglévőt a legördülő listából. BLOB-tároló létrehozásakor adja meg a tároló nevét. Ha egy tároló még nem létezik, a rendszer létrehozza a Storage-fiókban az újonnan létrehozott megosztási névvel.
   
    f. Attól függően, hogy létrehozott-e SMB-megosztást vagy NFS-megosztást, hajtsa végre a következő lépések egyikét: 
     
    - **SMB-megosztás**: **az összes jogosultság helyi felhasználó**területen válassza az **új létrehozása** vagy a **meglévő használata**lehetőséget. Ha új helyi felhasználót hoz létre, adjon meg egy felhasználónevet és egy jelszót, majd erősítse meg a jelszót. Ez a művelet engedélyeket rendel a helyi felhasználóhoz. Miután hozzárendelte az engedélyeket, a fájlkezelővel módosíthatja azokat.
    Ha bejelöli a **csak olvasási műveletek engedélyezése** jelölőnégyzetet ehhez a megosztási adatokhoz, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
    
        ![SMB-megosztás hozzáadása](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-megosztás**: adja meg a megosztást elérő engedélyezett ügyfelek IP-címeit.

        ![NFS-megosztás hozzáadása](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-nfs-1.png)
   
4. A megosztás létrehozásához válassza a **Létrehozás** lehetőséget.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. Miután létrehozta a megosztást a megadott beállításokkal, a **megosztások** csempe frissül, hogy tükrözze az új megosztást.
    

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az utolsó lépésben létrehozott megosztáshoz. Attól függően, hogy van-e SMB vagy NFS-megosztás, a lépések változhatnak.

Első lépésként győződjön meg arról, hogy az eszköz neve feloldható az SMB-vagy NFS-megosztás használata esetén.

### <a name="modify-host-file-for-name-resolution"></a>A gazdagép fájljának módosítása névfeloldáshoz

Ekkor hozzáadja az eszköz IP-címét és az eszköz helyi webes FELÜLETén megadott felhasználóbarát nevet a következőhöz:

- A gazdagép fájlja az ügyfélen, vagy
- A gazdagép fájlja a DNS-kiszolgálón

> [!IMPORTANT]
> Azt javasoljuk, hogy módosítsa a gazdagép fájlját a DNS-kiszolgálón az eszköz nevének feloldásához.

Az eszközhöz való kapcsolódáshoz használt Windows-ügyfélen hajtsa végre a következő lépéseket:

1. Indítsa el a **jegyzettömböt** rendszergazdaként, majd nyissa meg a következő helyen található **hosts** fájlt: `C:\Windows\System32\Drivers\etc` .

    ![Windows Intéző – fájl](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-1.png)


2. Adja hozzá a következő bejegyzést a **hosts** -fájlhoz az eszköz megfelelő értékei helyett: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    Az eszköz IP-címét a **hálózatról** , az eszköz rövid nevét pedig a helyi webes felhasználói felületen található **eszköz** oldaláról szerezheti be. A Hosts fájl következő képernyőképe a bejegyzést jeleníti meg:

    ![Windows Intéző – fájl](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

Az Azure Stack Edge-eszközhöz csatlakoztatott Windows Server-ügyfélen a parancsok beírásával kapcsolódjon az SMB-megosztáshoz:


1. A parancssorba írja be a következőt:

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > Csak az eszköz nevével csatlakozhat egy SMB-megosztáshoz, és nem az eszköz IP-címén keresztül.

2. Ha a rendszer erre kéri, adja meg a megosztás jelszavát.  
   Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. A billentyűzeten válassza a Windows + R lehetőséget.

4. A **Futtatás** ablakban adja meg a `\\<device name>` elemet, majd kattintson az **OK gombra**.  

    ![Windows Futtatás párbeszédpanel](media/azure-stack-edge-j-series-deploy-add-shares/run-window-1.png)

   Megnyílik a fájlkezelő. Ekkor meg kell tudnia tekinteni a mappákként létrehozott megosztásokat. A Fájlkezelőben kattintson duplán egy megosztásra (mappára) a tartalom megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/azure-stack-edge-j-series-deploy-add-shares/file-explorer-smbshare-1.png)

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

Az Azure Stack Edge-eszközhöz csatlakoztatott Linux-ügyfélen hajtsa végre a következő eljárást:

1. Győződjön meg arról, hogy az ügyfélen telepítve van a Nfsv4 névleképezője-ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/NFSv4Howto) cikkben találhat.

2. Az NFS-ügyfél telepítése után csatlakoztassa a Azure Stack Edge-eszközön létrehozott NFS-megosztást a következő parancs használatával:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    Az eszköz IP-címe a helyi webes felhasználói felület **hálózat** lapjáról kérhető le.

    > [!IMPORTANT]
    > Ha a `sync` csatlakoztatási megosztások növelik a nagy méretű fájlok átvitelének sebességét, a lehetőség használata.
    > A megosztás csatlakoztatása előtt győződjön meg arról, hogy a helyi számítógépen csatolásiként szolgáló könyvtárak már létre lettek hozva. Ezek a címtárak nem tartalmazhatnak fájlokat vagy almappákat.

    Az alábbi példa bemutatja, hogyan csatlakozhat NFS-en keresztül az Azure Stack Edge-eszközön található megosztáshoz. Az eszköz IP-címe: `10.10.10.60`. A `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik. A megosztás csatlakoztatási pontja: `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> A következő kikötések alkalmazhatók erre a kiadásra:
> - Miután létrehozta a fájlt a megosztásban, a fájl átnevezése nem támogatott. 
> - Egy fájl megosztásból való törlése nem törli a bejegyzést az Azure Storage-fiókban.
> - Ha az `rsync` NFS-en keresztül történő másolásra használja, használja a `--inplace` jelzőt. 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az alábbi Azure Stack Edge-témaköröket:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

Ha szeretné megtudni, hogyan alakíthatja át adatait Azure Stack Edge használatával, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Az adatátalakítás Azure Stack Edge-sel](./azure-stack-edge-j-series-deploy-configure-compute.md)


