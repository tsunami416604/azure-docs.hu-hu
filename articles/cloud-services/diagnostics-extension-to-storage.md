---
title: Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban
description: Ismerje meg, hogyan gyűjtheti össze az Azure diagnosztikai adatait egy Azure Storage-fiókban, hogy megtekinthesse őket a számos elérhető eszköz egyikével.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 17430330d068fb55b45f073afecb8ba348286cb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472673"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban
A diagnosztikai adatok csak akkor tárolódnak véglegesen, ha átviszi azokat a Microsoft Azure storage-emulátorba vagy az Azure storage-ba. Egyszer -ban raktározás, lehet nézett -val egy -ból több elérhető szerszámok.

## <a name="specify-a-storage-account"></a>Tárfiók megadása
A ServiceConfiguration.cscfg fájlban használni kívánt tárfiókot adja meg. A fiókadatok egy konfigurációs beállításban lévő kapcsolati karakterláncként vannak definiálva. A következő példa a Visual Studio új Cloud Service-projektjéhez létrehozott alapértelmezett kapcsolati karakterláncot mutatja be:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Ezt a kapcsolati karakterláncot módosíthatja, hogy egy Azure-tárfiók fiókadatait adja meg.

Az összegyűjtött diagnosztikai adatok típusától függően az Azure Diagnostics a Blob szolgáltatást vagy a Table szolgáltatást használja. Az alábbi táblázat a megőrzött adatforrásokat és azok formátumát mutatja be.

| Adatforrás | Tárolási formátum |
| --- | --- |
| Azure-naplók |Tábla |
| IIS 7.0 naplók |Blob |
| Azure Diagnostics-infrastruktúranaplók |Tábla |
| Sikertelen kérelemnyomkövetési naplók |Blob |
| Windows eseménynaplók |Tábla |
| Teljesítményszámlálók |Tábla |
| összeomlási memóriaképek, |Blob |
| Egyéni hibanaplók |Blob |

## <a name="transfer-diagnostic-data"></a>Diagnosztikai adatok átvitele
Az SDK 2.5-ös és újabb verziói esetében a diagnosztikai adatok átvitelére vonatkozó kérés a konfigurációs fájlon keresztül történhet. A diagnosztikai adatokat a konfigurációban megadott ütemezett időközönként viheti át.

Az SDK 2.4 és az előző sdk esetén kérheti a diagnosztikai adatok átvitelét a konfigurációs fájlon keresztül és programozott módon. A programozott megközelítés lehetővé teszi az igény szerinti átvitelt is.

> [!IMPORTANT]
> Amikor diagnosztikai adatokat továbbít egy Azure-tárfiókba, a diagnosztikai adatok által használt tárolási erőforrások költségei merülnek fel.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnosztikai adatok tárolása
A naplóadatok at blob vagy table tárolóban tárolják a következő nevekkel:

**Táblák**

* **WadLogsTable** - A nyomfigyelővel kódba írt naplók.
* **WADDiagnosticInfrastructureLogsTable** – Diagnosztikai figyelő és konfigurációs változások.
* **WADDirectoriesTable** – Könyvtárak, hogy a diagnosztikai monitor figyel.  Ide tartoznak az IIS-naplók, az IIS sikertelen kérelemnaplói és az egyéni könyvtárak.  A blob naplófájl helye a Tároló mezőben van megadva, és a blob neve a RelativePath mezőben van megadva.  Az AbsolutePath mező jelzi a helyét és nevét a fájl, ahogy létezett az Azure virtuális gépen.
* **WADPerformanceCountersTable** – Teljesítményszámlálók.
* **WADWindowsEventLogsTable** – Windows eseménynaplók.

**Blobok**

* **wad-control-container** – (csak az SDK 2.4 és az előző) Tartalmazza az XML konfigurációs fájlokat, amelyek szabályozzák az Azure diagnosztika.
* **wad-iis-failedreqlogfiles** – Az IIS sikertelen kérelemnaplóiból származó információkat tartalmazza.
* **wad-iis-logfiles** – Az IIS-naplókkal kapcsolatos információkat tartalmaz.
* **"egyéni"** – A diagnosztikai figyelő által figyelt könyvtárak konfigurálásán alapuló egyéni tároló.  A blobtároló neve a WADDirectoriesTable táblában lesz megadva.

## <a name="tools-to-view-diagnostic-data"></a>A diagnosztikai adatok megtekintésének eszközei
Számos eszköz áll rendelkezésre az adatok tárolásra való átvitele utáni megtekintéséhez. Példa:

* Server Explorer a Visual Studio – Ha telepítette az Azure Tools for Microsoft Visual Studio, használhatja az Azure Storage-csomópont a Server Explorer megtekintheti az írásvédett blob és a táblázat adatait az Azure storage-fiókok. A helyi táremulátorfiókból és az Azure-hoz létrehozott tárfiókokból is megjelenítheti az adatokat. További információt a [Tárolási erőforrások böngészése és kezelése a Kiszolgálókezelővel](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)című témakörben talál.
* [A Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy önálló alkalmazás, amely lehetővé teszi, hogy könnyedén dolgozzon az Azure Storage-adatokkal Windows, OSX és Linux rendszeren.
* [Az Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) tartalmazza az Azure Diagnostics Managert, amely lehetővé teszi az Azure-ban futó alkalmazások által gyűjtött diagnosztikai adatok megtekintését, letöltését és kezelését.

## <a name="next-steps"></a>Következő lépések
[A folyamat nyomon követése egy felhőalapú szolgáltatásokban az Azure Diagnostics segítségével](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


