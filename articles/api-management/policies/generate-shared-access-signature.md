---
title: Az Azure API management házirend minta – közös hozzáférésű Jogosultságkód létrehozása |} A Microsoft Docs
description: Az Azure API management házirend-minta - bemutatja, hogyan hozhat létre közös hozzáférésű Jogosultságkód kifejezésekkel, és továbbítja a kérést az Azure storage-újraírási-uri-házirendet az...
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 2c3adaa6f4e113f09e676583c2c35b5f1fbdb622
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877854"
---
# <a name="generate-shared-access-signature"></a>Közös hozzáférésű Jogosultságkód létrehozása

Ez a cikk bemutatja egy Azure API management házirend minta azt mutatja be, hogyan hozhat létre [közös hozzáférésű Jogosultságkód](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) kifejezésekkel, és továbbítja a kérést az Azure storage-újraírási-uri-házirendet. Az ismertetett lépéseket követve beállíthatja, vagy szerkesztheti egy szabályzat-kódot, [Set meg vagy szerkessze a szabályzat](../set-edit-policies.md). További példák megtekintéséhez lásd: [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** letiltása.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>További lépések

További információ az APIM-szabályzatokat:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [A házirend-minták](../policy-samples.md)

