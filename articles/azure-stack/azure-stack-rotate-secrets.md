---
title: Forgassa el a titkos kulcsok Azure verem |} Microsoft Docs
description: "Megtudhatja, hogyan forgassa el a titkos kulcsok Azure-készletben."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: 9b65a3cb5cdcc8a558e5c989026f2eee2f527bb5
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Forgassa el a titkos kulcsok Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Frissítés Azure verem összetevők egy rendszeres ütemben történik, a jelszavakat.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Az alaplapi felügyeleti vezérlővel (BMC) jelszava frissítése

Az alaplapi felügyeleti vezérlővel (BMC) a kiszolgálók fizikai állapotának figyelése. A paramétereknek, valamint útmutatást a gazdagépben található bmc jelszófrissítés számítógépgyártó (OEM) hardvergyártójához függően változhat.

1. A bmc-n a kiszolgáló frissítése az OEM utasításai szerint. A környezetben minden BMC jelszavát azonosnak kell lennie.
2. Nyissa meg a kiemelt végpont Azure verem munkamenetek. Utasítás, lásd: [használatával a privilegizált végpont Azure verem](azure-stack-privileged-endpoint.md).
3. Után a PowerShell parancssorba változott **[IP-cím vagy ERCS virtuális gép neve]: PS >** vagy **[azs-ercs01]: PS >**a környezet futtatása attól függően, hogy `Set-BmcPassword` futtatásával `invoke-command`. A privilegizált végpont munkamenet-változó továbbítsa paraméterként.  
Példa:
    ```powershell
    $PEPSession = New-PSSession -ComputerName <ERCS computer name> -Credential <CloudAdmin credential> -ConfigurationName "PrivilegedEndpoint"  
    
    Invoke-Command -Session $PEPSession -ScriptBlock {
        param($password)
        set-bmcpassword -bmcpassword $password
    } -ArgumentList (<LatestPassword as a SecureString>) 
    ```

## <a name="next-steps"></a>További lépések

A biztonsági és Azure verem kapcsolatos további információkért lásd: [Azure verem infrastruktúra biztonságot](azure-stack-security-foundations.md).