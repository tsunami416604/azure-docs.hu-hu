---
title: "Regisztrálja a ASDK Azure |} Microsoft Docs"
description: "Ismerteti, hogyan lehet regisztrálni Azure verem Azure piactér szindikálási és a használati jelentések engedélyezése."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 9e2dbc71f6424b87945e346a42c86d4cde7f740e
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral
Az Azure-ral Piactéri elemek letölteni az Azure-ból, és megkezdheti a Microsoft commerce adatok beállítása az Azure verem Development Kit (ASDK) telepítése is regisztrálhat. Regisztrációs használata ajánlott, mivel lehetővé teszi a fontos Azure verem funkciók, például a piactér szindikálási és használatai jelentések tesztelése. Miután regisztrálta Azure verem, használati Azure kereskedelmi bejelentések. Megtekintheti az előfizetésben a regisztrációhoz használt. ASDK felhasználók azonban nem felszámított bármely használati jelentést.


## <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral 
Kövesse az alábbi lépéseket a ASDK regisztrálni Azure-ral.

> [!NOTE]
> Ezeket a lépéseket egy számítógépről, amely hozzáfér a kiemelt végpontot kell futtatni. A ASDK esetén, amelyek a development kit gazdaszámítógépen. 

Az alábbi lépések végrehajtásával a ASDK regisztrálni Azure-ral, előtt győződjön meg arról, hogy a verem Azure PowerShell telepítése és az Azure-verem eszközök letöltött leírtak szerint a [telepítés utáni konfigurációjának](asdk-post-deploy.md) cikk. 

1. Nyissa meg rendszergazdaként a PowerShell-konzolban.  

2. A következő PowerShell parancsok futtatásával regisztrálja a ASDK telepített Azure (kell-e jelentkezni az Azure-előfizetéssel, mind a helyi ASDK telepítési):

    ```PowerShell
    # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
    Add-AzureRmAccount -EnvironmentName "AzureCloud"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    #Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    #Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"
    Set-AzsRegistration `
        -CloudAdminCredential $CloudAdminCred `
        -PrivilegedEndpoint AzS-ERCS01 `
        -BillingModel Development

3. When the script completes, you should see this message: **Your environment is now registered and activated using the provided parameters.**

    ![](media/asdk-register/1.PNG) 

## Verify the registration was successful
Follow these steps to verify that the ASDK registration with Azure was successful.

1. Sign in to the [Azure Stack administration portal](https://adminportal.local.azurestack.external).

2. Click **Marketplace Management** > **Add from Azure**.

    ![](media/asdk-register/2.PNG) 

3. If you see a list of items available from Azure, your activation was successful.

    ![](media/asdk-register/3.PNG) 

## Next steps
[Add an Azure Stack marketplace item](asdk-marketplace-item.md)