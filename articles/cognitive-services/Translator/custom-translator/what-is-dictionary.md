---
title: Mi az a szótár? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: A szótár egy igazított dokumentum, amely megadja a mondatok vagy mondatok (és azok fordításai) listáját, amelyeket mindig a Microsoft Translatornek kell lefordítani. A szótárakat más néven szószedeteknek vagy terminusoknak is nevezik.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 5103526956b5041771a1d8e4abb5e8800b971059
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595384"
---
# <a name="what-is-a-dictionary"></a>Mi az a szótár?

A szótár olyan igazított pár dokumentum, amely a mondatok vagy mondatok listáját és a hozzájuk tartozó fordításokat határozza meg. Használjon szótárt a képzésben, ha azt szeretné, hogy a Microsoft Translator mindig lefordítsa a forrás kifejezés vagy mondat bármely példányát a szótárban megadott fordítás használatával. A szótárakat más néven szószedeteknek vagy kifejezéseknek nevezzük. A szótárt a lista összes feltételének "másolás és csere" kifejezésével lehet meggondolni.

A szótárak csak olyan nyelvi párokban működő projektekhez működnek, amelyek teljes mértékben támogatottak a Microsoft neurális gépi fordítási (NMT) rendszer mögött. [Tekintse meg a nyelvek teljes listáját](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization).

## <a name="phrase-dictionary"></a>Szótár kifejezése
Ha egy kifejezés szótárt is tartalmaz a modell tanításához, a felsorolt szavak vagy kifejezések a megadott módon vannak lefordítva. A mondat többi része a szokásos módon lesz lefordítva. A kifejezéseket tartalmazó szótár segítségével megadhatja azokat a kifejezéseket, amelyek nem fordíthatók le a szótárban található forrás-és célfájl azonos fordított kifejezésének megadásával.

## <a name="sentence-dictionary"></a>Mondat szótár
A mondat szótár lehetővé teszi, hogy pontos cél fordítást határozzon meg a forrás mondathoz. A mondatok szótárának egyeztetéséhez a teljes elküldött mondatnak meg kell egyeznie a forrás szótár bejegyzésével.  Ha a mondatnak csak egy része egyezik, a bejegyzés nem egyezik.  Ha a rendszer egyezést észlel, a rendszer visszaadja a mondathoz tartozó szótár cél bejegyzését.

## <a name="dictionary-only-trainings"></a>Csak szótári képzések
A modelleket csak a szótárak adatai alapján lehet betanítani. Ehhez válassza ki a használni kívánt szótári dokumentumot (vagy több szótárt tartalmazó dokumentumot), majd koppintson a modell létrehozása lehetőségre. Mivel ez egy csak szótárban bekövetkező képzés, nem szükségesek minimális számú tanítási mondat. A modell általában sokkal gyorsabb képzést tesz lehetővé, mint a szokásos képzések.  Az eredményül kapott modellek a Microsoft alapmodelleket használják a fordításhoz a hozzáadott szótárak hozzáadásával.  Nem fog teszt jelentést kapni.

>[!Note]
>Az egyéni fordító nem mondattal igazítja a szótárakat, ezért fontos, hogy a szótár dokumentumaiban azonos számú forrás-és cél-kifejezés/mondat legyen, és hogy pontosan legyenek igazítva.

## <a name="recommendations"></a>Javaslatok

- A szótárak nem helyettesíthetik a betanított modellt a betanítási adattal.  A szótárak alapvetően szavakat vagy mondatokat találnak és cserélnek.  Ha úgy dönt, hogy a rendszer a teljes mondatok alapján tanulja meg a képzési anyagokat, általában jobb választás, mint a szótár használata.
- A kifejezés szótárát takarékosan kell használni. Ha a mondaton belüli kifejezést lecserélik, a mondaton belüli környezet elveszett vagy korlátozott a mondat hátralévő részének lefordítása érdekében. Ennek az az oka, hogy a mondaton belüli kifejezés vagy szó a kifejezés szótárának megfelelően lesz lefordítva, a mondat teljes fordítási minősége gyakran fog szenvedni.
- A kifejezés szótára jól működik az olyan összetett nevek esetében, mint például a Terméknév ("Microsoft SQL Server"), a megfelelő nevek ("Hamburg városa") vagy a termék funkciói ("pivot Table"). Nem működik együtt a műveletekhez és a melléknevekhez, mert ezek általában erősen ragozott a forrásban vagy a célként megadott nyelven. Kerülje a szótár bejegyzéseinek kifejezését az összetett nevekhez.
- Ha szótárt használ, a fordításban a kihasználás és a központozás a megcélzott fájlban megadott tőkésítés és írásjelek alapján jelenik meg. A rendszer figyelmen kívül hagyja a nagybetűket és a központozást, ha a bemeneti mondat és a szótárban lévő mondatok közötti egyezések azonosítására tesz kísérletet. Tegyük fel például, hogy egy angolról a spanyol rendszerre tanította a "Hamburg városa" nevű szótárt a forrásfájlban, a "Ciudad de Hamburg" kifejezést pedig a célfájl "a" fájljában. Ha egy olyan mondat fordítását kértem, amely tartalmazza a "Hamburg városa" kifejezést, akkor a "Hamburg városa" kifejezés megfelel a "Hamburg városa" bejegyzéshez tartozó "" Ciudad de Hamburg "szövegnek, és a végső fordításban a" Ciudad de Hamburg "értékre lesz leképezve.
- Ha egy szó többször is megjelenik egy szótárban, a rendszer mindig a megadott utolsó bejegyzést fogja használni. A szótár nem tartalmazhat ugyanazon szó több fordítását.

## <a name="next-steps"></a>További lépések

- Olvassa el [a dokumentumok formátumait ismertető útmutatót](document-formats-naming-convention.md).
