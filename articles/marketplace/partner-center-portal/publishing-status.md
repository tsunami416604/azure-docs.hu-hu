---
title: A kereskedelmi piactér ajánlat közzétételi állapotának megtekintése
description: Tekintse át az ajánlat közzétételéhez szükséges ellenőrzési, minősítési és előzetes lépések állapotát a Microsoft partner Center kereskedelmi Piactéren keresztül.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: c547ad256e6ba44da4ef81aa2a3aa2c77ba24815
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982732"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>A kereskedelmi piactér ajánlat közzétételi állapotának megtekintése

Az aktuális **közzétételi állapotát** a partner Center [kereskedelmi piactér portáljának](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) **ajánlat áttekintése** lapján tekintheti meg.

Az egyes ajánlatokhoz a következő állapotmutatók egyikét kell megjeleníteni.

| **Állapot**    | **Leírás**  |
| :---------- | :-------------------|
| **Tervezet** | Az ajánlat létre lett hozva, de nincs közzétéve. |
| **Közzététel folyamatban** | Az ajánlat/csomag a közzétételi folyamat lépésein dolgozik. |
| **Figyelem szükséges** | A Microsoft vagy a közzétételi lépések bármelyike miatt kritikus problémát észlelt a rendszer. |
| **Előzetes verzió** | Az ajánlatot a Microsoft tanúsította, és most a közzétevő végső ellenőrzésre vár. Válassza az élő indítás lehetőséget az ajánlat élő megjelenítéséhez. |
| **Live** | Az ajánlat a piactéren érhető el, és az ügyfelek megtekinthetik és megszerezhetik azokat. |
| **Leállításra váró eladás** | A kiadó az ajánlat vagy a csomag "értékesítés leállítása" elemét választotta, de a művelet még nem fejeződött be. |
| **Nem érhető el a piactéren** | A piactéren korábban közzétett ajánlat/csomag el lett távolítva. |

## <a name="automated-validation"></a>Automatikus ellenőrzés

A közzétételi folyamat első lépése az automatikus érvényesítések halmaza. Minden ellenőrzési lépés megfelel az ajánlat létrehozásában az engedélyezéshez kiválasztott szolgáltatásnak. Ha ez a szolgáltatás nem volt engedélyezve, az érvényesítés a következő közzétételi lépésre ugrik. A közzétételi állapot jóváhagyása előtt minden ellenőrzési ellenőrzést végre kell hajtani.

- **Ajánlat-megvásárlási folyamat beállítása (< 10 perc)**

Ebben a lépésben biztosítjuk, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.

- **Tesztvezetés adatellenőrzése (~ 5 perc)**

Ebben a lépésben érvényesítjük az ajánlat Test Drive technikai konfiguráció szakaszában megadott adatmennyiséget. A test Drive funkció tesztelése és jóváhagyása megtörténik. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Tesztelési meghajtó üzembe helyezése (~ 30 perc)**

Ebben a lépésben az előző lépésben a tesztelési meghajtó adatai és funkcióinak ellenőrzése után a rendszer üzembe helyezi és replikálja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára.  Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Vezető felügyelet ellenőrzése és regisztráció (< 15 perc)**

Ebben a lépésben megerősítjük, hogy a vezető felügyeleti rendszer az ajánlat beállítása során megadott adatok alapján fogadja az ügyfeleket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

## <a name="certification"></a>Tanúsítvány

Közzététel előtt a partner Centerben a kereskedelmi Piactérnek küldött ajánlatokat hitelesíteni kell. Az elküldött ajánlatok szigorú tesztelésen mennek keresztül, néhány automatizált és egyéb kézikönyvtel, beleértve az [Azure Marketplace minősítési szabályzatok](https://docs.microsoft.com/legal/marketplace/general-policies)ellenőrzését is. A közzétételi folyamat következő lépésének megkezdése előtt meg kell jelölni az ajánlati beadványok minősítését.

### <a name="types-of-validation-that-take-place-during-certification"></a>A minősítés során megkerülő érvényesítési típusok

Az egyes beküldött ajánlatok esetében három ellenőrzési szint szerepel a minősítési folyamatban.

- Kiadói üzleti jogosultság
- Tartalom érvényesítése
- Technikai ellenőrzés

#### <a name="publisher-business-eligibility"></a>Kiadói üzleti jogosultság

Minden ajánlat típusa ellenőrzi, hogy a közzétevőnek milyen alapszintű jogosultsági feltételeket kell teljesítenie. A jogosultsági feltételek magukban foglalhatják a közzétevő MPN-állapotát, a kompetenciákat, a kompetenciás szinteket stb.

#### <a name="content-validation"></a>Tartalom érvényesítése

A tartalom ellenőrzése során a rendszer az ajánlat létrehozásakor megadott adatokat ellenőrzi a minőség és a relevancia szempontjából. Ezek az ellenőrzések áttekintik a piactér-lista részleteit, a díjszabást, a rendelkezésre állást, a társított terveket stb. Az Azure Marketplace és/vagy a AppSource listázási feltételeinek teljesítése érdekében a következőket érvényesítjük:

- az ajánlatot pontosan leíró cím;
- jól megírt leírások, amelyek átfogó áttekintést és értéket biztosítanak.
- minőségi képernyőképek és kapcsolódó videók; és
- annak magyarázata, hogy az ajánlat hogyan használja a Microsoft platformokat és eszközöket.

A tartalom-ellenőrzési feltételekkel kapcsolatos további információkért olvassa el az [általános listázási szabályzatot](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Technikai ellenőrzés

A technikai ellenőrzés során az ajánlat (csomag vagy bináris) a következő ellenőrzéseken megy keresztül.
- Kártevő szoftverek vizsgálata
- Figyelt hálózati hívások
- Elemzett csomag
- Az ajánlat tényleges funkcióinak alapos vizsgálata

Az ajánlat a különböző platformokon és verziókon tesztelve biztosítható a robusztus működés biztosítása érdekében.

Tekintse át az ajánlathoz szükséges konfigurációs adatokat a jelen dokumentum technikai konfiguráció szakaszában.

### <a name="certification-failure-report"></a>Sikertelen minősítési jelentés

A felülvizsgálat befejezésekor, ha az ajánlata megfelelt a minősítésnek, akkor a közzétételi folyamat következő lépéseként halad át. Ha az ajánlata nem felelt meg a tőzsdei, műszaki vagy házirend-ellenőrzéseknek, vagy ha nem jogosult ilyen típusú ajánlat beküldésére, akkor a rendszer egy tanúsítvány-hibajelentést generál és e-mailben elküldi Önnek.

Ez a jelentés a sikertelen házirendek leírásait tartalmazza, valamint a felülvizsgálati megjegyzéseket. Tekintse át az e-mail-jelentést, a problémák megoldását, az ajánlat frissítését, ahol szükséges, majd küldje el újra az ajánlatot a partner Center [kereskedelmi piactér portáljának](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) használatával. (A minősítés átadása előtt többször is elküldheti az ajánlatot.

## <a name="preview-creation"></a>Előnézet létrehozása

Az **előzetes** verziójú létrehozási lépés során az ajánlat egy verzióját csak az ajánlat előzetes verzió szakaszában megadott célközönség számára elérhetővé tesszük.

## <a name="publisher-approval"></a>Közzétevő jóváhagyása

Ebben a lépésben e-mailben küldünk egy kérést, amellyel áttekintheti és jóváhagyhatja az ajánlat előzetes verzióját a végső közzétételi lépés előtt.

Ha úgy döntött, hogy az ajánlatot a Microsofton keresztül értékesíti, tesztelheti az ajánlat beszerzését és üzembe helyezését annak érdekében, hogy megfeleljen az előfizetésre vonatkozó előírásoknak. Ajánlata még nem lesz elérhető a ágyéki piactéren. Az előzetes verzió tesztelése és jóváhagyása után válassza a **Go-Live** lehetőséget az [**ajánlat áttekintése**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) irányítópulton.

Ha az előnézeti fázisban módosítani kívánja az ajánlatot, szerkesztheti és újból elküldheti az új előzetes verzió közzétételét. További részletekért tekintse meg a [meglévő Piactéri ajánlatok frissítése](#update-existing-marketplace-offers) című cikket.

Ha az ajánlata már él és elérhető a piactéren, akkor az Ön által végzett frissítések csak akkor lépnek életbe, ha a **Go-Live** lehetőséget választja az [**ajánlat áttekintése**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) irányítópulton.

### <a name="publish-offer-to-the-public"></a>Ajánlat közzététele nyilvánosként

Jelentkezzen be a partner Centerbe, és nyissa meg az ajánlatot. A rendszer átirányítja az **ajánlat áttekintése** oldalra. A lap tetején megjelenik a **Go Live**lehetőség. Válassza az **élő indítás** lehetőséget, és miután megerősítette, az ajánlat megkezdi a közzétételt a nyilvánosság számára. E-mailben értesítést fog kapni, ha az ajánlat élőben működik.

## <a name="publish"></a>Közzététel

Most, hogy úgy döntött, hogy az ajánlatával **él** , így elérhetővé válik a piactéren, a végső ellenőrzési ellenőrzések számos lépéssel megtalálhatók, amelyekkel biztosítható, hogy az élő ajánlat az ajánlat előzetes verziójához hasonlóan legyen konfigurálva.

- **Ajánlat-megvásárlási folyamat beállítása (> 10 perc)**

Ebben a lépésben biztosítjuk, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.

- **Tesztvezetés adatellenőrzése (~ 5 perc)**

Ebben a lépésben érvényesítjük az ajánlat Test Drive technikai konfiguráció szakaszában megadott adatmennyiséget. A test Drive funkció tesztelése és jóváhagyása megtörténik. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Tesztelési meghajtó üzembe helyezése (~ 30 perc)**

Ebben a lépésben üzembe helyezi és replikálja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára.  Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Vezető felügyelet ellenőrzése és regisztráció (> 15 perc)**

Ebben a lépésben megerősítjük, hogy a vezető felügyeleti rendszer az ajánlat beállítása során megadott adatok alapján fogadja az ügyfeleket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

- **Végső közzététel (> 30 perc)**

Ebben a lépésben biztosítjuk, hogy az ajánlat nyilvánosan elérhetővé válik a piactéren.

## <a name="update-existing-marketplace-offers"></a>Meglévő Piactéri ajánlatok frissítése

Ha módosítani szeretné egy már közzétett ajánlatot, először frissítenie kell a meglévő ajánlatot, majd újra közzé kell tennie.

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
