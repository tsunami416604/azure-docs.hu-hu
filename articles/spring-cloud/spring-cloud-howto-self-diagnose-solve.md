---
title: Az Azure Spring Cloud szolgáltatásban felmerülő problémák önálló diagnosztizálása és megoldása
description: Ismerje meg, hogyan diagnosztizálhatja és megoldhatja a problémákat az Azure Spring Cloud-ban.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/29/2020
ms.openlocfilehash: 581d77a5568b9533d85ff476b63001c8a237bc4e
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457201"
---
# <a name="how-to-self-diagnose-and-solve-problems-in-azure-spring-cloud"></a>Az Azure Spring Cloud szolgáltatásban felmerülő problémák önálló diagnosztizálása és megoldása
Az Azure Spring Cloud Diagnostics egy interaktív felület, amely segítséget nyújt az alkalmazás hibakeresésében. Nem igényel konfigurálást. Ha problémákat tapasztal, az Azure Spring Cloud Diagnostics arra utal, hogy mi a probléma, és útmutatást nyújt a problémák elhárításához és megoldásához.

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy üzembe helyezett Azure Spring Cloud Service-példány. Az első lépésekhez kövesse [az alkalmazások Azure CLI-n keresztüli üzembe helyezését ismertető](spring-cloud-quickstart-launch-app-cli.md) útmutatót.
* Legalább egy alkalmazás már létre van hozva a szolgáltatási példányban.

## <a name="navigate-to-the-diagnostics-page"></a>Navigáljon a diagnosztika lapra
1. Jelentkezzen be az Azure portálra.
2. Nyissa meg az Azure Spring Cloud **Áttekintés** oldalát.
3. Az oldal bal oldalán található menüben nyissa meg a **problémák diagnosztizálását és megoldását** .

 ![Diagnosztizálás, problémamegoldás párbeszédpanel](media/spring-cloud-diagnose/diagnose-solve-dialog.png)

 ## <a name="search-logged-issues"></a>Naplózott problémák keresése
A probléma megoldásához írjon be egy kulcsszót, vagy kattintson a megoldás csoport lehetőségre a kategória összes elemének megismeréséhez.

 ![Keresési problémák](media/spring-cloud-diagnose/search-detectors.png)

A **konfigurációs kiszolgáló állapotának**, a **konfigurációs kiszolgáló**állapotának vagy a **konfigurációs kiszolgáló frissítési előzményeinek** kiválasztása különböző eredményeket fog megjeleníteni.

![Problémák beállításai](media/spring-cloud-diagnose/detectors-options.png)

Keresse meg a cél detektort, és kattintson rá a végrehajtáshoz. A diagnosztika összefoglalása az érzékelő végrehajtása után jelenik meg. A **teljes jelentés megtekintése** lehetőség kiválasztásával megtekintheti a diagnosztikai adatokat, vagy kattintson a **csempe megjelenítése** gombra a detektorok listájára való visszatéréshez.

 ![Összegző diagnosztika](media/spring-cloud-diagnose/summary-diagnostics.png)

A diagnosztikai adatok lapon módosíthatja a diagnosztikai időtartományt a jobb felső sarokban található vezérlővel. Ha további mérőszámokat vagy naplókat szeretne látni, kapcsolja be az egyes diagnosztikai adatokat. A metrikák és naplók 15 perces késéssel is rendelkezhetnek.

 ![Diagnosztika részletei](media/spring-cloud-diagnose/diagnostics-details.png)

Egyes eredmények kapcsolódó dokumentációt tartalmaznak.

 ![Kapcsolódó részletek](media/spring-cloud-diagnose/related-details.png)

## <a name="next-steps"></a>Következő lépések
* [Spring Cloud-erőforrások monitorozása riasztások és műveletcsoportok használatával](spring-cloud-tutorial-alerts-action-groups.md)
* [Az Azure Spring Cloud Service biztonsági vezérlői](spring-cloud-concept-security-controls.md)