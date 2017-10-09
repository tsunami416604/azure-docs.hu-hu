---
title: "Azure-beli virtuális gépek kezelése leltár használatával | Microsoft Docs"
description: "Virtuális gépek kezelése leltár használatával"
services: automation
keywords: "leltár, automatizálás, változás, követés"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: hu-hu
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Azure-beli virtuális gépek kezelése leltár használatával

A leltár nyomon követése az egyes Azure-beli virtuális gépeken a gép erőforráslapján engedélyezhető. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a leltározás beállításához és konfigurálásához.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
Ha nem rendelkezik Azure-beli [virtuális géppel](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal), első lépésként mindössze néhány perc alatt létrehozhat egyet.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Leltározás engedélyezése a virtuális gép erőforrásainak lapjáról

1. A bal oldali képernyőn válassza a **Virtuális gépek** elemet.
1. Válasszon ki egy virtuális gépet a listáról.
1. Válassza a **Leltár (előzetes verzió)** elemet az erőforrások a **Műveletek** alatti menüjéből.
1. Egy szalagcím jelenik meg a lap tetején, amely értesíti arról, hogy a megoldás nincs engedélyezve. A megoldás engedélyezéséhez kattintson a szalagcímre.
1. Válasszon ki egy Log Analytics-munkaterületet az adatnaplók tárolásához. Ha az adott régióban nincsenek az Ön számára elérhető munkaterületek, a rendszer megkéri, hogy hozzon létre egy alapértelmezett munkaterületet és egy Automation-fiókot. A számítógép előkészítéséhez kattintson az **Engedélyezés** gombra.

   ![Előkészítési lehetőségek megtekintése](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. Az értesítősáv tájékoztatja, hogy a megoldás engedélyezése folyamatban van. Az engedélyezés akár 15 percet is igénybe vehet. Ez alatt az idő alatt bezárhatja a panelt, vagy ha nyitva hagyja, az jelenti, amikor a megoldás engedélyezve lett. Az üzembe helyezés állapota az értesítési panelről figyelemmel kísérhető.

   ![A leltármegoldás megjelenítése közvetlenül az előkészítés után](./media/automation-vm-inventory/inventory-onboarded.png)

1. Miután az üzembe helyezés befejeződött, az állapotsáv eltűnik. Ekkor a rendszer még gyűjti a leltáradatokat, ezért az adatok esetleg még nem láthatók. A teljes adatgyűjtés akár 24 órát is igénybe vehet.

## <a name="configure-your-inventory-settings"></a>A leltárbeállítások konfigurálása

Alapértelmezés szerint a szoftverek, a Windows-szolgáltatások és a Linux-démonok szerepelnek a leltározás hatókörében. A Windows beállításjegyzék- és a fájlleltárának összeállításához konfigurálnia kell a leltározási beállításokat.

1. A **Leltár (előzetes verzió)** nézetben válassza a **Beállítások szerkesztése** gombot a lap tetején.
2. Új leltározási beállítás hozzáadásához lépjen a hozzáadni kívánt kategóriára a **Windows beállításjegyzék**, a **Windows-fájlok** és a **Linux-fájlok** lapok használatával. Kattintson a **Hozzáadás** gombra a lap tetején.
3. Az egyes beállítástulajdonságok részleteinek és leírásának megtekintéséhez lépjen a [Leltár-dokumentáció lapjára](https://aka.ms/configinventorydocs).

## <a name="disconnecting-your-virtual-machine-from-management"></a>A virtuális gép kezelésének megszüntetése

A gép eltávolítása a leltárkezelésből:

1. Az Azure Portal bal oldali menüjében kattintson a **Log Analytics** elemre, majd válassza ki a virtuális gép előkészítése során használt munkaterületet.
1. Nyissa meg a Log Analytics lapot, majd válassza a **Virtuális gépek** lehetőséget az erőforrások menüjének **Munkaterület adatforrásai** kategóriájában. 
1. Válassza ki a listából a leválasztani kívánt virtuális gépet. A gép mellett egy zöld pipa jelenik meg, és az „Ez a munkaterület” felirat jelenik meg az **OMS-kapcsolat** oszlopban. Kattintson a **Leválasztás** parancsra a következő lap tetején, majd az **Igen** gombra a megerősítést kérő párbeszédpanelen a gép leválasztásához.

## <a name="next-steps"></a>Következő lépések

* A virtuális gépek fájl- és beállításjegyzék-beállításainak módosításával kapcsolatos információkért lásd a [változások követését](../log-analytics/log-analytics-change-tracking.md).
* A virtuális gépek Windows- és csomagfrissítéseinek kezelésével kapcsolatos információkért lásd a [frissítéskezelést](../operations-management-suite/oms-solution-update-management.md).

