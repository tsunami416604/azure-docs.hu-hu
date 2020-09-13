---
title: Megnevezett entitások a személyes adatokhoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: c50beef5c9c5dcae7edd487e8bf3d192ba557865
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662706"
---
> [!NOTE]
> A védett állapottal kapcsolatos információk (PHI) észleléséhez használja a `domain=phi` paramétert és a modell verzióját, `2020-04-01` vagy később.
>
> Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
A rendszer a következő entitás-kategóriákat adja vissza, amikor kéréseket küld a `/v3.1-preview.1/entities/recognition/pii` végpontnak.

| Kategória   | Alkategória | Description                          | Modell verziójának indítása | Jegyzetek |
|------------|-------------|--------------------------------------|------------------------|---|
| Személy     | N/A         | Személyek nevei.  | `2019-10-01`  | Szintén visszaadott `domain=phi` . |
| PersonType | N/A         | Egy személy által birtokolt feladattípusok vagy szerepkörök. | `2020-02-01` | |
| PhoneNumber | N/A | Telefonszámok (csak az USA-beli és az EU-telefonszámok). | `2019-10-01` | Szintén visszaadott `domain=phi` . |
|Szervezet  | N/A | Vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek.  | `2019-10-01` | A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban.  |
|Szervezet | Orvosi | Orvosi vállalatok és csoportok. | `2020-04-01` |  |
|Szervezet | Értéktőzsde | Tőzsdei csoportok. | `2020-04-01` |  |
| Szervezet | Sport | Sporttal kapcsolatos szervezetek. | `2020-04-01` |  |
| Cím | N/A | Teljes levelezési címek.  | `2020-04-01` | Szintén visszaadott `domain=phi` . |
| EU GPS-koordináták | N/A | GPS-koordináták az Európai Unión belüli helyszínekhez.  | `2019-10-01` |  |
| E-mail | N/A | E-mail-címek. | `2019-10-01` | Szintén visszaadott `domain=phi` .   |
| URL-cím | N/A | Webhelyek URL-címei. | `2019-10-01` | Szintén visszaadott `domain=phi` . |
| IP | N/A | Hálózati IP-címek. | `2019-10-01` | Szintén visszaadott `domain=phi` . |
| Dátum/idő | N/A | Dátum és napszakok. | `2019-10-01` |  | 
| Dátum/idő | Date | Naptári dátumok. | `2019-10-01` | Szintén visszaadott `domain=phi` . |
| Mennyiség | N/A | Számok és numerikus mennyiségek. | `2019-10-01` |  |
| Mennyiség | Életkor | Alábbi korhatárt szabja. | `2019-10-01` | | |

## <a name="azure-information"></a>Azure-információk

Az entitás kategóriája tartalmaz egy azonosítható Azure-információt, beleértve a hitelesítési adatokat és a kapcsolatok karakterláncait is. A modell verziójától kezdődően érhető el `2019-10-01` . A paraméterrel nem tért vissza `domain=phi` .

| Alkategória                           | Description                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB-hitelesítési kulcs             | Azure Cosmos DB-kiszolgáló engedélyezési kulcsa.                           |
| Azure IAAS adatbázis-kapcsolatok karakterlánca és Azure SQL-kapcsolatok karakterlánca | Az Azure infrastruktúra-szolgáltatás (IaaS) adatbázisának és az SQL-kapcsolatok karakterláncának a kapcsolatainak karakterlánca. |
| Azure SQL-kapcsolatok karakterlánca           | Azure SQL Databaseban található adatbázishoz tartozó kapcsolatok karakterlánca.                                |
| Azure IoT-kapcsolatok karakterlánca           | Az Azure IoT tartozó kapcsolatok karakterlánca.                        |
| Azure közzétételi beállítás jelszava        | Az Azure közzétételi beállításainak jelszava.                                        |
| Azure Redis Cache a kapcsolatok karakterlánca   | Redis-gyorsítótárhoz tartozó kapcsolatok karakterlánca.                             |
| Azure SAS                             | Az Azure-beli szolgáltatott szoftver (SaaS) közötti kapcsolatok karakterlánca.                     |
| Azure Service Bus a kapcsolatok karakterlánca   | Az Azure Service Bus szolgáltatáshoz tartozó kapcsolatok karakterlánca.                                 |
| Azure Storage-fiók kulcsa             | Egy Azure Storage-fiókhoz tartozó fiók kulcsa.                                   |
| Azure Storage-fiók kulcsa (általános)   | Általános fiók kulcsa egy Azure Storage-fiókhoz.                           |
| SQL Server a kapcsolatok karakterlánca          | SQL Server rendszert futtató számítógéphez tartozó kapcsolatok karakterlánca.                                         |

## <a name="identification"></a>Azonosítás

[!INCLUDE [supported identification entities](./identification-entities.md)]
