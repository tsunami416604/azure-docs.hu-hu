---
title: Az Azure Internet Analyzer hibaelhárítása | Microsoft Docs
description: Az Azure Internet Analyzer hibaelhárítási útmutatója.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: a7216e697680bcc049d2ceb4caec74adfc1760b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897389"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Az Azure Internet Analyzer hibaelhárítása

Ez a cikk az Internet Analyzer gyakori problémáinak hibaelhárítási lépéseit ismerteti.

## <a name="azure-portal"></a>Azure portálon
**"Nincs elég mennyiségű mérés ehhez a scorecardhoz" a scorecardok szakaszban**

Vegye figyelembe:
- A rendszer csak akkor gyűjti a mérési adatokat, ha az Internet Analyzer-profil ügyféloldali parancsfájlja valós felhasználói forgalmat fogadó alkalmazásba van beágyazva. A szintetikus forgalom (például az Azure WebApp teljesítménytesztek) általában nem hajtja végre a beágyazott JavaScript-kódokat, így az adott típusú forgalom nem hoz létre méréseket.
- Az idősorozatok óránként egyszer jönnek létre, ezért meg kell várnia, hogy az új mérési adatmennyiség mennyi idő elteltével jelenjen meg.
- A scorecardok napi rendszerességgel jönnek létre (minden nap végén, UTC idő szerint).
- A scorecardok csak akkor jönnek létre, ha a kiválasztott szűrési kombináció (teszt, időtartam, ország stb.) esetében több mint 100 mérés lett összegyűjtve.

## <a name="next-steps"></a>Következő lépések
Az [Internet Analyzer gyakori kérdéseinek](internet-analyzer-faq.md) beolvasása
