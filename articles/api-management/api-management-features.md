---
title: Az Azure API Management szintjeinek szolgáltatáson alapuló összehasonlítása | Microsoft Docs
description: Ez a cikk az általuk kínált funkciók alapján összehasonlítja API Managementi szinteket.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: bc5eab6bb5044e474c386d3e5771d8517e6ba9cf
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072533"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Az Azure API Management szintjeinek szolgáltatáson alapuló összehasonlítása

>[!IMPORTANT]
> Vegye figyelembe, hogy a fejlesztői réteg nem üzemi célú használati esetekre és értékelésekre szolgál. Nem biztosít SLA-t. 

Az egyes API Management [díjszabási szintjei](https://aka.ms/apimpricing) különböző szolgáltatásokat és egységnyi kapacitást kínálnak [](api-management-capacity.md). A következő táblázat összefoglalja az egyes szintjein elérhető főbb funkciókat. Egyes funkciók eltérően működhetnek, vagy a szinttől függően különböző képességekkel rendelkezhetnek. Ilyen esetekben a különbségeket az ezeket az egyéni funkciókat ismertető dokumentációs cikkekben nevezzük.

| Funkció                                                                                      | Használat | Fejlesztő      | Alapszintű          | Standard       | Prémium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Azure AD-integráció<sup>1</sup>                                                             | Nem                            | Igen            | Nem             | Igen            | Igen            |
| Virtual Network (VNet) támogatása                                                               | Nem                            | Igen            | Nem             | Nem             | Igen            |
| Többrégiós üzembe helyezés                                                                      | Nem                            | Nem             | Nem             | Nem             | Igen            |
| Több egyéni tartománynév                                                                 | Nem                            | Nem             | Nem             | Nem             | Igen            |
| Fejlesztői portál<sup>2</sup>                                                                 | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Beépített gyorsítótár                                                                               | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Beépített Analitika                                                                           | Nem                            | Igen            | Igen            | Igen            | Igen            |
| [SSL-beállítások](api-management-howto-manage-protocols-ciphers.md)                             | Igen                            | Igen            | Igen            | Igen            | Igen            |
| [Külső gyorsítótár](https://aka.ms/apimbyoc)                                                    | Igen                           | Igen            | Igen            | Igen            | Igen            |
| [Ügyféltanúsítvány-alapú hitelesítés](api-management-howto-mutual-certificates-for-clients.md) | Igen                | Igen            | Igen            | Igen            | Igen            |
| [Biztonsági mentés és visszaállítás](api-management-howto-disaster-recovery-backup-restore.md)               | Nem                            | Igen            | Igen            | Igen            | Igen            |
| [Felügyelet a git felett](api-management-configuration-repository-git.md)                        | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Közvetlen felügyeleti API                                                                        | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Naplók és mérőszámok Azure Monitor                                                               | Nem                | Igen            | Igen            | Igen            | Igen            |
| Statikus IP-cím                                                               | Nem                | Igen            | Igen            | Igen            | Igen            |

<sup>1</sup> lehetővé teszi az Azure ad (és Azure ad B2C) identitás-szolgáltatóként való használatát a fejlesztői portálon való felhasználói bejelentkezéshez.<br/>
<sup>2</sup> beleértve a kapcsolódó funkciókat, például a felhasználókat, a csoportokat, a problémákat, az alkalmazásokat és az e-mail sablonokat és az értesítéseket.<br/>
