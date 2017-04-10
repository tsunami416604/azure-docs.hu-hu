---
title: "Hálózati biztonsági csoportok az Azure-ban | Microsoft Docs"
description: "Ismerje meg, miként különítheti el és irányíthatja a forgalmat virtuális hálózatán belül a hálózati biztonsági csoportokra épülő Azure elosztott tűzfal használatával."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6ea9db6ac7a7ba63652b860c22899a8616ea81bc
ms.lasthandoff: 04/03/2017


---
# <a name="control-network-traffic-flow-with-network-security-groups"></a>Hálózati forgalom áramlásának vezérlése hálózati biztonsági csoportokkal

A hálózati biztonsági csoport (NSG) tartalmazza a hozzáférés-vezérlési (ACL) szabályok listáját, amelyek megszabják, hogy milyen típusú hálózati forgalom érhesse el a virtuális hálózatban futó virtuálisgép-példányokat. Az NSG-ket alhálózatokhoz vagy az alhálózaton belüli virtuálisgép-példányokhoz lehet hozzárendelni. Amikor egy NSG-t hozzárendelnek egy alhálózathoz, az ACL szabályok érvényessé válnak az alhálózat összes virtuálisgép-példányára. Emellett egy adott virtuális gépre irányuló forgalmat tovább is lehet korlátozni azzal hogy egy NSG-t közvetlenül ahhoz a virtuális géphez rendelnek.

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). A jelen cikk mindkét modell használatát bemutatja, de a Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="nsg-resource"></a>NSG-erőforrás
Az NSG-k az alábbi tulajdonságokat tartalmazzák.

| Tulajdonság | Leírás | Korlátozások | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Név |Az NSG neve |Egyedinek kell lennie a régión belül<br/>Betűket, számokat, aláhúzásjeleket, pontokat és kötőjeleket tartalmazhat<br/>Betűvel vagy számmal kell kezdődnie<br/>Betűvel, számmal vagy aláhúzásjellel kell végződnie<br/>Legfeljebb 80 karakterből állhat |Mivel előfordulhat, hogy több NSG-t kell létrehoznia, győződjön meg arról, hogy van egy elnevezési konvenciója, amellyel könnyedén azonosíthatja az NSG-k funkcióját |
| Régió |Az Azure-régió, ahol az NSG-t üzemeltetik |Az NSG-ket csak abban a régióban lehet alkalmazni az erőforrásokra, ahol létrehozták |Tekintse meg alább a [korlátok](#Limits) szakaszt, hogy megtudja az egy régióban megengedett NSG-k számát |
| Erőforráscsoport |Az erőforráscsoport, amelybe az NSG tartozik |Az NSG-k egy adott erőforráscsoportokhoz tartoznak, azonban bármely erőforráscsoport erőforrásaihoz társíthatók, feltéve, hogy az erőforrás ugyanahhoz az Azure-régióhoz tartozik, mint az NSG |Az erőforráscsoportokkal több erőforrást kezelnek egyszerre, egy üzembe helyezési egységként<br/>Érdemes az NSG-ket egy csoportba tenni azokkal az erőforrásokkal, amelyekhez társítva vannak |
| Szabályok |Az engedélyezett vagy tiltott forgalmat meghatározó szabályok | |Tekintse meg alább az [NSG-szabályok](#Nsg-rules) című szakaszt |

> [!NOTE]
> A végpont-alapú ACL-ek és hálózati biztonsági csoportok nem támogatottak ugyanazon a virtuálisgép-példányon. Ha használni szeretne egy NSG-t, és már be van állítva egy végponti ACL, először el kell távolítani a végponti ACL-t. Ennek módjáról a [Managing Access Control Lists (ACLs) for Endpoints by using PowerShell](virtual-networks-acl-powershell.md) (A hozzáférés-vezérlési listák (ACL-ek) kezelése a végpontoknál, a PowerShell használatával) szakaszban talál információkat.
> 

### <a name="nsg-rules"></a>NSG-szabályok
Az NSG-szabályok az alábbi tulajdonságokat tartalmazzák:

| Tulajdonság | Leírás | Korlátozások | Megfontolások |
| --- | --- | --- | --- |
| **Name (Név)** |A szabály neve |Egyedinek kell lennie a régión belül<br/>Betűket, számokat, aláhúzásjeleket, pontokat és kötőjeleket tartalmazhat<br/>Betűvel vagy számmal kell kezdődnie<br/>Betűvel, számmal vagy aláhúzásjellel kell végződnie<br/>Legfeljebb 80 karakterből állhat |Egy NSG-n belül több szabály is lehet, ezért győződjön meg arról, hogy van egy elnevezési konvenciója, amellyel azonosíthatja a szabályok funkcióját |
| **Protocol (Protokoll)** |A szabálynak megfelelő protokoll |TCP, UDP vagy \* |A \* protokollként történő használata tartalmazza az ICMP-t (csak kelet–nyugat irányú forgalom), valamint az UDP-t és a TCP-t, és csökkentheti a szükséges szabályok számát<br/>A \* használata azonban túl széles körű lehet, ezért ügyeljen arra, hogy csak szükség esetén használja |
| **Source port range (Forrásporttartomány)** |A szabálynak megfelelő forrásporttartomány |Egy portszám 1 és 65535 között, egy porttartomány (pl. 1–65635) vagy \* (minden porthoz) |A forrásportok rövid élettartamúak is lehetnek. Ha az ügyfélprogram nem egy adott portot használ, a legtöbb esetben a „*” jelet használja.<br/>A lehető legtöbb esetben használjon porttartományokat, hogy ne legyen szükség több szabályra<br/>Több portot vagy porttartományt nem lehet vesszővel csoportosítani |
| **Destination port range (Célporttartomány)** |A szabálynak megfelelő célporttartomány |Egy portszám 1 és 65535 között, egy porttartomány (pl. 1–65535) vagy \* (minden porthoz) |A lehető legtöbb esetben használjon porttartományokat, hogy ne legyen szükség több szabályra<br/>Több portot vagy porttartományt nem lehet vesszővel csoportosítani |
| **Source address prefix (Forráscímelőtag)** |A szabálynak megfelelő forráscím-előtag vagy címke |Egyetlen IP-cím (pl. 10.10.10.10), IP-alhálózat (pl. 192.168.1.0/24), [alapértelmezett címke](#default-tags) vagy * (minden címhez) |Érdemes lehet tartományokat, alapértelmezett címkéket és a * jelet használni a szabályok számának csökkentéséhez |
| **Destination address prefix (Célcímelőtag)** |A szabálynak megfelelő célcím-előtag vagy címke |Egyetlen IP-cím (pl. 10.10.10.10), IP-alhálózat (pl. 192.168.1.0/24), [alapértelmezett címke](#default-tags) vagy * (minden címhez) |Érdemes lehet tartományokat, alapértelmezett címkéket és a * jelet használni a szabályok számának csökkentéséhez |
| **Direction (Irány)** |A forgalom szabálynak megfelelő iránya |bejövő vagy kimenő |A bejövő vagy kimenő szabályokat a rendszer külön dolgozza fel, az irány alapján |
| **Priority (Prioritás)** |A szabályokat a rendszer prioritás szerinti sorrendben ellenőrzi, és amint talál egy érvényes szabályt, nem vizsgálja, hogy a többi szabálynak megfelel-e a forgalom |100 és 4096 közötti szám |A prioritások meghatározásakor érdemes 100-zas lépésközt használni, hogy az új szabályoknak is legyen hely a meglévő szabályok között |
| **Access (Hozzáférés)** |Az alkalmazandó hozzáférés típusa, ha a csomag megfelel a szabálynak |engedélyezés vagy megtagadás |Ne feledje, hogy ha egy csomag egyik szabálynak sem felel meg, akkor a rendszer eldobja a csomagot |

Az NSG-k két szabálykészletet tartalmaznak: bejövőt és kimenőt. A szabály prioritásának az egyes készleten belül kell egyedinek lennie. 

![Az NSG-szabály feldolgozása](./media/virtual-network-nsg-overview/figure3.png) 

A fenti ábrán az NSG-szabályok feldolgozásának folyamata látható.

### <a name="default-tags"></a>Alapértelmezett címkék
Az alapértelmezett címkék olyan rendszer által biztosított azonosítók, amelyek az IP-címek egy kategóriáját célozzák meg. Az alapértelmezett címkéket a szabályok tulajdonságainak **forráscím-előtagjában** és **célcím-előtagjában** lehet használni. Háromféle alapértelmezett címkét lehet használni.

* **VIRTUAL_NETWORK** (Azure Resource Manager használata esetén **VirtualNetwork**): Ez az alapértelmezett címke jelöli az összes hálózati címteret. Beletartozik a virtuális hálózat címtere (az Azure által meghatározott CIDR tartományok), illetve az összes csatlakoztatott helyszíni címtér és Azure VNet (helyi hálózatok).
* **AZURE_LOADBALANCER** (Azure Resource Manager használata esetén **AzureLoadBalancer**): Ez az alapértelmezett címke az Azure infrastruktúra terheléselosztóját jelöli. Ezt a rendszer le fogja fordítani arra az Azure-adatközponti IP-címre, ahonnan az Azure állapot-mintavételi csomagjai származnak.
* **INTERNET:** Ez az alapértelmezett címke azt az IP-címteret jelöli, amely a virtuális hálózaton kívül esik és a nyilvános interneten keresztül érhető el. A tartományba beletartozik az [Azure tulajdonában lévő nyilvános IP-címtér](https://www.microsoft.com/download/details.aspx?id=41653) is.

### <a name="default-rules"></a>Alapértelmezett szabályok
Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat. 

Ahogy az alábbi alapértelmezett szabályok is mutatják, a virtuális hálózatból eredő, és oda érkező forgalom a bejövő és kimenő irányban is engedélyezve van. Bár az internethez való kapcsolódás kimenő irányban engedélyezett, bejövő irányban alapértelmezés szerint le van tiltva. Van egy alapértelmezett szabály, ami engedélyezi az Azure terheléselosztója számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ezt a szabályt felül lehet bírálni, ha nem elosztott terhelésű készletet használ.

**Bejövő alapértelmezett szabályok**

| Név | Prioritás | Forrás IP-címe | Forrásport | Cél IP-címe | Célport | Protokoll | Hozzáférés |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ALLOW VNET INBOUND |65000 |VIRTUAL_NETWORK |* |VIRTUAL_NETWORK |* |* |ALLOW |
| ALLOW AZURE LOAD BALANCER INBOUND |65001 |AZURE_LOADBALANCER |* |* |* |* |ALLOW |
| DENY ALL INBOUND |65500 |* |* |* |* |* |DENY |

**Kimenő alapértelmezett szabályok**

| Név | Prioritás | Forrás IP-címe | Forrásport | Cél IP-címe | Célport | Protokoll | Hozzáférés |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ALLOW VNET OUTBOUND |65000 |VIRTUAL_NETWORK |* |VIRTUAL_NETWORK |* |* |ALLOW |
| ALLOW INTERNET OUTBOUND |65001 |* |* |INTERNET |* |* |ALLOW |
| DENY ALL OUTBOUND |65500 |* |* |* |* |* |DENY |

## <a name="associating-nsgs"></a>Az NSG-k társítása
Az NSG-ket virtuális gépekhez, hálózati adapterekhez és alhálózatokhoz is társíthatja attól függően, hogy milyen üzembe helyezési modellt használ.

* **NGS társítása virtuális géphez (csak klasszikus üzembe helyezés).** Amikor virtuális géphez társít egy NSG-t, a rendszer alkalmazza az NSG hálózatelérési szabályait a virtuális gépbe érkező, és az azt elhagyó forgalomra. 
* **NGS társítása hálózati adapterhez (csak Resource Manager üzembe helyezések).** Amikor hálózati adapterhez társít egy NSG-t, a rendszer az NSG hálózatelérési szabályait csak a hálózati adapterre alkalmazza. Ez azt jelenti, hogy ha egy több hálózati adapterrel rendelkező virtuális gépben a rendszer az NSG-t csak az egyik hálózati adapterre alkalmazza, az nem lesz hatással a többi hálózati adapter forgalmára. 
* **NSG társítása alhálózathoz (minden üzembe helyezés)**. Amikor alhálózathoz társít egy NSG-t, a rendszer alkalmazza az NSG hálózatelérési szabályait az alhálózat összes infrastruktúra-szolgáltatási és PaaS erőforrására. 

Különböző NSG-ket társíthat egy virtuális géphez (vagy hálózati adapterhez, a üzembe helyezési modelltől függően) és az alhálózathoz, amelyhez hozzá van kötve egy hálózati adapter vagy egy virtuális gép. Ebben az esetben a rendszer az összes hálózatelérési szabályt alkalmazza a forgalomra, minden NSG-ben prioritás szerint, a következő sorrendben:

- **Bejövő forgalom**

  1. **Alhálózatra alkalmazott NSG:** Ha egy alhálózati NSG rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer eldobja.

  2. **Hálózati adapterre (Resource Manager) vagy virtuális gépre (hagyományos) alkalmazott NSG**: Ha a VM\Hálózati adapter NSG rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer eldobja, miközben az alhálózati NSG rendelkezik a forgalmat engedélyező megfelelő szabállyal.

- **Kimenő forgalom**

  1. **Hálózati adapterre (Resource Manager) vagy virtuális gépre (hagyományos) alkalmazott NSG**: Ha a VM\Hálózati adapter NSG rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer eldobja.

  2. **Alhálózatra alkalmazott NSG**: Ha egy alhálózati NSG rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer eldobja, miközben a VM\Hálózati adapter NSG rendelkezik a forgalmat engedélyező megfelelő szabállyal.

> [!NOTE]
> Bár egy alhálózathoz, virtuális géphez vagy hálózati adapterhez csak egy NSG-t lehet társítani, ugyanazt az NSG-t korlátlan számú erőforráshoz hozzá lehet rendelni.
>

## <a name="implementation"></a>Megvalósítás
Az NSG-ket a hagyományos és a Resource Manager üzembe helyezési modellel is meg lehet valósítani a lent felsorolt eszközök használatával.

| Üzembe helyezési eszköz | Klasszikus | Resource Manager |
| --- | --- | --- |
| klasszikus portál | Nem  | Nem |
| Azure Portal   | Igen | [Igen](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Igen](virtual-networks-create-nsg-classic-ps.md) | [Igen](virtual-networks-create-nsg-arm-ps.md) |
| Azure CLI      | [Igen](virtual-networks-create-nsg-classic-cli.md) | [Igen](virtual-networks-create-nsg-arm-cli.md) |
| ARM-sablon   | Nem  | [Igen](virtual-networks-create-nsg-arm-template.md) |

## <a name="planning"></a>Tervezés
Az NSG-k megvalósítása előtt válaszolnia kell az alábbi kérdésekre:

1. Milyen típusú erőforrásokkal szeretné szűrni a bejövő és kimenő forgalmat (hálózati adapterek ugyanabban a virtuális gépben, virtuális gépek vagy más erőforrások, például ugyanahhoz az alhálózathoz vagy más alhálózatokhoz csatlakoztatott felhőszolgáltatások vagy alkalmazásszolgáltatási környezetek)?
2. Az erőforrások, amelyekkel a bejövő/kimenő forgalmat szeretné szűrni, meglévő VNetek alhálózataihoz csatlakoznak, vagy új VNetekhez vagy alhálózatokhoz fognak csatlakozni?

A hálózati biztonság Azure-ban történő megtervezésével kapcsolatos további információkért olvassa el [a felhőszolgáltatások és a hálózati biztonság ajánlott eljárásait](../best-practices-network-security.md). 

## <a name="design-considerations"></a>Kialakítási szempontok
Ha megválaszolta a [Tervezés](#Planning) szakasz kérdéseit, tekintse át az alábbiakat, mielőtt meghatározná az NSG-ket.

### <a name="limits"></a>Korlátok
Az NSG-k tervezésekor figyelembe kell vennie az alábbi korlátokat.

| **Leírás** | **Alapértelmezett korlát** | **Következmények** |
| --- | --- | --- |
| Az alhálózathoz, virtuális géphez vagy hálózati adapterhez társítható NSG-k száma |1 |Ez azt jelenti, hogy az NSG-ket nem lehet egyesíteni. Győződjön meg arról, hogy az NSG az adott erőforráskészlethez szükséges összes szabályt tartalmazza. |
| Az előfizető számára régiónként rendelkezésre álló NSG-k |100 |Alapértelmezés szerint az Azure portálon létrehozott összes virtuális géphez létrejön egy NSG. Ha engedélyezi ezt az alapértelmezett viselkedést, akkor az NSG-k gyorsan el fognak fogyni. Győződjön meg arról, hogy tervezés közben figyelembe veszi ezt a korlátot, és az erőforrásokat szükség esetén több régióba vagy előfizetésbe különíti el. |
| NSG-ben szereplő NSG-szabályok |200 |Használjon széles IP-cím- és porttartományt, hogy biztosan ne lépje át a korlátot. |

> [!IMPORTANT]
> Győződjön meg arról, hogy az [Azure hálózati szolgáltatásaihoz kapcsolódó korlátok](../azure-subscription-service-limits.md#networking-limits) mindegyikét megtekintette a megoldás tervezése előtt. Néhány korlátot lehet növelni egy támogatási jegy megnyitásával.
> 
> 

### <a name="vnet-and-subnet-design"></a>VNet és alhálózat kialakítása
Mivel az NSG-ket alhálózatokra is lehet alkalmazni, minimalizálhatja az NSG-k számát azzal, hogy alhálózat szerint csoportosítja az erőforrásokat, és az alhálózatokra alkalmazza az NSG-ket.  Ha úgy dönt, hogy NSG-ket alkalmaz az alhálózatokra, azt tapasztalhatja, hogy a meglévő VNetek és alhálózatok meghatározásánál nem vették figyelembe az NSG-ket. Előfordulhat, hogy az NSG-kialakítás támogatásához új virtuális hálózatokat és alhálózatokat kell meghatároznia. Helyezze üzembe az új erőforrásokat az új alhálózatokra. Ezt követően meghatározhatja az áttelepítési stratégiát a meglévő erőforrások új alhálózatokra történő áthelyezésére. 

### <a name="special-rules"></a>Különleges szabályok
Figyelembe kell vennie az alább felsorolt különleges szabályokat. Győződjön meg arról, hogy nem tiltotta le a szabályok által engedélyezett forgalmat, máskülönben az infrastruktúra nem fog tudni kommunikálni az alapvető fontosságú Azure szolgáltatásokkal.

* **A gazdacsomópont virtuális IP-címe:** A virtuális gazdagép IP-címe (168.63.129.16) biztosítja az olyan alapvető infrastruktúra szolgáltatásokat, mint a DHCP, a DNS és az állapotfigyelés. Ez a nyilvános IP-cím a Microsofthoz tartozik, és minden régióban ezt a virtualizált IP-címet fogják használni erre a célra. Az IP-cím a virtuális gépet üzemeltető kiszolgálógép (gazdacsomópont) fizikai IP-címét képezi le. Ez a gazdacsomópont látja el a DHCP-továbbító, a rekurzív DNS-feloldó, valamint vizsgálati forrás szerepét a terheléselosztó és a gép állapotmintáihoz. Az IP-címmel folytatott kommunikáció nem minősül támadásnak.
* **Licencelés (Kulcskezelő szolgáltatás):** A virtuális gépeken futó Windows-rendszerképeket licencelni kell. Ehhez el kell küldeni egy licencelési kérelmet a kulcskezelő szolgáltatás ilyen kérelmeket kezelő kiszolgálóinak. Ez mindig az 1688-as kimenő porton lesz.

### <a name="icmp-traffic"></a>ICMP-forgalom
A jelenlegi NSG-szabályok csak a *TCP* és az *UDP* protokollokat engedélyezik. Az *ICMP*-nek nincs külön címkéje. A virtuális hálózatokon belül azonban a bejövő VNet szabály (65000-es alapértelmezett bejövő szabály) alapértelmezés szerint engedélyezi az ICMP-forgalmat, amely lehetővé teszi a bármely portról és protokollról érkező, illetve oda irányuló forgalmat a VNeten belül.

### <a name="subnets"></a>Alhálózatok
* Vegye figyelembe a számítási feladat által igényelt rétegek számát. Minden réteget el lehet különíteni egy alhálózattal, amelyre alkalmazva van egy NSG. 
* Ha meg kell valósítania egy alhálózatot egy VPN-átjáró vagy ExpressRoute-kör számára, semmiképpen **NE** alkalmazzon NSG-t arra az alhálózatra. Ha mégis így tenne, a VNetek vagy helyszínek közötti kapcsolat nem fog működni.
* Ha egy virtuális készüléket kell megvalósítania, a virtuális készüléket mindenképpen saját alhálózatba helyezze üzembe, hogy a felhasználó által megadott útvonalak (UDR-ek) megfelelően tudnak működni. Megvalósíthat egy alhálózatszintű NSG-t is az alhálózat be- és kifelé irányuló forgalmának szűrésére. Tudjon meg többet arról, [hogyan lehet irányítani az adatforgalmat és hogyan kell használni a virtuális készülékeket](virtual-networks-udr-overview.md).

### <a name="load-balancers"></a>Terheléselosztók
* Vegyük példaként a terheléselosztási és a NAT-szabályokat a számítási feladatok által használt terheléselosztónál. A szabályok egy háttérkészlethez vannak kötve, ami hálózati adaptereket (Resource Manager-üzemelő példányok) vagy virtuális gépeket/szerepkörpéldányokat (klasszikus üzembe helyezés) tartalmaz. Érdemes létrehozni egy NSG-t minden egyes háttérkészlethez, így a terheléselosztókban csak a megvalósított szabályok által leképezett forgalom lesz engedélyezve. Ez garantálja, hogy szűrve lesz a háttérkészletbe közvetlenül érkező forgalom, ami nem halad át a terheléselosztón.
* A klasszikus üzembe helyezés során végpontokat hoz létre, amelyek leképezik a terheléselosztón található portokat a virtuális gépekre vagy szerepkörpéldányokra. Emellett a Resource Manager üzembe helyezés során egyéni, nyilvános terheléselosztót is létrehozhat. Ha a forgalmat olyan virtuális gépekre és szerepkörpéldányokra korlátozza, amelyek az NSG-k használatával egy terheléselosztó háttérkészletének részeivé válnak, ne feledje, hogy a bejövő forgalom célportja egyben a virtuális gép vagy szerepkörpéldány tényleges portja, nem pedig a terheléselosztó által elérhetővé tett port. Azt is vegye figyelembe, hogy a forrásport és a virtuális géppel való kapcsolat címe az interneten található távoli számítógép egy portjával és egy címével egyezik, nem pedig a terheléselosztó által elérhetővé tett porttal és címmel.
* A nyilvános terheléselosztókhoz hasonlóan amikor azért hoz létre NSG-ket, hogy a belső terheléselosztón (ILB) keresztülhaladó forgalmat szűrjék, tisztában kell lennie azzal, hogy az alkalmazott forrásport és címtartomány a hívást küldő számítógépével egyezik meg, nem pedig a terheléselosztóéval. A célport és a címtartomány nem a terheléselosztóhoz, hanem a forgalmat fogadó számítógéphez kapcsolódik.

### <a name="other"></a>Egyéb
* A végpont-alapú ACL-ek és NSG-k nem támogatottak ugyanazon a kiszolgálón. Ha használni szeretne egy NSG-t, és már be van állítva egy végponti ACL, először el kell távolítani a végponti ACL-t. Ennek módjáról a [Manage endpoint ACLs](virtual-networks-acl-powershell.md) (Végponti ACL-ek kezelése) szakaszban talál információt.
* A Resource Manager üzembe helyezési modellben a hálózati adapter általi kezelés (távelérés) engedélyezéséhez egy hálózati adapterhez társított NSG-t több hálózati adapterrel rendelkező virtuális gépekkel is használhat, elkülönítve ezzel a forgalmat.
* A terheléselosztók használatához hasonlóan a más VNetekről érkező forgalom szűrésénél a távoli gép forráscímtartományát kell használni, nem a VNeteket összekötő átjárót.
* Sok Azure-szolgáltatást nem lehet csatlakoztatni az Azure virtuális hálózataihoz, és ezért a feléjük irányuló, illetve tőlük érkező forgalmat nem lehet NSG-kkel szűrni.  Olvassa el a használatban lévő szolgáltatások dokumentációját annak megállapítására, hogy lehet-e őket VNetekhez csatlakoztatni.

## <a name="sample-deployment"></a>Üzembe helyezési minta
A cikkben leírtak alkalmazásának szemléltetéséhez NSG-ket fogunk meghatározni a hálózati forgalom szűrésére egy kétrétegű számítási feladatmegoldáshoz az alábbi követelményekkel:

1. Az előtér (Windows webkiszolgálók) és háttér (SQL Database-kiszolgálók) közötti forgalmat el kell különíteni.
2. A terheléselosztási szabályok a forgalmat terheléselosztóra, majd a 80-as porton található összes webkiszolgálóra továbbítják.
3. A NAT-szabályok az 50001-es portról érkező forgalmat a terheléselosztóra, majd a 3389-es porton található egyetlen előtérbeli virtuális gépére történő továbbítják.
4. Nem lehet hozzáférni az internetről az előtérben vagy háttérben lévő virtuális gépekhez, az 1. számú követelmény kivételével.
5. Nem lehet hozzáférni az internethez az előtérből és a háttérből.
6. A 3389-es port bármely előtérben lévő webkiszolgálón elérhető az előtérben lévő alhálózatról érkező forgalom számára.
7. A 3389-es port az összes háttérben lévő SQL Server virtuális gépen elérhető az előtérben lévő alhálózat számára, és csak ezen alhálózat számára.
8. A 1433-as port az összes háttérben lévő SQL Server virtuális gépen elérhető az előtérben lévő alhálózat számára, és csak ezen alhálózat számára.
9. A felügyeleti forgalom (3389-es port) elkülönül az adatbázis-forgalomtól (1433) a háttérben lévő virtuális gépek különböző hálózati adapterein.

![NSG-k](./media/virtual-network-nsg-overview/figure1.png)

A fenti diagramon látható módon a *Web1* és a *Web2* virtuális gépek a *FrontEnd* alhálózathoz csatlakoznak, a *DB1* és *DB2* virtuális gépek pedig a *BackEnd* alhálózathoz.  Mindkét alhálózat a *TestVNet* virtuális hálózat része. Minden erőforrás az *USA nyugati régiója* Azure-régióhoz van hozzárendelve.

A fent látható 1–6 követelmény (a 3. kivételével) alhálózati területekre korlátozódik. Az egyes NSG-khez szükséges szabályok számának minimalizálása, illetve a további virtuális gépek a meglévő virtuális gépekkel azonos számítási feladattípust futtató alhálózatokhoz való hozzáadásának megkönnyítése érdekében az alábbi alhálózat-szintű NSG-ket lehet megvalósítani.

### <a name="nsg-for-frontend-subnet"></a>NSG a FrontEnd alhálózathoz
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HTTP engedélyezése |Engedélyezés |100 |INTERNET |\* |\* |80 |TCP |
| RDP engedélyezése a FrontEnd alhálózatból |Engedélyezés |200 |192.168.1.0/24 |\* |\* |3389 |TCP |
| internetről érkező csomagok megtagadása |Megtagadás |300 |INTERNET |\* |\* |\* |TCP |

**Kimenő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| internetforgalom megtagadása |Megtagadás |100 |\* |\* |INTERNET |\* |\* |

### <a name="nsg-for-backend-subnet"></a>NSG a BackEnd alhálózathoz
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| internetforgalom megtagadása |Megtagadás |100 |INTERNET |\* |\* |\* |\* |

**Kimenő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| internetforgalom megtagadása |Megtagadás |100 |\* |\* |INTERNET |\* |\* |

### <a name="nsg-for-single-vm-nic-in-frontend-for-rdp-from-internet"></a>NSG egyetlen FrontEnd alhálózaton belüli virtuális géphez (hálózati adapterhez) az internetről érkező RDP számára
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| internetről érkező RDP engedélyezése |Engedélyezés |100 |INTERNET |* |\* |3389 |TCP |

> [!NOTE]
> Figyelje meg, hogy a szabály forráscímtartománya **Internet**, és nem a terheléselosztó virtuális IP-címe, a forrásport pedig **\***, nem 500001. Ne keverje össze a NAT-szabályokat/terheléselosztási szabályokat és az NSG-szabályokat. Az NSG-szabályok mindig a forgalom eredeti forrásához és végső célhelyéhez kapcsolódnak, **NEM** a kettő között lévő terheléselosztóhoz. 
> 
> 

### <a name="nsg-for-management-nics-in-backend"></a>NSG a háttérbeli felügyeleti hálózati adapterekhez
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| előtérből érkező RDP engedélyezése |Engedélyezés |100 |192.168.1.0/24 |* |\* |3389 |TCP |

### <a name="nsg-for-database-access-nics-in-back-end"></a>NSG a háttérbeli adatbázis-hozzáférési hálózati adapterekhez
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| előtérből érkező SQL engedélyezése |Engedélyezés |100 |192.168.1.0/24 |* |\* |1433 |TCP |

Mivel a fenti NSG-k közül néhányat különálló hálózati adapterekhez kell társítani, ezt a forgatókönyvet Resource Manager-telepítésként kell üzembe helyezni. Figyelje meg, hogy a szabályok egyesítve vannak az alhálózat és a hálózati adapterek szintjén, attól függően, hogy hogyan kell őket alkalmazni. 

## <a name="next-steps"></a>Következő lépések
* [Deploy NSGs in the classic deployment model](virtual-networks-create-nsg-classic-ps.md) (NSG-k telepítése a klasszikus üzembe helyezési modellel).
* [Deploy NSGs in Resource Manager](virtual-networks-create-nsg-arm-pportal.md) (NSG-k üzembe helyezése a Resource Manager eszközzel).
* [Manage NSG logs](virtual-network-nsg-manage-log.md) (NSG-naplók kezelése).

