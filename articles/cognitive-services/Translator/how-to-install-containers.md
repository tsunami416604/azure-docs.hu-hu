---
title: Tárolók telepítése és futtatása – Translator Text
titleSuffix: Azure Cognitive Services
description: Tárolók letöltése, telepítése és futtatása Translator Text elemzésekhez ebben a bemutató oktatóanyagban.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507879"
---
# <a name="install-and-run-translator-text-containers"></a>Translator Text tárolók telepítése és futtatása

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

A tárolók lehetővé teszik a Translator Text API-k futtatását a saját környezetében, és kiválóan használhatók az egyes biztonsági és adatirányítási követelményekhez.

## <a name="prerequisites"></a>Előfeltételek

Translator Text tárolók használata előtt meg kell felelnie a következő előfeltételeknek:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motornak telepítve kell lennie a [gazdagépen](#the-host-computer). A Docker csomagokat biztosít a Docker-környezet konfigurálásához [MacOS](https://docs.docker.com/docker-for-mac/), Windows és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) [rendszereken](https://docs.docker.com/docker-for-windows/). A Docker és a Container alapjairól a [Docker áttekintésében](https://docs.docker.com/engine/docker-overview/)talál további információt.<br><br> **Windows rendszeren a**Docker-t is konfigurálni kell a Linux-tárolók támogatásához.<br><br>|
|A Docker ismerete | Alapvető ismeretekkel kell rendelkeznie a Docker-fogalmakról, például a kibocsátásiegység-forgalmi jegyzékekről, a adattárakról, a tárolók és a tárolók rendszerképeiről, valamint az alapszintű `docker` parancsok megismeréséről.|

## <a name="request-access-to-the-container-registry"></a>Hozzáférés kérése a tároló beállításjegyzékéhez

Először be kell fejeznie és el kell küldenie a [kognitív Translator Text tárolók kérelem űrlapját](https://aka.ms/translatorcontainerform) , hogy hozzáférést Kérjen a tárolóhoz.

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>A gazdaszámítógép

A gazdagép egy x64-alapú számítógép, amelyen a Docker-tárolót futtató Linux operációs rendszer fut. Ez lehet egy számítógép a helyszínen vagy az Azure-beli Docker-üzemeltetési szolgáltatás, például:

* Azure Kubernetes szolgáltatás.
* Azure Container Instances.
* Azure Stack üzembe helyezett [Kubernetes](https://kubernetes.io/) -fürt.

### <a name="container-requirements-and-recommendations"></a>A tárolóra vonatkozó követelmények és javaslatok

Az alábbi táblázat a minimális és ajánlott CPU-magot, legalább 2,6 gigahertzes (GHz) vagy gyorsabb, valamint a memóriát (GB) tartalmazza az egyes Translator Text-tárolók lefoglalásához.

| Tároló | Minimális | Nyelvi pár |
|-----------|---------|---------------|
| Fordítói szöveg | 4 mag, 4 GB memória | 4 |

Minden nyelvi pár esetében 1 GB memóriát ajánlott használni. Alapértelmezés szerint a Translator Text tároló 3 vagy 4 nyelvi párral rendelkezik a futtatott `<image-tag>` függően. A részletekért tekintse meg a [támogatott nyelvek és fordítás](#supported-languages-and-translation) című témakört. Az alap és a memória a `docker run` parancs részeként használt `--cpus`-és `--memory`-beállításoknak felel meg.

## <a name="get-the-container-image-with-docker-pull"></a>A tároló rendszerképének beolvasása a `docker pull`

A Translator Text tároló rendszerképei a következő tároló-tárházban érhetők el. A tábla a tároló képcímkéit és a hozzájuk tartozó támogatott nyelveket is leképezi.

| Tároló | Rendszerkép címkéje | Támogatott nyelvek |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE`és `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR`és `es-ES` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Docker-lekérés a Translator Text tárolóhoz

A [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancs végrehajtásához először hozzá kell férnie a tároló-beállításjegyzékhez. Az Azure CLI-ből a [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) parancs használatával bejelentkezhet a Azure Container Registryba.

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Ez a parancs az aktuális felhasználót a megfelelő Azure Container Registry hitelesíti. Most már szabadon futtathatja a `docker pull` parancsot.

> [!NOTE]
> Attól függően, hogy milyen nyelvi támogatásra van szüksége, adja meg a megfelelő `<image-tag>`.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>A tároló használata

Miután a tároló a [gazdagépen](#the-host-computer)található, a következő eljárással dolgozhat a tárolóval.

1. [Futtassa a tárolót](#run-the-container-with-docker-run). További [példák](translator-text-container-config.md#example-docker-run-commands) a `docker run` parancsra.
1. [A tároló fordítási végpontjának lekérdezése](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>A tároló futtatása `docker run`

A három tároló bármelyikének futtatásához használja a [Docker Run](https://docs.docker.com/engine/reference/commandline/run/) parancsot. A `docker run` parancs [példái](translator-text-container-config.md#example-docker-run-commands) elérhetők.

### <a name="run-container-example-of-docker-run-command"></a>Tároló futtatása példa a Docker Run parancsra

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Ez a parancs:

* Translator Text tárolót futtat a tároló rendszerképből
* 4 CPU mag és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi a 5000-es TCP-portot, és kiosztja a tárolóhoz tartozó pszeudo-TTY-t
* Elfogadja a végfelhasználói szerződést (EULA)
* A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdagépen

### <a name="how-to-collect-docker-logs"></a>Docker-naplók összegyűjtése

Hibaelhárítási célból hasznos lehet a Docker-naplók megtekintése a tároló végrehajtásáról. Először hajtsa végre a [Docker PS](https://docs.docker.com/engine/reference/commandline/ps/) parancsot a formázási jelzővel, hogy korlátozza az összes tárolóban megjelenített részleteket.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Ezután futtassa a [Docker logs](https://docs.docker.com/engine/reference/commandline/logs/) parancsot a szóban forgó tárolóhoz tartozó `<Container ID>` a naplók megtekintéséhez.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

A fenti Docker-naplók a "hiba" naplókat gyűjtik az elmúlt négy órában.

## <a name="supported-languages-and-translation"></a>Támogatott nyelvek és fordítás

A `POST /translate` módszer a következő nyelveket támogatja, és az *angolról* a célnyelvre vált, és fordítva. Vegye figyelembe, hogy habár az angol nyelvről és a felsorolt nyelvek egyikével is elvégezhető, az egyik *nem angol* nyelvű nyelvről *nem* lehet *más* nyelvre lépni.

> [!NOTE]
> Az optimális minőség érdekében a felhasználóknak csak egy mondatot kell elküldeniük egy kérelemre.

| Nyelvi átalakítás | Nyelv ISO-átalakítása | Képcímkék |
|--|--|:--|
| Angol: left_right_arrow: kínai | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Angol: left_right_arrow: orosz | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Angol: left_right_arrow: Arab | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Angol: left_right_arrow: német | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` és `de_en_es_fr_1.0.0` |
| Angol: left_right_arrow: spanyol | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Angol: left_right_arrow: francia | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> A tároló futtatásához meg kell adni a `Eula`. Ellenkező esetben a tároló nem indul el.

## <a name="query-the-containers-translate-endpoint"></a>A tároló fordítási végpontjának lekérdezése

A tároló egy REST-alapú fordítási végpont API-t biztosít. A végpont több példás használata a következő:

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Hajtsa végre a következő cURL-parancsot a kívánt CLI-ből.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Ha ezt a cURL-BEJEGYZÉST a tároló előkészítése előtt kísérli meg, akkor a "szolgáltatás átmenetileg nem érhető el" választ kap. Egyszerűen várjon, amíg a tároló készen áll, majd próbálkozzon újra.

A rendszer a következő kimenetet fogja kinyomtatni a konzolra.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Navigáljon a hencegő oldalra, http://localhost:5000/swagger/index.html

1. **/Translate post** kiválasztása
1. **Próbálja** ki
1. Adja meg a **from** paramétert `en-US`
1. Adja meg a **to** paramétert `de-DE`
1. Adja meg az **API-Version** paramétert `3.0`
1. A **szövegek**alatt cserélje le a `string`t a következő JSON-ra
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Válassza a **végrehajtás**lehetőséget, az eredményül kapott fordítások a **Válasz törzsében**jelennek meg. A következőhöz hasonlónak kell lennie:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Indítsa el a Visual studiót, és hozzon létre egy új konzolszoftver-alkalmazást.
1. A `<LangVersion>7.1</LangVersion>` csomópont hozzáadásához szerkessze a `*.csproj` fájlt – ez C# 7,1.
1. Adja hozzá a [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet-csomagot, a 11.0.2 verzióját.
1. `Program.cs`cserélje le az összes meglévő kódot a következőre:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Nyomja le az **F5** billentyűt a program futtatásához.
1. A rendszer a következő kimenetet fogja kinyomtatni a konzolra.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>A tároló leállítása

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a tárolót egy kimeneti [csatlakoztatással](translator-text-container-config.md#mount-settings) futtatja, és a naplózás engedélyezve van, a tároló olyan naplófájlokat hoz létre, amelyek hasznosak a tároló indításakor vagy futtatásakor felmerülő problémák elhárításához.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Összefoglalás

Ebben a cikkben megtanulta Translator Text tárolók letöltésére, telepítésére és futtatására vonatkozó fogalmakat és munkafolyamatokat. Összegezve:

* Translator Text több Linux-tárolót biztosít a Docker számára, különböző nyelvi párokat ágyazhat be.
* A tároló lemezképei a "Container Preview" beállításjegyzékből tölthetők le.
* A tároló lemezképei a Docker-ben futnak.
* A REST API vagy az SDK használatával meghívhatja a műveleteket Translator Text tárolókban a tároló gazdagép URI azonosítójának megadásával.

## <a name="next-steps"></a>További lépések

* A [tárolók konfigurálásának](translator-text-container-config.md) áttekintése konfigurációs beállításokhoz
* A funkciókkal kapcsolatos problémák megoldásához tekintse meg a [Container – gyakori kérdések (GYIK)](../containers/container-faq.md) című témakört.
