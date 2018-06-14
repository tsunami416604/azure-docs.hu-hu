---
title: Fájlok megmaradnak az Azure felhőalapú rendszerhéj Bash |} Microsoft Docs
description: A forgatókönyv a hogyan Azure Cloud rendszerhéj Bash fájlokat továbbra is fennáll.
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
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: d8188634846a7ce75b5294cb3012069d9eafafc1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919542"
---
[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-bash-in-cloud-shell-storage-works"></a>A felhő rendszerhéj tárolási Bash működése 
A felhő rendszerhéj bash továbbra is fennáll, az alábbi eljárások közül mindkettő keresztül fájlokat: 
* A lemez lemezkép létrehozása a `$Home` directory megőrizni a címtárban található összes tartalmat. Az alapkonfiguráció lemezképét mentette: a megadott fájlmegosztással `acc_<User>.img` : `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, és automatikusan szinkronizálja a módosításokat. 
* A megadott fájlmegosztással csatlakoztatása `clouddrive` a a `$Home` könyvtárhoz, a fájlmegosztás közvetlen interakció. `/Home/<User>/clouddrive`van leképezve `fileshare.storage.windows.net/fileshare`.
 
> [!NOTE]
> Az összes fájlt a `$Home` címtár, például az SSH-kulcsokat, a lemez a csatlakoztatott fájl megosztáson található felhasználói lemezkép megmaradnak. Gyakorlati tanácsok alkalmazza, ha az adatokat továbbra is fennáll a `$Home` directory és a csatlakoztatott fájlmegosztás.

## <a name="use-the-clouddrive-command"></a>Használja a `clouddrive` parancs
A felhő rendszerhéj Bash is futtathatja egy olyan parancsot `clouddrive`, amely lehetővé teszi, hogy manuálisan frissítse a felhő rendszerhéj a fájlmegosztást, amely csatlakoztatva van.
![A "clouddrive" parancs futtatása](media/persisting-shell-storage/clouddrive-h.png)

## <a name="mount-a-new-clouddrive"></a>Egy új clouddrive csatlakoztatása

### <a name="prerequisites-for-manual-mounting"></a>Manuális csatlakoztatásának előfeltételei
A fájlmegosztás tartozó felhő rendszerhéj használatával frissítheti a `clouddrive mount` parancsot.

Ha egy meglévő fájlmegosztást csatlakoztatja, a tárfiókok kell lennie:
* Helyileg redundáns tárolás vagy georedundáns tárolás fájlmegosztások támogatásához.
* A hozzárendelt régióban található. Amikor bevezetése, a régiót hozzá van rendelve, az erőforráscsoport neve szerepel `cloud-shell-storage-<region>`.

### <a name="the-clouddrive-mount-command"></a>A clouddrive csatlakoztatási parancs

> [!NOTE]
> Ha egy új fájlmegosztást most csatlakoztatni, új felhasználói lemezkép hoz létre a `$Home` könyvtár. Az előző `$Home` kép tartják a korábbi fájlmegosztásban.

Futtassa a `clouddrive mount` parancsot a következő paraméterekkel:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

További részletek megtekintéséhez futtassa `clouddrive mount -h`, ahogy az itt látható:

![Fut a "clouddrive mount'command](media/persisting-shell-storage/mount-h.png)

## <a name="unmount-clouddrive"></a>Clouddrive leválasztása
Egy fájlmegosztást, hogy bármikor a felhő rendszerhéj csatlakoztatott is leválasztása. Az felhő rendszerhéj szükséges használható csatlakoztatott fájlmegosztást, a rendszer kéri, létrehozása és egy másik fájlmegosztás csatlakoztatása a következő munkamenet.

1. Futtassa az `clouddrive unmount` parancsot.
2. Igazolja, és erősítse meg a megjelenő utasításokat.

A fájlmegosztás továbbra is létezik, kivéve, ha manuálisan törölni. Felhő rendszerhéj program már nem keres a fájlmegosztást a további munkamenetekhez. További részletek megtekintéséhez futtassa `clouddrive unmount -h`, ahogy az itt látható:

![Fut a "clouddrive unmount'command](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Bár a parancs futtatása nem töröl semmilyen erőforráshoz, egy erőforráscsoport, a tárfiókhoz vagy a felhő rendszerhéj leképezett fájlmegosztás törlésével törli a `$Home` directory lemezképét, és a fájlmegosztást a fájlokat. Ez a művelet nem vonható vissza.

## <a name="list-clouddrive"></a>Lista`clouddrive`
Derítsen fel, melyik fájlmegosztás csatlakoztatva van, mint `clouddrive`- ben futtassa a `df` parancsot. 

A fájl elérési útját clouddrive jeleníti meg a tárfiók nevét és az URL-címben fájlmegosztás. Például: `//storageaccountname.file.core.windows.net/filesharename`

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

[!INCLUDE [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>További lépések
[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md) <br>
[További tudnivalók a Microsoft Azure Files storage:](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[További információk a tárolási címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
