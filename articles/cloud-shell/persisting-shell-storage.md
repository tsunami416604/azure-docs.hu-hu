---
title: Fájlok megtartása a Bash Azure Cloud shellben |} A Microsoft Docs
description: A forgatókönyv, hogy az Azure Cloud Shell Bash fájlokat továbbra is fennáll.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: juluk
ms.openlocfilehash: 9a22b14df18e10342bb2a872b82b94ab4ea62d0a
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859867"
---
[!INCLUDE [PersistingStorage-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>A Cloud Shell-storage működése 
A cloud Shell továbbra is fennáll, keresztül az alábbi eljárások közül mindkettőt fájlokat: 
* A lemezkép létrehozása a `$Home` directory megőrizni a könyvtárban lévő összes tartalmat. A lemezképet a rendszer menti a megadott fájlmegosztás `acc_<User>.img` , `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, és automatikusan szinkronizálja a módosításokat. 
* A megadott fájlmegosztás csatlakoztatása `clouddrive` a a `$Home` fájlmegosztás közvetlen interakció címtárat. `/Home/<User>/clouddrive` le van képezve `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Az összes fájlt a `$Home` címtár, például SSH-kulcsokat, a felhasználói lemezkép, a csatlakoztatott fájlmegosztásban tárolt őrzi. Ajánlott eljárások alkalmazza, ha az adatokat továbbra is fennáll a `$Home` directory és a csatlakoztatott fájlmegosztást.

## <a name="bash-specific-commands"></a>Bash-specifikus parancsok

### <a name="use-the-clouddrive-command"></a>Használja a `clouddrive` parancs
A Cloud Shell Bash, egy olyan parancsot futtathat `clouddrive`, amely lehetővé teszi, hogy manuálisan frissítse a fájlmegosztás, amely csatlakoztatva van a Cloud Shellbe.
![A "clouddrive" parancs futtatása](media/persisting-shell-storage/clouddrive-h.png)

### <a name="mount-a-new-clouddrive"></a>Egy új clouddrive csatlakoztatása

#### <a name="prerequisites-for-manual-mounting"></a>Manuális csatlakoztatásának előfeltételei
A fájlmegosztás társított Cloud Shell használatával frissítheti a `clouddrive mount` parancsot.

Ha egy meglévő fájlmegosztás csatlakoztatása, a storage-fiókok kell lennie:
* Helyileg redundáns tárolás vagy georedundáns tárolást támogatja a fájlmegosztások.
* A hozzárendelt régióban található. Ha bevezetése, a régió, hogy szerepel-e az erőforráscsoport neve `cloud-shell-storage-<region>`.

#### <a name="the-clouddrive-mount-command"></a>A `clouddrive mount` parancs

> [!NOTE]
> Ha egy új fájlmegosztást használ csatlakoztatására, egy új felhasználói lemezkép jön létre a `$Home` könyvtár. Az előző `$Home` a korábbi fájlmegosztás tárolt lemezképet.

Futtassa a `clouddrive mount` parancsot a következő paraméterekkel:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

További részletek megtekintéséhez futtassa `clouddrive mount -h`, ahogy az itt látható:

![Fut a "clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Clouddrive leválasztása
A Cloud Shellbe bármikor csatlakoztatott fájlmegosztást is leválasztása. A Cloud Shell használható csatlakoztatott fájlmegosztást igényel, mivel, a rendszer kéri, hozhat létre, és a egy másik fájlmegosztás csatlakoztatása a következő munkamenetben.

1. Futtassa az `clouddrive unmount` parancsot.
2. Tudomásul veszi, és ellenőrizze a megjelenő utasításokat.

A fájlmegosztás továbbra is megmarad, ha manuálisan törölni. A cloud Shell már nem lesz keresse meg a fájlmegosztáshoz a minden későbbi munkamenet során. További részletek megtekintéséhez futtassa `clouddrive unmount -h`, ahogy az itt látható:

![Fut a "clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Bár a parancs futtatása nem inaktiválja azokat az erőforrásokat, egy erőforráscsoport, a storage-fiók vagy a Cloud Shell leképezett fájlmegosztás törlésével törli az `$Home` directory lemezképét és a fájlmegosztás található fájlokat. A művelet nem vonható vissza.

### <a name="list-clouddrive"></a>Lista `clouddrive`
Felderítheti, mely fájlmegosztás van csatlakoztatva, `clouddrive`futtassa a `df` parancsot. 

Clouddrive fájl elérési útját jeleníti meg a tárfiók nevét és a fájlmegosztás URL-címét. Például: `//storageaccountname.file.core.windows.net/filesharename`

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

### <a name="list-clouddrive-azure-file-shares"></a>Lista `clouddrive` Azure-fájlmegosztások
A `Get-CloudDrive` parancsmag lekéri az Azure fájlmegosztási adatok által aktuálisan csatlakoztatva van a `clouddrive` a Cloud shellben. <br>
![Get-CloudDrive fut](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Válassza le a lemezképet `clouddrive`
A Cloud Shellbe bármikor csatlakoztatva van Azure-fájlmegosztások is leválasztása. Ha az Azure-fájlmegosztást az eltávolított, a rendszer kéri, hozhat létre, és a egy új Azure-fájlmegosztást csatlakoztathatja a következő munkamenet.

A `Dismount-CloudDrive` parancsmag leválasztja a Azure-fájlmegosztások a jelenlegi tárfiókból. Leválasztás a `clouddrive` leállítja az aktuális munkamenet. A felhasználó létrehozásához, és a egy új Azure-fájlmegosztás csatlakoztatása a következő munkamenet során kérni fogja.
![Futó Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>További lépések
[A bash Cloud Shell rövid](quickstart.md) <br>
[A PowerShell Cloud Shell rövid](quickstart-powershell.md) <br>
[További tudnivalók a Microsoft Azure Files storage:](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[További információ a tárolási címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
