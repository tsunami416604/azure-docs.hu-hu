---
title: Aliasrekordok – Azure DNS –
description: Ebből a cikkből megtudhatja, hogy miként támogatja az aliasrekordokat a Microsoft Azure DNS-ben.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 271770935cf4cb83d4abc6e82a4f4b13ffe865b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295487"
---
# <a name="azure-dns-alias-records-overview"></a>Az Azure DNS-aliasrekordok áttekintése

Az Azure DNS-aliasrekordok dns-rekordkészletben lévő minősítések. Hivatkozhatnak más Azure-erőforrások a DNS-zónán belül. Létrehozhat például egy alias rekordkészletet, amely egy Azure nyilvános IP-címre hivatkozik az A rekord helyett. Az aliasrekord-készlet dinamikusan egy Azure nyilvános IP-címszolgáltatás-példányra mutat. Ennek eredményeképpen az aliasrekord-készlet zökkenőmentesen frissíti magát a DNS-feloldás során.

Az Alias rekordkészlet az Azure DNS-zónában lévő következő bejegyzéstípusok esetében támogatott: 

- A
- AAAA
- CNAME

> [!NOTE]
> Ha az A vagy AAAA rekordtípusokhoz aliasrekordot kíván használni, hogy egy [Azure Traffic Manager-profilra](../traffic-manager/quickstart-create-traffic-manager-profile.md) mutasson, győződjön meg arról, hogy a Traffic Manager-profil csak [külső végpontokkal rendelkezik.](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) Meg kell adnia az IPv4- vagy IPv6-címet a Forgalomkezelő külső végpontjaihoz. A végpontokban nem használhat teljesen minősített tartományneveket.You can't use fully-qualified domain names (FQDNs) in endpoints. Ideális esetben használjon statikus IP-címeket.

## <a name="capabilities"></a>Funkciók

- **Mutasson egy nyilvános IP-erőforrásra egy DNS A/AAAA rekordkészletből.** Létrehozhat egy A/AAAA rekordkészletet, és aliasrekordként állíthatja be, hogy egy nyilvános IP-erőforrásra (szabványos vagy alapszintű) mutasson. A DNS-rekordkészlet automatikusan megváltozik, ha a nyilvános IP-cím megváltozik vagy törlődik. A helytelen IP-címekre pontbontó DNS-rekordok elkerülhetők.

   Erőforrásonként jelenleg 20 aliasrekord-készlet van.

- **Mutasson egy Traffic Manager-profilra egy DNS A/AAAA/CNAME rekordkészletből.** Létrehozhat egy A/AAAA vagy CNAME rekordkészletet, és aliasrekordok használatával a Traffic Manager-profilra irányíthatja. Ez különösen akkor hasznos, ha a forgalmat zónacsúcson kell irányítania, mivel a hagyományos CNAME rekordok nem támogatottak a zóna csúcsa esetén. Tegyük fel például, hogy a Traffic Manager-profil myprofile.trafficmanager.net, és az üzleti DNS-zóna contoso.com. Létrehozhat egy A/AAAA típusú aliasrekord-készletet contoso.com (a zóna csúcsa) számára, és myprofile.trafficmanager.net mutat.
- **Mutasson egy Azure Content Delivery Network (CDN) végpontra.** Ez akkor hasznos, ha statikus webhelyeket hoz létre az Azure Storage és az Azure CDN használatával.
- **Mutasson egy másik DNS-rekordra, amely ugyanabban a zónában található.** Az aliasrekordok hivatkozhatnak más azonos típusú rekordhalmazokra. Egy DNS CNAME rekordkészlet például egy másik CNAME rekordkészlet aliasa is lehet. Ez az elrendezés akkor hasznos, ha azt szeretné, hogy egyes rekordkészletek aliasok és egyes nem aliasok legyenek.

## <a name="scenarios"></a>Forgatókönyvek

Az Alias-rekordok esetében néhány gyakori forgatókönyv létezik.

### <a name="prevent-dangling-dns-records"></a>DNS-rekordok lelógatásának megakadályozása

A hagyományos DNS-rekordok gyakori problémája a rekordok lógatása. Például olyan DNS-rekordok, amelyek nem lettek frissítve, hogy tükrözzék az IP-címek változásait. A probléma különösen az A/AAAA vagy a CNAME rekordtípusoknál fordul elő.

Hagyományos DNS-zónarekord esetén, ha a cél IP vagy CNAME már nem létezik, a hozzá társított DNS-rekordot manuálisan kell frissíteni. Egyes szervezeteknél előfordulhat, hogy a manuális frissítés nem történik meg időben a folyamatproblémák vagy a szerepkörök és a kapcsolódó jogosultsági szintek elkülönítése miatt. Egy szerepkör például jogosult lehet egy alkalmazáshoz tartozó CNAME vagy IP-cím törlésére. De nem rendelkezik elegendő felhatalmazással a DNS-rekord frissítéséhez, amely a célokra mutat. A DNS-rekord frissítésének késleltetése a felhasználók kimaradását okozhatja.

Alias rekordok megakadályozzák lógó hivatkozások szorosan összekapcsolása a DNS-rekord életciklusát egy Azure-erőforrás. Vegyünk például egy aliasrekordnak minősített DNS-rekordot, amely nyilvános IP-címre vagy Traffic Manager-profilra mutat. Ha törli ezeket az alapul szolgáló erőforrásokat, a DNS-aliasrekord üres rekordkészletté válik. Már nem hivatkozik a törölt erőforrásra.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Dns-rekordkészlet automatikus frissítése az alkalmazás IP-címének változásakor

Ez a forgatókönyv hasonló az előzőhöz. Lehet, hogy egy alkalmazás átkerül, vagy az alapul szolgáló virtuális gép újraindul. Az aliasrekord automatikusan frissül, amikor az alapul szolgáló nyilvános IP-erőforrás IP-címe megváltozik. Ezzel elkerülhető a lehetséges biztonsági kockázatok, hogy a felhasználókat egy másik, a régi nyilvános IP-címhez rendelt alkalmazáshoz irányítsa.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>A terheléselosztással kapcsolatos alkalmazások üzemeltetése a zóna csúcsán

A DNS-protokoll megakadályozza a CNAME rekordok zónacsúcson történő hozzárendelését. Ha például a tartomány contoso.com; létrehozhat CNAME rekordokat somelabel.contoso.com; de nem hozhat létre CNAME contoso.com.
Ez a korlátozás problémát jelent azon alkalmazástulajdonosok számára, akik az [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)mögött terheléselosztással rendelkező alkalmazásokkal rendelkeznek. Mivel a Traffic Manager-profil használatához CNAME rekord létrehozása szükséges, a zónacsúcsról nem lehet a Traffic Manager-profilra mutatni.

A problémát alias-rekordok segítségével oldották meg. A CNAME rekordoktól eltérően aliasrekordok jönnek létre a zóna csúcsán, és az alkalmazástulajdonosok használhatják a zónacsúcsrekordjukat egy külső végpontokkal rendelkező Traffic Manager-profilra. Az alkalmazástulajdonosok ugyanarra a Traffic Manager-profilra mutatnak, amelyet a DNS-zónájuk bármely más tartományához használnak.

Például contoso.com és\.a www contoso.com ugyanarra a Traffic Manager-profilra mutathatnak. Ha többet szeretne tudni az aliasrekordok Azure Traffic Manager-profilokhasználatával kapcsolatos használatáról, olvassa el a Következő lépések szakaszt.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Pontzóna csúcsa az Azure CDN-végpontokhoz

A Traffic Manager-profilhoz hasonlóan aliasrekordok at is használhat a DNS-zóna csúcspontjának az Azure CDN-végpontokra való rámutatására. Ez akkor hasznos, ha statikus webhelyeket hoz létre az Azure Storage és az Azure CDN használatával. Ezután elérheti a honlapot anélkül, hogy a DNS-nevét "www" előadná.

Ha például a statikus `www.contoso.com, your users can access your site using contoso.com` webhely neve nem szükséges a www nevet a DNS-névre készíteni.

Ahogy azt korábban leírtuk, a CNAME rekordok nem támogatottak a zóna csúcsán. Ezért nem használhat CNAME rekordot a CDN-végpontra contoso.com. Ehelyett egy alias rekord segítségével közvetlenül a zóna csúcspontját cdN-végpontra irányíthatja.

> [!NOTE]
> Az Akamai Azure CDN-cdN-végpontjaira mutató zónacsúcs jelenleg nem támogatott.

## <a name="next-steps"></a>További lépések

Az aliasrekordokról az alábbi cikkekben olvashat bővebben:

- [Oktatóanyag: Állítson be egy aliasrekordot egy azure-beli nyilvános IP-címre való hivatkozáshoz](tutorial-alias-pip.md)
- [Oktatóanyag: aliasrekord konfigurálása a hogy, támogassa a legfelső szintű tartományneveket a Traffic Managerrel](tutorial-alias-tm.md)
- [DNS – gyakori kérdések](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)
