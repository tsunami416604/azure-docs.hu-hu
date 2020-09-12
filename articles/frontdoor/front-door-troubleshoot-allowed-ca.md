---
title: Engedélyezett HITELESÍTÉSSZOLGÁLTATÓ az egyéni HTTPS engedélyezéséhez az Azure-beli bejárati ajtón
description: Ha saját tanúsítványt használ a HTTPS engedélyezéséhez egy egyéni tartományon, a létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
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
ms.openlocfilehash: 7bdef37561687b49b030d8237472c0d35f945c13
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399123"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez az Azure-beli bejárati ajtón

Ha egy Azure-beli előtérben egyéni tartományt [használ, a https szolgáltatást a saját tanúsítványának használatával engedélyezi](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), a TLS/SSL-tanúsítvány létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia. Ellenkező esetben, ha nem engedélyezett HITELESÍTÉSSZOLGÁLTATÓT vagy önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
