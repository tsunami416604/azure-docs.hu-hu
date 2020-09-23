---
title: Csempék hozzáadása az Azure IoT Central irányítópulthoz | Microsoft Docs
description: A Builder segítségével megtudhatja, hogyan konfigurálhatja az alapértelmezett Azure IoT Central alkalmazás-irányítópultot csempék használatával.
author: Haley-Rowland
ms.author: harowl
ms.date: 05/27/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: beeb771ea5053dd0ad867a7568aa64bbb2d0b4ed
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985292"
---
# <a name="configure-the-application-dashboard"></a>Az alkalmazás irányítópultjának konfigurálása

Az **irányítópult** az első lap, amely akkor jelenik meg, amikor egy IoT Central alkalmazáshoz csatlakozik. Ha az alkalmazást az iparágban központilag megcélzott [alkalmazás-sablonokból](./concepts-app-templates.md)hozza létre, az alkalmazásnak előre definiált irányítópulttal kell kezdődnie. Ha egyéni [alkalmazás-sablonból](./concepts-app-templates.md)hozza létre az alkalmazást, az irányítópulton néhány tippet láthat a kezdéshez.

> [!TIP]
> A felhasználók az alapértelmezett alkalmazás-irányítópulton kívül [több irányítópultot is létrehozhatnak](howto-create-personal-dashboards.md) . Ezek az irányítópultok csak a felhasználó személyes tagjai, vagy az alkalmazás összes felhasználója között megoszthatók.  

## <a name="add-tiles"></a>Csempék hozzáadása

Az alábbi képernyőfelvételen az **egyéni alkalmazás** sablonjában létrehozott alkalmazás irányítópultja látható. Ha testre szeretné szabni az aktuális irányítópultot, válassza a **Szerkesztés**lehetőséget, ha egyéni személyes vagy megosztott irányítópultot szeretne hozzáadni, válassza az **új**lehetőséget:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png" alt-text="Az egyéni alkalmazás sablonján alapuló alkalmazások irányítópultja":::

A **Szerkesztés** vagy az **új**lehetőség kiválasztása után az irányítópult *szerkesztési* módban van. Az irányítópult **szerkesztése** panelen található eszközök segítségével csempéket adhat hozzá az irányítópulthoz, és saját maga is testreszabhatja és eltávolíthatja a csempéket az irányítópulton. Például egy **telemetria** csempe hozzáadásához egy vagy több eszköz által jelentett aktuális hőmérséklet megjelenítéséhez:

1. Az **irányítópult szerkesztése** panelen válasszon ki egy **eszközt**.
1. Válasszon ki egy vagy több eszközt az **eszközök** legördülő menüben a csempén való megjelenítéshez. Ekkor megjelenik az eszközökön elérhető telemetria, tulajdonságok és parancsok.
1. Válassza a **hőmérséklet** lehetőséget a telemetria szakaszban, majd kattintson a **csempe hozzáadása**lehetőségre. A csempe most már az irányítópulton látható, ahol módosíthatja a vizualizációt, átméretezheti a csempét, és konfigurálhatja:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/device-details.png" alt-text="Hőmérséklet-telemetria csempe hozzáadása az irányítópulthoz":::

Ha befejezte a csempék hozzáadását és testreszabását az irányítópulton, válassza a **Mentés**lehetőséget.

## <a name="customize-tiles"></a>Csempék testreszabása

Az irányítópulton egy csempe testreszabásához szerkesztési módban kell lennie. Az elérhető testreszabási beállítások a [csempe típusától](#tile-types)függenek:

* A csempe vonalzó ikonja lehetővé teszi a vizualizáció módosítását. A vizualizációk közé tartoznak a sorok, a legutóbbi ismert értékek és a Heat maps.

* A négyzet ikon segítségével átméretezheti a csempét.

* A fogaskerék ikon lehetővé teszi a vizualizáció konfigurálását. A Diagramos vizualizációk esetében például megadhatja a jelmagyarázat és a tengelyek megjelenítését, és kiválaszthatja a nyomtatandó időtartományt.

## <a name="tile-types"></a>Csempe típusai

A következő táblázat ismerteti az irányítópulthoz felvehető különböző típusú csempéket:

| Csempe             | Leírás |
| ---------------- | ----------- |
| Markdown         | A Markdown csempéi olyan csempék, amelyek fejléceket és leírásokat jelenítenek meg a Markdown használatával formázott szövegekben. Az URL-cím lehet relatív hivatkozás az alkalmazás egy másik oldalára, vagy egy külső helyre mutató abszolút hivatkozás.|
| Kép            | A képcsempék egy egyéni rendszerképet jelenítenek meg, és megadhatók. Az URL-cím lehet relatív hivatkozás az alkalmazás egy másik oldalára, vagy egy külső helyre mutató abszolút hivatkozás.|
| Címke            | A felirat csempék egyéni szöveget jelenítenek meg az irányítópulton. Megadhatja a szöveg méretét. A címke csempével fontos információkat adhat hozzá az irányítópulthoz, például a leírásokat, a kapcsolattartási adatokat vagy a súgót.|
| Darabszám            | A számlálási csempék az eszközcsoport eszközeinek számát jelenítik meg.|
| Térkép              | A térképes csempék egy vagy több eszköz helyét jelenítik meg a térképen. Akár 100 pontot is megjeleníthet egy eszköz tartózkodási előzményeiről. Megjelenítheti például a mintavételes útvonalat, ahol egy eszköz az elmúlt héten van.|
| KPI              |  A KPI-csempék egy vagy több eszköz összesített telemetria-értékeit jelenítik meg egy adott időszakban. A segítségével például megjelenítheti az elmúlt órában egy vagy több eszközre vonatkozóan elért maximális hőmérsékletet és nyomást.|
| Vonaldiagram       | A vonalas diagram csempék egy vagy több összesített telemetria-értéket ábrázolnak egy adott időszakra vonatkozóan egy vagy több eszközhöz. Megjeleníthet például egy diagramot, amely az elmúlt órában egy vagy több eszköz átlagos hőmérsékletét és terhelését ábrázolja.|
| Sávdiagram        | A oszlopdiagram-csempék egy vagy több összesített telemetria-értéket ábrázolnak egy adott időszakra vonatkozóan egy vagy több eszközhöz. Megjeleníthet például egy sávdiagram, amely egy vagy több eszköz átlagos hőmérsékletét és terhelését jeleníti meg az elmúlt órában.|
| Tortadiagram        | A tortadiagram csempéi egy vagy több összesített telemetria-értéket jelenítenek meg egy vagy több eszközhöz egy adott időszakra vonatkozóan.|
| Intenzitástérkép         | A Heat Map csempék egy vagy több, színként jelölt eszköz adatait jelenítik meg.|
| Utolsó ismert érték | Az utolsó ismert érték csempék egy vagy több eszköz legújabb telemetria-értékeit jelenítik meg. Ezt a csempét használhatja például egy vagy több eszköz legutóbbi hőmérséklet-, nyomás-és páratartalom-értékének megjelenítésére. |
| Események előzményei    | Az esemény előzményei csempék az adott időszakon belül megjelenítik az adott eszköz eseményeit. Használhatja például, hogy megjelenítse az összes nyitott és záró eseményt az elmúlt órában egy vagy több eszközhöz.|
| Tulajdonság         |  A tulajdonságok csempéi egy vagy több eszköz tulajdonságainak és Felhőbeli tulajdonságainak aktuális értékét jelenítik meg. Ezt a csempét használhatja például az eszköz tulajdonságainak, például az eszköz gyártójának vagy belső vezérlőprogram-verziójának a megjelenítéséhez. |

Jelenleg legfeljebb 10 eszközt adhat hozzá több eszközt támogató csempék hozzáadásához.

### <a name="customizing-visualizations"></a>Vizualizációk testreszabása

Az összesített értékeket megjelenítő csempék esetében válassza a telemetria típusa melletti fogaskerék ikont a **diagram konfigurálása** panelen az Összesítés kiválasztásához. Az átlag, a Sum, a maximum, a minimum és a darabszám közül választhat.

A vonalas diagramok, a sávdiagramok és a tortadiagramok esetében testreszabhatja a különböző telemetria-értékek színét. Válassza a paletta ikont a testreszabni kívánt telemetria mellett:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/color-customization.png" alt-text="Telemetria érték színének módosítása":::

A karakterlánc-tulajdonságokat vagy telemetria értékeket megjelenítő csempék esetében kiválaszthatja, hogyan jelenjen meg a szöveg. Ha például az eszköz egy karakterlánc-tulajdonságban tárolja az URL-címet, akkor megjeleníthető hivatkozásként. Ha az URL-cím egy képre hivatkozik, az utolsó ismert érték vagy tulajdonság csempén jelenítheti meg a képet. A karakterlánc megjelenítésének módosításához a csempe konfigurációjában válassza a fogaskerék ikont a telemetria típusa vagy tulajdonsága mellett:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/string-customization.png" alt-text="Karakterlánc megjelenítésének módosítása csempén":::

A numerikus **KPI**, az **utolsó ismert érték**és a **tulajdonságlapok** esetében feltételes formázást használhat a csempe színének a jelenlegi érték alapján történő testreszabásához. A feltételes formázás hozzáadásához válassza a **Konfigurálás** lehetőséget a csempén, majd válassza a **feltételes formázás** ikont az érték mellett a testreszabáshoz:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-1.png" alt-text="A csempék konfigurálási beállításának megkeresését bemutató képernyőkép, majd a feltételes formázás ikonja":::

Adja meg a feltételes formázási szabályokat:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-2.png" alt-text="Az átlagos folyamat feltételes formázási szabályait bemutató képernyőkép. Három szabály van – a THA 20 értéke zöld, a 50-nál kevesebb sárga, míg a 50-es minden más vörös":::

Az alábbi képernyőfelvételen a feltételes formázási szabály hatása látható:

:::image type="content" source="media/howto-add-tiles-to-your-dashboard/conditional-formatting-3.png" alt-text="Képernyőfelvétel: a sárga háttérszín az átlagos vízfolyás csempén. A csempe száma 40,84":::

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan konfigurálhatja az Azure IoT Central alapértelmezett alkalmazás-irányítópultját, [megtudhatja, hogyan hozhat létre személyes irányítópultot](howto-create-personal-dashboards.md).
