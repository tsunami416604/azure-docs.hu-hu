---
title: Az Azure Data Catalog gyakori kérdései
description: Gyakori kérdések az Azure Data Catalog, beleértve az adatforrás-felderítési, jegyzet- és felügyeleti képességek.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7dbb1b4a8b85350b8bf8a6df0c9035a19055444c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409019"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Az Azure Data Catalog gyakori kérdései
Ez a cikk az Azure Data Catalog szolgáltatással kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="what-is-azure-data-catalog"></a>Mi az az Azure Data Catalog?
A Data Catalog egy teljes körűen felügyelt szolgáltatás, amelyet a Microsoft Azure üzemeltet, és amely a vállalati adatforrások regisztrációs és felderítési rendszereként szolgál. A Data Catalog segítségével az elemzőktől az adatszakértőkig és -fejlesztőkig bármely felhasználó regisztrálhat, felderíthet, megérthet és felhasználhat adatforrásokat.

## <a name="what-customer-challenges-does-it-solve"></a>Milyen ügyfélkihívásokat old meg?
A Data Catalog az adatforrás-felderítés és a "sötét adatok" kihívásaival foglalkozik, így a felhasználók felfedezhetik és megérthetik a vállalati adatforrásokat.

## <a name="what-are-its-target-audiences"></a>Mik a célközönségei?
A Data Catalog műszaki és nem műszaki felhasználók számára készült, beleértve a következőket:

* Adatfejlesztők és üzletiintelligencia-és elemzési szakemberek: Olyan személyek, akik mások számára adat- és elemzési tartalmak előállításáért felelősek.
* Adatfelelősök: Azok a személyek, akik ismerik az adatokat, azokat, és hogyan kívánják használni őket.
* Adatfogyasztók: Azok az emberek, akiknek az általuk választott eszköz segítségével könnyen fel kell fedezniük, meg kell érteniük és csatlakozniuk kell a munkájukhoz szükséges adatokhoz.
* Központi informatikai részleg: Azok az emberek, akiknek több száz adatforrást kell felderíteniük az üzleti felhasználók számára, és akiknek fel ügyelniük kell arra, hogy hogyan és ki használja fel az adatokat.

## <a name="what-is-its-availability-by-region"></a>Mi a rendelkezésre állás a régió?
A Data Catalog-szolgáltatások jelenleg a következő adatközpontokban érhetők el:

* USA nyugati régiója
* USA keleti régiója
* Nyugat-Európa
* Észak-Európa
* Kelet-Ausztrália
* Délkelet-Ázsia

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Milyen korlátai vannak az adateszközök számára vonatkozóan?
A Data Catalog ingyenes kiadása legfeljebb 5000 regisztrált adateszközre van korlátozva.

A Data Catalog Standard Edition legfeljebb 100 000 regisztrált adateszközt támogat.

A Data Catalogban regisztrált bármely objektum, például táblák, nézetek, fájlok és jelentések adateszköznek számítanak.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Mik a támogatott adatforrás- és eszköztípusok?
A jelenleg támogatott adatforrások listáját a Data Catalog DSR című témakörben [tésszet.](data-catalog-dsr.md)

## <a name="how-do-i-request-support-for-another-data-source"></a>Hogyan kérhetek támogatást egy másik adatforráshoz?
A szolgáltatáskérések és egyéb visszajelzések elküldéséhez nyissa meg az [Azure-visszajelzési fórumok adatkatalógusát.](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources)

## <a name="why-do-i-get-an-error-catalog-already-exists-when-i-try-to-create-a-new-catalog"></a>Miért jelenik meg *hibaüzenet: A katalógus már létezik,* amikor új katalógust próbálok létrehozni?

Ha Power BI Pro licenccel vásárol Office 365 E5-öt, a Microsoft automatikusan létrehoz egy alapértelmezett katalógust az előfizetés régiójában. Ez a katalógus az ingyenes termékváltozatot használja. Az Office 365 / Power BI felhasználói licencét az Office 365 Felügyeleti lapon kezeli. 

Az ilyen típusú adatkatalógusazonban nem rendelkezik **rendszergazdai lehetőséggel,** és nem látható az **Azure Portalon.** Az ilyen típusú adatkatalógus nem törölhető. Hasonlóképpen nem nevezheti át az adatkatalógust, és nem helyezheti át másik régióba. 

Azok a felhasználói fiókok, amelyek automatikus Power BI Pro-licenccel rendelkeznek, licencszerződés miatt hozzáférhetnek az adatkatalógushoz, amikor a Power BI Pro licenccel regisztráltak az Office 365 E5-re. Az ilyen típusú felhasználók teljes hozzáféréssel rendelkeznek az adatkatalógus-eszközökhöz rendszergazdai jogosultságok nélkül. Ez a fajta felhasználó *nem* része a **katalógus felhasználói** szerepkör az Azure Data Catalog.


## <a name="how-do-i-get-started-with-data-catalog"></a>Hogyan kezdhetem el a Data Catalog-ot?
A kezdéshez a legjobb módszer az Első lépések a [Data Catalog alkalmazással.](data-catalog-get-started.md) Ez a cikk a szolgáltatás képességeinek teljes körű áttekintése.

## <a name="how-do-i-register-my-data"></a>Hogyan regisztrálhatom az adataimat?
Az adatok regisztrálása a Data Catalog-ban:
1. Az Azure Data Catalog portálon a **Közzététel** területen indítsa el az Azure Data Catalog regisztrációs eszközt. 
2. A Data Catalog adatforrás-regisztrációs eszközben jelentkezzen be ugyanazzal a hitelesítő adattal, amelyet a Data Catalog portál eléréséhez használ.
3. Válassza ki az adatforrást és a regisztrálni kívánt eszközöket.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>Milyen tulajdonságokat bont ki a regisztrált adateszközökre?
Az adott tulajdonságok adatforrásról adatforrásra változnak, de általában a Data Catalog közzétételi szolgáltatás a következő információkat nyeri ki:

* Eszköz neve
* Eszköz típusa
* Eszköz leírása
* Attribútum-/oszlopnevek
* Attribútum-/oszlopadattípusok
* Attribútum/oszlop leírása

> [!IMPORTANT]
> Az adatelemek regisztrálása a Data Catalog szolgáltatással nem mozgatja vagy másolja az adatokat a felhőbe. Az eszközök adatforrásból történő regisztrálása átmásolja az eszközök metaadatait az Azure-ba, de az adatok a meglévő adatforrás-helyen maradnak. Ez alól a szabály alól kivételt képez, ha úgy dönt, hogy az eszközök regisztrálásakor előzetes rekordokat vagy adatprofilt tölt fel. Ha előzetes verziót ad meg, a rendszer legfeljebb 20 rekordot másol az egyes eszközökből, és pillanatképként tárolódik az adatkatalógusban. Adatprofil felvételekor a rendszer kiszámítja az összesített adatokat, és tartalmazza a katalógusban tárolt metaadatokat. Az összesített adatok közé tartozhat a táblák mérete, az oszloponkénti null értékek százalékos aránya, illetve az oszlopok minimális, maximális és átlagos értékei. 
>
>

> [!NOTE]
> Az olyan adatforrások esetében, mint például az első osztályú **Description** tulajdonsággal rendelkező SQL Server Analysis Services, a Data Catalog adatforrás-regisztrációs eszköz kinyeri ezt a tulajdonságértéket. Az első osztályú **Leírás** tulajdonsággal nem rendelkező helyszíni SQL Server *relációs* adatbázisok esetében a Data Catalog adatforrás-regisztrációs eszköz kinyeri az objektumok és oszlopok **ms_description** kiterjesztett tulajdonságának értékét. Ez a tulajdonság nem támogatott az SQL Azure-ban. További információt a [Kiterjesztett tulajdonságok használata adatbázis-objektumokon című témakörben talál.](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx)
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Mennyi ideig tart az újonnan regisztrált eszközök megjelenése a katalógusban?
Miután regisztrálta az eszközöket a Data Catalog szolgáltatásban, 5–10 másodperces időszak lehet, mielőtt megjelennek a Data Catalog portálon.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Hogyan jegyzetelhetek és gazdagítom a regisztrált adatelemek metaadatait?
A regisztrált eszközök metaadatainak megadásának legegyszerűbb módja, ha kiválasztja az eszközt a Data Catalog portálon, majd beírja az értékeket a kijelölt objektum tulajdonságok ablaktáblájában vagy sémaablaktáblájában.

A regisztrációs folyamat során metaadatokat is megadhat, például szakértőket és címkéket. A Data Catalog közzétételi szolgáltatásban megadott értékek az adott időpontban regisztrált összes eszközre vonatkoznak. Ha további megjegyzéscéljából meg szeretné tekinteni a portálon a legutóbb regisztrált objektumokat, kattintson a **Portál megtekintése** gombra az Adatkatalógus adatforrás-regisztrációs eszköz utolsó képernyőjén.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Hogyan törölhetem a regisztrált adatobjektumokat?
Az objektumot úgy törölheti a Data Catalog programból, hogy kijelöli az objektumot a portálon, majd a **Törlés** gombra kattint. Az objektum eltávolítása eltávolítja a metaadatokat a Data Catalog-ból, de nincs hatással az alapul szolgáló adatforrásra.

## <a name="what-is-an-expert"></a>Mi az a szakértő?
A szakértő olyan személy, aki tájékozott anamnézissel rendelkezik egy adatobjektummal kapcsolatban. Egy objektumnak több szakértője is lehet. A szakértőnek nem kell egy objektum "tulajdonosának" lennie, hanem egyszerűen olyan valaki, aki tudja, hogyan lehet és kell használni az adatokat.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Hogyan oszthatok meg adatokat a Data Catalog csapatával, ha problémákba ütközöm?
A problémák jelentéséhez, az információk megosztásához és a kérdések hez látogasson el az [Azure Data Catalog fórumára.](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409)

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Működik a katalógus egy másik adatforrással, amely érdekel?
Aktívan dolgozunk azon, hogy további adatforrásokat adjunk hozzá az adatkatalógushoz. Ha egy adott adatforrás támogatottat szeretne látni, javasoljon azt (vagy a támogatást, ha már javasolták) [az Azure visszajelzési fórumain lévő adatkatalógusban.](https://feedback.azure.com/forums/906052-data-catalog)

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Milyen engedélyekre van szükségem az eszközök adatkatalógusban való regisztrálásához?
A Data Catalog regisztrációs eszköz futtatásához engedélyekre van szüksége az adatforráshoz, amely lehetővé teszi a metaadatok forrásból történő olvasását. Az előnézet felvételéhez olyan engedélyekkel kell rendelkeznie, amelyek lehetővé teszik a regisztrált objektumok adatainak olvasását.

A Data Catalog azt is lehetővé teszi, hogy a katalógusgazdák korlátozzák, hogy mely felhasználók és csoportok adhatnak hozzá metaadatokat a katalógushoz. További információt az [Adatkatalógushoz és adateszközökhöz való hozzáférés biztonságossá tétele című témakörben talál.](data-catalog-how-to-secure-catalog.md)

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>A Data Catalog elérhető lesz a helyszíni telepítéshez is?
A Data Catalog egy felhőalapú szolgáltatás, amely a felhőbeli és a helyszíni adatforrásokkal egy hibrid adatforrás-felderítési megoldás biztosításához is használható. Jelenleg nincsenek tervek a data katalógus szolgáltatás helyszíni verzióját.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Kinyerhetek több vagy gazdagabb metaadatot az általam regisztrált adatforrásokból?
Aktívan dolgozunk a Data Catalog képességeinek bővítésén. Ha azt szeretné, hogy a regisztráció során további metaadatokat nyerjen ki az adatforrásból, javasoljon azt (vagy szavazzon rá, ha már javasolták) [az Azure-visszajelzési fórumok adatkatalógusában.](https://feedback.azure.com/forums/906052-data-catalog) 

Ha oszlop-/sémametaadatokat, előnézeteket vagy adatprofilokat szeretne megadni olyan adatforrásokhoz, amelyeknél az adatforrás-regisztrációs eszköz nem nyeri ki ezeket a metaadatokat, a Data Catalog API-val hozzáadhatja ezt a metaadatot. További információ: [Azure Data Catalog REST API.](https://docs.microsoft.com/rest/api/datacatalog/)

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Hogyan korlátozhatom a regisztrált adateszközök láthatóságát, hogy csak bizonyos személyek fedezhessék fel őket?
Jelölje ki az adatelemeket az adatkatalógusban, majd kattintson a **Tulajdonjog átvétele** gombra. A Data Catalog adateszközeinek tulajdonosai módosíthatják a láthatósági beállításokat, hogy vagy minden felhasználó felfedezhesse a tulajdonában lévő eszközöket, vagy adott felhasználókra korlátozhatja a láthatóságot. További információt az [Adateszközök kezelése az Azure Data Catalogban című témakörben talál.](data-catalog-how-to-manage.md)

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Hogyan frissíthetem egy adateszköz regisztrációját, hogy az adatforrás változásai megjelenjenek a katalógusban?
A katalógusban már regisztrált adateszközök metaadatainak frissítéséhez egyszerűen regisztrálja újra az eszközöket tartalmazó adatforrást. Az adatforrás minden változása, például a táblákból vagy nézetekből hozzáadott vagy eltávolított oszlopok frissülnek a katalógusban, de a felhasználók által biztosított jegyzetek megmaradnak.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>A kérdésemre itt nem érkezett válasz. Hol kaphatok válaszokat?
Nyissa meg az [Azure Data Catalog fórumot.](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409) Az ott feltett kérdések ide kerülnek.
