---
title: Alaplap az Azure Security Centerbe a PowerShell segítségével
description: Ez a dokumentum végigvezeti az Azure Security Center PowerShell-parancsmagok használatával történő bevezetés folyamatán.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603691"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>Az Azure Security Center bevezetésautomatizálása a PowerShell használatával

Az Azure Security Center PowerShell moduljával programozott módon biztosíthatja az Azure-beli számítási feladatokat.
A PowerShell használatával automatizálhatja a feladatokat, és elkerülheti a manuális feladatokban rejlő emberi hibát. Ez különösen hasznos a nagy méretű telepítések, amelyek több tucat előfizetések több száz és több ezer erőforrást – amelyek mindegyike biztosítani kell a kezdetektől fogva.

Az Azure Security Center PowerShell használatával történő bevezetés lehetővé teszi, hogy programozott módon automatizálja az Azure-erőforrások bevezetést és az Azure-erőforrások felügyeletét, és adja hozzá a szükséges biztonsági vezérlőket.

Ez a cikk egy minta PowerShell-parancsfájlt tartalmaz, amely módosítható és használható a környezetben a Security Center előfizetések közötti bevezetéséhez. 

Ebben a példában engedélyezzük a Security Center előfizetést egy azonosítóval rendelkező előfizetésen: d07c0080-170c-4c24-861d-9c817742786c, és alkalmazzuk az ajánlott beállításokat, amelyek magas szintű védelmet biztosítanak a Security Center standard szintű alkalmazásának megvalósításával, amely fejlett veszélyforrások elleni védelem és észlelési képességek:

1. Állítsa be a [Security Center szabványos védelmi szintjét.](https://azure.microsoft.com/pricing/details/security-center/) 
 
2. Állítsa be a Log Analytics munkaterületet, amelyre a Microsoft Monitoring Agent elküldi az általa gyűjtött adatokat az előfizetéshez társított virtuális gépeken – ebben a példában egy meglévő felhasználó által definiált munkaterületet (myWorkspace).

3. Aktiválja a Security Center automatikus ügynök-kiépítését, amely [telepíti a Microsoft Monitoring Agent szolgáltatást.](security-center-enable-data-collection.md#auto-provision-mma)

5. Állítsa be a szervezet [CISO-ját a Security Center riasztásainak és figyelemre méltó eseményeinek biztonsági kapcsolattartójaként.](security-center-provide-security-contact-details.md)

6. Rendelje hozzá a Security Center [alapértelmezett biztonsági házirendjeit.](tutorial-security-policy.md)

## <a name="prerequisites"></a>Előfeltételek

Ezeket a lépéseket a Security Center parancsmagok futtatása előtt kell végrehajtani:

1.  Futtassa a PowerShellt rendszergazdaként.
2.  Futtassa a következő parancsokat a PowerShellben:
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>Beépített biztonsági központ a PowerShell használatával

1.  Előfizetések regisztrálása a Biztonsági központ erőforrás-szolgáltatójában:

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  Nem kötelező: Állítsa be az előfizetések fedezeti szintjét (tarifacsomag) (ha nincs megadva, a tarifacsomag szabadra van állítva):

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  Konfiguráljon egy Log Analytics-munkaterületet, amelyre az ügynökök jelentést tesznek. Rendelkeznie kell egy Log Analytics munkaterület, amely már létrehozott, hogy az előfizetés virtuális gépek jelentést. Több előfizetést is definiálhat, amelyek ugyanarra a munkaterületre vonatkoznak. Ha nincs megadva, a rendszer az alapértelmezett munkaterületet fogja használni.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  A Microsoft Monitoring Agent automatikus üzembe helyezése az Azure virtuális gépein:
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > Javasoljuk, hogy engedélyezze az automatikus kiépítést annak érdekében, hogy az Azure virtuális gépeit automatikusan védje az Azure Security Center.
    >

5.  Nem kötelező: Erősen ajánlott megadni a beépített előfizetések biztonsági kapcsolattartási adatait, amelyeket a Rendszer a Security Center által létrehozott riasztások és értesítések címzettjeiként fog használni:

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  Rendelje hozzá az alapértelmezett Biztonsági központ házirend-kezdeményezést:

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

Most már sikeresen beszállt az Azure Security Centerbe a PowerShell segítségével!

Most már használhatja ezeket a PowerShell-parancsmagokat az automatizálási parancsfájlokkal, hogy programozott módon iteráljon előfizetések és erőforrások között. Ez időt takarít meg, és csökkenti annak valószínűségét, az emberi hiba. Ezt a [mintaparancsfájlt](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) referenciaként használhatja.






## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni arról, hogy miként automatizálhatja a PowerShellt a Biztonsági központba való bevezetés automatizálására, olvassa el az alábbi cikket:

* [Az.Security](https://docs.microsoft.com/powershell/module/az.security).

A Security Center ről az alábbi cikkben olvashat bővebben:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – Ismerje meg, hogyan kezelheti és válaszolhat azokra a biztonsági riasztásokra.