---
title: Az Azure verem nyilvános kulcsokra épülő infrastruktúrát tanúsítványokra vonatkozó követelményei Azure verem integrált rendszerek |} Microsoft Docs
description: Az Azure verem nyilvános kulcsokra épülő infrastruktúra központi telepítési tanúsítványkövetelmények integrált Azure verem rendszerekhez ismerteti.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: 9a43179998e8377dfbbb1a41ba7d46936d63aedd
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030155"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Az Azure verem nyilvános kulcsokra épülő infrastruktúra tanúsítványkövetelmények

Azure verem van egy nyilvános infrastruktúra-hálózaton kívülről hozzáférhető nyilvános IP-címtartományból egy kis készletét a verem Azure-szolgáltatások, és esetleg a bérlői virtuális gépek használatával. Azure verem központi telepítése során a megfelelő DNS-neveit Azure verem nyilvános infrastruktúra végpontokkal PKI-tanúsítványok szükségesek. Ez a cikk nyújt tájékoztatást:

- Milyen tanúsítványokra szükség Azure verem telepítése
- A folyamat az i alapprofil megfelelő tanúsítványok beszerzése
- Hogyan lehet előkészíteni, érvényesítése és azok a tanúsítványok központi telepítése során használja

> [!Note]  
> Központi telepítése során a telepítési mappát, amely megfelel az identitásszolgáltató (az Azure AD vagy AD FS) ellen telepít tanúsítványokat kell másolnia. Végpontjai használja a rendszer egy tanúsítványt, ha minden központi telepítési mappába, az alábbi táblázatban leírt módon, hogy a tanúsítványfájl kell másolnia. A mappastruktúra a központi telepítés virtuális gépen előre épül, és helyen találhatók: C:\CloudDeployment\Setup\Certificates. 

## <a name="certificate-requirements"></a>Tanúsítványkövetelmények
Az alábbi lista a tanúsítvány Azure verem telepítéséhez szükséges követelményeket ismerteti: 
- Tanúsítványok vagy egy belső hitelesítésszolgáltatótól, vagy a nyilvános hitelesítésszolgáltatótól kell kiállítania. Egy nyilvános hitelesítésszolgáltató használata, akkor az alap operációs rendszer lemezképét a Microsoft megbízható legfelső szintű hitelesítésszolgáltatói Program részeként kell szerepelnie. A teljes listáját itt találja: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Az Azure-verem infrastruktúra hálózati hozzáféréssel kell rendelkeznie a tanúsítvány közzé a hitelesítésszolgáltató visszavont tanúsítványok listáját (CRL) helyre. A tanúsítvány-visszavonási listát egy http-végpont kell lennie.
- Váltás tanúsítványokat, amikor tanúsítványokat kell lennie, vagy a központi telepítés vagy a nyilvános hitelesítésszolgáltatótól származó fent megadott tanúsítványok aláírására használt azonos belső hitelesítésszolgáltatótól származó kiadott
- Az önaláírt tanúsítványok nem támogatottak.
- A tanúsítványt a tulajdonos alternatív nevére (SAN) mezőben minden neve szóközt kiterjedő egyetlen helyettesítő tanúsítvány lehet. Azt is megteheti, például az végpontok helyettesítő karakterek használatával az egyes tanúsítványokat is használhat **acs** és a kulcstároló, amennyiben azok szükségesek. 
- A tanúsítvány-aláírási algoritmus nem lehet SHA1, erősebb kell lennie. 
- A tanúsítvány formátumban kell lennie PFX, mint a nyilvános és titkos kulcsok szükségesek az Azure-verem telepítéséhez. 
- A tanúsítvány pfx-fájlok rendelkeznie érték "Digitális aláírás" és "KeyEncipherment" a "Kulcshasználat" mezőben.
- A tanúsítvány pfx-fájlok rendelkeznie kell az "Kiszolgálói hitelesítés (1.3.6.1.5.5.7.3.1)" és "Ügyfél-hitelesítés (1.3.6.1.5.5.7.3.2)" értéket a "Kibővített kulcshasználat" mezőben.
- A tanúsítvány "kiállítva a következőnek:" mező nem lehet ugyanaz, mint a "kiállító:" mező.
- Minden tanúsítvány pfx-fájlok a jelszavakat, meg kell egyeznie a központi telepítés során
- Jelszó a tanúsítvány PFX-nak kell lennie egy összetett jelszót.
- Ellenőrizze, hogy a tulajdonos neve és a tulajdonos alternatív nevének bővítmény (x509v3_config) egyeznie kell az alternatív tulajdonosnevekkel. A tulajdonos alternatív neve mezőnek további állomásnév (webhelyek, IP-címek, köznapi nevek) védelemmel egyetlen SSL-tanúsítvány megadását teszi lehetővé.

> [!NOTE]  
> Aláírt önaláírt tanúsítványok használata nem támogatott.

> [!NOTE]  
> A köztes hitelesítésszolgáltatókat egy tanúsítvány lánc-az-Megbízhatóságok IS jelenlétét támogatott. 

## <a name="mandatory-certificates"></a>Kötelező tanúsítványok
Ez a szakasz a táblázat az Azure-verem nyilvános végpontot PKI-tanúsítványokat, amelyek szükségesek az mindkét Azure AD és az AD FS Azure verem központi telepítéseket. Tanúsítványkövetelmények a terület, valamint a használt szerint vannak csoportosítva, és a tanúsítványokat, amelyek minden névtér esetében szükséges. A táblázat az a mappa, amelyben a megoldásszolgáltató másolja át a különböző tanúsítványok nyilvános végpontonként is ismerteti. 

Minden Azure verem nyilvános infrastruktúra végpont a megfelelő DNS-névvel rendelkező tanúsítványokra szükség. Minden egyes végpont DNS-név formátumban van megadva:  *&lt;előtag >.&lt; régió >. &lt;teljesen minősített tartományneve >*. 

A telepítéshez, a [régió] és [externalfqdn] az értékeknek egyezniük kell a terület és a külső tartományneveket, amelyekben az Azure-verem rendszer számára is választott. Tegyük fel, ha a terület neve *Redmond* és a külső tartománynév *contoso.com*, a DNS-nevek lenne a formátum *&lt;előtag >. redmond.contoso.com*. A  *&lt;előtag >* értékek vannak predesignated megadásával írhatja le a végpontot a tanúsítvány által védett Microsoft által. Emellett a  *&lt;előtag >* értékek a külső infrastruktúra végpontok függ az adott végponti használó Azure verem-szolgáltatás. 

> [!note]  
> Tanúsítványok csak megadott összes névtér a tulajdonos és a tulajdonos alternatív nevére (SAN) mezőben, átmásolja az összes könyvtár kiterjedő egyetlen helyettesítő tanúsítvány, vagy minden egyes tanúsítványok végpont másolni a megfelelő könyvtárba. Ne feledje, hogy mindkét lehetőség használatát írják elő helyettesítő tanúsítványokat végpontok például **acs** és a kulcstároló, amennyiben azok szükségesek. 

| Telepítési mappa | Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosnevekkel (SAN) | Hatókör (régiónként) | Altartomány névtér |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Nyilvános portál | portál. &lt;régió >. &lt;teljesen minősített tartományneve > | Portálok | &lt;region>.&lt;fqdn> |
| Felügyeleti portál | adminportal. &lt;régió >. &lt;teljesen minősített tartományneve > | Portálok | &lt;region>.&lt;fqdn> |
| Az Azure erőforrás-kezelő nyilvános | management.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| Az Azure Resource Manager-rendszergazda | adminmanagement. &lt;régió >. &lt;teljesen minősített tartományneve > | Azure Resource Manager | &lt;region>.&lt;fqdn> |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Blob Storage | blob.&lt;region>.&lt;fqdn> |
| ACSTable | *.table.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Table Storage | table.&lt;region>.&lt;fqdn> |
| ACSQueue | *.queue.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Queue Storage | queue.&lt;region>.&lt;fqdn> |
| KeyVault | *.vault.&lt;region>.&lt;fqdn><br>(Altartományokra is kibővített SSL-tanúsítvány) | Key Vault | vault.&lt;region>.&lt;fqdn> |
| KeyVaultInternal | *.adminvault. &lt;régió >. &lt;teljesen minősített tartományneve ><br>(Altartományokra is kibővített SSL-tanúsítvány) |  Belső Keyvault |  adminvault.&lt;region>.&lt;fqdn> |

Ha az Azure AD rendszerbe állítási mód használata Azure-vermet, csak az előző táblázatban szereplő tanúsítványok lekérésére kell. Azonban ha az AD FS telepítési móddal Azure verem, is kérnie kell a tanúsítványokat, a következő táblázat ismerteti:

|Telepítési mappa|Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosnevekkel (SAN)|Hatókör (régiónként)|Altartomány névtér|
|-----|-----|-----|-----|
|ADFS|adfs.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány)|ADFS|*&lt;region>.&lt;fqdn>*|
|Graph|graph.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány)|Graph|*&lt;region>.&lt;fqdn>*|
|

> [!IMPORTANT]
> A jelen szakaszban felsorolt összes tanúsítvány ugyanazt a jelszót kell rendelkeznie. 

## <a name="optional-paas-certificates"></a>Nem kötelező PaaS-tanúsítványok
Ha azt tervezi, a további Azure verem PaaS szolgáltatásainak (SQL, MySQL és az App Service) telepítését követően Azure verem telepítése és konfigurálása megtörtént, szüksége lesz a fedik le a végpontok a PaaS szolgáltatások további tanúsítványok lekérésére. 

> [!IMPORTANT]
> A tanúsítványok, használhatja az App Service, az SQL és a MySQL-szolgáltatók kell rendelkeznie a azonos legfelső szintű hitelesítésszolgáltató a globális Azure verem végpontok is használt. 

A következő táblázat ismerteti a végpontok és az SQL és MySQL adapterek és az App Service szükséges tanúsítványokat. Nem kell ezeket a tanúsítványokat másolja az Azure-verem telepítési mappát. Ehelyett biztosítanak ezekről a tanúsítványokról a további erőforrás-szolgáltató telepítésekor. 

|Hatókör (régiónként)|Tanúsítvány|Szükséges tanúsítvány tulajdonosára és alternatív tulajdonosnevek (SAN)|Altartomány névtér|
|-----|-----|-----|-----|
|SQL, MySQL|Az SQL és MySQL|&#42;.dbadapter.*&lt;region>.&lt;fqdn>*<br>(Altartományokra is kibővített SSL-tanúsítvány)|dbadapter.*&lt;region>.&lt;fqdn>*|
|App Service|Webes forgalom alapértelmezett SSL-tanúsítványt|&#42;.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice.*&lt;region>.&lt;fqdn>*<br>(Altartományokra is kibővített SSL-tanúsítvány több tartomány<sup>1</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|API|api.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|
|App Service|SSO|sso.appservice.*&lt;region>.&lt;fqdn>*<br>(SSL-tanúsítvány<sup>2</sup>)|appservice.*&lt;region>.&lt;fqdn>*<br>scm.appservice.*&lt;region>.&lt;fqdn>*|

<sup>1</sup> több helyettesítő alternatív tulajdonosnevek, egy tanúsítványra van szükség. Előfordulhat, hogy az összes nyilvános hitelesítésszolgáltatók által nem támogatott a rendszer egy tanúsítványt a San-okon több helyettesítő 

<sup>2</sup> A &#42;.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >* helyettesítő tanúsítvány nem használható a három tanúsítványok (api.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*, ftp.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*, és sso.appservice. *&lt;régió >. &lt;teljesen minősített tartományneve >*. App Service explicit módon igényel a külön tanúsítványok ezeket a végpontokat. 

## <a name="learn-more"></a>Részletek
Megtudhatja, hogyan [Azure Alkalmazásveremben üzembe nyilvános kulcsú infrastruktúra tanúsítványainak előállításához](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>További lépések
[Identitásintegráció](azure-stack-integrate-identity.md)

