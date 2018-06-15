---
title: Mi az egyéni döntési szolgáltatás? -Azure kognitív szolgáltatások |} Microsoft Docs
description: Ez a cikk áttekintése Azure egyéni döntési Service egy felhőalapú API, amely az révén élesíti környezetfüggő döntéshozatali.
services: cognitive-services
author: alekh
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 774467446513dcd7ade7255d998b11f41824cafe
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35350015"
---
# <a name="what-is-custom-decision-service"></a>Mi az egyéni döntési szolgáltatás?

A szokásos webes vagy mobilalkalmazás, több olyan cikket, vagy más típusú tartalmakra első lap hivatkozásainak. Az első oldal betöltése, azt a sorrendet megadó cikkek szerepel-e ez az első oldal egyéni döntési szolgáltatás kérhetik. Igen amikor egy felhasználó egy cikk azt, egy második kérést sikerült elküldeni a egyéni döntési szolgáltatás, amely naplózza, hogy felhasználói művelet eredményét.

Egyéni döntési szolgáltatása könnyen használható, mivel az csak RSS-hírcsatorna a tartalom és a JavaScript lehet hozzáadni az alkalmazásba néhány sornyi.

Egyéni döntési szolgáltatás a tartalmat a machine Learning szolgáltatáshoz szolgáltatások alakítja át. A rendszer ezeket a funkciókat használja tudni, hogy a tartalom szöveg, képek, videók, valamint általános céggel kapcsolatos véleményeket. Akkor használja, több más [Microsoft kognitív szolgáltatások](https://www.microsoft.com/cognitive-services), például [entitás Linking](../entitylinking/home.md), [Szövegelemzések](../text-analytics/overview.md), [Érzelemfelismerési](../emotion/home.md), és [Számítógép stratégiai](../computer-vision/home.md).

Egyéni döntési szolgáltatás néhány gyakori-használati esetek a következők:

* Személyre szabás cikkek egy hírek webhelyen
* Lemezen található media portál testreszabása
* Ad adatközpontokon vagy az ad vezeti weblapokat optimalizálása
* Ajánlott egy vásárlásra szolgáló webhelyen elemek rangsorolási.

Egyéni döntési szolgáltatása jelenleg *szabad nyilvános előzetes*. Szabhatja, hogy a webhely vagy alkalmazás a cikkek listáját. A szolgáltatás kivonása leginkább megfelelő angol nyelvű tartalom. [Korlátozott funkciók](../text-analytics/overview.md) van érhető el a többi nyelvet, spanyol, francia, német, portugál és japán hasonlóan. Ebben a dokumentációban felül kell vizsgálni, amint az elérhetővé válik az új funkciókat.

Egyéni döntési szolgáltatás használható alkalmazások, amelyek nincsenek a tartalom személyre szabása tartományban. Ezek az alkalmazások remekül beválik, ha egy egyéni előnézeti lehet. [Kapcsolatfelvétel](https://azure.microsoft.com/overview/sales-number/) további.

## <a name="api-usage-modes"></a>API-használati módok

Egyéni döntési szolgáltatás is alkalmazható weblapjait és a mobilalkalmazásait. Az API-kat a böngészőben vagy az alkalmazás hívható. Az API-használati mind hasonló, de egyes részleteket eltérőek.

## <a name="glossary-of-terms"></a>Szószedet

A jelen dokumentációban lévő gyakran több feltételek fordulhat elő:

* **A művelet set**: A tartalom elemeinek halmazát egyéni döntési szolgáltatáshoz való dimenziószáma. Ebben a készletben adhat meg egy *RSS* vagy *Atom* végpont.
* **Prioritás**: minden egyéni döntési szolgáltatásnak küldött kérelemben egy vagy több művelet beállítása határozza meg. A rendszer által a tartalom beállításokat a halmazok kiadási válaszol, és visszaadja azokat rangsorolt sorrendje.
* **A visszahívási függvény**: Ez a függvény, amelyet ad meg, ez a beállítás a felhasználói felületen a tartalmat. A tartalom rendezve egyéni döntési szolgáltatás által visszaadott sorrendet megadó sorrendje.
* **Ellenszolgáltatás**: biztosítása, hogy hogyan válaszolt a felhasználó a megjelenített tartalmat. Egyéni döntési szolgáltatás gombra kell kattintania a felhasználói válaszra méri. A kattintások jelenti a rendszer egyéni kód szúrja be az alkalmazás használatával.

## <a name="next-steps"></a>További lépések

* [Az alkalmazás regisztrálása](custom-decision-service-get-started-register.md) egyéni döntési szolgáltatással
* Első lépések optimalizálása [egy weblap](custom-decision-service-get-started-browser.md) vagy [alkalmazással](custom-decision-service-get-started-app.md).
* Tekintse át a [API-referencia](custom-decision-service-api-reference.md) további információt a megadott funkciót.