---
title: 'Gyors útmutató: a blob Storage C# -ban tárolt beszéd felismerése – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2f06d0015bd80b37407df28045d4ced4a128e47e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468233"
---
## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt ügyeljen a következőre:

> [!div class="checklist"]
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Forrásfájl feltöltése Azure-blobba](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [A fejlesztési környezet beállítása](../../../../quickstarts/setup-platform.md)
> * [Üres minta projekt létrehozása](../../../../quickstarts/create-project.md)

## <a name="download-and-install-the-api-client-library"></a>Az API ügyféloldali kódtár letöltése és telepítése

A minta végrehajtásához létre kell hoznia a Python-függvénytárat a [hencegő](https://swagger.io)használatával generált Rest APIhoz.

Kövesse az alábbi lépéseket a telepítéshez:

1. Nyissa meg a következőt: https://editor.swagger.io.
1. Kattintson a **fájl**, majd az **URL importálása**elemre.
1. Adja meg a felvágási URL-címet, beleértve a Speech Service-előfizetés régióját: `https://<your-region>.cris.ai/docs/v2.0/swagger`.
1. Kattintson az **ügyfél előállítása** és a **Python**elemre.
1. Mentse az ügyféloldali kódtárat.
1. Bontsa ki a letöltött Python-Client-Generated. zip fájlt a fájlrendszerben.
1. Telepítse a kinyert Python-Client modult a Python-környezetben a pip: `pip install path/to/package/python-client`használatával.
1. A telepített csomag neve `swagger_client`. A `python -c "import swagger_client"`parancs használatával megtekintheti, hogy a telepítés működik-e.

> **Megjegyzés:** A [hencegő autogeneráció ismert hibája](https://github.com/swagger-api/swagger-codegen/issues/7541)miatt előfordulhat, hogy a `swagger_client`-csomag importálásával kapcsolatos hibák merülhetnek fel.
> Ezeket úgy lehet megjavítani, ha törli a sort a tartalommal.
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> a fájl `swagger_client/models/model.py` és a tartalommal rendelkező sorban
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> a fájl `swagger_client/models/inner_error.py` a telepített csomagon belül. A hibaüzenetből megtudhatja, hol találhatók ezek a fájlok a telepítéshez.

## <a name="install-other-dependencies"></a>Egyéb függőségek telepítése

A minta a `requests` könyvtárat használja. A parancs használatával telepítheti.

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Kezdés néhány szabványos kóddal

Vegyünk fel egy olyan kódot, amely csontvázként működik a projekthez.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]
(`YourSubscriptionKey`, `YourServiceRegion`és `YourFileUrl` értékeit a saját értékeire kell cserélnie.)

## <a name="create-and-configure-an-http-client"></a>Http-ügyfél létrehozása és konfigurálása
Első lépésként egy olyan http-ügyfélre van szükségünk, amely megfelelő alap URL-címmel és hitelesítési készlettel rendelkezik.
A kód beillesztése `transcribe` [!code-pythonba [](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Átírási kérelem létrehozása
Ezután létrehozjuk az átírási kérelmet. A kód hozzáadása a `transcribe` [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Küldje el a kérést, és keresse meg az állapotát
Most közzétesszük a kérést a beszédfelismerési szolgáltatásba, és megvizsgáljuk a kezdeti válasz kódját. A válasz kódja egyszerűen azt jelzi, hogy a szolgáltatás megkapta-e a kérelmet. A szolgáltatás egy URL-címet ad vissza a válasz fejlécekben, amely az a hely, ahol az átírási állapotot tárolni fogja.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Várja meg, amíg az átírás befejeződik
Mivel a szolgáltatás aszinkron módon dolgozza fel az átírást, minden olyan esetben le kell kérdezni annak állapotát. 5 másodpercenként megvizsgáljuk.

Felszámoljuk a Speech Service-erőforrás által feldolgozott összes átírást, és megkeresjük az általunk létrehozott szöveget.

A sikeres befejezést követően az állapotot megjelenítő lekérdezési kód a következő lesz.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Az átírás eredményeinek megjelenítése
Ha a szolgáltatás sikeresen befejezte az átírást, a rendszer egy másik URL-címen tárolja az eredményeket, amelyet az állapot válasza alapján kaphat.

Itt bemutatjuk, hogy az eredmény JSON, és megjeleníti azt.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>A kód megkeresése
Ezen a ponton a kódnak így kell kinéznie: (adtunk hozzá néhány megjegyzést ehhez a verzióhoz) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll az alkalmazás felépítésére és a beszédfelismerési szolgáltatás használatával történő tesztelésre.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [footer](./footer.md)]
