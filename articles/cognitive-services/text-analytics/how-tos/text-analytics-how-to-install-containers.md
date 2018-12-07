---
title: Tárolók telepítése és futtatása
titleSuffix: Text Analytics - Cognitive Services - Azure
description: Hogyan letöltése, telepítése és -tárolókat futtathat szövegelemzési az bemutató oktatóanyag.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: dc5e34f1a9a63b5b3ce30cdd547b900a32eba42c
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017219"
---
# <a name="install-and-run-containers"></a>Tárolók telepítése és futtatása

Bontás a szoftverek terjesztéséhez, amelyben egy alkalmazás vagy szolgáltatás van csomagolva, egy tárolórendszerképet egy megközelítést. A konfiguráció és az alkalmazás vagy szolgáltatás függőségeinek szerepelnek a tároló rendszerképét. A tároló rendszerképét ezután központilag telepítheti alig vagy egyáltalán nem módosítással üzemeltető tárolón futnak. Tárolók el különítve egymással és az alapjául szolgáló operációs rendszer, tárhely kisebb, mint egy virtuális gépet. A tárolók rövid távú feladatok tárolórendszerképeket a példányt, és távolítja el, amikor már nincs rá szükség.

Szövegelemzés Docker-tárolók következő csoportját, amelyek mindegyike tartalmazza a funkciók egy részét biztosítja:

| Tároló| Leírás |
|----------|-------------|
|Kulcskifejezések kinyerése | Kiolvassa a fő pontokat azonosíthatja a kulcskifejezéseket. Például „Az étel finom volt, és a személyzet kedves volt” bemeneti szövegből az API a következő fő pontokat adja vissza: „étel” és „személyzet kedves”. |
|Nyelvfelismerés | Legfeljebb 120 nyelv, észleli és jelenti, hogy melyik nyelven írt szöveg a bemeneti szöveg. A tároló jelentések minden egyes dokumentum, amely megtalálható a kérelem egyetlen nyelvkód. A nyelvkód egy pontszámmal párba állítva jelzi a pontszám erősségét. |
|Véleményelemzés | Elemzi a nyers szöveg a keresőmotorok kapcsolatos pozitív vagy negatív véleményeket. Az API minden dokumentumhoz visszaad egy 0 és 1 közötti hangulati pontszámot, ahol az 1 a legpozitívabb pontszám. Az elemzési modellek is előre betanított használatával egy Microsoft szöveg és a természetes nyelvi technológiák széles körű törzse. [Bizonyos nyelvek](../language-support.md) esetében az API képes a megadott szöveg elemzéséből kiszámított pontszámot közvetlenül visszaadni a hívó alkalmazásnak. |

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="preparation"></a>Előkészítés

Szövegelemzés tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

**Docker-motor**: rendelkeznie kell helyileg telepített Docker-motor. A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), és [Windows](https://docs.docker.com/docker-for-windows/). A Windows Linux-tárolók támogatják a Docker kell konfigurálni. Docker-tárolóit közvetlenül is telepíthető [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), vagy egy [Kubernetes](https://kubernetes.io/) üzembehelyezettfürt[Az azure Stack](/azure/azure-stack/). Kubernetes az Azure Stackhez való telepítéséről további információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz.

**A Microsoft Container Registry és a Docker ismerős**: rendelkeznie kell Microsoft Container Registry és a Docker fő fogalmaira, például beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint ismerete alapvető ismeretekkel Alapszintű `docker` parancsokat.  

A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott, processzormagot legalább 2.6-os gigahertz (GHz) vagy gyorsabb, és a memória (gigabájtban), minden egyes Szövegelemzés tároló lefoglalása.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|Kulcskifejezések kinyerése | 1 mag, 2 GB memória | 1 mag, 4 GB memória |
|Nyelvfelismerés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |
|Véleményelemzés | 1 mag, 2 GB memória | 1 mag, 4 GB memória |

## <a name="download-container-images-from-microsoft-container-registry"></a>Töltse le a tárolórendszerképek Microsoft Container registryből

Tárolórendszerképek szövegelemzési Microsoft Tárolóregisztrációs adatbázisból érhetők el. A következő táblázat felsorolja a tárházakhoz elérhető Microsoft Tárolóregisztrációs adatbázisból Szövegelemzés tárolókhoz. Minden tárház tartalmaz egy tárolórendszerképet, amely, futtassa helyileg a tárolót le kell tölteni.

| Tároló | Tárház |
|-----------|------------|
|Kulcskifejezések kinyerése | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Nyelvfelismerés | `mcr.microsoft.com/azure-cognitive-services/language` |
|Véleményelemzés | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Használja a [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltse le a tárolórendszerkép-adattárból. Töltse le a legújabb kulcsot kulcsszókeresés tároló rendszerképét az adattárból, például használja a következő parancsot:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

A Text Analytics tárolók rendelkezésre álló címkék teljes leírását lásd a következő tárolókat a Docker hub:

* [Kulcskifejezések kinyerése](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Nyelvfelismerés](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Hangulatelemzés](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> Használhatja a [docker-rendszerképek](https://docs.docker.com/engine/reference/commandline/images/) paranccsal listát készíthet a letöltött tárolólemezképek. Például a következő parancs megjeleníti az azonosítója, a tárházat, és a címke az egyes letöltött tárolórendszerképet, és táblázatként vannak formázva:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Hozza létre a tároló letöltött tároló rendszerképből

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) letöltött tárolórendszerképet a tároló példányosítása parancsot. Ha például a következő parancsot:

* Példányosít egy tárolót a Hangulatelemzés tárolórendszerképet
* Több processzormaggal és 8 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* A tároló automatikusan eltávolítja az után kilép

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` parancssori kapcsolókat kell adni a tároló példányosítása; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

Példányosítani, miután a tároló gazdagép URI használatával műveleteket meghívhatja a tárolóból. A következő gazdagép URI például Hangulatelemzés tárolót, amelyet az előző példában volt példányosítása jelöli:

```http
http://localhost:5000/
```

> [!TIP]
> Elérheti a [OpenAPI-specifikáció](https://swagger.io/docs/specification/about/) (korábbi nevén a Swagger-specifikációra), a példányosított tárolót, a támogatott műveleteket leíró a `/swagger` tárolóban tartozó relatív URI. Például a következő URI Azonosítót tartalmaz Hangulatelemzés tárolót, amelyet az előző példában volt példányosítani az OpenAPI-specifikáció való hozzáférést:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Választhatja [hívás a REST API-műveleteket](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api) érhető el a tárolót, vagy használja a [Azure Cognitive Services Text Analytics SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) ügyféloldali kódtár, ezek a műveletek meghívásához.  
> [!IMPORTANT]
> Rendelkeznie kell az Azure Cognitive Services Text Analytics SDK 2.1.0-ás vagy újabb, ha azt szeretné, az ügyféloldali kódtár használata a tárolót.

Az egyetlen különbség a között egy adott művelet meghívása a tárolóból, és hív-e, hogy a műveletben megfelelő service-ből az Azure-ban, hogy fogja használni a gazdagépet a tároló URI-t, nem pedig a gazdagép egy Azure-régióban, az URI-t hívja meg a műveletet. Ha szeretne a West US Azure-régióban futó Szövegelemzés-példányt használ, például szeretné az alábbi REST API-művelet hívás:

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

Ha szeretne használni az alapértelmezett konfiguráció szerint a helyi gépen futó kulcs kulcsszókeresés tárolók, az alábbi REST API-művelet lenne hívja meg:

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>Számlázás

A Text Analytics tárolók számlázási adatokat küld az Azure-bA használatával egy megfelelő Szövegelemzés-erőforrást az Azure-fiókjával. Az alábbi parancssori kapcsolók számlázási célból használják a Text Analytics tárolók:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Text Analytics-erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a kiépített Text Analytics az Azure-erőforrás megadott API-kulcs `Billing`. |
| `Billing` | A végpont a Text Analytics-erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett Text Analytics az Azure-erőforrás URI-ját.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a licencet, a tároló.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és futtatása a Text Analytics tárolók munkafolyamatokat. Összegezve:

* Szövegelemzés biztosít három Linux-tárolók Docker, kulcsszókeresés, nyelvfelismerés és hangulatelemzés fejlécbe foglalja.
* Tárolórendszerképek letöltődnek az a Microsoft Container Registry (MCR) az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val a Text Analytics tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](../text-analytics-resource-container-config.md) a konfigurációs beállítások
* Felülvizsgálat [Text Analytics áttekintése](../overview.md) tudhat meg többet a kulcskifejezések észlelése, nyelvfelismerés és hangulatelemzés  
* Tekintse meg a [Text Analytics API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) részleteiről a tároló által támogatott különböző módszereit.
* Tekintse meg [– gyakori kérdések (GYIK)](../text-analytics-resource-faq.md) a Computer Vision funkcióihoz kapcsolódó problémák megoldása.