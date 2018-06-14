---
title: Az Azure verem Development Kit architektúrája |} Microsoft Docs
description: Az Azure verem Development Kit (ASDK) architektúráját ismerteti.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 68da3ac0eb135f5956dfea76e186d9c57beea79c
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
ms.locfileid: "29975866"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>A Microsoft Azure verem szoftverfejlesztői készlet architektúrája
Az Azure verem Development Kit (ASDK) az Azure verem egycsomópontos központi telepítését. Az összetevők, egy önálló gazdagépen futó virtuális gépek vannak telepítve. 

## <a name="logical-architecture-diagram"></a>Logikai architektúra diagramja
A következő ábra szemlélteti a ASDK és az összetevőinek logikai architektúrájának.

![ASDK architektúra](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Virtuálisgép-szerepkörök
A ASDK használatával a következő virtuális gépeket az development kit állomáson futó szolgáltatásokat nyújtja:

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
[Alapszintű ASDK felügyeleti feladatokkal kapcsolatos tudnivalók](asdk-admin-basics.md)
