---
title: Az Azure Content moderátor - moderálás feladatok és emberi hurok értékelést |} Microsoft Docs
description: Emberi felügyeletet vonatkozik a gép támogatású moderálás a legjobb eredmények elérése érdekében.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/21/2018
ms.author: sajagtap
ms.openlocfilehash: 35b3cdaa410712c3fd08d3df4ebe4c83e3955d50
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347130"
---
# <a name="moderation-jobs-and-reviews"></a>Moderálás feladatok és az ellenőrzések

Gép támogatású moderálás emberi hurok képességekkel kombinálhatja az Azure Content moderátor használatával [felülvizsgálati API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) a legjobb eredmények eléréséhez a vállalkozása számára.

A felülvizsgálati API emberi felügyeletet szerepeljenek a tartalom moderálás folyamat a következő lehetőséget kínál:

* `Job` gép támogatású moderálás és az emberi felülvizsgálati létrehozásának indítására egy lépésben leírt műveletek használt.
* `Review` műveletek emberi felülvizsgálati létrehozását, a moderálás lépés kívül használt.
* `Workflow` műveletek automatizálása a felülvizsgálati létrehozásához küszöbökkel vizsgálatát munkafolyamatok kezelése szolgálnak.

A `Job` és `Review` műveletek fogadja el a visszahívás-végpontokat állapotának és eredményeinek fogadására.

Ez a cikk ismerteti a `Job` és `Review` műveletek. Olvassa el a [munkafolyamatok áttekintése](workflow-api.md) létrehozásával kapcsolatos információkat, szerkesztése és munkafolyamat-definícióhoz beolvasása.

## <a name="job-operations"></a>Feladat műveletek

### <a name="start-a-job"></a>Feladat indítása
Használja a `Job.Create` egy moderálás és emberi felülvizsgálati létrehozására irányuló feladat indítási művelete. Tartalom moderátor megvizsgálja a tartalmat, és kiértékeli a megadott munkafolyamat. A munkafolyamat eredményei alapján, az értékelést hoz létre vagy a használatával kihagyhatja a lépést. A visszahívási végponthoz utáni moderálás és utólagos felülvizsgálat címkék is jelentést küld.

A bemeneti adatokat a következő információkat tartalmazza:

- Tekintse át a csapat azonosítója.
- A tartalom kell mérsékelni.
- A munkafolyamat nevét. (Az alapértelmezett érték a "alapértelmezett" munkafolyamatot.)
- Az API-visszahívás pont értesítéseket.
 
A következő válasz jeleníti meg a feladatot, amely indítása azonosítóját. Használhatja a feladatazonosítót a feladat állapotát, és részletes tájékoztatást kapni.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>Feladat állapotának beolvasása

Használja a `Job.Get` műveletet és egy futó vagy befejezett feladat részletes a feladatazonosítót. A művelet azonnal, amíg a moderálás feladat futtatása aszinkron módon adja vissza. A visszahívási végpont a rendszer visszairányítja az eredményeket.

A bemeneti adatokat a következő információkat tartalmazza:

- A felülvizsgálati csoport azonosítója: az előző művelet által visszaadott feladatazonosítót

A válasz a következő információkat tartalmazza:

- A létrehozott felülvizsgálat azonosítóját. (Ezt az Azonosítót használva a végső felülvizsgálati eredményeinek lekérése.)
- A feladat (befejezett vagy folyamatban) állapotának: A hozzárendelt moderálás címkék (kulcs-érték párok).
- A feladat végrehajtási jelentést.
 
 
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
 
![Az emberi moderátorok kép áttekintése](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>Tekintse át a műveletek

### <a name="create-reviews"></a>Ellenőrzések létrehozása

Használja a `Review.Create` művelet az emberi értékelést létrehozásához. Máshol közepes szintű vagy egyéni logika a segítségével rendelhet hozzá a moderálás címkék.

Ez a művelet a bemeneti adatok a következők:

- A tartalom vizsgálni.
- A hozzárendelt címkék (kulcs-érték párok) emberi moderátorok közösen vizsgálja át.

A következő válasz bemutatja a felülvizsgálati azonosítója:

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>Felülvizsgálat állapotának beolvasása
Használja a `Review.Get` lekérdezése az eredményeket a moderált kép emberi áttekintése után. Értesítést kaphat a megadott visszahívás-végpontot keresztül. 

A művelet címkék két csoportját adja vissza: 

* A címkék a moderálás szolgáltatás által hozzárendelt
* A címkék után az emberi áttekintése

A bemeneti adatok legalább a következők:

- A felülvizsgálati csoport neve
- Tekintse át az előző művelet által visszaadott azonosítóját

A válasz a következő információkat tartalmazza:

- A felülvizsgálati állapota
- A címkék (kulcs-érték párok) az emberi véleményező megerősítése
- A moderálás szolgáltatás által hozzárendelt címkék (kulcs-érték párok)

Mindkét a felülvizsgáló által hozzárendelt címkékkel látja (**reviewerResultTags**) és a kezdeti címkék (**metaadatok**) a következő minta válaszként:

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

* Tesztelése a [feladat API konzol](try-review-api-job.md), és a REST API-Kódminták használja. Ha ismeri a Visual Studio és a C#, is tekintse meg a [feladatok .NET gyors üzembe helyezés](moderation-jobs-quickstart-dotnet.md). 
* Felülvizsgálati, ismerkedjen meg a [felülvizsgálati API konzol](try-review-api-review.md), és a REST API-Kódminták használja. Megjelenik a [értékelést .NET gyors üzembe helyezés](moderation-reviews-quickstart-dotnet.md).
* Videó értékelést, használja a [videó felülvizsgálati gyors üzembe helyezés](video-reviews-quickstart-dotnet.md), és megtudhatja, hogyan [ki hozzá a videó felülvizsgálati](video-transcript-reviews-quickstart-dotnet.md).
