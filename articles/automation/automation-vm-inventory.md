---
title: "Azure-beli virtuális gépek kezelése leltár használatával | Microsoft Docs"
description: "Virtuális gép kezelése leltár használatával"
services: automation
keywords: "leltár, automatizálás, változás, követés"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: bfdd67ac1018f242e415da1a66d93f22bea8d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Azure-beli virtuális gépek kezelése leltár használatával

A leltár nyomon követése az egyes Azure-beli virtuális gépeken a gép erőforráslapján engedélyezhető. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a leltározás beállításához és konfigurálásához.

## <a name="before-you-begin"></a>Előkészületek
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
Ha nem rendelkezik Azure-virtuális géppel, [hozzon létre egy virtuális gépet](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Leltározás engedélyezése a virtuális gép erőforrásainak lapjáról

1. Az Azure Portal bal oldali panelén válassza a **Virtuális gépek** elemet.
2. Válasszon ki egy virtuális gépet a listáról.
3. Az **Erőforrás** menü **Műveletek** területén válassza a **Leltározás (előzetes verzió)** elemet.  
    Egy szalagcím jelenik meg az ablak tetején, amely értesíti arról, hogy a megoldás nincs engedélyezve. 
4. A megoldás engedélyezéséhez válassza ki a szalagcímet.
5. Válasszon ki egy Log Analytics-munkaterületet az adatnaplók tárolásához.  
    Ha az adott régióban nincsenek az Ön számára elérhető munkaterületek, a rendszer megkéri, hogy hozzon létre egy alapértelmezett munkaterületet és egy Automation-fiókot. 
6. A számítógép előkészítéséhez válassza az **Engedélyezés** lehetőséget.

   ![Előkészítési lehetőségek megtekintése](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    Az értesítősáv tájékoztatja, hogy a megoldás engedélyezése folyamatban van. Az engedélyezés akár 15 percet is igénybe vehet. Ez alatt az idő alatt bezárhatja az ablakot, vagy ha nyitva hagyja, az értesíteni fogja, amikor a megoldás engedélyezve lett. Az üzembe helyezés állapota az értesítési panelről figyelemmel kísérhető.

   ![A leltármegoldás megjelenítése közvetlenül az előkészítés után](./media/automation-vm-inventory/inventory-onboarded.png)

Miután az üzembe helyezés befejeződött, az állapotsáv eltűnik. A rendszer még gyűjti a leltáradatokat, ezért az adatok esetleg még nem láthatók. A teljes adatgyűjtés akár 24 órát is igénybe vehet.

## <a name="configure-your-inventory-settings"></a>A leltárbeállítások konfigurálása

Alapértelmezés szerint a szoftverek, a Windows-szolgáltatások és a Linux-démonok szerepelnek a leltározás hatókörében. A Windows beállításjegyzék- és a fájlleltárának összeállításához konfigurálnia kell a leltározási beállításokat.

1. A **Leltározás (előzetes verzió)** nézetben válassza a **Beállítások szerkesztése** gombot az ablak tetején.
2. Új leltározási beállítás hozzáadásához lépjen a hozzáadni kívánt kategóriára a **Windows beállításjegyzék**, a **Windows-fájlok** és a **Linux-fájlok** lapok használatával. 
3. Válassza a **Hozzáadás** lehetőséget az ablak tetején.
4. Az egyes beállítástulajdonságok részleteinek és leírásának megtekintéséhez lépjen a [Leltározás dokumentáció lapjára](https://aka.ms/configinventorydocs).

## <a name="disconnect-your-virtual-machine-from-management"></a>A virtuális gép kezelésének megszüntetése

A virtuális gép eltávolítása a leltárkezelésből:

1. Az Azure Portal bal oldali paneljén kattintson a **Log Analytics** elemre, majd válassza ki a virtuális gép előkészítése során használt munkaterületet.
2. Nyissa meg a **Log Analytics** ablakot, majd válassza a **Virtuális gépek** lehetőséget az **Erőforrás** menü **Munkaterület adatforrásai** kategóriájában. 
3. A listában válassza ki a leválasztani kívánt virtuális gépeket. A virtuális gép mellett egy zöld pipa és az **Ez a munkaterület** felirat jelenik meg az **OMS-kapcsolat** oszlopban. 
4. A következő lap tetején válassza a **Leválasztás** lehetőséget.
5. A megerősítési ablakban válassza a **Igen** lehetőséget.  
    Ezzel a művelettel megszünteti a gép kezelését.

## <a name="next-steps"></a>Következő lépések

* A virtuális gépek fájl- és beállításjegyzék-beállításainak módosításával kapcsolatos információkért lásd: [A szoftvermódosítások nyomon követése saját környezetében a Change Tracking megoldás használatával](../log-analytics/log-analytics-change-tracking.md).
* A virtuális gépek Windows- és csomagfrissítéseinek kezelésével kapcsolatos információkért lásd [az OMS-beli Update Management megoldást](../operations-management-suite/oms-solution-update-management.md).
