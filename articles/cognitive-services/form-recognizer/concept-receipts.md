---
title: Nyugták – űrlap-felismerő
titleSuffix: Azure Cognitive Services
description: Ismerje meg a beérkezési elemzéssel kapcsolatos fogalmakat az űrlap-felismerő API-használat és-korlátok alapján.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 7a14b3c93a6c545648faf28c991764e990e487b1
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724969"
---
# <a name="receipt-concepts"></a>Bevételezési fogalmak

A AzureForm-felismerő képes elemezni a nyugtákat a saját előre elkészített modelljeinek használatával. A beérkezési API Kinyeri a legfontosabb információkat az értékesítési visszaigazolásokból az angol nyelven, például a kereskedelmi név, a tranzakció dátuma, a tranzakciók összege, a sorok és egyebek. 

## <a name="understanding-receipts"></a>A visszaigazolások ismertetése 

Számos vállalat és személy továbbra is az értékesítési visszaigazolásokból származó adatok manuális kinyerésére támaszkodik, akár üzleti költségekkel kapcsolatos jelentéseket, költségtérítéseket, adózási célokat, költségvetést vagy más célokat. Ezekben a forgatókönyvekben gyakran a fizikai visszaigazolás képei szükségesek az érvényesítéshez.  

Az adatok e nyugtából való automatikus kinyerése bonyolult lehet. Előfordulhat, hogy a visszaigazolások gyűrött és nehezen olvashatók, és a beérkezések smartphone-lemezképei alacsony színvonalúak. Emellett a beérkezési sablonok és a mezők nagy mértékben a piac, a régió és a kereskedő szerint változhatnak. Ezek a kihívások mind az kinyerési, mind a helyszíni észlelési folyamat során egyedi problémát jelentenek.  

Az optikai karakterfelismerés (OCR) és az előre elkészített beérkezési modell használatával a beérkezési API lehetővé teszi ezen bevételezés-feldolgozási forgatókönyvek használatát. Mivel a modell előre be van tanítva az adataink számára, egyszerűen elemezheti a nyugtákat egy lépésben a &mdash; modell betanítása vagy a szükséges címkézés nélkül.

![minta nyugtája](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Mit tesz a beérkezési API? 

Az előre létrehozott beérkezési API kibontja az értékesítési nyugták tartalmát &mdash; , amelyet általában egy étteremben, a kereskedőn vagy egy élelmiszerboltban szereztek be.

### <a name="fields-extracted"></a>Kinyert mezők

* Kereskedő neve 
* Kereskedelmi címe 
* Kereskedő telefonszáma 
* Tranzakció dátuma 
* Tranzakciós idő 
* Részösszeg 
* Adó 
* Összesen 
* Tipp 
* Sorok kinyerése (például cikkmennyiség, elem ára, elem neve)

### <a name="additional-features"></a>További funkciók

A beérkezési API a következő információkat is megadja:

* Bevételezés típusa (például a részletezett, a bankkártya stb.)
* Mező megbízhatósági szintje (minden mező a hozzá tartozó megbízhatósági értéket adja vissza)
* OCR nyers szöveg (OCR – kinyert szöveges kimenet a teljes bevételezéshez)

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Támogatott területi beállítások 

* Az előre összeállított **beérkezési v 2.0** (GA) támogatja a Sales nyugtákat az en-us területi beállításban
* A **Pre-Builder v 2.1 – preview. 1** (nyilvános előzetes verzió) további támogatást nyújt a következő en-bevételezési területi beállításokhoz: 
  * EN-AU 
  * EN-CA 
  * EN-GB 
  * EN-IN 

  > [!NOTE]
  > Nyelvi bevitel 
  >
  > Az előre összeállított nyugták v 2.1 – preview. 1 egy opcionális kérési paraméterrel megadhatja a további angol piacoktól érkező bevételezési területi beállításokat. Az ausztrál (EN-AU), Kanada (EN-CA), Nagy-Britannia (EN-GB) és India (en-IN) típusú értékesítési nyugták esetében megadhatja a területi beállításokat a jobb eredmények eléréséhez. Ha nincs megadva területi beállítás a v 2.1-ben – előzetes verzió. 1, a modell alapértelmezett értéke az EN-US modell lesz.

## <a name="customer-scenarios"></a>Felhasználói forgatókönyvek  

A beérkezési API-val kinyert adatok különféle feladatok elvégzésére használhatók. Az alábbiakban néhány példát láthat arra, hogy ügyfeleink hogyan teljesítik a beérkezési API-t. 

### <a name="business-expense-reporting"></a>Üzleti költségek jelentése  

Gyakran az üzleti költségek bejelentése magában foglalja a beérkezési képekből származó adatok manuális bevitelét. A beérkezési API-val a kibontott mezők használatával részben automatizálhatja ezt a folyamatot, és gyorsan elemezheti a nyugtákat.  

Mivel a beérkezési API egyszerű JSON-kimenettel rendelkezik, több módon is használhatja a kinyert mezők értékeit. Integrálja a költségeket a belső költségekkel rendelkező alkalmazásokkal a költségelszámolás előzetes kitöltéséhez. Ennél a forgatókönyvnél további információért olvassa el, hogy a Acumatica hogyan használja a beérkezési API-t, hogy [a költségek kevésbé fájdalmas folyamaton](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure)alapulnak.  

### <a name="auditing-and-accounting"></a>Naplózás és nyilvántartás 

A beérkezési API kimenete nagy számú, a költség-jelentési és a költségtérítési folyamat különböző pontjain elvégezhető kiadások elemzésére is használható. A visszaigazolásokat feldolgozhatja manuális audit vagy gyors jóváhagyások osztályozásához.  

A nyugta kimenete is hasznos lehet az üzleti vagy személyes használatra való általános könyveléshez. A beérkezési API használatával bármilyen nyers beérkezési képet vagy PDF-adatokat átalakíthat egy végrehajtható digitális kimenetbe.

### <a name="consumer-behavior"></a>Fogyasztói viselkedés 

A nyugták hasznos információkat tartalmaznak, amelyek segítségével elemezheti a fogyasztói viselkedést és a vásárlási trendeket.

