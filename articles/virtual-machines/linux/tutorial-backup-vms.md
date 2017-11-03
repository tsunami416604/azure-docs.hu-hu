---
title: "Az Azure Linux virtuális gépek biztonsági mentése |} Microsoft Docs"
description: "A Linux virtuális gépek védelme készítésével Azure Backup segítségével."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0e659c1906c99415ab1b53785a606330ef9068c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-linux--virtual-machines-in-azure"></a>Készítsen biztonsági másolatot a Linux virtuális gépek Azure-ban

Adatai védelme érdekében érdemes rendszeres időközönként biztonság mentést végeznie. Az Azure Backup georedundáns helyreállítás tárolók tárolt helyreállítási pontokat hoz létre. Helyreállításakor a helyreállítási pont, az egész virtuális gép vagy csak adott fájlokat is helyreállíthatja. Ez a cikk azt ismerteti, hogyan nginx futó Linux virtuális gép egyetlen fájl visszaállítása. Ha még nem rendelkezik a virtuális gépek használatához, hozhat létre egy a [Linux gyors üzembe helyezés](quick-create-cli.md). Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Készítsen biztonsági másolatot a virtuális gépek
> * Napi biztonsági mentés ütemezése
> * A fájl visszaállítása biztonsági másolatból



## <a name="backup-overview"></a>A biztonsági mentés áttekintése

Ha az Azure biztonsági mentési szolgáltatás biztonsági másolatot, a tartalék mellék időpontban pillanatképének elkészítéséhez váltja ki. Az Azure Backup szolgáltatás használ a _VMSnapshotLinux_ Linux bővítményt. Ha a virtuális gép fut, a virtuális gép első biztonsági mentés során telepítve a bővítmény. Ha a virtuális gép nem fut, a biztonsági mentési szolgáltatás pillanatképet készít a az alapul szolgáló tárolási (mert nem alkalmazás írások végrehajthatók, miközben a virtuális gép le van állítva).

Alapértelmezés szerint Azure biztonsági mentési időt vesz igénybe a fájlrendszer konzisztens biztonsági mentése a Linux virtuális gép, de beállítható érvénybe [alkalmazás konzisztens biztonsági mentését parancsfájl előtti és utáni parancsfájl keretrendszerrel](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). Miután az Azure Backup szolgáltatás a pillanatfelvételt, az adatátvitel a tárolóba. Hatékonyságának maximalizálása érdekében a szolgáltatás azonosítja, és csak az adatok a korábbi biztonsági mentés óta módosult blokkok átvitele.

Ha az adatok átvitele befejeződött, a rendszer eltávolítja a pillanatkép, és egy helyreállítási pontot hoz létre.


## <a name="create-a-backup"></a>Biztonsági mentés létrehozása
Hozzon létre egy egyszerű, ütemezett napi biztonsági mentést egy Recovery Services-tárolóba. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A bal oldali menüben válassza a **Virtuális gépek** elemet. 
3. Válasszon egy virtuális gépet a listából, amelyről biztonsági mentést kíván készíteni.
4. A virtuális gép panelen a a **beállítások** kattintson **biztonsági mentés**. A **biztonságimásolat-készítés engedélyezése** panel nyílik meg.
5. A **Recovery Services-tároló**, kattintson a **hozzon létre új** , és adja meg az új tároló nevét. Egy új tárolót a azonos erőforráscsoport és a virtuális gép jön létre.
6. Kattintson a **biztonsági mentési házirend**. Ehhez a példához hagyja az alapértelmezett beállításokat, és kattintson a **OK**.
7. Az a **biztonságimásolat-készítés engedélyezése** panelen kattintson a **biztonsági mentés engedélyezése**. Ezzel létrehoz egy alapértelmezett ütemezés szerint a napi biztonsági mentéshez.
10. Az első helyreállítási pont létrehozásához a **biztonsági mentés** panelen kattintson **biztonsági mentés most**.
11. Az a **biztonsági mentés most** panelen kattintson a naptár ikonra, válassza ki az elmúlt nap során ez a helyreállítási pont őrzi meg, majd kattintson a havinaptár-vezérlő segítségével **biztonsági mentés**.
12. Az a **biztonsági mentés** a virtuális gép paneljén láthatja, amelyek a teljes helyreállítási pontok száma.

    ![Helyreállítási pontok](./media/tutorial-backup-vms/backup-complete.png)

Az első biztonsági mentés körülbelül 20 percet vesz igénybe. A biztonsági mentés befejezése után ez az oktatóanyag következő része folytassa a műveletet.

## <a name="restore-a-file"></a>Fájl visszaállítása

Ha véletlenül törli vagy módosítja a fájlt, a fájlok helyreállítása használatával állítsa vissza a fájlt a biztonsági mentési tárolóból. A fájlok helyreállítása a helyreállítási pont helyi meghajtóként csatlakoztatni a virtuális gép futó parancsfájlt használ. Ezek a meghajtók marad csatlakoztatott 12 óra, hogy a fájlok másolását a helyreállítási pont, és állítsa vissza őket a virtuális Gépet.  

Ebben a példában megmutatjuk, az alapértelmezett nginx weblap /var/www/html/index.nginx-debian.html helyreállítása. A nyilvános IP-cím, az ebben a példában a virtuális gép *13.69.75.209*. Az IP-cím a virtuálisgép-használatával található:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. A helyi számítógépen nyissa meg egy böngészőt, és írja be a nyilvános IP-címet a virtuális gép alapértelmezett nginx weblap megtekintéséhez.

    ![Alapértelmezett nginx-weblap](./media/tutorial-backup-vms/nginx-working.png)

1. SSH-ból a virtuális Gépet.

    ```bash
    ssh 13.69.75.209
    ```
2. /Var/www/html/index.nginx-debian.html törlése.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. A helyi számítógépen frissítheti a böngésző szerezze meg a CTRL + F5 nem alapértelmezett nginx lap megjelenítéséhez.

    ![Alapértelmezett nginx-weblap](./media/tutorial-backup-vms/nginx-broken.png)
    
1. A helyi számítógépen jelentkezzen be a [Azure-portálon](https://portal.azure.com/).
6. A bal oldali menüben válassza a **Virtuális gépek** elemet. 
7. A listában jelölje ki a virtuális Gépet.
8. A virtuális gép panelen a a **beállítások** kattintson **biztonsági mentés**. A **biztonsági mentés** panel nyílik meg. 
9. A panel tetején menüben válasszon ki **fájlhelyreállítás**. A **fájlhelyreállítás** panel nyílik meg.
10. A **1. lépés: válassza ki a helyreállítási pont**, a legördülő listán válasszon egy helyreállítási pontot.
11. A **2. lépés: Töltse le a parancsprogramot, keresse meg, majd a fájlok helyreállítása**, kattintson a **végrehajtható fájl letöltése** gombra. Mentse a letöltött fájlt a helyi számítógépen.
7. Kattintson a **parancsprogram letöltése** helyileg a parancsfájl-fájl letöltésére.
8. Nyisson meg egy Bash parancssort és írja be a következőt, hogy *Linux_myVM_05-05-2017.sh* a helyes elérési utat és a fájlnév letöltötte, a parancsfájl *azureuser* a felhasználónevet használva a virtuális gép és *13.69.75.209* a virtuális gép nyilvános IP-címmel.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. A helyi számítógépen nyissa meg az SSH-kapcsolatot a virtuális Gépet.

    ```bash
    ssh 13.69.75.209
    ```
    
10. A virtuális gépen, vegye fel a végrehajtási engedélyeket a parancsfájlt.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. A virtuális gépén futtassa a csatlakoztatási fájlrendszer a helyreállítási pontot.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. A parancsfájl kimenetében lehetővé teszi az elérési út a csatlakoztatási pont. A kimeneti ehhez hasonlóan néz ki:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. A virtuális Gépet másolja a nginx alapértelmezett weblap a csatlakoztatási pont vissza, ha törli a fájl a.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. A helyi számítógépen nyissa meg a böngészőlapon ahol csatlakozik a nginx alapértelmezett lap megjeleníti a virtuális IP-címét. Nyomja le a CTRL + F5 csak a böngésző lap frissítése. Most látnia kell, hogy az alapértelmezett lapon ismét működik-e.

    ![Alapértelmezett nginx-weblap](./media/tutorial-backup-vms/nginx-working.png)

18. A helyi számítógépen, térjen vissza az Azure portál és a böngészőlapon **3. lépés: a lemez leválasztása a helyreállítás után** kattintson a **lemez leválasztása** gombra. Ha ezt a lépést, a kapcsolat a csatlakozási pont nem automatikusan Bezárás 12 óra elteltével. E 12 óra elteltével le kell töltenie egy új parancsfájl egy új csatlakoztatási pont létrehozásához.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Készítsen biztonsági másolatot a virtuális gépek
> * Napi biztonsági mentés ütemezése
> * A fájl visszaállítása biztonsági másolatból

Előzetes további virtuális gépek figyelésével kapcsolatos következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitoring.md)

