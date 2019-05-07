---
title: Telepítse és futtassa a tárolók - Personalizer
titleSuffix: Azure Cognitive Services
description: Hogyan letöltése, telepítése és Personalizer a tárolókat futtatják.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 2a62fd288f9118882e5cd9899ab572d4b247fc5a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158538"
---
# <a name="install-and-run-personalizer-containers"></a>Telepítse és futtassa a Personalizer tárolók

A Personalizer szolgáltatás a következő tároló(k) rendelkezik: 

|Függvény|Szolgáltatások|
|-|-|
|Personalizer|Tartalom és a felhasználó az aktuális környezetben ajánlott műveletet határozza meg.|

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Personalizer Service-tárolók használata előtt a következő előfeltételeknek kell megfelelnie:

|Kötelező|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.| 
|Personalizer Service erőforrás |Ezek a tárolók használatához rendelkeznie kell:<br><br>A _Personalizer szolgáltatás_ Azure-erőforráshoz tartozó számlázási kulcs és számlázási végpont URI azonosítója. Mindkét értéket az Azure Portalon Personalizer szolgáltatás áttekintése és a kulcsok oldalon érhető el, és a tároló indításához szükséges.<br><br>**{BILLING_KEY}** : erőforrás-kulcs<br><br>**{BILLING_ENDPOINT_URI}** : végpont URI-példa: `https://westus.api.cognitive.microsoft.com/`|

### <a name="the-host-computer"></a>A számítógép

A **gazdagép** van a számítógépen, amelyen a docker-tárolót. A helyszíni vagy a docker-üzemeltetési szolgáltatás az Azure például egy számítógép lehet:

* [Azure Kubernetes Service](https://docs.microsoft.com/aks/index.yml)
* [Azure Container Instances](https://docs.microsoft.com/container-instances/index.yml)
* [Kubernetes](https://kubernetes.io/) fürtben telepített [Azure Stack](https://docs.microsoft.com/azure-stack/index.yml). További információkért lásd: [Kubernetes üzembe helyezése az Azure Stackhez](https://docs.microsoft.com/azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

A következő táblázat ismerteti a minimális és ajánlott processzormagot és memóriát lefoglalni az egyes Personalizer Service-tárolóban.

| Tároló | Minimális | Ajánlott |
|-----------|---------|-------------|
|Personalizer | 1 mag, 4 GB memória | 2 mag, 8 GB memória |

Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.

Core és a memória felel meg a `--cpus` és `--memory` beállítások, amelyek részeként használhatók a `docker run` parancsot.

A tároló tud csatlakozni az Azure Event hubsnak a Rank és ellenszolgáltatás hívásokat a Personalizer kiszolgálóhoz az Azure-ban a továbbítási adatok sorrendben kell lennie, és csatlakozni a Personalizer API megvalósításához szükséges konfigurációs és a legújabb gépi képesnek kell lennie tanulási modelleket.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

Tárolórendszerképek Personalizer szolgáltatás érhető el. 

| Tároló | Adattár |
|-----------|------------|
| Personalizer | `mcr.microsoft.com/azure-cognitive-services/personalizer:latest` |

> [!TIP]
> Használhatja a [docker-rendszerképek](https://docs.docker.com/engine/reference/commandline/images/) paranccsal listát készíthet a letöltött tárolólemezképek. Például a következő parancs megjeleníti az azonosítója, a tárházat, és a címke az egyes letöltött tárolórendszerképet, és táblázatként vannak formázva:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/personalizer                 latest
>  ``` 

### <a name="docker-pull-for-the-personalizer-service-container"></a>Docker pull Personalizer szolgáltatás tároló

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/personalizer:latest
```

## <a name="how-the-container-works"></a>A tároló működése

A Personalizer tárolót a Personalizer szolgáltatás ügyfél részét jelöli. A tároló fut, akkor kap a modellek és a konfigurációs beállítások Personalizer a szolgáltatás az Azure-felhőben. A tárolószolgáltatás ezen információk segítségével a kérelmek megválaszolásához **rang** és **ellenszolgáltatás**. A tároló is ezeket a kérelmeket küld az Personalizer erőforrást az Azure-felhőben. Ezek az információk alapján a jelenlegi modell frissítési gyakoriság beállítását a Personalizer hurok modell betanításához majd szolgál. A frissített modell küld vissza a tárolót. 

![A helyszíni, Personalizer tároló ügyfél helyi architektúrája](./media/personalizer-container-howto/personalizer-container-architecture.png)

## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

1. [A tároló futtatásához](#run-the-container-with-docker-run), a szükséges a számlázás a beállításokat. További [példák](personalizer-container-configuration.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancs futtatása bármely három tárolóra. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{BILLING_KEY} | Ezt a kulcsot a tároló elindításához szolgál, és az Azure Portalon Personalizer Szolgáltatáskulcsok oldalon érhető el.  |
|{BILLING_ENDPOINT_URI} | A számlázási végpont URI azonosítóját az Azure Portalon Personalizer szolgáltatás áttekintése lapon érhető el.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/personalizer\
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Ezzel a paranccsal:

* A tároló rendszerképét Personalizer szolgáltatástároló fut
* Több processzormaggal és 4 gigabájt (GB) memóriát foglal le
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).

### <a name="run-multiple-containers-on-the-same-host"></a>Több tároló futtatása ugyanazon a gazdagépen

Ha a futtatni kívánt több tároló elérhetővé tett porttal, győződjön meg arról, egy másik portot a tárolók futtatásához. Például futtassa az első tároló 5000-es és a második tároló-as porton 5001.

Cserélje le a `<container-registry>` és `<container-name>` az értékeket a tárolók használata. Ezeket nem kell ugyanazt a tárolót kell. A Personalizer tárolót és a LUIS-tároló a gazdagépen futó együtt használhat, vagy beállíthatja, hogy a futtató több Personalizer tárolókat. 

Futtassa az első tároló 5000-es porton. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Futtassa a második tárolót 5001 porton.


```bash 
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Minden ezt követő tároló lehet egy másik porton. 

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

A tároló REST-alapú lekérdezési előrejelzési végpontot API-kat biztosít. 

Használja a gazdagép https://localhost:5000, API-k tároló.

## <a name="stop-the-container"></a>Állítsa le a tároló

Állítsa le a tárolót, a parancssori környezetben, ahol a tárolóban fut, nyomja le a **Ctrl + C**.

## <a name="troubleshoot"></a>Hibaelhárítás

A kimenet futtatásakor a tároló [csatlakoztatási](personalizer-container-configuration.md#mount-settings) és naplózás engedélyezve van, a tárolót hoz létre a naplófájlokat, amelyek hasznosak a hibaelhárítás indítása, vagy a tároló futtatása közben történik. 

## <a name="container-api-documentation"></a>Container API-dokumentáció

A tároló a végpontok dokumentáció teljes készletét nyújtja, valamint egy `Try it now` funkció. Ez a funkció lehetővé teszi, hogy a beállítások megadása HTML webes űrlapon, és győződjön meg arról, a lekérdezés, kód írása nélkül. Ha a lekérdezés visszatér, például a CURL-parancs megadott bemutatják a HTTP-fejlécek és törzs szükséges formátumban. 

> [!TIP]
> Olvassa el a [OpenAPI-specifikáció](https://swagger.io/docs/specification/about/), a tároló által támogatott API-műveleteket leíró a `/swagger` relatív URI. Példa:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

## <a name="billing"></a>Számlázás

Personalizer szolgáltatás tárolók Küldés a számlázási adatokat az Azure-ba, a használatával egy _Personalizer szolgáltatás_ erőforrást az Azure-fiókjával. 

Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem vásárlói adatokat küldeni a Microsoftnak. 

A `docker run` parancsot használja a következő argumentumok számlázás szempontjából:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a _Personalizer szolgáltatás_ erőforrás számlázási adatok nyomon követésére szolgál. |
| `Billing` | A végpont, a _Personalizer szolgáltatás_ erőforrás számlázási adatok nyomon követésére szolgál.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a tároló licencét.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](personalizer-container-configuration.md).

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és a munkafolyamat letöltése, telepítése és a futó tárolók Personalizer szolgáltatás. Összegezve:

* Personalizer Service biztosít egy Linux-tárolók Docker, személyre szabása fejlécbe foglalja.
* Tárolórendszerképek letöltődnek az a Microsoft Container Registry (MCR) az Azure-ban.
* Tárolórendszerképek futtatása a Docker.
* Használhatja a REST API vagy SDK-val Personalizer szolgáltatás tárolókban műveletek hívására adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Tárolók is el fogja küldeni a kérelem adatai a megfelelő szolgáltatást az Azure-ban való online képzési az alkalmazásában, és megkapja a Personalizer rendszeres időközönként az Azure-ból modellek frissítése.

## <a name="next-steps"></a>További lépések

[A Docker-tároló konfigurálása a `Docker Run` parancs](personalizer-container-configuration.md)