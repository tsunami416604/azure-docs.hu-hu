---
title: Korlátozások és határok – QnA Maker
titleSuffix: Azure Cognitive Services
description: A QnA Maker részei a Tudásbázis és a szolgáltatás metaadat-korlátokkal rendelkeznek. Fontos, hogy a Tudásbázis tesztelése és közzététele érdekében ezeket korlátokon belül.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 51e494101b13b3020f79c9acfc4d853cee995d7b
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911422"
---
# <a name="qna-maker-knowledge-base-limits-and-boundaries"></a>A QnA Maker Tudásbázis korlátok és határok
A QnA Maker között korlátok átfogó listáját.

## <a name="knowledge-bases"></a>Tudásbázisok

* Tudásbázisok maximális száma alapján [Azure Search szint korlátok](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Az Azure Search szolgáltatásréteg** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Közzétett tudásbázisok engedélyezett maximális száma|2|14|49|199|199|2,999|

 Például ha a csomag esetében a 15 engedélyezett indexeket, közzéteheti 14 tudásbázisok (1 index / közzétett Tudásbázis). A tizenötödik index `testkb`, elkészítését és tesztelését tudásbázisok szolgál. 

## <a name="extraction-limits"></a>Kibontási korlátok
* Eseménystreamek olvashatóak fájlok maximális száma és a maximális fájlméret: Lásd: [QnAMaker díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/)
* – Gyakori kérdések HTML-lapok a a kivonási QnA-tudásbázisok bejárható részletes kapcsolatok maximális száma: 20

## <a name="metadata-limits"></a>Metaadat-korlátok
* Metaadatokat tartalmazó mezőket egy Tudásbázis, maximális száma alapján [Azure Search szint korlátok](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)

|**Az Azure Search szolgáltatásréteg** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximális metaadatokat tartalmazó mezőket a QnA Maker szolgáltatás (között az összes Tudásbázis)|1,000|100 *|1,000|1,000|1,000|1,000|

## <a name="knowledge-base-content-limits"></a>Tudásbázis-tartalmat korlátok
Általános korlátozások a tartalmat a Tudásbázis:
* Válasz a szöveg hossza: 25,000
* Kérdés szövege hossza: 1,000
* A metaadatok kulcs/érték szöveg hossza: 100
* Támogatott karakterek metaadat neve: Betűk, számjegyek és _  
* Támogatott karakterek metaadataihoz megadott érték: Mindenhol, kivéve: és |} 
* Fájlnév hossza: 200
* Támogatott fájlformátumok: ".tsv", ".pdf", "txt", ".docx", ".xlsx".
* Alternatív kérdések maximális száma: 100
* Kérdés-válasz párt maximális száma: Attól függ, a [Azure Search szolgáltatásréteg](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#document-limits) kiválasztása 

## <a name="create-knowledge-base-call-limits"></a>Hozza létre a Tudásbázis hívás korlátai:
Ezen felelnek a minden korlátokat Tudásbázis művelet; létrehozása azt jelenti, kattintson a *létrehozása KB* vagy a CreateKnowledgeBase API-t hívná.
* Alternatív kérdésnek válasz maximális száma: 100
* URL-címek maximális száma: 10
* Fájlok maximális száma: 10

## <a name="update-knowledge-base-call-limits"></a>Tudásbázis hívás vonatkozó korlátok módosítása
Ezek képviselik a frissítési műveletek; korlátai azt jelenti, kattintson a *mentéséhez és betanítunk* vagy a UpdateKnowledgeBase API-t hívná.
* Minden adatforrás nevének hossza: 300
* Alternatív kérdések felvett vagy törölt maximális száma: 100
* Metaadatokat tartalmazó mezőket felvett vagy törölt maximális száma: 10
* Frissíthető URL-címek maximális száma: 5

## <a name="next-steps"></a>További lépések

Ismerje meg, mikor és hogyan szolgáltatásszintek módosítása:

* [A QnA Maker](how-to/upgrade-qnamaker-service.md#upgrade-qna-maker-management-sku): amikor szüksége van további kérdések és válaszok a Tudásbázisban, az aktuális szint feletti a QnA Maker szolgáltatást a tarifacsomag frissítése.
* [Keresés](how-to/upgrade-qnamaker-service.md#upgrade-app-service) – Ha az ügyfél alkalmazásából további kérések kiszolgálására, frissítse az app service-tarifacsomag kell a Tudásbázis.
* [App Service-ben](how-to/upgrade-qnamaker-service.md#upgrade-azure-search-service): Ha azt tervezi, számos tudásbázisok rendelkezik, az Azure Search szolgáltatás tarifacsomag frissítése.