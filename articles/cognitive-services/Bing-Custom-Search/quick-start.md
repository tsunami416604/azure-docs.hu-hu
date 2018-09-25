---
title: A Microsoft Cognitive Services a Bing Custom Search első példányt - hoz létre
description: Bing Custom Search használatával szeretne létrehozni egy egyéni keresési példány, amely meghatározza a nézet vagy a webes szelet. A példány beállítása, hogy a nyilvános tartományokban, alhelyek és, amelyek azt szeretné, hogy a Bing keresés, és ennek a területnek a szükséges beállításokat tartalmazza.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 25d622772fe47ffad001834d476e612f8c606904
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981657"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Az első Bing Custom Search-példány létrehozása
Bing Custom Search használatával szeretne létrehozni egy egyéni keresési példány, amely meghatározza a nézet vagy a webes szelet. A példány beállítása, hogy a nyilvános tartományok, webhelyek és, amelyek azt szeretné, hogy a Bing keresés, és ennek a területnek a szükséges beállításokat tartalmazza. A példány létrehozásához használja a Bing Custom Search [portál](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Hozzon létre egy egyéni keresési példány

Bing Custom Search-példány létrehozása:

1.  Egyéni keresési API-kulcs lekérése. Lásd: [a Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Kattintson a **jelentkezzen be a** gombra, és jelentkezzen be Microsoft-fiókkal (MSA) a portálon. 
    - Ha az MSA nem rendelkezik, kattintson a **Microsoft-fiók létrehozásához**. A portál kéri a engedélyekkel az adatok eléréséhez. Kattintson a **Yes** (Igen) gombra.
    - Fogadja el a Cognitive Services feltételeket. Ellenőrizze **elfogadom** kattintson **Elfogadom**.  
3.  Miután bejelentkezett, kattintson a **új példányt** és a példány neve. Használja a jelentéssel bíró, és ismerteti a visszaadott tartalom típusát. A név bármikor módosíthatja. 
4.  Az a **aktív** lapjára **keresési funkciókat**, adjon meg egy URL-CÍMÉT, vagy további webhelyek fel szeretne venni a keresés.
5.  Győződjön meg arról, hogy a példány eredményeket ad vissza, adjon meg egy lekérdezést az előnézeti ablaktáblában láthatja a jobb oldalon. Ha nincsenek eredmények, adjon meg egy új webhelyet. Bing – csak a nyilvános webhelyekhez, azt még indexelt eredményeket ad vissza.
6.  Kattintson a **közzététel** konfigurációs módosítások közzétételére az éles környezetbe. Amikor a rendszer kéri, kattintson a **közzététel** megerősítéséhez.
7.  Kattintson a **éles** > **végpontok** , és másolja a **egyéni konfigurációs azonosító**. Ez az egyéni keresési API hívása azonosító szükséges.

## <a name="next-steps"></a>További lépések

Továbbra is működnek együtt az imént létrehozott kövesse az alábbi útmutatók egyéni keresési példány:

- [Konfigurálja az egyéni keresés](./define-your-custom-view.md)
- [Hívja meg az egyéni keresés](./search-your-custom-view.md)
- [Egyéni keresés megosztása](./share-your-custom-search.md)
- [A központi felhasználói felület konfigurálása](./hosted-ui.md)
- [Kiemeli a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Oldal weblapok](./page-webpages.md)
