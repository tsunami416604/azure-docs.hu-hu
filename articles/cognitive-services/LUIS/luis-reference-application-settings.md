---
title: Alkalmazásbeállítások - LUIS
description: Az Azure Cognitive Services nyelvtudási alkalmazásai alkalmazásbeállításait az alkalmazás és a portál tárolja.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382202"
---
# <a name="application-settings"></a>Alkalmazásbeállítások

Ezeket az alkalmazásbeállításokat az [exportált](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) alkalmazás tárolja, és [frissíti](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) a REST API-kkal. Az alkalmazás verzióbeállításainak módosítása visszaállítja az alkalmazás betanítási állapotát képzetlenre.

Ismerje meg a mellékjelek és írásjelek [fogalmait.](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)

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

## <a name="next-steps"></a>További lépések

* Ismerje meg a mellékjelek és írásjelek [fogalmait.](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation)
