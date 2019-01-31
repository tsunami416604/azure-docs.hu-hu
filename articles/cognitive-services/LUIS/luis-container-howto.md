---
title: Docker-tárolók
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS-tároló a betanított vagy a közzétett alkalmazást tölt be egy docker-tárolót, és hozzáférést biztosít az API-végpontokat a tárolót a lekérdezés előrejelzéseket.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 97f11523c0418caaee66930c87a7de64570097d6
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296902"
---
# <a name="install-and-run-luis-docker-containers"></a>Telepítse és futtassa a LUIS docker-tárolók
 
A Language Understanding (LUIS) tároló betölti a betanított vagy közzétett Language Understanding modell, azaz egy [LUIS-alkalmazásokon](https://www.luis.ai), egy docker-tárolóba, és hozzáférést biztosít a lekérdezés előrejelzéseket a tároló API-ból végpontok. Lekérdezés naplók összegyűjtése a tárolóból, és ezek biztonsági feltöltése az Azure Language Understanding modellre való az alkalmazás előrejelzési pontosság növeléséhez.

A következő videó bemutatja a tároló használatával.

[![Cognitive Services tároló bemutató](./media/luis-container-how-to/luis-containers-demo-video-still.png)](https://aka.ms/luis-container-demo)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A LUIS-tároló futtatásához az alábbiakkal kell rendelkeznie: 

|Szükséges|Cél|
|--|--|
|Docker-motor| A Docker-motor telepítve van szüksége egy [gazdaszámítógép](#the-host-computer). A docker csomagokat biztosít, a Docker-környezet konfigurálása a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), és [Linux](https://docs.docker.com/engine/installation/#supported-platforms). A Docker és a tárolók alapfogalmainak ismertetését lásd: a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/).<br><br> Docker kell konfigurálni, hogy a tárolók számlázási adatok küldése az Azure-ba történő csatlakozáshoz. <br><br> **A Windows**, a Docker Linux-tárolók támogatása is kell konfigurálni.<br><br>|
|Docker-ismeretek | A Docker fő fogalmaira, például a beállításjegyzékek, adattárak, tárolók, és tárolórendszerképeket, valamint alapszintű ismerete alapvető ismeretekkel kell `docker` parancsokat.| 
|Language Understanding (LUIS) erőforrás és a társított alkalmazás |A tároló használatához rendelkeznie kell:<br><br>* A [ _Language Understanding_ Azure-erőforrás](luis-how-to-azure-subscription.md), valamint a kapcsolódó végponti kulcs és a végpont URI azonosítója (használja a számlázási végpontként).<br>* Egy betanított vagy a közzétett alkalmazás csomagolt egy csatlakoztatott bemenetként a tárolóhoz, az a társított alkalmazás azonosítóját.<br>* A szerzői műveletek kulcs az alkalmazáscsomag letöltéséhez, akkor használatos, ha ez az API-ból.<br><br>Ezek a követelmények parancssori argumentumok átadása a következő változók használhatók:<br><br>**{AUTHORING_KEY}** : Ez a kulcs segítségével az alkalmazáscsomag beszerzése az intelligens HANGFELISMERÉSI szolgáltatás a felhőben és a lekérdezés naplók feltöltése a felhőbe. A formátum `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`.<br><br>**{APPLICATION_ID}** : Ez az azonosító segítségével válassza ki az alkalmazást. A formátum `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.<br><br>**{ENDPOINT_KEY}** : Ez a kulcs segítségével a tárolót. A végpont kulcs két helyen találja. Az első az Azure Portalon a _Language Understanding_ erőforrás kulcsok listája. A végpont kulcs is érhető el a kulcsokat és a végpont a LUIS portál beállítások lapján. Ne használja a kezdő szintű kulcs.<br><br>**{BILLING_ENDPOINT}** : A számlázási végpont értékét az Azure Portalon Language Understanding áttekintése oldalon érhető el. Példa: `https://westus.api.cognitive.microsoft.com/luis/v2.0`.<br><br>A [és végponti kulcs létrehozási](luis-boundaries.md#key-limits) Ha különböző célokat szolgálnak. Ne alkalmazza őket felcserélhető. |

### <a name="the-host-computer"></a>A számítógép

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Tároló-követelményeket és javaslatokat

Ez a tároló minimális és ajánlott értékek a beállításokat támogatja:

|Beállítás| Minimális | Ajánlott |
|-----------|---------|-------------|
|Processzormagok<BR>`--cpus`|1 mag|1 mag|
|Memory (Memória)<BR>`--memory`|2 GB|4 GB|
|Másodpercenkénti tranzakciók<BR>(TPS)|20 TPS|40 TPS|

Egyes maghoz kell lennie legalább 2.6-os gigahertz (GHz) vagy gyorsabb.

A `--cpus` és `--memory` részeként beállításokat használják a `docker run` parancsot.

## <a name="get-the-container-image-with-docker-pull"></a>A tárolórendszerkép beolvasása `docker pull`

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal töltse le a tárolórendszerkép az `mcr.microsoft.com/azure-cognitive-services/luis` tárházat:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/luis:latest
```

Használja a [ `docker pull` ](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal letöltheti egy tárolórendszerképet.

Rendelkezésre álló címkék teljes leírását például `latest` megtekintéséhez használja az előző parancsban, [LUIS](https://go.microsoft.com/fwlink/?linkid=2043204) a Docker hubon.

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>A tároló használata

Ha a tároló a [gazdaszámítógép](#the-host-computer), a következő eljárás használható a tárolóval.

![A Language Understanding (LUIS) tárolót használó folyamat](./media/luis-container-how-to/luis-flow-with-containers-diagram.jpg)

1. [Exportálási csomag](#export-packaged-app-from-luis) tároló LUIS-portálon vagy az intelligens HANGFELISMERÉSI API-k.
1. Alkalmazáscsomag-fájl helyezhetik át a szükséges **bemeneti** könyvtárába a [gazdaszámítógép](#the-host-computer). Ne nevezze, alter, és a LUIS alkalmazáscsomag-fájl kibontása.
1. [A tároló futtatásához](##run-the-container-with-docker-run), és a szükséges _bemeneti csatlakoztatási_ és számlázási beállításait. További [példák](luis-container-configuration.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 
1. [A tároló előrejelzési végpontja lekérdezése](#query-the-containers-prediction-endpoint). 
1. Amikor végzett a tárolóval [importálni az a végpont](#import-the-endpoint-logs-for-active-learning) a kimenetből csatlakoztassa a LUIS-portálon és [leállítása](#stop-the-container) a tárolót.
1. Használja a LUIS portál [aktív tanulás](luis-how-to-review-endoint-utt.md) a a **tekintse át a végpont utterances** oldalon az alkalmazás javítására.

Az alkalmazás a tárolóban futó nem módosítható. Az order a módosítás az alkalmazás a tárolóban, meg kell változtatnia az intelligens HANGFELISMERÉSI szolgáltatás használata az alkalmazás a [LUIS](https://www.luis.ai) portálon, vagy használja a LUIS [API-k készítése](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f). Ezután betanítása és/vagy közzététele, majd töltse le az új csomag és futtassa újra a tárolót.

A LUIS-alkalmazásokon belül a tároló nem lehet exportálni, térjen vissza az intelligens HANGFELISMERÉSI szolgáltatás. A lekérdezés csak a naplókat tölthet fel. 

## <a name="export-packaged-app-from-luis"></a>A LUIS csomagolt alkalmazás exportálása

A LUIS-tároló betanított vagy közzétett LUIS-alkalmazások felhasználói utterances az előrejelzési lekérdezések megválaszolásához szükséges. A LUIS alkalmazás eléréséhez használjon a betanított vagy közzétett csomag API-t. 

Az alapértelmezett hely a `input` alkönyvtár képest, amelyen futtatni a `docker run` parancsot.  

Az alkalmazáscsomag-fájl elhelyezése egy könyvtárban, és hivatkozni, a bemeneti csatlakoztatási ebben a könyvtárban, a docker-tároló futtatásakor. 

### <a name="package-types"></a>A csomagok

A bemeneti csatlakoztatási könyvtár is tartalmaz a **éles**, **átmeneti**, és **Trained** verziók az alkalmazás egyidejűleg. Összes csomag csatlakoztatva vannak. 

|Csomag típusa|Lekérdezési API-végpont|Lekérdezés rendelkezésre állása|Alkalmazáscsomag fájlnév formátuma|
|--|--|--|--|
|Betanított|GET, Post|Csak a tároló|`{APPLICATION_ID}_v{APPLICATION_VERSION}.gz`|
|Fájlok másolása folyamatban|GET, Post|Az Azure és a tároló|`{APPLICATION_ID}_STAGING.gz`|
|Production|GET, Post|Az Azure és a tároló|`{APPLICATION_ID}_PRODUCTION.gz`|

> [!IMPORTANT]
> Ne nevezze, alter, és a LUIS-csomag fájlok kibontása.

### <a name="packaging-prerequisites"></a>Csomagolási Előfeltételek

A LUIS-alkalmazások csomagolása, mielőtt az alábbiakkal kell rendelkeznie:

|Követelmények|Részletek|
|--|--|
|Azure _Language Understanding_ erőforrás-példány|Támogatott régiók közé tartozik.<br><br>USA nyugati RÉGIÓJA (```westus```)<br>Nyugat-Európa (```westeurope```)<br>Kelet-Ausztrália (```australiaeast```)|
|Betanított vagy közzétett LUIS-alkalmazás|Nélkül [nem támogatott a függőségek](#unsupported-dependencies). |
|A hozzáférést a [gazdaszámítógép](#the-host-computer)'s fájlrendszer |A gazdaszámítógép engedélyeznie kell egy [bemeneti csatlakoztatási](luis-container-configuration.md#mount-settings).|
  
### <a name="export-app-package-from-luis-portal"></a>A LUIS-portál alkalmazáscsomag exportálása

A LUIS [portál](https://www.luis.ai) lehetővé teszi a exportálni a csomagot a betanított vagy a közzétett alkalmazáshoz. 

### <a name="export-published-apps-package-from-luis-portal"></a>Közzétett alkalmazás csomag exportálása a LUIS-portálról

A közzétett alkalmazás-csomag érhető el a **saját alkalmazások** listáját tartalmazó lapon. 

1. Jelentkezzen be a LUIS [portál](https://www.luis.ai).
1. Az alkalmazás nevétől balra a jelölőnégyzet bejelölésével a listában. 
1. Válassza ki a **exportálása** elem a lista fölötti környezetfüggő eszköztárból.
1. Válassza ki **tároló (GZIP) exportálása**.
1. Válassza ki a környezetet, **üzemelési** vagy **előkészítési pont**.
1. A csomag letöltése a böngészőből.

![Az alkalmazás oldal exportálása menüből a tároló a közzétett csomag exportálása](./media/luis-container-how-to/export-published-package-for-container.png)

### <a name="export-trained-apps-package-from-luis-portal"></a>A LUIS portálról betanított alkalmazáscsomag exportálása

A betanított alkalmazáscsomag érhető el a **verziók** listáját tartalmazó lapon. 

1. Jelentkezzen be a LUIS [portál](https://www.luis.ai).
1. Válassza ki az alkalmazást a listáról. 
1. Válassza ki **kezelés** az alkalmazás navigációs sávban.
1. Válassza ki **verziók** a bal oldali navigációs sávon.
1. A bal oldalon, a verzió neve a jelölőnégyzet bejelölésével a listában.
1. Válassza ki a **exportálása** elem a lista fölötti környezetfüggő eszköztárból.
1. Válassza ki **tároló (GZIP) exportálása**.
1. A csomag letöltése a böngészőből.

![A tároló a betanított csomag exportálása a verziók lap exportálási menüből](./media/luis-container-how-to/export-trained-package-for-container.png)


### <a name="export-published-apps-package-from-api"></a>API-ból a közzétett alkalmazás csomag exportálása

A következő REST API-módszer segítségével, hogy már LUIS-alkalmazásokon csomag már [közzétett](luis-how-to-publish-app.md). A saját megfelelő értékeit helyettesítse a helyőrzők értékét az API-hívás a használatával a HTTP-specifikációnak az alábbi táblázatban.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Érték |
|-------------|-------|
|{APPLICATION_ID} | A közzétett a LUIS-alkalmazás azonosítója. |
|{APPLICATION_ENVIRONMENT} | A környezet a közzétett LUIS-alkalmazás. Használja a következő értékek egyikét:<br/>```PRODUCTION```<br/>```STAGING``` |
|{AUTHORING_KEY} | Az Authoring Tool kulcs a LUIS-fiók a közzétett LUIS-alkalmazás.<br/>Az Authoring Tool kulcs kap a **felhasználói beállítások** a LUIS-portál oldalain. |
|{AZURE_REGION} | A megfelelő Azure-régió:<br/><br/>```westus``` – USA nyugati RÉGIÓJA<br/>```westeurope``` – Nyugat-Európa<br/>```australiaeast``` – Ausztrália keleti régiója |

A következő CURL-parancs segítségével a közzétett csomag, a saját értékei behelyettesítésével:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/slot/{APPLICATION_ENVIRONMENT}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_{APPLICATION_ENVIRONMENT}.gz
```

Sikeres művelet esetén a válasz a LUIS alkalmazáscsomag-fájl. Mentse a fájlt a bemeneti csatlakoztatási a tároló megadott tárolási helyen található. 

### <a name="export-trained-apps-package-from-api"></a>API-ból betanított alkalmazáscsomag exportálása

A következő REST API-módszer segítségével, hogy már a LUIS alkalmazás becsomagolása már [betanított](luis-how-to-train.md). A saját megfelelő értékeit helyettesítse a helyőrzők értékét az API-hívás a használatával a HTTP-specifikációnak az alábbi táblázatban.

```http
GET /luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip HTTP/1.1
Host: {AZURE_REGION}.api.cognitive.microsoft.com
Ocp-Apim-Subscription-Key: {AUTHORING_KEY}
```

| Helyőrző | Érték |
|-------------|-------|
|{APPLICATION_ID} | A betanított LUIS-alkalmazás Alkalmazásazonosítója. |
|{APPLICATION_VERSION} | Az alkalmazás verziója a betanított LUIS-alkalmazás. |
|{AUTHORING_KEY} | Az Authoring Tool kulcs a LUIS-fiók a közzétett LUIS-alkalmazás.<br/>Az Authoring Tool kulcs kap a **felhasználói beállítások** a LUIS-portál oldalain.  |
|{AZURE_REGION} | A megfelelő Azure-régió:<br/><br/>```westus``` – USA nyugati RÉGIÓJA<br/>```westeurope``` – Nyugat-Európa<br/>```australiaeast``` – Ausztrália keleti régiója |

A következő CURL-parancs segítségével a betanított csomag:

```bash
curl -X GET \
https://{AZURE_REGION}.api.cognitive.microsoft.com/luis/api/v2.0/package/{APPLICATION_ID}/versions/{APPLICATION_VERSION}/gzip  \
 -H "Ocp-Apim-Subscription-Key: {AUTHORING_KEY}" \
 -o {APPLICATION_ID}_v{APPLICATION_VERSION}.gz
```

Sikeres művelet esetén a válasz a LUIS alkalmazáscsomag-fájl. Mentse a fájlt a bemeneti csatlakoztatási a tároló megadott tárolási helyen található. 

## <a name="run-the-container-with-docker-run"></a>A tároló futtatásához `docker run`

Használja a [futtatása docker](https://docs.docker.com/engine/reference/commandline/run/) parancsot a tároló futtatásához. A parancs paraméterei a következők:

| Helyőrző | Érték |
|-------------|-------|
|{ENDPOINT_KEY} | Ez a kulcs segítségével a tárolót. Ne használja a kezdő szintű kulcs. |
|{BILLING_ENDPOINT} | A számlázási végpont értékét az Azure Portalon Language Understanding áttekintése oldalon érhető el.|

Cserélje le ezeket a paramétereket a következő példában a saját értékeire `docker run` parancsot.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 2 \
--mount type=bind,src=c:\input,target=/input \
--mount type=bind,src=c:\output,target=/output \
mcr.microsoft.com/azure-cognitive-services/luis \
Eula=accept \
Billing={BILLING_ENDPOINT} \
ApiKey={ENDPOINT_KEY}
```

> [!Note] 
> Az előző parancs a könyvtárat használja ki a `c:` meghajtón a Windows-engedély ütközések elkerülése érdekében. Ha szeretné használni a bemeneti könyvtár egy adott címtárhoz, előfordulhat, hogy kell biztosítania a docker szolgáltatás engedéllyel. Az előző docker parancsot használja a fordított perjel `\`, egy sor folytatási karaktert. Cserélje le, vagy távolítsa el ezt a alapján a [gazdaszámítógép](#the-host-computer) operációs rendszerre vonatkozó követelmények. Ne módosítsa az argumentumok sorrendje, kivéve, ha nagyon ismeri a docker-tárolókat.


Ezzel a paranccsal:

* Egy tárolóban futtatja a LUIS tárolórendszerképet
* A LUIS alkalmazás tölt be bemeneti csatlakoztatási c:\input, tároló-gazdagépen található:
* Foglalja le a két processzormagot és memóriát 4 gigabájt (GB)
* Elérhetővé teszi az 5000-es TCP-porton és a egy pszeudo-TTY lefoglalja a tároló
* Menti a tároló és a LUIS csatlakoztatási c:\output, tároló-gazdagépen található a kimeneti naplók
* Után kilép, automatikusan eltávolítja a tárolót. A tároló rendszerképét az továbbra is elérhető az állomáson. 

További [példák](luis-container-configuration.md#example-docker-run-commands) , a `docker run` parancs érhetők el. 

> [!IMPORTANT]
> A `Eula`, `Billing`, és `ApiKey` beállítások meg kell adni a tároló futtatásához; ellenkező esetben a tároló nem indul el.  További információkért lásd: [számlázási](#billing).
> Apikey tulajdonsággal végzett tesztelése értéke a **kulcs** a kulcsok és a végpontok lapon a LUIS-portálon, és érhető el az Azure Language Understanding Resource kulcsok oldalon is.  

## <a name="query-the-containers-prediction-endpoint"></a>A tároló előrejelzési végpontja lekérdezése

A tároló REST-alapú lekérdezési előrejelzési végpontot API-kat biztosít. Közzétett (előkészítési vagy termelési) alkalmazások végpontoknak egy _különböző_ útvonalat, mint a végpontok betanított alkalmazásokhoz. 

Használja a gazdagép https://localhost:5000, API-k tároló. 

|Csomag típusa|Módszer|Útválasztás|Lekérdezési paraméterek|
|--|--|--|--|
|Közzétéve|[Első](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78), [Post](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)|/luis/v2.0/apps/{appId}?|q={q}<br>& előkészítés<br>[&timezoneOffset]<br>[& részletes]<br>[& log]<br>|
|Betanított|GET, Post|/luis/v2.0/apps/{appId}/versions/{versionId}?|q={q}<br>[&timezoneOffset]<br>[& részletes]<br>[& log]|

A lekérdezési paraméterek konfigurálása módját és a lekérdezésekre adott válaszok adja vissza:

|Lekérdezési paraméter|Typo|Cél|
|--|--|--|
|`q`|sztring|A felhasználó utterance (kifejezés).|
|`timezoneOffset`|szám|A timezoneOffset lehetővé teszi, hogy [időzóna módosítása](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) az előre összeállított entitások datetimeV2 használják.|
|`verbose`|logikai|Adja vissza minden leképezések és eredményeiket, ha a beállítása igaz értékre. Alapértelmezett érték FALSE (hamis), csak a felső leképezést ad vissza.|
|`staging`|logikai|Átmeneti környezet eredményei, ha az értéket ad vissza lekérdezés beállítása igaz értékre. |
|`log`|logikai|Lekérdezések, amellyel a rendszer később [aktív tanulás](luis-how-to-review-endoint-utt.md). Alapértelmezett érték az igaz.|

### <a name="query-published-app"></a>Közzétett alkalmazás lekérdezése

A következő CURL-parancs a közzétett alkalmazás esetében a tároló lekérdezéséhez például:

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}?q=turn%20on%20the%20lights&staging=false&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
Hogy a lekérdezések a **átmeneti** környezet, módosítsa a **átmeneti** lekérdezési karakterlánc paraméter értéke igaz: 

`staging=true`

### <a name="query-trained-app"></a>Lekérdezés betanított alkalmazás

A következő CURL-parancsot a tároló egy betanított alkalmazás lekérdezéséhez például: 

```bash
curl -X GET \
"http://localhost:5000/luis/v2.0/apps/{APPLICATION_ID}/versions/{APPLICATION_VERSION}?q=turn%20on%20the%20lights&timezoneOffset=0&verbose=false&log=true" \
-H "accept: application/json"
```
A verziónév rendelkezik egy legfeljebb 10 karakterből álló, és csak az engedélyezett URL-karaktereket tartalmaz. 

## <a name="import-the-endpoint-logs-for-active-learning"></a>Aktív tanulás importálni az a végpont

Ha egy kimeneti csatlakoztatási van megadva, a LUIS-tároló, alkalmazás lekérdezés naplófájlok lesznek mentve a kimeneti könyvtárba, ahol {INSTANCE_ID} a tároló azonosítója. Az alkalmazás lekérdezési napló tartalmazza a lekérdezés, válasz és időbélyegek minden egyes előrejelzési lekérdezés elküldve a LUIS-tárolóhoz. 

A következő helyen jeleníti meg a beágyazott könyvtárstruktúrát a tároló-naplófájlok.
`
/output/luis/{INSTANCE_ID}/
`
 
A LUIS-portálról, válassza ki az alkalmazást, majd válassza ki **végpont naplók importálása** feltölteni ezeket a naplókat. 

![Importálja a naplófájlokat tároló aktív tanulás](./media/luis-container-how-to/upload-endpoint-log-files.png)

Miután feltöltötte a napló, [tekintse át a végpont](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-review-endpoint-utterances) utterances a LUIS-portálon.

## <a name="stop-the-container"></a>Állítsa le a tároló

Állítsa le a tárolót, a parancssori környezetben, ahol a tárolóban fut, nyomja le a **Ctrl + C**.

## <a name="troubleshooting"></a>Hibaelhárítás

A kimenet futtatásakor a tároló [csatlakoztatási](luis-container-configuration.md#mount-settings) és naplózás engedélyezve van, a tárolót hoz létre a naplófájlokat, amelyek hasznosak a hibaelhárítás indítása, vagy a tároló futtatása közben történik. 

## <a name="containers-api-documentation"></a>Tároló API-dokumentáció

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Számlázás

A számlázási adatokat az Azure-ba, használja a LUIS tároló küld egy _Language Understanding_ erőforrást az Azure-fiókjával. 

Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (az utterance (kifejezés)) vásárlói adatokat küldeni a Microsoftnak. 

A `docker run` számlázási argumentumai:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a _Language Understanding_ erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a kiépített LUIS Azure erőforrás a megadott API-kulcs `Billing`. |
| `Billing` | Az a végpont a _Language Understanding_ erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett LUIS Azure erőforrás URI-ját.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a tároló licencét.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.

Ezek a beállítások kapcsolatos további információkért lásd: [tárolók konfigurálása](luis-container-configuration.md).

## <a name="unsupported-dependencies"></a>Nem támogatott függőségek

A LUIS alkalmazás használhatja, ha azt **nem tartalmazza a** valamelyik a következő függőségeket:

Alkalmazás nem támogatott konfigurációk|Részletek|
|--|--|
|A tároló nem támogatott kultúrák| Német (de-DE)<br>Holland (Hollandia, NL)<br>Japán (ja-JP)<br>|
|Nem támogatott tartományok|Előre összeállított tartományok, beleértve az előre összeállított tartományban szándékok és entitások felismerésére|
|Entitások nem támogatott kulturális környezetek listája|[KeyPhrase](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-keyphrase) kulturális környezetek listája az előre összeállított entitások|
|Nem támogatott entitásokat (en-US) angol kulturális környezet|[GeographyV2](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-prebuilt-geographyv2) előre összeállított entitások|
|Beszéd betanítási művelet|Külső függőségek nem támogatottak a tárolóban.|
|Hangulatelemzés|Külső függőségek nem támogatottak a tárolóban.|
|Bing – helyesírás-ellenőrzés|Külső függőségek nem támogatottak a tárolóban.|

## <a name="summary"></a>Összegzés

Ebben a cikkben megtanulta, fogalmak és letöltése, telepítése és Language Understanding (LUIS) tárolók futó munkafolyamatokat. Összegezve:

* Language Understanding (LUIS) egy Linux-tárolót biztosít a Docker biztosító végpont lekérdezés előrejelzések kimondott szöveg.
* Tárolórendszerképek a rendszer letölti a Microsoft Container Registry (MCR).
* Tárolórendszerképek futtatása a Docker.
* REST API használatával lekérdezheti a tárolói végpontok adja meg a gazdagép a tároló URI-t.
* Számlázási adatokat adjon meg egy tároló hárítható el.

> [!IMPORTANT]
> Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak.

## <a name="next-steps"></a>További lépések

* Felülvizsgálat [tárolók konfigurálása](luis-container-configuration.md) a konfigurációs beállítások
* Tekintse meg [– gyakori kérdések (GYIK)](luis-resources-faq.md) LUIS funkció kapcsolatos problémák megoldásához.
* Több [Cognitive Services-tárolók](../cognitive-services-container-support.md)
