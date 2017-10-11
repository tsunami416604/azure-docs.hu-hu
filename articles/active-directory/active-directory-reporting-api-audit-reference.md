---
title: "Az Azure Active Directory naplózási API-referencia |} Microsoft Docs"
description: "Ismerkedés az Azure Active Directory naplózási API-hoz"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 44e46be8-09e5-4981-be2b-d474aaa92792
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/05/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 573e940c5390e7b990d889681eb37b73c5b253d9
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="azure-active-directory-audit-api-reference"></a>Az Azure Active Directory naplózási API-referencia
Ez a témakör az Azure Active Directory reporting API-val kapcsolatos témakörök gyűjteményét részét képezi.  
Az Azure AD jelentéskészítési lehetőséget biztosít az API-k, amely lehetővé teszi a kód vagy a kapcsolódó eszközök naplózási adatok eléréséhez.
Ez a témakör a hatóköre biztosításához kapcsolatos útmutatót a **API naplózási**.

Lásd:

* [A naplók](active-directory-reporting-azure-portal.md#activity-reports) további információ

* [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md) a reporting API-val kapcsolatos további információk.


Esetén:

- Gyakori kérdések, olvassa el a [– gyakori kérdések](active-directory-reporting-faq.md) 

- Adjon ki [fájlt egy támogatási jegy](active-directory-troubleshooting-support-howto.md) 


## <a name="who-can-access-the-data"></a>Ki férhet hozzá az adatokhoz?
* A biztonsági rendszergazda vagy biztonsági olvasó szerepkörű felhasználók
* A globális rendszergazdák
* Bármely alkalmazás, amely rendelkezik hozzáférési az API-t (alkalmazás engedélyezési is lehet a telepítő csak a globális rendszergazdai engedély alapján)

## <a name="prerequisites"></a>Előfeltételek
Ez a jelentés eléréséhez a Reporting API-n keresztül, ha rendelkezik:

* Egy [Azure Active Directory ingyenes vagy jobb edition](active-directory-editions.md)
* Befejeződött a [Előfeltételek az Azure AD reporting API eléréséhez](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Az API elérése
Vagy az API keresztül hozzáférhet a [Graph Explorer](https://graphexplorer2.cloudapp.net) vagy programozott módon, például PowerShell használatával. Ahhoz, hogy megfelelően értelmezni az OData-szűrőszintaxisának AAD Graph REST-hívásokban használt PowerShell, a backtick kell használnia (más néven: aposztróf) "karaktert" a $ karaktert. A backtick karakter funkcionál [PowerShell escape-karakter](https://technet.microsoft.com/library/hh847755.aspx), lehetővé téve a $ karaktert a literális értelmezésének, és elkerülheti a PowerShell változó neveként zavaró, PowerShell (ie: $filter).

A jelen témakör elsősorban a Graph Explorer. A PowerShell például megjelenik ez [PowerShell-parancsfájl](active-directory-reporting-api-audit-samples.md#powershell-script).

## <a name="api-endpoint"></a>API-végpont
Ez az API a következő URI használatával érhető el:  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta

Az Azure AD naplózási API-t (OData tördelési használatával) által visszaadott rekordok száma korlátozva van.
A jelentés adatainak megőrzési-korlátok, tekintse meg [adatmegőrzési Reporting](active-directory-reporting-retention.md).

Ez a hívás kötegekben adatait jeleníti meg. Minden egyes legfeljebb 1000 rekord rendelkezik.  
A következő mérési adatköteget a rekordok megtekintéséhez használja a következő hivatkozásra. A skiptoken adatok lehívása az első visszaadott rekordok készletét. A kihagyási lexikális elem az eredmény végén értéke lesz.  

    https://graph.windows.net/contoso.com/activities/audit?api-version=beta&%24skiptoken=-1339686058




## <a name="supported-filters"></a>Támogatott szűrők
Az API-k által visszaadott rekordok számának megadásával szűkíthető hívható meg egy szűrő formában.  
Bejelentkezés az API-hoz kapcsolódó adatok, a következő szűrőket támogatottak:

* **$top =\<visszaadott száma\>**  - visszaadott rekordok számát. Ez az egy drága művelet. Ha szeretne visszaállítani az objektumok több ezer ne használjon a szűrőt.     
* **$filter =\<a szűrő utasítás\>**  – megadhatja, támogatott szűrő mezők alapján az Önt érdeklő rekordok

## <a name="supported-filter-fields-and-operators"></a>Támogatott Szűrő mezőket és operátorok
Adja meg az Önt érdeklő rekordok, egy szűrő utasítást, amely egy vagy a következő szűrő mező tartalmazhat hozhat létre:

* [activityDate](#activitydate) -dátum vagy dátumtartomány meghatározása
* [kategória](#category) -határozza meg a szűrni kívánt kategóriát.
* [activityStatus](#activitystatus) -tevékenység állapotát határozza meg
* [az activityType](#activitytype) -tevékenység típusát határozza meg.
* [tevékenység](#activity) -karakterláncot határozza meg a tevékenység  
* [aktor/name](#actorname) -határozza meg a szereplő a szereplő név formátumban
* [aktor/objectid](#actorobjectid) -határozza meg a szereplő képernyőn a szereplő azonosító   
* [aktor/upn](#actorupn) -határozza meg a szereplő a szereplő elv felhasználónév (UPN) formájában 
* [cél/neve](#targetname) -határozza meg a cél a szereplő név formátumban
* [cél/objectid](#targetobjectid) -határozza meg a cél a cél azonosítójának formában  
* [cél/upn](#targetupn) -határozza meg a szereplő a szereplő elv felhasználónév (UPN) formájában   

- - -
### <a name="activitydate"></a>activityDate
**Támogatott operátorok**: eq, ge, le, gt, lt

**Példa**:

    $filter=tdomain + 'activities/audit?api-version=beta&`$filter=activityDate gt ' + $7daysago    

**Megjegyzések**:

dátum és idő (UTC) formátumban kell megadni

- - -
### <a name="category"></a>category

**Támogatott értékek**:

| Kategória                         | Érték     |
| :--                              | ---       |
| Alapvető könyvtár                   | Címtár |
| Önkiszolgáló jelszókezelés | SSPR      |
| Önkiszolgáló csoportkezelés    | SSGM      |
| Fiók kiépítése             | Sync      |
| Automatizált jelszóváltás      | Automatizált jelszóváltás |
| Identity Protection              | IdentityProtection |
| Meghívott felhasználók                    | Meghívott felhasználók |
| MIM szolgáltatás                      | MIM szolgáltatás |



**Támogatott operátorok**: eq

**Példa**:

    $filter=category eq 'SSPR'
- - -
### <a name="activitystatus"></a>ActivityStatus

**Támogatott értékek**:

| A tevékenység állapota | Érték |
| :--             | ---   |
| Sikeres         | 0     |
| Hiba         | - 1   |

**Támogatott operátorok**: eq

**Példa**:

    $filter=activityStatus eq -1    

---
### <a name="activitytype"></a>az ActivityType tulajdonság
**Támogatott operátorok**: eq

**Példa**:

    $filter=activityType eq 'User'    

**Megjegyzések**:

kis-és nagybetűket

- - -
### <a name="activity"></a>Tevékenység
**Támogatott operátorok**: eq, tartalmaz, a startswith elemnek

**Példa**:

    $filter=activity eq 'Add application' or contains(activity, 'Application') or startsWith(activity, 'Add')    

**Megjegyzések**:

kis-és nagybetűket

- - -
### <a name="actorname"></a>aktor/neve
**Támogatott operátorok**: eq, tartalmaz, a startswith elemnek

**Példa**:

    $filter=actor/name eq 'test' or contains(actor/name, 'test') or startswith(actor/name, 'test')    

**Megjegyzések**:

Nem betűérzékeny

- - -
### <a name="actorobjectid"></a>aktor/objectId
**Támogatott operátorok**: eq

**Példa**:

    $filter=actor/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba'    


- - -
### <a name="targetname"></a>cél/neve
**Támogatott operátorok**: eq, tartalmaz, a startswith elemnek

**Példa**:

    $filter=targets/any(t: t/name eq 'some name')    

**Megjegyzések**:

Nem betűérzékeny

- - -
### <a name="targetupn"></a>cél/egyszerű felhasználónév
**Támogatott operátorok**: eq, startswith elemnek

**Példa**:

    $filter=targets/any(t: startswith(t/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity/userPrincipalName,'abc'))    

**Megjegyzések**:

* Nem betűérzékeny
* Hozzá kell adnia a teljes névtér Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.TargetResourceUserEntity lekérdezésekor

- - -
### <a name="targetobjectid"></a>cél/objectId
**Támogatott operátorok**: eq

**Példa**:

    $filter=targets/any(t: t/objectId eq 'e8096343-86a2-4384-b43a-ebfdb17600ba')    

- - -
### <a name="actorupn"></a>aktor/egyszerű felhasználónév
**Támogatott operátorok**: eq, startswith elemnek

**Példa**:

    $filter=startswith(actor/Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity/userPrincipalName,'abc')    

**Megjegyzések**:

* Nem betűérzékeny 
* Hozzá kell adnia a teljes névtér Microsoft.ActiveDirectory.DataService.PublicApi.Model.Reporting.AuditLog.ActorUserEntity lekérdezésekor

- - -
## <a name="next-steps"></a>Következő lépések
* Meg szeretné tekinteni a szűrt rendszertevékenységét példák? Tekintse meg a [Azure Active Directory naplózási API minták](active-directory-reporting-api-audit-samples.md).
* Meg szeretné ismerni az Azure AD reporting API-val kapcsolatos? Lásd: [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md).

