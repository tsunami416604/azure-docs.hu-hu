---
title: Riasztáspéldányok kezelése
description: Riasztás-példányok felügyeletére az Azure-ban
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: a444d0c2ec5f2219dbffdeb6677d5e248c7004fe
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414458"
---
# <a name="manage-alert-instances"></a>Riasztáspéldányok kezelése
Az a [egységes riasztások kezelőfelület](https://aka.ms/azure-alerts-overview) az Azure monitorban, most már megtekintheti a riasztások különböző típusait Azure-on, több előfizetést, egy egyetlen panelen tekinthesse átfedés. Ez a cikk végigvezeti a riasztási példányok megtekintésének és a meghatározott riasztási példányok található hibaelhárítási portálon részletesen.

1. A riasztások lapon kerül három módja van

   + Az a [portál](https://portal.azure.com/), jelölje be **figyelő** , és válassza ki a Monitor terület - **riasztások**.  
    ![Monitorozás](media/monitoring-alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + Egy adott környezetében riasztásai navigálhat **erőforrás**. Miután egy erőforráshoz van megnyitva, haladjon végig a tartalomjegyzékben, a figyelés szakaszban, és válassza **riasztások**, a riasztások az adott erőforráson előre szűrni kívánt kezdőlapjának alkotóelemeit.
   
    ![Figyelés](media/monitoring-alerts-managing-alert-instances/alert-resource.JPG)
    
   + Egy adott környezetében riasztásai navigálhat **erőforráscsoport**. Miután megnyílik egy erőforráscsoportot, haladjon végig a tartalomjegyzékben, a figyelés szakaszban, és válassza **riasztások**, az adott erőforráscsoport a riasztásaihoz előre szűrni kívánt kezdőlapja.    
   
    ![Figyelés](media/monitoring-alerts-managing-alert-instances/alert-rg.JPG)

1.  Kerül a **riasztások szerint** oldal, amely áttekintést nyújt az összes riasztás-példányok Azure-ban. Az összefoglaló nézet kiválasztásával módosíthatja **több előfizetést** (legfeljebb 5) vagy különböző szűrésével **erőforráscsoportok**meghatározott **erőforrások**, vagy **címtartományok idő**. Kattintson az értesítések száma vagy a súlyosság sávok bármelyikét nyissa meg a riasztások listanézetének megnyitására.     
    ![Riasztások szerint](media/monitoring-alerts-managing-alert-instances/alerts-summary.jpg)
 
1.  Kerül a **minden riasztás** oldal, ahol láthatja a riasztási példányok ki felsorolt Azure-ban. Ha a riasztási értesítéshez hamarosan még a portálra, használhatja a rendelkezésre álló szűkítheti a a meghatározott riasztási eseményről a szűrők. (**Megjegyzés**: Ha bármelyik súlyossági sávra kattintva a lap kapott, a lista lesz az adott súlyossági előre szűrt megnyitja, ha). A szűrők érhető el az előző lapon szereplőkkel most is szűrheti a beállítási szolgáltatás figyelése (például metrikák Platform), figyelési feltétel (aktivált vagy feloldása), súlyosság, (új/nyugtázva/zárt) riasztás állapota vagy az intelligens csoport azonosítója.

    ![Minden riasztás](media/monitoring-alerts-managing-alert-instances/all-alerts.jpg)

    > [!NOTE]
    >  Ha bármelyik súlyossági sávra kattintva a lap kapott, a lista lesz előre szűrt esetében, amikor ezen az oldalon, megnyitja.
 
1.  Bármelyik figyelmeztetési példányra kattintva megnyílik a **riasztás részletei** lap, amely lehetővé teszi a részletes leírása a meghatározott riasztási eseményről kapcsolatos információkat az.   
![Riasztás részletei](media/monitoring-alerts-managing-alert-instances/alert-details.jpg)  
