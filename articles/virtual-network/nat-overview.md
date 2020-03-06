---
title: Mi az Azure Virtual Network NAT?
description: A Virtual Network NAT funkcióinak, erőforrásainak, architektúrájának és megvalósításának áttekintése. Megtudhatja, hogyan működik Virtual Network NAT és hogyan használható a NAT Gateway-erőforrások a felhőben.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about Virtual Network NAT, its NAT gateway resources, and what I can use them for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: allensu
ms.openlocfilehash: 205826a6ad952383582f5a8086cbd8b85dbc3794
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359259"
---
# <a name="what-is-virtual-network-nat-public-preview"></a>Mi az Virtual Network NAT (nyilvános előzetes verzió)?

Virtual Network NAT (hálózati címfordítás) egyszerűbbé teszi a csak kimenő internetkapcsolatot a virtuális hálózatokhoz. Ha egy alhálózaton van konfigurálva, minden kimenő kapcsolat a megadott statikus nyilvános IP-címeket használja.  A kimenő kapcsolat a virtuális gépekhez közvetlenül csatlakoztatott terheléselosztó vagy nyilvános IP-címek nélkül is lehetséges. A NAT teljes körűen felügyelt és rugalmas.

<!-- 
<img src="./media/nat-overview/flow-map.svg" width="270" align="center">
![Virtual Network NAT](./media/nat-overview/flow-map.svg)
-->

<p align="center">
  <img src="./media/nat-overview/flow-map.svg" width="256" title="Virtual Network NAT">
</p>



*Ábra: Virtual Network NAT*


>[!NOTE] 
>Virtual Network NAT jelenleg nyilvános előzetes verzióként érhető el. Jelenleg csak korlátozott számú [régióban](#region-availability)érhető el. Ez az előzetes verzió szolgáltatási szintű szerződés nélkül érhető el, ezért nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="static-ip-addresses-for-outbound-only"></a>Statikus IP-címek csak kimenőként

A kimenő kapcsolatok a NAT-vel rendelkező minden egyes alhálózat esetében meghatározhatók.  Ugyanazon a virtuális hálózaton belül több alhálózat is rendelkezhet különböző NAT-hálózatokkal. Az alhálózat konfigurálásához meg kell adni a [NAT-átjáró által használt erőforrást](./nat-gateway-resource.md) . Bármely virtuálisgép-példány UDP-és TCP-kimenő forgalma a NAT protokollt fogja használni. 

A NAT kompatibilis a szabványos SKU [nyilvános IP-címek erőforrásaival](./virtual-network-ip-addresses-overview-arm.md#standard) vagy a [nyilvános IP-előtag erőforrásaival](./public-ip-address-prefix.md) vagy a kettő kombinációjával.  A nyilvános IP-előtagot közvetlenül is használhatja, vagy eloszthatja az előtag nyilvános IP-címeit több NAT Gateway-erőforrás között. A NAT a teljes adatforgalmat az előtag IP-címeinek tartományához irányítja.  Az üzemelő példányok IP-engedélyezési listája már könnyen elérhető.

Az alhálózat összes kimenő forgalmát a NAT automatikusan dolgozza fel az ügyfél-konfiguráció nélkül.  A felhasználó által megadott útvonalak nem szükségesek. A NAT elsőbbséget élvez más [kimenő forgatókönyvekkel](../load-balancer/load-balancer-outbound-connections.md) szemben, és lecseréli egy alhálózat alapértelmezett internetes célját.

## <a name="on-demand-snat-with-multiple-ip-addresses-for-scale"></a>Igény szerinti SNAT több IP-címmel a skálázáshoz

A NAT a "port hálózati címfordítást" (PNAT vagy PAT) használja, és a legtöbb munkaterheléshez ajánlott. A dinamikus vagy eltérő munkaterhelések könnyen elhelyezhetők az igény szerinti kimenő forgalom lefoglalásával. A rendszer elkerüli a részletes előzetes tervezést, előzetes kiosztást, és végül a kimenő erőforrások túlzott kiépítését. A SNAT-portok erőforrásai megosztva és minden alhálózaton elérhetők egy adott NAT Gateway-erőforrás használatával, és szükség esetén rendelkezésre állnak.

A NAT-hoz csatlakoztatott nyilvános IP-címek akár 64 000 egyidejű folyamatot biztosítanak az UDP és a TCP számára. Elkezdheti egyetlen IP-cím megadását, és akár 16 nyilvános IP-címet is felméretezhető.

A NAT lehetővé teszi, hogy a folyamatok a virtuális hálózatról az internetre jöjjenek létre. Az internetről érkező adatforgalom csak aktív folyamatra adott válaszként engedélyezett.

A terheléselosztó kimenő SNAT eltérően a NAT nem rendelkezik korlátozásokkal, amelyek esetében a virtuálisgép-példányok magánhálózati IP-címe kimenő kapcsolatokat hajthat végre.  A másodlagos IP-konfigurációk kimenő internetkapcsolatot hozhatnak létre a NAT használatával.

## <a name="coexistence-of-inbound-and-outbound"></a>A bejövő és a kimenő együttes létezése

A NAT a következő szabványos SKU-erőforrásokkal kompatibilis:

- [Load Balancer](../load-balancer/load-balancer-overview.md)
- [Nyilvános IP-cím](../virtual-network/virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)
- [Nyilvános IP-előtag](../virtual-network/public-ip-address-prefix.md)

NAT-vel együtt használva ezek az erőforrások bejövő internetkapcsolatot biztosítanak az alhálózat (k) számára. A NAT biztosítja az alhálózat (k) összes kimenő internetkapcsolatát.

A NAT és a kompatibilis szabványos SKU-funkciók tisztában vannak a folyamat elindításának irányával. A bejövő és a kimenő forgatókönyvek is létezhetnek. Ezek a forgatókönyvek megkapják a hálózati címfordítás helyes fordítását, mivel ezek a funkciók tisztában vannak a folyamat irányával. 

<!-- 
<img src="./media/nat-overview/flow-direction4.svg" width="500" align="center">
![Virtual Network NAT flow direction](./media/nat-overview/flow-direction4.svg)
-->
<p align="center">
  <img src="./media/nat-overview/flow-direction4.svg" width="512" title="Virtual Network NAT-folyamat iránya">
</p>

*Ábra: Virtual Network NAT flow iránya*

## <a name="fully-managed-highly-resilient"></a>Teljes körűen felügyelt, nagyon rugalmas

A NAT teljes mértékben ki van bővítve az elejétől. Nincs szükség Felskálázási vagy kibővített műveletre.  Az Azure kezeli a NAT működését.  A NAT-nak mindig több tartalék tartománya van, és a szolgáltatás kimaradása nélkül több hibát is képes fenntartani.

## <a name="tcp-reset-for-unrecognized-flows"></a>Ismeretlen folyamatok TCP-visszaállítása

A NAT privát oldala TCP-átállítási csomagokat küld a nem létező TCP-kapcsolatban lévő kommunikációra tett kísérletekhez. Az egyik példa olyan kapcsolat, amely elérte az üresjárati időkorlátot. A következő fogadott csomag visszaállítja a TCP-visszaállítást a magánhálózati IP-címekre, hogy jelezze és kényszerítse a kapcsolat bezárását.

A NAT nyilvános oldala nem hoz létre TCP-visszaállítási csomagokat vagy bármilyen más forgalmat.  Csak az ügyfél virtuális hálózata által előállított forgalom van kibocsátva.

## <a name="configurable-idle-timeout"></a>Konfigurálható Üresjárati időkorlát

A rendszer a 4 perces alapértelmezett üresjárati időkorlátot használja, és akár 120 percre is növelhető. A folyamat bármely tevékenysége visszaállíthatja az üresjárati időzítőt, beleértve a TCP-Keepalives is.

## <a name="regional-or-zone-isolation-with-availability-zones"></a>Területi vagy zónák elkülönítése rendelkezésre állási zónákkal

A NAT alapértelmezés szerint regionális. A [rendelkezésre állási zónák](../availability-zones/az-overview.md) létrehozásakor a NAT el lehet különíteni egy adott zónában (a zóna központi telepítése).

<!-- 
<img src="./media/nat-overview/az-directions.svg" width="500" align="center">
![Virtual Network NAT with availability zones](./media/nat-overview/az-directions.svg)
-->

<p align="center">
  <img src="./media/nat-overview/az-directions.svg" width="512" title="NAT Virtual Network rendelkezésre állási zónákkal">
</p>

*Ábra: Virtual Network NAT és rendelkezésre állási zónák*

## <a name="multi-dimensional-metrics-for-observability"></a>Többdimenziós mérőszámok a megfigyeléshez

A NAT működését a Azure Monitorban elérhető többdimenziós metrikák használatával figyelheti. Ezek a metrikák a használat és a hibaelhárítás megtartására használhatók.  A NAT-átjáró erőforrásai a következő metrikákat teszik elérhetővé:
- Bájt
- Csomagok
- Eldobott csomagok
- SNAT-kapcsolatok összesen
- SNAT-kapcsolatok állapotának átállítása időszakonként.

<!-- "ADD when PM is done" Learn more about [NAT gateway metrics](./nat-gateway-metrics.md) -->

## <a name="sla"></a>SLA

Általánosan elérhető a NAT-adatelérési út legalább 99,9%-os rendelkezésre állású.

## <a name = "region-availability"></a>Régió elérhetősége

A NAT jelenleg a következő régiókban érhető el:

- Nyugat-Európa
- Kelet-Japán
- USA 2. keleti régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- USA nyugati középső régiója

## <a name = "enable-preview"></a>Nyilvános előzetes részvétel

Az előfizetéseket regisztrálni kell a nyilvános előzetes verzióban való részvétel engedélyezéséhez.  A részvételhez két lépésből álló folyamatra van szükség, és az Azure CLI és a Azure PowerShell esetében az alábbi utasításokat kell megadnia.  Az aktiválás végrehajtása több percet is igénybe vehet.

### <a name="azure-cli"></a>Azure CLI

1. Nyilvános előzetes verzióra vonatkozó előfizetés regisztrálása

    ```azurecli-interactive
      az feature register --namespace Microsoft.Network --name AllowNatGateway
    ```

2. regisztráció aktiválása

    ```azurecli-interactive
      az provider register --namespace Microsoft.Network
    ```

### <a name="azure-powershell"></a>Azure PowerShell

1. Nyilvános előzetes verzióra vonatkozó előfizetés regisztrálása

    ```azurepowershell-interactive
      Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowNatGateway
    ```

2. regisztráció aktiválása

    ```azurepowershell-interactive
      Register-AzResourceProvider -ProviderNamespace Microsoft.Network
    ```

## <a name="pricing"></a>Díjszabás

A NAT-átjáró számlázása két külön mérőszámmal történik:

| Mérő | Rate (Egységár) |
| --- | --- |
| Erőforrás-órák | $0.045/óra |
| Feldolgozott adatfeldolgozás | $0.045/GB |

Az erőforrás-órák a NAT-átjáró erőforrásának időtartama alatt.
Az adatok feldolgozott fiókjai a NAT-átjáró erőforrásai által feldolgozott összes forgalomhoz.

A nyilvános előzetes verzióban a díjszabás 50%-kal diszkontálva érhető el.

## <a name="support"></a>Támogatás

A NAT a normál támogatási csatornákon keresztül támogatott.

## <a name="feedback"></a>Visszajelzés

Szeretnénk tudni, hogyan lehet javítani a szolgáltatást. Ossza meg velünk [véleményét a nyilvános előzetes](https://aka.ms/natfeedback) verzióban.  Azt is javasolhatja, hogy mit érdemes a következő címen létrehozni a [NAT-UserVoice](https://aka.ms/natuservoice).

## <a name="limitations"></a>Korlátozások

* A NAT kompatibilis a standard SKU nyilvános IP-címmel, a nyilvános IP-előtaggal és a terheléselosztó erőforrásaival.   Az alapszintű erőforrások (például az alapszintű Load Balancer) és a belőlük származtatott termékek nem kompatibilisek a NAT-val.  Az alapszintű erőforrásokat a NAT-mel nem konfigurált alhálózatra kell helyezni.
* Az IPv4-címek családja támogatott.  A NAT nem támogatja az IPv6-cím családját.  A NAT nem helyezhető üzembe IPv6-előtaggal rendelkező alhálózaton.
* A NSG folyamat naplózása nem támogatott a NAT használata esetén.
* A NAT nem tud több virtuális hálózatot kifogni.

## <a name="next-steps"></a>Következő lépések

* További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
* [Ossza meg velünk a következőt Virtual Network NAT UserVoice-ben való létrehozásához](https://aka.ms/natuservoice).
* [Visszajelzés küldése a nyilvános előzetes](https://aka.ms/natfeedback)verzióról.
