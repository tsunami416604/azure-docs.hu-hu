---
title: Azure nagyvállalati regisztrációs számlák
description: Ez a cikk az Azure Nagyvállalati számlák kezelését ismerteti.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 1379f4acb88020234aa3a8fa2d3289f0bff0f108
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76769552"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure nagyvállalati regisztrációs számlák

Ez a cikk az Azure Nagyvállalati Szerződés (Azure EA) számláinak kezelését ismerteti. A számla a számlájának felel meg. Ellenőrizze a pontosságát. Azokkal a feladatokkal is érdemes megismerkednie, amelyekre szükség lehet a számla kezeléséhez.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Túlhasználati számla rendelésszámának módosítása

Az Azure Enterprise Portal automatikusan létrehoz egy alapértelmezett rendelési (PO-) számot, ha a vállalati rendszergazda nem állít be egyet a számlázási dátum előtt. A vállalati rendszergazda az automatikus számlaértesítő e-mail beérkezése utáni hét napban frissítheti a rendelési számot.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Az Azure-szolgáltatások rendelési számának frissítése:

1. Az Azure Enterprise Portalon válassza a **Jelentés** > **Használati adatok összegzése** lapot.
1. Válassza a jobb felső sarokban található, **rendelési számok szerkesztésére** szolgáló lehetőséget.
1. Jelölje be az **Azure-szolgáltatások** választógombot.
1. Válasszon ki egy **számlázási időszakot** a dátumtartományok legördülő menüjéből.

   A rendelésszámot hét napig szerkesztheti a számláról szóló értesítést követően, addig, amíg nem egyenlíti ki a számlát.
1. Adja meg az új rendelési számot a **Rendelésszám** mezőben.
1. Válassza a  **Mentés**  lehetőséget a módosítások küldéséhez.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Az Azure Marketplace rendelési számának frissítése:

1. Az Azure Enterprise Portalon válassza a **Jelentés** > **Használati adatok összegzése** lapot.
1. Válassza a jobb felső sarokban található, **rendelési számok szerkesztésére** szolgáló lehetőséget.
1. Jelölje be a **Marketplace** választógombot.
1. Válasszon ki egy **számlázási időszakot** a dátumtartományok legördülő menüjéből.

   A rendelésszámot hét napig szerkesztheti a számláról szóló értesítést követően, addig, amíg nem egyenlíti ki a számlát.
1. Adja meg az új rendelési számot a **Rendelésszám** mezőben.
1. Válassza a  **Mentés**  lehetőséget a módosítások küldéséhez.

## <a name="cadence-of-azure-enterprise-billing"></a>Az Azure Enterprise-számlázás ütemezése

A Microsoft a regisztráció hatályba lépésének dátumától kezdve évente számlázza a Microsoft Azure-szolgáltatások költségkeret terhére történő vásárlásait. A kötelezettségvállalás összegét meghaladó használatot a Microsoft utólagosan számlázza.

- A kötelezettségvállalás díja havidíj alapján van kiszabva, és előre, évente számlázzuk ki.
- A keretet meghaladó szolgáltatások díját havonta számítjuk ki, és a számlázási időszak végén számlázzuk.

### <a name="billing-intervals"></a>Számlázási időközök

A számlázási intervallum attól függ, hogyan kívánja teljesíteni a kötelezettségvállalásait. Az Ön éves kötelezettségvállalása egybeesik az alábbiak valamelyikével:

- A regisztráció évfordulójának dátuma
- Az egyéves módosítási előfizetés hatályba lépésének időpontja.

A túlhasználatról szóló számla kézhezvételének dátuma a regisztráció kezdési dátumától és a beállítástól függ:

- **Közvetlen regisztrációk 2018. május 1. előtti kezdési dátummal**:
  - Ha Ön közvetlen nagyvállalati Azure-ügyfél, akkor éves számlázási ciklusa van az Azure-szolgáltatásokhoz, kivéve az Azure Marketplace-szolgáltatásokat. Számlázási ciklusa az évforduló dátumán alapul: azon az időponton, amikor a szerződése hatályba lépett.
  - Ha túllépte az EA pénzügyi keret (MC) küszöbének 150%-át, akkor a rendszer automatikusan negyedéves számlázási ciklusra váltja, amely az évforduló dátumán alapul. Emellett kapni fog egy, az Azure-szolgáltatások túlhasználatáról szóló számlát is.
  - Ha nem haladja meg az MC-küszöbérték 150%-át, a regisztráció az egyéves számlázási ciklusban marad. A túlhasználatról szóló számlát a kötelezettségvállalási év végén kapja meg.

- **Közvetlen regisztrációk 2018. május 1. utáni kezdési dátummal**:
  - Az Ön Azure-használatát és -díjait külön, havi számlázási ciklusban kiállított számlákon számlázzuk.
  - A pénzügyi kerete által nem fedezett díjak túlhasználati díjként lesznek esedékesek.  

- **Közvetett regisztrációk 2018. május 1. előtti kezdési dátummal rendelkező regisztrációval**:
  
  Ha Ön közvetett vállalati megállapodással (EA) rendelkező ügyfél, és a szerződés kezdési dátuma 2018. május 1. elé esik, akkor negyedéves számlázási ciklussal rendelkezik majd. A csatornapartner (CP) közvetlenül Önnek állítja ki a számlát.  

- **Közvetett regisztrációk 2018. május 1. utáni kezdési dátummal**:
  
  Költségei havi elszámolási időszakra lesznek vetítve.  

### <a name="increase-your-monetary-commitment"></a>A pénzügyi keret növelése

Kötelezettségvállalása bármikor megnövelhető. Az adott év kötelezettségvállalási időszakából fennmaradó hónapok száma után állítjuk ki a számlát. Ha például egyéves módosítási előfizetést regisztrál, és a hatodik hónap során növeli a kötelezettségvállalását, akkor az adott időszak hátralévő hat hónapjára szólóan kap számlát. A kötelezettségvállalásban foglalt mennyiségeket a rendszer ezután frissíti a kötelezettségvállalási időszak utolsó hat hónapjára vonatkozóan. A rendszer ezeket az új mennyiségeket használja fel a túlhasználati díjak meghatározásakor.

### <a name="overage"></a>Overage (Kereten túli díjak)

Túlhasználat esetén a kötelezettségvállalását a számlázási időszak során meghaladó használatért vagy foglalásokért kell fizetnie. Ha meg szeretné tekinteni az egyes tételekhez kapcsolódó túlhasználati mennyiségek számításának részletezését, tekintse meg a használati adatokat összegző jelentést, vagy forduljon csatornapartneréhez.

A számla minden tételénél látni fogja:

- **Bővített összeg**: a teljes díj
- **Kötelezettségvállalási összeg**: a díjak fedezésére felhasznált kötelezettségvállalás díja
- **Nettó összeg**: a kötelezettségvállalást meghaladó díjak összege

A vonatkozó adókat csak a kötelezettségvállalást meghaladó nettó összegre számítjuk ki.

A túlhasználat számlázása automatikusan történik. Az értesítések és a számlák érkezésének ideje az Ön számlázási időszakának záródátumától függ.

- A túlhasználatról szóló értesítést általában hét nappal az ügyfél számlázási időszakának záródátuma után küldi el a rendszer.
- A túlhasználatról szóló számlákat a rendszer 7–9 nappal az értesítés után küldi el.
- A túlhasználatról szóló értesítés és a számlázás közötti hét nap során áttekintheti a díjakat, és frissítheti a rendszer által létrehozott rendelésszámokat.

### <a name="azure-marketplace"></a>Azure Piactér

A 2019. áprilisi számlázási ciklustól kezdve az ügyfelek egyetlen Azure-számlát fognak kapni, mert az Azure és az Azure Marketplace díjainak mindegyikét egyetlen számlán egyesítettük, két külön számla kiküldése helyett. Ez a változás nem érinti az ausztrál, japán vagy szingapúri ügyfeleket.

Az összevont számlára való áttérés során egy részleges Azure Marketplace-számlát fog kapni. Ez az utolsó külön számla a számlázás frissítésének dátuma előtti Azure Marketplace-díjakat tartalmazza. A frissítés dátumát követően keletkező Azure Marketplace-díjak az Azure-számlán lesznek láthatók. Az áttérési időszak után az Azure- és Azure Marketplace-díjak mindegyikét az összevont számlán fogja látni.  

## <a name="adjust-billing-frequency"></a>Számlázási gyakoriság módosítása

Az ügyfél számlázási gyakorisága lehet éves, negyedéves vagy havi. A számlázási ciklus akkor lesz meghatározva, amikor az ügyfél aláírja a szerződést. A havi számlázás a legkisebb választható számlázási időszak.

- A közvetlen regisztrációk számlázási ciklusának évesről negyedévesre történő módosításához a vállalati rendszergazda **jóváhagyása** szükséges. A közvetett regisztrációk esetén a partnerrendszergazda jóváhagyására van szükség. A módosítás az aktuális számlázási negyedév végén lép életbe.
- A számlázási ciklus évesről vagy negyedévesről havira való megváltoztatásához **módosítani** kell a szerződést.  A meglévő regisztrációs számlázási ciklus módosításához a vállalati rendszergazda, vagy a „Számlázási kapcsolattartó” jóváhagyása szükséges.
- **Küldje be** jóváhagyását az [Azure Enterprise Portal ügyfélszolgálatához](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Válassza ki a problémakategóriát: **Számlázás és számlakiállítás**.

A módosítás az aktuális számlázási negyedév végén lép életbe.

Ha aláírt egy M503-as módosítást, bármely szerződés gyakoriságát módosíthatja havi számlázásra. 

## <a name="credits-and-adjustments"></a>Jóváírások és helyesbítések

A regisztrációra alkalmazott jóváírások vagy helyesbítések az [Azure Enterprise Portal](https://ea.azure.com) **Jelentések** területén láthatók.

Jóváírások megtekintése:

1. Az [Azure Enterprise Portalon](https://ea.azure.com) válassza a **Jelentések** szakaszt.
1. Válassza a **Használati adatok összegzése** lehetőséget.
1. A jobb felső sarokban módosítsa az **M** nézetet **C** nézetre.
1. Bontsa ki a helyesbítési mezőt az Azure-szolgáltatás kötelezettségvállalási táblájában.
1. Láthatja a regisztrációra alkalmazott jóváírásokat és azok rövid magyarázatát. Például: Szolgáltatói szerződésben szereplő jóváírás.

## <a name="request-an-invoice-copy"></a>Számlamásolat kérése

A számlamásolatok kéréséhez forduljon a partneréhez.

## <a name="overage-offset"></a>Többletköltségek kiegyenlítése

Pénzügyi kötelezettségvállalásának a túlteljesítésre való alkalmazásához meg kell felelnie a következő feltételeknek:

- A kiszámlázott szolgáltatás záró dátumától számított egy éven belüli, felmerült, de ki nem fizetett többletköltségekkel kell rendelkeznie.
- A rendelkezésre álló pénzügyi keret összegének az összes felmerült költségét fedeznie kell, a régebbi ki nem fizetett Azure-számláit is beleértve.
- A teljesítendő számlázási időszaknak teljesen lezártnak kell lennie. A számlázás teljes lezárása minden hónap ötödik napja után történik.
- A kiegyenlíteni kívánt számlázási időszaknak teljesen lezártnak kell lennie.
- Azure Hűségkedvezménye (ACD) a tényleges új kereten alapul a korábbi felhasználásra szánt összegek levonásával. Ez a követelmény csak a felmerült többletköltségekre vonatkozik. Csak a pénzügyi keretet felhasználó szolgáltatásoknál működik, ezért az Azure Marketplace díjai esetén nem érvényes. Az Azure Marketplace díjait külön számlázzuk.

Ha ki szeretné egyenlíteni a többletköltségeket, Ön vagy az ügyfélkapcsolati csapat megnyithat egy támogatási kérést. Szükség van egy e-mailes jóváhagyásra az EA-rendszergazdájától vagy a számlázási kapcsolattartótól.

## <a name="view-price-sheet-information"></a>Árlista adatainak megtekintése

A vállalati rendszergazdák megtekinthetik az Azure-szolgáltatások regisztrációihoz társított árlistát.

Az aktuális árlista megtekintése:

1. Az Azure Enterprise Portalon válassza a **Jelentések** szakaszt, majd az **Árlista** elemet.
2. Tekintse meg az árlistát, vagy válassza a **Letöltés** lehetőséget.

![Árlista adatait bemutató példa](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Korábbi árlista letöltése:

1. Az Azure Enterprise Portalon válassza a **Jelentések** szakaszt, majd a **Használati adatok letöltése** elemet.
2. Töltse le az árlistát.

![A régebbi árlisták letöltési helyét bemutató példa](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Az árképzés eltéréseinek lehetséges okai:

- Előfordulhat, hogy a korábbi regisztráció és az új regisztráció eltérő díjszabással rendelkezik. Az árak azért változhatnak, mert a díjszabás az adott regisztráció esetében szerződéses jellegű, a szerződés kezdő dátumától egészen a záró dátumáig.
- Amikor új regisztrációra vált, az az új szerződés díjszabását követi. Az árakat az árlista határozza meg, ezért előfordulhat, hogy az árak magasabbak lesznek az új regisztrációban.
- Ha egy regisztrációt meghosszabbítanak, a díjszabás is változni fog. Az árak használatalapú fizetésre módosulnak.

## <a name="request-detailed-usage-information"></a>Részletes használati adatok kérése

A vállalati rendszergazdák az Azure Enterprise Portalon megtekinthetik a használati adataik összegzését, a felhasznált pénzügyi keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

Az adott fiókok részletes használatának megtekintéséhez lépjen a **Jelentések** > **Használati adatok letöltése** elemre, és töltse le a Használati adatok jelentést.

> [!NOTE]
> A használati adatokról szóló jelentés nem tartalmazza a vonatkozó adókat. A használat felmerülésének időpontja és a jelentésben való megjelenése között akár nyolc órás késés is lehet.

Közvetett regisztrációk esetén a partnerének engedélyeznie kell az árrés funkciót ahhoz, hogy meg tudja tekinteni a költségekkel kapcsolatos információkat.

## <a name="reports"></a>Jelentések

A vállalati rendszergazdák az Azure Enterprise Portalon megtekinthetik a használati adataik összegzését, a felhasznált pénzügyi keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

### <a name="azure-enterprise-reports"></a>Azure Enterprise-jelentések

- Használati adatok összegzése és grafikonok
- Szolgáltatáshasználati jelentés
- Egyenleg és díjak jelentése
- Használati adatok jelentése
- Az Azure Marketplace díjainak jelentése
- Árlista
- Speciális jelentés letöltése
- CSV-jelentés formázása

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>A használati adatokat összegző jelentések és ábrák megtekintése:

1. Nyissa meg az Azure Enterprise Portalt.
1. Válassza a bal oldali panelen lévő **Jelentések** elemet.
1. Válassza a **Használati adatok összegzése** fület.
1. Válassza ki a kötelezettségvállalási szintet a bal felső sarokban lévő dátumtartományok menüből.
1. Jelöljön ki egy időtartamot vagy egy hónapot a grafikonon további részletek megtekintéséhez.
1. Ezen a lapon:
   - Tekintse meg a grafikont, amely a havi használatot mutatja használati adatok, a szolgáltatások túlhasználata, a külön számlázott díjak és az Azure Marketplace-költségek megjelenítésével.
   - A grafikon alatt részlegek, fiókok és előfizetések alapján szűrheti az adatokat.
   - Válthat a **díjak szolgáltatások szerint** és a **díjak hierarchia szerint** való lebontása között.
   - Részletesen megtekintheti az Azure-szolgáltatásokat, a külön számlázott díjakat és az Azure Marketplace díjait.

## <a name="service-usage-report"></a>Szolgáltatáshasználati jelentés

A Szolgáltatáshasználati jelentés oldalon a vállalati rendszergazdák megtekinthetik a szolgáltatáshasználati adataik összegzését. A használati adatok minden fióknál és előfizetésnél az összegzés szintjén jelennek meg. A jelentést fiókok vagy előfizetések alapján is szűrheti a használat részleteinek megtekintéséhez.

> [!NOTE]
> A használat felmerülésének időpontja és a jelentésben való megjelenése között akár öt nap késés is lehet.

### <a name="to-view-the-report"></a>A jelentés megtekintése:

1. Jelentkezzen be az Azure Enterprise Portalra.
1. Válassza a bal oldali navigációs területen lévő **Jelentések** elemet.
1. Válassza a **Használati adatok összegzése** fület.
1. Válassza ki a dátumtartományt.
1. Válassza ki a megtekinteni kívánt fiókokat vagy előfizetéseket.
1. Lehetősége van az alábbiakra is:
   - A különböző lebontások megtekintéséhez váltson a díjak **szolgáltatások** vagy **hierarchia** szerinti nézetének megjelenítése között.
   - Megtekintheti a részleteket, beleértve a szolgáltatás nevét, a mértékegységet, a felhasznált egységeket, a tényleges díjat és a kiterjesztett költségeket.

## <a name="download-csv-reports"></a>CSV-jelentések letöltése

A vállalati rendszergazdák a Havi jelentés letöltése lapot használhatják több jelentés CSV-fájlként való letöltéséhez. A letölthető jelentések közé tartoznak a következők:

- Egyenleg és díjak jelentése
- Használati adatok jelentése
- Az Azure Marketplace díjainak jelentése
- Árlista

### <a name="to-download-reports"></a>A jelentések letöltéséhez:

1. Az Azure Enterprise Portalon válassza a **Jelentés** lapot.
1. Válassza ki a **Használati adatok letöltése** elemet a felső menüszalagon.
1. Válassza a **Letöltés** elemet a megfelelő havi jelentés mellett.

### <a name="csv-report-formatting-issues"></a>CSV-jelentés formázási problémái

Az Azure Enterprise Portal CSV-jelentéseit euróban megtekintő ügyfelek a vesszők és pontok miatt formázási problémákba ütközhetnek.

A következőket láthatja például:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Óra | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

A következőnek kell megjelennie:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Óra | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Ez a formázási probléma az Excel importálási funkciójának alapértelmezett beállításai miatt merül fel. Az Excel az összes mezőt „Általános” szövegként importálja, és feltételezi, hogy a számok a következő matematikai szabvány szerint vannak elválasztva. Például: „1,000.00”.

Így az ezresek elválasztásához pontot (.) és a tizedesjegyek elválasztásához vesszőt (,) használó európai pénznemek esetén a szám helytelenül fog megjelenni. Például: „1.000,00” Az importálás eredménye a területi nyelvi beállításoktól függően változhat.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>A CSV-fájl formázási problémák nélkül való importálásához:

1. A Microsoft Excelben lépjen a **Fájl** > **Megnyitás** területre.
   Megjelenik a Szöveg importálása varázsló.
1. Az **Eredeti adattípus** területen válassza a **tagolt** lehetőséget.  Az alapértelmezett érték a **Rögzített szélesség**.
1. Kattintson a **Tovább** gombra.
1. Az Elválasztók területen jelölje be a **Vessző** jelölőnégyzetét. Törölje a **Tab** jelölőnégyzetet, ha be van jelölve.
1. Kattintson a **Tovább** gombra.
1. Görgessen a **ResourceRate** és az **ExtendedCost** oszlopokra.
1. Válassza ki a **ResourceRate** oszlopot. Fekete színnel lesz kiemelve.
1. Az **Oszlop adatformátuma** szakaszban az **Általános** helyett válassza a **Szöveg** lehetőséget. Az oszlopfejléc az **Általános** értékről a **Szöveg** értékre változik.
1. Ismételje meg a 8. és a 9. lépést a **Kiterjesztett költség** oszlop esetében is, majd válassza a **Befejezés** lehetőséget.

> [!TIP]
> Ha az Excel a CSV-fájlok automatikus megnyitására van beállítva, akkor ehelyett az Excel **Megnyitás** funkcióját kell használnia. Az Excelben lépjen a **Fájl** > **Megnyitás** területre.

## <a name="balance-and-charge-report"></a>Egyenleg és díjak jelentése

Az Egyenleg és díjak jelentése havi összefoglaló információkat nyújt az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a többletköltségekről.

Az Azure-szolgáltatásokra vonatkozó kötelezettségvállalás összegző táblázatának összes sora statikus marad minden hónapban. Az összes vásárlás és kiigazítás részletei az **Új vásárlások részletei** és a **Kiigazítások részletei** szakaszokban jelennek meg.

### <a name="download-the-balance-and-charge-report"></a>Az egyenleg és díjak jelentésének letöltése

1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
1. Válassza a bal oldali panelen lévő **Jelentések** elemet.
1. Válassza a **Jelentés letöltése** fület.
1. Válassza ki a megfelelő hónapot az **Egyenleg és díjak** oszlopban, és válassza a jelentés letöltése lehetőséget.

## <a name="usage-detail-report"></a>Használati adatok jelentése

A Használati adatok jelentése havi összefoglalást nyújt a regisztráció által felhasznált szolgáltatásokról és mennyiségekről. Ide tartoznak a mérőszámok nevével, a mérőszámok típusával és a felhasznált mennyiségekkel kapcsolatos információk.

### <a name="download-the-usage-detail-report"></a>A használati adatokról szóló jelentés letöltése

1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
1. Válassza a bal oldali navigációs területen lévő **Jelentések** elemet.
1. Válassza a **Használati adatok letöltése** fület.
1. Válassza ki a megfelelő hónapot a **Használati adatok** oszlopban, és válassza a jelentés letöltése lehetőséget.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Az Azure Marketplace díjai az Azure Enterprise Portal jelentéseiben

Az Azure Marketplace-díjainak két típusa van:

- **Használatalapú:** Tranzakciós egységekben mért termékek.  A virtuális gépek például óradíjjal járnak, míg a Bing API-keresések a keresések száma alapján vannak felszámítva.
- **Havi díj:** A számlázás havi használat vagy hozzáférés alapján történik.

Az Azure Marketplace-díjakkal kapcsolatos további információkért lásd: [Azure Marketplace – Gyakori kérdések](https://azure.microsoft.com/marketplace/faq/).

Az Azure Enterprise Portal különböző díjainak megtekintése:

- **Használati adatokat összegző jelentés**: Az Azure Marketplace használatalapú és havi díjait **egyaránt** megjeleníti.
- **A Marketplace díjainak jelentése**: **Csak** a használatalapú Azure Marketplace-díjakat tartalmazza.  Az egyszeri díjak nem jelennek meg.

> [!NOTE]
> Az Azure Marketplace nem érhető el az MPSA-regisztrációkhoz.

## <a name="advanced-report-download"></a>Speciális jelentés letöltése

Az adott dátumtartományokkal vagy fiókokkal kapcsolatos jelentések elkészítéséhez a speciális jelentések letöltése használható. 2016. augusztus 30-tól a kimeneti fájl formátuma a nagyobb rekordhalmazok befogadása érdekében CSV-re változik.

1. Az Azure Enterprise Portalon válassza a **Speciális jelentés letöltése** lehetőséget.
1. Válassza ki a **megfelelő dátumtartományt**.
1. Válassza ki a **megfelelő fiókokat**.
1. Válassza a **Használati adatok lekérése** lehetőséget.
1. Válassza a **Frissítés** gombot, amíg a jelentés állapota **Letöltés** értékűre nem frissül.
1. Töltse le a jelentést.

## <a name="reporting-for-non-enterprise-administrators"></a>Jelentéskészítés nem vállalati rendszergazdák számára

A vállalati rendszergazdák engedélyezhetik a költségek megtekintéséhez való hozzáférést a részlegszintű rendszergazdák (DA) és a fióktulajdonosok (AO) számára egy adott regisztrációban. A fiók tulajdonosa letöltheti a fiókjához és előfizetéseihez tartozó CSV-jelentéseket. Emellett megtekintheti az Azure Enterprise Portal jelentési szakaszaiban lévő információkat is.

### <a name="to-enable-access"></a>A hozzáférés engedélyezése:

 1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
 1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
 1. Válassza a **Regisztráció** fület.
 1. A **Regisztráció részletei** szakaszban válassza a **Díjtételek megtekintése a részlegszintű rendszergazda számára** vagy a **Díjtételek megtekintése a fióktulajdonos számára** melletti ceruza ikont.
 1. Válassza az **Engedélyezve** lehetőséget.
 1. Kattintson a **Mentés** gombra.

### <a name="to-view-reports"></a>Jelentések megtekintése:

1. Jelentkezzen be az Azure Enterprise Portalra részlegszintű rendszergazdaként vagy fióktulajdonosként.
1. Válassza a bal oldali navigációs területen lévő **Jelentések** elemet.
1. A fiókra és az előfizetésre vonatkozó információk vizuális megjelenítéséhez válassza a **Használati adatok összegzése** fület.
1. A CSV-jelentések megtekintéséhez válassza a **Használati adatok letöltése** lehetőséget.

A részlegszintű rendszergazdák és a fióktulajdonosok nem tudják megtekinteni a díjakat, ha a **Speciális jelentés letöltése** funkciót használják. A költségek 0 dollárként jelennek meg.

A Díjtételek megtekintése a fióktulajdonos számára beállítás a fióktulajdonosokra és a társított előfizetéseken engedélyekkel rendelkező összes felhasználóra kiterjed. Ha Ön közvetett ügyfél, a költségfunkciókat a csatornapartnerének kell engedélyeznie.

## <a name="move-usage-data-to-another-enrollment"></a>Használati adatok áthelyezése egy másik regisztrációba

A rendszer csak akkor helyezi át a használati adatokat, ha az átvitel visszakeltezett. Két lehetőség van a használati adatok egyik regisztrációból a másikba való átvitelére:

- Regisztrációk közötti fiókátvitel
- Regisztrációk közötti regisztrációátvitelek

Mindkét megoldásnál küldenie kell egy [támogatási kérést](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) az EA támogatási csapatnak. 

## <a name="azure-ea-pricing-overview"></a>Az Azure EA díjszabásának áttekintése

Ez a szakasz részletesen ismerteti a használat kiszámításának módját. Válaszokat ad a nagyvállalati szerződésben szereplő különböző Azure-szolgáltatások díjszabásával kapcsolatos gyakran feltett kérdésekre, amelyek esetében a számítások összetettebbek.

### <a name="price-protection"></a>Árvédelem

Ügyfélként vagy csatornapartnerként Önre garantáltan az Ön árlistáján (CPS) szereplő vagy annál alacsonyabb mértékű, illetve az Azure-vásárlás dátumakor érvényes árak érvényesek. Ezt az árat alapdíjnak nevezzük. Az Azure-vásárlást követően bevezetett szolgáltatások esetében a szolgáltatás első bevezetésekor érvényes, a megfelelő mértékű árengedménnyel ellátott árnak megfelelő díjat számlázzuk ki. Ez az árvédelem a kötelezettségvállalása időtartamára vonatkozik, amely a nagyvállalati szerződéstől függően egy vagy három év lehet.

### <a name="price-changes"></a>Árváltozások

A Microsoft a regisztráció ideje alatt csökkentheti az egyes Azure-szolgáltatások aktuális nagyvállalati szerződésének díját. Ezt a kedvezményes díjszabást kiterjesztjük a meglévő ügyfelekre, amíg az alacsonyabb díjszabás érvényes. Ha később ezek a díjak növekednek, akkor a szolgáltatás regisztrációs díja nem növekszik az Ön fent meghatározott felső árvédelmi határán túl. Az összeg azonban emelkedhet a korábbi csökkentett árhoz képest. Mindkét esetben tájékoztatjuk az ügyfeleket és a közvetett csatornapartnereket a közelgő árváltozásokról, amelyekről e-mailt küldünk a regisztrációhoz társított vállalatnak és partnerrendszergazdáknak.

### <a name="current-effective-pricing"></a>Jelenlegi érvényes díjszabás

Az ügyfelek és a csatornapartnerek a regisztrációik aktuális díjszabását az [Azure Enterprise Portalra](https://ea.azure.com/) belépve tekinthetik meg az adott regisztrációhoz tartozó Árlista oldalon. Ha az Azure-t közvetetten, az egyik csatornapartnerünkön keresztül vásárolta meg, akkor a csatornapartnertől kell beszereznie a díjszabás frissítéseit, ha a regisztrációhoz nem engedélyezték az árrésalapú díjszabás megjelenítését.

### <a name="introduction-of-new-azure-services"></a>Új Azure-szolgáltatások bevezetése

Folyamatosan fejlesztjük az Azure-t, így időnként olyan új szolgáltatásokat adunk hozzá, amelyek díjszabása a meglévő szolgáltatásoktól eltérő. Néhány előzetes verziójú szolgáltatás automatikusan elérhető, míg másokhoz az ügyfél beavatkozására van szükség az [Azure Account Portalon](https://account.windowsazure.com/PreviewFeatures).

Egyes szolgáltatások promóciós díjszabással rendelkezhetnek az első bevezetéskor, ami később növekedhet.

A szolgáltatásoknak az előzetes verzióról az általánosan elérhető (GA) verzióra való áttérésével az árak a teljes körű teljesítmény és a megbízhatósági SLA-k alkalmazásával növekedhetnek. Az ilyen emelkedést nem korlátozza a normál alapdíjvédelem. Ezeknek a szolgáltatásoknak az igénybevételét megemelt díjjal számoljuk fel. Elkerülheti az ezekkel a szolgáltatásokkal kapcsolatos díjakat, ha nem használja az új szolgáltatásokat.

### <a name="introduction-of-regional-pricing"></a>A regionális díjszabás bemutatása

Az új szolgáltatások bevezetésén túl a szolgáltatások időnként az egységes globális modellről regionálisabb modellekre váltanak, ahogy ezen szolgáltatások regionális támogatása növekszik.

Egy szolgáltatás regionalizálásának első bevezetésekor az árvédelem ugyanúgy érvényes az új régiókra. Az ugyanahhoz a szolgáltatáshoz később bevezetett további régiók azonban új szolgáltatásoknak tekintendők, így a saját egyéni díjszabásuk alapján érhetők el, az alapdíjvédelemtől függetlenül.

### <a name="enterprise-devtest"></a>Enterprise Dev/Test

A vállalati rendszergazdák engedélyezhetik, hogy a fióktulajdonosok az Enterprise Dev/Test ajánlat alapján hozzanak létre előfizetéseket. A fióktulajdonosnak be kell állítania a mögöttes előfizetők számára szükséges Enterprise Dev/Test-előfizetéseket. Ez a beállítás lehetővé teszi, hogy az aktív Visual Studio-előfizetők speciális Enterprise Dev/Test díjszabás mellett futtassanak fejlesztési és tesztelési számítási feladatokat az Azure-ban. Bővebb információ: [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Nagyvállalati szerződések használati díjának számítása

Az egyes szolgáltatások alapvető nyilvános díjszabási információit, mértékegységeit, gyakori kérdéseit és használati jelentéskészítési útmutatóit az [Azure-szolgáltatások](https://azure.microsoft.com/services/) és [Az Azure díjszabása](https://azure.microsoft.com/pricing/) című szakaszokban találja. A következő szakaszokban talál további információt az EA-számításokról.

### <a name="enterprise-agreement-units-of-measure"></a>Nagyvállalati szerződések mértékegységei

A nagyvállalati szerződések mértékegységei gyakran eltérnek a többi programban, például a Microsoft Online Services szerződés programban (MOSA) látottaktól. Ez az eltérés azt jelenti, hogy néhány szolgáltatás esetében a rendszer összesíti a mértékegységeket egy normalizált díjszabás biztosítása érdekében. Az Azure Enterprise Portal Használati adatok összegzése nézetében látható mértékegység mindig a nagyvállalati mértékegység. Az egyes szolgáltatásokhoz tartozó aktuális mértékegységek és átváltások teljes listáját a [Felhasználóbarát szolgáltatásneveket](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) tartalmazó Excel-fájlban találja.

### <a name="rounding-rules"></a>Kerekítési szabályok

Az Azure Enterprise Portal az IEEE szabvány szerinti banki kerekítés vagy Gauss-kerekítés logikáját használja. Ez a félszámjegyes értékeket a legközelebbi páros számjegyre kerekíti. A hagyományosabb felfelé kerekítő kerekítési függvények a fél számjegyeket a következő legmagasabb számjegyre kerekítik. Ez az Azure Enterprise Portalon használt módszer pontosabb összeget nyújt a csoportra vonatkozóan a normál Excel-logikához képest.

Például: ha az első lehagyott számjegy 5, és nincs következő számjegy, vagy a következő számjegyek nullák, akkor az előző számjegy páros lesz. Például a 2,315 és a 2,325 egyaránt 2,32 lesz, ha a legközelebbi 100-ra van kerekítve.

A következő táblázat bemutatja az Azure Enterprise Portal kerekítésre és konvertálásra vonatkozó szabályainak modellezéséhez használható Excel-képleteket:

| Forgatókönyv | Bankár logikai képlet |
| --- | --- |
| Kerekítés használata | = MROUND({_forrás_}, 0,0002) |
| Kerekítési díjszabás (2 tizedesjegy) | = MROUND({_forrás_}, 0,02) |
| Kerekítési díjszabás (0 tizedesjegy) | = MROUND({_forrás_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Konverzió a használati adatokról szóló jelentés és a használati adatokat összegző oldal között

A letöltött használatiadat-jelentésen látható, hogy a nyers erőforrás-használat adatai legfeljebb hat tizedesjegy pontossággal jelennek meg. Az Azure Enterprise Portal azonban négy tizedesjegyre kerekíti a használatot a kötelezettségvállalási egységek esetében, a túlhasználati egységek esetében pedig nulla tizedesjegyre rövidít. Az Azure Enterprise Portalon használt egységekre való átalakítás előtt a rendszer először négy számjegyre kerekíti a nyers használatot. Ezt követően az eredményül kapott nagyvállalati egységeket ismét négy számjegyre kerekíti. Az átalakítás előtt ténylegesen felhasznált órák csak a használati adatokat tartalmazó letöltött jelentésben jelennek meg, magán az Azure Enterprise Portalon nem.

Például: 694,533404 tényleges SQL Server-óra szerepel a használati adatokat tartalmazó jelentésben. Ezeket az egységeket a rendszer 6,94533404 egységére alakítja át 100 üzemórára vonatkozóan, amelyet aztán 6,9453-ra kerekítve jeleníti meg az Azure Enterprise Portalon.

- Ezeket az egységeket a rendszer ezután megszorozza a kötelezettségvállalási egység árával, és az eredményt két tizedesre csonkolja. A japán yen (JPY) és a koreai won (KRW) esetében a kiterjesztett mennyiség nulla tizedesjegyre van kerekítve.
- A túlhasználati díjak esetén a számlázható egységeket hat számjegyre csonkolja a rendszer, majd a kiterjesztett számlázási mennyiség meghatározása érdekében megszorozza a túlhasználati díjak egységárával.
- Felügyelt szolgáltatás szolgáltatójának (MSP) számlázása esetén az MSP-ként megjelölt részleghez tartozó összes használat nulla tizedesjegyre van csonkolva az EA mértékegységre való átalakítás után. Ennek eredményeképpen a használat összege alacsonyabb lehet, mint az Azure Enterprise Portalon jelentett összes használat összege. Ez attól függ, hogy az MSP még a pénzügyi keretének egyenlegén belül vagy már a kereten túl van.

### <a name="graduated-pricing"></a>Sávos díjszabás

A nagyvállalati szerződés díjszabása jelenleg nem tartalmaz sávos díjszabást, ahol az egységenkénti díjszabás a használat növekedésével csökken. Amikor sávos díjszabással rendelkező MOSA-programból vált nagyvállalati szerződésre, a díjszabás megállapítása a szolgáltatás alapszintje szerint történik, a nagyvállalati szerződés esetleges kedvezményeire vonatkozó kiigazítás levonásával.

### <a name="partial-hour-billing"></a>Nem teljes órák számlázása

A teljes órában számolt növekmények helyett az összes használat részleges órákká alakított percek szerint van számlázva. A felsorolt óránkénti nagyvállalati díjakat az összes órára és a részleges órákra is alkalmazni kell.

### <a name="average-daily-consumption"></a>Átlagos napi felhasználás

Vannak olyan, havi díjjal rendelkező szolgáltatások, ahol a használatról napi rendszerességgel készül jelentés. Ebben az esetben a rendszer ezt a használatot naponta egyszer kiértékeli, elosztja 31-gyel, és a számlázási hónap napjainak száma alapján összesíti. Így a díjszabás egy hónapban sem haladja meg a várt értéket, és a 31 napnál rövidebb hónapokban kissé alacsonyabb is.

### <a name="compute-hours-conversion"></a>Üzemórák átalakítása

2016. január 28. előtt minden A0, A2, A3 és A4 Standard és alapszintű virtuális gép és felhőszolgáltatás használatát az A1 virtuális gépek percenkénti egységeiként bocsátottuk rendelkezésre. Az A0 virtuális gépek használatát az A1 virtuális gépek percenkénti egységeinek törtrészének számítottuk, míg az A2-k, az A3-ak és az A4-ek többszörösekké alakultak. Mivel ez a szabályzat összezavarta a vásárlókat, a külön percalapú A0, A2, A3 és A4 mérőszámok használatára kezdünk áttérni.

Az új mérés a 2016. január 27. és január 28. között lépett érvénybe. A letöltött CSV-ben, amely az átmeneti időszak felhasználást mutatja, egyaránt láthatja a:

- Az A1 mérőszám alapján kibocsátott használatot
- Az üzemelő példány méretének megfelelő új, dedikált mérőszám alapján kibocsátott használatot.

| **Üzemelő példány mérete** | **Az A1 többszöröseként kibocsátott használat 2016. január 26-ig** | **Dedikált mérőszám alapján kibocsátott használat 2016. január 27-től** |
| --- | --- | --- |
| A0 | Az A1 óraszám 0,25-szöröse | Az A0 óraszám 1-szerese |
| A2 | Az A1 óraszám 2-szerese | Az A2 óraszám 1-szerese |
| A3 | Az A1 óraszám 4-szerese | Az A3 óraszám 1-szerese |
| A4 | Az A1 óraszám 8-szorosa | Az A4 óraszám 1-szerese |

### <a name="regions"></a>Régiók

Azon szolgáltatások esetében, ahol a zóna és a régió befolyásolja a díjszabást, az alábbi táblázatban látható a földrajzi térségek és régiók leképezése:

| Térség | Adatátviteli régiók | CDN-régiók |
| --- | --- | --- |
| 1\. zóna | Észak-Európa <br> Nyugat-Európa <br> USA nyugati régiója <br> USA keleti régiója <br> USA északi középső régiója <br> USA déli középső régiója <br> USA 2. keleti régiója <br> USA középső régiója | Észak-Amerika <br> Európa |
| 2\. zóna | Ázsia és a csendes-óceáni térség keleti régiója <br> Ázsia és a csendes-óceáni térség délkeleti régiója <br> Kelet-Japán <br> Nyugat-Japán <br> Kelet-Ausztrália <br> Délkelet-Ausztrália | Ázsia és a Csendes-óceáni térség <br> Japán <br> Latin-Amerika <br> Közel-Kelet / Afrika <br> Kelet-Ausztrália <br> Délkelet-Ausztrália |
| 3\. zóna | Dél-Brazília |   |

A szolgáltatások közötti, ugyanazon az adatközponton belüli adatforgalomért nem számítunk fel díjat. Például az Office 365 és az Azure esetében.

### <a name="monetary-commitment-and-unbilled-usage"></a>Pénzügyi keret és a ki nem számlázott használat

Az Azure pénzügyi keret az az összeg, amelyet előre kifizetnek az Azure-szolgáltatásokért. A szolgáltatások használata felhasználja a pénzügyi keretet. A belső Azure-szolgáltatások a pénzügyi keretet terhelik. Néhány díjat azonban külön számlázunk ki, és az Azure Marketplace-szolgáltatások nem használják fel a pénzügyi keretet.

### <a name="charges-billed-separately"></a>Külön számlázott tételek

Néhány külső féltől származó termék és szolgáltatás nem használja az Azure-beli pénzügyi keretet. Ehelyett az ilyen tételek esetében a számlázás külön történik a standard számlázási ciklus túlhasználati díjának részeként.

Az összes Azure- és Azure Marketplace-díjat egyetlen, a regisztráció számlázási ciklusával összhangban álló számlán egyesítettük. Az összevont számla nem vonatkozik az ausztrál, japán vagy szingapúri ügyfelekre.

Az összevont számla feltünteti az összes Azure-használatot és az esetleges Azure Marketplace-beli díjakat. Az ausztrál, japán vagy szingapúri ügyfelek továbbra is külön számlán tekinthetik meg az Azure Marketplace-díjakat.

Ha a standard számlázási ciklus végén nincs túlhasználat, a külön számlázott díjak külön számlán jelennek meg. Ez a folyamat az ausztrál, japán és szingapúri ügyfelekre vonatkozik.

A következő szolgáltatásokat külön számlázzuk:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (havi)
- Visual Studio Enterprise (éves)
- Visual Studio Professional (havi)
- Visual Studio Professional (éves)

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace EA-ügyfelek számára

A közvetlen ügyfelek esetében az Azure Marketplace-díjak az Azure Enterprise Portalon láthatók. Az Azure Marketplace-vásárlások és felhasználás számlázása a pénzügyi kereten kívül, negyedévente, havonta és utólagosan történik.

A közvetett ügyfelek megtalálhatják Azure Marketplace-előfizetéseiket az Azure Enterprise Portal **Előfizetések kezelése** lapján, de a díjszabás rejtve marad. Az Azure Marketplace díjaival kapcsolatos információkért az ügyfeleknek licencmegoldás-szolgáltatójukhoz (LSP) kell fordulniuk.

Az új havi vagy évente ismétlődő Azure Marketplace-vásárlások esetében a teljes összeg lesz kiszámlázva arra az időszakra, amelyben az Azure Marketplace-tételeket megvásárolták. Ezek a tételek a következő időszakban automatikusan megújulnak az eredeti vásárlás napján.

A havonta ismétlődő díjak megújítása továbbra is az egyes naptári hónapok első napján történik. Az éves díjak megújítása a vásárlási dátum évfordulóján történik.

Az Azure Marketplace-en elérhető külső viszonteladói szolgáltatások némelyike mostantól a nagyvállalati szerződés (EA) pénzügyi keretét használja. Ezeknek a szolgáltatásoknak a számlázása korábban az EA pénzügyi keretén kívül, külön számlákkal történt. A szolgáltatásokra az Azure Marketplace-en érvényes EA pénzügyi keret hozzájárul az ügyfelek vásárlás- és fizetéskezelésének egyszerűsítéséhez. A pénzügyi keretet használó szolgáltatások teljes listája az [Azure-webhely 2018. március 6-ai frissítésében](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/) érhető el.

### <a name="partners"></a>Partnerek

A licencmegoldás-szolgáltatók letölthetik az Azure Marketplace-árlistát az Azure Enterprise Portal Árlistájáról. Válassza a jobb felső sarokban található **Marketplace-árlista** lehetőséget. Az Azure Marketplace-árlistában az összes rendelkezésre álló szolgáltatás és azok ára is megjelenik.

Az árlista letöltéséhez:

1. Az Azure Enterprise Portalon lépjen a **Jelentések** > **Árlista** lapra.
1. A jobb felső sarokban, a felhasználóneve alatt keresse meg az Azure Marketplace-árlistára mutató hivatkozást.
1. Kattintson a jobb egérgombbal a hivatkozásra, majd válassza a **Cél mentése másként** lehetőséget.
1. A **Mentés** ablakban módosítsa a dokumentum címét a következőre: `AzureMarketplacePricelist.zip`. Ezáltal a fájl formátuma .xlsx helyett .zip lesz.
1. Ha a letöltés befejeződött, az országspecifikus árlisták egy zip-fájlban lesznek elérhetők.
1. A licencmegoldás-szolgáltatóknak az egyes országok fájljaira kell hivatkozniuk az országspecifikus díjszabás megállapításához. A licencmegoldás-szolgáltatók az **Értesítések** lapon találják azokat a termékváltozatokat, amelyek teljesen újak, illetve amelyek el lettek távolítva.
1. Ritkán fordul elő árváltozás. Ha mégis megtörténik, a licencmegoldás-szolgáltatók 30 nappal korábban e-mailben kapnak értesítést az áremelésekről és a valutaárfolyamok módosulásáról.
1. A licencmegoldás-szolgáltatók regisztrációnként, független szoftvergyártók szerint, illetve negyedévente egy számlát kapnak.

### <a name="enabling-azure-marketplace-purchases"></a>Az Azure Marketplace-vásárlások engedélyezése

A vállalati rendszergazdák engedélyezhetik vagy letilthatják az Azure Marketplace-vásárlásokat a regisztrációjukhoz tartozó összes Azure-előfizetésre vonatkozóan. Ha a vállalati rendszergazda letiltja a vásárlásokat, és vannak olyan Azure-előfizetések, amelyek már rendelkeznek Azure Marketplace-előfizetésekkel, az intézkedés ezeket az Azure Marketplace-előfizetéseket nem érinti.

Bár az ügyfelek átalakíthatják közvetlen Azure-előfizetéseiket Azure EA-előfizetéssé, ha az Azure Enterprise Portalon lévő regisztrációjukhoz társítják őket, a művelet nem konvertálja automatikusan az alárendelt gyermek előfizetéseket.

Az Azure Marketplace-vásárlások engedélyezése:

1. Jelentkezzen be az Azure Enterprise Portalra vállalati rendszergazdaként.
1. Lépjen a **Kezelés** menüpontra.
1. A **Regisztráció részletei** szakaszban válassza az **Azure Marketplace** vonalelem mellett található ceruzaikont.
1. Válassza igény szerint az **Engedélyezett/Letiltott** vagy az ingyenes **Csak BYOL-termékváltozat** lehetőséget.
1. Kattintson a **Mentés** gombra.

> [!NOTE]
> A BYOL (saját licenc használata) és a Csak ingyenes lehetőség az Azure Marketplace-vásárlásokat és -beszerzéseket a BYOL- és az ingyenes termékváltozatokra korlátozza.

További információkért lásd: [Az Azure Marketplace díjai az Azure Enterprise Portal jelentéseiben](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Az Azure EA esetében óradíj alapján számlázott szolgáltatások

Az alábbi szolgáltatások számlázása a nagyvállalati szerződés esetében óradíj alapján történik, és nem havi díjszabás szerint, mint a Microsoft Online Előfizetési Program (MOSP) esetében:

- Alkalmazásszolgáltatási hálózat
- Web Application Firewall (Webalkalmazási tűzfal)

### <a name="azure-remoteapp"></a>Azure RemoteApp

Ha nagyvállalati szerződéssel rendelkezik, akkor az Azure RemoteAppért a nagyvállalati szerződésben megállapított árszint alapján fizet. Nem számítunk fel további díjakat. A standard ár a kezdeti 40 órát tartalmazza. A korlátlan díjszabás a kezdeti 80 órát fedezi. A RemoteApp a 80 órán túl nem bocsátja ki a használatot.

## <a name="azure-marketplace-faq"></a>Azure Marketplace – Gyakori kérdések

Ez a szakasz elmagyarázza, hogy az Azure pénzügyi kerete hogyan vonatkozhat egyes harmadik felek viszonteladói szolgáltatásaira az Azure Marketplace-en.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Mi változott az Azure Marketplace-szolgáltatásokkal és az EA pénzügyi keretével kapcsolatban?

2018. március 1-jétől néhány harmadik fél viszonteladói szolgáltatása az EA pénzügyi keretét (MC) használja. Az Azure Reserved VM Instances kivételével ezeknek a szolgáltatásoknak a számlázása korábban az EA pénzügyi keretén kívül, külön számlákkal történt.

Kiterjesztettük a pénzügyi keret felhasználhatóságát, hogy az ügyfeleink által leggyakrabban vásárolt, külső féltől származó Azure Marketplace-szolgáltatásokat is lefedje. Az Azure Marketplace-en érvényes EA pénzügyi keret kiterjesztése ezen szolgáltatásokra hozzájárul az Ön vásárlás- és fizetéskezelésének egyszerűsítéséhez.

### <a name="why-did-we-make-this-change"></a>Mi oka van ennek a változtatásnak?

Az ügyfelek folyamatosan további lehetőségeket keresnek a pénzügyi keret formájában elvégzett befizetés kihasználása érdekében. Ezzel egy gyakran felmerülő vásárlói kérésre adtunk választ, illetve Azure Marketplace-ügyfeleink jelentős részének kedveztünk általa.

### <a name="how-do-you-benefit"></a>Milyen előnyt jelent ez az Ön számára?

Egyszerűbb lesz a számlázás az ügyfelek számára, és könnyebbé válik az EA pénzügyi keret teljes kihasználása. Mivel ezeket a szolgáltatásokat fedezi az előre kifizetett pénzügyi keret, az EA pénzügyi kerete értékesebb lesz.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Melyik Azure Marketplace-szolgáltatások használják az EA pénzügyi keretét? Honnan lehet ezt tudni?

Ha olyan szolgáltatást vásárol, amely a pénzügyi keretet használja, az Azure Marketplace figyelmezteti Önt. A jelenleg támogatottak közé a Red Hat, a SUSE, az Autodesk és az Oracle által közzétett egyes szolgáltatások tartoznak. A más felek által közzétett hasonló nevű szolgáltatások jelenleg nem fedezhetők a pénzügyi kerettel. A GYIK végén a teljes lista elérhető.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Mi a teendő, ha a kimerítem az EA pénzügyi keretem?

Ha felhasználta a teljes pénzügyi keretet, és jelenleg többlethasználatot folytat, az ezen szolgáltatásokkal kapcsolatos díjak a soron következő túlhasználati számlán jelennek meg a többi használatalapú szolgáltatással együtt. A 2018. március 1. előtti változtatást megelőzően ezek a díjak a többi Azure Marketplace-szolgáltatással együtt lettek kiszámlázva.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Miért nem használja az Azure Marketplace összes szolgáltatása az EA pénzügyi keretét?

Sokat dolgozunk azon, hogy az EA pénzügyi kerethez kapcsolódóan a legjobb felhasználói élményt nyújthassuk. Ez a változás számos ügyfelet érint, és az Azure Marketplace-beli teljes költés jelentős részére hatással van. A későbbiekben további szolgáltatásokkal is bővülhet a kör.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hogyan befolyásolja ez a közvetett regisztrációt és partnereket?

Mindennek nincs hatása a közvetett regisztrációs ügyfeleinkre és partnereinkre. Ezekre a szolgáltatásokra ugyanazok a partneri árréslehetőségek vonatkoznak, mint más használatalapú szolgáltatások esetében. Az egyetlen változás az, hogy a díjak egy másik számlán jelennek meg, és a díjak ellenértéke az ügyfél EA pénzügyi keretéből vonódik le.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Van lista azokról az Azure Marketplace-szolgáltatásokról, amelyek az EA pénzügyi keretét használják?

Bizonyos Azure Marketplace-ajánlatok esetében a pénzügyi keret összegei is felhasználhatók. A programban részt vevő termékek teljes listájáért tekintse meg [a pénzügyi keretet használó, harmadik féltől származó szolgáltatások](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) szakaszt.

## <a name="power-bi-reporting"></a>Power BI-jelentések készítése

A Power BI-jelentések készítése olyan közvetlen, partneri és közvetett vállalati szerződéssel rendelkező ügyfelek számára érhető el, akik hozzáféréssel rendelkeznek a számlázási adatok megtekintéséhez.

### <a name="power-bi-pro"></a>Power BI Pro

A Power BI Pro elérhető az EA-ügyfelek számára. A Power BI Pro segítségével jelentéseket hozhat létre és oszthat meg a költségadatok hatékony kezeléséhez. További együttműködési és adatfrissítési funkciókkal is rendelkezik. A Power BI Pro nagyobb adatkapacitást és adatátviteli korlátokat biztosít.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer. 
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Hozzáférés a Microsoft Azure használati elemzéseihez:

1. Lépjen a [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) területre.
1. Válassza a **Letöltés** elemet.
1. Adja meg a regisztrációs számot és a hónapok számát, majd válassza a **Tovább** elemet.
1. Adja meg API-hozzáférési kulcsát a kapcsolódáshoz. A regisztrációhoz tartozó kulcs az [Enterprise Portalon](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) érhető el.
1. Válassza a **Bejelentkezés** elemet az importálási folyamat automatikus elkezdéséhez.
1. Ha befejeződött, a navigációs panelen megjelenik egy új irányítópult, jelentés és modell. Az importált adatok megtekintéséhez válassza ki az irányítópultot.

> [!TIP]
> 
> - A regisztráció API-kulcsának létrehozásával kapcsolatos további információkért tekintse meg az API-jelentések súgófájlját az [Enterprise Portalon](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - A Power BI Azure-fogyasztással való összekapcsolásáról lásd: [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>A korábbi Power BI EA-tartalomcsomag elérése:

1. Lépjen a [Power BI webhelyére](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Jelentkezzen be egy érvényes munkahelyi vagy iskolai fiókkal.

   A munkahelyi vagy iskolai fiók lehet ugyanaz vagy egy másik fiók, mint amelyet a regisztrációnak az Azure Enterprise Portallal történő eléréséhez használ.
1. A szolgáltatások irányítópultján válassza a **Microsoft Azure Enterprise** elemet, majd válassza a **Csatlakozás** elemet.
1. Az **Azure Enterprise-hoz való kapcsolódás** képernyőjén töltse ki az alábbi mezőket:
    - Az Azure-környezet URL-címe: [https://ea.azure.com](https://ea.azure.com/)
    - Hónapok száma: 1 és 36 között
    - Regisztrációs szám: az Ön regisztrációs száma
1. Kattintson a **Tovább** gombra.
1. A **Hitelesítési kulcs mezőjében** adja meg az API-kulcsot.

    Az API-kulcsot az Azure Enterprise Portalon, a **Használati adatok letöltése** lapon szerezheti be. Válassza az **API hozzáférési kulcsa** elemet, majd másolja és illessze be a kulcsot a **Fiókkulcs** mezőbe.
1. Az adathalmaz méretétől függően az adatok betöltése a Power BI-ba 5–30 percet vesz igénybe.

## <a name="reports-faq"></a>Jelentések – Gyakori kérdések

Ez a szakasz a jelentésekkel kapcsolatos gyakori kérdéseket tárgyalja.

### <a name="why-is-my-cost-showing-as-0"></a>Miért jelenik meg 0 dollár a költségeim értékeként?

**Közvetlen regisztrációval** rendelkező ügyfeleink esetén a vállalati rendszergazdák hozzáférést biztosíthatnak a fióktulajdonosok és a részlegszintű rendszergazdák számára a használati jelentésekhez tartozó költség- és díjszabási információkhoz. Kövesse az alábbi lépéseket:

1. Az Azure Enterprise Portalon válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a kék ceruza szimbólumot a Díjtételek megtekintése a részlegszintű rendszergazda számára elem mellett.
1. Válassza az **Engedélyezve** lehetőséget, majd a mentést.
1. Válassza a kék ceruza elemet a Díjtételek megtekintése a fióktulajdonos számára elem mellett.
1. Válassza az **Engedélyezve** lehetőséget, majd a mentést.

> [!NOTE]
> Ha Ön fióktulajdonos vagy részlegszintű rendszergazda, a díjszabási funkció engedélyezéséhez forduljon a vállalati rendszergazdához.

Ha Ön **közvetett regisztrációval** rendelkező ügyfél, ellenőrizze, hogy partnere engedélyezte-e a díjszabási funkciót. Ezt csak a partner végezheti el. A funkció bekapcsolása után vállalati rendszergazdaként tekintheti meg regisztrációja költségét és díjszabását.

A partnerek, ha engedélyezni szeretnék a díjtételek megtekintését a fióktulajdonosok és a részlegszintű rendszergazdák számára, kövessék a **Közvetlen regisztráció** szakaszban felsorolt lépéseket.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Miért nem találhatók termékváltozat-adatok a használati adatokról szóló jelentésben?

A használati adatokról szóló jelentés nem tartalmaz termékváltozat-adatokat. A jelentés tartalmazza azonban a használati adatokat, így a termékváltozat-adatok beszerzéséhez letöltheti az árlistajelentést.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Miért nem egyezik az Azure Marketplace-en lévő teljes összeg a használati összesítésben szereplő összegzéssel?

Az Azure Marketplace-jelentés csak a használatalapú díjakat jeleníti meg. Az egyszeri díjak nem jelennek meg. A használati adatokat összegző oldalon megtalálja a használatalapú és az egyszeri díjakra vonatkozó legfrissebb használati információkat.

### <a name="why-is-there-no-information-on-my-api-report"></a>Miért nincs információ az API-jelentésről?

Az API-kulcsok érvényessége hathavonta lejár. Ha probléma merül fel, a vállalati rendszergazdának létre kell hoznia egy új API-kulcsot. Kövesse az API-jelentésekkel kapcsolatos gyakori kérdések lépéseit.

### <a name="why-isnt-my-power-bi-report-working"></a>Miért nem működik a Power BI-jelentés?

A Power BI-jal kapcsolatos probléma esetén küldjön egy hibajegyet a [Power BI támogatási csapatának](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Miért nem jelennek meg az erőforráscímkék a jelentéseimben?

Az erőforráscímkék felügyelete az Azure Portalon történik. Az Azure előfizetési csapatával az [Azure Portalon](https://portal.azure.com) veheti fel a kapcsolatot. Kövesse az [Azure-támogatáskérések létrehozása](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) cikkben leírt lépéseket.

### <a name="why-does-my-resource-rate-change-every-day"></a>Miért változik naponta az erőforrás-díjszabás?

A részletes használati jelentésben szereplő erőforrás-díjszabás számított érték. A szolgáltatásra vonatkozó átlagos havi díjat jelöli. Az erőforrás-díjszabás kiszámítása a havi keret és a havi túlhasználati díj átlaga alapján történik a szolgáltatási egység vonatkozásában. A keret terhére elszámolt használat aránya és a túlhasználati díjak a hónap végéig változnak. Ezért az erőforrás-díjszabás is változik a hónap folyamán. Az erőforrás-díjszabás a hónap végét követő ötödik napon zárolódik.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Az erőforrás-díjszabás kiszámítására szolgáló folyamatok szószedete

- **Összes RAW-egység:** A részletes használati jelentésen belül felhasznált mennyiség.
- **MOCP-erőforrás egységenként:** A felsőbb rétegbeli használati rendszer különböző egységekben bocsátja ki az egyes szolgáltatások használatát. (Előre beállított)
- **Egységenkénti felhasználás:** Azure Enterprise-beli mértékegység. (Előre beállított)
- **Ár:** Az Azure Enterprise Portalon lévő egységár.
- **Teljes költség:** A részletes használati jelentésből származó kiterjesztett költség vagy a kerethasználat + túlhasználat az Azure Enterprise Portalról.

### <a name="charges-calculations"></a>Díjszámítások

- **Konvertálás felhasznált MOCP-erőforrásokká** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Konvertálás felhasznált egységekké** = `Consumed MOCP Resources / Consumption per Unit`
- **Összköltség kiszámítása** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>A felhasználás számítási logikája

**Erőforrás-díjszabás** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Az erőforrás díjszabását a díjak alapján állapítjuk meg. Így gyakran nem egyezik meg az árlistán szereplő tényleges egységárral.

A letöltött használatiadat-jelentésen látható, hogy a nyers erőforrás-használat adatai legfeljebb hat tizedesjegy pontossággal jelennek meg. Ezek az adatok a túlhasználati díjak kiszámításához szükségesek. Az Azure Enterprise Portal azonban négy tizedesjegyre kerekíti a használatot a kötelezettségvállalási egységek esetében, a túlhasználati egységek esetében pedig nulla tizedesjegyre rövidít. Az Azure Enterprise Portalon az összes túlhasználati díjat csak a teljes egységek után számítjuk fel. Jelentős különbség észlelhető az egységek ára, valamint a túlhasználatért és a vegyes hónapokban fizetendő erőforrás-használati díj között.

## <a name="next-steps"></a>További lépések

- Az alábbi Excel-fájlok információkat szolgáltatnak az Azure-szolgáltatásokról, és havonta kétszer frissülnek, minden hónap 6. és 20. napján.

   | Cím | Leírás | Fájlnév |
   | --- | --- | --- |
   | [Felhasználóbarát szolgáltatásnevek](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Felsorolja az összes aktív szolgáltatást, és tartalmazza az alábbiakat: <br>  <ul><li>szolgáltatáskategória</li>   <li>felhasználóbarát szolgáltatásnév</li>   <li>kötelezettségvállalási név és cikkszám</li> <li>felhasználási név és cikkszám</li>   <li>mértékegységek</li>   <li>jelentett használat és a megjelenített Enterprise Portal-használat közötti átváltási tényezők</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Szolgáltatásletöltési mezők](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Ez a táblázat felsorolja a használati adatokat tartalmazó letöltött jelentésben szereplő, szolgáltatással kapcsolatos mezők összes lehetséges kombinációját. | Service\_Download\_Fields.xlsx |

- A számla és a díjak értelmezésével kapcsolatos további információkért lásd: [Az Azure Nagyvállalati Szerződés számlájának ismertetése](../understand/review-enterprise-agreement-bill.md).
- Az Azure Enterprise Portal használatának kezdő lépéseit az [Ismerkedés az Azure EA Portallal](ea-portal-get-started.md) című rész írja le.
