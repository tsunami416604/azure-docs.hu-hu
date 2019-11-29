---
title: A Cost és a tulajdonjog kezelése Azure DevTest Labs
description: Ez a cikk olyan információt tartalmaz, amely segít optimalizálni a környezetében a költségeket és a tulajdonjogot.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561666"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Azure DevTest Labs infrastruktúra irányítása – a Cost és a tulajdonjog kezelése
A fejlesztési és tesztelési környezetek létrehozásakor a költségeket és a tulajdonjogot elsődleges szempontnak tekintjük. Ebben a szakaszban olyan információkat talál, amelyek segítségével optimalizálhatja a saját környezetének költségeit és a tulajdonjogát.

## <a name="optimize-for-cost"></a>Optimalizálás a Cost szolgáltatáshoz

### <a name="question"></a>Kérdés
Hogyan optimalizálható a DevTest Labs-környezetbe való bekerülési díj?

### <a name="answer"></a>Válasz
A DevTest Labs számos beépített funkciója segít optimalizálni a költségeket. A felhasználók tevékenységeinek korlátozásához tekintse meg a [Cost Management, a küszöbértékek](devtest-lab-configure-cost-management.md) [és a házirendek](devtest-lab-set-lab-policy.md) cikkeket. 

Az DevTest Labs fejlesztési és tesztelési feladatokhoz való használata során érdemes lehet a [Enterprise dev/test előfizetési kedvezményt](https://azure.microsoft.com/offers/ms-azr-0148p/)használni a nagyvállalati szerződés részeként. Alternatív megoldásként érdemes megfontolnia, hogy az Ön fizetési díjas [DevTest ajánlatát](https://azure.microsoft.com/offers/ms-azr-0023p/)kell figyelembe vennie.

Ez a megközelítés számos előnyt kínál:

- A Windows Virtual Machines, a Cloud Services, a HDInsight, a App Service és a Logic Apps speciális fejlesztési és tesztelési díjai
- Kiváló Nagyvállalati Szerződés (EA) díjszabás más Azure-szolgáltatásokhoz
- Hozzáférés a katalógus exkluzív fejlesztési/tesztelési lemezképeihez, többek között a Windows 8.1- és Windows 10-lemezképekhez
 
Csak az aktív Visual Studio-előfizetők (standard előfizetések, éves felhőalapú előfizetések és havi felhőalapú előfizetések) használhatják a vállalati fejlesztési/tesztelési előfizetésen belül futtatott Azure-erőforrásokat. A végfelhasználók azonban hozzáférhetnek az alkalmazáshoz visszajelzés vagy elfogadási tesztek elvégzéséhez. Ezen az előfizetésen belül az erőforrások használata az alkalmazások fejlesztésére és tesztelésére korlátozódik, rendelkezésre állási garancia nélkül.

Ha úgy dönt, hogy a DevTest ajánlatot használja, vegye figyelembe, hogy ez az előny kizárólag az alkalmazások fejlesztéséhez és teszteléséhez használható. Az előfizetésen belüli használat nem vállal pénzügyi felelősséggel vállalt SLA-t, kivéve az Azure DevOps és a HockeyApp használatát.

## <a name="define-a-role-based-access-across-your-organization"></a>Szerepköralapú hozzáférés definiálása a szervezeten belül
### <a name="question"></a>Kérdés
Hogyan a saját DevTest Labs-környezetek szerepköralapú hozzáférés-vezérlésének megadásával biztosítható, hogy a fejlesztők és a tesztek a munkájukat is szabályozzák? 

### <a name="answer"></a>Válasz
Széles minta van, azonban a részletek a szervezettől függenek.

A központi informatikai részlegnek csak a szükségesnek kell lennie, és lehetővé kell tennie a projekt és az alkalmazás csapatának a szükséges szintű irányítást. Ez általában azt jelenti, hogy a központi informatikai részleg az előfizetés tulajdonosa, és az informatikai funkciók, például a hálózati konfigurációk kezelése. Az előfizetéshez tartozó **tulajdonosok** készletének kicsinek kell lennie. Ezek a tulajdonosok további tulajdonosokat is megadhatnak, ha szükség van rá, vagy előfizetési szintű szabályzatokat alkalmaznak, például "nincs nyilvános IP".

Előfordulhat, hogy a felhasználók olyan részhalmaza van, amelynek hozzáférésre van szüksége egy előfizetéshez, például a Tier1 vagy a 2. szint támogatásához. Ebben az esetben javasoljuk, hogy ezeknek a felhasználóknak a **közreműködők** számára biztosítson hozzáférést, hogy tudják kezelni az erőforrásokat, de nem biztosítanak felhasználói hozzáférést vagy szabályzatok módosítását.

A DevTest Labs-erőforrásnak olyan tulajdonosnak kell lennie, aki a projekt/alkalmazás csapathoz közeledik. Ez azért van, mert megértik a követelményeket a gépek és a szükséges szoftverek tekintetében. A legtöbb szervezetben a DevTest Labs-erőforrás tulajdonosa általában a projekt/fejlesztési vezető. Ez a tulajdonos felügyelheti a felhasználókat és a házirendeket a tesztkörnyezet környezetében, és kezelheti az összes virtuális gépet a DevTest Labs-környezetben.

A Project/Application csapat tagjait fel kell venni a DevTest Labs felhasználói szerepkörbe. Ezek a felhasználók virtuális gépeket hozhatnak létre (a labor és az előfizetési szintű szabályzatok segítségével). Saját virtuális gépeket is kezelhetnek. Nem kezelhetik más felhasználókhoz tartozó virtuális gépeket.

További információkért lásd: [Azure Enterprise állvány – előfizetési útmutató irányítási](/azure/architecture/cloud-adoption/appendix/azure-scaffold) dokumentációja.


## <a name="next-steps"></a>Következő lépések
Lásd: [a vállalati házirend és a megfelelőség](devtest-lab-guidance-governance-policy-compliance.md).
