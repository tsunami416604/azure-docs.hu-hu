---
title: A ServiceNow szinkronizálási problémáinak manuális javítása
description: Állítsa alaphelyzetbe a kapcsolatot a ServiceNow szolgáltatással, így a Microsoft Azure-ban a riasztások újra meghívhatják a ServiceNow-t
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313669"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>A ServiceNow szinkronizálási problémáinak manuális javítása

Az Azure Monitor külső informatikai szolgáltatáskezelő (ITSM) szolgáltatókhoz tud csatlakozni. ServiceNow egyike azoknak a szolgáltatóknak.

Biztonsági okokból előfordulhat, hogy frissítenie kell a ServiceNow-kapcsolathoz használt hitelesítési jogkivonatot.
A következő szinkronizálási folyamat segítségével aktiválja újra a kapcsolatot, és frissítse a jogkivonatot:


1. Keresse meg a megoldást a felső keresési szalagcímben, majd válassza ki a megfelelő megoldásokat

    ![Új kapcsolat](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. A megoldás képernyőn válassza az "Összes kiválasztása" lehetőséget az előfizetésszűrőben, majd szűrje a "ServiceDesk" parancsot.

    ![Új kapcsolat](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Válassza ki az ITSM-kapcsolat megoldását.
1. Válassza az ITSM-kapcsolatot a bal oldali szalagcímben.

    ![Új kapcsolat](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Jelölje ki az egyes összekötőket a listából. 
    1. A konfiguráláshoz kattintson az összekötő nevére
    1. A már használaton lévő összekötők törlése

    1. A mezők frissítése [a definíciók](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) szerint a partnertípus alapján

    1. Kattintson a szinkronizálásra

       ![Új kapcsolat](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Kattintson a mentésre

        ![Új kapcsolat](media/itsmc-resync-servicenow/save-8bit.png)

f.    Tekintse át az értesítéseket, és nézze meg, hogy a folyamat sikeresen befejeződött-e 

## <a name="next-steps"></a>Következő lépések

További információ [az INFORMATIKAI szolgáltatáskezelési kapcsolatokról](itsmc-connections.md)
