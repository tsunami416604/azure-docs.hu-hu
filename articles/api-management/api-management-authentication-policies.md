---
title: Azure API-kezelés hitelesítési házirendjei | Microsoft dokumentumok
description: Ismerje meg az Azure API Managementben használható hitelesítési szabályzatokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473557"
---
# <a name="api-management-authentication-policies"></a>API Management-hitelesítési szabályzatok
Ez a témakör a következő API Management-házirendek hivatkozási alapként szolgál. A házirendek hozzáadásáról és konfigurálásáról az [API-kezelés házirendjei](https://go.microsoft.com/fwlink/?LinkID=398186)című témakörben talál további információt.

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Hitelesítési házirendek

-   [Hitelesítés alapszintű](api-management-authentication-policies.md#Basic) - hitelesítés háttérszolgáltatással egyszerű hitelesítéssel.

-   [Hitelesítés ügyféltanúsítvánnyal](api-management-authentication-policies.md#ClientCertificate) – Hitelesítés háttérszolgáltatással az ügyféltanúsítványokkal.

-   [Hitelesítés felügyelt identitással](api-management-authentication-policies.md#ManagedIdentity) – Hitelesítse magát az API Management szolgáltatás [felügyelt identitásával.](../active-directory/managed-identities-azure-resources/overview.md)

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>Hitelesítés alapszintű sel
 A `authentication-basic` házirend segítségével hitelesítheti magát egy háttérszolgáltatás sal alapfokú hitelesítéssel. Ez a házirend hatékonyan beállítja a HTTP-engedélyezési fejlécet a házirendben megadott hitelesítő adatoknak megfelelő értékre.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Példa

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|hitelesítés-alapszintű|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|felhasználónév|Megadja az alapszintű hitelesítő adatok felhasználónevét.|Igen|N/A|
|jelszó|Megadja az alapszintű hitelesítő adatok jelszavát.|Igen|N/A|

### <a name="usage"></a>Használat
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>Hitelesítés ügyféltanúsítvánnyal
 A `authentication-certificate` házirend segítségével hitelesítheti magát egy háttérszolgáltatással az ügyféltanúsítvány használatával. A tanúsítványt először telepíteni kell az [API Management be,](https://go.microsoft.com/fwlink/?LinkID=511599) és az ujjlenyomata azonosítja.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Példák

Ebben a példában az ügyféltanúsítványt az ujjlenyomata azonosítja.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Ebben a példában az ügyféltanúsítványt az erőforrás neve azonosítja.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elemek  
  
|Név|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|hitelesítési tanúsítvány|Gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Kötelező|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|Ujjlenyomat|Az ügyféltanúsítvány ujjlenyomata.|Vagy `thumbprint` `certificate-id` jelen kell lennie, vagy jelen kell lennie.|N/A|  
|tanúsítvány-azonosító|A tanúsítványerőforrás neve.|Vagy `thumbprint` `certificate-id` jelen kell lennie, vagy jelen kell lennie.|N/A|  
  
### <a name="usage"></a>Használat  
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.  
  
-   **Házirendszakaszok:** bejövő  
  
-   **Házirend-hatókörök:** az összes hatókör  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Hitelesítés felügyelt identitással  
 A `authentication-managed-identity` házirend használatával hitelesítheti magát egy háttérszolgáltatás az API Management szolgáltatás felügyelt identitásával. Ez a szabályzat lényegében a felügyelt identitás t használja egy hozzáférési jogkivonatot az Azure Active Directoryból a megadott erőforrás eléréséhez. A jogkivonat sikeres beszerzése után a házirend a `Authorization` séma használatával állítja be a jogkivonat értékét a `Bearer` fejlécben.
  
### <a name="policy-statement"></a>Politikai nyilatkozat  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Példa  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Felügyelt identitás használata a háttérszolgáltatással való hitelesítéshez
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Felügyelt identitás használata a küldési kérelem házirendben
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elemek  
  
|Név|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|hitelesítés-felügyelt identitás |Gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Kötelező|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|Erőforrás|Sztring. A célweb API (biztonságos erőforrás) alkalmazásazonosítója az Azure Active Directoryban.|Igen|N/A|  
|kimeneti token-változó-név|Sztring. Annak a környezeti változónak a neve, `string`amely objektumtípusként kap tokenértéket. |Nem|N/A|  
|ignore-hiba|Logikai. Ha a `true`beállítás, a házirend-folyamat akkor is folytatja a végrehajtást, ha nem szerez hozzáférési jogkivonatot.|Nem|hamis|  
  
### <a name="usage"></a>Használat  
 Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.  
  
-   **Házirendszakaszok:** bejövő  
  
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések
A házirendekkel kapcsolatos további információkért lásd:

+ [Szabályzatok az API Managementben](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
+ [Házirendminták](policy-samples.md)
