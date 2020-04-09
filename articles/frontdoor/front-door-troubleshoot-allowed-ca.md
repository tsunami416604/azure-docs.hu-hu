---
title: Engedélyezett hitelesítésszolgáltató egyéni HTTPS engedélyezéséhez az Azure Bejárati ajtaján
description: Ha saját tanúsítványával engedélyezi a HTTPS protokollt egy egyéni tartományban, a létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874670"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez az Azure Bejárati ajtaján

Az Azure Bejárati ajtó egyéni tartomány, ha [engedélyezi a HTTPS-szolgáltatás segítségével a saját tanúsítványt,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)meg kell használnia egy engedélyezett hitelesítésszolgáltató (CA) a TLS/SSL-tanúsítvány létrehozásához. Ellenkező esetben, ha nem engedélyezett hitelesítésszolgáltatót vagy önaláírt tanúsítványt használ, a kérelmet a rendszer elutasítja.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
