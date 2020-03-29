---
title: Vendég OS család 1 nyugdíjazási értesítés | Microsoft dokumentumok
description: Tájékoztatást nyújt arról, hogy mikor történt az Azure Guest OS Family 1 nyugdíjba vonulása, és hogyan állapítható meg, hogy ön érintett-e
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
ms.service: cloud-services
ms.topic: article
ms.date: 5/21/2017
ms.author: raiye
ms.openlocfilehash: ae2df6f47d99fc5d452a6d3ea70f2dd2e4e7416b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359587"
---
# <a name="guest-os-family-1-retirement-notice"></a>1. vendég operációsrendszer-család használatból való kivonásáról szóló értesítés
Az OS Family 1 nyugdíjba vonulását először 2013.

**2014. szeptember 2.** A Windows Server 2008 operációs rendszeren alapuló Azure Guest operációs rendszer (Guest OS) Family 1.x hivatalosan megszüntetve lett. Az új szolgáltatások üzembe helyezésére vagy a meglévő szolgáltatások frissítésére tett minden kísérlet sikertelen lesz egy hibaüzenettel, amely tájékoztatja, hogy a Vendég operációsrendszer-család 1 kilett vonva.

**2014. november 3.** A Vendég operációsrendszer-család 1 kiterjesztett támogatása befejeződött, és teljesen visszavonult. Az 1-es család minden szolgáltatása érintett lesz. Ezeket a szolgáltatásokat bármikor leállíthatjuk. Nincs garancia arra, hogy a szolgáltatások továbbra is futni fognak, hacsak nem frissíti őket manuálisan.

Ha további kérdései vannak, keresse fel a [Felhőszolgáltatások fórumait,](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) vagy lépjen kapcsolatba az [Azure ügyfélszolgálatával.](https://azure.microsoft.com/support/options/)

## <a name="are-you-affected"></a>Ön érintett?
A felhőszolgáltatásokat érinti, ha az alábbiak valamelyike érvényes:

1. Az "osFamily = "1" érték kifejezetten meg van adva a Cloud Service ServiceConfiguration.cscfg fájlban.
2. Nincs értéke az osFamily kifejezetten megadott ServiceConfiguration.cscfg fájlban a Cloud Service. Jelenleg a rendszer ebben az esetben az "1" alapértelmezett értéket használja.
3. Az Azure portal a vendég operációs rendszer családi értékét "Windows Server 2008" néven sorolja fel.

Annak megkereséséhez, hogy mely felhőszolgáltatások melyik operációsrendszert futtatják, futtathatja a következő parancsfájlt az Azure PowerShellben, de először be kell állítania az [Azure PowerShellt.](/powershell/azureps-cmdlets-docs) A parancsfájllal kapcsolatos további információkért lásd: [Azure Guest OS Family 1 End of Life: June 2014](https://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx).

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

A felhőszolgáltatások hatással lesz az OS Family 1 nyugdíjba vonulás, ha az osFamily oszlop a parancsfájl kimenetüres, vagy tartalmaz egy "1".

## <a name="recommendations-if-you-are-affected"></a>Ajánlások, ha az érintett
Javasoljuk, hogy migrálja át a Felhőszolgáltatás-szerepköröket a támogatott vendég operációsrendszer-családok egyikére:

**Vendég operációsrendszer-család 4.x** – Windows Server 2012 R2 *(ajánlott)*

1. Győződjön meg arról, hogy az alkalmazás sdk 2.1-es vagy újabb sdk-t használ a .
2. Állítsa az osFamily attribútumot "4" értékre a ServiceConfiguration.cscfg fájlban, és telepítse újra a felhőszolgáltatást.

**Vendég operációsrendszer-család 3.x** – Windows Server 2012

1. Győződjön meg arról, hogy az alkalmazás sdk 1.8-as vagy újabb sdk-t használ a .
2. Állítsa az osFamily attribútumot "3" értékre a ServiceConfiguration.cscfg fájlban, és telepítse újra a felhőszolgáltatást.

**Vendég operációsrendszer-család 2.x** – Windows Server 2008 R2

1. Győződjön meg arról, hogy az alkalmazás sdk 1.3-as és újabb sdk-t használ a .
2. Állítsa az osFamily attribútumot "2" értékre a ServiceConfiguration.cscfg fájlban, és telepítse újra a felhőszolgáltatást.

## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>2014. november 3-án véget ért a vendég operációsrendszer-család kiterjesztett támogatása
A vendég operációsrendszer-család 1 felhőszolgáltatásai már nem támogatottak. A szolgáltatás kimaradásának elkerülése érdekében a lehető leghamarabb migráljon az 1-es családból.  

## <a name="next-steps"></a>További lépések
Tekintse át a vendég operációs rendszer legújabb [kiadásait.](cloud-services-guestos-update-matrix.md)
