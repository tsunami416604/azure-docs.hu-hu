---
title: Oktatóanyag – Azure Active Directory-naplók archiválása egy Azure-tárfiókba (előzetes verzió) | Microsoft Docs
description: Itt megtudhatja, hogyan állíthatja be az Azure Diagnosticsban az Azure Active Directory-naplók leküldését egy tárfiókba (előzetes verzió)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240242"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Oktatóanyag: Azure Active Directory-naplók archiválása egy Azure-tárfiókba (előzetes verzió)

Az oktatóanyagból megtudhatja, hogyan konfigurálhatja úgy az Azure Monitort, hogy az Azure Active Directory-naplókat egy Azure-tárfiókba irányítsa.

## <a name="prerequisites"></a>Előfeltételek 

A következők szükségesek:

* Egy Azure-előfizetés egy Azure-tárfiókkal. Ha nincs Azure-előfizetése, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/free/).
* Egy Azure Active Directory-bérlő.
* Egy felhasználó, aki az adott bérlő globális vagy biztonsági rendszergazdája.

## <a name="archive-logs-to-an-azure-storage-account"></a>Tevékenységnaplók archiválása egy Azure-tárfiókba

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Kattintson az **Azure Active Directory** -> **Tevékenység** -> **Auditnaplók** elemre. 
3. A **Beállítások exportálása** gombra kattintva nyissa meg a Diagnosztikai beállítások panelt. Kattintson a **Beállítás szerkesztése** gombra, ha a meglévő beállításokat szeretné szerkeszteni, vagy a **Diagnosztikai beállítás hozzáadása** gombra, ha egy új beállítást szeretne hozzáadni. Legfeljebb három beállítás adható meg. 
    ![Beállítások exportálása](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Beállítások exportálása")

4. Adjon egy rövid nevet a beállításnak, ami emlékezteti a beállítás céljára. Például: „Küldés az Azure-tárfiókba”. 
5. Jelölje be az **Archiválás tárfiókba** jelölőnégyzetet, és a **Tárfiók** gombra kattintva válassza ki az Azure-tárfiókot. 
6. Válassza ki, melyik Azure-előfizetésbe és tárfiókba kívánja irányítani a naplókat, majd az **OK** gombra kattintva zárja be a konfigurációs felületet.
7. Jelölje be az **AuditLogs** (Naplófájlok) jelölőnégyzetet, ha az auditnaplókat szeretné továbbítani a tárfiókba. 
8. Jelölje be a **SignInLogs** (Bejelentkezési naplók) jelölőnégyzetet, ha a bejelentkezési naplókat szeretné továbbítani a tárfiókba.
9. A naplóadatok megőrzési idejét a csúszkával állíthatja be. Ez az érték alapértelmezés szerint 0, ami azt jelenti, hogy a naplóadatok korlátlan ideig megmaradnak a tárfiókban. Ha más értéket ad meg, a rendszer automatikusan törli a megadott számú napnál régebbi eseményeket.
10. Kattintson a **Mentés** gombra a beállítás mentéséhez.
    ![Diagnosztikai beállítások](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Diagnosztikai beállítások")

11. Körülbelül 15 perc elteltével ellenőrizze, hogy a rendszer leküldi-e a naplókat a tárfiókba. Lépjen az Azure Portalra, kattintson a **Tárfiókok** lehetőségre, válassza ki a korábban használt tárfiókot, és kattintson a **Blobok** lehetőségre. 
12. Az **Auditnaplók** esetében kattintson az **insights-log-audit** blobra. A **Bejelentkezési naplók** esetében kattintson az **insights-logs-signin** blobra.
    ![Tárfiók](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Tárfiók")

## <a name="next-steps"></a>További lépések

* [Az auditnaplók sémájának értelmezése az Azure Monitorban](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [A bejelentkezési naplók sémájának értelmezése az Azure Monitorban](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Gyakori kérdések és ismert hibák](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)