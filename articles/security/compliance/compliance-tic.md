---
title: Megbízható internetes kapcsolatok útmutatást az Azure-hoz
description: Az Azure és az SaaS-szolgáltatások megbízható internetkapcsolatot útmutató
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 637f837ec2421f0bef5131a33c709087b891aa0f
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505111"
---
# <a name="trusted-internet-connections-guidance"></a>Megbízható internetes kapcsolatok útmutató

Ez a cikk ismerteti a kormányzati szerveknek használatát a Microsoft Azure Government, így biztosítva az internetes kapcsolatok (TIC)-kezdeményezéshez való megfelelést. A cikk bemutatja, hogyan használandó Azure Government különböző Azure-infrastruktúra (IaaS) és az Azure platform a platformszolgáltatás (PaaS) ajánlatok.

## <a name="trusted-internet-connections-overview"></a>Megbízható internetes kapcsolatok áttekintése

A TIC kezdeményezésére célja, hogy a Szövetségi ügynökségek által használt egyéni külső hálózati kapcsolatok biztonsági szabványosíthatja és optimalizálása. A szabályzat mutatja be a [Office-kezelés és M-08-05 Budget (omb) számára alapító](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

A November 2007 a OMB Szövetségi hálózati szegélyhálózat-alapú biztonság-és incidenskezelő függvények a TIC program jön létre. TIC célja a hálózat összes bejövő és kimenő .gov forgalom azonosítására adott aláírások és a minta-alapú adatok elemzését. TIC tárja tevékenységbeli anomáliákat, például botnet tevékenység. Szervek vannak megbízott összevonja a külső hálózati kapcsolataikat, és minden forgalmat irányítson a behatolásérzékelő és -megelőző eszközön EINSTEIN néven. Az eszközök hálózati végpontokat, amelyek nevezzük korlátozott számú üzemeltetett _megbízható internetes kapcsolatok_.

TIC célja a szerveknek tudnia:
- Akik a saját hálózaton (engedélyezve vagy nem engedélyezett)?
- Ha a hálózat érhető el, és miért?
- Milyen erőforrások érhetők el?

Minden ügynökség külső kapcsolatok jelenleg egy OMB jóváhagyott TIC keresztül kell irányítani. A TIC programban való részvételhez, mint egy TIC Access Provider (TICAP), illetve a szolgáltatások egyikét az 1. súlyos szintű internetszolgáltatók szerződő Szövetségi ügynökségek szükségesek. Ezek a szolgáltatók felügyelt megbízható Internet Protocol szolgáltatás (MTIPS) szolgáltatók nevezzük. TIC kötelező kritikus funkciókkal rendelkezik, az Ügynökség és MTIPS szolgáltató által végrehajtott tartalmazza. A jelenlegi verziójában TIC, a EINSTEIN 2-es verzió behatolásérzékelési és a 3-as verziójú EINSTEIN gyorsított (3A) a behatolás-megelőzési eszközök minden TICAP és MTIPS helyezik üzembe. Az Ügynökség hoz létre egy "tájékoztató az értelmezésre" és a részleg, haza biztonsági (DHS) EINSTEIN képességek Szövetségi rendszerek központi telepítése.

A feladata, hogy megvédje a .gov hálózatot részeként DHS szükséges a nyers adatcsatornák az Ügynökség nettó folyamat adatok incidensek összekapcsolását a Szövetségi vállalaton belül, és elemzéseket végezhetnek speciális eszközök használatával. DHS útválasztók lehetővé teszi, hogy IP-hálózati forgalom gyűjtése szerint kerül, vagy kilép egy felületet. A hálózati rendszergazdák elemezheti a hálózati forgalmi adatai határozza meg a forrás- és a forgalom, az osztály a szolgáltatás, és így tovább. Hálózati forgalmi adatai minősül "nem tartalom adatok" hasonló a fejléc, forrás IP-címe, cél IP-címe és így tovább. Nem-tartalom adatok lehetővé teszi, hogy a további információ a tartalom DHS: ki műveletet végzett mi történik, és mennyi ideig.

A kezdeményezés is tartalmaz a biztonsági házirendek, irányelvek és keretrendszereket, amely a helyszíni infrastruktúra feltételezik. Kormányzati szerveknek áthelyezni a felhőbe költségmegtakarítást működési hatékonyságot és innovációt érhet el, mivel a megvalósítás követelményei TIC lelassíthatja hálózati forgalom. A sebesség és az melyik government felhasználók is hozzáférhetnek az adataikhoz felhőalapú agilitás korlátozódik eredményeként.

## <a name="azure-networking-options"></a>Azure hálózatkezelési lehetőségekkel

Azure-szolgáltatásokhoz való csatlakozás három fő lehetőség áll rendelkezésre:

- A közvetlen internetkapcsolatot: Azure-szolgáltatások open internetkapcsolatra keresztül közvetlenül csatlakozhat. A közepes és a kapcsolat nyilvánosak legyenek. Az adatvédelem biztosításához a alkalmazás- és átviteli szintű titkosítást vannak támaszkodnak. A sávszélesség korlátozza a hely kapcsolódni az internetre. A megfelelő rugalmasság egynél több aktív szolgáltatót használják.
- Virtuális magánhálózati (VPN): az Azure virtuális hálózat privát módon csatlakozhat VPN-átjáró használatával.
A közepes azért nyilvános bejárja a hely normál internetkapcsolattal, de a kapcsolat az adatvédelem biztosításához a vezető alagút titkosított. Attól függően, a VPN-eszközök és a kiválasztott konfiguráció korlátozott a sávszélesség. Azure pont – hely kapcsolatok általában legfeljebb 100 MB/s és helyek közötti kapcsolatok korlátozva, 1,25 GB/s.
- Az Azure ExpressRoute: Az ExpressRoute közvetlen kapcsolatot Microsoft-szolgáltatásokhoz való. Mivel a kapcsolat egy elkülönített fiber channel vezérlőt használó keresztül, a kapcsolat lehet nyilvános vagy privát használt konfigurációjától függően. A sávszélesség általában korlátozva, legfeljebb 10 GB/s.

Többféleképpen is felel meg a TIC függelék H (Felhőbeli szempontok), az a szervezeti egység, haza Security, a "Megbízható internetes kapcsolatok (TIC) referencia architektúra dokumentum, 2.0-s verziójában." Ez a cikk útmutatást DHS TIC nevezzük **TIC 2.0**.

A kapcsolat engedélyezéséhez a **közigazgatási szervek vagy ügynökségek (D/A)** Azure vagy Office 365, a D/A TIC útválasztási forgalmát nélkül a D/A kell használnia egy titkosított csatornán vagy egy dedikált kapcsolat a felhőszolgáltató (CSP). A CSP-szolgáltatásokkal biztosíthatja a D és A felhőbeli eszközökhöz való kapcsolat nem használhatják az Ügynökség közvetlen munkatársai hozzáférés a nyilvános interneten.

Az Office 365 TIC 2.0 függelék H megfelelő, vagy az ExpressRoute használatával [Microsoft Peering](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) engedélyezve van, vagy a TLS 1.2 használatával titkosítja az összes forgalom internetkapcsolat. D és A végfelhasználók számára a D/A hálózaton keresztül az Ügynökség hálózat és a TIC infrastruktúra az interneten keresztül kapcsolódhatnak. Az összes távoli internet-hozzáférés az Office 365 le van tiltva, és továbbítja az Ügynökség keresztül. A D és A egy ExpressRoute-kapcsolaton keresztül a Microsoft-Peering (a nyilvános társviszony-létesítés típusa) engedélyezve is csatlakozhat az Office 365.  

Az Azure csak a második lehetőség (VPN) és a harmadik lehetőség (ExpressRoute) is kórháza, amikor a szolgáltatások, amelyek internet-hozzáférés korlátozása együtt használhatók.

![A Microsoft megbízható internetkapcsolatot (TIC)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Az Azure infrastruktúra-service-ajánlatokról

Azure IaaS használatával TIC házirendnek való megfelelőség az Azure-ügyfelek kezelése a saját virtuális hálózat útválasztási azért viszonylag egyszerű.

A fő követelmény, hogy biztosítsa a TIC referenciaarchitektúra való megfelelés érdekében, hogy győződjön meg, hogy a virtuális hálózat egy privát kiterjesztése, a D/A hálózaton. Kell egy _privát_ bővítmény, a házirend telepítéséhez nincs adatforgalom ne módosítsa kivételével a hálózaton keresztül a helyszíni TIC hálózati kapcsolat. Ez a folyamat más néven _kényszerített bújtatás_. TIC megfelelőségét a folyamat bármely rendszer érkező minden irányítja a CSP-környezetben az interneten keresztül a TIC a szervezet hálózati ki egy a helyszíni átjárón keresztül.

Az Azure IaaS TIC megfelelőségi van osztva a két fő lépést:

- 1. lépés: konfiguráció.
- 2. lépés: naplózás.

### <a name="azure-iaas-tic-compliance-configuration"></a>Az Azure IaaS TIC megfelelőségi: konfiguráció

TIC megfelelő architektúra konfigurálása az Azure-ral, először a virtuális hálózat megakadályozható a közvetlen internet-hozzáféréssel, és majd internetforgalmat a helyszíni hálózaton keresztül.

#### <a name="prevent-direct-internet-access"></a>Közvetlen internet-hozzáférés megakadályozása

Az Azure IaaS-hálózatok, amely a hálózati adapter vezérlők (NIC) a virtuális gépek társítva alhálózatból álló virtuális hálózatok keresztül történik.

TIC megfelelőségi támogatása a legegyszerűbb eset az, hogy biztosítsa, hogy egy virtuális gépet, vagy egy virtuális gépeket,-gyűjteményt nem lehet csatlakozni bármilyen külső erőforráshoz. A Leválasztás a külső hálózatok biztosítják a hálózati biztonsági csoportok (NSG-k) használatával. Az NSG-k segítségével egy vagy több hálózati adapterrel, vagy a virtuális hálózat alhálózatai forgalmának ellenőrzésére. A hálózati biztonsági csoport olyan hozzáférés-vezérlési szabályokat tartalmaz, amelyek engedélyezik vagy megtagadják a forgalmat a forgalom iránya, a protokoll, a forrás címe és portja, illetve a cél címe és portja alapján. A szabályokat az NSG-KET bármikor módosíthatja, és a módosítások érvénybe lépnek minden társított példány. Hálózati biztonsági csoportok létrehozásával kapcsolatos további tudnivalókért lásd: [egy hálózati biztonsági csoportot a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Egy a helyszíni hálózaton keresztüli

Az Azure automatikusan hoz létre rendszerútvonalakat, és a virtuális hálózatban lévő egyes alhálózatokhoz rendeli az útvonalakat. Nem hozható létre, vagy távolítsa el rendszerútvonalakat, de egyes rendszerútvonalakat egyéni útvonalakat felül lehet bírálni. Az Azure alapértelmezett rendszerútvonalakat minden alhálózat számára hoz létre. Az Azure ad hozzá választható alapértelmezett útvonalakat adott alhálózatokhoz vagy minden alhálózathoz, amikor bizonyos Azure-képességeket használ. Az ilyen típusú útválasztási biztosítja:
- A virtuális hálózaton belüli forgalmat a virtuális hálózaton belül marad.
- Az IANA által megadott magánhálózati címtartománynak például 10.0.0.0/8 a rendszer elveti, kivéve, ha azok már szerepel a virtuális hálózat címterét.
- "Utolsó-megoldásként" útválasztás a virtuális hálózat internetes végpontra 0.0.0.0/0.

![TIC kényszerített bújtatás](media/tic-diagram-c.png)

Minden olyan forgalmat, hogy a virtuális hálózaton keresztül a helyszíni kapcsolat, győződjön meg arról, hogy az összes adatforgalomra a D/A TIC kell. Egyéni útvonalakat hoz létre, felhasználó által megadott útvonalak létrehozásával, vagy a Border Gateway Protocol (BGP) útvonalakat a helyszíni hálózati átjáró és az Azure VPN Gateway-átjárók közötti kicserélésével. Felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: [virtuális hálózat forgalmának útválasztása: felhasználó által megadott útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). A BGP-t kapcsolatos további információkért lásd: [virtuális hálózat forgalmának útválasztása: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Felhasználó által megadott útvonalak hozzáadása

Útválasztó-alapú virtuális hálózati átjáró használatakor kényszerítheti az Azure-ban bújtatás. Adjon hozzá egy felhasználó által megadott útvonal (UDR), amely a 0.0.0.0/0 forgalom átirányítása egy **következő Ugrás** a virtuális hálózati átjáró. Azure felhasználó által megadott útvonalak rangsorolja a rendszer által meghatározott útvonalon keresztül. Minden nem virtuális hálózati forgalmat a virtuális hálózati átjáró, amely majd irányíthatja a forgalmat a helyszíni küld. Az UDR, meghatározása után társítsa az útvonal meglévő alhálózatokhoz vagy az Azure környezetben lévő összes virtuális hálózataikon belül új alhálózatokra.

![felhasználó által definiált útvonalak és TIC](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Használja a Border Gateway Protocol

Ha az ExpressRoute vagy egy BGP-kompatibilis virtuális hálózati átjárót, a BGP az előnyben részesített mechanizmusa útvonalak meghirdetése. A BGP hirdetett útvonal 0.0.0.0/0 ExpressRoute- és BGP-t támogató virtuális hálózati átjárók győződjön meg arról, az alapértelmezett útvonal vonatkozik a virtuális hálózatokon lévő összes alhálózathoz.

### <a name="azure-iaas-tic-compliance-auditing"></a>Az Azure IaaS TIC megfelelőségi: naplózás

Az Azure több módot is TIC megfelelőséget kínál.

#### <a name="view-effective-routes"></a>Érvényes útvonalak megtekintése

Győződjön meg arról, hogy az alapértelmezett útvonal megfigyelésével propagálja a _érvényes útvonalak_ egy adott virtuális gép, egy adott hálózati adapter vagy egy felhasználó által definiált útvonaltábla a [az Azure Portalon](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) vagy a [ Az Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). A **érvényes útvonalak** megjelenítése az érintett felhasználó által definiált útvonalak BGP hirdetett útvonalakat, és a rendszerútvonalakkal, amelyek a kapcsolódó entitás a alkalmazni az alábbi ábrán látható módon:

![Érvényes útvonalak](media/tic-screen-1.png)

> [!NOTE]
> Egy hálózati adapter nem érvényes útvonalak megtekintése, kivéve, ha egy futó virtuális géphez társított hálózati adapter.

#### <a name="use-azure-network-watcher"></a>Az Azure Network Watcher használatát

Az Azure Network Watcher TIC megfelelőséget számos eszközt kínál. További információkért lásd: [Ez az Áttekintés a Network Watcher névjegye](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Hálózati biztonsági csoport folyamatnaplóit rögzítése 

A Network Watcher használatával hálózati forgalmi naplók, amelyek jelzik a metaadatok jele körüli IP-forgalom rögzítése. A hálózati forgalmi naplók tartalmaznak, a forrás és cél címek a célok és egyéb adatokat. Ezek az adatok használata a virtuális hálózati átjáró naplóit, a helyszíni eszközök, vagy a TIC figyelése, hogy minden forgalmat irányítja-e a helyszíni él. 

## <a name="azure-platform-as-a-service-offerings"></a>Az Azure platform egy szolgáltatásajánlatok

Azure PaaS-szolgáltatások, például az Azure Storage, internetről elérhető URL-címének keresztül érhetők el. Az erőforrás, például a storage-fiók nélkül egy TIC áthaladó bárhonnan hozzáférhet bárki jóváhagyott hitelesítő adatokkal. Ezért számos kormányzati ügyfeleknek helytelenül utasításoknak megfelelően, hogy az Azure PaaS-szolgáltatások nem felelnek meg az TIC házirendeket. Sok Azure PaaS-szolgáltatások megfelelő TIC az is lehet. Egy szolgáltatás akkor megfelelő, ha az architektúra ugyanaz, mint a TIC megfelelő IaaS-környezet ([fentieknek](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)) és a szolgáltatás Azure-beli virtuális hálózathoz csatlakozik.

Azure PaaS-szolgáltatások integrálva vannak a virtuális hálózat, ha a szolgáltatás érhető el privát virtuális hálózaton. Egyéni útválasztás a felhasználó által megadott útvonalakat vagy BGP-n keresztül 0.0.0.0/0 alkalmazhat. Egyéni útválasztás biztosítja, hogy minden internetre irányuló forgalmat irányítja a TIC gyermekelemeinek helyszíni. Azure-szolgáltatások integrálása virtuális hálózatokat a következő minták segítségével:

- **A szolgáltatás egy dedikált példánya üzembe**: PaaS-szolgáltatások egyre több rendszer virtuális hálózati csatolású végpontokkal rendelkező dedikált példány üzembe helyezhető. App Service-környezet a powerapps szolgáltatásra, hogy a hálózati végpontot, hogy egy virtuális hálózathoz kell korlátozott, "Elkülönített" módban telepítheti. Az App Service Environment majd üzemeltethető számos Azure PaaS-szolgáltatások, például az Azure Web Apps, az Azure API Management és az Azure Functions.
- **Virtuális hálózati Szolgáltatásvégpontok használata**: PaaS-szolgáltatások egyre több engedélyezése arra, hogy a végpont át egy virtuális hálózat privát IP-cím nyilvános cím helyett.

Üzembe helyezés egy virtuális hálózatban a dedikált példányok vagy a szolgáltatásvégpontokat, 2018 május támogató szolgáltatások az alábbi táblázatban láthatók.

> [!Note]
> Az Azure-szolgáltatások, kereskedelmi forgalomban elérhető felel meg a rendelkezésre állási állapotát. Folyamatban a rendelkezésre állási állapotát az Azure-szolgáltatásokhoz az Azure Government szolgáltatásban.

### <a name="support-for-service-endpoints"></a>A Szolgáltatásvégpontok támogatása

|Szolgáltatás                        |Rendelkezésre állás      |
|-------------------------------|------------------|
|Azure Key Vault                | Privát előzetes verzió  |
|Azure Cosmos DB                | Privát előzetes verzió  |
|Identitás-szolgáltatások              | Privát előzetes verzió  |
|Azure Data Lake                | Privát előzetes verzió  |
|Azure Database for PostgreSQL  | Privát előzetes verzió  |
|Azure Database for MySQL       | Privát előzetes verzió  |
|Azure SQL Data Warehouse       | Nyilvános előzetes   |
|Azure SQL Database             | Általánosan elérhető (GA) |
|Azure Storage                  | FE               |

### <a name="support-for-virtual-network-injection"></a>Virtuális hálózat injektálási támogatása

|Szolgáltatás                               |Rendelkezésre állás      |
|--------------------------------------|------------------|
|Azure SQL Database felügyelt példány   | Nyilvános előzetes   |
|Azure Kubernetes Service (AKS)        | Nyilvános előzetes   |
|Azure Service Fabric                  | FE               |
|Azure API Management                  | FE               |
|Azure Active Directory                | FE               |
|Azure Batch                           | FE               |
|App Service-környezet               | FE               |
|Azure Redis Cache                     | FE               |
|Azure HDInsight                       | FE               |
|Virtuálisgép-méretezési csoport             | FE               |
|Azure Cloud Services                  | FE               |


### <a name="virtual-network-integration-details"></a>Virtuális hálózati integráció részletei

Az alábbi ábrán látható, az általános hálózati forgalom PaaS-szolgáltatások eléréséhez. Hozzáférés a virtuális hálózat injektálásos és a vnet szolgáltatásbújtatását jelenik meg. További információ a hálózati szolgáltatás átjárók, a virtuális hálózatok és a szolgáltatáscímkék: [hálózati és az alkalmazásbiztonsági csoportok: Szolgáltatáscímkéket](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![TIC PaaS kapcsolódási lehetőségei](media/tic-diagram-e.png)

1. Egy privát kapcsolat az Azure ExpressRoute használatával. Az ExpressRoute privát társviszony-létesítés, ha a kényszerített bújtatás az összes ügyfél virtuális hálózati forgalom kényszerített expressroute-on keresztül, és a helyszíni biztonsági szolgál. A Microsoft Peering nem szükséges.
2. Az Azure VPN Gateway, ExpressRoute- és Microsoft-Peering együtt használva lehet keverni végpontok közötti IPSec-titkosítás az ügyfél virtuális hálózatán és a helyszíni peremhálózati között. 
3. Hálózati kapcsolat és az ügyfél virtuális hálózatán szabályozza, amely lehetővé teszi ügyfeleink számára engedélyezése vagy megtagadása IP, port és protokoll alapján NSG-k használatával.
4. Hozzon létre egy végpontot az ügyfél szolgáltatás a PaaS-szolgáltatás kiterjeszti az ügyfél virtuális hálózatán.
5. A PaaS-szolgáltatásvégpontot legyen védett az **alapértelmezett az összes elutasítása** , és csak engedélyezze a hozzáférést megadott alhálózatokhoz az ügyfél virtuális hálózatán belül. Az alapértelmezett megtagadja is, amely az internet felől kapcsolatot foglal magában.
6. Más Azure-szolgáltatások, az ügyfél virtuális hálózatán belüli erőforrások elérését igénylő egyikének kell lennie:  
   - Közvetlenül a virtuális hálózaton helyezi üzembe.
   - Szelektív engedélyezett, a megfelelő Azure-szolgáltatás útmutatás alapján.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>"A" lehetőség Központi telepítése egy szolgáltatás (virtuális hálózat injektálás) egy dedikált példánya

Virtuális hálózat beszúrása lehetővé teszi a szelektív üzembe dedikált példányok adott Azure-szolgáltatás, például a HDInsight, a saját virtuális hálózatban. Szolgáltatáspéldányok a rendszer üzembe helyezi egy ügyfél virtuális hálózatán dedikált alhálózatán. Virtuális hálózat injektálási lehetővé teszi, hogy a nem internet irányítható-címeken keresztül a szolgáltatási erőforrások hozzáférésének. A helyszíni példányok ExpressRoute és a egy helyek közötti VPN használatával közvetlenül a virtuális hálózat címterét, a nyilvános interneten címtér tűzfal megnyitása helyett keresztül szolgáltatás példányok eléréséhez. Ha dedikált végpont csatlakozik, mint az IaaS TIC megfelelőségi ugyanezen stratégiák is használhatja. Alapértelmezett útválasztás biztosítja az internetre irányuló forgalmat a rendszer átirányítja a helyszíni kötött virtuális hálózati átjárót. További szabályozhatja a bejövő és kimenő hozzáférést, az adott alhálózat NSG-k segítségével.

![A Virtual network injektálási áttekintése](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>A beállítás használata a b virtuális hálózati Szolgáltatásvégpontok (szolgáltatás-alagút)

Az Azure több-bérlős szolgáltatások egyre növekvő számú ajánlat "Szolgáltatásvégpontok." A Szolgáltatásvégpontok egy alternatív módszer az Azure virtuális hálózatokhoz való integrálásához. Virtuális hálózati Szolgáltatásvégpontok a virtuális hálózati IP-címtér és a virtuális hálózat, a szolgáltatás identitásának bővítése egy közvetlen kapcsolaton keresztül. A virtuális hálózati forgalmat az Azure-szolgáltatás mindig az Azure gerinchálózatán belül marad. 

Miután engedélyezte egy végpontot a szolgáltatáshoz, a szolgáltatás által elérhetővé tett házirendek használatával korlátozza a kapcsolatokat a szolgáltatás virtuális hálózathoz. Hozzáférés-ellenőrzéseket a platform használatát követeli meg az Azure-szolgáltatás. Zárolt erőforrásokhoz való hozzáférés csak ha a kérés származik-e az engedélyezett virtuális hálózat vagy alhálózat, vagy a két IP-címek, amelyek azonosítják a helyszíni forgalom az ExpressRoute használatakor. Ezt a módszert hatékonyan megakadályozza, hogy bejövő/kimenő forgalom közvetlenül és a PaaS-szolgáltatás.

![Szolgáltatásvégpontok áttekintése](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Hálózati helyzetismeretet natív eszközei

Az Azure biztosít natív eszközök segítségével, győződjön meg arról, hogy rendelkezik-e a helyzetismeretet tudni, hogy a forgalom adatfolyamait, a hálózat szükséges. Az eszközök nem szükséges a TIC házirend betartását. Az eszközök jelentősen javítja a biztonsági képességeket.

### <a name="azure-policy"></a>Azure Policy

[Az Azure Policy](https://azure.microsoft.com/services/azure-policy/) egy Azure-szolgáltatás, amely a szervezete biztosít jobb képes naplózási és megfelelőségi irányelveinek kényszerítése. Az Azure Policy jelenleg nyilvános előzetes verzióban elérhető az Azure-szolgáltatások, amelyek a kereskedelmi forgalomban kapható. Az Azure Policy még nem érhető el az Azure Government szolgáltatásban. Ügyfelek tervezése és jövőbeli TIC megfelelőség biztosítása érdekében mostantól az Azure Policy szabályok teszteléséhez. 

Az Azure Policy célja az előfizetés szintjén. A szolgáltatás kínál egy központi felület, ahol megfelelőségi feladatokat, beleértve a hajthatja végre:
- Kezdeményezések kezelése
- A szabályzatdefiníciók konfigurálása
- Megfelelőség naplózása
- Kényszerítse a megfelelőséget
- Kivételek kezelése

Számos beépített definíciókat, valamint a rendszergazdák meghatározhatnak saját egyéni definíciók egyszerű JSON-sablonok használatával. A Microsoft javasolja a rangsort naplózási keresztüli végrehajtás, ahol csak lehetséges.

Az alábbi minta házirendek TIC megfelelőségi célokra használhatók:

|Szabályzat  |Mintaforgatókönyv  |Sablon  |
|---------|---------|---------|
|Felhasználó által definiált útvonaltábla kényszerítése. | Győződjön meg arról, hogy az összes virtuális hálózatot az alapértelmezett útvonal egy engedélyezett virtuális hálózati átjárót a helyszíni útválasztási mutat.    | Ehhez [sablon](https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table). |
|Ha a Network Watcher nincs engedélyezve egy régió naplózása.  | Győződjön meg arról, hogy a Network Watcher engedélyezve van, minden régióban használja.  | Ehhez [sablon](https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled). |
|NSG-t minden alhálózaton található x.  | Győződjön meg arról, hogy minden virtuális hálózatban lévő összes alhálózat NSG-t (vagy egy jóváhagyott NSG-k) az internetes forgalom blokkolva van alkalmazva. | Ehhez [sablon](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet). |
|NSG-t x az összes hálózati adapteren. | Győződjön meg arról, hogy egy NSG-t, az internetes forgalmat blokkolja az összes virtuális gép minden hálózati Adaptereihez van hozzárendelve. | Ehhez [sablon](https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic). |
|Jóváhagyott virtuális hálózat használata a virtuális géphez tartozó hálózati adapter.  | Győződjön meg arról, hogy az összes hálózati adapter egy engedélyezett virtuális hálózaton. | Ehhez [sablon](https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics). |
|Engedélyezett helyek. | Győződjön meg arról, hogy az összes erőforrás telepítve vannak-e a megfelelő virtuális hálózatok és a Network Watcher-konfigurációt régióban.  | Ehhez [sablon](https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs). |
|Nem engedélyezett az erőforrás-típus, például **Tartománycímkét**. | Tiltják a központi telepítését, amelyek nem rendelkeznek egy megfelelőségi terv erőforrástípusok. Ez a szabályzat használatával tiltják a nyilvános IP-cím-erőforrások központi telepítése. Bár az NSG-szabályok segítségével hatékonyan a bejövő internetes forgalom blokkolása, megakadályozza a további nyilvános IP-címek használatát csökkentve a támadási felületet.   | Ehhez [sablon](https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type).  |

### <a name="network-watcher-traffic-analytics"></a>Network Watcher a traffic analytics

Network Watcher [traffic analytics](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/) felhasználhatják folyamat naplóadatokat és a egy magas szintű áttekintést nyújt a hálózati forgalom, a többi naplófájlt. Az adatok akkor hasznos, a naplózás megfelelőségi TIC, és azonosíthatja a problémás. A magas szintű Irányítópult segítségével történik a kommunikáció az internettel és a egy célzott-lista lekérése TIC útválasztás a virtuális gépek gyors képernyőn.

![Traffic Analytics](media/tic-traffic-analytics-1.png)

Használja a **Földrajzitérkép** gyorsan azonosítani az internetes forgalmat a virtuális gépek, a valószínű fizikai célhelyre. Azonosítsa, és osztályozhatja a gyanús helyeket vagy megoszlásától:

![Földrajzi térképet](media/tic-traffic-analytics-2.png)

Használja a **virtuális hálózatok topológia** a felmérési gyorsan a meglévő virtuális hálózatok:

![Hálózati topológia térkép](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Network Watcher következő ugrás tesztek

Hálózati figyelő által figyelt régióban lévő hálózatok következő ugrási vizsgálatokat végezhet. Az Azure Portalon a forrás- és a Network Watcher feloldani a következő ugrás cél minta hálózati folyamathoz tartozó is megadhatja. Ez a teszt virtuális gépek és annak biztosítása érdekében a következő Ugrás célja a várt hálózati virtuális átjáró minta internetcímek futtatni.

![Következő ugrási tesztek](media/tic-network-watcher.png)

## <a name="conclusions"></a>Következtetések

Hozzáférés a Microsoft Azure, Office 365 és Dynamics 365 TIC 2.0 függelék H útmutatása alapján, meghatározott és írásbeli 2018 május, amelyek segítségével egyszerűen konfigurálhatja. Microsoft tisztában van vele, hogy a TIC útmutatást a változhat. A Microsoft-kezdeményezéshez segítenek az ügyfeleknek az útmutató új útmutatót akkor szabadul fel időben találkoznak.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>A függelék: Megbízható internetes kapcsolatok minták végzett gyakori számítási feladatokhoz

| Kategória | Számítási feladat | IaaS | PaaS dedikált / virtuális hálózati injektálás  | Szolgáltatásvégpontok  |
|---------|---------|---------|---------|--------|
| Compute | Azure-beli Linux rendszerű virtuális gépek | Igen | | |
| Compute | Azure-beli Windows virtuális gépek | Igen | | |
| Compute | Virtuálisgép-méretezési csoportok | Igen | | |
| Compute | Azure Functions | | App Service-környezet | |
| Webes és mobil | Belső webalkalmazások | | App Service-környezet| |
| Webes és mobil | Belső mobilalkalmazás | | App Service-környezet | |
| Webes és mobil | API-alkalmazások | | App Service-környezet | |
| Containers | Azure Container Service | | | Igen |
| Containers | Az Azure Kubernetes Service (AKS) \* | | | Igen |
| Adatbázis | Azure SQL Database | | Az Azure SQL Database felügyelt példány \* | Azure SQL |
| Adatbázis | Azure Database for MySQL | | | Igen |
| Adatbázis | Azure Database for PostgreSQL | | | Igen |
| Adatbázis | Azure SQL Data Warehouse | | | Igen |
| Adatbázis | Azure Cosmos DB | | | Igen |
| Adatbázis | Azure Redis Cache | | Igen | |
| Storage | Azure Blob Storage | Igen | | |
| Storage | Azure Files | Igen | | |
| Storage | Az Azure Queue storage | Igen | | |
| Storage | Azure Table Storage | Igen | | |
| Storage | Az Azure Disk storage | Igen | | |

\* Az Azure Governmentben, 2018 május nyilvános előzetes verziója.
