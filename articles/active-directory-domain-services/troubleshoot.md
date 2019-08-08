---
title: 'Azure Active Directory Domain Services: Hibaelhárítási útmutató | Microsoft Docs'
description: A Azure AD Domain Services hibaelhárítási útmutatója
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: acb001417b85b8ff45b2617e148e8b1961f3cbfa
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2019
ms.locfileid: "68772977"
---
# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Azure AD Domain Services – hibaelhárítási útmutató
Ez a cikk a Azure Active Directory (AD) tartományi szolgáltatások beállításakor vagy felügyeletekor felmerülő problémákkal kapcsolatos hibaelhárítási tippeket tartalmaz.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Az Azure AD-címtárhoz nem engedélyezhető Azure AD Domain Services
Ez a szakasz segítséget nyújt a hibák elhárításához, amikor megpróbálja engedélyezni a Azure AD Domain Services a címtárban.

Válassza ki a felmerülő hibaüzenetnek megfelelő hibaelhárítási lépéseket.

| **Hibaüzenet** | **Felbontás** |
| --- |:--- |
| *A contoso100.com név már használatban van ezen a hálózaton. Adjon meg olyan nevet, amely még nincs használatban.* |[Tartománynév-ütközés a virtuális hálózaton](troubleshoot.md#domain-name-conflict) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az „Azure AD Domain Services Sync” alkalmazáshoz. Törölje az „Azure AD Domain Services Sync” alkalmazást, és próbálja meg ezután engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlője számára.* |[A tartományi szolgáltatások nem rendelkezik megfelelő engedélyekkel a Azure AD Domain Services Sync alkalmazáshoz](troubleshoot.md#inadequate-permissions) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőjében található Domain Services alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.* |[A tartományi szolgáltatások alkalmazás nincs megfelelően konfigurálva a bérlőben](troubleshoot.md#invalid-configuration) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Ön Azure AD-bérlőjében. Engedélyezze a 00000002-0000-0000-c000-000000000000 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.* |[Az Microsoft Graph alkalmazás le van tiltva az Azure AD-bérlőben](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Tartomány neve ütközés
**Hibaüzenet:**

*A contoso100.com név már használatban van ezen a hálózaton. Adjon meg olyan nevet, amely még nincs használatban.*

**Szervizkiszolgáló**

Győződjön meg arról, hogy nem rendelkezik ugyanazzal a tartománynévvel rendelkező meglévő tartománnyal az adott virtuális hálózaton. Tegyük fel például, hogy a kiválasztott virtuális hálózatban már van egy contoso.com nevű tartomány. Később megpróbálhatja engedélyezni egy Azure AD Domain Services felügyelt tartományt ugyanazzal a tartománynévvel (azaz "contoso.com") az adott virtuális hálózaton. A Azure AD Domain Services engedélyezésére tett kísérlet során hiba lép fel.

Ezt a hibát az okozza, hogy a virtuális hálózatban a tartománynév ütközik a tartomány nevével. Ebben az esetben az Azure AD tartományi szolgáltatások által kezelt tartomány beállításához másik nevet kell használnia. A másik lehetséges megoldás az, hogy leépíti a meglévő tartományt, majd ezután folytatja a Azure AD tartományi szolgáltatások engedélyezését.

### <a name="inadequate-permissions"></a>Nem megfelelő engedélyek
**Hibaüzenet:**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az „Azure AD Domain Services Sync” alkalmazáshoz. Törölje az „Azure AD Domain Services Sync” alkalmazást, és próbálja meg ezután engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlője számára.*

**Szervizkiszolgáló**

Ellenőrizze, hogy van-e "Azure AD Domain Services Sync" nevű alkalmazás az Azure AD-címtárban. Ha az alkalmazás létezik, törölje, majd engedélyezze újra a Azure AD Domain Services.

A következő lépések végrehajtásával ellenőrizheti az alkalmazás jelenlétét, és törölheti azt, ha az alkalmazás létezik:

1. Navigáljon az Azure AD-címtár **alkalmazások** szakaszához a [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/).
2. Válassza ki az **összes alkalmazást** a **Megjelenítés** legördülő menüben. Válassza az **alkalmazások állapota** legördülő lista **bármelyik** elemét. Válassza a **bármelyik** lehetőséget az **alkalmazás láthatóságának** legördülő menüjében.
3. Írja be **Azure ad Domain Services szinkronizálás** kifejezést a keresőmezőbe. Ha az alkalmazás létezik, kattintson rá, és kattintson a **Törlés** gombra az eszköztáron a törléshez.
4. Miután törölte az alkalmazást, próbálja meg újból engedélyezni a Azure AD Domain Services.

### <a name="invalid-configuration"></a>Érvénytelen konfiguráció
**Hibaüzenet:**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőjében található Domain Services alkalmazás nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítójú alkalmazást, majd próbálja meg engedélyezni a Domain Servicest az Azure AD-bérlője számára.*

**Szervizkiszolgáló**

Ellenőrizze, hogy van-e olyan alkalmazás, amelynek a neve "AzureActiveDirectoryDomainControllerServices" (a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazás-azonosítójával) az Azure AD-címtárban. Ha az alkalmazás létezik, törölnie kell, majd újra engedélyeznie kell Azure AD Domain Services.

Az alábbi PowerShell-szkripttel keresse meg az alkalmazást, és törölje azt.

> [!NOTE]
> Ez a szkript az **Azure ad PowerShell 2-es verziójú** parancsmagokat használja. Az összes elérhető parancsmag teljes listáját és a modul letöltéséhez olvassa el a [AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx)-dokumentációját.
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

### <a name="microsoft-graph-disabled"></a>Microsoft Graph letiltva
**Hibaüzenet:**

Nem lehet engedélyezni a tartományi szolgáltatásokat ebben az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Ön Azure AD-bérlőjében. Engedélyezze az alkalmazást az 00000002-0000-0000-C000-000000000000 alkalmazás-azonosítóval, majd próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.

**Szervizkiszolgáló**

Ellenőrizze, hogy letiltotta-e az 00000002-0000-0000-C000-000000000000 azonosítójú alkalmazást. Ez az alkalmazás a Microsoft Azure AD alkalmazás, és Graph API hozzáférést biztosít az Azure AD-bérlőhöz. Azure AD Domain Services szükség van erre az alkalmazásra ahhoz, hogy szinkronizálja az Azure AD-bérlőt a felügyelt tartományával.

A hiba megoldásához engedélyezze ezt az alkalmazást, és próbálja meg engedélyezni az Azure AD-bérlő tartományi szolgáltatásait.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>A felhasználók nem tudnak bejelentkezni az Azure AD Domain Services által felügyelt tartományba
Ha az Azure AD-bérlő egy vagy több felhasználója nem tud bejelentkezni az újonnan létrehozott felügyelt tartományba, hajtsa végre a következő hibaelhárítási lépéseket:

* **Bejelentkezés UPN formátum használatával:** A bejelentkezéshez próbálja az egyszerű felhasználónév (UPN) formátumot használni (például „joeuser@contoso.com”) a SAMAccountName formátum helyett („CONTOSO\joeuser”). Előfordulhat, hogy a SAMAccountName automatikusan jön létre azon felhasználók számára, akiknek UPN-előtagja túl hosszú, vagy a felügyelt tartomány egy másik felhasználója. Az UPN formátuma garantáltan egyedi az Azure AD-bérlőn belül.

> [!NOTE]
> Javasoljuk, hogy az UPN formátumot használja a Azure AD Domain Services felügyelt tartományba való bejelentkezéshez.
>
>

* Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást](active-directory-ds-getting-started-password-sync.md) az első lépéseket ismertető útmutató lépéseinek megfelelően.
* **Külső fiókok:** Ügyeljen arra, hogy az érintett felhasználói fiók ne külső fiók legyen az Azure AD-bérlőben. Külső fiókok például a Microsoft-fiókok (például "joe@live.com") vagy a külső Azure ad-címtárból származó felhasználói fiókok. Mivel Azure AD Domain Services nem rendelkezik hitelesítő adatokkal az ilyen felhasználói fiókokhoz, ezek a felhasználók nem jelentkezhetnek be a felügyelt tartományba.
* **Szinkronizált fiókok:** Ha az érintett felhasználói fiókok egy helyszíni címtárból vannak szinkronizálva, ellenőrizze a következőket:

  * Telepítette vagy frissítette a [Azure ad Connect legújabb javasolt kiadását](https://www.microsoft.com/download/details.aspx?id=47594).
  * Úgy konfigurálta a Azure AD Connect, hogy [teljes szinkronizálást végezzen](active-directory-ds-getting-started-password-sync.md).
  * A címtár méretétől függően eltarthat egy ideig, amíg a felhasználói fiókok és a hitelesítő adatok kivonatai elérhetők lesznek Azure AD Domain Servicesban. Győződjön meg arról, hogy elég sokáig várnia kell a hitelesítés újrapróbálkozása előtt.
  * Ha a probléma az előző lépések ellenőrzése után sem szűnik meg, próbálja meg újraindítani a Microsoft Azure AD Sync szolgáltatást. A Szinkronizáló gépről indítson el egy parancssort, és hajtsa végre a következő parancsokat:

    1. net stop "Microsoft Azure AD Sync"
    2. net start "Microsoft Azure AD Sync"
* **Csak felhőalapú fiókok**: Ha az érintett felhasználói fiók csak felhőalapú felhasználói fiók, győződjön meg arról, hogy a felhasználó a Azure AD Domain Services engedélyezése után megváltoztatta a jelszavát. Ezzel a lépéssel létrejönnek az Azure AD tartományi szolgáltatásokhoz szükséges hitelesítő adatok kivonatai.
* **Ellenőrizze, hogy a felhasználói fiók aktív-e**: Ha a felhasználó fiókja zárolva van, nem tud bejelentkezni, amíg a fiókja újra nem aktív. A felügyelt tartomány 2 percen belül öt érvénytelen jelszóval próbálkozik, mert a felhasználói fiók 30 percig kizárja a felhasználót. 30 perc elteltével a felhasználói fiók automatikusan fel lesz oldva.
  * A felügyelt tartomány jelszavas próbálkozásai érvénytelenek, az Azure AD-ben nem zárhatók ki a felhasználói fiókok. A felhasználói fiók csak a Azure AD Domain Services felügyelt tartományon belül van zárolva. A felhasználói fiók állapotát a Active Directory felügyeleti konzol (ADAC) használatával, az Azure AD DS felügyelt tartományra vonatkozóan, az Azure AD-ben nem.
  * Emellett [olyan részletes jelszóházirendek is konfigurálhatók, amelyek megváltoztatják az alapértelmezett zárolási küszöbértéket és az időtartamot](https://docs.microsoft.com/azure/active-directory-domain-services/password-policy).

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Egy vagy több riasztás van a felügyelt tartományon

Tekintse meg a riasztások feloldása a felügyelt tartományon című cikket a [riasztások hibaelhárítása](troubleshoot-alerts.md) című cikkben.

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Az Azure AD-bérlőből eltávolított felhasználókat a rendszer nem távolítja el a felügyelt tartományból
Az Azure AD megvédi Önt a felhasználói objektumok véletlen törlésétől. Amikor töröl egy felhasználói fiókot az Azure AD-bérlőből, a megfelelő felhasználói objektum átkerül a Lomtárba. Ha ezt a törlési műveletet szinkronizálja a felügyelt tartományával, a rendszer letiltja a megfelelő felhasználói fiók jelölését. Ezzel a funkcióval később is helyreállíthatja vagy törölheti a felhasználói fiókot.

A felhasználói fiók a felügyelt tartomány letiltott állapotában marad, még akkor is, ha újból létrehoz egy felhasználói fiókot ugyanazzal az egyszerű felhasználónévvel az Azure AD-címtárban. A felhasználói fióknak a felügyelt tartományból való eltávolításához kényszerített törlést kell végeznie az Azure AD-bérlőből.

A felhasználói fióknak a felügyelt tartományból való teljes eltávolításához törölje a felhasználót véglegesen az Azure AD-bérlőből. Használja a `-RemoveFromRecycleBin` PowerShell-parancsmagot a kapcsolóval az alábbi MSDN-cikkben leírtak szerint. [](/previous-versions/azure/dn194132(v=azure.100)) `Remove-MsolUser`


## <a name="contact-us"></a>Kapcsolat
A [visszajelzések megosztásához és](contact-us.md)a támogatáshoz forduljon a Azure Active Directory Domain Services termék csapatához.
