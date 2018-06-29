---
title: Kereskedelmi katalógus moderálás a gépi tanulási és az Azure Content moderátor AI |} Microsoft Docs
description: A gépi tanulási és AI automatikusan mérsékelt kereskedelmi katalógusok
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 6177758eaa3e611ad67da0778d889df48b052d90
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37095751"
---
# <a name="ecommerce-catalog-moderation-with-machine-learning"></a>Kereskedelmi katalógus moderálás machine Learning segítségével

Ez az oktatóanyag azt megtudhatja, hogyan gép támogatású AI technológiák kombinálásával emberi moderálás arra, hogy egy intelligens termékkatalógus-rendszert a machine learning-alapú intelligens kereskedelmi katalógus moderálás végrehajtásához.

![Minősített Termékképek](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Üzleti forgatókönyv

Gép támogatású technológiák használatával besorolt és közepes szintű Termékképek ezekben a kategóriákban található:

1. Felnőtt (meztelenség)
2. Ellopható (kétértelmű)
3. Celebrities
4. USA jelzők
5. Játékok
6. Toll

## <a name="tutorial-steps"></a>Oktatóanyag lépései

Az oktatóanyag végigvezeti a felhasználót az alábbi lépéseket:

1. Regisztráció és a tartalom moderátor csoportot létrehozni.
2. Konfigurálja a lehetséges celebrity és jelző tartalom moderálás címkék (címke).
3. Tartalom moderátor kép API segítségével ellenőrizze a lehetséges felnőtt és ellopható tartalmát.
4. A számítógép Látástechnológiai API segítségével ellenőrizze a lehetséges celebrities.
5. Az egyéni stratégiai szolgáltatás segítségével ellenőrizze a lehetséges jelenlétével jelzők.
6. Jelenleg a nuanced vizsgálat eredményeit, emberi áttekintésre és végső révén.

## <a name="create-a-team"></a>A csoport létrehozása

Tekintse meg a [gyors üzembe helyezés](quick-start.md) tartalom moderátor regisztrálhat és a csoport létrehozása lapon. Megjegyzés: a **Csoportazonosító** a a **hitelesítő adatok** lap.


## <a name="define-custom-tags"></a>Egyéni címkék megadása

Tekintse meg a [címkék](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) cikk egyéni címkék megadása. A beépített mellett **felnőtt** és **ellopható** címkéket, az új címkék lehetővé teszik a felülvizsgálati eszközzel címkékkel leíró neveket.

Ebben az esetben ezeket az egyéni címkéket meghatároztuk (**celebrity**, **jelző**, **velünk**, **játék**, **toll**):

![Egyéni címkék konfigurálása](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Az API-kulcsokat és a végpontok felsorolása

1. Az oktatóanyag három API-k és tartozó kulcsok és API-végpontok használja.
2. Az API-végpontok eltérőek aszerint a előfizetés régiók és a tartalom moderátor tekintse át a csapat azonosítója.

> [!NOTE]
> Az oktatóanyag célja, hogy előfizetés-kulcsok használata a régiókban, a következő végpontok látható. Győződjön meg arról, az API-kulcsokat, és a régió, egyébként pedig a kulcsok nem működnek együtt a következő végpontok URI kereséséhez:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Felnőtt és ellopható tartalom keresése

1. A függvény argumentuma egy kép URL-CÍMÉT és a kulcs-érték párokból álló tömb paraméterekként.
2. Meghívja a tartalom moderátor kép API a felnőtt és Racy pontszámok eléréséhez.
3. Ha az érték nagyobb, mint 0,4 (tartomány: 0 és 1), azt az értéket állít be a **ReviewTags** a tömb **igaz**.
4. A **ReviewTags** tömb jelöljön ki a megfelelő címkét a felülvizsgálati eszköz használatával.

        public static bool EvaluateAdultRacy(string ImageUrl, ref KeyValuePair[] ReviewTags)
        {
            float AdultScore = 0;
            float RacyScore = 0;

            var File = ImageUrl;
            string Body = $"{{\"DataRepresentation\":\"URL\",\"Value\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(ImageUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // {“answers”:[{“answer”:“Hello”,“questions”:[“Hi”],“score”:100.0}]}
                // Parse the response body. Blocking!
                GetAdultRacyScores(response.Content.ReadAsStringAsync().Result, out AdultScore, out RacyScore);
            }

            ReviewTags[0] = new KeyValuePair();
            ReviewTags[0].Key = "a";
            ReviewTags[0].Value = "false";
            if (AdultScore > 0.4)
            {
                ReviewTags[0].Value = "true";
            }

            ReviewTags[1] = new KeyValuePair();
            ReviewTags[1].Key = "r";
            ReviewTags[1].Value = "false";
            if (RacyScore > 0.3)
            {
                ReviewTags[1].Value = "true";
            }
            return response.IsSuccessStatusCode;
        }

## <a name="scan-for-celebrities"></a>Celebrities vizsgálata

1. Regisztráljon egy [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision) , a [számítógép Látástechnológiai API](https://azure.microsoft.com/services/cognitive-services/computer-vision/).
2. Kattintson a **API-kulcs beolvasása** gombra.
3. Fogadja el a feltételeket.
4. A bejelentkezés, választhat a rendelkezésre álló internetes fiókjai.
5. Megjegyzés: az API-kulcsokat, a szolgáltatás lapon jelenik meg.
    
   ![Számítógép Látástechnológiai API-kulcsokat](images/tutorial-computer-vision-keys.PNG)
    
6. Tekintse meg a projekt forráskódot, a függvény, amely megvizsgálja a kép és a számítógép Látástechnológiai API-t.

         public static bool EvaluateComputerVisionTags(string ImageUrl, string ComputerVisionUri, string ComputerVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage Response = CallAPI(ComputerVisionUri, ComputerVisionKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            if (Response.IsSuccessStatusCode)
            {
                ReviewTags[2] = new KeyValuePair();
                ReviewTags[2].Key = "cb";
                ReviewTags[2].Value = "false";

                ComputerVisionPrediction CVObject = JsonConvert.DeserializeObject<ComputerVisionPrediction>(Response.Content.ReadAsStringAsync().Result);

                if ((CVObject.categories[0].detail != null) && (CVObject.categories[0].detail.celebrities.Count() > 0))
                {                 
                    ReviewTags[2].Value = "true";
                }
            }

            return Response.IsSuccessStatusCode;
        }

## <a name="classify-into-flags-toys-and-pens"></a>Jelző, játékok és toll besorolása

1. [Jelentkezzen be a](https://azure.microsoft.com/en-us/services/cognitive-services/custom-vision-service/) számára a [egyéni Látástechnológiai API előzetes](https://www.customvision.ai/).
2. Használja a [gyors üzembe helyezés](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) hozhat létre az egyéni osztályozó jelzők, játékok és toll lehetséges észleléséhez.
   ![Egyéni stratégiai képzési lemezképek](images/tutorial-ecommerce-custom-vision.PNG)
3. [Az előrejelzés végpont URL-cím beszerzése](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) az egyéni osztályozó.
4. Tekintse meg a projekt forráskódban a függvényt, amely meghívja a egyéni osztályozó előrejelzés végpont beolvasni a képet.

        public static bool EvaluateCustomVisionTags(string ImageUrl, string CustomVisionUri, string CustomVisionKey, ref KeyValuePair[] ReviewTags)
        {
            var File = ImageUrl;
            string Body = $"{{\"URL\":\"{File}\"}}";

            HttpResponseMessage response = CallAPI(CustomVisionUri, CustomVisionKey, CallType.POST,
                                                   "Prediction-Key", "application/json", "", Body);

            if (response.IsSuccessStatusCode)
            {
                // Parse the response body. Blocking!
                SaveCustomVisionTags(response.Content.ReadAsStringAsync().Result, ref ReviewTags);
            }
            return response.IsSuccessStatusCode;
        }       
 
## <a name="reviews-for-human-in-the-loop"></a>Emberi hurok értékelést

1. A korábbi szakaszokban ellenőrizte a bejövő lemezképek felnőtt és ellopható (tartalom moderátor) celebrities (számítógép stratégiai) és jelzők (egyéni stratégiai).
2. Az egyeztetés küszöbértékek minden vizsgálat alapján, a nuanced esetekben elérhetővé teszi emberi tekintse át a felülvizsgálati eszközben.
        nyilvános statikus logikai CreateReview (karakterlánc ImageUrl, KeyValuePair [] metaadatok) {

            ReviewCreationRequest Review = new ReviewCreationRequest();
            Review.Item[0] = new ReviewItem();
            Review.Item[0].Content = ImageUrl;
            Review.Item[0].Metadata = new KeyValuePair[MAXTAGSCOUNT];
            Metadata.CopyTo(Review.Item[0].Metadata, 0);

            //SortReviewItems(ref Review);

            string Body = JsonConvert.SerializeObject(Review.Item);

            HttpResponseMessage response = CallAPI(ReviewUri, ContentModeratorKey, CallType.POST,
                                                   "Ocp-Apim-Subscription-Key", "application/json", "", Body);

            return response.IsSuccessStatusCode;
        }

## <a name="submit-batch-of-images"></a>A képek kötegelt elküldése

1. Ez az oktatóanyag feltételezi, hogy a "C:Test" könyvtár, egy szövegfájlt, amely a kép URL-címek listája.
2. A következő kódot a fájl keres, és olvassa be az URL-címet a memóriába.
            Egy teszt címtár kép URL-címek var topdir beolvasásához listáját tartalmazó szövegfájlt keressen = @"C:\test\"; var Urlsfile = topdir +"Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Az összes vizsgálat kezdeményezése

1. Ez a legfelső szintű függvény végighalad a azt a korábban említett szövegfájlban szereplő összes kép URL-cím.
2. Megvizsgálja őket minden egyes API-t, és az egyeztetés abban, hogy pontszám a feltételek, ha létrehozott egy felülvizsgálati emberi moderátorok.
             az egyes kép URL-címet a fájlban... foreach (var URL-címet az URL-címek) {/ / egy új felülvizsgálati címkék tömb ReviewTags Initiatize = új KeyValuePair [MAXTAGSCOUNT];

                // Evaluate for potential adult and racy content with Content Moderator API
                EvaluateAdultRacy(Url, ref ReviewTags);

                // Evaluate for potential presence of celebrity (ies) in images with Computer Vision API
                EvaluateComputerVisionTags(Url, ComputerVisionUri, ComputerVisionKey, ref ReviewTags);

                // Evaluate for potential presence of custom categories other than Marijuana
                EvaluateCustomVisionTags(Url, CustomVisionUri, CustomVisionKey, ref ReviewTags);

                // Create review in the Content Moderator review tool
                CreateReview(Url, ReviewTags);
            }

## <a name="license"></a>Licenc

Az összes Microsoft kognitív Services SDK-k és minták a MIT licenccel rendelkező licencét. További részletekért lásd: [licenc](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Fejlesztői viselkedési szabályzat

A fejlesztők kognitív szolgáltatásokkal, beleértve az ügyféloldali kódtár & minta, hajtsa végre a "fejlesztői kódot, végezze el a Microsoft kognitív szolgáltatások", a következő címen található várhatóan http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>További lépések

Hozza létre, és az oktatóanyag kiterjesztése használatával a [forrásfájlok projekt](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) a Githubon.
