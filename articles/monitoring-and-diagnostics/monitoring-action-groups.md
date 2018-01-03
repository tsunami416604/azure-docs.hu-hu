---
title: "Az Azure portálon művelet csoportok létrehozása és kezelése |} Microsoft Docs"
description: "Útmutató az Azure portálon művelet csoportok létrehozásához és kezeléséhez."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: ancav
ms.openlocfilehash: 05775415e210333cf63565e7b5b554d014f6ba23
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="create-and-manage-action-groups-in-the-azure-portal"></a>Az Azure portálon művelet csoportok létrehozása és kezelése
## <a name="overview"></a>Áttekintés ##
Ez a cikk bemutatja, hogyan az Azure portálon művelet csoportok létrehozásához és kezeléséhez.

A művelet csoportok konfigurálható azon műveletek listáját. Ezek a csoportok majd használható napló tevékenységriasztásokat definiálásakor. Ezek a csoportok majd minden egyes napló figyelmeztetés a meghatározásához, biztosítva, hogy ugyanazokat a műveleteket a rendszer minden alkalommal, amikor a tevékenység napló figyelmeztetés felhasználhatók.

Egy legfeljebb 10 minden művelet típusú lehet. Egyes műveletek során a következő tulajdonságok tevődik össze:

* **Név**: a művelet csoporton belül egyedi azonosítója.  
* **Művelet típusa**: SMS küldése, küldjön egy e-mailt, hívható meg olyan webhook, adatokat küldeni egy ITSM eszköz, hívja az Azure app vagy az Automation-forgatókönyv futtatása.
* **Részletek**: A megfelelő telefonszám, számot, e-mail címét, webhook URI vagy ITSM kapcsolódási adatait.

Művelet csoportok konfigurálása Azure Resource Manager-sablonok használatával kapcsolatos információkért lásd: [művelet csoport Resource Manager-sablonok](monitoring-create-action-group-with-resource-manager-template.md).

## <a name="create-an-action-group-by-using-the-azure-portal"></a>Egy művelet csoport létrehozása az Azure-portál használatával ##
1. Az a [portal](https://portal.azure.com), jelölje be **figyelő**. A **figyelő** panel összes figyelési beállítások és adatok az egyik nézetben összesíti.

    ![A "Figyelés" szolgáltatás](./media/monitoring-action-groups/home-monitor.png)
2. Az a **beállítások** szakaszban jelölje be **művelet csoportok**.

    ![A "Művelet csoportok" lap](./media/monitoring-action-groups/action-groups-blade.png)
3. Válassza ki **művelet csoport hozzáadása**, és töltse ki a mezőket.

    ![A "Művelet csoport hozzáadása" parancs](./media/monitoring-action-groups/add-action-group.png)
4. Adjon meg egy nevet a a **művelet csoportnév** mezőbe, majd írjon be egy nevet a **rövid név** mezőbe. A rövid nevét használja a teljes műveletet csoport neve helyett amikor ez a csoport értesítések küldése.

      ![A művelet csoport hozzáadása"párbeszédpanel](./media/monitoring-action-groups/action-group-define.png)

5. A **előfizetés** a jelenlegi előfizetés autofills mezőben. Ez az előfizetés, amelyben a művelet csoport mentett lesz.

6. Válassza ki a **erőforráscsoport** az a művelet csoport mentve.

7. Adja meg azon műveletek listáját, adja meg a minden egyes művelethez:

    a. **Név**: Adjon meg egy egyedi azonosítót ehhez a művelethez.

    b. **Művelet típusa**: válassza ki az SMS, e-mailek, webhook, Azure-alkalmazás, ITSM vagy Automation-forgatókönyv.

    c. **Részletek**: művelet típusa alapján, adjon meg egy telefonszámot, e-mail címét, webhook URI, az Azure app, ITSM kapcsolat, vagy Automation-runbook. A ITSM művelet, továbbá meg **munkaelem** és más mezők a ITSM eszköz szükséges. 

   > [!NOTE]
   > ITSM művelet ITSM kapcsolatot igényel. Megtudhatja, hogyan hozzon létre egy [ITSM kapcsolat](../log-analytics/log-analytics-itsmc-overview.md). ITSM művelet jelenleg csak napló Tevékenységriasztásokat működik. A más típusú Ez a művelet jelenleg nem műveletvégzés.

8. Válassza ki **OK** a művelet csoport létrehozásához.

## <a name="manage-your-action-groups"></a>A művelet csoportok kezelése ##
Egy művelet csoport létrehozása után is látható, az a **művelet csoportok** szakasza a **figyelő** panelen. Válassza ki a kezelni kívánt:

* Adja hozzá, szerkeszthet és eltávolíthat műveletek.
* A művelet csoport törlése.

## <a name="next-steps"></a>További lépések ##
* További információ [SMS riasztási viselkedés](monitoring-sms-alert-behavior.md).  
* Szerezzen egy [megismerni a műveletnapló riasztási webhook séma](monitoring-activity-log-alerts-webhook.md).  
* További információ [ITSM összekötő](../log-analytics/log-analytics-itsmc-overview.md)
* További információ [sebességkorlátozást](monitoring-alerts-rate-limiting.md) értesítésekről. 
* Első egy [tevékenység napló riasztások áttekintése](monitoring-overview-alerts.md), és megtudhatja, hogyan szeretné megkapni a riasztásokat.  
* Megtudhatja, hogyan [riasztások konfigurálása, ha az állapotfigyelő szolgáltatáshoz értesítést visszaküldi](monitoring-activity-log-alerts-on-service-notifications.md).
