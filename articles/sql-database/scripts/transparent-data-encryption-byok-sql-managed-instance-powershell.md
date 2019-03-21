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
ms.date: 03/12/2019
ms.openlocfilehash: fbc62628bfec67bd648188920731f985f990a53d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837159"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transzparens adattitkosítás a felügyelt példány az Azure Key vaultból saját kulcs használata kezelheti

A PowerShell-példaszkript Bring Your Own Key-forgatókönyvben transzparens adattitkosítási (TDE) konfigurálja az Azure SQL felügyelt példány az Azure Key Vaultban lévő kulcsot használ. A TDE Bring Your Own Key (BYOK) támogatásával kapcsolatos további információkért lásd: [TDE Bring Your Own Key az Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha helyi telepítése és használata a PowerShell, az oktatóanyaghoz AZ PowerShell 1.4.0-s vagy újabb. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Ha helyi telepítése és használata a PowerShell, ebben az oktatóanyagban a azurerm.SQL-hez PowerShell csomag előzetes verziója is szükséges *4.11.6-preview*. Futtassa a következő parancsot a telepítéshez: `Install-Module -Name AzureRM.Sql -RequiredVersion 4.11.6-preview -AllowPrerelease`

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
