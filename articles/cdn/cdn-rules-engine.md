---
title: Az Azure CDN szabálymotorral használatával HTTP-viselkedés felülbírálásához |} A Microsoft Docs
description: A rules engine teszi lehetővé testre szabhatja, hogyan HTTP-kérelmekre az kezeli az Azure CDN, például blokkolja a bizonyos tartalomtípusok kézbesítését, gyorsítótárazási szabályzat definiálása és módosíthatja a HTTP-fejléceket.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 625a912b-91f2-485d-8991-128cc194ee71
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: magattus
ms.openlocfilehash: 2ac43b472758f3403bc87bf3d64321eb97109f53
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092396"
---
# <a name="override-http-behavior-using-the-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotorral használatával HTTP a működés felülbírálása
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Az Azure CDN szabálymotorral lehetővé teszi, hogy testre szabhatja a HTTP-kérések kezelésének módja. Például blokkolja a bizonyos tartalomtípusok kézbesítését, tartalom, gyorsítótárazási szabályzatot definiálhat, vagy módosítása egy HTTP-fejlécben. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy szabályt, amely az eszközök CDN gyorsítótárazási viselkedésének módosítása. A szabályok motor szintaxissal kapcsolatos további információkért lásd: [Azure CDN szabálymotor-referencia](cdn-rules-engine-reference.md).

## <a name="access"></a>Hozzáférés
Hozzáférhet a rules engine, először jelöljön ki **kezelés** tetején a **CDN-profil** oldal az Azure CDN felügyeleti lap elérésére. Attól függően, hogy a végpont a dinamikuswebhely-gyorsítás (DSA) van optimalizálva hogy elérhesse a a rules engine, az a végpont típusa megfelelő szabályokat együtt:

- Általános webes kézbesítés vagy egyéb nem DSA optimalizálási optimalizált végpontok: 
    
    Válassza ki a **HTTP nagy** lapfülre, majd válassza ki **Szabálymotorral**.

    ![Szabálymotor HTTP-hez](./media/cdn-rules-engine/cdn-http-rules-engine.png)

- A végpontok DSA optimalizálva: 
    
    Válassza ki a **ADN** lapfülre, majd válassza ki **Szabálymotorral**. 
    
    ADN DSA tartalom megadásához Verizon által használt kifejezés. Az itt létrehozott szabályok olyan végpontok, amelyek a profil nem optimalizált DSA figyelmen kívül hagyja. 

    ![A DSA szabálymotor](./media/cdn-rules-engine/cdn-dsa-rules-engine.png)

## <a name="tutorial"></a>Oktatóanyag
1. Az a **CDN-profil** lapon jelölje be **kezelés**.
   
    ![A CDN-profil felügyelet gombra](./media/cdn-rules-engine/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portálját.

2. Válassza ki a **HTTP nagy** lapfülre, majd válassza ki **Szabálymotorral**.
   
    Új szabály beállításait jelennek meg.
   
    ![Új CDN-szabály beállítások](./media/cdn-rules-engine/cdn-new-rule.png)
   
   > [!IMPORTANT]
   > A sorrend, amelyben több szabály szerepel a listában érinti, hogyan kezeli őket. A következő szabály felülbírálhatja az előző szabályok által meghatározott műveleteket.
   > 

3. Írjon be egy nevet a **neve / leírása** szövegmezőbe.

4. Azonosítja a kérelmeket a szabály vonatkozik. Az alapértelmezett egyezés feltétel használatához **mindig**. 
   
   ![CDN szabályfeltétel-egyezés](./media/cdn-rules-engine/cdn-request-type.png)
   
   > [!NOTE]
   > A legördülő listában több egyezési feltételei érhetők el. A jelenleg kijelölt egyezési feltétellel kapcsolatos információkért válassza a kék információs ikonra a bal oldalán.
   > 
   >  A feltételes kifejezések részletes listájáért lásd: [szabálymotor feltételes kifejezései](cdn-rules-engine-reference-match-conditions.md).
   >  
   > Részletes listát egyezési feltételei, lásd: [szabálymotor egyezési feltételei szabályok](cdn-rules-engine-reference-match-conditions.md).
   > 
   > 

5. Adjon hozzá egy új funkció, jelölje be a **+** megjelenítő gombra **funkciók**.  Válassza ki a legördülő listában, a bal oldali, **kényszerített belső Max-Age**.  A megjelenő szövegmezőbe írja be **300**. Ne változtassa meg a többi alapértelmezett értéket.
   
   ![CDN szabály funkció](./media/cdn-rules-engine/cdn-new-feature.png)
   
   > [!NOTE]
   > A legördülő listában több funkciók érhetők el. A jelenleg kijelölt szolgáltatással kapcsolatos további információkért válassza a kék információs ikonra a bal oldalán. 
   >
   > A **kényszerített belső Max-Age**, az eszköz `Cache-Control` és `Expires` fejlécek felülbírálják szabályozhatja, mikor a CDN élcsomópont frissíti az eszköz a forrásból. Ebben a példában a CDN élcsomópont gyorsítótárazza az eszköz 300 másodpercig, azaz 5 perc, mielőtt azt frissíti az eszköz a forrásból.
   > 
   > Funkciók részletes listájáért lásd: [szabálymotor funkciói](cdn-rules-engine-reference-features.md).
   > 
   > 

6. Válassza ki **Hozzáadás** az új szabály mentéséhez.  Az új szabály most jóváhagyására vár. Miután jóváhagyták, az állapot változik a **függőben lévő XML** való **aktív XML**.
   
   > [!IMPORTANT]
   > Szabályok módosítások keresztül az Azure CDN propagálása akár 10 percet is igénybe vehet.
   > 
   > 

## <a name="see-also"></a>Lásd még
* [Az Azure CDN áttekintése](cdn-overview.md)
* [Szabálymotor-referencia](cdn-rules-engine-reference.md)
* [Szabálymotor egyezési feltételei](cdn-rules-engine-reference-match-conditions.md)
* [Szabálymotor feltételes kifejezései](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabálymotor funkciói](cdn-rules-engine-reference-features.md)
* [Azure friday: Az Azure CDN hatékony új prémium szintű szolgáltatásai](https://azure.microsoft.com/documentation/videos/azure-cdns-powerful-new-premium-features/) (videó)