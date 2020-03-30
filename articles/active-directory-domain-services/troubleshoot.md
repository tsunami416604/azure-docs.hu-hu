---
title: Az Azure Active Directory tartományi szolgáltatásokkal kapcsolatos hibaelhárítás | Microsoft Dokumentumok"
description: Az Azure Active Directory tartományi szolgáltatások létrehozásakor és kezelésekor előforduló gyakori hibák elhárítása
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e17112cbe2a494a585cd5a09c36cfe449d3d433c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365815"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Gyakori hibák és hibaelhárítási lépések az Azure Active Directory tartományi szolgáltatásokhoz

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) az alkalmazások identitásának és hitelesítésének központi részeként néha problémákat okoznak. Ha problémákba ütközik, van néhány gyakori hibaüzenet és a kapcsolódó hibaelhárítási lépések, amelyek segítenek a dolgok újrafuttatásában. Bármikor [megnyithat egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségre.

Ez a cikk az Azure AD DS gyakori problémáinak hibaelhárítási lépéseit ismerteti.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Az Azure AD tartományi szolgáltatások nem engedélyezhetők az Azure AD-címtárhoz

Ha problémái vannak az Azure AD DS engedélyezésével, tekintse át az alábbi gyakori hibákat és lépéseket azok megoldásához:

| **Mintahibaüzenet** | **Resolution** (Osztás) |
| --- |:--- |
| *A aaddscontoso.com név már használatban van ezen a hálózaton. Adjon meg egy nevet, amely nincs használatban.* |[Tartománynév-ütközés a virtuális hálózatban](troubleshoot.md#domain-name-conflict) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az "Azure AD tartományi szolgáltatások szinkronizálása" nevű alkalmazáshoz. Törölje az "Azure AD tartományi szolgáltatások szinkronizálása" nevű alkalmazást, majd próbálja meg engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlő számára.* |[A tartományi szolgáltatások nem rendelkeznek megfelelő engedélyekkel az Azure AD tartományszolgáltatások szinkronizálási alkalmazásához](troubleshoot.md#inadequate-permissions) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőtartományi szolgáltatások alkalmazása nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje az alkalmazást a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítóval, majd próbálja meg engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlőszámára.* |[A Tartományi szolgáltatások alkalmazás nincs megfelelően konfigurálva az Azure AD-bérlőben](troubleshoot.md#invalid-configuration) |
| *A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Azure AD-bérlőben. Engedélyezze az alkalmazást a 00000002-0000-0000-c0000-0000000000000alkalmazás-azonosítóval, majd próbálja meg engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlőszámára.* |[A Microsoft Graph alkalmazás le van tiltva az Azure AD-bérlőben](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Tartománynév-ütközés

**Hibaüzenet**

*A aaddscontoso.com név már használatban van ezen a hálózaton. Adjon meg egy nevet, amely nincs használatban.*

**Resolution** (Osztás)

Ellenőrizze, hogy nincs-e meglévő AD DS-környezete ugyanazzal a tartománynévvel vagy társviszony-létesített virtuális hálózattal. Például előfordulhat, hogy egy *Aaddscontoso.com* nevű AD DS-tartománnyal rendelkezik, amely az Azure virtuális gépeken fut. Ha egy Azure AD DS felügyelt tartományt próbál engedélyezni a virtuális hálózaton *aaddscontoso.com* tartománynevével, a kért művelet sikertelen lesz.

Ez a hiba a virtuális hálózaton lévő tartománynév névütközései miatt történt. A DNS-keresése ellenőrzi, hogy egy meglévő AD DS-környezet válaszol-e a kért tartománynévre. A hiba megoldásához használjon másik nevet az Azure AD DS felügyelt tartományának beállításához, vagy a meglévő AD DS-tartomány kiépítésének kioldását, majd próbálkozzon újra az Azure AD DS engedélyezésével.

### <a name="inadequate-permissions"></a>Nem megfelelő engedélyek

**Hibaüzenet**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A szolgáltatás nem rendelkezik megfelelő engedélyekkel az "Azure AD tartományi szolgáltatások szinkronizálása" nevű alkalmazáshoz. Törölje az "Azure AD tartományi szolgáltatások szinkronizálása" nevű alkalmazást, majd próbálja meg engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlő számára.*

**Resolution** (Osztás)

Ellenőrizze, hogy *van-e egy Azure AD Domain Services Sync* nevű alkalmazás az Azure AD-címtárban. Ha ez az alkalmazás létezik, törölje, majd próbálja meg újra engedélyezni az Azure AD DS. Meglévő alkalmazás ellenőrzéséhez és szükség esetén törléséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon válassza az **Azure Active Directory** a bal oldali navigációs menüben.
1. Válassza **a Vállalati alkalmazások lehetőséget**. Válassza az **Alkalmazástípusa** legördülő menü *Minden alkalmazás* lehetőséget, majd válassza az **Alkalmaz parancsot.**
1. A keresőmezőbe írja be az *Azure AD Tartományszolgáltatások szinkronizálása című*kifejezést. Ha az alkalmazás létezik, jelölje ki, és válassza a **Törlés gombot.**
1. Miután törölte az alkalmazást, próbálja meg újra engedélyezni az Azure AD DS-t.

### <a name="invalid-configuration"></a>Érvénytelen konfiguráció

**Hibaüzenet**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. Az Azure AD-bérlőtartományi szolgáltatások alkalmazása nem rendelkezik a tartományi szolgáltatások engedélyezéséhez szükséges engedélyekkel. Törölje az alkalmazást a d87dcbc6-a371-462e-88e3-28ad15ec4e64 alkalmazásazonosítóval, majd próbálja meg engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlőszámára.*

**Resolution** (Osztás)

Ellenőrizze, hogy *van-e egy AzureActiveDirectoryDomainControllerServices* nevű alkalmazás, amelynek alkalmazásazonosítója *d87dcbc6-a371-462e-88e3-28ad15ec4e64* az Azure AD könyvtárban. Ha ez az alkalmazás létezik, törölje, majd próbálja meg újra engedélyezni az Azure AD DS.If this application exists, delete it and then try again to enable Azure AD DS.

A következő PowerShell-parancsfájl segítségével keressen meg egy meglévő alkalmazáspéldányt, és szükség esetén törölje azt:

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

### <a name="microsoft-graph-disabled"></a>A Microsoft Graph letiltva

**Hibaüzenet**

*A tartományi szolgáltatások nem engedélyezhetők ebben az Azure AD-bérlőben. A Microsoft Azure AD alkalmazás le van tiltva az Azure AD-bérlőben. Engedélyezze az alkalmazást a 00000002-0000-0000-c0000-0000000000000alkalmazás-azonosítóval, majd próbálja meg engedélyezni a tartományi szolgáltatásokat az Azure AD-bérlőszámára.*

**Resolution** (Osztás)

Ellenőrizze, hogy letiltott-e egy alkalmazást a *000000002-0000-0000-c000-00000000000000000azonosító azonosítóval.* Ez az alkalmazás a Microsoft Azure AD-alkalmazás, és graph API-hozzáférést biztosít az Azure AD-bérlőhöz. Az Azure AD-bérlő szinkronizálásához ezt az alkalmazást engedélyezni kell.

Az alkalmazás állapotának ellenőrzéséhez és szükség esetén engedélyezéséhez hajtsa végre az alábbi lépéseket:

1. Az Azure Portalon válassza az **Azure Active Directory** a bal oldali navigációs menüben.
1. Válassza **a Vállalati alkalmazások lehetőséget**. Válassza az **Alkalmazástípusa** legördülő menü *Minden alkalmazás* lehetőséget, majd válassza az **Alkalmaz parancsot.**
1. A keresőmezőbe írja be a *00000002-0000-0000-c000-000000000000000000000000000000000000000000000000000* Jelölje ki az alkalmazást, majd válassza **a Tulajdonságok lehetőséget.**
1. Ha **a felhasználók számára engedélyezve** van a bejelentkezéshez jelölőnégyzetek, állítsa az értéket *Igen*értékre, majd válassza a **Mentés lehetőséget.** *No*
1. Miután engedélyezte az alkalmazást, próbálja meg újra engedélyezni az Azure AD DS-t.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>A felhasználók nem tudnak bejelentkezni az Azure AD Domain Services által felügyelt tartományba

Ha az Azure AD-bérlő egy vagy több felhasználója nem tud bejelentkezni az Azure AD DS felügyelt tartományába, hajtsa végre az alábbi hibaelhárítási lépéseket:

* **Hitelesítő adatok formátuma** – Próbálja meg az UPN `dee@aaddscontoso.onmicrosoft.com`formátumot használni a hitelesítő adatok megadásához, például . Az upn formátum az ajánlott módja a hitelesítő adatok megadásának az Azure AD DS-ben. Győződjön meg arról, hogy ez az upn megfelelően van konfigurálva az Azure AD-ben.

    A fiók *SAMAccountName,* például *AADDSCONTOSO\driley* lehet automatikusan generált, ha több felhasználó azonos UPN előtag a bérlőben, vagy ha az UPN-előtag túl hosszú. Ezért a fiók *SAMAccountName* formátuma eltérhet attól, amit a helyszíni tartományban elvár vagy használ.

* **Jelszó-szinkronizálás** – Győződjön meg arról, hogy engedélyezte a jelszó-szinkronizálást [csak felhőbeli felhasználók][cloud-only-passwords] vagy [az Azure AD Connect használatával használt hibrid környezetek számára.][hybrid-phs]
    * **Hibrid szinkronizált fiókok:** Ha az érintett felhasználói fiókok egy helyszíni címtárból vannak szinkronizálva, ellenőrizze a következő területeket:
    
      * Telepítette vagy frissítette az [Azure AD Connect legújabb ajánlott kiadását.](https://www.microsoft.com/download/details.aspx?id=47594)
      * Az Azure AD Connectet úgy konfigurálta, hogy [teljes szinkronizálást hajtson végre.][hybrid-phs]
      * A címtár méretétől függően eltarthat egy ideig, amíg a felhasználói fiókok és a hitelesítő adatok kivéti elérhetők az Azure AD DS-ben. Győződjön meg arról, hogy elég sokáig vár, mielőtt megpróbálna hitelesíteni a felügyelt tartományt.
      * Ha a probléma az előző lépések ellenőrzése után is fennáll, próbálja meg újraindítani a *Microsoft Azure AD szinkronizálási szolgáltatást.* Az Azure AD Connect-kiszolgálóról nyisson meg egy parancssort, és futtassa a következő parancsokat:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Csak felhőalapú fiókok:** Ha az érintett felhasználói fiók csak felhőalapú felhasználói fiók, győződjön meg arról, hogy a felhasználó megváltoztatta a [jelszavát, miután engedélyezte az Azure AD DS szolgáltatást.][cloud-only-passwords] Ez a jelszó-visszaállítás okozza a szükséges hitelesítő adatok kimondott azure AD tartományi szolgáltatások létrehozásához.

* **A felhasználói fiók aktívállapotának ellenőrzése**: Alapértelmezés szerint a felügyelt tartományon lévő 2 percen belül öt érvénytelen jelszókísérlet miatt 30 percre zárolnak egy felhasználói fiókot. A felhasználó nem tud bejelentkezni, amíg a fiók zárolva van. 30 perc elteltével a felhasználói fiók zárolása automatikusan feloldódik.
  * Az Azure AD DS felügyelt tartományában érvénytelen jelszókísérletek nem zárják ki a felhasználói fiókot az Azure AD-ben. A felhasználói fiók csak a felügyelt tartományon belül van zárolva. Ellenőrizze a felhasználói fiók állapotát az *Active Directory felügyeleti konzolon (ADAC)* a [felügyeleti virtuális gép][management-vm]használatával, nem az Azure AD-ben.
  * [A részletes jelszóházirendek az][password-policy] alapértelmezett zárolási küszöbérték és időtartam módosításához is konfigurálhatók.

* **Külső fiókok** – Ellenőrizze, hogy az érintett felhasználói fiók nem egy külső fiók az Azure AD-bérlőben. Külső fiókok közé tartozik `dee@live.com` a Microsoft-fiókok, például vagy felhasználói fiókok egy külső Azure AD-címtárból. Az Azure AD DS nem tárolja a külső felhasználói fiókok hitelesítő adatait, így nem tudnak bejelentkezni a felügyelt tartományba.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Egy vagy több riasztás van a felügyelt tartományban

Ha az Azure AD DS felügyelt tartományában aktív riasztások vannak, előfordulhat, hogy a hitelesítési folyamat megfelelően működik.

Ha meg szeretné tudni, hogy vannak-e aktív riasztások, [ellenőrizze az Azure AD DS felügyelt tartomány ának állapotát.][check-health] Ha bármilyen riasztás jelenik meg, [hibaelhárításés és azok megoldása.][troubleshoot-alerts]

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Az Azure AD-bérlőjéből eltávolított felhasználók nem törlődnek a felügyelt tartományából

Az Azure AD védelmet nyújt a felhasználói objektumok véletlen törlése ellen. Amikor töröl egy felhasználói fiókot egy Azure AD-bérlőből, a megfelelő felhasználói objektum a lomtárba kerül. Ha ez a törlési művelet szinkronizálva van az Azure AD DS felügyelt tartományával, a megfelelő felhasználói fiók letiltva lesz megjelölve. Ez a funkció segít a felhasználói fiók helyreállításában vagy törlésének megvonásaként.

A felhasználói fiók továbbra is a letiltott állapotban az Azure AD DS felügyelt tartományban, akkor is, ha újra létrehoz egy felhasználói fiókot az azonos UPN az Azure AD címtárban. Ha el szeretné távolítani a felhasználói fiókot az Azure AD DS felügyelt tartományból, erőszakkal törölnie kell azt az Azure AD-bérlőből.

Ha teljes mértékben el szeretne távolítani egy felhasználói fiókot egy Azure AD DS felügyelt tartományból, törölje a felhasználót véglegesen az Azure AD-bérlőből az [Eltávolítás-MsolUser][Remove-MsolUser] PowerShell-parancsmag használatával a `-RemoveFromRecycleBin` paraméterrel.

## <a name="next-steps"></a>További lépések

Ha továbbra is problémákmerülnek fel, [nyisson meg egy Azure-támogatási kérelmet][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
