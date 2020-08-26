---
title: Az Azure szinapszis Analytics kimenete Azure Stream Analytics
description: Ez a cikk az Azure szinapszis Analytics-t ismerteti Azure Stream Analytics-kimenetként.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 5d281bf33542354e2904805deed2f1deaf2725a2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875737"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics-preview"></a>Az Azure szinapszis Analytics kimenete Azure Stream Analytics (előzetes verzió)

Az [Azure szinapszis Analytics](https://azure.microsoft.com/services/synapse-analytics) (korábban SQL Data Warehouse) egy korlátlan elemzési szolgáltatás, amely a nagyvállalati adattárházat és a Big adatelemzéseket is egyesíti. 

Azure Stream Analytics feladatok kimenete az Azure szinapszis Analytics egy SQL-készlet táblájában végezhető el, amely akár 200 MB/s sebességig is feldolgozható. Ez támogatja a legigényesebb valós idejű elemzési és a gyors elérésű adatfeldolgozási igényeket olyan számítási feladatokhoz, mint a jelentéskészítés és az irányítópult.  

Az SQL-készlet táblának léteznie kell ahhoz, hogy kimenetként hozzá lehessen adni a Stream Analytics feladatokhoz. A tábla sémájának meg kell egyeznie a feladatok kimenetében szereplő mezőkkel és típusokkal. 

Ha az Azure Szinapszisot kimenetként szeretné használni, győződjön meg arról, hogy a Storage-fiók konfigurálva van. Navigáljon a Storage-fiók beállításaihoz a Storage-fiók konfigurálásához. Csak a táblákat támogató Storage-fiókok engedélyezettek: általános célú v2 és általános célú v1. Csak a standard szintű csomag kiválasztása. A prémium szint nem támogatott.

## <a name="output-configuration"></a>Kimeneti konfiguráció

A következő táblázat felsorolja a tulajdonságok nevét és leírásait az am Azure szinapszis Analytics kimenetének létrehozásához.

|Tulajdonság neve|Leírás|
|-|-|
|Kimeneti alias |Egy rövid név, amely a lekérdezésekben a lekérdezés kimenetének az adatbázisba való irányításához használatos. |
|Adatbázis |Annak az SQL-készletnek a neve, ahová a kimenetet küldi. |
|Kiszolgálónév |Az Azure szinapszis-kiszolgáló neve.  |
|Felhasználónév |Az adatbázishoz írási hozzáféréssel rendelkező Felhasználónév. A Stream Analytics csak az SQL-hitelesítést támogatja. |
|Jelszó |Az adatbázishoz való kapcsolódáshoz használt jelszó. |
|Táblázat  | A tábla neve, ahol a kimenet meg van írva. A tábla neve megkülönbözteti a kis-és nagybetűket. A táblázat sémájának pontosan meg kell egyeznie a feladatok által létrehozott mezők és típusok számával.|

## <a name="next-steps"></a>Következő lépések

* [Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](stream-analytics-quick-create-portal.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása az Azure CLI használatával](quick-create-azure-cli.md)
* [Gyors útmutató: Azure Stream Analytics-feladatok létrehozása ARM-sablon használatával](quick-create-azure-resource-manager.md)
* [Gyors útmutató: Stream Analytics-feladatok létrehozása Azure PowerShell használatával](stream-analytics-quick-create-powershell.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio használatával](stream-analytics-quick-create-vs.md)
* [Rövid útmutató: Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-vs-code.md)