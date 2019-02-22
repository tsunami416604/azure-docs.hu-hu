---
title: Adatok növekményes másolása az Azure Data Factory használatával | Microsoft Docs
description: Ezekből az oktatóanyagokból megtudhatja, hogyan másolhat adatokat növekményes módon egy forrásadattárból egy céladattárba. Az első kimásolja az adatokat egy táblából.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: 69c4e7729ca4255eca24648e4c5fe03669f6186c
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593590"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Növekményes módon betölti az adatokat egy forrásadattárból egy céladattárba

Adatintegrációs megoldások esetében gyakran használt forgatókönyv az adatok növekményes (vagy delta-) betöltése egy kezdeti, teljes adatbetöltést követően. A jelen szakaszban található oktatóanyagokból különböző módszereket ismerhet meg az adatok növekményes betöltésére vonatkozóan az Azure Data Factory használatával.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Változásadatok betöltése küszöbérték használatával adatbázis
Ebben az esetben a forrásadatbázisban meghatároz egy vízjelet. A küszöbérték egy olyan oszlop, amely az utoljára frissített időbélyeget, vagy egy növekvő kulcsot tartalmaz. A változásbetöltési megoldás keretében a rendszer azokat az adatokat tölti be, amelyek megváltoztak egy régi és egy új küszöbérték között. A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható: 

![Vízjel használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

A részletes utasításokhoz tekintse meg a következő oktatóanyagokat: 

- [Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
- [Adatok növekményes másolása a helyszíni SQL Server több táblájából az Azure SQL Database-be](tutorial-incremental-copy-multiple-tables-powershell.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Változásadatok betöltése az SQL DB-ből a változáskövetési technológia használatával
A változáskövetési technológia az SQL Server és az Azure SQL Database szolgáltatás egyik egyszerű megoldása, amely hatékony változáskövetési mechanizmust biztosít az alkalmazások számára. Lehetővé teszi az alkalmazások számára a beszúrt, frissített vagy törölt adatok egyszerű azonosítását. 

A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható:

![A változáskövetés használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
[Adatok növekményes másolása az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-their-lastmodifieddate"></a>Csak azok LastModifiedDate segítségével új és módosított fájlok betöltése
A metaadatok (LastModifiedDate) a fájlok beolvasása először, és csak a cél a majd másolhatja az új és módosított fájlok.

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
[Növekményes másolása az adatok másolása eszköz használatával LastModifiedDate alapján új és módosított fájlok](tutorial-incremental-copy-lastmodified-copy-data-tool.md)


## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal: 

> [!div class="nextstepaction"]
>[Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
