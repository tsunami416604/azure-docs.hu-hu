---
title: A kereskedelmi piactér-ajánlat közzétételi állapotának ellenőrzése
description: Ellenőrizze az ajánlat közzétételéhez szükséges ellenőrzési, minősítési és előzetes verziójú lépések állapotát a Microsoft Partner Center kereskedelmi piactéren keresztül.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275679"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>A kereskedelmi piactér-ajánlat közzétételi állapotának ellenőrzése

Az aktuális **közzétételi állapotot** a Partnerközpont [Kereskedelmi piactér](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) **portáljának Ajánlat áttekintése** lapján tekintheti meg.

Az egyes ajánlatainkban meg kell jelennie az alábbi állapotjelzők egyikének.

| **Állapot**    | **Leírás**  |
| :---------- | :-------------------|
| **Vázlat** | Az ajánlat létrejött, de nem lett közzétéve. |
| **Közzététel folyamatban** | Az Ajánlat/Terv a közzétételi folyamat lépésein halad keresztül. |
| **Figyelmet kell fordítani** | A Microsoft vagy a közzétételi lépések bármelyikének minősítése során egy kritikus problémát fedeztek fel. |
| **Előnézet** | Az ajánlatot a Microsoft hitelesítette, és most a kiadó végső ellenőrzésére vár. Válassza az élő bensőgombot, ha az ajánlatot élőben szeretné kaszkozni. |
| **Élő** | Az ajánlat él a piacon, és látható és megszerzett az ügyfelek. |
| **Függőben lévő stop eladás** | A Publisher az ajánlatvagy a csomag "stop sell" (stop sell) lehetőséget választotta, de a művelet még nem fejeződött be. |
| **Nem érhető el a piacon** | Egy korábban közzétett ajánlat/terv a piactéren el lett távolítva. |

## <a name="automated-validation"></a>Automatikus ellenőrzés

A közzétételi folyamat első lépése az automatikus ellenőrzés. Minden ellenőrzési lépés megfelel az ajánlat létrehozása során engedélyezni kívánt funkciónak. Ha ez a szolgáltatás nincs engedélyezve, az ellenőrzés a következő közzétételi lépésre ugrik át. Minden érvényesítési ellenőrzést el kell végezni a közzétételi állapot jóváhagyása előtt.

- **Ajánlat beszerzési folyamat ának beállítása (<10 min)**

Ebben a lépésben biztosítjuk, hogy az ajánlat teljesíthető legyen, ha az ügyfelek az Azure Portalon keresztül vásárolják meg. Ez a lépés csak a Microsofton keresztül értékesített ajánlatokra vonatkozik.

- **Teszthajtómű-adatérvényesítés (~5 min)**

Ebben a lépésben érvényesítjük az ajánlat tesztmeghajtó műszaki konfigurációs szakaszában megadott adatokat. A tesztvezetés funkcióit tesztelték és jóváhagyták. Ez a lépés csak olyan ajánlatokra vonatkozik, amelyeken engedélyezve van a tesztmeghajtó.

- **Tesztvezetés kiépítése (~30 min)**

Ebben a lépésben a tesztmeghajtó adatainak és funkcióinak az előző lépésben történő érvényesítése után üzembe helyezjük és replikáljuk a tesztmeghajtó példányait, hogy azok készen álljanak az ügyfelek használatára.  Ez a lépés csak olyan ajánlatokra vonatkozik, amelyeken engedélyezve van a tesztmeghajtó.

- **Érdeklődőkezelés érvényesítése és regisztrálása (<15 min)**

Ebben a lépésben megerősítjük, hogy az érdeklődőkezelő rendszer az Ajánlat beállításában megadott adatok alapján fogadhat ügyfélérdeklődőket. Ez a lépés csak olyan ajánlatokra vonatkozik, amelyeknél engedélyezve van az érdeklődőkezelés.

## <a name="certification"></a>Tanúsítvány

A közzététel előtt a Partnerközpont kereskedelmi piacterére beküldött ajánlatoknak hitelesnek kell lenniük. A beküldött ajánlatok szigorú tesztelésen mennek keresztül, néhány automatizált és mások kézikönyvben, beleértve az [Azure Marketplace minősítési szabályzatainak](https://docs.microsoft.com/legal/marketplace/general-policies)ellenőrzését is. Az ajánlatbeküldéseket hitelesítésre jogosultként kell megjelölni, mielőtt továbblépnének a közzétételi folyamat következő lépésére.

### <a name="types-of-validation-that-take-place-during-certification"></a>A tanúsítás során lezajlott érvényesítési típusok

A hitelesítési folyamat három ellenőrzési szintet tartalmaz minden egyes benyújtott ajánlathoz.

- Kiadói vállalkozásokra való jogosultság
- Tartalom érvényesítése
- Műszaki ellenőrzés

#### <a name="publisher-business-eligibility"></a>Kiadói vállalkozásokra való jogosultság

Minden ajánlattípus ellenőrzi az alapjogosultsági feltételeket, amelyeknek a közzétevőnek meg kell felelnie. A jogosultsági feltételek közé tartozhat a kiadó MPN-státusza, a birtokolt kompetenciák, a kompetenciaszintek stb.

#### <a name="content-validation"></a>Tartalom érvényesítése

A tartalom érvényesítése során az ajánlat létrehozásakor megadott adatokat ellenőrzi a rendszer a minőség és a relevancia szempontjából. Ezek az ellenőrzések áttekintik a bejegyzéseket a piactér tőzsdei adatait, árképzés, rendelkezésre állás, kapcsolódó tervek, stb. Az Azure Marketplace és/vagy az AppSource termékismerési feltételeinek való megfelelés érdekében ellenőrizni fogjuk, hogy az ajánlat tartalmazza-e:

- az ajánlatot pontosan leíró cím;
- jól megírt leírások, amelyek alapos áttekintést és értékajánlatot nyújtanak;
- minőségi képernyőképek és kísérő videók; És
- annak magyarázata, hogy az ajánlat hogyan használja a Microsoft platformjait és eszközeit.

A tartalomérvényesítési feltételekkel kapcsolatos további tudnivalókaz [általános tőzsdei irányelvek](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)elolvasásával.

#### <a name="technical-validation"></a>Műszaki ellenőrzés

A technikai ellenőrzés során az ajánlat (csomag vagy bináris) a következő ellenőrzéseken megy keresztül.
- Rosszindulatú programok keresése
- Figyelt hálózati hívások
- A csomag elemzése
- Az ajánlat tényleges működésének alapos vizsgálata

Az ajánlatot különböző platformokon és verziókon tesztelik annak érdekében, hogy robusztus legyen.

Tekintse át az ajánlathoz szükséges konfigurációs részleteket a jelen dokumentum Műszaki konfigurációs szakaszában.

### <a name="certification-failure-report"></a>Hitelesítési hiba jelentés

Az ellenőrzés befejezéseután, ha az ajánlat megfelelt a minősítést, akkor mozog végig a következő lépés a közzétételi folyamat. Ha az ajánlat a tőzsdei, technikai vagy szabályzat-ellenőrzések bármelyike meghiúsult, vagy ha nem jogosult ilyen típusú ajánlat benyújtására, a rendszer tanúsítványhiba-jelentést hoz létre és küld e-mailben.

Ez a jelentés a sikertelen házirendek leírását, valamint az ellenőrzési megjegyzéseket tartalmazza. Tekintse át ezt az e-mail jelentést, oldja meg a problémákat, szükség esetén frissítse az ajánlatát, és küldje el újra az ajánlatot a [Partnerközpont Kereskedelmi piactér portálján](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) keresztül. (Az ajánlatot annyiszor küldheti el újra, ahányszor csak szükséges, amíg át nem adja a tanúsítványt).

## <a name="preview-creation"></a>Előzetes verzió létrehozása

Az **előzetes verzió létrehozása** lépés során hozzuk létre az ajánlat egy verzióját, amely csak az ajánlat Előzetes verzió szakaszában megadott közönség számára érhető el.

>[!Note]
> Ne használja ezt a lépést, hogy a szervezeten kívüli személyek nek betekintést nyújtson egy ajánlatba. Használja inkább a **Privát ajánlat** lehetőséget. Ezen a ponton az ajánlat nem teljesen tesztelt és érvényesített, és nem áll készen a külső forgalmazás.

## <a name="publisher-approval"></a>Közzétevő jóváhagyása

Ebben a lépésben e-mailben kéri, hogy a végső közzétételi lépés előtt tekintse át és hagyja jóvá az ajánlat előnézetét.

Ha úgy döntött, hogy eladja az ajánlatot a Microsofton keresztül, tesztelheti az ajánlat beszerzését és telepítését, hogy megbizonyosodjon arról, hogy az megfelel az előzetes verzió jóváhagyási szakaszában. Az ajánlat még nem lesz elérhető a nyilvános piacon. Miután tesztelte és jóváhagyta ezt az előzetes verziót, ki kell választania a **Go-Live lehetőséget** az [**Ajánlat áttekintése**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) irányítópulton.

Ha ebben az előzetes verzióban módosítani szeretné az ajánlatot, szerkesztheti és újra elküldheti az új előzetes verzió közzétételét. Tekintse meg a [cikk meglévő piactér i ajánlatok frissítése](#update-existing-marketplace-offers) további módosításokat.

Ha az ajánlat már él és elérhető a nyilvánosság számára a piactéren, az Ön által eszközhöz kapcsolódó frissítések nem lépnek le, amíg ki nem választja az **Ugrás** lehetőséget az [**Ajánlat áttekintése**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) irányítópulton.

### <a name="publish-offer-to-the-public"></a>Ajánlat közzététele a nyilvánosság számára

Jelentkezzen be a Partnerközpontba, és férjen hozzá az ajánlathoz. A lesz irányítva az **Ajánlat áttekintő oldalára.** Az oldal tetején megjelenik a Go **Live**lehetőség. Válassza **az Élő adás lehetőséget,** és a megerősítés után az ajánlat megjelenik a nyilvánosság számára. E-mailben értesítést kap, ha az ajánlat élőben érkezik.

## <a name="publish"></a>Közzététel

Most, hogy kiválasztotta a **Go live** az ajánlatával, elérhetővé téve a piactéren, van egy sor végső érvényesítési ellenőrzések, amelyek átkerülnek annak érdekében, hogy az élő ajánlat van beállítva, mint az előzetes verzió az ajánlat.

- **Ajánlat beszerzési folyamat ának beállítása (>10 min)**

Ebben a lépésben biztosítjuk, hogy az ajánlat teljesíthető legyen, ha az ügyfelek az Azure Portalon keresztül vásárolják meg. Ez a lépés csak a Microsofton keresztül értékesített ajánlatokra vonatkozik.

- **Teszthajtómű-adatérvényesítés (~5 min)**

Ebben a lépésben érvényesítjük az ajánlat tesztmeghajtó műszaki konfigurációs szakaszában megadott adatokat. A tesztvezetés funkcióit tesztelték és jóváhagyták. Ez a lépés csak olyan ajánlatokra vonatkozik, amelyeken engedélyezve van a tesztmeghajtó.

- **Tesztvezetés kiépítése (~30 min)**

Ebben a lépésben üzembe helyezjük és replikáljuk a tesztmeghajtó példányait, hogy azok készen álljanak az ügyfelek használatára.  Ez a lépés csak olyan ajánlatokra vonatkozik, amelyeken engedélyezve van a tesztmeghajtó.

- **Érdeklődőkezelés érvényesítése és regisztrálása (>15 min)**

Ebben a lépésben megerősítjük, hogy az érdeklődőkezelő rendszer az Ajánlat beállításában megadott adatok alapján fogadhat ügyfélérdeklődőket. Ez a lépés csak olyan ajánlatokra vonatkozik, amelyeknél engedélyezve van az érdeklődőkezelés.

- **Végleges közzététel (>30 perc)**

Ebben a lépésben biztosítjuk, hogy az ajánlat nyilvánosan elérhetővé válik a piacon.

## <a name="update-existing-marketplace-offers"></a>Meglévő piactéri ajánlatok frissítése

Ha módosítani szeretné a már közzétett ajánlatot, először frissítenie kell a meglévő ajánlatot, majd újra közzé kell tennie.

## <a name="next-steps"></a>További lépések

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](./update-existing-offer.md)
