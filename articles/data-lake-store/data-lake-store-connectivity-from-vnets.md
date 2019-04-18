---
title: Csatlakozás a virtuális hálózatok az Azure Data Lake Storage Gen1 |} A Microsoft Docs
description: Az Azure Data Lake Storage Gen1 csatlakozhat az Azure virtuális hálózatok
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878060"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Hozzáférés az Azure Data Lake Storage Gen1 egy Azure virtuális hálózaton belüli virtuális gépek
Az Azure Data Lake Storage Gen1 egy PaaS szolgáltatás, amely a nyilvános internetes IP-címeket. Bármely kiszolgálóra, amely képes kapcsolódni a nyilvános interneten általában csatlakozhat az Azure Data Lake Storage Gen1 végpontokat is. Alapértelmezés szerint minden virtuális gépre, amelyek az Azure Vneteken hozzáférnek az internethez, és így hozzáférhet az Azure Data Lake Storage Gen1. Azonban fontos konfigurálhatja a virtuális gépek Internet-hozzáféréssel rendelkezik a virtuális hálózaton. Az ilyen virtuális gépek az Azure Data Lake Storage Gen1 való hozzáférés korlátozva is. Nyilvános Internet-hozzáférés letiltása a virtuális gépek az Azure Vneteken teheti meg a következő módszerek bármelyikével:

* Konfigurálja a hálózati biztonsági csoportok (NSG)
* Konfigurálja a felhasználó által megadott útvonalak (UDR)
* Útvonalak (industry standard dinamikus útválasztási protokoll) BGP-n keresztül kicserélésével ExpressRoute használata esetén, amelyek blokkolják az Internet-hozzáférés

Ebben a cikkben megtudhatja, az Azure Data Lake Storage Gen1 való hozzáférés engedélyezése az Azure virtuális gépekről, amelyek hozzáférését az erőforrásokhoz a korábban felsorolt három módszer valamelyikével korlátozott.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Az Azure Data Lake Storage Gen1 engedélyezése kapcsolat korlátozott kapcsolattal rendelkező virtuális gépekről
Az ilyen virtuális gépek az Azure Data Lake Storage Gen1 eléréséhez konfigurálnia kell őket az IP-cím a régió, ahol érhető el az Azure Data Lake Storage Gen1 fiók eléréséhez. Az IP-címek a Data Lake Storage Gen1 fiók régió alapján azonosíthatja a DNS-nevek a fiókok feloldását (`<account>.azuredatalakestore.net`). A fiókok a DNS-nevek feloldására, eszközöket használhat például **nslookup**. Nyisson meg egy parancssort a számítógépen, és futtassa a következő parancsot:

    nslookup mydatastore.azuredatalakestore.net

A kimenet az alábbihoz hasonló. Az érték elleni **cím** tulajdonság pedig a Data Lake Storage Gen1 fiókjához társított IP-címe.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Korlátozza az NSG-t használó virtuális gépek kapcsolat engedélyezése
Ha Internet-hozzáférés letiltása egy NSG-szabályt használják, majd hozhat létre egy másik NSG-t, amely lehetővé teszi a hozzáférést a Data Lake Storage Gen1 IP-címre. Az NSG-szabályok kapcsolatos további információkért lásd: [hálózati biztonsági csoportok áttekintése](../virtual-network/security-overview.md). NSG-k létrehozásával kapcsolatos útmutatásért lásd: [egy hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Virtuális gépek, az URD vagy az ExpressRoute használatával korlátozható kapcsolat engedélyezése
Amikor útvonalakat, vagy az udr-EK, vagy az útvonalakat a BGP-kicserélt használ az interneten való hozzáférés letiltását, egy speciális útvonalat kell konfigurálni, hogy az ilyen alhálózatokban található virtuális gépek elérhetik a Data Lake Storage Gen1 végpontjait. További információkért lásd: [felhasználó által megadott útvonalak áttekintését](../virtual-network/virtual-networks-udr-overview.md). Az udr-EK létrehozásával kapcsolatos útmutatóért lásd: [Udr létrehozása a Resource Managerben](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Virtuális gépek az ExpressRoute használatával korlátozható kapcsolat engedélyezése
Ha az ExpressRoute-kapcsolatcsoport van konfigurálva, a helyszíni kiszolgálók elérhető Data Lake Storage Gen1 nyilvános társviszony-létesítésen keresztül. További részleteket a nyilvános társviszony-létesítés mindig elérhető legyen az ExpressRoute konfigurálása [ExpressRoute – gyakori kérdések](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake Storage Gen1 áttekintése](data-lake-store-overview.md)
* [Az Azure Data Lake Storage Gen1 tárolt adatok védelme](data-lake-store-security-overview.md)

