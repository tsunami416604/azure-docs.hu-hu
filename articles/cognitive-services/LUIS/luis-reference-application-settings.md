---
title: Alkalmazásbeállítások - LUIS
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services nyelvtudási alkalmazásai alkalmazásbeállításait az alkalmazás és a portál tárolja.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270806"
---
# <a name="application-settings"></a>Alkalmazásbeállítások

Ezeket az alkalmazásbeállításokat az [exportált](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) alkalmazás tárolja, és [frissíti](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) a REST API-kkal. Az alkalmazás verzióbeállításainak módosítása visszaállítja az alkalmazás betanítási állapotát képzetlenre.

|Beállítás|Alapértelmezett érték|Megjegyzések|
|--|--|--|
|Normalizálás: Pontosság|True (Igaz)|Eltávolítja az írásjeleket.|
|A jelek normalizálása|True (Igaz)|Eltávolítja az ékezőket.|

## <a name="diacritics-normalization"></a>Mellékjelek normalizálása

Kapcsolja be az utterance (kifejezés normalizálása a mellékjelek a LUIS JSON-alkalmazásfájl a `settings` paraméterben) mellékjelek bekapcsolása.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

A következő kimondott szöveg azt mutatja be, hogy a mellékkritikusok normalizálása hogyan befolyásolja az utterances:

|Hamis ra van állítva a mellékjelek|A mellékjelek értéke igaz|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Nyelvi támogatás a mellékjelekhez

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazil `pt-br` portugál mellékjelek

|A mellékjelek értéke hamis|A mellékjelek értéke igaz|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>Holland `nl-nl` mellékjelek

|A mellékjelek értéke hamis|A mellékjelek értéke igaz|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>Francia `fr-` mellékjelek

Ez magában foglalja mind a francia, mind a kanadai szubkultúrákat.

|A mellékjelek értéke hamis|A mellékjelek értéke igaz|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>Német `de-de` mellékjelek

|A mellékjelek értéke hamis|A mellékjelek értéke igaz|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Olasz `it-it` mellékjelek

|A mellékjelek értéke hamis|A mellékjelek értéke igaz|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>Spanyol `es-` mellékjelek

Ez magában foglalja mind a spanyol és a kanadai mexikói.

|A mellékjelek értéke hamis|A mellékjelek értéke igaz|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Írásjelek normalizálása

Kapcsolja be az utterance (kifejezés normalizálása) írásjelek `settings` a LUIS JSON alkalmazásfájl a paraméterben.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

A következő kimondott szöveg azt mutatja be, hogy az írásjelek hogyan befolyásolják az utterances:

|Hamis írásjelekre van állítva|Igaz értékre állított írásjelek|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Írásjelek eltávolítva

A következő írásjelek et `NormalizePunctuation` a rendszer eltávolítja, és a beállítás igaz.

|Írásjelek|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
