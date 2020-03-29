---
title: Szabályok konfigurálása és riasztások kezelése
description: A szabályok konfigurálása és a riasztások kezelése a FarmBeats-ben
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 02373da114b6dc4663cd3ffc9a0780ac6a7a9e23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75482984"
---
# <a name="configure-rules-and-manage-alerts"></a>Szabályok konfigurálása és riasztások kezelése

Az Azure FarmBeats lehetővé teszi, hogy az üzleti logika alapján hozzon létre szabályokat, a farmban telepített érzékelőkből és eszközökből származó érzékelőadatok on kívül. A szabályok riasztást váltanak ki a rendszerben, ha az érzékelő értékei átlépnek egy küszöbértéket. A küszöbértékek után létrehozott riasztások megtekintésével és elemzésével gyorsan eljátszhatja a problémákat, és megkaphatja a szükséges megoldásokat.

## <a name="create-rule"></a>Szabály létrehozása

1. A kezdőlapon nyissa meg a Szabályok ( Rules ) **(Szabályok) lapot.**
2. Válassza az **Új szabály lehetőséget.** Megjelenik az Új szabály ablak.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Írja be a **szabály nevét** és a **szabály leírását,** majd válasszon ki egy farmot a **Farm kiválasztása** legördülő menüből.
4. Írja be a farm nevét, ha ki szeretné jelölni, hogy a Farm és a **Feltételek** szakasz ugyanabban az ablakban jelenik meg.  

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. A **Feltételek mezőbe**írja be a **Mérték**, **Operátor** és **Érték**értékét.
6. Írja be a mérték nevét a **Mérték** legördülő menübe.
7. Válassza a **+Feltétel hozzáadása lehetőséget,** ha további feltételeket szeretne hozzáadni a szabályhoz.
8. Válassza ki a **Súlyossági szintet**.
9. A **Művelet alkalmazásban**kapcsolja be az **E-mail engedélyezve** a váltógombot az e-mail értesítések engedélyezéséhez.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Adja meg azokat az **e-mail címeket,** amelyekre el szeretné küldeni az e-mail értesítést, valamint az **e-mail tárgya** és a **További megjegyzések**.  
11. A **Szabály állapota**mezőben kapcsolja be az **Engedélyezve** váltógombot a szabály engedélyezéséhez vagy letiltásához.
    Megtekintheti a szabály által érintett eszközök számát.
12. A szabály létrehozásához válassza az **Alkalmaz** lehetőséget.

## <a name="view-rule"></a>Szabály megtekintése

A **Farm** lap az elérhető szabályok listáját jeleníti meg. Jelöljön ki egy **szabálynevet**. Az ablak a kiválasztott szabályra vonatkozó következő részleteket jeleníti meg:
 - Szabály neve
 - Hivatkozás arra a farmra, amelyhez a szabály társítva van
 - Létrehozás dátuma
 - Utolsó frissítés dátuma
 - Súlyossági szint
 - Szabály állapota
 - A feltételek listája  
 - A szabály által érintett eszközök száma

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Szabály szerkesztése

Szabály szerkesztéséhez kövesse az alábbi lépéseket:

1. A kezdőlapon válassza a bal oldali navigációs menü **Szabályok** parancsát.
   Megjelenik a szabályablak.
2. Jelölje ki azt a szabályt, amelynek a szerkesztett szabályát szeretné.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Válassza a műveletsáv **Szerkesztés parancsát,** és megjelenik a **Szabály szerkesztése** ablak.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Módosítsa a **szabály nevét**és a **szabály leírását,** majd válasszon ki egy farmot a **Farm kiválasztása** legördülő menüből.
5. Írja be a farm nevét, hogy kiválassza a **farmot,** és a Feltételek ugyanabban az ablakban jelenik meg.  
6. A **Feltételek ben**a **Mérték**, **Operátor** és **Érték szerkesztése**területen .
7. Írja be a mérték nevét a **Mérték** legördülő menübe.
8. Válassza a **+Feltétel hozzáadása lehetőséget** a feltételek hozzáadásához/szerkesztéséhez.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Válassza ki a **súlyossági szintet**.  
10. A **Művelet alkalmazásban**kapcsolja be az **E-mail engedélyezve** a váltógombot az e-mail értesítések engedélyezéséhez.
11. Az **e-mail-értesítés** küldéséhez kívánt e-mail címek, valamint az **e-mail tárgyés** a **további megjegyzések szerkesztése**.  
12. A **Szabály állapota**mezőben kapcsolja be az **Engedélyezve** váltógombot a szabály engedélyezéséhez vagy letiltásához.
Megtekintheti a szabály által érintett eszközök számát.
13. A szabály szerkesztéséhez válassza az **Alkalmaz** lehetőséget.

## <a name="change-rule-status"></a>Szabály állapotának módosítása

A szabály állapotának módosításához hajtsa végre az alábbi lépéseket:

1. A kezdőlapon válassza a bal oldali navigációs menü **Szabályok** parancsát. Megjelenik a szabályablak.
2. Jelölje ki azt a szabályt, amelynek állapotát módosítani szeretné.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Válassza az **Állapot módosítása lehetőséget** a műveletsávon. Megjelenik **az Állapot módosítása** ablak.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Módosítsa a szabály állapotát az **Állapot módosítása** váltógombbal.
   Megtekintheti a szabály által érintett eszközök számát.
4. A szabály állapotának módosításához válassza az **Alkalmaz** lehetőséget.

## <a name="delete-rule"></a>Szabály törlése

Szabály törléséhez hajtsa végre az alábbi lépéseket:

1. A kezdőlapon válassza a bal oldali navigációs menü **Szabályok** parancsát. Megjelenik a szabályablak.
2. Jelölje ki azt a szabályt, amelynek törölni kívánt szabályát törölni szeretné.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Válassza a **törlés** lehetőséget a műveletsávról.

    ![Projekt Farm Beats](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Megjelenik **a Szabály törlése** párbeszédpanel. Válassza a **Törlés** elemet.
