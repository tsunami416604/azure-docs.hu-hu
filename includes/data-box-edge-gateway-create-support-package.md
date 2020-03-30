---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b657ee32e76dd90671f7e91337ced01b925889a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179578"
---
Ha bármilyen eszközproblémákat tapasztal, létrehozhat egy támogatási csomagot a rendszernaplókból. A Microsoft támogatási szolgálata ezt a csomagot használja a problémák elhárításához. Támogatási csomag létrehozásához kövesse az alábbi lépéseket:

1. [Csatlakozzon az eszköz PowerShell-felületéhez.](#connect-to-the-powershell-interface)
2. A `Get-HcsNodeSupportPackage` paranccsal hozzon létre egy támogatási csomagot. A parancsmag használata a következő:

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

    A parancsmag összegyűjti a naplókat az eszközről, és másolja azokat egy adott hálózatra vagy helyi megosztásra.

    Az alkalmazott paraméterek a következők:

    - `-Path`- Adja meg a hálózatot vagy a helyi elérési utat, amelybe a támogatási csomagot másolni szeretné. (kötelező)
    - `-Credential`- Adja meg a védett elérési út eléréséhez szükséges hitelesítő adatokat.
    - `-Zip`- Adja meg, hogy létrehoz egy zip fájlt.
    - `-Include`- Adja meg, hogy tartalmazza a támogatási csomagban szerepeltetni kívánt összetevőket. Ha nincs megadva, `Default` a függvény feltételezi.
    - `-IncludeArchived`- Adja meg, hogy tartalmazza az archivált naplókat a támogatási csomagban.
    - `-IncludePeriodicStats`- Adja meg, hogy tartalmazza az időszakos stat naplók at a támogatási csomagban.

    
