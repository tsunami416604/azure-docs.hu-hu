---
title: Hibák vizsgálata az irányítópult használatával
description: Ez a dokumentum az irányítópult használatával végzett hibák vizsgálatával kapcsolatos információkat tartalmaz
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540621"
---
# <a name="error-investigation-using-the-dashboard"></a>Hibák vizsgálata az irányítópult használatával

Ez az oldal az ITSM-összekötő irányítópultjának információit tartalmazza. Ez az irányítópult segítséget nyújt az ITSM-összekötő állapotának vizsgálatához.

## <a name="how-to-view-the-dashboard"></a>Az irányítópult megtekintése

Az irányítópulton található hibák megtekintéséhez kövesse a következő lépéseket:

1. Az **összes erőforrás** területen keresse meg a **ügyfélszolgálati (*a munkaterület neve*)**:

   ![A Azure Portal legutóbbi erőforrásait bemutató képernyőkép.](media/itsmc-definition/create-new-connection-from-resource.png)

2. A bal oldali ablaktábla **munkaterület-adatforrások** területén válassza a **ITSM-kapcsolatok** elemet:

   ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok menüpontot.](media/itsmc-overview/add-new-itsm-connection.png)

3. A bal oldali mező **Összegzés** területén **it-szolgáltatásmenedzsmenti csatoló** válassza az **Összefoglalás megtekintése** lehetőséget:

    ![A megtekintés összegzését bemutató képernyőkép.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. A bal oldali mező **Összegzés** területén **it-szolgáltatásmenedzsmenti csatoló** kattintson a gráfra:

    ![A diagramot bemutató képernyőkép.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Az irányítópult használatával áttekintheti az összekötő állapotát és hibáit.
    ![Az összekötő állapotát megjelenítő képernyőkép.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Irányítópult-elemek

Az irányítópult a ITSM eszközre a jelen összekötő használatával továbbított riasztásokkal kapcsolatos információkat tartalmaz.
Az irányítópult négy részre oszlik:

1. Munkaelem létrehozva: a gráf és az alábbi táblázat a munkaelemek számát tartalmazza típus szerint. Ha a diagramra vagy a táblázatra kattint, további részleteket láthat a munkaelemekről.
    ![Képernyőkép, amely a létrehozott munkaelemet jeleníti meg.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Érintett számítógépek: a táblák tartalmazzák a konfigurációs elemeket létrehozó konfigurációs elemek részleteit.
    A táblák soraira kattintva további részleteket is megtudhat a konfigurációs elemekről.
    A tábla csak korlátozott számú sort tartalmaz, ha meg szeretné tekinteni az összes listát, amelyre rákattinthat az "összes megjelenítése" lehetőségre.
    ![Az érintett számítógépeket bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Összekötő állapota: a gráf és az alábbi táblázat az összekötő állapotával kapcsolatos üzeneteket tartalmaz. A táblázat soraiban található gráfra kattintva további részleteket tudhat meg az összekötő állapota üzenetekről.
    A tábla csak korlátozott számú sort tartalmaz, ha meg szeretné tekinteni az összes listát, amelyre rákattinthat az "összes megjelenítése" lehetőségre.
    ![Az összekötő állapotát megjelenítő képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Riasztási szabályok: a táblák tartalmazzák az észlelt riasztási szabályok számával kapcsolatos információkat.
    A táblák soraira kattintva további részleteket is megtudhat az észlelt szabályokról.
    A tábla csak korlátozott számú sort tartalmaz, ha meg szeretné tekinteni az összes listát, amelyre rákattinthat az "összes megjelenítése" lehetőségre.
    ![A riasztási szabályokat bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)