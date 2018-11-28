---
title: Adatok átvitele az Azure Data Box Gatewayjel | Microsoft Docs
description: Megismerheti, hogyan adhat hozzá megosztásokat a Data Box Gateway eszközön, és hogyan csatlakozhat azokhoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: fa31397e0ecffbd245557a824bdd770724bbc91c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249880"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway-preview"></a>Oktatóanyag: Adatok átvitele az Azure Data Factory Gatewayjel (előzetes verzió)


## <a name="introduction"></a>Bevezetés

Ebből a cikkből megtudhatja, hogyan adhat hozzá megosztásokat a Data Box Gatewayen, és hogyan csatlakozhat azokhoz. A megosztások hozzáadását követően a Data Box Gateway eszköz képes adatokat továbbítani az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

> [!IMPORTANT]
> - A Data Box Gateway előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztásokat ad a Data Box Gatewayhez, ellenőrizze az alábbiakat:

* Üzembe helyezett egy virtuális eszközt, és csatlakozott hozzá [A Data Box Gateway üzembe helyezése a Hyper-V-ben](data-box-gateway-deploy-provision-hyperv.md) vagy [A Data Box Gateway üzembe helyezése a VMware-ben](data-box-gateway-deploy-provision-vmware.md) témakörben leírt módon. 

    A virtuális eszköz aktiválva van az [Azure Data Box Gateway csatlakoztatását és aktiválását](data-box-gateway-deploy-connect-setup-activate.md) ismertető cikkben leírt módon, és készen áll a megosztások létrehozására és az adatátvitelre.


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

1. Lépjen vissza az Azure Portalra. Lépjen a **Minden erőforrás** területre, és keresse meg a Data Box Gateway erőforrást.
    
2. Az erőforrások szűrt listájában válassza ki a Data Box Gateway erőforrást, majd lépjen az **Áttekintés** lapra. Kattintson a **+ Megosztás hozzáadása** gombra az eszköz parancssorán.
   
   ![Megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak. 

   A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.
    
5. Válassza ki a megosztás **típusát**. A típus SMB vagy NFS lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek. 

6. Meg kell adnia egy tárfiókot, ahol a megosztást tárolva lesz. A rendszer a tárfiókban létrehoz egy tárolót a megosztás nevével, ha még nem létezik ilyen. Ha létezik, akkor a rendszer a meglévő tárolót használja. 
    
7. Válassza ki a **tárolási szolgáltatást** a blokkblobok, lapblobok vagy fájlok közül. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben az esetben például azt szeretnénk, hogy az adatok blokkblobokban legyenek tárolva az Azure-ban, ezért a Blokkblob lehetőséget választjuk. Ha a Lapblob lehetőséget választja, biztosítania kell az adatok 512 bájtos igazítását. Érdemes tudni, hogy a VHDX mindig 512 bájtos igazítású.
   
8. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre. 
     
    - **SMB-megosztás létrehozásakor** – A Teljes jogú helyi felhasználó mezőben válasszon az **Új létrehozása** és a **Meglévő használata** lehetőségek közül. Ha új helyi felhasználót hoz létre, adja meg a **felhasználónevet** és a **jelszót**, majd **erősítse meg a jelszót**. Ezzel hozzárendeli az engedélyeket a helyi felhasználóhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.
    
        ![SMB-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Ha a megosztott adatokhoz bejelöli a **Csak olvasási műveletek engedélyezése** lehetőséget, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.
        
    - **NFS-megosztás létrehozásakor** – Meg kell adnia az engedélyezett ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz.

        ![NFS-megosztás hozzáadása](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. A megosztás létrehozásához kattintson a **Létrehozás** parancsra. 
    
    A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást. 
    
    ![Megosztások frissített listája](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

A megosztáshoz való csatlakozáshoz végezze el az alábbi lépéseket a Data Box Gatewayhez csatlakoztatott Windows Server-ügyfélen.


1. Nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ha a rendszer kéri, adja meg a megosztás jelszavát. Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2817 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60' • 
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson az **OK** gombra. Ekkor megnyílik a Fájlkezelő. A létrehozott megosztásoknak meg kell jelenniük mappaként. Válasszon ki és duplán kattintva nyisson meg egy megosztást (mappát) a tartalma megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

Végezze el az alábbi lépéseket a Data Box Edge-hez csatlakoztatott Linux-ügyfélen.

1. Ellenőrizze, hogy az ügyfélen telepítve van-e az NFSv4-ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítését követően az alábbi parancs használatával csatlakoztathatja a Data Box Gateway eszközön létrehozott NFS-fájlmegosztást:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    A csatlakoztatás előtt ellenőrizze, hogy a könyvtárak, amelyek helyi számítógépen csatlakozási pontként fognak szolgálni, már létrejöttek-e, illetve hogy nem tartalmaznak-e fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Gateway eszközön található megosztáshoz. A virtuális eszköz IP-címe `10.10.10.60`, a `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik, a csatlakozási pont pedig a `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> Az előzetes verzióra a következő kikötések vonatkoznak:
> - Miután létrehoz egy fájlt a megosztásokban, a fájl átnevezése nem támogatott. 
> - A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Gatewayjel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz


A következő oktatóanyag azt mutatja be, hogyan felügyelhető a Data Box Gateway.

> [!div class="nextstepaction"]
> [A Data Box Gateway felügyelete a helyi webes felhasználói felületen](https://aka.ms/dbg-docs)


