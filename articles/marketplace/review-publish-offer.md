---
title: Ajánlat áttekintése és közzététele a Microsoft kereskedelmi piactéren
description: A partner Center használatával küldje el ajánlatát az előzetes verzióra, tekintse meg az ajánlatát, majd tegye közzé a Microsoft kereskedelmi piactéren.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 08/12/2020
ms.openlocfilehash: eff1db3aca2cab77d18698634b84e8e1b1c99f7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375880"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Ajánlat áttekintése és közzététele a kereskedelmi piactéren

Ebből a cikkből megtudhatja, hogyan használhatja a partner centert az ajánlat közzétételre való benyújtására, az ajánlat előzetes megtekintésére, majd a kereskedelmi piactéren való közzétételre. Azt is bemutatjuk, hogyan ellenőrizheti a közzétételi állapotot a közzétételi lépések végrehajtásával. Már létre kell hoznia egy ajánlatot, amelyet közzé szeretne tenni.

## <a name="offer-status"></a>Ajánlat állapota

Az ajánlat állapotát a [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)kereskedelmi piactér irányítópultjának **Áttekintés** lapján tekintheti át. Az egyes ajánlatok **állapot** oszlopában az alábbi állapotjelzők egyike jelenik meg.

| Állapot | Leírás |
| ------------ | ------------- |
| Draft | Az ajánlat létre lett hozva, de nincs közzétéve. |
| Közzététel folyamatban | Az ajánlat a közzétételi folyamaton keresztül működik. |
| Figyelem szükséges | Egy kritikus problémát észlelt a minősítés vagy egy másik közzétételi fázis során. |
| Előnézet | Igazoljuk az ajánlatot, amely most már a közzétevő általi végleges ellenőrzést várja. Válassza az **élő** indítás lehetőséget az ajánlat élő közzétételéhez. |
| Élő | Az ajánlat a piactéren érhető el, és az ügyfelek megtekinthetik és megszerezhetik azokat. |
| Leállításra váró eladás | A kiadó "értékesítés leállítása" lehetőséget adott egy ajánlaton vagy terven, de a művelet még nem fejeződött be. |
| Nem érhető el a piactéren | A piactéren korábban közzétett ajánlat el lett távolítva. |
|||

## <a name="validation-and-publishing-steps"></a>Érvényesítési és közzétételi lépések

Ha készen áll egy ajánlat közzétételre való elküldésére, válassza a **felülvizsgálat és közzététel** lehetőséget a portál jobb felső sarkában. A **felülvizsgálat és közzététel** oldalon az ajánlat egyes lapjainak állapota látható, amely a következők egyike lehet:

   - **Nincs elindítva** – a lap hiányos.
   - **Hiányos** – a lap nem tartalmaz szükséges információkat, vagy hibákat kell rögzítenie. Vissza kell lépnie az oldalra, és frissítenie kell.
   - **Befejezés** – a lap elkészült. Minden szükséges információ meg lett adta, és nincsenek hibák.

Ha bármelyik oldalon a **befejezéstől**eltérő állapot van, javítsa ki a problémát az oldalon, majd térjen vissza a **felülvizsgálat és közzététel** lapra, és ellenőrizze, hogy az állapot most **készként**jelenik-e meg. Egyes ajánlati típusok tesztelést igényelnek. Ha igen, megjelenik a **minősítési mezőhöz tartozó megjegyzések** , ahol tesztelési utasításokat kell megadnia a minősítési csapatnak, valamint az alkalmazás megértéséhez hasznos kiegészítő megjegyzéseket.

Miután az összes lap elkészült, és megadta a megfelelő tesztelési megjegyzéseket, válassza a **Közzététel** lehetőséget az érvényesítési és közzétételi folyamatok elindításához. A fázisok és a teljes szakasz a közzétett ajánlat típusától függően változhat. Az alábbi táblázat egy lehetséges közzétételi folyamatot mutat be. A következő szakaszok részletesebben ismertetik az egyes fázisokat.

| Fázis | Mi történik |
| ------------ | ------------- | ------------- |
| [Automatikus ellenőrzés](#automated-validation-phase) | Az automatizált érvényesítések egy készletét dolgozzák fel. |
| [Tanúsítvány](#certification-phase) | Manuális érvényesítést végezünk. |
| [Előnézet létrehozása](#preview-creation-phase) | Az ajánlat előzetes verziójának listázási lapja mindenki számára elérhető, aki rendelkezik az előzetes verziójú hivatkozással. Ha az ajánlatát a Microsoft (transactd) alapján értékesíti, akkor csak az ajánlat **előnézeti célközönsége** oldalon megadott célközönség vásárolhat és érheti el az ajánlatot tesztelésre. |
| [Közzétevő kijelentkezése](#publisher-sign-off-phase) | Egy e-mailt küldünk Önnek, amely az ajánlat előzetes megtekintését és jóváhagyását kéri. |
| [Közzététel](#publish-phase) | Egy sor lépést futtatunk annak ellenőrzéséhez, hogy az előzetes ajánlat közzé van-e téve élőben a kereskedelmi piactéren. |
|||

## <a name="automated-validation-phase"></a>Automatizált ellenőrzési fázis

A közzétételi folyamat első lépése az automatikus érvényesítések halmaza. Az egyes ellenőrzési lépések az ajánlat létrehozásakor kiválasztott szolgáltatásnak felelnek meg. Minden ellenőrzésnek teljesnek kell lennie, mielőtt az ajánlat továbblép a közzétételi folyamat következő lépésére.

- **Ajánlat-megvásárlási folyamat beállítása** (<10 perc)

   Gondoskodunk arról, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.

- **Tesztvezetés adatellenőrzése** (~ 5 perc)

   Érvényesítjük az ajánlat technikai konfigurációs oldalán megadott adatmennyiséget. Teszteljük és jóváhagyjuk a test Drive funkciót. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Tesztelési meghajtó üzembe** helyezése (~ 30 perc)

    Miután ellenőrizte az előző lépésben a tesztvezetés adatait és funkcióit, üzembe helyezheti és replikálhatja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Vezető felügyelet ellenőrzése és regisztráció** (<15 perc)

    Megerősítjük, hogy a vezető felügyeleti rendszer az **ajánlat beállítása** oldalon megadott részletek alapján fogadja az ügyfelektől érkező érdeklődőket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

## <a name="certification-phase"></a>Minősítési fázis

A kereskedelmi piactérnek küldött ajánlatokat a közzététel előtt hitelesíteni kell. Az ajánlatok szigorú tesztelésen mennek keresztül, és néhány automatizált és egyéb manuális. További információ: [kereskedelmi piactér – minősítési szabályzatok](https://aka.ms/commercial-marketplace-certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>A minősítés során megkerülő érvényesítési típusok

Az egyes elküldött ajánlatok esetében három ellenőrzési szint szerepel a minősítési folyamatban.

- Kiadói üzleti jogosultság
- Tartalom érvényesítése
- Technikai ellenőrzés

#### <a name="publisher-business-eligibility"></a>Kiadói üzleti jogosultság

Minden ajánlat típusa a szükséges alap jogosultsági feltételek készletét ellenőrzi. Ez a feltétel magában foglalhatja a közzétevő MPN-állapotát, a megőrzött kompetenciákat, a kompetenciás szinteket és így tovább.

#### <a name="content-validation"></a>Tartalom érvényesítése

Az ajánlat létrehozásakor megadott adatokat a minőség és a relevancia alapján ellenőrzi a rendszer. Ezek az ellenőrzések áttekintik a piactér-lista részleteit, a díjszabást, a rendelkezésre állást, a társított csomagokat és így tovább. A Microsoft AppSource és az Azure Marketplace tőzsdei feltételeinek teljesítése érdekében a következőket érvényesítjük:

- Az ajánlatot pontosan leíró cím
- Jól megírt leírások, amelyek átfogó áttekintést és értéket biztosítanak
- Minőségi képernyőképek és videók
- Annak magyarázata, hogy az ajánlat hogyan használja a Microsoft platformokat és eszközöket.

A tartalom-ellenőrzési feltételekkel kapcsolatos további információkért olvassa el az [általános listázási szabályzatot](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Technikai ellenőrzés

A technikai ellenőrzés során az ajánlat (csomag vagy bináris) a következő ellenőrzéseken megy keresztül.

- Kártevő szoftverek vizsgálata
- Figyelt hálózati hívások
- Elemzett csomag
- Az ajánlat funkcióinak alapos vizsgálata

Az ajánlat a különböző platformokon és verziókon tesztelve biztosítja a robusztus működést.

### <a name="certification-failure-report"></a>Sikertelen minősítési jelentés

Ha az ajánlata nem felel meg a tőzsdei, műszaki vagy házirend-ellenőrzéseknek, vagy ha nem jogosult ilyen típusú ajánlat elküldésére, e-mailt küldünk Önnek a minősítési hibajelentésről.

Ez a jelentés a sikertelen házirendek leírásait tartalmazza, valamint a felülvizsgálati megjegyzéseket. Tekintse át az e-mail-jelentést, a problémák megoldását, az ajánlat frissítését, ahol szükséges, majd küldje el újra az ajánlatot a partner Center [kereskedelmi piactér portálján](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) . A minősítés átadása előtt többször is elküldheti az ajánlatot.

## <a name="preview-creation-phase"></a>Előnézet-létrehozási fázis

Az előzetes verziójú létrehozási fázisban az ajánlat egy olyan verzióját hozunk létre, amely csak az ajánlat **előnézeti közönség** lapján megadott célközönség számára érhető el, ha van ilyen. Az ajánlat előzetes verziója nem érhető el mindenki számára az előnézeti közönségen kívül, amíg nem teszi közzé az ajánlatot élőben.

> [!NOTE]
> Ne használja az előzetes verzió célközönségét, hogy a szervezeten kívüli személyek is láthassanak egy ajánlatot. Használja helyette a privát ajánlat lehetőséget. Ezen a ponton az ajánlat nem lett teljes körűen tesztelve és érvényesítve, és nem áll készen a külső terjesztésre.

## <a name="publisher-sign-off-phase"></a>Közzétevő kijelentkezési fázisa

Ha az ajánlat készen áll arra, hogy áttekintse és kijelentkezzen, küldjön Önnek egy e-mailt, amely kéri, hogy tekintse át és hagyja jóvá az ajánlat előzetes verzióját. Az **ajánlat áttekintés** lapját is frissítheti a böngészőben, és megtudhatja, hogy az ajánlat elérte-e a közzétevő kijelentkezési fázisát. Ha igen, a **Go Live** gomb és az előzetes verziójú hivatkozások is elérhetők lesznek.

Az alábbi képernyőképen egy SaaS-ajánlat **ajánlat áttekintése** lapja látható. Az ezen az oldalon látható ellenőrzési lépések az ajánlat típusától és az ajánlat létrehozásakor létrehozott beállításoktól függően változnak.

![A partneri központban elérhető ajánlat áttekintés lapját mutatja be. Megjelenik a Go Live gomb és az előzetes verzió hivatkozásai.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Az ajánlat előzetes megtekintése és jóváhagyása

> [!IMPORTANT]
> A végpontok közötti vásárlási és beállítási folyamat ellenőrzéséhez vásárolja meg ajánlatát, amíg az előzetes verzióban is elérhető. Először értesítse a Microsoftot egy [támogatási jegyről](https://aka.ms/marketplacesupport) , hogy ne dolgozzon fel díjat.

Az **ajánlat áttekintése** lapon a **Go Live** gomb alatt láthatók az előzetes verziójú hivatkozások. A AppSource előzetes verziójához, az Azure Marketplace előzetes verziójához vagy mindkettőhöz az ajánlat létrehozásakor választott beállításoktól függően lesz hivatkozás. Ha úgy dönt, hogy az ajánlatot a Microsofton keresztül értékesíti, akkor bárki, aki hozzá lett adva az előzetes verzió célközönségéhez, tesztelheti az ajánlat beszerzését és üzembe helyezését, hogy az megfeleljen a jelen szakaszban foglalt követelményeknek.

Az előzetes verzió jóváhagyása után válassza az élő adás lehetőséget, hogy az ajánlat élőben **elérhető** legyen a kereskedelmi piactéren. 

Ha módosítani kívánja az ajánlat előzetes megtekintését követően, szerkesztheti és újra elküldheti a közzétételi kérelmet. Ha az ajánlata már él és elérhető a piactéren, az Ön által végzett frissítések csak akkor lépnek életbe, ha a **Go Live*lehetőséget választja. További információ: [meglévő ajánlat frissítése a kereskedelmi piactéren](./partner-center-portal/update-existing-offer.md)

## <a name="publish-phase"></a>Közzétételi fázis

Most, hogy úgy döntött, hogy az ajánlatával él, ami elérhetővé teszi a kereskedelmi piactéren, egy sor végső ellenőrzési ellenőrzést végzünk, hogy az élő ajánlat ugyanúgy legyen konfigurálva, mint az ajánlat előzetes verziója.

- **Ajánlat-megvásárlási folyamat beállítása** (>10 perc)

    Gondoskodunk arról, hogy az Ön ajánlata teljesíthető legyen, ha az ügyfelek megvásárolták a Azure Portalon keresztül. Ez a lépés csak a Microsofton keresztül eladott ajánlatokra vonatkozik.

- **Tesztvezetés adatellenőrzése** (~ 5 perc)

    Érvényesítjük az ajánlat technikai konfigurációs oldalán megadott adatmennyiséget. Teszteljük és jóváhagyjuk a test Drive funkciót. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Tesztelési meghajtó üzembe** helyezése (~ 30 perc)

    Üzembe helyezi és replikálja a tesztvezetés példányait, hogy azok használatra készek legyenek az ügyfelek számára. Ez a lépés csak olyan ajánlatokra alkalmazható, amelyeken engedélyezve van a tesztelési meghajtó.

- **Vezető felügyelet ellenőrzése és regisztráció** (>15 perc)

    Megerősítjük, hogy a vezető felügyeleti rendszer az **ajánlat beállítása** oldalon megadott részletek alapján fogadja az ügyfél-érdeklődőket. Ez a lépés csak az érdeklődők felügyeletét engedélyező ajánlatokra alkalmazható.

- **Végső közzététel (>30 perc)**

    Garantáljuk, hogy az ajánlat nyilvánosan elérhetővé válik a piactéren.

Az ellenőrzés befejezése után az ajánlat a piactéren lesz elérhető.

## <a name="next-steps"></a>Következő lépések

[A partner Center kereskedelmi piactérről származó analitikai jelentések elérése](partner-center-portal/analytics.md)
