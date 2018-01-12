---
title: "A bevezetni frissítés és a változáskövetési megoldás az Azure Automation |} Microsoft Docs"
description: "Ismerje meg a bevezetni frissítés és a változáskövetési megoldás az Azure Automation."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: eamono
ms.custom: mvc
ms.openlocfilehash: e277aa44dfe625780d72a78010f0638c73a6b182
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>A bevezetni frissítés és a változáskövetési megoldás az Azure Automation

Ebben az oktatóanyagban elsajátíthatja, hogyan kell virtuális gépeket az Azure Automation automatikusan előkészítésére frissítés, a változások követése, és a készlet megoldások:

> [!div class="checklist"]
> * A bevezetni az Azure virtuális gép
> * Megoldások
> * Telepítése és a modulok frissítése
> * A bevezetési forgatókönyv importálása
> * Elindítja a runbookot

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre szükség:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](automation-offering-get-started.md) gépek kezeléséhez.
* A szolgáltatásba felvenni kívánt [virtuális gép](../virtual-machines/windows/quick-create-portal.md).

## <a name="onboard-an-azure-vm"></a>A bevezetni az Azure virtuális gép

A beépített Azure virtuális gépek automatikusan, meglévő virtuális kell előkészítve a változáskövetés vagy frissítés felügyeleti megoldás. Ebben a lépésben hozzánk, felügyelete és a változások követése virtuális gépet.

### <a name="enable-change-tracking-and-inventory"></a>Változáskövetés és leltár engedélyezése

A változások követése és a készlet megoldás lehetővé teszi a [követni a változásokat](automation-vm-change-tracking.md) és [készlet](automation-vm-inventory.md) a virtuális gépeken. Ebben a lépésben engedélyezi a megoldás a virtuális gépen.

1. A bal oldali menüben válassza ki a **Automation-fiók**, majd válassza ki az automation-fiók a listában.
1. Válassza ki **készlet** alatt **konfigurációkezelés**.
1. Válasszon ki egy meglévő Naplóelemzési munkaterület, vagy új létrehozása. Kattintson a **engedélyezése** gombra.

![A bevezetni frissítés megoldás](media/automation-onboard-solutions/inventory-onboard.png)

Nyomon követését és a készlet megoldás bevezetése értesítést befejezését követően kattintson a **frissítéskezelés** alatt **konfigurációkezelés**.

### <a name="enable-update-management"></a>Frissítéskezelés engedélyezése

A frissítés felügyeleti megoldás lehetővé teszi a frissítések és javítások kezelheti a Windows Azure virtuális gépeken. A rendelkezésre álló frissítések, a szükséges frissítések telepítése ütemezés állapotát felmérheti, és felülvizsgálati központi telepítés eredményeinek ellenőrzése a frissítések alkalmazása sikeresen megtörtént a virtuális géphez. Ebben a lépésben engedélyezi a megoldás a virtuális Gépet.

1. Válassza ki az Automation-fiók **frissítéskezelés** alatt **FRISSÍTÉSKEZELÉS**.
1. A kiválasztott naplóelemzési munkaterület ugyanazon a munkaterületen használja az előző lépésben. Kattintson a **engedélyezése** bevezetni a frissítés-kezelési megoldásban.

![A bevezetni frissítés megoldás](media/automation-onboard-solutions/update-onboard.png)

Amíg a frissítés-kezelési megoldás telepítése folyik, a kék fejléc jelenik meg. Ha engedélyezve van az a megoldás kiválasztása **változáskövetés** alatt **konfigurációkezelés** számára a következő lépéssel.

### <a name="select-azure-vm-to-be-managed"></a>Válassza ki a kezelendő Azure virtuális Gépen

Most, hogy engedélyezett a megoldások, adhat hozzá egy Azure virtuális gép érheti el a partnermegoldások állapotösszegzése.

1. Az Automation-fiók, az a a **változáskövetés** lapon jelölje be **+ Azure virtuális gép hozzáadása** hozzáadása a virtuális gép.

1. Válassza ki a virtuális Gépet a listából, és válassza ki a **engedélyezése**. Ez a művelet lehetővé teszi, hogy a módosítás a nyomon követési és a készlet megoldással a virtuális gép.

   ![A virtuális gép frissítés megoldás engedélyezése](media/automation-onboard-solutions/enable-change-tracking.png)

1. A virtuális gép értesítését a bevezetés befejezésekor, az Automation-fiók válassza **frissítéskezelés** alatt **FRISSÍTÉSKEZELÉS**.

1. Válassza ki **+ Azure virtuális gép hozzáadása** hozzáadása a virtuális gép.

1. Válassza ki a virtuális Gépet a listából, és válassza ki a **engedélyezése**. Ez a művelet lehetővé teszi, hogy a frissítés-kezelési megoldás a virtuális géphez.

   ![A virtuális gép frissítés megoldás engedélyezése](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Ha nem várja meg a megoldást végezze el, ha engedélyezve van a következő megoldás kap, a következő üzenet: *egy másik megoldás telepítése folyamatban van a ez vagy egy másik virtuális gépet. A telepítés befejeződése után az Engedélyezés gombra engedélyezve van, és kérheti, hogy a virtuális gépen a megoldás telepítése.*

## <a name="install-and-update-modules"></a>Telepítése és a modulok frissítése

Frissítse a legújabb Azure-modulok és importálásához szükséges `AzureRM.OperationalInsights` sikeresen automatizálhatja a megoldás bevezetése.

## <a name="update-azure-modules"></a>Az Azure modulok frissítése

Válassza ki az Automation-fiók **modulok** alatt **megosztott erőforrások**. Válassza ki **frissítése Azure modulok** Azure modulok frissítése a legújabb verzióra. Válassza ki **Igen** az összes meglévő Azure modul frissítése a legújabb verzióra a parancssorba.

![Frissítés modulok](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>AzureRM.OperationalInsights modul telepítése

Az a **modulok** lapon jelölje be **Tallózás gyűjtemény** kattintva nyissa meg a modul gyűjteménye. Keresse meg a AzureRM.OperationalInsights, és importálja a modult az Automation-fiók.

![OperationalInsights modul importálása](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>A bevezetési forgatókönyv importálása

1. Az Automation-fiók, jelölje ki a **Runbookok** alatt a **folyamat**.
1. Válassza ki **Tallózás gyűjtemény**.
1. Keresse meg **frissítése és a változáskövetés**, kattintson a runbook, és válassza ki **importálási** a a **forrás megtekintése** lap. Válassza ki **OK** rendszerbe való importálás érdekében a runbook Automation-fiók.

  ![Bevezetési forgatókönyv importálása](media/automation-onboard-solutions/import-from-gallery.png)

1. Az a **Runbook** lapon jelölje be **szerkesztése**, majd jelölje be **közzététel**. Az a **Runbook közzététele** párbeszédablakban válassza **Igen** közzétenni a runbookot.

## <a name="start-the-runbook"></a>Elindítja a runbookot

Rendelkeznie kell előkészítve a változáskövetés vagy megoldások frissíteni egy Azure virtuális Gépen ahhoz, hogy elindítja a runbookot. Egy meglévő virtuális gép és a megoldás előkészítve erőforráscsoport igényel a paramétereket.

1. Nyissa meg az Enable-MultipleSolution runbookot.

   ![Több megoldás forgatókönyvek](media/automation-onboard-solutions/runbook-overview.png)

1. Kattintson a start gombra, és adja meg a következő paraméterek.

   * **VMNAME** -hagyja üresen. Meglévő virtuális érheti el a frissítést vagy a változáskövetési megoldás neve. Ezt az értéket üresen hagyja, az erőforráscsoport az összes virtuális gép található, a előkészítve.
   * **VMRESOURCEGROUP** -előkészítve kell a virtuális gépek esetén az erőforráscsoport nevét.
   * **A SUBSCRIPTIONID** -hagyja üresen. Az új virtuális Gépet előkészítve előfizetés-azonosítója. Ha üresen marad, az előfizetés a munkaterület szolgál. Ha egy másik előfizetés-azonosító van megadva, az automation-fiók a futtató fiók hozzá kell adni ehhez az előfizetéshez közreműködőként is.
   * **ALREADYONBOARDEDVM** – a virtuális Gépet, amely manuálisan lett előkészítve a frissítések vagy a változáskövetés neve megoldásban.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** -, amely a virtuális Gépet a tagja legyen az erőforráscsoport nevét.
   * **SOLUTIONTYPE** -meg **frissítések** vagy **változáskövetés**

   ![Enable-MultipleSolution runbook-paraméterek](media/automation-onboard-solutions/runbook-parameters.png)

1. Válassza ki **OK** a runbook feladatának elindításához.
1. Folyamatban van, és a runbook-feladat lapon hibák figyelése.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A bevezetni egy Azure virtuális gépet manuálisan.
> * Telepítése és frissítése a szükséges Azure modulokat.
> * Runbook importálása adott onboards Azure virtuális gépeken.
> * A runbook adott onboards Azure virtuális gépek automatikusan elindul.

Kattintson ide további információt a runbookok ütemezését.

> [!div class="nextstepaction"]
> [Runbookok ütemezése](automation-schedules.md).
