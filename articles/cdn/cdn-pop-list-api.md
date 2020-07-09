---
title: 'Az aktuális POP IP-lista beolvasása a következőhöz: Azure CDN | Microsoft Docs'
description: Megtudhatja, hogyan kérheti le az aktuális POP-listát.
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
ms.openlocfilehash: 7b98bb446fc400007e4061d09db8084960702943
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668287"
---
# <a name="retrieve-the-current-pop-ip-list-for-azure-cdn"></a>A Azure CDN aktuális POP IP-listájának beolvasása

## <a name="retrieve-the-current-verizon-pop-ip-list-for-azure-cdn"></a>A jelenlegi Verizon POP IP-lista lekérése a Azure CDN

A REST API segítségével lekérheti az IP-címek készletét a Verizon jelenléti pontjának (POP) kiszolgálóihoz. Ezek a POP-kiszolgálók kérelmeket küldenek az Azure Content Delivery Network-(CDN-) végpontokkal társított forrás-kiszolgálóknak a Verizon-profilokon**Azure CDN** (a verizontól vagy a **Azure CDN Premiumtól a verizontól**). Vegye figyelembe, hogy ez az IP-címek különböznek azoktól az IP-címektől, amelyeket az ügyfél a pop-kérelmek küldésekor lát. 

A POP-lista lekérésére szolgáló REST API művelet szintaxisát lásd: [Edge Nodes-List](https://docs.microsoft.com/rest/api/cdn/edgenodes/list).

## <a name="retrieve-the-current-microsoft-pop-ip-list-for-azure-cdn"></a>Az aktuális Microsoft POP IP-lista beolvasása a Azure CDN

Ha úgy szeretné zárolni az alkalmazást, hogy csak a Microsofttól származó Azure CDN fogadjon forgalmat, be kell állítania a háttér IP-ACL-jeit. A Microsofttól Azure CDN által küldött "X-Forwarded-host" fejlécre vonatkozóan is korlátozhatja az elfogadott értékek készletét. Ezeket a lépéseket az alábbiak szerint részletezjük:

Konfigurálja az IP-hozzáférés a háttérrendszer számára, hogy fogadja a forgalmat a Microsoft háttérbeli IP-címtartomány és az Azure infrastruktúra-szolgáltatásai Azure CDNból. 

* Azure CDN a Microsoft IPv4-háttér IP-területéről: 147.243.0.0/16
* A Microsoft IPv6-háttér IP-címének Azure CDNe: 2a01:111:2050::/44

Ha Azure CDN a Microsofttól szeretné használni a szolgáltatás címkéit, használja az Azure-beli bejárati ajtó címkéjét. Az IP-címtartományok és a Microsoft-szolgáltatásokhoz tartozó szolgáltatási címkék [itt](https://www.microsoft.com/download/details.aspx?id=56519) találhatók


## <a name="typical-use-case"></a>Tipikus használati eset

Biztonsági okokból ezt az IP-listát használhatja arra, hogy kikényszerítse, hogy a forráskiszolgáló iránti kérések csak egy érvényes Verizon-POP-ból legyenek elvégezve. Ha például valaki felderíti a CDN-végpont forrás-kiszolgálójának állomásnevét vagy IP-címét, akkor a kérést közvetlenül a forráskiszolgáló számára teheti meg, így megkerüli a Azure CDN által biztosított méretezési és biztonsági képességeket. Ha úgy állítja be az IP-címeket a visszaadott listában, hogy az csak az engedélyezett IP-címek legyenek a forráskiszolgálón, akkor ez a forgatókönyv megelőzhető. Győződjön meg arról, hogy rendelkezik a legújabb POP-listával, és legalább egyszer lekéri azt. 

## <a name="next-steps"></a>További lépések

További információ a REST APIről: [Azure CDN REST API](https://docs.microsoft.com/rest/api/cdn/).
