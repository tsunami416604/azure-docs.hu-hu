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
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708795"
---
# <a name="api-management-authentication-policies"></a>Az API Management a hitelesítési házirendek
Ez a témakör egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Hitelesítési házirendek  
  
-   [Az alapszintű hitelesítéshez](api-management-authentication-policies.md#Basic) -háttérszolgáltatás alapszintű hitelesítést használ a hitelesítéshez.  
  
-   [Ügyféltanúsítvány a hitelesítéshez](api-management-authentication-policies.md#ClientCertificate) -háttérszolgáltatás ügyféltanúsítványok használatát a hitelesítéshez.  

-   [Felügyelt identitás a hitelesítéshez](api-management-authentication-policies.md#ManagedIdentity) -hitelesítéshez a [identitás](../active-directory/managed-identities-azure-resources/overview.md) az API Management szolgáltatás.  
  
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
  
|Name (Név)|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|hitelesítés – alapszintű|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|username|Adja meg a felhasználónevet a alapszintű hitelesítés.|Igen|–|  
|password|Az alapszintű hitelesítés jelszava.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő  
  
-   **A házirend-hatókörök:** API  
  
##  <a name="ClientCertificate"></a> Ügyféltanúsítvány a hitelesítéshez  
 Használja a `authentication-certificate` házirend-háttérszolgáltatás az ügyféltanúsítványt használja hitelesítésre. A tanúsítványnak [telepítve van az API Management](https://go.microsoft.com/fwlink/?LinkID=511599) első és az ujjlenyomat azonosítja.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Példák  
  
Ez a példa az ügyfél a tanúsítványt az ujjlenyomat alapján azonosítja.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
Ebben a példában ügyféltanúsítvány erőforrás neve azonosítja.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|hitelesítés – tanúsítvány|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Az ügyféltanúsítvány ujjlenyomatát.|Akár `thumbprint` vagy `certificate-id` jelen kell lennie.|–|  
|certificate-id|A tanúsítvány-erőforrás neve.|Akár `thumbprint` vagy `certificate-id` jelen kell lennie.|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő  
  
-   **A házirend-hatókörök:** API  

##  <a name="ManagedIdentity"></a> Felügyelt identitás a hitelesítéshez  
 Használja a `authentication-managed-identity` szabályzatot egy háttérszolgáltatást, az API Management szolgáltatás a felügyelt identitás használata a hitelesítéshez. Ez a házirend a felügyelt identitás hatékonyan használja hozzáférési jogkivonat beszerzése az Azure Active Directoryból a megadott erőforráshoz való hozzáférést. 
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|authentication-managed-identity |A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|resource|karakterlánc. Az Alkalmazásazonosító URI-t a cél webes API-t (védett erőforrás) az Azure Active Directoryban.|Igen|–|  
|output-token-variable-name|karakterlánc. Neve a környezeti változó, amelyek megkapják a token értékét egy objektumtípust, `string`.|Nem|–|  
|ignore-error|Logikai érték. Ha beállítása `true`, a házirend folyamat továbbra is fut, még akkor is, ha a hozzáférési jogkivonat nem érik.|Nem|false|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő  
  
-   **A házirend-hatókörök:** globális, termék, API-művelet  

## <a name="next-steps"></a>További lépések
Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)   
