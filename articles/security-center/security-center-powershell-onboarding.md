---
title: Azure Security Center a PowerShell-lel
description: Ez a dokumentum végigvezeti a Azure Security Center PowerShell-parancsmagokkal történő bevezetésének folyamatán.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2018
ms.author: memildin
ms.openlocfilehash: 5aaaf539c07a7ba2c2463d5bfd1f452853f52379
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603691"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Azure Security Center bevezetésének automatizálása a PowerShell használatával

Az Azure számítási feladatait programozott módon, a Azure Security Center PowerShell-modul használatával biztosíthatja.
A PowerShell használatával automatizálhatja a feladatokat, és elkerülheti a manuális feladatokban rejlő emberi hibákat. Ez különösen hasznos a nagy léptékű központi telepítések esetében, amelyek több tucat előfizetést foglalnak magukban több száz vagy több ezer erőforrással – mindezt az elejétől védeni kell.

A PowerShell használatával történő bevezetési Azure Security Center lehetővé teszi az Azure-erőforrások bevezetését és felügyeletét, valamint a szükséges biztonsági vezérlők hozzáadását.

Ez a cikk egy minta PowerShell-parancsfájlt tartalmaz, amely módosítható és használható a környezetben az előfizetések Security Centerének kiszámításához. 

Ebben a példában a következő AZONOSÍTÓval rendelkező előfizetések esetében engedélyezzük a Security Centert: d07c0080-170c-4c24-861d-9c817742786c és a magas szintű védelmet biztosító ajánlott beállítások alkalmazása a Security Center Standard szintjének megvalósításával, amely a következőket biztosítja: komplex veszélyforrások elleni védelem és észlelési képességek:

1. Állítsa be a [Security Center standard szintű védelmet](https://azure.microsoft.com/pricing/details/security-center/). 
 
2. Állítsa be azt a Log Analytics munkaterületet, amelyhez a Microsoft monitoring Agent az előfizetéshez társított virtuális gépeken gyűjtött adatokat küldi el – ebben a példában egy meglévő felhasználó által definiált munkaterületet (Sajátmunkaterület).

3. Aktiválja Security Center automatikus ügynökének [üzembe helyezését, amely telepíti a Microsoft monitoring Agent ügynököt](security-center-enable-data-collection.md#auto-provision-mma).

5. A szervezet CISO adja meg [Security Center riasztások és jelentős események biztonsági kapcsolattartójának](security-center-provide-security-contact-details.md).

6. Security Center [alapértelmezett biztonsági házirendjeinek](tutorial-security-policy.md)kiosztása.

## <a name="prerequisites"></a>Előfeltételek

Ezeket a lépéseket az Security Center-parancsmagok futtatása előtt kell végrehajtani:

1.  Futtassa a PowerShellt rendszergazdaként.
2.  Futtassa a következő parancsokat a PowerShellben:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Security Center a PowerShell használatával

1.  Regisztrálja előfizetéseit a Security Center erőforrás-szolgáltatónál:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Nem kötelező: az előfizetések lefedettségi szintjének (árképzési szint) beállítása (ha nincs meghatározva, az árképzési szint szabad értékre van állítva):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Konfiguráljon egy Log Analytics munkaterületet, amelyre az ügynökök jelentést küldenek. Rendelkeznie kell egy már létrehozott Log Analytics-munkaterülettel, amelyet az előfizetés virtuális gépei fognak jelenteni. Több előfizetést is megadhat ugyanahhoz a munkaterülethez. Ha nincs meghatározva, a rendszer az alapértelmezett munkaterületet fogja használni.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  A Microsoft monitoring Agent Automatikus üzembe helyezése az Azure-beli virtuális gépeken:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Javasoljuk, hogy az automatikus kiépítés engedélyezésével győződjön meg arról, hogy az Azure-beli virtuális gépeket a Azure Security Center automatikusan védi.
    >

5.  Nem kötelező: erősen ajánlott megadnia a beépített előfizetések biztonsági kapcsolattartási adatait, amelyeket a rendszer a Security Center által generált riasztások és értesítések címzettjeiként használ:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Az alapértelmezett Security Center házirend-kezdeményezés kiosztása:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Sikeresen bevezetést Azure Security Center a PowerShell-lel!

Ezeket a PowerShell-parancsmagokat az Automation-parancsfájlok segítségével programozott módon is megismételheti az előfizetések és az erőforrások között. Ezzel időt takaríthat meg, és csökkentheti az emberi hiba valószínűségét. A [minta parancsfájl](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) hivatkozásként használható.






## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni arról, hogy a PowerShell Hogyan automatizálható a Security Center bevezetésének automatizálásához, tekintse meg a következő cikket:

* [Az. Security](https://docs.microsoft.com/powershell/module/az.security).

Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő cikket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.