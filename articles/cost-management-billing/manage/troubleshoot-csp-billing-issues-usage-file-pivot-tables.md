---
title: Azure CSP-számlázási problémák elhárítása a használati fájlok kimutatástábláival
description: Ez a cikk segítséget nyújt az Azure Cloud Solution Provider (CSP) számlázásával kapcsolatos problémák a használati CSV-fájlokból létrehozott kimutatástáblákkal történő elhárításában.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: 6525d809805da3a19a0efe423306f18d8e67a646
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026603"
---
# <a name="troubleshoot-csp-billing-issues-with-usage-file-pivot-tables"></a>CSP-számlázási problémák elhárítása a használati fájlok kimutatástábláival

Ez a cikk segítséget nyújt a Cloud Solution Provider (CSP) számlázási problémáinak a Partnerközpont egyeztetési (használati) fájljaiban található kimutatástáblákkal történő elhárításában. Az Azure használati fájljai tartalmazzák az Azure használatával és felhasználásával kapcsolatos összes információt. A fájlban található információk az alábbiak megértésében nyújthatnak segítséget:

- Az Azure-foglalások használatának és alkalmazásának megismerése
- Az Azure Cost Managementben található és a kiállított számlán szereplő információk egyeztetése
- Kiugróan magas költség kezelése
- Szolgáltatói szerződésre vonatkozó visszatérítés összegének kiszámítása

A használati fájlokból származó információk használatával jobban megismerheti a használattal kapcsolatos problémákat, és diagnosztizálhatja őket. A használati fájlok vesszővel tagolt (CSV) formátumban jönnek létre. Mivel a használati fájlok nagy méretű CSV-fájlok lehetnek, könnyebben kezelhetők és tekinthetők meg kimutatástáblákként táblázatkezelő alkalmazásokban, például az Excelben. Az ebben a cikkben szereplő példák az Excelt használják, de bármilyen táblázatkezelő alkalmazást használhat.

Csak a számlázási rendszergazdák és a globális rendszergazdák rendelkeznek hozzáféréssel az egyeztetési fájlok letöltéséhez. További információért lásd a [sorelemek olvasásának módját a Partnerközpont egyeztetési fájljaiban](/partner-center/use-the-reconciliation-files).

## <a name="get-the-data-and-format-it"></a>Adatok lekérése és formázása

Mivel az Azure használati fájljai CSV-formátumban vannak, ezért elő kell készítenie az adatokat az Excelben történő használathoz. Az alábbi lépések végrehajtásával formázza az adatokat táblázatként.

1. Töltse le a használati fájlokat a [Számla megkeresése](/partner-center/read-your-bill#find-your-bill) című szakaszban lévő utasításokat követve.
1. Nyissa meg a fájlt az Excelben.
1. A nem formázott adatok az alábbi példához hasonlók.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-csv-data.png" :::
1. Válassza ki a tábla első mezőjét, amely a **Partnerazonosító**.
1. Nyomja le a Ctrl + Shift + Lefelé nyíl, majd a Ctrl + Shift + Jobbra nyíl billentyűkombinációt a táblázatban szereplő összes információ kijelöléséhez.
1. A felső menüben válassza a **Beszúrás** > **Táblázat** lehetőséget. A Táblázat létrehozása mezőben válassza a **Fejléceket tartalmazó táblázat**, majd az **OK** lehetőséget.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" :::
1. A felső menüben válassza a **Beszúrás** > **Kimutatás** lehetőséget, majd kattintson az **OK** gombra. A művelet egy új lapot hoz létre a fájlban, és átlépteti a lap jobb oldalán lévő kimutatástábla-területre.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

A kimutatástábla mezőterülete egy áthúzásos módszerrel használható terület. Folytassa a következő szakasszal, amelyből megtudhatja, hogyan hozhat létre kimutatástáblát.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Kimutatástábla létrehozása az erőforrásonkénti Azure-költségek megjelenítéséhez

Ebben a szakaszban egy kimutatástáblát fog létrehozni, amelyben elháríthatja az Azure-használat általános problémáit. A példatábla segítségével megtudhatja, melyik szolgáltatás használja a legtöbb erőforrást. Vagy megtekintheti azokat az erőforrásokat, amelyek a legnagyobb költséggel járnak, valamint azt, hogyan történik a szolgáltatások díjának felszámítása.

1. A Kimutatástábla Mezők területén húzza a **Szolgáltatásnevet** és az **Erőforrást** a **Sorok** területre. Helyezze az **Erőforrást** a **Szolgáltatásnév** alá.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Következő lépésként helyezze az **Adózás utáni teljes összeget** az **Értékek** területre. Használhatja a Felhasznált mennyiség oszlopot is a felhasználási egységek és tranzakciók adatainak lekéréséhez. Ez például lehet GB vagy óra. Vagy a költségek helyett a tranzakciókat is lekérheti különböző pénznemekben (például: USD, EUR, INR).  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Most már rendelkezik irányítópulttal az általános felhasználás vizsgálatához. A kimutatástábla szűrési lehetőségével egy adott szolgáltatásra szűrhet.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Ha egy kimutatástábla második szintjére, például egy erőforrásra szeretne szűrni, a táblában válasszon ki egy második szintű elemet.
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. További szűrők eléréséhez hozzáadhatja az **Előfizetés azonosítóját** és az **Ügyfél vállalatának nevét** a **Szűrők** területhez, és kiválaszthatja a kívánt hatókört.

## <a name="create-a-pivot-table-to-view-azure-usage-by-date"></a>Kimutatástábla létrehozása az Azure használatának dátum szerinti megtekintéséhez

Ebben a szakaszban egy kimutatástáblát fog létrehozni, amelyben elháríthatja az Azure használatával kapcsolatos általános problémákat a Felhasznált mennyiség és a dátum alapján. Hasznos lehet megállapítani a számlázás kiugróan magas értékeit dátum és szolgáltatás szerint. Vagy megtekintheti azokat az erőforrásokat, amelyek a legnagyobb költséggel járnak, valamint azt, hogyan történik a szolgáltatások díjának felszámítása.

Az egyeztetési fájl két táblával rendelkezik. Az egyik (a fő tábla) a dokumentum tetején, a másik tábla pedig az alján található. Sok információ megegyezik a két táblában, de a második tábla nem tartalmaz díjszabási vagy költségadatokat. Azonban a használat dátumára és a felhasznált mennyiségre vonatkozó adatok megtalálhatók benne.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/reconciliation-file-two-tables.png" :::

1. Használja az [Adatok lekérése és formázása](#get-the-data-and-format-it) című szakaszban ismertetett lépéseket az egyeztetési fájl alján lévő információt tartalmazó Excel-táblázat létrehozásához.
1. Ha a táblázat elkészült és kimutatástábla-oldallal is rendelkezik, használja a kimutatástábla-létrehozása-az-erőforrásonkénti-Azure-költségek-megjelenítéséhez szakaszban ismertetett lépéseket az irányítópult előkészítéséhez. Az Adózás utáni teljes összeg használata helyett helyezze a **Felhasznált mennyiséget** az **Értékek** területre.
1. Adja hozzá a **Használat dátumát** az oszlopok szakaszához. A kimutatástáblának az alábbi példához hasonlóan kell kinéznie.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/final-pivot-table-fields.png" :::
1. Most már rendelkezik a használatot dátum szerint megjelenítő irányítópulttal. Az egyes hónapokat a **+** szimbólum kiválasztásával bővítheti.

Az irányítópulton a felhasznált mennyiség látható, különböző egységekben (pl.: GB, óra, átvitelek) kifejezve.

A napi díj megtekintéséhez hozzáadhatja az **Erőforrás GUID-jét** a **Sorok** területhez. A felső táblában adja hozzá az erőforrás egységárát (**Listaár**). A **Listaár** elemet szorozza meg a **Felhasznált mennyiséggel** az adózás előtti díjak kiszámításához. Az összegeknek egyezniük kell.

Egyes erőforrások (szolgáltatások) a felhasznált mennyiség alapján skálázott díjszabással rendelkeznek. Például egyes erőforrások esetében az első felhasznált 100 GB ára magasabb, az utána felhasznált GB-ok ára viszont már alacsonyabb. A költségek manuális kiszámításakor tartsa szem előtt a skálázott díjszabást.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Kimutatástábla létrehozása adott erőforrások költségeinek megtekintéséhez

Egyetlen erőforrás különböző szolgáltatásokhoz tartozó költségeket vonhat maga után. Például egy virtuális gép számítási, operációsrendszer-licencelési, valamint sávszélességgel (adatátvitellel), fenntartott példányok használatával, tárolással és pillanatfelvételekkel kapcsolatos költségeket vonhat maga után. Ha egy adott erőforrás általános használati adatait szeretné áttekinteni, a következő lépések végigvezetik az irányítópult létrehozásán, amelynek segítségével megtekintheti az általános használati adatokat a használati fájlokkal.

Az egyeztetési fájlok nem tartalmaznak erőforrás-specifikus adatokat. Ezért használja az aggregált használati fájlt. Vegye fel a kapcsolatot az [Azure számlázási támogatásával](https://go.microsoft.com/fwlink/?linkid=2083458), hogy elküldhessék Önnek az előfizetés aggregált használati fájlját. Az aggregált fájlok az előfizetés szintjén jönnek létre. A nem formázott adatok az alábbi példához hasonlók.

:::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/raw-aggregated-usage-file.png" :::

A fájl az alábbi oszlopokat tartalmazza.

- **Használat kezdete** és **Használat vége** – Az egyes sorelemek (egyes használati egységek) dátuma. Például minden nap.
- **Mért erőforrás azonosítója** – Az Azure-ban ez a mérési azonosítónak felel meg.
- **Tulajdonságok** – A példányazonosítót (erőforrás nevét) és egyéb részleteket, például a helyet tartalmazza.
- **Mennyiség** – Felhasznált mennyiség az egyeztetési fájlban.

1. Válassza ki a tábla első mezőjét, amely a **Partnerazonosító**.  
1. Nyomja le a Ctrl + Shift + Lefelé nyíl, majd a Ctrl + Shift + Jobbra nyíl billentyűkombinációt a táblázatban szereplő összes információ kijelöléséhez.
1. A felső menüben válassza a **Beszúrás** > **Táblázat** lehetőséget. A Táblázat létrehozása mezőben válassza a **Fejléceket tartalmazó táblázat**, majd az **OK** lehetőséget.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" :::
1. A felső menüben válassza a **Beszúrás** > **Kimutatás** lehetőséget, majd kattintson az **OK** gombra. A művelet egy új lapot hoz létre a fájlban, és átlépteti a lap jobb oldalán lévő kimutatástábla-területre.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/pivot-table-fields-reconciliation.png" :::
1. Ezután adja hozzá a **Mért erőforrás azonosítója** értékét a **Sorok** területhez, valamint a **Mennyiséget** az **Értékekhez**. Az eredmények a használatra vonatkozó általános információkat mutatják be. További részletekért helyezze a **Használat vége dátumidő** értékét az **Oszlopok** területre.  
    :::image type="content" source="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-csp-billing-issues-usage-file-pivot-tables/overall-usage.png" :::
1. Egy átfogó jelentés megtekintéséhez adja hozzá a **Tulajdonságokat** a **Sorokhoz** a **Mért erőforrás azonosítója** alatt. A használat teljes irányítópultját jeleníti meg.
1. Egy adott erőforrás alapján történő szűréshez adja hozzá a **Tulajdonságokat** a **Szűrők** területhez, majd válassza ki a kívánt használatot. Az erőforrásnevet a Keresés használatával keresheti meg.
    Az erőforrás költségeinek megtekintéséhez keresse meg a teljes felhasznált mennyiséget, és szorozza meg az értéket a listaárral. A listaár minden erőforrás-GUID (mért erőforrás azonosítója) esetében egyedi. Ha egy erőforrás több mérterőforrás-azonosítót használ, jegyezze fel az egyes azonosítók teljes értékét.

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Következő lépések

- [Ismerkedés a partnerek számára készült Azure Cost Management szolgáltatással](../costs/get-started-partners.md).