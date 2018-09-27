---
title: A PowerShell használatával előkészítése az Azure Security Center és a hálózat védelme |} A Microsoft Docs
description: Ez a dokumentum végigvezeti a bevezetése az Azure Security Center PowerShell-parancsmagok használatával folyamatán.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/20/2018
ms.author: rkarlin
ms.openlocfilehash: 4664a9f84a92b7a223409d764971fda81317bbf0
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222250"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Előkészítés a PowerShell-lel az Azure Security Center automatizálása

Gondoskodhat az Azure-beli számítási programozott módon, az Azure Security Center PowerShell modullal.
PowerShell-lel lehetővé teszi a feladatok automatizálását, és ez különösen hasznos a nagyméretű telepítésekkel több tucat, több száz és erőforrások – mindegyike védelméhez a több ezer előfizetések érintő manuális feladatok rejlő az emberi hibák elkerülése érdekében kezdete.

Bevezetési PowerShell-lel az Azure Security Center lehetővé teszi, hogy programozott módon regisztrációs és az Azure-erőforrások felügyeletének automatizálására, és adja hozzá a szükséges biztonsági ellenőrzések.

Ez a cikk egy minta PowerShell-parancsfájlt, hogy módosítani kell, és az előfizetések között a Security Center bevezetése a környezetben használt. 

Ebben a példában azt engedélyezi a Security Center egy adott előfizetés-azonosító: d07c0080-170c-4c24-861d-9c817742786c, és alkalmazza az ajánlott beállításokat, amelyek egy magas szintű védelemmel, a Standard szintű Security Centernek, amely biztosítja az életbe léptetésével a komplex veszélyforrások elleni védelmi és észlelési funkcióihoz:

1. Állítsa be a [ASC standard szintű védelmet](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Állítsa be a Log Analytics-munkaterületet, amelyhez a Microsoft Monitoring Agent fog küldeni az előfizetéshez – ebben a példában egy meglévő felhasználó által definiált munkaterületet (myWorkspace) tartozó az adatokat gyűjt a virtuális gépeken.

3. A Security Center automatikus ügynökének üzembe helyezési, amely aktiválja [telepíti a Microsoft Monitoring Agent](security-center-enable-data-collection.md#auto-provision-mma).

5. Állítsa be a szervezet [CISO, mint a biztonsági kapcsolattartó ASC riasztások és a jelentős események](security-center-provide-security-contact-details.md).

6. Rendelje hozzá a Security Center [alapértelmezett biztonsági házirendek](security-center-azure-policy.md).

## <a name="prerequisites"></a>Előfeltételek

Ezeket a lépéseket kell elvégezni, a Security Center-parancsmagok futtatása előtt:

1.  PowerShell futtatása rendszergazdaként
2.  A PowerShellben futtassa a következő parancsokat:
      
        Install-Module -Name PowerShellGet -Force
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Import-Module PowerShellGet
        Install-Module -Name AzureRM.profile -RequiredVersion 5.5.0
6.  Indítsa újra a PowerShell

7. A PowerShellben futtassa a következő parancsokat:

         Install-Module -Name AzureRM.Security -AllowPrerelease -Force

## <a name="onboard-security-center-using-powershell"></a>Felvétele a Security Center PowerShell-lel

1.  Az előfizetés a Security Center erőforrás-szolgáltató regisztrálása:

        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c”
        Register-AzureRmResourceProvider -ProviderNamespace ‘Microsoft.Security’ 

2.  Választható lehetőség: Az előfizetések lefedettség szint (tarifacsomag) beállítása (Ha nincs megadva, a tarifacsomag értéke ingyenes):

        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c”
        Set-AzureRmSecurityPricing -Name "default" -PricingTier "Standard"

3.  Az ügynökök jelentést, amelyhez a Log Analytics-munkaterület konfigurálása. Log Analytics-munkaterület már létrehozott, hogy az előfizetéshez tartozó virtuális gépek jelenteni fognak az kell rendelkeznie. Megadhatja, hogy több előfizetést, hogy ugyanazon a munkaterületen. Ha nincs megadva, használja az alapértelmezett munkaterületre.

        Set-AzureRmSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  A Microsoft Monitoring Agent szolgáltatást az Azure-beli virtuális gépek automatikus üzembe telepítését:
    
        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c”
    
        Set-AzureRmSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Azt Javasoltjuk, győződjön meg arról, hogy az Azure-beli virtuális gépek az Azure Security Center által automatikusan védett, az Automatikus kiépítés engedélyezése.
    >

5.  Választható lehetőség: Azt javasoljuk, hogy a biztonsági kapcsolattartói adatok esetében az előfizetések felvétele, mint a riasztások és értesítések címzettjeinek fogja használni létrehozott határoz meg a Security Center által:

        Set-AzureRmSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertsAdmin -NotifyOnAlert 

6.  Az alapértelmezett a Security Center házirend kezdeményezés hozzárendelése:

        Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzureRmPolicySetDefinition -Name ' [Preview]: Enable Monitoring in Azure Security Center'
        New-AzureRmPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope ‘/subscriptions/d07c0080-170c-4c24-861d-9c817742786c’

Akkor most sikerült előkészíteni az Azure Security Center a PowerShell használatával!

Mostantól használhatja a PowerShell-parancsmagok az automatizálási szkriptek között előfizetésekre és erőforrásokra programozott módon újrafuttathatja. Ezzel időt takaríthat meg, és csökkenti az emberi hibák valószínűségét. Ezzel [mintaparancsfájl](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) hivatkozásként van listázva.






## <a name="see-also"></a>Lásd még
Hogyan automatizálhatja a Security Center bevezetése használhatja PowerShell kapcsolatos további információkért tekintse meg a következő cikket:

* [AzureRM.Security](https://www.powershellgallery.com/packages/AzureRM.Security/0.1.0-preview).

A Security Centerrel kapcsolatos további tudnivalókért tekintse meg a következő cikket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
