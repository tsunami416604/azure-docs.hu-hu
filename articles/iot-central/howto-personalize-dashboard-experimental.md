---
title: Azure IoT Central személyes irányítópultok kialakítása |} A Microsoft Docs
description: Egy felhasználó megtudhatja, hogyan hozhat létre és kezelhet a személyes irányítópultok.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a7de9126716437e4e90185f46c9cf95f7de20d40
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57220535"
---
# <a name="create-and-manage-personal-dashboards"></a>Hozzon létre, és a személyes irányítópultok kezelése

A **irányítópult** az oldal, amely betölti, amikor először keresi fel az alkalmazást. A **builder** az alkalmazásban határozza meg az alapértelmezett irányítópult minden felhasználó számára. Az alapértelmezett irányítópult lecserélheti a saját, személyre szabott alkalmazás irányítópult. Számos olyan irányítópultokat, amelyek más adatok megjelenítése és a kettő közötti váltás rendelkezhet.

## <a name="create-dashboard"></a>irányítópult létrehozása

Az alábbi képernyőképen az irányítópult látható a létrehozott alkalmazás a **minta Contoso** sablont. Az alapértelmezett irányítópult lecserélheti egy egyéni irányítópultomat. Ehhez kattintson **+ új** tetején, az oldal jobb.

![A "Contoso minta" sablonon alapuló alkalmazások irányítópult](media/howto-personalize-dashboard-experimental/defaultdashboard.png)

Kattintson a **+ új**, megnyílik az irányítópult-szerkesztő. A szerkesztőben is adja meg az irányítópult nevét és a kiválasztott cikkek a könyvtárból. A függvénytár tartalmazza a csempék és irányítópult primitívek segítségével testre szabhatja az irányítópultot.

![Irányítópult-könyvtár](media/howto-personalize-dashboard-experimental/dashboardeditor.png)

Például hozzáadhat egy **eszközbeállítások, illetve tulajdonságok** csempe beállítások és tulajdonságok értékeit egy Ön által felügyelt eszközök megjelenítéséhez. Ehhez először válassza ki a **eszköz sablon** válassza ki a **eszközpéldány**. A csempe majd adjon meg egy címet és a válassza a **beállítás** vagy egy **tulajdonság** megjelenítéséhez. Az alábbi képernyőfelvételen a **sebesség ventilátor** a csempe hozzáadása a beállítást. Kattintson a **kész** menteni a módosítást az irányítópulton.

![Részletes beállítások és tulajdonságok "Konfigurálása eszköz részletei" képernyő](media/howto-personalize-dashboard-experimental/dashboardsetting.png)

Ekkor a személyes irányítópulton megtekintheti, megjelenik az új csempe a **sebesség ventilátor** az eszköz beállítása:

![A megjelenített beállítások és tulajdonságok csempe "Az irányítópult" lap](media/howto-personalize-dashboard-experimental/personaldashboard.png)

Áttekintheti a többi csempe a könyvtárban a felderítendő típusok további testreszabása a személyes irányítópultok.

## <a name="manage-dashboards"></a>Irányítópultok kezelése

Használhat több személyes irányítópultok és a kettő közötti váltás, vagy válassza ki az alapértelmezett irányítópult:

![Kapcsoló irányítópult](media/howto-personalize-dashboard-experimental/switchdashboards.png)

A személyes irányítópultok szerkesztheti, és törölje őket már nincs szüksége:

![Irányítópult törlése](media/howto-personalize-dashboard-experimental/managedashboards.png)

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre és kezelheti a személyes irányítópultok, akkor a következőket teheti:

> [!div class="nextstepaction"]
> [Az alkalmazás-beállítások kezelése](howto-manage-preferences.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
