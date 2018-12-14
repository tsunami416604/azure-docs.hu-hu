---
title: Riasztási és intelligenscsoport-állapotok kezelése
description: A tartományvezérlők állapotait az a csoport riasztási és intelligens példányok kezelése
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 88601383df5015f9ea23184d65266974bb0f35e1
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346087"
---
# <a name="manage-alert-and-smart-group-states"></a>Riasztási és intelligenscsoport-állapotok kezelése
Most már az Azure monitorban riasztásokat egy [riasztási állapotot és a egy figyelési feltétel](https://aka.ms/azure-alerts-overview) , és ehhez hasonlóan az intelligens csoportok egy [állapot intelligens](https://aka.ms/smart-groups). Az állapot módosítása már megfelelő riasztási és intelligens gazdagépcsoporthoz tartozó előzmények lesznek rögzítve. Ez a cikk végigvezeti az állapot, riasztás és a egy intelligens csoport folyamatát.

## <a name="change-the-state-of-an-alert"></a>Egy riasztás állapotának módosítása
1. Egy riasztás állapotának különböző a következőképpen módosíthatja: 
    * Az összes riasztás oldalán kattintson a riasztások állapotát módosítani kívánja a jelölőnégyzetét, majd kattintson a állapot módosítása.   
    ![Figyelés](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * A riasztás részleteit megjelenítő oldalon a meghatározott riasztási eseményről rákattinthat az állapot módosítása   
    ![Figyelés](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * A riasztás részleteit megjelenítő oldalon a meghatározott riasztási eseményről az intelligenskártya-csoport panelen rákattinthat be a kívánt értesítések melletti jelölőnégyzetet    
    ![Figyelés](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Az intelligens csoport részletei lap tag riasztások listájában, is kattintson a jelölőnégyzet állapotának módosítása, és kattintson a módosítás Stateto módosítani szeretné a riasztások mellett állapotát majd állapot módosítása.   
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Egy előugró ablak az állapot módosítása gombra kattintva megnyílik az lehetővé teszi, hogy az állapot (új/Visszaigazolva/lezárva) lehetőséget, adja meg a megjegyzést szükség esetén.   
![Figyelés](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Miután ez megtörtént, a megfelelő riasztás előzményeit rögzíti az állapotváltozás körülményeiről. Ez a megfelelő Részletek lap megnyitásával, és a korábbi szakaszban ellenőrzése is megtekinthetők.    
![Figyelés](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Az intelligens csoport állapotának módosítása
1. Az intelligens csoport állapotának különböző a következőképpen módosíthatja:
    1. Az intelligens csoport-lista oldalára az intelligens csoportok módosítsa állapotát, és kattintson az állapot módosítása jelölőnégyzetét kattintva  
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Az intelligens csoport részletei lap kattinthat állapot módosítása        
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Egy előugró ablak az állapot módosítása gombra kattintva megnyílik az lehetővé teszi, hogy az állapot (új/Visszaigazolva/lezárva) lehetőséget, adja meg a megjegyzést szükség esetén. 
![Monitorozás](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Az intelligens csoport állapotának módosítása nem módosítja a tagokhoz riasztások állapotát.

1. Miután ez megtörtént, a megfelelő intelligens csoportjában korábbi rögzíti az állapotváltozás körülményeiről. Ez a megfelelő Részletek lap megnyitásával, és a korábbi szakaszban ellenőrzése is megtekinthetők.     
![Figyelés](./media/alerts-managing-alert-states/state-sg-history.jpg)
