---
title: Virtuális hálózatok tervezése NAT átjáró-erőforrásokkal
titleSuffix: Azure Virtual Network NAT
description: Ismerje meg, hogyan tervezhet virtuális hálózatokat NAT átjáró-erőforrásokkal.
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
ms.date: 03/31/2020
ms.author: allensu
ms.openlocfilehash: 405d9bc09462f2940567080ec86775baf066d70d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584570"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Virtuális hálózatok tervezése NAT átjáró-erőforrásokkal

A NAT-átjáró-erőforrások a [virtuális hálózati hálózati kapcsolattal](nat-overview.md) kapcsolatos virtuális hálózati kapcsolatok részét képezik, és kimenő internetkapcsolatot biztosítanak a virtuális hálózat egy vagy több alhálózatához. A nat-átjárót használni hozandó virtuális hálózati állapotok alhálózata. A NAT forráshálózati címfordítást (SNAT) biztosít egy alhálózathoz.  A NAT-átjáró-erőforrások határozzák meg, hogy mely statikus IP-címeket használnak a virtuális gépek a kimenő folyamatok létrehozásakor. A statikus IP-címek nyilvános IP-címerőforrásokból, nyilvános IP-előtag-erőforrásokból vagy mindkettőből származnak. Egy NAT átjáró-erőforrás akár 16 statikus IP-címet is használhat egyikből.


<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuális hálózati hálózati hálózati kapcsolatiat az internetre való kimenő kapcsolathoz">
</p>

*Ábra: Virtuális hálózati hálózati hálózati kapcsolatiat az internetre való kifelé irányuló kapcsolathoz*

## <a name="how-to-deploy-nat"></a>A NAT telepítése

A NAT-átjáró konfigurálása és használata szándékosan egyszerű:  

NAT átjáró erőforrás:
- Regionális vagy zónaszintű (zónaáltal elszigetelt) NAT átjáró-erőforrás létrehozása,
- IP-címek hozzárendelése,
- Szükség esetén módosítsa a TCP tétlen időidejét (nem kötelező).  Az alapértelmezett beállítások módosítása <ins>előtt</ins> tekintse át az [időzítőket.](#timers)

Virtuális hálózat:
- Konfigurálja a virtuális hálózati alhálózatot nat-átjáró használatára.

A felhasználó által definiált útvonalaknem szükségesek.

## <a name="resource"></a>Erőforrás

Az erőforrás úgy van kialakítva, hogy egyszerű legyen, amint azt a következő Azure Resource Manager példa sablonszerű formátumban láthatja.  Ez a sablon-szerű formátum itt látható, hogy bemutassa a fogalmak és a szerkezet.  Módosítsa a példát az Ön igényeinek megfelelően.  Ez a dokumentum nem oktatóanyag.

Az alábbi ábrán a különböző Azure Resource Manager-erőforrások írható hivatkozásokat mutat be.  A nyíl jelzi a hivatkozás irányát, ahonnan az írható. Áttekintés 

<p align="center">
  <img src="media/nat-overview/flow-map.svg" width="256" title="Virtuális hálózati NAT objektummodell">
</p>

*Ábra: Virtuális hálózati NAT objektummodell*

A NAT a legtöbb számítási feladathoz ajánlott, kivéve, ha adott függősége van a [készletalapú terheléselosztó kimenő kapcsolatát.](../load-balancer/load-balancer-outbound-connections.md)  

A szabványos terheléselosztó-forgatókönyvekből, beleértve a [kimenő szabályokat](../load-balancer/load-balancer-outbound-rules-overview.md)is, áttelepítheti a NAT-átjáróra. Az áttelepítéshez helyezze át a nyilvános IP- és ip-előtag-erőforrásokat a terheléselosztó előkiszolgálóról a NAT átjáróra. A NAT-átjáró új IP-címei nem szükségesek. A szabványos nyilvános IP-cím és előtag mindaddig felhasználható, amíg az összeg nem haladja meg a 16 IP-címet. Tervezze meg az áttelepítést a szolgáltatás megszakításával az átmenet során.  A folyamat automatizálásával minimalizálhatja a megszakítást. Először tesztelje az áttelepítést egy átmeneti környezetben.  Az átmenet során a bejövő származó folyamatok nem érinti.

A következő példa egy Azure Resource Manager-sablonból származó kódrészlet.  Ez a sablon több erőforrást telepít, beleértve a NAT-átjárót is.  A sablon a következő paraméterekkel rendelkezik ebben a példában:

- **natgatewayname** - A NAT átjáró neve.
- **hely** – Az Azure-régió, ahol az erőforrás található.
- **publicipname** - a NAT-átjáróhoz társított kimenő nyilvános IP-cím neve.
- **vnetname** - A virtuális hálózat neve.
- **alhálózatnév** - a NAT-átjáróhoz társított alhálózat neve.

Az összes IP-cím és előtag-erőforrás által megadott IP-címek száma nem haladhatja meg a 16 IP-címet összesen. Tetszőleges számú IP-cím 1 és 16 között megengedett.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="81-96":::

A NAT átjáróerőforrás létrehozása után a virtuális hálózat egy vagy több alhálózatán használható. Adja meg, hogy mely alhálózatok használják ezt a NAT-átjáró-erőforrást. A NAT-átjáró nem képes egynél több virtuális hálózatra. Nem szükséges ugyanazt a NAT-átjárót hozzárendelni a virtuális hálózat összes alhálózatához. Az egyes alhálózatok különböző NAT-átjáró-erőforrásokkal konfigurálhatók.

A rendelkezésre állási zónákat nem használó forgatókönyvek területi (nincs megadva zóna) lesznek. Ha rendelkezésre állási zónákat használ, megadhat egy zónát a NAT egy adott zónához való elkülönítéséhez. A zónaredundancia nem támogatott. Tekintse át a NAT [rendelkezésre állási zónáit.](#availability-zones)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-vnet/azuredeploy.json" range="1-146" highlight="81-96":::

A NAT-átjárók egy virtuális hálózaton belüli alhálózaton lévő tulajdonsággal vannak definiálva. A virtuális hálózati virtuális hálózat **név** **alhálózati névén** lévő virtuális gépek által létrehozott folyamatok a NAT-átjárót fogják használni. Minden kimenő kapcsolat a **natgatewayname-hoz** társított IP-címeket fogja használni forrás IP-címként.

A példában használt Azure Resource Manager sablonról a következő témakörben olvashat bővebben:

- [Rövid útmutató: NAT-átjáró létrehozása – Erőforrás-kezelő sablon](quickstart-create-nat-gateway-template.md)
- [Virtuális hálózati NAT](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm/)

## <a name="design-guidance"></a>Tervezési útmutató

Tekintse át ezt a szakaszt, és ismerkedjen meg a virtuális hálózatok NAT-tal való tervezésével kapcsolatos szempontokkal.  

1. [Költségoptimalizálás](#cost-optimization)
1. [A bejövő és kimenő konélmények együttélése](#coexistence-of-inbound-and-outbound)
2. [Alapvető erőforrások kezelése](#managing-basic-resources)
3. [Elérhetőségi zónák](#availability-zones)

### <a name="cost-optimization"></a>Költségoptimalizálás

[A szolgáltatásvégpontok](virtual-network-service-endpoints-overview.md) és [a privát kapcsolat](../private-link/private-link-overview.md) olyan lehetőségek, amelyeket figyelembe kell venni a költségek optimalizálásához. A NAT nem szükséges ezekhez a szolgáltatásokhoz. A szolgáltatásvégpontokra vagy privát kapcsolatra irányuló forgalmat a virtuális hálózat hálózati szolgáltatása nem dolgozza fel.  

A szolgáltatásvégpontok az Azure-szolgáltatás erőforrásait a virtuális hálózathoz kötik, és szabályozzák az Azure-szolgáltatás erőforrásaihoz való hozzáférést. Például az Azure storage elérésekor használjon egy szolgáltatásvégpontot a tároláshoz az adatok feldolgozott NAT-díjainak elkerülése érdekében. A szolgáltatásvégpontok ingyenesek.

A privát kapcsolat az Azure PaaS-szolgáltatást (vagy a privát kapcsolattal üzemeltetett egyéb szolgáltatásokat) egy virtuális hálózaton belüli privát végpontként teszi elérhetővé.  A privát hivatkozás számlázása az időtartam és a feldolgozott adatok alapján történik.

Értékelje ki, hogy az egyik vagy mindkét megközelítés megfelel-e a forgatókönyvnek, és szükség szerint használja-e.

### <a name="coexistence-of-inbound-and-outbound"></a>A bejövő és kimenő konélmények együttélése

A NAT átjáró a következőkkel kompatibilis:

 - Standard terheléselosztó
 - Szabványos nyilvános IP
 - Szabványos nyilvános IP-előtag

Új üzembe helyezés fejlesztése kor, kezdje a szabványos skus.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtuális hálózati hálózati hálózati kapcsolatiat az internetre való kimenő kapcsolathoz">
</p>

*Ábra: Virtuális hálózati hálózati hálózati kapcsolatiat az internetre való kifelé irányuló kapcsolathoz*

A NAT-átjáró által biztosított internetes kimenő forgatókönyv kibontható az internetes funkciókból érkezővel. Minden erőforrás tisztában van azzal, hogy milyen irányból származik a folyamat. NAT-átjáróval rendelkező alhálózaton az internetes forgatókönyvek száma it a NAT-átjáró felülírja. Az internetes forgatókönyvekből érkező kontőket az adott erőforrás biztosítja.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT és virtuális gép példányszintű nyilvános IP-címvel

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" width="300" title="Virtuális hálózati hálózati hálózati címzés és virtuális gép példányszintű nyilvános IP-címvel">
</p>

*Ábra: Virtuális hálózati hálózati hálózati címzés és virtuális gép példányszintű nyilvános IP-címvel*

| Irány | Erőforrás |
|:---:|:---:|
| Bejövő | Virtuális gép példányszintű nyilvános IP-címvel |
| Kimenő | NAT-átjáró |

A virtuális gép nat-átjárót fog használni a kimenő.  A bejövő bejövő nem érinti.

#### <a name="nat-and-vm-with-public-load-balancer"></a>NAT és virtuális gép nyilvános terheléselosztóval

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" width="350" title="Virtuális hálózati NAT és virtuális gép nyilvános terheléselosztóval">
</p>

*Ábra: Virtuális hálózati NAT és virtuális gép nyilvános terheléselosztóval*

| Irány | Erőforrás |
|:---:|:---:|
| Bejövő | nyilvános terheléselosztó |
| Kimenő | NAT-átjáró |

A terheléselosztási szabályból vagy a kimenő szabályokból származó kimenő konfigurációkat a NAT-átjáró felülírja.  A bejövő bejövő nem érinti.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-public-load-balancer"></a>NAT és virtuális gép példányszintű nyilvános IP-címés nyilvános terheléselosztóval

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" width="425" title="Virtuális hálózati NAT és virtuális gép példányszintű nyilvános IP-címés nyilvános terheléselosztóval">
</p>

*Figure: Virtual Network NAT and VM with instance-level public IP and public Load Balancer*

| Irány | Erőforrás |
|:---:|:---:|
| Bejövő | Virtuális gép példányszintű nyilvános IP-címés nyilvános terheléselosztóval |
| Kimenő | NAT-átjáró |

A terheléselosztási szabályból vagy a kimenő szabályokból származó kimenő konfigurációkat a NAT-átjáró felülírja.  A virtuális gép nat-átjárót is használ a kimenő kimenő.  A bejövő bejövő nem érinti.

### <a name="managing-basic-resources"></a>Alapvető erőforrások kezelése

A szabványos terheléselosztó, a nyilvános IP-cím és a nyilvános IP-előtag kompatibilis a NAT-átjáróval. A NAT-átjárók alhálózat hatókörében működnek. Ezeknek a szolgáltatásoknak az alapvető termékváltozatát nat-átjáró nélküli alhálózaton kell telepíteni. Ez a szétválasztás lehetővé teszi, hogy mindkét Termékváltozat ugyanazon a virtuális hálózatban egymás mellett létezzen.

A NAT-átjárók elsőbbséget élveznek az alhálózat kimenő forgatókönyveivel szemben. Az alapszintű terheléselosztó vagy a nyilvános IP (és a velük készített felügyelt szolgáltatások) nem módosíthatóak a megfelelő fordításokkal. A NAT-átjáró átveszi az irányítást az alhálózaton lévő internetes forgalom felé irányuló kimenő forgalom felett. Bejövő forgalom az alapszintű terheléselosztó és a nyilvános ip nem érhető el. Bejövő forgalom egy alapszintű terheléselosztó, és vagy egy nyilvános ip-konfigurált egy virtuális gép nem lesz elérhető.

### <a name="availability-zones"></a>Rendelkezésre állási zónák

#### <a name="zone-isolation-with-zonal-stacks"></a>Zónaelkülönítés övezeti halmokkal

<p align="center">
  <img src="media/nat-overview/az-directions.svg" width="425" title="Virtuális hálózati hálózati hálózati kapcsolat zónaelkülönítéssel, több "zonal stacks"">
</p>

*Ábra: Virtuális hálózati Hálózati hálózati kapcsolat zónaelkülönítéssel, több "zónahalm" létrehozása*

A NAT még rendelkezésre állási zónák nélkül is rugalmas, és több infrastruktúra-összetevő meghibásodását is képes túlélni.  A rendelkezésre állási zónák erre a rugalmasságra épülnek a NAT zónaelkülönítési forgatókönyveivel.

A virtuális hálózatok és alhálózataik regionális konstrukciók.  Az alhálózatok nem korlátozódnak zónára.

Zónaszintű zónabeli ígéret akkor létezik, ha egy NAT-átjáró-erőforrást használó virtuálisgép-példány ugyanabban a zónában van, mint a NAT átjáró-erőforrás és a nyilvános IP-címek. A zónaelkülönítéshez használni kívánt minta egy "zónaverést" hoz létre rendelkezésre állási zónánként.  Ez a "zónaszintű verem" virtuálisgép-példányokból, NAT-átjáró-erőforrásokból, nyilvános IP-címből és/vagy előtag-erőforrásokból áll egy olyan alhálózaton, amely ről feltételezik, hogy csak ugyanazt a zónát szolgálja ki.   A vezérlősík műveletei és az adatsík ezután a megadott zónához igazodnak, és a megadott zónához vannak korlátozva. 

A forgatókönyv létezésétnem milyen hatással lesz a NAT-ra. Az ugyanabban a zónában lévő virtuális gépek kimenő forgalma a zóna elkülönítése miatt sikertelen lesz.  

#### <a name="integrating-inbound-endpoints"></a>Bejövő végpontok integrálása

Ha a forgatókönyv bejövő végpontokat igényel, két lehetősége van:

| Beállítás | Mintázat | Példa | Pro | Con |
|---|---|---|---|---|
| (1) | **Igazítsa** a bejövő végpontokat a megfelelő **zónaverések** létre a kimenő. | Hozzon létre egy szabványos terheléselosztót zónaszintű előlapokkal. | Ugyanaz az állapotmodell és a sikertelen mód a bejövő és kimenő üzemmódhoz. Egyszerűbb a működtetés. | Előfordulhat, hogy zónánként az egyes IP-címeket egy közös DNS-névnek kell elfednie. |
| (2) | **Fedje át** a zónaszintű halmokat egy **zónába** érkező végpontdal. | Hozzon létre egy szabványos terheléselosztózóna-redundáns előtérrel. | Egyetlen IP-cím a bejövő végponthoz. | Különböző állapotmodell és hibamódok a bejövő és kimenő.  Bonyolultabb a működtetés. |

>[!NOTE]
> A zónaszigetel zárt NAT-átjáróhoz ip-címek szükségesek a NAT-átjáró zónájának megfelelően. NAT átjáró erőforrások IP-címeket egy másik zónából, vagy anélkül, hogy a zóna nem engedélyezett.

#### <a name="cross-zone-outbound-scenarios-not-supported"></a>A zónán átkelő kimenő forgatókönyvek nem támogatottak

<p align="center">
  <img src="media/nat-overview/az-directions2.svg" width="425" title="A virtuális hálózati hálózati hálózati kapcsolat nem kompatibilis a zónaátív alhálózattal">
</p>

*Ábra: A virtuális hálózati hálózati hálózati kapcsolat nem kompatibilis a zónaátív alhálózattal*

A NAT-átjáró-erőforrásokkal nem érhető el zónaszintű ígéret, ha a virtuálisgép-példányok ugyanazon alhálózaton belül több zónában vannak üzembe helyezve.   És még akkor is, ha több zónaszintű NAT átjárók egy alhálózathoz, a virtuális gép példány nem tudja, melyik NAT átjáró erőforrás kiválasztásához.

A zónaszintű ígéret nem létezik, ha a) a virtuális gép példányzónája és a zónaszintű NAT-átjáró zónái nincsenek összehangolva, vagy b) egy regionális NAT átjáró-erőforrást használnak a zónaszintű virtuálisgép-példányokkal.

Míg a forgatókönyv úgy tűnik, hogy működik, az állapotmodell és a hiba mód nincs definiálva a rendelkezésre állási zóna szempontjából. Fontolja meg megy a zonal halom, vagy az összes regionális helyett.

>[!NOTE]
>A NAT átjáróerőforrás zónák tulajdonsága nem módosítható.  Telepítse újra a NAT-átjáró erőforrást a kívánt területi vagy zónapreferenciával.

>[!NOTE] 
>Az IP-címek önmagukban nem zónaredundánsak, ha nincs megadva zóna.  A standard terheléselosztó előtétje [zónaredundáns,](../load-balancer/load-balancer-standard-availability-zones.md#frontend) ha egy IP-cím nem jön létre egy adott zónában.  Ez nem vonatkozik a NAT-ra.  Csak a regionális vagy zóna-elkülönítés támogatott.

## <a name="source-network-address-translation"></a>Forrás hálózati címfordítása

A forráshálózati címfordítás (SNAT) átírja a folyamat forrását, hogy egy másik IP-címről származik.  A NAT átjáró-erőforrások a Portcímfordításra (PAT) gyakran emlegetett SNAT-változatot használják. A PAT átírja a forráscímet és a forrásportot. Az SNAT-nál nincs rögzített kapcsolat a privát címek száma és a lefordított nyilvános címek között.  

### <a name="fundamentals"></a>Alapok

Nézzünk egy példát a négy folyamatok megmagyarázni az alapkoncepció.  A NAT-átjáró a 65.52.0.2 nyilvános IP-címerőforrást használja.

| Folyamat | Forrás-megszakító | Cél- és felbőszés |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Ezek az áramlások így nézhetnek ki, miután a PAT megtörtént:

| Folyamat | Forrás-megszakító | SNAT'ed forrást, és megszakította | Cél- és felbőszés | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.2:234 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.2:235 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.2:236 | 65.52.0.1:80 |
| 4 | 192.168.0.16:4285 | 65.52.0.2:237 | 65.52.0.2:80 |

A cél a folyamat forrását 65.52.0.2 (SNAT forráscsatlakozó) látja a hozzárendelt porttal.  A PAT, ahogy az előző táblázatban látható, portálcázási SNAT néven is szerepel.  Az IP és a port mögött több magánforrás is szerepel.

Ne vegyen igénybe függőséget a forrásportok hozzárendelésének konkrét módjától.  Az előző csak az alapvető fogalmat szemlélteti.

A NAT által biztosított SNAT több szempontból is eltér a [terheléselosztótól.](../load-balancer/load-balancer-outbound-connections.md)

### <a name="on-demand"></a>Igény szerint

A NAT igény szerinti SNAT-portokat biztosít az új kimenő forgalomhoz. A készletben lévő összes rendelkezésre álló SNAT-portot a Hálózati címzettel konfigurált alhálózatokon lévő bármely virtuális gép használja. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" width="550" title="Virtuális hálózati Hálózati hálózati hálózati igény szerinti kimenő SNAT">
</p>

*Ábra: Virtuális hálózati Hálózati hálózati hálózati igény szerinti kimenő SNAT*

A virtuális gépek bármely IP-konfigurációja igény szerint igény szerint hozhat létre kimenő folyamatokat.  Előzetes allokáció, példányonkénttervezés, beleértve a példányonkénti legrosszabb esetben túlépítés, nem szükséges.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" width="550" title="Különbségek a kimerültségi forgatókönyvekben">
</p>

*Ábra: Különbségek a kimerültségi forgatókönyvekben*

Az SNAT-port kiadása után a NAT-tal konfigurált alhálózatokon lévő bármely virtuális gép számára elérhető.  Az igény szerinti allokáció lehetővé teszi, hogy az alhálózat(ok) dinamikus és eltérő munkaterhelései szükség szerint használják az SNAT-portokat.  Mindaddig, amíg van SNAT port leltár áll rendelkezésre, SNAT-folyamatok sikeres lesz. SNAT port hot spotok részesülnek a nagyobb leltár helyett. Az SNAT-portok nem maradnak használaton kívüliek olyan virtuális gépeknél, amelyeknek nincs szükségük rájuk.

### <a name="scaling"></a>Méretezés

A NAT méretezése elsősorban a megosztott, rendelkezésre álló SNAT-portkészlet kezelésének függvénye. A NAT-nak elegendő SNAT-portkészletre van szüksége a NAT-átjáró-erőforráshoz kapcsolódó összes alhálózat várható kimenő csúcsfolyamataihoz.  Nyilvános IP-cím erőforrások, nyilvános IP-előtag-erőforrások vagy mindkettő segítségével SNAT-portkészletet hozhat létre.

Az SNAT leképezi a privát címeket egy vagy több nyilvános IP-címre, átírva a forráscímet és a forrásportot a folyamatokban. A NAT-átjáró-erőforrások konfigurált nyilvános IP-címenként 64 000 portot (SNAT-portot) használnak ehhez a fordításhoz. A NAT átjáró-erőforrások legfeljebb 16 IP-címet és 1M SNAT-portot skálázhatnak. Ha nyilvános IP-előtag erőforrás van megadva, az előtagon belül minden IP-cím SNAT-portkészletet biztosít. És további nyilvános IP-címek hozzáadása növeli a rendelkezésre álló készlet SNAT portok. A TCP és az UDP különálló SNAT-portkészletek és független.

A NAT átjáró erőforrásai opportunista módon újrafelhasználják a forrásportokat. Skálázási célokra, feltételezi, hogy minden folyamat hoz egy új SNAT-port és a kimenő forgalom rendelkezésre álló IP-címek teljes számának méretezése.

### <a name="protocols"></a>Protokollok

A NAT-átjáró-erőforrások interakcióba lépnek az UDP- és TCP-folyamatok IP- és IP-átviteli fejléceivel, és agnotikusak az alkalmazásréteg-adatokhoz.  Más IP-protokollok nem támogatottak.

### <a name="timers"></a>Időzítők

>[!IMPORTANT]
>A hosszú tétlen időzítő szükségtelenül növelheti az SNAT kimerültség valószínűségét. Minél hosszabb ideig adja meg az időzítőt, annál hosszabb ideig tartja a NAT-portokat, amíg végül nem lépnek le. Ha a folyamatok tétlen időtúltöltés, akkor végül sikertelen lesz, és szükségtelenül fogyasztanak SNAT port leltár.  A 2 órán ként meghibásodó folyamatok az alapértelmezett 4 percen belül is sikertelenek voltak volna. Az alapjárati időhosszabbítás növelése egy végső lehetőség, amelyet takarékosan kell használni. Ha egy folyamat soha nem megy tétlen, akkor nem érinti az tétlen időzítő.

A TCP tétlen időtúlára 4 perc (alapértelmezett) és 120 perc (2 óra) között állítható be az összes folyamatesetében.  Emellett alaphelyzetbe állíthatja az alapjárati időzítőt a forgalom a folyamaton.  A hosszú tétlen kapcsolatok és végpontok élősdésének frissítéséhez ajánlott minta a TCP keepalives.  A TCP keepalives ismétlődő ACK-kként jelenik meg a végpontok számára, alacsony terheléssel rendelkezik, és az alkalmazásréteg számára láthatatlan.

A SNAT-portkiadáshoz a következő időzítők használatosak:

| Időzítő | Érték |
|---|---|
| TCP FIN | 60 másodperc |
| TCP RST | 10 másodperc |
| TCP félig nyitva | 30 másodperc |

Az SNAT-port 5 másodperc elteltével újra felhasználható ugyanarra a cél IP-címre és célportra.

>[!NOTE] 
>Ezek az időzítőbeállítások változhatnak. Az értékek a hibaelhárítás elősegítésére szolgálnak, és jelenleg nem szabad adott időzítőktől függeni.

## <a name="limitations"></a>Korlátozások

- A NAT kompatibilis a szabványos termékváltozat nyilvános IP-címével, nyilvános IP-előtaggal és terheléselosztó erőforrásokkal.   Az alapvető erőforrások (például az alapterhelés-elosztó) és az ezekből származó termékek nem kompatibilisek a NAT-tal.  Az alapvető erőforrásokat olyan alhálózaton kell elhelyezni, amely nincs konfigurálva a Hálózati címtón.
- Az IPv4-címcsalád támogatott.  A NAT nem lép kapcsolatba az IPv6-címcsaláddal.  A hálózati címt nem lehet iPv6-előtaggal rendelkező alhálózaton telepíteni.
- NSG-folyamatnaplózás nem támogatott, ha nat használatával.
- A Hálózati atta nem terjedhet ki több virtuális hálózatra.


## <a name="feedback"></a>Visszajelzés

Azt akarjuk tudni, hogyan tudjuk javítani a szolgáltatást. Hiányzik egy képesség? Hogy az Ön esetében, amit meg kell építeni a következő [UserVoice a NAT](https://aka.ms/natuservoice).

## <a name="next-steps"></a>További lépések

* További információ a [virtuális hálózati Hálózati hálózati kapcsolatról](nat-overview.md).
* Ismerje meg [a NAT-átjáró-erőforrások metrikáit és riasztásait.](nat-metrics.md)
* További információ a [NAT-átjáró erőforrásainak hibaelhárításáról.](troubleshoot-nat.md)
* Oktatóanyag a NAT-átjáró érvényesítéséhez
  - [Azure CLI](tutorial-create-validate-nat-gateway-cli.md)
  - [PowerShell](tutorial-create-validate-nat-gateway-cli.md)
  - [Portál](tutorial-create-validate-nat-gateway-cli.md)
* Rövid útmutató a NAT-átjáró-erőforrások telepítéséhez
  - [Azure CLI](./quickstart-create-nat-gateway-cli.md)
  - [PowerShell](./quickstart-create-nat-gateway-powershell.md)
  - [Portál](./quickstart-create-nat-gateway-portal.md)
  - [Sablon](./quickstart-create-nat-gateway-template.md)
* Tudnivalók a NAT átjáróerőforrás API-ról
  - [REST API](https://docs.microsoft.com/rest/api/virtualnetwork/natgateways)
  - [Azure CLI](https://docs.microsoft.com/cli/azure/network/nat/gateway?view=azure-cli-latest)
  - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway)
* További információ a [rendelkezésre állási zónákról.](../availability-zones/az-overview.md)
* További információ a [szabványos terheléselosztóról.](../load-balancer/load-balancer-standard-overview.md)
* További információ a [rendelkezésre állási zónákról és a szabványos terheléselosztóról.](../load-balancer/load-balancer-standard-availability-zones.md)
* [Mondja el, mit kell építeni a következő virtuális hálózati NAT a UserVoice](https://aka.ms/natuservoice).


