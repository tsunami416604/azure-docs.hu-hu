---
title: Az Azure API Felügyeleti szintek szolgáltatásalapú összehasonlítása | Microsoft dokumentumok
description: Ez a cikk az API-felügyeleti szinteket az általuk kínált funkciók alapján hasonlítja össze.
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/01/2019
ms.author: apimpm
ms.openlocfilehash: 2e84138419986ef1033ab076b3745187812e91b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335885"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Az Azure API Felügyeleti szintek szolgáltatásalapú összehasonlítása

Minden API Management [tarifacsomag](https://aka.ms/apimpricing) különböző funkciókat [capacity](api-management-capacity.md)és egységkapacitást kínál. Az alábbi táblázat összefoglalja az egyes szintekben elérhető legfontosabb funkciókat. Egyes szolgáltatások eltérően működhetnek, vagy a szinttől függően eltérő képességekkel rendelkeznek. Ilyen esetekben a különbségeket az egyes szolgáltatásokat leíró dokumentációs cikkek ben szólítja fel.

> [!IMPORTANT]
> Kérjük, vegye figyelembe, hogy a fejlesztői szint nem éles használati esetekhez és értékelésekhez használható. Nem kínál SLA-t.

| Szolgáltatás                                                                                      | Használat | Fejlesztői | Basic | Standard | Prémium |
| -------------------------------------------------------------------------------------------- | ----------- | --------- | ----- | -------- | ------- |
| Azure AD-integráció<sup>1</sup>                                                             | Nem          | Igen       | Nem    | Igen      | Igen     |
| Virtuális hálózat (VNet) támogatása                                                               | Nem          | Igen       | Nem    | Nem       | Igen     |
| Többrégiós üzembe helyezés                                                                      | Nem          | Nem        | Nem    | Nem       | Igen     |
| Több egyéni tartománynév                                                                 | Nem          | Nem        | Nem    | Nem       | Igen     |
| Fejlesztői portál<sup>2</sup>                                                                 | Nem          | Igen       | Igen   | Igen      | Igen     |
| Beépített gyorsítótár                                                                               | Nem          | Igen       | Igen   | Igen      | Igen     |
| Beépített analitika                                                                           | Nem          | Igen       | Igen   | Igen      | Igen     |
| [Saját üzemeltetésű átjáró](self-hosted-gateway-overview.md)<sup>3</sup>                           | Nem          | Igen       | Nem    | Nem       | Igen     |
| [TLS-beállítások](api-management-howto-manage-protocols-ciphers.md)                             | Igen         | Igen       | Igen   | Igen      | Igen     |
| [Külső gyorsítótár](https://aka.ms/apimbyoc)                                                    | Igen         | Igen       | Igen   | Igen      | Igen     |
| [Ügyféltanúsítvány-hitelesítés](api-management-howto-mutual-certificates-for-clients.md) | Igen         | Igen       | Igen   | Igen      | Igen     |
| [Biztonsági mentés és visszaállítás](api-management-howto-disaster-recovery-backup-restore.md)               | Nem          | Igen       | Igen   | Igen      | Igen     |
| [Git kezelése](api-management-configuration-repository-git.md)                        | Nem          | Igen       | Igen   | Igen      | Igen     |
| Közvetlen felügyeleti API                                                                        | Nem          | Igen       | Igen   | Igen      | Igen     |
| Az Azure Monitor naplói és metrikái                                                               | Nem          | Igen       | Igen   | Igen      | Igen     |
| Statikus IP                                                                                    | Nem          | Igen       | Igen   | Igen      | Igen     |

<sup>1</sup> Lehetővé teszi az Azure AD (és az Azure AD B2C) használatát a fejlesztői portálon való felhasználói bejelentkezés identitásszolgáltatójaként.<br/>
<sup>2</sup> Beleértve a kapcsolódó funkciókat, pl. felhasználókat, csoportokat, problémákat, alkalmazásokat, e-mail sablonokat és értesítéseket.<br/>
<sup>3</sup> Egyetlen átjáró-telepítés egyetlen átjárócsomóval rendelkező egyetlen saját üzemeltetésű átjáró-üzembe helyezésre korlátozva.<br/>
