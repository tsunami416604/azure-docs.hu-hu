---
title: "Csatlakozás az Azure Data Lake Store a Vnetek |} Microsoft Docs"
description: "Csatlakozás az Azure Data Lake Store az Azure Vnetekhez"
services: data-lake-store,data-catalog
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 029939eabc7283d7591576fa1b14731a9e626a88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Az Azure Data Lake Store egy Azure virtuális hálózaton belüli virtuális gépek
Azure Data Lake Store PaaS szolgáltatás, amely a nyilvános Internet IP-címek. Bármely kiszolgáló csatlakozni tud-e a nyilvános internethez általában csatlakozhat, valamint az Azure Data Lake Store-végpontok. Alapértelmezés szerint az Azure Vnetekhez lévő összes virtuális gép hozzáférhet az interneten, és ezért férhetnek hozzá az Azure Data Lake Store. Azonban úgy is egy VNET és nem rendelkezik Internet-hozzáférés a virtuális gépek konfigurálása. Ilyen virtuális gépek elérése az Azure Data Lake Store történik is. Nyilvános Internet-hozzáférés letiltása a virtuális gépek az Azure Vnetekhez végezhető a következő módon használja.

* Úgy konfigurálja a hálózati biztonsági csoportok (NSG)
* Úgy konfigurálja a felhasználó definiált útvonalakat (UDR)
* Útvonalak BGP (industry standard dinamikus útválasztási protokoll) útján kicserélésével ExpressRoute használatakor, amelyek blokkolják az Internet-hozzáférés

Ebből a cikkből megismerheti, hogyan hozzáférés engedélyezése az Azure Data Lake Store az Azure virtuális gépek, amelyek hozzáférését az erőforrásokhoz a fenti három módszer egyikével korlátozott lesz.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Azure Data Lake Store engedélyezése kapcsolat korlátozott kapcsolattal rendelkező virtuális gépek
Azure Data Lake Store ilyen virtuális gépek eléréséhez konfigurálnia kell azokat az IP-cím, amennyiben rendelkezésre áll-e az Azure Data Lake Store-fiók eléréséhez. Az IP-címek a Data Lake Store-fiókok alapján azonosíthatja a fiókok a DNS-nevek feloldása (`<account>.azuredatalakestore.net`). Ehhez használhatja például a **nslookup**. Nyisson meg egy parancssort a számítógépen, és futtassa a következő parancsot.

    nslookup mydatastore.azuredatalakestore.net

A kimenet az alábbihoz hasonló. Az érték alapján **cím** tulajdonság a Data Lake Store-fiókjához társított IP-címét.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Virtuális gépek NSG szolgáltatással kapcsolat engedélyezése
Használatakor egy NSG-szabály Internet-hozzáférés letiltása, majd létrehozhat egy másik NSG-t, amely lehetővé teszi a hozzáférést a Data Lake Store IP-cím. További információt az NSG-szabályok [Mi az a hálózati biztonsági csoport?](../virtual-network/virtual-networks-nsg.md). További információ az NSG-k létrehozásához [kezelése az Azure portál használatával NSG-k](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Virtuális gépek szolgáltatással UDR vagy ExpressRoute kapcsolat engedélyezése
Útvonalak udr-EK vagy a BGP-kicserélt útvonalakat, Internet-hozzáférés letiltása használata esetén egy különös útvonalat kell konfigurálni, hogy az ilyen alhálózatban lévő virtuális gépek hozzáférhessenek a Data Lake Store-végpontok. További információkért lásd: [Mik azok a felhasználó által megadott útvonalak?](../virtual-network/virtual-networks-udr-overview.md). Udr-EK létrehozásával kapcsolatos utasításokért lásd: [létrehozása udr a Resource Manager-EK](../virtual-network/virtual-network-create-udr-arm-ps.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Virtuális gépek szolgáltatással ExpressRoute kapcsolat engedélyezése
Ha ExpressRoute-kapcsolatcsoportot van konfigurálva, a helyszíni kiszolgálók elérhető Data Lake Store nyilvános társviszony keresztül. További részleteket a nyilvános társviszony található ExpressRoute konfigurálása [ExpressRoute – gyakori kérdések](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Lásd még:
* [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
* [Azure Data Lake Store-ban tárolt adatok védelme](data-lake-store-security-overview.md)

