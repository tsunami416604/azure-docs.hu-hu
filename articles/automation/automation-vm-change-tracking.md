---
title: "Az Azure virtuális gépek változásainak követése |} Microsoft Docs"
description: "A fájlok és a beállításjegyzék változásainak követése a virtuális gépek a változáskövetés segítségével."
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 5c6e8390ec8533fc7ab281c212e47a6982b30f1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Az Azure virtuális gépek változásainak követése

Azáltal, hogy a változások követését, követheti a módosításokat a fájlokhoz és a Windows-beállításkulcsok a virtuális gépeken. Konfigurációs változásokat segíthetnek a rögzítési ponthoz működési problémák.

Engedélyezheti a változáskövetés közvetlenül az Azure virtuális gépről.

Ha nem rendelkezik egy Azure virtuális gépen, utasításait követve létrehozhat a [Windows gyors üzembe helyezés](../virtual-machines/windows/quick-create-portal.md) vagy [Linux gyors üzembe helyezés](../virtual-machines/linux/quick-create-portal.md) cikk.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Egy Azure virtuális gép változáskövetése engedélyezése

1. Az Azure Portal bal oldali panelén válassza a **Virtuális gépek** elemet.
2. A listában válassza ki a virtuális gép.
3. A virtuális gép ablakban, a **műveletek**, jelölje be **változáskövetési**. 

   ![A változáskövetés előkészítésére vm](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    A **engedélyezze Frissítéskezelésről** ablak nyílik meg.

    Az érvényesség annak meghatározásához, hogy engedélyezve van-e változások követését a virtuális gép számára. Változáskövetés nincs engedélyezve, ha a fejléc jelenik meg, felkínálva a lehetőséget ahhoz, hogy a megoldás.

   ![Változás követési előkészítésére konfigurációs szalagcím](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. A megoldás engedélyezéséhez válassza ki a szalagcímet. Ha nem rendelkezik a következő elemek, a hozzáadott automatikusan:

   * [Naplófájl Analytics](../log-analytics/log-analytics-overview.md) munkaterület
   * [Automatizálási](../automation/automation-offering-get-started.md) fiók

5. Válassza ki a Naplóelemzési munkaterület a változáskövetési adatok naplók tárolásához, válasszon egy automatizálási fiókot követni a változásokat, és válassza **engedélyezése**.  
    Az értesítősáv tájékoztatja, hogy a megoldás engedélyezése folyamatban van. Az engedélyezés akár 15 percet is igénybe vehet.

## <a name="configure-change-tracking"></a>Változáskövetés konfigurálása

Miután a változáskövetés engedélyezve van, a **a változáskövetés** ablak jelenik meg. 

Mely fájlokat és beállításkulcsokat nyomon követéséhez kiválasztásához jelölje ki a **beállításainak szerkesztése**.

   ![Változáskövetés beállításainak szerkesztése](./media/automation-vm-change-tracking/change-edit-settings.png)

   A **munkaterület konfigurációs** ablak nyílik meg. 

Az a **munkaterület konfigurációs** ablakban adja meg a Windows-beállításkulcsok, a Windows-fájlok vagy a Linux-fájlok nyomon kell követni, a következő három szakasz a.

### <a name="add-a-windows-registry-key"></a>Windows beállításkulcs létrehozása

1. Az a **Windows beállításjegyzék** lapon jelölje be **Hozzáadás**.  
    A **Windows beállításjegyzék vegye fel a változások követése** ablak nyílik meg.

   ![Változáskövetés beállításjegyzék hozzáadása](./media/automation-vm-change-tracking/change-add-registry.png)

2. A **engedélyezve**, jelölje be **igaz**.
3. Az a **elem neve** adjon meg egy rövid nevet.
4. (Választható) Az a **csoport** mezőbe írja be a csoport nevét.
5. Az a **Windows beállításkulcs** mezőbe írja be a követni kívánt beállításkulcs nevét.
6. Kattintson a **Mentés** gombra.

### <a name="add-a-windows-file"></a>Windows-fájl hozzáadása

1. Az a **Windows fájlok** lapon jelölje be **Hozzáadás**.  
    A **Windows fájl hozzáadása a változások követése** ablak nyílik meg.

   ![Változáskövetés Windows fájl hozzáadása](./media/automation-vm-change-tracking/change-add-win-file.png)

2. A **engedélyezve**, jelölje be **igaz**.
3. Az a **elem neve** adjon meg egy rövid nevet.
4. (Választható) Az a **csoport** mezőbe írja be a csoport nevét.
5. Az a **elérési utat adjon meg** mezőbe írja be a nyomon követni kívánt fájl teljes elérési útját és nevét.
6. Kattintson a **Mentés** gombra.

### <a name="add-a-linux-file"></a>Linux-fájl hozzáadása

1. Az a **Linux fájlok** lapon jelölje be **Hozzáadás**.  
    A **Linux fájl hozzáadása a változások követése** ablak nyílik meg.

   ![Változáskövetés Linux fájl hozzáadása](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. A **engedélyezve**, jelölje be **igaz**.
3. Az a **elem neve** adjon meg egy rövid nevet.
4. (Választható) Az a **csoport** mezőbe írja be a csoport nevét.
5. Az a **elérési utat adjon meg** mezőbe írja be a nyomon követni kívánt fájl teljes elérési útját és nevét.
6. Az a **elérési út típusa** válassza **fájl** vagy **Directory**.
7. A **rekurzió**, ha követni a változásokat a megadott elérési út és minden fájl-és elérési utak alapján, válassza ki **a**. Csak a kiválasztott elérési út vagy a fájl követheti nyomon, válassza ki a **ki**.
8. A **használata Sudo**, nyomon követéséhez szükséges fájlokat a `sudo` hozzáférést, jelölje be a parancsot **a**. Máskülönben válassza **ki**.
9. Kattintson a **Mentés** gombra.

## <a name="view-changes"></a>Módosítások megtekintése

Az a **a változáskövetés** ablakban, a módosításokat megtekintheti a különböző kategóriákban a virtuális gép időbeli.

   ![Változáskövetés hozzáadása módosítások megtekintése](./media/automation-vm-change-tracking/change-view-changes.png)

A kategóriák és a módosítások megtekintéséhez időtartománya választhat. Az ablak tetején módosítások grafikus ábrázolása adott idő alatt tekintheti meg. Az ablak alján megtekintheti a legutóbbi módosításainak listája.

## <a name="view-change-tracking-log-data"></a>Változáskövetés naplóadatok megtekintéséhez

Változáskövetés Naplóelemzési küldött naplóadatokat hoz létre. Kereshet a naplók futó lekérdezések, válassza ki a **Naplóelemzési** tetején a **a változáskövetés** ablak.

   ![A változáskövetés naplóelemzési](./media/automation-vm-change-tracking/change-log-analytics.png)

Fut, és a naplófájlok keresési Naplóelemzési kapcsolatos további információkért lásd: [Naplóelemzési](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Következő lépések

* Változáskövetés kapcsolatos további információkért lásd: [a környezetben a módosítása a változáskövetési megoldás a szoftver változásainak követése](../log-analytics/log-analytics-change-tracking.md).
* A virtuális gépek számára frissítések kezelésével kapcsolatos további tudnivalókért lásd: [frissítés felügyeleti megoldás az OMS](../operations-management-suite/oms-solution-update-management.md).
