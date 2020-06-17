---
title: Megnevezett entitások a személyes adatokhoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/30/2020
ms.author: aahi
ms.openlocfilehash: 637c2bb1bc37d26dcdf1a169b2f5d20766d0a27f
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84902504"
---
> [!NOTE]
> Az észleléshez `PHI` használja a `domain=phi` paramétert és a modell verzióját, `2020-04-01` vagy később.
>
> Például:`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
A rendszer a következő entitás-kategóriákat adja vissza a végpontra irányuló kérelmek küldésekor `/v3.1-preview.1/entities/recognition/pii` .

| Kategória   | Subcategory | Leírás                          | Modell verziójának indítása | Megjegyzések |
|------------|-------------|--------------------------------------|------------------------|---|
| Személy     | N.A.         | Személyek nevei.  | `2019-10-01`  | Szintén visszaadott `domain=phi` . |
| PersonType | N.A.         | Egy személy által birtokolt feladattípusok vagy szerepkörök. | `2020-02-01` | |
| PhoneNumber | N.A. | Telefonszámok (csak az USA-beli és az EU-telefonszámok). | `2019-10-01` | Visszaadott érték`domain=phi` |
|Szervezet  | N.A. | Vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek.  | `2019-10-01` | A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban.  |
|Szervezet | Orvosi | Orvosi vállalatok és csoportok. | `2020-04-01` | Szintén visszaadott `domain=phi` . |
|Szervezet | Értéktőzsde | Tőzsdei csoportok. | `2020-04-01` | Szintén visszaadott `domain=phi` . |
| Szervezet | Sport | Sporttal kapcsolatos szervezetek. | `2020-04-01` | Szintén visszaadott `domain=phi` . |
| Cím | N.A. | Teljes levelezési címek.  | `2020-04-01` | Szintén visszaadott `domain=phi` . |
| EU GPS-koordináták | N.A. | GPS-koordináták az Európai Unión belüli helyszínekhez.  | `2019-10-01` |  |
| E-mail | N.A. | E-mail-címek. | `2019-10-01` | Szintén visszaadott `domain=phi` .   |
| URL-cím | N.A. | Webhelyek URL-címei. | `2019-10-01` | Szintén visszaadott `domain=phi` . |
| IP | N.A. | Hálózati IP-címek. | `2019-10-01` | |
| DateTime | N.A. | Dátum és napszakok. | `2019-10-01` |  | 
| DateTime | Dátum | Naptári dátumok. | `2019-10-01` | Szintén visszaadott `domain=phi` . |
| Mennyiség | N.A. | Számok és numerikus mennyiségek. | `2019-10-01` |  |
| Mennyiség | Kor | Alábbi korhatárt szabja. | `2019-10-01` | | |
| Betegségek nemzetközi osztályozása (ICD-9-CM) | N.A. | A betegségek nemzetközi besorolásával, a kilencedik változattal kapcsolatos entitások.   | `2020-04-01` | |
| Betegségek nemzetközi osztályozása (ICD-10-CM) | N.A. | A betegségek nemzetközi besorolásával, tizedik változatával kapcsolatos entitások.    | `2020-04-01` | |

## <a name="azure-information"></a>Azure-információk

Az entitás kategóriája tartalmaz egy azonosítható Azure-információt, beleértve a hitelesítési adatokat és a kapcsolatok karakterláncait. A modell verziójától kezdődően érhető el `2019-10-01` . A paraméterrel nem tért vissza `domain=phi` .

| Subcategory                           | Leírás                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB-hitelesítési kulcs             | Egy Azure DocumentDB-kiszolgáló engedélyezési kulcsa.                           |
| Azure IAAS adatbázis-kapcsolatok karakterlánca és Azure SQL-kapcsolatok karakterlánca | Az Azure infrastruktúra-szolgáltatás (IaaS) adatbázisának és az SQL-kapcsolatok karakterláncának a kapcsolatainak karakterlánca. |
| Azure SQL-kapcsolatok karakterlánca           | Az Azure SQL Database-adatbázishoz tartozó kapcsolatok karakterlánca.                                |
| Azure IoT-kapcsolatok karakterlánca           | Az Azure Internet of Things (IoT) szolgáltatáshoz tartozó kapcsolatok karakterlánca.                        |
| Azure közzétételi beállítás jelszava        | Az Azure közzétételi beállításainak jelszava.                                        |
| Azure Redis Cache a kapcsolatok karakterlánca   | A Redis tartozó Azure cache-hez tartozó kapcsolatok karakterlánca.                             |
| Azure SAS                             | Az Azure-beli szoftver (SAS) szolgáltatáshoz tartozó kapcsolatok karakterlánca.                     |
| Azure Service Bus a kapcsolatok karakterlánca   | Az Azure Service Bus szolgáltatáshoz tartozó kapcsolatok karakterlánca.                                 |
| Azure Storage-fiók kulcsa             | Egy Azure Storage-fiókhoz tartozó fiók kulcsa.                                   |
| Azure Storage-fiók kulcsa (általános)   | Általános fiók kulcsa egy Azure Storage-fiókhoz.                           |
| SQL Server a kapcsolatok karakterlánca          | Az SQL Serverhez tartozó kapcsolatok karakterlánca.                                         |

## <a name="identification"></a>Azonosítás

[!INCLUDE [supported identification entities](./identification-entities.md)]
