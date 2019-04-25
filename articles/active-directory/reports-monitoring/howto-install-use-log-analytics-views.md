---
title: Telepítése és a log analytics-nézetek használata az Azure Active Directoryhoz |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti és használhatja a log analytics nézetei az Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92a5d9539d254eb7d52e3277691c9d8ff5a41821
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60286735"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Telepítése és a log analytics-nézetek használata az Azure Active Directory

A Azure Active Directory log analytics nézetei segít elemez, és keresse az Azure AD-Tevékenységnaplók az Azure AD-bérlőben. Azure AD-tevékenység a naplófájlokban:

* Naplófájlok: A [naplók Tevékenységjelentés](concept-audit-logs.md) teszi elérhetővé a bérlőben végrehajtott minden tevékenység előzményeit.
* Bejelentkezési naplók: Az a [bejelentkezési tevékenységek jelentésének](concept-sign-ins.md), megadhatja, hogy ki hajtotta végre a jelentett feladatokat, a naplók.

## <a name="prerequisites"></a>Előfeltételek

A log analytics nézetek használatához az alábbiak szükségesek:

* Az Azure-előfizetés a Log Analytics-munkaterületet. Ismerje meg, hogyan [hozzon létre egy Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Először a lépéseket követve [útvonal az Azure AD-Tevékenységnaplók Log Analytics-munkaterület az](howto-integrate-activity-logs-with-log-analytics.md).
* Töltse le a nézeteket az [GitHub-adattár](https://aka.ms/AADLogAnalyticsviews) a helyi számítógépen.

## <a name="install-the-log-analytics-views"></a>Telepítse a log analytics nézetei

1. Keresse meg a Log Analytics-munkaterületre. Ehhez először keresse meg a [az Azure portal](https://portal.azure.com) válassza **minden szolgáltatás**. Típus **Log Analytics** a szövegmezőbe, és válassza a **Log Analytics-munkaterületek**. Válassza ki a munkaterületet, akkor irányítja a tevékenységnaplókat, az Előfeltételek részeként.
2. Válassza ki **adatforrásnézet-tervezőből**, jelölje be **importálása** majd **fájl kiválasztása** a nézetek importálhatja a helyi számítógépről.
3. Válassza ki az előfeltételeket, és válassza ki a letöltött nézeteket **mentése** az Importálás gombra. Tegye meg a **Azure AD-fiók kiépítési események** nézet és a **bejelentkezési események** megtekintése.

## <a name="use-the-views"></a>A nézetek használata

1. Keresse meg a Log Analytics-munkaterületre. Ehhez először keresse meg a [az Azure portal](https://portal.azure.com) válassza **minden szolgáltatás**. Típus **Log Analytics** a szövegmezőbe, és válassza a **Log Analytics-munkaterületek**. Válassza ki a munkaterületet, akkor irányítja a tevékenységnaplókat, az Előfeltételek részeként.

2. Ha egyszer már megkapta a munkaterületen, válassza ki a **munkaterület összefoglalás**. Az alábbi három nézet kell megjelennie:

    * **Azure AD-fiók kiépítése események**: Ebben a nézetben látható a kiépítési tevékenységgel, például egy üzembe helyezett új felhasználóknak a számát, naplózás kapcsolatos jelentéseket kiépítési hibákhoz, azon felhasználók száma, frissítik és frissítési hibák és a szolgáltatáskulcs felhasználók és a megfelelő hibák számát.    
    * **Bejelentkezési események**: Ebben a nézetben látható a leginkább releváns jelentések kapcsolódó bejelentkezési tevékenységeket, például az alkalmazás, felhasználó, eszköz, valamint nyomon követése a bejelentkezések száma idővel lemezkapacitásáról által bejelentkezések figyelése.

3. Válassza ki, ezek a nézetek az Ugrás az egyes jelentések egyikét. A jelentés paramétereinek valamelyik is beállíthat riasztásokat. Például állítsa a riasztás minden alkalommal, amikor bejelentkezési hiba van. Ehhez először válassza ki a **bejelentkezési események** nézetben válassza **bejelentkezési hibák az idő függvényében** jelentést, majd **Analytics** nyissa meg a részleteket, a tényleges lekérdezést mögött a jelentést. 

    ![Részletek](./media/howto-install-use-log-analytics-views/details.png)


4. Válassza ki **riasztási beállítása**, majd válassza ki **minden alkalommal, amikor az egyéni log Search &lt;nem definiált logika&gt;**  alatt a **riasztási feltételek** szakaszban. Mivel a kívánt riasztást, amikor egy bejelentkezési hiba, állítsa be a **küszöbérték** az alapértelmezett riasztási logika **1** , majd **kész**. 

    ![Jellogika konfigurálása](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Adjon meg egy nevet és leírást a riasztást, és állítsa a súlyosság **figyelmeztetés**.

    ![Szabály létrehozása](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Válassza ki a műveletcsoport a riasztáshoz. Általánosságban elmondható vagy egy értesítendő e-mail vagy szöveges üzeneten keresztül csapat is lehet, vagy webhookok, a runbookok, a functions, a logic apps vagy a külső ITSM-megoldások használatával egy automatizált feladat lehet. Ismerje meg, hogyan [létrehozása és kezelése az Azure Portalon Műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Válassza ki **riasztási szabály létrehozása** a riasztás létrehozásához. Most, értesítést kap minden alkalommal, amikor bejelentkezési hiba van.

## <a name="next-steps"></a>További lépések

* [Tevékenységnaplók az Azure Monitor-naplók elemzése](howto-analyze-activity-logs-log-analytics.md)
* [Ismerkedés az Azure Monitor-naplók az Azure Portalon](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)