---
title: Fájlok megőrzése Azure Cloud Shellban | Microsoft Docs
description: Útmutató a Azure Cloud Shell fájlok megőrzéséhez.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: f60125123d019cbfa93bfc1b06da7ac90b54e311
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742038"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>A Cloud Shell Storage működése 
A Cloud Shell a következő módszerekkel is megőrzi a fájlokat: 
* Hozzon létre egy lemezképet `$Home` a címtárból, hogy az a könyvtárban található összes tartalmat megőrzi. A rendszer a lemezképet a megadott fájlmegosztást `acc_<User>.img` a (z) helyen `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`menti, és automatikusan szinkronizálja a módosításokat. 
* A megadott fájlmegosztás `clouddrive` `$Home` csatlakoztatása a közvetlen fájlmegosztás-interakcióhoz a címtárban. `/Home/<User>/clouddrive`leképezve `fileshare.storage.windows.net/fileshare`a következőre:.
 
> [!NOTE]
> A `$Home` címtárban lévő összes fájl, például az ssh-kulcsok megmaradnak a felhasználói lemezképben, amelyet a csatlakoztatott fájlmegosztás tárol. Alkalmazza az ajánlott eljárásokat, ha a címtárban `$Home` és a csatlakoztatott fájlmegosztás adataiban marad.

## <a name="bash-specific-commands"></a>Bash-specifikus parancsok

### <a name="use-the-clouddrive-command"></a>A `clouddrive` parancs használata
A Cloud SHELL bash használatával futtathatja a nevű `clouddrive`parancsot, amely lehetővé teszi a Cloud Shellhoz csatlakoztatott fájlmegosztás manuális frissítését.
![A "clouddrive" parancs futtatása](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Új clouddrive csatlakoztatása

#### <a name="prerequisites-for-manual-mounting"></a>Manuális csatlakoztatás előfeltételei
A `clouddrive mount` parancs használatával frissítheti Cloud Shellhoz társított fájlmegosztást.

Ha meglévő fájlmegosztást csatlakoztat, a Storage-fiókoknak a Select Cloud Shell régióban kell elhelyezkedniük. Kérje le a helyet a `env` bash-ből való futtatással, és ellenőrizze a `ACC_LOCATION`következőt:.

#### <a name="the-clouddrive-mount-command"></a>A `clouddrive mount` parancs

> [!NOTE]
> Ha új fájlmegosztást csatlakoztat, a rendszer létrehoz `$Home` egy új felhasználói rendszerképet a címtárhoz. Az előző `$Home` képet az előző fájlmegosztás tárolja.

Futtassa a `clouddrive mount` parancsot a következő paraméterekkel:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

További részletek megtekintéséhez futtassa `clouddrive mount -h`a következőt:

![A "clouddrive mount'command futtatása](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Clouddrive leválasztása
Bármikor leválaszthatja a Cloud Shellhoz csatlakoztatott fájlmegosztást. Mivel a Cloud Shell csatlakoztatott fájlmegosztást kell használni, a rendszer felszólítja, hogy hozzon létre és csatoljon egy másik fájlmegosztást a következő munkamenetben.

1. Futtassa az `clouddrive unmount` parancsot.
2. Nyugtázza és erősítse meg a kéréseket.

A fájlmegosztás továbbra is fennáll, hacsak nem törli manuálisan. A következő munkamenetekben a Cloud Shell többé nem fogja megkeresni ezt a fájlmegosztást. További részletek megtekintéséhez futtassa `clouddrive unmount -h`a következőt:

![A "clouddrive unmount'command futtatása](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Bár a parancs futtatása nem törli az erőforrásokat, manuálisan törli az erőforráscsoportot, a Storage-fiókot vagy az Cloud Shellra leképezett fájlmegosztást, törli a `$Home` könyvtár lemezének lemezképét és a fájlmegosztás összes fájlját. A művelet nem vonható vissza.

### <a name="list-clouddrive"></a>Listáját`clouddrive`
`clouddrive` A`df` parancs futtatásával derítheti fel, hogy melyik fájlmegosztás van csatlakoztatva. 

A clouddrive elérési útja a Storage-fiók nevét és a fájlmegosztás URL-címét jeleníti meg. Például: `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```
## <a name="powershell-specific-commands"></a>PowerShell-specifikus parancsok

### <a name="list-clouddrive-azure-file-shares"></a>Azure `clouddrive` -fájlmegosztás listázása
A `Get-CloudDrive` parancsmag lekérdezi az Azure `clouddrive` -fájlmegosztás aktuálisan csatlakoztatott adatait a Cloud shell. <br>
![A Get-CloudDrive futtatása](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Foglalatlan`clouddrive`
Bármikor leválaszthatja a Cloud Shellhoz csatlakoztatott Azure-fájlmegosztást. Ha az Azure-fájlmegosztás el lett távolítva, a rendszer felszólítja egy új Azure-fájlmegosztás létrehozására és csatlakoztatására a következő munkamenet során.

A `Dismount-CloudDrive` parancsmag leválasztja az Azure-fájlmegosztást az aktuális Storage-fiókból. Az `clouddrive` aktuális munkamenet leválasztásának megszakítása. A rendszer a következő munkamenet során új Azure-fájlmegosztás létrehozására és csatlakoztatására kéri a felhasználót.
![Leválasztások futtatása – CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Megjegyzés: Ha meg kell határoznia egy függvényt egy fájlban, és hívnia kell a PowerShell-parancsmagokból, akkor a pont operátort is tartalmaznia kell. Például:. .\MyFunctions.ps1

## <a name="next-steps"></a>További lépések
[Bash Cloud Shell rövid útmutatóban](quickstart.md) <br>
[PowerShell Cloud Shell rövid útmutató](quickstart-powershell.md) <br>
[Tudnivalók a Microsoft Azure file Storage-ról](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Tudnivalók a tárolási címkékről](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
