---
title: Egyéni tartománynevek hozzáadása és ellenőrzése – Azure Active Directory | Microsoft dokumentumok
description: Felügyeleti fogalmak és útmutatók a tartománynevek kezeléséhez az Azure Active Directoryban
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2395aa5984de2a9fe41e4778d16aba69bfef5192
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559233"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Egyéni tartománynevek kezelése az Azure Active Directoryban

A tartománynév számos címtár-erőforrás azonosítójának fontos része: egy felhasználó felhasználónevének vagy e-mail-címének, egy csoport címének része, és néha egy alkalmazás alkalmazásazonosító URI-jának része. Az Azure Active Directoryban (Azure AD) egy erőforrás tartalmazhat egy tartománynevet, amely az erőforrást tartalmazó könyvtár tulajdonában van. Csak egy globális rendszergazda kezelheti a tartományokat az Azure AD-ben.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Az Azure AD-címtár elsődleges tartománynevének beállítása

A címtár létrehozásakor a kezdeti tartománynév( például "contoso.onmicrosoft.com") egyben az elsődleges tartománynév is. Új felhasználó létrehozásakor az elsődleges tartomány az új felhasználó alapértelmezett tartományneve. Az elsődleges tartománynév beállítása leegyszerűsíti a rendszergazda számára az új felhasználók létrehozásának folyamatát a portálon. Az elsődleges tartománynév módosítása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza az **Azure Active Directory**lehetőséget.
3. Válassza az **Egyéni tartománynevek** elemet.
  
   ![A felhasználókezelés lap megnyitása](./media/domains-manage/add-custom-domain.png)
4. Válassza ki annak a tartománynak a nevét, amelyet elsődleges tartományként szeretne megtekinteni.
5. Válassza az **Elsődleges átalakítása** parancsot. Erősítse meg a választást, amikor a rendszer kéri.
  
   ![A tartománynév legyen az elsődleges](./media/domains-manage/make-primary-domain.png)

Módosíthatja a címtár elsődleges tartománynevét, hogy minden olyan ellenőrzött egyéni tartomány legyen, amely nincs összevont. A címtár elsődleges tartományának módosítása nem változtatja meg a meglévő felhasználók felhasználónevét.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Egyéni tartománynevek hozzáadása az Azure AD-bérlőhöz

Legfeljebb 900 felügyelt tartománynevet adhat hozzá. Ha az összes tartományt a helyszíni Active Directoryval összevonásra konfigurálja, minden címtárban legfeljebb 450 tartománynevet adhat hozzá.

## <a name="add-subdomains-of-a-custom-domain"></a>Egyéni tartomány altartományainak hozzáadása

Ha harmadik szintű tartománynevet (például "europe.contoso.com" is szeretne hozzáadni a címtárhoz, először adja hozzá és ellenőrizze a második szintű tartományt, például a contoso.com. Az azure AD automatikusan ellenőrzi az altartományt. Ha ellenőrizni szeretné, hogy a hozzáadott altartomány ellenőrizve van-e, frissítse a tartománylistát a böngészőben.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Mi a teendő, ha módosítja az egyéni tartománynév DNS-regisztrálóját?

Ha módosítja a DNS-regisztrálók, nincsenek további konfigurációs feladatok az Azure AD-ben. A tartománynév megszakítás nélkül továbbra is használható az Azure AD-vel. Ha az egyéni tartománynevet az Office 365, az Intune vagy más, az Azure AD-ben egyéni tartományneveket használó szolgáltatásokkal használja, tekintse meg a szolgáltatások dokumentációját.

## <a name="delete-a-custom-domain-name"></a>Egyéni tartománynév törlése

Törölheti az egyéni tartománynevet az Azure AD-ből, ha a szervezet már nem használja ezt a tartománynevet, vagy ha ezt a tartománynevet egy másik Azure AD-vel kell használnia.

Egyéni tartománynév törléséhez először győződjön meg arról, hogy a címtáregyetlen erőforrása sem támaszkodik a tartománynévre. A tartománynév nem törölhető a címtárból, ha:

* Minden felhasználónak van felhasználóneve, e-mail címe vagy proxycíme, amely tartalmazza a tartománynevet.
* Minden csoport rendelkezik egy e-mail címmel vagy proxycímmel, amely tartalmazza a tartománynevet.
* Az Azure AD-ben minden alkalmazás rendelkezik egy alkalmazásazonosító-URI-val, amely tartalmazza a tartománynevet.

Az egyéni tartománynév törlése előtt módosítania kell vagy törölnie kell az ilyen erőforrásokat az Azure AD-címtárban.

### <a name="forcedelete-option"></a>ForceDelete beállítás

A tartománynevek **et kényszerítheti** az [Azure AD Felügyeleti központban](https://aad.portal.azure.com) vagy a Microsoft Graph API [használatával.](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta) Ezek a beállítások aszinkron műveletet használnak, és frissítik azuser@contoso.comegyéni tartománynévösszes hivatkozását,user@contoso.onmicrosoft.compéldául a " " " a kezdeti alapértelmezett tartománynevet, például a " " ". 

A **ForceDelete** hívásához az Azure Portalon, meg kell győződnie arról, hogy kevesebb mint 1000 hivatkozás van a tartománynévre, és minden olyan hivatkozást, amelyben az Exchange a létesítési szolgáltatás, frissíteni kell vagy el kell távolítania az [Exchange Felügyeleti központban.](https://outlook.office365.com/ecp/) Ide tartoznak az Exchange Mail-kompatibilis biztonsági csoportok és az elosztott listák; További információt a [Levelezést támogató biztonsági csoportok eltávolítása](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)című témakörben talál. A **ForceDelete** művelet nem lesz sikeres, ha az alábbi műveletek valamelyike igaz:

* Tartományt vásárolt az Office 365 tartományi előfizetési szolgáltatásain keresztül
* Ön egy másik ügyfél-bérlő nevében felügyelt partner

A **ForceDelete** művelet részeként a következő műveletek et hajtják végre:

* Átnevezi a felhasználók upn, emailaddress és proxycímét úgy, hogy az egyéni tartománynévre hivatkoznak a kezdeti alapértelmezett tartománynévre.
* Átnevezi a csoportok e-mailcímét az egyéni tartománynévre való hivatkozással a kezdeti alapértelmezett tartománynévre.
* Átnevezi az alkalmazások azonosítóit az egyéni tartománynévre való hivatkozással a kezdeti alapértelmezett tartománynévre.

A rendszer akkor ad vissza hibát, ha:

* Az átnevezendő objektumok száma meghaladja az 1000-et
* Az átnevezendő alkalmazások egyike egy több-bérlős alkalmazás

### <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért nem sikerül a tartomány törlése egy olyan hibával, amely azt állítja, hogy az Exchange által kisajátított csoportokkal rendelkezik ezen a tartománynéven?** <br>
**A.** Ma bizonyos csoportokat, például a Mail-kompatibilis biztonsági csoportokat és az elosztott listákat az Exchange kiépíti, és az [Exchange Felügyeleti központban (EAC)](https://outlook.office365.com/ecp/)manuálisan kell tisztítani. Lehet, hogy elhúzódó ProxyAddresses, amelyek támaszkodnak az egyéni tartomány nevét, és frissíteni kell manuálisan egy másik domain nevet. 

**K: Rendszergazdaként\@vagyok bejelentkezve contoso.com de nem tudom törölni a "contoso.com" tartománynevet?**<br>
**A.** A felhasználói fiók nevében nem hivatkozhat a törölni kívánt egyéni tartománynévre. Győződjön meg arról, hogy a globális rendszergazdai fiók admin@contoso.onmicrosoft.coma kezdeti alapértelmezett tartománynevet (.onmicrosoft.com) használja, például . Jelentkezzen be egy másik globális rendszergazdai fiókkal, amely például admin@contoso.onmicrosoft.com olyan admin@fabrikam.comegyéni tartománynév, mint például a "fabrikam.com", ahol a fiók található.

**K: Rákattintottam a Tartomány `In Progress` törlése gombra, és láttam a Törlés művelet állapotát. Mennyi ideig tart? Mi történik, ha nem sikerül?**<br>
**A.** A tartománytörlése művelet egy aszinkron háttérfeladat, amely átnevezi a tartománynévre mutató összes hivatkozást. Egy-két percen belül be kell fejezni. Ha a tartomány törlése sikertelen, győződjön meg arról, hogy nem rendelkezik:

* A tartománynévn konfigurált alkalmazások az appIdentifierURI-val
* Az egyéni tartománynévre hivatkozó bármely levélbarát csoport
* Több mint 1000 hivatkozás a domain névre

Ha úgy találja, hogy a feltételek bármelyike nem teljesült, manuálisan törölje a hivatkozásokat, és próbálja meg újra törölni a tartományt.

## <a name="use-powershell-or-the-microsoft-graph-api-to-manage-domain-names"></a>Tartománynevek kezelése a PowerShell vagy a Microsoft Graph API segítségével

Az Azure Active Directorytartomány-nevek felügyeleti feladatainak legtöbb feladata a Microsoft PowerShell használatával vagy programozott módon is elvégezhető a Microsoft Graph API használatával.

* [Tartománynevek kezelése a PowerShell használatával az Azure AD-ben](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#domains)
* [Tartományi erőforrás típusa](https://docs.microsoft.com/graph/api/resources/domain?view=graph-rest-1.0)

## <a name="next-steps"></a>További lépések

* [Egyéni tartománynevek hozzáadása](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Exchange-levelezést engedélyező biztonsági csoportok eltávolítása az Exchange Felügyeleti központban egyéni tartománynévről az Azure AD-ben](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [Egyéni tartománynév kényszerítése a Microsoft Graph API-val](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
