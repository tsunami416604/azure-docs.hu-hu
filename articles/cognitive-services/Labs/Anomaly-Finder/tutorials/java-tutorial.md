---
title: Anomáliadetektálás Java-alkalmazás – a Microsoft Cognitive Services |} A Microsoft Docs
description: Fedezze fel a Microsoft Cognitive Services az Anomáliadetektáló API-t használó Java-alkalmazás. Küldés eredeti adatpontok API-hoz és a várt érték és anomáliadetektálási pontokat.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: ac26e29f4a839f69b123489600c2c83fe395c48a
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247904"
---
# <a name="anomaly-detection-java-application"></a>Rendellenességek észlelése Java-alkalmazás

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ez a cikk azt ismerteti, egyszerű Java-alkalmazás használatával az Anomáliadetektáló API meghívásához.  
A példa az idősoros adatainak az Anomáliadetektáló API-t, elküldi az előfizetési kulccsal végzett, majd az anomáliadetektálási pontok és a várt érték az egyes lekérdezi az API-ból.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

Ez az oktatóanyag segítségével fejlesztette [az IntelliJ IDEA](https://www.jetbrains.com/idea). Emellett telepítenie kell, és [Java fejlesztői készlet (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8-as +, és a egy naprakész verzióját [az Apache Maven](http://maven.apache.org/) hozhat létre az eszköz.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Fizessen elő a rendellenességek észlelése és a egy előfizetési kulcs lekérése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Az oktatóanyag projektjének letöltése

1. Nyissa meg a MicrosoftAnomalyDetection [Java tárház](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Kattintson a Klónozás vagy Letöltés gomb.
3. Kattintson az oktatóanyag projektjének .zip fájl letöltése ZIP letöltése.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Nyissa meg az oktatóanyag projektjének

1. Bontsa ki a .zip-fájlt, az oktatóanyag projektjének.
2. Az IntelliJ IDEA, kattintson **fájl > Megnyitás**, fájl megnyitása vagy projekt párbeszédpanel jelenik meg.
3. Válassza ki a kibontott projekt a meghajtógyökér elérési útja, majd kattintson az OK gombra.
4. A projektek panelen bontsa ki a **src > fő > java**.
5. Kattintson duplán a fájl betöltése a szerkesztőbe com.microsoft.cognitiveservice.anomalydetection.Main.java.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Cserélje le a subscriptionKey és URI-régió

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Hozza létre és futtassa az oktatóanyag projektjének

1. A menü megjelenítéséhez kattintson a jobb gombbal bárhol com.microsoft.cognitiveservice.anomalydetection.Main.java forrás kód fülre. 
2. Válassza a "Main.main()" futtatása
3. A minta kérés eredményét adja vissza lesz, és a rendszer terminálon megjelenő.

### <a name="result-of-the-tutorial-project"></a>Az oktatóanyag projektjének eredménye

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
