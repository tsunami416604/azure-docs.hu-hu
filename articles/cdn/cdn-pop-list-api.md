---
title: Az Azure CDN- aktuális POP-IP-listájának lekérése Microsoft dokumentumok
description: További információ az aktuális POP-lista beolvasásáról.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: a58fd7c6f50cd46ac3c34cd7e5bd329c0007e5f6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260190"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>Az Azure CDN aktuális POP-IP-listájának lekérése

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>Az Azure CDN aktuális Verizon POP IP-listájának lekérése

A REST API segítségével lekérheti a Verizon jelenléti pont (POP) kiszolgálóinak IP-kiszolgálókészletét. Ezek a POP-kiszolgálók az Azure Content Delivery Network (CDN) végpontjaihoz társított forráskiszolgálókra kérnek egy Verizon-profilon **(Azure CDN Standard a Verizontól** vagy **az Azure CDN Premium a Verizontól).** Vegye figyelembe, hogy ez az IP-k eltér az IP-k, hogy az ügyfél látna, amikor a kérelmeket a POP-ok. 

A REST API-művelet szintaxisát a POP-lista beolvasásához lásd: [Edge Nodes - List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Az Azure CDN aktuális Microsoft POP IP-listájának lekérése

Az alkalmazás zárolásához csak a Microsoft Azure CDN-ből származó forgalom fogadásához be kell állítania az IP-aktil-címeket a háttérrendszerhez. Korlátozhatja az Azure CDN által a Microsofttól küldött "X-Forwarded-Host" fejléc elfogadott értékkészletét is. Ezeket a lépéseket az alábbiak szerint részletezzük:

Konfigurálja az IP-acling-et a háttérrendszerekszámára, hogy fogadja az Azure CDN-ből érkező forgalmat a Microsoft háttér-IP-címteréből és csak az Azure infrastruktúra-szolgáltatásaiból. 

* Azure CDN a Microsoft IPv4 háttérfelhasználói ip-területéről: 147.243.0.0/16
* Azure CDN a Microsoft IPv6-háttérfelhasználói területéről: 2a01:111:2050::/44

A Microsoft-szolgáltatások IP-tartományai és szolgáltatási címkéi [itt](https://www.microsoft.com/download/details.aspx?id=56519) találhatók


## <a name="typical-use-case"></a>Tipikus használati eset

Biztonsági okokból ezzel az IP-listával kényszerítheti, hogy a forráskiszolgálóra irányuló kérések csak érvényes Verizon POP-ból érkeznek. Ha például valaki egy CDN-végpont kezdőkiszolgálójának állomásnevét vagy IP-címét fedezte fel, közvetlenül az eredeti kiszolgálóra kérhet, így megkerülheti az Azure CDN által biztosított méretezési és biztonsági lehetőségeket. Ha a visszaadott listában az IP-eket állítja be az egyetlen engedélyezett IP-kiszolgálóként, ez a forgatókönyv megelőzhető. Annak érdekében, hogy a legújabb POP-listával rendelkezzen, naponta legalább egyszer olvassa be. 

## <a name="next-steps"></a>További lépések

A REST API-ról az [Azure CDN REST API című témakörben](https://docs.microsoft.com/rest/api/cdn/)talál további információt.
