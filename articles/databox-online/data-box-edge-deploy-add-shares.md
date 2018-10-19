---
title: Adatok átvitele az Azure Data Box Edge segítségével | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá megosztásokat a Data Box Edge-eszközön, és hogyan csatlakozhat hozzájuk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 61d427c22f2ac57627ac04a91748e4e6cf8c4e55
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165358"
---
# <a name="tutorial-transfer-data-with-azure-data-box-edge-preview"></a>Oktatóanyag: Adatok átvitele az Azure Data Box Edge segítségével (előzetes verzió)

Ebből az oktatóanyagból megtudhatja, hogyan adhat hozzá megosztásokat a Data Box Edge-en, és hogyan csatlakozhat hozzájuk. A megosztások hozzáadását követően a Data Box Edge-eszköz képes adatokat továbbítani az Azure-ba.

A folyamat elvégzése körülbelül 10 percet vesz igénybe. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz

> [!IMPORTANT]
> A Data Box Edge előzetes verzióban érhető el. A megoldás megrendelése és üzembe helyezése előtt tekintse át az [Azure előzetes verziókra vonatkozó szolgáltatási feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt megosztásokat ad a Data Box Edge-hez, győződjön meg az alábbiakról:

* Telepítette a fizikai eszközt a [Data Box Edge üzembe helyezése](data-box-edge-deploy-install.md) szakaszban ismertetett módon. 

    A fizikai eszköz az [Azure Data Box Edge csatlakoztatását és aktiválását](data-box-edge-deploy-connect-setup-activate.md) ismertető szakasz szerint aktiválva van. Az eszköz készen áll a megosztások létrehozására és az adatátvitelre.


## <a name="add-a-share"></a>Megosztás hozzáadása

Megosztás létrehozásához hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

1. Lépjen vissza az Azure Portalra. Lépjen a **Minden erőforrás** területre, és keresse meg a Data Box Edge erőforrást.
    
2. Az erőforrások szűrt listájában válassza ki a Data Box Edge erőforrást, majd lépjen az **Áttekintés** lapra. Kattintson a **+ Megosztás hozzáadása** gombra az eszköz parancssorán.
   
   ![Megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/click-add-share.png)

3. A **Megosztás hozzáadása** területen adja meg a megosztás beállításait. Adjon egy egyedi nevet a megosztásnak. 

   A megosztások nevei csak számokat, kisbetűket és kötőjelet tartalmazhatnak. A megosztás neve 3–63 karakter hosszú lehet, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat.
    
    1. Válassza ki a megosztás **típusát**. A típus SMB vagy NFS lehet. Az alapértelmezett érték az SMB. Ez a szokásos típus Windows-ügyfelekhez, míg az NFS a Linux rendszerű ügyfelekhez használatos. 

    2. Attól függően, hogy az SMB vagy az NFS típust választja, a megjelenő beállítások kis mértékben eltérőek. 

    3. Meg kell adnia egy tárfiókot, ahol a megosztást tárolva lesz. A rendszer a tárfiókban létrehoz egy tárolót a megosztás nevével, ha még nem létezik ilyen. Ha létezik, akkor a rendszer a meglévő tárolót használja. 
    
    4. Válassza ki a **tárolási szolgáltatást** a blokkblobok, lapblobok vagy fájlok közül. A kiválasztott szolgáltatástípustól függ, hogy az Azure milyen formátumban tárolja az adatokat. Ebben a példában például azt szeretnénk, hogy az adatok blokkblobokban legyenek az Azure-ban, ezért a Blokkblob lehetőséget választjuk. Ha a Lapblob lehetőséget választja, biztosítania kell az adatok 512 bájtos igazítását. A VHDX például mindig 512 bájtos igazítású.
   
    5. A következő lépés attól függ, hogy SMB- vagy NFS-megosztást hozunk-e létre. 
     
        - **SMB-megosztás létrehozásakor** – A Teljes jogú helyi felhasználó mezőben válasszon az **Új létrehozása** és a **Meglévő használata** lehetőségek közül. Ha új helyi felhasználót hoz létre, adja meg a **felhasználónevet** és a **jelszót**, majd **erősítse meg a jelszót**. Ezzel hozzárendeli az engedélyeket a helyi felhasználóhoz. A hozzárendelt engedélyeket később a Fájlkezelővel módosíthatja.

            Ha a megosztott adatokhoz bejelöli a **Csak olvasási műveletek engedélyezése** lehetőséget, megadhat csak olvasási jogosultsággal rendelkező felhasználókat.

            ![SMB-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-smb-1.png)
   
        - **NFS-megosztás létrehozásakor** – Meg kell adnia az engedélyezett ügyfelek IP-címeit, akik hozzáférhetnek a megosztáshoz.

            ![NFS-megosztás hozzáadása](./media/data-box-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. A megosztás létrehozásához kattintson a **Létrehozás** parancsra. 
    
    A rendszer megjelenít egy értesítést arról, hogy a megosztás létrehozása folyamatban van. Miután a megosztás létrejött a megadott beállításokkal, a **Megosztások** panel frissül, és megjeleníti az új megosztást. 
    
    ![Megosztások frissített listája](./media/data-box-edge-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Csatlakozás a megosztáshoz

Most már csatlakozhat egy vagy több, az előző lépésben létrehozott megosztáshoz. A lépések eltérők lehetnek annak függvényében, hogy SMB- vagy NFS-megosztást hozunk-e létre. 

### <a name="connect-to-an-smb-share"></a>Csatlakozás SMB-megosztáshoz

A megosztáshoz való csatlakozáshoz végezze el az alábbi lépéseket a Data Box Edge-hez csatlakoztatott Windows Server-ügyfélen.


1. Nyisson meg egy parancsablakot. A parancssorba írja be a következőt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Ha a rendszer kéri, adja meg a megosztás jelszavát. Az alábbiakban látható egy példa a parancs kimenetére.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60': 
    The command completed successfully. 
    
    C: \Users\DataBoxEdgeUser>
    ```   


2. Nyomja le a Windows + R billentyűkombinációt. A **Futtatás** ablakban adja meg a következőt: `\\<device IP address>`. Kattintson az **OK** gombra. Ekkor megnyílik a Fájlkezelő. A létrehozott megosztásoknak meg kell jelenniük mappaként. Válasszon ki és duplán kattintva nyisson meg egy megosztást (mappát) a tartalma megtekintéséhez.
 
    ![Csatlakozás SMB-megosztáshoz](./media/data-box-edge-deploy-add-shares/connect-to-share2.png)

    Az adatokat azok előállításakor a rendszer a megosztásba írja, majd az eszköz továbbítja az adatokat a felhőbe.

### <a name="connect-to-an-nfs-share"></a>Csatlakozás NFS-megosztáshoz

Végezze el az alábbi lépéseket a Data Box Edge-hez csatlakoztatott Linux-ügyfélen.

1. Ellenőrizze, hogy az ügyfélen telepítve van-e az NFSv4-ügyfél. Ha nincs, az NFS-ügyfél telepítéséhez használja az alábbi parancsot:

   `sudo apt-get install nfs-common`

    További információkat az [NFSv4-ügyfél telepítését ismertető](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) cikkben találhat.

2. Az NFS-ügyfél telepítését követően az alábbi parancs használatával csatlakoztathatja a Data Box Edge-eszközön létrehozott NFS-fájlmegosztást:

   `sudo mount <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    A megosztás csatlakoztatása előtt ellenőrizze, hogy a könyvtárak, amelyek helyi számítógépen csatlakozási pontként fognak szolgálni, már létrejöttek-e. Ezek a könyvtárak nem tartalmazhatnak fájlokat vagy almappákat.

    Az alábbi példa bemutatja az NFS-en keresztüli csatlakozást egy Data Box Edge-eszközön található megosztáshoz. Az eszköz IP-címe: `10.10.10.60`. A `mylinuxshare2` megosztás az ubuntuVM virtuális géphez csatlakozik. A megosztás csatlakoztatási pontja: `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> Az előzetes verzióra a következő kikötések vonatkoznak:
> - Miután létrehoz egy fájlt a megosztásokban, a fájl átnevezése nem támogatott. 
> - A fájlok megosztásból való törlése nem törli a bejegyzéseket a tárfiókból.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a Data Box Edge-hez kötődő alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * Megosztás hozzáadása
> * Csatlakozás megosztáshoz


A következő oktatóanyag azt mutatja be, hogyan alakíthat át adatokat a Data Box Edge segítségével.

> [!div class="nextstepaction"]
> [Adatok átalakítása a Data Box Edge segítségével](./data-box-edge-deploy-configure-compute.md)


