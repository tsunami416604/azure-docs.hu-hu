---
title: 'PowerShell: a BYOK TDE engedélyezése – Azure SQL Database felügyelt példány | Microsoft Docs'
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
ms.date: 11/04/2019
ms.openlocfilehash: 0413216bc666aff504193d6723d46a6ee26be8ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500058"
---
# <a name="manage-transparent-data-encryption-in-a-managed-instance-using-your-own-key-from-azure-key-vault"></a>Felügyelt példányon lévő transzparens adattitkosítás kezelése a saját kulcsának használatával Azure Key Vault

Ez a PowerShell-parancsfájl az Azure SQL felügyelt példányának Bring Your Own Key (előzetes verzió) forgatókönyvében transzparens adattitkosítás (TDE) konfigurációját konfigurálja, amely a Azure Key Vault egyik kulcsát használja. Ha többet szeretne megtudni a TDE Bring Your Own Key (BYOK) támogatásával kapcsolatban, tekintse meg a [TDE bring your own Key az Azure SQL-](../transparent-data-encryption-byok-azure-sql.md)ben című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Egy meglévő felügyelt példány. Lásd: [Azure SQL Database felügyelt példány létrehozása a PowerShell használatával](sql-database-create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A PowerShell helyi vagy Azure Cloud Shell használatával történő használatához AZ PowerShell 1.1.1-Preview vagy egy újabb előzetes verzió szükséges. Ha frissítenie kell, lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps), vagy futtassa az alábbi minta parancsfájlt a modul telepítéséhez.

`Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease -Force`

Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaszkriptek

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL Database PowerShell szkriptminták találhatók az [Azure SQL Database PowerShell szkriptekben](../sql-database-powershell-samples.md).
