---
title: Adatok átvitele az Azure Data Box Gatewayjel | Microsoft Docs
description: Megismerheti, hogyan adhat hozzá megosztásokat a Data Box Gateway eszközön, és hogyan csatlakozhat azokhoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: alkohli
ms.openlocfilehash: 32466cc0a1ab9b86fc2fb8eb791c232ae13f1c01
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79213572"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Oktatóanyag: Adatok átvitele az Azure Data Box Gateway segítségével


## <a name="introduction"></a>Introduction (Bevezetés)

Ez a cikk azt ismerteti, hogyan adhat hozzá megosztásokat az adatdoboz-átjárón, és hogyan csatlakozhat azokhoz. Miután hozzáadta a megosztásokat, a Data Box Gateway eszköz adatokat továbbíthat az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztásokat ad a Data Box Gatewayhez, ellenőrizze az alábbiakat:

- Kiépített egy virtuális eszközt, és a Hyper-V data box átjáró kiépítése vagy [a Virtuálisgép-szoftver adatdoboz-átjáró kiépítése](data-box-gateway-deploy-provision-vmware.md)című témakörben részletezett módon csatlakozott hozzá. [Provision a Data Box Gateway in Hyper-V](data-box-gateway-deploy-provision-hyperv.md)

- Aktiválta a Csatlakozás és [az Azure Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)szolgáltatásban leírt virtuális eszközt.

- Az eszköz készen áll a megosztások létrehozására és az adatátvitelre.

## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához tegye a következő eljárást:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a Data Box Gateway erőforrást, majd nyissa **meg az Áttekintés lehetőséget.** A készüléknek online állapotban kell lennie. Válassza a **+ Megosztás hozzáadása** lehetőséget az eszköz parancssávján.
   
   ![Megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. A **Megosztás hozzáadása területen**tegye a következő eljárást:

    1. Adjon egy egyedi nevet a megosztásnak. A megosztásneveknek csak kisbetűk, számok és kötőjelek lehetnek. A megosztási névnek 3 és 63 karakter közötti hosszúnak kell lennie, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.
    
    2. Válassza ki a megosztás **típusát**. A típus SMB vagy NFS lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

    3. Adjon meg egy tárfiókot, ahol a megosztás lesz lakhelye. Ha egy tároló még nem létezik, az újonnan létrehozott megosztási névvel a tárfiókban jön létre. Ha a tároló már létezik, a rendszer ezt a tárolót használja.
       > [!IMPORTANT]
       > Győződjön meg arról, hogy az Azure Storage-fiók, amely használata nem rendelkezik a nemmódosíthatósági szabályzatok, ha egy Azure Stack Edge vagy Data Box Gateway eszközzel használja. További információt a [Blob Storage-alapú megváltoztathatatlansági házirendek beállítása és kezelése című témakörben talál.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage)
    
    4. Válassza ki a **tárolási szolgáltatást** a blokkblobok, lapblobok vagy fájlok közül. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adatok blokkblobokban legyenek tárolva az Azure-ban, ezért a Blokkblob lehetőséget választjuk. Ha a Lapblob lehetőséget választja, biztosítania kell az adatok 512 bájtos igazítását. A VHDX például mindig 512 bájtos igazítású.
   
    5. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
     
    - **SMB-megosztás** – Az **Összes jogosultság helyi felhasználója**területen válassza **az Új létrehozása vagy** a **Meglévő használata**lehetőséget. Ha új helyi felhasználót hoz létre, adjon meg egy **felhasználónevet** és **egy jelszót,** majd **erősítse meg a jelszót.** Ez a művelet az engedélyeket a helyi felhasználóhoz rendeli. A megosztásszintű engedélyek módosítása jelenleg nem támogatott.
    
        ![SMB-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Ha bejelöli a **Csak olvasási műveletek engedélyezése** jelölőnégyzetet ehhez a megosztási adathoz, megadhatja az írásvédett felhasználókat.
        
    - **NFS-megosztás** – Adja meg a megosztáshoz hozzáférő engedélyezett ügyfelek IP-címét.

        ![NFS-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. A megosztás létrehozásához válassza a **Létrehozás** gombot.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás a megadott beállításokkal létrejött, a **Megosztások** csempe frissül, hogy tükrözze az új megosztást.
    
    ![Frissített megosztások csempe](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az utolsó lépésben létrehozott megosztáshoz. Attól függően, hogy SMB-vel vagy NFS-megosztáson van-e, a lépések eltérőek lehetnek.

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Data Box Gateway-hez csatlakoztatott Windows Server-ügyfélen a következő parancsokkal csatlakozzon egy SMB-megosztáshoz:


1. A parancsablakba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ha a rendszer kéri, adja meg a megosztás jelszavát. Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. A billentyűzeten válassza a Windows + R lehetőséget. 
3. A **Futtatás** ablakban `\\<device IP address>` adja meg a lehetőséget, majd kattintson az **OK gombra.** Megnyílik a Fájlkezelő. Most már meg kell tudnia tekinteni a létrehozott megosztásokat mappákként. A Fájlkezelőben kattintson duplán egy megosztásra (mappára) a tartalom megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

A Data Box Edge eszközhöz csatlakoztatott Linux-ügyfélen végezze el a következő eljárást:

1. Győződjön meg arról, hogy az ügyféln telepítve van az NFSv4 ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítését követően az alábbi parancs használatával csatlakoztathatja a Data Box Gateway eszközön létrehozott NFS-fájlmegosztást:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    A csatlakoztatás előtt ellenőrizze, hogy a könyvtárak, amelyek helyi számítógépen csatlakozási pontként fognak szolgálni, már létrejöttek-e, illetve hogy nem tartalmaznak-e fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Gateway eszközön található megosztáshoz. A virtuális eszköz IP-címe `10.10.10.60`, a `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> A következő kikötések vonatkoznak erre a kiadásra:
> - Miután létrehozott egy fájlt a megosztásokban, a fájl átnevezése nem támogatott.
> - A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.
> - Ha `rsync` adatok másolásával `rsync -a` történik, a beállítás nem támogatott.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz


A következő oktatóanyag azt mutatja be, hogyan felügyelhető a Data Box Gateway.

> [!div class="nextstepaction"]
> [A Data Box Gateway felügyelete a helyi webes felhasználói felületen](https://aka.ms/dbg-docs)


