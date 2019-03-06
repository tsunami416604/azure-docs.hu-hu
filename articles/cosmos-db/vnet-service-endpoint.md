---
title: Biztonságos hozzáférés az Azure Cosmos DB-fiókot az Azure virtuális hálózati szolgáltatásvégpont
description: Ez a dokumentum ismerteti a virtuális hálózati és alhálózati hozzáférés-vezérlés az Azure Cosmos-fiók.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d0bb9ad5c3a39f4065a81fb218a4b167f6fde9b1
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447557"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Hozzáférés az Azure Cosmos DB a virtuális hálózatok (VNet)

Beállíthatja, hogy engedélyezze a hozzáférést csak a megadott alhálózat virtuális hálózat (VNet) az Azure Cosmos fiókot. Engedélyezésével [szolgáltatásvégpont](../virtual-network/virtual-network-service-endpoints-overview.md) elérni az Azure Cosmos DB az alhálózat egy virtuális hálózaton belül, az adott alhálózatról származó forgalmat el kell küldeni Azure Cosmos DB és az alhálózat és virtuális hálózat azonosítóját. Az Azure Cosmos DB-szolgáltatásvégpont engedélyezése után az Azure Cosmos-fiók hozzáadásával korlátozhatja az alhálózathoz való hozzáférés.

Alapértelmezés szerint az Azure Cosmos-fiók érhető el bármilyen forrásból származó ha érvényes engedélyezési jogkivonat a kérelemben. Amikor hozzáad egy vagy több Vnetek alhálózatainak, csak érkező kérések ezekhez az alhálózatokhoz érvényes választ fog kapni. Bármilyen más forrásból érkező kérelmek esetén kapnak a 403-as (tiltott) választ. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Az alábbiakban néhány gyakori kérdés a virtuális hálózatok hozzáférés beállításával kapcsolatban:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Meghatározható, hogy mind a virtuális hálózati szolgáltatásvégpont IP hozzáférés-vezérlési szabályzat az Azure Cosmos-fiókkal? 

Engedélyezheti a virtuális hálózati szolgáltatásvégpont és a egy IP hozzáférés-vezérlési házirend (más néven tűzfal) a Azure Cosmos-fiókjában. Ezen két funkció egymást egészítik ki, és együttesen biztosítása elkülönítési és biztonsági az Azure Cosmos-fiók. IP használata tűzfal biztosítja, hogy statikus IP-címek is elérni a fiókját. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hogyan korlátozza a hozzáférést egy virtuális hálózatban lévő alhálózat? 

Egy alhálózatról korlátozható az Azure Cosmos-fiókjához való hozzáférés szükséges két lépésből áll. Először az alhálózat és virtuális hálózati azonosságát az Azure Cosmos DB alhálózatból érkező forgalmat engedélyezi. Szolgáltatásvégpont engedélyezése az Azure Cosmos DB az alhálózat végzi. Ezután a szabály hozzáadása az Azure Cosmos-fiókban adja meg ezt az alhálózatot, amelyből a fiókhoz elérhető lesz forrásként.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Elutasítják virtuális hálózati hozzáférés-vezérlési listák és IP-tűzfalon, kérések vagy a kapcsolatot? 

IP-tűzfal vagy a virtuális hálózati hozzáférési szabályok bővül, csak az engedélyezett források get adható érvényes válaszok érkező kérelmeket. Más kérelmeket a rendszer elutasítja a 403 (tiltott). Fontos megkülönböztetni a tűzfal az Azure Cosmos-fiók egy kapcsolat adatbázisszintű tűzfalszabály. A forrás továbbra is lehet kapcsolódni a szolgáltatáshoz, és maguk a kapcsolatok nem elutasították.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Saját kérések a blokkolja a I engedélyezésekor az alhálózaton az Azure Cosmos DB-service-végpont elindult. Mi történt?

Miután az Azure Cosmos DB a szolgáltatásvégpont engedélyezve van a alhálózat, a éri el a fiókot a forgalom forrását a nyilvános IP-címről vált, amennyiben az virtuális hálózatot és alhálózatot. Ha az Azure Cosmos-fiók nem rendelkezik IP-alapú tűzfal csak szolgáltatást engedélyező alhálózatról származó forgalmat már nem megfelelő IP-tűzfalszabályainak, és ezért elutasítja. Nyissa meg a lépéseket áttelepíthetik a tűzfal IP-alapú virtuális hálózat-alapú hozzáférés-vezérlés keresztül.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>A virtuális társhálózatok is rendelkezik hozzáféréssel az Azure Cosmos-fiók? 
Csak a virtuális hálózat és az Azure Cosmos-fiókhoz hozzáadott alhálózatok hozzáféréssel rendelkezik. A virtuális társhálózatba tartozó virtuális hálózatok nem tudja elérni a fiókját, a társviszonyban lévő virtuális hálózatokban lévő alhálózatok a fiók felvételének befejezéséig.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Alhálózatok maximális számának engedélyezett egyetlen Cosmos-fiókja eléréséhez? 
Egy Azure Cosmos-fiók számára engedélyezett legfeljebb 64 alhálózatok jelenleg is rendelkezhet.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Engedélyezheti a hozzáférést a VPN és Expressroute? 
Az Azure Cosmos-fiók eléréséhez feletti Express route a helyszínen, kell engedélyezni a Microsoft társviszony-létesítés. Ha IP-tűzfal vagy a virtuális hálózati hozzáférési szabályok, a Microsoft társviszony-létesítéshez az Azure Cosmos fiók IP-tűzfalon, hogy az Azure Cosmos-fiók a helyi szolgáltatások hozzáférést használt nyilvános IP-címeket is hozzáadhat. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Kell frissíteni a hálózati biztonsági csoportok (NSG) szabályai? 
NSG-szabályok és a egy alhálózatot a virtuális hálózati kapcsolat korlátozására használhatók. Hozzáadásakor szolgáltatásvégpontot az Azure Cosmos DB az alhálózathoz, van, a nem szükséges, a kimenő kapcsolat megnyitása az NSG-t az Azure Cosmos-fiók. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>A Szolgáltatásvégpontok mindegyik virtuális hálózat érhető el?
Nem, csak az Azure Resource Managerbeli virtuális hálózat lehet a szolgáltatásvégpont engedélyezve van. A klasszikus virtuális hálózatok nem támogatják a Szolgáltatásvégpontok.


## <a name="next-steps"></a>További lépések

* [Hogyan lehet Azure Cosmos fiók férjenek hozzá az alhálózat virtuális hálózatokon belül](how-to-configure-vnet-service-endpoint.md)
* [Az Azure Cosmos-fiókhoz tartozó IP-tűzfal konfigurálása](how-to-configure-firewall.md)

