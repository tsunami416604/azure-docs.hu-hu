---
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/10/2019
ms.author: danlep
ms.openlocfilehash: c1cca99a04e78c40cdd1061e52e49f45a73c73eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68361556"
---
### <a name="identity-tier"></a>Identitásszint 

A Microsoft-Oracle partnerség lehetővé teszi, hogy egységes identitást állítson be az Azure- és OCI-ben és az Oracle-alkalmazásban. A JD Edwards EnterpriseOne vagy a PeopleSoft alkalmazáscsomag esetében az Oracle HTTP Server (OHS) egy példánya szükséges az Azure AD és az Oracle IDCS közötti egyszeri bejelentkezés beállításához.

Az OHS az alkalmazásréteg fordított proxyjaként működik, ami azt jelenti, hogy a végső alkalmazásokhoz intézett összes kérelem átmegy rajta. Oracle Access Manager WebGate egy OHS webszerver plugin, amely elfogja minden kérelmet megy a végén alkalmazás. Ha egy elérésű erőforrás védett (hitelesített munkamenetet igényel), a WebGate a felhasználó böngészőjében keresztül elindítja az OIDC hitelesítési folyamatot az Identity Cloud Service szolgáltatással. Az OpenID Connect WebGate által támogatott folyamatokról az [Oracle Access Manager dokumentációjában](https://docs.oracle.com/en/middleware/idm/access-manager/12.2.1.3/aiaag/integrating-webgate-oidc-server.html)talál további információt.

Ezzel a beállítással az Azure AD-be már bejelentkezett felhasználó az Oracle Identity Cloud Service szolgáltatáson keresztül újra bejelentkezve navigálhat a JD Edwards EnterpriseOne vagy a PeopleSoft alkalmazáshoz. A megoldást üzembe helyező ügyfelek az egyszeri bejelentkezés előnyeit élvezhetik, beleértve az egyetlen hitelesítő adatokat, a jobb bejelentkezési élményt, a jobb biztonságot és az ügyfélszolgálati költségek csökkentését.

Ha többet szeretne tudni a JD Edwards EnterpriseOne vagy a PeopleSoft Azure AD-vel való egyszeri bejelentkezés beállításáról, olvassa el a kapcsolódó [Oracle tanulmányt.](https://cloud.oracle.com/iaas/whitepapers/deploy_peoplesoft_jdedwards_across_oci_azure.pdf)