---
title: "A Microsoft Azure verem szoftverfejlesztői készlet architektúra |} Microsoft Docs"
description: "A Microsoft Azure verem szoftverfejlesztői készlet architektúra megtekintése."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: a7e61ea4-be2f-4e55-9beb-7a079f348e05
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b754ff5b5a82ac284eb59ff9b9d30a581f3d3af5
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>A Microsoft Azure verem szoftverfejlesztői készlet architektúrája

*A következőkre vonatkozik: Azure szoftverfejlesztői készletet*

Az Azure verem szoftverfejlesztői készlet Azure-verem egycsomópontos központi telepítését. Az összetevők, egy önálló gazdagépen futó virtuális gépek vannak telepítve. 

## <a name="logical-architecture-diagram"></a>Logikai architektúra diagramja
A következő ábra szemlélteti a Azure verem csomagot és annak összetevői logikai architektúrájának.

![](media/azure-stack-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Virtuálisgép-szerepkörök
Az Azure-verem szoftverfejlesztői készlet a következő virtuális gépek használata a gazdagép szolgáltatásokat nyújtja:

| Name (Név) | Leírás |
| ----- | ----- |
| **AzS-ACS01** | Az Azure tárolási szolgáltatások a verem.|
| **AzS-ADFS01** | Az Active Directory összevonási szolgáltatások (AD FS).  |
| **AzS-BGPNAT01** | Útválasztó él és NAT- és VPN-képességeket biztosít Azure verem számára. |
| **AzS-CA01** | A tanúsítványszolgáltatás hatóság Azure verem szerepkör-szolgáltatásai.|
| **AzS-DC01** | A Microsoft Azure verem Active Directory, a DNS és DHCP szolgáltatások.|
| **AzS-ERCS01** | Vészhelyzeti helyreállítási konzol virtuális gép. |
| **AzS-GWY01** | A peremhálózati átjáró szolgáltatások, például a VPN-webhelyek kapcsolatok bérlői hálózatok számára.|
| **AzS-NC01** | Hálózati vezérlő, amely Azure verem hálózati szolgáltatások kezeli.  |
| **AzS-SLB01** | Terheléselosztás multiplexer szolgáltatások, Azure-készletben a bérlők és a verem Azure infrastruktúra-szolgáltatásokat.  |
| **AzS-SQL01** | Belső adatok tárolásához Azure verem infrastruktúra-szerepkörök.  |
| **AzS-WAS01** | Verem Azure felügyeleti portál és az Azure Resource Manager-szolgáltatások.|
| **AzS-WASP01**| Verem felhasználó (bérlő) az Azure portál és az Azure Resource Manager-szolgáltatások.|
| **AzS-XRP01** | A Microsoft Azure-vermet, beleértve a számítási, hálózati és Tárolóerőforrás-szolgáltatók infrastruktúra felügyeletvezérlő.|


## <a name="next-steps"></a>További lépések
[Az Azure verem telepítése](azure-stack-deploy.md)

[Próbálja első forgatókönyvek](azure-stack-first-scenarios.md)

