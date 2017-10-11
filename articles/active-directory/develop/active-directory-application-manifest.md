---
title: "Az Azure Active Directory Alkalmazásjegyzékének megismerése |} Microsoft Docs"
description: "Az Azure Active Directory alkalmazásjegyzék, amely jelöli az identitás alkalmazást az Azure AD-bérlő alkalmaz, és lehetővé teszi az OAuth hitelesítési, hozzájárulási élmény és további részletes körét."
services: active-directory
documentationcenter: 
author: sureshja
manager: mbaldwin
editor: 
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: sureshja
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: d5e18f41d6eb69ccb7eafaa4de2646c4c38df5e2
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="understanding-the-azure-active-directory-application-manifest"></a>Az Azure Active Directory alkalmazásjegyzékének megismerése
Alkalmazások, amelyek integrálják az Azure Active Directory (AD) regisztrálni kell az Azure AD-bérlő, az alkalmazás állandó identitása konfigurációjának megadása. Ez a konfiguráció a futásidőben, engedélyezése, hogy az alkalmazás erőforrás kihelyezése és az Azure AD a hitelesítési/engedélyezési replikaszervező forgatókönyvek megkeresett van. Az Azure AD alkalmazásmodell kapcsolatos további információkért tekintse meg a [hozzáadása, frissítése és eltávolítása egy alkalmazás] [ ADD-UPD-RMV-APP] cikk.

## <a name="updating-an-applications-identity-configuration"></a>Az alkalmazás identitását konfigurációjának frissítése
Ténylegesen több lehetőség áll rendelkezésre az alkalmazás identitását konfigurációban, képességeit és nehezen, beleértve a következő mértékben változó tulajdonságainak frissítése:

* A  **[Azure portal] [ AZURE-PORTAL] webes felhasználói felület** lehetővé teszi egy alkalmazás általános tulajdonságainak frissítéséhez. Ez a leggyorsabb és legalább egy hiba nagyon eséllyel fordulnak elő módja az alkalmazás tulajdonságainak frissítése, de nem Ön teljes hozzáférést biztosít minden tulajdonságai, például a következő két módszer.
* Speciális forgatókönyvek esetén be kell frissíteni, amelyek nem érhetők el a klasszikus Azure portálon tulajdonságait, módosíthatja a **alkalmazásjegyzék**. Ez a cikk a fókusz, és további részletes információkat a következő szakaszban indítása.
* Akkor is lehet **használó alkalmazásokat a [Graph API] [ GRAPH-API]**  frissítése az alkalmazás, amely a legtöbb beavatkozást igényel. Ennek az lehet vonzó lehetőség, ha, ha felügyeleti szoftver ír, vagy alkalmazás tulajdonságai automatizált módon rendszeresen frissíteni kell.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Az alkalmazás jegyzékében használata az alkalmazás identitását konfiguráció frissítése
Keresztül a [Azure-portálon][AZURE-PORTAL], az alkalmazás identitáskonfigurációs kezelheti az alkalmazás jegyzékében a beágyazott jegyzék szerkesztővel frissítésével. Letöltheti, és töltse fel az alkalmazás jegyzékében JSON-fájlként. Nincs tényleges fájl a könyvtárban található. Az alkalmazás jegyzékében csupán egy HTTP GET művelet az Azure AD Graph API-alkalmazás entitáson, és a feltöltés során HTTP javítására, de az alkalmazás entitáson.

Ennek eredményeképpen a formátum és az alkalmazás jegyzékében tulajdonságainak megértése, fog hivatkoznia kell a Graph API [alkalmazás entitás] [ APPLICATION-ENTITY] dokumentációját. Például, ha az alkalmazás jegyzékében feltöltés végrehajtható frissítések:

* **Deklarálja engedélyhatókörök (oauth2Permissions)** jelennek meg, ha a webes API-t. A "Kitettségének webes API-k az egyéb alkalmazások" témakörében [alkalmazások integrálása az Azure Active Directoryval] [ INTEGRATING-APPLICATIONS-AAD] információk segítségével a oauth2Permissions felhasználói megszemélyesítés delegált engedélyek hatókör. Ahogy korábban említettük, a alkalmazás entitás tulajdonságai szerepelnek-e a Graph API [entitás és a komplex típus] [ APPLICATION-ENTITY] áttekintésével foglalkozó cikkben, beleértve a oauth2Permissions tulajdonság, amely gyűjtemény a típus [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
* **Deklarálja az alkalmazás által elérhetővé tett alkalmazás-szerepkörök (appRoles)**. Az alkalmazás entitás appRoles tulajdonság típusú gyűjteményhez [birtokolhat][APPLICATION-ENTITY-APP-ROLE]. Tekintse meg a [szerepköralapú hozzáférés-vezérlés az Azure AD felhőalapú alkalmazások] [ RBAC-CLOUD-APPS-AZUREAD] cikk egy megvalósítási példát.
* **Deklarálja ismert ügyfél alkalmazások (knownClientApplications)**, amelyek lehetővé teszik, hogy az erőforrás vagy webes API-hoz a megadott ügyfél alkalmazás(ok) beleegyezése logikailag kötése.
* **Azure AD-csoport tagságát jogcímet kérelem** a bejelentkezett felhasználó (groupMembershipClaims).  Ez is konfigurálhatja a felhasználók directory szerepkörök tagságát kapcsolatos jogcímek kiállítására. Tekintse meg a [felhőalapú alkalmazások AD-csoportok használata az engedélyezési] [ AAD-GROUPS-FOR-AUTHORIZATION] cikk egy megvalósítási példát.
* **Az alkalmazás OAuth 2.0 Implicit grant támogatásához** adatfolyamok (oauth2AllowImplicitFlow). Az ilyen típusú grant flow beágyazott JavaScript weblapok vagy a lap alkalmazások (SPA) használatos. Az implicit hitelesítésengedélyezési további információkért lásd: [OAuth2 implicit ismertetése adja meg az Azure Active Directoryban folyamat][IMPLICIT-GRANT].
* **X509 használatának engedélyezése a titkos kulcs tanúsítványokhoz** (keyCredentials). Tekintse meg a [Office 365 szolgáltatás és -démon alkalmazások készítéséhez] [ O365-SERVICE-DAEMON-APPS] és [fejlesztői útmutatója a hitelesítés az Azure Resource Manager API-JÁVAL] [ DEV-GUIDE-TO-AUTH-WITH-ARM] cikkek megvalósítási példákat.
* **Adja hozzá egy új App ID URI** az alkalmazáshoz (identifierURIs[]). App ID URI-k segítségével egyedi módon azonosítja az alkalmazás az Azure AD-bérlő (vagy több Azure AD-bérlőre, több-bérlős forgatókönyvek minősített ellenőrzött egyéni tartomány keresztül). Egy erőforrás-alkalmazást, vagy egy erőforrás-alkalmazást a hozzáférési token beszerzése jogosultságokkal kérésekor használják. Ha ezt az elemet frissít, ugyanazt a frissítést él, az alkalmazás saját bérlőt a megfelelő szolgáltatás egyszerű servicePrincipalNames [] gyűjtemény történik.

Az alkalmazás jegyzékében is biztosít a legegyszerűbben az alkalmazás regisztrációja állapotának nyomon követését. JSON-formátumban, mert a fájl megjelenítése a verziókövetési, valamint az alkalmazás forráskódjához való ellenőrizhetők.

## <a name="step-by-step-example"></a>Lépés példa alapján lépés
Most már lehetővé teszi, hogy végezze el az alkalmazás identitását konfigurációs keresztül az alkalmazás jegyzékében frissítéséhez szükséges lépéseket. Ki a fenti példákban egyik bemutatja, hogyan meg kell adnia egy új engedély hatókör egy erőforrás-alkalmazáshoz:

1. Jelentkezzen be az [Azure Portalra][AZURE-PORTAL].
2. Ön már hitelesítése után válassza ki az Azure AD-bérlő el az oldal jobb felső sarkában.
3. Válassza ki **Azure Active Directory** a bal oldali navigációs panelen, majd kattintson a bővítmény **App regisztrációk**.
4. Frissítse a listában, és kattintson rá alkalmazás megkeresése.
5. Az alkalmazás lapon kattintson a **Manifest** a jegyzék beágyazott-szerkesztő megnyitásához. 
6. Közvetlenül szerkesztheti a jegyzéket a szerkesztő. Vegye figyelembe, hogy a jegyzék követi a sémát a [alkalmazás entitás] [ APPLICATION-ENTITY] azt a korábban említett: például, feltéve, hogy szeretnénk megvalósítása/elérhetővé tétele egy új engedély hívása "Employees.Read.All" a erőforrás-alkalmazáshoz (API), akkor volna egyszerűen vegyen fel egy új/másodperc oauth2Permissions gyűjtemény ie:
   
        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],
   
    A bejegyzés egyedinek kell lennie, és ezért létre kell hoznia egy új globálisan egyedi Azonosítót (GUID) a `"id"` tulajdonság. Ebben az esetben mivel azt a megadott `"type": "User"`, ezzel az engedéllyel is kell átadni kívánt hozzájárult e által az Azure AD által hitelesített bármely fiók bérlői található az erőforrás/API-alkalmazás regisztrálva van. Ez engedélyezi az ügyfél-e férni a fiók nevében alkalmazás számára. A leírás és megjelenítendő név karakterláncok hozzájárulási során, és az Azure-portálon megjelenítendő szolgálnak.
6. Ha végzett a jegyzékfájl frissítése, kattintson a **mentése** a jegyzékfájl mentése.  
   
Most, hogy a jegyzékfájl mentése adhat a regisztrált ügyfél alkalmazás-hozzáférés a fentiekben hozzáadott új engedélyt. Ezúttal az Azure-portál webes felhasználói felületén használhatja az ügyfél alkalmazásjegyzék szerkesztése helyett:  

1. Nyissa meg a a **beállítások** , amelyhez hozzá kívánja hozzáféréssel az új API-t, hogy az ügyfélalkalmazás panelen kattintson a **szükséges engedélyek** válassza **API kiválasztása**.
2. Majd meg fog jelenni a bérlő (API-k) regisztrált erőforrás-alkalmazások listáját. Kattintson rá kattintva jelölje ki az erőforrás-alkalmazáshoz, vagy írja be a keresőmezőbe alkalmazás nevét. Ha az alkalmazás talált, kattintson **válasszon**.  
3. Ekkor megjelenik a a **Select engedélyeket** lap, amely elérhetővé válik az erőforrás alkalmazás delegált engedélyek és Alkalmazásengedélyek listáját. Válassza ki ahhoz, hogy vegye fel azt az ügyfél kért engedélyeket az új engedély. Ez az új engedély az ügyfélalkalmazás identitáskonfigurációs, a "requiredResourceAccess" gyűjteménytulajdonság tárolódnak.


Ennyi az egész. Most már az alkalmazásokat az új identitás-konfigurációt használni fog futni.

## <a name="next-steps"></a>Következő lépések
* Az alkalmazás és szolgáltatás egyszerű objektumok az alkalmazás közötti kapcsolat a további részletekért lásd: [alkalmazás és szolgáltatás egyszerű objektumok az Azure AD][AAD-APP-OBJECTS].
* Tekintse meg a [az Azure AD fejlesztői szószedet] [ AAD-DEVELOPER-GLOSSARY] bizonyos Azure Active Directory (AD) fejlesztői alapfogalmakat-definíciók.

A Megjegyzések az alábbi szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

