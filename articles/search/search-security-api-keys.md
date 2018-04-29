---
title: Létrehozása, kezelése és biztonságos rendszergazdai és lekérdezés api-kulcsot az Azure Search |} Microsoft Docs
description: API-kulcsokat a szolgáltatási végpont való hozzáférés szabályozása. Adminisztrációs kulcsok írási hozzáférést. Lekérdezési kulcsok a csak olvasási hozzáféréssel is létrehozható.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 4215795b7cd2a25427a3ce9b3cde16bfc69cb009
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Hozzon létre és api-kulcsok az Azure Search szolgáltatás kezelése

Összes kérelmet, a keresési szolgáltatás kell egy api-kulcs lett létrehozva, kifejezetten a szolgáltatás számára. Az api-kulcsot, akkor az egyetlen eszköz a keresési szolgáltatás végpontjának való hozzáférés hitelesítéséhez. 

Api-kulcsát: véletlenszerűen generált számok és betűk álló karakterlánc. Keresztül [szerepköralapú engedélyeinek](search-security-rbac.md), törölheti vagy a kulcsok beolvasása a, de nem egy kulcs cserélje le a felhasználói jelszó vagy Active Directory keresési műveletek eléréséhez használják elsődleges hitelesítési módszert. 

A keresési szolgáltatás eléréséhez használt kulcsok kétféle: admin (olvasható és írható) és a lekérdezés (csak olvasható).

|Kulcs|Leírás|Korlátok|  
|---------|-----------------|------------|  
|Adminisztratív körzet|Teljes körű jogosultságot biztosít az összes művelethez, beleértve a szolgáltatás kezelése létrehozása, és törölje az indexek, az indexelők és az adatforrások.<br /><br /> Két adminisztrációs kulcsok, néven *elsődleges* és *másodlagos* kulcsok a portálon jönnek létre, ha a szolgáltatás jön létre, és az igény szerinti külön-külön újragenerálása. A két kulcs lehetővé teszi egy kulcs váltása a második kulcs használatakor a szolgáltatás folyamatos elérésére.<br /><br /> Adminisztrációs kulcsok csak HTTP-kérelmek fejléceinek vannak megadva. Az egy URL-cím nem helyezhető el egy adminisztrációs api-kulcsot.|Legfeljebb 2-szolgáltatás|  
|Lekérdezés|Csak olvasható hozzáférést biztosítanak az indexekhez és dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások számára.<br /><br /> Lekérdezési kulcsok igény szerint jönnek létre. Létrehozhat őket manuálisan a portálon vagy programozottan keresztül a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Lekérdezési kulcsok a keresés, a javaslat, vagy a keresési művelet HTTP-kérelem fejléc adható meg. Másik lehetőségként átadhatók egy lekérdezési kulcsot paraméterként egy URL-címen. Attól függően, hogy az ügyfélalkalmazás formulates a kérelmet akkor valószínűleg egyszerűbb felelt meg a kulcsot a lekérdezési paramétert:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50-szolgáltatás|  

 Nincs vizuálisan, egy adminisztrációs kulcsot vagy a lekérdezési kulcs közötti különbség. Mindkét kulcsai 32 véletlenszerűen álló karakterláncok generált alfanumerikus karakter. Ha elveszti nyomon milyen kulcs van megadva az alkalmazásban, akkor [ellenőrizze a portálon értékek](https://portal.azure.com) , vagy használja a [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) adhatja vissza az értéket és a kulcs típusa.  

> [!NOTE]  
>  A gyenge biztonsági eljárás felelt meg a bizalmas adatokat, mint egy `api-key` kérési URI-ban. Emiatt Azure Search csak fogad egy lekérdezési kulcsot egy `api-key` a lekérdezésben karakterlánc, és kerülje ennek során, kivéve, ha az index tartalmát nyilvánosan elérhetőnek kell lennie. Általános szabályként elmondható, sikeres javasoljuk a `api-key` fejléc szerint.  

## <a name="find-api-keys-for-your-service"></a>A szolgáltatás található api-kulcsok

Ezt úgy szerezheti be a portál vagy a tárelérési kulcsok a [felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement/). További információkért lásd: [rendszergazda és a lekérdezés api-kulcsok kezelése](search-security-api-keys.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lista a [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az előfizetéséhez.
3. Válassza ki a szolgáltatást, és keresse meg a szolgáltatás lapján **beállítások** >**kulcsok** rendszergazda és a lekérdezési kulcsok megtekintéséhez.

![Portállapon, beállítások, a kulcsok szakasz](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Adminisztrációs kulcsok újragenerálása

Két adminisztrációs kulcsok minden egyes szolgáltatás jönnek létre, hogy egy elsődleges kulcs átfordulási a másodlagos kulcs használatával a folyamatos hozzáférés érdekében.

Ha az elsődleges és másodlagos kulcsok újragenerálása egyszerre, vagy a kulcs eléréséhez a szolgáltatási műveletek használó alkalmazások többé nem lesz a szolgáltatás elérésére.

1. Az a **beállítások** >**kulcsok** lapon, a másodlagos kulcs másolása.
2. Minden olyan alkalmazásnál frissítse az api-kulcs beállításokat a másodlagos kulcsot használ.
3. Az elsődleges kulcs újragenerálása.
4. Frissítse az alkalmazásokat az új elsődleges kulcsot használják.

## <a name="secure-api-keys"></a>A biztonságos api-kulcsok
Kulcs biztonsági korlátozza a hozzáférést a portál vagy az erőforrás-kezelő felületek (a PowerShell vagy a parancssori felület) keresztül biztosított. Amint az előfizetés rendszergazdáihoz megtekintheti, és minden api-kulcs újragenerálása. Éppen ezért tekintse át a szerepkör-hozzárendelések megérteni, hogy ki férhet hozzá az adminisztrációs kulcsok.

+ A szolgáltatás irányítópultján kattintson **hozzáférés-vezérlés (IAM)** a szolgáltatás szerepkör-hozzárendelések megtekintése.

A következő szerepkörök tagjai megtekinthetik és kulcsok újragenerálása: tulajdonos, közreműködő, [keresési szolgáltatás közreműködők](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> A keresési eredmények azonosító-alapú hozzáférés létrehozhat lehet levágni eredményeket e identitása, a biztonsági szűrők eltávolítása a dokumentumok, amelynek a kérelmező nem rendelkeznek hozzáféréssel. További információkért lásd: [biztonsági szűrők](search-security-trimming-for-azure-search.md) és [biztonságos és az Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Lásd még

+ [Azure Search szerepköralapú hozzáférés-vezérlés](search-security-rbac.md)
+ [Kezelés a PowerShell használatával](search-manage-powershell.md) 
+ [Teljesítmény- és optimalizálási cikk](search-performance-optimization.md)