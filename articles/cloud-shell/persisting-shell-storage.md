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
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: 15a5770eb2964f0f2039fe93de904af65d4c81ed
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598748"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Fájlok megőrzése Azure Cloud Shell
A Cloud Shell az Azure file Storage-t használja a fájlok munkamenetek közötti megőrzéséhez. A kezdeti indításkor Cloud Shell egy új vagy meglévő fájlmegosztás hozzárendelését kéri a fájlok munkamenetek közötti megőrzéséhez.

> [!NOTE]
> A bash és a PowerShell ugyanazt a fájlmegosztást használja. Cloud Shell-ben csak egy fájlmegosztás társítható az automatikus csatlakoztatási szolgáltatáshoz.

> [!NOTE]
> A Cloud Shell Storage-fiókok nem támogatják az Azure Storage-tűzfalat.

## <a name="create-new-storage"></a>Új tároló létrehozása

Ha alapszintű beállításokat használ, és csak egy előfizetést választ ki, Cloud Shell három erőforrást hoz létre az Ön nevében a legközelebbi támogatott régióban:
* Erőforráscsoport: `cloud-shell-storage-<region>`
* Storage-fiók: `cs<uniqueGuid>`
* Fájlmegosztás: `cs-<user>-<domain>-com-<uniqueGuid>`

![Az előfizetés beállítása](media/persisting-shell-storage/basic-storage.png)

A fájlmegosztás `clouddrive`ként van csatolva a `$Home` könyvtárban. Ez egy egyszeri művelet, és a fájlmegosztás automatikusan csatolva lesz a következő munkamenetekben. 

A fájlmegosztás egy 5 GB-os rendszerképet is tartalmaz, amely az Ön számára készült, amely automatikusan megőrzi a `$Home` címtárban tárolt adatok adatait. Ez a bash és a PowerShell esetében is érvényes.

## <a name="use-existing-resources"></a>Meglévő erőforrások használata

A speciális beállítás használatával a meglévő erőforrásokat is hozzárendelheti. Cloud Shell régió kiválasztásakor ki kell választania egy azonos régióban található, biztonsági mentést tartalmazó Storage-fiókot. Ha például a hozzárendelt régiója az USA nyugati régiója, akkor az USA nyugati régiójában található fájlmegosztást is hozzá kell rendelnie.

Amikor megjelenik a Storage telepítője, válassza a **Speciális beállítások megjelenítése** lehetőséget a további beállítások megtekintéséhez. A kitöltött tárolási lehetőségek a helyileg redundáns tárolás (LRS), a Geo-redundáns tárolás (GRS) és a Zone-redundáns tárolási (ZRS) fiókok esetében használhatók. 

> [!NOTE]
> A GRS vagy a ZRS Storage-fiókok használata ajánlott a biztonsági mentéshez szükséges további rugalmasság érdekében. Milyen típusú redundancia függ a céloktól és az árképzéstől. [További információ az Azure Storage-fiókok replikációs lehetőségeiről](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![Az erőforráscsoport-beállítás](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>A tárterület-hozzáférés biztonságossá tétele
A biztonság érdekében minden felhasználónak ki kell építenie a saját Storage-fiókját.  A szerepköralapú hozzáférés-vezérlés (RBAC) esetében a felhasználóknak a Storage-fiók szintjén kell megadniuk a közreműködő vagy annál magasabb szintű hozzáférést.

Cloud Shell egy Azure-fájlmegosztást használ egy Storage-fiókban egy adott előfizetésen belül. Az örökölt engedélyek miatt az előfizetéshez megfelelő hozzáférési jogosultsággal rendelkező felhasználók hozzáférhetnek az összes Storage-fiókhoz, és az előfizetésben található fájlmegosztást is.

A felhasználók a fájlokhoz való hozzáférést a Storage-fiók vagy az előfizetési szint engedélyeinek beállításával zárolják.

## <a name="supported-storage-regions"></a>Támogatott tárolási régiók
Ha szeretné megkeresni az aktuális régiót, `env` a bash-ben, és keresse meg a `ACC_LOCATION`változót, vagy a PowerShell futtatási `$env:ACC_LOCATION`. A fájlmegosztás egy 5 GB-os rendszerképet kap, amely a `$Home` könyvtárának megtartására lett létrehozva.

Cloud Shell gépek léteznek a következő régiókban:

|Terület|Régió|
|---|---|
|Amerika|USA keleti régiója, USA déli középső régiója, USA nyugati régiója|
|Európa|Észak-Európa, Nyugat-Európa|
|Ázsia és a Csendes-óceáni térség|Közép-India, Délkelet-Ázsia|

Az ügyfeleknek az elsődleges régiót kell választaniuk, kivéve, ha olyan követelményük van, hogy a tárolt adataik egy adott régióban legyenek tárolva. Ha ilyen követelmény, másodlagos tárolási régiót kell használni.

### <a name="secondary-storage-regions"></a>Másodlagos tárolási régiók
Másodlagos tárolási régió használata esetén a társított Azure Storage-fiók egy másik régióban található, mint a csatlakoztatni kívánt Cloud Shell gép. Például Jane beállíthatja, hogy a Storage-fiókja kelet-Kanadában, egy másodlagos régióban legyen elhelyezve, de a gép, amelyhez csatlakoztatva van, továbbra is egy elsődleges régióban található. A nyugalmi állapotban lévő adatok Kanadában találhatók, de a rendszer a Egyesült Államokban dolgozza fel.

> [!NOTE]
> Másodlagos régió használata esetén előfordulhat, hogy a fájlokhoz való hozzáférés és a Cloud Shell indítási ideje lassabb.

A felhasználók a PowerShellben `(Get-CloudDrive | Get-AzStorageAccount).Location` futtathatják a fájlmegosztás helyét.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Erőforrás-létrehozás korlátozása Azure-erőforrás-házirenddel
A Cloud Shellban létrehozott Storage-fiókok `ms-resource-usage:azure-cloud-shell`címkével rendelkeznek. Ha azt szeretné, hogy a felhasználók ne hozzanak létre Storage-fiókokat a Cloud Shellban, hozzon létre egy Azure-erőforrás-szabályzatot az adott címke által aktivált [címkékhez](../azure-policy/json-samples.md) .

## <a name="how-cloud-shell-storage-works"></a>A Cloud Shell Storage működése 
A Cloud Shell a következő módszerekkel is megőrzi a fájlokat: 
* Hozzon létre egy lemezképet a `$Home` könyvtárában, hogy az a könyvtárban található összes tartalmat megőrzi. A lemez lemezképét a rendszer a megadott fájlmegosztás `acc_<User>.img` `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`, és automatikusan szinkronizálja a módosításokat. 
* A megadott fájlmegosztás csatlakoztatása `clouddrive`ként a `$Home` könyvtárban a közvetlen fájlmegosztás-interakcióhoz. a `/Home/<User>/clouddrive` `fileshare.storage.windows.net/fileshare`ra van leképezve.
 
> [!NOTE]
> A `$Home` könyvtárban lévő összes fájl, például az SSH-kulcsok megmaradnak a felhasználói lemezképben, amelyet a csatlakoztatott fájlmegosztás tárol. Alkalmazza az ajánlott eljárásokat, ha a `$Home` könyvtára és a csatlakoztatott fájlmegosztás adatait megőrzi.

## <a name="clouddrive-commands"></a>clouddrive parancsok

### <a name="use-the-clouddrive-command"></a>A `clouddrive` parancs használata
Cloud Shell futtathatja `clouddrive`nevű parancsot, amely lehetővé teszi a Cloud Shellhoz csatlakoztatott fájlmegosztás manuális frissítését.
a "clouddrive" parancs futtatásának ![](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>`clouddrive` listázása
A `df` parancs futtatásával derítheti fel, hogy mely fájlmegosztás van csatlakoztatva `clouddrive`ként. 

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

### <a name="mount-a-new-clouddrive"></a>Új clouddrive csatlakoztatása

#### <a name="prerequisites-for-manual-mounting"></a>Manuális csatlakoztatás előfeltételei
A Cloud Shellhoz társított fájlmegosztást a `clouddrive mount` parancs használatával frissítheti.

Ha meglévő fájlmegosztást csatlakoztat, a Storage-fiókoknak a Select Cloud Shell régióban kell elhelyezkedniük. A hely lekérése `env` futtatásával és a `ACC_LOCATION`ellenőrzésével.

#### <a name="the-clouddrive-mount-command"></a>Az `clouddrive mount` parancs

> [!NOTE]
> Ha új fájlmegosztást csatlakoztat, a rendszer létrehoz egy új felhasználói rendszerképet a `$Home` könyvtárához. Az előző `$Home` rendszerképet az előző fájlmegosztás tárolja.

Futtassa a `clouddrive mount` parancsot a következő paraméterekkel:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

További részletek megtekintéséhez futtassa `clouddrive mount -h`, ahogy az itt látható:

![A "clouddrive mount'command futtatása](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Clouddrive leválasztása
Bármikor leválaszthatja a Cloud Shellhoz csatlakoztatott fájlmegosztást. Mivel a Cloud Shell csatlakoztatott fájlmegosztást kell használni, a rendszer felszólítja, hogy hozzon létre és csatoljon egy másik fájlmegosztást a következő munkamenetben.

1. Futtassa az `clouddrive unmount` parancsot.
2. Nyugtázza és erősítse meg a kéréseket.

A fájlmegosztás továbbra is fennáll, hacsak nem törli manuálisan. A következő munkamenetekben a Cloud Shell többé nem fogja megkeresni ezt a fájlmegosztást. További részletek megtekintéséhez futtassa `clouddrive unmount -h`, ahogy az itt látható:

![A "clouddrive unmount'command futtatása](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Bár a parancs futtatása nem törli az erőforrásokat, manuálisan törli az erőforráscsoportot, a Storage-fiókot vagy az Cloud Shell leképezett fájlmegosztást, törli a `$Home` könyvtár lemezének lemezképét és a fájlmegosztás összes fájlját. Ez a művelet nem vonható vissza.
## <a name="powershell-specific-commands"></a>PowerShell-specifikus parancsok

### <a name="list-clouddrive-azure-file-shares"></a>Azure-fájlmegosztás `clouddrive` listázása
A `Get-CloudDrive` parancsmag lekérdezi az Azure-fájlmegosztás aktuálisan csatlakoztatott adatait a Cloud Shell `clouddrive`. <br>
![a Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png) futtatása

### <a name="unmount-clouddrive"></a>`clouddrive` leválasztása
Bármikor leválaszthatja a Cloud Shellhoz csatlakoztatott Azure-fájlmegosztást. Ha az Azure-fájlmegosztás el lett távolítva, a rendszer felszólítja egy új Azure-fájlmegosztás létrehozására és csatlakoztatására a következő munkamenet során.

A `Dismount-CloudDrive` parancsmag leválasztja az Azure-fájlmegosztást az aktuális Storage-fiókból. A `clouddrive` leválasztása megszakítja az aktuális munkamenetet. A rendszer a következő munkamenet során új Azure-fájlmegosztás létrehozására és csatlakoztatására kéri a felhasználót.
![leválasztást futtató CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Megjegyzés: Ha egy fájlban lévő függvényt kell megadnia, és a PowerShell-parancsmagokból kell meghívnia, akkor a pont operátort is tartalmaznia kell. Például:. .\MyFunctions.ps1

## <a name="next-steps"></a>Következő lépések
[Cloud Shell rövid útmutató](quickstart.md) <br>
[Tudnivalók a Microsoft Azure file Storage-ról](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Tudnivalók a tárolási címkékről](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
