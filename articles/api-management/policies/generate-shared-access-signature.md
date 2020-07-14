---
title: Példa API Management-szabályzat – közös hozzáférésű aláírás előállítása
titleSuffix: Azure API Management
description: Azure API Management házirend-minta – bemutatja, hogyan hozhatja meg a közös hozzáférésű aláírásokat kifejezések használatával, és továbbítsa a kérést az Azure Storage-ba az újraírható URI-házirenddel.
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
ms.openlocfilehash: 62a6e8c9fdec3b30bd193e9887d7e0cb7926e73e
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243001"
---
# <a name="generate-shared-access-signature"></a>Közös hozzáférésű aláírás előállítása

Ez a cikk egy Azure API Management házirend-mintát mutat be, amely bemutatja, hogyan hozhatja meg a [közös hozzáférésű aláírást](../../storage/common/storage-sas-overview.md) kifejezések használatával, és hogyan továbbíthatja a kérést az Azure Storage-ba az újraírható URI-házirenddel. A szabályzatok beállításához vagy szerkesztéséhez kövesse a [szabályzat beállítása vagy szerkesztése](../set-edit-policies.md)című témakörben leírt lépéseket. További példákat a következő témakörben talál: [Policy Samples](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkba.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ a APIM-házirendekről:

+ [Átalakítási szabályzatok](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)
