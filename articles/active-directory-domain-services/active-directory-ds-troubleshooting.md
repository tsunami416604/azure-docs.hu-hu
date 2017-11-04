---
title: "Az Azure Active Directory tartományi szolgáltatások: Hibaelhárítási útmutatója |} Microsoft Docs"
description: "Az Azure AD tartományi szolgáltatásokra vonatkozó hibaelhárítási útmutató"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: maheshu
ms.openlocfilehash: 34335db77a5e414af4cfa77d6223ab5290bae614
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD tartományi szolgáltatások - hibaelhárítási útmutatója
Ez a cikk hibaelhárítási tippek biztosít a problémák jelentkezhetnek, ha beállítása és felügyelete az Azure Active Directory (AD) tartományi szolgáltatásokban.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nem lehet engedélyezni az Azure AD-címtár az Azure AD tartományi szolgáltatások
Ez a szakasz segítséget nyújt a hibák elhárítása, amikor megpróbálja engedélyezni a címtár Azure AD tartományi szolgáltatásokat, és nem sikerül, vagy vissza a "Letiltva" váltható lekérdezi.

Válassza ki a hibaelhárítási lépéseket, amelyek megfelelnek a hibaüzenet a következő megjelenne.

| **Hibaüzenet** | **Megoldás** |
| --- |:--- |
| *A contoso100.com név már használatban van ezen a hálózaton. Adjon meg olyan nevet, amely még nincs használatban.* |[Tartomány névütközés a virtuális hálózat](active-directory-ds-troubleshooting.md#domain-name-conflict) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az „Azure AD Domain Services Sync” alkalmazáshoz. Törölje az „Azure AD Domain Services Sync” alkalmazást, és próbálja meg ezután engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlője számára.* |[Tartományi szolgáltatások nem rendelkezik megfelelő engedéllyel az Azure AD tartományi szolgáltatások Szinkronizáló alkalmazás](active-directory-ds-troubleshooting.md#inadequate-permissions) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőjében található Domain Services alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.* |[A tartományi szolgáltatások alkalmazás nincs megfelelően konfigurálva az Ön bérlőjében](active-directory-ds-troubleshooting.md#invalid-configuration) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Ön Azure AD-bérlőjében. Engedélyezze a 00000002-0000-0000-c000-000000000000 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.* |[A Microsoft Graph alkalmazás le van tiltva, az Azure AD-bérlőben](active-directory-ds-troubleshooting.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Tartomány névütközés
**Hibaüzenet:**

*A contoso100.com név már használatban van ezen a hálózaton. Adjon meg olyan nevet, amely még nincs használatban.*

**Szervizelés:**

Győződjön meg arról, hogy nem rendelkezik egy meglévő tartományhoz, a tartomány néven érhető el a virtuális hálózat. Tegyük fel például, hogy a kiválasztott virtuális hálózatban már van egy contoso.com nevű tartomány. Később akkor próbálja meg engedélyezni az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz, azonos nevű tartomány (például "contoso.com") a virtuális hálózat. Azure AD tartományi szolgáltatások engedélyezése közben hibát tapasztal.

Ez a hiba a tartománynév, a virtuális hálózatban tapasztalható névütközés okozza. Ebben az esetben az Azure AD tartományi szolgáltatások által kezelt tartomány beállításához másik nevet kell használnia. A másik lehetséges megoldás az, hogy leépíti a meglévő tartományt, majd ezután folytatja a Azure AD tartományi szolgáltatások engedélyezését.

### <a name="inadequate-permissions"></a>Nincsenek megfelelő engedélyei
**Hibaüzenet:**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az „Azure AD Domain Services Sync” alkalmazáshoz. Törölje az „Azure AD Domain Services Sync” alkalmazást, és próbálja meg ezután engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlője számára.*

**Szervizelés:**

Ellenőrizze, hogy van-e egy alkalmazás "Azure AD tartományi szolgáltatások Sync" az Azure AD-címtárát a néven. Ha ezt az alkalmazást, törölje azt, majd engedélyezze újra Azure AD tartományi szolgáltatásokat.

A következő lépésekkel az alkalmazás meglétének ellenőrzése és törlése, ha az alkalmazás létezik:

1. Nyissa meg a **klasszikus Azure-portált** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Válassza az **Active Directory** csomópontot a bal oldali panelen.
3. Válassza ki az Azure AD bérlőt (címtárat), amelyiken engedélyezni kívánja az Azure AD tartományi szolgáltatásokat.
4. Keresse meg a **alkalmazások** fülre.
5. Válassza ki a **a vállalat tulajdonában lévő alkalmazások** beállítást meg a legördülő listában.
6. Ellenőrizze az alkalmazás **Azure AD tartományi szolgáltatások szinkronizáló**. Ha az alkalmazás létezik, ugorjon a törölje azt.
7. Ha törölte az alkalmazáshoz, próbálja meg ismét Azure AD tartományi szolgáltatások engedélyezése.

### <a name="invalid-configuration"></a>Érvénytelen konfiguráció
**Hibaüzenet:**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőjében található Domain Services alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.*

**Szervizelés:**

Ellenőrizze, hogy van-e (az alkalmazás azonosítója, amelyet d87dcbc6-a371-462e-88e3-28ad15ec4e64) "AzureActiveDirectoryDomainControllerServices" nevű alkalmazás az Azure AD-címtárát. Ha az alkalmazás már létezik, törölje azt, és majd engedélyezze újra Azure AD tartományi szolgáltatások szüksége.

A következő PowerShell-parancsfájl segítségével keresse meg az alkalmazást, és törölje azt.

> [!NOTE]
> A parancsfájl a **Azure AD PowerShell 2-es** parancsmagok. Teljes listáját az összes rendelkezésre álló parancsmagok és a modul letöltése, olvassa el a [AzureAD PowerShell referenciadokumentációt](https://msdn.microsoft.com/library/azure/mt757189.aspx).
>
>

```
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
**Hibaüzenet:**

Tartományi szolgáltatások nem sikerült engedélyezni ezt az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Ön Azure AD-bérlőjében. Az alkalmazás az alkalmazás azonosítója 00000002-0000-0000-c000-000000000000 engedélyezése, és próbálja meg az Azure AD-bérlő tartományi szolgáltatások engedélyezése.

**Szervizelés:**

Ellenőrizze, hogy ha le van tiltva az alkalmazás azonosítóját 00000002-0000-0000-c000-000000000000. Ez az alkalmazás a Microsoft Azure AD-alkalmazás, és a Graph API-hozzáférést biztosít az Azure AD-bérlő. Az Azure AD tartományi szolgáltatásokat kell engedélyezni a felügyelt tartományra az Azure AD-bérlő szinkronizálni kell az alkalmazás.

Ez a hiba elhárításához alkalmazás engedélyezése, és próbálja meg az Azure AD-bérlő tartományi szolgáltatások engedélyezése.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>A felhasználók nem tudnak bejelentkezni az Azure AD Domain Services által felügyelt tartományba
Ha az Azure AD-bérlőről egy vagy több felhasználók nem jelentkezhetnek be az újonnan létrehozott felügyelt tartományra, hajtsa végre a következő hibaelhárítási lépéseket:

* **Jelentkezzen be UPN formátum használatával:** próbáljon meg bejelentkezni az UPN-formátumban (például "joeuser@contoso.com") helyett a SAMAccountName formátumban ("CONTOSO\joeuser"). A SAMAccountName előfordulhat, hogy automatikusan generált felhasználók amelynek UPN előtag túlságosan hosszú, vagy azonos egy másik felhasználója a felügyelt tartományra. Az egyszerű felhasználónév formátuma garantáltan az Azure AD-bérlő belül egyedinek kell lennie.

> [!NOTE]
> Azt javasoljuk, hogy jelentkezzen be az Azure AD tartományi szolgáltatások által kezelt tartomány a UPN formátumban.
>
>

* Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást](active-directory-ds-getting-started-password-sync.md) az első lépéseket ismertető útmutató lépéseinek megfelelően.
* **Külső fiókok:** győződjön meg arról, hogy az érintett felhasználó egy nem külső fiók az Azure AD-bérlőben. Külső fiókok például Microsoft-fiókok (például "joe@live.com") vagy külső felhasználói fiókok Azure AD-címtárban. Azure AD tartományi szolgáltatások nem tartozik ilyen felhasználói fiókhoz tartozó hitelesítő adatok, ezek a felhasználók nem jelentkezhet be a felügyelt tartományra.
* **Fiókok szinkronizálva:** az érintett felhasználói fiókok szinkronizálása a helyszíni könyvtárból, ellenőrizze, hogy:

  * Telepítése vagy frissítése a [legújabb ajánlott az Azure AD Connect kiadás](https://www.microsoft.com/en-us/download/details.aspx?id=47594).
  * Az Azure AD Connect konfigurálása [teljes szinkronizálást végezzen](active-directory-ds-getting-started-password-sync.md).
  * A címtár méretétől függően esetlegesen időigényes a felhasználói fiókok és hitelesítőadat-kivonatok elérhető lesz az Azure AD tartományi szolgáltatásokat. Gondoskodjon arról, hogy elegendő ideig, mielőtt újra próbálkozna a hitelesítési várja.
  * Ha az előző lépések ellenőrzése után a probléma továbbra is fennáll, indítsa újra a Microsoft Azure AD Sync szolgáltatás. A szinkronizálás gépről elindítani a parancssort, és hajtsa végre a következő parancsokat:

    1. net stop "Microsoft Azure AD Sync"
    2. a net start "Microsoft Azure AD Sync"
* **Csak felhőalapú fiókok**: Ha az érintett felhasználó fiók egy kizárólag felhőalapú felhasználói fiókot, győződjön meg arról, hogy a felhasználó módosította a jelszavát, miután engedélyezte az Azure AD tartományi szolgáltatások. Ezzel a lépéssel létrejönnek az Azure AD tartományi szolgáltatásokhoz szükséges hitelesítő adatok kivonatai.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Eltávolítja az Azure AD-bérlő felhasználók a felügyelt tartományok nem törlődnek
Az Azure AD megvédi Önt a felhasználói objektumok véletlen törlésétől. Amikor töröl egy felhasználói fiókot az Azure AD-bérlőből, a megfelelő felhasználói objektum átkerül a Lomtárba. Amikor a törlési művelet szinkronizálja a felügyelt tartományra, a megfelelő felhasználói fiókkal kell megjelölni, letiltott okoz. Ez a szolgáltatás segítségével helyre, vagy később törlésének visszavonása a felhasználói fiók.

A felhasználói fiók a felügyelt tartományok letiltott állapotban marad, akkor is, ha az Azure AD-címtárát hozza létre a azonos egyszerű Felhasználónévvel rendelkező felhasználói fiókkal. Távolítsa el a felhasználói fiók a felügyelt tartományok, kényszeríteni kell törölni az Azure AD-bérlőn.

A teljes mértékben eltávolítja a felügyelt tartományok a felhasználói fiók, a felhasználó véglegesen törli az Azure AD-bérlőn. Használja a Remove-MsolUser PowerShell-parancsmagot a „-RemoveFromRecycleBin” paraméterrel, ahogyan az az alábbi [MSDN-cikkben](https://msdn.microsoft.com/library/azure/dn194132.aspx) is olvasható.

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory tartományi szolgáltatások termékért felelős csoport a [visszajelzés fájlmegosztás vagy a támogatáshoz](active-directory-ds-contact-us.md).
