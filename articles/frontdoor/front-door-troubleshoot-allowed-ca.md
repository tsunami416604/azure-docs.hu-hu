---
title: Hitelesítésszolgáltatók engedélyezett az Azure bejárati ajtajának Service egyéni HTTPS engedélyezése |} A Microsoft Docs
description: Egyéni tartomány HTTPS engedélyezéséhez a saját tanúsítványt használ, ha egy engedélyezett hitelesítésszolgáltató (CA) létrehozásához kell használnia.
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
ms.openlocfilehash: e2bab9f9e1ae099952b34e66f7250e7ecbc8e689
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330750"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Hitelesítésszolgáltatók engedélyezett az Azure bejárati ajtajának Service egyéni HTTPS engedélyezése

Azure bejárati ajtajának Service egyéni tartomány amikor Ön [HTTPS szolgáltatás engedélyezése a saját tanúsítvánnyal](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), engedélyezett hitelesítésszolgáltató (CA) kell használnia az SSL-tanúsítvány létrehozásához. Ellenkező esetben nem engedélyezett hitelesítésszolgáltató vagy egy önaláírt tanúsítványt használ, ha a kérés rendszer elutasítja.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
