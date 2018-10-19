---
title: 'Első lépések: az első Bing Custom Search-példány létrehozása'
titlesuffix: Azure Cognitive Services
description: A Bing Custom Search használatához hozzon létre egy Custom Search-példányt, amelyben meghatározza a nézetet vagy a web egy részletét. A példány minden rangsorolási módosítást tartalmaz, valamint a beállításokat, amelyek meghatározzák azokat a nyilvános tartományokat, aloldalakat és weboldalakat, amelyeken a Binggel keresni kíván.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2017
ms.author: v-brapel
ms.openlocfilehash: 866d32aa4de45076fcbc4e413d8c2e67d5346878
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816238"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Rövid útmutató: az első Bing Custom Search-példány létrehozása
A Bing Custom Search használatához hozzon létre egy Custom Search-példányt, amelyben meghatározza a nézetet vagy a web egy részletét. A példány minden rangsorolási módosítást tartalmaz, valamint a beállításokat, amelyek meghatározzák azokat a nyilvános webhelyeket, aloldalakat és weboldalakat, amelyeken a Binggel keresni kíván. A példány létrehozásához használja a Bing Custom Search [portált](https://customsearch.ai). 

## <a name="create-a-custom-search-instance"></a>Egyéni keresési példány létrehozása

Bing Custom Search-példány létrehozása:

1.  Szerezzen be egy kulcsot a Custom Search API-hoz. Lásd: [A Cognitive Services kipróbálása](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
2.  Kattintson a **Bejelentkezés** gombra, és egy Microsoft-fiók (MSA) használatával jelentkezzen be a portálra. 
    - Ha nincs MSA-fiókja, kattintson a **Microsoft-fiók létrehozása** elemre. A portál engedélyeket kér, hogy hozzáférhessen az adataihoz. Kattintson a **Yes** (Igen) gombra.
    - Fogadja el a Cognitive Services feltételeit. Jelölje be az **Elfogadom** jelölőnégyzetet, majd kattintson az **Elfogadás** elemre.  
3.  Miután bejelentkezett, kattintson a **New Instance** (Új példány) elemre, és adjon nevet a példánynak. Használjon jelentéssel bíró nevet, amely leírja a keresés által visszaadott tartalom típusát. A nevet bármikor módosíthatja. 
4.  Az **Active** (Aktív) lap **Search Experience** (Keresési felület) területén adja meg a keresésbe belefoglalni kívánt egy vagy több webhely URL-címét.
5.  Ha meg kíván győződni arról, hogy a példány visszaad eredményeket, akkor adjon meg egy lekérdezést a jobb oldalon található előnézet panelen. Ha nincsenek eredmények, adjon meg egy új webhelyet. A Bing csak olyan nyilvános webhelyekhez jelenít meg eredményeket, amelyeket indexelt.
6.  Kattintson a **Publish** (Közzététel) elemre a konfigurációs módosítások éles környezetben történő közzétételéhez. Ha a rendszer kéri, kattintson a **Publish** (Közzététel) elemre a megerősítéshez.
7.  Kattintson a **Production** (Termelés)  > **Endpoints** (Végpontok) elemre, és másolja ki a **Custom Configuration ID** (Egyéni konfigurációazonosító értékét). Erre az azonosítóra szüksége lesz a Custom Search API hívásához.

## <a name="next-steps"></a>További lépések

Az alábbi használati útmutatók szerint használhatja tovább a frissen létrehozott Custom Search-példányt:

- [Egyéni keresési felület konfigurálása](./define-your-custom-view.md)
- [Egyéni keresés meghívása](./search-your-custom-view.md)
- [Egyéni keresés megosztása](./share-your-custom-search.md)
- [Üzemeltetett felhasználói felület konfigurálása](./hosted-ui.md)
- [Díszítő jelölők használata szövegkiemeléshez](./hit-highlighting.md)
- [Weboldalak lapozása](./page-webpages.md)
