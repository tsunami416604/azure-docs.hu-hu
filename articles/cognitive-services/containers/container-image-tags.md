---
title: Cognitive Services-tárolók rendszerképcímkéi
titleSuffix: Azure Cognitive Services
description: A kognitív szolgáltatás tárolójának összes képcímkéje átfogó felsorolása.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 11/17/2020
ms.author: aahi
ms.openlocfilehash: 09a83c28d07540b8ecd813e7ab2f10ceee891d7a
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95792989"
---
# <a name="azure-cognitive-services-container-image-tags-and-release-notes"></a>Azure Cognitive Services Container képcímkék és kibocsátási megjegyzések

Az Azure Cognitive Services számos tároló-lemezképet kínál. A tároló-beállításjegyzékek és a megfelelő adattárak a tároló lemezképei között változnak. Mindegyik tároló rendszerképének neve több címkét is kínál. A tároló képcímkéje a tároló rendszerképének verziószámozási mechanizmusa. Ez a cikk átfogó referenciául szolgál az összes Cognitive Services-tároló lemezképének és azok elérhető címkének a listázásához.

> [!TIP]
> A használatakor ügyeljen rá, [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) hogy a tároló beállításjegyzékének, a tárháznak, a tároló rendszerkép nevének és a hozzá tartozó címkének a becsomagolásával, a kis-és **nagybetűk megkülönböztetésével**.

## <a name="anomaly-detector"></a>Anomáliadetektor

Az [anomália-detektor][ad-containers] tárolójának képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/decision` tárházban található, és neve `anomaly-detector` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/decision/anomaly-detector` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/decision/anomaly-detector/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013560003-amd64-preview` |      |

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.012300001-amd64-preview` |       |

---

## <a name="read-ocr-optical-character-recognition"></a>OCR olvasása (optikai karakterfelismerés)

A [Computer Vision][cv-containers] az OCR-tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry syndicateban. A `azure-cognitive-services` tárházban található, és neve `read` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/vision/read` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/vision/read/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kibocsátási megjegyzések a következőhöz `3.2-preview.1` :

* Új v 3.2 tároló

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `3.2-preview.1` |  |

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

Kibocsátási megjegyzések a következőhöz `v2.0.013250001-amd64-preview` :

* A tároló memóriahasználat további csökkentése.
* A többrétegű telepítéshez külső gyorsítótár szükséges. Például állítsa be a Redis a gyorsítótárazáshoz.
* Rögzített hiányzó eredmények, ha a Redis cache be van állítva, és `ResultExpirationPeriod` 0 értékre van állítva.
* Távolítsa el a kérelem törzsének korlátozását a 26MB. A tároló mostantól képes fogadni >26MB fájlokat.
* Adja meg az időbélyeget, és készítsen verziót a konzol naplózásához.

Kibocsátási megjegyzések a következőhöz: `1.1.013050001-amd64-preview`

* `ReadEngineConfig:ResultExpirationPeriod`A tároló inicializálási konfigurációja hozzáadva, ha a rendszernek meg kell tisztítania az eredmények felismerését. 
    * A beállítás óra, az alapértelmezett érték pedig 48 óra.
    * A beállítás csökkenti a memória használatát az eredmény tárolására, különösen a memóriában tárolt tárolók használata esetén.
    * 1. példa. ReadEngineConfig: ResultExpirationPeriod = 1, a rendszer törli a felismerés eredményének 1 óra a folyamat után.
    * Ha a konfiguráció értéke 0, a rendszer törli az eredmény beolvasása után az elismerés eredményét.

* Rögzített 500 belső kiszolgálóhiba, ha a rendszer érvénytelen képformátumot ad át a rendszernek. Ekkor 400-es hibát ad vissza:

    ```json
    {
        "error": {
        "code": "InvalidImageSize",
        "message": "Image must be between 1024 and 209715200 bytes."
        }
    }
    ```

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `2.0.013250001-amd64-preview` |       |
| `1.1.013050001-amd64-preview` |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

---


## <a name="form-recognizer"></a>Form Recognizer

Az [űrlap-felismerő][fr-containers] tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/custom-form` tárházban található, és neve `labeltool` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/custom-form/labeltool/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |


# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

---

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

A [Luis][lu-containers] -tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/language` tárházban található, és neve `luis` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/language/luis` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/language/luis/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012280003-amd64-preview` |       |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.012130003-amd64-preview` |       |

---

## <a name="custom-speech-to-text"></a>Custom Speech – szöveg

A [Custom Speech – szöveg][sp-cstt] tároló rendszerképet a `mcr.microsoft.com` Container Registry szindikátusban találhatja meg. A `azure-cognitive-services/speechservices/` tárházban található, és neve `custom-speech-to-text` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-speech-to-text` . Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-speech-to-text/tags/list).


# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `2.7.0-amd64` :

**Szolgáltatások**
* A központozás alapértelmezettként van beállítva.

Vegye figyelembe, hogy a mellékelt kifejezések listája a tároló képének mérete miatt megnőtt.

| Képcímkék                    | Jegyzetek | Digest                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |
| `2.7.0-amd64`                 |       | `sha256:d1573c2543cb7afedb0122da0995f345767b02f9c5f181950acf1509ca65726` |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)
Kiadási Megjegyzés a következőhöz `2.6.0-amd64` :

**Szolgáltatások**
* A phraselist v2 támogatása 
* A kifejezések listáját a következő területi beállítások támogatják:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * hu-hu
    * zh-cn
* Egyéni alapmodell letöltése – támogatás. 
    * Használat `BaseModelLocale=<locale>` a `docker run` paranccsal
* Teljes mértékben áttelepítve a .NET 3,1-es verzióra

**Hibajavítások**
* Kijavítva a hiba, ahol a megbízhatósági pontszám mindig 1 Diarization módban
* Áttelepítve a TextAnalytics 3,0 API-ra

Vegye figyelembe, hogy a mellékelt kifejezések listája a tároló képének mérete miatt megnőtt.

Kiadási Megjegyzés a következőhöz `2.5.0-amd64` :

**Szolgáltatások**
* Egyéni kiejtés támogatása egyéni modelleken
* Az Azure és az USA kormányzati felhő támogatása

**Hibajavítások**
* A Futtatás nem gyökérszintű felhasználói probléma kijavítása Diarization módban

| Képcímkék                    | Jegyzetek               |
|-------------------------------|:--------------------|
| `2.6.0-amd64`                 |                     |
| `2.5.0-amd64`                 |   első GA-verzió    |

---

## <a name="custom-text-to-speech"></a>Egyéni szöveg – beszéd

Az [egyéni szöveg-beszéd][sp-ctts] tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `custom-text-to-speech` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/custom-text-to-speech` . Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/custom-text-to-speech/tags/list).


# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `1.9.0-amd64` :

Rendszeres havi kiadás

| Képcímkék                    | Jegyzetek | Digest                                                                  |
|-------------------------------|:------|:------------------------------------------------------------------------|
| `latest`                      |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |
| `1.9.0-amd64`                 |       | `sha256:e0397cf12d1367b13dd258f782bb513c93afcd5ee4b897794fe533205336355` |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)
Kiadási Megjegyzés a következőhöz `1.8.0-amd64` :

**Szolgáltatások**
* Teljes mértékben áttelepítve a .NET 3,1-es verzióra

Kiadási Megjegyzés a következőhöz `1.7.0-amd64` :

**Funkció**
* Részben migrálva a .NET 3,1-es verzióra

| Képcímkék                    | Jegyzetek               |
|-------------------------------|:--------------------|
| `1.8.0-amd64`                 |                     |
| `1.7.0-amd64`                 |   első GA-verzió    |

---

## <a name="speech-to-text"></a>Diktálás

A [beszédfelismerési][sp-stt] tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/speechservices/` tárházban található, és neve `speech-to-text` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/speech-to-text` . A címkék teljes listája megtalálható a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/speech-to-text/tags/list).

A beszédfelismerési v 2.5.0-ben a képek az USA-beli *közigazgatási Virginia* régióban támogatottak. Ezen régió használatakor használja az *Egyesült Államok kormányának Virginia* számlázási végpontját és API-kulcsait.

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `2.7.0-amd64-<locale>` :

**Szolgáltatások**
* A következő új területi beállítások támogatása:
    * AR-BH, AR-IQ, AR-jo, AR-LB, AR-om, AR-Sy
    * bg-bg
    * el-gr
    * EN-HK, en-IE, en-pH, en-SG, en-za
    * es-AR, es-Bo, es-cl, es-Co, es-CR, es-cu, es-do, es-EC, es-gt, es-PA, es-PE, es-PR, es-uy, es-ve
    * et-ee
    * GA – IE
    * hr-hr
    * hu-hu
    * lt-lt
    * lv-lv
    * MT – MT
    * ro-ro
    * sk-sk
    * SL-SL
* A központozás alapértelmezés szerint engedélyezve van.

Vegye figyelembe, hogy a mellékelt kifejezések listája a tároló képének mérete miatt megnőtt. 

| Képcímkék                    | Jegyzetek                                                                                                |
|-------------------------------|:-----------------------------------------------------------------------------------------------------|
| `latest`                      | Tároló képe a `en-US` területi beállítással.                                                             |
| `2.7.0-amd64-<locale>`        | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `2.7.0-amd64-en-us`. |

Ehhez a tárolóhoz a következő területi beállítások állnak rendelkezésre.

| Területi beállítás a v 2.7.0           | Jegyzetek                                    | Digest                                                                  |
|-----------------------------|:-----------------------------------------|:------------------------------------------------------------------------|
| `ar-ae`                     | Tároló képe a `ar-AE` területi beállítással. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-bh`                     | Tároló képe a `ar-BH` területi beállítással. | `sha256:5a2c140661f50d0c95587121ec1ab8895289f4dda5b3ad14074413e869e6bd4` |
| `ar-eg`                     | Tároló képe a `ar-EG` területi beállítással. | `sha256:783bb8321fcfb7890b0c99935099f7e84c85a698c2fe0031c661e265358d79c` |
| `ar-iq`                     | Tároló képe a `ar-IQ` területi beállítással. | `sha256:abd0101f73c1cf71f30da7b11b93d2a7ac8877dbfcfc2d34553d20705aca7a2` |
| `ar-jo`                     | Tároló képe a `ar-JO` területi beállítással. | `sha256:d4c7fd2a1637e163aa106c23b6a759e8c78366c60ece83b3aabfe93ebabae07` |
| `ar-kw`                     | Tároló képe a `ar-KW` területi beállítással. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-lb`                     | Tároló képe a `ar-LB` területi beállítással. | `sha256:20e5c9105e86625c72de54290a6eb07630d35c3760f729c4b855e3661583dfe` |
| `ar-om`                     | Tároló képe a `ar-OM` területi beállítással. | `sha256:97f1b44f2cbb837a2ef86441a0a52a07f706240edb6ef6618ee4db8cbbe1c19` |
| `ar-qa`                     | Tároló képe a `ar-QA` területi beállítással. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sa`                     | Tároló képe a `ar-SA` területi beállítással. | `sha256:c8e99e71e6740cf671f3bf79de8b7dd890122cb674eedd2440e71e7cbc4c66b` |
| `ar-sy`                     | Tároló képe a `ar-SY` területi beállítással. | `sha256:51980a2e2c3dd3548deedcedaf5fc688db602a5eced1a4b7df7d10750393623` |
| `bg-bg`                     | Tároló képe a `bg-BG` területi beállítással. | `sha256:1c1acf0fbb353ebb04692f37eb4d4cdf0b4e309720dd7e709001dada0d1ea81` |
| `ca-es`                     | Tároló képe a `ca-ES` területi beállítással. | `sha256:c60baa0007f61c7652b97b49645215de63411125d627c974c09222e316df204` |
| `cs-cz`                     | Tároló képe a `cs-CZ` területi beállítással. | `sha256:3fa09fc3a6bde6b77df2444aae8fc78b5f25fb9010171d1682db116ea5801f5` |
| `da-dk`                     | Tároló képe a `da-DK` területi beállítással. | `sha256:4b26dbba50c2771943880b68e0e4ea0713d0e3bb8bad884454849bccc9e94a3` |
| `de-de`                     | Tároló képe a `de-DE` területi beállítással. | `sha256:5109ed80b1fecf4db0328adcd50528d0aa9e726b5fc84587c40aaea4e91256d` |
| `el-gr`                     | Tároló képe a `el-GR` területi beállítással. | `sha256:fc8b466c588bf097efac2b79454d5ac0df5c6990398f07ede9be7e1d536e4bd` |
| `en-au`                     | Tároló képe a `en-AU` területi beállítással. | `sha256:3461892a27fc3eb3f9610b2def00bc15f380c6b9797c90ceca19e6abb55f6a6` |
| `en-ca`                     | Tároló képe a `en-CA` területi beállítással. | `sha256:a0509be39785f1e869bd96ab10e7c07d3f4e61c9aa17ff5900076e7bd64ba11` |
| `en-gb`                     | Tároló képe a `en-GB` területi beállítással. | `sha256:1b976fc7ac109e61dcf74af3652c12535e3db92931d2d0bb2ea59bd46f9efed` |
| `en-hk`                     | Tároló képe a `en-HK` területi beállítással. | `sha256:0b1e1df101f978869c98f6e50632712016b8311fc89b334e7f44e968d64bf2f` |
| `en-ie`                     | Tároló képe a `en-IE` területi beállítással. | `sha256:c5ba0d3c7219ce39f0b918a51a7cae8a65c277f564279cad920e068725aa39f` |
| `en-in`                     | Tároló képe a `en-IN` területi beállítással. | `sha256:e907f07be498f024103f6fe6abffa23e242bf3585724741b29a2f3f41d0899c` |
| `en-nz`                     | Tároló képe a `en-NZ` területi beállítással. | `sha256:66845f6ce20ae71d609867c6eb4772366ce042499e4bcdce4c1b579daf7fad7` |
| `en-ph`                     | Tároló képe a `en-PH` területi beállítással. | `sha256:e7874653bf66b1a1ab344b3391eb8767be34260b7f11b62fd057cbe17b805b2` |
| `en-sg`                     | Tároló képe a `en-SG` területi beállítással. | `sha256:827cdb158280e6f4037f4815410c7aa78abf9c6467876c1504aecfef787bdd7` |
| `en-us`                     | Tároló képe a `en-US` területi beállítással. | `sha256:248d17340055e3e137219ddc234c605e6a53ceead136ea55c9697c352da6a8d` |
| `en-za`                     | Tároló képe a `en-ZA` területi beállítással. | `sha256:a8abc99f498db7088bb25acec47da81e90b6a5eaa1c6f78e0f9a314d839d0ae` |
| `es-ar`                     | Tároló képe a `es-AR` területi beállítással. | `sha256:edf78429630851b6eb01f54f8a8a1aeeda9971c6a834403a204662eda22b3b9` |
| `es-bo`                     | Tároló képe a `es-BO` területi beállítással. | `sha256:5832b44f1da2f6b9a097c99babfbc370d8d0eabe1ff8daabec2c3f482dc9d63` |
| `es-cl`                     | Tároló képe a `es-CL` területi beállítással. | `sha256:409a712b96235e154472134f96ff9272265f1e5b555e00ad03c2260b0781009` |
| `es-co`                     | Tároló képe a `es-CO` területi beállítással. | `sha256:99792bc083dc16e0edf15491e6a840d786c9140b747551563a8d98f66f0b415` |
| `es-cr`                     | Tároló képe a `es-CR` területi beállítással. | `sha256:21fe14a538e5b8b2d288b00b8f5a02d87469e285f32e725155042079f336ac9` |
| `es-cu`                     | Tároló képe a `es-CU` területi beállítással. | `sha256:05d40eae01cec4c42c4febd379cd61373eb43d0aacfd47b988bb95e6a6ad216` |
| `es-do`                     | Tároló képe a `es-DO` területi beállítással. | `sha256:73dd0e0d4f39a259563ee7cc18c2e72c9ab20c52905fe343e0413ca7c4b3f0d` |
| `es-ec`                     | Tároló képe a `es-EC` területi beállítással. | `sha256:c3e69139ef365fe9332b5b68b43458242c7dad9d9f2b557431272306e81cb9e` |
| `es-es`                     | Tároló képe a `es-ES` területi beállítással. | `sha256:bd83fcfc116ba645a0e12a7a93b6ada74a8f701172f826a91c5f223a1dbaa61` |
| `es-gt`                     | Tároló képe a `es-GT` területi beállítással. | `sha256:5bb9b18b91b74e123e3720893d88bfcb0a87dac31a1f7171d23c7cb1fa09fee` |
| `es-hn`                     | Tároló képe a `es-HN` területi beállítással. | `sha256:941d108a4b76eb554e8f13cf5090665a702de3ebf35b75e4350f0916dfccd72` |
| `es-mx`                     | Tároló képe a `es-MX` területi beállítással. | `sha256:cebea03732781b4425500d162ae6580bbd7ce9b5f4ede988c4570fe311d8567` |
| `es-ni`                     | Tároló képe a `es-NI` területi beállítással. | `sha256:8ba165f94ad840936ebd0af17a0a63aa08a6292e7ad9029f5b93eef41165eb9` |
| `es-pa`                     | Tároló képe a `es-PA` területi beállítással. | `sha256:c61b7f1b6801a03c3eab0dd1aede87017a86bc7368ded2f8bad8d9e5f60d0d3` |
| `es-pe`                     | Tároló képe a `es-PE` területi beállítással. | `sha256:447a3ab3f302aba24d201d9f5b2877ffcd64dfd5e9d6b88d9924847160b2de2` |
| `es-pr`                     | Tároló képe a `es-PR` területi beállítással. | `sha256:a53b3295c986e91ee8cf93ebe1057b997c76ef7f99913508b859311a194fdd4` |
| `es-py`                     | Tároló képe a `es-PY` területi beállítással. | `sha256:85b3f75e75e63e29521daf772ee68a59ac2428579512501aa81dc51a2315652` |
| `es-sv`                     | Tároló képe a `es-SV` területi beállítással. | `sha256:db5ece7ba536e38d5de59cd37807630ab76589dcf1c97e253f98d7f44d9424e` |
| `es-us`                     | Tároló képe a `es-US` területi beállítással. | `sha256:99f2743725bb71e25543484f49bcfde14584ccbbaaa912678938d69d965075a` |
| `es-uy`                     | Tároló képe a `es-UY` területi beállítással. | `sha256:a3e11c16a97a1ae76408d812b2fee1e4b3ba07160bbcb62a22814523568ee5d` |
| `es-ve`                     | Tároló képe a `es-VE` területi beállítással. | `sha256:8cb431aafd84263ead8de946377c1d3f2ddfa7e172b8a4c5aa7ba477c5b41f0` |
| `et-ee`                     | Tároló képe a `et-EE` területi beállítással. | `sha256:943e7cf894e9d75341a58993104824c1c8cd8da1322cc5a732e9d53882c6523` |
| `fi-fi`                     | Tároló képe a `fi-FI` területi beállítással. | `sha256:35658e9dce796cb96a1371f250398e86351ea1b5ada080da7ce8471b30c7cae` |
| `fr-ca`                     | Tároló képe a `fr-CA` területi beállítással. | `sha256:62256cad671e8baa03fdd4c5f4eca7d5c5effedd64cafd9020ba72c9c4210e0` |
| `fr-fr`                     | Tároló képe a `fr-FR` területi beállítással. | `sha256:b385993232d9daa327d1a7b067268927b17f36eed3e8d423748794544c62746` |
| `ga-ie`                     | Tároló képe a `ga-IE` területi beállítással. | `sha256:ab9abdb993b0f7487edda8200f1393ac44ba4888c0f444a02afb6c85ca3e393` |
| `gu-in`                     | Tároló képe a `gu-IN` területi beállítással. | `sha256:328e69488f2948722d7ccc97e266071f61a8c9f65cd671688490955806526de` |
| `hi-in`                     | Tároló képe a `hi-IN` területi beállítással. | `sha256:b9b0bfec80aa53d06ea2cbd9097f753ec5caaf00ac2f00321ae7ad916fd7fa6` |
| `hr-hr`                     | Tároló képe a `hr-HR` területi beállítással. | `sha256:ab849cd2eeea682f8958bba8986fe90f0f7bb3b447512a10cf464e8e1ce4ea5` |
| `hu-hu`                     | Tároló képe a `hu-HU` területi beállítással. | `sha256:30f239b155d91523442cf74a1f2732304fa2b50ae7b786833bb6a020b982621` |
| `it-it`                     | Tároló képe a `it-IT` területi beállítással. | `sha256:288f95413870eb9d33bf1dabfa6fbd6b55b0faa52e4d5face3171d1dd4ddbdd` |
| `ja-jp`                     | Tároló képe a `ja-JP` területi beállítással. | `sha256:e3ab37a80c215dec565eca212f57eb81887fc2894452868dff92e3bd42c4bb9` |
| `ko-kr`                     | Tároló képe a `ko-KR` területi beállítással. | `sha256:c1208b8459333b606af516cd7806e9d4d5e002247bb1225e1f246563b356890` |
| `lt-lt`                     | Tároló képe a `lt-LT` területi beállítással. | `sha256:8dec331161d3c29fc65ba6651fcc6cfe69fa314519f408b5f9f8eb27da09830` |
| `lv-lv`                     | Tároló képe a `lv-LV` területi beállítással. | `sha256:7cf31282910b339666bb2b0a555caa7fc6ae414eea4423a41f35c3527f83235` |
| `mr-in`                     | Tároló képe a `mr-IN` területi beállítással. | `sha256:9cb012bd58ef7723d4905d6fa3c1fde96e33c354b3d96d4e3ff69cf6e1bfe3a` |
| `mt-mt`                     | Tároló képe a `mt-MT` területi beállítással. | `sha256:a0094c032ea555b168ec5751ab3257337d902d526e9ae335671fb751a352378` |
| `nb-no`                     | Tároló képe a `nb-NO` területi beállítással. | `sha256:6bbc326e20a6a785b1ca33143b42a060858efb67b863a267d6efb7aebb48f87` |
| `nl-nl`                     | Tároló képe a `nl-NL` területi beállítással. | `sha256:94b4ddf4cc80fa666e422f8416aea3f98ebe4842dfe9b1f4bfea7c47eb61127` |
| `pl-pl`                     | Tároló képe a `pl-PL` területi beállítással. | `sha256:58e5f78bf772c3c8cbd5f0c5d6e67f5348e04e3f893d84738a2a3e964bab256` |
| `pt-br`                     | Tároló képe a `pt-BR` területi beállítással. | `sha256:f500ef956bd28807f40df1f9f0520e437c5084f61a3be6d1379e746887d5b7c` |
| `pt-pt`                     | Tároló képe a `pt-PT` területi beállítással. | `sha256:c841d2dbe5f40adf6039242c106985febb1a44212feb55d9769fe31134ec116` |
| `ro-ro`                     | Tároló képe a `ro-RO` területi beállítással. | `sha256:93271c39c0a134e987a069c2a65289acff9869ae0d90fdcb39928c9ef0fd86b` |
| `ru-ru`                     | Tároló képe a `ru-RU` területi beállítással. | `sha256:8d6b3c600e56cc96813b8c14b7916c5539a20ba561dc1c6d5bbef6285d6eef6` |
| `sk-sk`                     | Tároló képe a `sk-SK` területi beállítással. | `sha256:6d604092cc6c964663a1c97d91c8f1c8cf4b46d07427d03f7041c0cc55eb521` |
| `sl-si`                     | Tároló képe a `sl-SI` területi beállítással. | `sha256:f237ed58fedefcc749e74be1258cc70e5a690ee6c5a6b6388bd24075faa61da` |
| `sv-se`                     | Tároló képe a `sv-SE` területi beállítással. | `sha256:da4233e6658b00eefdadb9d4acd889c6550a5e2a4a7af7a9f915c878abd4c9c` |
| `ta-in`                     | Tároló képe a `ta-IN` területi beállítással. | `sha256:22b77606d25e9c2f52bf3cad6218782b4719f6a9dcfadc770468d266758a56c` |
| `te-in`                     | Tároló képe a `te-IN` területi beállítással. | `sha256:7f4d11372862ca1d65fc9b868e2d775701b8e6eabd786c90c4e9ab82ba86e88` |
| `th-th`                     | Tároló képe a `th-TH` területi beállítással. | `sha256:69033bcd7c0f59d31bafec6c2b7a9ff343928cdd58c16105415c291d555d37b` |
| `tr-tr`                     | Tároló képe a `tr-TR` területi beállítással. | `sha256:4b7d339846a0d371dfe25aa2e626f131003c01329c9a1da468eb3703ef176ea` |
| `zh-cn`                     | Tároló képe a `zh-CN` területi beállítással. | `sha256:a428459830fb766083212f71c5638a65ce30d8dd84f6c624ae22768e8a76976` |
| `zh-hk`                     | Tároló képe a `zh-HK` területi beállítással. | `sha256:7a2903462b67336a6ce4c8e2faac42052f0a4392d1d5eb3839758cc8d0429f1` |
| `zh-tw`                     | Tároló képe a `zh-TW` területi beállítással. | `sha256:30fd2b3660e047d24a46fbba14ba282f15bc0339ec93f49afd0d02ff4069146` |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

Kiadási Megjegyzés a következőhöz `2.6.0-amd64-<locale>` :

**Szolgáltatások**
* Frissítve a legújabb modellekre, és teljes mértékben migrálva a .NET 3,1-es verzióra
* A phraselist v2 támogatása
* A kifejezések listáját a következő területi beállítások támogatják:
    * EN-au
    * en-CA
    * en-GB
    * EN-in
    * hu-hu
    * zh-cn
* Új területi beállítás támogatása `cs-CZ` 
    * A tőkésítés és a központozás jelenleg nem támogatott.

**Hibajavítások**
* Javít egy problémát, amelyben a megbízhatósági pontszámok mindig Diarization módban voltak
* Áttelepítve a TextAnalytics 3,0 API használatával

Vegye figyelembe, hogy a mellékelt kifejezések listája a tároló képének mérete miatt megnőtt. 

Kiadási Megjegyzés a következőhöz `2.5.0-amd64-<locale>` :

**Szolgáltatások**
* Az Azure US government Cloud támogatása

**Hibajavítások**
* Kijavít egy problémát, amely nem root felhasználóként fut Diarization módban

| Képcímkék                  | Jegyzetek                                    |
|-----------------------------|:-----------------------------------------|
| `2.6.0-amd64-<locale>`      | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `2.6.0-amd64-en-us`. |
| `2.5.0-amd64-<locale>`      | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `2.5.0-amd64-en-us`. |


Ehhez a tárolóhoz a következő területi beállítások állnak rendelkezésre.

| Területi beállítás a v 2.6.0 esetében           | Jegyzetek                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Tároló képe a `ar-AE` területi beállítással. |
| `ar-eg`                     | Tároló képe a `ar-EG` területi beállítással. |
| `ar-kw`                     | Tároló képe a `ar-KW` területi beállítással. |
| `ar-qa`                     | Tároló képe a `ar-QA` területi beállítással. |
| `ar-sa`                     | Tároló képe a `ar-SA` területi beállítással. |
| `ca-es`                     | Tároló képe a `ca-ES` területi beállítással. |
| `cs-cz`                     | Tároló képe a `cs-CZ` területi beállítással. |
| `da-dk`                     | Tároló képe a `da-DK` területi beállítással. |
| `de-de`                     | Tároló képe a `de-DE` területi beállítással. |
| `en-au`                     | Tároló képe a `en-AU` területi beállítással. |
| `en-ca`                     | Tároló képe a `en-CA` területi beállítással. |
| `en-gb`                     | Tároló képe a `en-GB` területi beállítással. |
| `en-in`                     | Tároló képe a `en-IN` területi beállítással. |
| `en-nz`                     | Tároló képe a `en-NZ` területi beállítással. |
| `en-us`                     | Tároló képe a `en-US` területi beállítással. |
| `es-es`                     | Tároló képe a `es-ES` területi beállítással. |
| `es-mx`                     | Tároló képe a `es-MX` területi beállítással. |
| `fi-fi`                     | Tároló képe a `fi-FI` területi beállítással. |
| `fr-ca`                     | Tároló képe a `fr-CA` területi beállítással. |
| `fr-fr`                     | Tároló képe a `fr-FR` területi beállítással. |
| `gu-in`                     | Tároló képe a `gu-IN` területi beállítással. |
| `hi-in`                     | Tároló képe a `hi-IN` területi beállítással. |
| `it-it`                     | Tároló képe a `it-IT` területi beállítással. |
| `ja-jp`                     | Tároló képe a `ja-JP` területi beállítással. |
| `ko-kr`                     | Tároló képe a `ko-KR` területi beállítással. |
| `mr-in`                     | Tároló képe a `mr-IN` területi beállítással. |
| `nb-no`                     | Tároló képe a `nb-NO` területi beállítással. |
| `nl-nl`                     | Tároló képe a `nl-NL` területi beállítással. |
| `pl-pl`                     | Tároló képe a `pl-PL` területi beállítással. |
| `pt-br`                     | Tároló képe a `pt-BR` területi beállítással. |
| `pt-pt`                     | Tároló képe a `pt-PT` területi beállítással. |
| `ru-ru`                     | Tároló képe a `ru-RU` területi beállítással. |
| `sv-se`                     | Tároló képe a `sv-SE` területi beállítással. |
| `ta-in`                     | Tároló képe a `ta-IN` területi beállítással. |
| `te-in`                     | Tároló képe a `te-IN` területi beállítással. |
| `th-th`                     | Tároló képe a `th-TH` területi beállítással. |
| `tr-tr`                     | Tároló képe a `tr-TR` területi beállítással. |
| `zh-cn`                     | Tároló képe a `zh-CN` területi beállítással. |
| `zh-hk`                     | Tároló képe a `zh-HK` területi beállítással. |
| `zh-tw`                     | Tároló képe a `zh-TW` területi beállítással. |

| Területi beállítás a v 2.5.0-hoz           | Jegyzetek                                    |
|-----------------------------|:-----------------------------------------|
| `ar-ae`                     | Tároló képe a `ar-AE` területi beállítással. |
| `ar-eg`                     | Tároló képe a `ar-EG` területi beállítással. |
| `ar-kw`                     | Tároló képe a `ar-KW` területi beállítással. |
| `ar-qa`                     | Tároló képe a `ar-QA` területi beállítással. |
| `ar-sa`                     | Tároló képe a `ar-SA` területi beállítással. |
| `ca-es`                     | Tároló képe a `ca-ES` területi beállítással. |
| `da-dk`                     | Tároló képe a `da-DK` területi beállítással. |
| `de-de`                     | Tároló képe a `de-DE` területi beállítással. |
| `en-au`                     | Tároló képe a `en-AU` területi beállítással. |
| `en-ca`                     | Tároló képe a `en-CA` területi beállítással. |
| `en-gb`                     | Tároló képe a `en-GB` területi beállítással. |
| `en-in`                     | Tároló képe a `en-IN` területi beállítással. |
| `en-nz`                     | Tároló képe a `en-NZ` területi beállítással. |
| `en-us`                     | Tároló képe a `en-US` területi beállítással. |
| `es-es`                     | Tároló képe a `es-ES` területi beállítással. |
| `es-mx`                     | Tároló képe a `es-MX` területi beállítással. |
| `fi-fi`                     | Tároló képe a `fi-FI` területi beállítással. |
| `fr-ca`                     | Tároló képe a `fr-CA` területi beállítással. |
| `fr-fr`                     | Tároló képe a `fr-FR` területi beállítással. |
| `gu-in`                     | Tároló képe a `gu-IN` területi beállítással. |
| `hi-in`                     | Tároló képe a `hi-IN` területi beállítással. |
| `it-it`                     | Tároló képe a `it-IT` területi beállítással. |
| `ja-jp`                     | Tároló képe a `ja-JP` területi beállítással. |
| `ko-kr`                     | Tároló képe a `ko-KR` területi beállítással. |
| `mr-in`                     | Tároló képe a `mr-IN` területi beállítással. |
| `nb-no`                     | Tároló képe a `nb-NO` területi beállítással. |
| `nl-nl`                     | Tároló képe a `nl-NL` területi beállítással. |
| `pl-pl`                     | Tároló képe a `pl-PL` területi beállítással. |
| `pt-br`                     | Tároló képe a `pt-BR` területi beállítással. |
| `pt-pt`                     | Tároló képe a `pt-PT` területi beállítással. |
| `ru-ru`                     | Tároló képe a `ru-RU` területi beállítással. |
| `sv-se`                     | Tároló képe a `sv-SE` területi beállítással. |
| `ta-in`                     | Tároló képe a `ta-IN` területi beállítással. |
| `te-in`                     | Tároló képe a `te-IN` területi beállítással. |
| `th-th`                     | Tároló képe a `th-TH` területi beállítással. |
| `tr-tr`                     | Tároló képe a `tr-TR` területi beállítással. |
| `zh-cn`                     | Tároló képe a `zh-CN` területi beállítással. |
| `zh-hk`                     | Tároló képe a `zh-HK` területi beállítással. |
| `zh-tw`                     | Tároló képe a `zh-TW` területi beállítással. |

---

## <a name="text-to-speech"></a>Szövegfelolvasás

A [szöveg-beszéd][sp-tts] tároló képe a `mcr.microsoft.com` Container Registry syndicateban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `text-to-speech` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/text-to-speech` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/text-to-speech/tags/list).


# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kiadási Megjegyzés a következőhöz `1.9.0-amd64-<locale-and-voice>` :

* Rendszeres havi kiadás

| Képcímkék                                  | Jegyzetek                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással és a `en-US-AriaRUS` hanggal.                                            | 
| `1.9.0-amd64-<locale-and-voice>`            | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.9.0-amd64-en-us-ariarus`.  |


| Területi beállítások a v 1.9.0                          | Jegyzetek                                                                      | Digest                         |
|---------------------------------------------|:---------------------------------------------------------------------------|:-------------------------------|
| `ar-eg-hoda`                                | A tároló képe a `ar-EG` területi beállítással és a `ar-EG-Hoda` hanggal.            | `sha256:2b19cfd2212d6517b286aa18617d2f9d1dd1520078b559cbbf9240599270d10` | 
| `ar-sa-naayf`                               | A tároló képe a `ar-SA` területi beállítással és a `ar-SA-Naayf` hanggal.           | `sha256:6063aae5fb15c62b234cf945220916516a06ca81354c5311dee02af4d8cb0d3` |
| `bg-bg-ivan`                                | A tároló képe a `bg-BG` területi beállítással és a `bg-BG-Ivan` hanggal.            | `sha256:c6786916464755e64ffa64e69e8f3e7ef16115bac00bb6ea1e45368c42c58d1` |
| `ca-es-herenarus`                           | A tároló képe a `ca-ES` területi beállítással és a `ca-ES-HerenaRUS` hanggal.       | `sha256:2a8a1accbf99e2746c9345b77e2f261e0111227312c402cc2e1cd8760cdc82a` |
| `cs-cz-jakub`                               | A tároló képe a `cs-CZ` területi beállítással és a `cs-CZ-Jakub` hanggal.           | `sha256:3e464356bb08c9c966af2b28a88ccafd591aecd2e37a0fedb356bd443720e8d` |
| `da-dk-hellerus`                            | A tároló képe a `da-DK` területi beállítással és a `da-DK-HelleRUS` hanggal.        | `sha256:b85c43080804103673ff99dddea644a516c4103e8b1f11fa3dd34857492cd40` |
| `de-at-michael`                             | A tároló képe a `de-AT` területi beállítással és a `de-AT-Michael` hanggal.         | `sha256:87b57ee61f964e4d72e75d860c499fa3b3d8dbda6a96c97d696beb20aa8b2a9` |
| `de-ch-karsten`                             | A tároló képe a `de-CH` területi beállítással és a `de-CH-Karsten` hanggal.         | `sha256:ab1385b9746f4f054204302b9d564a433ae03748021b8ed71b4a3a224af1e9b` |
| `de-de-heddarus`                            | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-hedda`                               | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           | `sha256:82185a710c87f9dde678d88036867559ab3bf5f08f234d60d1548d3e106db57` |
| `de-de-stefan-apollo`                       | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal.   | `sha256:56a1c63e7e6a0f5623ddc1f6a44ac6e51471d073e02e14e8c8b1e577930d816` |
| `el-gr-stefanos`                            | A tároló képe a `el-GR` területi beállítással és a `el-GR-Stefanos` hanggal.        | `sha256:ccbbb09f29ff8f276e246037183c7a3e9a3eb5bf33a942b22205cce3c6857f2` |
| `en-au-catherine`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal.       | `sha256:0c7374890f963e1ae9507e89dc9965a94723bd57802826c0677cd5262189783` |
| `en-au-hayleyrus`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal.       | `sha256:7430bf8eace8294ca085f36ea56399261b2b4f69027e86649e8f3868fc3d811` |
| `en-ca-heatherrus`                          | A tároló képe a `en-CA` területi beállítással és a `en-CA-HeatherRUS` hanggal.      | `sha256:0166ce1de3d669ea4ad80738c63369b7032125a54ecabade07241d740a94cfe` |
| `en-ca-linda`                               | A tároló képe a `en-CA` területi beállítással és a `en-CA-Linda` hanggal.           | `sha256:50bed6a7bde9b793d307bcc3ace4c0f28d4a33c7a4dad9b3a394dc39a3e1c28` |
| `en-gb-george-apollo`                       | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal.   | `sha256:50b800c0018a39609ddb1cee1b10062bf38a907644c393d20786db7c3ade748` |
| `en-gb-hazelrus`                            | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal.        | `sha256:2aa79394dfeac8cec0cc1704a5199949cfccf347fe61161d02c7000c4ffcfa6` |
| `en-gb-susan-apollo`                        | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal.    | `sha256:7a3174b3aae5f10241e731d392b56f124808cdd506f881ced919ced73d836c0` |
| `en-ie-sean`                                | A tároló képe a `en-IE` területi beállítással és a `en-IE-Sean` hanggal.            | `sha256:2457202fadb2354fc8d3666432096bd87c07760a4e3f4dbcc49853fff658577` |
| `en-in-heera-apollo`                        | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal.    | `sha256:e4068cd7ca4272ea94819e2ba8743d2a76c8710b162db5e9ecbde6c92c12877` |
| `en-in-priyarus`                            | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal.        | `sha256:9d63a0ed53ac06178ab84588551421c0e1d04b8bad3321410ebb99c3ca2a9e8` |
| `en-in-ravi-apollo`                         | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal.     | `sha256:67049c9ce591336655943f5030afcfdaa150a8aace7b372425a69cc33a6b7b9` |
| `en-us-aria24krus`                          | A tároló képe a `en-US` területi beállítással és a `en-US-Aria24kRUS` hanggal.      | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-ariarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-AriaRUS` hanggal.         | `sha256:a95acf6874bf3df7ae8e96be779f80cb5405d21250227b0c4b3ddbcb3014082` |
| `en-us-benjaminrus`                         | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal.     | `sha256:93cd49adaaa2a1bdfb06ab655be164ae66f206cb7c03a2cbd59e5fba70610ab` |
| `en-us-guy24krus`                           | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal.       | `sha256:7b788bfcaae4c63c274ca15924bfd861cfcafd5fec13f685d80babc25b2949d` |
| `en-us-zirarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal.         | `sha256:bfc87a77df5695ad43481348500fba8f6a7b495708fba200706049469b5ba97` |
| `es-es-helenarus`                           | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal.       | `sha256:0b6c17aca75efb64aa9bfc0d83303038fe58d4b2fb1fc94c9380a4335b80796` |
| `es-es-laura-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal.    | `sha256:d6fcffc944c37a2dd0de29c39b82f3f8cce3a95ad925d2814ed7538335d5d4f` |
| `es-es-pablo-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal.    | `sha256:a460bc53d9083d3c3770129995cf96cc1069ae4e8101f1739d304fe210f0af0` |
| `es-mx-hildarus`                            | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal.        | `sha256:5b7578fc5b00158dfa674d95a3f1d57f22eb285e8333b4006d1fe1808bda7ba` |
| `es-mx-raul-apollo`                         | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal.     | `sha256:03922fb017783c86d788c72e01c7ede440f8f3c913c86cab19bad4dfc2e4a2b` |
| `fi-fi-heidirus`                            | A tároló képe a `fi-FI` területi beállítással és a `fi-FI-HeidiRUS` hanggal.        | `sha256:146c1f98d6fa061016eba41db6e7b654eef222d37f35406d4b43477bb2ff897` |
| `fr-ca-caroline`                            | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal.        | `sha256:1ee2e53f12ad1c72665d2aef64e9d4a7f9ea05670cad84dcae5e75409494f32` |
| `fr-ca-harmonierus`                         | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal.     | `sha256:a21d25d3ac699af4e9ba9194aadd9b45f35fd9205224f3429a4c7da41fc38fe` |
| `fr-ch-guillaume`                           | A tároló képe a `fr-CH` területi beállítással és a `fr-CH-Guillaume` hanggal.       | `sha256:216125a9bd89a95d3c4dc2d7e031398659427b3aa7d4663d23a65737972e42b` |
| `fr-fr-hortenserus`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal.     | `sha256:795a698120eecbd80c48e738f73300739c1698ca859130ddb4236317bcdf70f` |
| `fr-fr-julie-apollo`                        | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal.    | `sha256:f6eb70d523c435c2e3a713b32a8af4a781df7ec043caad2fc7f458ee341eb2f` |
| `fr-fr-paul-apollo`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal.     | `sha256:28864c662a20f459b3051b1da2967a605e06267e6408285f7c2552748cf4eed` |
| `he-il-asaf`                                | A tároló képe a `he-IL` területi beállítással és a `he-IL-Asaf` hanggal.            | `sha256:eaa834bac6b69abef096b36a8baead741db78fe438af3d30f60abde3631d639` |
| `hi-in-hemant`                              | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Hemant` hanggal.          | `sha256:cfea0fa7cce9cc512f2fbb8b76f1c00fe5c32fad853c90b15934cf4ee6262fa` |
| `hi-in-kalpana-apollo`                      | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana-Apollo` hanggal.  | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hi-in-kalpana`                             | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana` hanggal.         | `sha256:afbd6cc0413f3a3c9f6df044b6df6d9dac9e8e888c2cb619fefbdc3e105c644` |
| `hr-hr-matej`                               | A tároló képe a `hr-HR` területi beállítással és a `hr-HR-Matej` hanggal.           | `sha256:86683597c62752b4d769b69e5294979fafd4c277aaef1536e1cb19f9f06c0bf` |
| `hu-hu-szabolcs`                            | A tároló képe a `hu-HU` területi beállítással és a `hu-HU-Szabolcs` hanggal.        | `sha256:aa64eed28ca2ad060e2e02188e0401bf34e4caf7e2182b70a30ce33b3c11c9c` |
| `id-id-andika`                              | A tároló képe a `id-ID` területi beállítással és a `id-ID-Andika` hanggal.          | `sha256:0e1394d231a57a1df8163ccb634dc2ef2f8103b10608a40ab3efc5c0fbe9ded` |
| `it-it-cosimo-apollo`                       | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal.   | `sha256:eef97f2817fc24405823a5fe4e825244db32279b44c0e6631e8ad9a5c1acf40` |
| `it-it-luciarus`                            | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal.        | `sha256:ebc331b0685f482d2f55619fa81fd451fd7c8f107f9cd7ad159bc6213ae4e33` |
| `ja-jp-ayumi-apollo`                        | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal.    | `sha256:e9cb7dfd2eec154c8f3d530c16b66e8558c5955a2edaede69740067f00e43cf` |
| `ja-jp-harukarus`                           | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal.       | `sha256:93ce2ef6177c0d8ac70b61df8b11fcbcdfd3c0be0cc51cd8644f26679a741c2` |
| `ja-jp-ichiro-apollo`                       | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal.   | `sha256:6a18bae69ac63b42ba992b8b74d8d31d91ca984d61b5f62f38be988cf38645e` |
| `ko-kr-heamirus`                            | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal.        | `sha256:7a48252d4ada2af43f9266a70113426d330bac192348cbdc929022295a0e727` |
| `ms-my-rizwan`                              | A tároló képe a `ms-MY` területi beállítással és a `ms-MY-Rizwan` hanggal.          | `sha256:90e2ecac14f8e960934fd013d208fc2a0afe1bfff037d5648d422bda8d8a76e` |
| `nb-no-huldarus`                            | A tároló képe a `nb-NO` területi beállítással és a `nb-NO-HuldaRUS` hanggal.        | `sha256:217b61bd6244b5effda8f12a2c563ce1b4572e9c5b8a08df143665f9ff754e4` |
| `nl-nl-hannarus`                            | A tároló képe a `nl-NL` területi beállítással és a `nl-NL-HannaRUS` hanggal.        | `sha256:fbff48dfc9dfadadf377867b28f6e3a3bd605e59da20f77a531efcc7d85d16e` |
| `pl-pl-paulinarus`                          | A tároló képe a `pl-PL` területi beállítással és a `pl-PL-PaulinaRUS` hanggal.      | `sha256:856a033a09925773fa4b4531e199ab7c03c537f366acecbda60f8d21735725e` |
| `pt-br-daniel-apollo`                       | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal.   | `sha256:2d1ec975f1aee56a6fc6039d154fb3f2fbeb4636f7078c5dfe99aeddb6a3634` |
| `pt-br-heloisarus`                          | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal.      | `sha256:b7d629f37ab3305274764264dc08fab5236e60ef18d40e987618115db67ce44` |
| `pt-pt-heliarus`                            | A tároló képe a `pt-PT` területi beállítással és a `pt-PT-HeliaRUS` hanggal.        | `sha256:8b380ae7e4aac9d4ada4d15fa9e667387bc9ca038796d9b6999953bfbc97259` |
| `ro-ro-andrei`                              | A tároló képe a `ro-RO` területi beállítással és a `ro-RO-Andrei` hanggal.          | `sha256:b00ca7f1411169a5baf7263a8d7e5eed1a72084d9489eaf458429dfc338564a` |
| `ru-ru-ekaterinarus`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-EkaterinaRUS` hanggal.    | `sha256:31c588c31e3ac67305af66091e7756dfc4ca454317d0228116ea0b2fedf5d71` |
| `ru-ru-irina-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Irina-Apollo` hanggal.    | `sha256:e76437f8da7c279b38d2643defc997a13b4a364e9a212895cdb33a9a3f6457f` |
| `ru-ru-pavel-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Pavel-Apollo` hanggal.    | `sha256:461c1efa6cce0b10a87f338bc637aca76aef8458061a688870fb3343d682da0` |
| `sk-sk-filip`                               | A tároló képe a `sk-SK` területi beállítással és a `sk-SK-Filip` hanggal.           | `sha256:7fb0cfab4c0fe2913eb20f28a25c6663015d62f82e7e7864d9f7fac2d27697b` |
| `sl-si-lado`                                | A tároló képe a `sl-SI` területi beállítással és a `sl-SI-Lado` hanggal.            | `sha256:5336173d410e10ffeb5dc211a583887e33754319c757914955057d398dfbb0a` |
| `sv-se-hedvigrus`                           | A tároló képe a `sv-SE` területi beállítással és a `sv-SE-HedvigRUS` hanggal.       | `sha256:5dc8cdcc3054386bf69596707d9d261d4db5bfd09f1882ceb4e29238a34b24e` |
| `ta-in-valluvar`                            | A tároló képe a `ta-IN` területi beállítással és a `ta-IN-Valluvar` hanggal.        | `sha256:74ea485f23e4c1fe0029e06894860aa0188c36c0e14ea3584a06d4216ccef56` |
| `te-in-chitra`                              | A tároló képe a `te-IN` területi beállítással és a `te-IN-Chitra` hanggal.          | `sha256:ff2977a98ef691da543db08be9cfe04d7fc3bf8f78b29310c163e47303b2ddd` |
| `th-th-pattara`                             | A tároló képe a `th-TH` területi beállítással és a `th-TH-Pattara` hanggal.         | `sha256:ba7e2c0e5e75d9f2b52aa50c97728616c43e81f48c15e24665e4c2ea5770a8f` |
| `tr-tr-sedarus`                             | A tároló képe a `tr-TR` területi beállítással és a `tr-TR-SedaRUS` hanggal.         | `sha256:375a8ceae89ea1f0dda551feff30ae3679231189b527992edbc49988d042d66` |
| `vi-vn-an`                                  | A tároló képe a `vi-VN` területi beállítással és a `vi-VN-An` hanggal.              | `sha256:b6f82148295b38b4039c45c48695ec50b4e97cd02b18d49c39bf9fca3bec958` |
| `zh-cn-huihuirus`                           | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal.       | `sha256:3e773931f3adaac92cba43773a241692a2b471ebe73ec51c475df8ff63b7ee1` |
| `zh-cn-kangkang-apollo`                     | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. | `sha256:05fc0d5075a1094caf70d98b4a9469952be52cb6eb4d9f7b9ff4ae961100c7b` |
| `zh-cn-yaoyao-apollo`                       | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal.   | `sha256:d7613bcefc48e85b9d6f07c8cd223c16d4958bcf7f24087575250e97c593ac1` |
| `zh-hk-danny-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Danny-Apollo` hanggal.    | `sha256:efe22bc123dac9312dcaeb859a377d81f61fbb25ef46e4678d36ec6bebc5d32` |
| `zh-hk-tracy-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Tracy-Apollo` hanggal.    | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-hk-tracyrus`                            | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-TracyRUS` hanggal.        | `sha256:802c60bc65012c03ffe96268dca79b8c6dcd0c5cc6180ec271c50ef5c9ba132` |
| `zh-tw-hanhanrus`                           | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-HanHanRUS` hanggal.       | `sha256:95d58922463d577d4c4722ab722a5768af35fb62236d47f6709717dea758909` |
| `zh-tw-yating-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Yating-Apollo` hanggal.   | `sha256:33eec6e3aaaedafaf3969746eeaf97a1760e763505decfe2abaa03f5054bfd2` |
| `zh-tw-zhiwei-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Zhiwei-Apollo` hanggal.   | `sha256:456db2898b2e5a9c30b7071ce6ea3f141438cbf1aa4899c7ffccfc2f0dde5bd` |


# <a name="previous-version"></a>[Előző verzió](#tab/previous)

Kiadási Megjegyzés a következőhöz `1.8.0-amd64-<locale-and-voice>` :

**Funkció**

* Teljes mértékben áttelepítve a .NET 3,1-es verzióra

Kiadási Megjegyzés a következőhöz `1.7.0-amd64-<locale-and-voice>` :

**Funkció**

* Frissített összetevők a .NET 3,1-es verzióra

| Képcímkék                                  | Jegyzetek                                                                                                         |
|---------------------------------------------|:--------------------------------------------------------------------------------------------------------------|
| `1.8.0-amd64-<locale-and-voice>`            | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.8.0-amd64-en-us-ariarus`.  |
| `1.7.0-amd64-<locale-and-voice>`            | első GA-verzió. Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.7.0-amd64-en-us-ariarus`.  |


| Területi beállítások a v 1.8.0-hoz                          | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | A tároló képe a `ar-EG` területi beállítással és a `ar-EG-Hoda` hanggal.            |
| `ar-sa-naayf`                               | A tároló képe a `ar-SA` területi beállítással és a `ar-SA-Naayf` hanggal.           |
| `bg-bg-ivan`                                | A tároló képe a `bg-BG` területi beállítással és a `bg-BG-Ivan` hanggal.            |
| `ca-es-herenarus`                           | A tároló képe a `ca-ES` területi beállítással és a `ca-ES-HerenaRUS` hanggal.       |
| `cs-cz-jakub`                               | A tároló képe a `cs-CZ` területi beállítással és a `cs-CZ-Jakub` hanggal.           |
| `da-dk-hellerus`                            | A tároló képe a `da-DK` területi beállítással és a `da-DK-HelleRUS` hanggal.        |
| `de-at-michael`                             | A tároló képe a `de-AT` területi beállítással és a `de-AT-Michael` hanggal.         |
| `de-ch-karsten`                             | A tároló képe a `de-CH` területi beállítással és a `de-CH-Karsten` hanggal.         |
| `de-de-hedda`                               | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-heddarus`                            | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-stefan-apollo`                       | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal.   |
| `el-gr-stefanos`                            | A tároló képe a `el-GR` területi beállítással és a `el-GR-Stefanos` hanggal.        |
| `en-au-catherine`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal.       |
| `en-au-hayleyrus`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal.       |
| `en-ca-heatherrus`                          | A tároló képe a `en-CA` területi beállítással és a `en-CA-HeatherRUS` hanggal.      |
| `en-ca-linda`                               | A tároló képe a `en-CA` területi beállítással és a `en-CA-Linda` hanggal.           |
| `en-gb-george-apollo`                       | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal.   |
| `en-gb-hazelrus`                            | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal.        |
| `en-gb-susan-apollo`                        | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal.    |
| `en-ie-sean`                                | A tároló képe a `en-IE` területi beállítással és a `en-IE-Sean` hanggal.            |
| `en-in-heera-apollo`                        | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal.    |
| `en-in-priyarus`                            | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal.        |
| `en-in-ravi-apollo`                         | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal.     |
| `en-us-benjaminrus`                         | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal.     |
| `en-us-guy24krus`                           | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal.       |
| `en-us-aria24krus`                          | A tároló képe a `en-US` területi beállítással és a `en-US-Aria24kRUS` hanggal.      |
| `en-us-ariarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-AriaRUS` hanggal.         |
| `en-us-zirarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal.         |
| `es-es-helenarus`                           | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal.       |
| `es-es-laura-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal.    |
| `es-es-pablo-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal.    |
| `es-mx-hildarus`                            | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal.        |
| `es-mx-raul-apollo`                         | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal.     |
| `fi-fi-heidirus`                            | A tároló képe a `fi-FI` területi beállítással és a `fi-FI-HeidiRUS` hanggal.        |
| `fr-ca-caroline`                            | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal.        |
| `fr-ca-harmonierus`                         | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal.     |
| `fr-ch-guillaume`                           | A tároló képe a `fr-CH` területi beállítással és a `fr-CH-Guillaume` hanggal.       |
| `fr-fr-hortenserus`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal.     |
| `fr-fr-julie-apollo`                        | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal.    |
| `fr-fr-paul-apollo`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal.     |
| `he-il-asaf`                                | A tároló képe a `he-IL` területi beállítással és a `he-IL-Asaf` hanggal.            |
| `hi-in-hemant`                              | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Hemant` hanggal.          |
| `hi-in-kalpana-apollo`                      | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana-Apollo` hanggal.  |
| `hi-in-kalpana`                             | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana` hanggal.         |
| `hr-hr-matej`                               | A tároló képe a `hr-HR` területi beállítással és a `hr-HR-Matej` hanggal.           |
| `hu-hu-szabolcs`                            | A tároló képe a `hu-HU` területi beállítással és a `hu-HU-Szabolcs` hanggal.        |
| `id-id-andika`                              | A tároló képe a `id-ID` területi beállítással és a `id-ID-Andika` hanggal.          |
| `it-it-cosimo-apollo`                       | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal.   |
| `it-it-luciarus`                            | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal.        |
| `ja-jp-ayumi-apollo`                        | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal.    |
| `ja-jp-harukarus`                           | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal.       |
| `ja-jp-ichiro-apollo`                       | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal.   |
| `ko-kr-heamirus`                            | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal.        |
| `ms-my-rizwan`                              | A tároló képe a `ms-MY` területi beállítással és a `ms-MY-Rizwan` hanggal.          |
| `nb-no-huldarus`                            | A tároló képe a `nb-NO` területi beállítással és a `nb-NO-HuldaRUS` hanggal.        |
| `nl-nl-hannarus`                            | A tároló képe a `nl-NL` területi beállítással és a `nl-NL-HannaRUS` hanggal.        |
| `pl-pl-paulinarus`                          | A tároló képe a `pl-PL` területi beállítással és a `pl-PL-PaulinaRUS` hanggal.      |
| `pt-br-daniel-apollo`                       | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal.   |
| `pt-br-heloisarus`                          | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal.      |
| `pt-pt-heliarus`                            | A tároló képe a `pt-PT` területi beállítással és a `pt-PT-HeliaRUS` hanggal.        |
| `ro-ro-andrei`                              | A tároló képe a `ro-RO` területi beállítással és a `ro-RO-Andrei` hanggal.          |
| `ru-ru-ekaterinarus`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-EkaterinaRUS` hanggal.    |
| `ru-ru-irina-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Irina-Apollo` hanggal.    |
| `ru-ru-pavel-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Pavel-Apollo` hanggal.    |
| `sk-sk-filip`                               | A tároló képe a `sk-SK` területi beállítással és a `sk-SK-Filip` hanggal.           |
| `sl-si-lado`                                | A tároló képe a `sl-SI` területi beállítással és a `sl-SI-Lado` hanggal.            |
| `sv-se-hedvigrus`                           | A tároló képe a `sv-SE` területi beállítással és a `sv-SE-HedvigRUS` hanggal.       |
| `ta-in-valluvar`                            | A tároló képe a `ta-IN` területi beállítással és a `ta-IN-Valluvar` hanggal.        |
| `te-in-chitra`                              | A tároló képe a `te-IN` területi beállítással és a `te-IN-Chitra` hanggal.          |
| `th-th-pattara`                             | A tároló képe a `th-TH` területi beállítással és a `th-TH-Pattara` hanggal.         |
| `tr-tr-sedarus`                             | A tároló képe a `tr-TR` területi beállítással és a `tr-TR-SedaRUS` hanggal.         |
| `vi-vn-an`                                  | A tároló képe a `vi-VN` területi beállítással és a `vi-VN-An` hanggal.              |
| `zh-cn-huihuirus`                           | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal.       |
| `zh-cn-kangkang-apollo`                     | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `zh-cn-yaoyao-apollo`                       | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal.   |
| `zh-hk-danny-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Danny-Apollo` hanggal.    |
| `zh-hk-tracy-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Tracy-Apollo` hanggal.    |
| `zh-hk-tracyrus`                            | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-TracyRUS` hanggal.        |
| `zh-tw-hanhanrus`                           | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-HanHanRUS` hanggal.       |
| `zh-tw-yating-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Yating-Apollo` hanggal.   |
| `zh-tw-zhiwei-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Zhiwei-Apollo` hanggal.   |

| Területi beállítások a v 1.7.0                          | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `ar-eg-hoda`                                | A tároló képe a `ar-EG` területi beállítással és a `ar-EG-Hoda` hanggal.            |
| `ar-sa-naayf`                               | A tároló képe a `ar-SA` területi beállítással és a `ar-SA-Naayf` hanggal.           |
| `bg-bg-ivan`                                | A tároló képe a `bg-BG` területi beállítással és a `bg-BG-Ivan` hanggal.            |
| `ca-es-herenarus`                           | A tároló képe a `ca-ES` területi beállítással és a `ca-ES-HerenaRUS` hanggal.       |
| `cs-cz-jakub`                               | A tároló képe a `cs-CZ` területi beállítással és a `cs-CZ-Jakub` hanggal.           |
| `da-dk-hellerus`                            | A tároló képe a `da-DK` területi beállítással és a `da-DK-HelleRUS` hanggal.        |
| `de-at-michael`                             | A tároló képe a `de-AT` területi beállítással és a `de-AT-Michael` hanggal.         |
| `de-ch-karsten`                             | A tároló képe a `de-CH` területi beállítással és a `de-CH-Karsten` hanggal.         |
| `de-de-hedda`                               | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-heddarus`                            | A tároló képe a `de-DE` területi beállítással és a `de-DE-Hedda` hanggal.           |
| `de-de-stefan-apollo`                       | A tároló képe a `de-DE` területi beállítással és a `de-DE-Stefan-Apollo` hanggal.   |
| `el-gr-stefanos`                            | A tároló képe a `el-GR` területi beállítással és a `el-GR-Stefanos` hanggal.        |
| `en-au-catherine`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-Catherine` hanggal.       |
| `en-au-hayleyrus`                           | A tároló képe a `en-AU` területi beállítással és a `en-AU-HayleyRUS` hanggal.       |
| `en-ca-heatherrus`                          | A tároló képe a `en-CA` területi beállítással és a `en-CA-HeatherRUS` hanggal.      |
| `en-ca-linda`                               | A tároló képe a `en-CA` területi beállítással és a `en-CA-Linda` hanggal.           |
| `en-gb-george-apollo`                       | A tároló képe a `en-GB` területi beállítással és a `en-GB-George-Apollo` hanggal.   |
| `en-gb-hazelrus`                            | A tároló képe a `en-GB` területi beállítással és a `en-GB-HazelRUS` hanggal.        |
| `en-gb-susan-apollo`                        | A tároló képe a `en-GB` területi beállítással és a `en-GB-Susan-Apollo` hanggal.    |
| `en-ie-sean`                                | A tároló képe a `en-IE` területi beállítással és a `en-IE-Sean` hanggal.            |
| `en-in-heera-apollo`                        | A tároló képe a `en-IN` területi beállítással és a `en-IN-Heera-Apollo` hanggal.    |
| `en-in-priyarus`                            | A tároló képe a `en-IN` területi beállítással és a `en-IN-PriyaRUS` hanggal.        |
| `en-in-ravi-apollo`                         | A tároló képe a `en-IN` területi beállítással és a `en-IN-Ravi-Apollo` hanggal.     |
| `en-us-benjaminrus`                         | A tároló képe a `en-US` területi beállítással és a `en-US-BenjaminRUS` hanggal.     |
| `en-us-guy24krus`                           | A tároló képe a `en-US` területi beállítással és a `en-US-Guy24kRUS` hanggal.       |
| `en-us-aria24krus`                          | A tároló képe a `en-US` területi beállítással és a `en-US-Aria24kRUS` hanggal.      |
| `en-us-ariarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-AriaRUS` hanggal.         |
| `en-us-zirarus`                             | A tároló képe a `en-US` területi beállítással és a `en-US-ZiraRUS` hanggal.         |
| `es-es-helenarus`                           | A tároló képe a `es-ES` területi beállítással és a `es-ES-HelenaRUS` hanggal.       |
| `es-es-laura-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Laura-Apollo` hanggal.    |
| `es-es-pablo-apollo`                        | A tároló képe a `es-ES` területi beállítással és a `es-ES-Pablo-Apollo` hanggal.    |
| `es-mx-hildarus`                            | A tároló képe a `es-MX` területi beállítással és a `es-MX-HildaRUS` hanggal.        |
| `es-mx-raul-apollo`                         | A tároló képe a `es-MX` területi beállítással és a `es-MX-Raul-Apollo` hanggal.     |
| `fi-fi-heidirus`                            | A tároló képe a `fi-FI` területi beállítással és a `fi-FI-HeidiRUS` hanggal.        |
| `fr-ca-caroline`                            | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-Caroline` hanggal.        |
| `fr-ca-harmonierus`                         | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-HarmonieRUS` hanggal.     |
| `fr-ch-guillaume`                           | A tároló képe a `fr-CH` területi beállítással és a `fr-CH-Guillaume` hanggal.       |
| `fr-fr-hortenserus`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-HortenseRUS` hanggal.     |
| `fr-fr-julie-apollo`                        | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Julie-Apollo` hanggal.    |
| `fr-fr-paul-apollo`                         | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-Paul-Apollo` hanggal.     |
| `he-il-asaf`                                | A tároló képe a `he-IL` területi beállítással és a `he-IL-Asaf` hanggal.            |
| `hi-in-hemant`                              | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Hemant` hanggal.          |
| `hi-in-kalpana-apollo`                      | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana-Apollo` hanggal.  |
| `hi-in-kalpana`                             | A tároló képe a `hi-IN` területi beállítással és a `hi-IN-Kalpana` hanggal.         |
| `hr-hr-matej`                               | A tároló képe a `hr-HR` területi beállítással és a `hr-HR-Matej` hanggal.           |
| `hu-hu-szabolcs`                            | A tároló képe a `hu-HU` területi beállítással és a `hu-HU-Szabolcs` hanggal.        |
| `id-id-andika`                              | A tároló képe a `id-ID` területi beállítással és a `id-ID-Andika` hanggal.          |
| `it-it-cosimo-apollo`                       | A tároló képe a `it-IT` területi beállítással és a `it-IT-Cosimo-Apollo` hanggal.   |
| `it-it-luciarus`                            | A tároló képe a `it-IT` területi beállítással és a `it-IT-LuciaRUS` hanggal.        |
| `ja-jp-ayumi-apollo`                        | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ayumi-Apollo` hanggal.    |
| `ja-jp-harukarus`                           | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-HarukaRUS` hanggal.       |
| `ja-jp-ichiro-apollo`                       | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-Ichiro-Apollo` hanggal.   |
| `ko-kr-heamirus`                            | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-HeamiRUS` hanggal.        |
| `ms-my-rizwan`                              | A tároló képe a `ms-MY` területi beállítással és a `ms-MY-Rizwan` hanggal.          |
| `nb-no-huldarus`                            | A tároló képe a `nb-NO` területi beállítással és a `nb-NO-HuldaRUS` hanggal.        |
| `nl-nl-hannarus`                            | A tároló képe a `nl-NL` területi beállítással és a `nl-NL-HannaRUS` hanggal.        |
| `pl-pl-paulinarus`                          | A tároló képe a `pl-PL` területi beállítással és a `pl-PL-PaulinaRUS` hanggal.      |
| `pt-br-daniel-apollo`                       | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-Daniel-Apollo` hanggal.   |
| `pt-br-heloisarus`                          | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-HeloisaRUS` hanggal.      |
| `pt-pt-heliarus`                            | A tároló képe a `pt-PT` területi beállítással és a `pt-PT-HeliaRUS` hanggal.        |
| `ro-ro-andrei`                              | A tároló képe a `ro-RO` területi beállítással és a `ro-RO-Andrei` hanggal.          |
| `ru-ru-ekaterinarus`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-EkaterinaRUS` hanggal.    |
| `ru-ru-irina-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Irina-Apollo` hanggal.    |
| `ru-ru-pavel-apollo`                        | A tároló képe a `ru-RU` területi beállítással és a `ru-RU-Pavel-Apollo` hanggal.    |
| `sk-sk-filip`                               | A tároló képe a `sk-SK` területi beállítással és a `sk-SK-Filip` hanggal.           |
| `sl-si-lado`                                | A tároló képe a `sl-SI` területi beállítással és a `sl-SI-Lado` hanggal.            |
| `sv-se-hedvigrus`                           | A tároló képe a `sv-SE` területi beállítással és a `sv-SE-HedvigRUS` hanggal.       |
| `ta-in-valluvar`                            | A tároló képe a `ta-IN` területi beállítással és a `ta-IN-Valluvar` hanggal.        |
| `te-in-chitra`                              | A tároló képe a `te-IN` területi beállítással és a `te-IN-Chitra` hanggal.          |
| `th-th-pattara`                             | A tároló képe a `th-TH` területi beállítással és a `th-TH-Pattara` hanggal.         |
| `tr-tr-sedarus`                             | A tároló képe a `tr-TR` területi beállítással és a `tr-TR-SedaRUS` hanggal.         |
| `vi-vn-an`                                  | A tároló képe a `vi-VN` területi beállítással és a `vi-VN-An` hanggal.              |
| `zh-cn-huihuirus`                           | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-HuihuiRUS` hanggal.       |
| `zh-cn-kangkang-apollo`                     | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Kangkang-Apollo` hanggal. |
| `zh-cn-yaoyao-apollo`                       | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-Yaoyao-Apollo` hanggal.   |
| `zh-hk-danny-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Danny-Apollo` hanggal.    |
| `zh-hk-tracy-apollo`                        | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-Tracy-Apollo` hanggal.    |
| `zh-hk-tracyrus`                            | A tároló képe a `zh-HK` területi beállítással és a `zh-HK-TracyRUS` hanggal.        |
| `zh-tw-hanhanrus`                           | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-HanHanRUS` hanggal.       |
| `zh-tw-yating-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Yating-Apollo` hanggal.   |
| `zh-tw-zhiwei-apollo`                       | A tároló képe a `zh-TW` területi beállítással és a `zh-TW-Zhiwei-Apollo` hanggal.   |

---

## <a name="neural-text-to-speech"></a>Neurális szöveg – beszéd

A [neurális szöveg-beszéd][sp-ntts] tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `neural-text-to-speech` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/neural-text-to-speech` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/neural-text-to-speech/tags/list).


# <a name="latest-version"></a>[Legújabb verzió](#tab/current)

Kibocsátási megjegyzések a következőhöz `v1.3.0` :
* A neurális szöveg-beszéd tároló már általánosan elérhető. 

| Képcímkék                                  | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `1.3.0-amd64-<locale-and-voice>`    | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.3.0-amd64-en-us-arianeural`. |


| v 1.3.0 területi beállítások és hangok           | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `de-de-katjaneural`                 | A tároló képe a `de-DE` területi beállítással és a `de-DE-KatjaNeural` hanggal.     |
| `en-au-natashaneural`               | A tároló képe a `en-AU` területi beállítással és a `en-AU-NatashaNeural` hanggal.   |
| `en-ca-claraneural`                 | A tároló képe a `en-CA` területi beállítással és a `en-CA-ClaraNeural` hanggal.     |
| `en-gb-libbyneural`                 | A tároló képe a `en-GB` területi beállítással és a `en-GB-LibbyNeural` hanggal.     |
| `en-gb-mianeural`                   | A tároló képe a `en-GB` területi beállítással és a `en-GB-MiaNeural` hanggal.       |
| `en-us-arianeural`                  | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `en-us-guyneural`                   | A tároló képe a `en-US` területi beállítással és a `en-US-GuyNeural` hanggal.       |
| `es-es-elviraneural`                | A tároló képe a `es-ES` területi beállítással és a `es-ES-ElviraNeural` hanggal.    |
| `es-mx-dalianeural`                 | A tároló képe a `es-MX` területi beállítással és a `es-MX-DaliaNeural` hanggal.     |
| `fr-ca-sylvieneural`                | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-SylvieNeural` hanggal.    |
| `fr-fr-deniseneural`                | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-DeniseNeural` hanggal.    |
| `it-it-elsaneural`                  | A tároló képe a `it-IT` területi beállítással és a `it-IT-ElsaNeural` hanggal.      |
| `ja-jp-nanamineural`                | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-NanamiNeural` hanggal.    |
| `ko-kr-sunhineural`                 | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-SunHiNeural` hanggal.     |
| `pt-br-franciscaneural`             | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-FranciscaNeural` hanggal. |
| `zh-cn-xiaoxiaoneural`              | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-XiaoxiaoNeural` hanggal.  |

# <a name="previous-version"></a>[Előző verzió](#tab/previous)

| Képcímkék                                  | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `1.2.0-amd64-<locale-and-voice>-preview`    | Cserélje le az értékét `<locale>` az alább felsorolt elérhető területi beállítások egyikére. Például: `1.2.0-amd64-en-us-arianeural-preview`. |


| a v 1.2.0 előzetes verziójának területi beállítása és Voices           | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `de-de-katjaneural-preview`                 | A tároló képe a `de-DE` területi beállítással és a `de-DE-KatjaNeural` hanggal.     |
| `en-au-natashaneural-preview`               | A tároló képe a `en-AU` területi beállítással és a `en-AU-NatashaNeural` hanggal.   |
| `en-ca-claraneural-preview`                 | A tároló képe a `en-CA` területi beállítással és a `en-CA-ClaraNeural` hanggal.     |
| `en-gb-libbyneural-preview`                 | A tároló képe a `en-GB` területi beállítással és a `en-GB-LibbyNeural` hanggal.     |
| `en-gb-mianeural-preview`                   | A tároló képe a `en-GB` területi beállítással és a `en-GB-MiaNeural` hanggal.       |
| `en-us-arianeural-preview`                  | A tároló képe a `en-US` területi beállítással és a `en-US-AriaNeural` hanggal.      |
| `en-us-guyneural-preview`                   | A tároló képe a `en-US` területi beállítással és a `en-US-GuyNeural` hanggal.       |
| `es-es-elviraneural-preview`                | A tároló képe a `es-ES` területi beállítással és a `es-ES-ElviraNeural` hanggal.    |
| `es-mx-dalianeural-preview`                 | A tároló képe a `es-MX` területi beállítással és a `es-MX-DaliaNeural` hanggal.     |
| `fr-ca-sylvieneural-preview`                | A tároló képe a `fr-CA` területi beállítással és a `fr-CA-SylvieNeural` hanggal.    |
| `fr-fr-deniseneural-preview`                | A tároló képe a `fr-FR` területi beállítással és a `fr-FR-DeniseNeural` hanggal.    |
| `it-it-elsaneural-preview`                  | A tároló képe a `it-IT` területi beállítással és a `it-IT-ElsaNeural` hanggal.      |
| `ja-jp-nanamineural-preview`                | A tároló képe a `ja-JP` területi beállítással és a `ja-JP-NanamiNeural` hanggal.    |
| `ko-kr-sunhineural-preview`                 | A tároló képe a `ko-KR` területi beállítással és a `ko-KR-SunHiNeural` hanggal.     |
| `pt-br-franciscaneural-preview`             | A tároló képe a `pt-BR` területi beállítással és a `pt-BR-FranciscaNeural` hanggal. |
| `zh-cn-xiaoxiaoneural-preview`              | A tároló képe a `zh-CN` területi beállítással és a `zh-CN-XiaoxiaoNeural` hanggal.  |

---

## <a name="speech-language-detection"></a>Beszéd nyelvének felismerése

A [beszédfelismerési nyelvi észlelési][sp-lid] tároló képe a `mcr.microsoft.com` Container Registry szindikátusban található. A `azure-cognitive-services/speechservices/` tárházban található, és neve `language-detection` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/speechservices/language-detection` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/speechservices/language-detection/tags/list).

| Képcímkék                                  | Jegyzetek                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                       |      |
| `1.1.0-amd64-preview`                       |      |

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

a tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/textanalytics/` tárházban található, és neve `keyphrase` . A teljes tároló rendszerképének neve: `mcr.microsoft.com/azure-cognitive-services/textanalytics/keyphrase` .

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/keyphrase/tags/list).

# <a name="latest-version"></a>[Legújabb verzió](#tab/current)


| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` |       |

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `1.1.012840001-amd64` |       |
| `1.1.012830001-amd64`    |       |

---

## <a name="text-language-detection"></a>Szöveg nyelvének felismerése

Az [nyelvfelismerés][ta-la] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/textanalytics/` tárházban található, és neve `language` . A teljes tároló rendszerképének neve a (z), `mcr.microsoft.com/azure-cognitive-services/textanalytics/language`


Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/language/tags/list).

# <a name="latest-versions"></a>[Legújabb verziók](#tab/current)

| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.013570001-amd64` | |
   

# <a name="previous-versions"></a>[Korábbi verziók](#tab/previous)


| Képcímkék                    | Jegyzetek |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.012840001-amd64` |   |
| `1.1.012830001-amd64` |   |

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

Az [Hangulatelemzés][ta-se] -tároló rendszerképe megtalálható a `mcr.microsoft.com` Container Registry szindikátusban. A `azure-cognitive-services/textanalytics/` tárházban található, és neve `sentiment` . A teljes tároló rendszerképének neve a (z), `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment`

Ehhez a tároló-rendszerképhez a következő címkék érhetők el. Megtalálhatja a címkék teljes listáját is a [MCR](https://mcr.microsoft.com/v2/azure-cognitive-services/textanalytics/sentiment/tags/list).

| Képcímkék | Jegyzetek                                         |
|------------|:----------------------------------------------|
| `latest`   |                                               |
| `3.0-en`   | Hangulatelemzés v3 (angol)               |
| `3.0-es`   | Hangulatelemzés v3 (spanyol)               |
| `3.0-fr`   | Hangulatelemzés v3 (francia)                |
| `3.0-it`   | Hangulatelemzés v3 (olasz)               |
| `3.0-de`   | Hangulatelemzés v3 (német)                |
| `3.0-zh`   | Hangulatelemzés v3 (kínai – egyszerűsített)  |
| `3.0-zht`  | Hangulatelemzés v3 (kínai – hagyományos) |
| `3.0-ja`   | Hangulatelemzés v3 (Japán)              |
| `3.0-pt`   | Hangulatelemzés v3 (portugál)            |
| `3.0-nl`   | Hangulatelemzés v3 (holland)                 |
| `2.1`    | Hangulatelemzés v2      |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[sp-ntts]: ../speech-service/speech-container-howto.md?tabs=ntts
[sp-lid]: ../speech-service/speech-container-howto.md?tabs=lid
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
