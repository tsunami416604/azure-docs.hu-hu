---
title: Https kényszerítése a Standard Azure CDN-ben | Microsoft dokumentumok
description: A Microsoft Standard Azure Content Delivery Network (Azure CDN) szabálymotorjával testreszabhatja, hogy az Azure CDN hogyan kezelje a HTTP-kérelmeket, beleértve bizonyos típusú tartalmak kézbesítésének letiltását, a gyorsítótárazási házirend meghatározását és a HTTP-fejlécek módosítását. Ebből a cikkből megtudhatja, hogyan hozhat létre egy szabályt a felhasználók HTTPS-re való átirányításához.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: 724861305d7a25db409072200ac2bc3bd83f0682
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74171574"
---
# <a name="set-up-the-standard-rules-engine-for-azure-cdn"></a>Az Azure CDN szabványos szabálymotorjának beállítása

Ez a cikk ismerteti, hogyan kell beállítani és használni az Azure Content Delivery Network (Azure CDN) szabványos szabályok motorját.

## <a name="standard-rules-engine"></a>Általános szabályokkal bíró motor

Az Azure CDN szabványos szabályok motorjával testreszabhatja a HTTP-kérelmek kezelésének módját. A szabálymotor segítségével például kényszerítheti a tartalomkézbesítést adott protokollok használatára, gyorsítótárazási házirend meghatározására vagy HTTP-fejléc módosítására. Ez a cikk bemutatja, hogyan hozhat létre olyan szabályt, amely automatikusan átirányítja a felhasználókat a HTTPS-re. 

> [!NOTE]
> A cikkben ismertetett szabálymotor csak a Microsoft Standard Azure CDN-jéhez érhető el. 

## <a name="redirect-users-to-https"></a>Felhasználók átirányítása HTTPS-re

1. A Microsoft-profilokban nyissa meg az Azure Content Delivery Network webhelyet.

1. A **CDN-profillapon** jelölje ki azt a végpontot, amelyhez szabályokat szeretne létrehozni.
  
1. Válassza a **Szabálymotor** lapot.
   
    Megnyílik **a Szabálymotor ablaktábla,** és megjeleníti az elérhető globális szabályok listáját. 
   
    [![Az Azure CDN új szabályok lapja](./media/cdn-standard-rules-engine/cdn-new-rule.png)](./media/cdn-standard-rules-engine/cdn-new-rule.png#lightbox)
   
   > [!IMPORTANT]
   > A több szabály felsorolásának sorrendje befolyásolja a szabályok kezelésének módját. Előfordulhat, hogy a szabályban megadott műveleteket egy későbbi szabály felülírja.
   >

1. Válassza **a Szabály hozzáadása lehetőséget,** és adjon meg egy szabálynevet. A szabályneveknek betűvel kell kezdődniük, és csak számokat és betűket tartalmazhatnak.

1. A szabály által alkalmazott kérelmek típusának azonosításához hozzon létre egyegyezési feltételt:
    1. Válassza **a Feltétel hozzáadása**lehetőséget, majd a Protokoll **kérése** feltételt.
    1. A **Operátor** területen válassza az **Egyenlő** lehetőséget.
    1. Az **Érték csoportban**válassza a **HTTP**lehetőséget.
   
   [![Az Azure CDN-szabály egyezési feltétele](./media/cdn-standard-rules-engine/cdn-match-condition.png)](./media/cdn-standard-rules-engine/cdn-match-condition.png#lightbox)
   
   > [!NOTE]
   > A **Feltétel hozzáadása** legördülő listában több egyezési feltétel közül választhat. Az egyezési feltételek részletes listáját [a Normál szabályok motor feltételek egyeztetése című témakörben található.](cdn-standard-rules-engine-match-conditions.md)
   
1. Válassza ki az egyezési feltételnek megfelelő kérelmekre alkalmazandó műveletet:
   1. Válassza **a Művelet hozzáadása**lehetőséget, majd az **URL-átirányítás**lehetőséget.
   1. A **Típus mezőben**válassza a **Found (302) lehetőséget.**
   1. A **Protokoll csoportban**válassza a **HTTPS**lehetőséget.
   1. A bejövő értékek használatához hagyja üresen az összes többi mezőt.
   
   [![Azure CDN-szabályművelet](./media/cdn-standard-rules-engine/cdn-action.png)](./media/cdn-standard-rules-engine/cdn-action.png#lightbox)
   
   > [!NOTE]
   > A **Művelet hozzáadása** legördülő listában több művelet közül is választhat. A műveletek részletes listáját a [Műveletek a Normál szabályok motorban](cdn-standard-rules-engine-actions.md)című témakörben található.

6. Az új szabály mentéséhez válassza a **Mentés** lehetőséget. A szabály már használható.
   
   > [!IMPORTANT]
   > A szabály módosítása akár 15 percet is igénybe vehet az Azure CDN-en keresztültörténő propagálása.
   >
   

## <a name="next-steps"></a>További lépések

- [Az Azure CDN – áttekintés](cdn-overview.md)
- [A Standard szabálymotor referenciája](cdn-standard-rules-engine-reference.md)
- [Feltételek egyeztetése a Standard rules motorban](cdn-standard-rules-engine-match-conditions.md)
- [Műveletek a Standard szabálymotorban](cdn-standard-rules-engine-actions.md)
