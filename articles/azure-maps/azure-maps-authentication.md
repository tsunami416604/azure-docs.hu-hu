---
title: Hitelesítési módszerek
titleSuffix: Azure Maps
description: Ebben a cikkben megismerheti a Azure Active Directory és a megosztott kulcsos hitelesítést. Mindkettő a Microsoft Azure Maps-szolgáltatásokhoz használatos. Útmutató Azure Maps előfizetési kulcs beszerzéséhez.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e270a9a1a773442a4d1d28d9bbfe37639cac7c65
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133384"
---
# <a name="authentication-with-azure-maps"></a>Hitelesítés az Azure Maps használatával

Azure Maps a kérelmek hitelesítésének két módját támogatja: megosztott kulcsos hitelesítés és [Azure Active Directory (Azure ad)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) hitelesítés. Ez a cikk ezeket a hitelesítési módszereket ismerteti Azure Maps szolgáltatások megvalósításának elősegítése érdekében.

> [!NOTE]
> A Azure Mapsekkel való biztonságos kommunikáció javítása érdekében mostantól támogatjuk a Transport Layer Security (TLS) 1,2, és a TLS 1,0 és 1,1 támogatását kihasználjuk. A szolgáltatás megszakadásának elkerülése érdekében **frissítse a kiszolgálókat és az alkalmazásokat a TLS 1,2 használatára a 2020. április 2. előtt**.  Ha jelenleg a TLS 1. x protokollt használja, értékelje ki a TLS 1,2 készültséget, és fejlesszen egy áttelepítési tervet a [tls 1,0-probléma megoldása](https://docs.microsoft.com/security/solving-tls1-problem)című cikkben ismertetett teszteléssel.

## <a name="shared-key-authentication"></a>Megosztott kulcsos hitelesítés

 Az elsődleges és a másodlagos kulcsok a Azure Maps fiók létrehozása után jönnek létre. Javasoljuk, hogy a megosztott kulcsos hitelesítés használatával Azure Maps meghívásakor az elsődleges kulcsot használja előfizetési kulcsként. A megosztott kulcsos hitelesítés egy Azure Maps fiók által generált kulcsot ad át egy Azure Maps szolgáltatáshoz. A szolgáltatásokra Azure Maps minden egyes kérelemhez adja hozzá az *előfizetési kulcsot* paraméterként az URL-címhez. A másodlagos kulcsot olyan helyzetekben lehet használni, mint például a kulcsok változásai.  

A kulcsok a Azure Portalban való megtekintésével kapcsolatos információkért lásd: a [hitelesítés kezelése](https://aka.ms/amauthdetails).

> [!Tip]
> Javasoljuk, hogy rendszeresen újragenerálja a kulcsokat. Két kulccsal van ellátva, így a kapcsolatok egy kulccsal kezelhetők, miközben újragenerálja a másikat. A kulcsok újragenerálása után frissítenie kell minden olyan alkalmazást, amely hozzáfér a fiókjához az új kulcsokkal.

## <a name="azure-ad-authentication"></a>Azure AD-hitelesítés

Az Azure-előfizetések egy Azure AD-Bérlővel érhetők el, és lehetővé teszik a részletes hozzáférés-vezérlést. A Azure Maps az Azure AD-vel Azure Maps-szolgáltatások hitelesítését nyújtja. Az Azure AD identitás-alapú hitelesítést biztosít az Azure AD-bérlőben regisztrált felhasználók és alkalmazások számára.

Azure Maps elfogadja a **OAuth 2,0** hozzáférési jogkivonatokat egy Azure Maps-fiókot tartalmazó Azure-előfizetéshez társított Azure ad-bérlők számára. A Azure Maps a következő jogkivonatokat is elfogadja:

* Azure AD-felhasználók
* A felhasználók által delegált engedélyeket használó partneri alkalmazások
* Azure-erőforrások felügyelt identitásai

A Azure Maps minden Azure Maps-fiókhoz létrehoz egy *egyedi azonosítót (ügyfél-azonosítót)* . Ha az ügyfél-azonosítót további paraméterekkel kombinálja, kérhet tokeneket az Azure AD-től.

További információ az Azure AD konfigurálásáról és a Azure Maps jogkivonatok igényléséről: [a Azure Maps hitelesítésének kezelése](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

Az Azure AD-vel történő hitelesítéssel kapcsolatos általános információkért lásd: [Mi az a hitelesítés?](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Felügyelt identitások az Azure-erőforrásokhoz és a Azure Maps

Az Azure- [erőforrások felügyelt identitásai](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) biztosítják az Azure-szolgáltatásokat egy automatikusan felügyelt alkalmazás-alapú rendszerbiztonsági tag számára, amely képes hitelesíteni az Azure ad-t. A szerepköralapú hozzáférés-vezérlés (RBAC) révén a felügyelt identitás rendszerbiztonsági tag jogosult a Azure Maps-szolgáltatások elérésére. Néhány példa a felügyelt identitásokra: Azure App Service, Azure Functions és Azure Virtual Machines. A felügyelt identitások listáját lásd: [felügyelt identitások az Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).

### <a name="configuring-application-azure-ad-authentication"></a>Az alkalmazás Azure AD-hitelesítésének konfigurálása

Az alkalmazások az Azure ad-ben egy vagy több támogatott forgatókönyv használatával hitelesítve lesznek az Azure AD-Bérlővel. Az egyes Azure AD-alkalmazási forgatókönyvek az üzleti igények alapján eltérő követelményeket jelentenek. Egyes alkalmazások felhasználói bejelentkezést igényelhetnek, és más alkalmazásokhoz is szükség lehet az alkalmazás bejelentkezési élményére. További információ: [hitelesítési folyamatok és alkalmazási forgatókönyvek](https://docs.microsoft.com/azure/active-directory/develop/authentication-flows-app-scenarios).

Miután az alkalmazás hozzáférési jogkivonatot kap, az SDK és/vagy az alkalmazás egy HTTPS-kérést küld a következő, a szükséges HTTP-fejléceket tartalmazó, a többi REST API HTTP-fejléceken kívül:

| Fejléc neve    | Érték               |
| :------------- | :------------------ |
| x-MS-Client-ID | 30d7cc....9f55        |
| Engedélyezés  | Tulajdonos eyJ0e.... HNIVN |

> [!Note]
> `x-ms-client-id`a Azure Maps fiók-alapú GUID, amely megjelenik a Azure Maps hitelesítés lapon.

Íme egy példa egy Azure Maps Route-kérelemre, amely egy Azure AD OAuth-tulajdonosi jogkivonatot használ:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Az ügyfél-azonosító megtekintésével kapcsolatos információkért lásd a [hitelesítési adatok megtekintése](https://aka.ms/amauthdetails)című témakört.

## <a name="authorization-with-role-based-access-control"></a>Engedélyezés szerepköralapú hozzáférés-vezérléssel

Azure Maps támogatja az Azure [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/overview) összes elsődleges típusának elérését, többek között a következőket: egyéni Azure ad-felhasználók,-csoportok,-alkalmazások, Azure-erőforrások és az Azure által felügyelt identitások. A rendszerbiztonsági tag-típusok engedélyeket, más néven szerepkör-definíciókat kapnak. A szerepkör-definíciók engedélyeket biztosítanak a REST API műveletekhez. Egy vagy több Azure Maps-fiókhoz való hozzáférés alkalmazása hatókörként ismert. A rendszerbiztonsági tag, a szerepkör-definíció és a hatókör alkalmazásakor a rendszer létrehoz egy szerepkör-hozzárendelést. 

A következő részek az Azure AD szerepköralapú hozzáférés-vezérléssel való Azure Maps integrációjának alapfogalmait és összetevőit tárgyalják. A Azure Maps-fiók beállításának részeként egy Azure AD-címtár van társítva ahhoz az Azure-előfizetéshez, amelyhez a Azure Maps-fiók található. 

Az Azure RBAC konfigurálásakor ki kell választania egy rendszerbiztonsági tag szerepkör-hozzárendelését. Ha szeretné megtudni, hogyan adhat hozzá szerepkör-hozzárendeléseket a Azure Portal, tekintse meg az [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)című témakört.

### <a name="picking-a-role-definition"></a>Szerepkör-definíció kiválasztása

A következő szerepkör-definíciós típusok léteznek az alkalmazási forgatókönyvek támogatásához.

| Azure-szerepkör definíciója       | Description                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Adatolvasó Azure Maps      | Hozzáférést biztosít a nem változtatható Azure Maps REST API-khoz.                                                       |
| Azure Maps adatközreműködő | Hozzáférést biztosít a megváltoztathatatlan Azure Maps REST API-khoz. A változékonyság a műveletek: írás és törlés művelet határozza meg. |
| Egyéni szerepkör-definíció      | Hozzon létre egy kialakított szerepkört, amely lehetővé teszi a rugalmasan korlátozott hozzáférést Azure Maps REST API-khoz.                      |

Egyes Azure Maps szolgáltatások emelt szintű jogosultságokat igényelhetnek az írási és törlési műveletek elvégzéséhez Azure Maps REST API-kon. Írási vagy törlési műveleteket biztosító szolgáltatásokhoz Azure Maps adatközreműködői szerepkör szükséges. Az alábbi táblázat ismerteti, hogy mely szolgáltatások Azure Maps adatközreműködő, ha írási vagy törlési műveleteket használ a megadott szolgáltatásban. Ha a szolgáltatásban csak olvasási műveletek vannak használatban, Azure Maps adatolvasót Azure Maps adatközreműködő helyett lehet használni.

| Azure Maps szolgáltatás | Azure Maps szerepkör-definíció  |
| :----------------- | :-------------------------- |
| Adatok               | Azure Maps adatközreműködő |
| Létrehozó            | Azure Maps adatközreműködő |
| Térbeli            | Azure Maps adatközreműködő |

A RBAC-beállítások megtekintésével kapcsolatos további információkért lásd: [RBAC konfigurálása a Azure Mapshoz](https://aka.ms/amrbac).

#### <a name="custom-role-definitions"></a>Egyéni szerepkör-definíciók

Az alkalmazások biztonságának egyik aspektusa, hogy alkalmazza a legalacsonyabb jogosultsági szint elvét. Az alapelv azt jelenti, hogy a rendszerbiztonsági tag csak a szükséges hozzáférésre jogosult, és nincs további hozzáférés. Az egyéni szerepkör-definíciók létrehozása olyan használati eseteket támogat, amelyek további részletességet igényelnek a hozzáférés-vezérléshez. Egyéni szerepkör-definíció létrehozásához kiválaszthatja azokat a konkrét adatműveleteket, amelyek belefoglalják vagy kizárják a definíciót. 

Ezt követően az egyéni szerepkör-definíció bármely rendszerbiztonsági tag szerepkör-hozzárendelésében használható. Az Azure egyéni szerepkör-definíciókkal kapcsolatos további tudnivalókért tekintse meg az [Azure egyéni szerepkörei](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)című témakört.

Íme néhány példa arra, hogy az egyéni szerepkörök Hogyan javíthatják az alkalmazások biztonságát.

| Forgatókönyv                                                                                                                                                                                                                 | Egyéni szerepkörre vonatkozó adatművelet (ek)                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Nyilvános vagy interaktív bejelentkezési weblap, amely alapszintű Térkép csempével és más REST API-kkal nem rendelkezik.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Olyan alkalmazás, amely csak fordított helymeghatározáshoz és más REST API-k használatát igényli.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Egy rendszerbiztonsági tag szerepköre, amely Azure Maps Creator-alapú térképi adatok és az alaptérkép csempe REST API-k olvasását kéri.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Olyan rendszerbiztonsági tag szerepköre, amelynek a létrehozási alapú térképi adatait kell olvasni, írni és törölni. Ez egy Térkép adatszerkesztői szerepkörként definiálható, de nem teszi lehetővé más REST API-k, például az alapszintű Térkép csempék elérését. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>A hatókör ismertetése

Szerepkör-hozzárendelés létrehozásakor az Azure-erőforrás-hierarchiában van definiálva. A hierarchia tetején egy [felügyeleti csoport](https://docs.microsoft.com/azure/governance/management-groups/overview) , a legalacsonyabb pedig egy Azure-erőforrás, például egy Azure Maps-fiók.
Ha hozzárendel egy szerepkör-hozzárendelést egy erőforráscsoporthoz, a hozzáférés több Azure Maps fiókhoz vagy erőforráshoz is engedélyezhető a csoportban.

> [!Tip]
> A Microsoft általános javaslata a Azure Maps fiók hatóköréhez való hozzáférés kiosztása, mivel megakadályozza az azonos Azure-előfizetésben meglévő **Azure Maps fiókok nem kívánt elérését** .

## <a name="next-steps"></a>További lépések

* További információ a RBAC: [szerepköralapú hozzáférés-vezérlés áttekintése](https://docs.microsoft.com/azure/role-based-access-control/overview)

* Ha többet szeretne megtudni az alkalmazások Azure AD-vel és Azure Maps-vel történő hitelesítésével kapcsolatban, tekintse meg a következő témakört: a [hitelesítés kezelése Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)

* Ha többet szeretne megtudni a Azure Maps térképkezelés és az Azure AD hitelesítéséről, tekintse meg [a Azure Maps Térképkezelés használatát](https://aka.ms/amaadmc)ismertető témakört.
