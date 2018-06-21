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
ms.openlocfilehash: 22aed976ef69288aa0e49215a739174786843527
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284589"
---
# <a name="authorize-access-using-google-oauth-token"></a>Engedélyezi a hozzáférést a Google OAuth jogkivonat használatával

Ez a cikk bemutatja az Azure API management házirend minta azt mutatja be, hogyan szeretné engedélyekkel felruházni a végpontokat, Google használja, mint az OAuth jogkivonat-szolgáltató elérésére. Beállíthatja vagy szerkesztheti a házirendet kódot, kövesse a témakörben ismertetett [beállítása és módosítása a házirend](../set-edit-policies.md). További példák, olvassa el [házirend minták](../policy-samples.md).

## <a name="policy"></a>Szabályzat

Illessze be a kódot a **bejövő** blokkot.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>További lépések

További tudnivalók APIM házirendek:

+ [Átalakítási házirendek](../api-management-transformation-policies.md)
+ [Házirend-minták](../policy-samples.md)

