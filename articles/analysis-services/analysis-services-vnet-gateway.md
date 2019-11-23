---
title: A VNet-adatforrások Azure Analysis Services konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Azure Analysis Services kiszolgálót az Azure Virtual Network (VNet) adatforrásaihoz tartozó átjáró használatára.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 384f4c2ee3021678c530aa5c98eaf4dbfd661e95
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572265"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Átjáró használata adatforrásokhoz Azure-Virtual Network (VNet)

Ez a cikk a Azure Analysis Services **AlwaysUseGateway** Server-tulajdonságot ismerteti, ha az adatforrások [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)vannak használatban.

## <a name="server-access-to-vnet-data-sources"></a>Kiszolgáló-hozzáférés VNet-adatforrásokhoz

Ha az adatforrások egy VNet keresztül érhetők el, a Azure Analysis Services-kiszolgálónak csatlakoznia kell ezekhez az adatforrásokhoz, mintha a helyszínen, a saját környezetében legyenek. A **AlwaysUseGateway** -kiszolgáló tulajdonság konfigurálásával megadhatja, hogy a kiszolgáló az összes adatforrást egy helyszíni [átjárón](analysis-services-gateway.md)keresztül férhessen hozzá. 

Azure SQL Database felügyelt példány adatforrásai saját IP-címmel futnak az Azure VNet belül. Ha a nyilvános végpont engedélyezve van a példányon, nincs szükség átjáróra. Ha a nyilvános végpont nincs engedélyezve, egy helyszíni adatátjáróra van szükség, és a AlwaysUseGateway tulajdonságot igaz értékre kell állítani.

> [!NOTE]
> Ez a tulajdonság csak akkor érvényes, ha egy helyszíni [adatátjáró](analysis-services-gateway.md) van telepítve és konfigurálva. Az átjáró lehet a VNet.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway tulajdonság konfigurálása

1. A SSMS > Server > **tulajdonságok** > **általános**területen válassza a **speciális (mind) tulajdonságok megjelenítése**lehetőséget.
2. A **ASPaaS\AlwaysUseGateway**válassza az **igaz**lehetőséget.

    ![Mindig az átjáró tulajdonság használata](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Lásd még
[Csatlakozás helyszíni adatforrásokhoz](analysis-services-gateway.md)   
Helyszíni [adatátjáró telepítése és konfigurálása](analysis-services-gateway-install.md)   
[Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

