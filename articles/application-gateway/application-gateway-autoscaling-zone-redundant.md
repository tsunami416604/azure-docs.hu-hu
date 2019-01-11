---
title: Automatikus skálázás és zónaredundáns Application Gateway az Azure-ban (nyilvános előzetes verzió)
description: Ez a cikk információkat tartalmaz, a webes alkalmazás tűzfal kérelem méretbeli korlátokat és kizárási listák az Application Gateway az Azure portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/10/2019
ms.author: victorh
ms.openlocfilehash: f5885fd2ac76550990c9a56a1d200bbe11555918
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213756"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Automatikus skálázás és zónaredundáns az Application Gateway (nyilvános előzetes verzió)

Az Application Gateway és a webalkalmazási tűzfal (WAF) egy új v2 szintű Termékváltozatot kínál a nagyobb teljesítmény és a kritikus fontosságú új funkciók, például a statikus virtuális IP-címek támogatása, az automatikus skálázás és a zone redudancy támogatásának hozzáadása a nyilvános előzetes verziója mostantól elérhetők. Az általánosan elérhető Termékváltozat a meglévő szolgáltatás néhány kivételtől eltekintve ismert korlátozások szakaszban felsorolt támogatott az új v2 szintű Termékváltozatot, az továbbra is. Új v2 SKU-ja a következő fejlesztéseket tartalmazza:

- **Az automatikus skálázás**: Az automatikus skálázás Termékváltozat az Application Gateway vagy WAF üzembe helyezésekre alapuló forgalmi terhelés minták módosítása vagy is méretezhetők. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. Ezért a Termékváltozatot kínál valódi rugalmassága. Az új termékváltozatban az Application Gateway rögzített kapacitás (az automatikus skálázás le van tiltva) és az automatikus skálázás engedélyezése mellett módban is működhet. Rögzített kapacitás mód hasznos forgatókönyvek egységes és kiszámítható számítási feladatokkal. Az automatikus skálázási mód akkor előnyös, amelyeket a nagy mennyiségű eltérések az alkalmazás forgalmának az alkalmazásokban.
   
   > [!NOTE]
   > Az automatikus skálázás jelenleg nem áll rendelkezésre a WAF Termékváltozat. WAF konfigurálása rögzített kapacitás üzemmódot, az automatikus skálázási mód helyett.
- **A redundancia zóna**: Egy Application Gateway vagy WAF üzembe helyezés több rendelkezésre állási zónában, minden zónában egy Traffic Manager üzembe helyezése és léptetéses külön Application Gateway-példány eltávolítása is kiterjedhetnek. Egy zóna vagy több zónában, az Application Gateway-példány telepítve vannak, így biztosítása hiba zónarugalmasságot választhat. A háttérkészlet alkalmazások hasonló módon – szét lehetnek osztva a rendelkezésre állási zónák.
- **Nagyobb teljesítmény**: Az automatikus skálázás Termékváltozatot kínál legfeljebb 5 X nagyobb SSL kiszervezheti a teljesítmény az általánosan elérhető Termékváltozat képest.
- **Központi telepítési és frissítési gyorsabb** az automatikus skálázás Termékváltozat biztosít, mint a korábban megszokott Termékváltozat általánosan elérhető üzembe helyezési és frissítési gyorsabb.
- **Statikus virtuális IP-CÍMEK**: Az application gateway virtuális IP-CÍMEK mostantól támogatja a statikus VIP típusa kizárólag. Így biztosítható, hogy az Application Gatewayhez társított virtuális IP-cím újraindítást követően se módosuljon.

> [!IMPORTANT]
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Támogatott régiók
Az automatikus skálázás Termékváltozat az USA keleti RÉGIÓJA 2, USA középső régiója, USA 2. nyugati régiója, USA északi középső Régiója, USA nyugati RÉGIÓJA, USA déli középső Régiója, közép-Franciaország, Nyugat-Európa, Észak-Európa, Egyesült Királyság nyugati régiója, Délkelet-Ázsia és kelet-Japánban érhető el.

## <a name="pricing"></a>Díjszabás
Az előzetes időszakban nem jár költségekkel. Alkalmazásátjáró, például a Key Vault, a virtuális gépeket, és így tovább naplóátvitelen kívüli egyéb erőforrásokra számlázzuk ki. 

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

|Probléma|Részletek|
|--|--|
|Hitelesítési tanúsítvány|Nem támogatott.<br>További információkért lásd: [az Application Gateway teljes körű SSL áttekintése](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Standard_v2 és a Standard Application Gateway keverve ugyanazon az alhálózaton|Nem támogatott.<br>Ezenkívül az automatikus skálázás engedélyezve van, egy alhálózatot csak egy alkalmazás-átjáróval rendelkezhet.|
|Felhasználó által megadott útvonal (UDR) az Application Gateway-alhálózat|Nem támogatott|
|NSG bejövő port-tartomány| -65200 – 65535 Standard_v2 Termékváltozat<br>-65503 65534 Standard Termékváltozat esetében.<br>További információkért lásd: a [– gyakori kérdések](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Teljesítmény az Azure-beli diagnosztikai naplók|Nem támogatott.<br>Az Azure-metrikák kell használni.|
|Számlázás|Nincs, jelenleg nincs számlázási.|
|FIPS-módban, WebSocket|Ezek jelenleg nem támogatottak.|
|ILB mód|Ez jelenleg nem támogatott. Nyilvános és ILB mód együtt használható.|
|Web application firewall automatikus skálázás|WAF nem támogatja az automatikus skálázási mód. Rögzített kapacitás módban támogatott.|

## <a name="next-steps"></a>További lépések
- [Az automatikus skálázás, a zóna redundáns az application gateway létrehozása foglalt virtuális IP-címmel rendelkező Azure PowerShell-lel](tutorial-autoscale-ps.md)
- Tudjon meg többet [Application Gateway](overview.md).
- Tudjon meg többet [Azure tűzfal](../firewall/overview.md). 

