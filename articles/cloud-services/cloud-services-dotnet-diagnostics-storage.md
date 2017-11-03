---
title: "Az Azure Storage diagnosztikai adatokat tároló és a nézet |} Microsoft Docs"
description: "Az Azure diagnosztikai adatokat az Azure Storage és az megtekintése"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: robb
ms.openlocfilehash: 374cc179e13c00e439415e3df16e0c6d5ccba5e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Az Azure Storage diagnosztikai adatok tárolása és nézet
Diagnosztikai adatok nem tárolódik véglegesen, kivéve, ha azt át a Microsoft Azure storage emulator vagy az Azure storage. Egyszer, megőrzés is megtekinthető számos elérhető eszköz egyike.

## <a name="specify-a-storage-account"></a>Adja meg a storage-fiók
Megadja a a ServiceConfiguration.cscfg fájlban használni kívánt tárfiókot. A fiók adatait egy kapcsolati karakterláncot egy konfigurációs beállítás típusúként van definiálva. A következő példa bemutatja a létrehozott egy új Felhőszolgáltatás-projektet a Visual Studio alapértelmezett kapcsolati karakterlánc:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Módosíthatja a kapcsolati karakterláncot adja meg a fiók adatait az Azure storage-fiók.

Gyűjtött diagnosztikai adatok típusától függően az Azure Diagnostics használja a Blob szolgáltatás vagy a Table szolgáltatás. Az alábbi táblázat az adatforrásokat, amelyek megmaradnak, és a formátuma.

| Adatforrás | Tárolási formátum |
| --- | --- |
| Az Azure naplói |Tábla |
| Az IIS 7.0-naplók |Blob |
| Az Azure Diagnostics infrastruktúra naplók |Tábla |
| Sikertelen kérelmek nyomkövetési naplóit |Blob |
| Windows-Eseménynapló |Tábla |
| Teljesítményszámlálók |Tábla |
| Összeomlási memóriaképek |Blob |
| Egyéni hibanaplókat |Blob |

## <a name="transfer-diagnostic-data"></a>Diagnosztikai adatok átvitele
SDK 2.5 és újabb a kérelem diagnosztikai adatok átviteléhez akkor fordulhat elő, a konfigurációs fájlban. Diagnosztikai adatok vihetők át a konfigurációt a rendszeres időközönként.

Az SDK 2.4 és az előző a diagnosztikai adatok átviteléhez keresztül, valamint a konfigurációs fájlban, programozott módon kérhet. A programozási megközelítés lehetővé teszi igény átvitelek tegye.

> [!IMPORTANT]
> Diagnosztikai adatok átvitele az Azure storage-fiók, amikor Ön tudomásával a diagnosztikai adatok által használt tárolási erőforrások költségeit.
> 
> 

## <a name="store-diagnostic-data"></a>Diagnosztikai adatok tárolásához
A következő nevű vagy a Blob, vagy a tábla tárolási naplóadatokat tárolja:

**Táblák**

* **WadLogsTable** - kód megadása a nyomkövetés-figyelő írt naplókat.
* **WADDiagnosticInfrastructureLogsTable** -diagnosztikai figyelő és a konfigurációs módosításokat.
* **WADDirectoriesTable** – a diagnosztikai figyelő által figyelt könyvtárak.  Ez magában foglalja az IIS-napló, az IIS nem sikerült a kérelem naplókat, és egyéni könyvtárak.  A blob naplófájl helyét a tároló mezőben megadott és a blob neve a RelativePath mezőben.  A AbsolutePath mező jelzi, helyét és nevét, a fájl adott verzióját az Azure virtuális géphez.
* **WADPerformanceCountersTable** – teljesítményszámlálók.
* **WADWindowsEventLogsTable** – Windows-eseményt naplózza.

**Blobok**

* **üvegvatta-vezérlő-tároló** – (csak SDK 2.4 és előző) az Azure diagnostics meghatározza XML konfigurációs fájlokat tartalmazza.
* **üvegvatta-az iis-failedreqlogfiles** – IIS sikertelen kérelem naplók adatait tartalmazza.
* **üvegvatta-az iis-naplófájlok** – IIS-napló adatait tartalmazza.
* **"egyéni"** – egy egyéni tároló alapján a diagnosztikai figyelő által figyelt könyvtárak beállítása.  A blob-tároló neve a WADDirectoriesTable történik.

## <a name="tools-to-view-diagnostic-data"></a>Eszközök diagnosztikai adatainak megtekintéséhez
Számos eszköz után szeretné megtekinteni az adatokat tároló átkerül érhetők el. Példa:

* A Visual Studio - Server Explorer Ha telepítette az Azure-eszközök a Microsoft Visual Studio segítségével az Azure Storage-csomópont a Server Explorer csak olvasható blob és table adatok megtekintése az Azure storage-fiókok a. A helyi storage emulator fiókhoz tartozó megjeleníthető adatok, és is a storage-fiókok hozott létre az Azure-bA. További információkért lásd: [böngészés és tárolási erőforrások kezelése a Server Explorer](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md).
* [A Microsoft Azure Tártallózó](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy különálló alkalmazás, amelynek segítségével egyszerűen dolgozhat Azure Storage-adatokkal Windows, OSX és Linux.
* [Az Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) Azure Diagnostics Managert, amely lehetővé teszi a megtekintése, töltse le és kezelése az Azure-on futó alkalmazások által gyűjtött diagnosztikai adatokat tartalmazza.

## <a name="next-steps"></a>Következő lépések
[A folyamat Felhőszolgáltatások-alkalmazásokban az Azure diagnosztikai nyomkövetési](cloud-services-dotnet-diagnostics-trace-flow.md)

