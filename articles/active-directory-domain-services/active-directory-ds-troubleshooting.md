---
title: 'Az Azure Active Directory tartományi szolgáltatások: Hibaelhárítási útmutató |} A Microsoft Docs'
description: Az Azure AD Domain Services hibaelhárítási útmutató
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: ergreenl
ms.openlocfilehash: 8b752585fc72b7f4be8e7b9320290f8ad56f53c2
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844653"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Az Azure AD tartományi szolgáltatások – hibaelhárítási útmutató
Ez a cikk a problémák jelentkezhetnek, ha beállítása és felügyelete Azure Active Directory (AD) Domain Services hibaelhárítási útmutatók nyújt.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Az Azure AD-címtár az Azure AD Domain Services nem engedélyezhető.
Ez a szakasz segítséget nyújt a hibák elhárítása Azure AD tartományi szolgáltatásokat a címtárhoz engedélyezéséhez meg.

Válassza ki a hibaelhárítási lépéseket, amelyek megfelelnek a hibaüzenetet tapasztal.

| **Hibaüzenet** | **Felbontás** |
| --- |:--- |
| *A contoso100.com név már használatban van ezen a hálózaton. Adjon meg olyan nevet, amely még nincs használatban.* |[Tartomány névütközés a virtuális hálózatban](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az „Azure AD Domain Services Sync” alkalmazáshoz. Törölje az „Azure AD Domain Services Sync” alkalmazást, és próbálja meg ezután engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlője számára.* |[Domain Services nem rendelkezik megfelelő engedélyekkel az Azure AD Domain Services Sync alkalmazás](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőjében található Domain Services alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.* |[A bérlő a Domain Services alkalmazás nem megfelelően van konfigurálva](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Ön Azure AD-bérlőjében. Engedélyezze a 00000002-0000-0000-c000-000000000000 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.* |[A Microsoft Graph-alkalmazás le van tiltva, az Azure AD-bérlőben](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Tartomány névütközés
**Hibaüzenet jelenik meg:**

*A contoso100.com név már használatban van ezen a hálózaton. Adjon meg olyan nevet, amely még nincs használatban.*

**Szervizelési:**

Győződjön meg arról, hogy nem kell ugyanazon a néven érhető el, hogy a virtuális hálózatban a meglévő tartományhoz. Tegyük fel például, hogy a kiválasztott virtuális hálózatban már van egy contoso.com nevű tartomány. Később akkor próbálja meg engedélyezni a ugyanazon tartomány nevét (például "contoso.com") a virtuális hálózat az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz. Az Azure AD tartományi szolgáltatások engedélyezése közben hibát tapasztal.

Ez a hiba a tartomány nevét, hogy a virtuális hálózatban tapasztalható névütközés okozza. Ebben az esetben az Azure AD tartományi szolgáltatások által kezelt tartomány beállításához másik nevet kell használnia. A másik lehetséges megoldás az, hogy leépíti a meglévő tartományt, majd ezután folytatja a Azure AD tartományi szolgáltatások engedélyezését.

### <a name="inadequate-permissions"></a>Nincsenek megfelelő engedélyei
**Hibaüzenet jelenik meg:**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az „Azure AD Domain Services Sync” alkalmazáshoz. Törölje az „Azure AD Domain Services Sync” alkalmazást, és próbálja meg ezután engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlője számára.*

**Szervizelési:**

Ellenőrizze, hogy van-e az Azure AD-címtárban "az Azure AD Domain Services Sync" nevű alkalmazás. Ha az alkalmazás már létezik, törölje azt, és ezután engedélyezze újra az Azure AD Domain Services.

A következő lépésekkel az alkalmazás jelenlétének az ellenőrzéséhez és törölni, ha az alkalmazás már létezik:

1. Keresse meg a **alkalmazások** az Azure AD-címtárakat szakaszában a [az Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Válassza ki **minden alkalmazás** a a **megjelenítése** legördülő listából. Válassza ki **bármely** a a **alkalmazások állapotának** legördülő listából. Válassza ki **bármely** a a **alkalmazás láthatósága** legördülő listából.
3. Típus **az Azure AD Domain Services Sync** kifejezést a keresőmezőbe. Ha az alkalmazás már létezik, kattintson rá, és kattintson a **törlése** gomb az eszköztáron, törölje azt.
4. Miután az alkalmazás törölt, próbálja meg ismét az Azure AD tartományi szolgáltatások engedélyezése.

### <a name="invalid-configuration"></a>Érvénytelen konfiguráció
**Hibaüzenet jelenik meg:**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőjében található Domain Services alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.*

**Szervizelési:**

Ellenőrizze, hogy ha az Azure AD-címtárban kell "AzureActiveDirectoryDomainControllerServices" (az alkalmazás azonosítója, amelyet d87dcbc6-a371-462e-88e3-28ad15ec4e64) nevű kérelmet. Ha az alkalmazás már létezik, törölje azt, és ezután engedélyezze újra az Azure AD Domain Services szüksége.

A következő PowerShell-parancsfájl használatával keresse meg az alkalmazást, és törölje azt.

> [!NOTE]
> Ez a szkript **az Azure AD PowerShell 2-es verzió** parancsmagok. A teljes listát az összes elérhető parancsmagjainak, és töltse le a modult, olvassa el a [Azure ad PowerShell-referenciáinak dokumentációja](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>A Microsoft Graph le van tiltva
**Hibaüzenet jelenik meg:**

Nem sikerült engedélyezni a domain Services az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Ön Azure AD-bérlőjében. Engedélyezze a 00000002-0000-0000-c000-000000000000 az alkalmazást, és ezután próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlővel.

**Szervizelési:**

Ellenőrizze, hogy ha 00000002-0000-0000-c000-000000000000 azonosítójú alkalmazás le van tiltva. Ez az alkalmazás a Microsoft Azure AD-alkalmazás és a Graph API-hozzáférést biztosít az Azure AD-bérlővel. Az Azure AD Domain Services az alkalmazás szinkronizálása az Azure AD-bérlőhöz, a felügyelt tartomány engedélyezni kell.

Ez a hiba elhárításához alkalmazás engedélyezése, és ezután próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlővel.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>A felhasználók nem tudnak bejelentkezni az Azure AD Domain Services által felügyelt tartományba
Ha az Azure AD-bérlőben egy vagy több felhasználó nem tud bejelentkezni az újonnan létrehozott felügyelt tartományba, hajtsa végre az alábbi hibaelhárítási lépéseket:

* **Jelentkezzen be használatával egyszerű felhasználónév formátuma:** A bejelentkezéshez próbálja az egyszerű felhasználónév (UPN) formátumot használni (például „joeuser@contoso.com”) a SAMAccountName formátum helyett („CONTOSO\joeuser”). A SAMAccountName előfordulhat, hogy automatikusan generált felhasználók amelynek egyszerű Felhasználónévi előtagját túl hosszú, vagy ugyanaz, mint egy másik felhasználó a felügyelt tartományon. Az egyszerű felhasználónév formátumú garantáltan Azure AD-bérlő belül egyedinek kell lennie.

> [!NOTE]
> Azt javasoljuk, hogy az Azure AD tartományi szolgáltatásokkal felügyelt tartományban jelentkezzen be az egyszerű felhasználónév formátumban.
>
>

* Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást](active-directory-ds-getting-started-password-sync.md) az első lépéseket ismertető útmutató lépéseinek megfelelően.
* **Külső fiókok:** Ügyeljen arra, hogy az érintett felhasználói fiók ne külső fiók legyen az Azure AD-bérlőben. Külső fiókok például a Microsoft-fiókok (például "joe@live.com") vagy egy külső felhasználói fiókok Azure AD-címtárban. Az Azure AD Domain Services nem rendelkezik ilyen felhasználói fiókokhoz tartozó hitelesítő adatokat, mivel ezek a felhasználók nem tud bejelentkezni a felügyelt tartományhoz.
* **A szinkronizált fiókok:** Ha az érintett felhasználói fiókok helyszíni címtárból vannak szinkronizálva, ellenőrizze, hogy:

  * Van üzembe helyezve, vagy a frissített a [legújabb ajánlott kiadását az Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).
  * Konfigurálta az Azure AD Connect [végezzen teljes szinkronizálást](active-directory-ds-getting-started-password-sync.md).
  * A címtár méretétől függően ez lehetséges, hogy tarthat a felhasználói fiókokhoz, és hitelesítőadat-kivonatok Azure AD tartományi szolgáltatások elérhetők. Győződjön meg arról, hogy elég hosszú újrapróbálkozás előtt egy kis hitelesítés.
  * Ha ellenőrizte, hogy a fenti lépéseket a probléma továbbra is fennáll, próbálja meg újraindítani a Microsoft Azure AD Sync szolgáltatást. A szinkronizált gépen nyisson meg egy parancssort, és hajtsa végre a következő parancsokat:

    1. net stop "Microsoft Azure AD Sync"
    2. a net start "Microsoft Azure AD Sync"
* **Kizárólag felhőalapú fiókok**: Ha az érintett felhasználói fiók egy csak felhőalapú felhasználói fiók, győződjön meg arról, hogy a felhasználó megváltoztatta a jelszavát, miután engedélyezte az Azure AD tartományi szolgáltatásokat. Ezzel a lépéssel létrejönnek az Azure AD tartományi szolgáltatásokhoz szükséges hitelesítő adatok kivonatai.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Nincsenek egy vagy több riasztást a felügyelt tartományon

Tekintse meg a felügyelt tartományra a riasztások megoldása funkcionáló a [riasztások hibaelhárítása](active-directory-ds-troubleshoot-alerts.md) cikk.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Az Azure AD-bérlőjéből eltávolított felhasználók nem törlődnek a felügyelt tartományhoz
Az Azure AD megvédi Önt a felhasználói objektumok véletlen törlésétől. Amikor töröl egy felhasználói fiókot az Azure AD-bérlőből, a megfelelő felhasználói objektum átkerül a Lomtárba. Ezt a műveletet a felügyelt tartomány szinkronizálásakor azt eredményezi, a megfelelő felhasználói fiók letiltottként lesz megjelölve. Ez a funkció segít a helyre, vagy később törlésének visszavonása a felhasználói fiókot.

A felhasználói fiók a felügyelt tartományra a letiltott állapotban marad, akkor is, ha újra létrehozza ugyanazzal az egyszerű Felhasználónévvel rendelkező felhasználói fiókkal az Azure AD-címtárhoz. A felhasználói fiók eltávolítása a felügyelt tartományra, szüksége kényszerített törlése az Azure AD-bérlővel.

Teljes mértékben eltávolítja a felügyelt tartományra a felhasználói fiókot, hogy a felhasználó véglegesen törli a az Azure AD-bérlőjében. Használja a `Remove-MsolUser` PowerShell-parancsmagot a `-RemoveFromRecycleBin` paraméterrel, ahogyan azt a jelen [MSDN-cikkben](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](active-directory-ds-contact-us.md).
