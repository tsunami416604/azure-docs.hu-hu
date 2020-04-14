---
title: Fájlok megőrzése az Azure Cloud Shellben | Microsoft dokumentumok
description: Forgatókönyv arról, hogy az Azure Cloud Shell hogyan őrizi meg a fájlokat.
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
ms.openlocfilehash: 37005a722d4a1962b4f6e1ddb8bb1c7a1229d28a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273290"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Fájlok megőrzése az Azure Cloud Shellben
A Cloud Shell az Azure File storage-ot használja a fájlok munkamenetek közötti megőrzéséhez. A cloud shell első indításkor kéri, hogy társítson egy új vagy meglévő fájlmegosztást a fájlok munkamenetek közötti megőrzéséhez.

> [!NOTE]
> A Bash és a PowerShell ugyanazt a fájlmegosztást osztja meg. A Cloud Shell automatikus csatlakoztatásához csak egy fájlmegosztás társítható.

> [!NOTE]
> Az Azure storage tűzfal nem támogatott a felhőbeli rendszerhéj-tárfiókok.

## <a name="create-new-storage"></a>Új tároló létrehozása

Ha alapvető beállításokat használ, és csak egy előfizetést választ ki, a Cloud Shell három erőforrást hoz létre az Ön nevében az Önhöz legközelebb eső támogatott régióban:
* Erőforráscsoport: `cloud-shell-storage-<region>`
* Tárfiók:`cs<uniqueGuid>`
* Fájlmegosztás:`cs-<user>-<domain>-com-<uniqueGuid>`

![Az Előfizetés beállítás](media/persisting-shell-storage/basic-storage.png)

A fájlmegosztás a `clouddrive` címtárban találhatómódon `$Home` csatlakoztatva van. Ez egy egyszeri művelet, és a fájlmegosztás automatikusan csatlakoztatódik a következő munkamenetekben. 

A fájlmegosztás egy 5 GB-os lemezképet is tartalmaz, amely `$Home` et hozlétre, amely automatikusan megőrzi az adatokat a könyvtárban. Ez a Bash és a PowerShell egyaránt vonatkozik.

## <a name="use-existing-resources"></a>Meglévő erőforrások használata

A speciális beállítás használatával meglévő erőforrásokat társíthat. A Cloud Shell-régió kiválasztásakor ki kell választania egy biztonsági tárfiókot, amely ugyanabban a régióban található. Ha például a hozzárendelt régió usa nyugati régiója, akkor az USA nyugati régiójában is társítania kell egy fájlmegosztást.

Amikor megjelenik a tárolási beállítási üzenet, válassza a **Speciális beállítások megjelenítése** lehetőséget a további beállítások megtekintéséhez. A helyileg redundáns tárolás (LRS), a georedundáns tárolás (GRS) és a zónaredundáns tárolási (ZRS) fiókok feltöltött tárolási beállítások szűrője. 

> [!NOTE]
> Grs- vagy ZRS-tárfiókok használata ajánlott a biztonsági fájlmegosztás további rugalmassága érdekében. A redundancia típusa a céloktól és az árpreferenciáktól függ. [További információ az Azure Storage-fiókok replikációs beállításairól.](../storage/common/storage-redundancy.md)

![Az Erőforráscsoport beállítása](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>A tárolási hozzáférés biztonságossá tétele
A biztonság érdekében minden felhasználónak saját tárfiókot kell létesítenie.  Szerepköralapú hozzáférés-vezérlés (RBAC) esetén a felhasználóknak közreműködői hozzáféréssel kell rendelkezniük a tárfiók szintjén.

A Cloud Shell egy Azure-fájlmegosztást használ egy tárfiókban, egy adott előfizetésen belül. Az örökölt engedélyek miatt az előfizetéshez megfelelő hozzáférési jogokkal rendelkező felhasználók hozzáférhetnek az összes tárfiókhoz és az előfizetésben található fájlmegosztásokhoz.

A felhasználóknak le kell zárniuk a fájlokhoz való hozzáférést a tárfiók vagy az előfizetés szintjén az engedélyek beállításával.

## <a name="supported-storage-regions"></a>Támogatott tárolási területek
Az aktuális régió megkereséséhez `env` futtathatja a `ACC_LOCATION`Bash-t, és `$env:ACC_LOCATION`megkeresheti a változót, vagy a PowerShell futtatása között. A fájlmegosztások egy 5 GB-os `$Home` lemezképet kapnak, amelyet a könyvtár megőrzéséhez hoztak létre.

A Cloud Shell-gépek a következő régiókban léteznek:

|Terület|Régió|
|---|---|
|Amerika|USA keleti régiója, USA déli középső régiója, USA nyugati régiója|
|Európa|Észak-Európa, Nyugat-Európa|
|Ázsia és a Csendes-óceáni térség|India Közép-, Délkelet-Ázsia|

Az ügyfeleknek ki kell választaniuk egy elsődleges régiót, kivéve, ha követelmény, hogy az inkóbeli adataikat egy adott régióban kell tárolniuk. Ha rendelkeznek ilyen követelménnyel, másodlagos tárolási régiót kell használni.

### <a name="secondary-storage-regions"></a>Másodlagos tárolási területek
Ha egy másodlagos tárolási régiót használ, a társított Azure storage-fiók egy másik régióban található, mint a Cloud Shell-gép, amelyhez csatlakoztatja őket. Jane beállíthatja például, hogy a tárfiók Kanadában, egy másodlagos régióban legyen, de a gép, amelyhez csatlakoztatva van, továbbra is egy elsődleges régióban található. Az inkett adatok Kanadában találhatók, de az Egyesült Államokban dolgozzák fel.

> [!NOTE]
> Másodlagos régió esetén a Cloud Shell fájlhozzáférési és indítási ideje lassabb lehet.

A felhasználó `(Get-CloudDrive | Get-AzStorageAccount).Location` futtathatja a PowerShell a fájlmegosztás helyét.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Erőforrás-létrehozás korlátozása Azure-erőforrás-szabályzattal
A Cloud Shellben létrehozott tárfiókok `ms-resource-usage:azure-cloud-shell`címkével vannak ellátva. Ha azt szeretné, hogy a felhasználók ne hozzanak létre tárfiókokat a Cloud Shellben, hozzon létre egy [Azure-erőforrás-szabályzatot](../azure-policy/json-samples.md) az adott címke által aktivált címkékhez.

## <a name="how-cloud-shell-storage-works"></a>A Cloud Shell-tároló működése 
A Cloud Shell az alábbi módszerekkel is megőrzi a fájlokat: 
* Lemezkép létrehozása a `$Home` könyvtárban a könyvtár összes tartalmának megőrzéséhez. A lemezképet a rendszer a megadott `acc_<User>.img` `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img`fájlmegosztásba menti, ahogy az a alkalmazásban, és automatikusan szinkronizálja a módosításokat. 
* A megadott fájlmegosztás `clouddrive` csatlakoztatása a `$Home` könyvtárhoz a közvetlen fájlmegosztási interakcióhoz. `/Home/<User>/clouddrive`a hozzá van `fileshare.storage.windows.net/fileshare`rendelve.
 
> [!NOTE]
> A címtárban lévő összes fájl, például az `$Home` SSH-kulcsok, megmaradnak a csatlakoztatott fájlmegosztásban tárolt felhasználói lemezképen. Gyakorlati tanácsok a címtárban `$Home` és a csatlakoztatott fájlmegosztásban való megőrzéskor ajánlott eljárás.

## <a name="clouddrive-commands"></a>clouddrive parancsok

### <a name="use-the-clouddrive-command"></a>A `clouddrive` parancs használata
A Cloud Shell ben futtathat `clouddrive`egy parancsot, amely lehetővé teszi a Cloud Shellhez csatlakoztatott fájlmegosztás manuális frissítését.

![A "clouddrive" parancs futtatása](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Lista`clouddrive`
A parancs futtatásához futtassa `clouddrive`a `df` parancsot. 

A clouddrive fájlelérési útja a tárfiók nevét és fájlmegosztását jeleníti meg az URL-ben. Például: `//storageaccountname.file.core.windows.net/filesharename`

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

### <a name="mount-a-new-clouddrive"></a>Új felhőmeghajtó csatlakoztatása

#### <a name="prerequisites-for-manual-mounting"></a>A kézi szerelés előfeltételei
A `clouddrive mount` paranccsal frissítheti a Cloud Shellhez társított fájlmegosztást.

Ha egy meglévő fájlmegosztást csatlakoztat, a tárfiókoknak a kiválasztott Cloud Shell-régióban kell lenniük. A hely lekéréséhez `env` `ACC_LOCATION`fut, és ellenőrzi a.

#### <a name="the-clouddrive-mount-command"></a>A `clouddrive mount` parancs

> [!NOTE]
> Ha új fájlmegosztást szerel fel, új felhasználói lemezkép jön létre a `$Home` könyvtárhoz. Az `$Home` előző kép az előző fájlmegosztásban marad.

Futtassa a `clouddrive mount` parancsot a következő paraméterekkel:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

További részletek megtekintéséhez `clouddrive mount -h`futtassa a fut, ahogy itt látható:

![A 'clouddrive mount'parancs futtatása](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Leválasztás a felhőalapú meghajtóról
A Cloud Shellhez csatlakoztatott fájlmegosztást bármikor leválaszthatja. Mivel a Cloud Shell csatlakoztatott fájlmegosztást igényel, a rendszer kérni fogja egy másik fájlmegosztás létrehozását és csatlakoztatását a következő munkamenetben.

1. Futtassa az `clouddrive unmount` parancsot.
2. Az utasítások nyugtázása és megerősítése.

A fájlmegosztás továbbra is fennáll, hacsak nem törli manuálisan. A Cloud Shell a következő munkamenetek során már nem keresi ezt a fájlmegosztást. További részletek megtekintéséhez `clouddrive unmount -h`futtassa a fut, ahogy itt látható:

![A 'clouddrive unmount'command futtatása](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Bár a parancs futtatása nem törli az erőforrásokat, az erőforráscsoport, a tárfiók vagy a Cloud `$Home` Shellre leképezett fájlmegosztás manuális törlése törli a címtárlemez-lemezképét és a fájlmegosztásban lévő fájlokat. Ez a művelet nem állítható vissza.
## <a name="powershell-specific-commands"></a>PowerShell-specifikus parancsok

### <a name="list-clouddrive-azure-file-shares"></a>Azure-fájlmegosztások listázása `clouddrive`
A `Get-CloudDrive` parancsmag lekéri az Azure-fájlmegosztási `clouddrive` adatokat, amelyeket jelenleg a Felhőrendszerhéjban csatlakoztatnak. <br>
![A Get-CloudDrive futtatása](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Foglalatlan`clouddrive`
Bármikor leválaszthategy Azure-fájlmegosztást, amely a Cloud Shellhez van csatlakoztatva. Ha az Azure-fájlmegosztás t lett eltávolítva, a rendszer kérni fogja, hogy hozzon létre és csatolja az új Azure-fájlmegosztása a következő munkamenetben.

A `Dismount-CloudDrive` parancsmag leválaszt egy Azure-fájlmegosztást az aktuális tárfiókból. A leválasztás `clouddrive` leállítja az aktuális munkamenetet. A következő munkamenet során a rendszer kéri a felhasználót, hogy hozzon létre és csatoljon egy új Azure-fájlmegosztást.
![A Leválasztás-CloudDrive futtatása](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Megjegyzés: Ha meg kell határoznia egy függvényt egy fájlban, és meg kell hívnia a PowerShell-parancsmagokból, akkor a dot operátort is bele kell foglalni. Például: . .\Sajatfunkciók.ps1

## <a name="next-steps"></a>További lépések
[Felhőbeli rendszerhéj – rövid útmutató](quickstart.md) <br>
[További információ a Microsoft Azure Files tárhelyről](../storage/files/storage-files-introduction.md) <br>
[További információ a tárolási címkékről](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
