---
title: Csatlakozás az Azure Data Lake Storage Gen1 szolgáltatáshoz a VNET-ktől | Microsoft dokumentumok
description: Csatlakozás az Azure Data Lake Storage Gen1 szolgáltatáshoz az Azure VNET-ktől
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878868"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Az Azure Data Lake Storage Gen1 elérése virtuális gépekből egy Azure-beli virtuális hálózaton belül
Az Azure Data Lake Storage Gen1 egy Nyilvános internetes IP-címeken futó PaaS-szolgáltatás. Minden olyan kiszolgáló, amely képes csatlakozni a nyilvános internethez, általában az Azure Data Lake Storage Gen1 végpontokhoz is csatlakozhat. Alapértelmezés szerint az Azure VNET-kben lévő összes virtuális gép hozzáférhet az internethez, és így hozzáférhet az Azure Data Lake Storage Gen1 szolgáltatáshoz. A virtuális hálózatban lévő virtuális gépek azonban konfigurálhatók úgy, hogy ne férhessenek hozzá az internethez. Az ilyen virtuális gépek, az Azure Data Lake Storage Gen1-hez való hozzáférés is korlátozott. A virtuális gépek nyilvános internet-hozzáférésének letiltása az Azure VNET-kben az alábbi módszerek bármelyikével végezhető el:

* Hálózati biztonsági csoportok (NSG) konfigurálásával
* Felhasználó által definiált útvonalak (UDR) konfigurálásával
* Útvonalak cseréjével a BGP-n (iparági szabványnak megfelelő dinamikus útválasztási protokoll) keresztül, ha ExpressRoute-ot használ, amely blokkolja az internet-hozzáférést

Ebben a cikkben megtudhatja, hogyan engedélyezheti a hozzáférést az Azure Data Lake Storage Gen1 azure-beli virtuális gépekről, amelyek csak az erőforrások elérésére a korábban felsorolt három módszer egyikével érhetők el.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Az Azure Data Lake Storage Gen1 korlátozott kapcsolattal rendelkező virtuális gépekről való kapcsolat engedélyezése
Az Azure Data Lake Storage Gen1 ilyen virtuális gépekről való eléréséhez konfigurálnia kell őket, hogy hozzáférjenek az IP-címhez azon régióban, ahol az Azure Data Lake Storage Gen1 fiók elérhető. A Data Lake Storage Gen1 fiókrégiók IP-címeit a fiókok DNS-nevének ( feloldásával)`<account>.azuredatalakestore.net`azonosíthatja. A fiókok DNS-nevének feloldásához olyan eszközöket használhat, mint az **nslookup**. Nyisson meg egy parancssort a számítógépen, és futtassa a következő parancsot:

    nslookup mydatastore.azuredatalakestore.net

A kimenet a következőhöz hasonlít. A Address tulajdonsággal **szembeni** érték a Data Lake Storage Gen1 fiókhoz társított IP-cím.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Az NSG használatával korlátozott virtuális gépek ről származó kapcsolat engedélyezése
Ha egy NSG-szabály az internet-hozzáférés letiltására szolgál, akkor létrehozhat egy másik NSG-t, amely lehetővé teszi a Data Lake Storage Gen1 IP-cím elérését. Az NSG-szabályokról a Hálózati biztonsági csoportok – áttekintés című témakörben olvashat [bővebben.](../virtual-network/security-overview.md) Az NSG-k létrehozásáról a [Hálózati biztonsági csoport létrehozása](../virtual-network/tutorial-filter-network-traffic.md)című témakörben talál útmutatást.

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Internetkapcsolat engedélyezése az UDR vagy ExpressRoute használatával korlátozott virtuális gépekről
Ha az útvonalakat, akár az UDRs-t, akár a BGP-cserélt útvonalakat használják az internet-hozzáférés blokkolására, egy speciális útvonalat kell konfigurálni, hogy az ilyen alhálózatokban lévő virtuális gépek hozzáférhessenek a Data Lake Storage Gen1 végpontokhoz. További információt a [Felhasználó által definiált útvonalak – áttekintés című témakörben talál.](../virtual-network/virtual-networks-udr-overview.md) Az UDRs létrehozásáról az [UdRs létrehozása az erőforrás-kezelőben](../virtual-network/tutorial-create-route-table-powershell.md)című témakörben talál.

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Az ExpressRoute használatával korlátozott virtuális gépek ről származó kapcsolat engedélyezése
ExpressRoute-kapcsolat konfigurálása esetén a helyszíni kiszolgálók nyilvános társviszony-létesítésen keresztül férhetnek hozzá a Data Lake Storage Gen1 szolgáltatáshoz. Az ExpressRoute nyilvános társviszony-létesítéshez való konfigurálásával kapcsolatos további részletek az [ExpressRoute GYIK-ben](../expressroute/expressroute-faqs.md)érhetők el.

## <a name="see-also"></a>Lásd még
* [Az Azure Data Lake storage gen1 áttekintése](data-lake-store-overview.md)
* [Az Azure Data Lake Storage Gen1-ben tárolt adatok védelme](data-lake-store-security-overview.md)

