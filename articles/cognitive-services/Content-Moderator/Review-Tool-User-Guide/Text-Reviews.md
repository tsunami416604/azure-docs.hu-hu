---
title: Az Azure Content Moderator – a Content Moderator felülvizsgálati szöveg
description: Megtudhatja, hogyan tekintse át a szöveg a Content Moderator, a pontszám és címkék észlelt. Kiszűrni az adatokat használja, hogy megfelelő-e tartalmat.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 896956653ed924b7e30e63c1ae5cfa90dd0cc94d
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55219787"
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
