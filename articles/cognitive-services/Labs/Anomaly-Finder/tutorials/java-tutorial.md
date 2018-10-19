---
title: 'Oktatóanyag: Anomáliadetektálás a Java használatával'
titlesuffix: Azure Cognitive Services
description: Megismerkedhet egy Java-alkalmazással, amely az Anomaly Detection API-t használja. Ha az eredeti adatpontokat elküldi az API-nak, visszakapja a várt értéket és az attól eltérő pontokat.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 4b544e2e59a40cebf75042c4040b84bceebcecf7
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887700"
---
# <a name="tutorial-anomaly-detection-with-java-application"></a>Oktatóanyag: Anomáliadetektálás Java-alkalmazással

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ez a cikk az Anomaly Detection API meghívását mutatja be egy egyszerű Java-alkalmazás használatával.  
A példa elküldi az idősoradatokat az Anomaly Detection API-nak az előfizetési kulccsal, és az API visszaküldi az összes rendellenes pontot és az elvárt értéket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

Ezt az oktatóanyagot az [IntelliJ IDEA](https://www.jetbrains.com/idea) használatával állítottuk össze. Emellett a [Java fejlesztői készlet (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8-as vagy azt követő verzióját és az [Apache Maven](http://maven.apache.org/) összeállítási eszköz egy naprakész verzióját is telepítenie kell.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Előfizetés az anomáliadetektálás használatára és előfizetői azonosító beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Az oktatóprojekt letöltése

1. Nyissa meg a MicrosoftAnomalyDetection [Java-adattárat](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Kattintson a Clone or download (Klónozás vagy letöltés) gombra.
3. A Download ZIP (ZIP letöltése) gombra kattintva töltse le az oktatóprojekt .zip-fájlját.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Az oktatóprojekt megnyitása

1. Bontsa ki az oktatóprojekt .zip-fájlját.
2. Az IntelliJ IDEA felületén kattintson a **File > Open** (Fájl > Megnyitás) lehetőségre, és megnyílik az Open File or Project (Fájl vagy projekt megnyitása) párbeszédablak.
3. Válassza ki a kicsomagolt projekt gyökerének elérési útját, majd kattintson az OK gombra.
4. A Project (Projekt) panelen bontsa ki az **src > main > java** elemet.
5. Duplán a com.microsoft.cognitiveservice.anomalydetection.Main.java elemre kattintva töltse be a fájlt a szerkesztőbe.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>A subscriptionKey és az URI-régió cseréje

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Az oktatóprojekt létrehozása és futtatása

1. A jobb gombbal a com.microsoft.cognitiveservice.anomalydetection.Main.java forráskódlap bármely részére kattintva jelenítse meg a menüt. 
2. Válassza a Run 'Main.main()' (Main.main() futtatása) lehetőséget.
3. A kérésminta visszaadott eredménye megjelenik a terminálon.

### <a name="result-of-the-tutorial-project"></a>Az oktatóprojekt eredménye

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
