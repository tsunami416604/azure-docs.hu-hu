---
title: Minta API-kezelési házirend – Ip-cím szűrése az Application Gateway használatakor
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan szűrheti kérésre AZ IP-cím, ha egy alkalmazásátjáró használatakor.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75942476"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Alkalmazásátjáró használatakor kérésre az IP-cím szűrése

Ez a cikk egy Azure API-felügyeleti szabályzat mintát mutat be, amely bemutatja, hogyan szűrheti a kérelem IP-címét, amikor az API-felügyeleti példány egy application gateway vagy más közvetítő keresztül érhető el. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Hozzáférési korlátozásokra vonatkozó házirendek](../api-management-access-restriction-policies.md)
+ [Házirendminták](../policy-samples.md)
