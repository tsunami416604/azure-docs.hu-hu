---
title: "Vendég operációsrendszer-család 1 használatból való kivonást figyelje meg |} Microsoft Docs"
description: "Ha az Azure vendég operációs rendszer családja 1 használatból való kivonást történt és annak megállapítása, hogy hatással nyújt információkat"
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: 3178a09dab1cb972a3460d54dc9908fb95cce68b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="guest-os-family-1-retirement-notice"></a>Vendég operációsrendszer-család 1 használatból való kivonást értesítés
Az operációsrendszer-család 1 kivonása 2013. június 1 bejelentése volt.

**2014. szeptember 2** az Azure vendég operációs rendszer (a vendég operációs rendszer) termékcsalád 1.x, amely a Windows Server 2008 operációs rendszeren alapul, hivatalosan lett visszavonva. Az új szolgáltatásokat telepíti, vagy frissítse a meglévő szolgáltatások termékcsalád 1 használatával bármilyen kísérlet sikertelen lesz, és hibaüzenetet tájékoztatja arról, hogy a vendég operációs rendszer családja 1 eltávolították.

**2014. novemberi 3** kibővített támogatása vendég operációs rendszer családja 1 befejeződik, és a teljes kivonják. Minden szolgáltatás továbbra is a családja 1 csökkenhet. Bármikor előfordulhat, hogy ezek a szolgáltatások leállítása. Nincs a szolgáltatások tovább fut, ha manuálisan frissíti őket saját kezűleg garancia.

Ha további kérdésekre, keresse fel a [Cloud Services fórumban](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) vagy [kérje az Azure támogatási](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Érintettek?
A Cloud Services érintett közül legalább az alábbi esetekben alkalmazza:

1. Az érték "osFamily ="1"explicit módon megadott a ServiceConfiguration.cscfg fájlban a felhőalapú szolgáltatáshoz.
2. Nincs a felhőszolgáltatás explicit módon a ServiceConfiguration.cscfg fájlban megadott osFamily értékét. Jelenleg a rendszer használja az alapértelmezett érték "1" Ebben az esetben.
3. Az Azure-portálon, a "Windows Server 2008" sorolja fel a Vendég operációsrendszer-családba tartozó értéket.

Kereséséhez, amelyek a felhőalapú szolgáltatások futnak melyik operációsrendszer-család, futtathatja a következő parancsfájl az Azure PowerShell, bár kell [beállítása az Azure PowerShell](/powershell/azureps-cmdlets-docs) első. A parancsfájl további információkért lásd: [Azure vendég operációs rendszer termékcsalád 1 vége az élettartam: 2014. június](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

A felhőszolgáltatások által érintett operációsrendszer-család 1 kivonása, ha az osFamily a parancsfájl kimenetében üres vagy tartalmaz egy "1".

## <a name="recommendations-if-you-are-affected"></a>Ha érinti javaslatok
Azt javasoljuk, hogy a Felhőszolgáltatási szerepkörök áttelepítéséhez a támogatott vendég operációsrendszer-családok egyikét:

**Vendég operációs rendszer termékcsalád 4.x** – Windows Server 2012 R2 *(ajánlott)*

1. Győződjön meg arról, hogy az alkalmazás SDK 2.1-es vagy újabb használ a .NET-keretrendszer 4.0-s, 4.5-ös és 4.5.1-es verzióját.
2. Az osFamily attribútum "4" értékre a ServiceConfiguration.cscfg fájlban, és telepítse újra a felhőalapú szolgáltatás.

**Vendég operációs rendszer termékcsalád 3.x** – Windows Server 2012-ben

1. Győződjön meg arról, hogy az alkalmazás SDK 1,8 vagy újabb használ a .NET-keretrendszer 4.0 vagy 4.5.
2. Az osFamily attribútum "3" értékre a ServiceConfiguration.cscfg fájlban, és telepítse újra a felhőalapú szolgáltatás.

**Vendég operációs rendszer termékcsalád 2.x** – Windows Server 2008 R2

1. Győződjön meg arról, hogy az alkalmazás SDK 1.3 használ, és a fenti .NET-keretrendszer 3.5-ös vagy 4.0-s verziójával.
2. Az osFamily attribútum "2" értékre a ServiceConfiguration.cscfg fájlban, és telepítse újra a felhőalapú szolgáltatás.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Kiterjesztett terméktámogatás a Vendég operációsrendszer-család 1 rendszerhez 2014. november 3.
A Vendég operációsrendszer-család 1 felhőszolgáltatások támogatása megszűnt. Áttelepítés kikapcsolása termékcsalád 1 szolgáltatás megszűnésének megelőzése érdekében a lehető leghamarabb.  

## <a name="next-steps"></a>Következő lépések
Tekintse át a legutóbbi [feloldja a vendég operációs rendszer](cloud-services-guestos-update-matrix.md).
