---
title: Verizon POP aktuális listájának beolvasása az Azure CDN |} Microsoft Docs
description: Ismerje meg, hogyan lehet lekérni az aktuális Verizon POP-lista a REST API használatával.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: f796d18a03e14fdf652af72366762e1365523f09
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36754554"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Az Azure CDN aktuális Verizon POP listájának beolvasása

A REST API segítségével az IP-címek készletét Verizon tartozó pont jelenlétét (POP) kiszolgálók beolvasása. Ezek a POP-kiszolgálók kéréseket származási kiszolgálókra, amelyek kapcsolódnak az Azure Content Delivery Network (CDN) végpontok Verizon profilok (**Azure CDN Standard verizon** vagy **verizonAzureCDNPremium**). Vegye figyelembe, hogy az IP-címek készlete különbözik az IP-címek, amelyek egy ügyfél jelenik meg a kapcsolódási kérelem meghozásakor. 

A REST API műveletének POP listájának lekérése a szintaxist, lásd: [peremhálózati csomópontok - lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Tipikus használati eset

Biztonsági okokból a IP listával, hogy az eredeti kiszolgálóhoz kérelmek csak a egy érvényes Verizon POP kényszerítéséhez. Például ha valaki észlelhető az állomásnév vagy IP-címet a CDN-végpontok eredeti kiszolgálóra, egy sikerült kérelmek közvetlenül az eredeti kiszolgálóra, így megkerüljék a méretezés és Azure CDN által biztosított biztonsági képességeivel. Úgy, hogy az IP-címek a visszaadott lista csak engedélyezett IP-cím forrás-kiszolgálón, ez a forgatókönyv nem történik meg. Győződjön meg arról, hogy a legújabb POP-lista, lekéréséhez naponta legalább egyszer kell rendelkeznie. 

## <a name="next-steps"></a>További lépések

A REST API kapcsolatos információkért lásd: [Azure CDN REST API](https://docs.microsoft.com/en-us/rest/api/cdn/).