---
title: Az Azure Stack Development Kit architektúrája |} A Microsoft Docs
description: Az Azure Stack Development Kit (ASDK) architektúrát ismerteti.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 2cee4d75a111f56012782228d8070bb728720aca
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238338"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>A Microsoft Azure Stack Development Kit architektúrája
Az Azure Stack Development Kit (ASDK) egy egy csomópontos üzembe helyezhető Azure Stacket. Minden az összetevő telepítve van az önálló gazdagépen futó virtuális gépek. 

## <a name="logical-architecture-diagram"></a>Logikai architektúra ábrája
A következő ábra szemlélteti a ASDK és összetevői a logikai architektúrát.

![ASDK architektúra](media/asdk-architecture/image1.png)

## <a name="virtual-machine-roles"></a>Virtuálisgép-szerepkörök
A ASDK használatával a következő virtuális gépek a development kit gazdagépen futó szolgáltatásokat nyújtja:

| Name (Név) | Leírás |
| ----- | ----- |
| **AzS-ACS01** | Az Azure Stack-tárolási szolgáltatások.|
| **AzS-ADFS01** | Active Directory összevonási szolgáltatások (ADFS).  |
| **AzS-BGPNAT01** | Útválasztó él, és a NAT- és VPN-képességeket biztosít az Azure Stackhez. |
| **AzS-CA01** | A tanúsítványszolgáltatás a szerepkör-szolgáltatások az Azure Stack szolgáltató szolgáltatásaival.|
| **AzS-DC01** | A Microsoft Azure Stackhez az Active Directory, DNS és DHCP szolgáltatások.|
| **AzS-ERCS01** | Vészhelyzeti helyreállítási konzol virtuális Gépet. |
| **AzS-GWY01** | Edge-átjáró szolgáltatásokon, például a VPN-helyek közötti kapcsolatok bérlői hálózatok számára.|
| **AzS-NC01** | Hálózati vezérlő, amely kezeli az Azure Stack hálózati szolgáltatások.  |
| **AzS-SLB01** | A terheléselosztás multiplexer szolgáltatások az Azure Stackben egyaránt bérlők és az Azure Stack-infrastruktúra-szolgáltatások.  |
| **AzS-SQL01** | Belső adatok tárolása az Azure Stack-infrastruktúra-szerepkörök.  |
| **AzS-WAS01** | Az Azure Stack felügyeleti portálon, és Azure Resource Manager-szolgáltatásokat.|
| **AzS-WASP01**| Az Azure Stack (bérlő) felhasználói portál és Azure Resource Manager-szolgáltatások.|
| **AzS-XRP01** | A Microsoft Azure Stack, beleértve a számítási, hálózati és tárolási erőforrás-szolgáltató infrastruktúra felügyeletvezérlő.|


## <a name="next-steps"></a>További lépések
[Alapszintű ASDK adminisztrációs feladatok megismerése](asdk-admin-basics.md)
