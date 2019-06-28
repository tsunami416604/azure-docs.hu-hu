---
title: Hitelesítésszolgáltatók engedélyezett az Azure CDN egyéni HTTPS engedélyezése |} A Microsoft Docs
description: Egyéni tartomány HTTPS engedélyezéséhez a saját tanúsítványt használ, ha egy engedélyezett hitelesítésszolgáltató (CA) létrehozásához kell használnia.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 8ce141fbf3d767159d16495bff5a93d791224c17
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331886"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Hitelesítésszolgáltatók engedélyezett az Azure CDN egyéni HTTPS engedélyezése

Az Azure Content Delivery Network (CDN) egyéni tartomány egy **Azure CDN Standard a Microsoft** végpont, amikor, [HTTPS szolgáltatás engedélyezése a saját tanúsítvánnyal](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), egy engedélyezett kell használnia hitelesítésszolgáltató (CA) az SSL-tanúsítvány létrehozásához. Ellenkező esetben nem engedélyezett hitelesítésszolgáltató vagy egy önaláírt tanúsítványt használ, ha a kérés rendszer elutasítja.

> [!NOTE]
> Csak érhető el a beállítást, a saját tanúsítvány használatával az egyéni HTTPS engedélyezése **Azure CDN Standard a Microsoft** profilok. 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

