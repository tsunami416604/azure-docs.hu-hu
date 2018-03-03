---
title: "Az Azure CDN szabályok motor használata HTTP működés felülbírálásához |} Microsoft Docs"
description: "A szabályok motor teszi lehetővé testre szabhatja a HTTP-kérések kezelésének módja Azure CDN által például blokkolja-e a tartalom bizonyos típusú kézbesítését, gyorsítótárazási házirend határozza meg és módosíthatja a HTTP-fejlécek."
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mazha
ms.openlocfilehash: fe3df703f7eb244a52756c4d015e9ea598224ce1
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotor HTTP viselkedésének felülbírálása
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Az Azure CDN szabálymotor lehetővé teszi, hogy segítségével testre szabhatja a HTTP-kérések kezelésének módja. Például blokkolja az egyes tartalomtípusok gyorsítótárazási házirend meghatározása, vagy a HTTP-fejléc módosításával kézbesítését. Ez az oktatóanyag bemutatja, hogyan hozzon létre egy szabályt, amely módosítja a gyorsítótár-viselkedést CDN eszközök. A szabályok motor szintaxissal kapcsolatos további információkért lásd: [Azure CDN szabályok motor hivatkozás](cdn-rules-engine-reference.md).

## <a name="access"></a>Hozzáférés
A szabályok motor szeretne használni, először válasszon **kezelése** tetején a **CDN-profil** lap az Azure CDN felügyeleti lap eléréséhez. Attól függően, hogy a végponthoz van optimalizálva dinamikus acceleration (DSA) majd hozzáférhet a szabályok motor a szabálykészlet a végpont típusa megfelelő:

- Az általános webes kézbesítési vagy egyéb nem DSA optimalizálási optimalizált végpontok: 
    
    Válassza ki a **HTTP nagy** lapra, majd válasszon **szabálymotor**.

    ![A HTTP szabálymotor](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- A végpontok a DSA-re optimalizált: 
    
    Válassza ki a **ADN** lapra, majd válasszon **szabálymotor**. 
    
    ADN egy kifejezés DSA tartalom Verizon segítségével. Az itt létrehozott szabályok figyelmen kívül hagyja a nem optimalizált DSA végpontok a profilban. 

    ![A DSA szabálymotor](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Oktatóanyag
1. Az a **CDN-profil** lapon jelölje be **kezelése**.
   
    ![CDN-profil kezelésére gomb](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Válassza ki a **HTTP nagy** lapra, majd válasszon **szabálymotor**.
   
    Új szabály beállításait jelennek meg.
   
    ![Új CDN-szabály beállítások](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A több szabály listán sorrendje befolyásolja kezelésének módját. A következő szabály felülbírálhatják az előző szabályok által meghatározott műveleteket.
   > 
3. Írjon be egy nevet a **neve / leírása** szövegmező.
4. Azonosítja a kéréseket a szabály vonatkozik. Az alapértelmezett egyezés feltételt **mindig**. 
   
   ![CDN szabályfeltétel-egyezés](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > Több egyező feltételek érhetők el a legördülő listából. A jelenleg kijelölt egyezés feltétel információt válassza ki a kék információs ikonra a bal oldalán.
   > 
   >  A feltételes kifejezések részletes listájáért lásd: [szabályok feltételes kifejezések motor](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Feltételek egyeznek részletes listájáért lásd: [motor egyezés feltételek szabályok](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 
1. Egy új szolgáltatás hozzáadásához válassza a  **+**  gombra **szolgáltatások**.  A bal oldali legördülő menüben válassza ki **kényszerített belső maximális életkora**.  A szövegmezőben, amely akkor jelenik meg, írja be a **300**. Ne változtassa meg a további alapértelmezett értékei.
   
   ![CDN-szabály szolgáltatás](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > Több funkciók érhetők el a legördülő listából. A jelenleg kijelölt szolgáltatással kapcsolatos további információkért válassza ki a kék információs ikonra a bal oldalán. 
   >
   > A **kényszerített belső maximális életkora**, az eszköz `Cache-Control` és `Expires` fejlécek felülbírálják szabályozására, ha a CDN élcsomópont frissíti az eszköz a forrásból. Ebben a példában CDN élcsomópont gyorsítótárazza az eszköz 300 másodpercig, azaz 5 perc, mielőtt az eszköz a forrásból frissítésekor.
   > 
   > A funkciók részletes listájáért lásd: [szabályok motor szolgáltatások](cdn-rules-engine-reference-features.md).
   > 
   > 
1. Kattintson a **Hozzáadás** gombra kattintva mentse az új szabályt.  Az új szabály most jóváhagyására vár. Miután engedélyezte, az állapota a **függőben lévő XML** való **aktív XML**.
   
   > [!IMPORTANT]
   > Szabályok módosítások a CDN propagálásához akár 90 percig is tarthat.
   > 
   > 

## <a name="see-also"></a>Lásd még
* [Az Azure CDN áttekintése](cdn-overview.md)
* [Szabályok motor referencia](cdn-rules-engine-reference.md)
* [Szabályok motor egyezés feltételek](cdn-rules-engine-reference-match-conditions.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [Az Azure péntekenként: Az Azure CDN új premium szolgáltatással](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (videó)