---
title: Az Azure Stream Analytics-feladatok egy adatok bemenet hozzáadása
description: Útmutató a Stream Analytics-feladat, mint streaming adatok bemenetének Blog tárolási adatokat az Event Hubs vagy hivatkozás egy adatforrást a számítógéphez.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 13c3c948fbe24d5536b32967c8394060ee898377
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="add-a-streaming-data-input-or-reference-data-to-a-stream-analytics-job"></a>Adatfolyam-továbbítási adatok beviteli vagy hivatkozás adatok hozzáadása a Stream Analytics-feladat
Útmutató a Stream Analytics-feladat, mint az Event Hubs vagy hivatkozás adatokat a Blob storage adatbevitel streaming egy adatforrást a számítógéphez.

Az Azure Stream Analytics-feladatok összekapcsolható egy-egy beviteli vagy több, amelyek mindegyike egy kapcsolat egy meglévő adatforráson határozza meg. Adatokat küld, hogy az adatforrás, mivel a Stream Analytics-feladat által felhasznált és valós időben adatfolyam feldolgozása. A Stream Analytics első osztályú integrálva van [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) és [Azure Blob Storage tárolóban](../storage/blobs/storage-dotnet-how-to-use-blobs.md) belül és kívül a feladat előfizetés.

Ez a cikk Ez a lépés a [Stream Analytics képzési terv](/documentation/learning-paths/stream-analytics/).

## <a name="data-input-streaming-data-and-reference-data"></a>Bemenetek: adatok és referenciaadatok
Két különböző típusú bemenetében benne a Stream Analytics: adatfolyamokat és a referenciaadatoknál.

* **Az adatfolyamok**: Stream Analytics-feladatok tartalmaznia kell legalább egy adatfolyam-bemenetre felhasznált és át legyenek-e a feladat által. Az Azure Blob storage és az Azure Event Hubs adatforrások adatfolyam bemeneti is támogatottak. Az Azure Event Hubs segítségével csatlakoztatott eszközök, szolgáltatások és alkalmazások szolgáltatásból összegyűjtsék az eseményfolyamokat. Az Azure Blob storage eseményközpontokból tömeges adatfolyamként egy bemeneti forrásaként használható.  
* **Referenciaadatok**: a Stream Analytics támogatja a második típusú bemeneti hívott hivatkozást kiegészítő adatok.  Adatok mozgó, szemben az adatokat, de statikus lelassulnak, ami módosítása.  Ez általában look-ups és az adatfolyamok korrelációt végrehajtásához létrehozásához használt adatok csoportadatokra.  Az Azure Blob storage jelenleg az egyetlen támogatott bemeneti forrás a referenciaadatoknál.  

A Stream Analytics-feladat bemenete hozzáadása:

1. Az Azure portálon kattintson a **bemenetek** majd **vegye fel a bemeneti** a Stream Analytics-feladat.
   
    ![Azure portál – bemeneti hozzáadása.](./media/stream-analytics-add-inputs/1-stream-analytics-add-inputs.png)  
   
    Az Azure portálon kattintson a **bemenetek** csempére a Stream Analytics-feladat.  
   
    ![Azure portál – vegye fel a bemeneti adatok.](./media/stream-analytics-add-inputs/7-stream-analytics-add-inputs.png)  
2. Adja meg a bemeneti adatok típusát: vagy **adatfolyam** vagy **referenciaadatok**.
   
    ![A megfelelő adatok bemeneti, folyamatos átviteli vagy hivatkozás hozzáadása](./media/stream-analytics-add-inputs/2-stream-analytics-add-inputs.png)  
   
    ![A megfelelő adatok bemeneti, folyamatos átviteli vagy hivatkozás hozzáadása](./media/stream-analytics-add-inputs/8-stream-analytics-add-inputs.png)  
3. Egy adatfolyam-bemenet létrehozásakor, adja meg a bemeneti forrás típusát.  Ez a lépés is kimarad, csak a Blob storage jelenleg támogatott referenciaadatok létrehozása során.
   
    ![Adatfolyam-bemenetre adatok hozzáadása](./media/stream-analytics-add-inputs/3-stream-analytics-add-inputs.png)  
   
    ![Adatfolyam-bemenetre adatok hozzáadása](./media/stream-analytics-add-inputs/9-stream-analytics-add-inputs.png)  
4. Adja meg a beviteli mezőben a bemeneti Áljel rövid nevét.  Ez a név használandó a feladat lekérdezésben később tekintse meg a bemeneti adatok.
   
    Töltse ki a többi a szükséges csatlakozási tulajdonságokat az adatforráshoz való kapcsolódáshoz. 
   
    ![Event hub bemeneti hozzáadása](./media/stream-analytics-add-inputs/4-stream-analytics-add-inputs.png)  
5. Adja meg a bemeneti adatok szerializálási beállításait:
   
   * Győződjön meg arról, hogy a lekérdezések a várt módon működik-e, adja meg a **esemény szerializálási formátum** a bejövő adatok.  Támogatott szerializálási formátumok a következők: JSON, CSV és az Avro. Győződjön meg arról, a JSON formátum igazodik a meghatározás nem tartalmazza a decimális számok kezdő 0.
   * Ellenőrizze a **kódolás** az adatok számára.  Jelenleg az UTF-8 az egyetlen támogatott kódolási formátum.
     
     ![A bemeneti adatok szerializálási beállítások](./media/stream-analytics-add-inputs/5-stream-analytics-add-inputs.png)  
     
     ![A bemeneti adatok szerializálási beállítások](./media/stream-analytics-add-inputs/10-stream-analytics-add-inputs.png)  
6. A bemeneti létrehozásának befejezése után a Stream Analytics ellenőrzi, hogy tud-e csatlakozni a bemeneti forrás.  A kapcsolat tesztelése művelet állapotát megtekintheti az értesítési központban.
   
    ![A streamadatok bemeneti kapcsolat tesztelése](./media/stream-analytics-add-inputs/6-stream-analytics-add-inputs.png)  
   
    ![A streamadatok bemeneti kapcsolat tesztelése](./media/stream-analytics-add-inputs/11-stream-analytics-add-inputs.png)  

## <a name="get-help-with-streaming-data-inputs"></a>Segítség az adatok bemeneti adatfolyam
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

