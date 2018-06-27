---
title: Regisztrálja a ASDK Azure |} Microsoft Docs
description: Ismerteti, hogyan lehet regisztrálni Azure verem Azure piactér szindikálási és a használati jelentések engedélyezése.
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
ms.date: 06/26/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 08a300d0e2d1565428f282a2073d91b5dd08c060
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016999"
---
# <a name="azure-stack-registration"></a>Az Azure verem regisztrációs
Az Azure-ral Piactéri elemek letölteni az Azure-ból, és megkezdheti a Microsoft commerce adatok beállítása az Azure verem Development Kit (ASDK) telepítése is regisztrálhat. Regisztrációs teljes Azure verem funkciókat, beleértve a Piactéri szindikálási támogatásához szükséges. Regisztrációs használata ajánlott, mivel lehetővé teszi a fontos Azure verem funkciók, például a piactér szindikálási és használatai jelentések tesztelése. Miután regisztrálta Azure verem, használati Azure kereskedelmi bejelentések. Megtekintheti az előfizetésben a regisztrációhoz használt. ASDK felhasználók azonban nem felszámított bármely használati jelentést.

Ha nem regisztrálja a ASDK, megjelenhet egy **szükséges aktiválási** figyelmeztető riasztás, amely azt ajánlja, hogy regisztrálja az Azure verem szoftverfejlesztői készlet. Ez az elvárt viselkedés.

## <a name="prerequisites"></a>Előfeltételek
Ezeket az utasításokat a ASDK regisztrálni Azure használatához győződjön meg arról, hogy a verem Azure PowerShell telepítése és az Azure-verem eszközök letöltött leírtak szerint a [telepítés utáni konfigurációjának](asdk-post-deploy.md) cikk.

Emellett a PowerShell nyelvi mód értékre kell állítani **FullLanguageMode** való regisztrálásához a ASDK Azure használt számítógépen. Győződjön meg arról, hogy a jelenlegi nyelvi mód állítsa teljes nyisson meg egy rendszergazda jogú PowerShell-ablakot, és futtassa a következő PowerShell-parancsokat:

```powershell
$ExecutionContext.SessionState.LanguageMode
```

Győződjön meg arról, a kimenetet visszaadja **FullLanguageMode**. Ha más nyelv módot ad vissza, regisztrációs kell egy másik számítógépen kell futtatnia, vagy a nyelvi módot kell beállítani **FullLanguageMode** folytatása előtt.

## <a name="register-azure-stack-with-azure"></a>Azure verem regisztrálni Azure-ral
Kövesse az alábbi lépéseket a ASDK regisztrálni Azure-ral.

> [!NOTE]
> Ezeket a lépéseket egy számítógépről, amely hozzáfér a kiemelt végpontot kell futtatni. A ASDK esetén, amelyek a development kit gazdaszámítógépen.

1. Nyissa meg rendszergazdaként a PowerShell-konzolban.  

2. Futtassa a következő PowerShell-parancsok futtatásával regisztrálja a ASDK telepített Azure. Jelentkezzen be az Azure-előfizetéssel, mind a helyi ASDK telepítési kell. Ha nem rendelkezik Azure-előfizetéssel, még akkor is [ingyenes Azure-fiók létrehozása itt](https://azure.microsoft.com/free/?b=17.06). Azure verem regisztrálása költséget nem áll, az Azure-előfizetése.

  ```powershell
  # Add the Azure cloud subscription environment name. Supported environment names are AzureCloud or, if using a China Azure Subscription, AzureChinaCloud.
  Add-AzureRmAccount -EnvironmentName "AzureCloud"

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  #Register Azure Stack
  $AzureContext = Get-AzureRmContext
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01 `
      -BillingModel Development
  ```
3. A parancsfájl befejezése után ez az üzenet jelenik meg: **a környezet most már regisztrálva van-e, és a megadott paraméterek aktiválva.**

    ![](media/asdk-register/1.PNG)

## <a name="verify-the-registration-was-successful"></a>Ellenőrizze, hogy a regisztráció sikeres volt
A lépések végrehajtásával ellenőrizze, hogy sikeres volt-e a ASDK regisztráció az Azure-ral.

1. Jelentkezzen be a [verem Azure felügyeleti portálján](https://adminportal.local.azurestack.external).

2. Kattintson a **piactér felügyeleti** > **adja hozzá az Azure-ból**.

    ![](media/asdk-register/2.PNG)

3. Ha az Azure-ból rendelkezésre álló elemek listája látható, az aktiválás sikeres volt.

    ![](media/asdk-register/3.PNG)

## <a name="next-steps"></a>További lépések
[Vegyen fel egy Azure verem Piactéri elemet](.\.\azure-stack-marketplace.md)
