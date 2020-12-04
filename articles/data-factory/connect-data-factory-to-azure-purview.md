---
title: Data Factory összekötése az Azure-beli hatáskörébe
description: Tudnivalók a Data Factory az Azure hatáskörébe való kapcsolódásáról
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 94b2ed8a25ca5cc837f6677dea1c0bbb54225fcd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603221"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Data Factory összekötése az Azure hatáskörébe (előzetes verzió)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebből a cikkből megtudhatja, hogyan csatlakoztatható az adatgyár az Azure hatáskörébe, és hogyan jelentheti az ADF-tevékenységek adatvonalának jelentését, az adatfolyamatokat és a SSIS-csomagok végrehajtását.

## <a name="connect-data-factory-to-azure-purview"></a>Az adatgyár összekötése az Azure-beli hatáskörébe
Az Azure hatáskörébe egy új felhőalapú szolgáltatás, amellyel az adatkezelők központilag kezelhetik az adatkezelést a Felhőbeli és a helyszíni környezetek között. Az adatgyárat az Azure hatáskörébe csatlakoztathatja, és a kapcsolat lehetővé teszi, hogy az Azure hatáskörébe tartozik a másolási, az adatfolyam-és a SSIS-csomag kibontási adatainak rögzítése. Az adatgyár Azure-beli hatáskörébe való regisztrálásával kapcsolatban lásd: [a Azure Data Factory és az Azure hatáskörébe való kapcsolódás](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory). 

## <a name="report-lineage-data-to-azure-purview"></a>Lineage-adatjelentések az Azure hatáskörébe
Ha az ügyfelek a másolás, az adatfolyam vagy a SSIS-csomag végrehajtása műveletet futtatják az Azure-beli adatgyárban, az ügyfelek megkapják a függőségi kapcsolatot, és magas szintű áttekintést nyújtanak a teljes munkafolyamat-folyamatról az adatforrások és a célhelyek között.
Az adatok az Azure-beli adatgyárból való gyűjtésével kapcsolatban lásd: a "a" [adatfeldolgozó-vonal](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities).

## <a name="next-steps"></a>További lépések
[A katalógus Lineage felhasználói útmutatója](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[Oktatóanyag: a leküldéses Data Factory az Azure hatáskörébe](turorial-push-lineage-to-purview.md)