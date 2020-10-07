---
title: Azonosító entitások
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 49a2b285d9494dfc5ca0863c021733b4bc874efa
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779544"
---
Az entitás kategóriája pénzügyi információkat és az azonosítás hivatalos formáit tartalmazza. A modell verziójától kezdődően érhető el `2019-10-01` . Az altípusok alább láthatók. 

### <a name="financial-account-identification"></a>Pénzügyi fiók azonosítása

| Altípus neve               | Leírás                                                                |
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

A következő entitások az ország szerint vannak csoportosítva és listázva:

Argentína
* Argentin nemzeti identitás (DNI) száma

Ausztria
* Osztrák személyazonosító igazolvány
* Ausztria adóazonosító száma
* Ausztria-hozzáadottérték-adó (ÁFA) száma

Ausztrália
* Ausztráliai bankszámla száma
* Ausztráliai üzleti szám
* Ausztráliai vállalat száma
* Ausztráliai jogosítvány száma
* Ausztráliai egészségügyi fiók száma
* Ausztráliai útlevél száma
* Ausztráliai adó-fájl száma

Belgium
* Belgium nemzeti száma
* Belgium – értéknövelő adó száma

Brazília 
* Brazília jogi személy száma (CNPJ)
* Brazília CPF száma
* Brazília nemzeti azonosító kártya (RG)

Bulgária
* Bulgáriai egységes polgári szám

Kanada
* Kanadai bankszámla száma
* Kanadai jogosítvány száma
* Kanada Állapotfigyelő szolgáltatás száma
* Kanadai útlevél száma
* Kanadai személyi állapot azonosítójának száma (PHIN)
* Kanadai társadalombiztosítási szám

Chile
* Azonosító kártya száma 

Kína
* Kínai rezidens személyazonosító kártya (PRC) száma

Horvátország
* Horvátország személyazonosító igazolványának száma
* Horvátország nemzeti azonosító kártya száma
* Horvátország személyes azonosítójának (OIB) száma

Ciprus
* Ciprusi személyazonosító igazolvány száma
* Ciprusi adóazonosító szám

Cseh Köztársaság
* Cseh személyi azonosító száma

Dánia
* Dániai személyi azonosító száma

Észtország
* Észtország személyes azonosító kódja

Európai Unió (EU)
* EU Debit kártya száma
* Az EU-illesztőprogram licencének száma
* EU nemzeti azonosító száma
* EU Passport-szám
* KÖZÖSSÉGI társadalombiztosítási szám (SSN) vagy azzal egyenértékű azonosító
* EU adóazonosító szám (TIN)

Finnország
* Finnország európai egészségbiztosítási száma
* Finnország nemzeti azonosítója
* Finnország Passport száma

Franciaország
* France-illesztőprogram licencének száma
* Franciaország egészségbiztosítási száma
* Francia nemzeti azonosító kártya (CNI)
* Franciaországi Passport-szám
* Franciaországi társadalombiztosítási szám (INSEE)
* France-adóazonosító szám (numéro SPI)
* Franciaország értékével hozzáadott adó száma

Németország
* Német jogosítvány száma
* Németországi személyazonosító igazolvány száma
* Német útlevél száma
* Németországi adóazonosító szám
* Németországi értéknövelő adó száma

Görögország 
* Görögországi nemzeti azonosító kártya száma
* Görögországi adóazonosító szám

Hongkong
* Hong Kong Identity Card (HKID) száma

Magyarország
* Magyar nemzeti azonosító szám
* Magyarország adóazonosító száma
* Magyarország – értéknövelő adó száma

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
* Olaszországi pénzügyi kód
* Olaszország értékével hozzáadott adószám

Japán
* Japán bankszámla száma
* Japán jogosítvány száma
* Japán saját szám személyes
* Japán cégem száma
* Japán rezidens regisztrációs szám
* Japán tartózkodási kártya száma
* Japán társadalombiztosítási szám (SIN)
* Japán Passport-szám

Lettország
* Lettország személyes kódja

Litvánia
* Litvániai személyes kód

Luxemburg
* Luxemburg nemzeti azonosító száma (természetes személyek)
* Luxemburg nemzeti azonosító száma (nem természetes személyek)

Malajzia
* Malajziai személyazonosító kártya száma

Málta
* Málta személyazonosító igazolványának száma
* Málta-adóazonosító szám

Hollandia
* Holland állampolgári szolgáltatás (BSN) száma
* Holland adóazonosító szám
* Holland értéknövelő adó száma

Új-Zéland
* Új-zélandi bankszámla száma
* Új-zélandi illesztőprogram licencének száma
* Új-zélandi belföldi bevétel száma
* Új-zélandi Ministry of Health Number
* Új-Zéland közösségi jóléti száma

Norvégia
* Norvégia azonosítójának száma

Fülöp-szigetek
* Fülöp-szigetek egységes többcélú azonosító száma

Lengyelország
* Lengyel személyazonosító igazolvány
* Lengyel nemzeti azonosító (PESEL)
* Lengyel útlevél száma
* Lengyelország REGON száma
* Lengyelország adóazonosító száma

Portugália 
* Portugál állampolgári kártya száma
* Portugáliai adóazonosító szám

Románia
* Románia személyes numerikus kódja (CNP)

Oroszország
* Orosz útlevél száma (belföldi)
* Orosz útlevél száma (nemzetközi)

Szaúd-Arábia
* Szaúd-arábiai nemzeti azonosító

Szingapúr
* Szingapúri nemzeti regisztrációs azonosító kártya (NRIC) száma

Szlovákia 
* Szlovákia személyes száma

Szlovénia
* Szlovénia adóazonosító száma
* Szlovénia egyedi fő polgárának száma

Dél-afrikai Köztársaság
* Dél-afrikai azonosító száma

Dél-Korea
* Dél-Korea rezidens regisztrációs száma

Spanyolország 
* Spanyolországi DNI
* Spanyolországi társadalombiztosítási szám (SSN)
* Spanyol adóazonosító szám

Svédország
* Svéd nemzeti azonosító
* Svédország Passport-szám
* Svédország-adóazonosító szám

Svájc
* Svájci társadalombiztosítási szám adja meg

Tajvan 
* Tajvani nemzeti azonosító
* Tajvani rezidens tanúsítvány (ARC/TARC)
* Tajvani Passport-szám

Thaiföld
* Thai populáció-azonosító kód

Törökország
* Török nemzeti azonosító száma

Ukrajna
* Ukrán Passport-szám (belföldi)
* Ukrán Passport-szám (nemzetközi)

Egyesült Királyság
* brit Illesztőprogram licencének száma
* brit Választási tekercs száma
* brit Nemzeti Állapotfigyelő szolgáltatás (NHS) száma
* brit Nemzeti biztosítási szám (NINO)
* brit Passport-szám
* brit Egyedi adófizető hivatkozási szám

Egyesült Államok
* Egyesült államokbeli társadalombiztosítási szám (SSN)
* Egyesült államokbeli jogosítvány száma
* US Passport-szám
* Egyesült államokbeli egyedi adófizető-azonosító szám (ITIN)
* Egyesült államokbeli kábítószer-érvényesítő Ügynökség (DEA) száma
* Egyesült államokbeli bankszámla száma
