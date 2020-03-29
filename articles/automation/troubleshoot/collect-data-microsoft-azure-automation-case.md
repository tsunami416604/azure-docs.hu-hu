---
title: A Microsoft Azure Automation eseteinek megnyitásakor gyűjtandó adatok| Microsoft dokumentumok
description: Ez a cikk néhány alapvető információt ismertet, amelyeket össze kell gyűjtenie, mielőtt megnyitna egy esetet az Azure Automation számára a Microsoft Azure-támogatással.
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
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849377"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Összegyűjtendő adatok egy Microsoft Azure Automation-eset megnyitásakor

Ez a cikk néhány alapvető információt ismertet, amelyeket össze kell gyűjtenie, mielőtt megnyitna egy esetet az Azure Automation számára a Microsoft Azure-támogatással. Ez az információ nem szükséges az eset megnyitásához. Ez azonban segíthet a Microsoftnak a probléma gyorsabb megoldásában. Az eset megnyitása után a támogatási szakember is kérheti ezeket az adatokat.

## <a name="collect-more-information"></a>További információk gyűjtése

Mielőtt megnyitna egy esetet a Microsoft Azure Automation-támogatás számára, javasoljuk, hogy gyűjtse össze az alábbi adatokat.

### <a name="basic-data-collection"></a>Alapvető adatgyűjtés

Gyűjtse össze a Tudásbázis következő cikkében ismertetett adatokat:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) Az Azure Automation által parancsfájlozott diagnosztika rögzítése

## <a name="collect-data-depending-on-issue"></a>Adatok gyűjtése a problémától függően
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>A Linux frissítési menedzsmentet érintő problémák esetén

1. A KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)kb.

   [OMS Linux ügynök naplógyűjtője](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Tömörítse a következő mappa tartalmát, majd küldje el a tömörített fájlt az Azure-támogatásnak:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Ellenőrizze, hogy az OMS Linux-ügynök által jelentett munkaterület-azonosító megegyezik-e a frissítések figyelt munkaterületével.

### <a name="for-issues-that-affect-update-management-on-windows"></a>A Windows frissítés-kezelést érintő problémák esetén

A [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)alatt felsorolt tételeken kívül tegye a következőket:

1. Exportálja a következő eseménynaplókat AZ EVTX formátumba:

   * Rendszer
   * Alkalmazás
   * Biztonság
   * Operations Manager
   * Microsoft-SMA/Működési

2. Ellenőrizze, hogy az ügynök által jelentett munkaterület-azonosító megegyezik-e a Windows-frissítések által figyelt munkaterülettel.

### <a name="for-issues-that-affect-a-job"></a>A munkát érintő problémák esetén

A [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)alatt felsorolt tételeken kívül gyűjtse össze a következő információkat:

1. A **JobID-szám** összegyűjtése (a problémát tapasztaló feladathoz):

   1. Az Azure Portalon nyissa meg az **Automation-fiókok** panelt.
   2. Válassza ki a hibaelhárítást elhárító **automatizálási fiókot.**
   3. Válassza a **Feladatok**lehetőséget.
   4. Válassza ki a hibaelhárítási feladatot.
   5. A **Feladat összegzése**csoportban keressen egy **feladatazonosítót** (GUID).

   ![Feladatazonosító a Feladat összegzése ablaktáblában](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. A fiók névének összegyűjtése:

   1. Az Azure Portalon nyissa meg az **Automation-fiókok** panelt.
   2. A hibaelhárításhoz szükséges **Automation-fiók** nevének beszerezése.

3. Gyűjtse össze a futtatott parancsfájl mintáját.

4. Gyűjtse össze a naplófájlokat:

   1. Az Azure Portalon nyissa meg az **Automation-fiókok** panelt.
   2. Válassza ki a hibaelhárítást elhárító **automatizálási fiókot.**
   3. Válassza a **Feladatok**lehetőséget.
   4. Válassza ki a hibaelhárítási feladatot.
   5. Válassza az **Összes napló lehetőséget.**
   6. A kapott panelen gyűjtse össze az adatokat.

   ![A minden napló alatt felsorolt adatok](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>A modulokat érintő problémák esetén

Az "Alapszintű adatgyűjtés" alatt szereplő tételeken kívül gyűjtse össze a következő információkat:

* A követett lépések, hogy a probléma reprodukálható legyen.
* Képernyőkép a hibaüzenetekről.
* Képernyőkép az aktuális modulokról és azok verziószámáról.

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége a cikk bármely pontján, forduljon az Azure szakértőihez [az MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/forums/)

Másik lehetőségként nyújtson be egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
