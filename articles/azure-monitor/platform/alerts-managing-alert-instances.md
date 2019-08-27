---
title: Riasztáspéldányok kezelése
description: Riasztási példányok kezelése az Azure-ban
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: dbc7d37ce311d203ee051b5a23632d2bf401e27a
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034830"
---
# <a name="manage-alert-instances"></a>Riasztáspéldányok kezelése
A Azure Monitor [egyesített riasztások](https://aka.ms/azure-alerts-overview) szolgáltatásával mostantól az összes különböző típusú riasztást láthatja az Azure-ban, több előfizetést is egyesítve egyetlen ablaktáblán. Ebből a cikkből megtudhatja, hogyan tekintheti meg a riasztási példányokat, és hogyan derítheti fel a portálon, hogy konkrét riasztási példányokat találjon a hibaelhárításhoz.

> [!NOTE]
   >  Csak az elmúlt 30 napban generált riasztások érhetők el az UX-en vagy a REST API-kon keresztül.

1. A riasztások lapon háromféle módon lehet leszállni

   + A [portálon](https://portal.azure.com/)kattintson a **figyelés** elemre, és a figyelés szakaszban válassza a **riasztások**lehetőséget.  
     ![Monitorozás](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + A riasztások egy adott **erőforrás**kontextusában is megtalálhatók. Az erőforrás megnyitása után navigáljon a tartalomjegyzékből a figyelés szakaszra, és válassza a riasztásoklehetőséget, és a kezdőlapot az adott erőforráson lévő riasztásokra vonatkozóan előre szűrni kell.
   
     ![Figyelés](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + A riasztások egy adott **erőforráscsoport**kontextusában is megtalálhatók. Az erőforráscsoport megnyitása után navigáljon a tartalomjegyzékből a figyelés szakaszra, és válassza a riasztások lehetőséget, és a kezdőlapot az adott erőforráscsoporthoz tartozó riasztásokra vonatkozóan előre szűrni kell.    
   
     ![Figyelés](media/alerts-managing-alert-instances/alert-rg.JPG)

1. A riasztások összegzése oldalon fog megjelenni, amely áttekintést nyújt az Azure összes riasztási példányáról. Az összegző nézetet úgy módosíthatja, hogy **több** előfizetést (legfeljebb 5) választ ki, vagy az **erőforráscsoportok**, adott **erőforrások**vagy időtartományok szűrésével. A riasztások listájának megtekintéséhez kattintson a teljes riasztások vagy a súlyossági sávok egyikére.     
   ![Riasztások összegzése](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
1. A **minden riasztás** lapon megjelenik az összes, az Azure-ban felsorolt riasztási példány. Ha riasztási értesítésből érkezik a portálra, az adott riasztási példányon elérhető szűrők használatával szűkítheti a rendelkezésre álló szűrőket. (**Megjegyzés**: Ha az oldalra a súlyossági sávok bármelyikére kattintva jutott, a lista az adott súlyossági szintnél előre szűrve jelenik meg. Az előző oldalon elérhető szűrőktől eltekintve mostantól a figyelési szolgáltatás (például a mérőszámok platformja), a figyelési feltétel (kilőtt vagy feloldott), a súlyosság, a riasztási állapot (új/visszaigazolt/lezárt), vagy az intelligens csoport azonosítója alapján is szűrhet.

   ![Minden riasztás](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  Ha a súlyossági sávok bármelyikére kattintva jutott a laphoz, a lista az adott súlyossági szinthez képest előre szűrve jelenik meg, amikor ezen a lapon landol.
 
1. Ha bármelyik riasztási példányra kattint, megnyílik a **riasztás részletei** lap, amely lehetővé teszi az adott riasztási példány információinak részletes ismertetését.   
   ![Riasztás részletei](media/alerts-managing-alert-instances/alert-details.jpg)  

