---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68361556"
---
### <a name="identity-tier"></a>Identitási rétegek 

A Microsoft-Oracle partnerség lehetővé teszi, hogy egységes identitást állítson be az Azure-ban, a OCI-ban és az Oracle-alkalmazásban. A JD Edwards EnterpriseOne vagy PeopleSoft alkalmazáscsomag esetében az Oracle HTTP-kiszolgáló (OHS) egy példánya szükséges az Azure AD és az Oracle IDCS közötti egyszeri bejelentkezés beállításához.

A OHS fordított proxyként működik az alkalmazási szinten, ami azt jelenti, hogy a végfelhasználók felé irányuló összes kérelem áthalad. Az Oracle Access Manager WebGate egy OHS webkiszolgáló beépülő modul, amely minden, a befejezési alkalmazásra vonatkozó kérelmet elfogja. Ha egy hozzáférő erőforrás védett (hitelesített munkamenet szükséges), a WebGate a felhasználó böngészőjén keresztül kezdeményezi a OIDC-hitelesítési folyamatot az Identity Cloud Service használatával. Az OpenID Connect WebGate által támogatott folyamatokkal kapcsolatos további információkért tekintse meg az [Oracle Access Manager dokumentációját](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html).

Ezzel a beállítással egy, az Azure AD-be már bejelentkezett felhasználó a JD Edwards EnterpriseOne vagy a PeopleSoft alkalmazásba való belépés nélkül, az Oracle Identity Cloud Service-en keresztül is megnyitható. A megoldást telepítő ügyfelek az egyszeri bejelentkezés előnyeit vehetik igénybe, beleértve a hitelesítő adatok egy készletét, a továbbfejlesztett bejelentkezési élményt, a jobb biztonságot és a csökkentett ügyfélszolgálati költségeket.

Ha többet szeretne megtudni a JD Edwards EnterpriseOne vagy a PeopleSoft Azure AD-vel való egyszeri bejelentkezés beállításáról, tekintse meg a kapcsolódó [Oracle](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)-tanulmányt.