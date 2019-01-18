---
title: 'PowerShell: A BYOK TDE - engedélyezése az Azure SQL Database felügyelt példány |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az Azure felügyelt SQL-példány használatához BYOK transzparens adattitkosítási (TDE) az inaktív titkosítási PowerShell használatával.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 01/17/2019
ms.openlocfilehash: fdf300d8aa288a80c88830e0a8d4cbe80acf0e28
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389891"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transzparens adattitkosítás a felügyelt példány az Azure Key vaultból saját kulcs használata kezelheti

A PowerShell-példaszkript Bring Your Own Key-forgatókönyvben transzparens adattitkosítási (TDE) konfigurálja az Azure SQL felügyelt példány az Azure Key Vaultban lévő kulcsot használ. A TDE Bring Your Own Key (BYOK) támogatásával kapcsolatos további információkért lásd: [TDE Bring Your Own Key az Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az Azure PowerShell-modul 5.7.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzureRmAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Ha helyi telepítése és használata a PowerShell, ebben az oktatóanyagban a azurerm.SQL-hez PowerShell csomag előzetes verziója is szükséges *4.11.6-preview*. Futtassa a következő parancsot a telepítéshez: `Install-Module -Name AzureRM.Sql -RequiredVersion 4.11.6-preview -AllowPrerelease`

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
