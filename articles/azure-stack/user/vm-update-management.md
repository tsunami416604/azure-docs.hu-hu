---
title: Virtuális gép frissítése és kezelése az Azure Stack |} A Microsoft Docs
description: Ismerje meg, az Update Management, Change Tracking és Inventory megoldásainak használata az Azure Automationben az Azure Stack üzembe helyezett Windows virtuális gépek kezeléséhez.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: rtiberiu
ms.openlocfilehash: 1ef20dc35b069c5f12c2f31d0979949be27271e0
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324274"
---
# <a name="azure-stack-vm-update-and-management"></a>Az Azure Stack VM update és kezelése
A következő Azure Automation-megoldás funkciók segítségével Windows üzembe helyezett virtuális gépeket az Azure Stack használata kezelheti:

- **[Frissítéskezelés](https://docs.microsoft.com/azure/automation/automation-update-management)**. Az Update Management megoldással gyorsan felmérheti az elérhető frissítések minden ügynökszámítógépen állapotát és a folyamat szükséges frissítéseinek telepítése a Windows virtuális gépek kezeléséhez.

- **[A változáskövetés](https://docs.microsoft.com/azure/automation/automation-change-tracking)**. Telepített szoftverek, a Windows-szolgáltatások, a Windows beállításjegyzék és a fájlokat a figyelt kiszolgálókról történő változásokat küldi el a Log Analytics szolgáltatás a felhőben a feldolgozáshoz. A fogadott adatokat logikát alkalmaz, és a felhőszolgáltatás-adatait rögzíti. A Change Tracking irányítópultján található információk segítségével könnyen megtekintheti az a kiszolgáló-infrastruktúrájában elvégzett módosítások.

- **[Készlet](https://docs.microsoft.com/azure/automation/automation-vm-inventory)**. A leltár nyomon követése az Azure Stack Windows virtuális gépként és leltár konfigurálása egy böngészőalapú felhasználói felületet biztosít. 

> [!IMPORTANT]
> Ezek a megoldások ugyanazok, mint az Azure virtuális gépek kezelésére szolgáló azokat. Az Azure és az Azure Stack Windows virtuális gépek felügyelt ugyanolyan módon, egy közös felületen, ugyanazokkal az eszközökkel. Az Azure Stack-beli virtuális gépek is díjszabása ugyanaz, mint az Azure virtuális gépek használatakor az Update Management, Change Tracking és Inventory megoldások az Azure Stack használatával.

## <a name="prerequisites"></a>Előfeltételek
Néhány előfeltételnek teljesülnie kell, mielőtt használja ezeket a funkciókat, frissítése és az Azure Stack Windows virtuális gépek kezelése. Ezek közé tartozik, amely kell tenni a lépéseket az Azure Portalon, valamint az Azure Stack felügyeleti portálon.

### <a name="in-the-azure-portal"></a>Az Azure Portalon
A készlet, a Change Tracking és a frissítés kezelése az Azure automation szolgáltatásokat az Azure Stack Windows virtuális gépek használatához, először engedélyeznie kell ezeket a megoldásokat az Azure-ban.

> [!TIP]
> Ha már rendelkezik ezeket a funkciókat az Azure virtuális gépek engedélyezve van, használhatja a már meglévő LogAnalytics munkaterület hitelesítő adatait. Ha már rendelkezik egy LogAnalytics munkaterület azonosítója és az elsődleges kulcs, amelyet használni szeretne, ugorjon előre [a következő szakaszban](.\vm-update-management.md#in-the-azure-stack-administration-portal). Ellenkező esetben továbbra is ebben a szakaszban egy új LogAnalytics munkaterületet és automation-fiók létrehozásához.

Az első lépése abban, hogy ezek a megoldások [LogAnalytics munkaterület létrehozása](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) az Azure-előfizetésében. Log Analytics-munkaterület egy saját adattárházzal, adatforrások és megoldások egyedi Log Analytics-környezetet. Miután létrehozott egy munkaterületet, vegye figyelembe a munkaterület azonosítója és kulcsa. Ezek az információk megtekintéséhez nyissa meg a munkaterület panel, kattintson a **speciális beállítások**, és tekintse át a **munkaterület-Azonosítót** és **elsődleges kulcs** értékeket. 

Ezt követően kell [Automation-fiók létrehozása](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Az Automation-fiók egy tároló az Azure Automation-erőforrások számára. A környezetek elkülönítésére vagy további rendszerezésére az automatizált munkafolyamatokat és erőforrásokat biztosít. Az automation-fiók létrehozása után meg kell ahhoz, hogy a készlet változások követése, és felügyeleti funkciók frissítése. Ehhez kövesse az alábbi lépéseket mindegyik funkciónak az engedélyezéséhez:

1. Az Azure Portalon nyissa meg az Automation-fiókot, amelyet használni szeretne.

2. Válassza ki a megoldás (vagy **készlet**, **Change tracking**, vagy **frissítéskezelés**).

3. Használja a **munkaterület kiválasztása...**  legördülő listában válassza ki a Log Analytics-munkaterület használatára.

4. Győződjön meg arról, hogy helyesen szerepel-e az összes többi adatot, és kattintson **engedélyezése** a megoldás engedélyezéséhez.

5. Ismételje meg a 2 – 4 összes három megoldás engedélyezéséhez. 

   [![](media/vm-update-management/1-sm.PNG "Automation-fiók szolgáltatások engedélyezése")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="in-the-azure-stack-administration-portal"></a>Az Azure Stack felügyeleti portálon
Miután engedélyezte az Azure Automation-megoldások az Azure Portalon, ezután kell bejelentkezni az Azure Stack felügyeleti portálon, a felhő rendszergazdájához, és töltse le a **Azure Update és a konfigurációkezelés** Azure bővítmény Piactéri elem stack. 

   ![Az Azure frissítési és konfigurációs felügyeleti bővítmény Piactéri elem](media/vm-update-management/2.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Az Update Management engedélyezése a virtuális gépek az Azure Stack
Kövesse az alábbi lépéseket az Azure Stack Windows virtuális gépek az update management engedélyezése.

1. Jelentkezzen be az Azure Stack felhasználói portálon.

2. Az Azure Stack felhasználói portálon, nyissa meg a bővítmények panelt, a Windows, amelynek az ezekkel a megoldásokkal engedélyezéséhez kattintson a kívánt virtuális gépeknek **+ Hozzáadás**, jelölje be a **Azure Update és a konfigurációkezelés**bővítményt, majd kattintson **létrehozás**:

   [![](media/vm-update-management/3-sm.PNG "Windows VM-bővítmény panel")](media/vm-update-management/3-lg.PNG#lightbox)

3. Adja meg a korábban létrehozott munkaterület azonosítója és az elsődleges kulcsot az ügynököt a LogAnalytics munkaterület összekapcsolása, és kattintson a **OK** a bővítmény telepítéséhez.

   [![](media/vm-update-management/4-sm.PNG "A munkaterület azonosítója és kulcsa")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Leírtak szerint a [automation update management dokumentációja](https://docs.microsoft.com/azure/automation/automation-update-management), engedélyezze a frissítéskezelési megoldás az egyes virtuális Gépekhez, amelyet kezelni szeretne. A megoldás engedélyezéséhez a munkaterületre jelentő összes virtuális gép, jelölje ki **frissítéskezelés**, kattintson a **gépek kezelése**, majd válassza ki a **engedélyezés minden rendelkezésre álló és jövőbeli gépen** lehetőséget.

   [![](media/vm-update-management/5-sm.PNG "A munkaterület azonosítója és kulcsa")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Ismételje meg ezt a lépést minden megoldás engedélyezése az Azure Stack Windows virtuális gépek, hogy a jelentés a munkaterülethez. 
  
Az Azure-frissítés és a konfigurációkezelés bővítmény engedélyezése után történik vizsgálat naponta kétszer az egyes felügyelt Windows virtuális Gépekhez. A Windows API hívása az utolsó frissítésének időpontját, hogy módosultak-e az állapot meghatározására a lekérdezéshez 15 percenként. Ha a állapota megváltozott, a megfelelőségi vizsgálatot kezdeményez.

Miután a virtuális gépek vizsgálata, ezek megjelennek a frissítéskezelési megoldás az Azure Automation-fiók: 

   [![](media/vm-update-management/6-sm.PNG "A munkaterület azonosítója és kulcsa")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> 30 perc és a felügyelt számítógépekből származó frissített adatok megjelennek az irányítópulton 6 óráig is eltarthat.

Az Azure Stack Windows virtuális gépek és Azure virtuális gépeken ütemezett frissítéstelepítések most már szerepelni.

## <a name="enable-update-management-using-a-resource-manager-template"></a>Resource Manager-sablon használatával az Update Management engedélyezése
Ha az Azure Stack Windows virtuális gépek nagy számú, használhat [ezen Azure Resource Manager-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) könnyebben üzembe helyezheti a megoldást, a Windows virtuális gépeken. A sablon üzembe helyezi a Microsoft Monitoring Agent bővítményt egy meglévő Windows virtuális Gépet, és hozzáadja azt a meglévő Azure LogAnalytics munkaterülethez.
 
## <a name="next-steps"></a>További lépések
[Az SQL Server teljesítményének optimalizálásához](azure-stack-sql-server-vm-considerations.md)
