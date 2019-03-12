---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555167"
---
Minden olyan eszközökkel kapcsolatos problémákat tapasztal, létrehozhat egy támogatási csomagot a rendszer naplókat. Support használja ezt a csomagot a problémák elhárításához. Kövesse az alábbi lépéseket egy támogatási csomag létrehozásához:

1. [A PowerShell-felületén az eszköz csatlakozni](#connect-to-the-powershell-interface).
2. Használja a `Get-HcsNodeSupportPackage` paranccsal hozzon létre egy támogatási csomagot. A parancsmag használata a következőképpen:

    ```powershell
    Get-HcsNodeSupportPackage [-Path] <string> [-Zip] [-ZipFileName <string>] [-Include {None | RegistryKeys | EtwLogs
            | PeriodicEtwLogs | LogFiles | DumpLog | Platform | FullDumps | MiniDumps | ClusterManagementLog | ClusterLog |
            UpdateLogs | CbsLogs | StorageCmdlets | ClusterCmdlets | ConfigurationCmdlets | KernelDump | RollbackLogs |
            Symbols | NetworkCmdlets | NetworkCmds | Fltmc | ClusterStorageLogs | UTElement | UTFlag | SmbWmiProvider |
            TimeCmds | LocalUILogs | ClusterHealthLogs | BcdeditCommand | BitLockerCommand | DirStats | ComputeRolesLogs |
            ComputeCmdlets | DeviceGuard | Manifests | MeasuredBootLogs | Stats | PeriodicStatLogs | MigrationLogs |
            RollbackSupportPackage | ArchivedLogs | Default}] [-MinimumTimestamp <datetime>] [-MaximumTimestamp <datetime>]
            [-IncludeArchived] [-IncludePeriodicStats] [-Credential <pscredential>]  [<CommonParameters>]
    ```

    A parancsmag naplókat gyűjt az eszközt, és ezeket a naplókat másolja át a megadott hálózati vagy helyi megosztásba.

    Használt paraméterek a következők:

    - `-Path` -Adja meg a hálózat vagy a támogatási csomag másolása helyi elérési útja. (szükséges)
    - `-Credential` -Adja meg a hitelesítő adatokat a védett elérési úthoz való hozzáférésre.
    - `-Zip` -Adjon meg egy zip-fájl létrehozásához.
    - `-Include` – Megadhatja, hogy az összetevők szerepeltetni a támogatási csomagot. Ha nincs megadva, `Default` feltételezi.
    - `-IncludeArchived` – Megadhatja, hogy a támogatási csomag archivált naplókat.
    - `-IncludePeriodicStats` – Megadhatja, hogy a támogatási csomag rendszeres stat naplókat.

    
