---
title: Azure Security Center fő irányítópultja vagy az "áttekintés" oldal
description: További információ a Security Center áttekintése oldal funkcióiról
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: cc52610eacc3916b7a8978cba17a1db3f3d50686
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447681"
---
# <a name="azure-security-centers-overview-page"></a>Azure Security Center áttekintése lap

A Azure Security Center megnyitásakor az Áttekintés oldalon megjelenik az első lap. 

:::image type="content" source="media/overview-page/overview.png" alt-text="A Security Center áttekintő lapja":::

Felderítheti és elemezheti a számítási feladatok biztonságát, valamint azonosíthatja és csökkentheti a kockázatokat az Security Center áttekintése oldalon.

Az Áttekintés egy egységes nézetet biztosít a hibrid Felhőbeli számítási feladatok biztonsági állapotáról. Emellett a biztonsági riasztásokat, a lefedettségi adatokat és egyebeket is megjeleníti.


## <a name="features-of-the-overview-page"></a>Az Áttekintés oldal funkciói

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="A Security Center áttekintő lapja":::

A **felső menüsor** a következőket kínálja:
- **Előfizetések** – a gombra kattintva megtekintheti és szűrheti az előfizetések listáját. A Security Center úgy állítja be a kijelzőt, hogy tükrözze a kiválasztott előfizetések biztonsági állapotát.
- **Újdonságok –** megnyithatja a [kibocsátási megjegyzéseket](release-notes.md) , így naprakészen tarthatja az új funkciókat, hibajavításokat és elavult funkciókat.
- A csatlakoztatott felhőalapú fiókok **magas szintű száma** az alábbi fő csempén található információk kontextusának megjelenítéséhez. Valamint az aktív javaslatok és riasztások száma.
    További információ az AWS- [fiókok](quickstart-onboard-aws.md) és a [GCP-projektek](quickstart-onboard-gcp.md)csatlakoztatásáról.


A lap közepén **négy központi csempe**található, melyek mindegyike egy dedikált irányítópultra mutat, további részletekért:
- **Biztonságos pontszám** – a Security Center folyamatosan értékeli az erőforrásokat, az előfizetéseket és a szervezetet a biztonsági problémákhoz. Ezután összesíti az összes megállapítást egyetlen pontszámba, így eldöntheti, hogy az aktuális biztonsági helyzet: minél magasabb a pontszám, annál alacsonyabb az azonosított kockázati szint. [További információ](secure-score-security-controls.md).
- **Megfelelőség** – a Security Center az Azure-környezet folyamatos felmérése alapján betekintést nyújt a megfelelőségi állapotba. A Security Center az ajánlott biztonsági eljárásoknak megfelelően elemzi a hibrid felhőalapú környezet kockázati tényezőit. Ezek az értékelések a megfelelőségi vezérlőkre vannak leképezve a szabványok támogatott készletében. [További információ](security-center-compliance-dashboard.md).
- **Azure Defender** – ez a felhőalapú munkaterhelés-védelmi platform (CWPP), amely az Azure-beli és a hibrid számítási feladatok speciális, intelligens és védett Security Centeron belül van integrálva. A csempén látható a csatlakoztatott erőforrások (a jelenleg kiválasztott előfizetések esetében) és a legutóbbi riasztások lefedettsége, súlyosság szerint kódolva. [További információ](azure-defender.md).
- **Leltár** – a csempe a nem figyelt virtuális gépek számát és a biztonsági CenterBen által figyelt erőforrások egyszerű barométerét jeleníti meg. [További információ](asset-inventory.md).


Az **Áttekintés** panel testreszabott elemeket kínál a környezethez, többek között a következőket:
- A legtöbbet megtámadott erőforrás
- A biztonsági pontszámok, amelyek a legnagyobb potenciállal rendelkeznek a biztonságos pontszám növeléséhez
- A legtöbb erőforrást érintő aktív javaslatok
- Legutóbbi blogbejegyzések Azure Security Center szakértők által

## <a name="next-steps"></a>Következő lépések

Ez az oldal a Security Center áttekintése lapot mutatta be. A kapcsolódó információk a következő témakörben találhatók:

- [Erőforrások megismerése és kezelése az eszközök leltározásával és felügyeleti eszközeivel](asset-inventory.md)
- [Biztonságos pontszám Azure Security Center](secure-score-security-controls.md)