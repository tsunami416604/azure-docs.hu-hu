---
title: Telepítése és a Log Analytics-nézetek használata az Azure Active Directory (előzetes verzió) |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti és használhatja a Log Analytics-nézetek az Azure Active Directory (előzetes verzió)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 10/01/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 11cc6a69b8072fca0639da5e517a39c22645710e
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300506"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Telepítése és a Log Analytics-nézetek használata az Azure Active Directory

Az Azure Active Directory Log Analytics-nézetek segítségével elemezheti és keresés az Azure AD-Tevékenységnaplók az Azure AD-bérlőben. Azure AD-tevékenység a naplófájlokban:

* Auditnaplók: A [naplók Tevékenységjelentés](concept-audit-logs.md) teszi elérhetővé a bérlőben végrehajtott minden tevékenység előzményeit.
* Bejelentkezési naplók: az a [bejelentkezési tevékenységek jelentésének](concept-sign-ins.md), megadhatja, hogy ki hajtotta végre a jelentett feladatokat, a naplók.

## <a name="prerequisites"></a>Előfeltételek

A Log Analytics-nézetek használatához az alábbiak szükségesek:

* Az Azure-előfizetés a Log Analytics-munkaterületet. Ismerje meg, hogyan [hozzon létre egy Log Analytics-munkaterület](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Először a lépéseket követve [útvonal az Azure AD-Tevékenységnaplók Log Analytics-munkaterület az](howto-integrate-activity-logs-with-log-analytics.md).
* Töltse le a nézeteket az [GitHub-adattár](https://aka.ms/AADLogAnalyticsviews) a helyi számítógépen.

## <a name="install-the-log-analytics-views"></a>Telepítse a Log Analytics nézetei

1. Keresse meg a Log Analytics-munkaterületre. Ehhez először keresse meg a [az Azure portal](https://portal.azure.com) válassza **minden szolgáltatás**. Típus **Log Analytics** a szövegmezőbe, és válassza a **Log Analytics**. Válassza ki a munkaterületet, akkor irányítja a tevékenységnaplókat, az Előfeltételek részeként.
2. Válassza ki **adatforrásnézet-tervezőből**, jelölje be **importálása** majd **fájl kiválasztása** a nézetek importálhatja a helyi számítógépről.
3. Válassza ki az előfeltételeket, és válassza ki a letöltött nézeteket **mentése** az Importálás gombra. Tegye meg a **Azure AD-fiók kiépítési események** nézet és a **bejelentkezési események** megtekintése.

## <a name="use-the-views"></a>A nézetek használata

1. Keresse meg a Log Analytics-munkaterületre. Ehhez először keresse meg a [az Azure portal](https://portal.azure.com) válassza **minden szolgáltatás**. Típus **Log Analytics** a szövegmezőbe, és válassza a **Log Analytics**. Válassza ki a munkaterületet, akkor irányítja a tevékenységnaplókat, az Előfeltételek részeként.

2. Ha egyszer már megkapta a munkaterületen, válassza ki a **munkaterület összefoglalás**. Az alábbi három nézet kell megjelennie:

    * **Az Azure AD fiók kiépítése események**: Ebben a nézetben látható a kiépítési tevékenységgel, például egy üzembe helyezett új felhasználóknak a számát, naplózás kapcsolatos jelentéseket kiépítési hibákhoz, azon felhasználók száma, frissítik és frissítési hibák és a felhasználók száma eltávolítjuk és a kapcsolódó hibák.    
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

* [Tevékenység elemzése a Log Analytics-naplók](howto-analyze-activity-logs-log-analytics.md)
* [Ismerkedés a Log Analytics használatával az Azure Portalon](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)
