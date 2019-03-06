---
title: Vertikális felskálázás az Azure DevTest Labs-infrastruktúra
description: Ez a cikk nyújt útmutatást az Azure DevTest Labs-infrastruktúra méretezése.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 25a088686c739c53feadd6354baf75f3147bdc33
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431190"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Vertikális felskálázás az Azure DevTest Labs-infrastruktúra
DevTest Labs megvalósítása nagyvállalati szintű, mielőtt nincsenek több fő döntési pontokat. A döntési pontokat magas szintű megismerése segít tervezési döntéseket a szervezete a jövőben. Azonban ezeket a pontokat kell tartalmaz vissza egy szervezet a koncepció igazolása elindítását. A kezdeti tervezésen növelheti az első három területek a következők:

- Hálózati és biztonsági
- Előfizetés-topológia
- Szerepkörök és felelősségek

## <a name="networking-and-security"></a>Hálózati és biztonsági
Hálózati és biztonsági rendszer minden olyan szervezetnek alapja. Egy vállalati szintű üzembe helyezési sok mélyebb elemzésre van szüksége, amíg nincsenek követelmények sikeres elvégzéséhez a koncepció igazolása csökkentett számát. Fókusz néhány legfontosabb területek a következők:

- **Azure-előfizetés** – DevTest Labs, üzembe helyezése az Azure-előfizetéshez hozhat létre erőforrásokat a megfelelő jogosultságokkal hozzáféréssel kell rendelkeznie. Nincsenek különféle módokon, például egy nagyvállalati szerződés és a Használatalapú Azure-előfizetések eléréséhez. Egy Azure-előfizetés hozzáférjenek a további információkért lásd: [licencelése az Azure nagyvállalati](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **A helyszíni erőforrásokhoz való hozzáférés** – egyes szervezetek megkövetelik a DevTest Labs szolgáltatásban létrehozott erőforrások rendelkezik a helyszíni erőforrásokhoz való hozzáférést. A helyszíni környezetből az Azure-bA biztonságos kapcsolat szükséges. Ezért fontos, hogy beállíthatja, VPN vagy Express Route-kapcsolat az első lépések előtt fel vagy konfigurálása. További információkért lásd: [virtuális hálózatok áttekintése](../virtual-network/virtual-networks-overview.md).
- **További biztonsági követelményeket** – egyéb biztonsági követelményekkel, például a számítógép-házirendek, nyilvános IP-címek, kapcsolódjon az internethez való hozzáférést is előfordulhat, hogy kell vizsgálni a koncepció igazolása implementálása előtt forgatókönyvek. 

## <a name="subscription-topology"></a>Előfizetés-topológia
Előfizetés a topológia akkor a fontos tervezési szempontot jelent, az Enterprise DevTest Labs telepítésekor. Azonban nem szükséges minden döntés, amíg megszilárdulni megvalósíthatósági befejezése után. A szükséges vállalati meghatározásában előfizetések számát kiértékelésekor van két szélsőséges: 

- Egy előfizetéssel a teljes cég számára
- Előfizetés felhasználónként

Ezután azt jelöljön ki egyes megközelítés a szakemberek számára.

### <a name="one-subscription"></a>Egy előfizetéssel
A módszer egy előfizetés gyakran nem áll nagy vállalatok kezelhető. Azonban az előfizetések számának korlátozása a következő előnyöket nyújtja:

- **Előrejelzés** vállalati költségeit.  Költségvetés lesz egy előfizetés sokkal egyszerűbb, mivel egyetlen szerepelnek az összes erőforrás. Ez a megközelítés lehetővé teszi, hogy egyszerűbb döntési végzett az elszámolási időszakban egy adott időpontban méri a költségek ellenőrzés során.
- **Kezelhetőségi** virtuális gépeket, összetevők, képleteket, hálózati konfigurációt, engedélyek, szabályzatok, stb. egyszerűbb, mivel a frissítések csak szükséges egy adott előfizetéshez szemben, így a frissítések sok előfizetések között.
- **Hálózatkezelés** erőfeszítés nagyban megkönnyíti a vállalatok számára, ahol a helyszíni kapcsolat egy előfizetés. Virtuális hálózatok összekapcsolása az előfizetések (küllős modell) szükség további előfizetésekkel, amelyhez szükséges további konfigurációs, felügyeleti, IP-címtereket, stb.
- **Csoportmunka** esetén könnyebb mindenki ugyanabban az előfizetésben működik – például egyszerűbb újbóli hozzárendelése a virtuális gép egy munkatársnak, megoszthatja a csapatával erőforrások stb.

### <a name="subscription-per-user"></a>Előfizetés felhasználónként
Felhasználónként különálló előfizetést, az alternatív spektrum egyenlő lehetőségeket biztosít. Sok előfizetést kellene előnyei a következők:

- **Kvóták méretezés Azure** nem akadályozzák a bevezetési fog. Ha például a jelen cikk írásakor az Azure lehetővé teszi, hogy előfizetésenként 200 tárfiókra. Nincsenek a legtöbb szolgáltatás az Azure-ban működési kvóták (számos testre szabható, néhányat nem). Ebben a modellben az előfizetés felhasználónként nagyon valószínűtlen, hogy elérte-e a legtöbb kvóták. Aktuális Azure skálázási kvótákkal kapcsolatos további információkért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md).
- **Költséghelyi elszámolási műveleteket** csoportokat vagy egyedi fejlesztők sokkal egyszerűbb, így a szervezetek költségek a jelenlegi modellt használó tárfiókot válnak.
- **Tulajdonjog és engedélyek** a DevTest Labs-környezetben egyszerűek. Engedélyezi a fejlesztők az előfizetés-szintű elérését és 100 %-os mindenről, beleértve a hálózati konfiguráció, a laborszabályzatok és a virtuális gépek felügyelete.

A vállalat a szélsőséges szélsőség elegendő korlátozásaihoz előfordulhatnak. Ezért szükség lehet az előfizetéseket úgy, hogy ezek a szélsőséges közepén esik. Ajánlott eljárásként a cél a szervezet lehetséges szem a kényszerítési funkciók, amelyek növelik a előfizetéseinek száma az előfizetések a lehető legkevesebb adatokként kell lennie. Kifejezni, előfizetés-topológia kritikus vállalati üzembe helyezéshez, DevTest Labs, de a koncepció igazolása késedelem nélkül kell. Nincsenek további részletek a [Cégirányítási](devtest-lab-guidance-governance-policy-compliance.md) dönthet arról, hogy az előfizetés és a tesztkörnyezet granularitási a szervezet cikkünket.

## <a name="roles-and-responsibilities"></a>Szerepkörök és felelősségek
A DevTest Labs megvalósíthatósági példa három elsődleges szerepkör meghatározott tartományvezérlőkért – előfizetés tulajdonosa, DevTest Labs tulajdonos, DevTest Labs-felhasználó, és opcionálisan egy közreműködő rendelkezik.

- **Előfizetés tulajdonosa** – jogosultsága az előfizetés tulajdonosa felügyelheti az Azure-előfizetés hozzárendelése a felhasználók, a házirendek kezelése, létrehozása és kezelése a hálózati topológiát, beleértve kérő kvótanövelésre stb. További információkért tekintse meg [ezt a cikket](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs-tulajdonos** – a DevTest Labs tulajdonosa a labor teljes rendszergazdai hozzáféréssel rendelkezik. Ezt a személyt felelős, felhasználók hozzáadása vagy eltávolítása költség általános labor beállítások, és más virtuális gép/összetevő-alapú feladatok kezelésére. A lab is tulajdonosa a DevTest Labs-felhasználó összes jogosultságát.
- **DevTest Labs-felhasználó** – a DevTest Labs-felhasználó hozhat létre és felhasználását a virtuális gépek a tesztkörnyezetben. Ezek a személyek néhány minimális felügyeleti képességekkel rendelkeznek az általuk létrehozott virtuális gépeken (indítása/leállítása/delete/konfigurálja a virtuális gépek). A felhasználók nem tudja kezelni a más felhasználók virtuális gépeket.

## <a name="next-steps"></a>További lépések
Ez a sorozat következő cikkében talál: [Koordinálhatja az Azure DevTest Labs megvalósítása](devtest-lab-guidance-orchestrate-implementation.md)