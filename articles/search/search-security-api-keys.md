---
title: Rendszergazdai és lekérdezési API-kulcsok létrehozása, kezelése és biztonságossá tétele
titleSuffix: Azure Cognitive Search
description: Az API-Key vezérli a szolgáltatás végpontjának elérését. A rendszergazdai kulcsok írási hozzáférést biztosítanak. A lekérdezési kulcsok csak olvasási hozzáféréshez hozhatók létre.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cd6e61b8614e4c8ff5d54232972865c81cbb3ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928870"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Azure Cognitive Search-szolgáltatás API-kulcsainak létrehozása és kezelése

A keresési szolgáltatásnak küldött összes kérelemnek egy írásvédett API-kulcsot kell használnia, amely kifejezetten a szolgáltatáshoz lett létrehozva. Az API-Key az egyetlen mechanizmus a keresési szolgáltatási végponthoz való hozzáférés hitelesítéséhez, és minden kérelemhez szerepelnie kell. A [Rest-megoldásokban](search-get-started-postman.md)általában az API-kulcs van megadva a kérelem fejlécében. A [.net-megoldásokban](search-howto-dotnet-sdk.md#core-scenarios)a rendszer gyakran konfigurációs beállításként adja meg a kulcsot, majd [hitelesítő adatként](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (felügyeleti kulcs) vagy [SearchCredentials](/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (lekérdezési kulcsként) továbbítja a [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient).

A kulcsok a szolgáltatás kiépítés során jönnek létre a keresési szolgáltatással. A [Azure Portalban](https://portal.azure.com)megtekintheti és beszerezheti a kulcs értékeit.

![Portál lap, beállítások, kulcsok szakasz](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Mi az API-kulcs?

Az API-Key egy véletlenszerűen generált számokból és betűkből álló karakterlánc. A [szerepköralapú engedélyekkel](search-security-rbac.md)törölheti vagy elolvashatja a kulcsokat, de a kulcs nem cserélhető le felhasználó által definiált jelszóval, vagy nem használhatja a Active Directory elsődleges hitelesítési módszerként a keresési műveletek eléréséhez. 

A keresési szolgáltatás eléréséhez két típusú kulcs használható: admin (olvasás és írás) és lekérdezés (csak olvasható).

|Kulcs|Leírás|Korlátok|  
|---------|-----------------|------------|  
|Rendszergazda|Minden művelethez teljes körű jogosultságot biztosít, beleértve a szolgáltatás felügyeletének képességét, indexek létrehozását és törlését, indexelő és adatforrásokat.<br /><br /> A szolgáltatás létrehozásakor két, a portál *elsődleges* és *másodlagos* kulcsának nevezett rendszergazdai kulcs jön létre, és igény szerint egyénileg újragenerálható. A két kulcs lehetővé teszi egy kulcs átadását, miközben a második kulcsot használja a szolgáltatás folyamatos eléréséhez.<br /><br /> A rendszergazdai kulcsok csak a HTTP-kérések fejlécében vannak megadva. A felügyeleti API-kulcs nem helyezhető el egy URL-címben.|Maximum 2/szolgáltatás|  
|Lekérdezés|Csak olvasási hozzáférést biztosít az indexekhez és a dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások számára történik.<br /><br /> A lekérdezési kulcsok igény szerint jönnek létre. Ezeket manuálisan is létrehozhatja a portálon vagy programozott módon a [felügyeleti REST API](/rest/api/searchmanagement/)használatával.<br /><br /> A lekérdezési kulcsok a keresés, javaslat vagy keresési művelet HTTP-kérelem fejlécében adhatók meg. Azt is megteheti, hogy a lekérdezési kulcsot paraméterként adja át egy URL-címben. Attól függően, hogy az ügyfélalkalmazás hogyan fogalmazza meg a kérést, könnyebb lehet átadni a kulcsot lekérdezési paraméterként:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50/szolgáltatás|  

 Vizuálisan, nincs különbség a rendszergazdai kulcs vagy a lekérdezési kulcs között. Mindkét kulcs 32 véletlenszerűen generált alfanumerikus karakterből álló karakterlánc. Ha nem tudja nyomon követni, hogy milyen típusú kulcs van megadva az alkalmazásban, [ellenőrizze a kulcs értékeit a portálon](https://portal.azure.com) , vagy használja a [REST API](/rest/api/searchmanagement/) az érték és a kulcs típusának visszaadásához.  

> [!NOTE]  
>  A bizalmas adatok (például `api-key` a kérelem URI-ja) számára nem megfelelő biztonsági gyakorlatnak minősülnek. Emiatt az Azure Cognitive Search csak a lekérdezési `api-key` karakterláncot fogadja el a lekérdezési sztringben, és ennek elkerülését kell elkerülnie, kivéve, ha az index tartalmának nyilvánosan elérhetőnek kell lennie. Általános szabályként azt javasoljuk, hogy a `api-key` kérelem fejlécét küldje el.  

## <a name="find-existing-keys"></a>Meglévő kulcsok keresése

A hozzáférési kulcsokat a portálon vagy a [felügyeleti REST API](/rest/api/searchmanagement/)keresztül szerezheti be. További információ: [felügyeleti és lekérdezési API-kulcsok kezelése](search-security-api-keys.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az előfizetéshez tartozó [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  listázása.
3. Válassza ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások**  > **kulcsok** elemre a felügyeleti és a lekérdezési kulcsok megtekintéséhez.

   ![Portál lap, beállítások, kulcsok szakasz](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Lekérdezési kulcsok létrehozása

A lekérdezési kulcsok csak olvasási hozzáférést biztosítanak a dokumentumokhoz egy indexen belül a dokumentumok gyűjteményét célzó műveletekhez. A keresés, szűrés és javaslatok lekérdezése minden olyan művelet, amely lekérdezési kulcsot hajt végre. A rendszeradatokat vagy az objektumokat, például az index definícióját vagy az indexelő állapotát visszaadó írásvédett művelethez rendszergazdai kulcs szükséges.

Az ügyfélalkalmazások hozzáférésének és műveleteinek korlátozása elengedhetetlen a szolgáltatásban található keresési eszközök védelméhez. Az ügyfélalkalmazások által létrehozott összes lekérdezéshez mindig használjon egy lekérdezési kulcsot, nem pedig rendszergazdai kulcsot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az előfizetéshez tartozó [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  listázása.
3. Válassza ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások**  > **kulcsok**elemre.
4. Kattintson a **lekérdezési kulcsok kezelése**lehetőségre.
5. Használja a szolgáltatáshoz már létrehozott lekérdezési kulcsot, vagy hozzon létre akár 50 új lekérdezési kulcsot. Az alapértelmezett lekérdezési kulcs neve nem, de a kezelhetőséghez további lekérdezési kulcsok is megtekinthetők.

   ![Lekérdezési kulcs létrehozása vagy használata](media/search-security-overview/create-query-key.png) 

> [!Note]
> A lekérdezési kulcs használatát bemutató példa a [C# nyelven elérhető Azure Cognitive Search index lekérdezésében](./search-get-started-dotnet.md)található.

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Rendszergazdai kulcsok újragenerálása

A rendszer két rendszergazdai kulcsot hoz létre az egyes szolgáltatásokhoz, így az elsődleges kulcsot az üzletmenet folytonossága érdekében a másodlagos kulccsal lehet elforgatni.

1. A **Beállítások**  > **kulcsok** lapon másolja a másodlagos kulcsot.
2. Az összes alkalmazás esetében frissítse az API-kulcs beállításait a másodlagos kulcs használatára.
3. Az elsődleges kulcs újbóli előállítása.
4. Frissítse az összes alkalmazást az új elsődleges kulcs használatára.

Ha véletlenül újragenerálja mindkét kulcsot, a kulcsokat használó összes ügyfél-kérelem sikertelen lesz, és a HTTP 403 Tiltott. A tartalom azonban nincs törölve, és a rendszer véglegesen nem zárolja őket. 

Továbbra is hozzáférhet a szolgáltatáshoz a portálon vagy a felügyeleti rétegen ([REST API](/rest/api/searchmanagement/), [PowerShell](./search-manage-powershell.md)vagy Azure Resource Manager). A felügyeleti függvények egy előfizetés-AZONOSÍTÓn keresztül nem a Service API-Key, így még akkor is elérhetők, ha az API-kulcsok nem. 

Miután a portálon vagy a felügyeleti rétegen keresztül új kulcsokat hozott létre, a rendszer visszaállítja a hozzáférést a tartalomhoz (indexek, indexelő, adatforrások, szinonimák), ha az új kulcsokkal rendelkezik, és megadja ezeket a kulcsokat a kérésekhez.

## <a name="secure-api-keys"></a>Biztonságos API – kulcsok
A kulcs biztonsága a portálon vagy a Resource Manager-felületeken (PowerShell vagy parancssori felületen) keresztüli hozzáférés korlátozásával biztosítható. Az előfizetés-rendszergazdák az összes API-kulcsot megtekinthetik és újragenerálják. Elővigyázatosságból tekintse át a szerepkör-hozzárendeléseket, és Ismerje meg, hogy ki férhet hozzá a rendszergazdai kulcsokhoz.

+ A szolgáltatás Irányítópultján kattintson a **hozzáférés-vezérlés (iam)** elemre, majd a **szerepkör-hozzárendelések** fülre a szolgáltatáshoz tartozó szerepkör-hozzárendelések megtekintéséhez.

A következő szerepkörök tagjai megtekinthetik és újragenerálják a kulcsokat: tulajdonos, közreműködő, [Search Service közreműködők](../role-based-access-control/built-in-roles.md#search-service-contributor)

> [!Note]
> A keresési eredményeken alapuló, identitás-alapú hozzáféréshez biztonsági szűrőket hozhat létre, amelyek identitás alapján metszik az eredményeket, és eltávolíthatja azokat a dokumentumokat, amelyekhez a kérelmezőnek nincs hozzáférése. További információ: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [biztonságos a Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Lásd még

+ [Szerepköralapú hozzáférés-vezérlés az Azure-ban Cognitive Search](search-security-rbac.md)
+ [Kezelés a PowerShell-lel](search-manage-powershell.md) 
+ [Teljesítmény-és optimalizálási cikk](search-performance-optimization.md)