---
title: "Fájlok visszaállítása egy virtuális Gépet az Azure Backup szolgáltatással |} Microsoft Docs"
description: "Útmutató: a fájlszintű helyreállítást végrehajtani a biztonsági mentési és helyreállítási szolgáltatások egy Azure virtuális gépen."
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e1bbae56b70c50fcf691db47efd9dc587686e7da
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Fájlok visszaállítása az Azure virtuális géphez
Az Azure Backup georedundáns helyreállítás tárolók tárolt helyreállítási pontokat hoz létre. Helyreállításakor a helyreállítási pont, az egész virtuális gép vagy a fájlokat is helyreállíthatja. Ez a cikk részletezi az egyes fájlok visszaállítása. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Lista, és válassza a helyreállítási pontok
> * A helyreállítási pont csatlakozni a virtuális gépek
> * A helyreállítási pont fájlok visszaállítása

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Telepítése és a parancssori felület helyileg használata mellett dönt, ha ez az oktatóanyag van szükség, hogy futnak-e az Azure parancssori felület 2.0.18 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag egy Linux virtuális Gépet, amely védett az Azure Backup szolgáltatással van szükség. Ezzel szimulálva egy véletlen fájl törlése és a helyreállítási folyamat, egy lap a webkiszolgáló törölnie. Ha egy Linux virtuális Gépet, amely egy webkiszolgálón fut, és a védett az Azure Backup szolgáltatással van szüksége, tekintse meg [készítsen biztonsági másolatot a virtuális gép az Azure-ban a parancssori felület](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>A biztonsági mentés áttekintése
Ha Azure biztonsági másolatot, a biztonsági mentési bővítményt a virtuális Gépre időpontban pillanatképet készít. A tartalék mellék a virtuális Gépen telepítve van, ha az első biztonsági mentés van szükség. Azure biztonsági mentés is is pillanatkép készítése a mögöttes tároló Ha a virtuális gép nem fut, amikor megtörténik a biztonsági mentés.

Alapértelmezés szerint Azure biztonsági mentési időt vesz igénybe a fájlrendszer konzisztens biztonsági mentése. Amennyiben az Azure Backup a pillanatfelvételt, az adatok átvitele történik meg a Recovery Services-tároló. Hatékonyságának maximalizálása érdekében Azure biztonsági mentési azonosítja, és csak az adatok a korábbi biztonsági mentés óta módosult blokkok átvitele.

Ha az adatok átvitele befejeződött, a rendszer eltávolítja a pillanatkép, és egy helyreállítási pontot hoz létre.


## <a name="delete-a-file-from-a-vm"></a>Fájl törlése a virtuális gépek
Ha véletlenül törli vagy módosítja a fájlt, helyreállíthatja a fájlok helyreállítási pontból. Ez a folyamat teszi keresse meg a fájlok biztonsági mentés egy helyreállítási pontot, és visszaállítása csak a szükséges fájlokat. Ebben a példában azt töröl egy fájlt egy webkiszolgálón a fájlszintű helyreállítási folyamat bemutatásához.

1. Csatlakoztassa a virtuális Gépet, szerezze be a virtuális Gépet, az IP-címe [az vm megjelenítése](/cli/azure/vm?view=azure-cli-latest#az_vm_show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Győződjön meg arról, hogy a webhely jelenleg működik, nyissa meg a virtuális gép a nyilvános IP-címet egy webböngészőben. Hagyja nyitva a böngészőablak.

    ![Alapértelmezett NGINX-weblap](./media/tutorial-restore-files/nginx-working.png)

3. Csatlakoztassa a virtuális Gépet az SSH. Cserélje le *publicIpAddress* a nyilvános IP-címmel, amely egy előző parancs beolvasott:

    ```bash
    ssh publicIpAddress
    ```

4. Az alapértelmezett oldal törlése a webkiszolgáló, */var/www/html/index.nginx-debian.html* az alábbiak szerint:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. A böngészőben a weblap frissítése. A webhely már nem betölti a lapon, a következő példában látható módon:

    ![NGINX webhely már nem töltődik alapértelmezett oldal](./media/tutorial-restore-files/nginx-broken.png)

6. Zárja be a virtuális gép SSH-munkamenetet az alábbiak szerint:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Fájl-helyreállítási parancsfájl létrehozása
A fájlok visszaállítására, az Azure Backup biztosít, amely összeköti a helyreállítási pont, mint egy helyi meghajtó a virtuális gépen futtatandó parancsprogram. Keresse meg a helyi meghajtó, a virtuális gépért fájlok visszaállítása, majd válassza le a helyreállítási pont. Azure biztonsági mentés továbbra is fennáll, készítsen biztonsági másolatot az adatairól a hozzárendelt ütemezés és a megőrzési házirend alapján.

1. A lista helyreállítási pontok a virtuális gép használata [az biztonsági mentési recoverypoint lista](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). Ebben a példában azt a legutóbbi helyreállítási pont a virtuális Géphez kiválasztandó nevű *myVM* a védett *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Alkalmazásazonosítójának beszerzéséhez a parancsfájlt, amely csatlakozik, vagy felhőszolgáltatásában, a virtuális géphez a helyreállítási pont [az biztonságimásolat-visszaállítással fájlok csatlakoztatási-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp). A következő példa a parancsfájl beolvassa a virtuális gép nevű *myVM* a védett *myRecoveryServicesVault*.

    Cserélje le *myRecoveryPointName* nevű, a helyreállítási pont az előző parancsban szereplő beszerzett:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    A parancsfájlt a rendszer letölti és egy jelszó jelenik meg, az alábbi példában látható módon:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. A parancsfájl át a virtuális Gépet, használja a biztonságos másolása (SCP). Adja meg a letöltött parancsfájl nevét, és cserélje le *publicIpAddress* a virtuális gép a nyilvános IP-címmel. Győződjön meg arról, a záró `:` a szolgáltatáskapcsolódási pont végén parancsot az alábbiak szerint:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Állítsa vissza a fájlt a virtuális géphez
A helyreállítási másolni a virtuális Gépet, parancsfájl most csatlakozhat a helyreállítási pont, és fájlokat.

1. Csatlakoztassa a virtuális Gépet az SSH. Cserélje le *publicIpAddress* a nyilvános IP-cím a virtuális gép az alábbiak szerint:

    ```bash
    ssh publicIpAddress
    ```

2. A parancsfájl működéséhez engedélyezéséhez adja hozzá a végrehajtási jogosultságokkal rendelkező **chmod**. Adja meg a saját parancsfájl nevét:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Csatlakoztassa a helyreállítási pont, futtassa a parancsfájlt. Adja meg a saját parancsfájl nevét:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    A parancsfájl futtatása, mert éri el a helyreállítási pontot jelszó megadását kéri. Adja meg a jelszót, az előző a kimenetben megjelenő [az biztonságimásolat-visszaállítással fájlok csatlakoztatási-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp) jön létre a helyreállítási parancsprogram-utasítás.

    A parancsfájl kimenetében lehetővé teszi az elérési út a helyreállítási pont. Az alábbi példa kimenetben látható, hogy a helyreállítási pont csatlakoztatva van-e a */home/azureuser/myVM-20170919213536/Volume1*:

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. Használjon **cp** másolni a csatlakoztatott helyreállítási pontból NGINX alapértelmezett weblap vissza az eredeti fájl helyére. Cserélje le a */home/azureuser/myVM-20170919213536/Volume1* csatlakoztatási pontot a saját helyét:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. A böngészőben a weblap frissítése. A webhely most töltődik megfelelően újra, a következő példában látható módon:

    ![NGINX webhely most már megfelelően tölt be](./media/tutorial-restore-files/nginx-restored.png)

7. Zárja be a virtuális gép SSH-munkamenetet az alábbiak szerint:

    ```bash
    exit
    ```

8. A helyreállítási pont leválasztani a virtuális Gépet a [az biztonságimásolat-visszaállítással fájlok leválasztása rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_unmount_rp). Az alábbi példa leválasztja a helyreállítási pont nevű virtuális gép *myVM* a *myRecoveryServicesVault*.

    Cserélje le *myRecoveryPointName* nevű, a helyreállítási pont beolvasott parancsokat:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy helyreállítási pontot egy virtuális Géphez kapcsolódó, és vissza a fájlokat egy webkiszolgálón. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Lista, és válassza a helyreállítási pontok
> * A helyreállítási pont csatlakozni a virtuális gépek
> * A helyreállítási pont fájlok visszaállítása

Továbblépés a következő oktatóanyag tájékozódhat a biztonsági mentése a Windows Server az Azure-bA.

> [!div class="nextstepaction"]
> [Azure biztonsági mentés a Windows Server](tutorial-backup-windows-server-to-azure.md)

