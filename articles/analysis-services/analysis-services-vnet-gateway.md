---
title: Az Azure Analysis Services konfigurálása virtuális hálózati adatforrásokhoz | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja az Azure Analysis Services-kiszolgálót az Azure Virtuális hálózaton (VNet) lévő adatforrások átjárójának használatára.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572265"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Átjáró használata adatforrásokhoz egy Azure virtuális hálózaton (VNet)

Ez a cikk az Azure Analysis Services **AlwaysUseGateway** kiszolgálói tulajdonságát ismerteti, amelyet akkor használhat, ha az adatforrások [egy Azure virtuális hálózaton (VNet)](../virtual-network/virtual-networks-overview.md)találhatók.

## <a name="server-access-to-vnet-data-sources"></a>Kiszolgálói hozzáférés a virtuális hálózat adatforrásaihoz

Ha az adatforrások virtuális hálózaton keresztül érhetők el, az Azure Analysis Services-kiszolgálónak úgy kell csatlakoznia ezekhez az adatforrásokhoz, mintha a saját környezetében lennének a helyszínen. Az **AlwaysUseGateway** kiszolgáló tulajdonság konfigurálásával megadhatja, hogy a kiszolgáló a helyszíni átjárón keresztül az összes adatforrást [elérhesse.](analysis-services-gateway.md) 

Az Azure SQL Database felügyelt példányadatforrásai az Azure Virtuális hálózaton belül privát IP-címmel futnak. Ha a nyilvános végpont engedélyezve van a példányon, az átjáró nem szükséges. Ha a nyilvános végpont nincs engedélyezve, szükség van egy helyszíni adatátjáróra, és az AlwaysUseGateway tulajdonságot true értékre kell állítani.

> [!NOTE]
> Ez a tulajdonság csak akkor érvényes, ha egy [helyszíni adatátjáró](analysis-services-gateway.md) telepítve van és konfigurálva van. Az átjáró lehet a virtuális hálózaton.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway tulajdonság konfigurálása

1. Az SSMS > a kiszolgáló > **a Tulajdonságok** > **általános**beállításában válassza a **Speciális (összes) tulajdonságok megjelenítése lehetőséget.**
2. Az **ASPaaS\AlwaysUseGateway**mappában válassza a **true**lehetőséget.

    ![Mindig használja az átjáró tulajdonságot](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Lásd még
[Csatlakozás helyszíni adatforrásokhoz](analysis-services-gateway.md)   
[Helyszíni adatátjáró telepítése és konfigurálása](analysis-services-gateway-install.md)   
[Azure virtuális hálózat (VNET)](../virtual-network/virtual-networks-overview.md)   

