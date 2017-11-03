---
title: "Az Azure API Management hitelesítési házirendek |} Microsoft Docs"
description: "Ismerje meg a hitelesítési házirendek az Azure API Management használható."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 2907c1e4e39f975b4dc4e9382d7258c5d56dbbdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-authentication-policies"></a>Az API Management hitelesítési házirendek
Ez a témakör egy hivatkozást a következő API-felügyeleti házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AuthenticationPolicies"></a>Hitelesítési házirendek  
  
-   [Basic hitelesítés](api-management-authentication-policies.md#Basic) -alapszintű hitelesítést használó háttérszolgáltatás a hitelesítést.  
  
-   [Az ügyféltanúsítvány hitelesítéséhez](api-management-authentication-policies.md#ClientCertificate) -az ügyféltanúsítványok háttérszolgáltatás a hitelesítést.  
  
##  <a name="Basic"></a>Alapszintű hitelesítés  
 Használja a `authentication-basic` házirend az egyszerű hitelesítést használ a háttérszolgáltatáshoz való hitelesítéshez szükséges. Ez a házirend hatékonyan állítja be a HTTP Authorization fejlécet a házirendben megadott hitelesítő adatokhoz megfelelő értéket.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|egyszerű hitelesítés|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|felhasználónév|Az alapszintű hitelesítő adat felhasználónevet adja meg.|Igen|N/A|  
|jelszó|Megadja az alapvető hitelesítő adat.|Igen|N/A|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
  
-   **Házirend hatókörök:** API  
  
##  <a name="ClientCertificate"></a>Az ügyféltanúsítvány hitelesítéséhez  
 Használja a `authentication-certificate` házirend az ügyféltanúsítvány használatával háttérszolgáltatás való hitelesítéshez szükséges. A tanúsítvány kell lennie [telepítve az API Management](http://go.microsoft.com/fwlink/?LinkID=511599) első és az ujjlenyomat azonosíthatók.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|hitelesítési tanúsítvány|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|ujjlenyomat|Az ügyféltanúsítvány ujjlenyomata.|Igen|N/A|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
  
-   **Házirend hatókörök:** API  
  

## <a name="next-steps"></a>Következő lépések
Házirendek használata további információkért lásd: [házirendek az API Management](api-management-howto-policies.md).  