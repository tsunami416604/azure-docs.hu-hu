---
title: "Aszimmetrikus útválasztás | Microsoft Docs"
description: "Ez a cikk végigvezeti Önt azokon a problémákon, amelyekkel az ügyfeleknek szembe kell nézniük, amikor aszimmetrikus útválasztást állítanak be olyan hálózatokon, amelyek több kapcsolattal is rendelkeznek a célhoz."
documentationcenter: na
services: expressroute
author: osamazia
manager: carmonm
editor: 
ms.assetid: a754bff9-95c9-44b5-9796-377fc21e8322
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: osamam
ms.openlocfilehash: 8568c13d2834a0643e15ab1814a35c92123837d1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Aszimmetrikus útválasztás több hálózati elérési úttal
A cikk leírja, hogy hogyan követhet a kimenő és a bejövő hálózati forgalom különböző utakat, amikor a hálózati forrás és cél között több elérési út is rendelkezésre áll.

Az aszimmetrikus útválasztás megértéséhez két koncepciót kell átlátnunk. Elsőként azt, hogy milyen hatással van a hálózatra, ha több elérési út is létezik. A másik pedig az, hogy az eszközök, például a tűzfalak, hogyan őrzik meg állapotukat. Az ilyen típusú eszközöket állapot-nyilvántartó eszközöknek nevezzük. E két tényező kombinációja olyan helyzeteket teremthet, amikor a hálózati forgalmat egy állapot-nyilvántartó eszköz elveti, mivel nem észleli, hogy a forgalom kiindulópontja saját maga.

## <a name="multiple-network-paths"></a>Több hálózati elérési út
Ha a vállalati hálózat csupán egyetlen, az internetszolgáltatón átmenő kapcsolattal rendelkezik az internethez, az internet felé irányuló, illetve az internetről visszaérkező minden forgalom ugyanazt az utat járja be. A vállalatok azonban gyakran több kapcsolatcsoportot is vásárolnak, mivel ezek redundáns elérési útként szolgálhatnak, illetve hozzájárulnak a hálózat állásidejének csökkentéséhez. Ebben az esetben előfordulhat, hogy a hálózatról kilépő, az internet felé irányuló forgalom az egyik kapcsolaton, míg a visszatérő forgalom egy másik kapcsolaton halad át. Ezt a helyzetet nevezik közkeletűen aszimmetrikus útválasztásnak. Az aszimmetrikus útválasztás esetén a fordított irányú hálózati forgalom egy másik elérési utat vesz igénybe, mint az eredeti adatfolyam.

![Több elérési úttal rendelkező hálózatok](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Bár ez elsősorban az internet elérésekor használatos, az aszimmetrikus útválasztás az elérési utak egyéb kombinációit is jelölheti. Ilyenek például azok az esetek, amikor ugyanahhoz a célhoz internetes és privát útvonal is vezet, amikor ugyanahhoz a célhoz több privát útvonal vezet stb.

A forrás és a cél közötti elérési úton elhelyezkedő minden egyes útválasztó a lehető legjobb útvonalat számítja ki a cél eléréséhez. Az útválasztó két fontos tényezőt vesz figyelembe a lehető legjobb útvonal meghatározásakor:

* A külső hálózatok közötti útválasztást az általában csak BGP néven emlegetett útválasztási protokoll, a peremátjáró protokoll (Border Gateway Protocol) határozza meg. A BGP átveszi a szomszédok hirdetményeit, és különböző műveleteket futtat rajtuk annak érdekében, hogy megállapítsa a célhoz vezető legjobb utat. A legjobb útvonalat az útválasztó beírja az útvonaltáblába.
* Az útvonalhoz társított alhálózati maszk hossza befolyásolja az útválasztási útvonalakat. Ha az útválasztó ugyanahhoz az IP-címhez több hirdetményt kap különböző alhálózati maszkokkal, akkor a hosszabb alhálózati maszkkal rendelkező hirdetményt részesíti előnyben, mivel ezt tartja pontosabb útvonalnak.

## <a name="stateful-devices"></a>Állapot-nyilvántartó eszközök
Az útválasztók az útválasztás megállapítása során a csomag IP-fejlécét veszik alapul. Bizonyos eszközök még mélyebben megvizsgálják a csomagot. Általában ezek az eszközök a 4. rétegbeli (Transmission Control Protocol, azaz TCP vagy User Datagram Protocol, azaz UDP), sőt akár a 7. rétegbeli (alkalmazási réteg) fejléceket vizsgálják. Ezek az eszközök általában biztonsági vagy sávszélesség-optimalizáló eszközök. 

Gyakori állapot-nyilvántartó eszközök például a tűzfalak. A tűzfalak különböző mezők, például a protokoll, a TCP/UDP-port vagy az URL-fejlécek alapján engedik át vagy fordítják vissza a felületekre érkező csomagokat. A csomagok vizsgálata jelentős számítási terhelést ró az eszközre. A teljesítmény javítása érdekében a tűzfalak általában csak az adatfolyamok első csomagját vizsgálják meg. Ha a tűzfal átengedi a csomagot, akkor az állapottáblázatában megőrzi az adatfolyam információit. Az adatfolyamhoz tartozó további csomagokat az első csomag alapján hozott döntésnek megfelelően engedélyezi vagy tiltja a tűzfal. Tegyük fel, hogy egy már létező adatfolyamhoz tartozó csomag érkezik a tűzfalra. Amennyiben a tűzfal nem rendelkezik a csomagról előzetes állapotinformációkkal, eldobja.

## <a name="asymmetric-routing-with-expressroute"></a>Aszimmetrikus útválasztás az ExpressRoute-tal
Ha az Azure ExpressRoute segítségével csatlakozik a Microsofthoz, a hálózata az alábbiak szerint módosul:

* Több kapcsolat jön létre a Microsofthoz. Az egyik kapcsolat a meglévő internetkapcsolat, a másik pedig az ExpressRoute-on keresztüli kapcsolat. Előfordulhat, hogy a forgalom egy része az interneten keresztül jut el a Microsofthoz, de az ExpressRoute-on át tér vissza, vagy fordítva.
* Az ExpressRoute konkrétabb IP-címeket küld. Így az ExpressRoute-on keresztül kínált szolgáltatásokra vonatkozó, a hálózat és a Microsoft közötti forgalom esetén az útválasztók mindig az ExpressRoute-ot fogják előnyben részesíteni.

E két változás hálózatra gyakorolt hatásának megértéséhez gondoljunk át néhány forgatókönyvet. Példaképpen tegyük fel, hogy csak egyetlen internet felé néző kapcsolatcsoport van a birtokában, és az összes Microsoft-szolgáltatást az interneten keresztül használja. Az Ön hálózatáról a Microsoft felé küldött, majd visszatérő forgalom ugyanazon az internetes kapcsolaton halad át, és egy tűzfalon is keresztülmegy. A tűzfal az első csomag észlelésekor rögzíti az adatfolyamot, és engedélyezi a visszatérő csomagokat is, mivel za adatfolyam már szerepel az állapottáblázatban.

![Aszimmetrikus útválasztás az ExpressRoute-tal](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Ezután bekapcsolja az ExpressRoute-ot, és a Microsoft-szolgáltatásokat az ExpressRoute-on keresztül veszi igénybe. A többi Microsoft-szolgáltatást az interneten keresztül használjuk fel. A peremen egy különálló tűzfalat helyez üzembe, amely csatlakozik az ExpressRoute-hoz. A Microsoft bizonyos szolgáltatásokhoz az ExpressRoute-on keresztül konkrétabb előtagokat hirdet meg a hálózaton. Az útválasztási infrastruktúra ezért ezeknél az előtagoknál az ExpressRoute elérés utat fogja előnyben részesíteni. Ha Ön nem az ExpressRoute-on keresztül hirdeti meg nyilvános IP-címeit a Microsoft számára, akkor a Microsoft az interneten keresztül fog kommunikálni ezekkel a nyilvános IP-címekkel. A hálózatából a Microsoft felé irányuló kimenő forgalom az ExpressRoute-ot fogja használni, míg a Microsofttól visszaérkező forgalom az internetet. Ha a peremen működő tűzfal az állapottáblázatban nem szereplő adatfolyamhoz tartozó válaszcsomagot észlel, eldobja a visszatérő forgalmat.

Ha ugyanazt a hálózati címfordítási (NAT) készletet használja az ExpressRoute-hoz és az internethez, hasonló problémák léphetnek fel a hálózatán belüli, privát IP-címmel rendelkező ügyfeleknél. A Windows Update-re és más hasonló szolgáltatásokra irányuló kérelmeket az interneten keresztül küldi el a hálózat, mivel ezeknek a szolgáltatásoknak az IP-címét nem az ExpressRoute-on keresztül hirdeti meg a rendszer. A visszatérő forgalom azonban az ExpressRoute-on keresztül fog érkezni. Ha a Microsoft az internettől és az ExpressRoute-tól megegyező alhálózati maszkkal rendelkező IP-címet kap, az ExpressRoute-ot részesíti előnyben. Ha a peremhálózatban működő, az ExpressRoute felé néző tűzfalnak vagy más állapot-nyilvántartó eszköznek nincs előzetes információja egy adatfolyamról, eldobja az adott adatfolyamhoz tartozó csomagokat.

## <a name="asymmetric-routing-solutions"></a>Megoldások az aszimmetrikus útválasztásra
Az aszimmetrikus útválasztás problémájának megoldásához alapvetően két lehetőség áll rendelkezésére. Az egyik az útválasztás, a másik pedig a forrásalapú hálózati címfordítás (SNAT) használata.

### <a name="routing"></a>Útválasztás
Gondoskodjon róla, hogy a nyilvános IP-címeit a megfelelő távoli hálózati (NAT) kapcsolatoknak hirdesse meg. Ha például az internetet szeretné használni a hitelesítési forgalomhoz és az ExpressRoute-ot a levelezési forgalomhoz, akkor ne hirdesse meg az Active Directory összevonási szolgáltatások (AD FS) nyilvános IP-címeit az ExpressRoute-on keresztül. Hasonlóképpen ügyeljen arra is, hogy ne tegye közzé a helyszíni AD FS-kiszolgálót olyan IP-címek számára, amelyek az útválasztó az ExpressRoute-on keresztül kap. Az ExpressRoute-on keresztül kapott útvonalak konkrétabbak, ezért a rendszer az ExpressRoute-on keresztüli elérési utat részesíti előnyben a Microsoft felé irányuló hitelesítési forgalom esetén. Ez aszimmetrikus útválasztáshoz vezet.

Ha az ExpressRoute-ot szeretné használni a hitelesítéshez, ügyeljen rá, hogy az AD FS nyilvános IP-címeit az ExpressRoute-on keresztül, NAT nélkül hirdesse meg. Ezzel a módszerrel a Microsofttól érkező, és egy helyszíni AD FS-kiszolgálóra irányuló forgalom az ExpressRoute-on halad keresztül. Az ügyféltől a Microsoft felé irányuló forgalom az ExpressRoute-ot használja, mivel az az előnyben részesített elérési út.

### <a name="source-based-nat"></a>Forrásalapú NAT
Az aszimmetrikus útválasztás által okozott problémák megoldásának másik módját az SNAT használata jelenti. Tegyük fel, hogy nem hirdette meg egy helyszíni SMTP-kiszolgáló nyilvános IP-címét az ExpressRoute-on keresztül, mert az ilyen típusú kommunikációhoz az internetet szeretné használni. A Microsoft felől érkező, a helyszíni SMTP-kiszolgáló irányába tartó kérés az interneten halad keresztül. A beérkező kérelmet az SNAT használatával egy belső IP-címre irányítja át. Az SMTP-kiszolgálótól származó, fordított irányú forgalom az ExpressRoute helyett a peremhálózati tűzfalra érkezik (mivel ezt használja az SNAT megvalósításához). A visszafelé irányuló forgalom az interneten halad keresztül.

![A forrásalapú NAT hálózati konfigurációja](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Az aszimmetrikus útválasztás észlelése
A Traceroute a legjobb módszer annak vizsgálatához, hogy a forgalom a várt útvonalat járja-e be. Ha azt várja, hogy a helyszíni SMTP-kiszolgálóról a Microsoft felé irányuló forgalom az interneten haladjon keresztül, a traceroute által megjelenített útvonalnak az SMTP-kiszolgálótól kell indulnia és az Office 365 szolgáltatásnál kell végződnie. Az eredmény alapján ellenőrizheti, hogy a forgalom valóban az internet felé, és nem az ExpressRoute irányában hagyja el a hálózatot.

