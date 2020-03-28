---
title: Adatok növekményes másolása
description: Ezekből az oktatóanyagokból megtudhatja, hogyan másolhat adatokat növekményes módon egy forrásadattárból egy céladattárba. Az első kimásolja az adatokat egy táblából.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: e87f8d648a617aab24008bd63596db31dd24ab68
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74926437"
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Növekményes módon betölti az adatokat egy forrásadattárból egy céladattárba

Adatintegrációs megoldások esetében gyakran használt forgatókönyv az adatok növekményes (vagy delta-) betöltése egy kezdeti, teljes adatbetöltést követően. A jelen szakaszban található oktatóanyagokból különböző módszereket ismerhet meg az adatok növekményes betöltésére vonatkozóan az Azure Data Factory használatával.

## <a name="delta-data-loading-from-database-by-using-a-watermark"></a>Különbözeti adatok betöltése az adatbázisból vízjel használatával
Ebben az esetben a forrásadatbázisban meghatároz egy vízjelet. A küszöbérték egy olyan oszlop, amely az utoljára frissített időbélyeget, vagy egy növekvő kulcsot tartalmaz. A változásbetöltési megoldás keretében a rendszer azokat az adatokat tölti be, amelyek megváltoztak egy régi és egy új küszöbérték között. A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható: 

![Vízjel használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

A részletes utasításokhoz tekintse meg a következő oktatóanyagokat: 
- [Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
- [Adatok növekményes másolása a helyszíni SQL Server több táblájából az Azure SQL Database-be](tutorial-incremental-copy-multiple-tables-powershell.md)

Sablonokról az alábbi témakörökben olvashat:
- [Változásadatok másolása vezérlőtáblával](solution-template-delta-copy-with-control-table.md)

## <a name="delta-data-loading-from-sql-db-by-using-the-change-tracking-technology"></a>Különbözeti adatok betöltése az SQL DB-ből a Változáskövetés technológia használatával
A változáskövetési technológia az SQL Server és az Azure SQL Database szolgáltatás egyik egyszerű megoldása, amely hatékony változáskövetési mechanizmust biztosít az alkalmazások számára. Lehetővé teszi az alkalmazások számára a beszúrt, frissített vagy törölt adatok egyszerű azonosítását. 

A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható:

![A változáskövetés használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Adatok növekményes másolása az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-powershell.md)

## <a name="loading-new-and-changed-files-only-by-using-lastmodifieddate"></a>Új és módosított fájlok betöltése csak a LastModifiedDate használatával
Az új és módosított fájlokat csak a LastModifiedDate használatával másolhatja a céltárolóba. Az ADF átkezi a forrástárolóösszes fájlját, alkalmazza a fájlszűrőt a LastModifiedDate adataival, és csak az új és frissített fájlt másolja a céltárolóba.  Kérjük, vegye figyelembe, ha hagyja, hogy az ADF hatalmas mennyiségű fájlt szítson, de csak néhány fájlt másoljon a rendeltetési helyre, akkor is elvárná a hosszú időtartamot a fájlszkennelés miatt.   

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Növekményes másolása az új és a módosított fájlokhoz az Azure Blob storage LastModifiedDate alapján az Azure Blob storage-ba](tutorial-incremental-copy-lastmodified-copy-data-tool.md)

Sablonokról az alábbi témakörökben olvashat:
- [Új fájlok másolása az utolsó módosítás dátuma szerint](solution-template-copy-new-files-lastmodifieddate.md)

## <a name="loading-new-files-only-by-using-time-partitioned-folder-or-file-name"></a>Az új fájlok betöltése csak időpartidott mappa vagy fájlnév használatával.
Csak akkor másolhat új fájlokat, ahol a fájlokat vagy mappákat már időszelettel particionálták a fájl vagy mappa nevének részeként (például /yyyy/mm/dd/file.csv). Ez a legteljesítményi megközelítés az új fájlok fokozatos betöltéséhez. 

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
- [Növekményes másolása az új fájlok idő particionált mappája vagy fájlneve alapján az Azure Blob storage-ból az Azure Blob storage-ba](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)

## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal: 

> [!div class="nextstepaction"]
>[Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
