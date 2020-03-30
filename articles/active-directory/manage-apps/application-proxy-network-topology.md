---
title: Az Azure AD-alkalmazásproxy hálózati topológiájával kapcsolatos szempontok
description: Az Azure AD alkalmazásproxy használatakor a hálózati topológia szempontjait ismerteti.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaceaf1f5e9b6e34ced5db39b61e607fffcb5953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295140"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Hálózati topológia szempontok az Azure Active Directory alkalmazásproxy használatakor

Ez a cikk ismerteti a hálózati topológia szempontok használatakor az Azure Active Directory (Azure AD) alkalmazásproxy az alkalmazások távoli közzétételéhez és eléréséhez.

## <a name="traffic-flow"></a>Forgalom

Ha egy alkalmazást az Azure AD alkalmazásproxyn keresztül tesz közzé, a felhasználók tól az alkalmazásokfelé irányuló forgalom három kapcsolaton keresztül áramlik:

1. A felhasználó csatlakozik az Azure AD alkalmazásproxy szolgáltatás nyilvános végponthoz az Azure-ban
1. Az alkalmazásproxy szolgáltatás csatlakozik az alkalmazásproxy-összekötőhöz
1. Az alkalmazásproxy-összekötő csatlakozik a célalkalmazáshoz

![A felhasználótól a célalkalmazásig irányuló forgalom ról a célalkalmazásra irányuló forgalmat bemutató diagram](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Bérlőhelye és alkalmazásproxy-szolgáltatása

Amikor feliratkozik egy Azure AD-bérlőre, a bérlő régióját a megadott ország/régió határozza meg. Ha engedélyezi az alkalmazásproxyt, a bérlő alkalmazásproxy-szolgáltatáspéldányai ugyanabban a régióban lesznek kiválasztva vagy létrehozva, mint az Azure AD-bérlő, vagy a hozzá legközelebbi régióban.

Ha például az Azure AD-bérlő országa vagy régiója az Egyesült Királyság, az összes Alkalmazásproxy-összekötő szolgáltatáspéldányt használ az európai adatközpontokban. Amikor a felhasználók hozzáférnek a közzétett alkalmazásokhoz, a forgalom az ezen a helyen lévő alkalmazásproxy szolgáltatáspéldányokon keresztül történik.

## <a name="considerations-for-reducing-latency"></a>A késés csökkentésének szempontjai

Minden proxymegoldás késleltetést vezet be a hálózati kapcsolatba. Függetlenül attól, hogy melyik proxy- vagy VPN-megoldást választja távelérési megoldásként, mindig tartalmaz egy olyan kiszolgálókészletet, amely lehetővé teszi a kapcsolatot a vállalati hálózaton belül.

A szervezetek általában kiszolgálóvégpontokat tartalmaznak a peremhálózatukban. Az Azure AD alkalmazásproxyval azonban a forgalom a felhőben a proxyszolgáltatáson keresztül áramlik, miközben az összekötők a vállalati hálózaton vannak. Nincs szükség peremhálózatra.

A következő szakaszok további javaslatokat tartalmaznak a késés további csökkentésére. 

### <a name="connector-placement"></a>Csatlakozó elhelyezése

Alkalmazásproxy a bérlői hely alapján kiválasztja a példányok helyét. Azonban ön dönti el, hogy hol telepítse az összekötőt, így a hálózati forgalom késési jellemzőinek meghatározásához szükséges.

Az alkalmazásproxy-szolgáltatás beállításakor tegye fel a következő kérdéseket:

- Hol található az alkalmazás?
- Hol található a legtöbb felhasználó, aki hozzáfér az alkalmazáshoz?
- Hol található az alkalmazásproxy-példány?
- Van már dedikált hálózati kapcsolata az Azure-adatközpontokkal beállított, például az Azure ExpressRoute vagy egy hasonló VPN?

Az összekötőnek kommunikálnia kell az Azure-ral és az alkalmazásokkal (a traffic flow diagram 2. és 3. lépései), így az összekötő elhelyezése befolyásolja a két kapcsolat késését. Az összekötő elhelyezésének értékelésekor tartsa szem előtt a következő pontokat:

- Ha a Kerberos korlátozott delegálást (KCD) szeretné használni egyszeri bejelentkezéshez, akkor az összekötőnek szüksége van egy adatközpontlátóerre. Ezenkívül az összekötő kiszolgálót tartományhoz kell csatlakoztatni.  
- Ha kétségei vannak, telepítse az összekötőt közelebb az alkalmazáshoz.

### <a name="general-approach-to-minimize-latency"></a>Általános megközelítés a késés minimalizálására

Az egyes hálózati kapcsolatok optimalizálásával minimalizálhatja a végpontok között irányuló forgalom késését. Minden kapcsolat optimalizálható:

- A komló két vége közötti távolság csökkentése.
- A megfelelő hálózat kiválasztása. A dedikált hivatkozások miatt például a nyilvános internet helyett a magánhálózaton való áthaladás gyorsabb lehet.

Ha dedikált VPN- vagy ExpressRoute-kapcsolattal rendelkezik az Azure és a vállalati hálózat között, érdemes lehet ezt használnia.

## <a name="focus-your-optimization-strategy"></a>Összpontosítsd optimalizálási stratégiádat

Kevés, amit tehetünk, hogy ellenőrizzék a kapcsolatot a felhasználók és az Application Proxy szolgáltatás. A felhasználók otthoni hálózatról, kávézóból vagy más országból/régióból érhetik el az alkalmazásokat. Ehelyett optimalizálhatja a kapcsolatot az alkalmazásproxy szolgáltatás az alkalmazásproxy-összekötők az alkalmazásokhoz. Fontolja meg a következő minták beépítését a környezetében.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>1. minta: Helyezze az összekötőt az alkalmazás közelébe

Helyezze az összekötőt a célalkalmazás közelébe az ügyfélhálózatba. Ez a konfiguráció minimalizálja a topográfiai diagram 3.

Ha az összekötőnek szüksége van egy látókörre a tartományvezérlőhöz, akkor ez a minta előnyös. A legtöbb ügyfelünk használja ezt a mintát, mert jól működik a legtöbb esetben. Ez a minta is kombinálható minta 2 a szolgáltatás és az összekötő közötti forgalom optimalizálása érdekében.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>2. minta: Az ExpressRoute előnyeinek kihasználása a Microsoft társviszony-létesítésével

Ha az ExpressRoute-ot microsoftos társviszony-létesítéssel állította be, a gyorsabb ExpressRoute-kapcsolatot használhatja az alkalmazásproxy és az összekötő közötti forgalomhoz. Az összekötő továbbra is a hálózaton van, közel az alkalmazáshoz.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>3. minta: Az ExpressRoute kihasználása privát társviszony-létesítéssel

Ha dedikált VPN-t vagy ExpressRoute-ot állított be az Azure és a vállalati hálózat közötti privát társviszony-létesítéshez, akkor van egy másik lehetősége. Ebben a konfigurációban az Azure-ban a virtuális hálózat általában a vállalati hálózat kiterjesztésének számít. Így telepítheti az összekötőt az Azure-adatközpontban, és továbbra is megfelel az összekötő-alkalmazás kapcsolat alacsony késési követelményeinek.

A késés nem sérül, mert a forgalom egy dedikált kapcsolaton keresztül áramlik. Emellett továbbfejlesztett alkalmazásproxy-szolgáltatás-összekötő késést is kap, mivel az összekötő egy Azure-adatközpontban van telepítve, közel az Azure AD-bérlői helyhez.

![Egy Azure-adatközpontban telepített összekötőt megjelenítő diagram](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Egyéb megközelítések

Bár a cikk középpontjában az összekötő elhelyezése, az alkalmazás elhelyezését is módosíthatja a jobb késési jellemzők elérése érdekében.

A szervezetek egyre inkább áthelyezik hálózataikat üzemeltetett környezetekbe. Ez lehetővé teszi számukra, hogy alkalmazásaikat olyan üzemeltetett környezetben helyezhessék el, amely szintén a vállalati hálózat része, és továbbra is a tartományon belül vannak. Ebben az esetben az előző szakaszokban tárgyalt minták alkalmazhatók az új alkalmazás helyére. Ha ezt a lehetőséget fontolgatja, olvassa el az [Azure AD tartományi szolgáltatások.](../../active-directory-domain-services/overview.md)

Emellett fontolja meg az összekötők [rendszerezését összekötőcsoportok](application-proxy-connector-groups.md) használatával a különböző helyeken és hálózatokon található alkalmazások célzásához.

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Ebben a szakaszban néhány gyakori forgatókönyvet vezetünk be. Tegyük fel, hogy az Azure AD-bérlő (és ezért proxy szolgáltatás végpont) az Egyesült Államokban (USA) található. Az ezekben a használati esetekben tárgyalt megfontolások a világ más régióira is vonatkoznak.

Az ilyen esetekben minden kapcsolatot "ugrásnak" hívunk, és számozunk a könnyebb vita érdekében:

- **1. ugrás**: Felhasználó az alkalmazásproxy szolgáltatáshoz
- **2. ugrás:** Alkalmazásproxy-szolgáltatás az alkalmazásproxy-összekötőhöz
- **3. ugrás:** Alkalmazásproxy-összekötő a célalkalmazáshoz 

### <a name="use-case-1"></a>1. eset használata

**Forgatókönyv:** Az alkalmazás egy szervezet hálózatában található az Egyesült Államokban, és a felhasználók ugyanabban a régióban. Nincs ExpressRoute vagy VPN az Azure-adatközpont és a vállalati hálózat között.

**Ajánlás:** Kövesse az 1. A jobb késés érdekében szükség esetén fontolja meg az ExpressRoute használatát.

Ez egy egyszerű minta. Az ugrás 3 optimalizálása az összekötő alkalmazás közelében történő elhelyezésével. Ez is természetes választás, mivel az összekötő általában az alkalmazás és az adatközpont látótávolságával van telepítve a KCD-műveletek végrehajtásához.

![A felhasználókat, a proxyt, az összekötőt és az alkalmazást ábrázoló diagram az Egyesült Államokban található](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>2. eset használata

**Forgatókönyv:** Az alkalmazás egy szervezet hálózatában található az Egyesült Államokban, és a felhasználók globálisan jelennek meg. Nincs ExpressRoute vagy VPN az Azure-adatközpont és a vállalati hálózat között.

**Ajánlás:** Kövesse az 1.

A közös minta ismét optimalizálja a 3-as ugrást, ahol az összekötőt az alkalmazás közelében helyezi el. A Hop 3 általában nem drága, ha minden ugyanabban a régióban található. Az 1-es ugrás azonban drágább lehet attól függően, hogy hol van a felhasználó, mivel a felhasználóknak világszerte hozzá kell férniük az alkalmazásproxy-példányhoz az Egyesült Államokban. Érdemes megjegyezni, hogy minden proxy megoldás hasonló jellemzőkkel rendelkezik a felhasználók számára, hogy elterjedt világszerte.

![A felhasználók világszerte elterjedtek, de minden más az USA-ban van](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>3. eset használata

**Forgatókönyv:** Az alkalmazás egy szervezet hálózatában található az Egyesült Államokban. ExpressRoute a Microsoft társviszony-létesítés i Azure és a vállalati hálózat között.

**Ajánlás:** Kövesse az 1-es és 2-es mintákat, amelyeket az előző szakaszban ismertetett.

Először helyezze az összekötőt a lehető legközelebb az alkalmazáshoz. Ezután a rendszer automatikusan használja az ExpressRoute-ot a 2-es ugráshoz.

Ha az ExpressRoute-kapcsolat Microsoft-társviszony-létesítést használ, a proxy és az összekötő közötti forgalom az adott kapcsolaton keresztül áramlik. A Hop 2 optimalizált késleltetést.

![A proxy és az összekötő közötti ExpressRoute-ot megjelenítő diagram](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>4.

**Forgatókönyv:** Az alkalmazás egy szervezet hálózatában található az Egyesült Államokban. ExpressRoute privát társviszony-létesítés i Azure és a vállalati hálózat között.

**Ajánlás:** Kövesse a 3.

Helyezze az összekötőt az Azure-adatközpontba, amely expressroute-alapú privát társviszony-létesítésen keresztül csatlakozik a vállalati hálózathoz.

Az összekötő az Azure-adatközpontban helyezhető el. Mivel az összekötő továbbra is rendelkezik egy látótávolságon belül az alkalmazás és az adatközpont a magánhálózaton keresztül, ugrás 3 továbbra is optimalizált. Ezen túlmenően, hop 2 optimalizált tovább.

![Összekötő az Azure adatközpontban, ExpressRoute az összekötő és az alkalmazás között](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>5. eset használata

**Forgatókönyv:** Az alkalmazás egy szervezet európai hálózatában található, az Alkalmazásproxy-példánysal és az Egyesült Államok legtöbb felhasználójával.

**Ajánlás:** Helyezze az összekötőt az alkalmazás közelébe. Mivel az amerikai felhasználók olyan alkalmazásproxy-példányhoz férnek hozzá, amely történetesen ugyanabban a régióban található, az 1-es ugrás nem túl drága. A Hop 3 optimalizálva van. Fontolja meg az ExpressRoute használatát a 2-es ugrás optimalizálásához.

![Az ábra az Egyesült Államokban, az európai összekötőket és alkalmazásokat mutatja be a felhasználókat és a proxyt](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Ebben a helyzetben érdemes lehet egy másik változatot is használni. Ha a szervezet legtöbb felhasználója az Egyesült Államokban van, akkor valószínű, hogy a hálózat az Egyesült Államokra is kiterjed. Helyezze el a csatlakozót az Egyesült Államokban, és használja a dedikált belső vállalati hálózati vonalat az európai alkalmazáshoz. Így a 2-es és 3-as ugrások optimalizáltak.

![Az ábra a felhasználókat, a proxyt és az összekötőt mutatja az Egyesült Államokban, az európai alkalmazást](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>További lépések

- [Alkalmazásproxy engedélyezése](application-proxy-add-on-premises-application.md)
- [Egyszeri bejelentkezés engedélyezése](application-proxy-configure-single-sign-on-with-kcd.md)
- [Feltételes hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)
- [Az alkalmazásproxyval kapcsolatos problémák hibaelhárítása](application-proxy-troubleshoot.md)
