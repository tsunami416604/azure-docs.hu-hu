---
title: A ASDK regisztrálása az Azure-ral |} A Microsoft Docs
description: Ismerteti, hogyan lehet regisztrálni az Azure Stack az Azure Marketplace-en tartalomtípus-gyűjtési és használati jelentések engedélyezése.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 19fb5d3cb793b6e1e8e715c41edf8cde5746278b
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257922"
---
# <a name="azure-stack-registration"></a>Azure Stack-regisztráció
Regisztrálhat az Azure Stack Development Kit (ASDK) telepítése az Azure marketplace-elemek letöltése az Azure-ból, és megkezdheti a Microsoft kereskedelmi adatok beállítása. Regisztráció teljes Azure Stack-funkciók, többek között a piactér tartalomtípus-gyűjtési támogatásához szükséges. Regisztrációs ajánlott, mivel lehetővé teszi, hogy tesztelje fontos Azure Stack-funkciók, például a Marketplace-en tartalomtípus-gyűjtési és használati jelentések készítése. Miután regisztrálta Azure Stack, az Azure kereskedelmi jelentett használati. Láthatja a regisztráció során használt előfizetés alatt. Azonban ASDK felhasználók nem számítunk fel díjat minden használati jelentést.

Ha nem regisztrál az ASDK, megjelenhet egy **aktiválás szükséges** figyelmeztetés, ami arról, hogy kell regisztrálni az Azure Stack Development Kit kapcsolódjanak. Ez a viselkedés várható.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt ezeket az utasításokat a ASDK regisztrálni az Azure-ral, győződjön meg arról, hogy az Azure Stack PowerShell telepítése és az Azure Stack eszközök letöltött leírtak szerint a [üzembe helyezés utáni konfigurációs](asdk-post-deploy.md) cikk.

Emellett a PowerShell nyelvmód értékre kell állítani **FullLanguageMode** azon a számítógépen, az Azure-ral a ASDK regisztrálhatók. Annak ellenőrzéséhez, hogy a jelenlegi nyelvmód beállítása teljes nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsokat:

```PowerShell  
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, a kimenetet visszaadja **FullLanguageMode**. Ha bármilyen más nyelvmód adja vissza, egy másik számítógépen kell futtatnia kell a regisztrációs vagy a nyelvmód kell beállítani **FullLanguageMode** a folytatás előtt.

## <a name="register-azure-stack-with-azure"></a>Regisztráljon az Azure Stack az Azure-ral
Kövesse az alábbi lépéseket a ASDK regisztrálni az Azure-ral.

> [!NOTE]
> Ezeket a lépéseket minden olyan számítógépen, amelyen a kiemelt végponthoz való hozzáférést adatbázisból kell futtatni. A ASDK Ez a fejlesztői csomag gazdaszámítógépen.

1. Nyisson meg egy PowerShell-konzolt rendszergazdaként.  

2. Futtassa a következő PowerShell-parancsokat a ASDK telepítési regisztrálni az Azure-ral. Jelentkezzen be az Azure-előfizetés és a helyi ASDK telepítési is kell. Ha nem rendelkezik Azure-előfizetéssel, még akkor is [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Az Azure-előfizetése ingyenes regisztrálása az Azure Stack tekintetében.

    Ha futtatja a regisztrációs parancsfájl az Azure Stack több példányának az azonos Azure-előfizetési Azonosítót használ, állítsa be egy egyedi nevet a regisztráció futtatásakor a **Set-AzsRegistration** parancsmagot. A **RegistrationName** paraméterének alapértelmezett értéke **AzureStackRegistration**. Azonban, ha ugyanazzal a névvel egynél több Azure Stack-példányt használ, a parancsfájl futtatása sikertelen lesz.

  ```PowerShell  
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $RegistrationName = "<unique-registration-name>"
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
      -RegistrationName $RegistrationName
  ```
3. Miután a parancsfájl futása befejeződött, megjelenik ez az üzenet: **környezete már regisztrálva van, és aktiválta a megadott paraméterekkel.**

    ![A környezet már regisztrálva van](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Ellenőrizze a regisztráció sikeres volt
Kövesse az alábbi lépéseket, győződjön meg arról, hogy sikeres volt-e a ASDK regisztráció az Azure-ral.

1. Jelentkezzen be a [Azure Stack felügyeleti portálon](https://adminportal.local.azurestack.external).

2. Kattintson a **Marketplace felügyeleti** > **hozzáadása az Azure-ból**.

    ![](media/asdk-register/2.PNG)

3. Ha látja az Azure-ból elérhető elemek listáját, az aktiválás sikeres volt.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>További lépések
[Azure Stack piactéren elem hozzáadása](.\.\azure-stack-marketplace.md)
