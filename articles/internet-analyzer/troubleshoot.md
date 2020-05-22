---
title: Az Azure Internet Analyzer hibaelhárítása | Microsoft Docs
description: Az Azure Internet Analyzer hibaelhárítási útmutatója.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 0c9e203d7e8be7b24c711f73e2152a7745a57dac
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745464"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Az Azure Internet Analyzer hibaelhárítása

Ez a cikk az Internet Analyzer gyakori problémáinak hibaelhárítási lépéseit ismerteti.

## <a name="things-to-keep-in-mind"></a>Ügyeljen az alábbiakra
- Az ügyfél parancsfájlját be kell ágyazni egy **https** -webhelyre. A rendszer nem gyűjti a mértékeket, ha a szkript egy egyszerű szöveges (**http://**) vagy helyi (**file://**) webhelyen fut.
- A rendszer csak akkor gyűjti a mérési adatokat, ha az Internet Analyzer-profil ügyféloldali parancsfájlja valós felhasználói forgalmat fogadó alkalmazásba van beágyazva. A szintetikus forgalom (például az Azure WebApp teljesítménytesztek) általában nem hajtja végre a beágyazott JavaScript-kódokat, így az adott típusú forgalom nem hoz létre méréseket.

## <a name="azure-portal"></a>Azure Portal
**"A scorecardok szakaszban nem jött létre a kiválasztott szűrő kombinációhoz tartozó scorecard**
- A scorecardok napi rendszerességgel jönnek létre (minden nap végén, UTC idő szerint).
- A scorecardok csak akkor jönnek létre, ha a kiválasztott szűrési kombináció (teszt, időtartam, ország/régió stb.) esetében több mint 100 mérés lett összegyűjtve.

**A "teljes mérési szám" nulla a teszt egyik vagy mindkét végpontja esetében**
- Az idősorozatok és a mérések száma óránként egyszer történik, ezért meg kell várnia legalább az új mérési adatok megjelenítésének időtartamát.
- Az Internet Analyzer csak a sikeres méréseket (azaz HTTP 200-válaszokat) számítja ki az elemzéshez. Ha egy teszt egyik vagy mindkét végpontja nem érhető el, vagy nem 200 HTTP-kódot ad vissza, akkor a rendszer nulla teljes mérési értéket jelenít meg.

## <a name="next-steps"></a>További lépések
Az [Internet Analyzer gyakori kérdéseinek](internet-analyzer-faq.md) beolvasása
