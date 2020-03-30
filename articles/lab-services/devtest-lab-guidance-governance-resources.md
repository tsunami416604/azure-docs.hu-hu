---
title: Az Azure DevTest Labs infrastruktúrájának irányítása – Erőforrás
description: Ez a cikk az Azure DevTest Labs erőforrások igazításával és kezelésével foglalkozik a szervezeten belül.
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
ms.openlocfilehash: 8bb00c770c61a0a5462a01ae552bd7e40a7cdb36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470649"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Az Azure DevTest Labs infrastruktúrájának irányítása – Erőforrások
Ez a cikk a szervezeten belüli DevTest Labs erőforrások igazításával és kezelésével foglalkozik. 

## <a name="align-within-an-azure-subscription"></a>Igazítás egy Azure-előfizetésen belül 

### <a name="question"></a>Kérdés
Hogyan igazíthatom a DevTest Labs-erőforrásokat egy Azure-előfizetésen belül?

### <a name="answer"></a>Válasz
Mielőtt egy szervezet elkezdi használni az Azure-t az általános alkalmazásfejlesztéshez, az informatikai tervezőknek először át kell tekinteniük, hogyan vezethetik be a képességet a teljes szolgáltatási portfóliójuk részeként. A felülvizsgálati területeknek a következő aggályokkal kell foglalkozniuk:

- Hogyan mérhető az alkalmazásfejlesztési életciklushoz kapcsolódó költségek?
- Hogyan igazítja a szervezet a javasolt szolgáltatásajánlatot a vállalati biztonsági házirendhez? 
- Szükség van-e szegmentálásra a fejlesztési és termelési környezetek elkülönítéséhez? 
- Milyen ellenőrzéseket vezetnek be a hosszú távú könnyű kezelés, a stabilitás és a növekedés?

Az **első ajánlott gyakorlat** a szervezetek Azure-taxonómia áttekintése, ahol a termelési és fejlesztési előfizetések közötti felosztások körvonalazva vannak. A következő ábrán a javasolt rendszertani rendszer lehetővé teszi a fejlesztési/tesztelési és termelési környezetek logikus elkülönítését. Ezzel a megközelítéssel a szervezet számlázási kódokat vezethet be az egyes környezetekhez kapcsolódó költségek külön-külön nyomon követéséhez. További információ: [Előíró előfizetés-szabályozás.](/azure/architecture/cloud-adoption/appendix/azure-scaffold) Emellett [az Azure-címkék](../azure-resource-manager/management/tag-resources.md) segítségével rendszerezheti az erőforrásokat nyomon követési és számlázási célokra.

A **második ajánlott eljárás** a DevTest-előfizetés engedélyezése az Azure Enterprise portalon belül. Lehetővé teszi, hogy egy szervezet olyan ügyféloperációs rendszereket futtasson, amelyek általában nem érhetők el egy Azure-beli vállalati előfizetésben. Ezután használjon olyan vállalati szoftvert, amelyen csak a számításért kell fizetnie, és ne aggódjon a licencelés miatt. Biztosítja, hogy a kijelölt szolgáltatások számlázása, beleértve az IaaS-ben, például a Microsoft SQL Serverben található galériaképeket is, csak a felhasználáson alapul. Az Azure DevTest-előfizetés részletei [itt](https://azure.microsoft.com/offers/ms-azr-0148p/) találhatók a nagyvállalati szerződés (EA) ügyfeleinek és [itt](https://azure.microsoft.com/offers/ms-azr-0023p/) a Go-alapú fizetéssel kapcsolatos ügyfelekszámára.

![Erőforrás-összehangolás előfizetésekkel](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Ez a modell rugalmasságot biztosít egy szervezet számára az Azure DevTest Labs nagy méretekben történő üzembe helyezéséhez. Egy szervezet több száz labort támogathat különböző üzleti egységekhez, 100–1000 párhuzamosan futó virtuális géppel. Elősegíti a központosított vállalati labor megoldás fogalmát, amely ugyanazokat az alapelveket használhatja a konfigurációkezelés és a biztonsági vezérlők.

Ez a modell azt is biztosítja, hogy a szervezet nem meríti ki az Azure-előfizetéshez társított erőforrás-korlátokat. Az előfizetési és szolgáltatáskorlátokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)témakörben talál. A DevTest Labs létesítési folyamat nagy számú erőforráscsoportok felhasználására. Kérheti, hogy az Azure DevTest-előfizetés támogatási kérésével a korlátok at kell növelni. Az erőforrások at a termelési előfizetés nem érinti, mint a fejlesztési előfizetés használatban növekszik. A DevTest Labs méretezése című témakörben talál további információt [a Kvóták és korlátok méretezése a DevTest Labs ben című témakörben.](devtest-lab-scale-lab.md)

Egy közös előfizetési szint korlát, amelyet el kell számolni, hogy a hálózati IP-tartomány-hozzárendelések vannak lefoglalva, hogy mind az éles, mind a fejlesztési előfizetések támogatása. Ezek a hozzárendelések figyelembe kell venni az időbeli növekedést (feltételezve, hogy a helyszíni kapcsolat vagy más hálózati topológia, amely megköveteli a vállalatnak, hogy kezelje a hálózati verem, ahelyett, hogy az Azure-megvalósítás alapértelmezett). Az ajánlott gyakorlat az, hogy néhány virtuális hálózatok, amelyek egy nagy IP-cím előtag rendelt és osztva sok nagy alhálózatok helyett, hogy több virtuális hálózatok kis alhálózatokkal. 10 előfizetéssel például 10 virtuális hálózatot határozhat meg (előfizetésekenként egyet). Minden olyan laborok, amelyek nem igényelnek elkülönítést, megoszthatják ugyanazt az alhálózatot az előfizetés virtuális hálózatán.

## <a name="maintain-naming-conventions"></a>Elnevezési konvenciók karbantartása

### <a name="question"></a>Kérdés
Hogyan tarthatok fenn elnevezési konvenciót a DevTest Labs környezetben?

### <a name="answer"></a>Válasz
Érdemes lehet kiterjeszteni a jelenlegi vállalati elnevezési konvenciók az Azure-műveletek, és azok konzisztensek a DevTest Labs környezetben.

DevTest Labs telepítésekor azt javasoljuk, hogy speciális indítási szabályzatok. Ezeket a házirendeket egy központi parancsfájl és JSON-sablonok a konzisztencia kényszerítése érdekében. Az elnevezési szabályzatok az előfizetés szintjén alkalmazott Azure-szabályzatokon keresztül valósíthatók meg. JSON-minták az Azure-szabályzat, tekintse meg az [Azure-szabályzat mintákat.](../governance/policy/samples/index.md)

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Felhasználók száma laboronként és laboronként szervezetenként

### <a name="question"></a>Kérdés 
Hogyan állapíthatom meg a felhasználók laboronkénti arányát és a szervezeten belül szükséges laborok teljes számát?

### <a name="answer"></a>Válasz
Azt javasoljuk, hogy az azonos fejlesztési projekthez társított részlegek és fejlesztői csoportok ugyanahhoz a laborhoz vannak társítva. Lehetővé teszi, hogy azonos típusú házirendek, képek és leállítási házirendek mindkét csoportra alkalmazható legyen. 

Előfordulhat, hogy figyelembe kell vennie a földrajzi határokat is. Az Egyesült Államok (USA) északkeleti részén például az USA keleti részén (USA) kiépített labort is használhatnak. A coloradói Dallasban és Denverben lévő fejlesztők et pedig arra utasíthatják, hogy használjanak egy erőforrást az USA déli részén. Ha egy külső harmadik féllel együttműködve, akkor lehet hozzárendelni egy labor, amely nem használja a belső fejlesztők. 

Az Azure DevOps-projekteken belül egy adott projekthez is használhat labort. Ezután egy adott Azure Active Directory-csoporton keresztül alkalmazza a biztonságot, amely lehetővé teszi a hozzáférést mindkét erőforráskészlethez. A tesztkörnyezethez rendelt virtuális hálózat egy másik határ lehet a felhasználók konszolidálására.

## <a name="deletion-of-resources"></a>Az erőforrások törlése

### <a name="question"></a>Kérdés
Hogyan előzhetjük meg az erőforrások törlését egy laborban?

### <a name="answer"></a>Válasz
Azt javasoljuk, hogy állítsa be a megfelelő engedélyeket a labor szintjén, hogy csak a jogosult felhasználók törölhetik az erőforrásokat, vagy módosíthatja a labor szabályzatok. A fejlesztőket a **DevTest Labs-felhasználók** csoportba kell helyezni. A vezető fejlesztő nek vagy az infrastruktúra-vezetőnek a **DevTest Labs tulajdonosának**kell lennie. Azt javasoljuk, hogy csak két labor tulajdonos. Ez a házirend a kódtár felé terjed a korrupció elkerülése érdekében. A labor erőforrások használatára vonatkozó jogokkal rendelkezik, de nem frissítheti a tesztkörnyezet-házirendeket. Tekintse meg az alábbi cikket, amely felsorolja az egyes beépített csoportok szerepköröket és jogokat egy tesztkörnyezetben: [Tulajdonosok és felhasználók hozzáadása az Azure DevTest Labs-ben.](devtest-lab-add-devtest-user.md)

## <a name="move-lab-to-another-resource-group"></a>Labor áthelyezése másik erőforráscsoportba 

### <a name="question"></a>Kérdés
Támogatja egy tesztkörnyezet áthelyezése egy másik erőforráscsoportba?

### <a name="answer"></a>Válasz
Igen. Nyissa meg az Erőforráscsoport lapot a tesztkörnyezet kezdőlapjáról. Ezután válassza az **Áthelyezés** lehetőséget az eszköztáron, és jelölje ki azt a tesztkörnyezetet, amelyet egy másik erőforráscsoportba szeretne áthelyezni. Labor létrehozásakor automatikusan létrehoz egy erőforráscsoportot. Azonban érdemes lehet áthelyezni a labor egy másik erőforráscsoport, amely követi a vállalati elnevezési konvenciók. 

## <a name="next-steps"></a>További lépések
Lásd: [Költség és tulajdonjog kezelése](devtest-lab-guidance-governance-cost-ownership.md).
