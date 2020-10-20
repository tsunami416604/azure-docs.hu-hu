---
title: Azure EA-számlázási problémák elhárítása a használati fájlok kimutatástábláival
description: Ez a cikk segítséget nyújt a Nagyvállalati Szerződés (EA) számlázásával kapcsolatos problémák a használati CSV-fájlokból létrehozott kimutatástáblákkal történő elhárításában.
author: banders
ms.reviewer: isvargas
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 10/09/2020
ms.author: banders
ms.openlocfilehash: a30f85ae1b65321a25ee93239374d2a8aae26769
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92026431"
---
# <a name="troubleshoot-ea-billing-issues-with-usage-file-pivot-tables"></a>EA-számlázási problémák elhárítása a használati fájlok kimutatástábláival

Ez a cikk segítséget nyújt az EA-számlázási problémák, a használati fájlokban található kimutatástáblákkal történő elhárításában. Az Azure használati fájljai tartalmazzák az Azure használatával és felhasználásával kapcsolatos összes információt. A fájlban található információk az alábbiak megértésében nyújthatnak segítséget:

- Az Azure-foglalások használatának és alkalmazásának megismerése
- Az Azure Cost Managementben található és a kiállított számlán szereplő információk egyeztetése
- Kiugróan magas költség kezelése
- Szolgáltatói szerződésre vonatkozó visszatérítés összegének kiszámítása

A használati fájlokból származó információk használatával jobban megismerheti a használattal kapcsolatos problémákat, és diagnosztizálhatja őket. A használati fájlok vesszővel tagolt (CSV) formátumban jönnek létre. Mivel a használati fájlok nagy méretű CSV-fájlok lehetnek, könnyebben kezelhetők és tekinthetők meg kimutatástáblákként táblázatkezelő alkalmazásokban, például az Excelben. Az ebben a cikkben szereplő példák az Excelt használják, de bármilyen táblázatkezelő alkalmazást használhat.

Csak az EA-rendszergazdák, a fióktulajdonosok és a részleg rendszergazdái tölthetik le a használati fájlokat.

## <a name="get-the-data-and-format-it"></a>Adatok lekérése és formázása

Mivel az Azure használati fájljai CSV-formátumban vannak, ezért elő kell készítenie az adatokat az Excelben történő használathoz. Az alábbi lépések végrehajtásával formázza az adatokat táblázatként.

1. Töltse le a Használati adatok 2. verzióját a Minden költség (használat és vásárlások) fájllal együtt, az [EA-ügyfelek használati adatainak letöltését](https://docs.microsoft.com/azure/cost-management-billing/manage/download-azure-invoice-daily-usage-date#download-usage-for-ea-customers) ismertető szakasz utasításait követve.
1. Nyissa meg a fájlt az Excelben.
1. A nem formázott adatok az alábbi példához hasonlók.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/raw-csv-data-ea.png" :::
1. Válassza ki a táblázat első mezőjét, amely a **Számlázási fiók azonosítja** nevet, az első oszlopnevet tartalmazza.
1. Nyomja le a Ctrl + Shift + Lefelé nyíl, majd a Ctrl + Shift + Jobbra nyíl billentyűkombinációt a táblázatban szereplő összes információ kijelöléséhez.
1. A felső menüben válassza a **Beszúrás** > **Táblázat** lehetőséget. A Táblázat létrehozása mezőben válassza a **Fejléceket tartalmazó táblázat**, majd az **OK** lehetőséget.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/create-table-dialog.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" :::
1. A felső menüben válassza a **Beszúrás** > **Kimutatás** lehetőséget, majd kattintson az **OK** gombra. Ez a művelet egy új lapot hoz létre a fájlban. Ez átlépteti a lap jobb oldalán lévő kimutatástábla-területre.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields.png" :::

A kimutatástábla mezőterülete egy áthúzásos módszerrel használható terület. Folytassa a következő szakasszal, amelyből megtudhatja, hogyan hozhat létre kimutatástáblát.

## <a name="create-pivot-table-to-view-azure-costs-by-resources"></a>Kimutatástábla létrehozása az erőforrásonkénti Azure-költségek megjelenítéséhez

Ebben a szakaszban egy kimutatástáblát fog létrehozni, amelyben elháríthatja az Azure-használat általános problémáit. A példatábla segítségével megtudhatja, melyik szolgáltatás használja a legtöbb erőforrást. Vagy megtekintheti azokat az erőforrásokat, amelyek a legnagyobb költséggel járnak, valamint azt, hogyan történik a szolgáltatások díjának felszámítása.

1. A Kimutatás Mezők területén húzza a **Mérési kategóriát** és a **Terméket** a **Sorok** szakaszba. A **Terméket** helyezze a **Mérési kategória** alá.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/rows-section.png" :::
1. Ezután adja hozzá a **Költségek** oszlopot az **Értékek** szakaszhoz. Használhatja a Felhasznált mennyiség oszlopot is a felhasználási egységek és tranzakciók adatainak lekéréséhez. Ez például lehet GB vagy óra. Vagy a költségek helyett a tranzakciókat is lekérheti különböző pénznemekben (például: USD, EUR, INR).  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/add-pivot-table-fields.png" :::
1. Most már rendelkezik irányítópulttal az általános felhasználás vizsgálatához. A kimutatástábla szűrési lehetőségével egy adott szolgáltatásra szűrhet.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-row-label.png" :::
    Ha egy kimutatástábla második szintjére, például egy erőforrásra szeretne szűrni, a táblában válasszon ki egy második szintű elemet.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-filter-option-select-field.png" :::
1. Húzza az **Erőforrás azonosítója** oszlopot a **Termék** alá a **Sorok** területen, hogy láthassa az egyes szolgáltatások erőforrásonkénti költségeit. A részletes díjszabási információk megtekintéséhez tekintse meg a szervezet Egységárát, és keresse meg a **Terméket** az árlista első oszlopában.
1. Adja hozzá a **Dátum** oszlopot az **Oszlopok** szakaszhoz, a termék napi felhasználásának megtekintéséhez.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-date.png" :::
1. A **+** szimbólumokkal kibonthatja és összecsukhatja a hónapokat az egyes hónapok oszlopainál.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" :::  
    A **Költség** és a **Mennyiség** oszlop hozzáadása az **Értékek** területhez nem kötelező. Ha mégis így tesz, ez két oszlopot hoz létre az egyes adatszakaszokhoz minden hónap és nap alatt, amikor a Dátum oszlop a kimutatástábla Oszlopok szakaszában van.
1. További szűrők eléréséhez hozzáadhatja az Előfizetési azonosító, Részleg, Erőforráscsoport, Címkék vagy Költségközpont oszlopot a **Szűrők** területhez, és kiválaszthatja a kívánt elemet.

## <a name="create-pivot-table-to-view-cost-for-a-specific-resource"></a>Kimutatástábla létrehozása adott erőforrások költségeinek megtekintéséhez

Egyetlen erőforrás különböző szolgáltatásokhoz tartozó költségeket vonhat maga után. Például egy virtuális gép számítási, operációsrendszer-licencelési, valamint sávszélességgel (adatátvitellel), fenntartott példányok használatával, tárolással és pillanatfelvételekkel kapcsolatos költségeket vonhat maga után. Ha egy adott erőforrás általános használati adatait szeretné áttekinteni, a következő lépések végigvezetik az irányítópult létrehozásán, amelynek segítségével megtekintheti az általános használati adatokat a használati fájlokkal.

1. A jobb oldali menüben húzza az **Erőforrás azonosítója** elemet a kimutatástábla menüjének **Szűrő** szakaszába.
1. Kattintson arra az erőforrásra, amelynek a költségeit meg szeretné tekinteni. Írjon be a **keresőmezőbe** az erőforrásnév megkereséséhez.
1. Adja hozzá a **Mérési kategóriát** és a **Terméket** a Sorok szakaszhoz. A **Terméket** helyezze a **Mérési kategória** alá.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" lightbox="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-fields-meter-category.png" :::
1. Ezután adja hozzá a **Költségek** oszlopot az **Értékek** szakaszhoz. Használhatja a Felhasznált mennyiség oszlopot is a felhasználási egységek és tranzakciók adatainak lekéréséhez. Ez például lehet GB vagy óra. Vagy a költségek helyett a tranzakciókat is lekérheti különböző pénznemekben (például: USD, EUR, INR). Most már rendelkezik egy irányítópulttal, amely megjeleníti az erőforrás által használt összes szolgáltatást.
1. Adja hozzá a **Dátum** oszlopot az **Oszlopok** szakaszhoz. Ez a napi felhasználást jeleníti meg.
1. Az egyes hónapok oszlopaiban lévő **+** szimbólumokkal kibonthatja és összecsukhatja a megjelenő elemeket.  
    :::image type="content" source="./media/troubleshoot-ea-billing-issues-usage-file-pivot-tables/pivot-table-month-expand-collapse.png" alt-text="Az Excelben lévő nem formázott adatokat bemutató példa" :::

[!INCLUDE [Transform data before using large usage files](../../../includes/cost-management-billing-transform-data-before-using-large-usage-files.md)]

## <a name="next-steps"></a>Következő lépések

- [Költségek feltérképezése és elemzése költségelemzés használatával](../costs/quick-acm-cost-analysis.md).