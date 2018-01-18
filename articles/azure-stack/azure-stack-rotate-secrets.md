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
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Forgassa el a titkos kulcsok Azure verem

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Frissítés Azure verem összetevők egy rendszeres ütemben történik, a jelszavakat.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Az alaplapi felügyeleti vezérlővel (BMC) jelszava frissítése

Az alaplapi felügyeleti vezérlővel (BMC) a kiszolgálók fizikai állapotának figyelése. A paramétereknek, valamint útmutatást a gazdagépben található bmc jelszófrissítés számítógépgyártó (OEM) hardvergyártójához függően változhat.

1. Frissítse a bmc-hez, az Azure-verem fizikai kiszolgálókon az OEM utasításai szerint. A környezetben minden BMC jelszavát azonosnak kell lennie.
2. Nyissa meg a kiemelt végpont Azure verem munkamenetek. Utasítás, lásd: [használatával a privilegizált végpont Azure verem](azure-stack-privileged-endpoint.md).
3. Után a PowerShell parancssorba változott **[IP-cím vagy ERCS virtuális gép neve]: PS >** vagy **[azs-ercs01]: PS >**a környezet futtatása attól függően, hogy `Set-BmcPassword` futtatásával `invoke-command`. A privilegizált végpont munkamenet-változó továbbítsa paraméterként. Példa:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    A jelszavak kód vonalakként is használható a statikus PowerShell-verzió:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>További lépések

A biztonsági és Azure verem kapcsolatos további információkért lásd: [Azure verem infrastruktúra biztonságot](azure-stack-security-foundations.md).
