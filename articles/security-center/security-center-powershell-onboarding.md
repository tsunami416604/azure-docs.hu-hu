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
ms.date: 10/2/2018
ms.author: rkarlin
ms.openlocfilehash: 756aadfb015ada8ea642e9e4893664eed3f6c9b2
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042552"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Előkészítés a PowerShell-lel az Azure Security Center automatizálása

Gondoskodhat az Azure-beli számítási programozott módon, az Azure Security Center PowerShell modullal.
PowerShell-lel lehetővé teszi, hogy a feladatok automatizálásához és a manuális feladatok járó az emberi hibák elkerülése érdekében. Ez különösen hasznos a nagyméretű telepítésekkel több tucat, több száz és több ezer erőforrások – amelyek mindegyike az elejétől kell védeni az előfizetést érintő.

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
6.  Indítsa újra a PowerShell

7. A PowerShellben futtassa a következő parancsokat:

         Install-Module -Name AzureRM.Security -AllowPrerelease -Force

## <a name="onboard-security-center-using-powershell"></a>Felvétele a Security Center PowerShell-lel

1.  Az előfizetés a Security Center erőforrás-szolgáltató regisztrálása:

        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Választható lehetőség: Az előfizetések lefedettség szint (tarifacsomag) beállítása (Ha nincs megadva, a tarifacsomag értéke ingyenes):

        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzureRmSecurityPricing -Name "default" -PricingTier "Standard"

3.  Az ügynökök jelentést, amelyhez a Log Analytics-munkaterület konfigurálása. Log Analytics-munkaterület már létrehozott, hogy az előfizetéshez tartozó virtuális gépek jelenteni fognak az kell rendelkeznie. Megadhatja, hogy több előfizetést, hogy ugyanazon a munkaterületen. Ha nincs megadva, használja az alapértelmezett munkaterületre.

        Set-AzureRmSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  A Microsoft Monitoring Agent szolgáltatást az Azure-beli virtuális gépek automatikus üzembe telepítését:
    
        Set-AzureRmContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzureRmSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Azt Javasoltjuk, győződjön meg arról, hogy az Azure-beli virtuális gépek az Azure Security Center által automatikusan védett, az Automatikus kiépítés engedélyezése.
    >

5.  Választható lehetőség: Azt javasoljuk, hogy a biztonsági kapcsolattartói adatok esetében az előfizetések felvétele, mint a riasztások és értesítések címzettjeinek fogja használni létrehozott határoz meg a Security Center által:

        Set-AzureRmSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertsAdmin -NotifyOnAlert 

6.  Az alapértelmezett a Security Center házirend kezdeményezés hozzárendelése:

        Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzureRmPolicySetDefinition -Name ' [Preview]: Enable Monitoring in Azure Security Center'
        New-AzureRmPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Akkor most sikerült előkészíteni az Azure Security Center a PowerShell használatával!

Mostantól használhatja a PowerShell-parancsmagok az automatizálási szkriptek között előfizetésekre és erőforrásokra programozott módon újrafuttathatja. Ezzel időt takaríthat meg, és csökkenti az emberi hibák valószínűségét. Ezzel [mintaparancsfájl](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) hivatkozásként van listázva.






## <a name="see-also"></a>Lásd még
Hogyan automatizálhatja a Security Center bevezetése használhatja PowerShell kapcsolatos további információkért tekintse meg a következő cikket:

* [AzureRM.Security](https://www.powershellgallery.com/packages/AzureRM.Security/0.2.0-preview).

A Security Centerrel kapcsolatos további tudnivalókért tekintse meg a következő cikket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
