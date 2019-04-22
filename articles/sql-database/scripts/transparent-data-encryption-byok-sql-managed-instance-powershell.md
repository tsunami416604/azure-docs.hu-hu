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
ms.date: 04/09/2019
ms.openlocfilehash: c08b5559fd599fb297f294a54aed67c65676aee4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783395"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transzparens adattitkosítás a felügyelt példány az Azure Key vaultból saját kulcs használata kezelheti

A PowerShell-példaszkript Bring Your Own Key-forgatókönyvben transzparens adattitkosítási (TDE) konfigurálja az Azure SQL felügyelt példány az Azure Key Vaultban lévő kulcsot használ. A TDE Bring Your Own Key (BYOK) támogatásával kapcsolatos további információkért lásd: [TDE Bring Your Own Key az Azure SQL](../transparent-data-encryption-byok-azure-sql.md).

## <a name="prerequisites"></a>Előfeltételek

- Meglévő felügyelt példányt. Lásd: [PowerShell-lel történő létrehozása az Azure SQL Database felügyelt példány](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Mindkét PowerShell segítségével helyileg vagy az Azure Cloud Shellt használja AZ PowerShell 1.1.1-preview vagy újabb verziója előzetes verzió szükséges. Ha frissíteni szeretne, olvassa el [Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps), vagy futtassa az alábbi minta parancsfájl a modul telepítése.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
