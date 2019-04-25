---
title: Az Azure CDN szabálymotor feltételes kifejezései |} A Microsoft Docs
description: Az Azure CDN dokumentációja szabályok szabálymotor egyezési feltételei és funkciókat.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 73c41b754c0aca5ddb1a49fcd2794aa41b2fa705
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324191"
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Az Azure CDN szabálymotor feltételes kifejezései
Ez a témakör felsorolja a részletes leírását a feltételes kifejezések, az Azure Content Delivery Network (CDN) [Szabálymotorral](cdn-rules-engine.md).

A szabály első része a feltételes kifejezéssel.

Feltételes kifejezéssel | Leírás
-----------------------|-------------
HA | Egy IF kifejezés mindig egy szabályban utasításnak elsőként egy részét képezi. Mint minden más feltételes kifejezés IF nyilatkozattal egyezést társítva kell lennie. Ha nincsenek további feltételes kifejezéseket meg vannak adva, a megfelelés a szolgáltatások alkalmazandó kérelem előtt teljesítendő feltétel határozza meg.
HA | ÉS a egy IF kifejezés csak a feltételes kifejezések: Ha, és ha a következő típusú után adható hozzá. Azt jelzi, hogy nincs-e egy másik feltétel, amelyeknek teljesülniük kell a kezdeti IF utasítás.
VAGY HA| Egy vagy ha egy másik feltétel, amelyeknek teljesülniük kell, vagy ha nyilatkozattal adott szolgáltatások előtt adja meg. VAGY ha utasítás jelzi az előző utasítást végén. Csak a feltételes kifejezés, amely lehet helyezni, vagy ha egy másik utasítás egy más IF utasítás után. Ez azt jelenti, hogy egy vagy ha utasítás csak akkor használható, amely rendelkezik, amelyeknek teljesülniük kell egyetlen további feltétel megadásához.

**Példa**: ![CDN egyezési feltételei](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > A következő szabály felülbírálhatja az előző szabályok által meghatározott műveleteket. Példa: Egy kevésbé szabály összes kérelem keresztül jogkivonat-alapú hitelesítés védi. Egy másik szabály közvetlenül alá kivételt az egyes kérések hozható létre.

### <a name="next-steps"></a>További lépések
* [Az Azure CDN áttekintése](cdn-overview.md)
* [Szabályok szabálymotor referencia](cdn-rules-engine-reference.md)
* [Szabálymotor egyezési feltételei](cdn-rules-engine-reference-match-conditions.md)
* [Szabályok adatbázismotor-funkciók](cdn-rules-engine-reference-features.md)
* [A rules engine használatával a HTTP alapértelmezés felülbírálása](cdn-rules-engine.md)
