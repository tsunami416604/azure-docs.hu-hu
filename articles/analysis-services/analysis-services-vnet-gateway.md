---
title: A helyszíni adatátjáró használata az Azure Virtual Network adatforrásokat |} A Microsoft Docs
description: Ismerje meg, hogy egy kiszolgáló egy átjáró használata az adatforrásokhoz a virtuális hálózat konfigurálása.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 62f6413854ecdfc257655fd864167d8e24d35e06
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427310"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Átjáró használata az adatforrásokhoz egy Azure virtuális hálózaton (VNet)

Ez a cikk ismerteti a **AlwaysUseGateway** használja, ha az adatforrások azok a kiszolgáló tulajdonsága egy [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>-Kiszolgáló hozzáférését a VNet-adatforrások

Az adatforrások egy virtuális hálózaton keresztül érhetők el, ha az Azure Analysis Services-kiszolgáló kapcsolódni kell ezeket az adatforrásokat, mintha azok a helyszínen, a saját környezetében. Konfigurálhatja a **AlwaysUseGateway** kiszolgáló tulajdonságot adja meg a kiszolgáló összes adatforrás adatigénylésekre keresztül egy [helyszíni átjáró](analysis-services-gateway.md). 

> [!NOTE]
> Ez a tulajdonság nem érvényes csak akkor, ha egy [a helyszíni adatátjáró](analysis-services-gateway.md) telepítve és konfigurálva van. Az átjáró lehet a virtuális hálózaton.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway tulajdonság beállítása

1. Az ssms-ben > kiszolgáló > **tulajdonságok** > **általános**válassza **megjelenítése (összes) speciális tulajdonságai**.
2. Az a **ASPaaS\AlwaysUseGateway**válassza **igaz**.

    ![Mindig használja az átjáró tulajdonság](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Lásd még
[Csatlakozás helyszíni adatforrásokhoz](analysis-services-gateway.md)   
[Telepítse és konfigurálja a helyszíni adatátjáró](analysis-services-gateway-install.md)   
[Az Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

