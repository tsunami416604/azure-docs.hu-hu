---
title: Irányítás az Azure DevTest Labs-infrastruktúra
description: Ez a cikk az Azure DevTest Labs-infrastruktúra cégirányítási útmutatást nyújt.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 623e4392ff8ff75d09da92c54b9e4dc620146432
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48251040"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Irányítás az Azure DevTest Labs-infrastruktúra - kezelheti a költségeket és tulajdonosi viszonyainak
Költség és tulajdonosi is elsődleges szempontok, fontolja meg a fejlesztési és tesztelési környezetek. Ebben a szakaszban található információk segítségével optimalizálhatja a költségeket, és tulajdonosi igazítása megtalálhatja a környezetben.

## <a name="optimize-for-cost"></a>A költségek optimalizálása

### <a name="question"></a>Kérdés
Hogyan tudok optimalizálhatja a költségeket a DevTest Labs-környezetben?

### <a name="answer"></a>Válasz
Nincsenek a DevTest Labs, amelyek segítenek a beépített funkciók számos optimalizálása, a költségek. Lásd: [Költségkezelés, küszöbértékek](devtest-lab-configure-cost-management.md) [, és a házirendek](devtest-lab-set-lab-policy.md) cikkek korlátozhatja a felhasználók tevékenységeit. 

DevTest Labs fejlesztési és tesztelési feladatokhoz használhatja, mint használó érdemes a [nagyvállalati fejlesztési és tesztelési előfizetés Benefit](https://azure.microsoft.com/offers/ms-azr-0148p/), a nagyvállalati szerződés részeként. Azt is megteheti, ha egy Használatalapú ügyfél, érdemes figyelembe venni a [Használatalapú – mint-akkor lépjen DevTest ajánlat](https://azure.microsoft.com/offers/ms-azr-0023p/).

Ez a megközelítés nyújt számos előnye:

- Kedvező fejlesztési/tesztelési díjszabás a Windows virtual machines, cloud services, HDInsight, App Service és Logic Apps
- Más Azure-szolgáltatások díjai nagyszerű nagyvállalati szerződés (EA)
- Hozzáférés a katalógus exkluzív fejlesztési/tesztelési lemezképeihez, többek között a Windows 8.1- és Windows 10-lemezképekhez
 
Csak aktív Visual Studio-előfizetők (standard szintű, éves felhőalapú előfizetések és havi) használható az enterprise Dev/Test előfizetésen belül futtatott Azure-erőforrásokat. Azonban a végfelhasználók elérhetik az alkalmazást a visszajelzés és elfogadottsági. Ezen az előfizetésen belül az erőforrások használata az alkalmazások fejlesztésére és tesztelésére korlátozódik, rendelkezésre állási garancia nélkül.

Ha úgy dönt, hogy a DevTest ajánlat használja, vegye figyelembe, hogy ez a kedvezmény kizárólag fejlesztési és tesztelési az alkalmazások számára. Előfizetésekhez nem vállalunk pénzügyi kötelezettségvállalással egybekötött szolgáltatási szerződést, a Visual Studio Team Services és a HockeyApp kivételével.

## <a name="define-a-role-based-access-across-your-organization"></a>A szerepköralapú hozzáférés megadása a szervezetben
### <a name="question"></a>Kérdés
Hogyan szerepköralapú hozzáférés-vezérlő megadásához a DevTest Labs környezetben, győződjön meg arról, hogy az informatikai részleg szabályozhatja fejlesztői és tesztelési is munkájuk során? 

### <a name="answer"></a>Válasz
Széles körű mintát, van azonban a részletek a szervezet függ.

Központi informatikai kell saját, csak hogy mire szükség, és engedélyeznie kell a projektet és az alkalmazás csapatok rendelkezik a szükséges mértékű. Általában azt jelenti, hogy a központi informatikai az előfizetés tulajdonosa, és kezeli a központi informatikai funkciók, például a hálózati konfiguráció. Készletét **tulajdonosok** előfizetés kis kell lennie. Ezeket a tulajdonosokat további tulajdonosok jelöl ki, amikor szükség van, vagy előfizetési szintű szabályzatok, például "nincs nyilvános IP-címnél.

Előfizetésen, például az 1. szint vagy a 2. rétegbeli támogatási hozzáférést igénylő felhasználók alcsoportjaihoz lehet. Ebben az esetben javasoljuk, hogy ezek a felhasználók számára a **közreműködői** elérni, hogy azok is felügyelheti az erőforrásokat, de nem adja meg a felhasználói hozzáférés vagy házirendek beállítása.

A DevTest Labs-erőforrás a tulajdonosok, akik megközelíti a projekt vagy alkalmazás csapat helyvezérlőhöz kell tartoznia. Fontos, mivel megértik a rájuk vonatkozó követelményeket a gépek és a szükséges szoftverek tekintetében. A szervezetek többségében a DevTest Labs erőforrás tulajdonosa gyakran a projekt/fejlesztési vezető. Erre a tulajdonosra kezelheti a felhasználók és a tesztkörnyezet-szabályzatait, és kezelheti a DevTest Labs-környezetben lévő összes virtuális gép.

A projekt vagy alkalmazás csapat tagjai a a DevTest Labs-felhasználó szerepkör lehet hozzáadni. Ezek a felhasználók is létrehozhatnak virtuális gépeket (beágyazott a labor és előfizetés-szintű szabályzatok). Is kezelhetik saját virtuális gépeiket. Más felhasználókhoz tartozó virtuális gépek, nem tudja kezelni.

További információkért lásd: [Azure enterprise scaffold-előíró előfizetés-irányítás](/architecture/cloud-adoption/appendix/azure-scaffold) dokumentációját.


## <a name="next-steps"></a>További lépések
Lásd: [vállalati házirend és megfelelőség](devtest-lab-guidance-governance-policy-compliance.md).