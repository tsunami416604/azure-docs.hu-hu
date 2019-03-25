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
ms.openlocfilehash: d930b1db48e3a5c4bda96f0b7d80a9c9f24d53d9
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400644"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Oktatóanyag: Adatok áthelyezése az Azure Data Box Gateway


## <a name="introduction"></a>Bevezetés

Ez a cikk azt ismerteti, hogyan adhat hozzá, és csatlakozhat a Data Box-Gateway-megosztások. Miután hozzáadta a megosztásokat, a Data Box átjáróeszköz küldhetnek adatokat az Azure-bA.

A folyamat elvégzése körülbelül 10 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz


## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztásokat ad a Data Box Gatewayhez, ellenőrizze az alábbiakat:

- A virtuális eszköz kiépítése és az ahhoz kapcsolódó leírt módon a [a Hyper-V Data Box átjáró kiépítése](data-box-gateway-deploy-provision-hyperv.md) vagy [üzembe helyezése VMware-ben Data Box Gateway](data-box-gateway-deploy-provision-vmware.md).

- Miután aktiválta a virtuális eszköz ismertetett [Connect és az Azure Data Box Gateway aktiválása](data-box-gateway-deploy-connect-setup-activate.md).

- Az eszköz készen áll a megosztások létrehozására és az adatátvitelre.

## <a name="add-a-share"></a>Megosztás hozzáadása

Egy megosztás tegye az alábbi eljárás létrehozása:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki a Data Box-átjáró-erőforrást, és folytassa a **áttekintése**. Az eszköz online állapotúnak kell lennie. Válassza ki **+ Hozzáadás megosztás** az eszköz parancssávon.
   
   ![Megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. A **megosztás hozzáadása**, hajtsa végre az alábbi eljárást:

    1. Adjon egy egyedi nevet a megosztásnak. A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat rendelkezhet. A megosztás neve 3 és 63 karakter között kell, és betűvel vagy számmal kezdődhet. A kötőjelek előtt és után csak nem kötőjel karakter állhat.
    
    2. Válassza ki a megosztás **típusát**. A típus SMB vagy NFS lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek.

    3. Adja meg a megosztást tároló tárfiók. Ha egy tároló nem létezik, az újonnan létrehozott megosztás neve az a tárfiók létrejön. Ha a tároló már létezik, a tárolót használja.
    
    4. Válassza ki a **tárolási szolgáltatást** a blokkblobok, lapblobok vagy fájlok közül. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adatok blokkblobokban legyenek tárolva az Azure-ban, ezért a Blokkblob lehetőséget választjuk. Ha a Lapblob lehetőséget választja, biztosítania kell az adatok 512 bájtos igazítását. A VHDX például mindig 512 bájtos igazítású.
   
    5. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre.
     
    - **SMB-megosztás** – a **jogosultság az összes helyi felhasználó**válassza **új létrehozása** vagy **meglévő**. Ha létrehoz egy új helyi felhasználót, adja meg egy **felhasználónév** és **jelszó**, majd **jelszó megerősítése**. Ez a művelet rendel a helyi felhasználói engedélyeket. Miután az engedélyek itt társított, használhatja a Fájlkezelőben módosítani ezeket az engedélyeket.
    
        ![SMB-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Ha **csak olvasási műveletek engedélyezése** jelölőnégyzetet a megosztás adataihoz, megadhatja a csak olvasható felhasználók.
        
    - **Az NFS-megosztások** – adja meg az IP-címeit az engedélyezett ügyfeleknek, amely hozzáféréssel rendelkezik a megosztáshoz.

        ![NFS-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Válassza ki **létrehozás** a megosztás létrehozásához.
    
    Értesítést kap arról, hogy a megosztás létrehozása folyamatban van. A megadott beállításokkal a megosztás létrehozása után a **megosztások** csempére, hogy az új megosztásban frissítéseket.
    
    ![Frissített megosztások csempén](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Mostantól csatlakozhat egy vagy több, amely az előző lépésben létrehozott megosztás. Attól függően, hogy van-e az SMB vagy NFS-megosztás esetén, a lépések eltérőek lehetnek.

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A Windows Server-ügyfél csatlakozik a Data Box Gateway csatlakozhat egy SMB-megosztás a parancsok beírásával:


1. Egy parancsablakban írja be:

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


2. Válassza ki a billentyűzet Windows + r billentyűkombinációt. 
3. Az a **futtatása** ablakban adja meg a `\\<device IP address>` majd **OK**. Megnyílik a Fájlkezelőben. Most már lehet a megosztások, mappák létrehozott megtekintheti. A Fájlkezelőben kattintson duplán egy megosztási (mappa), a tartalom megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

A Linux-ügyfél a Data Box peremhálózati eszköz csatlakozik hajtsa végre az alábbi eljárást:

1. Győződjön meg arról, hogy az ügyfél rendelkezik-e az NFSv4 ügyfél telepítve van. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítését követően az alábbi parancs használatával csatlakoztathatja a Data Box Gateway eszközön létrehozott NFS-fájlmegosztást:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    A csatlakoztatás előtt ellenőrizze, hogy a könyvtárak, amelyek helyi számítógépen csatlakozási pontként fognak szolgálni, már létrejöttek-e, illetve hogy nem tartalmaznak-e fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Gateway eszközön található megosztáshoz. A virtuális eszköz IP-címe `10.10.10.60`, a `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Az alábbi korlátozásokkal erre a kiadásra vonatkoznak:
> - Miután létrehoz egy fájlt a megosztásokban, a fájl átnevezése nem támogatott.
> - A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.
> - Ha használ `rsync` másolhat adatokat, majd `rsync -a` beállítás nem támogatott.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz


A következő oktatóanyag azt mutatja be, hogyan felügyelhető a Data Box Gateway.

> [!div class="nextstepaction"]
> [A Data Box Gateway felügyelete a helyi webes felhasználói felületen](https://aka.ms/dbg-docs)


