---
title: "Az Azure CDN szabályok motor feltételes kifejezések |} Microsoft Docs"
description: "Az Azure CDN referenciadokumentációt szabályok motor egyezés feltételek és a szolgáltatások."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Az Azure CDN-szabályok a feltételes kifejezések motor
Ez a témakör részletes leírását tartalmazza a feltételes kifejezések az Azure Content Delivery Network (CDN) [szabálymotor](cdn-rules-engine.md).

A szabály első része a feltételes kifejezés.

A feltételes kifejezés | Leírás
-----------------------|-------------
HA | Egy IF kifejezés mindig egy szabályt a következő utasításnak elsőként részét képezi. Mint minden más feltételes kifejezés ez IF utasítást kapcsolódó egyezést kell lennie. Ha nincsenek további feltételes kifejezések meghatározása a megfelelés a feltételnek az teljesülniük kell ahhoz funkciókat is alkalmazható egy kérelem határozza meg.
HA | ÉS IF kifejezés csak a feltételes kifejezések: Ha, és ha a következő típusú után adhatók hozzá. Azt jelzi, hogy van-e egy másik feltétel, amely a kezdeti IF utasítást kell teljesülniük.
MÁSKÜLÖNBEN HA| MÁS IF kifejezés határozza meg, amelyeknek teljesülniük kell előtt kerül sor az adott a MÁSKÜLÖNBEN Ha nyilatkozat funkciókat alternatív feltételt. Az ELSE IF utasítást jelenlétét az előző utasítást végét jelöli. A csak a feltételes kifejezés, amely után az ELSE IF utasítást egy másik más IF utasítást kell elhelyezni. Ez azt jelenti, hogy az ELSE IF utasítást csak használható olyan egyetlen további feltételt kell megfelelnie.

**Példa**: ![CDN feltétel felel meg.](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > A következő szabály felülbírálhatják az előző szabályok által meghatározott műveleteket. Példa: Általános szabály bérlőkulcshoz kapcsolódó összes kérelem jogkivonat-alapú hitelesítéssel. Közvetlenül alatta kivételt bizonyos típusú kérelmet egy másik szabály lehet létrehozni.

### <a name="next-steps"></a>Következő lépések
* [Az Azure CDN áttekintése](cdn-overview.md)
* [Szabályok motor referencia](cdn-rules-engine-reference.md)
* [Szabályok motor egyezés feltételek](cdn-rules-engine-reference-match-conditions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)
