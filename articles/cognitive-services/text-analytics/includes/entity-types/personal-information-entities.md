---
title: Megnevezett entitások a személyes adatokhoz
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/21/2019
ms.author: aahi
ms.openlocfilehash: 3aa4da9a9cf3d1d4b664e81f1fd18f2b225d731d
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799906"
---
## <a name="personal-information-entity-types"></a>Személyes információ típusú entitások típusai:

### <a name="phone-number"></a>Telefonszám

Telefonszámok. 

Nyelvek

* Nyilvános előzetes verzió: `English`

| Altípus neve           | Leírás                                           |
|------------------------|-------------------------------------------------------|
| N/A                    | Telefonszámok, például `+1 123-123-123`.          |
| EU-telefonszám        | Az Európai Unióra jellemző telefonszámok.         |
| EU mobil telefonszáma | Az Európai Unióra jellemző mobiltelefon-telefonszámok. |

### <a name="eu-gps-coordinates"></a>EU GPS-koordináták

 GPS-koordináták az Európai Unión belüli helyszínekhez. 

Nyelvek

* Nyilvános előzetes verzió: `English`

| Altípus neve | Leírás                               |
|--------------|-------------------------------------------|
| N/A          | GPS-koordináták az Európai Unión belül |

### <a name="azure-information"></a>Azure-információk

Azonosítható Azure-információk, beleértve a hitelesítési adatokat és a kapcsolatok karakterláncait. 

Nyelvek

* Nyilvános előzetes verzió: `English`

| Altípus neve                          | Leírás                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB-hitelesítési kulcs             | Egy Azure DocumentDB-kiszolgáló engedélyezési kulcsa.                           |
| Azure IAAS adatbázis-kapcsolatok karakterlánca | Az Azure infrastruktúra-szolgáltatás (IaaS) adatbázisához tartozó kapcsolatok karakterlánca. |
| Azure SQL-kapcsolatok karakterlánca           | Az Azure SQL Database-adatbázishoz tartozó kapcsolatok karakterlánca.                                |
| Azure IoT-kapcsolatok karakterlánca           | Az Azure Internet of Things (IoT) szolgáltatáshoz tartozó kapcsolatok karakterlánca.                        |
| Azure közzétételi beállítás jelszava        | Az Azure közzétételi beállításainak jelszava.                                        |
| Azure Redis Cache a kapcsolatok karakterlánca   | A Redis tartozó Azure cache-hez tartozó kapcsolatok karakterlánca.                             |
| Azure SAS                             | Az Azure-beli szoftver (SAS) szolgáltatáshoz tartozó kapcsolatok karakterlánca.                     |
| Azure Service Bus a kapcsolatok karakterlánca   | Az Azure Service Bus szolgáltatáshoz tartozó kapcsolatok karakterlánca.                                |
| Azure Storage-fiók kulcsa             | Egy Azure Storage-fiókhoz tartozó fiók kulcsa.                                   |
| Azure Storage-fiók kulcsa (általános)   | Általános fiók kulcsa egy Azure Storage-fiókhoz.                           |
| SQL Server a kapcsolatok karakterlánca          | Az SQL Serverhez tartozó kapcsolatok karakterlánca.                                         |

### <a name="identification"></a>Szoftverazonosító

Nyelvek

* Nyilvános előzetes verzió: `English`

#### <a name="financial-account-identification"></a>Pénzügyi fiók azonosítása

| Altípus neve               | Leírás                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA-útválasztási számok        | American Banker Association (ABA) tranzit-útválasztási számok.                  |
| SWIFT-kód                 | SWIFT-kódok a fizetési utasítással kapcsolatos információkhoz.                           |
| Hitelkártya                | Hitelkártya-számok.                                                       |
| IBAN-kód                  | IBAN-kódok a fizetési utasítással kapcsolatos információkhoz.                            |

#### <a name="government-and-country-specific-identification"></a>Kormányzati és országspecifikus azonosítás

Az alábbi entitások ország szerint vannak csoportosítva és listázva:

Argentína
* Nemzeti identitás (DNI) száma

Ausztrália
* Adó fájl száma 
* Illesztőprogram licenc-azonosítója
* Passport-azonosító
* Egészségügyi fiók száma
* bankszámla-számok (például ellenőrzés, megtakarítások és betéti fiókok)

Belgium
* Nemzeti szám

Brazília
* Jogi entitás száma (CNPJ)
* CPF száma
* Nemzeti azonosító kártya (RG)

Kanada
* Passport-azonosító
* Illesztőprogram licenc-azonosítója
* Egészségbiztosítási szám
* Személyes állapot AZONOSÍTÓjának száma (PHIN)
* Társadalombiztosítási szám
* bankszámla-számok (például ellenőrzés, megtakarítások és betéti fiókok)

Chile
* Azonosító kártya száma 

Kína
* Azonosító kártya száma
* Rezidens azonosító kártya (PRC) száma

Horvátország
* AZONOSÍTÓ kártya száma
* Személyes azonosító (OIB) száma

Cseh Köztársaság
* Nemzeti azonosító kártya száma

Dánia
* Személyes azonosító száma

Európai Unió (EU)
* Nemzeti azonosító száma
* Passport-azonosító
* Illesztőprogram licenc-azonosítója
* Társadalombiztosítási szám (SSN) vagy azzal egyenértékű azonosító
* EU adóazonosító szám (TIN)
* EU Debit kártya száma

Finnország
* Nemzeti azonosító száma
* Passport-azonosító

Franciaország
* Nemzeti azonosító kártya (CNI)
* Társadalombiztosítási szám (INSEE)
* Passport-azonosító
* Illesztőprogram licenc-azonosítója

Németország
* AZONOSÍTÓ kártya száma
* Passport-azonosító
* Illesztőprogram licenc-azonosítója

Görögország 
* Nemzeti azonosító kártya száma

Hongkong
* AZONOSÍTÓ kártya (HKID) száma

India
* Állandó fiók száma (PAN)
* Egyedi azonosító (Aadhaar) száma

Indonézia
* AZONOSÍTÓ kártya száma (KTP)

Írország
* Személyes nyilvános szolgáltatás (PPS) száma

Izrael
* Nemzeti azonosító
* bankszámla-számok (például ellenőrzés, megtakarítások és betéti fiókok)

Olaszország
* Illesztőprogram licenc-azonosítója

Japán
* Rezidens regisztrációs szám
* Tartózkodási kártya száma
* Illesztőprogram licenc-azonosítója
* Társadalombiztosítási szám (SIN)
* Passport-azonosító
* bankszámla-számok (például ellenőrzés, megtakarítások és betéti fiókok)

Malajzia
* AZONOSÍTÓ kártya száma

Hollandia
* Citizen szolgáltatás (BSN) száma

Új-Zéland
* Egészségügyi Minisztérium száma

Norvégia
* AZONOSÍTÓ kártya száma

Fülöp-szigetek
* Egyesített többcélú azonosító száma

Lengyelország
* AZONOSÍTÓ kártya száma
* Nemzeti azonosító (PESEL)
* Passport-azonosító

Portugália 
* Citizen kártya száma

Szaúd-Arábia
* Nemzeti azonosító

Szingapúr
* Nemzeti regisztrációs azonosító kártya (NRIC) száma

Dél-Afrika
* AZONOSÍTÓ száma
* Rezidens regisztrációs szám

Dél-Korea
* Rezidens regisztrációs szám

Spanyolország 
* Társadalombiztosítási szám (SSN)

Svédország
* Nemzeti azonosító
* Passport-azonosító

Tajvan 
* Nemzeti azonosító
* Rezidens tanúsítvány (ARC/TARC) száma
* Passport-azonosító

Thaiföld
* Populáció azonosító kódja

Egyesült Királyság
* Passport-azonosító
* Illesztőprogram licenc-azonosítója
* Nemzeti biztosítási szám (NINO)
* Nemzeti Állapotfigyelő szolgáltatás (NHS) száma

Egyesült Államok
* Társadalombiztosítási szám (SSN)
* Illesztőprogram licenc-azonosítója
* Passport-azonosító
* Választási tekercs száma
* Egyéni adóazonosító szám (ITIN)
* Kábítószer-érvényesítő Ügynökség (DEA) száma
* bankszámla-számok (például ellenőrzés, megtakarítások és betéti fiókok)
