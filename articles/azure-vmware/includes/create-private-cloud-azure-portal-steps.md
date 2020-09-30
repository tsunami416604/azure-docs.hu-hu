---
title: Azure VMware-megoldás üzembe helyezése
description: Az Azure VMware-megoldás a Azure Portal használatával történő üzembe helyezésének lépései.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578315"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza **az új erőforrás létrehozása**lehetőséget. A **Keresés a piactér** szövegmezőbe mezőbe írja be `Azure VMware Solution` az **Azure VMware megoldás** elemet a listából. Az **Azure VMware megoldás** ablakban válassza a **Létrehozás**lehetőséget.

1. Az **alapvető beállítások** lapon adja meg a mezők értékeit. A következő táblázat a mezők tulajdonságait sorolja fel.

   | Mező   | Érték  |
   | ---| --- |
   | **Előfizetés** | Az üzembe helyezéshez használni kívánt előfizetés.|
   | **Erőforráscsoport** | A saját felhőalapú erőforrásainak erőforráscsoport. |
   | **Hely** | Válasszon egy helyet, például az **USA keleti**régióját.|
   | **Erőforrás neve** | Az Azure VMware-megoldás saját Felhőbeli neve. |
   | **Termékváltozat** | Válassza ki a következő SKU-értéket: AV36 |
   | **Hosts** | A saját felhőalapú fürtbe felvenni kívánt gazdagépek száma. Az alapértelmezett érték 3, amely az üzembe helyezés után növelhető vagy csökkenthető.  |
   | **vCenter rendszergazdai jelszava** | Adja meg a Felhőbeli rendszergazdai jelszót. |
   | **NSX-T kezelő jelszava** | Adjon meg egy NSX-T rendszergazdai jelszót. |
   | **Címterület** | Adja meg a CIDR-hálózat IP-címét a privát felhőhöz, például 10.175.0.0/22. |
   | **Virtual Network** | Válasszon ki egy Virtual Network, vagy hozzon létre egy újat az Azure VMware megoldás saját felhőhöz.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Az alapvető beállítások lapon adja meg a mezők értékeit." border="true":::

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás**elemet. A következő képernyőn ellenőrizze a megadott adatokat. Ha az adatok helyesek, válassza a **Létrehozás**lehetőséget.

   > [!NOTE]
   > Ez a lépés nagyjából két órát vesz igénybe. 

1. Ellenőrizze, hogy a központi telepítés sikeres volt-e. Navigáljon a létrehozott erőforráscsoporthoz, és válassza ki saját felhőjét.  A telepítés befejeződése után a **sikeres** állapot jelenik meg. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Az alapvető beállítások lapon adja meg a mezők értékeit." border="true":::