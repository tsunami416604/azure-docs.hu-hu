---
title: Azure Active Directory Application Proxy használata esetén a hálózati topológia szempontjai |} Microsoft Docs
description: Hálózati topológia szempontjai vonatkozik, az Azure AD-alkalmazásproxy használata esetén.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c3f07aa0ef4fea773e214e3e161a61e9337dc617
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Hálózati topológia való használatának szempontjai Azure Active Directory Alkalmazásproxyjával

Ez a cikk ismerteti a hálózati topológia szempontjai közzétételéhez, és távolról fér hozzá az alkalmazások Azure Active Directory (Azure AD) alkalmazásproxy használata esetén.

## <a name="traffic-flow"></a>Forgalom

Ha egy alkalmazás az Azure AD-alkalmazásproxy használatával van közzétéve, a felhasználóktól az alkalmazások a forgalom három kapcsolatokon keresztül:

1. A felhasználó kapcsolódik az Azure AD alkalmazásproxy szolgáltatás nyilvános végpontot az Azure-on
2. Az alkalmazásproxy-szolgáltatás kapcsolódik az alkalmazásproxy-összekötő
3. Az alkalmazásproxy-összekötő kapcsolódik a célalkalmazás

![Ábra: adatforgalmat a felhasználó a célalkalmazás](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Bérlő helyét és az alkalmazásproxy-szolgáltatás

Amikor regisztrál az Azure AD-bérlő, a bérlő régiója megadott ország határozza meg. Alkalmazásproxy engedélyezése, ha az alkalmazásproxy szolgáltatáspéldány, a bérlő a kiválasztott vagy és az Azure AD-bérlő ugyanabban a régióban, vagy a legközelebbi régiót azt létre.

Például ha az Azure AD-bérlő régió az Európai Unió, az alkalmazásproxy-összekötő használatára szolgáltatáspéldány az Azure adatközpontjaiban az EU. Amikor a felhasználók hozzáférést a közzétett alkalmazásokhoz, a forgalom végig kell vinnie az alkalmazásproxy-szolgáltatás példányainak ezen a helyen.

## <a name="considerations-for-reducing-latency"></a>Csökkenti a késéseket szempontjai

Minden proxymegoldások késés bevezetéséhez a hálózati kapcsolatot. Függetlenül attól, milyen proxy- vagy VPN-megoldás a távelérési megoldás választja azt mindig tartalmazza a engedélyezése a kapcsolatot a vállalati hálózaton belüli kiszolgálók egy csoportja.

A szervezetek server végpontok rendszerint tartalmazza a szegélyhálózaton. Az Azure AD-alkalmazásproxy azonban forgalmat a proxy szolgáltatás a felhőben közben az összekötők a vállalati hálózaton találhatók. Nincs szegélyhálózat hálózatra azért szükség.

A következő szakaszok tartalmazzák még tovább a késés csökkentése érdekében további javaslatokat. 

### <a name="connector-placement"></a>Összekötő elhelyezése

Alkalmazásproxy példányok helyét, a bérlő helye alapján választja ki. Azonban elérhetővé döntse el, hogy az összekötő telepítési helyének nyílik lehetősége a hálózati forgalom késés jellemzői meghatározásához.

Az alkalmazásproxy beállításakor tegye fel a következő kérdéseket:

* Az alkalmazás helyét?
* Hol található a legtöbb olyan felhasználók számára, akik az alkalmazást?
* Az alkalmazásproxy-példány helyét?
* Már rendelkezik egy dedikált hálózati kapcsolat beállításához, például az Azure ExpressRoute vagy egy hasonló VPN Azure adatközpontjaiban?

Az összekötő rendelkezik kommunikálni az Azure, és az alkalmazások (2. és a forgalmának folyamatábrája 3. lépés), így az összekötő érinti elhelyezésének e két kapcsolat késleltetése. Ha az összekötő a elhelyezésének értékelésekor, vegye figyelembe a következő szempontokat:

* Ha egyszeri bejelentkezéshez a Kerberos által korlátozott delegálás (KCD) használni kívánt, az összekötő egy sor a láthatáron adatközpontba szüksége lesz. Emellett az összekötő kiszolgálónak eltérőnek kell lennie a tartományhoz.  
* A kétségei vannak, az alkalmazásnak a közelebb az összekötő telepítéséhez.

### <a name="general-approach-to-minimize-latency"></a>Általános megközelítést a késés csökkentése érdekében érdemes

A Tiltás késése a végpont forgalmat a minimalizálhatja optimalizálásával minden hálózati kapcsolat. Minden kapcsolat segítségével optimalizálható:

* Csökkenti a Ugrás két vége közötti távolságot.
* A megfelelő hálózati átjárása kiválasztása. Például a nyilvános Internet helyett egy magánhálózaton áthaladó gyorsabb lehet, dedikált hivatkozások miatt.

Ha egy dedikált VPN- vagy ExpressRoute hivatkozást Azure és a vállalati hálózat között, érdemes lehet azt használja.

## <a name="focus-your-optimization-strategy"></a>Összpontosítson az optimalizálás stratégia

Nincs kevés szabályozására, a felhasználók és az alkalmazásproxy-szolgáltatás közötti kapcsolat is van. Felhasználók férhetnek hozzá az alkalmazások egy otthoni hálózatot, egy kávézóban vagy egy másik országból. Ehelyett az alkalmazásproxy-összekötőt az alkalmazások számára az alkalmazásproxy közötti kapcsolatok is optimalizálhatja. Vegye fontolóra a következő minták a környezetben.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>1. minta: Helyezze az megközelíti a az alkalmazás-összekötő

Az összekötő megközelíti a célalkalmazás az ügyfél hálózati helye. Ebben a konfigurációban minimalizálja a 3. lépés a topográfia diagramon, mivel az összekötő és az alkalmazás Bezárás. 

Ha az összekötő egy sor a láthatáron a tartományvezérlőre, zárolás előnyös. A felhasználók a legtöbb ebben a mintában használható, mert a legtöbb esetben jól alkalmazható. Ebben a mintában is kombinálható mintát 2 a szolgáltatás és az összekötő közötti forgalom optimalizálása érdekében.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>2. szabály: A nyilvános társviszony ExpressRoute előnyeinek kihasználása

Ha ExpressRoute állítsa be a nyilvános társviszony-létesítést, használhatja a gyorsabb ExpressRoute-kapcsolat Proxy és az összekötő közötti forgalom. Az összekötő továbbra is a hálózaton, az alkalmazás közel van.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>3. szabály: A magánhálózati társviszony-létesítés ExpressRoute előnyeinek kihasználása

Ha egy dedikált VPN vagy ExpressRoute állítsa be a magánhálózati társviszony-létesítés Azure és a vállalati hálózat között, akkor is van lehetősége. Ebben a konfigurációban a virtuális hálózat az Azure-ban általában a vállalati hálózat kiterjesztése tekinteni. Így az összekötő telepítése az Azure adatközpontjában, és továbbra is megfelel kis késés az összekötő-alkalmazás kapcsolat.

Késés nem sérül, mert a forgalom áramlik dedikált kapcsolaton keresztül. Mivel az összekötő telepítve van egy Azure-adatközpontban megközelíti a Azure AD-bérlő helye a is kap továbbfejlesztett alkalmazásproxy-szolgáltatás-összekötő várakozási ideje.

![Összekötő telepítve vannak az Azure-adatközpontban bemutató ábra](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Más megoldások

Bár ez a cikk célja összekötő elhelyezése, az alkalmazások jobb késés jellemzőit elhelyezésére is módosíthatja.

Egyre több szervezet áthelyezi hálózataikat üzemeltetett környezetekben. Ez lehetővé teszi, hogy helyezze alkalmazások egy része emellett a vállalati hálózatában üzemeltetési környezetben, és továbbra is a tartományon belül kell. Ebben az esetben a minták, a fenti szakaszokban ismertetett alkalmazhatók az új alkalmazás helyre. Ha ezt a beállítást fontolóra vette, lásd: [Azure AD tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md).

Emellett vegye figyelembe az összekötők használatával rendszerezéséhez [összekötő csoportok](application-proxy-connector-groups.md) cél alkalmazásokra, amelyek különböző helyekre és -hálózatok találhatók. 

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Ez a szakasz azt ismerteti keresztül néhány olyan gyakori forgatókönyvet. Feltételeztük, hogy az Azure AD-bérlő (és ezért a proxy szolgáltatás végpontjának) az Amerikai Egyesült Államok (US) található. A szempontok ezen esetek is alkalmazhat a világ minden táján más régiókban használatát tárgyalja.

A forgatókönyvekben azt hívjuk kapcsolatonként egy "ugrást" és egyszerűbb vitafórum number őket:

- **1 ugrások**: az alkalmazásproxy felhasználó
- **2 ugrások**: alkalmazásproxy-szolgáltatás és az alkalmazásproxy-összekötő
- **3 ugrások**: a célalkalmazás alkalmazásproxy-összekötő 

### <a name="use-case-1"></a>Használati eset 1

**Forgatókönyv:** az alkalmazás egy szervezet hálózatához az Egyesült Államokban, a felhasználók ugyanabban a régióban van. Nem expressroute-on vagy VPN létezik-e az Azure-adatközpontban és a vállalati hálózat között.

**Javaslat:** kövesse mintát 1, az előző szakaszban ismertetett. Továbbfejlesztett késésének érdemes lehet ExpressRoute, ha szükséges.

Ez az egyszerű mintát. Ugrás 3 optimalizálhatja úgy, hogy az összekötő közel az alkalmazást. Ennek oka az is természetes választani, az összekötő általában telepítve van a sor a láthatáron az alkalmazás és az Adatközpont Kerberos által korlátozott Delegálás műveletek végrehajtásához.

![Hogy a felhasználók, a proxy, a összekötő és a alkalmazás van minden az Egyesült Államokban bemutató ábra](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Használati eset 2

**Forgatókönyv:** egy szervezet hálózatához az Egyesült Államokban, az alkalmazás van globálisan felülbírálásokkal felhasználókkal. Nem expressroute-on vagy VPN létezik-e az Azure-adatközpontban és a vállalati hálózat között.

**Javaslat:** kövesse mintát 1, az előző szakaszban ismertetett. 

Ebben az esetben a közös minta nem optimalizálása Ugrás 3, a közel az alkalmazás-összekötő helyétől. Ugrás 3 nincs általában költséges, ha az összes belül ugyanabban a régióban. Azonban Ugrás 1 lehet drágább, attól függően, hogy a felhasználó esetén, mert a világ különböző kell férnie az alkalmazásproxy-példány az Egyesült Államokban. Érdemes megjegyezni, hogy bármely proxy megoldás globálisan alatt felülbírálásokkal felhasználók vonatkozó hasonló jellemzőkkel rendelkezik-e.

![Felhasználók globálisan van elosztva, de a proxy, az összekötő és az alkalmazás az Egyesült Államokban megjelenítő ábra](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Használati eset 3

**Forgatókönyv:** az alkalmazás megtalálható-e az Egyesült Államokban egy szervezet hálózatához. A nyilvános társviszony ExpressRoute Azure és a vállalati hálózat között van.

**Javaslat:** hajtsa végre az 1. és 2, az előző szakaszban ismertetett minták.

Először az alkalmazást a lehető legközelebb az összekötőt. Ezt követően a rendszer automatikusan használja az ExpressRoute az ugrások 2. 

Ha az ExpressRoute-kapcsolat nyilvános társviszony használja, a között a proxy- és az összekötő a forgalom adott hivatkozáson keresztül. Ugrás 2 optimalizálta késés.

![A proxy- és összekötő között ExpressRoute bemutató ábra](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Használati eset 4

**Forgatókönyv:** az alkalmazás megtalálható-e az Egyesült Államokban egy szervezet hálózatához. A magánhálózati társviszony-létesítés ExpressRoute Azure és a vállalati hálózat között van.

**Javaslat:** kövesse mintát 3, az előző szakaszban ismertetett.

Az összekötő elhelyezni az Azure adatközpontjában, amely csatlakozik a vállalati hálózaton keresztül ExpressRoute magánhálózati társviszony-létesítés. 

Az összekötő az Azure-adatközpontban helyezhető. Mivel az összekötő továbbra is csak egy sor a láthatáron az alkalmazás és a magánhálózaton keresztül az adatközpont, hop 3 optimalizált marad. Ezenkívül további optimalizálták Ugrás 2.

![Ábra: az összekötő egy Azure-adatközpontban és ExpressRoute az összekötő és az alkalmazás között](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Használati eset 5

**Forgatókönyv:** az alkalmazás megtalálható-e az EU az alkalmazásproxy-példány és a legtöbb felhasználó az Egyesült Államokban egy szervezet hálózatához.

**Javaslat:** helyezze el az összekötő közel az alkalmazást. USA felhasználók használják az alkalmazásproxy-példánya, amely ugyanabban a régióban kell történik, mert Ugrás 1 nincs túl drága. Ugrás 3 megfelelően lett optimalizálva. Érdemes ExpressRoute Ugrás 2 optimalizálása érdekében. 

![Az Egyesült Államokban, az összekötő és az Európai Unió app felhasználók és a proxy bemutató ábra](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Is megfontolhatja egy variant ebben a helyzetben. Ha a szervezet a legtöbb felhasználói az Egyesült Államokban nem, akkor valószínűleg, amely a hálózat, valamint az USA terjed. Az összekötő elhelyezni az Egyesült Államokban, és használja a dedikált belső vállalati hálózaton az alkalmazásnak az EU-ban. Ez úgy ugrások 2 és 3 vannak optimalizálva.

![Az Egyesült Államokban, az Európai Unió alkalmazás a felhasználók, proxy és összekötő bemutató ábra](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>További lépések

- [Alkalmazásproxy engedélyezése](application-proxy-enable.md)
- [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
- [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Az alkalmazásproxy problémák elhárítása](../active-directory-application-proxy-troubleshoot.md)
