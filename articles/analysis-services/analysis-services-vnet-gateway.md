---
title: A helyszíni adatátjáró használata az Azure Virtual Network adatforrásokat |} A Microsoft Docs
description: Ismerje meg, hogy egy kiszolgáló egy átjáró használata az adatforrásokhoz a virtuális hálózat konfigurálása.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f1cf447f27509bc62cc71a9c64237ff7dc3d7b30
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446084"
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

