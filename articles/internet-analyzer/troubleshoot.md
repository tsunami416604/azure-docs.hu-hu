---
title: Az Azure Internet Analyzer hibaelhárítása | Microsoft Docs
description: Az Azure Internet Analyzer hibaelhárítási útmutatója.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: 74bf0422bbe6c2c1c84365c1e8f9329a01ff9fdd
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2020
ms.locfileid: "75909041"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Az Azure Internet Analyzer hibaelhárítása

Ez a cikk az Internet Analyzer gyakori problémáinak hibaelhárítási lépéseit ismerteti.

## <a name="azure-portal"></a>Azure Portal
**"Nincs elég mennyiségű mérés ehhez a scorecardhoz" a scorecardok szakaszban**

Vegye figyelembe:
- Az ügyfél parancsfájlját be kell ágyazni egy **https** -webhelyre. A rendszer nem gyűjti a mértékeket, ha a szkript egy egyszerű szöveges (**http://** ) vagy helyi (**file://** ) webhelyen fut.
- A rendszer csak akkor gyűjti a mérési adatokat, ha az Internet Analyzer-profil ügyféloldali parancsfájlja valós felhasználói forgalmat fogadó alkalmazásba van beágyazva. A szintetikus forgalom (például az Azure WebApp teljesítménytesztek) általában nem hajtja végre a beágyazott JavaScript-kódokat, így az adott típusú forgalom nem hoz létre méréseket.
- Az idősorozatok óránként egyszer jönnek létre, ezért meg kell várnia, hogy az új mérési adatmennyiség mennyi idő elteltével jelenjen meg.
- A scorecardok napi rendszerességgel jönnek létre (minden nap végén, UTC idő szerint).
- A scorecardok csak akkor jönnek létre, ha a kiválasztott szűrési kombináció (teszt, időtartam, ország stb.) esetében több mint 100 mérés lett összegyűjtve.

## <a name="next-steps"></a>Következő lépések
Az [Internet Analyzer gyakori kérdéseinek](internet-analyzer-faq.md) beolvasása
