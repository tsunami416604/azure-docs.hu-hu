---
title: Példa API-kezelési házirend – Megosztott hozzáférési aláírás létrehozása
titleSuffix: Azure API Management
description: Azure API-kezelési szabályzat minta – bemutatja, hogyan hozhat létre megosztott hozzáférésű aláírás kifejezések használatával, és továbbítja a kérelmet az Azure storage-ban átírás-uri szabályzat..
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 0f003bc268af6b7f8bd6b046ae84734dbefeac28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442456"
---
# <a name="generate-shared-access-signature"></a>Megosztott hozzáférési aláírás létrehozása

Ez a cikk egy Azure API-felügyeleti házirend-mintát mutat be, amely bemutatja, hogyan hozhat létre [megosztott hozzáférésű aláírás](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) kifejezések használatával, és továbbíthatja a kérelmet az Azure Storage-nak az újraírás-uri szabályzattal. Házirendkód beállításához vagy szerkesztéséhez kövesse a [Házirend beállítása vagy szerkesztése](../set-edit-policies.md)című részben ismertetett lépéseket. További példák megtekintéséhez tekintse meg [a házirendmintákat.](../policy-samples.md)

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirendminták](../policy-samples.md)

