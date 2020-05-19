---
title: fájl belefoglalása
ms.topic: include
ms.custom: include file
ms.date: 5/04/2020
ms.openlocfilehash: 819dfa3127eb91a2f08687a76ea0586439b650a4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590986"
---
|Szint|Felhasználói felület beállításai|API-beállítás|Információ|
|--|--|--|--|
|Alkalmazás|Végpontok nyilvánosvé tétele|`Public`|Bárki hozzáférhet a nyilvános alkalmazáshoz, ha rendelkezik előrejelzési kulccsal, és ismeri az alkalmazás AZONOSÍTÓját. |
|Verzió|Nem determinisztikus képzés használata|`UseAllTrainingData`|A képzések a negatív mintavételezés kis hányadát használják. Ha a kis negatív mintavételezés helyett az összes adatát szeretné használni, állítsa a következőre: `true` . |
|Verzió|Mellékjelek normalizálása|`NormalizeDiacritics`|A Mellékjelek normalizálása a hosszúságú kimondott szöveg lévő mellékjeleket normál karakterekkel helyettesíti.|
|Verzió|Központozás normalizálása|`NormalizePunctuation`|A központozás normalizálása azt jelenti, hogy a modellek betanítása előtt és a végponti lekérdezések megkezdése előtt az írásjelek el lesznek távolítva a hosszúságú kimondott szöveg.|
|Verzió|Word-űrlapok normalizálása|`NormalizeWordForm`|A gyökéren túli Word-űrlapok figyelmen kívül hagyása.|

Ismerje meg a normalizálás [fogalmait](../luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) , és az [alkalmazás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58aeface39e2bb03dcd5909e) és a [verzió](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API-k használatával frissítse ezeket a beállításokat, vagy használja a Luis-portál **felügyelet** szakaszát, az **Alkalmazásbeállítások** lapot.