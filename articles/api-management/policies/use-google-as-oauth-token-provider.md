---
title: Az Azure API management-házirendet a minta - engedélyezi a hozzáférést a Google OAuth jogkivonat használatával |} Microsoft Docs
description: Az Azure API management házirend minta - mutatja be a végpontok Google OAuth jogkivonat-szolgáltató használatával való hozzáférés engedélyezésére.
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
ms.openlocfilehash: 46ffe21b31597f889b411a40a9f3e1f2dd76f1ea
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933462"
---
# <a name="authorize-access-using-google-oauth-token"></a>Engedélyezi a hozzáférést a Google OAuth jogkivonat használatával

Ez a cikk bemutatja az Azure API management házirend minta azt mutatja be, hogyan szeretné engedélyekkel felruházni a végpontokat, Google használja, mint az OAuth jogkivonat-szolgáltató elérésére. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

