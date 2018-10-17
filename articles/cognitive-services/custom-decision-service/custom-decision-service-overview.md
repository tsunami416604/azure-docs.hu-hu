---
title: Mi a Custom Decision Service?
titlesuffix: Azure Cognitive Services
description: A cikk áttekintést nyújt a Custom Decision szolgáltatásról.
services: cognitive-services
author: alekh
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-decision-service
ms.topic: overview
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: 273f2965a0fcaaa729175c5232da1aba69589eec
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364210"
---
# <a name="what-is-custom-decision-service"></a>Mi a Custom Decision Service?

A szokásos webes vagy mobilalkalmazásban a kezdőlapon több cikkre vagy más típusú tartalomra mutató hivatkozás van. Amikor az első oldal töltődik, megkérhetné a Custom Decision szolgáltatást, hogy rangsorolja a kezdőlapon szereplő cikkeket. Így amikor a felhasználó rákattintva kiválaszt egy cikket, lehetne küldeni a Custom Decision szolgáltatásnak egy másik kérést, ami feljegyezné a felhasználói döntés eredményét.

A Custom Decision szolgáltatás használata könnyű, csak az kell hozzá, hogy legyen RSS hírcsatorna a tartalomhoz és néhány sor JavaScriptet kell az alkalmazásba belevenni.

A Custom Decision szolgáltatás a gépi tanulási funkciókhoz alakítja át a tartalmat. A rendszer a funkciókat a tartalom megértésére használja, ami a szöveget, képeket, videókat és általános hangulatot illeti. Használ még több más [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) is, pl. [Entitáskapcsolás](../entitylinking/home.md), [Text Analytics](../text-analytics/overview.md), [Emotion](../emotion/home.md), és [Computer Vision](../computer-vision/home.md).

A Custom Decision szolgáltatás néhány gyakori-használati esete a következő:

* Cikkek személyre szabása újság webhelyeken
* Videó tartalom személyre szabása média portálon
* Reklám elhelyezés vagy a weblap optimalizálása, ahova a reklám mutat
* Ajánlott tételek rangsorolása áruház webhelyeken.

A Custom Decision Service jelenleg *ingyenes nyilvános előzetes verzió*. Személyre szabhatja a cikkek listáját a webhelyen vagy az alkalmazásban. A kivonási funkció legjobban angol nyelvű tartalomnál működik. Egyéb nyelvekre, például spanyol, francia, német, portugál és japán esetében [korlátozott funkciókat](../text-analytics/overview.md) nyújtunk. Ha új funkciók válnak elérhetővé, a dokumentációt frissíteni fogjuk.

A Custom Decision Service nem csak a tartalom személyre szabás területét használó alkalmazásokban használható. Az alkalmazások nagyon jól megfelelhetnek egyedi villámnézetre. További információkért [vegye fel velünk a kapcsolatot](https://azure.microsoft.com/overview/sales-number/).

## <a name="api-usage-modes"></a>API-használati módok

A Custom Decision Service weblapokon és mobilalkalmazásokban is használható. Az API meghívható a böngészőből és alkalmazásból is. Az API használata mindkét esetben hasonló, de van néhány különbség.

## <a name="glossary-of-terms"></a>Kifejezések jegyzéke

A dokumentációban van több gyakran előforduló kifejezés is:

* **Művelet halmaz**: A tartalom elemek halmaza, amely a Custom Decision Service rangsorolására vár. A halmaz megadása történhet *RSS* vagy *Atom* végpont formában.
* **Rangsorolás**: A Custom Decision szolgáltatásnak küldött minden kérés megad egy vagy több művelethalmazt. A rendszer veszi valamennyi tartalom elemet és válaszában rangsorolva küldi vissza őket.
* **Visszahívási függvény**: Ez az ön által megadott függvény jeleníti meg a felhasználói felületen a tartalmat. A tartalom a Custom Decision Service által visszaadott rangsor alapján van rendezve.
* **Jutalom**: Azt méri, hogyan reagált a felhasználó a megjelenített tartalomra. A Custom Decision a felhasználói válaszokat a kattintások felhasználásával méri. A kattintásokról a rendszer az alkalmazásba illesztett egyedi kód használatával kap értesítést.

## <a name="next-steps"></a>További lépések

* [Alkalmazás regisztrálása](custom-decision-service-get-started-register.md) a Custom Decision szolgáltatással
* Első lépések a [weblap](custom-decision-service-get-started-browser.md) vagy [okostelefon alkalmazás](custom-decision-service-get-started-app.md) optimalizálásához.
* Az [API-referencia](custom-decision-service-api-reference.md) még további információkat tartalmaz a nyújtott funkciókról.