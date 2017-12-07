---
title: "Hálózati biztonsági csoportok az Azure-ban | Microsoft Docs"
description: "Ismerje meg, miként különítheti el és irányíthatja a forgalmat virtuális hálózatán belül a hálózati biztonsági csoportokra épülő Azure elosztott tűzfal használatával."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 20e850fc-6456-4b5f-9a3f-a8379b052bc9
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: 726799e5d885f144d6e24ab88aaa022f95f0bdd8
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="filter-network-traffic-with-network-security-groups"></a>Hálózati forgalom szűrése hálózati biztonsági csoportokkal

A hálózati biztonsági csoport (NSG) egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a hálózati forgalmat az Azure-alapú virtuális hálózatokhoz (VNet-ekhez) csatlakozó erőforrásoknak. Az NSG-k társíthatóak alhálózatokhoz, egyedi virtuális gépekhez (klasszikus) vagy virtuális gépekhez (Resource Manager) kapcsolt hálózati adapterekhez (NIC). Ha az NSG-t hozzárendelik egy alhálózathoz, a szabályok érvényesek lesznek az alhálózathoz csatlakozó összes erőforrásra. A forgalom tovább korlátozható egy NGS-t virtuális géphez vagy hálózati adapterhez társításával.

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). A jelen cikk mindkét modell használatát bemutatja, de a Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja.

## <a name="nsg-resource"></a>NSG-erőforrás
Az NSG-k az alábbi tulajdonságokat tartalmazzák:

| Tulajdonság | Leírás | Korlátozások | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Név |Az NSG neve |Egyedinek kell lennie a régión belül.<br/>Betűket, számokat, aláhúzásjeleket, pontokat és kötőjeleket tartalmazhat.<br/>Betűvel vagy számmal kell kezdődnie.<br/>Betűvel, számmal vagy aláhúzásjellel kell végződnie.<br/>Nem lehet hosszabb 80 karakternél. |Mivel előfordulhat, hogy több NSG-t kell létrehoznia, győződjön meg arról, hogy van egy elnevezési konvenciója, amellyel könnyedén azonosíthatja az NSG-k funkcióját. |
| Régió |Az Azure-[régió](https://azure.microsoft.com/regions), ahol az NSG létrejön. |Az NSG-ket csak az NSG-kkel megegyező régióban található erőforrásokra lehet alkalmazni. |A régiónként elérhető NSG-k számáról az [Azure korlátairól](../azure-subscription-service-limits.md#virtual-networking-limits-classic) szóló cikkben olvashat.|
| Erőforráscsoport |Az [erőforráscsoport](../azure-resource-manager/resource-group-overview.md#resource-groups), amelyben az NSG található. |Az NSG-k egy adott erőforráscsoportban találhatók, azonban bármely erőforráscsoport erőforrásaihoz társíthatók, feltéve, hogy az erőforrás ugyanahhoz az Azure-régióhoz tartozik, mint az NSG. |Az erőforráscsoportokkal több erőforrást kezelnek egyszerre, egy üzembe helyezési egységként.<br/>Érdemes az NSG-ket egy csoportba tenni azokkal az erőforrásokkal, amelyekhez társítva vannak. |
| Szabályok |Az engedélyezett vagy tiltott forgalmat meghatározó bejövő vagy kimenő szabályok. | |Lásd az [NSG-szabályokkal](#Nsg-rules) kapcsolatos részt ebben a cikkben. |

> [!NOTE]
> A végpont-alapú ACL-ek és hálózati biztonsági csoportok nem támogatottak ugyanazon a virtuálisgép-példányon. Ha használni szeretne egy NSG-t, és már be van állítva egy végponti ACL, először el kell távolítani a végponti ACL-t. Az ACL-ek eltávolításával kapcsolatban olvassa el [a hozzáférés-vezérlési listák a végpontoknál, a PowerShell-lel való kezelésével](virtual-networks-acl-powershell.md) kapcsolatos cikket.
> 

### <a name="nsg-rules"></a>NSG-szabályok
Az NSG-szabályok az alábbi tulajdonságokat tartalmazzák:

| Tulajdonság | Leírás | Korlátozások | Megfontolások |
| --- | --- | --- | --- |
| **Name (Név)** |A szabály neve. |Egyedinek kell lennie a régión belül.<br/>Betűket, számokat, aláhúzásjeleket, pontokat és kötőjeleket tartalmazhat.<br/>Betűvel vagy számmal kell kezdődnie.<br/>Betűvel, számmal vagy aláhúzásjellel kell végződnie.<br/>Nem lehet hosszabb 80 karakternél. |Egy NSG-n belül több szabály is lehet, ezért győződjön meg arról, hogy van egy elnevezési konvenciója, amellyel azonosíthatja a szabályok funkcióját. |
| **Protocol (Protokoll)** |A szabálynak megfelelő protokoll. |TCP, UDP vagy * |A * protokollként történő használata tartalmazza az ICMP-t (csak a kiszolgálók közötti forgalom), valamint az UDP-t és a TCP-t, és csökkentheti a szükséges szabályok számát.<br/>A * használata azonban túl széles körű lehet, ezért ügyeljen arra, hogy csak szükség esetén használja. |
| **Source port range (Forrásporttartomány)** |A szabálynak megfelelő forrásporttartomány. |Egy portszám 1 és 65535 között, egy porttartomány (például 1–65535) vagy * (minden porthoz). |A forrásportok rövid élettartamúak is lehetnek. Ha az ügyfélprogram nem egy adott portot használ, a legtöbb esetben a * jelet használja.<br/>A lehető legtöbb esetben használjon porttartományokat, hogy ne legyen szükség több szabályra.<br/>Több portot vagy porttartományt nem lehet vesszővel csoportosítani. |
| **Destination port range (Célporttartomány)** |A szabálynak megfelelő célporttartomány. |Egy portszám 1 és 65535 között, egy porttartomány (pl. 1–65535) vagy \* (minden porthoz). |A lehető legtöbb esetben használjon porttartományokat, hogy ne legyen szükség több szabályra.<br/>Több portot vagy porttartományt nem lehet vesszővel csoportosítani. |
| **Source address prefix (Forráscímelőtag)** |A szabálynak megfelelő forráscím-előtag vagy címke. |Egy IP-cím (pl. 10.10.10.10), IP-alhálózat (pl. 192.168.1.0/24), [alapértelmezett címke](#default-tags) vagy * (minden címhez). |Érdemes lehet tartományokat, alapértelmezett címkéket és a * jelet használni a szabályok számának csökkentéséhez. |
| **Destination address prefix (Célcímelőtag)** |A szabálynak megfelelő célcím-előtag vagy címke. | Egy IP-cím (pl. 10.10.10.10), IP-alhálózat (pl. 192.168.1.0/24), [alapértelmezett címke](#default-tags) vagy * (minden címhez). |Érdemes lehet tartományokat, alapértelmezett címkéket és a * jelet használni a szabályok számának csökkentéséhez. |
| **Direction (Irány)** |Az a forgalomirány, amely megfelel a szabálynak. |Bejövő vagy kimenő. |A bejövő vagy kimenő szabályokat a rendszer külön dolgozza fel, az irány alapján. |
| **Priority (Prioritás)** |A szabályokat a rendszer prioritás szerinti sorrendben ellenőrzi. Amint talál egy érvényes szabályt, nem vizsgálja, hogy a többi szabálynak megfelel-e a forgalom. | 100 és 4096 közötti szám. | A prioritások meghatározásakor érdemes 100-as lépésközt használni, hogy a jövőben esetlegesen létrejövő új szabályoknak is legyen hely a meglévő szabályok között. |
| **Access (Hozzáférés)** |Az alkalmazandó hozzáférés típusa, ha a csomag megfelel a szabálynak. | Engedélyezés vagy megtagadás. | Ne feledje, hogy ha egy csomag egyik szabálynak sem felel meg, akkor a rendszer eldobja a csomagot. |

Az NSG-k két szabálykészletet tartalmaznak: bejövőt és kimenőt. A szabály prioritásának az egyes készleten belül kell egyedinek lennie. 

![Az NSG-szabály feldolgozása](./media/virtual-network-nsg-overview/figure3.png) 

Az előző ábrán az NSG-szabályok feldolgozásának folyamata látható.

### <a name="default-tags"></a>Alapértelmezett címkék
Az alapértelmezett címkék olyan rendszer által biztosított azonosítók, amelyek az IP-címek egy kategóriáját célozzák meg. Az alapértelmezett címkéket a szabályok tulajdonságainak **forráscím-előtagjában** és **célcím-előtagjában** lehet használni. Háromféle alapértelmezett címkét lehet használni:

* **VirtualNetwork** (Resource Manager) (**VIRTUAL_NETWORK** klasszikus telepítéshez): Ez a címke tartalmazza a virtuális hálózat címterét (az Azure-ban meghatározott CIDR-tartományok), valamint az összes csatlakoztatott helyszíni címteret és a csatlakoztatott Azure virtuális hálózatokat (helyi hálózatokat).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** klasszikus telepítéshez): Ez a címke az Azure infrastruktúra terheléselosztóját jelöli. A címkét a rendszer lefordítja arra az Azure-adatközponti IP-címre, ahonnan az Azure állapot-mintavételei származnak.
* **Internet** (Resource Manager) (**INTERNET** klasszikus telepítéshez): Ez a címke azt az IP-címteret jelöli, amely a virtuális hálózaton kívül esik, és a nyilvános interneten érhető el. A tartományba beletartozik az [Azure tulajdonában lévő nyilvános IP-címtér](https://www.microsoft.com/download/details.aspx?id=41653) is.

### <a name="default-rules"></a>Alapértelmezett szabályok
Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat. 

Az alapértelmezett szabályok az alábbiak szerint engedélyezik és tiltják le a forgalmat:
- **Virtuális hálózat:** A virtuális hálózatból kiinduló és oda érkező forgalom a bejövő és kimenő irányban is engedélyezve van.
- **Internet:** A kimenő forgalom engedélyezett, de a bejövő forgalom le van tiltva.
- **Terheléselosztó:** Engedélyezi az Azure terheléselosztója számára, hogy megvizsgálja a virtuális gépek és a szerepkörpéldányok állapotát. Ha nem terheléselosztásos készletet használ, ezt a szabályt felül lehet bírálni.

**Bejövő alapértelmezett szabályok**

| Név | Prioritás | Forrás IP-címe | Forrásport | Cél IP-címe | Célport | Protokoll | Hozzáférés |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVNetInBound |65000 | VirtualNetwork | * | VirtualNetwork | * | * | Engedélyezés |
| AllowAzureLoadBalancerInBound | 65001 | AzureLoadBalancer | * | * | * | * | Engedélyezés |
| DenyAllInBound |65500 | * | * | * | * | * | Megtagadás |

**Kimenő alapértelmezett szabályok**

| Név | Prioritás | Forrás IP-címe | Forrásport | Cél IP-címe | Célport | Protokoll | Hozzáférés |
| --- | --- | --- | --- | --- | --- | --- | --- |
| AllowVnetOutBound | 65000 | VirtualNetwork | * | VirtualNetwork | * | * | Engedélyezés |
| AllowInternetOutBound | 65001 | * | * | Internet | * | * | Engedélyezés |
| DenyAllOutBound | 65500 | * | * | * | * | * | Megtagadás |

## <a name="associating-nsgs"></a>Az NSG-k társítása
Az NSG-ket virtuális gépekhez, hálózati adapterekhez és alhálózatokhoz is társíthatja attól függően, hogy milyen üzembe helyezési modellt használ, a következők szerint:

* **VM (csak klasszikus modell):** A biztonsági szabályok a virtuális gép teljes bejövő és kimenő forgalmára érvényesek. 
* **NIC (csak Resource Manager-alapú modell):** A biztonsági szabályok az NSG-hez társított hálózati adapter teljes bejövő és kimenő forgalmára érvényesek. Egy több hálózati adapterrel rendelkező virtuális gép esetében minden egyes hálózati adapterhez rendelhet eltérő NSG-t (vagy akár ugyanazt is). 
* **Alhálózat (Resource Manager-alapú és klasszikus modell):** A biztonsági szabályok az összes, a virtuális hálózathoz csatlakozó erőforrás teljes bejövő és kimenő forgalmára érvényesek.

Különböző NSG-ket társíthat egy virtuális géphez (vagy hálózati adapterhez, a üzembe helyezési modelltől függően) és az alhálózathoz, amelyhez csatlakoztatva van egy hálózati adapter vagy egy virtuális gép. A rendszer a biztonsági szabályokat prioritás szerint alkalmazza a forgalomra az egyes NSG-kben, a következő sorrendben:

- **Bejövő forgalom**

  1. **Alhálózatra alkalmazott NSG:** Ha egy alhálózati NSG rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer elveti.

  2. **Hálózati adapterre alkalmazott NSG** (Resource Manager-alapú modell) vagy virtuális gépre alkalmazott NSG (klasszikus modell): Ha a VM\hálózati adapter NSG-je rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer elveti, még akkor is, ha az alhálózati NSG rendelkezik a forgalmat engedélyező megfelelő szabállyal.

- **Kimenő forgalom**

  1. **Hálózati adapterre alkalmazott NSG** (Resource Manager-alapú modell) vagy virtuális gépre alkalmazott NSG (klasszikus modell): Ha a VM\hálózati adapter NSG-je rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer elveti.

  2. **Alhálózatra alkalmazott NSG**: Ha az alhálózati NSG rendelkezik a forgalom megtagadásához szükséges megfelelő szabállyal, a csomagot a rendszer elveti, még akkor is, ha egy VM\hálózati adapter NSG-je rendelkezik is a forgalmat engedélyező megfelelő szabállyal.

> [!NOTE]
> Bár egy alhálózathoz, virtuális géphez vagy hálózati adapterhez csak egy NSG-t lehet társítani, ugyanazt az NSG-t korlátlan számú erőforráshoz hozzá lehet rendelni.
>

## <a name="implementation"></a>Megvalósítás
Az NSG-ket a klasszikus és a Resource Manager-alapú üzemi modellel is meg lehet valósítani az alábbi eszközökkel:

| Üzembe helyezési eszköz | Klasszikus | Resource Manager |
| --- | --- | --- |
| Azure Portal   | Nem | [Igen](virtual-networks-create-nsg-arm-pportal.md) |
| PowerShell     | [Igen](virtual-networks-create-nsg-classic-ps.md) | [Igen](virtual-networks-create-nsg-arm-ps.md) |
| Azure CLI **V1**   | [Igen](virtual-networks-create-nsg-classic-cli.md) | [Igen](virtual-networks-create-nsg-arm-cli.md) |
| Azure CLI **V2**   | Nem | [Igen](virtual-networks-create-nsg-arm-cli.md) |
| Azure Resource Manager-sablon   | Nem  | [Igen](virtual-networks-create-nsg-arm-template.md) |

## <a name="planning"></a>Tervezés
Az NSG-k megvalósítása előtt válaszolnia kell az alábbi kérdésekre:

1. Milyen típusú erőforrások bejövő és kimenő forgalmát szeretné szűrni? Csatlakoztathat erőforrásokat, például hálózati adaptereket (Resource Manager-alapú modell), virtuális gépeket (klasszikus modell), felhőszolgáltatásokat, alkalmazásszolgáltatási környezeteket és virtuálisgép-méretezési csoportokat. 
2. Az erőforrások, amelyek bejövő/kimenő forgalmát szűrni kívánja, meglévő virtuális hálózatok alhálózataihoz csatlakoznak?

A hálózati biztonság Azure-ban történő megtervezésével kapcsolatos további információkért olvassa el [a felhőszolgáltatásokkal és a hálózati biztonsággal](../best-practices-network-security.md) kapcsolatos cikket. 

## <a name="design-considerations"></a>Kialakítási szempontok
Ha megválaszolta a [Tervezés](#Planning) szakasz kérdéseit, tekintse át az alábbiakat az NSG-k meghatározása előtt:

### <a name="limits"></a>Korlátok
Előfizetésenként korlátozott számú NSG-t használhat és NSG-nként korlátozott számú szabályt. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-subscription-service-limits.md#networking-limits) szóló cikket.

### <a name="vnet-and-subnet-design"></a>VNet és alhálózat kialakítása
Mivel az NSG-ket alhálózatokra is lehet alkalmazni, minimalizálhatja az NSG-k számát azzal, hogy alhálózat szerint csoportosítja az erőforrásokat, és az alhálózatokra alkalmazza az NSG-ket.  Ha úgy dönt, hogy NSG-ket alkalmaz az alhálózatokra, azt tapasztalhatja, hogy a meglévő VNetek és alhálózatok meghatározásánál nem vették figyelembe az NSG-ket. Előfordulhat, hogy az NSG-rendszer támogatásához új VNet-eket és alhálózatokat kell meghatároznia és új alhálózataihoz új erőforrásokat kell üzembe helyeznie. Ezt követően meghatározhatja az áttelepítési stratégiát a meglévő erőforrások új alhálózatokra történő áthelyezésére. 

### <a name="special-rules"></a>Különleges szabályok
Ha letiltja a következő szabályok által engedélyezett forgalmat, infrastruktúrája nem tud majd kommunikálni alapvető Azure-szolgáltatásokkal:

* **A gazdacsomópont virtuális IP-címe:** A virtuális gazdagép IP-címe (168.63.129.16) biztosítja az olyan alapvető infrastruktúra szolgáltatásokat, mint a DHCP, a DNS és az állapotfigyelés. Ez a nyilvános IP-cím a Microsofthoz tartozik, és minden régióban ezt a virtualizált IP-címet fogják használni erre a célra. Az IP-cím a virtuális gépet üzemeltető kiszolgálógép (gazdacsomópont) fizikai IP-címére van leképezve. Ez a gazdacsomópont látja el a DHCP-továbbító, a rekurzív DNS-feloldó, valamint vizsgálati forrás szerepét a terheléselosztó és a gép állapotmintáihoz. Az IP-címmel folytatott kommunikáció nem minősül támadásnak.
* **Licencelés (Kulcskezelő szolgáltatás):** A virtuális gépeken futó Windows-rendszerképeket licencelni kell. A licenceléshez el kell küldeni egy licencelési kérelmet a Kulcskezelő szolgáltatás ilyen kérelmeket kezelő kiszolgálóinak. A kérelmet az 1688-as kimenő porton küldi el a rendszer.

### <a name="icmp-traffic"></a>ICMP-forgalom
A jelenlegi NSG-szabályok csak a *TCP* és az *UDP* protokollokat engedélyezik. Az *ICMP*-nek nincs külön címkéje. Azonban a virtuális hálózatokon belül az ICMP-forgalmat az AllowVNetInBound alapértelmezett szabály engedélyezi, amely engedélyezi a virtuális hálózaton belüli portok és protokollok minden bejövő és kimenő forgalmát.

### <a name="subnets"></a>Alhálózatok
* Vegye figyelembe a számítási feladat által igényelt rétegek számát. Minden réteget el lehet különíteni egy alhálózattal, amelyre alkalmazva van egy NSG. 
* Ha meg kell valósítania egy alhálózatot egy VPN-átjáró vagy ExpressRoute-kapcsolatcsoport számára, **ne** alkalmazzon NSG-t arra az alhálózatra. Ha mégis így tesz, virtuális hálózatok vagy létesítmények közötti kapcsolatok megszakadhatnak. 
* Ha meg kell valósítania egy hálózati virtuális készüléket (NVA-t), csatlakoztassa az NVA-t a saját alhálózatához, és hozzon létre felhasználó által megadott útvonalakat (UDR) az NVA felé. Megvalósíthat egy alhálózatszintű NSG-t is az alhálózat be- és kifelé irányuló forgalmának szűrésére. A felhasználó által megadott útvonalakkal (UDR-ekkel) kapcsolatban további információt a [felhasználó által megadott útvonalakkal](virtual-networks-udr-overview.md) kapcsolatos cikkben olvashat.

### <a name="load-balancers"></a>Terheléselosztók
* Vegyük példaként a terheléselosztási és a hálózati címfordítási (NAT-) szabályokat a számítási feladatok által használt terheléselosztóknál. A NAT-szabályok egy háttérkészlethez vannak kötve, amely hálózati adaptereket (Resource Manager-alapú modell) vagy virtuális gépeket/felhőszolgáltatásiszerepkör-példányokat (klasszikus modell) tartalmaz. Érdemes létrehozni egy NSG-t minden egyes háttérkészlethez, így a terheléselosztókban csak a megvalósított szabályok által leképezett forgalom lesz engedélyezve. Ha létrehoz egy-egy NSG-t minden háttérkészlet számára, azzal biztosítja, hogy a háttérkészletbe közvetlenül (nem a terheléselosztón keresztül) érkező forgalom is szűrve lesz.
* A klasszikus üzembe helyezés során végpontokat hoz létre, amelyek leképezik a terheléselosztón található portokat a virtuális gépekre vagy szerepkörpéldányokra. Emellett a Resource Managerrel létrehozhatja saját önálló, nyilvános terheléselosztóját. A bejövő forgalom célportja egyben a virtuális gép vagy szerepkörpéldány tényleges portja, nem a terheléselosztó által elérhetővé tett port. A forrásport és a virtuális géppel való kapcsolat címe az interneten található távoli számítógép portjával és címével egyezik, nem a terheléselosztó által elérhetővé tett port és cím.
* Amikor azért hoz létre NSG-ket, hogy azok a belső terheléselosztón (ILB) keresztüli forgalmat szűrjék, az alkalmazott forrásport és címtartomány a forrásszámítógépről származik, nem a terheléselosztóról. A célport és a címtartomány nem a terheléselosztóhoz, hanem a célszámítógéphez tartozik.

### <a name="other"></a>Egyéb
* Végpontalapú hozzáférés-vezérlési listák (ACL-ek) és NSG-k használata nem támogatott ugyanazon a virtuálisgép-példányon. Ha használni szeretne egy NSG-t, és már be van állítva egy végponti ACL, először el kell távolítani a végponti ACL-t. Végponti ACL-ek eltávolításával kapcsolatos információkért tekintse meg a [végponti ACL-ek kezelésével](virtual-networks-acl-powershell.md) kapcsolatos cikket.
* A Resource Managerben a hálózati adapterenkénti felügyelet (távoli elérés) lehetővé tételéhez használhat olyan NSG-t, amely több hálózati adapterrel rendelkező virtuális gép hálózati adapteréhez van hozzárendelve. Ha minden hálózati adapterhez egyedi NSG-t társít, azzal lehetővé teszi a hálózati adaptereken keresztüli forgalom típusainak elkülönítését.
* A terheléselosztók használatához hasonlóan a más VNetekről érkező forgalom szűrésénél a távoli gép forráscímtartományát kell használni, nem a VNeteket összekötő átjárót.
* Számos Azure-szolgáltatást nem lehet csatlakoztatni virtuális hálózatokhoz. Ha egy Azure-erőforrás nem csatlakozik virtuális hálózathoz, nem használhat NSG-t az erőforrás felé irányuló forgalom szűrésére.  Olvassa el az Ön által használt szolgáltatások dokumentációját annak megállapítására, hogy lehet-e őket virtuális hálózatokhoz csatlakoztatni.

## <a name="sample-deployment"></a>Üzembe helyezési minta
A cikkben leírtak alkalmazásának szemléltetéséhez tekintsük át egy kétszintű alkalmazás gyakori forgatókönyvét, amely az alábbi képen látható:

![NSG-k](./media/virtual-network-nsg-overview/figure1.png)

Ahogyan az az ábrán látható, a *Web1* és a *Web2* virtuális gép a *FrontEnd* alhálózathoz csatlakozik, a *DB1* és a *DB2* virtuális gép pedig a *BackEnd* alhálózathoz.  Mindkét alhálózat a *TestVNet* virtuális hálózat része. Minden egyes alkalmazás-összetevő egy virtuális hálózathoz csatlakozó Azure-beli virtuális gépen fut. A forgatókönyvre a következő követelmények vonatkoznak:

1. A WEB és a DB kiszolgálók közötti forgalom elkülönítése.
2. A terheléselosztási szabályok a forgalmat a terheléselosztótól a 80-as porton található összes webkiszolgálóra továbbítják.
3. A terheléselosztási NAT-szabályok az 50001-es porton keresztül érkező forgalmat a 3389-es porton található WEB1 virtuális gépre irányítják.
4. Az előtérbeli vagy a háttérbeli virtuális gép nem érhető el az internetről, kivéve a 2-es és 3-as követelmények esetében.
5. Nincs kimenő internetelérés a WEB vagy a DB kiszolgálókról.
6. A felhasználói réteg alhálózatáról engedélyezett bármely WEB kiszolgáló 3389-es portjának elérése.
7. A felhasználói réteg alhálózatáról engedélyezett bármely DB kiszolgáló 3389-es portjának elérése.
8. A felhasználói réteg alhálózatáról engedélyezett az összes DB kiszolgáló 1433-as portjának elérése.
9. A felügyeleti forgalom (3389-es port) elkülönül az adatbázis-forgalomtól (1433) a DB kiszolgálók különböző hálózati adapterein.

A fent látható 1–6. követelmény (a 3. és a 4. kivételével) alhálózati területekre korlátozódik. Az alábbi NSG-k felelnek meg az előbbi követelményeknek, a szükséges NSG-k számának minimalizálásával:

### <a name="frontend"></a>Előtér
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-HTTP-Internet | Engedélyezés | 100 | Internet | * | * | 80 | TCP |
| Allow-Inbound-RDP-Internet | Engedélyezés | 200 | Internet | * | * | 3389 | TCP |
| Deny-Inbound-All | Megtagadás | 300 | Internet | * | * | * | TCP |

**Kimenő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All |Megtagadás |100 | * | * | Internet | * | * |

### <a name="backend"></a>Háttér
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Megtagadás | 100 | Internet | * | * | * | * |

**Kimenő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Internet-All | Megtagadás | 100 | * | * | Internet | * | * |

Az alábbi NSG-k jönnek létre, és a rendszer hálózati adapterekhez társítja őket a következő virtuális gépeken:

### <a name="web1"></a>WEB1
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Internet | Engedélyezés | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Engedélyezés | 200 | Internet | * | * | 80 | TCP |

> [!NOTE]
> Az előbbi szabályok forráscímtartománya **Internet**, nem a terheléselosztó virtuális IP-címe. A forrásport *, nem 500001. A terheléselosztókra vonatkozó NAT-szabályok nem egyeznek meg az NSG biztonsági szabályaival. Az NSG biztonsági szabályai mindig a forgalom eredeti forrásához és végső célhelyéhez kapcsolódnak, **nem** a kettő között lévő terheléselosztóhoz. 
> 
> 

### <a name="web2"></a>WEB2
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Deny-Inbound-RDP-Internet | Megtagadás | 100 | Internet | * | * | 3389 | TCP |
| Allow-Inbound-HTTP-Internet | Engedélyezés | 200 | Internet | * | * | 80 | TCP |

### <a name="db-servers-management-nic"></a>DB kiszolgálók (felügyeleti hálózati adapter)
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-RDP-Front-end | Engedélyezés | 100 | 192.168.1.0/24 | * | * | 3389 | TCP |

### <a name="db-servers-database-traffic-nic"></a>DB kiszolgálók (adatbázis-forgalmi hálózati adapter)
**Bejövő szabályok**

| Szabály | Hozzáférés | Prioritás | Forráscímtartomány | Forrásport | Célcímtartomány | Célport | Protokoll |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Allow-Inbound-SQL-Front-end | Engedélyezés | 100 | 192.168.1.0/24 | * | * | 1433 | TCP |

Mivel néhány NSG önálló hálózati adapterhez van hozzárendelve, a szabályok a Resource Managerrel üzembe helyezett erőforrásokra érvényesek. A szabályokat egyesíti a rendszer az alhálózat és a hálózati adapterek vonatkozásában, attól függően, hogyan vannak társítva. 

## <a name="next-steps"></a>Következő lépések
* [NSG-k üzembe helyezése a Resource Managerrel](virtual-networks-create-nsg-arm-pportal.md).
* [NSG-k telepítése a klasszikus modellel](virtual-networks-create-nsg-classic-ps.md).
* [Manage NSG logs](virtual-network-nsg-manage-log.md) (NSG-naplók kezelése).
* [NSG-k hibaelhárítása] (virtual-network-nsg-troubleshoot-portal.md)
