---
title: Az Azure Data Catalog – gyakori kérdések
description: – Gyakori kérdések az Azure Data Catalog, beleértve az adatforrás-felderítés jegyzet és felügyeleti képességeket.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: f08825c17c1ba61fc09edc1d8f2c226af8d4266f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231464"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Az Azure Data Catalog – gyakori kérdések
Ez a cikk az Azure Data Catalog szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat ismerteti.

## <a name="what-is-azure-data-catalog"></a>Mi az az Azure Data Catalog?
A Data Catalog egy teljes körűen felügyelt szolgáltatás, a rendszer a regisztráció és a vállalati adatforrások ellátó Microsoft Azure-ban üzemeltetett. A Data Catalog minden felhasználónak – az elemzőktől az adatelemzők és fejlesztők, is regisztrálni, Fedezze fel, ismertetése és felhasználhat adatforrásokat.

## <a name="what-customer-challenges-does-it-solve"></a>Milyen ügyfél ügyfélfeladatainak mindezt megoldani?
A Data Catalog kihívásaira az adatforrás-felderítés és az "sötét adatok" úgy, hogy a felhasználók felderítéséhez és megismerheti a vállalati adatforrások.

## <a name="what-are-its-target-audiences"></a>Mik azok a célközönségekkel?
A Data Catalog úgy tervezték, technikai és nem technikai jellegű felhasználóhoz, többek között:

* Adatok fejlesztők és az üzleti Intelligencia és elemzés szakemberek: személyek, akik felelősek az adatok és analitika tartalom felhasználásához mások előállító.
* Adatok megbízott: személyeket, akik rendelkeznek a ismeretek szerezhetők az adatokat, azt jelenti, és hogyan feltétlenül használható.
* Adatfelhasználók: egyszerűen észlelését, személyek ismertetése, és csatlakozzon a munka elvégzéséhez, az általuk választott eszközzel szükséges adatok.
* Központi informatikai: ki kell üzleti felhasználók, az adatforrások felderíthető, és akik karban kell tartania a felügyeletet, az adatok használatának módjától keresztül, és ki használja őket.

## <a name="what-is-its-availability-by-region"></a>Mi az a rendelkezésre állása régiónként?
Data Catalog-szolgáltatások érhetők el jelenleg a következő adatközpontokban:

* USA nyugati régiója
* USA keleti régiója
* Nyugat-Európa
* Észak-Európa
* Kelet-Ausztrália
* Délkelet-Ázsia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Mik azok a korlátok adategységek száma?
A Data Catalog ingyenes kiadása legfeljebb 5000 regisztrált adategységeket.

A Data Catalog Standard kiadása támogatja a legfeljebb 100 000 regisztrált adategységeket.

Minden objektum regisztrálva a Data Catalog, (például táblákat) egy adategységet nézetek, a fájlok és a jelentések, számít.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Mik azok a támogatott forrás- és eszköz adattípusok?
A jelenleg támogatott adatforrások listájáért lásd: [Data Catalog DSR](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Hogyan egy másik adatforrás támogatás igénylése?
Szolgáltatással kapcsolatos kéréseit és más visszajelzéseit, keresse fel a a [az Azure visszajelzés – fórumok a Data Catalog](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Hogyan kezdhetem a Data Catalog?
A rendszer a legjobb módszer a kezdéshez [Ismerkedés a Data Catalog](data-catalog-get-started.md). Ez a cikk a szolgáltatás funkcióinak teljes körű áttekintést.

## <a name="how-do-i-register-my-data"></a>Hogyan regisztrálhatom az adataimat?
A Data Catalog az adatok regisztrálása:
1. Az Azure Data Catalog-portálon található a **közzététel** területet, indítsa el az Azure Data Catalog-regisztráló eszköz. 
2. A Data Catalog adatforrás-regisztráló eszköz, a jelentkezzen be ugyanazokkal a hitelesítő adatokkal, hogy a Data Catalog-portál eléréséhez használhatja.
3. Válassza ki az adatforrás és az adott eszközök, amelyeket regisztrálni szeretne.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Mely tulajdonságok nem, bontsa ki a regisztrált adategységeket?
A konkrét tulajdonságok különböznek az adatforrást az adatforrás, de általában az a Data Catalog-közzétételi szolgáltatás kinyeri a következő információkat:

* Eszköz neve
* Eszköztípus
* Eszközintelligencia leírása
* Attribútum-/ oszlopnevek
* Attribútum-és oszlopdiagram adattípusok
* Attribútum/oszlop leírása

> [!IMPORTANT]
> Adategységek regisztrálása a Data Catalog helyezze át vagy nem az adatok másolása a felhőbe. Az eszközök általi metaadatok adatforrásból származó eszközök regisztrációja másolja át az Azure-ba, de az adatok továbbra is a meglévő adatforrás-helyen. Ez a szabály a kivétel a, ha úgy dönt, hogy előzetes rekordok vagy egy adatprofil töltse fel az eszközök regisztrálásakor. Amikor felvesz egy előzetes, legfeljebb 20 bejegyzést tartalmazhat minden eszköz átmásolva és pillanatképet készít a Data Catalog tárolt. Ha egy adatprofil, összesített adatokat kiszámítása és szerepel a katalógusban tárolt metaadatok. Összesített adatok között szerepelhetnek táblákat, a null értékek oszloponként aránya vagy a minimális, maximális és átlagos értékek, az oszlopok méretét. 
>
>

> [!NOTE]
> Adatforrások, például az SQL Server Analysis Services egy első osztályú rendelkező **leírás** tulajdonság, a Data Catalog adatforrás-regisztráló eszköz kinyeri a tulajdonság értéke. Az SQL Server relációs adatbázisok, amelyek nem rendelkeznek egy első osztályú **leírás** tulajdonság, a Data Catalog adatforrás-regisztráló eszköz kinyeri az értékét a **ms_description** kiterjesztett tulajdonság az objektumok és oszlopokat. További információkért lásd: [kiterjesztett tulajdonságok használatával adatbázis-objektumok](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Mennyi ideig kell időt vesz igénybe, megjelennek a katalógus az újonnan regisztrált eszközök?
Eszközök regisztrálása a Data Catalog, után egy időszak, 5-10 másodperc után jelennek meg a Data Catalog-portálon lehet.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hogyan jegyzettel láthatja el és a metaadatok bővíteni saját regisztrált adategységek?
Adja meg a regisztrált eszközökre vonatkozó metaadatokat a legegyszerűbb módja, hogy a Data Catalog-portálon jelölje ki az eszközt, és majd adja meg az értékeket a Tulajdonságok vagy séma ablaktáblán a kiválasztott objektum.

Bizonyos metaadatait, például a szakértők és a címkék és a regisztráció során is megadhatja. A Data Catalog-közzétételi szolgáltatás meg az értékeket az összes olyan eszköz, hogy regisztrálta vonatkoznak. A portálon további megjegyzéseket, a legutóbb regisztrált objektumok megtekintéséhez jelölje ki a **portál megtekintése** gombra a Data Catalog adatforrás-regisztráló eszköz végső képernyőjén.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hogyan törölhető a regisztrált adatforrások objektumok?
Az objektum kiválasztása a portálon, majd kattintson a Data Catalog egy objektum segítségével törölheti a **törlése** gombra. Az objektum eltávolítása a metaadatait távolít el a Data Catalog, de nem befolyásolja alapul szolgáló adatforrásban.

## <a name="what-is-an-expert"></a>Mit jelent a szakértő?
Szakértő egy megalapozott perspektíva vonatkozó olyan adatobjektumot rendelkező személy. Az objektum rendelkezhet több szakértői. Szakértői kell lennie a "tulajdonos" objektumhoz, de egyszerűen személy, aki tudja, hogyan is az adatokat, és használható.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hogyan oszthatom meg az adatokat a Data Catalog csapat Ha jutok a problémák?
Problémák jelentése, megoszthatják az információkat, és tegyen fel kérdéseket, nyissa meg a [Azure Data Catalog fórumon](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>A katalógus, amely a következők érdekelnek más adatforrással működik?
Aktívan dolgozunk további adatforrások hozzáadása a Data Catalog szolgáltatásba. Ha szeretne egy adott adatforrás támogatott lásd, javasolja azt (vagy a támogatási hangtípus, ha már javasolt) nyissa meg a [az Azure visszajelzés – fórumok a Data Catalog](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Milyen az Azure Data Catalog kapcsolódik a Power bi-ban a Data Catalog az Office 365?
A Power bi-ban a Data Catalog továbbfejlesztett változata, az Azure Data Catalog is felfoghatók. Től 2017 spring az Azure Data Catalog segítségével lehetővé teszik a megosztási és felderítési lekérdezéseket az Excel 2016-ban és a Power Query az Excel programhoz. A Data Catalog képességei az Excelben a Power BI Pro licenccel rendelkező felhasználók számára érhetők el.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Milyen engedélyekkel van szükségem az eszközök regisztrálása a Data Catalog?
Az adatkatalógus-regisztráló eszköz futtatásához az adatforrás, amely lehetővé teszi, hogy a metaadatok olvasása a forrásból a engedélyek szükségesek. Előzetes is tartalmazza, az engedélyeket, amelyek lehetővé teszik, hogy olvassa el az adatokat a segédprogramot a regisztrálni kívánt objektumokat kell rendelkeznie.

A Data Catalog azt is lehetővé teszi, hogy a katalógus-rendszergazdák korlátozni, mely felhasználókat és csoportokat adhat hozzá metaadatokat a katalógusban. További információkért lásd: [hogyan biztonságos hozzáférés az adatkatalógushoz és adategységeket](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>A Data Catalog lesz elérhető, valamint a helyszíni telepítéshez?
A Data Catalog egy felhőalapú szolgáltatás, amely használható felhőbeli, mind a helyszíni adatforrások, hogy az adatforrás-felderítés hibrid megoldás. Jelenleg nincsenek csomagok a Data Catalog szolgáltatást, amely a helyszíni verziójához.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Is több vagy részletesebb metaadatok is kinyerni az adatforrásokból tudom regisztrálni?
Folyamatosan dolgozunk a Data Catalog képességei. Ha azt szeretné, hogy a regisztráció során az adatforrásból kinyert metaadatokat, javasoljuk, (vagy szavazzon rá, ha már javasolt) az a [az Azure visszajelzés – fórumok a Data Catalog](https://feedback.azure.com/forums/906052-data-catalog). 

Ha szeretné oszlop/séma-metaadatok, az előzetes verziók, illetve az adatok profilokat, az adatforrásokat, ahol a metaadatok nem ki kell olvasni az adatforrás-regisztráló eszköz, amelyet a Data Catalog API használatával adja hozzá ezeket a metaadatokat. További információkért lásd: [Azure Data Catalog REST API](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hogyan korlátozhatom a regisztrált adategységekből, látható-e, hogy csak bizonyos felhasználók derítheti fel azokat?
Válassza ki a Data Catalog az adategységeket, és kattintson a **saját tulajdonba vétel** gombra. A Data catalogban az adategységek tulajdonosainak módosíthatja a profilláthatósági beállításokról, vagy hogy minden felhasználó számára a saját tulajdonú eszközök feltérképezésére, vagy meghatározott felhasználókra korlátozható a láthatóságuk. További információkért lásd: [kezelése az Azure Data Catalogban az adategységek](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hogyan frissíthetem a regisztráció egy adategységet, hogy a módosításokat az adatforrásban is megjelennek a katalógusban?
Már a katalógusban regisztrált adategységek a metaadatok frissítéséhez egyszerűen regisztrálja újra az adatforrás, amely tartalmazza az eszközöket. Az adatforrás módosításainak oszlopok hozzáadása vagy eltávolítása táblák / nézetek, mint például frissülnek a katalógusban, de minden olyan felhasználó által megadott jegyzetek megmaradnak.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>A nem választ a kérdésére itt. Hol tudhatok meg a válaszokat?
Nyissa meg a [Azure Data Catalog fórumon](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Hiba gyakori kérdéseket talál itt eljutnak.
