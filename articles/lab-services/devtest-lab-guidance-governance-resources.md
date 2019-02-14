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
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: aff57e58ffd247bcc697908f2f25cbf17ff9b91a
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244130"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Irányítás az Azure DevTest Labs-infrastruktúra - erőforrások
Ez a cikk foglalkozik az igazítási és erőforrások kezelését a DevTest Labs a szervezeten belül. 

## <a name="align-within-an-azure-subscription"></a>Egy Azure-előfizetésen belül igazítása 

### <a name="question"></a>Kérdés
Hogyan igazítása a DevTest Labs-erőforrások egy Azure-előfizetésen belül?

### <a name="answer"></a>Válasz
Mielőtt egy szervezet elkezdi használni az Azure általános alkalmazásfejlesztés, IT-tervezők először tekintse át a szolgáltatások teljes portfólióját részeként funkciójaként hogyan. Területek, tekintse át az alábbi szempontok kell érintenie:

- Hogyan lehet mérni az alkalmazásfejlesztési életciklus teljes költsége?
- Milyen a szervezetben a vállalati biztonsági házirendet a javasolt szolgáltatásajánlatot igazítása? 
- Az egymástól a fejlesztési és éles környezetekben szükséges szegmentálást? 
- Milyen vezérlők jelennek meg, a stabilitás és növekedés a hosszú távú könnyű?

A **először ajánlott gyakorlat** , hogy tekintse át a szervezet Azure besorolás, ahol a részlegek között éles és a fejlesztői előfizetésekhez foglaltaknak. Az alábbi ábrán a javasolt besorolás lehetővé teszi, hogy egy logikai el a fejlesztési-tesztelési és éles környezetekben. Ezzel a módszerrel egy szervezet bevezethet számlázási kódok minden környezethez külön-külön társított költségek nyomon követéséhez. További információkért lásd: [előíró előfizetés-irányítás](/azure/architecture/cloud-adoption/appendix/azure-scaffold). Emellett használhatja [Azure címkék](../azure-resource-manager/resource-group-using-tags.md) nyomon követésre, és a számlázás szempontjából erőforrások rendezéséhez.

A **második ajánlott gyakorlat** engedélyezése a DevTest-előfizetés az Azure Enterprise Portalon. Lehetővé teszi a szervezetek, amelyek nem érhetők el általában az Azure enterprise előfizetéssel az ügyfél operációs rendszereket futtató. Ezután használja a nagyvállalati szoftverek, amelyen csak azért kell fizetnie a számítási és nem licenceléssel kapcsolatos ne aggódjon. Biztosítja, hogy a kijelölt szolgáltatások, beleértve a gyűjtemény lemezképei iaas, például a Microsoft SQL Server, a számlázás használatalapú csak alapul. Az Azure DevTest-előfizetéssel kapcsolatos információk találhatók [Itt](https://azure.microsoft.com/offers/ms-azr-0148p/) az ügyfelek a nagyvállalati szerződés (EA) és [Itt](https://azure.microsoft.com/offers/ms-azr-0023p/) Használatalapú ügyfelek számára.

![Előfizetések erőforrás igazítása](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Ez a modell rugalmasságának köszönhetően egy szervezet az üzembe helyezés az Azure DevTest Labs szolgáltatásban. Egy szervezet támogatható labs több száz különböző üzleti egységek a párhuzamosan futó 100 – 1000 virtuális gépek. Ez elősegíti a egy központosított vállalati labor megoldás megosztására használható kezelési és biztonsági vezérlők alapelveket fogalma.

Ez a modell emellett biztosítja, hogy a szervezet nem meríti azok erőforráskorlátok társított Azure-előfizetésében. Előfizetés- és Szolgáltatáskorlátok kapcsolatos részletekért lásd: [Azure-előfizetés és a szolgáltatások korlátozásai, kvótái és megkötései](../azure-subscription-service-limits.md). A DevTest Labs létesítésének folyamatát kell használnia az erőforráscsoportok sok használhatnak fel. Korlátok növelni egy támogatási kérést az Azure DevTest-előfizetés keretében is kérhető. Az éles előfizetésen belüli erőforrások használatát a fejlesztési előfizetés növekedésével nem érinti. DevTest Labs méretezéssel kapcsolatos további információkért lásd: [kvótái és korlátai méretezése a DevTest Labs](devtest-lab-scale-lab.md).

Egy közös előfizetési szintű vezérlőprogramjai számára szükséges határértéke a hálózati IP-Címek tartomány hozzárendelése kiosztás éles és a fejlesztői előfizetésekhez támogatásához. Ezeket a hozzárendeléseket növekedési tényezővel (feltéve, hogy a helyszíni kapcsolat vagy egy másik hálózati topológiát, a vállalat kezelése helyett az Azure végrehajtása alapértelmezés szerint a hálózati vermet igénylő) idővel kell figyelembe venni. Az ajánlott eljárás, amelyek egy nagy IP-címelőtag hozzárendelve, és számos nagy méretű alhálózattal osztani néhány virtuális hálózatokkal rendelkeznek helyett a kisméretű alhálózattal több virtuális hálózat. Például 10 előfizetések definiálhat 10 virtuális hálózatok (egy minden egyes előfizetés esetén). Elkülönítés nem igénylő összes labs oszthatnak meg ugyanabban az alhálózatban az előfizetéshez tartozó virtuális hálózaton.

## <a name="maintain-naming-conventions"></a>Elnevezési konvenciók karbantartása

### <a name="question"></a>Kérdés
Hogyan karbantartása egy elnevezési konvenciója a DevTest Labs-környezetben?

### <a name="answer"></a>Válasz
Előfordulhat, hogy szeretné kiterjeszteni a jelenlegi vállalati elnevezési konvenciók Azure műveletekhez, és azokat konzisztens a DevTest Labs-környezetben.

DevTest Labs, javasoljuk, hogy adott kezdési házirendek. Ezek a szabályzatok központi parancsfájlt és a JSON-sablonok, ha egységes telepít. Házirendek elnevezési implementálható az előfizetői szintre vonatkoznak az Azure szabályzatokkal. JSON-minták az Azure Policy, lásd: [Azure Policy minták](../azure-policy/json-samples.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Labor és a szervezet labs felhasználók száma

### <a name="question"></a>Kérdés 
Hogyan állapítható meg a felhasználók labor és a egy szervezeten belül szükséges labs teljes számának aránya?

### <a name="answer"></a>Válasz
Azt javasoljuk, hogy üzleti egységek és az azonos alkalmazásfejlesztési projekt társított a fejlesztői csoportok társítva a azonos labor. Lehetővé teszi a szabályzatok, a képek és a Leállítás házirendek azonos típusú csoportban is alkalmazható. 

Szükség lehet figyelembe venni a földrajzi határokon. Például a fejlesztők az észak-keleti Egyesült Államok (amerikai) felhasználhatja az üzembe helyezett 2. keleti régiója labor. És a fejlesztők számára, Dallas, Texas, és Denver, Colorado irányított használjon egy erőforrást, az USA déli középső régiója. Ha egy külső külső gyártótól származó egy együttműködésen alapuló tevékenységi, azok sikerült hozzárendelni a belső fejlesztők által nem használt laborokhoz. 

Az Azure DevOps projects belül egy adott projekt labor is használhatja. Ezután alkalmazza megadott Azure Active Directory csoporthoz, amely lehetővé teszi, hogy mindkét erőforráskészlettel való hozzáférés biztonságát. A virtuális hálózat a labor rendelt egyesíthetők a felhasználók egy másik határ lehet.

## <a name="deletion-of-resources"></a>Az erőforrások törlése

### <a name="question"></a>Kérdés
Hogyan tudjuk megakadályozni labor belüli erőforrások törlését?

### <a name="answer"></a>Válasz
Javasoljuk, hogy, megfelelő engedélyekkel a labor szintjén, hogy csak a jogosult felhasználók erőforrásokat törölheti vagy módosíthatja a laborszabályzatok. A fejlesztők belül kell elhelyezni a **DevTest Labs-felhasználó** csoport. A vezető fejlesztői vagy az infrastruktúra érdeklődő kell lennie a **DevTest Labs tulajdonosa**. Azt javasoljuk, hogy csak két labortulajdonosok. Ez a szabályzat kiterjesztése felé a kódtárat a sérülés elkerülése érdekében. Labor használ erőforrások használatára jogosult, de nem tudja frissíteni a laborszabályzatok. Tekintse meg a következő cikkben, amely felsorolja a szerepkörök és a jogosultságokat, amelyeket a labor belül minden beépített csoport rendelkezik: [Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs szolgáltatásban](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Labor áthelyezése másik erőforráscsoportba 

### <a name="question"></a>Kérdés
Támogatja az labor áthelyezése egy másik erőforrás-csoportba?

### <a name="answer"></a>Válasz
Igen. Az erőforráscsoport lapján lépjen a kezdőlapon a tesztkörnyezethez. Ezután válassza ki **áthelyezése** gombjára, és válassza ki a labor áthelyezi egy másik erőforráscsoportba. Ha a labor létrehozása, egy erőforráscsoport automatikusan létrejön az Ön számára. Azonban érdemes a labor áthelyezése egy másik erőforráscsoportban található, amely vállalati elnevezési szabályokat követi. 

## <a name="next-steps"></a>További lépések
Lásd: [kezelheti a költségeket és tulajdonosi viszonyainak](devtest-lab-guidance-governance-cost-ownership.md).
