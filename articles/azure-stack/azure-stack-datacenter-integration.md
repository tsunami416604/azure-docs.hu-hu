---
title: Általános datacenter integrációs szempontjai Azure verem integrált rendszerek |} Microsoft Docs
description: Ismerje meg, mi mindent most tervezhető meg és készítse elő a datacenter integrációja több csomópontos Azure verem.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 0c43b66a9d6210ea951af3fae5eca8bc6d47c3d9
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261219"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Integrált Azure verem rendszerek Datacenter integrációs szempontjai
Ha érdekli, egy integrált Azure verem rendszerben, tisztában kell lennie egy központi telepítés, és hogy a rendszer hogyan illeszkedik az Adatközpont a fő szempontokat. Ez a cikk a magas szintű áttekintést nyújt az ezeket a szempontokat segítséget nyújtanak az Azure-verem többcsomópontos rendszer fontos infrastruktúra döntéseket. Ezeket a szempontokat megértését megkönnyíti módon működik-e az OEM hardver gyártójánál azok telepítése Azure verem az Adatközpont.  

> [!NOTE]
> Az Azure verem több csomópontos rendszerekhez csak hitelesített hardveren szállítóktól származó vásárolható meg. 

Azure verem központi telepítéséhez adja meg a tervezési információkat a megoldás szolgáltató telepítési nyissa meg gyorsan és problémamentesen lezajlott folyamat megkezdése előtt kell. Az információk tartományok szükséges hálózati, biztonsági és a legtöbb fontos döntés, amelyre szüksége lehet a Tudásbázis számos különböző területein és döntéshozók azonosító adatok között. Emiatt előfordulhat, hogy van bekérésére személyeket a szervezetében, több csapat, és győződjön meg arról, hogy minden szükséges információ készen áll a telepítés megkezdése előtt. Forduljon a hardver forgalmazójával ezen információk összegyűjtése közben tanácsot hasznos lehet a döntések meghozatala során előfordulhat, hogy rendelkeznek segítséget.

Vizsgálja, és a szükséges információk gyűjtésére, miközben szükség lehet néhány központi telepítés előtti konfigurációs módosításokat végezni a hálózati környezetben. Ez magában foglalhatja foglalása az IP-címteret a útválasztók, kapcsolók és a kapcsolatot az új Azure verem megoldás kapcsolók előkészítése tűzfalak konfigurálása az Azure verem megoldásban. Ügyeljen arra, hogy a tulajdonos terület szakértő legfeljebb vonalas segítséget nyújtanak az tervezés rendelkezik.

## <a name="capacity-planning-considerations"></a>Kapacitástervezésének szempontjai
Egy Azure beszerzési verem megoldás kiértékelésekor hardver konfigurációs beállításokkal kell tenni amely közvetlen hatással vannak az Azure-verem megoldás teljes kapacitásának. Ezek közé tartozik a CPU, a memória sűrűség, a tárolási konfigurációt és a teljes megoldás méretezési (pl. kiszolgálók száma) klasszikus kiválasztása. Egy hagyományos virtualizálási megoldás eltérően ezeket az összetevőket szükséges használható kapacitás egyszerű számtani nem vonatkozik. Az első ilyen ok az, hogy Azure verem tervezett-e magát a megoldáson infrastruktúra vagy felügyeleti összetevőinek üzemeltetésére. A második oka, hogy a megoldás kapacitás némelyike van fenntartva elősegítésére rugalmassági; a szoftvert, hogy a lehető legkevesebb bérlői munkaterheléseket fennakadás frissítése. 

A [Azure verem kapacitás planner számolótábla](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) segít létrehozni küldjenek vonatkozó kétféleképpen kapacitásának tervezési döntések: vagy az egy hardver ajánlat kiválasztásával és az erőforrások vagy kombinációját megfelelően tett kísérlet megadásával a munkaterhelés Azure a verem célja, hogy a hardver SKU által támogatott megtekintéséhez futtassa. Végezetül a táblázat célja Azure verem tervezési és konfigurációs kapcsolódik egy útmutatót a döntések meghozatala érdekében. 

A táblázat nem kívánják a saját vizsgálati és elemzési helyettesíti.  A Microsoft vállal felelősséget vagy rájuk kifejezett vagy vélelmezett felelősséget, a megadott belül a táblázat adatai.



## <a name="management-considerations"></a>Eszközkezeléssel kapcsolatos szempontok
Azure verem rendszer lezárt, ahol az infrastruktúra zárolva van az egy engedélyek és a hálózati szempontjából. Hálózati hozzáférés-vezérlési listák (ACL) minden nem hitelesített bejövő forgalom és az összes szükségtelen kommunikációs infrastruktúra összetevői közötti blokkolása érvényesek. Így a jogosulatlan felhasználók is hozzáférhetnek a rendszerhez.

A napi felügyelete és műveletei nem az infrastruktúra nem korlátozott rendszergazdai hozzáférés van. Az Azure verem operátorok a rendszer a felügyeleti portálon keresztül vagy a keresztül Azure Resource Manager (keresztül a PowerShell vagy a REST API-t) kell kezelni. Nincs más felügyeleti eszközökhöz, például a Hyper-V kezelője vagy a Feladatátvevőfürt-kezelő által a rendszer nem lehet hozzáférni. A számítógép védelme érdekében harmadik féltől származó szoftverek (például ügynökök) nem telepíthető az Azure-verem infrastruktúra összetevői belül. Együttműködés külső felügyeleti és biztonsági szoftver a PowerShell vagy a REST API használatával következik be.

Ha magasabb szintű hozzáférési problémák megoldásához, amelyek nem megoldott riasztás közvetítés lépések van szükség, kérje a Microsoft Support. Támogatásával a ideiglenes teljes rendszergazdai hozzáférést biztosít a rendszer speciális műveletek végrehajtásához módszer. 

## <a name="identity-considerations"></a>Identitás kapcsolatos szempontok

### <a name="choose-identity-provider"></a>Válassza ki az identitásszolgáltató
Fontolja meg, melyik identitásszolgáltató az Azure Alkalmazásveremben üzembe, vagy Azure AD vagy AD FS használni kívánt lesz szüksége. Identitás-szolgáltatóktól nem lehet átállítani a központi telepítés nélkül teljes rendszer újbóli üzembe helyezése után. Ha nem rendelkezik az Azure AD-fiókot, és a Felhőszolgáltató által biztosított olyan fiókot használjon, és úgy dönt, hogy a szolgáltató váltson, és használjon egy másik Azure AD fiókot, ezen a ponton fog kell fordulnia annak érdekében, hogy a megoldás f újbóli telepítéséhez vagy, a költség mellett.



Az identitás szolgáltató választásra nincs hatással a bérlői virtuális gépeket, a identitásrendszere és fiókokat használnak, hogy lehetővé teszi azok csatlakoztatását egy Active Directory-tartomány, stb. Ez a külön.

Az identitásszolgáltató kiválasztására vonatkozó többet is megtudhat a [Azure verem integrált rendszerek kapcsolat modellek cikk](.\azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Az AD FS és a Graph-integráció
Ha az AD FS segítségével az identitás-szolgáltatóként Azure verem telepítése mellett dönt, integrálnia kell az AD FS-példányt Azure veremben keresztül összevonási megbízhatósági kapcsolat egy meglévő AD FS-példányt a. Ez lehetővé teszi, hogy egy meglévő Active Directory-erdőben való hitelesítéshez szükséges erőforrások Azure verem identitások.

A Graph szolgáltatás Azure-készletben is integrálható a meglévő Active Directory. Ez lehetővé teszi, hogy kezelése szerepköralapú hozzáférés-vezérlés (RBAC) Azure-készletben. Delegált hozzáférést egy erőforráshoz, amikor a diagram összetevő keres a felhasználói fiók a meglévő Active Directory-erdőben, az LDAP protokoll használatával.

Az alábbi ábrán látható, integrált az AD FS és a Graph adatforgalmat.
![Az AD FS és a Graph adatforgalmat bemutató ábra](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Licencelési modelltől
Döntse el, melyik licencelési modelltől szeretne használni. Az elérhető lehetőségek függ-e az internethez csatlakozó Azure verem telepít:
- Az egy [kapcsolódó telepítési](azure-stack-connected-deployment.md), dönthet úgy, vagy a fizetési,-akkor-használható, vagy a kapacitás-alapú licencelési. Fizetési,-akkor-használható jelentés használatra, amely majd lesz számlázva keresztül Azure kereskedelmi Azure való kapcsolatot igényel. 
- Csak kapacitás-alapú licencelési támogatott, ha Ön [telepítése leválasztása](azure-stack-disconnected-deployment.md) az internetről. 

A licencelési modellek kapcsolatos további információkért lásd: [Microsoft Azure verem csomagolás és árképzési](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Elnevezési döntések

Tervezze meg az Azure-verem névtér, különösen a régió nevét és külső tartománynév módját gondolja át lesz szüksége. Az Azure Alkalmazásveremben üzembe nyilvánosan elérhető végpontok külső teljesen minősített tartománynevét (FQDN) beállítás a következő két neve kombinációja: &lt; *régió*&gt;.&lt; *fqdn*&gt;. Például *east.cloud.fabrikam.com*. Ebben a példában az Azure-verem portálok lenne a következő URL-címekkel érhető el:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> A régió nevét, az Azure-verem üzembe helyezéshez választott egyedinek kell lennie, és a portál címek jelennek meg. 

A következő táblázat összefoglalja a tartományi névhasználati döntéseket.

| Name (Név) | Leírás | 
| -------- | ------------- | 
|Régió neve | Az első Azure verem terület neve. Ez a név a nyilvános virtuális IP-címek (VIP), amely Azure verem intelligens módon kezeli az FQDN részeként használatos. A terület neve általában egy fizikai hely azonosítója, például egy adatközpont helye lesz. | 
| Külső tartomány neve | A tartománynévrendszer (DNS) zóna neve kívülre irányuló virtuális IP-címmel rendelkező végpontokon. Az FQDN használatos a nyilvános virtuális IP-címmel. | 
| Saját tartománynév (belső) | A létrehozott Azure veremben infrastruktúra felügyeleti tartomány (és belső DNS-zóna) neve. 
| | |

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények

A telepítéshez szüksége Secure Sockets Layer (SSL) tanúsítvány biztosít nyilvánosan elérhető végpontot. Magas szinten a tanúsítványokat az alábbi követelményekkel rendelkezik:

- Egyetlen helyettesítő tanúsítványt is használhatja, vagy egy dedikált tanúsítványok készletét használja, és helyettesítő karaktereket használ, csak a például a tároló és a Key Vault végpontokhoz.
- A nyilvános megbízható hitelesítésszolgáltatótól (CA) adhat ki tanúsítványokat vagy felhasználó által felügyelt hitelesítésszolgáltató.

Milyen PKI kapcsolatos további információk tanúsítványok Azure-vermet, és azok beszerzési tudnivalókat, telepítéséhez szükséges [Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványkövetelmények](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> A megadott PKI-tanúsítvány adatait általános útmutatásként használhatók. Mielőtt bármilyen PKI-tanúsítványok Azure verem szerez be, a Számítógépgyártó hardver partner működik. További részletes tanúsítvány útmutatás és követelmények adnia.


## <a name="time-synchronization"></a>Időszinkronizálás
Ki kell választania egy megadott idő a kiszolgáló Azure verem szinkronizálására használják.  Idő symbolization alapvető fontosságú Azure verem és a infrastruktúra-szerepkörök egymással belső szolgáltatások hitelesítéséhez szükséges Kerberos-jegyek létrehozásához használt.

Meg kell adnia egy IP-Címek használhatók a szinkronizálási kiszolgálót, bár a legtöbb az infrastruktúra-összetevők tudja oldani az egy URL-CÍMÉT, néhány csak egyet támogat IP-címeket. Ha vannak a leválasztott rendszerbe állítási beállításának használata, meg kell adnia a vállalati hálózaton, amelynek Ön a kiszolgálót, hogy elérhető a verem Azure infrastruktúra-hálózat.

## <a name="connect-azure-stack-to-azure"></a>Csatlakozás Azure verem az Azure-bA

A hibrid felhős rendszerekben szüksége lesz megtervezése, hogyan szeretné Azure verem csatlakoztatni az Azure-bA. Azure-veremben lévő virtuális hálózatok az Azure virtuális hálózatokhoz való kapcsolódásának támogatott két módszer áll rendelkezésre: 
- **Pont-pont**. Az IPSec (IKE v1 és IKE v2) virtuális magánhálózati (VPN) kapcsolat. Ilyen típusú kapcsolat van szükség, a VPN-eszköz vagy az Útválasztás és távelérés szolgáltatás (RRAS). Az Azure VPN gatewayek kapcsolatos további információkért lásd: [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Ezen az alagúton keresztül kommunikációs titkosítva van, és biztonságos. A maximális átviteli sebesség (100 – 200 MB/s) alagút azonban sávszélesség korlátozza.
- **Kimenő forgalmat kezelő NAT**. Alapértelmezés szerint az összes virtuális gépek Azure-készletben lesz és külső hálózatokon keresztül kimenő hálózati címfordítást. Minden egyes virtuális hálózati Azure verem létrehozott lekérdezi egy nyilvános IP-cím hozzárendelve. A virtuális gép közvetlenül hozzá van rendelve egy nyilvános IP-címet, vagy egy nyilvános IP-cím egy terheléselosztó mögött van, hogy kimenő forgalmat kezelő NAT használata a virtuális hálózat VIP kimenő hozzáférésének lesz. Ez a módszer csak, amely a virtuális gép által kezdeményezett, és külső hálózatokon (internetes vagy intranetes) szánt kommunikációhoz. A virtuális gép kívülről folytatott kommunikációhoz használható.

### <a name="hybrid-connectivity-options"></a>Hibrid kapcsolati lehetőségek

A hibrid kapcsolat fontos figyelembe kell venni, hogy milyen típusú központi telepítés szeretne ajánlani, és hova szeretné telepíteni. Vegye figyelembe, hogy el kell különítenie bérlői hálózati forgalmat, és hogy konfigurálnia kell az intranetes vagy internetes központi telepítése lesz szüksége.

- **Single-bérlő Azure verem**. Egy Azure verem központi telepítés, a következőhöz, legalább egy hálózati szempontjából, mintha egy bérlő. Nem határozható meg sok bérlői előfizetések, de minden intranetes szolgáltatás, például az összes forgalom az azonos hálózaton keresztül választ. Hálózati forgalmat a előfizetés egy másik előfizetést, az azonos hálózati kapcsolaton keresztül halad, és nem kell különíteni egy titkosított alagúton keresztül.

- **Több-bérlős Azure verem**. Egy Azure Alkalmazásveremben üzembe, ahol minden bérlői előfizetéshez forgalom Azure verem külső hálózatok kötött különítve a többi bérlő hálózati forgalmat kell lennie.
 
- **Intranetes telepítési**. Egy Azure verem központi telepítés, a vállalati intraneten, amely általában a magánhálózati IP-címtér és egy vagy több tűzfal mögött. A nyilvános IP-címek nincsenek valóban nyilvános, mivel azok nem továbbíthatók a nyilvános interneten keresztül közvetlenül.

- **Internet telepítési**. Egy Azure Alkalmazásveremben üzembe, hogy csatlakozik-e a nyilvános internet és felhasználási internetről elérhető nyilvános IP-címek, a nyilvános virtuális IP-címtartomány. A központi telepítés továbbra is helyet kaphat például egy tűzfal mögött található, de a nyilvános virtuális IP-címtartomány a nyilvános internetről és az Azure közvetlenül elérhető.
 
A következő táblázat összefoglalja a hibrid kapcsolat forgatókönyveket, az informatikai szakemberek, hátrányait és használati eseteket.

| Forgatókönyv | Csatlakozási módszer | Informatikai szakemberek | Hátrányok | A jó |
| -- | -- | --| -- | --|
| Az egyszeri bérlői Azure verem, intranet központi telepítés | Kimenő forgalmat kezelő NAT | Gyorsabb átvitelt jobb sávszélességet. Egyszerű megvalósítani; nem szükséges átjárók. | A forgalom nem titkosított; Nincs elkülönítés vagy a halmazon kívül a titkosítás. | Nagyvállalati környezetben, ahol egyetlen bérlő számára egyaránt megbízhatónak számítanak.<br><br>A vállalatok, amelyek az Azure-bA Azure ExpressRoute-kapcsolatcsoportot. |
| Több-bérlős Azure verem intranetes központi telepítés | Telephelyek közötti VPN | A bérlői hálózatok érkező forgalom cél biztonságos. | Pont-pont VPN-alagúton korlátozza a sávszélesség.<br><br>A virtuális hálózat és a VPN-eszköz a cél hálózati átjáró szükséges. | Nagyvállalati környezetben, ahol az egyes bérlői forgalom védetté kell tennie a többi bérlőtől. |
| Az egyszeri bérlői Azure verem, internet-telepítés | Kimenő forgalmat kezelő NAT | Gyorsabb átvitelt jobb sávszélességet. | A forgalom nem titkosított; Nincs elkülönítés vagy a halmazon kívül a titkosítás. | Ha a bérlő lekérdezi a saját Azure Alkalmazásveremben üzembe és az Azure-verem környezetbe dedikált expressroute-kapcsolatcsoporthoz szolgáltatókörnyezetekben. Például az ExpressRoute és Multiprotocol címke váltás (MPLS).
| Több-bérlős Azure verem, internet-telepítés | Telephelyek közötti VPN | A bérlői hálózatok érkező forgalom cél biztonságos. | Pont-pont VPN-alagúton korlátozza a sávszélesség.<br><br>A virtuális hálózat és a VPN-eszköz a cél hálózati átjáró szükséges. | Szolgáltatási forgatókönyvek esetében, ahol a szolgáltató egy több-bérlős felhőalapú ajánlat szeretne, ahol a bérlők számára nem megbízható egymással és a forgalom titkosítani kell.
|  |  |  |  |  |

### <a name="using-expressroute"></a>ExpressRoute segítségével

Kapcsolódás Azure verem keresztül Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) single-bérlő intranet és a több-bérlős forgatókönyvek. Szüksége lesz egy kiosztott ExpressRoute-kapcsolatcsoportot keresztül [a kapcsolat szolgáltatójánál](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Az alábbi ábrán látható ExpressRoute single-bérlő forgatókönyv esetén (ha "Az ügyfél kapcsolat" ExpressRoute-kapcsolatcsoport van).

![Diagram megjelenítése egyetlen-bérlő ExpressRoute forgatókönyv](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Az alábbi ábrán látható ExpressRoute egy több-bérlős forgatókönyv esetén.

![Diagram ábrázoló több-bérlős ExpressRoute forgatókönyv](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Külső figyelése
Az összes riasztás egyetlen nézetben a Azure Alkalmazásveremben üzembe és az eszközök, illetve riasztások integrálja a meglévő informatikai szolgáltatás felügyeleti munkafolyamatainak jegykezelési, akkor [Azure verem integrálása figyelési megoldásoknakkülsődatacenter](azure-stack-integrate-monitor.md).

Az Azure-verem megoldás részét képező, a hardver életciklus állomás kívüli Azure verem hardver OEM szállító által biztosított felügyeleti eszközöket futtató számítógépen. Ezek az eszközök vagy egyéb megoldások, amelyek közvetlenül integrálható az Adatközpont meglévő figyelési megoldások is használhatja.

A következő táblázat összefoglalja a jelenleg rendelkezésre álló beállítások listáját.

| Terület | Külső felügyeleti megoldás |
| -- | -- |
| Az Azure verem szoftver | [Az Azure verem felügyeleti csomag az Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Nagios beépülő modul](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>REST-alapú API-hívások | 
| Fizikai kiszolgálók (bmc-k IPMI keresztül) | OEM hardver - Operations Manager szállító felügyeleti csomag<br>OEM hardveres szállító által biztosított megoldás<br>Hardver szállítójával Nagios beépülő modulok | OEM partner által támogatott felügyeleti megoldás (tartalmazza) | 
| Hálózati eszközök (SNMP) | Az Operations Manager hálózati eszközök felderítését<br>OEM hardveres szállító által biztosított megoldás<br>Nagios kapcsoló beépülő modul |
| Bérlői előfizetéshez állapotfigyelés | [System Center felügyeleti csomag a Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vegye figyelembe az alábbi követelményeknek:
- A megoldás, használja az ügynök nélkül kell lennie. Külső ügynökök belül Azure verem összetevő nem telepíthető. 
- Ha szeretné használni a System Center Operations Manager, az Operations Manager 2012 R2 vagy az Operations Manager 2016 megadása kötelező.

## <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és katasztrófa utáni helyreállítás

Biztonsági mentés és katasztrófa utáni helyreállítás megtervezése magában foglalja a tervezési mindkét az alapul szolgáló Azure verem infrastruktúra IaaS virtuális gépek és PaaS szolgáltatásait üzemeltető, és a bérlői alkalmazások és adatok. Figyelembe kell vennie ezeket külön-külön.

### <a name="protect-infrastructure-components"></a>Infrastruktúra-összetevőihez védelme

Is [biztonsági mentése Azure verem](azure-stack-backup-back-up-azure-stack.md) infrastruktúra-összetevőihez egy SMB-fájlmegosztás meg kell adnia:

- Külső SMB-fájlmegosztásra egy meglévő, Windows-alapú fájlkiszolgálón vagy egy külső eszköz lesz szüksége.
- A biztonsági mentés hálózati kapcsolók és a hardver életciklus gazdagép ugyanennek a megosztásnak kell használnia. OEM hardvergyártójához útmutatás nyújtása a biztonsági mentési és visszaállítási ezeket az összetevőket, ezek a külső Azure verem segítségével. Ön a felelős a OEM gyártója által biztosított a javaslaton alapuló biztonsági mentési munkafolyamatokat futtató.

Végzetes adatvesztés esetén használhatja a központi telepítés a be- és azonosítók, szolgáltatásfiókok, legfelső szintű hitelesítésszolgáltató tanúsítványát, összevont erőforrások (a kapcsolat nélküli telepítések), tervek, ajánlatokat, központi telepítésével kapcsolatos adatokat ismételt előállításával infrastruktúra biztonsági mentése előfizetések, kvótákat, az RBAC-házirend- és szerepkör-hozzárendelések és Key Vault titkos kulcsok.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Bérlői alkalmazások IaaS virtuális gépek védelme

Az Azure verem nem biztonsági mentését bérlői alkalmazások és adatok. Meg kell terveznie a biztonsági mentés és katasztrófa recovery általi védelem külső Azure verem célnak. Bérlői védelem egy bérlő adatvezérelt tevékenységet. Infrastruktúra-szolgáltatási virtuális gépeket, a bérlők technológiákat is használhatnak a Vendég fájl mappák, az alkalmazás adatainak és a rendszerállapot védelme érdekében. Azonban egy vállalati vagy szolgáltatás-szolgáltatóként, érdemes lehet ugyanabban az adatközpontban vagy külsőleg egy felhőbe biztonsági mentési és helyreállítási megoldást kínál.

Linux- vagy Windows IaaS virtuális gépek biztonsági mentésére, segítségével kell biztonságimásolat-készítő hozzáférést a vendég operációs rendszer fájl, mappa, operációs rendszer állapotának és adatainak védelme érdekében. Használhatja az Azure biztonsági mentés, a System Center Data Center Protection Manager, vagy harmadik féltől származó termékekre támogatott.

A másodlagos helyre replikálja az adatokat, és az alkalmazás feladatátvétel levezényléséhez, ha katasztrófa történik, használhatja az Azure Site Recovery, vagy a támogatott harmadik féltől származó termékekre. (A kezdeti kiadásának integrált rendszerek, Azure Site Recovery nem támogatja a feladat-visszavétel. Azonban érhet el egy manuális folyamat feladat-visszavétel.) Is az alkalmazások, amely támogatja a natív replikációt (mint például a Microsoft SQL Server) is replikálja az adatokat egy másik helyre, ahol az alkalmazás fut-e.

> [!IMPORTANT]
> Integrált rendszerek eredeti kiadásának az infrastruktúra-szolgáltatási virtuális gép vendég szintű védelmet technológiákat lesz támogatott. Az alapul szolgáló infrastruktúra-kiszolgálók nem telepíthet ügynököket.

## <a name="learn-more"></a>Részletek

- További információ a használati esetek, megvásárlásáról, partnerek és OEM hardverszállítók: a [Azure verem](https://azure.microsoft.com/overview/azure-stack/) termék oldalát.
- A terv és a földrajzi rendelkezésre állása információt Azure verem integrált rendszerek, tekintse meg a: [Azure verem: Azure kiterjesztése](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>További lépések
[Az Azure verem telepítési kapcsolat modellje](azure-stack-connection-models.md)
