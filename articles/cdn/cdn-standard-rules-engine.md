---
title: HTTPS kikényszerítés a Azure CDN Standard Rules Engine használatával | Microsoft Docs
description: A Standard Rules Engine lehetővé teszi a HTTP-kérések kezelésének testreszabását a Microsoft Azure CDN, például bizonyos tartalomtípusok kézbesítésének blokkolását, a gyorsítótárazási házirend meghatározását és a HTTP-fejlécek módosítását.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: b24c4a04e0c02258a918ee075066d90c22ea0c75
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615922"
---
# <a name="azure-cdn-standard-rules-engine"></a>Azure CDN Standard Rules Engine

> [!NOTE]
> A standard szintű szabályok motorja csak a Microsoft Azure CDN számára érhető el. 

A Azure CDN Standard Rules Engine lehetővé teszi a HTTP-kérések kezelésének testreszabását. Például a tartalom továbbításának kényszerítése adott protokollokon keresztül, a gyorsítótárazási házirend meghatározása vagy egy HTTP-fejléc módosítása. Ez az oktatóanyag bemutatja, hogyan hozhat létre olyan szabályt, amely automatikusan átirányítja a felhasználókat a HTTPS-re. 


## <a name="tutorial"></a>Oktatóanyag

1. A Microsoft-profilok Azure CDN a **CDN-profil** lapon válassza ki azt a végpontot, amelynek a szabályait konfigurálni kívánja.
  
2. Válassza a **szabályok motor** fület a bal oldalon.
   
    Megjelenik a szabályok motor panelje, a globális szabállyal együtt. 
   
    [![CDN új szabályok lap](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A különböző szabályok listájának sorrendje befolyásolja a kezelésük módját. Egy későbbi szabály felülbírálhatja egy korábbi szabály által megadott műveleteket.
   >

3. Kattintson a **szabály hozzáadása** gombra, és adja meg a szabály nevét. A szabályok nevének betűvel kell kezdődnie, és csak számokból és betűkből állhat.

4. Azon kérelmek típusának meghatározása, amelyekre a szabály vonatkozik. A legördülő listából válassza ki a **kérelem protokolljának** egyeztetése feltételt, és használja a **http** **értéket.**
   
   [![CDN-szabály egyeztetési feltétele](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > A legördülő listában több egyezési feltétel is elérhető. Az egyeztetési feltételek részletes listáját lásd: a [szabályok motorjának egyeztetési feltételei](cdn-standard-rules-engine-match-conditions.md).
   
5. Válassza ki azt a műveletet, amely az azonosított kérelmekre lesz alkalmazva. A legördülő listából válassza ki az **URL-átirányítási** műveletet, és használja a **(302)** értéket a típushoz, a **https** protokollhoz pedig a következőt:. A bejövő értékek használatához hagyja üresen az összes többi mezőt.
   
   [![CDN-szabály művelete](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > Több művelet is elérhető a legördülő listában. A műveletek részletes listáját lásd: a [szabályok motorjának műveletei](cdn-standard-rules-engine-actions.md).

6. Az új szabály mentéséhez válassza a **Mentés** lehetőséget.  Az új szabály most már telepítve lesz.
   
   > [!IMPORTANT]
   > A szabályok változásai akár 15 percet is igénybe vehetnek Azure CDN.
   >
   

## <a name="see-also"></a>Lásd még:

- [Azure CDN áttekintése](cdn-overview.md)
- [Szabványos szabályok – motor referenciája](cdn-standard-rules-engine-reference.md)
- [Szabványos szabályok motorjának egyeztetési feltételei](cdn-standard-rules-engine-match-conditions.md)
- [Szabványos szabályok motor műveletei](cdn-standard-rules-engine-actions.md)
