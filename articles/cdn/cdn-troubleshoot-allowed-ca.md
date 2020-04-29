---
title: Engedélyezett HITELESÍTÉSSZOLGÁLTATÓ az egyéni HTTPS engedélyezéséhez Azure CDN
description: Ha saját tanúsítványt használ a HTTPS engedélyezéséhez egy egyéni tartományon, a létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259970"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez Azure CDN

Ha a HTTPS szolgáltatást egy Azure Content Delivery Network (CDN) egyéni tartományhoz tartozó [saját tanúsítvánnyal engedélyezi](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) , akkor meg kell felelnie bizonyos tanúsítványokra vonatkozó követelményeknek. A **Microsoft profil Azure CDN szabványának** az alábbi listán szereplő jóváhagyott HITELESÍTÉSSZOLGÁLTATÓK (CA) tanúsítványát kell megadnia. Ha egy tanúsítvány nem jóváhagyott HITELESÍTÉSSZOLGÁLTATÓTÓL származik, vagy ha önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet. **Azure CDN a** Verizon és **a prémium szintű Azure CDN a Verizon** -profilokból származó, érvényes tanúsítványokat bármely érvényes hitelesítésszolgáltatótól elfogadják.

> [!NOTE]
> A saját tanúsítvány használata az egyéni tartomány HTTPS-funkciójának engedélyezéséhez *nem* érhető el **Azure CDN standard Akamai-** profilokból. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

