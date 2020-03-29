---
title: SQL virtuális gépek tömeges regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval | Microsoft dokumentumok
description: Tömeges regisztráció SQL Server virtuális gépek az SQL virtuális gép erőforrás-szolgáltató a kezelhetőség javítása érdekében.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353886"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>SQL virtuális gépek tömeges regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval

Ez a cikk ismerteti, hogyan tömegesen regisztrálhatja az SQL Server virtuális gép (VM) az Azure-ban az SQL VM erőforrás-szolgáltató a `Register-SqlVMs` PowerShell-parancsmag használatával.

A `Register-SqlVMs` parancsmag segítségével regisztrálhatja az összes virtuális gépet egy adott előfizetések, erőforráscsoportok vagy adott virtuális gépek listájában. A parancsmag _regisztrálja_ a virtuális gépeket könnyű felügyeleti módban, majd létrehoz egy [jelentést és egy naplófájlt.](#output-description) 

A regisztrációs folyamat nem hordoz kockázatot, nincs állásidő, és nem indítja újra az SQL Server t vagy a virtuális gépet. 

Az erőforrás-szolgáltatóról további információt az [SQL VM erőforrás-szolgáltató című](virtual-machines-windows-sql-register-with-resource-provider.md)témakörben talál. 

## <a name="prerequisites"></a>Előfeltételek

Az SQL Server virtuális gép regisztrálásához az erőforrás-szolgáltatónál a következőkre lesz szükség: 

- Az [erőforrás-szolgáltatónál regisztrált](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) És nem regisztrált SQL Server virtuális gépeket tartalmazó [Azure-előfizetés.](https://azure.microsoft.com/free/) 
- A virtuális gépek regisztrálásához használt ügyfélhitelesítő adatok a következő RBAC-szerepkörök valamelyikében léteznek: **Virtuálisgép-közreműködő**, **Közreműködő**vagy **Tulajdonos.** 
- Az Az [PowerShell](/powershell/azure/new-azureps-module-az)legújabb verziója . 
- A legújabb verziója [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Első lépések

A folytatás előtt létre kell hoznia a parancsfájl helyi példányát, powershell-modulként importálnia kell, és csatlakoznia kell az Azure-hoz. 

### <a name="create-script"></a>Parancsfájl létrehozása

A parancsfájl létrehozásához másolja a [teljes parancsfájlt](#full-script) a cikk végéből, és mentse helyileg a ( parancsfájl) néven. `RegisterSqlVMs.psm1` 

### <a name="import-script"></a>Parancsfájl importálása

A parancsfájl létrehozása után importálhatja azt modulként a Powershell terminálban. 

Nyisson meg egy felügyeleti PowerShell-terminált, és keresse meg a fájlt. `RegisterSqlVMs.psm1` Ezután futtassa a következő PowerShell-parancsmast a parancsfájl modulként történő importálásához: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Az Azure-hoz való csatlakozáshoz használja a következő PowerShell-parancsmast:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Az előfizetések listájában szereplő összes virtuális gép 

Az alábbi parancsmag segítségével regisztrálja az összes SQL Server virtuális gépet az előfizetések listájában:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Példa a kimenetre: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Az összes virtuális gép egyetlen előfizetésben

A következő parancsmag segítségével egyetlen előfizetésben regisztrálhatja az összes SQL Server virtuális gépet: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Példa a kimenetre:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Több erőforráscsoport összes virtuális gépe

A következő parancsmag segítségével egyetlen előfizetésen belül több erőforráscsoportban regisztrálhatja az összes SQL Server virtuális gépet:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Példa a kimenetre:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Az erőforráscsoport összes virtuális gépe

A következő parancsmag segítségével egyetlen erőforráscsoportban regisztrálhatja az összes SQL Server virtuális gépet: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Példa a kimenetre:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>Adott virtuális gépek egyetlen erőforráscsoportban

A következő parancsmag segítségével egyetlen erőforráscsoporton belül regisztrálhat adott SQL Server virtuális gépeket:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Példa a kimenetre:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>Adott virtuális gép

Adott SQL Server virtuális gép regisztrálásához használja a következő parancsmast: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Példa a kimenetre:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Kimenet leírása

A parancsmag minden egyes használatakor `Register-SqlVMs` jelentés és naplófájl jön létre. 

### <a name="report"></a>Jelentés

A jelentés egy `.txt` fájlként `RegisterSqlVMScriptReport<Timestamp>.txt` jön létre, ahol az időbélyeg a parancsmag indításának időpontja. A jelentés a következő részleteket sorolja fel:

| **Kimeneti érték** | **Leírás** |
| :--------------  | :-------------- | 
| Az előfizetések regisztrációjának száma nem sikerült, mert nem rendelkezik hozzáféréssel, vagy a hitelesítő adatok helytelenek | Ez biztosítja a megadott hitelesítéssel kapcsolatos problémákat okozó előfizetések számát és listáját. A részletes hiba megtalálható a naplóban az előfizetés-azonosító keresésével. | 
| Azon előfizetések száma, amelyeket nem lehetett megpróbálni, mert nincsenek regisztrálva az RP-hez | Ez a szakasz az SQL virtuálisgép-erőforrás-szolgáltatóhoz még nem regisztrált előfizetések számát és listáját tartalmazza. |
| Összes talált virtuális gép | A parancsmagnak átadott paraméterek hatókörében talált virtuális gépek száma. | 
| Már regisztrált virtuális gépek | A kihagyott virtuális gépek száma, mivel már regisztrálva voltak az erőforrás-szolgáltatónál. |
| A sikeresen regisztrált virtuális gépek száma | A parancsmag futtatása után sikeresen regisztrált virtuális gépek száma. A regisztrált virtuális gépek `SubscriptionID, Resource Group, Virtual Machine`et sorolja fel formátumban. | 
| Hiba miatt nem sikerült regisztrálni a virtuális gépek száma | Azon virtuális gépek száma, amelyek valamilyen hiba miatt nem tudtak regisztrálni. A hiba részletei a naplófájlban találhatók. | 
| A virtuális gép vagy a virtuális gép szélszóráti ügynöke által kihagyott virtuális gépek száma nem fut | A virtuális gépként vagy a virtuális gépen nem regisztrálható virtuális gépek száma és listája nem futott. Ezek a virtuális gép vagy a vendégügynök indítása után újra próbálkozhatók. A részletek a naplófájlban találhatók. |
| Kihagyott virtuális gépek száma, mivel nem windowsos SQL Server-t futtatnak | Azon virtuális gépek száma, amelyek nem SQL Server rendszert futtatnak, vagy nem Windows virtuális gépek. A virtuális gépek a formátumban `SubscriptionID, Resource Group, Virtual Machine`vannak felsorolva. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Napló 

A hibák a naplófájlba kerülnek, `VMsNotRegisteredDueToError<Timestamp>.log` ahol az időbélyeg a parancsfájl indítása. Ha a hiba az előfizetés szintjén van, a napló tartalmazza a vesszővel tagolt SubscriptionID azonosítót és a hibaüzenetet. Ha a hiba a virtuális gép regisztrációja, a napló tartalmazza az előfizetési azonosító, erőforráscsoport neve, virtuális gép neve, hibakód és üzenet vesszővel elválasztva. 

## <a name="remarks"></a>Megjegyzések

Amikor az SQL Server virtuális gépeket a megadott parancsfájl használatával regisztrálja az erőforrás-szolgáltatónál, vegye figyelembe a következőket:

- Az erőforrás-szolgáltatóval való regisztrációhoz az SQL Server virtuális gépen futó vendégügynökszükséges. A Windows Server 2008 rendszerképeknem rendelkeznek vendégügynökkel, ezért ezek a virtuális gépek sikertelenek lesznek, és manuálisan kell regisztrálni őket a [NoAgent felügyeleti módban.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)
- Van újrapróbálkozási logika beépített átlátható hibák leküzdésére. Ha a virtuális gép sikeresen regisztrálva van, akkor ez egy gyors művelet. Ha azonban a regisztráció sikertelen, akkor minden virtuális gép újra próbálkozik.  Mint ilyen, meg kell adnia jelentős időt a regisztrációs folyamat befejezéséhez - bár a tényleges időkövetelmény a hibák típusától és számától függ. 

## <a name="full-script"></a>Teljes szkript

A GitHub teljes parancsfájljával kapcsolatban lásd: [Tömeges regisztráció SQL virtuális gépek az Az PowerShell.](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1) 

Másolja a teljes parancsfájlt, és mentse a . `RegisterSqLVMs.psm1`

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>További lépések

További információkért tekintse át a következő cikkeket: 

* [Az SQL Server áttekintése Windows virtuális gépen](virtual-machines-windows-sql-server-iaas-overview.md)
* [Gyakori kérdések az SQL Server rendszerrel kapcsolatos kérdésekről Windows VM rendszeren](virtual-machines-windows-sql-server-iaas-faq.md)
* [Az SQL Server díjszabási útmutatója Windows virtuális gépen](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Kibocsátási megjegyzések az SQL Server rendszerhez Windows vM rendszeren](virtual-machines-windows-sql-server-iaas-release-notes.md)
