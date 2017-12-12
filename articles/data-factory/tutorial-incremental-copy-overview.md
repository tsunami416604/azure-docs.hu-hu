---
title: "Adatok növekményes másolása az Azure Data Factory használatával | Microsoft Docs"
description: "Ezekből az oktatóanyagokból megtudhatja, hogyan másolhat adatokat növekményes módon egy forrásadattárból egy céladattárba. Az első kimásolja az adatokat egy táblából. "
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/05/2017
ms.author: shlo
ms.openlocfilehash: 2ae6cb42685dfb227bd75f83e73dfdf646ab909f
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="incrementally-load-data-from-a-source-data-store-to-a-destination-data-store"></a>Növekményes módon betölti az adatokat egy forrásadattárból egy céladattárba

Adatintegrációs megoldások esetében gyakran használt forgatókönyv az adatok növekményes (vagy delta-) betöltése egy kezdeti, teljes adatbetöltést követően. A jelen szakaszban található oktatóanyagokból különböző módszereket ismerhet meg az adatok növekményes betöltésére vonatkozóan az Azure Data Factory 2-es verziójának használatával.

## <a name="delta-data-loading-using-a-watermark"></a>Változásadatok betöltése vízjel használatával
Ebben az esetben a forrásadatbázisban meghatároz egy vízjelet. A vízjel egy olyan oszlop, amely az utoljára frissített időbélyeget, vagy egy növekvő kulcsot tartalmaz. A változásbetöltési megoldás keretében a rendszer egy régi és egy új vízjel közötti adatokat tölti be. A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható: 

![Vízjel használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-watermark.png)

A részletes utasításokhoz tekintse meg a következő oktatóanyagokat: 

- [Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
- [Adatok növekményes másolása a helyszíni SQL Server több táblájából az Azure SQL Database adatbázisba](tutorial-incremental-copy-multiple-tables-powershell.md)


## <a name="delta-data-loading-using-the-change-tracking-technology"></a>Változásadatok betöltése a változáskövetési technológia használatával
A változáskövetési technológia az SQL Server és az Azure SQL Database szolgáltatások egyik egyszerű megoldása, amely hatékony változáskövetési mechanizmust biztosít az alkalmazások számára. Lehetővé teszi az alkalmazások számára a beszúrt, frissített vagy törölt adatok egyszerű azonosítását. 

A jelen megközelítés munkafolyamatának ábrázolása a következő diagramon látható:

![A változáskövetés használatának munkafolyamata](media/tutorial-incremental-copy-overview/workflow-using-change-tracking.png)

A részletes utasításokhoz lásd a következő oktatóanyagot: <br/>
[Adatok növekményes másolása az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-powershell.md)


## <a name="next-steps"></a>Következő lépések
Folytassa a következő oktatóanyaggal: 

> [!div class="nextstepaction"]
>[Adatok növekményes másolása az Azure SQL Database egyik táblájából az Azure Blob Storage-ba](tutorial-incremental-copy-powershell.md)
