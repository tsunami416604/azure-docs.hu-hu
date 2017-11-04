---
title: "Tervezési megfontolások a Azure verem integrált rendszerek |} Microsoft Docs"
description: "Ismerje meg, mi mindent most tervezhető meg és készítse elő a többcsomópontos Azure verem."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 90f8fa1a-cace-4bfa-852b-5abe2b307615
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: twooley
ms.openlocfilehash: 8484f7947f23a00c05b34babf13cd75f9d227740
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="planning-considerations-for-azure-stack-integrated-systems"></a>Tervezési megfontolások a Azure verem integrált rendszerek

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ha érdekli, egy integrált Azure verem rendszerben, érdemes megismerni néhány főbb tervezési szempontok központi telepítés, és hogy a rendszer hogyan illeszkedik az Adatközpont. Ez a témakör általános áttekintést nyújt az ezeket a szempontokat.

Ha úgy dönt, hogy integrált rendszer beszerzési, a számítógépgyártó (OEM) hardver gyártójától segít a részletes útmutatót a tervezési folyamat részletesebben nagy része. Azokat a tényleges telepítési is végez.

## <a name="management-considerations"></a>Eszközkezeléssel kapcsolatos szempontok

Azure verem rendszer lezárt, ahol az infrastruktúra zárolva van az egy engedélyek és a hálózati szempontjából. Hálózati hozzáférés-vezérlési listák (ACL) minden nem hitelesített bejövő forgalom és az összes szükségtelen kommunikációs infrastruktúra összetevői közötti blokkolása érvényesek. Így a jogosulatlan felhasználók is hozzáférhetnek a rendszerhez.

A napi felügyelete és műveletei nem az infrastruktúra nem korlátozott rendszergazdai hozzáférés van. Az Azure verem operátorok a rendszer a felügyeleti portálon keresztül vagy a keresztül Azure Resource Manager (keresztül a PowerShell vagy a REST API-t) kell kezelni. Nincs más felügyeleti eszközökhöz, például a Hyper-V kezelője vagy a Feladatátvevőfürt-kezelő által a rendszer nem lehet hozzáférni. A számítógép védelme érdekében harmadik féltől származó szoftverek (például ügynökök) nem telepíthető az Azure-verem infrastruktúra összetevői belül. Együttműködés külső felügyeleti és biztonsági szoftver a PowerShell vagy a REST API használatával következik be.

Amikor egy magasabb szintű hozzáférés szükséges problémák megoldásához, amelyek nem megoldott riasztás közvetítés lépéseit, együttműködve támogatása. Támogatásával a ideiglenes teljes rendszergazdai hozzáférést biztosít a rendszer speciális műveletek végrehajtásához módszer. 

## <a name="deploy-connected-or-disconnected"></a>Csatlakoztatott vagy leválasztott telepítése
 
Ha szeretné, központi telepítése az Azure-verem csatlakozzon az internethez (és az Azure-bA), vagy nincs csatlakoztatva. Az Azure veremből, beleértve a hibrid forgatókönyvek között Azure verem és az Azure, a legjobb eredmény volna számára telepíteni kívánja kapcsolódik az Azure. Az alábbi táblázat segít a üzembe helyezési mód közötti fő különbségek összefoglalásához.

| Terület | Csatlakoztatott módú | Kapcsolat nélküli módban |
| -------- | ------------- | ----------|
| Identitásszolgáltató | Azure Active Directory (Azure AD) vagy az Active Directory összevonási szolgáltatások (AD FS) | Csak az AD FS |
| Piactér szindikálási | Töltse le az elemek közvetlenül az Azure piactérről verem Azure piactér | Manuális kezelése az Azure-készletben a piactér szükséges |
| Licencelési modelltől | Fizetési,-akkor-használható vagy kapacitás-alapú | Kapacitás-alapú csak|
| Javítás és frissítés  | Töltse le a frissítési csomagok közvetlenül az Azure-verem | Cserélhető adathordozó és egy különálló csatlakoztatott eszköz igényel |
| | | |

A telepítési módot később a teljes rendszer újratelepítés nélkül nem módosítható.

## <a name="identity-considerations"></a>Identitás kapcsolatos szempontok

### <a name="choose-identity-provider"></a>Válassza ki az identitásszolgáltató

Fontolja meg, melyik identitásszolgáltató az Azure Alkalmazásveremben üzembe, vagy Azure AD vagy AD FS használni kívánt lesz szüksége. Identitás-szolgáltatóktól nem lehet átállítani a központi telepítés nélkül teljes rendszer újbóli üzembe helyezése után.

Az identitás szolgáltató választásra nincs hatással a bérlői virtuális gépeket, a identitásrendszere és fiókokat használnak, hogy lehetővé teszi azok csatlakoztatását egy Active Directory-tartomány, stb. Ez a külön.

**Érdemes lehet az Azure AD okok**

- Már meglévő befektetések (például az Azure vagy Office 365) Azure AD-ben.
- Azure- és Azure verem felhőkben ugyanazzal az identitással használni kívánt.
- Több vállalat kiszolgálása forgatókönyvek, ahol tárolhatja, különböző szervezetek ugyanazon Azure veremben példányra a támogatni kívánt.
- Használandó REST-alapú címtárat tartalomablakban Azure AD Graph rendelkezés felhasználók, csoportok, API-k segítségével stb.

> [!NOTE]
> Azure AD-példányban és egy meglévő AD FS-példányt is egy Azure Alkalmazásveremben üzembe egyszerre nem lehet csatlakoztatni. Az Azure ad-val telepít, és szeretné egy meglévő AD FS-példányt használja, ha akkor is összevonni a helyszíni AD FS-példányt az Azure ad-val.

**Fontolja meg az AD FS okok**

- Nincs nem vagy csak részleges internetkapcsolat.
- Nincsenek szabályozó közös/joghatóság alá.
- Használni kívánt saját identitásrendszere (például a vállalati Active Directory) operátor és a felhasználói fiókok. Ehhez az szükséges, akkor is összevonni egy meglévő AD FS-példányt (a Windows Server 2012, 2012 R2 vagy 2016) Active Directory által támogatott.
- Nincs meglévő Azure beruházások rendelkezik, és nem kívánja használni az Azure AD.

> [!NOTE]
> Akkor is összevonni Azure verem csak egy másik Active Directory által támogatott AD FS-példányt. Egyéb identitás-szolgáltatóktól nem támogatottak. Ha más identitásszolgáltatók, amelyhez Azure verem használata, inkább használja helyette: Azure AD-alapú üzembe helyezési.

### <a name="ad-fs-and-graph-integration"></a>Az AD FS és a Graph-integráció

Ha az AD FS segítségével az identitás-szolgáltatóként Azure verem telepítése mellett dönt, integrálnia kell az AD FS-példányt Azure veremben keresztül összevonási megbízhatósági kapcsolat egy meglévő AD FS-példányt a. Ez lehetővé teszi, hogy egy meglévő Active Directory-erdőben való hitelesítéshez szükséges erőforrások Azure verem identitások.

A Graph szolgáltatás Azure-készletben is integrálható a meglévő Active Directory. Ez lehetővé teszi, hogy kezelése szerepköralapú hozzáférés-vezérlés (RBAC) Azure-készletben. Delegált hozzáférést egy erőforráshoz, amikor a diagram összetevő keres a felhasználói fiók a meglévő Active Directory-erdőben, az LDAP protokoll használatával.

Az alábbi ábrán látható, integrált az AD FS és a Graph adatforgalmat.
![Az AD FS és a Graph adatforgalmat bemutató ábra](media/azure-stack-planning-considerations/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Licencelési modelltől

Döntse el, melyik licencelési modelltől szeretne használni. Csatlakoztatott üzembe helyezés esetén dönthet úgy, vagy a fizetési,-akkor-használható, vagy a kapacitás-alapú licencelési. Fizetési,-akkor-használható jelentés használatra, amely majd lesz számlázva keresztül Azure kereskedelmi Azure való kapcsolatot igényel. Csak kapacitás-alapú licencelési támogatott, ha telepít kapcsolódik az internethez. A licencelési modellek kapcsolatos további információkért lásd: [Microsoft Azure verem csomagolás és árképzési](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

## <a name="naming-decisions"></a>Elnevezési döntések

Tervezze meg az Azure-verem névtér, különösen a régió nevét, és külső tartománynév módját gondolja át lesz szüksége. A nyilvánosan elérhető végpontok Azure verem telepítés teljesen minősített tartománynevét (FQDN) beállítás a következő két neve kombinációja &lt; *régió*&gt;&lt;*external_FQDN*  &gt;, például *east.cloud.fabrikam.com*. Ebben a példában az Azure-verem portálok lenne a következő URL-címekkel érhető el:

- https://Portal.East.cloud.Fabrikam.com
- https://adminportal.East.cloud.Fabrikam.com

A következő táblázat összefoglalja a tartományi névhasználati döntéseket.

| Név | Leírás | 
| -------- | ------------- | 
|régió neve | Az első Azure verem terület neve. Ez a név a nyilvános virtuális IP-címek (VIP), amely Azure verem intelligens módon kezeli az FQDN részeként használatos. A terület neve általában egy fizikai hely azonosítója, például egy adatközpont helye lesz. | 
| Külső tartomány neve | A tartománynévrendszer (DNS) zóna neve kívülre irányuló virtuális IP-címmel rendelkező végpontokon. Az FQDN használatos a nyilvános virtuális IP-címmel. | 
| Saját tartománynév (belső) | A létrehozott Azure veremben infrastruktúra felügyeleti tartomány (és belső DNS-zóna) neve. 
| | |

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények

A telepítéshez szüksége Secure Sockets Layer (SSL) tanúsítvány biztosít nyilvánosan elérhető végpontot. Magas szinten a tanúsítványokat az alábbi követelményekkel rendelkezik:

> [!IMPORTANT]
> Ebben a cikkben a tanúsítványadatokat csak általános útmutatásként valósul meg. A tanúsítványok Azure verem szerez be, mielőtt a OEM hardver partnerrel működik. További részletes tanúsítvány útmutatás és követelmények adnia.

- Egyetlen helyettesítő tanúsítványt is használhatja, vagy egy dedikált tanúsítványok készletét használja, és helyettesítő karaktereket használ, csak a például a tároló és a Key Vault végpontokhoz.
- Tanúsítványok nyilvános megbízható hitelesítésszolgáltató (CA) kell kiállítania vagy felhasználó által felügyelt hitelesítésszolgáltató.
 
A következő táblázat a szolgáltatások és a nyilvánosan elérhető végpontot, amely tanúsítványt igényel a kezdeti Azure Alkalmazásveremben üzembe. 

| A használt | Végpont 
| -------- | ------------- | 
| Az Azure Resource Manager (rendszergazda) | adminmanagment. [régió]. [external_domain] |
| Az Azure Resource Manager (felhasználó) | felügyeleti. [régió]. [external_domain] |
| Portal (rendszergazda) | adminportal. [régió]. [external_domain] |
| Portal (felhasználó) | portál. [régió]. [external_domain] |
| Key Vault (felhasználó) | &#42;. tároló. [régió]. [external_domain] |
| Key Vault (rendszergazda) | &#42;. adminvault. [régió]. [external_domain] |
| Storage | &#42;. a BLOB. [régió]. [external_domain]<br>&#42;. tábla. [régió]. [external_domain]<br>&#42;. várólista. [régió]. [external_domain]  |
| Graph ** | diagram. [régió]. [external_domain] |
| AD FS ** | az AD FS. [régió]. [external_domain] |
| | |

** Diagram és az AD FS-végpontok tanúsítványait csak van szükség az AD FS-telepítések.

Ha egy helyettesítő tanúsítványt használni kívánt, összesen hat alternatív tulajdonosnevek (SAN) a kezdeti Azure Alkalmazásveremben üzembe kell. Az ilyen San hálózatok a következők: 

- &#42;. [régió]. [external_domain]
- &#42;. tároló. [régió]. [external_domain]
- &#42;. adminvault. [régió]. [external_domain]
- &#42;. a BLOB. [régió]. [external_domain]
- &#42;. tábla. [régió]. [external_domain] 
- &#42;. várólista. [régió]. [external_domain]

## <a name="time-synchronization"></a>Időszinkronizálás

IP-cím használatával oldható fel külső idő kiszolgálóval szinkronizálnia kell a Azure verem kiszolgálót. A kiszolgálót a vállalati hálózaton egy kapcsolat nélküli telepítéshez szükséges.

## <a name="network-connectivity"></a>Hálózati kapcsolat

### <a name="dns-integration"></a>DNS-integráció

Hárítsa el a külső DNS-nevek (például www.bing.com) Azure-veremből, lesz szüksége arra, hogy Azure verem használható amelyek Azure verem esetében nem mérvadó DNS-kérelmek továbbítása DNS-kiszolgálók. Központi telepítés bemeneteként meg kell adnia legalább két kiszolgáló használandó DNS-továbbítók az hibatűrést.

Azure verem végpontok külső Azure veremből (például a vállalati erdő) a DNS-nevek feloldására, a DNS-kiszolgálók, amelyek a külső DNS-zóna üzemeltetésére Azure verem a használni kívánt szülőzónát futtató DNS-kiszolgálókkal kell integrálni. Ehhez a DNS-név feloldása Azure verem és a meglévő DNS-zónák az Adatközpont között. Ennek megvalósítása érdekében módszerek, például feltételes továbbítást vagy zóna delegálás fogja használni. Feltételes továbbítás, ha a DNS-kiszolgálók közvetlenül lehet szabályozni a szülőzónát futtató az Azure-verem külső DNS-névtér javasoljuk. (Ha a külső Azure verem DNS-zóna jelenik meg a vállalati tartomány nevét (például azurestack.contoso.com és contoso.com) tartomány, kell használnia a zónadelegálás.

### <a name="network-infrastructure"></a>Hálózati infrastruktúra

A hálózati infrastruktúra Azure verem több logikai hálózatok, a kapcsolók konfigurált áll. Az alábbi ábrán látható, ezeket a logikai hálózatokat, és hogyan integrálható a-tor (TOR) alaplapi felügyeleti vezérlővel (BMC), és szegély (ügyfél hálózati) kapcsolók.

![Logikai hálózati diagram és kapcsoló kapcsolatok](media/azure-stack-planning-considerations/NetworkDiagram.png)

A következő táblázat a logikai hálózatok és a kapcsolódó IPv4 alhálózati megadott tartományok figyelembe kell vennie.

| Logikai hálózat | Leírás | Méret | 
| -------- | ------------- | ------------ | 
| Nyilvános VIP | Egy kis készletét Azure verem szolgáltatások, a többi bérlő virtuális gépek által használt nyilvános IP-címet. Az Azure-verem infrastruktúrát a hálózati 32 címeit használja. Ha azt tervezi, az App Service és az SQL erőforrás-szolgáltató használatára, 7 további használja. | / 26 (62 gazdagépek) – /22 (1022 gazdagép)<br><br>Ajánlott = /24 (254 gazdagép) | 
| Kapcsoló-infrastruktúra | Pont-pont típusú IP-címek útválasztásra, dedikált felügyeleti felületek, és a kapcsoló rendelt visszacsatolási címek váltani. | /26 | 
| Infrastruktúra | Való kommunikációhoz használt Azure verem belső összetevőnél. | /24 |
| Saját | A tárolóhálózat és titkos virtuális IP-címek használata. | /24 | 
| BMC-HEZ | A bmc-k, a fizikai állomáson folytatott kommunikációhoz használandó. | /27 | 
| | | |

### <a name="uplink-to-border-switches"></a>Szegély kapcsolókhoz kimenő

Egy kimenő szegély kapcsoló az Adatközpont konfigurálva lesz szüksége. A következő módszerek valamelyikével integrált Azure verem rendszer részét képező-tor (TOR) kapcsoló a 3. rétegbeli forgalmát irányíthatja:

- Border Gateway Protocol (BGP) 
- a statikus útválasztás

Ajánlott BGP, mivel lehetővé teszi, hogy a szoftver terhelés terheléselosztás Multiplexer (SLB Multiplexer) külső hálózatokhoz által közzétett útvonalak automatikus frissítését. Ez azért fontos, ha nyilvános IP-címtartományok ad hozzá a telepítés után. Ha így tesz, a BGP társviszony-létesítés a a TOR-kapcsolók összesített kapcsolóhoz, hogy a TOR-kapcsolók csatlakozik, a nyilvános IP-címtartományt a telepítés utáni automatikusan van-e hirdetve beavatkozás nélkül összesített kapcsoló hozzáadása. Ha statikus útválasztást használ, manuálisan kell frissíteni az új tartományt útvonalakat minden alkalommal, amikor Ön egy nyilvános IP-Címblokk hozzáadása.

### <a name="proxy-server"></a>Proxykiszolgáló

Az Azure verem csak a transzparens proxy kiszolgálókat támogatja. A transzparens proxy elfogja a hálózati szint kérelmek anélkül, hogy semmilyen különleges ügyfél-konfigurációt.

### <a name="publish-azure-stack-services"></a>Azure verem szolgáltatásokat

Azure verem szolgáltatások felhasználók számára elérhetővé külső Azure veremből lesz szüksége. Az Azure verem hoz létre az infrastruktúra-szerepkörök különböző végpontok. Ezeket a végpontokat a nyilvános IP-címkészletből hozzárendelt virtuális IP-címmel. A DNS-bejegyzés jön létre a külső DNS-zónában, amelyek a központi telepítéskor megadott végpontok. Például a felhasználói portál hozzá van rendelve, a DNS-állomás bejegyzés "portal. <*régió*>. <*external_FQDN*>." 

#### <a name="ports-and-urls"></a>Portok és URL-címek

Azure verem szolgáltatásokat (például a portálok, Azure Resource Manager-, DNS, stb.) külső hálózatok számára elérhető, engedélyeznie kell a fenti végpontokkal való bejövő forgalom az adott URL-címeket, portokat és protokollokat.
 
Központi telepítés, ahol a transzparens proxy kimenő hagyományos proxykiszolgálónak engedélyeznie kell a adott portok és URL-címek kimenő kommunikációra. Idetartozik a portok és URL-címek identitás, piactér szindikálási, javítási és frissítési, regisztrációs és használati adatok.

További információkért lásd:
- [Bejövő portok és protokollok](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [Kimenő portok és URL-címek](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)

### <a name="connect-azure-stack-to-azure"></a>Csatlakozás Azure verem az Azure-bA

A hibrid felhős rendszerekben szüksége lesz megtervezése, hogyan szeretné Azure verem csatlakoztatni az Azure-bA. Azure-veremben lévő virtuális hálózatok az Azure virtuális hálózatokhoz való kapcsolódásának támogatott két módszer áll rendelkezésre: 
- **Pont-pont**. Az IPSec (IKE v1 és IKE v2) virtuális magánhálózati (VPN) kapcsolat. Ilyen típusú kapcsolat van szükség, a VPN-eszköz vagy az Útválasztás és távelérés szolgáltatás (RRAS). Az Azure VPN gatewayek kapcsolatos további információkért lásd: [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Ezen az alagúton keresztül kommunikációs titkosítva van, és biztonságos. A maximális átviteli sebesség (100 – 200 MB/s) alagút azonban sávszélesség korlátozza.
- **Kimenő forgalmat kezelő NAT**. Alapértelmezés szerint az összes virtuális gépek Azure-készletben lesz és külső hálózatokon keresztül kimenő hálózati címfordítást. Minden egyes virtuális hálózati Azure verem létrehozott lekérdezi egy nyilvános IP-cím hozzárendelve. A virtuális gép közvetlenül hozzá van rendelve egy nyilvános IP-címet, vagy egy nyilvános IP-cím egy terheléselosztó mögött van, hogy kimenő forgalmat kezelő NAT használata a virtuális hálózat VIP kimenő hozzáférésének lesz. Ez a módszer csak, amely a virtuális gép által kezdeményezett, és külső hálózatokon (internetes vagy intranetes) szánt kommunikációhoz. A virtuális gép kívülről folytatott kommunikációhoz használható.

#### <a name="hybrid-connectivity-options"></a>Hibrid kapcsolati lehetőségek

A hibrid kapcsolat fontos figyelembe kell venni, hogy milyen típusú központi telepítés szeretne ajánlani, és hova szeretné telepíteni. Vegye figyelembe, hogy el kell különítenie bérlői hálózati forgalmat, és hogy konfigurálnia kell az intranetes vagy internetes központi telepítése lesz szüksége.

- **Single-bérlő Azure verem**. Egy Azure verem központi telepítés, a következőhöz, legalább egy hálózati szempontjából, mintha egy bérlő. Nem határozható meg sok bérlői előfizetések, de minden intranetes szolgáltatás, például az összes forgalom az azonos hálózaton keresztül választ. Hálózati forgalmat a előfizetés egy másik előfizetést, az azonos hálózati kapcsolaton keresztül halad, és nem kell különíteni egy titkosított alagúton keresztül.

- **Több-bérlős Azure verem**. Egy Azure Alkalmazásveremben üzembe, ahol minden bérlői előfizetéshez forgalom Azure verem külső hálózatok kötött különítve a többi bérlő hálózati forgalmat kell lennie.
 
- **Intranetes telepítési**. Egy Azure verem központi telepítés, a vállalati intraneten, amely általában a magánhálózati IP-címtér és egy vagy több tűzfal mögött. A nyilvános IP-címek nincsenek valóban nyilvános, mivel azok nem továbbíthatók a nyilvános interneten keresztül közvetlenül.

- **Internet telepítési**. Egy Azure Alkalmazásveremben üzembe, hogy csatlakozik-e a nyilvános internet és felhasználási internetről elérhető nyilvános IP-címek, a nyilvános virtuális IP-címtartomány. A központi telepítés továbbra is helyet kaphat például egy tűzfal mögött található, de a nyilvános virtuális IP-címtartomány a nyilvános internetről és az Azure közvetlenül elérhető.
 
A következő táblázat összefoglalja a hibrid kapcsolat forgatókönyveket, az informatikai szakemberek, hátrányait és használati eseteket.

| Forgatókönyv | Csatlakozási módszer | Informatikai szakemberek | Hátrányok | A jó |
| -- | -- | --| -- | --|
| Az egyszeri bérlői Azure verem, intranet központi telepítés | Kimenő forgalmat kezelő NAT | Gyorsabb átvitelt jobb sávszélességet. Egyszerű megvalósítani; nem szükséges átjárók. | A forgalom nem titkosított; Nincs elkülönítés vagy a titkosítási túl a TOR. | Nagyvállalati környezetben, ahol egyetlen bérlő számára egyaránt megbízhatónak számítanak.<br><br>A vállalatok, amelyek az Azure-bA Azure ExpressRoute-kapcsolatcsoportot. |
| Több-bérlős Azure verem intranetes központi telepítés | Telephelyek közötti VPN | A bérlői hálózatok érkező forgalom cél biztonságos. | Pont-pont VPN-alagúton korlátozza a sávszélesség.<br><br>A virtuális hálózat és a VPN-eszköz a cél hálózati átjáró szükséges. | Nagyvállalati környezetben, ahol az egyes bérlői forgalom védetté kell tennie a többi bérlőtől. |
| Az egyszeri bérlői Azure verem, internet-telepítés | Kimenő forgalmat kezelő NAT | Gyorsabb átvitelt jobb sávszélességet. | A forgalom nem titkosított; Nincs elkülönítés vagy a titkosítási túl a TOR. | Ha a bérlő lekérdezi a saját Azure Alkalmazásveremben üzembe és az Azure-verem környezetbe dedikált expressroute-kapcsolatcsoporthoz szolgáltatókörnyezetekben. Például az ExpressRoute és Multiprotocol címke váltás (MPLS).
| Több-bérlős Azure verem, internet-telepítés | Telephelyek közötti VPN | A bérlői hálózatok érkező forgalom cél biztonságos. | Pont-pont VPN-alagúton korlátozza a sávszélesség.<br><br>A virtuális hálózat és a VPN-eszköz a cél hálózati átjáró szükséges. | Szolgáltatási forgatókönyvek esetében, ahol a szolgáltató egy több-bérlős felhőalapú ajánlat szeretne, ahol a bérlők számára nem megbízható egymással és a forgalom titkosítani kell.
|  |  |  |  |  |

#### <a name="using-expressroute"></a>ExpressRoute segítségével

Kapcsolódás Azure verem keresztül Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) single-bérlő intranet és a több-bérlős forgatókönyvek. Szüksége lesz egy kiosztott ExpressRoute-kapcsolatcsoportot keresztül [a kapcsolat szolgáltatójánál](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Az alábbi ábrán látható ExpressRoute single-bérlő forgatókönyv esetén (ha "Az ügyfél kapcsolat" ExpressRoute-kapcsolatcsoport van).

![Diagram megjelenítése egyetlen-bérlő ExpressRoute forgatókönyv](media/azure-stack-planning-considerations/ExpressRouteSingleTenant.PNG)

Az alábbi ábrán látható ExpressRoute egy több-bérlős forgatókönyv esetén.

![Diagram ábrázoló több-bérlős ExpressRoute forgatókönyv](media/azure-stack-planning-considerations/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Külső figyelése
Az összes riasztás egyetlen nézetben a Azure Alkalmazásveremben üzembe és az eszközök, illetve riasztások integrálja a meglévő informatikai szolgáltatás felügyeleti munkafolyamatainak jegykezelési, Azure verem integrálható figyelési megoldásoknak külső datacenter.

Az Azure-verem megoldás részét képező, a hardver életciklus állomás kívüli Azure verem hardver OEM szállító által biztosított felügyeleti eszközöket futtató számítógépen. Ezek az eszközök vagy egyéb megoldások, amelyek közvetlenül integrálható az Adatközpont meglévő figyelési megoldások is használhatja.

A következő táblázat összefoglalja a jelenleg rendelkezésre álló beállítások listáját.

| Terület | Külső felügyeleti megoldás |
| -- | -- |
| Az Azure verem szoftver | - [Az Azure verem felügyeleti csomag az Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>- [Nagios beépülő modul](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>-REST-alapú API-hívások | 
| Fizikai kiszolgálók (bmc-k IPMI keresztül) | -Operations Manager szállító felügyeleti csomag<br>-OEM hardveres szállító által biztosított megoldás<br>-Hardvergyártójához Nagios beépülő modulok | OEM partner által támogatott felügyeleti megoldás (tartalmazza) | 
| Hálózati eszközök (SNMP) | -Az operations Manager hálózati eszközök felderítését<br>-OEM hardveres szállító által biztosított megoldás<br>-Nagios kapcsoló beépülő modul |
| Bérlői előfizetéshez állapotfigyelés | - [System Center felügyeleti csomag a Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vegye figyelembe az alábbi követelményeknek:
- A megoldás, használja az ügynök nélkül kell lennie. Külső ügynökök belül Azure verem összetevő nem telepíthető. 
- Ha szeretné használni a System Center Operations Manager, az ehhez szükséges, az Operations Manager 2012 R2 vagy az Operations Manager 2016-ot.

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és katasztrófa utáni helyreállítás

Biztonsági mentés és katasztrófa utáni helyreállítás megtervezése magában foglalja a tervezési mindkét az alapul szolgáló Azure verem infrastruktúra IaaS virtuális gépek és PaaS szolgáltatásait üzemeltető, és a bérlői alkalmazások és adatok. Figyelembe kell vennie ezeket külön-külön.

### <a name="protect-infrastructure-components"></a>Infrastruktúra-összetevőihez védelme

Az Azure verem megadott megosztásra készít biztonsági infrastruktúra-összetevőihez.

- Külső SMB-fájlmegosztásra egy meglévő, Windows-alapú fájlkiszolgálón vagy egy külső eszköz lesz szüksége.
- A biztonsági mentés hálózati kapcsolók és a hardver életciklus gazdagép ugyanennek a megosztásnak kell használnia. OEM hardvergyártójához útmutatás nyújtása a biztonsági mentési és visszaállítási ezeket az összetevőket, ezek a külső Azure verem segítségével. Ön a felelős a OEM gyártója által biztosított a javaslaton alapuló biztonsági mentési munkafolyamatokat futtató.

Végzetes adatvesztés esetén használhatja a központi telepítés a be- és azonosítók, szolgáltatásfiókok, legfelső szintű hitelesítésszolgáltató tanúsítványát, összevont erőforrások (a kapcsolat nélküli telepítések), tervek, ajánlatokat, központi telepítésével kapcsolatos adatokat ismételt előállításával infrastruktúra biztonsági mentése előfizetések, kvótákat, az RBAC-házirend- és szerepkör-hozzárendelések és Key Vault titkos kulcsok.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Bérlői alkalmazások IaaS virtuális gépek védelme

Az Azure verem nem biztonsági mentését bérlői alkalmazások és adatok. Meg kell terveznie a biztonsági mentés és katasztrófa recovery általi védelem külső Azure verem célnak. Bérlői védelem egy bérlő adatvezérelt tevékenységet. Infrastruktúra-szolgáltatási virtuális gépeket, a bérlők technológiákat is használhatnak a Vendég fájl mappák, az alkalmazás adatainak és a rendszerállapot védelme érdekében. Azonban egy vállalati vagy szolgáltatás-szolgáltatóként, érdemes lehet ugyanabban az adatközpontban vagy külsőleg egy felhőbe biztonsági mentési és helyreállítási megoldást kínál.

Linux- vagy Windows IaaS virtuális gépek biztonsági mentésére, segítségével kell biztonságimásolat-készítő hozzáférést a vendég operációs rendszer fájl, mappa, operációs rendszer állapotának és adatainak védelme érdekében. Használhatja az Azure biztonsági mentés, a System Center Data Center Protection Manager, vagy harmadik féltől származó termékekre támogatott.

A másodlagos helyre replikálja az adatokat, és az alkalmazás feladatátvétel levezényléséhez, ha katasztrófa történik, használhatja az Azure Site Recovery, vagy a támogatott harmadik féltől származó termékekre. (A kezdeti kiadásának integrált rendszerek, Azure Site Recovery nem támogatja a feladat-visszavétel. Azonban érhet el egy manuális folyamat feladat-visszavétel.) Is az alkalmazások, amely támogatja a natív replikációt (mint például a Microsoft SQL Server) is replikálja az adatokat egy másik helyre, ahol az alkalmazás fut-e.

> [!IMPORTANT]
> Integrált rendszerek eredeti kiadásának az infrastruktúra-szolgáltatási virtuális gép vendég szintű védelmet technológiákat lesz támogatott. Az alapul szolgáló infrastruktúra-kiszolgálók nem telepíthet ügynököket.

## <a name="next-steps"></a>Következő lépések

- További információ a használati esetek, megvásárlásáról, partnerek és OEM hardverszállítók: a [Azure verem](https://azure.microsoft.com/overview/azure-stack/) termék oldalát.
- A terv és a földrajzi rendelkezésre állása információt Azure verem integrált rendszerek, tekintse meg a: [Azure verem: Azure kiterjesztése](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
