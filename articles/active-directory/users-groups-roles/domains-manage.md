---
title: Adja hozzá és ellenőrizze az egyéni tartománynevek – Azure Active Directory |} A Microsoft Docs
description: Felügyeleti fogalmakat és a egy tartománynevet az Azure Active Directory felügyeletéhez útmutatók
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
ms.openlocfilehash: 797ac714d3205a271fe6590a3a320e1b9e2a073e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194733"
---
# <a name="managing-custom-domain-names-in-your-azure-active-directory"></a>Az Azure Active Directoryban egyéni tartománynevek kezelése

Tartománynév fontos része annak a directory-erőforrások esetében az azonosító: egy felhasználói név vagy e-mail cím egy felhasználó, csoport, a cím szerepel, és egyes esetekben az alkalmazás Alkalmazásazonosító URI-t az alkalmazás részét képezi. Egy erőforrást az Azure Active Directory (Azure AD), amely a címtár, amely tartalmazza az erőforrás tulajdonosa tartománynevet is tartalmaznak. Csak globális rendszergazda is tartományok kezelése az Azure ad-ben.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Állítsa be az elsődleges tartomány nevét az Azure AD-címtár

Ha a címtár létrejött, a kezdeti tartománynevet, például "contoso.onmicrosoft.com", is tartománynév elsődleges. Az elsődleges tartomány a egy új felhasználó alapértelmezett tartománynév, ha új felhasználót hoz létre. Elsődleges tartománynév beállítása leegyszerűsíti a folyamatot a rendszergazdák az új felhasználók létrehozása a portálon. Az elsődleges tartomány nevének módosítása:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája lesz.
2. Válassza az **Azure Active Directory** elemet.
3. Válassza az **Egyéni tartománynevek** elemet.
  
   ![Felhasználókezelés megnyitása](./media/domains-manage/add-custom-domain.png)
4. Válassza ki a tartományt, érdemes az elsődleges tartomány nevét.
5. Válassza ki a **elsődlegessé** parancsot. Erősítse meg választását, amikor a rendszer kéri.
  
   ![Tartománynév elsődlegessé alakítás](./media/domains-manage/make-primary-domain.png)

Módosíthatja a át minden olyan ellenőrzött egyéni tartománynak, amely nincs összevonva az elsődleges tartomány nevét. Az elsődleges tartomány a címtár módosítása minden olyan meglévő felhasználók számára a felhasználónév nem változnak.

## <a name="add-custom-domain-names-to-your-azure-ad-tenant"></a>Egyéni tartománynevek hozzáadása az Azure AD-bérlővel

Legfeljebb 900 felügyelt tartományneveket adhat hozzá. Az összevonáshoz a tartományok konfigurálása esetén a helyszíni Active Directoryval, minden könyvtár akár 450 tartományneveket adhat hozzá.

## <a name="add-subdomains-of-a-custom-domain"></a>Egyéni tartomány altartományait hozzáadása

Ha például a "europe.contoso.com" harmadik szintű tartománynév hozzáadása a címtárhoz, kell először adja hozzá és ellenőrizze a második szintű tartomány, például a contoso.com. Az altartomány automatikusan az Azure AD által ellenőrizve. A hozzáadott altartomány ellenőrzése, hogy megtekintéséhez frissítse a tartományok listáját a böngészőben.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Mi a teendő, ha módosítja a DNS-regisztráló az egyéni tartománynevet?

Ha módosítja a DNS-regisztráló szervezetek, nincsenek további konfigurációs feladatok az Azure ad-ben. Továbbra is tartománynév használata az Azure ad-vel megszakítás nélkül. Ha az egyéni tartománynevet az Office 365-öt használ, Intune-ban vagy más szolgáltatások által használt egyéni tartománynevek, az Azure ad-ben, ezeket a szolgáltatásokat a dokumentációjában talál.

## <a name="delete-a-custom-domain-name"></a>Egyéni tartománynév törlése

Egyéni tartománynév törölheti az Azure AD-ből, ha a szervezet már nem használja ezt a nevet, vagy ha szeretné használni ezt a nevet egy másik Azure AD-val.

Egyéni tartománynév törlése, akkor előbb ellenőrizze, hogy nincsenek erőforrások a címtár támaszkodik a tartomány nevét. A címtárban lévő tartománynév nem törölhető, ha:

* Minden felhasználó rendelkezik egy felhasználónevet, a e-mail cím vagy a proxykiszolgáló címét, amely tartalmazza a tartománynevet.
* Minden csoport rendelkezik egy e-mail-címet vagy a proxykiszolgáló címét, amely tartalmazza a tartománynevet.
* Bármilyen alkalmazást az Azure ad-alkalmazás Alkalmazásazonosító URI-ja, amely tartalmazza a tartománynevet rendelkezik.

Módosítani kell, vagy bármely ilyen erőforrás törlése az Azure AD-címtárban, az egyéni tartománynév törlése előtt.

### <a name="forcedelete-option"></a>ForceDelete option

Is **ForceDelete** egy tartománynevet a [Azure AD felügyeleti központban](https://aad.portal.azure.com) vagy [Microsoft Graph API](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta). Ezek a beállítások egy aszinkron művelet, és frissítse az összes hivatkozást az egyéni tartománynév például a "user@contoso.com", a kezdeti alapértelmezett tartománynévnek például"user@contoso.onmicrosoft.com." 

A hívás **ForceDelete** az Azure Portalon, gondoskodnia kell arról, hogy is tartománynév 1000-nél kevesebb hivatkozik, és esetleges hivatkozásokat, ahol az Exchange-e a kiépítési szolgáltatás kell lehet frissíteni vagy abból eltávolított a [ Az Exchange felügyeleti központban](https://outlook.office365.com/ecp/). Ez magában foglalja a Exchange Mail-Enabled biztonsági csoportok és elosztott listák; További információkért lásd: [eltávolítás, levelezési címmel rendelkező biztonsági csoportok](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups). Emellett a **ForceDelete** művelet nem sikeres, ha teljesül a következők egyikét:

* Egy tartomány O365-szolgátasokat, tartományi előfizetés keresztül vásárolt
* Partnerként nevében egy másik ügyfél bérlői felügyelete

Részeként kell végezni a következő műveleteket a **ForceDelete** műveletet:

* A kezdeti alapértelmezett tartománynévnek átnevezi az egyszerű felhasználónév, EmailAddress és a felhasználók az egyéni tartománynév hivatkozó ProxyAddress.
* A csoportok az egyéni tartománynév hivatkozó EmailAddress átnevezi a kezdeti alapértelmezett tartománynévnek.
* Az egyéni tartománynév hivatkozó alkalmazások az identifierUris átnevezi a kezdeti alapértelmezett tartománynévnek.

Hibát ad vissza mikor:

* Át kell nevezni objektumok száma nagyobb, mint 1000
* Az alkalmazások, át kell nevezni egyik egy több-bérlős alkalmazást

### <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Miért sikertelen egy hiba, amely arról tájékoztatja, hogy a tartománynév van kezelt Exchange-csoportok a tartomány törlés?** <br>
**VÁLASZ:** Még ma, bizonyos csoportok, például a postafiókhoz biztonsági csoportok és elosztott listák az Exchange által kiépített, és a manuálisan törölni kell [az Exchange felügyeleti központban (EAC)](https://outlook.office365.com/ecp/). Nem lehet, hogy lehet fennmaradó ProxyAddresses, amely az egyéni tartománynév támaszkodjon, és a egy másik tartománynevet manuálisan frissíteni kell. 

**K: E vagyok jelentkezve admin@contoso.com , de nem lehet törölni a tartomány neve "contoso.com"?**<br>
**VÁLASZ:** Az egyéni tartománynevet szeretne törölni a felhasználói fiók nevében nem lehet hivatkozni. Győződjön meg arról, hogy a globális rendszergazdai fiókot használ a kezdeti alapértelmezett tartománynévnek (. onmicrosoft.com) például admin@contoso.onmicrosoft.com. Jelentkezzen be egy másik globális rendszergazdai fiók, amely például admin@contoso.onmicrosoft.com vagy egy másik egyéni tartománynevet, például a "fabrikam.com" az fióknak admin@fabrikam.com.

**K: A Delete tartomány gombra, és tekintse meg gombra kattintás után `In Progress` a törlési művelet állapotát. Mennyi időt vesz igénybe? Mi történik, ha a sikertelen?**<br>
**VÁLASZ:** A törlés tartomány művelet egy aszinkron háttérfeladat átnevezi is tartománynév mutató összes hivatkozást. Egy percen belül teljesítéséhez. Ha a tartomány törlése sikertelen, győződjön meg arról, hogy nem rendelkezik:

* A tartománynevet a appIdentifierURI konfigurált alkalmazások
* Az egyéni tartománynév hivatkozó levelezési csoport
* A tartománynév több mint 1000 hivatkozik

Ha azt tapasztalja, hogy a feltételek nem teljesülnek, manuálisan törölni a hivatkozásokat, és próbálja meg újra törölni a tartományhoz.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Tartománynevek kezelése a PowerShell vagy Graph API használata

A tartománynevek az Azure Active Directoryban a legtöbb kezelési feladatot a Microsoft PowerShell-lel, vagy programozott módon az Azure AD Graph API használatával is elvégezhető.

* [Tartománynevek kezelése az Azure ad-ben a PowerShell segítségével](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
* [Tartománynevek kezelése az Azure AD Graph API-val](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>További lépések

* [Egyéni tartománynevek hozzáadása](/azure/active-directory/fundamentals/add-custom-domain?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Távolítsa el az Exchange levelezési címmel rendelkező biztonsági csoportok az Exchange felügyeleti központban az egyéni tartománynév az Azure ad-ben](https://technet.microsoft.com/library/bb123521(v=exchg.160).aspx#Remove%20mail-enabled%20security%20groups)
* [Egyéni tartománynév ForceDelete Microsoft Graph API-val](https://docs.microsoft.com/graph/api/domain-forcedelete?view=graph-rest-beta)
