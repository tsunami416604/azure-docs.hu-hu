---
title: A kereskedelmi Piactéri ajánlat közzétételi állapotának ellenőrzése
description: Ellenőrizze a Microsoft Partner Centeren kereskedelmi piactéren keresztüli ajánlat közzétételének szükséges az érvényesítést, minősítési és előzetes lépések állapotát.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 461c9f3f3725ba27410088ca19f1ec050375adf2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65806146"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>A kereskedelmi Piactéri ajánlat közzétételi állapotának ellenőrzése

Megtekintheti az aktuális **közzétételi állapota** a a **ajánlat áttekintése** lapján a [kereskedelmi piactér portálján](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) a Partner Center.

## <a name="automated-validation"></a>Az automatikus ellenőrzés

A közzétételi folyamat az első lépés egy olyan automatikus ellenőrzések. Minden egyes ellenőrzési lépés felel meg egyik funkciója, úgy döntött, hogy engedélyezze az előfizetési csomag létrehozása. A funkció nincs engedélyezve, ha az érvényesítés azokat, amelyek a következő közzétételi lépéssel kihagyja. A közzétételi állapot jóváhagyása előtt minden ellenőrzés kell végezni.

- **Vásárlási folyamat telepítő ajánlat (> 10 perc)**

Ebben a lépésben azt győződjön meg arról, hogy az ajánlat teljesíthetők, ha ügyfelek az Azure Portalon keresztül vásárolta meg. Ez a lépés csak akkor alkalmazható Microsoft értékesített ajánlatok.

- **Tesztelje a meghajtó adatellenőrzésen (~ 5 perc)**

Ebben a lépésben azt a test drive az ajánlat technikai konfigurációs szakaszban megadott adatok érvényesítése. Teszt meghajtó funkció tesztelt és jóváhagyott. A lépés csak akkor alkalmazható, amelyek próbálja ki az engedélyezve van a.

- **Tesztelje a meghajtó kiépítése (KB. 30 perc)**

Ebben a lépésben az adatok és az előző lépésben, a test drive működésének ellenőrzése után azt üzembe helyezése és replikálja a test drive példányát, hogy az ügyfél használatra készen állnak a.  A lépés csak akkor alkalmazható, amelyek próbálja ki az engedélyezve van a.

- **Felügyeleti érvényesítése és a regisztrációs vezethet (> 15 perc)**

Ebben a lépésben azt győződjön meg arról, hogy a vezető felügyeleti rendszer fogadhat ügyfélérdeklődések az ajánlat beállításában megadott adatok alapján. Ez a lépés csak akkor alkalmazható, amelyek Lead felügyelet engedélyezve van a.

## <a name="certification"></a>Tanúsítás

A közzététel előtt a kereskedelmi Partnerközpont-piactér elküldött ajánlatok minősítéssel kell rendelkezniük. Elküldött ajánlatok rendelkezéseinknek szigorú tesztelése, bizonyos automatizált és mások manuális, beleértve a-ellenőrzést futtat a [Azure Marketplace-en hitelesítési házirendek](https://docs.microsoft.com/legal/marketplace/general-policies). Ajánlat jelentkezés kell megjelölni jogosult hitelesítő előtt, lépjen tovább a következő lépés a közzétételi folyamat.

### <a name="types-of-validation-that-take-place-during-certification"></a>Minősítés során végrehajtott érvényesítési típusai

Nincsenek elküldött ajánlatok a minősítési folyamat szereplő érvényesítési három szintje.

- Közzétevő üzleti való jogosultság
- Tartalomérvényesítés
- Műszaki érvényesítést

#### <a name="publisher-business-eligibility"></a>Közzétevő üzleti való jogosultság

Minden ajánlat alapszintű megfelelőségi feltételeket, amelyek a közzétevő meg kell felelnie egy készletét ellenőrzi. Megfelelőségi feltételeket tartalmazhat, a közzétevő MPN állapota, kompetenciák tárolt, kompetencia szintek, stb.

#### <a name="content-validation"></a>Tartalomérvényesítés

Az adatokat, az ajánlat létrehozásakor megadott tartalom érvényesítése során minőség és a relevancia alapján végzett ellenőrzi. Ezeket az ellenőrzéseket fog tekintse át a bejegyzéseket a Marketplace-en listaelem részletei, díjszabás, elérhetőség, társított csomagokat, stb. Megfelel az Azure Marketplace-en és/vagy az appsource-ban ajánlati feltételek, a Microsoft ellenőrzi, hogy az ajánlat tartalmazza:

- az ajánlat; pontosabban leíró címet
- Adjon meg egy alapos áttekintése és értékajánlat; jól megírt tájékoztatókat
- minőségi képernyőképeket, valamint a hozzájuk kapcsolódó videók; és
- hogyan használja az ajánlat a Microsoft-platformokat, eszközöket részéből.

További a tartalomérvényesítés feltételek kapcsolatban olvassa el a [általános házirendek ajánlati](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies).

#### <a name="technical-validation"></a>Műszaki érvényesítést

Technikai érvényesítése során (csomag vagy bináris) az ajánlat az alábbi ellenőrzéseket megy keresztül.
- Kártevő szoftverek vizsgálata
- Figyelt hálózati hívások
- Elemzett csomag
- Az ajánlat tényleges funkció alapos vizsgálata

Az ajánlat érdekében, hogy robusztus különféle platformok és verziók között lett tesztelve.

Tekintse át az ajánlat a műszaki konfigurációs szakasz ebben a dokumentumban a szükséges konfigurációs adatokat.

### <a name="certification-failure-report"></a>Hitelesítési hiba jelentése

A felülvizsgálat befejezésekor az ajánlat már eltelt hitelesítő majd áthelyezi a közzétételi folyamat a következő lépéssel. Ha ajánlata nem sikerült minden listázása, a technikai vagy a házirend-ellenőrzést, vagy ha Ön nem jogosult egy ajánlatot az adott típusú elküldeni, a minősítési hiba jelentés jön létre, e-mailben.

Ez a jelentés a házirendekben, amelyek nem sikerült, valamint a felülvizsgálati feljegyzések leírását tartalmazza. Tekintse át ezt az e-mail jelentés, a problémák elhárítása, az ajánlat frissítéseket végez, ha szükséges, és küldje el újra, az ajánlat használatával a [kereskedelmi piactér portálján](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) a Partner Center. (Az ajánlatra, ahányszor szükséges, amíg megadásának hitelesítő van újraküldeni).

## <a name="preview-creation"></a>Előzetes létrehozása

Során a **előzetes verzió létrehozása** lépésben hozunk létre az Ön ajánlatát a verzió csak az ajánlat az előzetes verziókat tartalmazó szakaszában megadott körben.

## <a name="publisher-approval"></a>Közzétevő jóváhagyása

Ebben a lépésben lesz küldve az, hogy tekintse át és hagyja jóvá az ajánlat előzetes közzététel utolsó lépése előtt egy kéréssel.

Ha be van jelölve, hogy az ajánlat keretében a Microsoft, tudják, tesztelje a beszerzési és üzembe helyezés az Ön ajánlatát, annak érdekében, hogy ezen előzetes jóváhagyási szakasz során megfelel a követelményeknek. Az ajánlat még nem érhető el az összeköttetés Marketplace-en. Tesztelése, és ez az előzetes verzió jóváhagyása után kell kiválasztása **éles** a a [ **ajánlat áttekintése** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) irányítópultot.

Ha azt szeretné, módosíthatja az ebben a szakaszban előzetes ajánlat, szerkesztheti, és küldje el újra egy új előnézetet közzététele. Tekintse meg a cikket [meglévő piactéren frissítés](#update-existing-marketplace-offers) további módosításokat részleteiért.

Ha az ajánlat már élő és a Marketplace-en a nyilvánosan elérhető, végrehajtott kapcsolódhatunk élő kiválasztást követően **éles** a a [ **ajánlat áttekintése** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Az irányítópult.

### <a name="publish-offer-to-the-public"></a>A nyilvános ajánlat közzététele

Jelentkezzen be a Partner Center és az ajánlat eléréséhez. A rendszer átirányítja a **ajánlat áttekintése** lapot. Ez a lap tetején látni fogja a beállítás a **élesben**. Válassza ki **élesben,** , és miután meggyőződött arról, az ajánlat nyilvános első közzé. E-mail-értesítést fog kapni, amikor az ajánlat élő.

## <a name="publish"></a>Közzététel

Most, hogy a kiválasztott **élesben** az ajánlat keretében elérhetővé teszi a Marketplace-en vannak, győződjön meg arról, hogy az élő ajánlat hasonlóan az előzetes verzió van-e konfigurálva keresztül lépcsőzetes végső érvényesség-ellenőrzések sorozatának az ajánlat verziója.

- **Vásárlási folyamat telepítő ajánlat (> 10 perc)**

Ebben a lépésben azt győződjön meg arról, hogy az ajánlat teljesíthetők, ha ügyfelek az Azure Portalon keresztül vásárolta meg. Ez a lépés csak akkor alkalmazható Microsoft értékesített ajánlatok.

- **Tesztelje a meghajtó adatellenőrzésen (~ 5 perc)**

Ebben a lépésben azt a test drive az ajánlat technikai konfigurációs szakaszban megadott adatok érvényesítése. Teszt meghajtó funkció tesztelt és jóváhagyott. A lépés csak akkor alkalmazható, amelyek próbálja ki az engedélyezve van a.

- **Tesztelje a meghajtó kiépítése (KB. 30 perc)**

Ebben a lépésben azt üzembe helyezése és replikálja a test drive példányát, hogy az ügyfél használatra készen állnak a.  A lépés csak akkor alkalmazható, amelyek próbálja ki az engedélyezve van a.

- **Felügyeleti érvényesítése és a regisztrációs vezethet (> 15 perc)**

Ebben a lépésben azt győződjön meg arról, hogy a vezető felügyeleti rendszer fogadhat ügyfélérdeklődések az ajánlat beállításában megadott adatok alapján. Ez a lépés csak akkor alkalmazható, amelyek Lead felügyelet engedélyezve van a.

- **Utolsó közzététel (> 30 perc)**

Ebben a lépésben azt győződjön meg arról, hogy az ajánlatot a piactéren nyilvánosan elérhetővé válik.

## <a name="update-existing-marketplace-offers"></a>Marketplace-ajánlat frissítése

Ha azt szeretné, módosíthatja a korábban közzétett egy ajánlatot, először frissítse a meglévő ajánlat, és tegye közzé ismét szüksége.

## <a name="next-steps"></a>További lépések

- [A kereskedelmi piactéren egy létező ajánlat frissítése](./update-existing-offer.md)
