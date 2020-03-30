---
title: A naplóelemzési nézetek telepítése és használata | Microsoft dokumentumok
description: További információ az Azure Active Directory naplóelemzési nézeteinek telepítéséről és használatáról
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
ms.openlocfilehash: b17026e4cfbe69e36c8e459aa259fe16b1c9d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014418"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Az Azure Active Directory naplóelemzési nézeteinek telepítése és használata

Az Azure Active Directory-naplóelemzési nézetek segítségével elemezheti és keresheti az Azure AD-tevékenységnaplókat az Azure AD-bérlőben. Az Azure AD tevékenységnaplók a következők:

* Naplók naplózása: A [naplótevékenység-jelentés](concept-audit-logs.md) hozzáférést biztosít a bérlőben végrehajtott összes feladat előzményeihez.
* Bejelentkezési naplók: A [bejelentkezési tevékenység jelentéssel](concept-sign-ins.md)meghatározhatja, hogy ki hajtotta végre a naplókban jelentett feladatokat.

## <a name="prerequisites"></a>Előfeltételek

A naplóelemzési nézetek használatához a következőkre van szükség:

* A Log Analytics munkaterület az Azure-előfizetésben. További információ a [Log Analytics-munkaterület létrehozásáról.](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)
* Először hajtsa végre az [Azure AD-tevékenységnaplók a Log Analytics-munkaterületre való továbbításához](howto-integrate-activity-logs-with-log-analytics.md)szükséges lépéseket.
* Töltse le a nézeteket a [GitHub-tárházból](https://aka.ms/AADLogAnalyticsviews) a helyi számítógépre.

## <a name="install-the-log-analytics-views"></a>A naplóelemzési nézetek telepítése

1. Nyissa meg a Log Analytics-munkaterületet. Ehhez először keresse meg az [Azure Portalt,](https://portal.azure.com) és válassza a **Minden szolgáltatás**lehetőséget. Írja be a **Log Analytics** parancsot a szövegmezőbe, és válassza a **Log Analytics-munkaterületeket.** Az előfeltételek részeként válassza ki azt a munkaterületet, ahhoz a tevékenységnaplókat irányította.
2. Válassza **a Nézettervező**lehetőséget, válassza **az Importálás** lehetőséget, majd a Fájl kiválasztása **lehetőséget** a nézetek helyi számítógépről történő importálásához.
3. Jelölje ki az előfeltételekből letöltött nézeteket, és válassza a **Mentés** gombot az importálás mentéséhez. Ehhez az **Azure AD-fiók létesítési események** nézet és a **Bejelentkezési események** nézetben.

## <a name="use-the-views"></a>A nézetek használata

1. Nyissa meg a Log Analytics-munkaterületet. Ehhez először keresse meg az [Azure Portalt,](https://portal.azure.com) és válassza a **Minden szolgáltatás**lehetőséget. Írja be a **Log Analytics** parancsot a szövegmezőbe, és válassza a **Log Analytics-munkaterületeket.** Az előfeltételek részeként válassza ki azt a munkaterületet, ahhoz a tevékenységnaplókat irányította.

2. Miután a munkaterületen vagy, válassza a **Munkaterület összegzése**lehetőséget. A következő három nézetnek kell megtekintenie:

    * **Azure AD-fiók létesítési események:** Ez a nézet a naplózási létesítési tevékenységgel kapcsolatos jelentéseket jeleníti meg, például a kiépített új felhasználók számát és a kiépítési hibákat, a frissített felhasználók számát és a frissítési hibákszámát, valamint a kiépített és a megfelelő hibák számát.    
    * **Bejelentkezési események:** Ez a nézet a bejelentkezési tevékenység figyelésével kapcsolatos legfontosabb jelentéseket jeleníti meg, például az alkalmazás, a felhasználó, az eszköz, valamint a bejelentkezések számát az idő múlásával nyomon követő összefoglaló nézetet.

3. Jelölje ki a nézetek egyikét, ha be szeretne ugrani az egyes jelentésekbe. A jelentés iimént bármelyik paraméterre vonatkozóan is beállíthat riasztásokat. Például állítson be egy riasztást minden alkalommal, amikor bejelentkezési hiba történik. Ehhez először válassza ki a **Bejelentkezési események** nézetet, válassza a **Bejelentkezési hibák idővel jelentés,** majd válassza **az Analytics** lehetőséget a részletek lap megnyitásához, a jelentés mögötti tényleges lekérdezéssel. 

    ![Részletek](./media/howto-install-use-log-analytics-views/details.png)


4. Válassza **a Riasztás beállítása**lehetőséget, majd a Ha az egyéni **naplókeresés logikai &lt;definiálatlan,&gt; ** válassza a **Riasztási feltételek** szakaszban lehetőséget. Mivel bejelentkezési hiba esetén értesítést szeretnénk kapni, állítsa az alapértelmezett riasztási logika **küszöbértékét** **1-re,** majd kattintson a Kész **gombra.** 

    ![Jellogika konfigurálása](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Adja meg a riasztás nevét és leírását, és állítsa a súlyosságát **Figyelmeztetés beállításra.**

    ![Szabály létrehozása](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Jelölje ki a riasztásra vonatkozó műveletcsoportot. Ez általában lehet egy csapat, amelyet e-mailben vagy szöveges üzenetben szeretne értesíteni, vagy lehet automatizált feladat webhookok, runbookok, függvények, logikai alkalmazások vagy külső ITSM-megoldások használatával. Ismerje meg, hogyan [hozhat létre és kezelhet műveletcsoportokat az Azure Portalon.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)

7. A riasztás létrehozásához válassza a **Figyelmeztetési szabály létrehozása** lehetőséget. Most antól minden bejelentkezési hiba esetén figyelmeztetést kap.

## <a name="next-steps"></a>További lépések

* [Tevékenységnaplók elemzése az Azure Monitor-naplókkal](howto-analyze-activity-logs-log-analytics.md)
* [Ismerkedés az Azure Monitor-naplókkal az Azure Portalon](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)