---
title: 'PowerShell: a BYOK TDE engedélyezése – Azure SQL Database felügyelt példány '
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL felügyelt példányt úgy, hogy a PowerShell használatával megkezdje a BYOK transzparens adattitkosítás (TDE) használatát a titkosításhoz.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73691404"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Felügyelt példányon lévő transzparens adattitkosítás kezelése a saját kulcsának használatával Azure Key Vault

Ez a PowerShell-parancsfájl transzparens adattitkosítás (TDE) konfigurációját konfigurálja a felügyelt Azure SQL-példányhoz az ügyfél által felügyelt kulccsal, Azure Key Vault-kulcs használatával. Ezt gyakran nevezik Bring Your Own Key TDE-forgatókönyvnek. Ha többet szeretne megtudni az ügyfél által felügyelt kulccsal TDE, tekintse meg a [TDE bring your own Key az Azure SQL](../transparent-data-encryption-byok-azure-sql.md)-ben című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő felügyelt példány. Lásd: [Azure SQL Database felügyelt példány létrehozása a PowerShell használatával](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A PowerShell helyi vagy Azure Cloud Shell használatával történő használatához AZ PowerShell 2.3.2 vagy egy újabb verzió szükséges. Ha frissítenie kell, tekintse meg a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című részt, vagy futtassa az alábbi minta parancsfájlt a modul telepítéséhez az aktuális felhasználóhoz:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaparancsfájlok

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
