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
ms.openlocfilehash: b42a7667321215247233776eb0f2d733d67ab945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73684451"
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Azure PowerShell-minták az Azure Data Factory-hoz

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



