---
title: A koncepció igazolása - Azure DevTest Labs | Microsoft dokumentumok
description: Ismerje meg, hogyan juttathatja el a koncepció igazolását, hogy az Azure DevTest Labs sikeresen beépülhessen egy vállalati környezetbe.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: takamath
ms.openlocfilehash: ca843213760cee60799568a6f33059c2bd91c835
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75643287"
---
# <a name="deliver-a-proof-of-concept"></a>A koncepció igazolásának biztosítása 

Az Azure DevTest Labs egyik legfontosabb forgatókönyve a felhőben futó környezetek fejlesztésének és tesztelésének engedélyezése. Példák erre vonatkozóan:

* Fejlesztői asztalok létrehozása a felhőben.
* Környezetek konfigurálása tesztelésre.
* Virtuális gépekhez és más Azure-erőforrásokhoz való hozzáférés engedélyezése.
* Sandbox-terület beállítása a fejlesztők számára a tanuláshoz és a kísérletezéshez.

A DevTest Labs biztosítja a házirend-korlátokat és a költségvezérlőket is, hogy a vállalat "önkiszolgáló Azure-t" biztosítson a vállalati biztonsági, szabályozási és megfelelőségi szabályzatokat betartó fejlesztőknek. 

Minden vállalat nak különböző követelményei vannak arra vonatkozóan, hogy az Azure DevTest Labs hogyan építhető be sikeresen a környezetébe. Ez a cikk ismerteti a leggyakoribb lépéseket, amelyeket a vállalatoknak el kell végeznie a koncepció sikeres igazolásának biztosításához. A koncepció igazolása az első lépés a sikeres végpontok között üzembe helyezés felé. 

## <a name="getting-started"></a>Első lépések 

A koncepció igazolásának első lépései. Fontos, hogy eltöltsön egy kis időt az Azure és a DevTest Labs megismerésével.  Íme néhány kezdő forrás: 

* [Az Azure Portal ismertetése](https://azure.microsoft.com/features/azure-portal/)
* [A DevTest Labs alapjai](devtest-lab-overview.md)
* [A DevTest Labs által támogatott forgatókönyvek](devtest-lab-guidance-get-started.md)
* [DevTest Labs vállalati dokumentáció](devtest-lab-guidance-prescriptive-adoption.md)
* [Bevezetés az Azure-hálózatba](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Előfeltételek 

A DevTest Labs próba- vagy koncepcióigazolásának sikeres elvégzéséhez néhány előfeltétel áll: 

* **Azure-előfizetés:** A vállalatok gyakran rendelkeznek egy meglévő [nagyvállalati szerződéssel,](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) amely lehetővé teszi az Azure-hoz való hozzáférést, és használhatnak egy meglévő vagy új előfizetést a DevTest Labs számára. Azt is megteheti, hogy a próbaidőszak alatt [visual studio-előfizetést](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) is használhat (kihasználva az ingyenes Azure-krediteket). Ha egyik lehetőség sem érhető el, a vállalat létrehozhat és használhat [ingyenes Azure-fiókot.](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) Ha van érvényben nagyvállalati szerződés, [a nagyvállalati fejlesztési/tesztelési előfizetés](https://azure.microsoft.com/offers/ms-azr-0148p/) használata nagyszerű lehetőség a Windows 10/Windows 8.1 ügyféloperációs rendszerek hez való hozzáférésre, valamint a fejlesztési és tesztelési számítási feladatok kedvezményes díjaira. 
* **Azure Active Directory-bérlő:** A felhasználók kezelésének engedélyezéséhez (például felhasználók hozzáadása vagy labortulajdonosok hozzáadása), ezek a felhasználók az [Azure Active Directory-bérlő](https://azure.microsoft.com/services/active-directory/) az Azure-előfizetésben használt a próbaidőszak részét kell, hogy legyenek. A vállalatok gyakran hibrid [identitást](../active-directory/hybrid/whatis-hybrid-identity.md) állítanak be, hogy a felhasználók a felhőben használhassák a helyszíni identitásukat, de ez nem szükséges a DevTest Labs próbamegoldásához. 

## <a name="scoping-of-the-pilot"></a>A pilóta hatóköre 

Fontos, hogy tervezzen egy kísérleti, mielőtt elkezdené a végrehajtást. Tudva előre, hogy az erőforrások nem marad körül a végtelenségig határozza meg a megfelelő elvárásokat a felhasználók számára a pilóta. 

> [!IMPORTANT]
> Nem tudjuk eléggé hangsúlyozni annak fontosságát, hogy élesen scoping a pilóta és a beállítás elvárások elöl.

Válaszoljon ezekre a kulcsfontosságú kérdésekre, mielőtt elindítja a pilótát: 

* Mit akarsz tanulni, és hogyan néz ki a siker a pilóta számára? 
* Milyen számítási feladatokat vagy forgatókönyveket fog lefedni a pilot? Fontos, hogy csak egy kis készletet határozzon meg annak érdekében, hogy a próbapilóta hatóköre és gyors befejezése legyen. 
* Milyen erőforrásokat kell rendelkezésre állnia a laborban? Például: egyéni képek, marketplace-képek, szabályzatok, hálózati topológia. 
* Kik azok a felhasználók és csapatok, akik részt vesznek a pilóta, hogy ellenőrizze a tapasztalat?  
* Mennyi ideig tart a pilóta? Válasszon olyan időkeretet, amely jól illeszkedik a tervezett hatókörhöz, például két héthez vagy egy hónaphoz. 
* Miután a pilóta elkészült, mi fog történni a lefoglalt erőforrásokkal, amelyeket a pilóta alatt használtak fel? Tervezi, hogy eldobja a kísérleti erőforrásokat? Azt gondolhatnád:
   
   "Ha a pilóta végén el tudjuk dobni a virtuális gépeket és laborokat, akkor egyetlen előfizetést tudunk létrehozni a pilot számára, és minden munkát ott végezhetünk, miközben párhuzamosan megoldjuk a méretezési kérdéseket." 

Van egy általános tendencia, hogy a pilóta "tökéletes", így azonos képviseli, mi a végső állapot lesz, miután a szolgáltatás gördült ki a cégnél. Ez egy hamis feltételezés. Minél közelebb kerüla "tökéletes", annál többet kell befejezni, *mielőtt* elkezdeleg a pilóta. A pilóta célja, hogy helyes döntéseket hozzon a végső szolgáltatás bővítéséről és begurulásáról. 

A pilot kell a kiválasztott a minimálisan szükséges számítási feladatok és függőségek annak kérdésére, hogy az Azure DevTest Labs a megfelelő szolgáltatás a vállalat számára. Azt javasoljuk, hogy válassza ki a legegyszerűbb számítási feladatok a legkevesebb függőségek, hogy segítsen biztosítani a gyors és tiszta siker. Ha ez nem lehetséges, válasszon ki egy legreprezentatívabb számítási feladatot, amely potenciális összetettségeket tár fel, hogy a kísérleti fázisban a sikeresség replikálható legyen a kibővített fázisban. 

A következő példa egy jól ható körű koncepcióigazolást mutat be. 

## <a name="example-proof-of-concept-plan"></a>Példa: koncepcióigazolási terv 

Ez a szakasz egy minta használható a devtest labs kísérleti koncepciójának vizsgálatához. 

> [!TIP]
> A projekt sikertelen beállításának lehetőségének minimalizálása érdekében javasoljuk, hogy ne hagyja ki az ebben a szakaszban ismertetett példát. 

### <a name="overview"></a>Áttekintés 

Azt tervezzük, hogy egy új környezetet fejlesztünk ki az Azure-ban a DevTest Labs alapján, amelyet a gyártók a vállalati hálózat elszigetelt környezeteként használhatnak. Annak megállapításához, hogy a megoldás megfelel-e a követelményeknek, kidolgozunk egy koncepcióigazolást a végpontok között megoldás érvényesítéséhez. Több gyártót is bevontunk, hogy kipróbáljuk és ellenőrizzük az élményt. 

### <a name="outcomes"></a>Eredmények 

Amikor épület egy igazolást a koncepció, összpontosítunk először az eredmények (mit próbálunk elérni). A koncepció igazolásának végére a következőket várjuk: 

* Egy működő, végpontok között működő megoldás a szállítók számára, hogy az Azure Active Directoryban (Azure AD) vendégfiókokhasználatával férjenek hozzá egy elszigetelt környezethez az Azure-ban. A környezet rendelkezik a hatékony munkavégzéshez szükséges erőforrásokkal. 
* A szélesebb körű használatot és bevezetést befolyásoló esetleges blokkolási problémákat a számba veszik és megértik.
* A koncepció igazolásának kidolgozásában részt vevő személyek jól ismerik az összes kódot. Ők is megértik az érintett biztosítékokat, és bíznak a szélesebb körű elfogadásban.

### <a name="open-questions-and-prerequisites"></a>Nyitott kérdések és előfeltételek 

* Van olyan előfizetésünk, amelyet felhasználhatunk ehhez a projekthez? 
* Van egy Azure AD-bérlő és egy Azure AD globális rendszergazda azonosított, aki segítséget és útmutatást nyújt az Azure AD-vel kapcsolatos kérdésekhez? 
* Van-e helyünk az együttműködésre a projekten dolgozó személyek számára? 

   * Forráskód és parancsfájlok (például Az Azure-adatposak) 
   * Dokumentumok (például Microsoft Teams vagy SharePoint)  
   * Beszélgetések (például Microsoft Teams) 
   * Munkaelemek (például Az Azure-táblák) 
* Melyek a szállítók hoz szükséges erőforrások? Ez magában foglalja a hálózaton elérhető alkalmazásokat, mind helyileg, mind más szükséges kiszolgálókon. 
* A virtuális gépek csatlakoznak egy azure-beli tartományhoz? Ha igen, ez lesz az Azure Active Directory tartományi szolgáltatások (Azure AD DS) vagy valami más? 
* Azonosítottuk-e azt a csapatot vagy szállítókat, akik a koncepció bizonyítékának célpontjai lesznek? Kik lesznek a környezet vásárlói?
* Melyik Azure-régiót használjuk a koncepció igazolásához? 
* Van egy listája a szolgáltatások a gyártók számára engedélyezett a DevTest Labs mellett IaaS (VM)? 
* Hogyan tervezzük beadnunk a szállítókat/felhasználókat a labor használatával? 

### <a name="components-of-the-proof-of-concept-solution"></a>A proof-of-concept megoldás összetevői 

Arra számítunk, hogy a megoldás a következő összetevőkkel rendelkezik: 

* Különböző szállítói csapatok az Azure-ban laborok készletét fogják használni.
* A laborok egy olyan hálózati infrastruktúrához csatlakoznak, amely támogatja a követelményeket.
* A szállítók az Azure AD-n és a szerepkör-hozzárendeléseken keresztül férhetnek hozzá a laborokhoz.
* A szállítók nak van egy módja annak, hogy sikeresen csatlakozzanak az erőforrásaikhoz. Pontosabban, a helyek közötti VPN lehetővé teszi a virtuális gépek közvetlen elérését nyilvános IP-címek nélkül.
* A műtermékek készlete lefedi a szükséges szoftvereket, amelyeka gyártóknak szükségük van a virtuális gépeken.

## <a name="additional-planning-and-design-decisions"></a>További tervezési és tervezési döntések 

A teljes DevTest Labs-megoldás kiadása előtt néhány fontos tervezési és tervezési döntést kell hoznia. A koncepció igazolásán végzett munka tapasztalata segíthet abban, hogy ezeket a döntéseket meghozhassa. További megfontolás: 

* **Előfizetési topológia**: Az Azure-beli erőforrások vállalati szintű követelményei egyetlen előfizetésen belül túlnyúlhatnak a [rendelkezésre álló kvótákon](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Ez több Azure-előfizetést és/vagy szolgáltatási kérelmet tesz szükségessé a kezdeti előfizetési korlátok növelése érdekében. Fontos, hogy előre döntse el, hogyan ossza el az erőforrásokat az előfizetések között. Az egyik értékes erőforrás az [előfizetési döntési útmutató,](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) mert később nehéz áthelyezni az erőforrásokat egy másik előfizetésbe. Például egy tesztkörnyezet nem helyezhető át egy másik előfizetésbe a létrehozása után.  
* **Hálózati topológia:** Előfordulhat, hogy a DevTest Labs által automatikusan létrehozott [alapértelmezett hálózati infrastruktúra](../app-service/networking-features.md) nem elegendő a vállalati felhasználók követelményeinek és korlátainak teljesítéséhez. Gyakori, hogy [az Azure ExpressRoute-hoz csatlakoztatott virtuális hálózatok,](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) [hub-and-spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) az előfizetések közötti kapcsolat, és még [a kényszerített útválasztás](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) biztosítása érdekében csak a helyszíni kapcsolat. DevTest Labs lehetővé teszi, hogy a meglévő virtuális hálózatok csatlakozik a laborba, hogy azok használatát, amikor új virtuális gépek et hoz létre a laborban. 
* **Virtuális gépek távoli hozzáférése:** Számos lehetőség van a DevTest Labs ben található virtuális gépek távoli elérésére. A legegyszerűbb a nyilvános IP-k vagy a megosztott nyilvános IP-k használata. Ezek a [beállítások a laborban érhetők el.](devtest-lab-shared-ip.md) Ha ezek a beállítások nem elegendőek, a távelérési átjáró használata is lehetséges. Ez a beállítás a [DevTest Labs vállalati referenciaarchitektúráján](devtest-lab-reference-architecture.md) látható, és a [DevTest Labs távoli asztali átjáró dokumentációjában](configure-lab-remote-desktop-gateway.md)ismertet. A vállalatok az ExpressRoute vagy a helyek közötti VPN segítségével is csatlakoztathatják a laborjaikat a helyszíni hálózatukhoz. Ez a beállítás lehetővé teszi a közvetlen távoli asztali vagy SSH-kapcsolatokat a virtuális gépekhez a saját IP-címük alapján, az internetnek való kitettség nélkül. 
* **Kezelési engedélyek:** A DevTest Labs ben gyakran használt két kulcsengedély a tulajdonos és a [Labor felhasználója.](devtest-lab-add-devtest-user.md) Fontos, hogy a DevTest Labs széles körű kigördülése előtt döntse el, hogy ki lesz megbízva a labor ban minden hozzáférési szinttel. Egy közös modell a költségvetés tulajdonosa (például a csoport vezető), mint a labor tulajdonosa és a csapat tagjai a tesztkörnyezet felhasználói. Ez a modell lehetővé teszi, hogy a költségvetésért felelős személy (csoportérdeklődő) módosítsa a házirend-beállításokat, és a csapat ot a költségkereten belül tartsa.  

## <a name="completing-the-proof-of-concept"></a>A koncepció igazolásának kitöltése 

Miután a várt tanulságokat fedezték, itt az ideje, hogy befejezze a pilóta. Itt az ideje, hogy visszajelzéseket gyűjtsön a felhasználóktól, meghatározza, hogy a próbaüzem sikeres volt-e, és döntse el, hogy a szervezet folytatja-e a DevTest Labs skálázási bevezetését a vállalatban. Ez is egy nagyszerű alkalom, hogy fontolja meg a DevTest Labs és a kapcsolódó erőforrások üzembe helyezésének automatizálását a konzisztencia biztosítása érdekében a méretezési szint bevezetése során. 

## <a name="next-steps"></a>További lépések 

* [DevTest Labs vállalati dokumentáció](devtest-lab-guidance-prescriptive-adoption.md)
* [Hivatkozási architektúra egy vállalat számára](devtest-lab-reference-architecture.md)
* [A DevTest Labs telepítésének növelése](devtest-lab-guidance-orchestrate-implementation.md)
* [Az Azure DevTest Labs megvalósításának koordinálása](devtest-lab-guidance-orchestrate-implementation.md)
