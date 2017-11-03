---
title: "Fájlok megmaradnak a Bash Azure Cloud rendszerhéj (előzetes verzió) |} Microsoft Docs"
description: "A forgatókönyv a hogyan Azure Cloud rendszerhéj Bash fájlokat továbbra is fennáll."
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 8b8a82e1c1328d952a85ea5afd975a95f5a6e740
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-storage-works"></a>Felhő rendszerhéj tárolási működése 
Felhő rendszerhéj továbbra is fennáll, az alábbi eljárások közül mindkettő keresztül fájlokat: 
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
> Bár a parancs futtatása nem összes erőforrást törli, egy erőforráscsoport, a tárfiók törlésével vagy felhő rendszerhéj leképezett fájlmegosztás törli a `$Home` directory lemezképét, és a fájlmegosztást a fájlokat. Ez a művelet nem vonható vissza.

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

[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>Következő lépések
[Felhő rendszerhéj gyors üzembe helyezés](quickstart.md) <br>
[További tudnivalók az Azure File storage](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[További információk a tárolási címkék](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
