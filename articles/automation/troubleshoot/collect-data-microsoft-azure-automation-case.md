---
title: A Microsoft Azure Automation eseteinek megnyitásakor gyűjtandó adatok| Microsoft dokumentumok
description: Ez a cikk néhány olyan információt ismertet, amelyeket az Azure Automation esetének Microsoft Azure-támogatással történő megnyitása előtt kell összegyűjtenie.
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
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679403"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Összegyűjtendő adatok egy Microsoft Azure Automation-eset megnyitásakor

Ez a cikk néhány olyan információt ismertet, amelyeket az Azure Automation esetének Microsoft Azure-támogatással történő megnyitása előtt kell összegyűjtenie. Ez az információ nem szükséges az eset megnyitásához. Ez azonban segíthet a Microsoftnak a probléma gyorsabb megoldásában. Az eset megnyitása után a támogatási szakember is kérheti ezeket az adatokat.

## <a name="basic-data"></a>Alapadatok

Gyűjtse össze a Tudásbázis [4034605 – Az Azure Automation által írt parancsfájlokkal írt diagnosztika – Című](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)cikkében ismertetett alapvető adatokat.

## <a name="data-for-update-management-issues-on-linux"></a>Adatok frissítéskezelési problémák linuxon

1. A KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)kb.

   [OMS Linux ügynök naplógyűjtője](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Tömörítse a **/var/opt/microsoft/omsagent/run/automationworker/** mappa tartalmát, majd küldje el a tömörített fájlt az Azure-támogatásnak.
 
3. Ellenőrizze, hogy a munkaterület azonosítója, amelynek a Log Analytics ügynök linuxos jelentések megegyezik az azonosítóa a munkaterület figyelt frissítések.

## <a name="data-for-update-management-issues-on-windows"></a>Frissítéskezelési adatokkal kapcsolatos problémák a Windows rendszeren

1. Gyűjtsön adatokat a [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)alatt felsorolt tételekről.

2. Exportálja a következő eseménynaplókat AZ EVTX formátumba:

   * Rendszer
   * Alkalmazás
   * Biztonság
   * Operations Manager
   * Microsoft-SMA/Működési

3. Ellenőrizze, hogy a munkaterület azonosítója, amelynek az ügynök jelentése megegyezik a Windows-frissítések által figyelt munkaterület azonosítójával.

## <a name="data-for-job-issues"></a>A feladatokkal kapcsolatos problémák adatai

1. Gyűjtsön adatokat a [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)alatt felsorolt tételekről.

2. Gyűjtse össze a probléma miatt imitoráshoz a feladat azonosítójának összegyűjtését:

   1. Az Azure Portalon nyissa meg az **Automation-fiókok című.**
   2. Válassza ki a hibaelhárítást elhárító Automation-fiókot, és jegyezze fel a nevet.
   3. Válassza a **Feladatok**lehetőséget.
   4. Válassza ki a hibaelhárítási feladatot.
   5. A Feladat összegzése ablaktáblában keresse meg a **feladatazonosító**GUID értékét.

   ![Feladatazonosító a Feladat összegzése ablaktáblában](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Gyűjtse össze a futtatott parancsfájl mintáját.

4. Gyűjtse össze a naplófájlokat:

   1. Az Azure Portalon nyissa meg az **Automation-fiókok című.**
   2. Válassza ki a hibaelhárításhoz szükséges Automation-fiókot.
   3. Válassza a **Feladatok**lehetőséget.
   4. Válassza ki a hibaelhárítási feladatot.
   5. Válassza az **Összes napló lehetőséget.**
   6. Az eredményül kapott ablaktáblában gyűjtse össze az adatokat.

   ![A minden napló alatt felsorolt adatok](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Modulproblémák adatai

Az alapvető [adatelemekmellett](#basic-data)gyűjtse össze a következő információkat is:

* A követett lépések, hogy a probléma reprodukálható legyen.
* Képernyőképek a hibaüzenetekről.
* Képernyőképek az aktuális modulokról és azok verziószámáról.

## <a name="next-steps"></a>További lépések

Ha további segítségre van szüksége:

* Válaszokat kaphat az Azure szakértőitől [az Azure Forums segítségével.](https://azure.microsoft.com/support/forums/)
* Lépjen [@AzureSupport](https://twitter.com/azuresupport)kapcsolatba a hivatalos Microsoft Azure-fiókkal az ügyfélélmény javítása érdekében, ha az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.
* Az Azure-támogatási incidens fájlja. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**
