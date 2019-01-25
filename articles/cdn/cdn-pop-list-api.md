---
title: Az aktuális Verizon POP listájának letöltése az Azure CDN |} A Microsoft Docs
description: Ismerje meg, hogyan kérheti le az aktuális Verizon POP-listát a REST API-val.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 2b34f6bcbf37a48fb49a2a64d727fc9330b0d735
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54882256"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Az Azure CDN aktuális Verizon POP listájának beolvasása

A REST API segítségével az IP-címek készletét, a Verizon pont jelenléti (POP) kiszolgálók beolvasása. Ezek a jelenléti pontra Irányíthatja a kiszolgálók kéréseket küld az Azure Content Delivery Network (CDN) végpontok a Verizon profilok rendelt szerverek (**Azure CDN Standard verizon** vagy **Azure CDN Premiumhoz a Verizontól**). Vegye figyelembe, hogy az IP-címek készlete különbözik az IP-címek, amelyek ügyfél-kre történő propagálása kérések küldésekor jelennének meg. 

A szintaxist a REST API-művelet a POP-lista lekérése, lásd: [Élcsomópontok - lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="typical-use-case"></a>Tipikus használati eset

Biztonsági okokból az IP-lista segítségével kényszerítéséhez, hogy forráskiszolgálóra kérések csak a Verizon POP érvényes. Például ha valaki észlelhető, az állomásnév vagy IP-címét a forráskiszolgáló egy CDN-végpont, egy sikerült kérést erre a forráskiszolgálóra, így megkerüli a skálázás és az Azure CDN által biztosított biztonsági funkciókkal. A visszaadott listában az IP-címek állít be, az egyetlen engedélyezett IP-címek egy forrás kiszolgálón, ez a forgatókönyv nem történik meg. Győződjön meg arról, hogy rendelkezik-e a legújabb POP-lista, lekérésére, naponta legalább egyszer. 

## <a name="next-steps"></a>További lépések

A REST API-val kapcsolatos további információkért lásd: [Azure CDN – REST API](https://docs.microsoft.com/rest/api/cdn/).
