---
title: Hozzon létre, kezelhet és secure admin és lekérdezési api-kulcsok az Azure Search |} A Microsoft Docs
description: API-kulcsok a szolgáltatásvégpont való hozzáférés szabályozása. Az adminisztrációs kulcsok írási hozzáférést. Lekérdezési kulcsok a csak olvasási hozzáféréssel is létrehozható.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: heidist
ms.openlocfilehash: 0a8975fad21645535a6e7407bcd2d59f537a9fff
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727973"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Az Azure Search szolgáltatás api-kulcsok létrehozása és kezelése

Egy keresési szolgáltatás érkező összes kérés egy csak olvasható api-kulcs jött létre kifejezetten a szolgáltatás a kell. Az api-kulcsot a egyetlen a keresési szolgáltatás végpontját való hozzáférés hitelesítéséhez mechanizmus, és szerepelnie kell halasztása minden kérelemnél. A [REST megoldások](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key), az api-kulcsot általában a kérelem fejlécében van megadva. A [.NET – megoldások](search-howto-dotnet-sdk.md#core-scenarios), egy kulcs gyakran megadott konfigurációs beállításai, és ezután adhatók be [hitelesítő adatok](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (admin key) vagy [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (lekérdezési kulcsot) a [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Kulcsok jönnek létre a search Service szolgáltatás kiépítése során. Megtekintheti, és szerezze be a kulcs értékeit az [az Azure portal](https://portal.azure.com).

![Portál oldalán beállításait, a kulcsok szakaszban](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Mi az api-kulcsát

Api-kulcsát: véletlenszerűen generált számokból és betűkből álló karakterlánc. Keresztül [szerepköralapú engedélyeinek](search-security-rbac.md), törölheti, vagy olvassa el a kulcsokat, de nem egy felhasználói jelszót cserélje le egy kulcsot és keresési műveletek eléréséhez, az elsődleges hitelesítési módszert az Active Directory használatával. 

A keresési szolgáltatás eléréséhez használt kulcsok kétféle: admin (olvasás / írás) és a lekérdezés (csak olvasható).

|Kulcs|Leírás|Korlátok|  
|---------|-----------------|------------|  
|Adminisztratív körzet|Minden művelet, beleértve a szolgáltatás kezelését lehetővé teszi teljes jogokat biztosít létrehozása, és az indexek, indexelők és adatforrások törlése.<br /><br /> Két adminisztrációs kulcsot néven *elsődleges* és *másodlagos* a Portal kulcsok akkor jönnek létre, ha a szolgáltatás jön létre, és igény szerint külön-külön újragenerálása. Két kulcs lehetővé teszi, hogy vihetők át egy kulcs használatával, a második kulcsot e folyamatos hozzáférésre a szolgáltatáshoz.<br /><br /> Az adminisztrációs kulcsok csak a HTTP-kérelemfejlécek vannak megadva. Egy URL-cím nem helyezhető el egy rendszergazdai api-kulcsát.|Legfeljebb 2 szolgáltatásonként|  
|Lekérdezés|Csak olvasási hozzáférést biztosít az indexekhez és dokumentumokhoz, és általában oszlanak meg, hogy a keresési kérelmeket kibocsátó ügyfélalkalmazások számára.<br /><br /> Lekérdezési kulcsok igény szerint jönnek létre. Létrehozhatja őket manuálisan a portálon vagy programozottan keresztül a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Lekérdezési kulcsok segítségével is megadható egy HTTP-kérelem fejléce keresési, a javaslat vagy a keresési műveletet. Másik lehetőségként adhat át egy lekérdezési kulcsot paraméterként az URL-cím. Attól függően, hogy az ügyfélalkalmazás formulates a kérelmet azt valószínűleg egyszerűbb adja át a kulcsot a lekérdezési paramétert:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 szolgáltatásonként|  

 Vizuálisan nincs különbség a között, egy adminisztrációs kulcsot vagy a lekérdezési kulcsot. Mindkét kulcsai karakterláncok mikroszolgáltatásokból álló, 32 véletlenszerűen generált alfanumerikus karakterek. Milyen típusú kulcs van megadva, az alkalmazás nyomon megszakad, ha [ellenőrizze a kulcs a portálon](https://portal.azure.com) vagy használja a [REST API-val](https://docs.microsoft.com/rest/api/searchmanagement/) adhatja vissza az értéket, és a kulcs típusa.  

> [!NOTE]  
>  Bizalmas adatok átadására például gyenge biztonsági gyakorlat minősül egy `api-key` a kérelem URI-t. Emiatt az Azure Search csak fogad egy lekérdezési kulcsot egy `api-key` a lekérdezésben szereplő karakterláncot, és kerülje ennek során, kivéve, ha az index tartalmát nyilvánosan elérhetőnek kell lennie. Általános szabály, javasoljuk, hogy megadásának a `api-key` fejléc szerint.  

## <a name="find-api-keys-for-your-service"></a>A szolgáltatás api-kulcsainak megkereséséhez

Hozzáférési kulcsok a portálon vagy keresztül szerezheti be a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/). További információkért lásd: [rendszergazda és a lekérdezési api-kulcsok kezelése](search-security-api-keys.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lista a [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az előfizetéshez.
3. Válassza ki a szolgáltatást, és keresse meg a szolgáltatás oldalán **beállítások** >**kulcsok** rendszergazda és a lekérdezési kulcsok megtekintéséhez.

![Portál oldalán beállításait, a kulcsok szakaszban](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Az adminisztrációs kulcsok újragenerálása

Két adminisztrációs kulcsot minden egyes szolgáltatás jön létre, így is váltása elsődleges kulcs, a másodlagos kulcs használatával a folyamatos hozzáférés érdekében.

Ha az elsődleges és másodlagos kulcsok egy időben hozza létre újra mindkét kulcsot használó szolgáltatási műveletek eléréséhez szükséges alkalmazások többé nem lesz a szolgáltatáshoz való hozzáférést.

1. Az a **beállítások** >**kulcsok** lapon, a másodlagos kulcs másolásához.
2. Az összes alkalmazást frissítse a másodlagos kulcs használatára api-kulcs beállításait.
3. Az elsődleges kulcs újragenerálása.
4. Frissítse az összes alkalmazást az új elsődleges kulcsot használja.

## <a name="secure-api-keys"></a>Biztonságos api-kulcsok
Kulcsfontosságú biztonsági biztosított korlátozza a hozzáférést a portálon vagy a Resource Manager-felületeket (a PowerShell vagy a parancssori felület). Feljegyzett, előfizetés-rendszergazda megtekintheti, és minden api-kulcsok újragenerálása. Elővigyázatosságból tekintse át a szerepkör-hozzárendelések megértéséhez, hogy ki férhet hozzá az adminisztrációs kulcsok.

+ A szolgáltatás irányítópultján kattintson **hozzáférés-vezérlés (IAM)** , majd a **szerepkör-hozzárendelések** fülre kattintva megtekintheti a szolgáltatás szerepkör-hozzárendeléseit.

A következő szerepkörök tagjai megtekinthetik és kulcsok újragenerálása: tulajdonos, közreműködő, [keresési szolgáltatás közreműködők](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> A keresési eredmények az identitásalapú hozzáférés létrehozhat által identitás, az eredmények azokat a biztonsági szűrők, amelynek a kérelmező nem szeretett volna dokumentumok eltávolítása. További információkért lásd: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [az Active Directory biztonságos](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Lásd még

+ [Szerepköralapú hozzáférés-vezérlés az Azure Search](search-security-rbac.md)
+ [Kezelés a PowerShell használatával](search-manage-powershell.md) 
+ [Teljesítmény és optimalizálás a cikk](search-performance-optimization.md)