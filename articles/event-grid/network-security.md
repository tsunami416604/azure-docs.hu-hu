---
title: Az Azure Event Grid erőforrásainak hálózati biztonsága
description: Ez a cikk a privát végpontok hozzáférésének konfigurálását ismerteti
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300153"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Az Azure Event Grid erőforrásainak hálózati biztonsága
Ez a cikk a következő biztonsági funkciók azure Event Grid használatával ismertetjük: 

- A kimenő forgalom szolgáltatáscímkéi (előzetes verzió)
- A be- és biztonságba való be- és visszaszolgáltatás IP-tűzfal szabályai (előzetes verzió)
- Privát végpontok a be- és visszatámadásokhoz (előzetes verzió)


## <a name="service-tags"></a>Szolgáltatáscímkék
A szolgáltatáscímke egy adott Azure-szolgáltatás IP-címelőtagjainak csoportját jelöli. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásaként, minimalizálva a hálózati biztonsági szabályok gyakori frissítéseinek összetettségét. A szolgáltatáscímkékről a [Szolgáltatáscímkék – áttekintés című témakörben olvashat bővebben.](../virtual-network/service-tags-overview.md)

A szolgáltatáscímkék segítségével hálózati hozzáférés-vezérlést határozhat meg [a hálózati biztonsági csoportokon](../virtual-network/security-overview.md#security-rules) vagy az [Azure Firewall-en.](../firewall/service-tags.md) Biztonsági szabályok létrehozásakor a szolgáltatáscímkéket használjon adott IP-címek helyett. A szolgáltatáscímke nevének (például **az AzureEventGrid)** megadásával a szabály megfelelő *forrás-* vagy *célmezőjében* engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát.

| Szolgáltatáscímke | Cél | Használhatja a bejövő vagy kimenő? | Lehet regionális? | Használhatja az Azure Tűzfal? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. <br/><br/>*Megjegyzés:* Ez a címke az Azure Event Grid végpontjait tartalmazza az USA déli középső részén, az USA keleti részén, az USA keleti részén 2, az USA nyugati 2- és usa-beli központi végpontjait. | Mindkettő | Nem | Nem |


## <a name="ip-firewall"></a>IP-tűzfal 
Az Azure Event Grid támogatja az IP-alapú hozzáférés-vezérlést a témakörökés tartományok közzétételéhez. Az IP-alapú vezérlőkkel a közzétevők egy témakörre vagy tartományra korlátozhatók, csak jóváhagyott gépek és felhőszolgáltatások készletére. Ez a szolgáltatás kiegészíti az Event Grid által támogatott [hitelesítési mechanizmusokat.](security-authentication.md)

Alapértelmezés szerint a témakör és a tartomány elérhető az internetről, amíg a kérelem érvényes hitelesítéssel és engedélyezéssel érkezik. Az IP-tűzfal lal tovább korlátozhatja a [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelölésben lévő IP-címek vagy IP-címtartományok készletére. Bármely más IP-címről származó megjelenítők elutasításra kerülnek, és 403 (Tiltott) választ kapnak.


## <a name="private-endpoints"></a>Privát végpontok
[A privát végpontok](../private-link/private-endpoint-overview.md) használatával engedélyezheti az események közvetlenül a virtuális hálózatról a témakörökbe és tartományokba biztonságosan egy [privát kapcsolaton](../private-link/private-link-overview.md) keresztül, anélkül, hogy a nyilvános interneten keresztül haladna. A privát végpont egy speciális hálózati felület egy Azure-szolgáltatás a virtuális hálózatban. Amikor létrehoz egy privát végpontot a témakörhöz vagy a tartományhoz, biztonságos kapcsolatot biztosít a virtuális hálózaton és az Event Grid erőforráson lévő ügyfelek között. A privát végpont hoz egy IP-címet a virtuális hálózat IP-címtartományából. A privát végpont és az Event Grid szolgáltatás közötti kapcsolat biztonságos privát kapcsolatot használ.

![Architektúradiagram](./media/network-security/architecture-diagram.png)

Az Event Grid erőforrás privát végpontjainak használata lehetővé teszi a következőket:

- Biztonságos hozzáférés a témakörhöz vagy tartományhoz a Microsoft gerinchálózaton keresztüli virtuális hálózatról, nem pedig a nyilvános internetről.
- Biztonságosan csatlakozhat a helyszíni hálózatokról, amelyek VPN vagy ExpressRoutes használatával csatlakoznak a virtuális hálózathoz, privát társviszony-létesítéssel.

Amikor létrehoz egy privát végpontot egy témakörhöz vagy tartományhoz a virtuális hálózatban, a rendszer jóváhagyásra jóváhagyást küld jóváhagyásra. Ha a privát végpont létrehozását kérő felhasználó is az erőforrás tulajdonosa, ez a hozzájárulási kérelem automatikusan jóváhagyásra kerül. Ellenkező esetben a kapcsolat **függőben** van, amíg jóvá nem hagyják. A virtuális hálózatban lévő alkalmazások zökkenőmentesen csatlakozhatnak az Event Grid szolgáltatáshoz a privát végponton keresztül, ugyanazokat a kapcsolati karakterláncokat és engedélyezési mechanizmusokat használva, amelyeket egyébként használnának. Az erőforrás-tulajdonosok kezelhetik a hozzájárulási kérelmeket és a privát végpontokat az Azure Portalon lévő erőforrás **privát végpontjai** lapon keresztül.

### <a name="connect-to-private-endpoints"></a>Csatlakozás privát végpontokhoz
A magánhálózati végpontot használó virtuális hálózat közzétevőinek ugyanazt a kapcsolati karakterláncot kell használniuk a témakörhöz vagy a tartományhoz, mint a nyilvános végponthoz csatlakozó ügyfelek. A DNS-feloldás automatikusan a virtuális hálózatról a témakörhöz vagy tartományhoz egy privát kapcsolaton keresztül irányítja a kapcsolatokat. Event Grid létrehoz egy [privát DNS-zóna](../dns/private-dns-overview.md) csatlakozik a virtuális hálózat hoz létre a szükséges frissítést a magán-végpontok, alapértelmezés szerint. Ha azonban saját DNS-kiszolgálót használ, előfordulhat, hogy további módosításokat kell végrehajtania a DNS-konfiguráción.

### <a name="dns-changes-for-private-endpoints"></a>DNS-módosítások a magánvégpontokhoz
Privát végpont létrehozásakor az erőforrás DNS CNAME rekordja egy altartományban lévő aliasra `privatelink`frissül. Alapértelmezés szerint létrejön egy privát DNS-zóna, amely megfelel a privát hivatkozás altartományának. 

Ha feloldja a témakör vagy a tartomány végpontjának URL-címét a virtuális hálózaton kívülről a privát végponttal, a szolgáltatás nyilvános végpontjára oldódik fel. A "topicA" DNS-erőforrásrekordjai, ha a privát végpontot üzemeltető **virtuális hálózaton kívülről** oldják fel, a következők lesznek:

| Név                                          | Típus      | Érték                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<az azure traffic manager profilja\>

A virtuális hálózaton kívüli ügyfelek hozzáférését megtagadhatja vagy szabályozhatja a nyilvános végponton keresztül az [IP-tűzfal](#ip-firewall)használatával. 

Ha a privát végpontot üzemeltető virtuális hálózatról oldják fel, a témakör vagy a tartomány végpontjának URL-címe a privát végpont IP-címére oldódik fel. A "topicA" témakör DNS-erőforrásrekordjai, ha a privát végpontot üzemeltető **virtuális hálózatból** oldják fel, a következők lesznek:

| Név                                          | Típus      | Érték                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Ez a megközelítés lehetővé teszi a hozzáférést a témakörhöz vagy a tartományhoz a privát végpontokat üzemeltető virtuális hálózaton lévő ügyfelek és a virtuális hálózaton kívüli ügyfelek azonos kapcsolati karakterláncának használatával.

Ha egyéni DNS-kiszolgálót használ a hálózaton, az ügyfelek feloldhatják a témakör vagy tartományvégpont teljes tartománynát a privát végpont IP-címére. Konfigurálja úgy a DNS-kiszolgálót, hogy delegálja a privát hivatkozás altartományát a virtuális hálózat privát DNS-zónájának, vagy konfigurálja az A rekordokat a privát végpont `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` IP-címével.

Az ajánlott DNS-zónanév . `privatelink.eventgrid.azure.net`

### <a name="private-endpoints-and-publishing"></a>Privát végpontok és közzététel

Az alábbi táblázat a privát végpontkapcsolat különböző állapotait és a közzétételre gyakorolt hatásokat ismerteti:

| Kapcsolat állapota   |  Sikeres közzététel (Igen/Nem) |
| ------------------ | -------------------------------|
| Approved           | Igen                            |
| Elutasítva           | Nem                             |
| Függőben            | Nem                             |
| Elválasztott       | Nem                             |

Ahhoz, hogy a közzététel sikeres legyen, a privát végpont kapcsolati állapotát jóvá kell **hagyni.** Ha egy kapcsolat elutasításra kerül, nem lehet jóváhagyni az Azure Portalhasználatával. Az egyetlen lehetőség az, hogy törölje a kapcsolatot, és hozzon létre egy újat helyette.

## <a name="pricing-and-quotas"></a>Árképzés és kvóták
**A privát végpontok** csak a prémium szintű témakörök és tartományok érhetők el. Az Event Grid témakörenként vagy tartományonként legfeljebb 64 privát végpont-kapcsolat létrehozását teszi lehetővé. Az alapszintű szintről a prémium szintre való frissítéshez tekintse meg a [Tarifacsomag frissítése](update-tier.md) cikket.

**Az IP-tűzfal** szolgáltatás az Event Grid alap- és prémium szintjein is elérhető. Témánként vagy tartományonként akár 16 IP-tűzfal szabályt is lehetővé teszünk.


## <a name="next-steps"></a>További lépések
Az Event Grid erőforrásHOZ beállíthatja az IP-tűzfalat, hogy korlátozza a hozzáférést a nyilvános interneten keresztül csak az IP-címek vagy IP-címtartományok kiválasztott készletéből. Az [IP-tűzfal konfigurálása](configure-firewall.md)című témakörben talál részletes útmutatást.

A privát végpontok konfigurálhatók úgy, hogy csak a kiválasztott virtuális hálózatokhozzáférését korlátozzák. A privát [végpontok konfigurálása](configure-private-endpoints.md)című témakörben talál részletes útmutatást.
