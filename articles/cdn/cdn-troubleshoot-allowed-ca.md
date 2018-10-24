---
title: Hitelesítésszolgáltatók engedélyezett az Azure CDN egyéni HTTPS engedélyezése |} A Microsoft Docs
description: Egyéni tartomány HTTPS engedélyezéséhez a saját tanúsítványt használ, ha egy engedélyezett hitelesítésszolgáltató (CA) létrehozásához kell használnia.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 79b8cfa061bafb43cbcf2ec8a43638fd14c2fcd0
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954037"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Hitelesítésszolgáltatók engedélyezett az Azure CDN egyéni HTTPS engedélyezése

Az Azure Content Delivery Network (CDN) egyéni tartomány egy **Azure CDN Standard a Microsoft** végpont, amikor, [HTTPS szolgáltatás engedélyezése a saját tanúsítvánnyal](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), egy engedélyezett kell használnia hitelesítésszolgáltató (CA) az SSL-tanúsítvány létrehozásához. Ellenkező esetben nem engedélyezett hitelesítésszolgáltató vagy egy önaláírt tanúsítványt használ, ha a kérés rendszer elutasítja.

> [!NOTE]
> Csak érhető el a beállítást, a saját tanúsítvány használatával az egyéni HTTPS engedélyezése **Azure CDN Standard a Microsoft** profilok. 
>

A következő CAs engedélyezettek a saját tanúsítvány létrehozásakor:

- AddTrust külső hitelesítésszolgáltató legfelső szintű
- AlphaSSL legfelső szintű hitelesítésszolgáltató
- Infrastruktúra AME hitelesítésszolgáltató 01
- Infrastruktúra AME hitelesítésszolgáltató 02
- Ameroot
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
- A DigiCert SHA2 bővített ellenőrzés kiszolgáló hitelesítésszolgáltató
- A DigiCert SHA2 magas szintű megbízhatóság kiszolgáló hitelesítésszolgáltató
- A DigiCert SHA2 biztonságos kiszolgáló hitelesítésszolgáltató
- Nyári Időszámítás legfelső szintű hitelesítésszolgáltató X3
- D – megbízható legfelső szintű osztály 3 hitelesítésszolgáltató 2 2009
- Sokfelé kell tárolnia a titkosítási DV TLS hitelesítésszolgáltató
- Entrust-legfelső szintű hitelesítésszolgáltató
- Entrust-legfelső szintű hitelesítésszolgáltató - G2 csomag
- Hitelesítésszolgáltató Entrust.NET (2048)
- GeoTrust globális hitelesítésszolgáltató
- GeoTrust elsődleges hitelesítésszolgáltató
- GeoTrust elsődleges hitelesítésszolgáltató - G2 csomag
- Geotrust RSA CA-2018.
- GlobalSign
- Bővített ellenőrzés CA - SHA256 - G2 GlobalSign
- GlobalSign szervezet érvényesítési CA - G2 csomag
- GlobalSign legfelső szintű hitelesítésszolgáltató
- Go Daddy legfelső szintű hitelesítésszolgáltató - G2 csomag
- Go Daddy biztonságos hitelesítésszolgáltató - G2 csomag
- RapidSSL RSA CA-2018.
- Legfelső szintű Hivatal
- A Symantec osztály 3 Bővített SSL CA - G3 csomag
- A Symantec osztály 3 biztonságos kiszolgáló CA - G4
- A Microsoft a Symantec Enterprise Mobile Root
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató - G2 csomag
- Thawte elsődleges legfelső szintű hitelesítésszolgáltató - G3 csomag
- Thawte RSA CA-2018.
- Hitelesítésszolgáltató Thawte időbélyegző
- TrustAsia TLS RSA hitelesítésszolgáltató
- SSL-Hitelesítésszolgáltatói VeriSign osztály 3 bővített ellenőrzés
- A VeriSign osztály 3 bővített ellenőrzés SSL kiszolgáló által Kezdeményezett hitelesítésszolgáltató
- 3. a VeriSign osztály nyilvános elsődleges hitelesítésszolgáltató - G5
- A VeriSign nemzetközi kiszolgáló CA - 3 osztályban
- Időbélyegzési fájlszolgáltatási gyökerén VeriSign idő
- A VeriSign univerzális legfelső szintű hitelesítésszolgáltató


