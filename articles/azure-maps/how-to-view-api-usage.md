---
title: Az Azure Maps API-használat megtekintése |} A Microsoft Docs
description: Ismerje meg, a metrikák az Azure Maps API-hívások megtekintése a portálon.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f8977a0a50103b3c2935ee27a69a53e636f5aecb
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580395"
---
# <a name="view-azure-maps-api-usage"></a>Azure Maps API-használati adatok megtekintése

Ez a cikk bemutatja, hogyan az API-t az Azure Maps-fiók, a használati metrikáinak megtekintéséhez a [portál](https://portal.azure.com). A metrikákat egy testreszabható időtartam mentén kényelmesen használható grafikus formátumban jelennek meg.

## <a name="view-metric-snapshot"></a>Metrika pillanatkép megtekintése

Tekintheti meg néhány gyakori metrikák az **áttekintése** a Maps-fiók oldalán. Jelenleg látható *kérések teljes*, *az összes hiba*, és *rendelkezésre állási* választható időtartam alatt.

![Az Azure Maps Méretmetrikák – áttekintés](media/how-to-view-api-usage/portal-overview.png)

Ha testre kell szabnia az adott elemzéshez diagramokra továbbra is a következő szakaszban.

## <a name="view-detailed-metrics"></a>Részletes metrikák megtekintése

1. Jelentkezzen be az Azure-előfizetését a [portál](https://portal.azure.com).

2. Kattintson a **összes erőforrás** elemet a bal oldali menüben, és keresse meg a *Azure Maps-fiók*.

3. Ha a Maps-fiók meg nyitva, kattintson a a **metrikák** a bal oldali menüben.

4. Az a **metrikák** ablaktáblán választhat a következők egyikét:

    1. **Rendelkezésre állási** – amely bemutatja a *átlagos* API rendelkezésre állás egy időszakon belül.
    2. **Használati** – amely bemutatja hogyan a használati *száma* a fiókjához.

    ![Azure Maps-metrikák panelje](media/how-to-view-api-usage/portal-metrics.png)

5. Ezután választhat a *időtartomány* kattintva **(automatikus) az elmúlt 24 órából**. Alapértelmezés szerint az időtartomány 24 óra értéke. Gombra kattintva megjelenik a választható időintervallumok. Kiválaszthatja a *idő részletessége* is megjelenítheti az időt, majd *helyi* vagy *GMT* az azonos legördülő. Kattintson az **Alkalmaz** gombra.

    ![Az Azure Maps metrikák időtartomány](media/how-to-view-api-usage/time-range.png)

6. Miután hozzáadta a metrika, ezek közül **szűrő hozzáadása** többek között a tulajdonságokat, amelyek a metrika, és válassza ki a kívánt tulajdonság értéke látható a graph számára.

    ![Az Azure Maps metrikák szűrése](media/how-to-view-api-usage/filter.png)

7. Lehetősége van **alkalmazni a felosztás** a metrika a kiválasztott metrika tulajdonság alapján. Ez lehetővé teszi a gráf több diagramok, egyet-egyet mindegyik érték a tulajdonság felosztva. A következő képen látható az egyes színét felel meg a tulajdonság értéke, a diagram alján látható.

    ![Azure Maps-felosztás metrikák](media/how-to-view-api-usage/splitting.png)

8. Azt is megfigyelheti az ugyanazon a grafikonon több metrikát kattintva a **metrika hozzáadása** felül gombra.

## <a name="next-steps"></a>További lépések

További információ az Azure Maps API-k használatának nyomon követéséhez szeretné:

> [!div class="nextstepaction"]
> [Az Azure Maps REST API dokumentációja](https://docs.microsoft.com/rest/api/maps)
