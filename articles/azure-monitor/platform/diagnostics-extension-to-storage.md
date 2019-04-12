---
title: Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban
description: Azure diagnostics-adatok beolvasása az Azure Storage-ba, és azok megtekintése
services: azure-monitor
author: jpconnock
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: jeconnoc
ms.subservice: diagnostic-extension
ms.openlocfilehash: 23379e9d9bb29efb7fb026260e8245e8eb8a2d71
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54468522"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban
Diagnosztikai adatok nem tartósan tárolja, ha azt át a Microsoft Azure storage emulator vagy az Azure storage. Egyszer a storage szolgáltatással, azt is megtekinthetők a számos elérhető eszközök egyikét.

## <a name="specify-a-storage-account"></a>A storage-fiók megadása
Megadhatja a a ServiceConfiguration.cscfg fájlban használni kívánt tárfiókot. A fiók adatait a konfigurációs beállítások kapcsolati karakterláncként van definiálva. Az alábbi példa bemutatja a Felhőszolgáltatás a Visual Studio új projekt számára létrehozott alapértelmezett kapcsolati karakterlánc:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Módosíthatja ezt a fiókot információk az Azure storage-fiókhoz tartozó kapcsolati karakterláncot.

Gyűjtött diagnosztikai adatok típusától függően az Azure Diagnostics használja a Blob szolgáltatás vagy a Table service. Az alábbi táblázat bemutatja azokat az adatforrásokat, tárolja és a formátuma.

| Adatforrás | Tárolási formátum |
| --- | --- |
| Az Azure-naplók |Tábla |
| Az IIS 7.0-naplók |Blob |
| Azure Diagnostics-infrastruktúranaplók |Tábla |
| Sikertelen kérelmek nyomkövetési naplók |Blob |
| Windows-eseménynaplók |Tábla |
| Teljesítményszámlálók |Tábla |
| összeomlási memóriaképek, |Blob |
| Egyéni hibanaplók |Blob |

## <a name="transfer-diagnostic-data"></a>Diagnosztikai adatok átvitele
SDK 2.5-ös és újabb a diagnosztikai adatok átadására irányuló kérelmet fordulhat elő, a konfigurációs fájl segítségével. Diagnosztikai adatok átadhatja a konfigurációban megadott időközönként.

Az SDK 2.4 és az előző kérheti a diagnosztikai adatok átviteléhez, valamint a konfigurációs fájl segítségével, programozott módon. Programozott megközelítés lehetővé teszi, hogy igény szerinti adatforgalom.

> [!IMPORTANT]
> Diagnosztikai adatok átvitele az Azure storage-fiók, ha a diagnosztikai adatok által használt tárolási erőforrások költségeinek számítunk fel.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnosztikai adatok Store
Naplóadatok tárolja a Blob vagy Table storage a következő nevekkel:

**Táblák**

* **WadLogsTable** – írt kód a nyomkövetés-figyelő naplókat.
* **WADDiagnosticInfrastructureLogsTable** -diagnosztikai figyelő és a konfigurációs módosítások.
* **WADDirectoriesTable** – a diagnosztikai figyelő által figyelt könyvtárak.  Ez magában foglalja az IIS-naplók, IIS sikertelen kérelmekről készült naplók, és egyéni könyvtárak.  A blob naplófájl helye a tároló mezőben van megadva, és a blob nevét, a RelativePath mezőben.  A AbsolutePath jelzi a hely és a fájl nevét, az Azure virtuális gépen is létezett.
* **WADPerformanceCountersTable** – teljesítményszámlálók.
* **WADWindowsEventLogsTable** – Windows eseménynaplói nem tartalmaznak.

**Blobok**

* **WAD-vezérlés – container** – (csak az SDK 2.4 és az előző) szabályozza az Azure diagnostics XML konfigurációs fájlokat tartalmazza.
* **WAD-az iis-failedreqlogfiles** – a sikertelen kéréseket az IIS-naplók adatait tartalmazza.
* **WAD-az iis-naplófájlok** – IIS-naplók információkat tartalmaz.
* **"egyéni"** – egy egyéni tároló alapján a diagnosztikai figyelő által figyelt könyvtárak beállítása.  Ez a blobtároló nevét kell megadni a WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Eszközök diagnosztikai adatainak megtekintése
Storage követően az adatok megtekintéséhez számos eszközök érhetők el. Példa:

* Server Explorerben a Visual Studio - Ha telepítette az Azure-eszközöket a Microsoft Visual Studio segítségével az Azure Storage-csomópont a Server Explorerben csak olvasható blobok és táblák adatainak megtekintése az Azure storage-fiókok. Helyi emulátor tárfiókban lévő adatokat megjelenítheti és is a storage-fiókok számára létrehozott Azure. További információkért lásd: [böngészés és tárolási erőforrások kezelése a Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [A Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, os x és Linux rendszereken.
* [Az Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) magában foglalja az Azure Diagnostics Managert, amely lehetővé teszi, hogy a megtekintése, letöltése és kezelése az Azure-ban futó alkalmazások által gyűjtött diagnosztikai adatok.

## <a name="next-steps"></a>További lépések
[A Cloud Services-alkalmazás az Azure Diagnostics segítségével folyamatot nyomon követése](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


