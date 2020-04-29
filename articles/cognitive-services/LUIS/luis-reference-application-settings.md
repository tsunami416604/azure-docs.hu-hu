---
title: Alkalmazásbeállítások – LUIS
description: Az Azure Cognitive Services Language Understanding-alkalmazások beállításai az alkalmazásban és a portálon vannak tárolva.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81382202"
---
# <a name="application-settings"></a>Alkalmazásbeállítások

Ezeket az Alkalmazásbeállítások az [exportált](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) alkalmazásban tárolódnak, és a REST API-kkal [frissülnek](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) . Az alkalmazás verziójának beállításainak módosítása visszaállítja az alkalmazás betanítási állapotát a képzetlen értékre.

A Mellékjelek és a központozás [fogalmának](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) megismerése.

|Beállítás|Alapértelmezett érték|Megjegyzések|
|--|--|--|
|NormalizePunctuation|True (Igaz)|Eltávolítja a központozást.|
|NormalizeDiacritics|True (Igaz)|Eltávolítja a mellékjeleket.|

## <a name="diacritics-normalization"></a>Mellékjelek normalizálása

A `settings` paraméterben kapcsolja be a mellékjeleket a Luis JSON-alkalmazás fájljába.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

Az alábbi hosszúságú kimondott szöveg bemutatják, hogyan befolyásolják a Mellékjelek a hosszúságú kimondott szöveg:

|Ha a Mellékjelek hamis értékre vannak állítva|Ha a Mellékjelek értéke TRUE (igaz)|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>Mellékjelek nyelvi támogatása

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazíliai portugál `pt-br` mellékjelek

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
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

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
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

Ide tartozik a francia és a kanadai alkultúra is.

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
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

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>Olasz `it-it` mellékjelek

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
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

Ide tartozik a spanyol és a kanadai mexikói is.

|Hamis értékre beállított mellékjelek|Igaz értékre beállított mellékjelek|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Központozás normalizálása

A `settings` (z) paraméterben bekapcsolhatja az írásjelek kikapcsolását a Luis JSON-alkalmazás fájljába.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

A következő hosszúságú kimondott szöveg szemléltetik, hogy a központozás milyen hatással van a hosszúságú kimondott szöveg:

|Az írásjel hamis értékre van állítva|A központozás igaz értékre van állítva|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Írásjel eltávolítva

A következő írásjelek törlődnek `NormalizePunctuation` a tulajdonság értéke TRUE (igaz).

|Központozási|
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

## <a name="next-steps"></a>További lépések

* A Mellékjelek és a központozás [fogalmának](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) megismerése.
