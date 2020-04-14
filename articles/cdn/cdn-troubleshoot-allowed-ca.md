---
title: Engedélyezett hitelesítésszolgáltató egyéni HTTPS engedélyezéséhez az Azure CDN-en
description: Ha saját tanúsítványával engedélyezi a HTTPS protokollt egy egyéni tartományban, a létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: faf51dbb1f1c3c0346b1ae9104494538efcc2ee7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259970"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Az egyéni HTTPS engedélyezése engedélyezett hitelesítésszolgáltatók az Azure CDN-en

Meg kell felelnie a speciális tanúsítványkövetelményeknek, ha engedélyezi a HTTPS-szolgáltatást az Azure Content Delivery Network (CDN) egyéni [tartománysaját tanúsítványának használatával.](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) Az **Azure CDN Standard a Microsoft-profilhoz** az alábbi listában szereplő jóváhagyott hitelesítésszolgáltatók (CA) egyikének tanúsítványát igényli. Ha egy nem jóváhagyott hitelesítésszolgáltató tól származó tanúsítványt használ, vagy önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet. **A Verizon Azure CDN Standard és** **a Verizon-profilokból származó Azure CDN Premium** bármely érvényes tanúsítványt elfogad.

> [!NOTE]
> A lehetőség, hogy a saját tanúsítványt, hogy az egyéni tartomány HTTPS-funkció *nem* érhető el az **Azure CDN Standard Akamai profilok.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

