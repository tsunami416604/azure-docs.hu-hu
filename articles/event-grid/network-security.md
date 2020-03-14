---
title: Azure Event Grid erőforrások hálózati biztonsága
description: Ez a cikk azt ismerteti, hogyan konfigurálható a hozzáférés a privát végpontokról
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300153"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Azure Event Grid erőforrások hálózati biztonsága
Ez a cikk azt ismerteti, hogyan használhatók a következő biztonsági szolgáltatások a Azure Event Grid használatával: 

- Szolgáltatások címkéi a kimenő forgalomhoz (előzetes verzió)
- IP-tűzfalszabályok a bejövő forgalomhoz (előzetes verzió)
- Privát végpontok a bejövő forgalomhoz (előzetes verzió)


## <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatás címkéje egy adott Azure-szolgáltatás IP-címeinek egy csoportját jelöli. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. A szolgáltatás címkével kapcsolatos további információkért lásd: [szolgáltatási címkék áttekintése](../virtual-network/service-tags-overview.md).

A szolgáltatási címkék használatával hálózati hozzáférés-vezérlést határozhat meg a [hálózati biztonsági csoportokon](../virtual-network/security-overview.md#security-rules) vagy [Azure Firewall](../firewall/service-tags.md). A szolgáltatási címkéket adott IP-címek helyett használhatja biztonsági szabályok létrehozásakor. Ha a szolgáltatási címke nevét (például **AzureEventGrid**) adja meg egy szabály megfelelő *forrás* vagy *cél* mezőjében, engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

| Szolgáltatás címkéje | Cél | Használhat bejövő vagy kimenő adatforgalmat? | Lehet regionális? | Használható a Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. <br/><br/>*Megjegyzés:* Ez a címke az USA déli középső régiójában, az USA keleti régiójában, az USA keleti régiójában, az USA 2. nyugati régiójában és az USA középső régiójában található Azure Event Grid | Mindkettő | Nem | Nem |


## <a name="ip-firewall"></a>IP-tűzfal 
A Azure Event Grid támogatja az IP-alapú hozzáférés-vezérlést a témakörökben és tartományokban való közzétételhez. Az IP-alapú vezérlők használatával a közzétevőket egy témakörre vagy tartományra korlátozhatja, hogy csak a jóváhagyott gépek és felhőalapú szolgáltatások készlete legyen. Ez a szolgáltatás kiegészíti a Event Grid által támogatott [hitelesítési mechanizmusokat](security-authentication.md) .

Alapértelmezés szerint a témakör és a tartomány elérhető az internetről, feltéve, hogy a kérés érvényes hitelesítéssel és engedélyezéssel rendelkezik. Az IP-tűzfallal továbbra is korlátozhatja az IP-címek és IP-címtartományok [CIDR (osztály nélküli tartományok közötti útválasztás)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelölését. A más IP-címről származó közzétevőket a rendszer elutasítja, és 403 (tiltott) választ fog kapni.


## <a name="private-endpoints"></a>Privát végpontok
A [privát végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy közvetlenül a virtuális hálózatról küldje el az eseményeket egy [privát kapcsolaton](../private-link/private-link-overview.md) keresztül, anélkül, hogy a nyilvános interneten kellene haladnia. A privát végpontok egy speciális hálózati adapterek a VNet található Azure-szolgáltatásokhoz. Ha saját témakörhöz vagy tartományhoz hoz létre privát végpontot, biztonságos kapcsolatot biztosít a VNet található ügyfelek és a Event Grid erőforrás között. A magánhálózati végpont IP-címet kap a VNet IP-címének tartományához. A magánhálózati végpont és a Event Grid szolgáltatás közötti kapcsolat biztonságos privát hivatkozást használ.

![Architektúradiagram](./media/network-security/architecture-diagram.png)

A Event Grid erőforráshoz tartozó privát végpontok használata lehetővé teszi a következőket:

- Biztonságos hozzáférést biztosíthat a témakörhöz vagy tartományhoz egy VNet a Microsoft gerinc hálózatán keresztül, a nyilvános internettel szemben.
- Biztonságos csatlakozás a VNet csatlakozó helyszíni hálózatokról VPN vagy Expressroute használatával.

Ha a VNet egy témakörhöz vagy tartományhoz hoz létre privát végpontot, a jóváhagyásra vonatkozó kérést küld a rendszer az erőforrás tulajdonosának. Ha a privát végpont létrehozását kérő felhasználó az erőforrás tulajdonosa is, akkor a rendszer ezt a jóváhagyási kérést automatikusan jóváhagyja. Ellenkező esetben a rendszer a jóváhagyás előtt **függő** állapotba kerül. A VNet lévő alkalmazások zökkenőmentesen csatlakozhatnak a Event Grid szolgáltatáshoz a magánhálózati végponton keresztül, ugyanazokkal a kapcsolati karakterláncokkal és engedélyezési mechanizmusokkal, amelyeket egyébként használni fognak. Az erőforrás-tulajdonosok a Azure Portal erőforráshoz tartozó **magánhálózati végpontok** lapon kezelhetik a belefoglalt kérelmeket és a privát végpontokat.

### <a name="connect-to-private-endpoints"></a>Kapcsolódás privát végpontokhoz
A privát végpontot használó VNet közzétevői ugyanazt a kapcsolati karakterláncot használják a témakörhöz vagy a tartományhoz, mint a nyilvános végponthoz csatlakozó ügyfelek. A DNS-feloldás automatikusan átirányítja a kapcsolatokat a VNet a témakörre vagy tartományra egy privát hivatkozáson keresztül. A Event Grid alapértelmezés szerint létrehoz egy [saját DNS-zónát](../dns/private-dns-overview.md) , amely a VNet csatolva van a saját végpontokhoz szükséges frissítéssel. Ha azonban a saját DNS-kiszolgálóját használja, előfordulhat, hogy további módosításokat kell végeznie a DNS-konfigurációban.

### <a name="dns-changes-for-private-endpoints"></a>A magánhálózati végpontok DNS-módosításai
Privát végpont létrehozásakor az erőforráshoz tartozó DNS CNAME rekord a `privatelink`előtaggal rendelkező altartományban található aliasra frissül. Alapértelmezés szerint a rendszer létrehoz egy magánhálózati DNS-zónát, amely megfelel a magánhálózati kapcsolat altartományának. 

Ha a témakör vagy a tartomány végpontjának URL-címét a VNet kívülről a privát végpontra oldja fel, a megoldás a szolgáltatás nyilvános végpontját oldja fel. A "Topica" DNS-erőforrásrekordjait, ha a privát végpontot üzemeltető **VNet kívülről** oldották fel, a következő lesz:

| Név                                          | Típus      | Érték                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure Traffic Manager-profil\>

Az [IP-tűzfal](#ip-firewall)használatával megtagadhatja vagy szabályozhatja az VNet kívüli ügyfelek hozzáférését a nyilvános végponton keresztül. 

A privát végpontot futtató VNet feloldva a témakör vagy a tartomány végpontjának URL-címe feloldódik a magánhálózati végpont IP-címére. A "témakör" nevű DNS-erőforrásrekordok a privát végpontot futtató **VNet** feloldva a következő lesz:

| Név                                          | Típus      | Érték                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | Az         | 10.0.0.5

Ez a megközelítés lehetővé teszi, hogy a témakörhöz vagy tartományhoz ugyanazt a kapcsolati karakterláncot használja, mint a privát végpontokat üzemeltető VNet és a VNet kívüli ügyfelek számára.

Ha a hálózaton egyéni DNS-kiszolgálót használ, akkor az ügyfelek a témakör vagy a tartomány végpontjának teljes tartománynevét feloldják a magánhálózati végpont IP-címére. Konfigurálja a DNS-kiszolgálót úgy, hogy delegálja a magánhálózati kapcsolat altartományát a VNet tartozó magánhálózati DNS-zónához, vagy konfigurálja a `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` a magánhálózati végpont IP-címével.

Az ajánlott DNS-zóna neve `privatelink.eventgrid.azure.net`.

### <a name="private-endpoints-and-publishing"></a>Privát végpontok és közzététel

A következő táblázat ismerteti a magánhálózati végponti kapcsolatok különböző állapotait és a közzétételre gyakorolt hatásokat:

| Kapcsolat állapota   |  Sikeres közzététel (igen/nem) |
| ------------------ | -------------------------------|
| Jóváhagyott           | Igen                            |
| Elutasítva           | Nem                             |
| Függőben            | Nem                             |
| Leválasztva       | Nem                             |

Ahhoz, hogy a közzététel sikeres legyen, **jóvá**kell hagyni a privát végponti kapcsolatok állapotát. Ha a rendszer visszautasítja a kapcsolatokat, a Azure Portal használatával nem lehet jóváhagyni. Az egyetlen lehetőség, hogy törölje a kapcsolódást, és hozzon létre egy újat.

## <a name="pricing-and-quotas"></a>Díjszabás és kvóták
A **privát végpontok** csak a prémium szintű csomagokkal és tartományokkal érhetők el. Event Grid lehetővé teszi, hogy a rendszer akár 64 privát végponti kapcsolatot hozzon létre egy témakör vagy tartomány alapján. Az alapszintű és a prémium szintű csomagra való frissítéshez tekintse meg a [frissítés díjszabási szintjét](update-tier.md) ismertető cikket.

Az **IP-tűzfal** funkció a Event Grid alapszintű és prémium szintjein is elérhető. Egy témakör vagy tartomány alapján legfeljebb 16 IP-tűzfalszabály hozható létre.


## <a name="next-steps"></a>További lépések
A Event Grid erőforrás IP-tűzfalát úgy is beállíthatja, hogy a nyilvános interneten keresztül csak az IP-címek és az IP-címtartományok egyetlen kiválasztott készletével korlátozza a hozzáférést. Részletes útmutatásért lásd: az [IP-tűzfal konfigurálása](configure-firewall.md).

A privát végpontokat úgy is beállíthatja, hogy csak a kiválasztott virtuális hálózatokról korlátozza a hozzáférést. Részletes útmutatásért lásd: [privát végpontok konfigurálása](configure-private-endpoints.md).
