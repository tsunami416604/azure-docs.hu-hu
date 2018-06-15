---
title: Kérdések és válaszok készítő korlátok - Azure kognitív szolgáltatások |} Microsoft Docs
description: Kérdések és válaszok készítő korlátok
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 4d2bafad08ffab76743cb802733a5d2f01a97f06
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "35349928"
---
# <a name="qna-maker-limits"></a>Kérdések és válaszok készítő korlátok
Kérdések és válaszok készítő között korlátok átfogó listáját.

## <a name="knowledge-bases"></a>Tudásbázis körrel

* Tudásbázis körrel maximális száma alapján [Azure Search réteg korlátok](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Az Azure Search réteg** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Közzétett ismeretcikkek körrel maximális száma a megengedett (Max indexek – 1 (teszt számára fenntartott)|2|14|49|199|199|2999|

## <a name="extraction-limits"></a>Kibontási korlátok
* Kiolvasható fájlok maximális számát és méretét: lásd: [QnAMaker díjszabása](https://azure.microsoft.com/en-in/pricing/details/cognitive-services/qna-maker/)
* A gyakran ismételt kérdések HTML-lapok bejárható QnAs kivonására mély hivatkozások maximális száma: 20

## <a name="metadata-limits"></a>Metaadatok korlátok
* A Tudásbázis, egy metaadatok mezők maximális száma alapján [Azure Search réteg korlátok](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity)

|**Az Azure Search réteg** | **Ingyenes** | **Basic** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximális metaadatmezőket Maker kérdések és válaszok szolgáltatás (közötti összes KB)|1000|100 *|1000|1000|1000|1000|

## <a name="knowledge-base-content-limits"></a>Tudásbázis tartalom korlátok
A Tudásbázis tartalma vonatkozó általános korlátozások:
* Válasz szövege hossza: 250000
* Kérdés szöveg hossza: 1000
* A metaadatok kulcs/érték szöveg hosszát: 100
* Támogatott karakterek metaadat neve: ábécé betűit, számokat, valamint _  
* Támogatott karakterek metaadataihoz megadott érték: kivételével: és |} 
* Fájl nevének hossza: 200
* Támogatott fájlformátumok: ".tsv", ".pdf", ".txt", ".docx", ".xlsx".
* Alternatív kérdések maximális számát: 100
* Kérdés-válasz pár maximális száma: függ a [Azure Search réteg](https://docs.microsoft.com/en-in/azure/search/search-limits-quotas-capacity#document-limits) választása 

## <a name="create-knowledge-base-call-limits"></a>Hozza létre a Tudásbázis hívás korlátozások:
Ezek jelentik az egyes korlátok Tudásbázis művelet; létrehozása Ez azt jelenti, hogy kattintva *létrehozása KB* vagy a CreateKnowledgeBase API felület meghívásakor.
* Alternatív kérdések / válasz maximális számát: 100
* URL-címek maximális száma: 10
* Fájlok maximális száma: 10

## <a name="update-knowledge-base-call-limits"></a>Tudásbázis hívás vonatkozó korlátok módosítása
Ezek képviselik, minden frissítés művelet; korlátairól Ez azt jelenti, hogy kattintva *mentéséhez és a vonat* vagy a UpdateKnowledgeBase API felület meghívásakor.
* Minden adatforrás nevének hossza: 300
* Kérdések maximális számát alternatív felvett vagy törölt kérdések: 100
* A metaadatok mezők maximális száma hozzá, vagy törölve: 10
* Frissíthető URL-címek maximális száma: 5
