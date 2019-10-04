---
title: Felügyeleti és lekérdezési API-kulcsok létrehozása, kezelése és védelme – Azure Search
description: az API-kulcsok vezérlik a szolgáltatás végpontjának elérését. A rendszergazdai kulcsok írási hozzáférést biztosítanak. A lekérdezési kulcsok csak olvasási hozzáféréshez hozhatók létre.
author: HeidiSteen
manager: nitinme
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.openlocfilehash: a148ccccd156b0bf637a134758b3a1c8b9db70a7
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647886"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>API-kulcsok létrehozása és kezelése Azure Search szolgáltatáshoz

A keresési szolgáltatásnak küldött összes kérelemnek egy írásvédett API-kulcsot kell használnia, amely kifejezetten a szolgáltatáshoz lett létrehozva. Az API-Key az egyetlen mechanizmus a keresési szolgáltatási végponthoz való hozzáférés hitelesítéséhez, és minden kérelemhez szerepelnie kell. A [Rest](search-get-started-postman.md)-megoldásokban általában az API-kulcs van megadva a kérelem fejlécében. A [.net](search-howto-dotnet-sdk.md#core-scenarios)-megoldásokban a rendszer gyakran konfigurációs beállításként adja meg a kulcsot, majd [hitelesítő adatként](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (felügyeleti kulcs) vagy [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (lekérdezési kulcsként) továbbítja a [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

A kulcsok a szolgáltatás kiépítés során jönnek létre a keresési szolgáltatással. A Azure Portalban megtekintheti és beszerezheti [](https://portal.azure.com)a kulcs értékeit.

![Portál lap, beállítások, kulcsok szakasz](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Mi az API-kulcs?

Az API-Key egy véletlenszerűen generált számokból és betűkből álló karakterlánc. A [szerepköralapú engedélyekkel](search-security-rbac.md)törölheti vagy elolvashatja a kulcsokat, de a kulcs nem cserélhető le felhasználó által definiált jelszóval, vagy nem használhatja a Active Directory elsődleges hitelesítési módszerként a keresési műveletek eléréséhez. 

A keresési szolgáltatás eléréséhez két típusú kulcs használható: admin (olvasás és írás) és lekérdezés (csak olvasható).

|Kulcs|Leírás|Korlátok|  
|---------|-----------------|------------|  
|rendszergazda|Minden művelethez teljes körű jogosultságot biztosít, beleértve a szolgáltatás felügyeletének képességét, indexek létrehozását és törlését, indexelő és adatforrásokat.<br /><br /> A szolgáltatás létrehozásakor két, a portál *elsődleges* és *másodlagos* kulcsának nevezett rendszergazdai kulcs jön létre, és igény szerint egyénileg újragenerálható. A két kulcs lehetővé teszi egy kulcs átadását, miközben a második kulcsot használja a szolgáltatás folyamatos eléréséhez.<br /><br /> A rendszergazdai kulcsok csak a HTTP-kérések fejlécében vannak megadva. A felügyeleti API-kulcs nem helyezhető el egy URL-címben.|Maximum 2/szolgáltatás|  
|Lekérdezés|Csak olvasási hozzáférést biztosít az indexekhez és a dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások számára történik.<br /><br /> A lekérdezési kulcsok igény szerint jönnek létre. Ezeket manuálisan is létrehozhatja a portálon vagy programozott módon a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/)használatával.<br /><br /> A lekérdezési kulcsok a keresés, javaslat vagy keresési művelet HTTP-kérelem fejlécében adhatók meg. Azt is megteheti, hogy a lekérdezési kulcsot paraméterként adja át egy URL-címben. Attól függően, hogy az ügyfélalkalmazás hogyan fogalmazza meg a kérést, könnyebb lehet átadni a kulcsot lekérdezési paraméterként:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50/szolgáltatás|  

 Vizuálisan, nincs különbség a rendszergazdai kulcs vagy a lekérdezési kulcs között. Mindkét kulcs 32 véletlenszerűen generált alfanumerikus karakterből álló karakterlánc. Ha nem tudja nyomon követni, hogy milyen típusú kulcs van megadva az alkalmazásban, [ellenőrizze a kulcs értékeit a portálon](https://portal.azure.com) , vagy használja a [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) az érték és a kulcs típusának visszaadásához.  

> [!NOTE]  
>  A bizalmas adatok (például `api-key` a kérelem URI-ja) számára nem megfelelő biztonsági gyakorlatnak minősülnek. Ezért Azure Search csak egy lekérdezési kulcsot `api-key` fogad el a lekérdezési karakterláncban, és el kell kerülnie, ha az index tartalmának nyilvánosan elérhetőnek kell lennie. Általános szabályként azt javasoljuk, hogy a kérelem `api-key` fejlécét küldje el.  

## <a name="find-existing-keys"></a>Meglévő kulcsok keresése

A hozzáférési kulcsokat a portálon vagy a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/)keresztül szerezheti be. További információ: [felügyeleti és lekérdezési API-kulcsok kezelése](search-security-api-keys.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az előfizetéshez tartozó [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) listázása.
3. Válassza ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások** >**kulcsok** elemre a felügyeleti és a lekérdezési kulcsok megtekintéséhez.

   ![Portál lap, beállítások, kulcsok szakasz](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Lekérdezési kulcsok létrehozása

A lekérdezési kulcsok csak olvasási hozzáférést biztosítanak a dokumentumokhoz egy indexen belül a dokumentumok gyűjteményét célzó műveletekhez. A keresés, szűrés és javaslatok lekérdezése minden olyan művelet, amely lekérdezési kulcsot hajt végre. A rendszeradatokat vagy az objektumokat, például az index definícióját vagy az indexelő állapotát visszaadó írásvédett művelethez rendszergazdai kulcs szükséges.

Az ügyfélalkalmazások hozzáférésének és műveleteinek korlátozása elengedhetetlen a szolgáltatásban található keresési eszközök védelméhez. Az ügyfélalkalmazások által létrehozott összes lekérdezéshez mindig használjon egy lekérdezési kulcsot, nem pedig rendszergazdai kulcsot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az előfizetéshez tartozó [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) listázása.
3. Válassza ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások** >**kulcsok**elemre.
4. Kattintson a **lekérdezési kulcsok kezelése**lehetőségre.
5. Használja a szolgáltatáshoz már létrehozott lekérdezési kulcsot, vagy hozzon létre akár 50 új lekérdezési kulcsot. Az alapértelmezett lekérdezési kulcs neve nem, de a kezelhetőséghez további lekérdezési kulcsok is megtekinthetők.

   ![Lekérdezési kulcs létrehozása vagy használata](media/search-security-overview/create-query-key.png) 

> [!Note]
> A lekérdezési kulcs használatát bemutató példa a [Azure Search indexének C#lekérdezésében ](search-query-dotnet.md)található.

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Rendszergazdai kulcsok újragenerálása

A rendszer két rendszergazdai kulcsot hoz létre az egyes szolgáltatásokhoz, így az elsődleges kulcsot az üzletmenet folytonossága érdekében a másodlagos kulccsal lehet elforgatni.

1. A **Beállítások** >**kulcsok** lapon másolja a másodlagos kulcsot.
2. Az összes alkalmazás esetében frissítse az API-kulcs beállításait a másodlagos kulcs használatára.
3. Az elsődleges kulcs újbóli előállítása.
4. Frissítse az összes alkalmazást az új elsődleges kulcs használatára.

Ha véletlenül újragenerálja mindkét kulcsot, a kulcsokat használó összes ügyfél-kérelem sikertelen lesz, és a HTTP 403 Tiltott. A tartalom azonban nincs törölve, és a rendszer véglegesen nem zárolja őket. 

Továbbra is hozzáférhet a szolgáltatáshoz a portálon vagy a felügyeleti rétegen ([REST API](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)vagy Azure Resource Manager). A felügyeleti függvények egy előfizetés-AZONOSÍTÓn keresztül nem a Service API-Key, így még akkor is elérhetők, ha az API-kulcsok nem. 

Miután a portálon vagy a felügyeleti rétegen keresztül új kulcsokat hozott létre, a rendszer visszaállítja a hozzáférést a tartalomhoz (indexek, indexelő, adatforrások, szinonimák), ha az új kulcsokkal rendelkezik, és megadja ezeket a kulcsokat a kérésekhez.

## <a name="secure-api-keys"></a>Biztonságos API – kulcsok
A kulcs biztonsága a portálon vagy a Resource Manager-felületeken (PowerShell vagy parancssori felületen) keresztüli hozzáférés korlátozásával biztosítható. Az előfizetés-rendszergazdák az összes API-kulcsot megtekinthetik és újragenerálják. Elővigyázatosságból tekintse át a szerepkör-hozzárendeléseket, és Ismerje meg, hogy ki férhet hozzá a rendszergazdai kulcsokhoz.

+ A szolgáltatás Irányítópultján kattintson a **hozzáférés-vezérlés (iam)** elemre, majd a **szerepkör** -hozzárendelések fülre a szolgáltatáshoz tartozó szerepkör-hozzárendelések megtekintéséhez.

A következő szerepkörök tagjai megtekinthetik és újragenerálják a kulcsokat: Tulajdonos, közreműködő, [Search Service közreműködők](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> A keresési eredményeken alapuló, identitás-alapú hozzáféréshez biztonsági szűrőket hozhat létre, amelyek identitás alapján metszik az eredményeket, és eltávolíthatja azokat a dokumentumokat, amelyekhez a kérelmezőnek nincs hozzáférése. További információ: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [biztonságos a Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Lásd még

+ [Szerepköralapú hozzáférés-vezérlés a Azure Searchban](search-security-rbac.md)
+ [Kezelés a PowerShell használatával](search-manage-powershell.md) 
+ [Teljesítmény-és optimalizálási cikk](search-performance-optimization.md)