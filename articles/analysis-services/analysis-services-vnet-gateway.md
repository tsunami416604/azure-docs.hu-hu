---
title: A helyszíni adatok átjáró használata a Azure Virtual Network adatforrások |} Microsoft Docs
description: 'Útmutató: az átjáró használatára az adatforrások VNet-kiszolgáló konfigurálása.'
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7cb1fb7bcd3a50532d5ff994afac56e226fb2018
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596906"
---
# <a name="use-gateway-for-data-sources-on-an-azure-virtual-network-vnet"></a>Az adatforrások egy Azure virtuális hálózatot (VNet) átjáró használata

Ez a cikk ismerteti a **AlwaysUseGateway** használ, amikor az adatforrások azok a kiszolgáló következő tulajdonsága egy [Azure Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md).

## <a name="server-access-to-vnet-data-sources"></a>Kiszolgáló elérése VNet adatforrásokhoz

Az adatforrások egy virtuális hálózaton keresztül érhetők el, ha az Azure Analysis Services-kiszolgálóhoz kell kapcsolódnia az adatforrások, mintha a helyszínen, a saját környezetében. Konfigurálhatja a **AlwaysUseGateway** server tulajdonsággal keresztül az összes adatforrás adatok eléréséhez a kiszolgáló egy [helyszíni átjáró](analysis-services-gateway.md). 

> [!NOTE]
> Ez a tulajdonság hatékony csak akkor, ha egy [helyszíni adatátjáró](analysis-services-gateway.md) telepítve és konfigurálva van. Az átjáró lehet a virtuális hálózaton.

## <a name="configure-alwaysusegateway-property"></a>AlwaysUseGateway tulajdonság beállítása

1. Az SSMS > server > **tulajdonságok** > **általános**, jelölje be **megjelenítése (All) speciális tulajdonságok**.
2. Az a **ASPaaS\AlwaysUseGateway**, jelölje be **igaz**.

    ![Mindig használjon az átjáró tulajdonság](media/analysis-services-vnet-gateway/aas-ssms-always-property.png)


## <a name="see-also"></a>Lásd még
[A helyszíni adatforrások csatlakoztatása](analysis-services-gateway.md)   
[Telepítse és konfigurálja a helyszíni adatátjáró](analysis-services-gateway-install.md)   
[Az Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md)   

