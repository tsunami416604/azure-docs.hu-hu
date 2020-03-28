---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 01/13/2020
ms.author: dapine
ms.openlocfilehash: 646cce25efcbebab6229389f63912346e3712cdd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925849"
---
## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdene, győződjön meg arról, hogy:

> [!div class="checklist"]
> * [A fejlesztői környezet beállítása és üres projekt létrehozása](../../../../quickstarts/setup-platform.md)
> * [Azure Speech-erőforrás létrehozása](../../../../get-started.md)
> * [Forrásfájl feltöltése Azure-blobba](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>Az API-ügyfélkódtár letöltése és telepítése

A minta végrehajtásához létre kell hoznia a Python-függvénytárat a [Swagger](https://swagger.io)által generált REST API-hoz.

A telepítéshez kövesse az alábbi lépéseket:

1. Nyissa meg a következőt: https://editor.swagger.io.
1. Kattintson **a Fájl**menü **URL-importálása parancsára.**
1. Adja meg a Swagger URL-címét, `https://<your-region>.cris.ai/docs/v2.0/swagger`beleértve a beszédfelismerési szolgáltatás-előfizetés régióját is: .
1. Kattintson **az Ügyfél létrehozása gombra,** és válassza a **Python**lehetőséget.
1. Mentse az ügyfélkönyvtárat.
1. A letöltött python-client-generated.zip kibontása valahol a fájlrendszerben.
1. Telepítse a kivont python-kliens modult a Python környezetben a pip használatával: `pip install path/to/package/python-client`.
1. A telepített csomag `swagger_client`neve . Ellenőrizheti, hogy a telepítés `python -c "import swagger_client"`működött-e a paranccsal.

> [!NOTE]
> A [Swagger automatikus generálásának ismert hibája](https://github.com/swagger-api/swagger-codegen/issues/7541)miatt előfordulhat, `swagger_client` hogy hibákat észlel a csomag importálásakor.
> Ezek javíthatók a sor törlésével a
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> a fájlból `swagger_client/models/model.py` és a sor a tartalom
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> a telepített `swagger_client/models/inner_error.py` csomagon belüli fájlból. A hibaüzenet ből megtudom, hogy ezek a fájlok hol találhatók a telepítéshez.

## <a name="install-other-dependencies"></a>Egyéb függőségek telepítése

A minta `requests` a könyvtárat használja. Telepítheti azt a parancsot

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>Kezdje néhány sablonkóddal.

Adjunk hozzá néhány kódot, ami csontvázként működik a projektünkhöz.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Http-ügyfél létrehozása és konfigurálása
Az első dolog, amire szükségünk lesz egy Http Client, amely a megfelelő alap URL-t és hitelesítési készlet.
A kód `transcribe` beszúrása a következőbe:[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>Átírási kérelem létrehozása
Ezután létrehozunk egy átírási kérelmet. A kód `transcribe` hozzáadása a következőhöz:[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>Küldje el a kérelmet, és ellenőrizze annak állapotát
Most közzétesszük a kérést a beszédszolgáltatásnak, és ellenőrizzük a kezdeti válaszkódot. Ez a válaszkód egyszerűen jelzi, hogy a szolgáltatás megkapta-e a kérést. A szolgáltatás egy URL-címet ad vissza a válaszfejlécekben, amely az a hely, ahol az átírásállapotát tárolja.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>Várja meg, amíg az átírás befejeződik
Mivel a szolgáltatás aszinkron módon dolgozza fel az átírást, minden alkalommal le kell közvéleményítenünk az állapotát. 5 másodpercenként ellenőrizzük.

Felsoroljuk az összes átiratot, amelyet ez a beszédszolgáltatás-erőforrás feldolgoz, és megkeressük azt, amit létrehoztunk.

Itt van a lekérdezési kódot állapot kijelző mindent, kivéve a sikeres befejezését, akkor ezt a következő.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>Az átírási eredmények megjelenítése
Miután a szolgáltatás sikeresen befejezte az átírást, az eredmények egy másik URL-ben tárolódnak, amelyet az állapotválaszból kaphatunk.

Itt megkapjuk ezt az eredményt JSON és megjeleníti azt.
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>A kód ellenőrzése
Ezen a ponton, a kódot kell kinéznie: (Már hozzá néhány megjegyzést, hogy ez a verzió)[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>Az alkalmazás létrehozása és futtatása

Most már készen áll arra, hogy létrehozza az alkalmazást, és tesztelje a beszédfelismerést a Beszédszolgáltatás használatával.

## <a name="next-steps"></a>További lépések

[!INCLUDE [footer](./footer.md)]
