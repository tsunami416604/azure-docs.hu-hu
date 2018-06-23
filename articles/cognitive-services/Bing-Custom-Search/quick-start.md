---
title: Hozzon létre első Bing egyéni keresés példányt - kognitív Microsoft-szolgáltatások
description: Bing egyéni keresési használatához hozzon létre egy egyéni keresési példányt, amely meghatározza a nézet vagy a Web szelet kell. A példány beállítása, hogy a nyilvános tartományokban, alwebhelyek és internetes keresés a kívánt weblapjait és rangsorolási módosítások tartalmaz.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 35f0bca01de1c2087f6ae30949cca9b03192b838
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347874"
---
# <a name="create-your-first-bing-custom-search-instance"></a>Az első Bing egyéni keresés példányának létrehozása
Bing egyéni keresési használatához hozzon létre egy egyéni keresési példányt, amely meghatározza a nézet vagy a Web szelet kell. A példány beállítása, hogy a nyilvános tartományok, webhelyek és internetes keresés a kívánt weblapjait és rangsorolási módosítások tartalmaz. A példány létrehozásához használja a Bing egyéni keresés [portal](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Egy egyéni keresési példányának létrehozása

Bing egyéni keresés példány létrehozása:

1.  A kulcs lekérése egyéni keresési API-hoz. Lásd: [kognitív-szolgáltatás kipróbálására](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Jelentkezzen be a portálra a Microsoft-fiókkal (msa-t). Kattintson a **bejelentkezés** gombra. Ha most először használja a portál kövesse az alábbi kiegészítő lépéseket, ellenkező esetben folytassa a 3. lépés.
    - Ha még nem rendelkezik az msa-t, kattintson a **Microsoft-fiók létrehozása**. A portál kér engedélyeket, hogy az adatok. Kattintson a **Yes** (Igen) gombra.
    - Fogadja el a kognitív szolgáltatási feltételek. Ellenőrizze **elfogadom** kattintson **Elfogadom**.  
3.  Kattintson a bejelentkezést követően a **új példány** és a példány neve. Használja a kifejező, és ismerteti a tartalomtípushoz, a keresés adja vissza. Bármikor módosíthatja a nevét. 
4.  A a **aktív** lapján **keresésekhez**, adja meg az URL-cím, egy vagy több hely szeretne felvenni a keresés.
5.  Győződjön meg arról, hogy a példány eredményeket ad vissza, adjon meg egy lekérdezést a betekintő ablaktáblában kattintson a jobb. Ha nem, adja meg egy új helyet. Bing csak a nyilvános helyeken, akkor rendelkezik indexelt eredményeket ad vissza.
6.  Kattintson a **közzététel** éles konfigurációs változások közzétételére. Amikor a rendszer kéri, kattintson a **közzététel** megerősítéséhez.
7.  Kattintson a **éles** > **végpontok** , és másolja a **egyéni konfiguráció azonosítója**. Ez az egyéni Search API hívása azonosító szükséges.

## <a name="next-steps"></a>További lépések

Továbbra is működnek ezen útmutatókat követve utasításait által imént létrehozott egyéni keresési példányhoz:

- [Az egyéni keresési funkciók konfigurálása](./define-your-custom-view.md)
- [Az egyéni keresési hívás](./search-your-custom-view.md)
- [Egyéni keresés megosztása](./share-your-custom-search.md)
- [A központi felhasználói felületi élmény konfigurálása](./hosted-ui.md)
- [Jelölje ki a szöveget decoration jelölők használatával](./hit-highlighting.md)
- [Lap szolgáltatásának konfigurálása](./page-webpages.md)
