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
ms.openlocfilehash: eae36aa6e60e3da03c59952a1d9e035e6a773d2d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156697"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Az Azure API Management csomagjairól szolgáltatásalapú összehasonlítása

Minden API Management [tarifacsomag](https://aka.ms/apimpricing) kínál különböző funkcióit és egységenként [kapacitás](api-management-capacity.md). Az alábbi táblázat foglalja össze az egyes rétegek a legfontosabb funkcióit. Előfordulhat, hogy egyes funkciók eltérően működik, vagy a csomagtól függően különböző képességekkel rendelkeznek. Ebben az esetben a különbségek hívjuk a dokumentációba leíró egyedi ezeket a funkciókat.

| Szolgáltatás                                                                                      | Használatalapú<sup>előzetes verzió</sup> | Fejlesztői      | Alapszintű          | Standard       | Prémium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Az Azure AD-integrációs<sup>1</sup>                                                             | Nem                            | Igen            | Nem             | Igen            | Igen            |
| Virtual Network (VNet) támogatása                                                               | Nem                            | Igen            | Nem             | Nem             | Igen            |
| Többrégiós üzembe helyezés                                                                      | Nem                            | Nem             | Nem             | Nem             | Igen            |
| Több egyéni tartománynév                                                                 | Nem                            | Nem             | Nem             | Nem             | Igen            |
| Fejlesztői portál<sup>2</sup>                                                                 | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Beépített gyorsítótár                                                                               | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Beépített elemzési                                                                           | Nem                            | Igen            | Igen            | Igen            | Igen            |
| [Az SSL-beállítások](api-management-howto-manage-protocols-ciphers.md)                             | Nem                            | Igen            | Igen            | Igen            | Igen            |
| [Külső gyorsítótár](https://aka.ms/apimbyoc)                                                    | Igen                           | Nem<sup>3</sup> | Nem<sup>3</sup> | Nem<sup>3</sup> | Nem<sup>3</sup> |
| [Ügyféltanúsítvány-alapú hitelesítés](api-management-howto-mutual-certificates-for-clients.md) | Nem<sup>4</sup>                | Igen            | Igen            | Igen            | Igen            |
| [Biztonsági mentés és visszaállítás](api-management-howto-disaster-recovery-backup-restore.md)               | Nem                            | Igen            | Igen            | Igen            | Igen            |
| [Git-felügyeletet](api-management-configuration-repository-git.md)                        | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Közvetlen felügyelet API                                                                        | Nem                            | Igen            | Igen            | Igen            | Igen            |
| Az Azure Monitor-naplók és mérőszámok                                                               | Nem<sup>5</sup>                | Igen            | Igen            | Igen            | Igen            |

<sup>1</sup> lehetővé teszi az Azure ad-ben használható (és az Azure AD B2C-t) a felhasználó bejelentkezik a fejlesztői portálon Identitásszolgáltatóként.<br/>
<sup>2</sup> kapcsolódó funkciókat többek között például felhasználók, csoportok, problémák, alkalmazások és az e-mail-sablonok és értesítések.<br/>
<sup>3</sup> ezen a szinten külső gyorsítótár támogatása hamarosan elérhető lesz.<br/>
<sup>4</sup> ügyféltanúsítvány-alapú hitelesítés bekerül a Használatalapú csomag az általános rendelkezésre állás előtt.<br/>
<sup>5</sup> teljes Azure Monitor fogja támogatni a fogyasztás szintre.
