---
title: Riasztási és intelligenscsoport-állapotok kezelése
description: A riasztási és az intelligens csoportpéldányok állapotának kezelése
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: a22d7b7b962b36f93ee804c64b3bc6b08ecceb0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667516"
---
# <a name="manage-alert-and-smart-group-states"></a>Riasztási és intelligenscsoport-állapotok kezelése

Az Azure Monitor riasztási [állapota és figyelőállapota,](https://aka.ms/azure-alerts-overview) valamint az intelligens csoportok [intelligens csoportállapota](https://aka.ms/smart-groups)már rendelkezik. Az állapot módosításai most már rögzítésre kerülnek a megfelelő riasztási vagy intelligens csoporthoz társított előzményekben. Ez a cikk végigvezeti az állapot módosításának folyamatán, mind a riasztás, mind az intelligens csoport esetében.

## <a name="change-the-state-of-an-alert"></a>A riasztás állapotának módosítása

1. A riasztások állapotát a következő módokon módosíthatja: 
    * A Minden értesítés lapon kattintson a módosítani kívánt riasztások melletti jelölőnégyzetre, majd kattintson a Változás állapotgombra.   
    ![Figyelés](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * Egy adott riasztási példány riasztásrészletei lapján kattintson a Változás állapot   
    ![Figyelés](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Egy adott riasztási példány riasztásrészletei lapján az Intelligens csoport ablaktáblán a kívánt riasztások melletti jelölőnégyzetre kattinthat.    
    ![Figyelés](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Az Intelligens csoport részletei lap tagriasztások listájában kattintson a jelölőnégyzetre a módosítani kívánt riasztások mellett, és kattintson az Állapot módosítása gombra az állapot módosításához, és kattintson az Állapot módosítása gombra.   
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. A Change State gombra kattintva megnyílik egy felugró ablak, amely lehetővé teszi az állapot kiválasztását (Új/ Nyugtázott / Lezárt), és szükség esetén megjegyzést ad meg.   
![Figyelés](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Ha ez megtörtént, az állapotváltozás rögzítésre kerül a megfelelő riasztás előzményeiközött. Ez megtekinthető a megfelelő Részletek oldal megnyitásával és az előzmények szakasz ellenőrzésével.    
![Figyelés](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>Intelligens csoport állapotának módosítása
1. Az intelligens csoportok állapotát az alábbi módokon módosíthatja:
    1. Az Intelligens csoport listalapon kattintson a módosítani kívánt intelligens csoportok melletti jelölőnégyzetre, és kattintson az Állapot módosítása gombra.  
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Az Intelligens csoport részletei lapon kattintson a Változás állapot        
    ![Figyelés](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. A Change State gombra kattintva megnyílik egy felugró ablak, amely lehetővé teszi az állapot kiválasztását (Új/ Nyugtázott / Lezárt), és szükség esetén megjegyzést ad meg. 
![Monitorozás](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Az intelligens csoport állapotának módosítása nem változtatja meg az egyes tagi riasztások állapotát.

1. Ha ez megtörtént, az állapotváltozás rögzítésre kerül az adott intelligens csoport történetében. Ez megtekinthető a megfelelő Részletek oldal megnyitásával és az előzmények szakasz ellenőrzésével.     
![Figyelés](./media/alerts-managing-alert-states/state-sg-history.jpg)

