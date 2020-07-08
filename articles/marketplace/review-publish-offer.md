---
title: Ajánlat áttekintése és közzététele a Microsoft kereskedelmi piactéren
description: A partner Center használatával küldje el ajánlatát az előzetes verzióra, tekintse meg az ajánlatát, majd tegye közzé a Microsoft kereskedelmi piactéren.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/05/2020
ms.openlocfilehash: 910a886f02b386d7242bdd5e85f0710baa25408e
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86090974"
---
# <a name="review-and-publish-an-offer-to-commercial-marketplace"></a>Ajánlat áttekintése és közzététele kereskedelmi piactéren

Ebből a cikkből megtudhatja, hogyan használhatja a partner centert az ajánlat előzetes verzióra való beküldésére, az ajánlat előzetes megtekintésére, majd közzétételére a Microsoft kereskedelmi piactéren. Azt is bemutatjuk, hogyan ellenőrizheti a közzétételi állapotot a közzétételi lépések végrehajtásával. Már létre kell hoznia egy ajánlatot, amelyet közzé szeretne tenni.

## <a name="go-to-your-offer-in-commercial-marketplace"></a>A kereskedelmi piactéren elérhető ajánlat

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
1. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
1. Az **Áttekintés** lap **ajánlatok**területén az alábbi állapotjelzők egyike jelenik meg az egyes ajánlatok **állapot** oszlopában.
 
    | Állapot | Leírás |
    | ------------ | ------------- |
    | Vázlat | Az ajánlat létre lett hozva, de nincs közzétéve. |
    | Közzététel folyamatban | Az ajánlat a közzétételi folyamaton keresztül működik. |
    | Figyelem szükséges | Egy kritikus problémát észlelt a minősítés vagy egy másik közzétételi fázis során. |
    | Előnézet | Igazoljuk az ajánlatot, amely most már a közzétevő általi végleges ellenőrzést várja. Válassza az **élő** indítás lehetőséget az ajánlat élő közzétételéhez. |
    | Élő | Az ajánlat a piactéren érhető el, és az ügyfelek megtekinthetik és megszerezhetik azokat. |
    | Leállításra váró eladás | A kiadó "értékesítés leállítása" lehetőséget adott egy ajánlaton vagy terven, de a művelet még nem fejeződött be. |
    | Nem érhető el a piactéren | A piactéren korábban közzétett ajánlat el lett távolítva. |
    |||

4. Az **ajánlat-alias** oszlopban válassza ki az előnézetet és a közzétenni kívánt ajánlatot.

## <a name="submit-your-offer-to-preview"></a>Ajánlat beküldése az előzetes verzióra

1. Ha szeretné elküldeni az ajánlatát az előzetes verzióra, válassza a **felülvizsgálat és közzététel** lehetőséget a portál jobb felső sarkában. Megjelenik a **felülvizsgálat és közzététel** oldal.
1. Győződjön meg arról, hogy az egyes lapok **állapot** oszlopa **befejeződött**. A három lehetséges állapot a következő:
   - **Nincs elindítva** – a lap nem lett megérintve, és el kell végezni.
   - **Hiányos** – a lap nem tartalmaz szükséges információkat, vagy hibákat kell rögzítenie. Vissza kell lépnie az oldalra, és frissítenie kell.
   - **Befejezés** – a lap elkészült. Minden szükséges információ meg lett adta, és nincsenek hibák.
1. Ha bármelyik oldal állapota nem **fejeződött**be, az **oldal** oszlopban válassza ki az oldal nevét, javítsa ki a problémát, mentse a lapot, majd válassza a **felülvizsgálat és közzététel** újra lehetőséget, hogy visszatérjen ehhez a laphoz.
1. Az összes oldal befejezése után a **tanúsítványok megjegyzései** mezőben adja meg a minősítési csapat tesztelési utasításait, hogy az alkalmazás megfelelően legyen tesztelve. Az alkalmazás megértéséhez hasznos kiegészítő megjegyzéseket is megadhat.
1. Az ajánlat közzétételre való elküldéséhez válassza a **Közzététel**lehetőséget. Megjelenik az **ajánlat áttekintése** lap, és megjeleníti a közzétételi állapotot.

## <a name="validation-and-publishing-steps"></a>Érvényesítési és közzétételi lépések

Miután kiválasztotta a **Közzététel**lehetőséget, az érvényesítési és a közzétételi folyamatok sorrendben jelennek meg. A leggyakoribb közzétételi folyamat a következő táblázatban látható:

| Fázis | Mi történik | 
| ------------ | ------------- | ------------- |
| Automatikus ellenőrzés | Az automatizált érvényesítések egy készletét dolgozzák fel. | 
| Tanúsítvány | Manuális érvényesítést végezünk. | 
| Előnézet létrehozása | Az ajánlat előzetes verziójának listázási lapja mindenki számára elérhető, aki rendelkezik az előzetes verziójú hivatkozással. Ha az ajánlatát a Microsoft (transactd) alapján értékesíti, akkor csak az ajánlat **előnézeti célközönsége** oldalon megadott célközönség vásárolhat és érheti el az ajánlatot tesztelésre. | 
| Közzétevő PM | Egy e-mailt küldünk Önnek, amely az ajánlat előzetes megtekintését és jóváhagyását kéri. | 
| Közzététel | Egy sor lépést futtatunk annak ellenőrzéséhez, hogy az előzetes ajánlat közzé van-e téve élőben a kereskedelmi piactéren. | 
|||

## <a name="automated-validation-phase"></a>Automatizált ellenőrzési fázis

A közzétételi folyamat első lépése az automatikus érvényesítések halmaza. Az egyes ellenőrzési lépések az ajánlat létrehozásakor kiválasztott szolgáltatásnak felelnek meg. Minden érvényesítési ellenőrzést végre kell hajtani, mielőtt az ajánlat továbblép a közzétételi folyamat következő lépésére.

- **Ajánlat-megvásárlási folyamat beállítása (<10 perc)**

   Ebben a lépésben biztosítjuk, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.
- **Tesztvezetés adatellenőrzése (~ 5 perc)**

   Ebben a lépésben érvényesítjük az ajánlat technikai konfigurációs oldalán megadott adatmennyiséget. A test Drive funkció tesztelése és jóváhagyása megtörténik. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

-   **Tesztelési meghajtó üzembe helyezése (~ 30 perc)**

    Ebben a lépésben az előző lépésben a tesztelési meghajtó adatai és funkcióinak ellenőrzése után a rendszer üzembe helyezi és replikálja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

-   **Vezető felügyelet ellenőrzése és regisztráció (<15 perc)**

    Ebben a lépésben megerősítjük, hogy az érdeklődői felügyeleti rendszer az **ajánlat beállítása** lapon megadott részletek alapján fogadja az ügyfelektől érkező érdeklődőket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

## <a name="certification-phase"></a>Minősítési fázis

Közzététel előtt a kereskedelmi piactérnek küldött ajánlatokat hitelesíteni kell. Az elküldött ajánlatok szigorú tesztelésen mennek keresztül, és néhány automatizált és egyéb manuális. További információért tekintse át a [kereskedelmi piactér minősítési házirendjeit](https://aka.ms/commercial-marketplace-certification-policies) .

### <a name="types-of-validation-that-take-place-during-certification"></a>A minősítés során megkerülő érvényesítési típusok
Az egyes elküldött ajánlatok esetében három ellenőrzési szint szerepel a minősítési folyamatban.
-   Kiadói üzleti jogosultság
-   Tartalom érvényesítése
-   Technikai ellenőrzés

#### <a name="publisher-business-eligibility"></a>Kiadói üzleti jogosultság
Minden ajánlat típusa ellenőrzi, hogy a közzétevőnek milyen alapszintű jogosultsági feltételeket kell teljesítenie. A jogosultsági feltételek magukban foglalhatják a közzétevő MPN-állapotát, a kompetenciákat, a kompetenciás szinteket stb.

#### <a name="content-validation"></a>Tartalom érvényesítése

Az ajánlat létrehozásakor megadott adatokat a minőség és a relevancia alapján ellenőrzi a rendszer. Ezek az ellenőrzések áttekintik a piactér-lista részleteit, a díjszabást, a rendelkezésre állást, a társított csomagokat és így tovább. Az Azure Marketplace és a Microsoft AppSource listázási feltételeinek teljesítése érdekében a következőket érvényesítjük:
-   Az ajánlatot pontosan leíró cím
-   Jól megírt leírások, amelyek átfogó áttekintést és értéket biztosítanak
-   Minőségi képernyőképek és videók
-   Annak magyarázata, hogy az ajánlat hogyan használja a Microsoft platformokat és eszközöket.

A tartalom-ellenőrzési feltételekkel kapcsolatos további információkért olvassa el az [általános listázási szabályzatot](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Technikai ellenőrzés
A technikai ellenőrzés során az ajánlat (csomag vagy bináris) a következő ellenőrzéseken megy keresztül.
-   Kártevő szoftverek vizsgálata
-   Figyelt hálózati hívások
-   Elemzett csomag
-   Az ajánlat funkcióinak alapos vizsgálata

Az ajánlat a különböző platformokon és verziókon tesztelve biztosítja a robusztus működést.

### <a name="certification-failure-report"></a>Sikertelen minősítési jelentés

Ha az ajánlata nem felel meg a tőzsdei, műszaki vagy házirend-ellenőrzéseknek, vagy ha nem jogosult ilyen típusú ajánlat elküldésére, e-mailt küldünk Önnek a minősítési hibajelentésről.

Ez a jelentés a sikertelen házirendek leírásait tartalmazza, valamint a felülvizsgálati megjegyzéseket. Tekintse át az e-mail-jelentést, a problémák megoldását, az ajánlat frissítését, ahol szükséges, majd küldje el újra az ajánlatot a partner Center [kereskedelmi piactér portálján](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) . A minősítés átadása előtt többször is elküldheti az ajánlatot.

## <a name="preview-creation-phase"></a>Előnézet létrehozási fázisa

Az előzetes verziójú létrehozási fázisban az ajánlat egy olyan verzióját hozunk létre, amely csak az ajánlat **előnézeti közönség** lapján megadott célközönség számára érhető el, ha van ilyen. Az ajánlat előzetes verziója nem érhető el mindenki számára az előnézeti közönségen kívül, amíg nem teszi közzé az ajánlatot élőben.

> [!NOTE]
> Ne használja az előzetes verzió célközönségét, hogy a szervezeten kívüli személyek is láthassanak egy ajánlatot. Használja helyette a privát ajánlat lehetőséget. Ezen a ponton az ajánlat nem lett teljes körűen tesztelve és érvényesítve, és nem áll készen a külső terjesztésre. 

## <a name="publisher-signoff-phase"></a>Közzétevő PM fázisa

Ha az ajánlat készen áll arra, hogy áttekintse és PM, küldjön Önnek egy e-mailt, amely kéri, hogy tekintse át és hagyja jóvá az ajánlat előzetes verzióját. Az **ajánlat áttekintés** lapját is frissítheti a böngészőben, és megtekintheti, hogy az ajánlat elérte-e a kiadói PM fázist. Ha igen, a **Go Live** gomb és az előzetes verziójú hivatkozások is elérhetők lesznek.

Az alábbi képernyőképen egy SaaS-ajánlat **ajánlat áttekintése** lapja látható. Az ezen az oldalon látható ellenőrzési lépések az ajánlat típusától és az ajánlat létrehozásakor létrehozott beállításoktól függően változnak.

![A partneri központban elérhető ajánlat áttekintés lapját mutatja be. Megjelenik a Go Live gomb és az előzetes verzió hivatkozásai.](./partner-center-portal/media/publish-status-publisher-signoff.png)

**Az ajánlat és a PM előzetes verziójának megtekintése**
1. Az ajánlat **áttekintése** lapon az ajánlat előnézetének megtekintéséhez válassza a **Go Live (ugrás** ) gomb alatt található hivatkozást.
   > [!NOTE]
   > A AppSource előzetes verziójához, az Azure Marketplace előzetes verziójához vagy mindkettőhöz az ajánlat létrehozásakor választott beállításoktól függően lesz hivatkozás. Ha úgy dönt, hogy az ajánlatot a Microsofton keresztül értékesíti, akkor bárki, aki hozzá lett adva az előzetes verzió célközönségéhez, tesztelheti az ajánlat beszerzését és üzembe helyezését, hogy az megfeleljen a jelen szakaszban foglalt követelményeknek.

1. Ha módosítani kívánja az ajánlat előnézetét, szerkesztheti és újraküldheti az új előzetes verzió közzétételét. További információ: [meglévő ajánlat frissítése a kereskedelmi piactéren](./partner-center-portal/update-existing-offer.md).

1. Miután jóváhagyta az előnézetét, az ajánlat élőben is elérhetővé tehető a kereskedelmi piactéren. Válassza az **élő indítás**lehetőséget.
   > [!TIP]
   > Ha az ajánlata már él és elérhető a piactéren, az Ön által végzett frissítések csak akkor lépnek életbe, ha a **Go Live**lehetőséget választja.

## <a name="publish-phase"></a>Közzétételi fázis

Most, hogy úgy döntött, hogy az ajánlatával él, így elérhetővé válik a kereskedelmi piactéren, számos végső ellenőrzési ellenőrzéssel gondoskodunk arról, hogy az élő ajánlat ugyanúgy legyen konfigurálva, mint az ajánlat előzetes verziója.

-   **Ajánlat-megvásárlási folyamat beállítása (>10 perc)**

    Ebben a lépésben biztosítjuk, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.
-   **Tesztvezetés adatellenőrzése (~ 5 perc)**

    Ebben a lépésben érvényesítjük az ajánlat technikai konfigurációs oldalán megadott adatmennyiséget. A test Drive funkció tesztelése és jóváhagyása megtörténik. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

-   **Tesztelési meghajtó üzembe helyezése (~ 30 perc)**

      Ebben a lépésben üzembe helyezi és replikálja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.
-   **Vezető felügyelet ellenőrzése és regisztráció (>15 perc)**

    Ebben a lépésben megerősítjük, hogy a vezető felügyeleti rendszer az ajánlat **telepítési** lapján megadott részletek alapján fogadja az ügyfeleket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

-   **Végső közzététel (>30 perc)**

    Ebben a lépésben biztosítjuk, hogy az ajánlat nyilvánosan elérhetővé válik a piactéren.

Az ellenőrzés befejezése után az ajánlat a piactéren lesz elérhető.

## <a name="next-step"></a>Következő lépés

[A partner Center kereskedelmi piactérről származó analitikai jelentések elérése](./partner-center-portal/analytics.md)