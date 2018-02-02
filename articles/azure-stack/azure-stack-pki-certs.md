---
title: "Az Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványokra vonatkozó követelményei Azure verem integrált rendszerek |} Microsoft Docs"
description: "Az Azure verem nyilvános kulcsokra épülő infrastruktúra központi telepítési tanúsítványkövetelmények integrált Azure verem rendszerekhez ismerteti."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: c8dd2866e24faacfccff7f5f490710853f426345
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Az Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványkövetelmények
Azure verem van egy nyilvános infrastruktúra-hálózaton kívülről hozzáférhető nyilvános IP-címtartományból egy kis készletét a verem Azure-szolgáltatások, és esetleg a bérlői virtuális gépek használatával. Azure verem központi telepítése során a megfelelő DNS-neveit Azure verem nyilvános infrastruktúra végpontokkal PKI-tanúsítványok szükségesek. Ez a cikk nyújt tájékoztatást:

- Milyen tanúsítványokra szükség Azure verem telepítése
- A folyamat az i alapprofil megfelelő tanúsítványok beszerzése
- Hogyan lehet előkészíteni, érvényesítése és azok a tanúsítványok központi telepítése során használja

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények
Az alábbi lista a tanúsítvány Azure verem telepítéséhez szükséges követelményeket ismerteti: 
- Tanúsítványok vagy egy belső hitelesítésszolgáltatótól, vagy a nyilvános hitelesítésszolgáltatótól kell kiállítania. Egy nyilvános hitelesítésszolgáltató használata, akkor az alap operációs rendszer lemezképét a Microsoft megbízható legfelső szintű hitelesítésszolgáltatói Program részeként kell szerepelnie. A teljes listáját itt találja: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- A tanúsítványt a tulajdonos alternatív nevére (SAN) mezőben minden neve szóközt kiterjedő egyetlen helyettesítő tanúsítvány lehet. Azt is megteheti például a tároló és a kulcstároló, amennyiben azok szükségesek végpontok helyettesítő karakterek használatával az egyes tanúsítványokat is használhat. 
- A tanúsítvány-aláírási algoritmus nem lehet SHA1, erősebb kell lennie. 
- A tanúsítvány formátumban kell lennie PFX, mint a nyilvános és titkos kulcsok szükségesek az Azure-verem telepítéséhez. 
- A tanúsítvány pfx-fájlok rendelkeznie érték "Digitális aláírás" és "KeyEncipherment" a "Kulcshasználat" mezőben.
- Minden tanúsítvány pfx-fájlok a jelszavakat, meg kell egyeznie a központi telepítés során
- Győződjön meg arról, hogy a tulajdonos nevét és minden tanúsítványok tulajdonosának alternatív nevét egyezik-e a sikertelen központi telepítéssel elkerülése érdekében ebben a cikkben leírt előírásoknak.

> [!NOTE]
> A köztes hitelesítésszolgáltatókat egy tanúsítvány lánc-az-Megbízhatóságok IS jelenlétét támogatott. 

## <a name="mandatory-certificates"></a>Kötelező tanúsítványok
Ez a szakasz a táblázat az Azure-verem nyilvános végpontot PKI-tanúsítványokat, amelyek szükségesek az mindkét Azure AD és az AD FS Azure verem központi telepítéseket. Tanúsítványkövetelmények a terület, valamint a használt szerint vannak csoportosítva, és a tanúsítványokat, amelyek minden névtér esetében szükséges. A táblázat az a mappa, amelyben a megoldásszolgáltató másolja át a különböző tanúsítványok nyilvános végpontonként is ismerteti. 

Minden Azure verem nyilvános infrastruktúra végpont a megfelelő DNS-névvel rendelkező tanúsítványokra szükség. Minden egyes végpont DNS-név formátumban van megadva:  *&lt;előtag >.&lt; régió >. &lt;teljesen minősített tartományneve >*. 

A telepítéshez, a [régió] és [externalfqdn] az értékeknek egyezniük kell a terület és a külső tartományneveket, amelyekben az Azure-verem rendszer számára is választott. Tegyük fel, ha a terület neve *Redmond* és a külső tartománynév *contoso.com*, a DNS-nevek lenne a formátum  *&lt;előtag >. redmond.contoso.com* . A  *&lt;előtag >* értékek vannak predesignated megadásával írhatja le a végpontot a tanúsítvány által védett Microsoft által. Emellett a  *&lt;előtag >* értékek a külső infrastruktúra végpontok függ az adott végponti használó Azure verem-szolgáltatás. 

|Telepítési mappa|Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosnevekkel (SAN)|Hatókör (régiónként)|SubDomain namespace|
|-----|-----|-----|-----|
|Nyilvános portál|portal.*&lt;region>.&lt;fqdn>*|Portálok|*&lt;region>.&lt;fqdn>*|
|Felügyeleti portál|adminportal.*&lt;region>.&lt;fqdn>*|Portálok|*&lt;region>.&lt;fqdn>*|
|Az Azure erőforrás-kezelő nyilvános|management.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|Az Azure Resource Manager-rendszergazda|adminmanagement.*&lt;region>.&lt;fqdn>*|Azure Resource Manager|*&lt;region>.&lt;fqdn>*|
|ACS<sup>1</sup>|Tulajdonos alternatív neveit egy multi-altartomány helyettesítő tanúsítvány:<br>&#42;.blob.*&lt;region>.&lt;fqdn>*<br>&#42;.queue.*&lt;region>.&lt;fqdn>*<br>&#42;.table.*&lt;region>.&lt;fqdn>*|Tárolás|blob.*&lt;region>.&lt;fqdn>*<br>table.*&lt;region>.&lt;fqdn>*<br>queue.*&lt;region>.&lt;fqdn>*|
|KeyVault|&#42;.vault.*&lt;region>.&lt;fqdn>*<br>(Wildcard SSL Certificate)|Key Vault|vault.*&lt;region>.&lt;fqdn>*|
|KeyVaultInternal|&#42;.adminvault.*&lt;region>.&lt;fqdn>*<br>(Wildcard SSL Certificate)|Belső Keyvault|adminvault.*&lt;region>.&lt;fqdn>*|
|
<sup>1</sup> az ACS-tanúsítványt igényel a rendszer egy tanúsítványt a három helyettesítő San. Előfordulhat, hogy az összes nyilvános hitelesítésszolgáltatók nem támogatja a több helyettesítő karakterrel tárkezelő a rendszer egy tanúsítványt. 

Ha az Azure AD rendszerbe állítási mód használata Azure-vermet, csak az előző táblázatban szereplő tanúsítványok lekérésére kell. Azonban ha az AD FS telepítési móddal Azure verem, is kérnie kell a tanúsítványokat, a következő táblázat ismerteti:

|Telepítési mappa|Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosnevekkel (SAN)|Hatókör (régiónként)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(SSL Certificate)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> A jelen szakaszban felsorolt összes tanúsítvány ugyanazt a jelszót kell rendelkeznie. 

## <a name="optional-paas-certificates"></a>Nem kötelező PaaS-tanúsítványok
Ha azt tervezi, a további Azure verem PaaS szolgáltatásainak (SQL, MySQL és az App Service) telepítését követően Azure verem telepítése és konfigurálása megtörtént, szüksége lesz a fedik le a végpontok a PaaS szolgáltatások további tanúsítványok lekérésére. 

> [!IMPORTANT]
> A tanúsítványok, használhatja az App Service, az SQL és a MySQL-szolgáltatók kell rendelkeznie a azonos legfelső szintű hitelesítésszolgáltató a globális Azure verem végpontok is használt. 

A következő táblázat ismerteti a végpontok és az SQL és MySQL adapterek és az App Service szükséges tanúsítványokat. Nem kell ezeket a tanúsítványokat másolja az Azure-verem telepítési mappát. Ehelyett biztosítanak ezekről a tanúsítványokról a további erőforrás-szolgáltató telepítésekor. 

|Hatókör (régiónként)|Tanúsítvány|Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosnevek (SAN)|SubDomain namespace|
|-----|-----|-----|-----|
|SQL, MySQL|Az SQL és MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Wildcard SSL Certificate)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Webes forgalom alapértelmezett SSL-tanúsítványt|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>(Altartományokra is kibővített SSL-tanúsítvány több tartomány<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|EGYSZERI BEJELENTKEZÉS|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> több helyettesítő alternatív tulajdonosnevek, egy tanúsítványra van szükség. Előfordulhat, hogy az összes nyilvános hitelesítésszolgáltatók által nem támogatott a rendszer egy tanúsítványt a San-okon több helyettesítő 

<sup>2</sup> m &#42;. App Service.  *&lt;régió >.&lt; teljesen minősített tartományneve >* helyettesítő tanúsítvány nem használható a három tanúsítványok (api.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*, ftp.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*, és sso.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*. App Service explicit módon igényel a külön tanúsítványok ezeket a végpontokat. 

## <a name="learn-more"></a>Részletek
Megtudhatja, hogyan [Azure Alkalmazásveremben üzembe nyilvános kulcsú infrastruktúra tanúsítványainak előállításához](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>További lépések
[Identitásintegráció](azure-stack-integrate-identity.md)

