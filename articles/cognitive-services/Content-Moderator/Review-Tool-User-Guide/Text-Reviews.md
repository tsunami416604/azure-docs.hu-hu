---
title: Az Azure Content Moderator – a Content Moderator felülvizsgálati szöveg
description: Megtudhatja, hogyan tekintse át a szöveg a Content Moderator, a pontszám és címkék észlelt. Kiszűrni az adatokat használja, hogy megfelelő-e tartalmat.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 005e6ed853cbc6013d74e7ff479097dcbb0a1043
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259563"
---
# <a name="let-human-reviewers-review-text"></a>Emberi véleményezők szöveg felülvizsgálata

Tekintse át a szöveges értékeket használatával az Azure Content Moderator használható, és címkék észlelt. Az információ használatával határozhatja meg, hogy-e megfelelő tartalom. 

## <a name="select-or-enter-the-text-to-review"></a>Válassza ki vagy írja be a szöveget áttekintése

A Content Moderator, válassza ki a **próbálja** fülre. Ezután válassza ki a **szöveg** szöveg moderálása kezdőképernyőjén nyissa meg a beállítást. Adja meg a szöveg, vagy küldje el az alapértelmezett mintaszöveget automatizált szövegmoderálás számára. Megadhat egy legfeljebb 1024 karakter.

## <a name="get-ready-to-review-results"></a>Felkészülés az eredmények áttekintése

A felülvizsgálati eszköz először a moderálási API-hívás. Ezt követően hozza azt létre szövegekre vonatkozó vélemények észlelt a címkék használatával. A felülvizsgálati eszköz megfelel a pontszám modell eredményeinek a csapata figyelmet.

## <a name="review-text-results"></a>Szöveg eredmények áttekintése

A windows részletes eredmények jelennek meg. Eredmények tartalmazzák a észlelt címkéket és a moderálási API által visszaadott feltételeket. Váltsa át egy címke kijelölési állapotát, jelölje be a címke. Előfordulhat, hogy már létrehozott egyéni címkéket is együttműködik.

![A felülvizsgálati eszköz ábrázoló képernyőkép megjelölt szöveget a Chrome böngésző ablakában](../images/reviewresults_text.png)
