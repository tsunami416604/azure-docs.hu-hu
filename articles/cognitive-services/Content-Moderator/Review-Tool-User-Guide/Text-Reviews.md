---
title: Tekintse át az Azure Content Moderator szöveg |} A Microsoft Docs
description: Megtudhatja, hogyan tekintse át a szöveg a Content Moderator, a pontszám és címkék észlelt. Kiszűrni az adatokat használja, hogy megfelelő-e tartalmat.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: 60a8d03a6562c9a66c2f6781ebbc330c989b343e
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51852095"
---
# <a name="review-text"></a>Szöveg felülvizsgálata

Tekintse át a szöveges értékeket használatával az Azure Content Moderator használható, és címkék észlelt. Az információ használatával határozhatja meg, hogy-e megfelelő tartalom. 

## <a name="select-or-enter-the-text-to-review"></a>Válassza ki vagy írja be a szöveget áttekintése

A Content Moderator, válassza ki a **próbálja** fülre. Ezután válassza ki a **szöveg** szöveg moderálása kezdőképernyőjén nyissa meg a beállítást. Adja meg a szöveg, vagy küldje el az alapértelmezett mintaszöveget automatizált szövegmoderálás számára. Megadhat egy legfeljebb 1024 karakter.

## <a name="get-ready-to-review-results"></a>Felkészülés az eredmények áttekintése

A felülvizsgálati eszköz először a moderálási API-hívás. Ezt követően hozza azt létre szövegekre vonatkozó vélemények észlelt a címkék használatával. A felülvizsgálati eszköz megfelel a pontszám modell eredményeinek a csapata figyelmet.

## <a name="review-text-results"></a>Szöveg eredmények áttekintése

A windows részletes eredmények jelennek meg. Eredmények tartalmazzák a észlelt címkéket és a moderálási API által visszaadott feltételeket. Váltsa át egy címke kijelölési állapotát, jelölje be a címke. Előfordulhat, hogy már létrehozott egyéni címkéket is együttműködik.

![A felülvizsgálati eszköz ábrázoló képernyőkép megjelölt szöveget a Chrome böngésző ablakában](../images/reviewresults_text.png)
