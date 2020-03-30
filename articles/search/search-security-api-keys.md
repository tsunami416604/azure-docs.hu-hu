---
title: Felügyeleti és lekérdezési api-kulcsok létrehozása, kezelése és biztonságossá tétele
titleSuffix: Azure Cognitive Search
description: Egy API-kulcs szabályozza a szolgáltatás végpontjához való hozzáférést. A rendszergazdai kulcsok írási hozzáférést biztosítanak. Lekérdezési kulcsok csak olvasható hozzáféréshez hozhatók létre.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794368"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Api-kulcsok létrehozása és kezelése azure Cognitive Search szolgáltatáshoz

A keresési szolgáltatás minden kérésének szüksége van egy csak olvasható API-kulcsra, amely kifejezetten a szolgáltatáshoz lett létrehozva. Az api-kulcs az egyetlen mechanizmus a keresési szolgáltatás végpontjához való hozzáférés hitelesítésére, és minden kérésben szerepelnie kell. A [REST-megoldásokban](search-get-started-postman.md)az api-kulcs általában egy kérelemfejlécben van megadva. A [.NET-megoldásokban](search-howto-dotnet-sdk.md#core-scenarios)a kulcs gyakran konfigurációs beállításként van megadva, majd a [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) [kiszolgálón hitelesítő adatként](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (rendszergazdai kulcs) vagy [SearchCredentials (lekérdezési](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) kulcs) néven adják át.

A kulcsok a keresési szolgáltatással együtt jönnek létre a szolgáltatás kiépítése során. A kulcsértékeket az [Azure Portalon](https://portal.azure.com)tekintheti meg és szerezheti be.

![Portállap, Beállítások, Billentyűk szakasz](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Mi az api-kulcs

Az api-kulcs véletlenszerűen generált számokból és betűkből álló karakterlánc. A [szerepköralapú engedélyeken](search-security-rbac.md)keresztül törölheti vagy elolvashatja a kulcsokat, de a kulcsokat nem helyettesítheti felhasználó által definiált jelszóval, és nem használhatja az Active Directoryt elsődleges hitelesítési módszerként a keresési műveletek eléréséhez. 

A keresési szolgáltatás eléréséhez kétféle kulcs használható: rendszergazda (írási és írási) és lekérdezés (írásvédett).

|Kulcs|Leírás|Korlátok|  
|---------|-----------------|------------|  
|Rendszergazda|Teljes körű jogokat biztosít minden művelethez, beleértve a szolgáltatás kezelését, az indexek, indexelők és adatforrások létrehozását és törlését.<br /><br /> A *portálon elsődleges* és *másodlagos* kulcsoknak nevezett két felügyeleti kulcs jön létre a szolgáltatás létrehozásakor, és igény szerint külön-külön újragenerálható. A két kulcs lehetővé teszi, hogy a második kulcs használata közben átgördüljen egy kulcson a szolgáltatás hoz való folyamatos hozzáféréshez.<br /><br /> A rendszergazdai kulcsok csak a HTTP-kérelemfejlécekben vannak megadva. Rendszergazdai API-kulcs nem helyezhet ő-url-cím.|Szolgáltatásonként legfeljebb 2|  
|Lekérdezés|Csak olvasható hozzáférést biztosít az indexekhez és a dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások között van elosztva.<br /><br /> A lekérdezési kulcsok igény szerint jönnek létre. Manuálisan vagy programozott módon is létrehozhatja őket a [Felügyeleti REST API-n](https://docs.microsoft.com/rest/api/searchmanagement/)keresztül.<br /><br /> A lekérdezési kulcsok megadhatók egy HTTP-kérelem fejlécében a keresési, javaslati vagy keresési művelethez. Azt is megteheti, hogy egy lekérdezési kulcsot egy URL-cím paramétereként ad meg. Attól függően, hogy az ügyfélalkalmazás hogyan fogalmazza meg a kérést, egyszerűbb lehet a kulcsot lekérdezési paraméterként átadni:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 szolgáltatásonként|  

 Vizuálisan nincs különbség a rendszergazdai kulcs vagy a lekérdezési kulcs között. Mindkét billentyű 32 véletlenszerűen generált alfa-numerikus karakterből álló karakterlánc. Ha nem tudja, hogy milyen típusú kulcs van megadva az alkalmazásban, [ellenőrizheti a legfontosabb értékeket a portálon,](https://portal.azure.com) vagy a [REST API segítségével](https://docs.microsoft.com/rest/api/searchmanagement/) adja vissza az értéket és a kulcs típusát.  

> [!NOTE]  
>  A bizalmas adatok, például a kérelem `api-key` URI-ban való átadására gyenge biztonsági gyakorlat nak minősül. Ebből az okból az Azure Cognitive Search `api-key` csak akkor fogadja el a lekérdezési kulcsot, mint egy a lekérdezési karakterlánc, és ne tegye, kivéve, ha az index tartalma nyilvánosan elérhetőnek kell lennie. Általános szabályként azt javasoljuk, `api-key` hogy adja át a kérelem fejléce.  

## <a name="find-existing-keys"></a>Meglévő kulcsok keresése

A hozzáférési kulcsokat a portálon vagy a [Management REST API-n](https://docs.microsoft.com/rest/api/searchmanagement/)keresztül szerezheti be. További információt a [Rendszergazda és a lekérdezésapi-kulcsok kezelése című témakörben talál.](search-security-api-keys.md)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Sorolja fel az előfizetés [keresési szolgáltatásait.](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
3. Jelölje ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások** >**kulcsok** elemre a rendszergazdai és lekérdezési kulcsok megtekintéséhez.

   ![Portállap, Beállítások, Billentyűk szakasz](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Lekérdezéskulcsok létrehozása

A lekérdezési kulcsok csak olvasható hozzáférésre szolgálnak az indexen belül lévő dokumentumokhoz a dokumentumgyűjteményt célzó műveletekhez. A keresési, szűrő- és javaslatlekérdezési lekérdezések mind olyan műveletek, amelyek lekérdezési kulcsot vesznek igénybe. Minden olyan írásvédett művelethez, amely rendszeradatokat vagy objektumdefiníciókat ad vissza, például indexdefiníciót vagy indexelő állapotot, rendszergazdai kulcsszükséges.

Az ügyfélalkalmazásokhoz való hozzáférés és műveletek korlátozása elengedhetetlen a szolgáltatás keresési eszközeinek védelméhez. Az ügyfélalkalmazásból származó lekérdezésekhez mindig rendszergazdai kulcs helyett használjon lekérdezési kulcsot.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Sorolja fel az előfizetés [keresési szolgáltatásait.](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
3. Jelölje ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások** >**kulcsok gombra.**
4. Kattintson **a Lekérdezéskulcsok kezelése gombra.**
5. Használja a szolgáltatáshoz már létrehozott lekérdezési kulcsot, vagy hozzon létre legfeljebb 50 új lekérdezési kulcsot. Az alapértelmezett lekérdezési kulcs neve nincs elnevezve, de további lekérdezési kulcsok is elnevezhetők a kezelhetőség érdekében.

   ![Lekérdezéskulcs létrehozása vagy használata](media/search-security-overview/create-query-key.png) 

> [!Note]
> A lekérdezési kulcs használatát bemutató kódpélda megtalálható az [Azure Cognitive Search index lekérdezése c# mezőben.](search-query-dotnet.md)

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Felügyeleti kulcsok újragenerálása

Minden szolgáltatáshoz két felügyeleti kulcs jön létre, így az elsődleges kulcs elforgatható, a másodlagos kulcs használatával az üzletmenet folytonosságához.

1. A **Beállítások** >**kulcsok** lapon másolja a másodlagos kulcsot.
2. Az összes alkalmazás esetében frissítse az api-kulcs beállításait a másodlagos kulcs használatához.
3. Az elsődleges kulcs újragenerálása.
4. Frissítse az összes alkalmazást az új elsődleges kulcs használatához.

Ha véletlenül újragenerálja mindkét kulcsot egyszerre, az ezeket a kulcsokat használó összes ügyfélkérelem sikertelen lesz a HTTP 403 Tiltott rendszerrel. A tartalom azonban nem törlődik, és nem zárle véglegesen. 

Továbbra is elérheti a szolgáltatást a portálon vagy a felügyeleti rétegen[(REST API,](https://docs.microsoft.com/rest/api/searchmanagement/) [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)vagy Azure Resource Manager). A felügyeleti funkciók egy előfizetési azonosítón keresztül működnek, nem egy szolgáltatás api-kulcs, és így továbbra is elérhető, még akkor is, ha az api-kulcsok nem. 

Miután új kulcsokat hoz létre a portálon vagy a felügyeleti rétegen keresztül, a rendszer visszaállítja a hozzáférést a tartalomhoz (indexek, indexelők, adatforrások, szinonimaleképezések), miután rendelkezik az új kulcsokkal, és kérésekre biztosítja ezeket a kulcsokat.

## <a name="secure-api-keys"></a>Biztonságos api-kulcsok
A kulcsbiztonságot a portálon vagy az Erőforrás-kezelő felületeken (PowerShellen vagy parancssori felületen) keresztüli hozzáférés korlátozása biztosítja. Mint említettük, az előfizetés-rendszergazdák megtekinthetik és újragenerálhatják az összes api-kulcsokat. Elővigyázatosságból tekintse át a szerepkör-hozzárendeléseket, hogy tisztában van azzal, ki férhet hozzá a felügyeleti kulcsokhoz.

+ A szolgáltatás irányítópultján kattintson a **Hozzáférés-vezérlés (IAM)** elemre, majd a **Szerepkör-hozzárendelések** fülre a szolgáltatás szerepkör-hozzárendelései megtekintéséhez.

A következő szerepkörök tagjai megtekinthetik és újragenerálhatják a kulcsokat: Tulajdonos, Közreműködő, [Keresési szolgáltatás közreműködői](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> Az identitásalapú hozzáférés a keresési eredményeken keresztül, biztonsági szűrőket hozhat létre az eredmények identitás szerint való levágására, eltávolítva azokat a dokumentumokat, amelyekhez a kérelmezőnek nem kellene hozzáférnie. További információt a [Biztonsági szűrők](search-security-trimming-for-azure-search.md) és az Active [Directoryval való védelem című témakörben talál.](search-security-trimming-for-azure-search-with-aad.md)

## <a name="see-also"></a>Lásd még

+ [Szerepköralapú hozzáférés-vezérlés az Azure Cognitive Search szolgáltatásban](search-security-rbac.md)
+ [Kezelés a PowerShell-lel](search-manage-powershell.md) 
+ [Teljesítmény- és optimalizálási cikk](search-performance-optimization.md)