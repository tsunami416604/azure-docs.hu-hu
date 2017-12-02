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
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: eamono
ms.openlocfilehash: 9ae5e9ebcbcf3af61318eac98defc6b249417f41
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>A bevezetni frissítés és a változáskövetési megoldás az Azure Automation

Ebben az oktatóanyagban elsajátíthatja, hogyan kell virtuális gépeket az Azure Automation automatikusan előkészítésére frissítés, a változások követése, és a készlet megoldások:

> [!div class="checklist"]
> * A bevezetni egy Azure virtuális gépet manuálisan.
> * Telepítése és frissítése a szükséges Azure modulokat.
> * Runbook importálása adott onboards Azure virtuális gépeken.
> * A runbook adott onboards Azure virtuális gépek automatikusan elindul.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre szükség.
+ Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Automation-fiók](automation-offering-get-started.md) gépek kezeléséhez.

## <a name="onboard-an-azure-virtual-machine-manually"></a>A bevezetni egy Azure virtuális gépet manuálisan

1.  Nyissa meg az Automation-fiók.
2.  Kattintson a készlet oldalon.
![A bevezetni készlet megoldás](media/automation-onboard-solutions/inventory-onboard.png)
3.  Válasszon ki egy meglévő Naplóelemzési munkaterület, vagy új létrehozása. Kattintson az Engedélyezés gombra.
4.  Ha nyomon követését és a készlet megoldás bevezetése értesítést befejeződött, kattintson a frissítés kezelése lapján.
![A bevezetni frissítés megoldás](media/automation-onboard-solutions/update-onboard.png)
4.  Kattintson az Engedélyezés gombra, hogy a frissítés megoldást onboards.
5.  Ha a frissítés megoldás bevezetése értesítési befejeződött, kattintson a változások követése oldalon.
![Változáskövetés előkészítéséről](media/automation-onboard-solutions/change-tracking-onboard-vm.png)
6.  Kattintson az Azure VM hozzáadása gombra.
![Válassza ki a virtuális gép a változások követése](media/automation-onboard-solutions/enable-change-tracking.png)
7.  Jelöljön ki egy Azure virtuális Gépet, majd kattintson a az Engedélyezés gombra érheti el a módosítása nyomon követését és a készlet megoldás.
8.  Ha a virtuális gép értesítését a bevezetési befejeződött, kattintson a frissítés kezelése lap.
![A bevezetni virtuális Gépet, a frissítéskezelés](media/automation-onboard-solutions/update-onboard-vm.png)
9.  Kattintson az Azure VM hozzáadása gombra.
![Válassza ki a virtuális gép a kezeléséhez](media/automation-onboard-solutions/enable-update.png)
10.  Jelöljön ki egy Azure virtuális Gépet, és kattintson az Engedélyezés gombra érheti el a frissítés-kezelési megoldás.

## <a name="install-and-update-required-azure-modules"></a>Telepítése és a szükséges Azure modulokat frissítése

Célszerű telepíteni sikeresen automatizálhatja a megoldás bevezetése AzureRM.OperationalInsights importálása és frissítése a legújabb Azure-modulok.
1.  Kattintson a modulok oldalon.
![Frissítés modulok](media/automation-onboard-solutions/update-modules.png)
2.  Kattintson a frissítés Azure modulok gombra, amely a legújabb verzióra frissíti. Várjon, amíg a frissítés befejezéséhez.
3.  Kattintson a Tallózás gyűjtemény gombra kattintva nyissa meg a modul gyűjteménye.
![OperationalInsights modul importálása](media/automation-onboard-solutions/import-operational-insights-module.png)
4.  Keresse meg a AzureRM.OperationalInsights, és importálja a modult az Automation-fiók.

## <a name="import-a-runbook-that-onboards-solutions-to-azure-vms"></a>Az Azure virtuális gépen az adott onboards megoldások runbook importálása

1.  Kattintson a "Folyamatok AUTOMATIZÁLÁSA" kategóriához tartozó Runbookok oldalon.
2.  Kattintson a "Tallózás gyűjtemény" gombra.
3.  Keresse meg a "frissítés és a változáskövetés", és a runbook importálása az Automation-fiók.
![Bevezetési forgatókönyv importálása](media/automation-onboard-solutions/import-from-gallery.png)
4.  Kattintson az "Edit" tekintse meg a Runbook forrást, és kattintson a "Közzététele" gombra.
![Bevezetési forgatókönyv importálása](media/automation-onboard-solutions/publish-runbook.png)

## <a name="start-the-runbook-that-onboards-azure-vms-automatically"></a>Elindítja a runbookot, hogy onboards Azure virtuális gépek automatikusan

Rendelkeznie kell előkészítve a változáskövetés vagy megoldások frissíteni egy Azure virtuális Gépen ahhoz, hogy elindítja a runbookot. Egy meglévő virtuális gép és a megoldás előkészítve erőforráscsoport igényel a paramétereket.
1.  Nyissa meg az Enable-MultipleSolution runbookot.
![Több megoldás forgatókönyvek](media/automation-onboard-solutions/runbook-overview.png)
2.  Kattintson a start gombra, és adja meg a következő paraméterek.
    *   VMNAME. Meglévő virtuális érheti el a frissítést vagy a változáskövetési megoldás neve. Hagyja üresen, és az erőforráscsoport összes virtuális gépe előkészítve.
    *   VMRESOURCEGROUP. Az erőforráscsoport, amely a virtuális gép tagjának neve.
    *   ELŐFIZETÉS-AZONOSÍTÓ. Az előfizetés-azonosító az új virtuális Gépet a bevezetni megtalálható. Hagyja üresen, és az előfizetés a munkaterület szolgál. Ha egy másik előfizetés-azonosító van megadva, az automation-fiók a futtató fiók hozzá kell adni ehhez az előfizetéshez közreműködőként is.
    *   ALREADYONBOARDEDVM. A virtuális Gépet, amely manuálisan lett előkészítve a frissítések vagy a változáskövetés neve megoldásban.
    *   ALREADYONBOARDEDVMRESOURCEGROUP. Az erőforráscsoport, amely a virtuális gép tagjának neve.
    *   SOLUTIONTYPE. Adja meg a "Frissítések" vagy "Változáskövetés"
    
    ![Megoldás több runbook-paraméterek](media/automation-onboard-solutions/runbook-parameters.png)
3.  Kattintson az OK gombra a runbook-feladat indítása.
4.  Folyamatban van, és a runbook-feladat lapon hibák figyelése.

## <a name="next-steps"></a>Következő lépések

További információkért lásd: [runbookok ütemezése](automation-schedules.md).