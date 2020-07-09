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
ms.topic: troubleshooting
ms.date: 10/18/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 29b6cb25e021e86ce6663b4db5c89217aaf70a37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84887402"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez Azure CDN

Ha a HTTPS szolgáltatást egy Azure Content Delivery Network (CDN) egyéni tartományhoz tartozó [saját tanúsítvánnyal engedélyezi](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) , akkor meg kell felelnie bizonyos tanúsítványokra vonatkozó követelményeknek. A **Microsoft profil Azure CDN szabványának** az alábbi listán szereplő jóváhagyott HITELESÍTÉSSZOLGÁLTATÓK (CA) tanúsítványát kell megadnia. Ha egy tanúsítvány nem jóváhagyott HITELESÍTÉSSZOLGÁLTATÓTÓL származik, vagy ha önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet. **Azure CDN a** Verizon és **a prémium szintű Azure CDN a Verizon** -profilokból származó, érvényes tanúsítványokat bármely érvényes hitelesítésszolgáltatótól elfogadják.

> [!NOTE]
> A saját tanúsítvány használata az egyéni tartomány HTTPS-funkciójának engedélyezéséhez *nem* érhető el **Azure CDN standard Akamai-** profilokból. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

