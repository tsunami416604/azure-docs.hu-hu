---
title: Személyes adatok elnevezett entitásai
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: d678a29de9dea8a5a2f6d0259a452ca4c69feb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77086899"
---
## <a name="personal-information-entity-types"></a>Személyes adatok entitás típusok:

### <a name="person"></a>Személy
Személyek nevének felismerése a szövegben.

Nyelvek:
* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                               | Modellverzióval kezdve elérhető |
|--------------|-----------------------------------------------------------|----------------------------------------|
| N/A          | Elismert nevek, `Bill Gates`például :`Marie Curie` | `2020-02-01`                           |

### <a name="organization"></a>Szervezet  

Ismerje el a szervezeteket, vállalatokat, ügynökségeket, vállalatokat, klubokat és más embercsoportokat.

Nyelvek: 

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                                                                       | Modellverzióval kezdve elérhető|
|--------------|---------------------------------------------------------------------------------------------------|--------------|
| N/A          | szervezetek, például `Microsoft` `NASA`, ,`National Oceanic and Atmospheric Administration` | `2020-02-01` |

### <a name="phone-number"></a>Telefonszám

Telefonszámok (csak amerikai telefonszámok). 

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                    | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------------|----------------------------------------|
| N/A          | Amerikai telefonszámok, például`(312) 555-0176` | `2020-02-01`                           |

### <a name="email"></a>E-mail

E-mail-cím. 

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                      | Modellverzióval kezdve elérhető |
|--------------|--------------------------------------------------|----------------------------------------|
| N/A          | E-mail cím, például`support@contoso.com` | `2020-02-01`                           |

### <a name="url"></a>URL-cím

internetes URL-eket.

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                                          | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------------------|----------------------------------------|
| N/A          | URL-címek a weboldalak, például`https://www.bing.com` | `2020-02-01`                           |

### <a name="ip-address"></a>IP-cím

IP-cím

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                              | Modellverzióval kezdve elérhető |
|--------------|------------------------------------------|----------------------------------------|
| N/A          | Hálózati cím például`10.0.0.101` | `2020-02-01`                           |

### <a name="quantity"></a>Mennyiség 

Numerikus mennyiségek

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                   | Modellverzióval kezdve elérhető |
|--------------|-------------------------------|----------------------------------------|
| Kor          | `90 days old`, `30 years old` | `2020-02-01`                           |

### <a name="datetime"></a>DateTime

Dátum és idő entitások

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                   | Modellverzióval kezdve elérhető |
|--------------|-------------------------------|----------------------------------------|
| Dátum         | `May 2nd, 2017`, `05/02/2017` | `2020-02-01`                           |

### <a name="eu-gps-coordinates"></a>EU GPS koordináták

 Az Európai Unión belüli helyek GPS koordinátái. 

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve | Leírás                               | Modellverzióval kezdve elérhető |
|--------------|-------------------------------------------|----------------------------------------|
| N/A          | GPS koordináták az Európai Unión belül | `2019-10-01`                           |

### <a name="azure-information"></a>Azure-információk

Azonosítható Azure-adatok, beleértve a hitelesítési adatokat és a kapcsolati karakterláncokat. 

* A modellverzióval `2019-10-01`kezdve érhető el.

Nyelvek:

* Nyilvános előzetes verzió:`English`

| Altípus neve                          | Leírás                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Azure DocumentDB hitelesítési kulcs             | Egy Azure DocumentDB-kiszolgáló engedélyezési kulcsa.                           |
| Azure IAAS adatbázis-kapcsolati karakterlánc | Az Azure Infrastructure szolgáltatásként (IaaS) adatbázis ának kapcsolati karakterlánca. |
| Azure SQL-kapcsolati karakterlánc           | Kapcsolati karakterlánc egy Azure SQL-adatbázishoz.                                |
| Azure IoT-kapcsolati karakterlánc           | Kapcsolati karakterlánc az Azure Internet of things(IoT) számára.                        |
| Azure közzétételi beállítás jelszava        | Az Azure Közzététel beállításainak jelszava.                                        |
| Azure Redis gyorsítótár-kapcsolati karakterlánc   | A Redis Azure-gyorsítótárának kapcsolati karakterlánca.                             |
| Azure SAS                             | Az Azure Software szolgáltatásként (SAS) kapcsolati karakterlánca.                     |
| Az Azure Service Bus kapcsolati karakterlánca   | Kapcsolati karakterlánc egy Azure-szolgáltatásbuszhoz.                                |
| Azure storage-fiókkulcs             | Egy Azure-tárfiók fiókkulcsa.                                   |
| Azure storage-fiókkulcs (általános)   | Általános fiókkulcs egy Azure-tárfiókhoz.                           |
| SQL Server kapcsolati karakterlánc          | SQL-kiszolgáló csatlakozási karakterlánca.                                         |

### <a name="identification"></a>Azonosítás

* A modellverzióval `2019-10-01`kezdve érhető el.

Nyelvek:

* Nyilvános előzetes verzió:`English`

#### <a name="financial-account-identification"></a>Pénzügyi számla azonosítása

| Altípus neve               | Leírás                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA útválasztási számok        | American Banker Association (ABA) tranzitútvonal-számok.                  |
| SWIFT-kód                 | SWIFT-kódok a fizetési megbízási információkhoz.                           |
| Hitelkártya                | Hitelkártya számok.                                                       |
| IBAN-kód                  | IBAN-kódok a fizetési utasítás adataihoz.                            |

#### <a name="government-and-country-specific-identification"></a>Kormányzati és országspecifikus azonosítás

Az alábbi entitások országonként vannak csoportosítva és felsorolva:

Argentína
* Nemzeti azonosító (DNI) szám

Ausztrália
* Adófájl száma 
* Jogosítvány azonosítója
* Útlevél azonosító
* Orvosi számlaszám
* bankszámlaszámok (például csekk-, megtakarítás- és tartozik számlák)

Belgium
* Nemzeti szám

Brazília
* Jogi személy száma (CNPJ)
* CPF-szám
* Személyi igazolvány (RG)

Kanada
* Útlevél azonosító
* Jogosítvány azonosítója
* Egészségbiztosítási szám
* Személyi egészségügyi azonosító száma (PHIN)
* Társadalombiztosítási
* bankszámlaszámok (például csekk-, megtakarítás- és tartozik számlák)

Chile
* Személyazonosító igazolvány száma 

Kína
* Személyazonosító igazolvány száma
* Rezidens személyazonosító igazolvány (KNK) száma

Horvátország
* Személyi igazolvány száma
* Személyi azonosító (OIB) száma

Cseh Köztársaság
* Személyi igazolvány száma

Dánia
* Személyi azonosító száma

Európai Unió (EU)
* Nemzeti azonosító szám
* Útlevél azonosító
* Jogosítvány azonosítója
* Társadalombiztosítási szám (SSN) vagy azzal egyenértékű azonosító
* Eu adóazonosító szám (TIN)
* Eu betéti kártya száma

Finnország
* Nemzeti azonosító szám
* Útlevél azonosító

Franciaország
* Nemzeti személyi igazolvány (CNI)
* Társadalombiztosítási szám (INSEE)
* Útlevél azonosító
* Jogosítvány azonosítója

Németország
* Személyi igazolvány száma
* Útlevél azonosító
* Jogosítvány azonosítója

Görögország 
* Személyi igazolvány száma

Hongkong
* Személyi igazolvány (HKID) száma

India
* Állandó számlaszám (PAN)
* Egyedi azonosító (Aadhaar) száma

Indonézia
* Személyi igazolvány száma (KTP)

Írország
* Személyi közszolgálati (PPS) szám

Izrael
* Nemzeti azonosító
* bankszámlaszámok (például csekk-, megtakarítás- és tartozik számlák)

Olaszország
* Jogosítvány azonosítója

Japán
* Rezidens nyilvántartási száma
* Tartózkodási kártya száma
* Jogosítvány azonosítója
* Társadalombiztosítási szám (Bűn)
* Útlevél azonosító
* bankszámlaszámok (például csekk-, megtakarítás- és tartozik számlák)

Malajzia
* Személyi igazolvány száma

Hollandia
* Állampolgári szolgálat (BSN) száma

Új-Zéland
* Egészségügyi Minisztérium száma

Norvégia
* Személyi igazolvány száma

Fülöp-szigetek
* Egyesített többcélú azonosítószám

Lengyelország
* Személyi igazolvány száma
* Nemzeti azonosító (PESEL)
* Útlevél azonosító

Portugália 
* Állampolgári kártya száma

Szaúd-Arábia
* Nemzeti azonosító

Szingapúr
* Nemzeti regisztrációs azonosító kártya (NRIC) száma

Dél-afrikai Köztársaság
* Azonosító száma
* Rezidens nyilvántartási száma

Dél-Korea
* Rezidens nyilvántartási szám

Spanyolország 
* Társadalombiztosítási szám (SSN)

Svédország
* Nemzeti azonosító
* Útlevél azonosító

Tajvan 
* Nemzeti azonosító
* Rezidens bizonyítvány (ARC/TARC) száma
* Útlevél azonosító

Thaiföld
* Népességazonosító kód

Egyesült Királyság
* Útlevél azonosító
* Jogosítvány azonosítója
* Nemzeti biztosítási szám (NINO)
* Nemzeti Egészségügyi Szolgálat (NHS) száma

Egyesült Államok
* Társadalombiztosítási szám (SSN)
* Jogosítvány azonosítója
* Útlevél azonosító
* Választási névjegyzék száma
* Egyéni adóazonosító szám (ITIN)
* Kábítószer-végrehajtási Ügynökség (DEA) száma
* bankszámlaszámok (például csekk-, megtakarítás- és tartozik számlák)
