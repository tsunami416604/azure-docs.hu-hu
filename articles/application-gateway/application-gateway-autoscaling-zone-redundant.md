---
title: Automatikus skálázás és zónaredundáns Application Gateway az Azure-ban (nyilvános előzetes verzió)
description: Ez a cikk bemutatja az Azure-alkalmazás v2 szintű Termékváltozatot, automatikus skálázást és zónaredundáns szolgáltatást tartalmaz.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 95b14a0028134e522206f3595bc3b9ebf9aaf396
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548714"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Automatikus skálázás és zónaredundáns az Application Gateway (nyilvános előzetes verzió)

Az Application Gateway és a webalkalmazási tűzfal (WAF) egy új v2 szintű Termékváltozatot kínál a nagyobb teljesítmény és a kritikus fontosságú új funkciók, például a statikus virtuális IP-címek támogatása, az automatikus skálázás és a zone redudancy támogatásának hozzáadása a nyilvános előzetes verziója mostantól elérhetők. Az általánosan elérhető Termékváltozat a meglévő szolgáltatás néhány kivételtől eltekintve ismert korlátozások szakaszban felsorolt támogatott az új v2 szintű Termékváltozatot, az továbbra is. Új v2 SKU-ja a következő fejlesztéseket tartalmazza:

- **Az automatikus skálázás**: Az automatikus skálázás Termékváltozat az Application Gateway vagy WAF üzembe helyezésekre alapuló forgalmi terhelés minták módosítása vagy is méretezhetők. Az automatikus skálázással elkerülhető, hogy már a kiépítés során meg kelljen határozni az üzemelő példány méretét vagy a példányszámot. Ez a Termékváltozat valódi rugalmasságot nyújt. Az új termékváltozatban az Application Gateway rögzített kapacitás (az automatikus skálázás le van tiltva) és az automatikus skálázás engedélyezése mellett módban is működhet. Rögzített kapacitás mód hasznos forgatókönyvek egységes és kiszámítható számítási feladatokkal. Az automatikus skálázási mód akkor előnyös, amelyeket a nagy mennyiségű eltérések az alkalmazás forgalmának az alkalmazásokban.

- **A redundancia zóna**: Egy Application Gateway vagy WAF üzembe helyezés több rendelkezésre állási zónában, minden zónában egy Traffic Manager üzembe helyezése és léptetéses külön Application Gateway-példány eltávolítása is kiterjedhetnek. Egy zóna vagy több zónában, az Application Gateway-példány telepítve vannak, így biztosítása hiba zónarugalmasságot választhat. A háttérkészlet alkalmazások hasonló módon – szét lehetnek osztva a rendelkezésre állási zónák.
- **Nagyobb teljesítmény**: Az automatikus skálázás Termékváltozatot kínál legfeljebb 5 X nagyobb SSL kiszervezheti a teljesítmény az általánosan elérhető Termékváltozat képest.
- **Központi telepítési és frissítési gyorsabb** az automatikus skálázás Termékváltozat biztosít, mint a korábban megszokott Termékváltozat általánosan elérhető üzembe helyezési és frissítési gyorsabb.
- **Statikus virtuális IP-CÍMEK**: Az application gateway virtuális IP-CÍMEK mostantól támogatja a statikus VIP típusa kizárólag. Ez biztosítja, hogy az application gateway társított virtuális IP-CÍMEK újraindítás után is nem változik.

> [!IMPORTANT]
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

> [!NOTE]
> Az automatikus skálázás és zónaredundáns az application gateway mostantól támogatja-e Termékváltozat [alapértelmezett állapotadat-mintavétel](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe) automatikusan a háttér-készletben található összes erőforrás állapotának figyelésére, és jelölje ki a háttérrendszer a tagokkal szolgálnak nem megfelelő állapotú. Az alapértelmezett állapot mintavételi még a összes-e, amelyhez nem állított be egyéni mintavétel konfigurációra háttérrendszerek automatikusan megtörténik. További tudnivalókért lásd: [állapotadat-mintavételek az application gatewayben](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Termékváltozat v1 és v2 szintű Termékváltozatot közötti funkcióinak összehasonlítása

Az alábbi táblázat az egyes Termékváltozat szolgáltatásait hasonlítja össze.

|                                                   | V1 Termékváltozatot   | v2 szintű Termékváltozatot   |
| ------------------------------------------------- | -------- | -------- |
| Automatikus skálázás                                       |          | &#x2713; |
| A Zone redudancy                                   |          | &#x2713; |
| &nbsp;Statikus virtuális IP-cím&nbsp;&nbsp;                      |          | &#x2713; |
| URL-alapú útválasztás                                 | &#x2713; | &#x2713; |
| Több hely üzemeltetése                             | &#x2713; | &#x2713; |
| Forgalom átirányítása                               | &#x2713; | &#x2713; |
| Webalkalmazási tűzfal (WAF)                    | &#x2713; | &#x2713; |
| Secure Sockets Layer- (SSL-) lezárás            | &#x2713; | &#x2713; |
| Végpontok közötti SSL-titkosítás                         | &#x2713; | &#x2713; |
| Munkamenet-affinitás                                  | &#x2713; | &#x2713; |
| Egyéni hibalapok                                | &#x2713; | &#x2713; |
| HTTP (S) fejlécek újraírása                           |          | &#x2713; |
| WebSocket támogatás                                 | &#x2713; | &#x2713; |
| HTTP/2-támogatás                                    | &#x2713; | &#x2713; |
| Kapcsolatkiürítés                               | &#x2713; | &#x2713; |
| Az Azure Kubernetes Service (AKS) Bejövőforgalom-vezérlőt |          | &#x2713; |

## <a name="supported-regions"></a>Támogatott régiók

Az automatikus skálázás Termékváltozat érhető el ezekben a régiókban: USA északi középső RÉGIÓJA, USA déli középső RÉGIÓJA, USA nyugati RÉGIÓJA, USA nyugati RÉGIÓJA 2, USA keleti RÉGIÓJA, USA keleti RÉGIÓJA 2, USA középső RÉGIÓJA, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Franciaország, közép-India, Egyesült Királyság nyugati, kelet-japán, Nyugat-japán.

## <a name="pricing"></a>Díjszabás

Az előzetes időszakban nem jár költségekkel. Fizetnie kell application Gateway átjáróhoz is a Key Vault, a virtuális gépek, például naplóátvitelen kívüli egyéb erőforrásokra, és így tovább.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

|Probléma|Részletek|
|--|--|
|Hitelesítési tanúsítvány|Nem támogatott.<br>További információkért lásd: [az Application Gateway teljes körű SSL áttekintése](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Standard_v2 és a Standard Application Gateway keverve ugyanazon az alhálózaton|Nem támogatott|
|Felhasználó által megadott útvonal (UDR) az Application Gateway-alhálózat|Nem támogatott|
|NSG bejövő port-tartomány| -65200 – 65535 Standard_v2 Termékváltozat<br>-65503 65534 Standard Termékváltozat esetében.<br>További információkért lásd: a [– gyakori kérdések](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Teljesítmény az Azure-beli diagnosztikai naplók|Nem támogatott.<br>Az Azure-metrikák kell használni.|
|Számlázás|Nincs, jelenleg nincs számlázási.|
|FIPS-módban|Ezek jelenleg nem támogatottak.|
|ILB mód|Ez jelenleg nem támogatott. Nyilvános és ILB mód együtt használható.|
|Netwatcher integráció|A nyilvános előzetes verzióban nem támogatott.|

## <a name="next-steps"></a>További lépések
- [Az automatikus skálázás, a zóna redundáns az application gateway létrehozása foglalt virtuális IP-címmel rendelkező Azure PowerShell-lel](tutorial-autoscale-ps.md)
- Tudjon meg többet [Application Gateway](overview.md).
- Tudjon meg többet [Azure tűzfal](../firewall/overview.md).
