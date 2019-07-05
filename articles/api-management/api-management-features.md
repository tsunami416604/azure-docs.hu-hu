---
title: Az Azure API Management csomagjairól szolgáltatásalapú összehasonlítása |} A Microsoft Docs
description: Ez a cikk összehasonlítja az API Management csomagjairól az eddigieknél szolgáltatások alapján.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: a57f8e44d19432f82abe4fa5e7bafce900db3394
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448016"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Az Azure API Management csomagjairól szolgáltatásalapú összehasonlítása

Minden API Management [tarifacsomag](https://aka.ms/apimpricing) kínál különböző funkcióit és egységenként [kapacitás](api-management-capacity.md). Az alábbi táblázat foglalja össze az egyes rétegek a legfontosabb funkcióit. Előfordulhat, hogy egyes funkciók eltérően működik, vagy a csomagtól függően különböző képességekkel rendelkeznek. Ebben az esetben a különbségek hívjuk a dokumentációba leíró egyedi ezeket a funkciókat.

| Funkció                                                                                      | Használat | Fejlesztői      | Alapszintű          | Standard       | Prémium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Az Azure AD-integrációs<sup>1</sup>                                                             | Nem                            | Igen            | Nem             | Igen            | Igen            |
| Virtual Network (VNet) támogatása                                                               | Nem                            | Igen            | Nem             | Nem             | Igen            |
| Többrégiós üzembe helyezés                                                                      | Nem                            | Nem             | Nem             | Nem             | Igen            |
| Több egyéni tartománynév                                                                 | Nem                            | Nem             | Nem             | Nem             | Igen            |
| Fejlesztői portál<sup>2</sup>                                                                 | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Beépített gyorsítótár                                                                               | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Beépített elemzési                                                                           | Nem                            | Igen            | Igen            | Igen            | Igen            |
| [Az SSL-beállítások](api-management-howto-manage-protocols-ciphers.md)                             | Igen                            | Igen            | Igen            | Igen            | Igen            |
| [Külső gyorsítótár](https://aka.ms/apimbyoc)                                                    | Igen                           | Igen            | Igen            | Igen            | Igen            |
| [Ügyféltanúsítvány-alapú hitelesítés](api-management-howto-mutual-certificates-for-clients.md) | Igen                | Igen            | Igen            | Igen            | Igen            |
| [Biztonsági mentés és visszaállítás](api-management-howto-disaster-recovery-backup-restore.md)               | Nem                            | Igen            | Igen            | Igen            | Igen            |
| [Git-felügyeletet](api-management-configuration-repository-git.md)                        | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Közvetlen felügyelet API                                                                        | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Az Azure Monitor-naplók és mérőszámok                                                               | Nem                | Igen            | Igen            | Igen            | Igen            |
| Statikus IP-cím                                                               | Nem                | Igen            | Igen            | Igen            | Igen            |

<sup>1</sup> lehetővé teszi az Azure ad-ben használható (és az Azure AD B2C-t) egy identitás-szolgáltatója felhasználó jelentkezzen be a fejlesztői portálon.<br/>
<sup>2</sup> kapcsolódó funkciókat többek között például felhasználók, csoportok, problémák, alkalmazások és az e-mail-sablonok és értesítések.<br/>
