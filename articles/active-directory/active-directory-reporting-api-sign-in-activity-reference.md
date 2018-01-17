---
title: "Az Azure Active Directory bejelentkezési tevékenység jelentés API-referencia |} Microsoft Docs"
description: "Az Azure Active Directory bejelentkezési tevékenység jelentés API-referencia"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: ddcd9ae0-f6b7-4f13-a5e1-6cbf51a25634
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 859459bbce6b81e2e855201d5c310233d88d0393
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-sign-in-activity-report-api-reference"></a>Az Azure Active Directory bejelentkezési tevékenység jelentés API-referencia
Ez a témakör az Azure Active Directory reporting API-val kapcsolatos témakörök gyűjteményét részét képezi.  
Az Azure AD jelentéskészítési lehetőséget biztosít az API-k, amely lehetővé teszi a kód és a kapcsolódó eszközök használata a bejelentkezési tevékenység jelentés adatok eléréséhez.
Ez a témakör a hatóköre biztosításához kapcsolatos útmutatót a **bejelentkezés tevékenység jelentés API**.

Lásd:

* [Bejelentkezési tevékenységek](active-directory-reporting-azure-portal.md#activity-reports) további információ
* [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md) a reporting API-val kapcsolatos további információk.


## <a name="who-can-access-the-api-data"></a>Az API-adatok hozzáférésének?
* Felhasználók és a biztonsági rendszergazda vagy a biztonsági olvasó szerepkörben Szolgáltatásnevekről
* A globális rendszergazdák
* Bármely alkalmazás, amely rendelkezik hozzáférési az API-t (alkalmazás engedélyezési is lehet a telepítő csak a globális rendszergazdai engedély alapján)

Hozzáférés az alkalmazás például bejelentkezik események API-k biztonsági eléréséhez konfigurálásához használja a következő PowerShell az alkalmazások egyszerű szolgáltatásnév az olvasó biztonsági szerepkör hozzáadása

```PowerShell
Connect-MsolService
$servicePrincipal = Get-MsolServicePrincipal -AppPrincipalId "<app client id>"
$role = Get-MsolRole | ? Name -eq "Security Reader"
Add-MsolRoleMember -RoleObjectId $role.ObjectId -RoleMemberType ServicePrincipal -RoleMemberObjectId $servicePrincipal.ObjectId
```

## <a name="prerequisites"></a>Előfeltételek
Ez a jelentés eléréséhez a reporting API-n keresztül, rendelkeznie kell:

* Egy [Azure Active Directory Premium P1 és P2 edition](active-directory-editions.md)
* Befejeződött a [Előfeltételek az Azure AD reporting API eléréséhez](active-directory-reporting-api-prerequisites.md). 

## <a name="accessing-the-api"></a>Az API elérése
Vagy az API keresztül hozzáférhet a [Graph Explorer](https://graphexplorer2.cloudapp.net) vagy programozott módon, például PowerShell használatával. Ahhoz, hogy megfelelően értelmezni az OData-szűrőszintaxisának AAD Graph REST-hívásokban használt PowerShell, a backtick kell használnia (más néven: aposztróf) "karaktert" a $ karaktert. A backtick karakter funkcionál [PowerShell escape-karakter](https://technet.microsoft.com/library/hh847755.aspx), lehetővé téve a $ karaktert a literális értelmezésének, és elkerülheti a PowerShell változó neveként zavaró, PowerShell (ie: $filter).

A jelen témakör elsősorban a Graph Explorer. A PowerShell például megjelenik ez [PowerShell-parancsfájl](active-directory-reporting-api-sign-in-activity-samples.md#powershell-script).

## <a name="api-endpoint"></a>API-végpont
Ez az API a következő alap URI használatával érhető el:  

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta  



Az adatok mennyisége miatt az API rendelkezik maximális egymillió visszaadott rekordok száma. 

Ez a hívás kötegekben adatait jeleníti meg. Minden egyes legfeljebb 1000 rekord rendelkezik.  
A következő mérési adatköteget a rekordok megtekintéséhez használja a következő hivatkozásra. Beolvasása a [skiptoken](https://msdn.microsoft.com/library/dd942121.aspx) adatait az első visszaadott rekordok készletét. A kihagyási lexikális elem az eredmény végén értéke lesz.  

    https://graph.windows.net/$tenantdomain/activities/signinEvents?api-version=beta&%24skiptoken=-1339686058


## <a name="supported-filters"></a>Támogatott szűrők
Az API-k által visszaadott rekordok számának megadásával szűkíthető hívható meg egy szűrő formában.  
Bejelentkezés az API-hoz kapcsolódó adatok, a következő szűrőket támogatottak:

* **$top =\<visszaadott száma\>**  - visszaadott rekordok számát. Ez az egy drága művelet. Ha szeretne visszaállítani az objektumok több ezer ne használjon a szűrőt.  
* **$filter =\<a szűrő utasítás\>**  – megadhatja, támogatott szűrő mezők alapján az Önt érdeklő rekordok

## <a name="supported-filter-fields-and-operators"></a>Támogatott Szűrő mezőket és operátorok
Adja meg az Önt érdeklő rekordok, egy szűrő utasítást, amely egy vagy a következő szűrő mező tartalmazhat hozhat létre:

* [signinDateTime](#signindatetime) -dátum vagy dátumtartomány meghatározása
* [userId](#userid) -határoz meg konkrét felhasználói alapján a felhasználó azonosítóját.
* [userPrincipalName](#userprincipalname) -határoz meg konkrét felhasználói alapján a felhasználó egyszerű felhasználónév (UPN)
* [appId](#appid) -meghatározása egy adott alkalmazás az alkalmazás azonosítója alapján
* [appDisplayName](#appdisplayname) -meghatározása egy adott alkalmazás alapján az alkalmazás megjelenített neve
* [loginStatus](#loginStatus) -a bejelentkezések állapotát határozza meg (sikeres vagy sikertelen)

> [!NOTE]
> Graph Explorer használata esetén meg a kis-és nagybetűk használata minden levél a Szűrő mezőket.
> 
> 

A visszaadott adatok körének szűkítéséhez, Szűrő mezők és a támogatott szűrők kombinációit hozhat létre. A következő utasítás például a felső 10 rekordokat. július 1-jétől 2016 és a 6. 2016. július között adja vissza:

    https://graph.windows.net/contoso.com/activities/signinEvents?api-version=beta&$top=10&$filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T00:00:00Z


- - -
### <a name="signindatetime"></a>signinDateTime
**Támogatott operátorok**: eq, ge, le, gt, lt

**Példa**:

Egy adott dátumot használatával

    $filter=signinDateTime+eq+2016-04-25T23:59:00Z    



Dátumtartomány használatával    

    $filter=signinDateTime+ge+2016-07-01T17:05:21Z+and+signinDateTime+le+2016-07-07T17:05:21Z


**Megjegyzések**:

A datetime paraméter az UTC formátumban kell megadni 

- - -
### <a name="userid"></a>userId
**Támogatott operátorok**: eq

**Példa**:

    $filter=userId+eq+’00000000-0000-0000-0000-000000000000’

**Megjegyzések**:

A userId értéke karakterlánc-értéke

- - -
### <a name="userprincipalname"></a>userPrincipalName
**Támogatott operátorok**: eq

**Példa**:

    $filter=userPrincipalName+eq+'audrey.oliver@wingtiptoysonline.com' 


**Megjegyzések**:

UserPrincipalName értéke karakterlánc-értéke

- - -
### <a name="appid"></a>appId
**Támogatott operátorok**: eq

**Példa**:

    $filter=appId+eq+’00000000-0000-0000-0000-000000000000’



**Megjegyzések**:

AppId érték karakterlánc-értéke

- - -
### <a name="appdisplayname"></a>appDisplayName
**Támogatott operátorok**: eq

**Példa**:

    $filter=appDisplayName+eq+'Azure+Portal' 


**Megjegyzések**:

AppDisplayName értéke karakterlánc-értéke

- - -
### <a name="loginstatus"></a>loginStatus
**Támogatott operátorok**: eq

**Példa**:

    $filter=loginStatus+eq+'1'  


**Megjegyzések**:

A loginStatus esetén két lehetőség áll rendelkezésre: 0 – sikeres, 1 – hiba

- - -
## <a name="next-steps"></a>További lépések
* Meg szeretné tekinteni a szűrt bejelentkezési tevékenységek példák? Tekintse meg a [Azure Active Directory bejelentkezési tevékenység jelentés API minták](active-directory-reporting-api-sign-in-activity-samples.md).
* Meg szeretné ismerni az Azure AD reporting API-val kapcsolatos? Lásd: [Bevezetés az Azure Active Directory Reporting API használatába](active-directory-reporting-api-getting-started.md).

