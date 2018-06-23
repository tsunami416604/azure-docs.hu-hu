---
title: Anomáliadetektálás Java-alkalmazás – Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Megismerkedhet a Java-alkalmazást, amely az Anomáliadetektálási észlelési API kognitív Microsoft-szolgáltatásokban. Eredeti adatpontok küldeni API, és a várt érték és anomáliadetektálási pontok.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348166"
---
# <a name="anomaly-detection-java-application"></a>Az anomáliadetektálási észlelési Java-alkalmazás

Ez a cikk bemutatja, egyszerű Java-alkalmazások használata az Anomáliadetektálási észlelési API meghívására.  
A példa az Anomáliadetektálási észlelési API adatsorozat időadatok elküldi az előfizetés kulccsal, majd az anomáliadetektálási pontok és a várt érték az egyes adatpontokban lekérdezi az API-n.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

Ez az oktatóanyag segítségével fejlesztette [IntelliJ IDEA](https://www.jetbrains.com/idea). Is szeretne telepíteni, és [Java fejlesztői készlet (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 +, és az aktuális verzió [tartozó Apache Maven](http://maven.apache.org/) eszköz felépítéséhez.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Az anomáliadetektálás szolgáltatásra, és egy előfizetés kulcs beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Az oktatóanyag projekt letöltése

1. Keresse fel a MicrosoftAnomalyDetection [Java tárház](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Kattintson a Klónozás vagy Letöltés gomb.
3. Kattintson a letöltés a ZIP-az oktatóanyag projekt egy .zip fájl letöltése.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Az oktatóanyag projekt megnyitása

1. Bontsa ki a .zip-fájlt az oktatóanyag projekt.
2. IntelliJ IDEA kattintson **fájl > nyitott**, fájl megnyitása vagy projekt párbeszédpanel jelenik meg.
3. Adja meg a kibontott projekt legfelső szintű elérési útvonalát, majd kattintson az OK gombra.
4. A projektek panelen bontsa ki a **src > fő > java**.
5. Kattintson duplán a fájl betöltése a szerkesztőbe com.microsoft.cognitiveservice.anomalydetection.Main.java.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Cserélje le a subscriptionKey és URI régió

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Hozza létre, és futtatja az oktatóprogram projektet

1. Nyissa meg a menüben kattintson a jobb gombbal bárhol com.microsoft.cognitiveservice.anomalydetection.Main.java forrás kód fülre. 
2. Válassza ki a "Main.main()" futtatása
3. A minta kérelem eredménye vissza lesz, és a rendszer Terminálszolgáltatások látható.

### <a name="result-of-the-tutorial-project"></a>Az oktatóanyag projekt eredménye

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
