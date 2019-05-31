---
title: A Partner Center kereskedelmi Marketplace-en Analytics
description: Megtudhatja, hogyan figyelheti az értékesítést, teljesítmény kiértékelése és optimalizálhatja a marketplace-ajánlat elemzési jelentések eléréséhez.
author: mattwojo
manager: evansma
ms.author: shthota
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 0da95dbdca52232b4b24ad921bf2d9c78dc76d56
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247724"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>A Partner Center kereskedelmi Marketplace-en Analytics

Ismerje meg, hogyan analitikai jelentéseket a Microsoft Partner Centeren értékesítési figyelése, a teljesítmény kiértékelése és optimalizálhatja az ajánlatokat a piactéren érheti el. Partnerként az ajánlati információk az adatvizualizációk és -Partnerközpont által támogatott insight-diagramok használatával figyelheti, és maximalizálja az értékesítést módjait. A továbbfejlesztett elemzési eszközök lehetővé teszik a teljesítmény eredmények viselkednek, és az ügyfelekkel és viszonteladók jobb kapcsolatok kezelése. 

A Partner Center elemzési eszközök eléréséhez nyissa meg a **[elemzés](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** irányítópult kereskedelmi Marketplace területen.

|**Irányítópult**|**Megjelenített tartalom**|
|:---|:---|
|[összegzés](#summary-dashboard)|Gráfok, trendeket és értékeket az összesített adatok, az ajánlatok piactéren tevékenység összefoglalója|
|[Rendelések](#orders-dashboard)|A rendelések grafikus és letölthető formában adatait|
|[Ügyfelek](#customer-dashboard)|Az ügyfelek, beleértve a növekedési trendeket, grafikus és letölthető formában adatait|
|[Letöltések](#downloads-dashboard)|A letöltési kérelmek az elmúlt 30 napban listája|

## <a name="summary-dashboard"></a>Összegzési irányítópult

A **összefoglalás** irányítópult áttekintést nyújt minden ajánlat alapján. **Insights** kritikus információk megjelenítése egy pillantással és értékesítési tevékenységét az ajánlatok széles körű dokumentálja. Ezek a jelentések használatával jelenítheti meg a **összefoglalás** irányítópult:

- [Teljes rendelések](#totals)
- [Teljes ügyfelek](#totals)
- [Az ügyfelek földrajzi helye](#customers-by-geography)
- [Új trendek alapján az ügyfél és a rendelési adatokat](#growth-trend-chart)
- [A legmagasabb rendelések ügyfél ranglista](#customer-leaderboard)
- [Ajánlat neve szerint vannak rendezve megrendelések számát](#offers-by-orders)

### <a name="date-range"></a>Dátumtartomány

Minden oldal jobb felső sarkában található egy Dátumtartomány kijelölése. Kimenetét a **összegzése** oldal gráfok testre szabható a múltban alapján dátumtartományt kiválasztásával 3, 6 vagy 12 hónapban, vagy egy egyéni dátumtartományt maximális időtartama: 12 hónapig kiválasztásával. Az alapértelmezett dátum tartománya hat hónapban.

![Partnerközpont elemzés irányítópult](./media/analyze-dashboard.png)

### <a name="totals"></a>Összegek

A **összegek** szakasz az összes létrehozott rendeléseket, vagy az ügyfelek számára, a kijelölt dátumtartományban során szerzett számát jeleníti meg. 

- A százalékos érték a **Total Orders** és **teljes ügyfelek** növekedési rátákat az előző hónapra összegét jelöli. 
- Egy pozitív növekedési trend felfelé egy zöld háromszögnek mutat jelzi. Egy piros háromszög mutat lefelé azt jelzi, hogy egy negatív növekedési trendje az előző hónapra viszonyítva. 
- Rendelés és ügyfelek növekedési trendekre a sávdiagramok képviseli, és megjeleníti az értéket az egyes hónapok az egérmutatót a diagram oszlopai.

![Partnerközpont elemzés összegek](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Felhasználók földrajzi hely szerint

A **földrajzi hely szerint ügyfelek** intenzitástérkép az ügyfelek számát a világ térképen jeleníti meg. 

- Továbbléphet a térkép megtekintéséhez a pontos helyét. 
- Egy adott helyen nagyíthatja. 
- Az intenzitástérkép rendelkezik egy kiegészítő rács ügyfelek száma részleteinek megtekintése (vagy [rendelés száma](#orders-by-geography)) az adott helyen. 
- Kereshet, és válasszon egy országot a rácsban nagyítás helyét a térképen. Az eredeti nézet billentyű megnyomásával visszatérhet az **kezdőlap** gomb a térképen.
- A **új** ügyfél egyik az ajánlatok először a kijelölt dátumtartományban belül a hónap során vásárolt.

![Partnerközpont elemzés ügyfél földrajzi hely](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Növekedési trend diagram

Megtekintheti a trendek alapján növekedését a **létrehozott rendelések** vagy **megszerzett ügyfelek**, havi megfelelően a kijelölt dátumtartományban jelennek meg. Ezek a trendek is további elemzéséhez, kövesse az alábbi hivatkozásokat a diagram, amely keresse meg a megfelelő kiválasztásával **rendelés** vagy **ügyfél** oldalakat.

![Partnerközpont elemzés növekedési trendek](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Ügyfél-ranglista

A megrendelések számát vesszük figyelembe az 50 legfontosabb ügyfeleket jelennek meg a *érdeklődők*, rendezett legmagasabb rendelés száma és a sorrend százalékos. 

- Válassza ki a vásárlók saját profil adatai, a rendelések ajánlat szerint vannak rendezve, illetve a rendelések Azure licenc típusa és árképzési csatorna szerint vannak rendezve megtekintése. 
- A **kínál a rendelések** fánkdiagram megadja az első négy ajánlatok (sorrendben száma) szerint, és a csoportosított "Rest-All", a fennmaradó kínál.

> [!NOTE]
> Ügyfelek személyes adatainak csak akkor, ha az ügyfél adta jelenik meg. Ezen információk is megtekinthetők, ha, a bejelentkezés a következővel egy **tulajdonosa** engedélyek szerepkörszintű. A felhasználók a **közreműködői** szerepkör nem fogja tudni ezt az információt. [További információ a felhasználói szerepkörök és engedélyek](./manage-account.md#define-user-roles-and-permissions).

![Partnerközpont elemzés növekedési trendek](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>A rendelések ajánlatok

A **kínál a rendelések** diagram az aktív megrendelések, az ajánlat neve alapján rendezi. 

- Akkor is húzza át szeletek a bal oldali fánkdiagramot az a megfelelő fánkdiagram annak érdekében, hogy az ajánlat húzott további részletek megjelenítéséhez. Ezeken a diagramokon lehetővé teszi egy adott ajánlat, a teljesítmény, az összes az ("Rest minden") ajánlatok összehasonlítása. 

![Partnerközpont elemzés ajánlatok által](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Rendelések irányítópult

A **rendelések** irányítópultján a **elemzés** menüben megjelenik az SaaS-ajánlatok mindegyike aktuális rendelését. Grafikus ábrázolását tekintheti meg:

- [Rendelés összegek](#order-totals)
- [Rendelések földrajzi hely szerint](#orders-by-geography)
- [Az aktív és a visszavont rendelés trendek](#trends-for-active-and-canceled-orders)
- [Rendelések marketplace licenc típusa szerint rendezve](#orders-by-marketplace-license-type)
- [Rendelések új és meglévő ügyfelek szerint rendezve](#orders-by-customer-type)
- [ORDER details táblához](#order-details-table)

> [!NOTE]
> Hogyan analytics-jelentések megjelenítése a Cloud Partner Portal (CPP) és a Partner Center új kereskedelmi Piactérrel közötti különbségek vannak. Egy meghatározott módon, hogy a **értékesítői Insights** CPP rendelkezik egy **rendelések & használati** jeleníti meg a használat alapú ajánlatokat és ajánlatok nem használat alapú lapon. A Partner Center a **rendelések** oldalon külön lapon rendelkezik SaaS kínál.

### <a name="order-totals"></a>Rendelés összegek

A **megnyitjuk** szakasz mind hozta létre, az összes megrendelések számát jeleníti meg **aktív** és **meg lett szakítva** rendelések során a kiválasztott [dátumtartománya ](#date-range). 

- A százalékos érték a **Total Orders** növekedési rátákat az előző hónapra összegét jelöli. 
- Egy pozitív növekedési trend felfelé egy zöld háromszögnek mutat jelzi. Egy piros háromszög mutat lefelé azt jelzi, hogy egy negatív növekedési trendje az előző hónapra viszonyítva. 
- Növekedési trendeket a sávdiagramok képviseli, és megjeleníti az értéket az egyes hónapok az egérmutatót a diagram oszlopai.

### <a name="orders-by-geography"></a>Rendelések földrajzi hely szerint

A **földrajzi hely szerint rendelések** intenzitástérkép világ a térképen a megrendelések számát jeleníti meg, és a functions ugyanaz, mint a  **[földrajzi intenzitástérkép ügyfelek](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Az aktív és a visszavont rendelés trendek

A **aktív orders ajánlatok** fánkdiagram graph minden, az aktív rendelések ajánlat nevük alapján rendezi.

- Az első 4 ajánlatok jelennek meg a Graph és az ajánlatokat a többi csoportosítva vannak benne, mint "Rest-All".
- Kiválaszthat meghatározott ajánlatok a jelmagyarázatban megjelenítendő csak azokat az ajánlatokat a gráfban. 
- Egy szeletet a diagramon felett megrendelések számát és százalékos arányát, hogy az ajánlat minden ajánlatok között képest a megrendelések teljes számát jeleníti meg.
- **Ajánlatok trend által Orders** havonta készülő növekedési trendeket jelenít meg. A hónap oszlop ajánlat neve szerint megrendelések számát jelöli. A vonaldiagramot egy z ábrázolt növekedési százalékos trendjét jeleníti meg.
- A diagram tetején a csúszka segítségével görgessen jobbra és balra fókusz és az x tengely mentén az adott adatpontokat.
- A trend diagram a jelmagyarázat adott elem kiválasztásával jelenítheti meg.
- Azt is beállíthatja trendeket és az adatok megjelenítéséhez **meg lett szakítva a rendelések**. A gráf aktív rendelések azonos módon fognak működni.

![Partnerközpont elemzés aktív megrendelések](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Rendelések marketplace licenc típusa szerint
<!-- Section needs review and clarification!  -->
A **marketplace licenc típusa szerint rendelések** diagram megjeleníti a licenc típusa és a számlázási módja a marketplace-ajánlat alapján havonta készülő rendelés számát. Licenctípusok a következők:

- **Azure-on keresztül számlázzuk**: A Microsoft bankkártyáján ügyfeleknek az Ön nevében, ha úgy dönt, hogy [értékesíthetik az ajánlat keretében a Microsoft](./create-new-saas-offer.md#sell-through-microsoft) és a licenc típusa. Fizetési típusok: hitelkártya vagy vállalati számlázás használatalapú fizetés.
- **A saját licenc használata**: A Microsoft nem számláz ügyfelek számára az ilyen típusú marketplace-ajánlat, felhasználási. Állapottal **[Letöltés most (ingyenes)](./create-new-saas-offer.md#get-it-now-free)** a Marketplace-en.
- **Ingyenes**: A Microsoft nem számláz ügyfelek számára az ilyen típusú marketplace-ajánlat, felhasználási. Állapottal **[az ingyenes próbaidőszak](./create-new-saas-offer.md#free-trial)** a Marketplace-en.
- **A Microsoft a viszonteladóként**: Egy Microsoft-viszonteladók által értékesített ajánlatok jelöli a  **[Cloud Solution Provider (CSP) program](./create-new-saas-offer.md#csp-program-opt-in)** .

![Írja be a Partnerközpontba elemzés rendelések licenc](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Rendelések ügyfél típusa szerint

A **ügyfél típusa alapján rendelések** sávdiagram között elosztva megrendelések számát jeleníti meg **új ügyfeleket** és **meglévő ügyfelek**. 

- A **új ügyfél** szerzett meg legalább egy, az ajánlatok először a azonos naptári hónap (y tengely) belül. Egy **meglévő ügyfél** korábban szerzett egy ajánlatot, Ön előtt a naptári hónap jelentett (y tengely). 
- Egy további tortadiagram a kijelölt dátumtartományban az új vagy meglévő ügyfél által létrehozott összes rendelést jelöli.
- Mindkét diagramon csak az új vagy csak meglévő ügyfelek megtekintéséhez válassza a megfelelő jelmagyarázat választhatja ki.

![Írja be a Partnerközpontba elemzés rendelések ügyfél által](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>ORDER details táblához

A **Order details tábla** számozott a beszerzés dátuma szerint rendezve 1000 leggyakoribb rendelések listáját jeleníti meg.

- A rács minden oszlop rendezhető.
- Az adatok egy TSV-fájlt kiolvasható, ha a bejegyzések száma 1000-nél kisebb.
- Ha a rekordok száma 1000, keresztül exportált adatok aszinkron módon kerülnek a letöltési oldalon a következő 30 napra.
- Szűrők alkalmazhatók a **Order details tábla** csak az Önt érdeklő adatok megjelenítéséhez. Adatok szűrhetők, országonként Azure licenctípust, Marketplace licenctípus, ajánlattípusra, rendelés állapota, ingyenes nyomot hagyjanak maguk után, Marketplace előfizetés-azonosító, ügyfél azonosítója, és a céges neve.

![Partnerközpont elemzés rendelés részletei](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Ügyfél-irányítópult

A **ügyfél** irányítópultján a **elemzés** menüben az adatok ügyfelek esetében, akik az ajánlatok szerezték be jeleníti meg. Grafikus ábrázolását tekintheti meg:

- [Ügyfelek összesen](#customer-totals)
- [Felhasználók földrajzi hely szerint](#customers-by-geography)
- [Ügyfelek trendekre](#customer-trends)
- [Ügyfelek által](#customers-by-orders)
- [Ügyfél details táblához](#customer-details-table)

### <a name="customer-totals"></a>Ügyfelek összesen

A **ügyfél összegek** szakasz jelenít meg minden ügyfelünk, beleértve az új, meglévő és előállított, a kiválasztott során számát [dátumtartomány](#date-range).

- Ügyfelek százalékos növekedési rátákat az előző hónap száma és felfelé mutató, a zöld vagy a lefelé mutató vörös színnel jelöli.
- Növekedési trendeket a sávdiagramok képviseli, és megjeleníti az értéket az egyes hónapok az egérmutatót a diagram oszlopai.

#### <a name="customer-types"></a>Ügyfél-típusok

Három ügyfél típusa: új, meglévő és előforduló. 

- Új ügyfél egy vagy több, az ajánlatok először a kiválasztott hónapok szerzett meg.
- Egy meglévő ügyfél szerzett meg legalább egy, az ajánlatok kiválasztva a hónap előtt.
- Churned ügyfél megszakította a korábban megvásárolt minden ajánlat.

### <a name="customer-trends"></a>Ügyfelek trendekre

A **ügyfelek trendekre** diagram minden ügyfelünk, beleértve az új, meglévő és előforduló, és a egy havonta készülő növekedési trendje számát jeleníti meg.

- A vonaldiagramot a teljes ügyfél növekedési százalékos jelöli. 
- A hónap oszlop felel meg az új, meglévő és előforduló ügyfél-ig halmozott ügyfelek száma.
- Y tengely negatív irányát az előforduló ügyfél száma jelenik meg.
- Kiválaszthatja az adott jelmagyarázat-elemek jelennek meg, további részletes nézeteket. Például válassza ki az új ügyfelek csak az új ügyfelek megjelenítése a jelmagyarázatból.
- A diagram tetején a csúszka segítségével görgessen jobbra és balra az x tengelyen, így nyugodtan összpontosíthat a meghatározott adatpontokat további részletek megtekintéséhez.
- Egy oszlop, a diagram felett jelenik meg részletei csak az adott hónapban.

![Partnerközpont elemzés ügyfelek trendekre](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Ügyfelek által

A **ügyfelek által a rendelések** diagram megjeleníti a **felső ügyfél PERCENTILIS** mentén az x tengely, mint a megrendelések számát határozza meg. Az y tengelyen jeleníti meg az ügyfél rendelés száma. A z (vonaldiagram) összegző százalékos aránya a megrendelések teljes számát jeleníti meg. Megjelenítheti a részleteket az egérmutatót a vonaldiagram mentén pontok.

Tegyük fel tudhat meg a a rendelés ügyféladatai, amely az első 30 %-a az ügyfelek a rendelések 2,130 rendelések egyenlő 83 % működik közre.

![Partnerközpont elemzés megrendelések](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Ügyfél details táblához

A **ügyfél details tábla** számozott listát a top 1000 ügyfelek először megszerezték a ajánlatok egyik dátuma szerint rendezve jeleníti meg.

- Ügyfelek személyes adatainak csak akkor érhető el, ha az ügyfél adta. Csak megtekintheti ezeket az információkat, a bejelentkezés a következővel: Ha egy **tulajdonosa** szerepkör szintű engedélyeket. [További információ a felhasználói szerepkörök és engedélyek](./manage-account.md#define-user-roles-and-permissions).
- A rács minden oszlop rendezhető.
- Az adatok egy TSV-fájlt kiolvasható, ha a bejegyzések száma 1000-nél kisebb.
- Ha a rekordok száma 1000, keresztül exportált adatok aszinkron módon kerülnek a letöltési oldalon a következő 30 napra.
- Szűrők is alkalmazható a táblázat csak az Önt érdeklő adatok megjelenítéséhez. Adatok szűrt vállalat neve, az ügyfél-azonosító, Marketplace előfizetés-azonosító, Azure licenctípus, dátum megszerzett, dátum elveszett, ügyfél E-mail, ügyfél város/állam / / Zip, ügyfél nyelvi, stb.

![Partnerközpont elemzés ügyfelek részletei](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Letöltések irányítópult

A **letölti** irányítópultján a **elemzés** menü bármely ügyfél vagy a rendelési adatok több mint 1000 sort tartalmazó letöltések kéréseket jeleníti meg.

Egy előugró értesítések és mutató hivatkozást tartalmazó e-mailt fog kapni a **letölti** irányítópult, amikor több mint 1000 adatsorokat tartalmazó letöltés kér. Adatok letöltéseket egy 30 napos időtartamon belül lesz elérhető, és ezután eltávolítja.

![Partnerközpont elemzés letöltések](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Adatok kifejezések listája

| Attribútum neve | Jelentések | Meghatározás|
|---|---|---|
| Azure License Type | Ügyfél, Order | Az ügyfelek használják az Azure megvásárolható licencszerződés típusa. Más néven csatorna |
| Azure License Type: Cloud Solution Provider program | Ügyfél, Order | A végfelhasználó ügyfél vásárol az Azure-ban és a Piactéri ajánlat használatával a Cloud Solution Provider, akik viszonteladójával funkcionál.|
| Azure License Type: Vállalati | Ügyfél, Order | A végfelhasználó ügyfél vásárol az Azure-ban és a Piactéri ajánlat aláírt közvetlenül a Microsoft nagyvállalati szerződés keretében.|
| Azure License Type: Vállalati viszonteladói keresztül  | Ügyfél, Order | A végfelhasználó ügyfél Azure-ban és a Piactéri ajánlat lehetővé teszi a Microsoft nagyvállalati szerződés viszonteladótól vásárol.|  |
| Azure License Type: Használatalapú fizetés| Ügyfél, Order | A végfelhasználó ügyfél Azure-ban és a Piactéri ajánlat keresztül egy "Használatalapú fizetés" megállapodás, közvetlenül a Microsofttól vásárol.||
| Felhő-példány neve| Rendelés| A Microsoft Cloud egy virtuális gép üzembe helyezésének lépett fel.||
| Felhő-példány neve: Azure Global| Rendelés| A nyilvános globális Microsoft cloud.|| |
| Felhő-példány neve: Azure Government | Rendelés| Kormányzati Microsoft felhők a következő kormányok egyike: Kína, Németország, vagy az Egyesült Államok jogszabályaival.| |
| Ügyfél városa| Ügyfél| A város nevét, az ügyfél által biztosított. Város egy ügyfél az Azure-előfizetés az városa eltérő lehet.||
| Ügyfél-kommunikáció nyelv  | Ügyfél| A nyelv, az ügyfél-kommunikációhoz ajánlott.||
| Ügyfél cég neve | Ügyfél, Order | A vállalat nevét az ügyfél által biztosított. Lehet, hogy a neve eltér az városa egy ügyfél az Azure-előfizetés.|  |
| Vevői ország | Ügyfél, Order | Az ügyfél által megadott ország neve. Ország, mint az ország, egy ügyfél az Azure-előfizetés eltérő lehet.|  |
| Ügyfél e-mailben| Ügyfél| A végfelhasználó ügyfél által biztosított e-mail-címe. Lehet, hogy egy ügyfél az Azure-előfizetés az e-mail-cím eltér e-mailt.||
| Ügyfél első neve| Ügyfél| Az ügyfél által megadott név. Lehet, hogy a neve eltér az ügyfél Azure-előfizetéshez megadott névvel.| |
| Ügyfél-azonosító | Ügyfél, Order | Egy ügyfél hozzárendelt egyedi azonosítója. Egy ügyfél rendelkezhet, nulla vagy több Azure piactér-beli előfizetések.|  |
| Ügyfél postai irányítószám  | Ügyfél| A postai irányítószám, az ügyfél által biztosított. Lehet, hogy a kód egy ügyfél az Azure-előfizetés a megadott irányítószám eltér.| |
| Ügyfél állapota| Ügyfél| Az állam (cím) a vásárló által biztosított. Állapota a vezetéknevet, egy ügyfél az Azure-előfizetéshez biztosított eltérő lehet.| |
| Beszerzés dátuma| Ügyfél| Első napjától az ügyfél az Ön által közzétett bármely ajánlatot vásárolt.| |
| Elveszett dátuma| Ügyfél| A legutóbbi dátum: az ügyfél korábban megvásárolt minden ajánlat az utolsó megszakítva.||
| Új ügyfél  | Rendelés| Az érték beszerzése legalább egy, az ajánlatok először a (vagy sem) egy új ügyfél azonosítja. Érték lesz az "Igen" azonos naptári hónap belül a "Dátum beszerzett". Érték "No" lesz, ha az ügyfél megvásárolta, az ajánlatok előtt a naptári hónap jelentett bármelyikét. |
| Termékváltozat előzetes| Rendelés| Az érték értesítjük, ha az "előnézet" Termékváltozattal rendelkezik címkékkel ellátott. Érték lesz, "Igen" Ha a Termékváltozat címkével ellátott annak megfelelően, és csak az Azure-előfizetések által, üzembe helyezheti és használja ezt a képet. Érték "No" lesz, ha a Termékváltozat nem azonosította "előzetes".  |
| Promóciós ügyfél engedélyezve van a| Ügyfél| Az érték értesítjük, ha az ügyfél proaktív módon másolásért gyártóktól származó promóciós kapcsolatba. Jelenleg azt nem bemutatót a beállítást az ügyfelek számára, így azt jelezte, hogy a tábla között a "No". Ez a funkció üzembe helyezését követően megkezdjük hogy ennek megfelelően frissíti.|
| Marketplace-en licenc típusa| Rendelés| A Marketplace-ajánlat a számlázási mód.||
| Marketplace License Type: Számlázás az Azure-on keresztül| Rendelés| A Microsoft az ügynök ezt az ajánlatot a piactéren, és keresztül számláz az adott felhasználók az Ön nevében. (Használatalapú hitelkártya vagy vállalati számla)||
| Marketplace License Type: A saját licenc használata | Rendelés| A virtuális gép üzembe helyezéséhez az ügyfél által biztosított licenc kulcs szükséges. A Microsoft nem számláz ügyfelek ily módon a piactéren ajánlataik listázásához.||
| Marketplace License Type: Ingyenes| Rendelés| Az ajánlat ingyenes minden felhasználóra van konfigurálva. A Microsoft nem számláz ügyfelek azok Ez az ajánlat használatáért.||
| Marketplace License Type: A Microsoft a viszonteladóként  | Rendelés| A Microsoft a viszonteladójával, ezt az ajánlatot a piactéren.|  |
| Marketplace-en előfizetés-azonosító | Ügyfél, Order | Az Azure-előfizetés az ügyfél a piactéren az ajánlat kifizetni társított egyedi azonosítója. Az Azure-előfizetés GUID azonosító korábban volt.||
| Csomag neve  | Rendelés| A Marketplace kínálatának neve.|| |
| Csomag típusa  | Rendelés| A Microsoft Marketplace-ajánlat típusát.|||
| Csomag típusa: Felügyelt alkalmazás  | Ahhoz, | Használja az Azure-alkalmazás: az ajánlat alkalmazástípus kezelése, ha az alábbi feltételek szükségesek: Vagy egy előfizetés-alapú megoldás üzembe helyezése egy virtuális gép vagy a teljes IaaS-alapú megoldás segítségével az ügyfelek számára. Ön vagy az ügyfél van szükség, hogy a megoldás lehet egy partner kezeli. |
| Csomag típusa: Azure-alkalmazás| Ahhoz, | Az Azure-alkalmazások megoldás ajánlat sablontípus akkor használja, ha a megoldáshoz szükséges további telepítési és konfigurációs automation túl egy egyszerű virtuális Gépet.||
| Csomag típusa: Tanácsadói szolgáltatás| Rendelés| Az Azure Marketplace tanácsadási szolgáltatások segítségével való csatlakozáshoz ügyfeleket támogatja, és bővítheti használata során az Azure-szolgáltatásokkal.| |
| Csomag típusa: Tároló | Rendelés| A tároló ajánlattípusra használja, amikor a megoldás üzembe egy Kubernetes-alapú Azure tárolószolgáltatás, Docker-tároló rendszerképét.||
| Csomag típusa: Dynamics 365 Business Central| Rendelés| Ez az ajánlat típusát használja, ha a megoldás integrálva van a Dynamics 365 for Finance and Operations| |
| Csomag típusa: Dynamics 365 for Customer Engagement | Rendelés| Ez az ajánlat típus akkor használja, ha a megoldás integrálva van a Dynamics 365 for Customer Engagement.||
| Csomag típusa: IoT Edge Module | Rendelés| Az Azure IoT Edge-modulok IoT Edge által kezelt legkisebb számítási egységek és a Microsoft-szolgáltatások (például az Azure Stream Analytics), 3. fél szolgáltatások vagy a saját megoldásspecifikus kódját is tartalmazhat. |
| Csomag típusa: Power BI-alkalmazás | Rendelés| A Power BI alkalmazás ajánlattípusra használja a Power BI szolgáltatással integrált alkalmazás központi telepítésekor.|  |
| Csomag típusa: SaaS-alkalmazás| Rendelés| Az SaaS-alkalmazás ajánlattípus használatával engedélyezhető az ügyfelek a SaaS-alapú, technikai megoldás egy előfizetés vásárlása.||
| Csomag típusa: Virtuális gép | Rendelés| Virtuális berendezés az előfizetéshez társított az ügyfelek központi telepítéséhez használja a virtuális gép ajánlat típusát.||
| Csomag típusa: Visual Studio Marketplace Extension  | Rendelés| Csomag típusa korábban elérhető az Azure DevOps bővítmény fejlesztői számára. Továbbítás az Azure DevOps fog bővítmény fejlesztők is értékesítheti a bővítmény közvetlenül az ügyfelek számára. Bővítmény ajánlatok konfigurálható, fizetős vagy egy próbaverzióra beleértve. |
| Rendelés megszakítás dátuma| Rendelés| A dátum a Marketplace-en rendelés törölve lett.||
| Order ID (Rendelésazonosító)| Rendelés| Az ügyfél ahhoz a Piactéri szolgáltatás egyedi azonosítója. Virtuális gép használatalapú ajánlatok, amelyek nem tartoznak egy megrendelés.| |
| Rendelés vásárlás ideje| Rendelés| A piactér sorrendben létrehozásának dátuma.|||
| Rendelés állapota| Rendelés| Az adatok utolsó frissítésének időpontjában a Marketplace-en rendelés állapota.|     |
| Rendelés állapota: Aktív  | Rendelés| Az ügyfél egy megrendelés vásárolt, és nem visszavonta a rendelési.|         |
| ORDER Status: megszakítva | Rendelés| Az ügyfél korábban megvásárolt egy rendelést, és ezt követően a megfelelő sorrendbe megszakítva.||
| Szolgáltató E-mail| Ügyfél| A Microsoft és a végfelhasználó ügyfél közötti kapcsolat részt szolgáltató e-mail-címét. Ha az ügyfél vállalati viszonteladói keresztül, ez lesz a viszonteladó. Ha egy Felhőszolgáltató (CSP) van szó, ez lesz a kriptográfiai Szolgáltató.|
| Szolgáltató neve| Ügyfél| A Microsoft és a záró ügyfél közötti kapcsolat részt szolgáltató neve. Ha az ügyfél vállalati viszonteladói keresztül, ez lesz a viszonteladó. Ha egy Felhőszolgáltató (CSP) van szó, ez lesz a kriptográfiai Szolgáltató.|
| SKU| Rendelés| Termékváltozat neve meghatározott közzététele során. Ajánlat előfordulhat, hogy sok termékváltozatok, de a Termékváltozat csak egyetlen ajánlatba társítva.||
| Próbaverziós befejezési dátum| Rendelés| A dátum a próbaidőszak alatt a megrendelés megszűnik, vagy véget ért.||