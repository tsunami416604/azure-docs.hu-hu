---
title: Az Azure API management házirend minta - közös hozzáférésű Jogosultságkód létrehozása |} Microsoft Docs
description: Az Azure API management házirend minta - bemutatja, hogyan kell létrehozni a közös hozzáférésű Jogosultságkód kifejezésekkel, és továbbítja a kérést az átírást-uri házirendek az Azure storage...
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
ms.openlocfilehash: fadad6f83f1a855f3ca1509a6a5e02c822db512d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="generate-shared-access-signature"></a>Közös hozzáférésű Jogosultságkód létrehozása

Ez a cikk bemutatja az Azure API management házirend minta, amely bemutatja, hogyan kell létrehozni [közös hozzáférésű Jogosultságkód](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) kifejezésekkel, és továbbítja a kérelmet az Azure storage az átírást-uri-házirendet. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

