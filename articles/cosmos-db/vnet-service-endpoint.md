---
title: Biztonságos hozzáférés Azure Cosmos DB fiókokhoz a virtuális hálózati szolgáltatás végpontjának használatával
description: Ez a dokumentum az Azure Cosmos-fiók virtuális hálózati és alhálózati hozzáférés-vezérlését ismerteti.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d264ead87e7fa638830bf25fdb07983b164334b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83698662"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Az Azure Cosmos DB elérése virtuális hálózatról (VNet)

Az Azure Cosmos-fiókot úgy is beállíthatja, hogy csak a virtuális hálózat (VNet) egy adott alhálózatáról engedélyezze a hozzáférést. A [szolgáltatás végpontjának](../virtual-network/virtual-network-service-endpoints-overview.md) a virtuális hálózaton belüli alhálózathoz Azure Cosmos db való hozzáférésének engedélyezésével az adott alhálózatról érkező forgalmat a rendszer a Azure Cosmos db az alhálózat és a Virtual Network identitásával küldi el. Ha a Azure Cosmos DB szolgáltatás végpontja engedélyezve van, az alhálózathoz való hozzáférést az Azure Cosmos-fiókjához hozzáadva korlátozhatja.

Alapértelmezés szerint az Azure Cosmos-fiók bármely forrásból elérhető, ha a kérést érvényes engedélyezési jogkivonat kíséri. Ha egy vagy több alhálózatot ad hozzá a virtuális hálózatok-n belül, csak ezekből az alhálózatokból származó kérelmek érvényes választ kapnak. A más forrásból származó kérelmek 403 (tiltott) választ kapnak. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Íme néhány gyakori kérdés a virtuális hálózatok hozzáférésének konfigurálásáról:

### <a name="are-notebooks-and-mongo-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>A jegyzetfüzetek és a Mongo-rendszerhéj jelenleg kompatibilis a Virtual Network engedélyezve fiókokkal?

A Cosmos DB Adatkezelő és a [Jupyter Notebooks szolgáltatás](https://docs.microsoft.com/azure/cosmos-db/cosmosdb-jupyter-notebooks) [Mongo-rendszerhéj-integrációja](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) jelenleg nem támogatott a VNET-hozzáférés esetén. Ez jelenleg aktív fejlesztés alatt áll.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Megadhatom a virtuális hálózati szolgáltatás végpontját és az IP-hozzáférés-vezérlési házirendet egy Azure Cosmos-fiókon? 

A virtuális hálózati szolgáltatás végpontját és egy IP-hozzáférés-vezérlési házirendet (aka tűzfal) is engedélyezhet az Azure Cosmos-fiókban. Ez a két funkció kiegészíti és együttesen biztosítja az Azure Cosmos-fiók elkülönítését és biztonságát. Az IP-tűzfal használatával biztosíthatja, hogy a statikus IP-címek hozzáférhessenek a fiókjához. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hogyan korlátozza a virtuális hálózaton belüli alhálózat elérését? 

Két lépés szükséges az Azure Cosmos-fiókhoz való hozzáférés korlátozásához egy alhálózaton. Először is engedélyezheti az alhálózatról érkező adatforgalmat, hogy az alhálózatot és a virtuális hálózati identitást Azure Cosmos DBba vigye. Ezt úgy teheti meg, hogy engedélyezi a szolgáltatási végpontot Azure Cosmos DB az alhálózaton. A következő lépés egy olyan szabály hozzáadása az Azure Cosmos-fiókban, amely ezt az alhálózatot adja meg forrásként, amelyből a fiók elérhető.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>A virtuális hálózati ACL-ek és az IP-tűzfal elutasítja a kérelmeket és a kapcsolatokat? 

Az IP-tűzfal vagy a virtuális hálózati hozzáférési szabályok hozzáadásakor a rendszer csak az engedélyezett forrásokból érkező kérelmeket fogadja el érvényes válaszként. A többi kérelem elutasítása 403 (tiltott). Fontos, hogy megkülönböztetni az Azure Cosmos-fiók tűzfalát a kapcsolódási szintű tűzfallal. A forrás továbbra is csatlakozhat a szolgáltatáshoz, és maga a kapcsolat nem kerül elutasításra.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>A rendszer megkezdte a kérések letiltását, amikor Engedélyeztem a szolgáltatási végpontot az alhálózaton való Azure Cosmos DB. Mi történt?

Ha a Azure Cosmos DB szolgáltatás végpontja engedélyezve van az alhálózaton, akkor a fiók a nyilvános IP-címről a virtuális hálózatra és az alhálózatra irányuló forgalom forrása. Ha az Azure Cosmos-fiók csak IP-alapú tűzfallal rendelkezik, akkor a szolgáltatással kompatibilis alhálózatról érkező forgalom már nem felel meg az IP-tűzfalszabályok feltételeinek, ezért a rendszer elutasítja. Ugorjon át az IP-alapú tűzfalról a virtuális hálózati hozzáférés-vezérlésre való zökkenőmentes Migrálás lépéseire.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>További RBAC engedélyek szükségesek az Azure Cosmos-fiókokhoz a VNET szolgáltatás-végpontokkal?

Miután hozzáadta a VNet szolgáltatás-végpontokat egy Azure Cosmos-fiókhoz, a Fiókbeállítások módosítása érdekében hozzá kell férnie az `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` Azure Cosmos-fiókban konfigurált összes virtuális hálózatok művelethez. Erre az engedélyre azért van szükség, mert az engedélyezési folyamat érvényesíti az erőforrásokhoz való hozzáférést (például az adatbázist és a virtuális hálózati erőforrásokat) a tulajdonságok kiértékelése előtt.
 
Az engedélyezés ellenőrzi a VNet erőforrás-művelet engedélyét akkor is, ha a felhasználó nem ad meg VNET ACL-eket az Azure CLI használatával. Az Azure Cosmos-fiók vezérlő síkja jelenleg az Azure Cosmos-fiók teljes állapotának beállítását támogatja. A vezérlési sík hívásának egyik paramétere a következő: `virtualNetworkRules` . Ha ez a paraméter nincs megadva, az Azure CLI beolvassa az adatbázis lekérését, `virtualNetworkRules` és ezt az értéket használja a frissítési hívásban.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Az elérhető virtuális hálózatok hozzáférhetnek az Azure Cosmos-fiókhoz is? 
Csak a virtuális hálózat és az Azure Cosmos-fiókhoz hozzáadott alhálózatok férnek hozzá. A társ virtuális hálózatok nem férhetnek hozzá a fiókhoz, amíg a virtuális hálózatok alhálózatai hozzá nem kerülnek a fiókhoz.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Legfeljebb hány alhálózat fér hozzá egyetlen Cosmos-fiókhoz? 
Jelenleg legfeljebb 256 alhálózat lehet egy Azure Cosmos-fiókhoz.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Engedélyezhető a hozzáférés a VPN-ről és az expressz útvonalról? 
Ahhoz, hogy az Azure Cosmos-fiókot a helyszíni expressz útvonalon keresztül lehessen elérni, engedélyeznie kell a Microsoft-partneri kapcsolatot. Az IP-tűzfal vagy a virtuális hálózat hozzáférési szabályainak megadása után hozzáadhatja a Microsoft-társhoz használt nyilvános IP-címeket az Azure Cosmos-fiók IP-tűzfalán, hogy engedélyezze a helyszíni szolgáltatások hozzáférését az Azure Cosmos-fiókhoz. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Frissíteni kell a hálózati biztonsági csoportok (NSG) szabályait? 
A NSG szabályok a virtuális hálózattal rendelkező alhálózatok és azok közötti kapcsolatok korlátozására szolgálnak. Amikor szolgáltatási végpontot ad hozzá Azure Cosmos DBhoz az alhálózathoz, nincs szükség a kimenő kapcsolat megnyitására az Azure Cosmos-fiók NSG. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Elérhetőek-e a szolgáltatási végpontok az összes virtuális hálózatok?
Nem, csak Azure Resource Manager virtuális hálózatokon engedélyezhető a szolgáltatás végpontja. A klasszikus virtuális hálózatok nem támogatják a szolgáltatási végpontokat.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>"Elfogadom a kapcsolatokat a nyilvános Azure-adatközpontokban", ha a szolgáltatás-végponti hozzáférés engedélyezve van a Azure Cosmos DBhoz?  
Erre csak akkor van szükség, ha azt szeretné, hogy a Azure Cosmos DB-fiókját más Azure-szolgáltatások, például az Azure-beli adat-előállító, az Azure Cognitive Search vagy bármely, az adott Azure-régióban üzembe helyezett szolgáltatás hozzáférhessen.


## <a name="next-steps"></a>További lépések

* [Azure Cosmos-fiók hozzáférésének korlátozása virtuális hálózatokon belüli alhálózatokhoz](how-to-configure-vnet-service-endpoint.md)
* [Az IP-tűzfal konfigurálása az Azure Cosmos-fiókhoz](how-to-configure-firewall.md)

