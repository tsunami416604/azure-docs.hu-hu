---
title: Hitelesítésszolgáltatók engedélyezett az Azure bejárati ajtajának Service egyéni HTTPS engedélyezése |} A Microsoft Docs
description: Egyéni tartomány HTTPS engedélyezéséhez a saját tanúsítványt használ, ha egy engedélyezett hitelesítésszolgáltató (CA) létrehozásához kell használnia.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: de709133099674a0aa0386113b6459f8bc05e378
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047527"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Hitelesítésszolgáltatók engedélyezett az Azure bejárati ajtajának Service egyéni HTTPS engedélyezése

Azure bejárati ajtajának Service egyéni tartomány amikor Ön [HTTPS szolgáltatás engedélyezése a saját tanúsítvánnyal](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), engedélyezett hitelesítésszolgáltató (CA) kell használnia az SSL-tanúsítvány létrehozásához. Ellenkező esetben nem engedélyezett hitelesítésszolgáltató vagy egy önaláírt tanúsítványt használ, ha a kérés rendszer elutasítja.

A következő CAs engedélyezettek a saját tanúsítvány létrehozásakor:

- AddTrust külső hitelesítésszolgáltató legfelső szintű
- Hozzáférési pont legfelső szintű hitelesítésszolgáltató
- Hozzáférési pont legfelső szintű hitelesítésszolgáltató 2013-hoz
- Hozzáférési pont legfelső szintű hitelesítésszolgáltató 2014
- APCA-DM3P
- Az autopilot legfelső szintű hitelesítésszolgáltató
- Baltimore CyberTrust legfelsőbb szintű
- Nyilvános elsődleges hitelesítésszolgáltató osztály 3
- Hitelesítésszolgáltató COMODO RSA
- COMODO RSA tartomány érvényesítési biztonságos kiszolgáló hitelesítésszolgáltató
- D – megbízható legfelső szintű osztály 3 hitelesítésszolgáltató 2 2009
- A Felhőszolgáltatások DigiCert hitelesítésszolgáltató-1
- A DigiCert globális legfelső szintű hitelesítésszolgáltató
- A DigiCert nagy frissítési garanciát biztosító hitelesítésszolgáltató – 3
- A DigiCert nagy garancia, Bővített legfelső szintű hitelesítésszolgáltató
- A DigiCert SHA2 magas szintű megbízhatóság kiszolgáló hitelesítésszolgáltató
- A DigiCert SHA2 biztonságos kiszolgáló hitelesítésszolgáltató
- GeoTrust globális hitelesítésszolgáltató
- GeoTrust elsődleges hitelesítésszolgáltató
- GeoTrust elsődleges hitelesítésszolgáltató - G2 csomag
- GlobalSign
- Bővített ellenőrzés CA - SHA256 - G2 GlobalSign
- GlobalSign szervezet érvényesítési CA - G2 csomag
- GlobalSign legfelső szintű hitelesítésszolgáltató
- Go Daddy legfelső szintű hitelesítésszolgáltató - G2 csomag
- A Microsoft Authenticode(tm) főtanúsítvány tanúsítványláncát.
- A Microsoft Exchange-szolgáltatások hitelesítésszolgáltató 2015.
- A Microsoft belső, vállalati legfelső szintű
- A Microsoft IT ITO SSL CA 1
- A Microsoft IT SSL SHA1
- A Microsoft IT SSL SHA2
- A Microsoft IT TLS hitelesítésszolgáltató 1
- A Microsoft IT TLS hitelesítésszolgáltató 2
- A Microsoft IT TLS hitelesítésszolgáltató 4
- A Microsoft IT TLS hitelesítésszolgáltató 5
- A Microsoft főtanúsítvány tanúsítványláncát.
- A Microsoft legfelső szintű hitelesítésszolgáltató
- A Microsoft legfelső szintű hitelesítésszolgáltató 2010
- A Microsoft legfelső szintű hitelesítésszolgáltató 2011
- A Microsoft biztonságos kiszolgáló hitelesítésszolgáltató 2011
- Microsoft-szolgáltatások Partner legfelső szintű
- A Microsoft idő időbélyegzési fájlszolgáltatási gyökerén
- A Microsoft Windows hardverkompatibilitás
- MSIT HITELESÍTÉSSZOLGÁLTATÓ Z2
- MSIT vállalati hitelesítésszolgáltató 1
- MSIT vállalati hitelesítésszolgáltató 3
- Legfelső szintű Hivatal
- A Symantec osztály 3 Bővített SSL CA - G3 csomag
- A Symantec osztály 3 biztonságos kiszolgáló CA - G4
- A Microsoft a Symantec Enterprise Mobile Root
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató - G2 csomag
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató - G3 csomag
- Hitelesítésszolgáltató Thawte időbélyegző
- UTN-USERFirst-Object
- SSL-Hitelesítésszolgáltatói VeriSign osztály 3 bővített ellenőrzés
- A VeriSign osztály 3 bővített ellenőrzés SSL kiszolgáló által Kezdeményezett hitelesítésszolgáltató
- 3. a VeriSign osztály nyilvános elsődleges hitelesítésszolgáltató - G5
- A VeriSign nemzetközi kiszolgáló CA - 3 osztályban
- Időbélyegzési fájlszolgáltatási gyökerén VeriSign idő
- A VeriSign univerzális legfelső szintű hitelesítésszolgáltató
- WMSvc-SHA2-DALEDGE1008
