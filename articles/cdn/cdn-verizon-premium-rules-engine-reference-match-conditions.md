---
title: Azure CDN a Verizon Premium Rules motor Match feltételekkel | Microsoft Docs
description: Az Azure Content Delivery Network a Verizon Premium Rules motor egyeztetési feltételeit ismertető dokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: ecec7c3c5e4cc72ee637eb91033b9f2b0f158f1a
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872549"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN a Verizon Premium szabályainak motorjának egyeztetési feltételeiről

Ez a cikk részletes leírást nyújt az Azure Content Delivery Network (CDN) a Verizon Premium [Rules Engine](cdn-verizon-premium-rules-engine.md)-ről elérhető egyezési feltételeiről.

A szabály második része az egyeztetési feltétel. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez a rendszer a különböző funkciókat fogja végrehajtani.

Az egyeztetési feltételt például a következőre használhatja:

- A tartalomra vonatkozó kérelmek szűrése egy adott helyen.
- Egy adott IP-címről vagy országból/régióból generált kérelmek szűrése.
- Kérelmek szűrése fejléc-információk alapján.

A legutóbbi egyeztetési feltételekről a [Verizon Rules Engine dokumentációjában](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)talál további információt.


## <a name="next-steps"></a>További lépések

- [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
- [Szabályok motor referenciája](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [Az alapértelmezett HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
