---
title: Hitelesítésszolgáltatók engedélyezett egyéni HTTPS, Azure CDN engedélyezése |} Microsoft Docs
description: Ha egyéni tartományt a HTTPS engedélyezéséhez a saját tanúsítványt használ, a megengedett hitelesítésszolgáltatótól (CA) létrehozásához kell használnia.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 3c41ca7e375324ff784bf7bee347bb56400ddfbd
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238280"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Hitelesítésszolgáltatók engedélyezett egyéni HTTPS, Azure CDN engedélyezése

Az Azure Content Delivery Network (CDN) egyéni tartomány egy **Azure CDN Standard Microsoft** végpont, amikor Ön [a HTTPS-funkció engedélyezése a saját tanúsítvány használatával](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates), kell használnia egy engedélyezett hitelesítésszolgáltató (CA) az SSL-tanúsítvány létrehozásához. Ellenkező esetben nem engedélyezett hitelesítésszolgáltató vagy egy önaláírt tanúsítványt használ, ha a kérés elutasításra kerül.

> [!NOTE]
> Egyéni HTTPS engedélyezéséhez a saját tanúsítvány használatát csak érhető **Azure CDN Standard Microsoft** profilok. 
>

A következő CAs vannak engedélyezve, ha a tanúsítvány létrehozása:

- AddTrust külső hitelesítésszolgáltató legfelső szintű
- Ázsia és a Csendes legfelső szintű hitelesítésszolgáltató
- Ázsia és a Csendes legfelső szintű hitelesítésszolgáltató 2013
- Ázsia és a Csendes legfelső szintű hitelesítésszolgáltató 2014
- APCA-DM3P
- Automata legfelső szintű hitelesítésszolgáltató
- Baltimore CyberTrust legfelső szintű
- 3 osztály nyilvános elsődleges hitelesítésszolgáltató
- COMODO RSA hitelesítésszolgáltató
- COMODO RSA érvényesítési biztonságos tartománykiszolgálóhoz hitelesítésszolgáltató
- D megbízható legfelső szintű osztály 3 hitelesítésszolgáltató 2 2009
- DigiCert Cloud Services – CA-1
- DigiCert globális legfelső szintű hitelesítésszolgáltató
- DigiCert magas megbízhatósági CA-3
- DigiCert nagy EV megbízható legfelső szintű hitelesítésszolgáltató
- DigiCert SHA2 magas szintű megbízhatóság Server hitelesítésszolgáltató
- A biztonságos kiszolgáló DigiCert SHA2 hitelesítésszolgáltató
- GlobalSign
- Bővített érvényesítési CA - SHA256 - G2 GlobalSign
- GlobalSign szervezet érvényesítési CA - G2
- GlobalSign legfelső szintű hitelesítésszolgáltató
- Microsoft Authenticode(tm) legfelső szintű hitelesítésszolgáltató
- Hitelesítésszolgáltató 2015 Microsoft Exchange-szolgáltatásokhoz
- A Microsoft belső vállalati gyökér
- A Microsoft informatikai ITO SSL hitelesítésszolgáltató 1
- A Microsoft informatikai SSL SHA1
- A Microsoft informatikai SSL 2
- A Microsoft informatikai TLS hitelesítésszolgáltató 1
- A Microsoft informatikai TLS hitelesítésszolgáltató 2
- A Microsoft informatikai TLS hitelesítésszolgáltató 4
- A Microsoft informatikai TLS hitelesítésszolgáltató 5
- Microsoft legfelső szintű hitelesítésszolgáltató
- Microsoft legfelső szintű hitelesítésszolgáltató
- Microsoft legfelső szintű hitelesítésszolgáltató 2010
- Microsoft legfelső szintű hitelesítésszolgáltató 2011
- A Microsoft biztonságos kiszolgáló hitelesítésszolgáltató 2011
- A Microsoft a szolgáltatások Partner gyökere
- Microsoft idő legfelső szintű időbélyegzési szolgáltatás
- Microsoft Windows hardverkompatibilitás
- MSIT HITELESÍTÉSSZOLGÁLTATÓ Z2
- MSIT vállalati hitelesítésszolgáltató 1
- MSIT vállalati hitelesítésszolgáltató 3
- Legfelső szintű ügynökség
- Symantec osztály 3 EV SSL CA - G3
- Symantec osztály 3 biztonságos kiszolgáló CA - G4
- Symantec Enterprise Mobile Root Microsoft
- Thawte Timestamping hitelesítésszolgáltató
- UTN-USERFirst-objektum
- VeriSign osztály 3 bővített érvényesítési SSL-hitelesítésszolgáltató
- VeriSign osztály 3 bővített érvényesítési SSL kiszolgáló által Kezdeményezett hitelesítésszolgáltató
- 3 VeriSign osztály nyilvános elsődleges hitelesítésszolgáltató - G5
- VeriSign nemzetközi Server CA - osztály 3
- VeriSign idő legfelső szintű időbélyegzési szolgáltatás
- VeriSign univerzális legfelső szintű hitelesítésszolgáltató
- WMSvc-SHA2-DALEDGE1008

