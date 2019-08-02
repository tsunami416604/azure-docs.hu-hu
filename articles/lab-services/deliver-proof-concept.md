---
title: A koncepció igazolása – Azure DevTest Labs | Microsoft Docs
description: Megtudhatja, hogyan teheti elérhetővé a koncepciót, hogy Azure DevTest Labs sikeresen beépíthető legyen egy vállalati környezetbe.
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
ms.openlocfilehash: 6739679b1687393737dda1ded4265a95c4fce169
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501545"
---
# <a name="delivering-a-proof-of-concept"></a>A koncepció igazolása 

Azure DevTest Labs egyik fő forgatókönyve a Felhőbeli fejlesztési és tesztelési környezetek engedélyezése. Példák erre vonatkozóan:

* fejlesztői asztalok létrehozása a felhőben,
* környezetek konfigurálása teszteléshez,
* a virtuális gépekhez és más Azure-erőforrásokhoz való hozzáférés engedélyezése
* egy sandbox-terület beállítása a fejlesztők számára a tanuláshoz és a kísérletezéshez.

A DevTest Labs emellett biztosítja a szabályzat guardrails és a költséghatékonyságot, amely lehetővé teszi a vállalat számára, hogy "önkiszolgáló" Azure-t biztosítson a vállalati biztonsági, szabályozási és megfelelőségi szabályzatoknak megfelelő fejlesztők számára. 

Minden vállalat különböző követelményekkel rendelkezik, hogy a Azure DevTest Labs hogyan lehet sikeresen beépíteni a környezetbe. Ez a cikk azokat a leggyakoribb lépéseket ismerteti, amelyeket a vállalatoknak általában teljesíteniük kell a koncepció sikeres igazolása érdekében, amely az első lépés a sikeres végpontok közötti üzembe helyezés felé. 

## <a name="getting-started"></a>Első lépések 

A koncepció bizonyításának megkezdéséhez. Fontos, hogy némi időt töltsön fel az Azure és a DevTest Labs megismerésére.  Íme néhány kezdő erőforrás: 

* [A Azure Portal ismertetése](https://azure.microsoft.com/features/azure-portal/)
* [Az DevTest Labs alapjai](devtest-lab-overview.md)
* [DevTest Labs – támogatott forgatókönyvek](devtest-lab-guidance-get-started.md)
* [DevTest Labs vállalati dokumentáció](devtest-lab-guidance-prescriptive-adoption.md)
* [Bevezetés az Azure hálózatkezelésbe](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Előfeltételek 

A DevTest Labs szolgáltatással való tesztelés vagy a koncepció igazolása sikeres elvégzéséhez néhány előfeltétel szükséges: 

* **Azure-előfizetés**: A vállalatoknak gyakran van [](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/) meglévő nagyvállalati szerződésük, amely lehetővé teszi az Azure-hoz való hozzáférést, és meglévő vagy új előfizetést is használhat a DevTest Labs szolgáltatásban. Másik lehetőségként a próbaüzem során [Visual Studio Subscription](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) is használható (az ingyenes Azure-kreditek kihasználása). Ha egyik lehetőség sem érhető el, az [ingyenes Azure-fiókot](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) létrehozhatja és használhatja. Ha Nagyvállalati Szerződés van érvényben, a nagyvállalati [fejlesztési/tesztelési](https://azure.microsoft.com/offers/ms-azr-0148p/) előfizetéssel kiváló lehetőség a Windows 10/Windows 8,1 ügyfél operációs rendszereinek elérésére, valamint a fejlesztési és tesztelési feladatok kedvezményes díjszabására. 
* **Azure Active Directory bérlő**:  A felhasználók kezelésének engedélyezéséhez (például felhasználók hozzáadásához vagy labor-tulajdonosok hozzáadásához) a felhasználóknak a próbaüzem Azure-előfizetésében használt [Azure Active Directory bérlő](https://azure.microsoft.com/services/active-directory/) részét kell tartalmazniuk. A vállalatok gyakran [hibrid identitást](../active-directory/hybrid/whatis-hybrid-identity.md) állítanak be, hogy a felhasználók a felhőben használják a helyszíni identitást, de erre nincs szükség a DevTest Labs-pilóta számára. 

## <a name="scoping-of-the-pilot"></a>A pilóta hatóköre 

A megvalósítás megkezdése előtt nagyon fontos a megfelelő pilóta megtervezése. Előzetesen megtudhatja, hogy az erőforrások határozatlan ideig nem maradnak meg, a próbaüzem felhasználói számára megfelelő elvárásokat határoz meg. 

> [!IMPORTANT]
> Nem tudjuk eléggé hangsúlyozni, hogy a próbaüzem és az elvárások előzetesen való kitűzése fontos.

A próbaüzem elindításához kulcsfontosságú kérdésekkel kell válaszolni: 

* Mit szeretne tanulni, és mi a siker a pilóta számára? 
* Milyen számítási feladatokra vagy forgatókönyvekre vonatkozik a próbaüzem? Fontos, hogy csak egy kis készletet adjon meg, amely biztosítja, hogy a próbaüzem hatóköre és gyors befejezése is megtörténjen. 
* Milyen erőforrásoknak kell elérhetőnek lenniük a laborban? Például: egyéni lemezképek, Piactéri lemezképek, szabályzatok, hálózati topológia stb. 
* Kik azok a végfelhasználók/csapatok, akik a pilótában részt vesznek a felhasználói élmény ellenőrzésében?  
* Mi a pilóta időtartama? Válasszon olyan időkeretet, amely jól igazodik a tervezett hatókörhöz, például két hétig vagy egy hónapig. 
* A próba befejezése után mi fog történni a próbaverzióban használt lefoglalt erőforrásokkal? Tervezi a kísérleti erőforrások elvetését?
   
   Ha a pilóta végén megtervezjük a virtuális gépek és a laborok eldobását, egyszerűen beállíthat egy előfizetést a próbaüzem számára, és elvégezheti az összes munkát ott, miközben megoldja a méretezési bevezetési kérdéseket párhuzamosan. 

Általános tendencia, hogy a próbaüzem "tökéletes", így az a végső állapotot jelenti, amely a szolgáltatás a vállalatnál való kivonása után válik elérhetővé. Ez egy hamis feltételezés. Minél közelebb kerül a "tökéletes" eléréshez, annál többet kell elvégeznie a próbaüzem megkezdése *előtt* . A próba célja, hogy a végső szolgáltatás horizontális felskálázásával és bevezetésével megfelelő döntéseket hozzon. 

A pilóta középpontjának a minimálisan szükséges számítási feladatok és függőségek kiválasztásával kell megválaszolnia, hogy Azure DevTest Labs a vállalat számára megfelelő szolgáltatás-e. Célszerű kiválasztani a lehető legkevesebb függőséggel rendelkező legegyszerűbb számítási feladatot a gyorsabb és tiszta siker érdekében. Ha ez nem lehetséges, a következő legjobb lehetőség egy olyan legjelentősebb számítási feladat kiválasztása, amely lehetővé teszi a lehetséges bonyolultságot, hogy a kísérleti fázis sikere a kibővített fázisban is replikálható legyen. 

Az alábbi példa jól hatókörű koncepciót mutat be. 

## <a name="example-proof-of-concept-plan"></a>Példa: a terv igazolása 

Ez a szakasz egy olyan mintát mutat be, amely a DevTest Labs-hez készült próba koncepciójának meghatározására használható. 

> [!TIP]
> Ha csökkenteni szeretné a projekt meghibásodásának lehetőségét, azt javasoljuk, hogy ne ugorja át az ebben a szakaszban ismertetett példát. 

### <a name="overview"></a>Áttekintés 

Az Azure-beli DevTest Labs-alapú új környezet kialakítását tervezzük a szállítók számára a vállalati hálózatról elkülönített környezetként való használatra. Annak megállapításához, hogy a megoldás megfelel-e a követelményeknek, létre fogunk tenni egy olyan koncepciót, amely ellenőrzi a végpontok közötti megoldást, és több szállítót is tartalmaz, hogy kipróbálja és ellenőrizze a felhasználói élményt. 

### <a name="outcomes"></a>Eredmények 

A megvalósíthatósági koncepció kiépítésekor először az eredményekre koncentrálunk (mit próbálunk elérni) – ezeket itt találja.  A koncepció igazolásának végére a következőket várjuk: 

* Teljes körű megoldás a szállítók számára a Azure Active Directory (Azure AD) vendég fiókjainak kihasználása érdekében az Azure-ban olyan elszigetelt környezet eléréséhez, amely a számukra szükséges erőforrásokkal rendelkezik. 
* A szélesebb körű használatot és bevezetést érintő esetleges blokkolási problémák enumerálása és értelmezése.
* A koncepció igazolásának kidolgozásában részt vevő személyeknek jól kell ismerniük az összes kódot. Emellett tisztában vannak az érintett biztosítékokkal és a szélesebb körű bevezetéssel.

### <a name="open-questions--prerequisites"></a>Kérdések megnyitása & előfeltételek 

* Létrehozunk egy előfizetést, amelyet a projekthez használhatunk? 
* Van-e olyan Azure AD-bérlőnk és egy globális rendszergazda, aki az Azure AD-vel kapcsolatos kérdésekkel kapcsolatban nyújt segítséget & útmutatásért? 
* A projektben dolgozó személyek együttműködésének helyszíne: 

   * Forráskód és parancsfájlok (például az Azure Repos) 
   * Dokumentumok (például csapatok vagy SharePoint)  
   * Beszélgetések (például Microsoft Teams) 
   * Munkaelemek (például Azure-táblák) 
* Mik a szükséges erőforrások a szállítók számára? Ez a virtuális gépeken és a hálózaton elérhető egyéb szükséges kiszolgálókon egyaránt helyileg megtalálható. 
* A virtuális gépek csatlakozni fognak az Azure-beli tartományhoz? Ha igen, akkor ez Azure AD Domain Services vagy valami más? 
* Azonosítjuk a csapatot/szállítókat, hogy az a koncepció igazolásának célja lesz? Kik lesznek a környezet ügyfelei?
* Melyik Azure-régiót fogjuk használni a koncepció igazolásához? 
* Vannak olyan szolgáltatások listája, amelyeket a szállítók a IaaS (VM) mellett DevTest Labs használatával is használhatnak? 
* Hogyan tervezhető a beszállítók/felhasználók betanítása a labor használatával? 

### <a name="proof-of-concept-solution-components"></a>A koncepció megoldási összetevőinek igazolása 

A következő összetevőkkel várjuk a megoldást: 

* DevTest Labs-készlet különböző gyártói csapatoknak az Azure-ban.
* A Labs egy hálózati infrastruktúrához csatlakozik, amely támogatja a követelményeket.
* A szállítók hozzáférhetnek a laborokhoz az Azure AD-n keresztül, és szerepkör-hozzárendeléseket biztosítanak a labornak.
* Lehetővé teszik, hogy a szállítók sikeresen csatlakozhassanak az erőforrásaihoz. A két hálózat közötti pont-pont típusú VPN lehetővé teszi, hogy közvetlenül a nyilvános IP-címek nélkül férhessenek hozzá a virtuális gépekhez.
* Azon összetevők összessége, amelyek a virtuális gépeken a szállítók számára szükséges szoftvereket fedik le.

## <a name="additional-planning-and-design-decisions"></a>További tervezési és kialakítási döntések 

A teljes DevTest Labs-megoldás kiadása előtt fontos tervezési és kialakítási döntéseket kell meghoznia. A megvalósíthatósági koncepció használatának tapasztalatai segíthetnek a döntések meghozatalában. A További szempontok a következők: 

* **Előfizetési topológia**: Az Azure-beli erőforrásokra vonatkozó vállalati szintű követelmények a [rendelkezésre álló kvótán](https://docs.microsoft.com/azure/azure-subscription-service-limits)kívülre is kiterjedhetnek egy adott előfizetésen belül, ami több Azure-előfizetést és/vagy szolgáltatási kérelmet tesz szükségessé a kezdeti előfizetési korlátok növelése érdekében. Fontos, hogy eldöntse, hogyan oszthat ki erőforrásokat az előfizetések között, egy értékes erőforrás az [előfizetés döntési útmutatója](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) , mivel a későbbiekben nehéz áthelyezni az erőforrásokat egy másik előfizetésre. Egy DevTest-tesztkörnyezet például nem helyezhető át egy másik előfizetésbe a létrehozása után.  
* **Hálózati topológia**: Előfordulhat, hogy az DevTest Labs által automatikusan létrehozott [alapértelmezett hálózati infrastruktúra](../app-service/networking-features.md) nem elegendő ahhoz, hogy megfeleljen a vállalati felhasználókra vonatkozó követelményeknek és korlátozásoknak. Gyakran előfordul, hogy a [ExpressRoute csatlakoztatott virtuális hálózatok](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/), a [hub és a küllő](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) kapcsolata van az előfizetések között, és még [kényszerített útválasztást](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) is biztosít a helyszíni kapcsolathoz. A DevTest Labs lehetővé teszi a meglévő virtuális hálózatok csatlakozását a laborhoz, hogy a használatuk lehetővé váljon az új virtuális gépek létrehozásakor a laborban. 
* **Virtual Machines távoli elérése**: A DevTest Labs szolgáltatásban található virtuális gépek távoli elérésének számos lehetősége van. A legegyszerűbb a nyilvános IP-címek vagy a megosztott nyilvános IP-címek használata, ezek a [beállítások a laborban érhetők el](devtest-lab-shared-ip.md). Ha ezek a lehetőségek nem elegendőek, a távelérési átjáró használata szintén a [DevTest Labs vállalati hivatkozási architektúráján](devtest-lab-reference-architecture.md) látható, és a [DevTest Labs távoli asztali átjáró dokumentációjában](configure-lab-remote-desktop-gateway.md)ismertetett további információ. A vállalatok Express Route vagy helyek közötti VPN-t is használhatnak a DevTest Labs a helyszíni hálózathoz való csatlakozásához. Ez lehetővé teszi, hogy a távoli asztali vagy SSH-kapcsolatok a saját magánhálózati IP-címük alapján, az internet felé irányuló kapcsolat nélkül is elérhetők legyenek a Virtual Machines. 
* **Kezelési engedélyek**: A DevTest Labs szolgáltatásban gyakran használt két fő engedély a ["tulajdonos" és a "labor user"](devtest-lab-add-devtest-user.md). Fontos eldönteni, hogy a DevTest Labs széleskörű kivonása előtt a laborban minden egyes hozzáférési szinthez meg kell bíznia. A közös modell a költségvetés tulajdonosa (például a csapat vezetője), mint a labor tulajdonosa és a csapat tagjai labor felhasználóként. Ez lehetővé teszi a költségvetésért felelős személy (csapat vezető) számára a házirend-beállítások módosítását, hogy a csapat a Költségvetésen belül maradjon.  

## <a name="completing-the-proof-of-concept"></a>A koncepció igazolásának befejezése 

A várt beolvasás után az idő a próba becsomagolására és befejezésére. Itt az ideje, hogy visszajelzést kapjon a felhasználóktól, megállapítsa, hogy a próbaüzem sikeres volt-e, és döntse el, hogy a szervezet a vállalat DevTest Labs szolgáltatásának fokozatos kiépítését követően halad-e előre. Emellett érdemes megfontolni a DevTest Labs és a kapcsolódó erőforrások üzembe helyezésének automatizálását, hogy a teljes körű kiépítés során biztosítható legyen a konzisztencia. 

## <a name="next-steps"></a>További lépések 

* [DevTest Labs vállalati dokumentáció](devtest-lab-guidance-prescriptive-adoption.md)
* [Vállalati hivatkozási architektúra](devtest-lab-reference-architecture.md)
* [A DevTest Labs üzembe helyezésének vertikális felskálázása](devtest-lab-guidance-orchestrate-implementation.md)
* [A Azure DevTest Labs megvalósításának összehangolása](devtest-lab-guidance-orchestrate-implementation.md)
