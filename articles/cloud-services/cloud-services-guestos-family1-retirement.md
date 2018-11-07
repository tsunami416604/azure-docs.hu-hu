---
title: Vendég operációsrendszer-család 1 kivezetéséről szóló értesítés |} A Microsoft Docs
description: Ismerteti, mikor történt a az Azure Vendég operációsrendszer-család 1 használatból való kivonást egyaránt, és miként állapítható meg, ha Ön érintett
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 37b422e9-0713-4a81-a942-f553ef478064
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: d6429766b6aac547fd99279659acb1067298e77c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51244678"
---
# <a name="guest-os-family-1-retirement-notice"></a>Vendég operációsrendszer-család 1 használatból való kivonást egyaránt értesítés
Operációsrendszer-család 1 funkciókészletét először a 2013. június 1-én mutattuk be.

**2014 Szeptembertől 2** az Azure vendég operációs rendszerek (Guest OS) család 1.x, amely a Windows Server 2008 operációs rendszeren alapul, a rendszer hivatalosan elavult. Új szolgáltatások telepítését, vagy frissítse a meglévő szolgáltatások használatával család 1 kísérleteit sikertelen lesz, és a egy hibaüzenet tájékoztatja, hogy a vendég operációs rendszer termékcsalád 1 kivonásra került.

**2014. novemberi 3** Vendég operációsrendszer-család 1 kiterjesztett technikai támogatása véget ért, és teljes mértékben kivonták. Minden szolgáltatás továbbra is a család 1 érinti. Ezek a szolgáltatások bármikor leállíthatja azt. Nincs garancia arra, a szolgáltatások továbbra is futtassa, ha manuálisan frissíti őket saját maga.

Ha további kérdése van, keresse fel a [Cloud Services-fórumok](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) vagy [lépjen kapcsolatba az Azure-támogatás](https://azure.microsoft.com/support/options/).

## <a name="are-you-affected"></a>Érintettek?
A Cloud Services érintettek, ha a következők bármelyike vonatkozik:

1. Az érték "operációsrendszer-család ="1"a felhőszolgáltatás a ServiceConfiguration.cscfg fájlban explicit módon megadott.
2. A felhőszolgáltatás a ServiceConfiguration.cscfg fájlban explicit módon megadott osFamily érték nem rendelkezik. Jelenleg a rendszer használja az alapértelmezett érték "1" Ebben az esetben.
3. Az Azure Portalon "A Windows Server 2008" a vendég operációs rendszer család érték listája.

Keresse meg, amely a cloud services futnak, hogy melyik operációsrendszer-család, futtathatja a következő szkriptet az Azure PowerShell, azonban meg kell [beállítása az Azure PowerShell-lel](/powershell/azureps-cmdlets-docs) első. A szkript további információkért lásd: [Azure vendég operációs rendszer termékcsalád 1 záró az élettartam: 2014. június](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

A cloud services érinti, operációsrendszer-család 1 használatból való kivonást egyaránt, ha a parancsfájl kimenetében az osFamily oszlop üres, vagy tartalmaz egy "1".

## <a name="recommendations-if-you-are-affected"></a>Ha érinti javaslatok
Javasoljuk, hogy a Cloud Service szerepkörök áttelepítéséhez a támogatott vendég operációsrendszer-családok egyikét:

**A vendég operációs rendszer családi 4.x** – Windows Server 2012 R2 *(ajánlott)*

1. Győződjön meg arról, hogy az alkalmazás SDK 2.1-es vagy újabb verzióját használja a .NET-keretrendszer 4.0-s, 4.5-ös és 4.5.1-es verzióját.
2. Állítsa be az osFamily attribútum "4"-re a ServiceConfiguration.cscfg fájlban, és ismételt üzembe helyezése a cloud Services.

**A vendég operációs rendszer családi 3.x** – Windows Server 2012

1. Győződjön meg arról, hogy az alkalmazás a .NET-keretrendszer 4.0 vagy 4.5-ös használ SDK 1.8-as vagy újabb verziója.
2. Az osFamily attribútum "3" állítsa a ServiceConfiguration.cscfg fájlban, és ismételt üzembe helyezése a cloud Services.

**A vendég operációs rendszer családi 2.x** – Windows Server 2008 R2

1. Győződjön meg arról, hogy az alkalmazás használ SDK 1.3-as vagy újabb verzió a .NET-keretrendszer 3.5-ös vagy 4.0-s verzióját.
2. Az osFamily attribútum "2" állítsa a ServiceConfiguration.cscfg fájlban, és ismételt üzembe helyezése a cloud Services.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Vendég operációsrendszer-család 1 meghosszabbított támogatása véget ért, 2014. november 3.
1. Vendég operációsrendszer-család a cloud services már nem támogatottak. Áttelepítés kikapcsolása a szolgáltatáskimaradás elkerülése érdekében a lehető leghamarabb 1. termékcsaládját.  

## <a name="next-steps"></a>További lépések
Tekintse át a legújabb [vendég operációs rendszer kiadással](cloud-services-guestos-update-matrix.md).
