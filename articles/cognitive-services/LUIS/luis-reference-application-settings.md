---
title: Alkalmazásbeállítások – LUIS
titleSuffix: Azure Cognitive Services
description: Ismerje meg az alkalmazások beállításait a Language Understanding alkalmazásokhoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 7dec738fca6991cbcbd822c192b96bf6b1cc6d87
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563505"
---
# <a name="application-settings"></a>Alkalmazásbeállítások

Ezeket az Alkalmazásbeállítások az [exportált](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) alkalmazásban tárolódnak, és a REST API-kkal [frissülnek](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) . Az alkalmazás verziójának beállításainak módosítása visszaállítja az alkalmazás betanítási állapotát a képzetlen értékre.

|Beállítás|Alapértelmezett érték|Megjegyzések|
|--|--|--|
|NormalizePunctuation|True|Eltávolítja a központozást.|
|NormalizeDiacritics|True|Eltávolítja a mellékjeleket.|

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

Az alábbi hosszúságú kimondott szöveg bemutatják, hogyan befolyásolják a Mellékjelek a hosszúságú kimondott szöveg:

|Ha a Mellékjelek hamis értékre vannak állítva|Ha a Mellékjelek értéke TRUE (igaz)|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Írásjel eltávolítva

A következő írásjelek törlődnek `NormalizePunctuation` a tulajdonság értéke TRUE (igaz).

|Absztrakt|
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
