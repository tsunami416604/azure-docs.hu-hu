---
title: Alkalmazásbeállítások
titleSuffix: Azure Cognitive Services
description: A Language understanding apps alkalmazások beállítások ismertetése.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 3682b9e0c38344be1522440290b46f8c10bd5607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275905"
---
# <a name="application-settings"></a>Alkalmazásbeállítások

Ezeket az alkalmazásbeállításokat vannak tárolva a [exportált](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) alkalmazást és [frissített](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) a REST API-kkal. Az alkalmazásbeállítások verzió módosítása visszaállítja az alkalmazás képzési állapota kellő.

|Beállítás|Alapértelmezett érték|Megjegyzések|
|--|--|--|
|NormalizePunctuation|True|Eltávolítja az absztrakt.|
|NormalizeDiacritics|True|Eltávolítja az e mellékjeleket.|

## <a name="diacritics-normalization"></a>E mellékjeleket normalizálási 

Az intelligens HANGFELISMERÉSI JSON alkalmazás fájlhoz mellékjeleket az utterance (kifejezés) normalizálási bekapcsolása a `settings` paraméter.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

A következő utterances bemutatják, milyen hatással van a mellékjeleket normalizálási kimondott szöveg:

|"False" értékűre mellékjeleket|Az igaz értékre állítva mellékjeleket|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>E mellékjeleket nyelvi támogatás

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>Brazíliai portugál nyelveken `pt-br` mellékjeleket

|E mellékjeleket "false" értékűre.|E mellékjeleket beállítása igaz értékre|
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

#### <a name="dutch-nl-nl-diacritics"></a>Holland `nl-nl` mellékjeleket

|E mellékjeleket "false" értékűre.|E mellékjeleket beállítása igaz értékre|
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

#### <a name="french-fr--diacritics"></a>Francia `fr-` mellékjeleket

Ez magában foglalja a francia és a kanadai tápfolyadékok cseréjét.

|E mellékjeleket "false" értékűre.|E mellékjeleket beállítása igaz értékre|
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

#### <a name="german-de-de-diacritics"></a>Német `de-de` mellékjeleket

|E mellékjeleket "false" értékűre.|E mellékjeleket beállítása igaz értékre|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>Olasz `it-it` mellékjeleket

|E mellékjeleket "false" értékűre.|E mellékjeleket beállítása igaz értékre|
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

#### <a name="spanish-es--diacritics"></a>Spanyol `es-` mellékjeleket

Ez magában foglalja, spanyol és a kanadai mexikói.

|E mellékjeleket "false" értékűre.|E mellékjeleket beállítása igaz értékre|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>Absztrakt normalizálási

Kapcsolja be az utterance (kifejezés) normalizálási írásjelek, az intelligens HANGFELISMERÉSI JSON alkalmazás fájlhoz tartozó a `settings` paraméter.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

A következő utterances bemutatják, milyen hatással van a mellékjeleket kimondott szöveg:

|"False" értékűre mellékjeleket|Mellékjeleket igaz értékre van állítva|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>Írásjelek

Az alábbi írásjelek távolítja el `NormalizePunctuation` értéke igaz.

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
