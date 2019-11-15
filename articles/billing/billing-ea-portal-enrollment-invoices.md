---
title: Azure nagyvállalati regisztrációs számlák
description: Ez a cikk az Azure Nagyvállalati számlák kezelését ismerteti.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/07/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 6bd70a58552a0217cff82cad10b11783aec64347
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888402"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure nagyvállalati regisztrációs számlák

Ez a cikk az Azure Nagyvállalati számlák kezelését ismerteti. Mindig érdemes ellenőrizni a számla pontosságát. Azokkal a feladatokkal is érdemes megismerkednie, amelyekre szükség lehet a számla kezeléséhez.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Túlhasználati számla rendelésszámának módosítása

Az Azure EA Portal automatikusan létrehoz egy alapértelmezett rendelési (PO-) számot, ha az EA-rendszergazda nem állít be egyet a számlázási dátum előtt. Az EA-rendszergazda az automatikus számlaértesítő e-mail beérkezése utáni hét napban frissítheti a rendelési számot.

Adott fiókra vagy előfizetésre vonatkozóan is megadható rendelési szám. A jelentések alapértelmezés szerint a hierarchiában megadott legalacsonyabb szintű rendelési számot használják, ami azt jelenti, hogy ha előfizetéshez kapcsolódó rendelési szám nem lett megadva, a rendszer a fiókhoz kapcsolódó rendelési számot fogja használni, ha pedig a fiókhoz kapcsolódó rendelési szám nem lett megadva, akkor a részleghez kapcsolódó rendelési számot fogja használni.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Az Azure-szolgáltatások rendelési számának frissítése:

1. Az Azure EA portálon kattintson a **Jelentés**, majd a **Használati adatok összegzése** elemre.
1. Válassza a jobb felső sarokban található, **rendelési számok szerkesztésére** szolgáló lehetőséget.
1. Jelölje be az **Azure-szolgáltatások** választógombot.
1. Válasszon ki egy **számlázási időszakot** a dátumtartományok legördülő menüjéből. A rendelési számok hét napig szerkeszthetők a számlaértesítő vagy a számla kifizetése után, attól függően, melyik következik be előbb.
1. Adja meg az új rendelési számot a **Rendelésszám** mezőben.
1. Kattintson a **Mentés** elemre a módosítás elküldéséhez.

### <a name="to-update-the-marketplace-purchase-order-number"></a>A Marketplace rendelési számának frissítése:

1. Az Azure EA portálon kattintson a **Jelentés**, majd a **Használati adatok összegzése** elemre.
1. Válassza a jobb felső sarokban található, **rendelési számok szerkesztésére** szolgáló lehetőséget.
1. Jelölje be a **Marketplace** választógombot.
1. Válasszon ki egy **számlázási időszakot** a dátumtartományok legördülő menüjéből. A rendelési számok hét napig szerkeszthetők a számlaértesítő vagy a számla kifizetése után, attól függően, melyik következik be előbb.
1. Adja meg az új rendelési számot a **Rendelésszám** mezőben.
1. Kattintson a **Mentés** elemre a módosítás elküldéséhez.

## <a name="cadence-of-azure-ea-billing"></a>Az Azure EA-számlázás ütemezése

### <a name="billing-intervals"></a>Számlázási időközök

A Microsoft a regisztráció hatálybalépésének dátumától kezdve évente számlázza a Microsoft Azure-szolgáltatások kötelezettségvállalás keretében történt vásárlásait, illetve utólagosan a kötelezettségvállalás összegét meghaladó használatot. A kötelezettségvállalás díja havidíj alapján van kiszabva, és előre, évente számlázzuk ki. A keretet meghaladó szolgáltatások díját havonta számítjuk ki, és a számlázási időszak végén számlázzuk.

Attól függően, hogy Ön milyen módon vásárol a kötelezettségvállalás keretében, az éves kötelezettségvállalás a regisztráció évfordulójának dátumával vagy az egyéves módosítási előfizetés hatálybalépésének napjával esik egybe.

A túlhasználatról szóló számlát a regisztráció kezdési dátumától és típusától függően kapja meg.

**Közvetlen regisztrációk 2018. május 1. előtti kezdési dátummal**: A közvetlen nagyvállalati Azure- (EA-) ügyfelek éves számlázási ciklusban kapják meg az Azure-szolgáltatásokról (a Marketplace-szolgáltatások kivételével) szóló számlát. A számlázási ciklus az évforduló dátumán alapul. Az évforduló dátuma az a dátum, amikor a szerződés hatályba lépett. Az Azure-szolgáltatások túlhasználatáról szóló első számla kiállításához meg kell haladnia a pénzügyi kötelezettségvállalás („MC”) küszöbértékének 150%-át.  Amint a teljes szolgáltatáshasználat meghaladja az MC-küszöbérték 150%-át, automatikusan negyedéves számlázási ciklusban kapja meg a számlákat az évforduló dátuma alapján.  Ha nem haladja meg az MC-küszöbérték 150%-át, a regisztráció az egyéves számlázási ciklusban marad, és a túlhasználatról szóló számlát a kötelezettségvállalási év végén kapja meg.

**Közvetlen regisztrációk 2018. május 1. utáni kezdési dátummal**: Az ügyfél Azure-használatát és -díjait külön, havi számlázási ciklusban kiállított számlákon számlázzuk.  Az Azure-ra vonatkozó pénzügyi kötelezettségvállalás által nem fedezett díjak túlhasználati díjként lesznek esedékesek.  

**Közvetett regisztrációk 2018. május 1. előtti kezdési dátummal rendelkező regisztrációval**: A közvetett nagyvállalati Azure- (EA-) ügyfelek negyedéves számlázási ciklusban kapják meg a számlát.  A csatornapartner (CP) közvetlenül az ügyfélnek állítja ki a számlát.  

**Közvetett regisztrációk 2018. május 1. utáni kezdési dátummal**: A 2018. május 1-jei vagy későbbi kezdési dátummal rendelkező közvetett szerződések esetén a számlázás havonta történik.  

### <a name="increasing-commitment"></a>Kötelezettségvállalás növelése

A kötelezettségvállalás bármikor megnövelhető, és az adott év kötelezettségvállalási időszakából fennmaradó hónapok száma után állítjuk ki a számlát. Ha például egyéves módosítási előfizetést regisztrál, és a hatodik hónap során növeli a kötelezettségvállalását, akkor az adott időszak hátralévő hat hónapjára szólóan kap számlát. A kötelezettségvállalásban foglalt mennyiségeket a rendszer ezután frissíti a kötelezettségvállalási időszak utolsó hat hónapjára vonatkozóan a túlhasználati díjak megállapítása céljából.

### <a name="overage"></a>Overage (Kereten túli díjak)

Túlhasználat esetén a kötelezettségvállalását a számlázási időszak során meghaladó használatért vagy foglalásokért kell fizetnie. Ha szeretné megtekinteni az egyes tételekhez kapcsolódó túlhasználati mennyiségek számításának részletezését, tekintse meg a használati adatokat összegző jelentést, vagy forduljon csatornapartneréhez.

A számlán található minden egyes elemnél megjelenik a teljes díj (bővített összeg), a díjak fedezésére szolgáló kötelezettségvállalási összeg (kötelezettségvállalás felhasználása), valamint a kötelezettségvállalást meghaladó díjak összege (nettó összeg).  A vonatkozó adókat csak a kötelezettségvállalást meghaladó nettó összegre számítjuk ki.

A túlhasználat számlázása automatikusan történik.  Az értesítések és a számlák érkezésének ideje az ügyfél számlázási időszakának záródátumától függ.  A túlhasználatról szóló értesítést általában hét nappal az ügyfél számlázási időszakának záródátuma után küldi el a rendszer, mely időtartam során az ügyfelek beléphetnek a portálra, áttekinthetik a díjaikat, és frissíthetik a rendszer által létrehozott rendelési számot (a szám a túlhasználatról szóló számla kiadása előtt is bármikor frissíthető).  A túlhasználatról szóló számlákat 7–9 nappal később küldjük el.

### <a name="azure-marketplace"></a>Azure Piactér

A 2019. áprilisi számlázási ciklustól kezdve az ügyfelek egyetlen Azure-számlát fognak kapni, mert az Azure és az Azure Marketplace díjainak mindegyikét egyetlen számlán egyesítettük, két külön számla kiküldése helyett. (Ez a változás nem érinti az ausztrál, japán vagy szingapúri ügyfeleket.) Az összevont számlára való áttérés során egy részleges Marketplace-számlát fog kapni. Ez az utolsó külön számla a számlázás frissítésének dátuma előtti Marketplace-díjakat tartalmazza. A frissítés dátumát követő Marketplace-díjak az Azure-számlán lesznek láthatók. Az áttérési időszak után az Azure- és Marketplace-díjak mindegyikét az összevont számlán fogja látni.  

### <a name="purchase-order-numbers"></a>Rendelési számok

A rendszer alapértelmezés szerint létrehoz egy rendelési számot a rendeléseknek. A felhasználók az Enterprise Portalra vállalati rendszergazdaként belépve frissíthetik a rendelési számot a Jelentések szakaszban. az ablak jobb felső sarkában található rendelési szám mezőjében a vállalati rendszergazda a Ceruza ikonra kattintva szerkesztheti a rendelés számát.

## <a name="adjust-billing-frequency"></a>Számlázási gyakoriság módosítása

Az ügyfél számlázási gyakorisága lehet éves, negyedéves vagy havi. A számlázási ciklus akkor lesz meghatározva, amikor az ügyfél aláírja a szerződést. A havi számlázás a legkisebb választható számlázási időszak.

A közvetlen regisztrációk számlázási ciklusának évesről negyedévesre történő módosításához a vállalati rendszergazda jóváhagyása szükséges. A közvetett regisztrációk esetén a partnerrendszergazda jóváhagyására van szükség. A módosítás az aktuális számlázási negyedév végén lép életbe.

A számlázási ciklus évesről negyedévesre való megváltoztatásához módosítani kell a szerződést.  A meglévő regisztrációs számlázási ciklus módosításához a vállalati rendszergazda, vagy a szerződésben _Számlázási kapcsolattartóként_ meghatározott személy jóváhagyása szükséges. A jóváhagyást az [Azure EA Portal-támogatás](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) területen nyújthatja be. Ezután válassza ki a **Számlázás és számlakiállítás** hibakategóriát.  A módosítás az aktuális számlázási negyedév végén lép életbe.

Ha aláírt egy M503-as módosítást, bármely szerződés gyakoriságát módosíthatja havi számlázásra. 

## <a name="credits-and-adjustments"></a>Jóváírások és helyesbítések

A regisztrációra alkalmazott jóváírások vagy helyesbítések a [https://ea.azure.com](https://ea.azure.com) webhely **Jelentések** területén láthatók.

A jóváírások megtekintéséhez kövesse az alábbi lépéseket:

1. Válassza a **Jelentések** szakaszt.
1. Kattintson a **Használati adatok összegzése** lehetőségre.
1. A jobb felső sarokban módosítsa az _M_ nézetet _C_ nézetre.
1. Bontsa ki a helyesbítési mezőt az Azure-szolgáltatás kötelezettségvállalási táblájában.
1. Ebben a sorban láthatja a regisztrációra alkalmazott jóváírásokat és azok rövid magyarázatát, például: Szolgáltatói szerződésben szereplő jóváírás.

## <a name="request-an-invoice-copy"></a>Számlamásolat kérése

A számlamásolatok kéréséhez forduljon a partneréhez.

## <a name="overage-offset-by-customers"></a>A többletköltségek ügyfelek általi kiegyenlítése

Ha az ügyfél olyan többletköltségekkel rendelkezik, amelyekhez a pénzügyi keretet szeretné felhasználni, az alábbi feltételeknek kell teljesülniük:

- Az ügyfélnek a kiszámlázott szolgáltatás záró dátumától számított egy éven belüli, felmerült, de ki nem fizetett többletköltségekkel kell rendelkeznie.
- A rendelkezésre álló pénzügyi keret összegének az összes felmerült költséget fedeznie kell, a régebbi ki nem fizetett Azure-számlákat is beleértve.
- A teljesítendő számlázási időszaknak lezártnak kell lennie. A számlázás teljes lezárása minden hónap ötödik napja után történik.
- A kiegyenlíteni kívánt számlázási időszaknak teljesen lezártnak kell lennie.
- Az ACD-kedvezmény a tényleges új kereten alapul, a korábbi felhasználásra szánt összegek levonásával. Ez a követelmény csak a felmerült többletköltségekre vonatkozik. Csak a pénzügyi keretet felhasználó szolgáltatásoknál működik, ezért a Marketplace díjait nem fedezi. A Marketplace díjait külön számlázzuk.
- Ha az ügyfél ki szeretné egyenlíteni a többletköltségeket, megnyithat egy támogatási kérést. A támogatási kérést a fiók ügyfélszolgálata is megnyithatja. A folyamat befejezéséhez szükség van egy e-mailes jóváhagyásra az ügyfél EA-rendszergazdájától vagy a számlázási kapcsolattartótól.

## <a name="view-price-sheet-information"></a>Árlista adatainak megtekintése

A vállalati rendszergazdák megtekinthetik az Azure-szolgáltatások regisztrációihoz társított árlistát.

Az aktuális árlista megtekintése:

1. Az Azure EA Portalon kattintson a **Jelentések**, majd az **Árlista** elemre.
2. Tekintse meg az árlistát, vagy kattintson a **Letöltés** gombra.

![Árlista adatait bemutató példa](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Korábbi árlista letöltése:

1. Az Azure EA Portalon kattintson a **Jelentések**, majd a **Használati adatok letöltése** elemre.
2. Töltse le az árlistát.

![A régebbi árlisták letöltési helyét bemutató példa](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

Ha érdekli, hogy miért van eltérés a díjszabás terén, íme néhány ok:

Előfordulhat, hogy a korábbi regisztráció és az új regisztráció eltérő díjszabással rendelkezik. Az árak azért változnak, mert a díjszabás az adott regisztráció esetében szerződéses jellegű, a szerződés kezdő dátumától egészen a záró dátumáig. Amikor egy regisztrációt átvisznek egy új regisztrációba, az az új szerződés díjszabását követi. A díjszabást az ügyfél árlistája határozza meg. Ezért előfordulhat, hogy az árak magasabbak lesznek az új regisztrációban.

Ha egy regisztrációt meghosszabbítanak, a díjszabás is változni fog. Az árak használatalapú fizetésre módosulnak.

## <a name="request-detailed-usage-information"></a>Részletes használati adatok kérése

A vállalati rendszergazdák az Azure EA Portalon megtekinthetik a használati adataik összegzését, a felhasznált pénzügyi keretet, valamint a további használattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

Az adott fiókok részletes használatának megtekintéséhez lépjen a **Jelentések** > **Használati adatok letöltése** elemre, és töltse le a Használati adatok jelentést. A jelentés nem tartalmazza a vonatkozó adókat. A használat felmerülésének időpontja és a jelentésben való megjelenése között akár nyolc órás késés is lehet.

Közvetett regisztrációk esetén a partnerének engedélyeznie kell az árrés funkciót ahhoz, hogy meg tudja tekinteni a költségekkel kapcsolatos információkat.

## <a name="reports"></a>Jelentések

A vállalati rendszergazdák az Enterprise Portalon megtekinthetik a használati adataik összegzését, a felhasznált pénzügyi keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén fognak megjelenni.

**EA-jelentések**

- Használati adatok összegzése és grafikonok
- Szolgáltatáshasználati jelentés
- Egyenleg és díjak jelentése
- Használati adatok jelentése
- A Marketplace díjainak jelentése
- Árlista
- Speciális jelentés letöltése
- CSV-jelentés formázása

**A használati adatokat összegző jelentések és grafikonok megtekintése**

1. Az Azure EA Portal bal oldali navigációs területén kattintson a **Jelentések** elemre, és tekintse meg a **Használati adatok összegzése** lapot.
1. Válassza ki a kívánt kötelezettségvállalási szintet a bal felső sarokban lévő dátumtartományok legördülő menüjéből.
1. Jelölje ki a kívánt időtartamot vagy hónapot a grafikonon további részletek megtekintéséhez.
1. Tekintse meg a grafikont, amely a havi használatot mutatja használati adatok, a szolgáltatások túlhasználata, a külön számlázott díjak és a Marketplace-költségek megjelenítésével.
1. A kiválasztott hónapra vonatkozóan a grafikon alatt részlegek, fiókok és előfizetések alapján szűrheti az adatokat.
1. Válthat a díjak szolgáltatások és hierarchia szerinti lebontása között.
1. Részletesen megtekintheti az Azure-szolgáltatásokat, a külön számlázott díjakat és az Azure Marketplace díjait.

## <a name="service-usage-report"></a>Szolgáltatáshasználati jelentés

A Szolgáltatáshasználati jelentés oldalon a vállalati rendszergazdák megtekinthetik a szolgáltatáshasználati adataik összegzését. Míg a használat az összes fióknál és előfizetésnél az összegzés szintjén jelenik meg, a jelentést fiókok vagy előfizetések alapján is szűrheti a használat részleteinek megtekintéséhez.

Vegye figyelembe, hogy a használat felmerülésének időpontja és a jelentésben való megjelenése között akár öt nap késés is lehet.

### <a name="to-view-the-report"></a>A jelentés megtekintése:

1. Jelentkezzen be az Enterprise Portalba.
1. Kattintson a bal oldali navigációs területen lévő **Jelentések** elemre.
1. Kattintson a **Használati adatok összegzése** fülre.
1. Kattintson a kívánt dátumtartományra.
1. Válassza ki a megtekinteni kívánt fiókokat vagy előfizetéseket.
1. A különböző lebontások megtekintéséhez váltson a díjak szolgáltatások vagy hierarchia szerinti nézetének megjelenítése között.
1. Megtekintheti a részleteket, beleértve a szolgáltatás nevét, a mértékegységet, a felhasznált egységeket, a tényleges díjat és a kiterjesztett költségeket.

## <a name="download-csv-reports"></a>CSV-jelentések letöltése

A Havi jelentés letöltése oldalon a vállalati rendszergazdák több jelentést tölthetnek le .csv fájlként, beleértve az Egyenleg és díjak jelentését, a Használati adatok jelentését, a Marketplace díjainak jelentését és az Árlistát.

## <a name="to-download-reports"></a>A jelentések letöltéséhez:

1. Az Azure EA Portalon kattintson a **Jelentés** elemre.
1. Válassza ki a **Használati adatok letöltése** elemet a felső menüszalagon.
1. Válassza a **Letöltés** elemet a megfelelő havi jelentés mellett.



## <a name="csv-report-formatting"></a>CSV-jelentés formázása

Az Azure EA Portal CSV-jelentéseit euróban megtekintő ügyfelek formázási problémákba ütközhetnek a vesszők és pontok kapcsán.

A következőket láthatja például:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Óra | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

A következőnek kell megjelennie:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Óra | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

### <a name="root-cause"></a>Gyökérok

Az Excel az összes mezőt „Általános” szövegként importálja, és feltételezi, hogy a számok a következő matematikai szabvány szerint vannak elválasztva: „1,000.00”.  Így az ezresek elválasztásához pontot (.) és a tizedesjegyek elválasztásához vesszőt (,) használó európai pénznemek esetén az „1.000,00” helytelenül fog megjelenni. Ez a területi nyelvi beállításoktól függően változhat.

### <a name="when-importing-the-csv-use-the-following-steps"></a>A CSV importálásakor alkalmazza a következő lépéseket:

1.    Nyissa meg az Excelt, majd lépjen a Fájl > Megnyitás lehetőségre.
1.    Megjelenik a Szöveg importálása varázsló.
1.    Az Eredeti adattípus területen válassza a _tagolt_lehetőséget.  Az alapértelmezett érték a _Rögzített szélesség_.
1.    Kattintson a **Következő** lehetőségre.
1.    Az Elválasztók területen jelölje be a Vessző jelölőnégyzetét. Az alapértelmezett érték a „Tab” (törölje a jelölését).
1.    Kattintson a **Következő** lehetőségre.
1.    Görgessen a ResourceRate és az ExtendedCost oszlopokra.
1.    Válassza ki a ResourceRate oszlopot (fekete színnel lesz kiemelve).
9.    Az Oszlop adatformátuma szakaszban az Általános helyett válassza a Szöveg lehetőséget.  Látni fogja, hogy az oszlopfejléc az Általános értékről a Szöveg értékre változik.
10. Ismételje meg a 8. és a 9. lépést a Kiterjesztett költség oszlop esetében is. Válassza a **Finish** (Befejezés) elemet.

Ha az Excel a \*.csv fájlok automatikus megnyitására van beállítva, akkor ehelyett az Excel Megnyitás funkcióját kell használnia. Nyissa meg az Excelt, és lépjen a Fájl > Megnyitás területre.

## <a name="balance-and-charge-report"></a>Egyenleg és díjak jelentése

Az Egyenleg és díjak jelentése havi összefoglaló információkat nyújt az egyenlegekről, az új vásárlásokról, az Azure Marketplace szolgáltatási díjairól, a kiigazításokról és a többletköltségekről. Az Azure-szolgáltatásokra vonatkozó kötelezettségvállalás összegző táblázatának összes sora statikus marad minden hónapban, míg az összes vásárlás és kiigazítás részletei az Új vásárlások részletei és a Kiigazítások részletei szakaszokban jelennek meg.

### <a name="download-the-balance-and-charge-report"></a>Az egyenleg és díjak jelentésének letöltése

1. Jelentkezzen be az Azure EA Portalra vállalati rendszergazdaként.
1. Kattintson a bal oldali navigációs területen lévő **Jelentések** elemre.
1. Kattintson a **Jelentés letöltése** fülre.
1. Válassza ki a megfelelő hónapot az _Egyenleg és díjak_ oszlopban, és kattintson a jelentés letöltéséhez.

## <a name="usage-detail-report"></a>Használati adatok jelentése

A Használati adatok jelentése havi összefoglalást nyújt a regisztráció által felhasznált szolgáltatásokról és mennyiségekről, beleértve a mérőszámok nevével, a mérőszámok típusával és a felhasznált mennyiségekkel kapcsolatos információkat.

### <a name="download-the-usage-detail-report"></a>A használati adatokról szóló jelentés letöltése

1. Jelentkezzen be az Azure EA Portalra vállalati rendszergazdaként.
1. Kattintson a bal oldali navigációs területen lévő **Jelentések** elemre.
1. Kattintson a **Használati adatok letöltése** fülre.
1. Válassza ki a megfelelő hónapot a _Használati adatok_ oszlopban, és kattintson a jelentés letöltéséhez.

## <a name="marketplace-charges-in-azure-ea-portal-reports"></a>A Marketplace díjai az Azure EA Portal jelentéseiben

A Marketplace díjairól [itt](https://azure.microsoft.com/marketplace/faq/) talál további információt.

A Marketplace díjainak két típusa van:

- **Használatalapú:** Tranzakciós egységekben mért termékek.  A virtuális gépek például óradíjjal járnak, míg a Bing API-keresések a keresések száma alapján vannak felszámítva.
- **Havi díj:** A számlázás havi használat/hozzáférés alapján történik.

A Enterprise Portal különböző díjainak megtekintése:

1. **Használati adatokat összegző jelentés –** A Marketplace használatalapú és havi díjait **egyaránt** megjeleníti.
1. **A Marketplace díjainak jelentése –** **Csak** a használatalapú Marketplace-díjakat jeleníti meg.  Az egyszeri díjak nem jelennek meg.

Nem arról van szó, hogy az Azure Marketplace nem érhető el az MPSA-regisztrációkhoz.

## <a name="advanced-report-download"></a>Speciális jelentés letöltése

Az adott dátumtartományokkal vagy fiókokkal kapcsolatos jelentések elkészítéséhez a speciális jelentések letöltése használható. 2016. augusztus 30-tól a kimeneti fájl formátuma a nagyobb rekordhalmazok befogadása érdekében .xlsx-ről .csv-re változik.

1. Válassza a **Speciális jelentés letöltése** lehetőséget.
1. Válassza ki a **megfelelő dátumtartományt**.
1. Válassza ki a **megfelelő fiókokat**.
1. Válassza a **Használati adatok lekérése** lehetőséget.
1. Válassza a **Frissítés** gombot, amíg a jelentés állapota Letöltés értékűre frissül.
1. Jelentés letöltése.

## <a name="reporting-for-non-enterprise-administrators"></a>Jelentéskészítés nem vállalati rendszergazdák számára

A vállalati rendszergazdák engedélyezhetik a költségek megtekintéséhez való hozzáférést a részlegszintű rendszergazdák (DA) és a fióktulajdonosok (AO) számára egy adott regisztrációban. Az engedélyezés után a fiók tulajdonosa letöltheti a fiókjához és előfizetéséhez tartozó .csv jelentéseket, és megtekintheti az Enterprise Portal jelentési szakaszaiban lévő információkat.

### <a name="to-enable-access"></a>A hozzáférés engedélyezése:

 1. Jelentkezzen be vállalati rendszergazdaként.
 1. Kattintson a **Kezelés** elemre a bal oldali navigációs sávon.
 1. Kattintson a **Regisztráció** fülre.
 1. A Regisztráció részletei szakaszban válassza a következő melletti ceruza ikont:
    - Díjtételek megtekintése a részlegszintű rendszergazda számára
    - Díjtételek megtekintése a fióktulajdonos számára
 1. Válassza az **Engedélyezve** lehetőséget.
 1. Kattintson a **Save** (Mentés) gombra.

### <a name="to-view-reports"></a>Jelentések megtekintése:

1. Jelentkezzen be az Azure EA Portalra részlegszintű rendszergazdaként vagy fióktulajdonosként.
1. Kattintson a bal oldali navigációs területen lévő **Jelentések** elemre.
1. A fiókra és az előfizetésre vonatkozó információk vizuális megjelenítéséhez kattintson a **Használati adatok összegzése** fülre.
1. A .csv kiterjesztésű jelentések megtekintéséhez kattintson a **Használati adatok letöltése** lehetőségre.

A részlegszintű rendszergazdák és a fióktulajdonosok nem tudják megtekinteni a díjakat, ha a _Speciális jelentés letöltése_ funkciót használják. A költségek 0 dollárként jelennek meg.

A Díjtételek megtekintése a fióktulajdonos számára beállítás a fióktulajdonosokra és a társított előfizetéseken engedélyekkel rendelkező összes felhasználóra kiterjed. Ha Ön közvetett ügyfél, a költségfunkciókat a csatornapartnerének kell engedélyeznie.

## <a name="move-usage-data-to-another-enrollment"></a>Használati adatok áthelyezése egy másik regisztrációba

A rendszer csak akkor helyezi át a használati adatokat, ha az átvitel visszakeltezett. Két lehetőség van a használati adatok egyik regisztrációból a másikba való átvitelére:

- Regisztrációk közötti fiókátvitel
- Regisztrációk közötti regisztrációátvitel

Mindkét megoldásnál küldenie kell egy [támogatási kérést](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) az EA támogatási csapatnak. 

## <a name="azure-ea-pricing-overview"></a>Az Azure EA díjszabásának áttekintése

Ez a dokumentum részletesen ismerteti a használat kiszámításának módját, és válaszokat ad a nagyvállalati programokban szereplő különböző Azure-szolgáltatások díjszabásával kapcsolatos gyakran feltett kérdésekre, amelyek esetében a számítások összetettebbek.

### <a name="price-protection"></a>Árvédelem

Az ügyfelekre és a csatornapartnerekre (közvetett csatornák esetén) garantáltan az ügyfél árlistáján (CPS) szereplő vagy annál alacsonyabb mértékű, illetve az Azure-vásárlás dátumakor érvényes árak érvényesek. Ezt az árat alapdíjnak nevezzük. A vásárlást követően bevezetett szolgáltatások esetében ez a szolgáltatás első bevezetésekor érvényes, a megfelelő mértékű árengedménnyel ellátott árnak megfelelő díj. Ez az árvédelem a kötelezettségvállalás időtartamára vonatkozik, amely a nagyvállalati programtól függően egy vagy három év lehet.

### <a name="price-changes"></a>Árváltozások

A Microsoft a regisztráció ideje alatt csökkentheti az egyes Azure-szolgáltatások aktuális nagyvállalati programjának díját.  Ezt a kedvezményes díjszabást kiterjesztjük a meglévő ügyfelekre, amíg az alacsonyabb díjszabás érvényes.  Ha később ezek a díjak növekednek, akkor a szolgáltatás regisztrációs díja nem növekszik az ügyfél fent meghatározott felső árvédelmi határán túl, de a korábbi csökkentett árakhoz képest növekedhet.  A Microsoft mindkét esetben tájékoztatja az ügyfeleket és a közvetett csatornapartnereket a közelgő árváltozásokról, amelyekről e-mailt küld a regisztrációhoz társított vállalatnak és partnerrendszergazdáknak.

### <a name="current-effective-pricing"></a>Jelenlegi érvényes díjszabás

Az ügyfelek és a csatornapartnerek a regisztrációik aktuális díjszabását az [Azure Enterprise Portalra](https://ea.azure.com/) belépve tekinthetik meg az adott regisztrációhoz tartozó Árlista oldalon.  Ha az Azure-t közvetetten, az egyik csatornapartnerünkön keresztül vásárolta meg, akkor a csatornapartnertől kell beszereznie a díjszabás frissítéseit, ha a regisztrációhoz nem engedélyezték az árrésalapú díjszabás megjelenítését.

### <a name="introduction-of-new-azure-services"></a>Új Azure-szolgáltatások bevezetése

Folyamatosan fejlesztjük az Azure-t, így időnként olyan új szolgáltatásokat adunk hozzá, amelyek díjszabása a meglévő szolgáltatásoktól eltérő.  Néhány előzetes verziójú szolgáltatás automatikusan elérhető, míg másokhoz az ügyfél beavatkozására van szükség az [Azure Account Portalon](https://account.windowsazure.com/PreviewFeatures). Vegye figyelembe, hogy a szolgáltatások az előzetes verzióról az általánosan elérhető verzióra való áttérésével az árak a teljes körű teljesítmény és a megbízhatósági SLA-k alkalmazásával növekedhetnek. Végül egyes szolgáltatások promóciós díjszabással rendelkezhetnek az első bevezetéskor, ami később növekedhet. Az előzetes verzióról vagy a promóciós díjszabásról az általános elérhetőségre való áttéréssel járó növekedéseket a normál alapdíjvédelem nem korlátozza, így a jövőben érvényesek lesznek ezen szolgáltatások használatára. Az ügyfelek elkerülhetik az ezekkel a szolgáltatásokkal kapcsolatos díjakat, ha nem használják az új szolgáltatást.

### <a name="introduction-of-regional-pricing"></a>A regionális díjszabás bemutatása

Az új szolgáltatások bevezetésén túl a szolgáltatások időnként az egységes globális modellről regionálisabb modellekre váltanak, ahogy ezen szolgáltatások regionális támogatása növekszik. Egy szolgáltatás regionalizálásának első bevezetésekor az árvédelem ugyanúgy érvényes az új régiókra, ahogy a regisztrációra vonatkozó addigi globális árra. Az ugyanahhoz a szolgáltatáshoz később bevezetett további régiók azonban új szolgáltatásoknak tekintendők, így a saját egyéni díjszabásuk alapján érhetők el, az alapdíjvédelemtől függetlenül.

### <a name="enterprise-msdn-devtest"></a>Enterprise MSDN Dev/Test

A vállalati rendszergazdák engedélyezhetik, hogy a fióktulajdonosok az EA MSDN Dev/Test ajánlat alapján hozzanak létre előfizetéseket. Ahhoz, hogy ez megfelelően működjön, a fióktulajdonosnak be kell állítania a mögöttes MSDN-előfizetők számára szükséges EA MSDN Dev/Test-előfizetéseket. Ez lehetővé teszi, hogy az aktív MSDN-előfizetők speciális fejlesztési és tesztelési díjszabás mellett futtassanak fejlesztési és tesztelési számítási feladatokat az Azure-ban. További információ: [EA MSDN Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

### <a name="enterprise-program-usage-calculation-guidelines"></a>A nagyvállalati program használatának számítási útmutatója

Az egyes szolgáltatások alapvető nyilvános díjszabási információit, mértékegységeit, gyakori kérdéseit és használati jelentéskészítési útmutatóit az [Azure-szolgáltatások](https://azure.microsoft.com/services/) és [Az Azure díjszabása](https://azure.microsoft.com/pricing/) című szakaszokban találja. Emellett a szolgáltatás használatának kiszámításakor a következő, nagyvállalati programokra vonatkozó útmutatókat kell használni.

### <a name="enterprise-program-units-of-measure"></a>Nagyvállalati program mértékegységei

A vállalati programok mértékegységei gyakran eltérnek a többi programban, például a Microsoft Online Services szerződés programban (MOSA) látottakkal. Ez azt jelenti, hogy néhány szolgáltatás esetében a rendszer összesíti a mértékegységeket egy normalizált díjszabás biztosítása érdekében. Az Enterprise Portal Használati adatok összegzése nézetében látható mértékegység mindig a nagyvállalati mértékegység. Az egyes szolgáltatásokhoz tartozó aktuális mértékegységek és átváltások teljes listáját a [felhasználóbarát szolgáltatásneveket](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) tartalmazó szakaszban találja.

### <a name="rounding-rules"></a>Kerekítési szabályok

A Enterprise Portalon végzett kerekítés az IEEE szabvány szerinti banki kerekítés vagy Gauss-kerekítés logikáját használja. Ez a félszámjegyes értékeket a legközelebbi páros számjegyre kerekíti, míg a hagyományosabb felfelé kerekítő kerekítési függvények a fél számjegyeket a következő legmagasabb számjegyre kerekítik. Ez a módszer pontosabb összeget nyújt a csoportra vonatkozóan a normál Excel-logikához képest.

Ha az első számjegy 5, és nincs következő számjegy, vagy a következő számjegyek nullák, akkor az előző számjegy páros lesz (azaz a legközelebbi páros számjegyre van kerekítve). Például a 2,315 és a 2,325 egyaránt 2,32 lesz, ha a legközelebbi 100-ra van kerekítve.

Referenciaképpen, ha az Excellel modellezi az Enterprise Portalon használt kerekítési és átalakítási szabályokat, az MROUND képleteket kell használnia az alábbiakban látható módon.

| Forgatókönyv | Bankár logikai képlet |
| --- | --- |
| Kerekítés használata | = MROUND({_forrás_}, 0,0002) |
| Kerekítési díjszabás (2 tizedesjegy) | = MROUND({_forrás_}, 0,02) |
| Kerekítési díjszabás (0 tizedesjegy) | = MROUND({_forrás_}, 2) |

**2.**  **táblázat**  **– Cloud Service és a virtuális gépek óráinak átalakítása**

### <a name="conversion-between-download-usage-file-and-usage-summary-portal-view"></a>A Használati adatokat tartalmazó letöltött fájl és a Használati adatok összegzése portálnézet közötti átalakítás

A nyers erőforrás-használat adatai legfeljebb hat tizedesjegy pontossággal jelennek meg, ahogy ez a letöltött használatiadat-jelentésen látható. Az Azure Enterprise Portal azonban négy tizedesjegyre kerekíti a használatot a kötelezettségvállalási egységek esetében, a túlhasználati egységek esetében pedig nulla tizedesjegyre rövidít. A nagyvállalati egységekre való átalakítás előtt a rendszer először négy számjegyre kerekíti a nyers használatot, majd az eredményül kapott nagyvállalati egységeket ismét négy számjegyre kerekíti. Az átalakítás előtt ténylegesen felhasznált órák csak a használati adatokat tartalmazó letöltött jelentésben jelennek meg, magán a felhasználói felületen nem.

Tegyük fel például, hogy 694,533404 tényleges SQL Server-óra szerepel a használati adatokat tartalmazó letöltött jelentésben. Ezeket az egységeket a rendszer ezután 6,94533404 egységére alakítja át 100 üzemórára vonatkozóan, amelyet aztán 6,9453-ra kerekítve jeleníti meg az Enterprise Portalon.

Ezeket az egységeket a rendszer ezután egyszerűen megszorozza a kötelezettségvállalási egység árával, és az eredményt két tizedesre csonkolja a kiterjesztett számlázási mennyiség meghatározása érdekében. A japán yen (JPY) és a koreai won (KRW) esetében a kiterjesztett mennyiség nulla tizedesjegyre van kerekítve.

Ugyanebben a példában a túlhasználati díjak esetén a számlázható egységek hatra lennének csonkolva, majd meg lennének szorozva a túlhasználati díjak egységárával a kiterjesztett számlázási mennyiség meghatározása érdekében. Felügyelt szolgáltatás szolgáltatójának (MSP) számlázása esetén az MSP-ként megjelölt részleghez tartozó összes használat nulla tizedesjegyre van csonkolva az EA mértékegységre való átalakítás után, ahogy a túlhasználati díjak jelentésénél is. Ennek eredményeképpen a használat összege alacsonyabb lehet, mint a felhasználói felületen jelentett összes használat összege, attól függően, hogy az MSP még a pénzügyi keretének egyenlegén belül vagy már a kereten túl van.

### <a name="graduated-pricing"></a>Sávos díjszabás

A nagyvállalati program díjszabása jelenleg nem tartalmaz sávos díjszabást (ahol az egységenkénti díjszabás a használat növekedésével csökken). Amikor sávos díjszabással rendelkező MOSA-programból vált nagyvállalati programra, a díjszabás megállapítása a szolgáltatás alapszintje szerint történik, a nagyvállalati program kedvezményeire (ha vannak) vonatkozó kiigazítás után.

### <a name="partial-hour-billing"></a>Nem teljes órák számlázása

A teljes órában számolt növekmények helyett az összes használat részleges órákká alakított percek szerint van számlázva.  Az óránkéntiként feltüntetett nagyvállalati díjak egyszerűen a teljes órákra vannak alkalmazva, beleértve a részleges órákat is.

### <a name="average-daily-consumption"></a>Átlagos napi felhasználás

Ha egy szolgáltatás havi díjjal rendelkezik, de a használatról napi rendszerességgel készül jelentés, akkor a rendszer ezt a használatot naponta egyszer kiértékeli, elosztja 31-gyel, és a számlázási hónap napjainak száma alapján összesíti. Ez olyan díjszabást eredményez, amely egy hónapban sem haladja meg a várt értéket, és a 31 napnál rövidebb hónapokban kissé alacsonyabb is.

### <a name="compute-hours-conversion"></a>Üzemórák átalakítása

Korábban minden A0, A2, A3 és A4 Standard és alapszintű virtuális gép és felhőszolgáltatás használatát az A1 percenkénti egységeinek törtrészeként (A0) vagy többszöröseként (A2, A3 és A4) bocsátottuk rendelkezésre. Ez összezavarta a vásárlókat, ezért a külön percalapú A0, A2, A3 és A4 mérőszámok használatára kezdünk áttérni.

Az új mérés a 2016. január 27. és január 28. között lép érvénybe. Az áttérési időszak alatt az üzemelő példányhoz tartozó letöltött .csv-ben a következő két sorelem jelenik meg: Egy az A1 mérőszám alapján kibocsátott használathoz, egy másik pedig az üzemelő példány méretének megfelelő új, dedikált mérőszám alapján kibocsátott használathoz.

| **Üzemelő példány mérete** | **Az A1 többszöröseként kibocsátott használat 2016. január 26-ig** | **Dedikált mérőszám alapján kibocsátott használat 2016. január 27-től** |
| --- | --- | --- |
| A0 | Az A1 óraszám 0,25-szöröse | Az A0 óraszám 1-szerese |
| A2 | Az A1 óraszám 2-szerese | Az A2 óraszám 1-szerese |
| A3 | Az A1 óraszám 4-szerese | Az A3 óraszám 1-szerese |
| A4 | Az A1 óraszám 8-szorosa | Az A4 óraszám 1-szerese |

### <a name="regions"></a>Régiók

Azon szolgáltatások esetében, ahol a zóna és a régió befolyásolja a díjszabást, az alábbi táblázatban láthatók a földrajzi térségek és régiók leképezése:

| Térség | Adatátviteli régiók | CDN-régiók |
| --- | --- | --- |
| 1\. zóna | Észak-Európa <br> Nyugat-Európa <br> USA nyugati régiója <br> USA keleti régiója <br> USA északi középső régiója <br> USA déli középső régiója <br> USA 2. keleti régiója <br> USA középső régiója | Észak-Amerika <br> Európa |
| 2\. zóna | Ázsia és a csendes-óceáni térség keleti régiója <br> Ázsia és a csendes-óceáni térség délkeleti régiója <br> Kelet-Japán <br> Nyugat-Japán <br> Kelet-Ausztrália <br> Délkelet-Ausztrália | Ázsia és a Csendes-óceáni térség <br> Japán <br> Latin-Amerika <br> Közel-Kelet / Afrika <br> Kelet-Ausztrália <br> Délkelet-Ausztrália |
| 3\. zóna | Dél-Brazília |   |

**4.**  **táblázat**  **– Adatátviteli régiók**

A szolgáltatások (például az O365 és az Azure) közötti, ugyanazon az adatközponton belüli adatforgalomért nem számítunk fel díjat.

### <a name="monetary-commitment-and-unbilled-usage"></a>Pénzügyi keret és a ki nem számlázott használat

Az Azure pénzügyi keret az az összeg, amelyet előre kifizetnek az Azure-szolgáltatásokért. A szolgáltatások használata felhasználja a pénzügyi keretet. A belső Azure-szolgáltatások a pénzügyi keretet használják. Ez alól azonban kivételt képeznek a külön számlázott díjak és a Marketplace-szolgáltatások.

### <a name="charges-billed-separately"></a>Külön számlázott tételek

Néhány külső féltől származó termék és szolgáltatás nem használja az Azure-beli pénzügyi keretet. Ehelyett az ilyen tételek esetében a számlázás külön történik a standard számlázási ciklus túlhasználati díjának részeként.

Az összes Azure- és Marketplace-díjat egyetlen, a regisztráció számlázási ciklusával összhangban álló számlán egyesítettük. (Ez nem vonatkozik az ausztrál, japán vagy szingapúri ügyfelekre.)

Az összevont számlán először az Azure-használat, majd az esetleges Marketplace-díjak jelennek meg. Az ausztrál, japán vagy szingapúri ügyfelek továbbra is külön számlán tekinthetik meg a Marketplace-díjakat.

Ha a standard számlázási ciklus végén nincs túlhasználat, a külön számlázott díjak külön számlán jelennek meg. (Az ausztrál, japán és szingapúri ügyfelekre vonatkozik.)

**A külön számlázott szolgáltatások a következők:**

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

A közvetlen ügyfelek esetében a Marketplace-díjak az Azure EA Portalon láthatók. A Marketplace-vásárlások és felhasználás számlázása a pénzügyi kereten kívül, negyedévente, havonta és utólagosan történik.

A közvetett ügyfelek megtalálhatják Azure Marketplace-előfizetéseiket az Azure EA Portal Előfizetések kezelése lapján, de a díjszabás rejtve marad. A Marketplace díjaival kapcsolatos információkért az ügyfeleknek licencmegoldás-szolgáltatójukhoz kell fordulniuk.

Az új havi vagy évente ismétlődő Marketplace-vásárlások esetében a teljes összeg lesz kiszámlázva arra az időszakra, amelyben a Marketplace-tételeket megvásárolták. Ezek a tételek a következő időszakban automatikusan megújulnak az eredeti vásárlás napján.

Ez nem befolyásolja a már meglévő ismétlődő Marketplace-díjakat. A havonta ismétlődő díjak megújítására továbbra is az egyes naptári hónapok első napján kerül sor, az éves díjak megújítása pedig a vásárlási dátum évfordulóján történik.

Az Azure Marketplace-en elérhető külső viszonteladói szolgáltatások némelyike mostantól a nagyvállalati szerződés (EA) pénzügyi keretét használja. Ezeknek a szolgáltatásoknak a számlázása korábban az EA pénzügyi keretén kívül, külön számlákkal történt. A szolgáltatásokra a Marketplace-en érvényes EA pénzügyi keret hozzájárul az ügyfelek vásárlás- és fizetéskezelésének egyszerűsítéséhez. A pénzügyi keretet használó szolgáltatások teljes listája az [Azure-webhelyen](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/?WT.mc_id=azurebg_email_Trans_33771_1695_Release_Direct_Tier2_March14) érhető el.

### <a name="partners"></a>Partnerek

A licencmegoldás-szolgáltatók Marketplace-specifikus árlistát tölthetnek le, ha megkeresik az árlistát az Azure EA Portalon, majd a jobb felső sarokban lévő **Marketplace-árlista** hivatkozására kattintanak. A Marketplace-árlistában az összes rendelkezésre álló szolgáltatás és azok ára is megjelenik.

**A árlista letöltéséhez kövesse az alábbi lépéseket:**

1. Lépjen a Jelentések > Árlista ponthoz.
1. A jobb felső sarokban, a felhasználóneve alatt keresse meg az Azure Marketplace-árlistára mutató hivatkozást.
1. Kattintson a jobb egérgombbal a hivatkozásra, majd válassza a **Cél mentése másként** lehetőséget.
1. A Mentés ablakban módosítsa a dokumentum címét a következőre: _AzureMarketplacePricelist.zip_. Ezáltal a fájl formátuma xlsx helyett zip lesz.
1. Ha a letöltés befejeződött, az országspecifikus árlisták egy zip-fájlban lesznek elérhetők.
1. A licencmegoldás-szolgáltatóknak az egyes országok fájljaira kell hivatkozniuk az országspecifikus díjszabás megállapításához. A licencmegoldás-szolgáltatók az Értesítések lapon találják azokat a termékváltozatokat, amelyek teljesen újak a Marketplace-en, illetve amelyek el lettek távolítva.
1. Árváltozás ritkán fordul elő, de ha mégis megtörténik, a licencmegoldás-szolgáltatók harminc nappal korábban e-mailben kapnak értesítést az áremelésekről és a valutaárfolyamok módosulásáról.
1. A licencmegoldás-szolgáltatók regisztrációnként, független szoftvergyártók szerint, illetve negyedévente egy számlát kapnak.

### <a name="enabling-marketplace-purchases"></a>A Marketplace-vásárlások engedélyezése

A vállalati rendszergazdák engedélyezhetik vagy letilthatják a Marketplace-vásárlásokat az adott regisztrációhoz tartozó összes Azure-előfizetésre vonatkozóan. Ha a vállalati rendszergazda letiltja a vásárlásokat, és vannak olyan Azure-előfizetések, amelyek már rendelkeznek Marketplace-előfizetésekkel, az intézkedés ezeket a Marketplace-előfizetéseket nem érinti.

Bár az ügyfelek átalakíthatják közvetlen Azure-előfizetéseiket EA-előfizetéssé, ha az Azure EA Portalon lévő regisztrációjukhoz társítják őket, a művelet nem konvertálja automatikusan az alárendelt Marketplace-előfizetéseket.

**A Marketplace-vásárlások engedélyezése:**

1. Jelentkezzen be az Azure EA Portalra vállalati rendszergazdaként.
1. Keresse meg a _Kezelés_ területet.
1. A _Regisztráció részletei_ szakaszban kattintson az _Azure Marketplace_ vonalelem mellett található ceruzaikonra.
1. Válassza igény szerint az _Engedélyezett/Letiltott_ vagy az ingyenes _Csak BYOL-termékváltozat\*_ lehetőséget.
1. Kattintson a **Save** (Mentés) gombra.

### <a name="marketplace-charges-in-azure-ea-portal-reports"></a>A Marketplace díjai az Azure EA Portal jelentéseiben

A Marketplace díjairól [itt](https://azure.microsoft.com/marketplace/faq/) talál további információt.

A Marketplace díjainak két típusa van:

- **Használatalapú:** A termékek mérése tranzakciós egységekben történik.  A virtuális gépek például óradíjjal járnak, míg a Bing API-keresések a keresések száma alapján vannak felszámítva.
- **Nem használatalapú:** Egyszeri díj vagy ismétlődő havi díj, amely független a használattól.

A használatalapú és a nem használatalapú díjak egyaránt szerepelnek majd a Marketplace díjjelentésében.

Vegye figyelembe, hogy az Azure Marketplace MPSA-regisztráció esetében nem érhető el.

\*A BYOL (saját licenc használata) és a Csak ingyenes lehetőség az Azure Marketplace-vásárlásokat és -beszerzéseket a BYOL- és az ingyenes termékváltozatokra korlátozná.

### <a name="services-billed-hourly-for-ea"></a>Az EA esetében óradíj alapján számlázott szolgáltatások

Az alkalmazásszolgáltatási hálózat és a webalkalmazási tűzfal számlázása az EA esetében óradíj alapján történik, és nem havi díjszabás szerint, mint a Microsoft Online Előfizetési Program (MOSP) esetében.

### <a name="remote-app"></a>Távoli alkalmazás

Az EA-ügyfelek a nagyvállalati szerződés díja alapján fizetnek a távoli alkalmazásért, és további díjakkal nem kell számolniuk. A standard ár a kezdeti 40 órát tartalmazza, míg a korlátlan díjszabás a kezdeti 80 órát fedezi. A távoli alkalmazás a 80 órán túl nem bocsátja ki a használatot.

## <a name="azure-marketplace-faq"></a>Azure Marketplace – Gyakori kérdések

Ez a GYIK-dokumentum az Azure-beli pénzügyi keret Azure Marketplace-en található, külső fél által közzétett szolgáltatásokra való alkalmazhatóságának frissítéseit tekinti át.

### <a name="what-are-we-changing-with-respect-to-marketplace-services-and-azure-monetary-commitment"></a>Mi változik a Marketplace-szolgáltatások és az Azure-beli pénzügyi keret tekintetében?

A fenntartott Azure virtuálisgép-példányok (RI-k) mellett az ügyfelek külön számlát kapnak az Azure Marketplace-en vásárolt összes szolgáltatáshoz. Kiterjesztjük az Azure-beli pénzügyi keret felhasználhatóságát, hogy az ügyfeleink által leggyakrabban vásárolt, külső féltől származó Azure Marketplace-szolgáltatásokat is lefedje.

### <a name="why-are-we-making-this-change"></a>Miért vezetjük be ezt a változtatást?

Az ügyfelek folyamatosan további lehetőségeket keresnek az Azure-beli pénzügyi keret formájában elvégzett kezdeti befizetés kihasználása érdekében.  Egy gyakran felmerülő vásárlói kérésre adunk választ, illetve Azure Marketplace-ügyfeleink jelentős részének kedvezünk azáltal, hogy a pénzügyi keretet kiterjesztjük ezekre a szolgáltatásokra.

### <a name="what-is-the-customer-benefit"></a>Milyen előnyökkel jár ez az ügyfél számára?

Egyszerűbb lesz a számlázász az ügyfelek számára, és biztosabbá válik az Azure-beli pénzügyi keret teljes kihasználása.  Ha hozzávesszük ezt az előnyt az előre fizetett pénzügyi kerethez és a fenntartott példányokhoz, a pénzügyi keret felhasználásával még többet lehet kihozni az Azure-beli pénzügyi keretből.

### <a name="what-services-will-deduct-from-azure-monetary-commitment-and-how-will-my-customer-know"></a>Milyen szolgáltatásokat lehet igénybe venni az Azure-beli pénzügyi keret terhére, és honnan fogja ezt tudni az ügyfelem?

Az Azure Marketplace vásárlási folyamata során jogi nyilatkozattal különböztetünk meg minden olyan szolgáltatást, amely a pénzügyi keretet használja. A jelenleg támogatottak közé a Red Hat, a SUSE, az Autodesk és az Oracle által közzétett egyes szolgáltatások tartoznak. A hasonló elnevezési konvenciókkal rendelkező, de a fentiekben nem említett felek által közzétett szolgáltatások nem számolhatók el a pénzügyi keret terhére. A GYIK végén a teljes lista elérhető.

### <a name="what-if-my-customer-runs-out-of-monetary-commitment"></a>Mi a teendő, ha az ügyfelem kimeríti a pénzügyi keretét?

Azon ügyfelek esetében, akik felhasználták a teljes pénzügyi keretet, és jelenleg többlethasználatot folytatnak, az ezen szolgáltatásokkal kapcsolatos díjak a soron következő túlhasználati számlán jelennek meg, a többi használatalapú szolgáltatással együtt.  Ez egy változás az eddigiekhez képest, mivel korábban ezek a díjak a saját külön számlájukon jelentek volna meg más Azure Marketplace-ajánlatokkal együtt.

### <a name="why-are-we-not-enabling-azure-monetary-commitment-for-all-marketplace-purchases"></a>Miért nem engedélyezzük az Azure-beli pénzügyi keretet az összes Marketplace-vásárláshoz?

Sokat dolgozunk azon, hogy az Azure-beli pénzügyi kerethez kapcsolódóan a legjobb felhasználói élményt nyújthassuk. Ez a változás számos ügyfelet érint, és az Azure Marketplace-beli teljes költés jelentős részére hatással van. A későbbiekben további szolgáltatásokkal is bővülhet a kör.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Hogyan befolyásolja ez a közvetett regisztrációt és partnereket?

Mindennek nincs hatása a közvetett regisztrációs ügyfeleinkre és partnereinkre. Ezekre a szolgáltatásokra ugyanazok a partneri árréslehetőségek vonatkoznak, mint más használatalapú szolgáltatások esetében. Az egyetlen változás az a számla, amelyen megjelennek, illetve a díjak pénzügyi keret alapján történő fizetése.

### <a name="list-of-services-that-will-deduct-from-azure-monetary-commitment"></a>Az Azure-beli pénzügyi keret terhére igénybe vehető szolgáltatások listája

Bizonyos Azure Marketplace-ajánlatok esetében a pénzügyi keret összegei is felhasználhatók. A programban részt vevő termékek teljes listájáért tekintse meg az [Azure-beli pénzügyi keretet](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment).

## <a name="additional-information"></a>További információ

További részletekért tekintse meg ezeket a kiegészítő forrásanyagokat. Ezek a fájlok havonta kétszer frissülnek, minden hónap 6. és 20. napján. Az egyes fájlok részletei a következők:

| Függelék címe | Leírás | URL-elnevezési konvenciók |
| --- | --- | --- |
| [**Felhasználóbarát szolgáltatásnevek**](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Felsorolja az összes aktív szolgáltatást a szolgáltatáskategória, a felhasználóbarát szolgáltatásnév, a kötelezettségvállalási név és a cikkszám, a felhasználási név és a cikkszám, a mértékegység, valamint a jelentett használat és a megjelenített Enterprise Portal-használat közötti átváltási tényezők belefoglalásával. | Friendly\_Service\_Names.xlsx |
| [**Szolgáltatásletöltési mezők**](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Ez a táblázat felsorolja a használati adatokat tartalmazó letöltött jelentésben szereplő, szolgáltatással kapcsolatos mezők összes lehetséges kombinációját. | Service\_Download\_Fields.xlsx |

**5.**  **táblázat**  **– További forrásanyagok**

## <a name="power-bi-reporting"></a>Power BI-jelentések készítése

### <a name="power-bi-pro"></a>Power BI Pro

A Power BI Pro mostantól elérhető az EA-ügyfelek számára. A Power BI Pro segítségével létrehozhat és megoszthat jelentéseket a költségadatok hatékony kezeléséhez, további együttműködési és adatfrissítési funkciókkal. A Power BI Pro nagyobb adatkapacitást és adatátviteli korlátokat biztosít. Hamarosan elérhetővé válnak az Azure nagyvállalati ügyfeleinek készült izgalmas, új költségkezelési szolgáltatások is.

A Power BI ingyenes változatának jelenlegi, a Microsoft Azure Consumption Insights tartalomcsomaggal rendelkező felhasználói a Power BI Pro 60 napos ingyenes próbaidőszakára jogosultak. Ha az ingyenes próbaidőszak után továbbra is használni szeretné a Power BI Pro szolgáltatást, ezt a megfelelő licenc hozzáadásával megteheti.

Az ingyenes próbaverzióra való regisztrációhoz lépjen a fogaskerék ikonra, és válassza a **Személyes tárhely kezelése** lehetőséget. Itt kattintson a **Pro kiadás ingyenes kipróbálása** lehetőségre a jobb oldalon. A Power BI Pro ingyenes próbaverziójával kapcsolatos további információ a [Power BI önkiszolgáló regisztrációs oldalán](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) érhetők el.

### <a name="microsoft-azure-ea-power-bi-pro-trial-terms"></a>A Microsoft Azure EA Power BI Pro próbaverziójának feltételei

- **Általános cél**: A „Microsoft Azure Enterprise” tartalomcsomag próbaverziós ajánlatának (az „Ajánlat”) kiterjesztett Power BI Pro szolgáltatása az Ajánlat időtartama alatt elérhető a meglévő és arra jogosult felhasználók számára, hogy hozzáférhessenek a Microsoft Azure használatával kapcsolatos elemzésekhez egy adott Power BI tartalomcsomag használata révén.
- **Jogosultság**: A Nagyvállalati Szerződés (EA) alá tartozó felhasználók igénybe vehetik az Ajánlatot, ha a szervezetük Microsoft Azure-használatával kapcsolatos számlázási, szolgáltatási, szolgáltatás-és/vagy költségkezelési funkcióval rendelkeznek.
- **Kizárások**:
  - A Power BI Pro kiterjesztett próbaverziójának felhasználói esetében továbbra is a már meglévő ajánlat marad érvényben, így ők nem vehetik igénybe az Azure EA Power BI Pro próbaverziójának ajánlatát.
  - Az ajánlat felhasználói csak a Microsoft Azure Enterprise tartalomcsomaggal használhatják a Power BI Pro szolgáltatást. A Power BI Pro bármely egyéb használata tilos.
  - Időtartam: Az Ajánlat 2017. június 1-től 2018. május 31-ig érvényes.  Az elfogadás a 12 hónapos időszak alatt bármikor megtörténhet, bár az ajánlat 2018. május 31-én megszűnik az összes felhasználó számára, függetlenül attól, hogy mikor fogadták el az Ajánlatot.

### <a name="to-access-the-microsoft-azure-consumption-insights-content-pack"></a>A Microsoft Azure Consumption Insights tartalomcsomag eléréséhez a következőket kell tennie:

1. Lépjen a [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) területre. Kattintson a **Letöltés** elemre.
1. Adja meg a regisztrációs számot és a hónapok számát. Kattintson a **Tovább** gombra.
1. Adja meg API-hozzáférési kulcsát a kapcsolódáshoz. A regisztrációhoz tartozó kulcs az [Enterprise Portalon](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) érhető el. Kattintson a **Bejelentkezés** elemre.
1. Az importálási folyamat automatikusan elindul. Ha befejeződött, a navigációs panelen megjelenik egy új irányítópult, jelentés és modell. Kattintson az irányítópultra az importált adatok megtekintéséhez.

A regisztráció API-kulcsának létrehozásával kapcsolatos további információért tekintse meg az API-jelentések súgófájlját az [Enterprise Portalon](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26). Az új tartalomcsomaggal kapcsolatos további információért töltse le a [Microsoft Azure Consumption Insights](https://automaticbillingspec.blob.core.windows.net/spec/Microsoft%20Azure%20Consumption%20Insights.docx?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26) dokumentumot.

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>A korábbi Power BI EA-tartalomcsomag elérése:

 1. Lépjen a [Power BI webhelyére](https://app.powerbi.com/getdata/services/azure-enterprise).
 1. Jelentkezzen be egy érvényes munkahelyi vagy iskolai fiókkal.
    - A munkahelyi vagy iskolai fiók lehet ugyanaz vagy egy másik fiók, mint amelyet a regisztrációnak az Azure EA Portallal történő eléréséhez használ.
 1. A szolgáltatások irányítópultján válassza a **Microsoft Azure Enterprise** elemet, majd kattintson a **Csatlakozás** elemre.
 1. Az Azure Enterprise-hoz való kapcsolódás képernyőjén válassza a következőt:
    - Az Azure-környezet URL-címe: [https://ea.azure.com](https://ea.azure.com/).
    - Hónapok száma: válasszon 1 és 36 között.
    - Regisztrációs szám: adja meg a regisztrációs számot.
    - Kattintson a **Tovább** gombra.
 1. A hitelesítési kulcs mezőjében adja meg az API-kulcsot. Az API-kulcsot itt szerezheti be az Azure Enterprise Portalon, a fenti „Használati adatok letöltése” lap „API hozzáférési kulcsa” elemére kattintva.
    - Másolja és illessze be a kulcsot a „Fiókkulcs” mezőbe.
 1. Az adathalmazok méretétől függően az adatok betöltése a Power BI-ba körülbelül 5–30 percet vesz igénybe.

A Power BI-jelentések készítése olyan közvetlen, partneri és közvetett vállalati szerződéssel rendelkező ügyfelek számára érhető el, akik hozzáféréssel rendelkeznek a számlázási adatok megtekintéséhez.

## <a name="report-faq"></a>Jelentés – Gyakori kérdések

A cikk ezen szakasza a jelentések értelmezésével kapcsolatos gyakori kérdésekre ad választ.

### <a name="why-is-my-cost-showing-as-0"></a>Miért jelenik meg 0 dollár a költségeim értékeként?

**Közvetlen regisztráció** Ha Ön fióktulajdonos vagy részlegszintű rendszergazda, forduljon az EA-rendszergazdához a díjszabási funkció engedélyezése céljából:

1. Kattintson a **Kezelés** elemre a bal oldali navigációs sávon.
1. Kattintson a kék ceruza szimbólumra Díjtételek megtekintése a részlegszintű rendszergazda számára elem mellett.
1. Válassza az **Engedélyezve** lehetőséget, majd a mentést.
1. Kattintson a kék ceruza elemre a Díjtételek megtekintése a fióktulajdonos számára elem mellett.
1. Válassza az **Engedélyezve** lehetőséget, majd a mentést.

Ez a művelet hozzáférést biztosít a fióktulajdonosok és a részlegszintű rendszergazdák számára a használati jelentésekhez tartozó költség- és díjszabási információkhoz.

**Közvetett regisztráció** Ellenőrizze, hogy partnere engedélyezte-e a díjszabási funkciót az Ön részére. Ezt csak a partner végezheti el, és a funkció bekapcsolása után Ön EA-rendszergazdaként tekintheti meg regisztrációja költségét és díjszabását.

Ha engedélyezni szeretné a díjtételek megtekintését a fióktulajdonosok és a részlegszintű rendszergazda számára, kövesse a fenti, **Közvetlen regisztráció** szakaszban felsorolt lépéseket.

### <a name="there-is-no-sku-information-on-the-usage-detail-report"></a>Nem található termékváltozat-adat a használati adatokról szóló jelentésben

A használati adatokról szóló jelentés nem tartalmazza a termékváltozat-adatokat; a jelentésben felhasznált szolgáltatási adatokat azonban megtekintheti. Ezután letöltheti az árlistajelentést a termékváltozat-adatok beszerzéséhez.

### <a name="the-total-amount-on-marketplace-does-not-match-on-usage-summary-and-csv-report"></a>A Marketplace-en lévő teljes összeg nem egyezik a használati összesítésben és a CSV-jelentésben szereplő összegzéssel.

A Marketplace díjainak jelentése csak a használatalapú Marketplace-díjakat tartalmazza. Az egyszeri díjak nem jelennek meg. A használati adatokat összegző oldalon megtalálja a használatalapú és az egyszeri díjakra vonatkozó legfrissebb használati információkat.

### <a name="there-is-no-information-on-my-api-report"></a>Nincs információ az API-jelentésről

Az API-kulcsok érvényessége hathavonta lejár. Ha probléma merül fel, hozzon létre egy új API-kulcsot. Ezenkívül fontos megkérni az EA-rendszergazdát, hogy hozza létre az új API-kulcsokat, és kövesse az API-jelentésekkel kapcsolatos gyakori kérdések lépéseit.

### <a name="my-power-bi-report-isnt-working"></a>Nem működik a Power BI-jelentés

A Power BI-jal kapcsolatos probléma esetén küldjön egy műszaki hibajegyet a Power BI csapatának ([https://support.powerbi.com](https://support.powerbi.com)), hogy segíthessenek Önnek a megoldásban.

### <a name="my-resource-tags-arent-showing-up-on-my-reports"></a>Az erőforráscímkék nem jelennek meg a jelentésekben

Az erőforráscímkék felügyelete az Azure Portalon történik. Az Azure előfizetési csapatával a következő elérhetőségen veheti fel a kapcsolatot: [https://portal.azure.com](https://portal.azure.com). Ha támogatási kérést szeretne létrehozni, kövesse az [ezen a hivatkozáson](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) elérhető lépéseket.

### <a name="why-does-my-resource-rate-change-every-day"></a>Miért változik naponta az erőforrás-díjszabás?

A részletes használati jelentésben szereplő erőforrás-díjszabás számított érték, amely a szolgáltatásra vonatkozó átlagos havi díjat jelöli. A díj kiszámítása a havi keret és a havi túlhasználati díj átlaga alapján történik a szolgáltatási egység vonatkozásában. A keret terhére elszámolt használat aránya és a túlhasználati díjak a hónap végéig változnak. Ezért az erőforrás-díjszabás is változik a hónap folyamán. Az erőforrás-díjszabás a hónap végét követő ötödik napon zárolódik.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Az erőforrás-díjszabás kiszámítására szolgáló folyamatok szószedete

**Összes RAW-egység:** A részletes használati jelentésen belül felhasznált mennyiség.
**MOCP-erőforrás egységenként:** A felsőbb rétegbeli használati rendszer különböző egységekben bocsátja ki az egyes szolgáltatások használatát. (Előre beállított) **Egységenkénti felhasználás:** EA-mértékegység. (Előre beállított) **Ár:** Az Azure EA Portalról származó egységár.
**Teljes költség:** A részletes használati jelentésből származó kiterjesztett költség vagy a kerethasználat + túlhasználat az Azure EA Portalról.


### <a name="charges-calculation"></a>Díjszámítás

**Átalakítás MOCP-erőforrásra egységenként** = KEREKÍTÉS(Teljes RAW-egység * MOCP-erőforrás egységenként,4) **Egységekre való átváltás** = Egységek az egységenkénti MOCP-erőforrásra való átváltás után / Egységenkénti felhasználás **Teljes költség** = Egységek * ár

### <a name="download-usage-calculation-logic"></a>A felhasználás számítási logikájának letöltése

**Erőforrás-díjszabás** = teljes költség / (összes RAW-egység / MOCP-erőforrás egységenként)

Az erőforrás díjszabását a díjak alapján állapítjuk meg, így gyakran nem egyezik meg az árlistán szereplő tényleges egységárral.

A túlhasználati díjak számítása esetén a nyers erőforrás-használat adatai legfeljebb hat tizedesjegy pontossággal jelennek meg, ahogy ez a letöltött használatiadat-jelentésen látható. Az Azure EA Portal azonban négy tizedesjegyre kerekíti a használatot a kötelezettségvállalási egységek esetében, a túlhasználati egységek esetében pedig nulla tizedesjegyre rövidít. Ez azt jelenti, hogy az Azure EA Portalon túlhasználat esetén csak a teljes egységekért számolunk fel díjat. Jelentős különbség észlelhető az egységek ára, valamint a túlhasználatért és a vegyes hónapokban fizetendő erőforrás-használati díj között.

## <a name="next-steps"></a>További lépések
- A számla és a díjak értelmezésével kapcsolatos további információkért lásd: [Az Azure Nagyvállalati Szerződés számlájának ismertetése](billing-understand-your-bill-ea.md).
- Az Azure EA Portal használatának kezdő lépéseit az [Ismerkedés az Azure EA Portallal](billing-ea-portal-get-started.md) című rész írja le.
