---
title: Tekintse át az Azure Content moderátor szöveg |} Microsoft Docs
description: 'Útmutató: tekintse át a szöveges tartalom moderátor megtekintéséhez a pontszám és címkék észlelte. A adatokat a megállapításához használja a tartalom megfelelő-e.'
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/03/2017
ms.author: sajagtap
ms.openlocfilehash: cb8774395b7374677e8de3b80630a2d4abf490f9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346962"
---
# <a name="review-text"></a>Szöveg felülvizsgálata

Tekintse át a szöveg pontszámok használatával Azure tartalom moderátor használható, és címkék észlelte. Az információ használatával határozhatja meg, hogy-e megfelelő tartalom. 

## <a name="select-or-enter-the-text-to-review"></a>Válassza ki vagy írja be a szöveget áttekintése

A tartalom moderátor, válassza ki a **próbálja** fülre. Ezután válassza ki a **szöveg** nyissa meg a szöveg moderálás kezdőképernyőn lehetőséget. Adja meg a szöveget, vagy küldje el az Automatikus szöveg moderálás alapértelmezett szöveg. Megadhat egy legfeljebb 1024 karakter hosszú lehet.

## <a name="get-ready-to-review-results"></a>Felkészülés az eredmények áttekintése

A felülvizsgálati eszköz először meghívja a szöveg moderálás API. Szöveg értékelést, majd a észlelt címkék használatával létrehozott. A felülvizsgálati eszköz megfelel a csapata figyelmet a pontszám modell eredményeinek.

## <a name="review-text-results"></a>Szöveg eredményeinek áttekintése

A windows részletes adatok jelennek meg. Eredmények tartalmazzák a észlelt címkék és a szöveg moderálás API által visszaadott eredményobjektumokban tárolt feltételeket. A címke kiválasztása állapot átváltása, válassza ki a címke. Előfordulhat, hogy hozott létre egyéni címkéket is dolgozhat.

![Szöveg eredményeinek áttekintése](images/3-review-text-2.png)
