---
title: Azure PowerShell-minták az Azure Data Factory-hoz
description: Azure PowerShell-minták – Parancsfájlok az adatgyárak létrehozásához és kezeléséhez.
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/16/2018
ms.openlocfilehash: 705b2464285b747ea7999b112733b7e99f761f22
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419085"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure PowerShell-minták az Azure Data Factory-hoz

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Az alábbi táblázat az Azure Data Factory minta-Azure PowerShell-parancsfájljaira mutató hivatkozásokat tartalmaz.

| |  |
|---|---|
|**Adatok másolása**||
|[Blobok másolása egy mappából egy másik mappába egy Azure Blob Storage-ban](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl az Azure Blob Storage egyik mappájából másolja a blobokat egy másik mappába ugyanabban a Blob Storage-ban. |
|[Adatok másolása a helyszíni SQL Server kiszolgálóról az Azure Blob Storage szolgáltatásba](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl adatokat másol egy helyszíni SQL Server-adatbázisból egy Azure blobstorage-ba. |
|[Tömeges másolat](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a minta PowerShell-parancsfájl adatokat másol egy Azure SQL-adatbázis több táblából egy Azure SQL-adattárházba. |
|[Növekményes másolat](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a minta PowerShell-parancsfájl csak új vagy frissített rekordokat tölt be egy forrás-adattárból egy fogadó adattárba a forrásból a fogadóba származó adatok kezdeti teljes másolása után. |
|**Adatok átalakítása**||
|[Adatok átalakítása Spark-fürt használatával](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl úgy alakítja át az adatokat, hogy egy programot egy Spark-fürtön futtat. |
|**SSIS-csomagok felemelése és átcsoportosítása az Azure-ba**||
|[Azure SSIS integrációs modul létrehozása](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ez a PowerShell-parancsfájl egy Azure-SSIS-integrációs futásidejű, az Azure-beli SQL Server Integration Services (SSIS) csomagokat futtató üzemidőt tartalmaz. |



