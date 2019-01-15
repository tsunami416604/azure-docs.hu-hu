---
title: Jóváhagyás feladatok és emberi hurok felülvizsgálatok - Content Moderator
titlesuffix: Azure Cognitive Services
description: Gépi támogatású képmoderálás emberi hurok képességekkel rendelkező egyesítése a legjobb eredmények elérése érdekében az üzleti lekérése az Azure Content Moderator felülvizsgálati API használatával.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: a348b18d1ecc9c0e4405c54a8e554d932781ec92
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265345"
---
# <a name="content-moderation-jobs-and-reviews"></a>Tartalom-jóváhagyás feladatok és értékelések

Az Azure Content Moderator a gépi támogatású képmoderálás emberi hurok képességekkel rendelkező egyesítése [felülvizsgálati API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) beolvasni a legjobb eredmények elérése érdekében a vállalkozása számára.

A felülvizsgálati API emberi felvenni a tartalom-jóváhagyás folyamat az alábbi lehetőségeket kínálja:

* `Job` műveletek a gépi támogatású képmoderálás és az emberi ellenőrző létrehozása egy lépésben szolgálnak.
* `Review` műveletek emberi vizsgálóeszközt létrehozásakor, a moderálás lépés kívül használható.
* `Workflow` műveletek küszöbértékeinek felülvizsgálat létrehozása a keresés automatizáló munkafolyamatok kezelésére használhatók.

A `Job` és `Review` műveleteket fogadja el a visszahívás végpontjainak állapotának és eredményeinek fogadásakor.

Ez a cikk ismerteti a `Job` és `Review` műveleteket. Olvassa el a [munkafolyamatok áttekintése](workflow-api.md) létrehozásával kapcsolatos információkat, szerkesztheti és munkafolyamat-meghatározások beolvasása.

## <a name="job-operations"></a>Feladat műveletei

### <a name="start-a-job"></a>Feladat indítása
Használja a `Job.Create` műveletet a moderálás és emberi vizsgálóeszközt létrehozó feladat elindításához. A Content Moderator megvizsgálja a tartalmat, és kiértékeli a megadott munkafolyamat. A munkafolyamat eredményei alapján, vagy hoz létre értékelések vagy kihagyja a lépést. Azt is elküldi a utáni moderálás és az utólagos felülvizsgálat címkék a visszahívás-végpontra.

A bemeneti adatok az alábbi információkat:

- A felülvizsgálati csapat azonosítója.
- A tartalom kell mérsékelni.
- A munkafolyamat nevét. (Az alapértelmezett érték a "alapértelmezett" munkafolyamatot.)
- Az API-visszahívás értesítések pontján.
 
A következő választ mutatja a indított feladat azonosítóját. A feladat állapotát, és részletes információkban részesülnek a használja a feladat azonosítója.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Feladat állapotának beolvasása

Használja a `Job.Get` művelet és a feladat azonosító lekérése egy futó vagy befejezett feladat részleteit. A művelet azonnal, míg a moderálás feladat aszinkron módon fut adja vissza. Az eredmény a visszahívás-végponton keresztül.

A bemeneteket az alábbi információkat:

- A felülvizsgálati csoport azonosítója: Az előző művelet által visszaadott feladat azonosítója

A válasz tartalmazza a következő információkat:

- A létrehozott felülvizsgálat azonosítója. (Ezt az Azonosítót használja a végleges ellenőrzésig eredmények eléréséhez.)
- A feladat (befejezett és folyamatban) állapota: A hozzárendelt moderálás címkék (kulcs-érték párok).
- A feladat-végrehajtási jelentés.
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
            {
                "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
                },
                {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
                }
            ]
        }
 
![Képek felülvizsgálata emberi moderátorok által](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Műveletek áttekintése

### <a name="create-reviews"></a>Felülvizsgálat létrehozása

Használja a `Review.Create` létrehozni a az emberi ellenőrzések. Vagy máshol közepes szintű vagy az egyéni logikát használja a moderálás címkéket társíthat.

Ez a művelet a bemeneti adatokat a következők:

- Át kell tekinteni a tartalmat.
- A hozzárendelt címkék (kulcs-érték párral) által az emberi moderátorok felülvizsgálatra.

A következő választ mutatja a felülvizsgálat azonosítója:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Felülvizsgálati állapot beolvasása
Használja a `Review.Get` művelettel lekérheti az eredményeket egy emberi vizsgálóeszközt moderált obrázku befejeződése után. Értesítést kaphat a megadott visszahívási végponton keresztül. 

A művelet a címkék két csoportját adja vissza: 

* A moderálás szolgáltatás által hozzárendelt címkék
* A címkék, Miután befejeződött az emberi ellenőrző

A bemenetek legalább a következők:

- A felülvizsgálat csoport neve
- Tekintse át az előző művelet által visszaadott azonosítója

A válasz tartalmazza a következő információkat:

- A felülvizsgálati állapot
- A címkék (kulcs-érték párok), az emberi ellenőrző erősítette
- A moderálás szolgáltatás által hozzárendelt címkék (kulcs-érték párok)

Mindkét felülvizsgáló által hozzárendelt címkéinek megtekintéséhez (**reviewerResultTags**) és a kezdeti címkék (**metaadatok**) a következő minta válasz:

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>További lépések

* Próbálja ki a [feladat API-konzol](try-review-api-job.md), és használja a REST API-Kódminták. Ha ismeri a Visual Studio és C#, is tekintse meg a [feladatok .NET – rövid útmutató](moderation-jobs-quickstart-dotnet.md). 
* Az értékelések, használatának első lépései a [felülvizsgálati API-konzol](try-review-api-review.md), és használja a REST API-Kódminták. Ekkor megjelenik a [felülvizsgálatok .NET – rövid útmutató](moderation-reviews-quickstart-dotnet.md).
* A videót felülvizsgálatok, használja a [tekintse át a rövid videó](video-reviews-quickstart-dotnet.md), és ismerje meg, hogyan [átiratok a videó felülvizsgálat hozzáadása](video-transcript-reviews-quickstart-dotnet.md).
