---
title: Azonosító entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f07b71bf8996612798b87d32a21a15ec72db0b32
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84140927"
---
Az entitás kategóriája pénzügyi információkat és az azonosítás hivatalos formáit tartalmazza. A modell verziójától kezdődően érhető el `2019-10-01` . Az altípusok alább láthatók. 

### <a name="financial-account-identification"></a>Pénzügyi fiók azonosítása

| Altípus neve               | Description                                                                |
|----------------------------|----------------------------------------------------------------------------|
| ABA átutalási kód        | American Banker Association (ABA) tranzit-útválasztási számok.                  |
| SWIFT-kód                 | SWIFT-kódok a fizetési utasítással kapcsolatos információkhoz.                           |
| Hitelkártya                | Hitelkártya-számok.                                                       |
| Nemzetközi bankszámlaszám (IBAN)                  | IBAN-kódok a fizetési utasítással kapcsolatos információkhoz.                            |


### <a name="government-and-countryregion-specific-identification"></a>Kormányzati és ország/régió-specifikus azonosítás

> [!NOTE]
> A következő pénzügyi és országspecifikus entitások nem lesznek visszaadva a `domain=phi` paraméterrel:
> * Passport-számok
> * Adó-azonosítók

Az alábbi entitások ország szerint vannak csoportosítva és listázva:

Argentína
* Argentin nemzeti identitás (DNI) száma

Ausztrália
* Ausztráliai útlevél száma
* Ausztráliai adó-fájl száma
* Ausztráliai jogosítvány száma
* Ausztráliai egészségügyi fiók száma
* Ausztráliai bankszámla száma

Belgium
* Belgium nemzeti száma

Brazília 
* Brazília jogi személy száma (CNPJ)
* Brazília CPF száma
* Brazília nemzeti azonosító kártya (RG)

Kanada
* Kanadai társadalombiztosítási szám
* Kanadai jogosítvány száma
* Kanadai bankszámla száma
* Kanadai útlevél száma
* Kanadai személyi állapot azonosítójának száma (PHIN)
* Kanada Állapotfigyelő szolgáltatás száma

Chile
* Azonosító kártya száma 

Kína
* Kínai rezidens személyazonosító kártya (PRC) száma

Horvátország
* Horvátország személyazonosító igazolványának száma
* Horvátország személyes azonosítójának (OIB) száma

Cseh Köztársaság
* Cseh személyi azonosító száma

Dánia
* Dániai személyi azonosító száma

Európai Unió (EU)
* EU nemzeti azonosító száma
* EU Passport-szám
* Az EU-illesztőprogram licencének száma
* KÖZÖSSÉGI társadalombiztosítási szám (SSN) vagy azzal egyenértékű azonosító
* EU adóazonosító szám (TIN)
* EU Debit kártya száma

Finnország
* Finnország nemzeti azonosítója
* Finnország Passport száma

Franciaország
* Francia nemzeti azonosító kártya (CNI)
* Franciaországi társadalombiztosítási szám (INSEE)
* Franciaországi Passport-szám
* France-illesztőprogram licencének száma

Németország
* Németországi személyazonosító igazolvány száma
* Német útlevél száma
* Német jogosítvány száma

Görögország 
* Görögországi nemzeti azonosító kártya száma

Hongkong
* Hong Kong Identity Card (HKID) száma

India
* Indiai állandó fiók száma (PAN)
* Indiai egyedi azonosító (Aadhaar) száma

Indonézia
* Indonéz személyazonosító kártya (KTP) száma

Írország
* Írországi személyes nyilvános szolgáltatás (PPS) száma

Izrael
* Izraeli nemzeti azonosító
* Izraeli bankszámla száma

Olaszország
* Olaszország-illesztőprogram licenc-azonosítója

Japán
* Japán rezidens regisztrációs szám
* Japán tartózkodási kártya száma
* Japán jogosítvány száma
* Társadalombiztosítási szám (SIN)
* Japán Passport-szám
* Japán bankszámla száma

Malajzia
* Malajziai személyazonosító kártya száma

Hollandia
* Holland állampolgári szolgáltatás (BSN) száma

Új-Zéland
* Új-zélandi Ministry of Health Number

Norvégia
* Norvégia azonosítójának száma

Fülöp-szigetek
* Fülöp-szigetek egységes többcélú azonosító száma

Lengyelország
* Lengyel személyazonosító igazolvány
* Lengyel nemzeti azonosító (PESEL)
* Lengyel útlevél

Portugália 
* Portugál állampolgári kártya száma

Szaúd-Arábia
* Szaúd-arábiai nemzeti azonosító

Szingapúr
* Szingapúri nemzeti regisztrációs azonosító kártya (NRIC) száma

Dél-afrikai Köztársaság
* Dél-afrikai azonosító száma

Dél-Korea
* Dél-Korea rezidens regisztrációs száma

Spanyolország 
* Spanyolországi társadalombiztosítási szám (SSN)

Svédország
* Svéd nemzeti azonosító
* Svédország Passport-szám

Tajvan 
* Tajvani nemzeti azonosító
* Tajvani rezidens tanúsítvány (ARC/TARC)
* Tajvani Passport-szám

Thaiföld
* Thai populáció-azonosító kód

Egyesült Királyság
* Passport-azonosító
* brit Illesztőprogram licencének száma
* brit Nemzeti biztosítási szám (NINO)
* brit Nemzeti Állapotfigyelő szolgáltatás száma
* brit Választási tekercs száma
* EGYESÜLT ÁLLAMOK/EGYESÜLT KIRÁLYSÁG Passport-szám

Egyesült Államok
* Egyesült államokbeli társadalombiztosítási szám (SSN)
* Egyesült államokbeli jogosítvány száma
* EGYESÜLT ÁLLAMOK/EGYESÜLT KIRÁLYSÁG Passport-szám
* Egyesült államokbeli egyedi adófizető-azonosító szám (ITIN)
* Kábítószer-érvényesítő Ügynökség (DEA) száma
* Egyesült államokbeli bankszámla száma
