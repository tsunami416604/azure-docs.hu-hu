---
title: Virtuális hálózatok tervezése NAT Gateway-erőforrásokkal
titleSuffix: Azure Virtual Network NAT
description: Ismerje meg, hogyan tervezhet virtuális hálózatokat NAT Gateway-erőforrásokkal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to learn more about how to design virtual networks with NAT gateway resources.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: allensu
ms.openlocfilehash: 3cc459b7f4b81b14f57bbb702f0b0d988654189f
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298654"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Virtuális hálózatok tervezése NAT Gateway-erőforrásokkal

A NAT-átjáró erőforrásai [Virtual Network NAT](nat-overview.md) részét képezik, és kimenő internetkapcsolatot biztosítanak a virtuális hálózatok egy vagy több alhálózatához. Annak a virtuális hálózatnak az alhálózata, amelyet a NAT-átjáró használni fog. A NAT a forrás hálózati címfordítást (SNAT) biztosítja egy alhálózat számára.  A NAT-átjáró erőforrásai határozzák meg, hogy a virtuális gépek mely statikus IP-címeket használják a kimenő folyamatok létrehozásakor. A statikus IP-címek a nyilvános IP-címek erőforrásaiból, a nyilvános IP-előtag erőforrásaiból vagy mindkettőből származnak. A NAT-átjáró erőforrásai akár 16 statikus IP-címet is használhatnak.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network az internet felé">
</p>

*Ábra: Virtual Network NAT a kimenő internethez*

## <a name="how-to-deploy-nat"></a>NAT üzembe helyezése

A NAT-átjáró konfigurálása és használata szándékosan egyszerű:  

NAT-átjáró erőforrása:
- Hozzon létre regionális vagy zóna (elkülönített) NAT Gateway-erőforrást,
- IP-címek kiosztása,
- Üresjárat időkorlátjának módosítása (nem kötelező).

Virtuális hálózat:
- Konfigurálja a virtuális hálózati alhálózatot NAT-átjáró használatára.

A felhasználó által megadott útvonalak nem szükségesek.

## <a name="resource"></a>Resource

Az erőforrás úgy lett kialakítva, hogy egyszerű legyen, mint az alábbi Azure Resource Manager példa a sablonhoz hasonló formátumban.  Ez a sablon-szerű formátum itt látható, hogy bemutassa a fogalmakat és a struktúrát.  Módosítsa a példát az igényeinek megfelelően.  Ez a dokumentum nem az oktatóanyaghoz készült.

A következő ábrán a különböző Azure Resource Manager erőforrások közötti írható hivatkozások láthatók.  A nyíl a hivatkozás irányát jelzi, amelyből az írható. Felülvizsgálat 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="NAT-objektummodell Virtual Network">
</p>

*Ábra: Virtual Network NAT-objektummodell*

A legtöbb számítási feladathoz a NAT használata ajánlott, kivéve, ha a [készleten alapuló Load Balancer kimenő kapcsolathoz](../load-balancer/load-balancer-outbound-connections.md)adott függősége van.  

Áttelepítheti a standard Load Balancer-forgatókönyveket, beleértve a [kimenő szabályokat](../load-balancer/load-balancer-outbound-rules-overview.md)is a NAT-átjáróra. A Migrálás érdekében helyezze át a nyilvános IP-címet és a nyilvános IP-előtag erőforrásait a terheléselosztó felületéről a NAT-átjáróra. A NAT-átjáró új IP-címei nem szükségesek. A standard nyilvános IP-cím és az előtag mindaddig felhasználható, amíg az összeg nem haladja meg a 16 IP-címet. Tervezze meg a szolgáltatás-megszakítással való áttelepítést az áttérés során.  A folyamat automatizálásával csökkentheti a megszakítást. Először tesztelje az áttelepítést átmeneti környezetben.  Az áttérés során a bejövő folyamatokat nem érinti a rendszer.

A következő példa egy _myNATGateway_ nevű NAT Gateway-erőforrást hoz létre az _USA 2. keleti régiójában, az 1_ és _4 perces_ Üresjárati időkorlát használatával. A megadott kimenő IP-címek a következők:
- A nyilvános IP-cím erőforrásainak _myIP1_ és _myIP2_ , valamint 
- Nyilvános IP-előtag-erőforrások készlete _myPrefix1_ és _myPrefix2_. 

A négy IP-cím-erőforrás által megadott IP-címek teljes száma nem haladhatja meg az összes 16 IP-címet. Az 1 és 16 közötti IP-címek száma engedélyezett.

```json
{
"name": "myNATGateway",
   "type": "Microsoft.Network/natGateways",
   "apiVersion": "2018-11-01",
   "location": "East US 2",
   "sku": { "name": "Standard" },
   "zones": [ "1" ],
   "properties": {
      "idleTimeoutInMinutes": 4, 
      "publicIPPrefixes": [
         {
            "id": "ref to myPrefix1"
         },
         {
            "id": "ref to myPrefix2"
         }
      ],
      "publicIPAddresses": [
         {
            "id": "ref to myIP1"
         },
         {
            "id": "ref to myIP2"
         }
      ]
   }
}
```

A NAT-átjáró erőforrásának létrehozásakor a virtuális hálózat egy vagy több alhálózatán is használható. Itt adhatja meg, hogy mely alhálózatok használják ezt a NAT Gateway-erőforrást. A NAT-átjárók nem tudnak több virtuális hálózatot kiterjedni. Nem kell ugyanazt a NAT-átjárót hozzárendelni a virtuális hálózat összes alhálózatához. Az egyes alhálózatokat különböző NAT Gateway-erőforrásokkal lehet konfigurálni.

A rendelkezésre állási zónákat nem használó forgatókönyvek regionálisak lesznek (nincs megadva zóna). Ha rendelkezésre állási zónákat használ, megadhat egy zónát, amely elkülöníti a NAT-t egy adott zónához. A Zone-redundancia nem támogatott. Tekintse át a NAT [rendelkezésre állási zónáit](#availability-zones).


```json
{
   "name": "myVNet",
   "apiVersion": "2018-11-01",
   "type": "Microsoft.Network/virtualNetworks",
   "location": "myRegion", 
   "properties": {
      "addressSpace": {
          "addressPrefixes": [
           "192.168.0.0/16"
          ]
      },
      "subnets": [
         {
            "name": "mySubnet1",
            "properties": {
               "addressPrefix": "192.168.0.0/24",
               "natGateway": {
                  "id": "ref to myNATGateway"
               }
            }
         } 
      ]
   }
}
```
A NAT-átjárók egy, a virtuális hálózaton belüli alhálózaton lévő tulajdonsággal vannak definiálva. A virtuális hálózati _myVNet_ alhálózati _mySubnet1_ virtuális gépek által létrehozott folyamatok a NAT-átjárót fogják használni. Minden kimenő kapcsolat a _myNatGateway_ társított IP-címeket fogja használni a forrás IP-címként.


## <a name="design-guidance"></a>Tervezési útmutató

Tekintse át ezt a szakaszt, és ismerkedjen meg a virtuális hálózatok NAT-beli kialakításával kapcsolatos szempontokkal.  

1. [Cost Optimization](#cost-optimization)
1. [A bejövő és a kimenő együttes létezése](#coexistence-of-inbound-and-outbound)
2. [Alapszintű erőforrások kezelése](#managing-basic-resources)
3. [Availability Zones](#availability-zones)

### <a name="cost-optimization"></a>Költségoptimalizálás

A [szolgáltatási végpontok](virtual-network-service-endpoints-overview.md) és a [magánjellegű hivatkozások](../private-link/private-link-overview.md) a költségeket optimalizáló beállítások. Ezekhez a szolgáltatásokhoz nincs szükség NAT-ra. A virtuális hálózat NAT-kapcsolata nem dolgozza fel a szolgáltatási végpontokra vagy privát hivatkozásokra irányuló forgalmat.  

A szolgáltatás-végpontok az Azure-szolgáltatások erőforrásainak a virtuális hálózatra való összekötését és az Azure-szolgáltatás erőforrásaihoz való hozzáférést vezérlik. Ha például az Azure Storage-hoz fér hozzá, a tároláshoz használjon szolgáltatási végpontot, hogy elkerülje az adatok feldolgozott NAT-díját. A szolgáltatási végpontok ingyenesek.

A privát hivatkozás a virtuális hálózaton belüli privát végpontként teszi elérhetővé az Azure Pásti szolgáltatást (vagy a privát hivatkozással üzemeltetett egyéb szolgáltatásokat).  A privát hivatkozás számlázása az időtartam és a feldolgozott adatmennyiség alapján történik.

Értékelje ki, hogy az egyik vagy mindkét megközelítés jól illeszkedik-e a forgatókönyvhöz, és igény szerint használja-e.

### <a name="coexistence-of-inbound-and-outbound"></a>A bejövő és a kimenő együttes létezése

A NAT-átjáró kompatibilis a rel:

 - Standard Load Balancer
 - Standard nyilvános IP-cím
 - Szabványos nyilvános IP-előtag

Új üzemelő példány fejlesztésekor a standard SKU-val kell kezdeni.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="NAT Virtual Network az internet felé">
</p>

*Ábra: Virtual Network NAT a kimenő internethez*

A NAT-átjáró által biztosított internetes kimenő forgatókönyvek kiterjeszthetők az internetről érkező funkciókkal. Minden erőforrás ismeri a folyamat eredetének irányát. NAT-átjárót tartalmazó alhálózaton a NAT-átjáró felülírja az összes kimenő – internetes forgatókönyvet. Az internetes forgatókönyvek bejövő adatait a megfelelő erőforrás biztosíthatja.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT és virtuális gép példány szintű nyilvános IP-címmel

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel">
</p>

*Ábra: Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel*

| Irány | Resource |
|:---:|:---:|
| Bejövő | Példány szintű nyilvános IP-címmel rendelkező virtuális gép |
| Kimenő | NAT-átjáró |

A virtuális gép a NAT-átjárót fogja használni a kimenő forgalomhoz.  A bejövő származik nem érinti.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT és virtuális gép nyilvános Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Virtual Network NAT és virtuális gép nyilvános Load Balancer">
</p>

*Ábra: Virtual Network NAT és virtuális gép nyilvános Load Balancer*

| Irány | Resource |
|:---:|:---:|
| Bejövő | nyilvános Load Balancer |
| Kimenő | NAT-átjáró |

Egy terheléselosztási szabály vagy kimenő szabályok kimenő konfigurációját a NAT-átjáró váltja fel.  A bejövő származik nem érinti.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT és virtuális gép példány-szintű nyilvános IP-címmel és nyilvános Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel és nyilvános Load Balancer">
</p>

*Ábra: Virtual Network NAT és virtuális gép példány szintű nyilvános IP-címmel és nyilvános Load Balancer*

| Irány | Resource |
|:---:|:---:|
| Bejövő | Példány szintű nyilvános IP-címmel rendelkező virtuális gép és nyilvános Load Balancer |
| Kimenő | NAT-átjáró |

Egy terheléselosztási szabály vagy kimenő szabályok kimenő konfigurációját a NAT-átjáró váltja fel.  A virtuális gép a NAT-átjárót is használja a kimenő rendszerhez.  A bejövő származik nem érinti.

### <a name="managing-basic-resources"></a>Alapszintű erőforrások kezelése

A standard Load Balancer, a nyilvános IP-cím és a nyilvános IP-előtag kompatibilis a NAT-átjáróval. A NAT-átjárók egy alhálózat hatókörében működnek. Ezeknek a szolgáltatásoknak az alapszintű SKU-t NAT-átjáró nélküli alhálózatra kell telepíteni. Ez az elkülönítés lehetővé teszi, hogy mindkét SKU-változat egyidejűleg ugyanazzal a virtuális hálózattal is létezhet.

A NAT-átjárók elsőbbséget élveznek az alhálózat kimenő eseteivel szemben. Az alapszintű terheléselosztó vagy a nyilvános IP-cím (és a velük létrehozott felügyelt szolgáltatások) nem állíthatók be a megfelelő fordításokkal. A NAT-átjáró átirányítja a kimenő forgalmat egy alhálózat internetes forgalmára. Az alapszintű Load Balancer és a nyilvános IP-cím bejövő forgalma nem érhető el. Egy alapszintű terheléselosztó és egy virtuális gépen konfigurált nyilvános IP-cím bejövő forgalma nem lesz elérhető.

### <a name="availability-zones"></a>Rendelkezésre állási zónák

A NAT a rendelkezésre állási zónák nélkül is rugalmas, és képes túlélni több infrastruktúra-összetevő meghibásodását. Ha a rendelkezésre állási zónák a forgatókönyv részét képezik, akkor egy adott zónához kell konfigurálnia a NAT-t.  A vezérlési sík műveletei és az adatsíkok a megadott zónára vannak korlátozva. Hiba történt egy olyan zónában, ahol a forgatókönyv nem lesz hatással a NAT-ra. Az ugyanabban a zónában lévő virtuális gépek kimenő forgalma a zónák elkülönítése miatt sikertelen lesz.

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="NAT Virtual Network rendelkezésre állási zónákkal">
</p>

*Ábra: Virtual Network NAT és rendelkezésre állási zónák*

A Zone-elkülönített NAT-átjárók IP-címeket igényelnek a NAT-átjáró zónájának megfelelően. A NAT-átjáró erőforrásai eltérő zónából származó IP-címekkel vagy nem támogatottak.

A virtuális hálózatok és az alhálózatok regionális és nem egymásra igazítottak. A virtuális gépnek ugyanabban a zónában kell lennie, mint a NAT-átjárónak a kimenő kapcsolatok zónákhoz való fogadásához. A zónák elkülönítése a rendelkezésre állási zóna egy "verem" létrehozásával hozható létre. Egy zónákra vonatkozó ígéret nem létezik, ha egy zónabeli NAT-átjáró zónáit keresztezi, vagy egy regionális NAT-átjárót használ a zónákhoz tartozó virtuális gépekkel.

Ha virtuálisgép-méretezési csoportokat telepít a NAT-hoz, akkor a saját alhálózatán üzembe kell helyeznie egy zóna-méretezési csoportot, és csatlakoztatnia kell a megfelelő zóna NAT-átjárót az adott alhálózathoz. Ha zóna-átívelő méretezési csoportokat használ (két vagy több zónában a méretezési csoport), a NAT nem ad meg zónákra vonatkozó ígéretet.  A NAT nem támogatja a Zone-redundanciát.  Csak regionális vagy zóna-elkülönítés támogatott.

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="zóna – átívelő Virtual Network NAT">
</p>

*Ábra: zóna – átívelő Virtual Network NAT*

A zónák tulajdonság nem változtatható.  Telepítse újra a NAT-átjáró erőforrását a kívánt területi vagy zóna-beállítással.

>[!NOTE] 
>Önmagukban az IP-címek nem redundánsak, ha nincs megadva zóna.  Egy [standard Load Balancer előtér-zónája redundáns](../load-balancer/load-balancer-standard-availability-zones.md#frontend) , ha egy IP-cím nincs létrehozva egy adott zónában.  Ez nem vonatkozik a NAT-ra.  Csak regionális vagy zóna-elkülönítés támogatott.

## <a name="source-network-address-translation"></a>Forrás hálózati címfordítás

A forrás hálózati címfordítás (SNAT) egy másik IP-címről származó folyamat forrását írja le.  A NAT-átjáró erőforrásai a SNAT gyakran hivatkoznak a port címfordítás (PAT) használatára. A PAT újraírja a forrás és a forrás portját. A SNAT nem rendelkezik rögzített kapcsolattal a privát címek száma és a lefordított nyilvános címek között.  

### <a name="fundamentals"></a>Alapok

Lássunk egy példát négy folyamatra, hogy megismertesse az alapvető koncepciót.  A NAT-átjáró a nyilvános IP-cím erőforrás-65.52.0.2 használja.

| Folyamat | Forrás rekord | Cél rekord |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

A folyamat a PAT megtörténte után is így néz ki:

| Folyamat | Forrás rekord | SNAT'ed-forrás rekordja | Cél rekord | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

A cél a folyamat forrását fogja látni a 65.52.0.2 (SNAT-forrás rekord), amely a hozzárendelt portot mutatja.  A PAT, ahogy az előző táblázatban is látható, a port maszkolása SNAT is nevezik.  A több privát forrás egy IP-cím és egy port mögött van.

Ne vegyen fel függőséget az adott forrásoldali portok hozzárendelésekor.  Az előző az alapvető koncepció szemléltetése.

A NAT által biztosított SNAT számos szempontból eltér a [Load Balancer](../load-balancer/load-balancer-outbound-connections.md) .

### <a name="on-demand"></a>Igény szerint

A NAT igény szerinti SNAT-portokat biztosít az új kimenő forgalom forgalmához. A leltárban lévő összes elérhető SNAT-portot a NAT-nal konfigurált alhálózatokon található bármely virtuális gép használja. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Virtual Network NAT igény szerinti kimenő SNAT">
</p>

*Ábra: Virtual Network NAT igény szerinti kimenő SNAT*

A virtuális gépek bármely IP-konfigurációja szükség szerint képes kimenő folyamatokat létrehozni igény szerint.  Az előzetes kiosztást, az egyes példányok esetében a legrosszabb esetek túlzott kiépítését, nem kötelező.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Különbségek a kimerülési forgatókönyvekben">
</p>

*Ábra: különbségek a kimerülési forgatókönyvekben*

A SNAT-portok kiadása után a NAT-nal konfigurált alhálózatokon található bármely virtuális gép használhatja azt.  Az igény szerinti foglalás lehetővé teszi, hogy az alhálózatok dinamikus és eltérő számítási feladatait a szükséges SNAT-portok használatára használják.  Amíg rendelkezésre áll a SNAT-portok készlete, a SNAT-folyamatok sikeresek lesznek. A SNAT port elérési helyei a nagyobb leltár előnyeit élvezik. A SNAT portok nem maradnak meg a virtuális gépeken, amelyek nem igényelnek aktívan rájuk.

### <a name="scaling"></a>Méretezés

A NAT-nak elegendő SNAT-portra van szüksége a teljes kimenő forgatókönyvhöz. A NAT méretezése elsősorban a megosztott, elérhető SNAT-készletek felügyeletének funkciója. Elegendő leltárra van szükség a NAT-átjáró erőforrásához csatolt összes alhálózat kimenő forgalmának kezeléséhez.

A SNAT több magánhálózati címet is leképez egy nyilvános címre, és több nyilvános IP-címet használ a skálázáshoz.

A NAT-átjáró erőforrása 64 000 portot (SNAT-portokat) használ a nyilvános IP-címekhez.  Ezek a SNAT-portok elérhetővé válnak a privát és a nyilvános folyamat megfeleltetésének. A több nyilvános IP-cím hozzáadása pedig növeli a rendelkezésre álló leltározási SNAT-portokat. A NAT-átjáró erőforrásai legfeljebb 16 IP-címet és 1 millió SNAT-portot tudnak méretezni.  A TCP és az UDP külön SNAT-portok és nem kapcsolódók.

A NAT-átjáró erőforrásai felhasználhatják a forrás portjait. Skálázási célokra érdemes feltételezni, hogy minden folyamathoz új SNAT-port szükséges, és a kimenő forgalom számára elérhető IP-címek teljes számát meg kell méretezni.

### <a name="protocols"></a>Protokollok

A NAT-átjáró erőforrásai az UDP-és TCP-folyamatok IP-és IP-továbbítási fejlécével működnek együtt, és az alkalmazás rétegbeli hasznos adatokhoz is függetlenek.  Más IP-protokollok nem támogatottak.

### <a name="timers"></a>Időzítők

Az Üresjárati időkorlát 4 perc (alapértelmezett) és 120 perc (2 óra) között állítható be minden folyamathoz.  Emellett alaphelyzetbe állíthatja az üresjárati időzítőt a folyamat forgalmával.  A hosszú üresjárati kapcsolatok és a végpontok közötti élő észlelések frissítésére szolgáló javasolt minta a TCP-Keepalives.  A TCP-Keepalives ismétlődő nyugták jelennek meg a végpontok számára, alacsony terheléssel és az alkalmazás rétegében láthatatlanok.

A következő időzítők használatosak a SNAT-port kiadásához:

| Időzítő | Érték |
|---|---|
| TCP FIN | 60 másodperc |
| ELSŐ TCP | 10 másodperc |
| TCP félig nyitott | 30 másodperc |

A SNAT-portok 5 másodperc elteltével újra felhasználhatók ugyanarra a cél IP-címhez és célport-portra.

>[!NOTE] 
>Ezek az időzítő beállítások változhatnak. Az értékek segítséget nyújtanak a hibaelhárításhoz, és jelenleg nem kell függőséget adni a megadott időzítők közül.

## <a name="limitations"></a>Korlátozások

- A NAT kompatibilis a standard SKU nyilvános IP-címmel, a nyilvános IP-előtaggal és a terheléselosztó erőforrásaival.   Az alapszintű erőforrások (például az alapszintű Load Balancer) és a belőlük származtatott termékek nem kompatibilisek a NAT-val.  Az alapszintű erőforrásokat a NAT-mel nem konfigurált alhálózatra kell helyezni.
- Az IPv4-címek családja támogatott.  A NAT nem támogatja az IPv6-cím családját.  A NAT nem helyezhető üzembe IPv6-előtaggal rendelkező alhálózaton.
- A NSG folyamat naplózása nem támogatott a NAT használata esetén.
- A NAT nem tud több virtuális hálózatot kifogni.


## <a name="feedback"></a>Visszajelzés

Szeretnénk tudni, hogyan lehet javítani a szolgáltatást. Javasolja és szavazzon arra, hogy mi a következő lépés a [NAT-UserVoice](https://aka.ms/natuservoice).

## <a name="next-steps"></a>További lépések

* További tudnivalók a [Virtual Network NAT](nat-overview.md)-ról.
* Tudnivalók a [NAT-átjáró erőforrásaira vonatkozó mérőszámokról és riasztásokról](nat-metrics.md).
* További információ a [NAT-átjárók erőforrásainak hibaelhárításáról](troubleshoot-nat.md).
* Oktatóanyag a NAT-átjáró ellenőrzéséhez
  - [Azure CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-cli.md)
  - [Portal](tutorial-create-validate-nat-gateway-cli.md)
* Gyors útmutató NAT Gateway-erőforrás üzembe helyezéséhez
  - [Azure CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portal](./quickstart-create-nat-gateway-portal.md)
  - [Sablon](./quickstart-create-nat-gateway-template.md)
* Tudnivalók a NAT Gateway Resource API-ról
  - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)

* További információ a [rendelkezésre állási zónákról](../availability-zones/az-overview.md).
* Ismerje meg a [standard Load balancert](../load-balancer/load-balancer-standard-overview.md).
* További információ a [rendelkezésre állási zónákról és a standard Load balancerről](../load-balancer/load-balancer-standard-availability-zones.md).


