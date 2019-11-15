---
title: Engedélyezett HITELESÍTÉSSZOLGÁLTATÓ az egyéni HTTPS engedélyezéséhez Azure CDN
description: Ha saját tanúsítványt használ a HTTPS engedélyezéséhez egy egyéni tartományon, a létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5462502514a3e327913122fe99fd699856891216
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083100"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez Azure CDN

Ha egy Azure Content Delivery Network (CDN) egyéni tartományra van szüksége egy, a **Microsoft-végponton található Azure CDN standardban** , akkor ha [a https-szolgáltatást saját tanúsítvány használatával engedélyezi](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), akkor az SSL-tanúsítvány létrehozásához egy engedélyezett hitelesítésszolgáltatót (CA) kell használnia. Ellenkező esetben, ha nem engedélyezett HITELESÍTÉSSZOLGÁLTATÓT vagy önaláírt tanúsítványt használ, a rendszer elutasítja a kérelmet.

> [!NOTE]
> Ha a saját tanúsítványát használja az egyéni HTTPS engedélyezéséhez, csak a **Microsoft-profilokból származó Azure CDN szabvány** érhető el. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

