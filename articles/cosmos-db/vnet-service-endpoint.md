---
title: Biztonságos hozzáférés az Azure Cosmos DB-fiókokhoz a virtuális hálózati szolgáltatás végpontjának használatával
description: Ez a dokumentum ismerteti a virtuális hálózati és alhálózati hozzáférés-vezérlés egy Azure Cosmos-fiók.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: af1032de9aabac45ad7a86cfe1f36ed2c04c0f71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444619"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Az Azure Cosmos DB elérése virtuális hálózatról (VNet)

Konfigurálhatja az Azure Cosmos-fiókot, hogy csak a virtuális hálózat (VNet) adott alhálózatáról engedélyezze a hozzáférést. Azáltal, hogy lehetővé teszi [a szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) számára az Azure Cosmos DB elérését egy virtuális hálózaton belüli alhálózaton, az adott alhálózatból érkező forgalmat az azure Cosmos DB-nek küldi el az alhálózat és a virtuális hálózat identitásával. Miután az Azure Cosmos DB szolgáltatás végpontja engedélyezve van, korlátozhatja az alhálózathoz való hozzáférést az Azure Cosmos-fiókhoz való hozzáadásával.

Alapértelmezés szerint egy Azure Cosmos-fiók bármely forrásból elérhető, ha a kérelemhez érvényes engedélyezési jogkivonat tartozik. Ha egy vagy több alhálózatot ad hozzá a virtuális hálózatokon belül, csak az ezekből az alhálózatokból származó kérelmek kapnak érvényes választ. Bármely más forrásból származó kérések 403-as (Tiltott) választ kapnak. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Íme néhány gyakori kérdés a virtuális hálózatokból való hozzáférés konfigurálásával kapcsolatban:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Megadhatom a virtuális hálózati szolgáltatás végpontját és az IP-hozzáférés-vezérlési szabályzatot egy Azure Cosmos-fiókon? 

Engedélyezheti a virtuális hálózati szolgáltatás végpontját és az IP-hozzáférés-vezérlési szabályzatot (más néven tűzfalat) az Azure Cosmos-fiókban. Ez a két funkció kiegészíti egymást, és együttesen biztosítja az Azure Cosmos-fiók elkülönítését és biztonságát. Az IP-tűzfal használata biztosítja, hogy a statikus IP-k hozzáférhessenek a fiókjához. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hogyan korlátozhatom a virtuális hálózaton belüli alhálózathoz való hozzáférést? 

Két lépés szükséges az Azure Cosmos-fiókhoz való hozzáférés korlátozásához egy alhálózatból. Először engedélyezze az alhálózatból származó forgalmat az alhálózat és a virtuális hálózati identitás azure Cosmos DB-re történő átviteléhez. Ez úgy történik, hogy engedélyezi az Azure Cosmos DB szolgáltatásvégpontját az alhálózaton. Következő egy szabály hozzáadása az Azure Cosmos-fiókban, amely ezt az alhálózatot adja meg forrásként, amelyből a fiók elérhető.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>A virtuális hálózati ACL-k és az IP-tűzfal elutasítják a kérelmeket vagy a kapcsolatokat? 

Ip-tűzfal vagy virtuális hálózati hozzáférési szabályok hozzáadásakor csak az engedélyezett forrásokból érkező kérelmek kapnak érvényes válaszokat. Más kéréseket elutasítunk a 403 (Tiltott). Fontos megkülönböztetni az Azure Cosmos-fiók tűzfalát a kapcsolatszintű tűzfaltól. A forrás továbbra is csatlakozhat a szolgáltatáshoz, és maguk a kapcsolatok nem utasíthatók el.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>A kéréseim letiltásba kezdtek, amikor engedélyeztem a szolgáltatásvégpontot az Azure Cosmos DB-nek az alhálózaton. Mi történt?

Ha az Azure Cosmos DB szolgáltatásvégpontja engedélyezve van egy alhálózaton, a fiók eléréséhez vezető forgalom forrása nyilvános IP-címről virtuális hálózatra és alhálózatra vált. Ha az Azure Cosmos-fiók csak IP-alapú tűzfallal rendelkezik, a szolgáltatásáltal engedélyezett alhálózatból érkező forgalom már nem felel meg az IP tűzfalszabályainak, ezért elutasításra kerül. Az IP-alapú tűzfalról a virtuális hálózatalapú hozzáférés-vezérlésre való zökkenőmentes áttelepítés lépéseit.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>További RBAC-engedélyek szükségesek az Azure Cosmos-fiókok vNET-szolgáltatás végpontokkal?

Miután hozzáadja a virtuális hálózat szolgáltatás végpontjait egy Azure Cosmos-fiókhoz, a `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` fiókbeállítások módosításához hozzáférésre van szüksége az Azure Cosmos-fiókban konfigurált összes VNET művelethez. Erre az engedélyre azért van szükség, mert az engedélyezési folyamat ellenőrzi az erőforrásokhoz (például az adatbázishoz és a virtuális hálózati erőforrásokhoz) való hozzáférést, mielőtt bármilyen tulajdonságot kiértékelne.
 
Az engedélyezés érvényesíti a virtuális hálózati erőforrás-művelet engedélyét, még akkor is, ha a felhasználó nem adja meg a VNET ACL-eket az Azure CLI használatával. Jelenleg az Azure Cosmos-fiók vezérlősíkja támogatja az Azure Cosmos-fiók teljes állapotának beállítását. A vezérlősík hívásainak egyik paramétere a `virtualNetworkRules`. Ha ez a paraméter nincs megadva, az Azure CLI `virtualNetworkRules` lekéréses adatbázis-hívást kezdeményez a lekéréshez, és ezt az értéket használja a frissítési hívásban.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>A társviszony-létesített virtuális hálózatok is hozzáférhetnek az Azure Cosmos-fiókhoz? 
Csak a virtuális hálózat és az Azure Cosmos-fiókhoz hozzáadott alhálózatai rendelkeznek hozzáféréssel. A társviszony-létesített virtuális hálózatok nem férhetnek hozzá a fiókhoz, amíg a társviszonyba tartozó virtuális hálózatokalhálózatai hozzá nem kerülnek a fiókhoz.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Mi az egyetlen Cosmos-fiók eléréséhez engedélyezett alhálózatok maximális száma? 
Jelenleg legbőlegelhet egy Azure Cosmos-fiókhoz engedélyezett legbőlegelhető 64 alhálózatot.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Engedélyezhetem a hozzáférést a VPN-ről és az expressz útvonalról? 
Az Azure Cosmos-fiók elérése expressz útvonalon a helyszínen, engedélyeznie kell a Microsoft társviszony-létesítés. Miután ip-tűzfalat vagy virtuális hálózati hozzáférési szabályokat tett, hozzáadhatja a Microsoft társviszony-létesítéséhez használt nyilvános IP-címeket az Azure Cosmos-fiók IP-tűzfalán, hogy a helyszíni szolgáltatások hozzáférjenek az Azure Cosmos-fiókhoz. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Frissítenem kell a hálózati biztonsági csoportok (NSG) szabályait? 
Az NSG-szabályok a virtuális hálózattal rendelkező alhálózathoz való csatlakozás korlátozására szolgálnak. Amikor hozzáadja az Azure Cosmos DB szolgáltatásvégpontját az alhálózathoz, nincs szükség kimenő kapcsolat megnyitására az NSG-ben az Azure Cosmos-fiókhoz. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Szolgáltatás-végpontok érhetők el az összes virtuális hálózathoz?
Nem, csak az Azure Resource Manager virtuális hálózatok rendelkezhetnek szolgáltatásvégpont engedélyezve. A klasszikus virtuális hálózatok nem támogatják a szolgáltatás végpontjait.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>"A nyilvános Azure-adatközpontokon belüli kapcsolatok fogadása" funkció, ha az Azure Cosmos DB szolgáltatásvégpont-hozzáférés engedélyezve van?  
Erre csak akkor van szükség, ha azt szeretné, hogy az Azure Cosmos DB-fiókját más Azure-szolgáltatások, például az Azure Data-gyár, az Azure Cognitive Search vagy bármely olyan szolgáltatás is elérhetőlegyen, amely adott Azure-régióban van telepítve.


## <a name="next-steps"></a>További lépések

* [Az Azure Cosmos-fiók hozzáférésének korlátozása a virtuális hálózatokon belüli alhálózat(ok)hoz](how-to-configure-vnet-service-endpoint.md)
* [Az IP-tűzfal beállítása az Azure Cosmos-fiókhoz](how-to-configure-firewall.md)

