---
title: 'Oktatóanyag: Elektronikus kereskedelmi katalógusok moderálása – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Automatikusan moderálhat elektronikus kereskedelmi katalógusokat gépi tanulással és mesterséges intelligenciával.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: sajagtap
ms.openlocfilehash: 285590435a7e3c31d45d5d154d4e430ed3252838
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256230"
---
# <a name="tutorial-ecommerce-catalog-moderation-with-machine-learning"></a>Oktatóanyag: Elektronikus kereskedelmi katalógusok moderálása gépi tanulással

Ebben az oktatóanyagban megismerheti, hogyan valósítható meg az elektronikus kereskedelmi katalógusok gépi tanuláson alapuló intelligens moderálása a gépi támogatású MI-technológiák és az emberi moderálás ötvözésével egy intelligens katalógusrendszer biztosítása érdekében.

![Besorolt termékképek](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="business-scenario"></a>Vállalati forgatókönyv

Gépi támogatású technológiákat használhat a termékképek következő kategóriákba történő besorolásához és moderálásához:

1. Felnőtt (meztelenség)
2. Kényes (kétértelmű)
3. Hírességek
4. Amerikai zászlók
5. Játékok
6. Tollak

## <a name="tutorial-steps"></a>Az oktatóanyag lépései

Az oktatóanyag a következő lépéseken vezeti végig:

1. Regisztráció, és egy Content Moderator-csapat létrehozása.
2. Moderálási címkék (feliratok) konfigurálása a lehetséges „híresség” és „zászló” tartalomhoz.
3. A Content Moderator Image API-jának használata a lehetséges felnőtteknek szóló és kényes tartalom keresésére.
4. A Computer Vision API használata a lehetséges hírességek keresésére.
5. A Custom Vision Service használata zászlók lehetséges jelenlétének keresésére.
6. Az árnyalt vizsgálati eredmények bemutatása emberi felülvizsgálathoz és végső döntéshozatalhoz.

## <a name="create-a-team"></a>Csapat létrehozása

A [rövid útmutatóban](quick-start.md) szereplő lépéseket követve regisztráljon a Content Moderatorba, és hozzon létre egy csapatot. Jegyezze fel a **Csapatazonosító** **Hitelesítő adatok** oldalon látható értékét.


## <a name="define-custom-tags"></a>Egyéni címkék meghatározása

Tekintse meg a [címkékkel](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) foglalkozó cikket az egyéni címkék hozzáadásához. A beépített **felnőtt** és **kényes** címkék mellett az új címkék lehetővé teszik, hogy a felülvizsgálati eszköz megjelenítse a címkék leíró nevét.

Esetünkben az alábbi egyéni címkéket határozzuk meg (**celebrity**, **flag**, **us**, **toy**, **pen**):

![Egyéni címkék konfigurálása](images/tutorial-ecommerce-tags2.PNG)

## <a name="list-your-api-keys-and-endpoints"></a>Az API-kulcsok és -végpontok listázása

1. Az oktatóanyag három API-t, valamint a hozzájuk tartozó kulcsokat és API-végpontokat használja.
2. Az API-végpontok eltérőek az előfizetés régiója és Content Moderator felülvizsgálati csapatának azonosítója szerint.

> [!NOTE]
> Az oktatóanyag az előfizetési kulcsoknak a következő végpontokban látható régiókban való használatára lett tervezve. Győződjön meg arról, az API-kulcsok megfelelnek a régió URI-jaihoz, különben előfordulhat, hogy a kulcsok nem működnek a következő végpontokkal:

         // Your API keys
        public const string ContentModeratorKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string ComputerVisionKey = "XXXXXXXXXXXXXXXXXXXX";
        public const string CustomVisionKey = "XXXXXXXXXXXXXXXXXXXX";

        // Your end points URLs will look different based on your region and Content Moderator Team ID.
        public const string ImageUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/moderate/v1.0/ProcessImage/Evaluate";
        public const string ReviewUri = "https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0/teams/YOURTEAMID/reviews";
        public const string ComputerVisionUri = "https://westcentralus.api.cognitive.microsoft.com/vision/v1.0";
        public const string CustomVisionUri = "https://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/Prediction/XXXXXXXXXXXXXXXXXXXX/url";

## <a name="scan-for-adult-and-racy-content"></a>Felnőtteknek szóló és kényes tartalom keresése

1. A függvény egy kép URL-jét és kulcs-érték párok egy tömbjét veszi fel paraméterként.
2. A Content Moderator Image API-jának meghívásával lekéri a „felnőtt” és „kényes” besorolás pontszámát.
3. Ha a pontszám 0,4-nél nagyobb (a 0–1 tartományban), a **ReviewTags** tömbben **True** értéket állít be.
4. A **ReviewTags** tömb a megfelelő címkének a felülvizsgálati eszközben való kiemelésére szolgál.

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

## <a name="scan-for-celebrities"></a>Hírességek keresése

1. Regisztráljon a [Computer Vision API](https://azure.microsoft.com/services/cognitive-services/computer-vision/) [ingyenes próbaverziójára](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision).
2. Kattintson az **API-kulcs beszerzése** gombra.
3. Fogadja el a feltételeket.
4. A bejelentkezéshez válasszon az elérhető internetes fiókok listájáról.
5. Jegyezze fel a szolgáltatás oldalán megjelenő API-kulcsokat.
    
   ![Computer Vision API-kulcsok](images/tutorial-computer-vision-keys.PNG)
    
6. A képet a Computer Vision API-val vizsgáló függvénnyel kapcsolatban tekintse meg a projekt forráskódját.

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

## <a name="classify-into-flags-toys-and-pens"></a>Besorolás a zászlók, játékok és tollak kategóriába

1. [Jelentkezzen be](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) a [Custom Vision API előzetes verziójába](https://www.customvision.ai/).
2. A [rövid útmutató](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) segítségével hozza létre az egyéni besorolót a zászlók, játékok és tollak lehetséges jelenlétének észleléséhez.
   ![Egyéni vizuális képzési képek](images/tutorial-ecommerce-custom-vision.PNG)
3. [Kérje le az előrejelzési végpont URL-címét](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api) az egyéni besoroló számára.
4. Az egyéni besoroló előrejelzési végpontját a kép vizsgálata céljából meghívó függvénnyel kapcsolatban tekintse meg a projekt forráskódját.

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
 
## <a name="reviews-for-human-in-the-loop"></a>Emberi beavatkozást igénylő felülvizsgálat

1. Az előző szakaszokban megvizsgálta, hogy a bejövő képeken van-e felnőtteknek szóló és kényes tartalom (Content Moderator), híresség (Computer Vision) és zászló (Custom Vision).
2. Minden egyes keresés egyezési küszöbértéke alapján az árnyalt esetek elérhetővé tehetők emberi felülvizsgálatra a felülvizsgálati eszközben.
        public static bool CreateReview(string ImageUrl, KeyValuePair[] Metadata) {

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

## <a name="submit-batch-of-images"></a>Képköteg elküldése

1. Az oktatóanyag egy „C:Test” könyvtár meglétét feltételezi, amelyben egy szövegfájl tartalmazza a képek URL-jeinek a listáját.
2. A következő kód ellenőrzi a fájl meglétét, és beolvassa az összes URL-t a memóriába.
            // Check for a test directory for a text file with the list of Image URLs to scan var topdir = @"C:\test\"; var Urlsfile = topdir + "Urls.txt";

            if (!Directory.Exists(topdir))
                return;

            if (!File.Exists(Urlsfile))
            {
                return;
            }

            // Read all image URLs in the file
            var Urls = File.ReadLines(Urlsfile);

## <a name="initiate-all-scans"></a>Az összes vizsgálatának indítása

1. Ez a legfelső szintű funkció végighalad a korábban említett szövegfájlban lévő összes kép URL-jén.
2. Átvizsgálja az API-k mindegyikével, és ha az egyezés megbízhatósági pontszáma a feltételeken belülre esik, létrehoz egy felülvizsgálatot az emberi moderátorok számára.
             // for each image URL in the file... foreach (var Url in Urls) { // Initiatize a new review tags array ReviewTags = new KeyValuePair[MAXTAGSCOUNT];

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

Az összes Microsoft Cognitive Services SDK és -minta licencelése MIT-licenccel történik. További információ: [LICENC](https://microsoft.mit-license.org/).

## <a name="developer-code-of-conduct"></a>Fejlesztői magatartási kódex

A Cognitive Servicest (beleértve a jelen ügyfélkódtárat és mintát is) használó fejlesztőknek követniük kell a Microsoft Cognitive Services fejlesztőire vonatkozó magatartási kódexben foglaltakat. A dokumentum ezen a címen érhető el: http://go.microsoft.com/fwlink/?LinkId=698895.

## <a name="next-steps"></a>További lépések

Hozhat létre, és az oktatóanyag kiterjesztése használatával a [forrás soubory projektu](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) a Githubon.
