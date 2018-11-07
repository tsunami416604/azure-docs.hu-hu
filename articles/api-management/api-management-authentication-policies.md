---
title: Az Azure API Management hitelesítési házirendek |} A Microsoft Docs
description: Ismerje meg a hitelesítési házirendek az Azure API Management használható.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4c4c03fffa5786bf3a50f4d2c03511f0a2de0f48
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250951"
---
# <a name="api-management-authentication-policies"></a>Az API Management a hitelesítési házirendek
Ez a témakör egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Hitelesítési házirendek  
  
-   [Az alapszintű hitelesítéshez](api-management-authentication-policies.md#Basic) -háttérszolgáltatás alapszintű hitelesítést használ a hitelesítéshez.  
  
-   [Ügyféltanúsítvány a hitelesítéshez](api-management-authentication-policies.md#ClientCertificate) -háttérszolgáltatás ügyféltanúsítványok használatát a hitelesítéshez.  
  
##  <a name="Basic"></a> Az alapszintű hitelesítés  
 Használja a `authentication-basic` szabályzatot egy háttérszolgáltatás alapszintű hitelesítést használ a hitelesítéshez. Ez a szabályzat hatékonyan állítja be a HTTP-engedélyezési fejléc a házirendben megadott hitelesítő adatok megfelelő értéket.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|hitelesítés – alapszintű|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|felhasználónév|Adja meg a felhasználónevet a alapszintű hitelesítés.|Igen|–|  
|jelszó|Az alapszintű hitelesítés jelszava.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő  
  
-   **A házirend-hatókörök:** API  
  
##  <a name="ClientCertificate"></a> Ügyféltanúsítvány a hitelesítéshez  
 Használja a `authentication-certificate` házirend-háttérszolgáltatás az ügyféltanúsítványt használja hitelesítésre. A tanúsítványnak [telepítve van az API Management](https://go.microsoft.com/fwlink/?LinkID=511599) első és az ujjlenyomat azonosítja.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|hitelesítés – tanúsítvány|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|ujjlenyomat|Az ügyféltanúsítvány ujjlenyomatát.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő  
  
-   **A házirend-hatókörök:** API  
  

## <a name="next-steps"></a>További lépések
Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)   
