---
title: Automatikus felhasználói kiépítés engedélyezése több-bérlős alkalmazásokhoz – Azure AD
description: Útmutató független szoftvergyártók számára az automatikus kiépítés engedélyezéséhez
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 93e1d879f69a95fe7472ce530e0e9f38f3480f39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522393"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>Automatikus felhasználói kiépítés engedélyezése a több-bérlős alkalmazáshoz

Az automatikus felhasználói kiépítés a felhasználói identitások létrehozásának, karbantartásának és eltávolításának automatizálása a célrendszerekben, például a szoftverszolgáltatás-alkalmazásokban.

## <a name="why-enable-automatic-user-provisioning"></a>Miért érdemes engedélyezni az automatikus felhasználói kiépítést?

Alkalmazások, amelyek megkövetelik, hogy a felhasználói rekord jelen van az alkalmazásban, mielőtt a felhasználó első bejelentkezési felhasználói kiépítést igényel. Vannak előnyei az Ön számára, mint a szolgáltató, és előnyöket az ügyfelek számára.

### <a name="benefits-to-you-as-the-service-provider"></a>Előnyök az Ön mint szolgáltató számára

* Növelje az alkalmazás biztonságát a Microsoft identity platform használatával.

* Csökkentse a tényleges és érzékelt ügyfél-erőfeszítéseket az alkalmazás elfogadásához.

* Csökkentse a több identitásszolgáltatóval való integráció költségeit az automatikus felhasználói kiépítéshez a System for Cross-Domain Identity Management (SCIM) alapú kiépítés használatával.

* Csökkentse a támogatási költségeket azáltal, hogy gazdag naplókat biztosít az ügyfeleknek a felhasználói kiépítési problémák elhárításához.

* Az alkalmazás láthatóságának növelése az [Azure AD alkalmazásgalériában.](https://azuremarketplace.microsoft.com/marketplace/apps)

* Kiemelt adatlapot kaphat az Alkalmazásoktatói oldalon.

### <a name="benefits-to-your-customers"></a>Előnyök az ügyfelek számára

* Növelje a biztonságot azáltal, hogy automatikusan eltávolítja az alkalmazáshoz való hozzáférést azon felhasználók számára, akik szerepkört módosítanak, vagy a szervezetet az alkalmazásra hagyják.

* Egyszerűsítse az alkalmazás felhasználói felügyeletét az emberi hibák és a manuális kiépítéssel kapcsolatos ismétlődő munka elkerülésével.

* Csökkentse az egyedi fejlesztésű kiépítési megoldások üzemeltetésének és karbantartásának költségeit.

## <a name="choose-a-provisioning-method"></a>Az üzembehelyezési módszer kiválasztása

Az Azure AD számos integrációs elérési utat biztosít az alkalmazás automatikus felhasználói kiépítésének engedélyezéséhez.

* Az [Azure AD-létesítési szolgáltatás](../app-provisioning/user-provisioning.md) kezeli a kiépítés és a felhasználók kiépítése az Azure AD-től az alkalmazás (kimenő kiépítése) és az alkalmazásból az Azure AD (bejövő kiépítés). A szolgáltatás csatlakozik a System for Cross-Domain Identity Management (SCIM) felhasználófelügyeleti API-végpontokhoz, amelyeket az alkalmazás biztosít.

* A [Microsoft Graph](https://docs.microsoft.com/graph/)használatakor az alkalmazás kezeli a felhasználók és csoportok bejövő és kimenő kiépítése az Azure AD-től az alkalmazás lekérdezésével a Microsoft Graph API-t.

* A biztonsági helyestítési jelölőnyelv just in time (SAML JIT) felhasználói kiépítés akkor engedélyezhető, ha az alkalmazás SAML-t használ összevonáshoz. Az SAML-jogkivonatban küldött jogcímadatokat használja a kiépítési felhasználókszámára.

Az alkalmazáshoz használni való integrációs lehetőség meghatározásához tekintse meg a magas szintű összehasonlító táblázatot, majd tekintse meg az egyes beállításokkal kapcsolatos részletesebb információkat.

| Az automatikus kiépítés által engedélyezett vagy továbbfejlesztett funkciók| Azure AD-létesítési szolgáltatás (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Felhasználó- és csoportkezelés az Azure AD-ben| √| √| Csak felhasználó |
| A helyszíni Active Directoryból szinkronizált felhasználók és csoportok kezelése| √*| √*| Csak felhasználó* |
| Az O365-adatokhoz való hozzáférés (Csapatok, SharePoint, E-mail, Naptár, Dokumentumok stb.) kiépítése során a felhasználókon és csoportokon túli adatok elérése| X+| √| X |
| Felhasználók létrehozása, olvasása és frissítése üzleti szabályok alapján| √| √| √ |
| Felhasználók törlése üzleti szabályok alapján| √| √| X |
| Az Azure Portalon található összes alkalmazás automatikus felhasználói kiépítésének kezelése| √| X| √ |
| Több identitásszolgáltató támogatása| √| X| √ |
| Vendégfiókok támogatása (B2B)| √| √| √ |
| Nem vállalati számlák támogatása (B2C)| X| √| √ |

<sup>*</sup>– Az Azure AD Connect beállítása szükséges a felhasználók szinkronizálásához az AD-ről az Azure AD-re.  
<sup>+</sup >– Az SCIM használata a kiépítéshez nem zárja ki, hogy az alkalmazást más célokra integrálja a MIcrosoft Graph-ba.

## <a name="azure-ad-provisioning-service-scim"></a>Azure AD-létesítési szolgáltatás (SCIM)

Az Azure AD létesítési szolgáltatások [scim-et](https://aka.ms/SCIMOverview)használ, amely számos identitásszolgáltató (IdPs) és alkalmazások (pl. Slack, G Suite, Dropbox) által támogatott kiépítés iparági szabványa. Azt javasoljuk, hogy használja az Azure AD-kiépítési szolgáltatás, ha szeretné támogatni az idPs mellett az Azure AD, mint bármely SCIM-kompatibilis IdP csatlakozhat az SCIM-végponthoz. Egy egyszerű /User végpont létrehozása, engedélyezheti a kiépítés tartjuk fenn a saját szinkronizálási motorkarbantartása. 

Az Azure AD-kiépítési szolgáltatás felhasználóinak SCIM-jével kapcsolatos további információkért lásd: 

* [További információ az SCIM szabványról](https://aka.ms/SCIMOverview)

* [A System for Cross-Domain Identity Management (SCIM) használatával automatikusan kiépítheti a felhasználókat és csoportokat az Azure Active Directoryból az alkalmazásokba](../app-provisioning/use-scim-to-provision-users-and-groups.md)

* [Az Azure AD SCIM implementációjának megismerése](../app-provisioning/use-scim-to-provision-users-and-groups.md)

## <a name="microsoft-graph-for-provisioning"></a>Microsoft Graph kiépítéshez

Ha a Microsoft Graph-ot használja a kiépítéshez, hozzáférhet a Graph ban elérhető összes gazdag felhasználói adathoz. A felhasználók és csoportok részletei mellett további információkat is lehívhat, például a felhasználó szerepköreit, kezelőjét és közvetlen jelentéseit, tulajdonában lévő és regisztrált eszközeit, valamint a [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)ban elérhető több száz egyéb adatdarabot. 

Több mint 15 millió szervezet és a fortune 500-as vállalatok 90%-a használja az Azure AD-t, miközben feliratkozik a Microsoft felhőszolgáltatásaira, például az Office 365-re, a Microsoft Azure-ra, az Enterprise Mobility Suite-ra vagy a Microsoft 365-re. A Microsoft Graph segítségével integrálhatja alkalmazását felügyeleti munkafolyamatokkal, például alkalmazotti bevezetéssel (és megszüntetéssel), profilkarbantartással stb. 

További információ a Microsoft Graph kiépítésre való használatáról:

* [A Microsoft Graph kezdőlapja](https://developer.microsoft.com/graph)

* [A Microsoft Graph áttekintése](https://docs.microsoft.com/graph/overview)

* [Microsoft Graph hitelesítés – áttekintés](https://docs.microsoft.com/graph/auth/)

* [A Microsoft Graph – első lépések](https://developer.microsoft.com/graph/get-started)

## <a name="using-saml-jit-for-provisioning"></a>SAML jit használata a kiépítéshez

Ha csak az első bejelentkezéskor szeretné kiépíteni a felhasználókat az alkalmazásba, és nem kell automatikusan kiépítenie a felhasználókat, saml jit egy lehetőség. Az alkalmazásnak támogatnia kell az SAML 2.0-t összevonási protokollként az SAML JIT használatához.

Saml JIT az SAML jogkivonatban lévő jogcímadatok at használja a felhasználói adatok létrehozásához és frissítéséhez az alkalmazásban. Az ügyfelek konfigurálhatják ezeket a szükséges jogcímeket az Azure AD-alkalmazásban, ha szükséges. Előfordulhat, hogy a jit-kiépítést engedélyezni kell az alkalmazás oldalról, hogy az ügyfél használhassa ezt a funkciót. Az SAML JIT a felhasználók létrehozásához és frissítéséhez hasznos, de nem tudja törölni vagy inaktiválni az alkalmazás felhasználóit.

## <a name="next-steps"></a>Következő lépések

* [Egyszeri bejelentkezés engedélyezése az alkalmazáshoz](../manage-apps/isv-sso-content.md)

* [Küldje el alkalmazáslistáját,](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx) és lépjen kapcsolatba a Microsofttal, hogy dokumentációt hozzon létre a Microsoft webhelyén.

* [Csatlakozzon a Microsoft Partner Networkhöz (ingyenes), és készítse el a piacra jutási tervet.](https://partner.microsoft.com/en-us/explore/commercial)
