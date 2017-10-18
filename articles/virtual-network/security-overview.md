---
title: "Az Azure-hálózat biztonsági áttekintése | Microsoft Docs"
description: "Ismerkedjen meg az Azure-erőforrások közötti hálózati forgalom szabályzására szolgáló biztonsági beállításokkal."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: jdial
ms.openlocfilehash: 98559cbb0acab91c4b2c30c6d0129e955eef85f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="network-security"></a>Hálózati biztonság

A hálózati biztonsági csoportokkal korlátozhatja az egy adott virtuális hálózatban lévő erőforrásokra irányuló hálózati forgalmat. A hálózati biztonsági csoport egy biztonsági szabályokból álló listát tartalmaz, amelyek engedélyezik vagy megtagadják a bejövő vagy kimenő hálózati forgalmat a forrás vagy a cél IP-címe, illetve portok vagy protokollok alapján. 

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

Mindegyik hálózati adapter nulla vagy egy társított hálózati biztonsági csoporttal rendelkezik. Mindegyik hálózati adapter egy [virtuális hálózati](virtual-networks-overview.md) alhálózatban létezik. Az alhálózat szintén nulla vagy egy társított hálózati biztonsági csoporttal rendelkezhet. 

Az alhálózatokra alkalmazott biztonsági szabályok az adott alhálózatban minden erőforrásra vonatkoznak. A hálózati adapterek mellett más Azure-szolgáltatások, például a HDInsight, a virtuálisgép-méretezési csoportok és az alkalmazásszolgáltatási környezetek példányait is üzembe helyezheti az alhálózaton.

Ha egy hálózati biztonsági csoport egy hálózati adapterrel és az azt tartalmazó alhálózattal is társítva van, a hálózati biztonsági csoportok alkalmazása a következőképp történik:

- **Bejövő forgalom**: A rendszer először a hálózati adaptert tartalmazó alhálózathoz rendelt hálózati biztonsági csoportot értékeli ki. Ezután az alhálózathoz rendelt hálózati biztonsági csoporton átengedett forgalmat a hálózati adapterhez rendelt hálózati biztonsági csoport is kiértékeli. Tegyük fel, hogy bejövő hozzáférésre van szüksége egy virtuális géphez a 80-as porton keresztül az internetről. Ha a hálózati adapterhez és az azt tartalmazó alhálózathoz is rendel egy hálózati biztonsági csoportot, az alhálózathoz és a hálózati adapterhez rendelt hálózati biztonsági csoportoknak egyaránt engedélyeznie kell a 80-as port használatát. Ha csak a hálózati adapterhez vagy az azt tartalmazó alhálózathoz rendelt hálózati biztonsági csoporton engedélyezi a 80-as port használatát, a kommunikáció az alapértelmezett biztonsági szabályok miatt meghiúsul. Részletekért lásd az [alapértelmezett biztonsági szabályokat](#default-security-rules). Ha csak az alhálózatra vagy a hálózati adapterre alkalmazott hálózati biztonsági csoportot, és a hálózati biztonsági csoport tartalmaz például olyan szabályt, amely engedi a bejövő forgalmat a 80-as porton, a kommunikáció sikeres lesz. 
- **Kimenő forgalom**: A rendszer először a hálózati adapterhez rendelt hálózati biztonsági csoportot értékeli ki. Ezután a hálózati adapterhez rendelt hálózati biztonsági csoporton átengedett forgalmat az alhálózathoz rendelt hálózati biztonsági csoport is kiértékeli.

Nem biztos, hogy minden esetben tisztában lesz vele, ha a hálózati adapterrel és az azt tartalmazó alhálózattal is társítva van hálózati biztonsági csoport. A hálózati adapterekhez rendelt összesített szabályokat könnyen megismerheti a hálózati adapter [érvényes biztonsági szabályainak](virtual-network-manage-nsg-arm-portal.md) megtekintésével. Emellett az Azure Network Watcher [IP-forgalom ellenőrzése](../network-watcher/network-watcher-check-ip-flow-verify-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szolgáltatásával is megállapíthatja, hogy a kommunikáció engedélyezett-e a hálózati adapterre/adapterről. Az eszköz megmutatja, hogy a kommunikáció engedélyezett-e, valamint azt is, hogy melyik hálózati biztonsági szabály engedélyezi vagy blokkolja a forgalmat.
 
> [!NOTE]
> A hálózati biztonsági csoportok alhálózatokhoz vagy klasszikus üzemi modellben üzembe helyezett virtuális gépekhez és felhőszolgáltatásokhoz vannak társítva, nem pedig Resource Manager-alapú üzemi modellben üzemelő hálózati adapterekhez. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Egyazon hálózati biztonsági csoport tetszőleges számú egyéni hálózati adapterhez és alhálózathoz rendelhető.

## <a name="security-rules"></a>Biztonsági szabályok

A hálózati biztonsági csoportok nulla vagy tetszőleges számú szabályt tartalmazhatnak, az Azure-előfizetések [korlátain](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) belül. Az egyes szabályok az alábbi tulajdonságokat határozzák meg:

|Tulajdonság  |Magyarázat  |
|---------|---------|
|Név|Egy egyedi név a hálózati biztonsági csoporton belül.|
|Prioritás | Egy 100 és 4096 közötti szám. A szabályok feldolgozása prioritási sorrendben történik. Az alacsonyabb sorszámúak feldolgozása a magasabb sorszámúak előtt történik, mivel az alacsonyabb sorszámok magasabb prioritást jelölnek. Ha az adatforgalom megfelel valamelyik szabálynak, a feldolgozás leáll. Ennek eredményeképp az olyan alacsonyabb prioritású (magasabb számú) szabályokat, amelyek attribútumai megegyeznek a magasabb prioritású szabályokéival, a rendszer nem dolgozza fel.|
|Forrás vagy cél| Bármelyik, vagy egy egyéni IP-cím, CIDR-blokk (például 10.0.0.0/24), szolgáltatáscímke vagy alkalmazásbiztonsági csoport. Tudjon meg többet a [szolgáltatáscímkékről](#service-tags) és az [alkalmazásbiztonsági csoportokról](#application-security-groups). Tartományok, szolgáltatáscímkék vagy alkalmazásbiztonsági csoportok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. A több egyéni IP-cím vagy tartomány megadásának lehetősége (szolgáltatáscímkékből és alkalmazásbiztonsági csoportokból nem adható meg több) az egyes szabályokban még csak előzetes verzióként érhető el, kibővített biztonsági szabályok néven. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban nem adhat meg több IP-címet vagy -címtartományt.|
|Protokoll     | TCP, UDP, vagy Bármely, amely a TCP, az UDP és az ICMP protokollokat tartalmazza. Az ICMP önmagában nem adható meg, azért ha arra van szüksége, a Bármely lehetőséget kell alkalmaznia. |
|Irány| Megadja, hogy a szabály a bejövő vagy a kimenő adatforgalomra vonatkozik.|
|Porttartomány     |Megadhat egy egyéni portot vagy egy porttartományt is. Megadhatja például a 80-as portot vagy a 10000–10005 tartományt. Tartományok megadásával kevesebb biztonsági szabályt kell majd létrehoznia. A több egyéni port vagy tartomány megadásának lehetősége az egyes szabályokban még csak előzetes verzióként érhető el, kibővített biztonsági szabályok néven. A kibővített biztonsági szabályok használata előtt olvassa át az [Előzetes verziók](#preview-features) szakaszban foglalt fontos információkat. Kibővített biztonsági szabályok kizárólag a Resource Manager-alapú üzemi modellben létrehozott hálózati biztonsági csoportokban hozhatóak létre. A klasszikus üzemi modellben létrehozott hálózati biztonsági csoportokban egyazon szabályban nem adhat meg több portot vagy porttartományt.   |
|Műveletek     | Engedélyezés vagy letiltás        |

**Megfontolások**

- **A gazdacsomópont virtuális IP-címe**: A virtuális gazdagép IP-címei (168.63.129.16 és 169.254.169.254) biztosítják az olyan alapvető infrastruktúra szolgáltatásokat, mint a DHCP, a DNS és az állapotfigyelés. Ezek a nyilvános IP-címek a Microsofthoz tartoznak, és minden régióban ezeket a virtualizált IP-címeket fogják használni erre a célra. A címek a virtuális gépet üzemeltető kiszolgálógép (gazdacsomópont) fizikai IP-címét képezik le. Ez a gazdacsomópont látja el a DHCP-továbbító, a rekurzív DNS-feloldó, valamint vizsgálati forrás szerepét a terheléselosztó és a gép állapotmintáihoz. Az IP-címekkel folytatott kommunikáció nem minősül támadásnak. Ha blokkolja ezen IP-címek be-vagy kimenő forgalmát, valamely virtuális gép esetleg nem megfelelően fog működni.
- **Licencelés (kulcskezelő szolgáltatás)**: A virtuális gépeken futó Windows-rendszerképeket licencelni kell. A licenceléshez el kell küldeni egy licencelési kérelmet a Kulcskezelő szolgáltatás ilyen kérelmeket kezelő kiszolgálóinak. A kérelmet az 1688-as kimenő porton küldi el a rendszer.
- **Virtuális gépek az elosztott terhelésű készletekben**: Az alkalmazott forrásport és címtartomány a forrásszámítógépről származik, nem a terheléselosztóról. A célport és a címtartomány nem a terheléselosztóhoz, hanem a célszámítógéphez tartozik.
- **Azure szolgáltatáspéldányok**: Több Azure szolgáltatás, például a HDInsight, az alkalmazásszolgáltatási környezetek és a virtuálisgép-méretezési csoportok példányai is virtuális hálózati alhálózatokon vannak üzembe helyezve. Mindenképp ismerkedjen meg behatóan az egyes szolgáltatások portkövetelményeivel, mielőtt egy hálózati biztonsági csoportot alkalmazna az erőforrást üzemeltető alhálózatra. Ha a valamely szolgáltatás által használt portokat letiltja, a szolgáltatás nem megfelelően fog működni. 

A biztonsági szabályok állapotalapúak. Ha bármely címre meghatároz egy kimenő biztonsági szabályt a 80-as porton keresztül, nem szükséges biztonsági szabályt megadnia a bejövő forgalomra a válaszhoz. Ha a kommunikáció kívülről indul, csak egy bejövő biztonsági szabályt kell meghatároznia. Ennek az ellenkezője is igaz. Ha egy porton engedélyezett a bejövő forgalom, nem szükséges egy kimenő biztonsági szabályt is megadni ugyanazon a porton történő válaszadáshoz. A biztonsági szabályokra vonatkozó korlátozásokkal kapcsolatban lásd: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Kibővített biztonsági szabályok

A kibővített szabályok megkönnyítik a virtuális hálózatok biztonsági definícióinak megadását, így nagyobb és összetettebb hálózati biztonsági házirendek alakíthatóak ki kevesebb szabállyal. Több portot, több konkrét IP-címet, szolgáltatáscímkéket és alkalmazásbiztonsági csoportokat foglalhat egyetlen, könnyen érthető biztonsági szabályba. Kibővített szabályokat a szabályok forrás, cél és port mezőiben is használhat. A szabályok létrehozásakor több konkrét IP-címet, CIDR-tartományt és portot is megadhat. A biztonsági szabály definíciójának egyszerűbb karbantartása érdekében kombinálja a kibővített biztonsági szabályokat szolgáltatáscímkékkel vagy alkalmazásbiztonsági csoportokkal. 

A kibővített biztonsági szabályok előzetes verzióként érhetőek el. A kibővített biztonsági szabályok létrehozására vonatkozó korlátozásokkal kapcsolatban lásd: [Az Azure korlátai](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Az előzetes kiadásban elérhető szolgáltatások rendelkezésre állása és megbízhatósága eltér az általánosan elérhető kiadásokban lévőekétől. A szolgáltatások csak a következő régiókban érhetőek el: az USA keleti régiója, az USA nyugati régiója, az USA 2. nyugati régiója, az USA nyugati középső régiója, Kelet-Ausztrália, Délkelet-Ausztrália, valamint az Egyesült Királyság déli régiója.

## <a name="default-security-rules"></a>Alapértelmezett biztonsági szabályok

Ha egy alhálózathoz vagy hálózati adapterhez nincs hálózati biztonsági csoport rendelve, az alhálózatra vagy hálózati adapterre érkező minden bejövő és az ezekről induló minden kimenő forgalom engedélyezve lesz. A hálózati biztonsági csoport létrehozásának pillanatában az Azure a következő alapértelmezett szabályokat hozza létre a hálózati biztonsági csoportban:

### <a name="inbound"></a>Bejövő

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Hozzáférés|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Összes|Engedélyezés|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Hozzáférés|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Összes|Engedélyezés|

#### <a name="denyallinbound"></a>DenyAllInbound

|Prioritás|Forrás|Forrásportok|Cél|Célportok|Protokoll|Hozzáférés|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Összes|Megtagadás|

### <a name="outbound"></a>Kimenő

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Összes | Engedélyezés |

#### <a name="allowinternetoutbound"></a>AllowInternetOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Összes | Engedélyezés |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Prioritás|Forrás|Forrásportok| Cél | Célportok | Protokoll | Hozzáférés |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Összes | Megtagadás |

A **Forrás** és a **Cél** oszlopban a *VirtualNetwork*, *AzureLoadBalancer* és *Internet* értékek [szolgáltatáscímkék](#tags), nem IP-címek. A Protokoll oszlopban az **Összes** érték a TCP, UDP és ICMP lehetőségeket foglalja magában. Szabályok létrehozásakor választhatja a TCP, az UDP vagy az Összes lehetőséget, az ICMP-t külön azonban nem adhatja meg. Ezért ha egy szabályhoz az ICMP szükséges, az *Összes* beállítást kell megadnia a protokollnál. A *0.0.0.0/0* érték a **Forrás** és a **Cél** oszlopban az összes címet jelöli.
 
Az alapértelmezett szabályok nem távolíthatók el, azonban magasabb prioritású szabályok létrehozásával felülírhatók.

## <a name="service-tags"></a>Szolgáltatáscímkék

 A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A következő szolgáltatáscímkék érhetőek el biztonsági szabályok meghatározásához. A neveik az egyes [Azure üzembehelyezési modellekben](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) némiképp eltérőek.

* **VirtualNetwork** (*Resource Manager) (**VIRTUAL_NETWORK** klasszikus telepítéshez): Ez a címke tartalmazza a virtuális hálózat címterét (a virtuális hálózathoz meghatározott minden CIDR-tartományt), valamint az összes csatlakoztatott helyszíni címteret és a [virtuális hálózati átjárókhoz](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) csatlakoztatott virtuális hálózatokat vagy [társviszonyban álló](virtual-network-peering-overview.md) virtuális hálózatokat.
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** klasszikus telepítéshez): Ez a címke az Azure infrastruktúra terheléselosztóját jelöli. A címkét a rendszer lefordítja arra az [Azure-adatközponti IP-címre](https://www.microsoft.com/download/details.aspx?id=41653), ahonnan az Azure állapot-mintavételei származnak. Ha nem az Azure Load Balancert használja, ezt a szabályt felül lehet bírálni.
* **Internet** (Resource Manager) (**INTERNET** klasszikus telepítéshez): Ez a címke azt az IP-címteret jelöli, amely a virtuális hálózaton kívül esik, és a nyilvános interneten érhető el. A címtartományba beletartozik az [Azure tulajdonában lévő nyilvános IP-címtér](https://www.microsoft.com/download/details.aspx?id=41653) is.
* **AzureTrafficManager** (csak Resource Manager esetében): Ez a címke az Azure Traffic Manager szolgáltatás IP-címterét jelöli.
* **Storage** (csak Resource Manager esetében): Ez a címke az Azure Storage szolgáltatás IP-címterét jelöli. Ha a *Storage* értéket adja meg, a Storage szolgáltatás felé irányuló forgalom engedélyezhető vagy letiltható. Ha csak egy adott [régióban](https://azure.microsoft.com/regions) szeretné engedélyezni a hozzáférést a Storage szolgáltatáshoz, megadhat egy régiót. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure Storage szolgáltatáshoz, megadhatja a *Storage.EastUS* szolgáltatáscímkét. További elérhető regionális szolgáltatáscímkék: Storage.AustraliaEast, Storage.AustraliaSoutheast, Storage.EastUS, Storage.UKSouth, Storage.WestCentralUS, Storage.WestUS és Storage.WestUS2. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure Storage szolgáltatást jelöli, de nem egy adott Azure Storage-fiókot.
* **Sql** (csak Resource Manager esetében): Ez a címke az Azure SQL Database és az Azure SQL Data Warehouse szolgáltatás címelőtagjait jelöli. Ezen szolgáltatáscímkéhez csak adott régiók adhatóak meg. Ha például csak az USA keleti régiójában szeretné engedélyezni a hozzáférést az Azure SQL Database-hez, megadhatja az *Sql.EastUS* szolgáltatáscímkét. Nem adhatja meg egyszerűen az Sql címkét az összes Azure-régióhoz, hanem egyenként meg kell adnia a régiókat. További elérhető regionális szolgáltatáscímkék: Sql.AustraliaEast, Sql.AustraliaSoutheast, Sql.EastUS, Sql.UKSouth, Sql.WestCentralUS, Sql.WestUS és Sql.WestUS2. A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de nem egy adott Azure SQL-adatbázist.

> [!WARNING]
> Az AzureTrafficManager, a Storage és az Sql szolgáltatáscímke előzetes kiadásban érhető el. Az előzetes kiadásban elérhető szolgáltatások rendelkezésre állása és megbízhatósága eltér az általánosan elérhető kiadásokban lévőekétől. A szolgáltatáscímkék csak a következő régiókban érhetőek el: az USA keleti régiója, az USA nyugati régiója, az USA 2. nyugati régiója, az USA nyugati középső régiója, Kelet-Ausztrália, Délkelet-Ausztrália, valamint az Egyesült Királyság déli régiója.

> [!NOTE]
> Ha egy virtuális hálózati szolgáltatásvégpontot valósít meg egy szolgáltatáshoz, például az Azure Storage-hoz vagy az Azure SQL Database-hez, az Azure hozzáad egy útvonalat a szolgáltatás virtuális hálózatának alhálózatához. Az útvonal címelőtagjai ugyanazok a címelőtagok vagy CIDR-tartományok lesznek, mint a megfelelő szolgáltatáscímke.

## <a name="application-security-groups"></a>Alkalmazásbiztonsági csoportok

Az alkalmazásbiztonsági csoportok segítségével az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot, így csoportosíthatja a virtuális gépeket, és ezen csoportok alapján meghatározhatja a hálózati biztonsági szabályokat. Ezzel a szolgáltatással újra felhasználhatja a biztonsági szabályokat nagy léptékben is a konkrét IP-címek manuális karbantartása nélkül. A platform képes kezelni a konkrét IP-címek és a szabálykészletek jelentette összetettséget, így Ön az üzleti logikára összpontosíthat.

Az alkalmazásbiztonsági csoportokat megadhatja forrásként és célként is a biztonsági szabályokban. A biztonsági szabály meghatározása után létrehozhat virtuális gépeket, és hozzárendelheti az adott virtuális gépekben lévő hálózati adaptereket egy alkalmazásbiztonsági csoporthoz. A szabály a virtuális gépben lévő egyes hálózati adapterek alkalmazásbiztonsági csoporttagsága alapján lesz alkalmazva. Az alábbi példa bemutatja, hogyan használhat egy alkalmazásbiztonsági csoportot az előfizetésében lévő összes webkiszolgálóhoz:

1. Hozzon létre egy *WebServers* nevű alkalmazásbiztonsági csoportot.
2. Hozzon létre egy *MyNSG* nevű hálózati biztonsági csoportot.
3. Hozzon létre egy bejövő biztonsági szabályt a hálózati biztonsági csoportban az *Internet* szolgáltatáscímke megadásával forráscímként és a *WebServers* alkalmazásbiztonsági csoport megadásával célcímként, és engedélyezze a 80-as és a 443-as portot.
4. Helyezzen üzembe egy webkiszolgáló alkalmazást futtató virtuális gépet. Adja meg, hogy a virtuális gépben lévő hálózati adapter a *WebServers* alkalmazásbiztonsági csoport tagja. Ezután a 80-as és a 443-as port engedélyezve lesz a virtuális gépen. A portok az ezt követően a *WebServers* alkalmazásbiztonsági csoport tagjaként létrehozott webkiszolgálókon is engedélyezve lesznek. 

Ha más szabályokat is létrehoz, amelyekben más alkalmazásbiztonsági csoportokat ad meg célként, azok a szabályok nem lesznek alkalmazva az előző példában szereplő webkiszolgálókra. Az alkalmazásbiztonsági csoportot megadó szabályok csak az adott alkalmazásbiztonsági csoport tagságába tartozó hálózati adapterekre lesznek alkalmazva. Az alkalmazásbiztonsági csoportok a kibővített biztonsági szabályokkal és a szolgáltatáscímkékkel kombinálva lehetővé teszik, hogy előfizetése hálózati biztonságát minimális számú hálózati biztonsági csoport létrehozásával felügyelje.
 
Az alkalmazásbiztonsági csoportok létrehozására és a biztonsági szabályokban való alkalmazására vonatkozó korlátozásokkal kapcsolatban tekintse meg az [Azure korlátairól](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) szóló cikket.

Az alkalmazásbiztonsági csoportok előzetes kiadásban érhetőek el. Az alkalmazásbiztonsági csoportok használata előtt regisztráljon azok használatára a [hálózati biztonsági csoport alkalmazásbiztonsági csoportokkal való létrehozását](create-network-security-group-preview.md#powershell) ismertető szakasz 1–5. lépések végrehajtásával, és olvassa el az [előzetes kiadásban elérhető funkciókról](#preview-features) szóló cikket a legfontosabb információkért. Az előzetes kiadásban az alkalmazásbiztonsági csoportok hatóköre csak a virtuális hálózatra terjed ki. A hálózati biztonsági csoportokon lévő alkalmazásbiztonsági csoportokkal kereszthivatkozással társviszonyban lévő virtuális hálózatok nem lesznek alkalmazva. 

Az előzetes kiadásban elérhető szolgáltatások rendelkezésre állása és megbízhatósága eltér az általánosan elérhető kiadásokban lévőekétől. Az alkalmazásbiztonsági csoportok használata előtt regisztrálnia kell azok használatához. A szolgáltatások csak a következő régiókban érhetőek el: az USA nyugati középső régiója.

## <a name="next-steps"></a>Következő lépések

* Végezze el a [hálózati biztonsági csoport létrehozásáról](virtual-networks-create-nsg-arm-pportal.md) szóló oktatóanyagot
* Végezze el a [hálózati biztonsági csoport alkalmazásbiztonsági csoportokkal való létrehozásáról](create-network-security-group-preview.md) szóló oktatóanyagot
