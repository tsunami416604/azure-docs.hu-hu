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
ms.openlocfilehash: 34bd958260056d7a0297c4d3545d97543a363480
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344792"
---
# <a name="retrieve-the-current-verizon-pop-list-for-azure-cdn"></a>Az Azure CDN aktuális Verizon POP listájának beolvasása

A REST API segítségével az IP-címek készletét, a Verizon pont jelenléti (POP) kiszolgálók beolvasása. Ezek a jelenléti pontra Irányíthatja a kiszolgálók kéréseket küld az Azure Content Delivery Network (CDN) végpontok a Verizon profilok rendelt szerverek (**Azure CDN Standard verizon** vagy **Azure CDN Premiumhoz a Verizontól**). Vegye figyelembe, hogy az IP-címek készlete különbözik az IP-címek, amelyek ügyfél-kre történő propagálása kérések küldésekor jelennének meg. 

A szintaxist a REST API-művelet a POP-lista lekérése, lásd: [Élcsomópontok - lista](https://docs.microsoft.com/rest/api/cdn/edgenodes/edgenodes_list).

## <a name="typical-use-case"></a>Tipikus használati eset

Biztonsági okokból az IP-lista segítségével kényszerítéséhez, hogy forráskiszolgálóra kérések csak a Verizon POP érvényes. Például ha valaki észlelhető, az állomásnév vagy IP-címét a forráskiszolgáló egy CDN-végpont, egy sikerült kérést erre a forráskiszolgálóra, így megkerüli a skálázás és az Azure CDN által biztosított biztonsági funkciókkal. A visszaadott listában az IP-címek állít be, az egyetlen engedélyezett IP-címek egy forrás kiszolgálón, ez a forgatókönyv nem történik meg. Győződjön meg arról, hogy a legújabb POP-lista, beolvasása, naponta legalább egyszer kell rendelkeznie. 

## <a name="next-steps"></a>További lépések

A REST API-val kapcsolatos további információkért lásd: [Azure CDN – REST API](https://docs.microsoft.com/rest/api/cdn/).
