---
title: 'PowerShell: engedélyezze a saját kulcsú (BYOK) TDE'
titleSuffix: Azure SQL Managed Instance
description: Megtudhatja, hogyan konfigurálhat egy Azure SQL felügyelt példányt úgy, hogy az a PowerShell használatával megkezdje a saját kulcsú (BYOK) transzparens adattitkosítás (TDE) használatát a titkosításhoz.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: vanto, carlrab
ms.date: 11/05/2019
ms.openlocfilehash: cff98c0acfe06a9dbf9e3d7c7dae1b2411823d28
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051793"
---
# <a name="transparent-data-encryption-tde-in-a-sql-managed-instance-using-your-own-key-from-azure-key-vault"></a>Transzparens adattitkosítás (TDE) egy SQL felügyelt példányban a saját kulcsának használatával Azure Key Vault
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Ez a PowerShell-parancsfájl transzparens adattitkosítás (TDE) konfigurációját konfigurálja a felügyelt Azure SQL-példányhoz az ügyfél által felügyelt kulccsal, Azure Key Vault-kulcs használatával. Ezt gyakran nevezik Bring Your Own Key TDE-forgatókönyvnek. Ha többet szeretne megtudni az ügyfél által felügyelt kulccsal TDE, tekintse meg a [TDE bring your own Key az Azure SQL](../../database/transparent-data-encryption-byok-overview.md)-ben című témakört.

## <a name="prerequisites"></a>Előfeltételek

- Meglévő SQL felügyelt példány. Lásd: az [Azure SQL felügyelt példányának létrehozása a PowerShell használatával](create-configure-managed-instance-powershell.md).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

A PowerShell helyi vagy Azure Cloud Shell használatával történő használatához AZ PowerShell 2.3.2 vagy egy újabb verzió szükséges. Ha frissítenie kell, tekintse meg a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című részt, vagy futtassa az alábbi minta parancsfájlt a modul telepítéséhez az aktuális felhasználóhoz:

`Install-Module -Name Az -AllowClobber -Scope CurrentUser`

Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="sample-scripts"></a>Mintaparancsfájlok

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-database/transparent-data-encryption/setup-tde-byok-sqlmi.ps1 "Set up BYOK TDE for SQL Managed Instance")]

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/azure/overview).

További SQL felügyelt példányok PowerShell-parancsfájlokat az [Azure SQL felügyelt példány PowerShell-parancsfájljaiban](../../database/powershell-script-content-guide.md)találhat.
