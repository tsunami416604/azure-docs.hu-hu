---
title: AI-alapú felhőalapú keresési szolgáltatás a mobileszköz-fejlesztéshez az Azure Cognitive Search használatával
description: Ismerje meg, hogyan használhat egy olyan szolgáltatást, amely egy AI-alapú felhőalapú keresési szolgáltatást használ a mobil alkalmazások fejlesztéséhez.
author: codemillmatt
ms.assetid: 34a8a070-0123-8982-8345-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 33ef251f60a833721e17d36d8df01a6095434af5
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450996"
---
# <a name="ai-powered-cloud-service-with-azure-cognitive-search"></a>AI-alapú felhőalapú szolgáltatás az Azure Cognitive Search
Az [Azure Cognitive Search](https://azure.microsoft.com/services/search/) egy felhőalapú keresési megoldás, amely lehetővé teszi a fejlesztők számára API-k és eszközök számára, hogy a webes, mobil-és vállalati alkalmazásokban gazdag keresési élményt adjanak hozzá. Az egyéni kód meghívja az adatfeldolgozást (indexelést) egy index létrehozásához és betöltéséhez. A másik oldalon az alkalmazás kódja lekérdezi a kérelmeket, és kezeli a válaszokat. A keresési élmény az ügyfélen az Azure Cognitive Search funkcióinak használatával van meghatározva, és a lekérdezés végrehajtása a saját szolgáltatásában létrehozott, saját és tárolt megőrzött indexen keresztül történik.

## <a name="azure-cognitive-search-features"></a>Azure Cognitive Search-funkciók
- **Szabadkézi szöveg keresése**: teljes szöveges keresést, egyszerű lekérdezési szintaxist és Lucene lekérdezési szintaxist biztosít.
- **Egyszerű pontozási profilok**: a dokumentációban szereplő értékek függvényében a modell relevanciája.
- **Geosearch funkciót**: a keresés eredményének egy fizikai helyre való közelsége alapján tárja fel az adatmennyiséget.
- **Felhasználói élmény funkciói**: automatikus kiegészítést, keresési javaslatokat, rendezést és lapozást biztosít.
- **Kognitív keresés**: az indexelési folyamatra alkalmazhatja a szöveges információk szövegből való kinyerését a képek és a szövegek elemzéséhez.
- **Knowledge Store**: az indexelés során létrehozott dúsítások mentése.
- Adatforrások: kereshető tartalom kinyerése az elsődleges adattárakban Azure SQL Database, Azure Cosmos DB és az Azure Blob Storage **szolgáltatáshoz**való csatlakozáskor.
- **Adatimportálás varázsló: az**indexelő konfigurálása. 
- **Index Designer**: egy indexet állít elő. 
- **Keresési Explorer**: tesztek lekérdezése és a pontozási profilok pontosítása.

Részletes leírást talál az Azure Cognitive Search [szolgáltatásairól](/azure/search/search-what-is-azure-search#feature-descriptions).

## <a name="references"></a>Referencia
- [Azure Portal](https://portal.azure.com) 
- [Az Azure Cognitive Search dokumentációja](/azure/search/)
- [Fejlesztői útmutató](https://azure.microsoft.com/resources/iot-developers-guide/)
- [Az Azure Cognitive Search használata](/azure/search/search-what-is-azure-search#how-to-use-azure-cognitive-search)
- [Rövid útmutatók](/azure/search/search-create-service-portal)

 