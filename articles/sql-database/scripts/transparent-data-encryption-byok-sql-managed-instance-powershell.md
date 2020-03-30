---
title: 'PowerShell: BYOK TDE engedélyezése – Azure SQL Database felügyelt példánya '
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL felügyelt példányt a BYOK transzparens adattitkosítás (TDE) használatára a PowerShell használatával való inaktív titkosításhoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: ddffda5229c9c0d33c563e3ae7b4a884f0f92dff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73691404"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Átlátható adattitkosítás kezelése felügyelt példányban saját kulccsal az Azure Key Vaultból

Ez a PowerShell-parancsfájl-példa az Azure SQL felügyelt példányügyfél által felügyelt kulccsal konfigurálja az átlátszó adattitkosítást (TDE) az Azure Key Vault egyik kulcsa használatával. Ezt gyakran nevezik a Bring Your Own Key forgatókönyv tde. Ha többet szeretne megtudni a TDE-ről az ügyfél által felügyelt kulccsal, olvassa el [a TDE Saját kulcs hozása az Azure SQL-be című témakört.](../transparent-data-encryption-byok-azure-sql.md)

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő felügyelt példány. Az [Azure SQL Database felügyelt példányának létrehozásához olvassa el a PowerShell használata című témakört.](sql-database-create-configure-managed-instance-powershell.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Mindkét PowerShell helyi vagy Azure Cloud Shell használatával az AZ PowerShell 2.3.2 vagy újabb verzió szükséges. Ha frissítenie kell, olvassa el [az Azure PowerShell-modul telepítése](/powershell/azure/install-az-ps)című témakört, vagy futtassa az alábbi mintaparancsfájlt az aktuális felhasználó moduljának telepítéséhez:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
