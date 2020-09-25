---
title: Engedélyezett HITELESÍTÉSSZOLGÁLTATÓ az egyéni HTTPS engedélyezéséhez az Azure-beli bejárati ajtón
description: Ha saját tanúsítványt használ a HTTPS engedélyezéséhez egy Azure-beli bejárati 0custom tartományon, akkor a létrehozásához engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269801"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez az Azure-beli bejárati ajtón

Ha egy Azure-beli előtérben egyéni tartományt [használ, a https szolgáltatást a saját tanúsítványának használatával engedélyezi](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), a TLS/SSL-tanúsítvány létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia. Ellenkező esetben, ha nem engedélyezett HITELESÍTÉSSZOLGÁLTATÓT vagy önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
