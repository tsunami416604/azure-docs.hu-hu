---
title: "Az Azure API management házirend minta - közös hozzáférésű Jogosultságkód létrehozása |} Microsoft Docs"
description: "Az Azure API management házirend minta - bemutatja, hogyan kell létrehozni a közös hozzáférésű Jogosultságkód kifejezésekkel, és továbbítja a kérést az átírást-uri házirendek az Azure storage..."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e1f17f9f4e17a3eebb55e4ec1905aec19a2165a5
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="generate-shared-access-signature"></a>Közös hozzáférésű Jogosultságkód létrehozása

Ez a cikk bemutatja az Azure API management házirend minta, amely bemutatja, hogyan kell létrehozni [közös hozzáférésű Jogosultságkód](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1) kifejezésekkel, és továbbítja a kérelmet az Azure storage az átírást-uri-házirendet. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Következő lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

