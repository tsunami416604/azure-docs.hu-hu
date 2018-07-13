---
title: Megbízható internetes kapcsolat útmutatást az Azure-hoz
description: Megbízható internetes kapcsolat útmutatást az Azure és az SaaS-szolgáltatásokhoz
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 9d71efa35713500911c67d1df15612b64c8e97da
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990829"
---
# <a name="trusted-internet-connection-guidance"></a>Megbízható internetes kapcsolat útmutató

## <a name="background"></a>Háttér

Az internetes kapcsolatok (TIC) kezdeményezés célja optimalizálása és a jelenleg használatban lévő egyes külső hálózati kapcsolatok biztonságának szabványosíthatja a Szövetségi ügynökségek végezték. A szabályzat szemlélteti a OMB (Office-kezelés és költségvetés) az [alapító M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

A November 2007 a OMB Szövetségi hálózati szegélyhálózat-alapú biztonság-és incidenskezelő függvények a TIC program jön létre. TIC úgy lett kialakítva, az összes bejövő és kimenő .gov érkező forgalom aláírások és a minta-alapú adatok azonosítására és tárhat fel tevékenységbeli anomáliákat, például botnet tevékenység hálózati elemzését. Összevonja a külső hálózati kapcsolataikat, és a behatolásérzékelő és -megelőző eszközön (más néven EINSTEIN) (más néven megbízható internetes hálózati végpontok korlátozott számú üzemeltető összes forgalom szervek is megbízott Kapcsolatok).

Egyszerűen szólva TIC célja szerveknek tudnia:
- Akik a saját hálózaton (engedélyezve vagy nem engedélyezett)?
- Ha a hálózat használatban van, és miért?
- Milyen erőforrások használatban vannak?

Mai összes ügynökség külső kapcsolatokat kell átirányítani egy OMB jóváhagyott TIC keresztül. Szövetségi ügynökségek végezték a TIC Access Provider (TICAP), vagy a fő szint 1 internetszolgáltatók egyikével szolgáltatások Szerződő néven felügyelt megbízható Internet Protocol szolgáltatás (MTIPS) szolgáltatók a TIC programban való részvételhez szükségesek.  TIC kötelező kritikus funkciókkal rendelkezik, és még ma az Ügynökség és MTIPS szolgáltató által végrehajtott tartalmazza. A jelenlegi verziójában TIC, a EINSTEIN 2-es verzió behatolásérzékelési és a 3-as verziójú EINSTEIN gyorsított (3A) minden egyes TICAP és MTIPS behatolás-megelőzési eszközök vannak telepítve, és az Ügynökség hoz létre egy, a szervezeti ismertetése alapító Haza biztonsági (DHS) EINSTEIN képességek Szövetségi rendszerek központi telepítése.

A feladata, hogy megvédje a .gov hálózatot részeként DHS szükséges nyers adatcsatornák az Ügynökség Netflow adatok incidensek összekapcsolását a Szövetségi vállalaton belül, és speciális eszközök használatával elemzéseket végezhetnek. DHS útválasztók lehetővé teszi, hogy IP-hálózati forgalom gyűjtése szerint kerül, vagy kilép egy felületet. A hálózati forgalmi adatai egy hálózati elemzésével rendszergazdák megállapíthatják, többek között a forrás- és a forgalmat, osztály, szolgáltatás stb. Hálózati forgalmi adatai számít a "nem tartalom data" (például a fejlécet, forrás-IP, cél IP-cím, stb.), és lehetővé teszi, hogy tudni, hogy a tartalom; információk DHS azt jelenti, akik műveletet végzett mi történik, és milyen hosszú.

A kezdeményezés is tartalmaz a biztonsági házirendek, irányelvek és keretrendszereket, amely a helyszíni infrastruktúra feltételezik. Kormányzati szerveknek áthelyezni a felhőbe költségmegtakarítást működési hatékonyságot és innovációt érhet el, mert TIC a megvalósítás követelményei vannak bizonyos esetekben a hálózati forgalom lelassul, és a sebességet és rugalmasságot, mely kormányzati a felhasználók korlátozása felhőalapú adatok elérését.

Ez a cikk ismerteti a kormányzati szerveknek használatát a Microsoft Azure Government, így biztosítva a TIC házirendnek való megfelelőség is IaaS és PaaS-szolgáltatások között.

## <a name="summary-of-azure-networking-options"></a>Azure hálózatkezelési beállítások összefoglalása

Három fő lehetőség van az Azure-szolgáltatásokhoz való kapcsolódáskor:

1. A közvetlen internetkapcsolatot: Azure-szolgáltatások open internetkapcsolatra keresztül közvetlenül csatlakozhat. A közepes nyilvános, valamint a kapcsolat. Az adatvédelem biztosításához a alkalmazás- és átviteli szintű titkosítást vannak támaszkodnak. Egy hely kapcsolódni az internetre korlátozza a sávszélességet, és több aktív szolgáltató is használható a megfelelő rugalmasság
1. Virtuális magánhálózat: Csatlakozzon közvetlenül a Microsoftnak a VPN-átjáró használata az Azure Virtual Networkhöz.
A közepes nem nyilvános, mert bejárja a hely normál internetkapcsolattal, de a kapcsolat az adatvédelem biztosításához a vezető alagút titkosított. Attól függően, a VPN-eszközök és a kiválasztott konfiguráció korlátozott a sávszélesség. Azure pont – hely kapcsolatok általában legfeljebb 100 MB/s, míg a helyek közötti kapcsolatok korlátozva, 1,25 GB/s.
1. Microsoft ExpressRoute: Az ExpressRoute közvetlen kapcsolatot Microsoft-szolgáltatásokhoz való. Mivel a kapcsolat egy elkülönített fiber channel vezérlőt használó keresztül, a kapcsolat lehet nyilvános vagy privát használt konfigurációjától függően. A sávszélesség általában korlátozva, legfeljebb 10 GB/s.

Többféleképpen is megfelelnek a megbízható internetes kapcsolat függelék H (Felhőbeli szempontok), a a részleg, haza Security, "Megbízható internetes kapcsolatok (TIC) referencia architektúra dokumentum, 2.0-s verziójának" található. Ebben a dokumentumban DHS TIC útmutatást fog példányaként TIC 2.0.

Ahhoz, hogy a kapcsolat a közigazgatási szervek vagy ügynökségek (D/A) és Azure vagy Office 365, a D/A TIC útválasztási forgalmát nélkül a D és A egy titkosított csatornán, illetve a dedikált kapcsolat a Cloud Service Provider (CSP) kell használnia. A CSP-szolgáltatásokat is győződjön meg arról, hogy a D és A felhőbeli eszközökhöz való kapcsolat nem érhető el a nyilvános internetre közvetlen ügynökség személyzet hozzáféréshez.

TIC 2.0 függelék H használatával vagy Express Route-kompatibilis Office 365 [Microsoft Peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) engedélyezve vagy internetkapcsolattal, amely minden forgalom a TLS 1.2 használatával titkosítja.  D és A végfelhasználók számára a D/A hálózaton keresztül az Ügynökség hálózat és a TIC infrastruktúra az interneten keresztül kapcsolódhatnak. Az összes távoli internet-hozzáférés az O365 letiltva, és az Ügynökség keresztül irányítja át. A D/A Microsoft társviszony-létesítés, az Express Route-kapcsolaton keresztül, amelyhez egy nyilvános társviszony-létesítés, engedélyezett típusú O365 is csatlakozhat.  

Beállítások (VPN) 2. és 3 (ExpressRoute) csak Azure-szolgáltatások, amelyek Internet-hozzáférés korlátozása együtt használatakor is teljesítenie ezeket a követelményeket.

![A Microsoft megbízható internetes (TIC) kapcsolati Diagram](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Hogyan segíthet az Azure infrastruktúra-szolgáltatási ajánlatok TIC megfelelőség

IaaS használatával TIC szabályzat betartása viszonylag egyszerű, mivel az Azure-ügyfelek kezelése a saját virtuális hálózati útválasztást.

A fő követelmény, hogy biztosítsa a TIC referenciaarchitektúra való megfelelés érdekében, hogy győződjön meg arról, hogy a virtuális hálózat (VNet) válik a közigazgatási szervek vagy ügynökségek a hálózaton egy privát kiterjesztése. Lesz olyan _privát_ bővítmény, a házirend telepítéséhez nincs adatforgalom ne módosítsa a hálózaton, kivéve az On-Premises TIC hálózati kapcsolaton keresztül. Ezt a folyamatot "Kényszerített bújtatás", mint amikor TIC megfelelőségi használt, az a folyamat minden származó minden forgalmat az Útválasztás a CSP környezetben egy a helyszíni átjárón keresztül lépjen ki a szervezet hálózati az interneten keresztül a TIC.

Az Azure IaaS TIC megfelelőségi is lehet osztani két fő lépést:

1. Konfiguráció
1. Naplózás

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure iaas-beli TIC megfelelőségi konfigurációt

A TIC megfelelő architektúra konfigurálása az Azure-ral, akkor először megakadályozza a virtuális hálózat közvetlen internet-hozzáféréssel, és ezután internetforgalmat a helyszíni hálózaton keresztül.

#### <a name="prevent-direct-internet-access"></a>Közvetlen Internet-hozzáférés megakadályozása

Az Azure IaaS-hálózatok virtuális hálózatok, alhálózatok, csoportból áll, amely a hálózati adapter vezérlők (NIC) a virtuális gépek társítva keresztül történik.

TIC megfelelőségi támogatása a legegyszerűbb eset az, hogy biztosítsa, hogy egy virtuális gépet vagy egy gyűjteményt, nem lehet csatlakozni bármilyen külső erőforráshoz. A Leválasztás a külső hálózatok használata a hálózati biztonsági csoportok (NSG), amely egy vagy több hálózati adapterrel, vagy a virtuális hálózat alhálózatai adatforgalom vezérlésére használható kell biztosítani. A hálózati biztonsági csoport olyan hozzáférés-vezérlési szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a forgalmat a forgalom iránya, a protokoll, a forrás címe és portja, illetve a cél címe és portja alapján. A szabályokat az NSG-KET bármikor módosíthatók, és a módosítások érvénybe lépnek minden társított példány.  Hálózati biztonsági csoportok létrehozásával kapcsolatos további tudnivalókért lásd: Ez a cikk [létrehozni egy NSG-t](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>A helyi hálózaton keresztüli

Az Azure automatikusan hoz létre rendszerútvonalakat, és a virtuális hálózatban lévő egyes alhálózatokhoz rendeli az útvonalakat. Nem hozható létre rendszerútvonalakat, és nem távolíthat el rendszerútvonalakat, de egyes rendszerútvonalakat egyéni útvonalakat felül lehet bírálni. Azure hoz létre az alapértelmezett rendszerútvonalakat minden alhálózat számára, és hozzá további választható alapértelmezett útvonalakat adott alhálózatokhoz vagy minden alhálózathoz, amikor bizonyos Azure-képességeket használ. Az Útválasztás biztosítja, hogy a virtuális hálózat felé irányuló forgalom a virtuális hálózaton belül marad, az IANA magánhálózati címtartománynak termékek, például (kivéve, ha a virtuális hálózat címtere szerepel) a rendszer elveti a 10.0.0.0/8, és a "legutóbbi-megoldásként" Útválasztás a virtuális hálózat internetes végpontra 0.0.0.0/0.

![TIC kényszerített bújtatás](media/tic-diagram-c.png)

Győződjön meg arról, hogy az összes adatforgalomra a D/A TIC, hagyja el a virtuális hálózatot az összes forgalom legyen irányítva a helyszíni-kapcsolaton keresztül kell.  Létrehozhat egyéni útvonalak vagy létrehozni felhasználó által megadott útvonalakat, vagy a border gateway protocol (BGP) útvonalakat a helyszíni hálózati átjáró és a egy Azure virtuális hálózati átjáró között kicserélésével. További információ a felhasználó által megadott útvonalak található https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. További információ a Border Gateway Protocol is található https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Felhasználó által megadott útvonalak

Egy útválasztó-alapú virtuális hálózati átjárót használja, ha a kényszerített bújtatás elvégezhető Azure-ban egy felhasználó által megadott útvonal (UDR) beállítás 0.0.0.0/0 forgalom átirányítását a "Next-Hop" a virtuális hálózati átjáró hozzáadásával. Azure felhasználó által definiált útvonalak rangsorolja a rendszer által megadott útvonalak keresztül így eredményezne. a virtuális hálózati átjáró, amely majd irányíthatja, a helyszíni küld minden hálózatok közötti forgalmat. Definiálás után a felhasználó által megadott útvonalat kell meglévő összes alhálózatokhoz társított vagy az újonnan létrehozott összes virtuális hálózatot az Azure környezetben lévő.

![felhasználó által definiált útvonalak és TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Border Gateway Protocol

Ha a Border Gateway Protocol (BGP) engedélyezve van a virtuális hálózati átjáró vagy ExpressRoute használja, a BGP az előnyben részesített mechanizmusa útvonalak meghirdetése. A BGP-vel hirdetett útvonalat a 0.0.0.0/0, ExpressRoute- és BGP kompatibilis virtuális hálózati átjárók biztosítja, hogy az alapértelmezett útvonal, a virtuális hálózatokon lévő összes alhálózathoz van hozzárendelve.

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure iaas-beli KIEJTÉSI megfelelőségi naplózása

Az Azure több módot is TIC megfelelőséget kínál.

#### <a name="effective-routes"></a>Érvényes útvonalak

Győződjön meg arról, hogy az alapértelmezett útvonal propagálása megtörtént-e, vizsgálja meg az "érvényes útvonalak" egy adott virtuális gép, egy adott hálózati adapter vagy egy felhasználó által megadott útvonaltábla. Ezt megteheti az Azure Portalon keresztül ismertetett módon https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal, vagy a PowerShell-lel ismertetett módon https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell. Az érvényes útvonalak panel lehetővé teszi, hogy a megfelelő felhasználó által megadott útvonalakat, a BGP hirdetett útvonalakat, és a rendszerútvonalakkal, amelyek a kapcsolódó entitás a alkalmazni az alább látható módon.

![útvonalak képernyőképe](media/tic-screen-1.png)

**Megjegyzés:**: érvényes útvonalak megtekintése nem egy hálózati adapter, kivéve, ha egy futó virtuális Géphez társított azt.

#### <a name="network-watcher"></a>Network Watcher

Az Azure Network Watcher több eszközt, amely segítségével TIC megfelelőséget kínál.  További információ a Network Watcher https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview.

##### <a name="network-security-groups-flow-logs"></a>Hálózati biztonsági csoportok Folyamatnaplóit 

Az Azure Network Watcher lehetővé teszi a hálózati forgalmi naplók jelzi a metaadatok körülvevő IP-forgalom rögzítéséhez. Mellett egyéb adatok a hálózati forgalmi naplók tartalmaznak a forrás- és a célhelyek címeket tárolók. Ez a virtuális hálózati átjáró, a helyszíni peremhálózati eszközökön, illetve a TIC naplóinak kombinálva, lehetővé teszi a figyelési összes forgalom valóban folyamatban van a helyszíni irányított. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Hogyan segíthet az Azure Platform egy szolgáltatásajánlatok TIC megfelelőségi

Az Azure PaaS-szolgáltatások, például az Azure Storage hozzáférhetők egy internetről elérhető URL-CÍMÉT. Az erőforrás, például a storage-fiók nélkül egy TIC áthaladó bárhonnan hozzáférhet bárki jóváhagyott hitelesítő adatokkal. Ezért számos kormányzati ügyfeleknek helytelenül utasításoknak megfelelően, hogy az Azure PaaS-szolgáltatások, amelyek nem felelnek meg a TIC házirendeket. Sőt számos Azure PaaS-szolgáltatások megfelelő is lehet az azonos architektúra használatával, egy megfelelő TIC IaaS-környezet is csatlakoztatható egy virtuális hálózatot (VNet), ha a fent leírt TIC szabályzattal. Lehetővé teszi a szolgáltatás a virtuális hálózatról közvetlenül elérhetők legyenek, és lehetővé teszi, hogy egyéni útválasztás 0.0.0.0/0 BGP, amely biztosítja, hogy minden internetre irányuló forgalmat irányított a helyszíni vagy a felhasználó által megadott útvonalak keresztül alkalmazni az Azure PaaS-szolgáltatások integrálása az Azure-beli virtuális a TIC haladnak át.  Bizonyos Azure-szolgáltatások a virtuális hálózatokat a következő minták integrálható:

- **Szolgáltatás dedikált példányának üzembe helyezése**: PaaS-szolgáltatások telepíthetők dedikált példányok virtuális hálózattal, egyre több csatlakoztatott végpontok. Például egy App Service Environment (ASE) is üzembe helyezhetők "Elkülönített" módban, lehetővé téve a virtuális hálózathoz kell korlátozott a hálózati végpont. Az ASE Környezeten majd üzemeltethető számos Azure PaaS-szolgáltatások, például a Web Apps, API-k és funkciók.
- **Virtuális hálózati Szolgáltatásvégpontok**: PaaS-szolgáltatások egyre több engedélyezése arra, hogy a végpont át egy virtuális hálózatok közötti privát IP-cím nyilvános cím helyett.

Üzembe helyezés egy virtuális hálózat vagy a Szolgáltatásvégpontok 2018. május dedikált példányok támogató szolgáltatások az alábbiak: * (rendelkezésre állási jelöli az Azure kereskedelmi Azure Government rendelkezésre állási függőben van).

### <a name="service-endpoints"></a>Service Endpoints – szolgáltatásvégpont

|Szolgáltatás                   |status            |
|--------------------------|------------------|
|Az Azure KeyVault            | Privát előzetes verzió  |
|Cosmos DB                 | Privát előzetes verzió  |
|Identitás                  | Privát előzetes verzió  |
|Azure Data Lake           | Privát előzetes verzió  |
|SQL Postgress/Mysql       | Privát előzetes verzió  |
|Azure SQL Data Warehouse  | Nyilvános előzetes verzió   |
|Azure SQL                 | FE               |
|Storage                   | FE               |

### <a name="vnet-injection"></a>VNet-injektálás

|Szolgáltatás                            |status            |
|-----------------------------------|------------------|
|SQL felügyelt példánya               | Nyilvános előzetes verzió   |
|Az Azure Container Service(AKS)       | Nyilvános előzetes verzió   |
|Service Fabric                     | FE               |
|API Management                     | FE               |
|Azure Active Directory             | FE               |
|Azure Batch                        | FE               |
|ASE                                | FE               |
|Redis                              | FE               |
|HDI                                | FE               |
|COMPUTE-virtuálisgép-méretezési csoportot  | FE               |
|Számítási Felhőszolgáltatás              | FE               |

### <a name="vnet-integration-details"></a>VNet-integráció részletei

Az alábbi ábrán látható végigvezeti az általános hozzáférési a hálózati kialakítást a VNet-injektálásos és a VNet Szolgáltatásbújtatását PaaS-szolgáltatások.  Itt találhat további információkat a hálózati szolgáltatás átjárók, a virtuális hálózat és a Szolgáltatáscímkék https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![TIC PaaS kapcsolódási lehetőségei](media/tic-diagram-e.png)

1. Az Azure ExpressRoute használatával privát kapcsolatot. Minden ügyfél virtuális hálózatok közötti forgalom kényszerített expressroute-on keresztül vissza a helyi ExpressRoute privát társviszony-létesítés, ha a kényszerített bújtatás szolgál. A Microsoft Peering, nem szükséges.
2. Az ExpressRoute Microsoft társviszony-létesítés együtt használható az Azure VPN Gateway a végpontok közötti IPSec-titkosítás, az ügyfél virtuális hálózat és a helyszíni peremhálózati között átfedés használható. 
3. Az ügyfél virtuális hálózati kapcsolat segítségével kezelhető hálózati biztonsági csoportok (NSG) lehetővé teszi az ügyfelek számára, hogy engedélyezése vagy megtagadása IP, port és protokoll alapján.
4. Az ügyfél virtuális terjeszteni a PaaS-szolgáltatás az ügyfélszolgálat szolgáltatásvégpontot hoz létre.
5. A PaaS-szolgáltatásvégpontot legyen védett az összes elutasítása alapértelmezett, és csak engedélyezze a hozzáférést a megadott alhálózathoz, az ügyfél virtuális hálózaton belül.  Az alapértelmezett megtagadási is az internetről származó kapcsolatot foglal magában.
6. Bármely más Azure-szolgáltatás, amely az ügyfél virtuális hálózaton belüli erőforrásokhoz kell hozzáférniük egyikének kell lennie:  
  a. A virtuális hálózaton üzembe  
  b. Az útmutató a megfelelő Azure-szolgáltatás szelektív alapján engedélyezett.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>1. lehetőség: A dedikált példányt "Virtuális hálózat injektálási"

VNet-injektálás, a ügyfelei külön-külön dedikált példányok adott Azure-szolgáltatás, például a HDInsight, a saját virtuális hálózaton üzembe. Szolgáltatáspéldányok a rendszer üzembe helyezi az egy ügyfél virtuális hálózatában dedikált alhálózatán. Virtuális hálózatok közötti injektálási lehetővé teszi, hogy a szolgáltatási erőforrások a irányítható az Internet címeken keresztül érhető el.  A helyszíni példányok szolgáltatáspéldányok keresztül a virtuális hálózati címtér közvetlenül az ExpressRoute- vagy helyek közötti VPN-, nyilvános internetes címtér tűzfalak megnyitása helyett keresztül férhetnek hozzá. Ha a dedikált példánya, a végpont csatlakozik ugyanezen stratégiák IaaS TIC megfelelőségi használt alkalmazhatja, alapértelmezett útválasztási annak biztosítása, hogy az internetre irányuló forgalmat a rendszer átirányítja egy virtuális hálózati átjáró a helyszíni kötött. Bejövő és kimenő hozzáférést tovább szabályozható a hálózati biztonsági csoportok (NSG-k) az adott alhálózat.

![Virtuális hálózat injektálási – áttekintő ábra](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>2. lehetőség: A virtuális hálózati Szolgáltatásvégpontok 

Az Azure több-bérlős szolgáltatások egyre növekvő számú "Szolgáltatásvégpont" képesség, integrálható az Azure-alapú virtuális hálózatokhoz alternatív módszert kínálnak. VNet Szolgáltatásvégpontjai egy közvetlen kapcsolaton keresztül kiterjesztik az a virtuális hálózat IP-címtér és a szolgáltatás a VNet identitását.  A VNet forgalmát az Azure-szolgáltatás mindig az Azure gerinchálózatán belül marad. Miután egy végpontot egy szolgáltatás engedélyezve van, a szolgáltatáshoz korlátozható a virtuális hálózaton keresztül a szolgáltatás által elérhetővé tett házirendek. Hozzáférés-ellenőrzéseket tartat be a platform az Azure-szolgáltatás, és a zárolt erőforráshoz való hozzáférés csak ha a kérés engedélyezett virtuális hálózat/alhálózat származik, és/vagy a két IP-címek segítségével a OnPremises forgalom azonosítására, az ExpressRoute használatakor. Ez a hatékony megakadályozza, hogy bejövő/kimenő forgalom közvetlenül és a PaaS-szolgáltatás használható.

![A Szolgáltatásvégpontok áttekintése ábrán](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>A hálózati helyzetismeretet felhőbeli natív eszközök használatával

Az Azure biztosít felhőbeli natív eszközök segítségével, győződjön meg arról, hogy rendelkezik-e a forgalom adatfolyamait, a hálózat megértéséhez szükséges helyzetismeretet. Ezek nem szükségesek TIC szabályzatának, de azok jelentősen javítja a biztonsági képességeket.

### <a name="azure-policy"></a>Azure Policy

Az Azure Policy (https://azure.microsoft.com/services/azure-policy/) Azure-szolgáltatás, amely a szervezete biztosít jobb képes naplózási és megfelelőségi irányelveinek kényszerítése.  Jelenleg nyilvános Előzetesben érhető el az Azure kereskedelmi Felhőiben, de még nincs Microsoft Azure Government, TIC-tudatában ügyfelek megkezdheti tervezés és tesztelés a jövőbeli megfelelőségi garancia a szabályzat szabályainak. Az Azure Policy célja az előfizetés szintjén, és a egy központi felületen biztosít kezeléséhez a kezdeményezések, a szabályzatdefiníciók, naplózási és kényszerítési eredményeket és kivételek kezelése. A számos beépített Azure szabályzat-meghatározást mellett a rendszergazdák meghatározhatnak saját egyéni definíciók egyszerű json-sablonalapú keresztül. Sok ügyfél a Microsoft azt javasolja a rangsort naplózási kényszerítési keresztül, ahol csak lehetséges.

Az alábbi minta házirendek TIC megfelelőségi célokra hasznos lehet:

|Szabályzat  |Mintaforgatókönyv  |Sablon indítása  |
|---------|---------|---------|
|Felhasználó által definiált útvonaltábla kényszerítése |     Ellenőrizze, hogy az alapértelmezett útvonal, az összes virtuális hálózat pont felé egy engedélyezett virtuális hálózati átjáró a helyszíni Útválasztás | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Ha a régió nem engedélyezett a Network Watcher naplózása  | Győződjön meg arról, hogy engedélyezve van az összes régióban használja a Network Watcher  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|NSG X minden alhálózaton  | Győződjön meg arról, hogy egy NSG-t (vagy jóváhagyott NSG-k készlete) az internetes forgalom blokkolva van alkalmazva minden virtuális hálózaton lévő összes alhálózathoz | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|NSG X minden hálózati adapteren | Győződjön meg arról, hogy egy NSG-t, az internetes forgalmat blokkolja az összes virtuális gép minden hálózati adapter van hozzárendelve. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Használata engedélyezett virtuális hálózatok közötti virtuális hálózati adapterek  | Ügyeljen arra, hogy az összes hálózati adapter egy engedélyezett virtuális hálózaton | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Engedélyezett helyek | Győződjön meg arról, hogy az összes erőforrás telepítve vannak-e a régió, a megfelelő virtuális hálózatok és a Network Watcher konfigurálása  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Nem engedélyezett erőforrástípusok Tartománycímkét például  | Tiltják az erőforrástípusok, amelyek nem rendelkeznek megfelelőségi csomag telepítését. Tegyük fel ez a szabályzat központi telepítését a nyilvános IP-cím erőforrás időtartamig használható. Bár az NSG-szabályok segítségével hatékonyan a bejövő internetes forgalom blokkolása, megakadályozza a nyilvános IP-címek használatát további csökkentve a támadási felületet.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [Traffic Analytics](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

A Traffic Analytics az Azure Network Watcher flow naplóadatokat és magas szintű áttekintést nyújt a hálózati forgalom a többi naplófájlt használ fel. Ezek az adatok akkor lehet hasznos TIC megfelelőségi vizsgálatához és problémás azonosítása. Magas szintű irányítópult gyors képernyő, amely a virtuális gépek kommunikál az internethez, ami majd célzott listáját biztosítja a TIC útválasztáshoz használható.

![Traffic Analytics képernyőképe](media/tic-traffic-analytics-1.png)

"Földrajzitérkép" segítségével gyorsan azonosíthatja a valószínűleg fizikai célhelyre az internetes forgalmat a virtuális gépek, így azonosíthatja és osztályozhatja a minta módosítások vagy gyanús helyek számára.

![Traffic Analytics képernyőképe](media/tic-traffic-analytics-2.png)

A hálózati topológia térkép segítségével gyorsan a meglévő virtuális hálózatok felmérés:

![Traffic Analytics képernyőképe](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Az Azure Network Watcher következő ugrás

Hálózati figyelő által figyelt régióban lévő hálózatok is teszteket "Következő ugrás", egyszerűen portálalapú hozzáférést írja be a forrás- és a egy minta hálózati folyamatot, amelynek a Network Watcher feloldja a "Következő ugrás" cél. Ez használható virtuális gépek és a példa internetcímek ellen annak biztosításához, hogy a "következő ugrás" valójában a virtuális hálózati átjárót.

![Network watcher következő ugrás](media/tic-network-watcher.png)

## <a name="conclusions"></a>Következtetések

A Microsoft Azure, Office 365 és Dynamics 365-hozzáférés írásos és 2018 májusi meghatározott TIC 2.0 függelék H útmutatást ahhoz segítségével könnyen konfigurálható.  A Microsoft a vegye figyelembe, hogy ez az útmutató változhat, és segítenek az ügyfeleknek az útmutató új útmutatót akkor szabadul fel időben találkoznak endeavor lesz.

## <a name="appendix-tic-patterns-for-common-workloads"></a>A függelék: TIC minták végzett gyakori számítási feladatokhoz

| Kategória | Számítási feladat | IaaS | PaaS dedikált / VNet-injektálás  | Service Endpoints – szolgáltatásvégpont  |
|---------|---------|---------|---------|--------|
| Compute | Linux rendszerű virtuális gépek | Igen | | |
| Compute | Windows rendszerű virtuális gépek | Igen | | |
| Compute | Virtual Machine Scale Sets | Igen | | |
| Compute | Azure Functions | | az App Service Environment (ASE) keresztül | |
| Web és mobil | Belső webalkalmazások | | az App Service Environment (ASE) keresztül | |
| Web és mobil | Belső mobilalkalmazás | | az App Service Environment (ASE) keresztül | |
| Web és mobil | API Apps | | az App Service Environment (ASE) keresztül | |
| Containers | Az Azure Container Service (ACS) | | | Igen |
| Containers | Az Azure Container Service (AKS) * | | | Igen |
| Adatbázis | SQL Database | | Az Azure SQL Database felügyelt példány * | Azure SQL |
| Adatbázis | Azure Database for MySQL | | | Igen |
| Adatbázis | Azure Database for PostgreSQL | | | Igen |
| Adatbázis | SQL Data Warehouse | | | Igen |
| Adatbázis | Azure Cosmos DB | | | Igen |
| Adatbázis | Redis Cache | | Igen | |
| Storage | Blobok | Igen | | |
| Storage | Fájlok | Igen | | |
| Storage | Üzenetsorok | Igen | | |
| Storage | Táblák | Igen | | |
| Storage | Lemezek | Igen | | |

*: Nyilvános előzetes verziója 2018. május kezdődően az Azure Government az  
**: Az Azure Government 2018. május privát előzetes verzió
