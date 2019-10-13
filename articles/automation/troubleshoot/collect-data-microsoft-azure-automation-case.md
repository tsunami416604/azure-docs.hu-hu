---
title: A Microsoft Azure Automation esetének megnyitásakor begyűjtött adatok | Microsoft Docs
description: Ez a cikk néhány olyan alapszintű információt ismertet, amelyet össze kell gyűjtenie, mielőtt megnyit egy esetet a Azure Automation Microsoft Azure támogatással.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301749"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Összegyűjtendő adatok egy Microsoft Azure Automation-eset megnyitásakor

Ez a cikk néhány olyan alapszintű információt ismertet, amelyet össze kell gyűjtenie, mielőtt megnyit egy esetet a Azure Automation Microsoft Azure támogatással. Ez az információ nem szükséges az eset megnyitásához. Ugyanakkor a Microsoft segítséget nyújthat a probléma gyorsabb megoldásában. Emellett előfordulhat, hogy a támogatási mérnök ezt az adatkérést az eset megnyitása után is megkéri.

## <a name="collect-more-information"></a>További információk gyűjtése

Mielőtt megnyit egy Microsoft Azure Automation-támogatáshoz szükséges esetet, javasoljuk, hogy Gyűjtse össze a következő információkat.

### <a name="basic-data-collection"></a>Alapszintű adatgyűjtés

Gyűjtse össze a következő Tudásbázis-cikkben leírt adatokat:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Azure Automation-parancsfájlos diagnosztika rögzítése

## <a name="collect-data-depending-on-issue"></a>Adatok gyűjtése a probléma függvényében
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>A Linuxon Update Managementt érintő problémák esetén

1. A KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)-ban felsorolt elemek mellett futtassa a következő log Collection eszközt:

   [OMS Linux-ügynök naplójának gyűjtője](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Tömörítse a következő mappa tartalmát, majd küldje el a tömörített fájlt az Azure támogatási szolgálatának:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Ellenőrizze, hogy a OMS Linux-ügynök által jelentett munkaterület-azonosító megegyezik-e a frissítések figyeléséhez használt munkaterülettel.

### <a name="for-issues-that-affect-update-management-on-windows"></a>A Windows Update Managementt érintő problémák esetén

Az [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)-ben felsorolt elemek mellett tegye a következőket:

1. Exportálja a következő eseménynaplókat a EVTX formátumba:

   * Rendszer
   * Jelentkezés
   * Biztonság
   * Operations Manager
   * Microsoft-SMA/működési

2. Győződjön meg arról, hogy az ügynök által jelentett munkaterület-azonosító megegyezik a Windows-frissítések által figyelt munkaterülettel.

### <a name="for-issues-that-affect-a-job"></a>Feladatot érintő problémák

Az [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)-ben felsorolt elemek mellett Gyűjtse össze a következő információkat:

1. A **JobID** számának összegyűjtése (a problémát észlelő feladatokhoz):

   1. A Azure Portal lépjen az **Automation-fiókok** panelre.
   2. Válassza ki a hibaelhárításhoz használt **Automation-fiókot** .
   3. Válassza a **feladatok**lehetőséget.
   4. Válassza ki a hibaelhárítási feladatot.
   5. A **feladatok összegzése**területen keresse meg a **feladatok azonosítóját** (GUID).

   ![Feladatok azonosítója a feladatok összegzése ablaktáblán belül](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. A fiók nevének összegyűjtése:

   1. A Azure Portal lépjen az **Automation-fiókok** panelre.
   2. Szerezze be annak az **Automation-fióknak** a nevét, amelyen a hibaelhárítást végzi.

3. Gyűjtse össze a futtatott parancsfájl mintáját.

4. A naplófájlok összegyűjtése:

   1. A Azure Portal lépjen az **Automation-fiókok** panelre.
   2. Válassza ki a hibaelhárításhoz használt **Automation-fiókot** .
   3. Válassza a **feladatok**lehetőséget.
   4. Válassza ki a hibaelhárítási feladatot.
   5. Válassza ki **az összes naplót**.
   6. Az eredményül kapott panelen Gyűjtse össze az adatokat.

   ![Az összes naplóban felsorolt adat](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>A modulokat befolyásoló problémák esetén

Az "alapszintű adatgyűjtés" alatt található elemek mellett Gyűjtse össze a következő információkat:

* A követett lépések, hogy a probléma reprodukálható legyen.
* A hibaüzenetek képernyőképe.
* Képernyőkép az aktuális modulokról és azok verziószámáról.

## <a name="next-steps"></a>Következő lépések

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/).

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése**lehetőséget.