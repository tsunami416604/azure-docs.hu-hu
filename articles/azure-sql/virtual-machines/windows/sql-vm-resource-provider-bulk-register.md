---
title: SQL-alapú virtuális gépek tömeges regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval | Microsoft Docs
description: Az SQL VM erőforrás-szolgáltatóval SQL Server virtuális gépek tömeges regisztrálása a kezelhetőség javítása érdekében.
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
ms.openlocfilehash: 49b669627507af0e3e7386f31e344082cc4686df
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219311"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Több SQL virtuális gép regisztrálása az Azure-ban az SQL VM erőforrás-szolgáltatóval
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)][!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Ez a cikk azt ismerteti, hogyan regisztrálhatók a SQL Server virtuális gépek (VM-EK) az Azure-ban az SQL VM erőforrás-szolgáltatóval a `Register-SqlVMs` PowerShell-parancsmag használatával.

A `Register-SqlVMs` parancsmag használatával az összes virtuális gép regisztrálható az előfizetések, az erőforráscsoportok vagy az adott virtuális gépek listája alapján. A parancsmag _egyszerűsített_ felügyeleti módban regisztrálja a virtuális gépeket, majd [jelentést és naplófájlt](#output-description)is hoz. 

A regisztrációs folyamat nem jár kockázattal, nem rendelkezik állásidővel, és nem indítja újra SQL Server vagy a virtuális gépet. 

További információ az erőforrás-szolgáltatóról: [SQL VM erőforrás-szolgáltató](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Előfeltételek

A SQL Server VM erőforrás-szolgáltatóval való regisztrálásához a következőkre lesz szüksége: 

- Olyan [Azure-előfizetés](https://azure.microsoft.com/free/) , amely [regisztrálva van az erőforrás-szolgáltatónál](sql-vm-resource-provider-register.md#register-subscription-with-rp) , és nem regisztrált SQL Server virtuális gépeket tartalmaz. 
- A virtuális gépek regisztrálásához használt ügyfél-hitelesítő adatok a következő RBAC-szerepkörök bármelyikében léteznek: **virtuális gép közreműködői**, **közreműködő**vagy **tulajdonos**. 
- Az az [PowerShell](/powershell/azure/new-azureps-module-az)legújabb verziója. 
- Az az [. SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0)legújabb verziója.

## <a name="get-started"></a>Bevezetés

A továbblépés előtt először létre kell hoznia egy helyi példányt a parancsfájlból, majd importálnia kell egy PowerShell-modulként, és kapcsolódnia kell az Azure-hoz. 

### <a name="create-the-script"></a>A parancsfájl létrehozása

A parancsfájl létrehozásához másolja a [teljes szkriptet](#full-script) a cikk végéről, és mentse helyileg a következőként: `RegisterSqlVMs.psm1` . 

### <a name="import-the-script"></a>A parancsfájl importálása

A szkript létrehozása után a PowerShell-terminál modulként is importálhatja. 

Nyisson meg egy rendszergazdai PowerShell-terminált, és navigáljon arra a helyre, ahová a fájlt mentette `RegisterSqlVMs.psm1` . Ezután futtassa a következő PowerShell-parancsmagot a parancsfájl modulként történő importálásához: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

Az Azure-hoz való kapcsolódáshoz használja a következő PowerShell-parancsmagot:

```powershell-interactive
Connect-AzAccount
```


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Az összes virtuális gép regisztrálása az előfizetések listájában 

A következő parancsmaggal regisztrálja az összes SQL Server virtuális gépet az előfizetések listájában:

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

## <a name="register-all-vms-in-a-single-subscription"></a>Egyetlen előfizetésben lévő összes virtuális gép regisztrálása

A következő parancsmaggal regisztrálhat egyetlen előfizetésben lévő összes SQL Server virtuális gépet: 

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

## <a name="register-all-vms-in-multiple-resource-groups"></a>Az összes virtuális gép regisztrálása több erőforráscsoport esetében

A következő parancsmaggal regisztrálhat egyetlen előfizetésben található összes SQL Server virtuális gépet több erőforráscsoporthoz:

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

## <a name="resister-all-vms-in-a-resource-group"></a>Egy erőforráscsoport összes virtuális gépe

A következő parancsmaggal regisztrálhat egyetlen erőforráscsoport összes SQL Server virtuális gépét: 

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

## <a name="register-specific-vms-in-single-resource-group"></a>Adott virtuális gépek regisztrálása egyetlen erőforráscsoporthoz

A következő parancsmaggal regisztrálhat adott SQL Server virtuális gépeket egyetlen erőforráscsoporthoz:

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

## <a name="register-a-specific-vm"></a>Adott virtuális gép regisztrálása

Egy adott SQL Server virtuális gép regisztrálásához használja a következő parancsmagot: 

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

A rendszer minden alkalommal létrehoz egy jelentést és egy naplófájlt a `Register-SqlVMs` parancsmag használatakor. 

### <a name="report"></a>Jelentés

A jelentés egy `.txt` nevű fájlként jön létre, `RegisterSqlVMScriptReport<Timestamp>.txt` ahol az időbélyeg a parancsmag elindításának időpontja. A jelentés a következő adatokat sorolja fel:

| **Kimeneti érték** | **Leírás** |
| :--------------  | :-------------- | 
| Nem sikerült regisztrálni az előfizetéseket, mert nem rendelkezik hozzáféréssel vagy helytelen hitelesítő adatokkal | Itt adhatja meg azoknak az előfizetéseknek a számát és listáját, amelyek a megadott hitelesítéssel kapcsolatban léptek fel. A részletes hiba a naplóban található az előfizetés AZONOSÍTÓjának megkeresésével. | 
| Azon előfizetések száma, amelyeket nem lehetett kipróbálni, mert nincsenek regisztrálva az RP-ben | Ez a szakasz az SQL VM erőforrás-szolgáltatóhoz nem regisztrált előfizetések számát és listáját tartalmazza. |
| Talált virtuális gépek összesen | Azon virtuális gépek száma, amelyek a parancsmagnak átadott paraméterek hatókörében találhatók. | 
| Már regisztrált virtuális gépek | Azoknak a virtuális gépeknek a száma, amelyek ki lettek hagyva, mert már regisztrálva lettek az erőforrás-szolgáltatónál. |
| A sikeresen regisztrált virtuális gépek száma | Azoknak a virtuális gépeknek a száma, amelyek sikeresen regisztrálva lettek a parancsmag futtatása után. A regisztrált virtuális gépek listája a formátumban `SubscriptionID, Resource Group, Virtual Machine` . | 
| Nem sikerült regisztrálni a virtuális gépek számát a hiba miatt | Néhány hiba miatt nem sikerült regisztrálni a virtuális gépek számát. A hiba részletei a naplófájlban találhatók. | 
| A virtuális gép vagy a rendszerindító ügynök nem fut a virtuális gépen. | Azon virtuális gépek száma és listája, amelyek nem regisztrálhatók a virtuális gép vagy a vendég ügynök a virtuális gépen, nem voltak futtatva. Ezeket a rendszer a virtuális gép vagy a vendég ügynök elindítása után újrapróbálkozhat. A részletek a naplófájlban találhatók. |
| A kihagyott virtuális gépek száma, mivel azok nem futnak SQL Server Windows rendszeren | Azoknak a virtuális gépeknek a száma, amelyeket kihagytak, mert nem futnak SQL Server vagy nem Windows rendszerű virtuális gép. A virtuális gépek a formátumban vannak felsorolva `SubscriptionID, Resource Group, Virtual Machine` . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Napló 

A rendszer naplózza a hibákat a nevű naplófájlban `VMsNotRegisteredDueToError<Timestamp>.log` , ahol az időbélyeg az az idő, amikor a parancsfájl elindult. Ha a hiba az előfizetés szintjén van, a napló tartalmazza a vesszővel tagolt előfizetés-azonosítót és a hibaüzenetet. Ha a hiba a virtuális gép regisztrációja, a napló tartalmazza az előfizetés AZONOSÍTÓját, az erőforráscsoport nevét, a virtuális gép nevét, a hibakódot és az üzenetet vesszővel elválasztva. 

## <a name="remarks"></a>Megjegyzések

Ha SQL Server virtuális gépeket regisztrál az erőforrás-szolgáltatóval a megadott parancsfájl használatával, vegye figyelembe a következőket:

- Az erőforrás-szolgáltatóval való regisztrációhoz a SQL Server VM futó vendég ügynöknek kell futnia. A Windows Server 2008-lemezképek nem rendelkeznek vendég ügynökkel, így ezek a virtuális gépek sikertelenek lesznek, és manuálisan kell regisztrálni a nem [ügynök-felügyeleti mód](sql-vm-resource-provider-register.md#management-modes)használatával.
- A beépített újrapróbálkozási logika az átlátható hibák elhárítására szolgál. Ha a virtuális gép regisztrálása sikeresen megtörtént, akkor gyors művelet. Ha azonban a regisztráció meghiúsul, minden egyes virtuális gép újra próbálkozik.  Ezért jelentős időt kell biztosítani a regisztrációs folyamat befejezéséhez – bár a tényleges idő követelménye a hibák típusától és számától függ. 

## <a name="full-script"></a>Teljes szkript

A GitHub teljes parancsfájlját lásd: az [SQL-alapú virtuális gépek tömeges regisztrálása az az PowerShell-lel](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Másolja a teljes parancsfájlt, és mentse azt `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>További lépések

További információért tekintse át a következő cikkeket: 

* [Windows rendszerű virtuális gépek SQL Server áttekintése](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows rendszerű virtuális gépen SQL Server gyakori kérdések](frequently-asked-questions-faq.md)
* [A Windows rendszerű virtuális gépek SQL Server díjszabási útmutatója](pricing-guidance.md)
* [Windows rendszerű virtuális gépen SQL Server kibocsátási megjegyzései](../../database/doc-changes-updates-release-notes.md)
