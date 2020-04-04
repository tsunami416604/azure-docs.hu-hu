---
title: Jelszó nélküli biztonsági kulcs bejelentkezés a helyszíni erőforrásokba (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogy miként engedélyezheti a jelszó nélküli biztonsági kulcs bejelentkezését a helyszíni erőforrásokba az Azure Active Directory használatával (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 181e8192170cd7394d6817edd655f4e8257b48a4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654038"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Jelszó nélküli biztonsági kulcs bejelentkezés engedélyezése a helyszíni erőforrásokba az Azure Active Directoryval (előzetes verzió)

Ez a dokumentum arra összpontosít, hogy jelszó nélküli hitelesítést engedélyezaa helyszíni erőforrások környezetekben, mind **az Azure AD csatlakozott,** mind **a hibrid Azure AD csatlakozott** a Windows 10-eszközök. Ez a funkció zökkenőmentes egyszeri bejelentkezést (SSO) biztosít a helyszíni erőforrások számára a Microsoft-kompatibilis biztonsági kulcsok használatával.

|     |
| --- |
| A FIDO2 biztonsági kulcsok az Azure Active Directory nyilvános előzetes verziójú szolgáltatásai. Az előzetes verziókról további információt a [Kiegészítő használati feltételek a Microsoft Azure előzetes verzióihoz című](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) témakörben talál.|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Egyszeri erőforrás a helyszíni erőforrásokhoz FIDO2 kulcsok használatával

Az Azure Active Directory (AD) kerberos jegymegadási jegyeket (TGT) adhat ki egy vagy több Active Directory-tartományszámára. Ez a funkció lehetővé teszi a felhasználók számára, hogy modern hitelesítő adatokkal, például FIDO2 biztonsági kulcsokkal jelentkezzenek be a Windows rendszerbe, és hozzáférjenek a hagyományos Active Directory-alapú erőforrásokhoz. A Kerberos szolgáltatásjegyeket és az engedélyezést továbbra is a helyszíni Active Directory tartományvezérlők szabályozzák.

Egy Azure AD Kerberos Server objektum jön létre a helyszíni Active Directoryban, majd biztonságosan közzé az Azure Active Directoryban. Az objektum nincs fizikai kiszolgálókhoz társítva. Ez egyszerűen egy olyan erőforrás, amelyet az Azure Active Directory kerberos TGT-k létrehozásához használhat az Active Directory tartományi szolgáltatásokhoz.

![Jegymegadási jegy (TGT) beszerzése az Azure AD-től és az AD DS-ből](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. A felhasználó fido2 biztonsági kulccsal jelentkezik be Windows 10-es eszközükre, és hitelesíti magát az Azure AD-ben.
1. Az Azure AD ellenőrzi a címtárban a felhasználó helyszíni AD-tartományának megfelelő Kerberos-kiszolgálókulcs átnézését.
   1. Az Azure AD kerberos TGT-t hoz létre a felhasználó helyszíni AD-tartományában. A TGT csak a felhasználó SID-jét tartalmazza. A TGT nem tartalmaz engedélyezési adatokat.
1. A TGT az Azure AD elsődleges frissítési jogkivonatával (PRT) együtt kerül vissza az ügyfélhez.
1. Az ügyfélgép kapcsolatba lép egy helyszíni AD-tartományvezérlővel, és elcseréli a részleges TGT-t egy teljesen formázott TGT-re.
1. Az ügyfélgép most már rendelkezik egy Azure AD PRT-vel és egy teljes Active Directory TGT-vel, és hozzáférhet a felhőbeli és a helyszíni erőforrásokhoz.

## <a name="requirements"></a>Követelmények

A szervezeteknek a cikkben ismertetett lépések végrehajtása előtt végre kell hajtozniuk a Jelszó nélküli biztonsági kulcs jel engedélyezésének lépéseit a [Windows 10-es eszközökre (előzetes verzió).](howto-authentication-passwordless-security-key.md)

A szervezeteknek az alábbi szoftverkövetelményeknek is meg kell felelniük.

- Az eszközöknek Windows 10 Insider Build 18945 vagy újabb rendszert kell futtatniuk.
- Az [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)1.4.32.0-s vagy újabb verziójával kell rendelkeznie.
  - A rendelkezésre álló Azure AD hibrid hitelesítési beállításokról az [Azure Active Directory hibrid identitáskezelési megoldásmegfelelő hitelesítési módszerének kiválasztása](../../security/fundamentals/choose-ad-authn.md) és az Azure AD Connect hez használni kívánt telepítési típus kiválasztása című [témakörben](../hybrid/how-to-connect-install-select-installation.md)talál további információt.
- A Windows Server tartományvezérlőkön telepítve kell lennie a következő javításoknak:
    - Windows Server 2016 esetén –https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Windows Server 2019 esetén –https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Támogatott esetek

A forgatókönyv támogatja az egyszeri bejelentkezést (SSO) mindkét alábbi esetben:

- Felhőalapú erőforrások, például az Office 365 és más SAML-kompatibilis alkalmazások esetén.
- Helyszíni erőforrások és Windows-integrált hitelesítés webhelyeken. Az erőforrások tartalmazhatnak olyan webhelyeket és SharePoint-webhelyeket, amelyek IIS-hitelesítést igényelnek, valamint / vagy ntlm-hitelesítést használó erőforrásokat.

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő esetekben nem támogatottak:

- A Windows Server Active Directory tartományi szolgáltatások (AD DS) tartomány a központi telepítéshez csatlakozott (csak helyszíni eszközökön).
- RDP, VDI és Citrix forgatókönyvek biztonsági kulccsal.
- Biztonsági kulccsal.
- "Futtatás másként" egy biztonsági kulccsal.
- Bejelentkezés a kiszolgálóra biztonsági kulccsal.

## <a name="create-kerberos-server-object"></a>Kerberos-kiszolgálóobjektum létrehozása

A rendszergazdák az Azure AD Connect-kiszolgálóN i PowerShell-eszközök használatával hoznak létre egy Azure AD Kerberos Server-objektumot a helyszíni címtárban. Futtassa a következő lépéseket a szervezet minden olyan tartományában és erdőjében, amely Az Azure AD-felhasználókat tartalmazza:

1. Frissítsen az Azure AD Connect legújabb verziójára. Az utasítások feltételezik, hogy már konfigurálta az Azure AD Connectet a hibrid környezet támogatására.
1. Az Azure AD Connect Server ben nyisson meg egy emelt szintű PowerShell-kérdést, és keresse meg a`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Futtassa a következő PowerShell-parancsokat egy új Azure AD Kerberos-kiszolgálóobjektum létrehozásához a helyszíni Active Directory-tartományban és az Azure Active Directory-bérlőben.

> [!NOTE]
> Cserélje `contoso.corp.com` le a következő példában a helyszíni Active Directory tartománynevét.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Az Azure AD Kerberos-kiszolgáló megtekintése és ellenőrzése

Az újonnan létrehozott Azure AD Kerberos-kiszolgálót a következő paranccsal tekintheti meg és ellenőrizheti:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Ez a parancs az Azure AD Kerberos-kiszolgáló tulajdonságait adja ki. A tulajdonságok áttekintésével ellenőrizheti, hogy minden rendben van-e.

| Tulajdonság | Leírás |
| --- | --- |
| ID (Azonosító) | Az AD DS DC-objektum egyedi azonosítója. Ezt az azonosítót néha "bővítőhelynek" vagy "fiókazonosítónak" is nevezik. |
| DomainDnsName | Az Active Directory tartomány DNS-tartományneve. |
| Számítógépfiók | Az Azure AD Kerberos Server objektum (a tartományvezérlő) számítógépfiók-objektuma. |
| Felhasználói fiók | A letiltott felhasználói fiók objektum, amely rendelkezik az Azure AD Kerberos Server TGT titkosítási kulcs. A számla DN-je`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Kulcsverzió | Az Azure AD Kerberos Server TGT titkosítási kulcs kulcsverziója. A verzió a kulcs létrehozásakor lesz hozzárendelve. A kulcs minden elforgatásakor a verzió minden egyes lépésnél növekszik. A növekmények replikációs metaadatokon alapulnak, és valószínűleg nagyobbak, mint egy. Például, a kezdeti *KeyVersion* lehet *192272*. Az első alkalommal, amikor a kulcs elfordul, a verzió előre *212621*. A fontos dolog, hogy ellenőrizze, hogy a *KeyVersion* a helyszíni objektum és a *CloudKeyVersion* a felhőobjektum ugyanaz. |
| Kulcsfrissítés | Az Azure AD Kerberos Server TGT titkosítási kulcsának frissítése vagy létrehozása dátuma és időpontja. |
| KeyUpdatedFrom | A tartományvezérlő, ahol az Azure AD Kerberos Server TGT titkosítási kulcs utoljára frissült. |
| Felhőazonosító | Az Azure AD-objektum azonosítója. Meg kell egyeznie a fenti azonosítóval. |
| CloudDomainDnsName | A *DomainDnsName* az Azure AD-objektumból. Meg kell egyeznie a fenti *DomainDnsName.Must* match the DomainDnsName above. |
| CloudKeyVersion (Felhőkulcs-verzió) | A *KeyVersion* az Azure AD-objektumból. Meg kell egyeznie a fenti *KeyVersion.Must* Match the KeyVersion above. |
| CloudKeyUpdatedon között | A *KeyUpdatedOn* az Azure AD-objektumból. Meg kell egyeznie a *fenti KeyUpdatedOn.Must* match the KeyUpdatedOn above. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Az Azure AD Kerberos Server kulcs ának elforgatása

Az Azure AD Kerberos Server titkosítási krbtgt kulcsokat kell forgatni rendszeresen. Javasoljuk, hogy kövesse az összes többi Active Directory tartományvezérlő krbtgt kulcs elforgatásához használt ütemezést.

> [!WARNING]
> Vannak más eszközök, amelyek elforgatják a krbtgt kulcsokat, azonban az ebben a dokumentumban említett eszközökkel kell forgatni az Azure AD Kerberos Server krbtgt kulcsokat. Ez biztosítja, hogy a kulcsok a helyszíni AD és az Azure AD frissítése.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Az Azure AD Kerberos-kiszolgáló eltávolítása

Ha vissza szeretné állítani a forgatókönyvet, és el szeretné távolítani az Azure AD Kerberos-kiszolgálót a helyszíni Active Directoryból és az Azure Active Directoryból, futtassa a következő parancsot:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Többerdős és többtartományos forgatókönyvek

Az Azure AD Kerberos-kiszolgáló objektum *kerberosdomain* objektumként jelenik meg az Azure AD-ben. Minden helyszíni Active Directory-tartomány egyetlen *KerberosDomain* objektumként jelenik meg az Azure AD-ben.

A szervezet például két tartománnyal rendelkezik `contoso.com` `fabrikam.com`egy Active Directory erdővel, és . Ha úgy dönt, hogy engedélyezi az Azure AD kerberos TGT-k kiadását a teljes erdőre vonatkozóan, az Azure AD-ben két *KerberosDomain-objektum* található. Egy *KerberosDomain* `contoso.com`objektum a `fabrikam.com`és egy a számára. Ha több Active Directory-erdővel rendelkezik, minden erdő minden tartományához tartozik egy *KerberosDomain* objektum.

A [Kerberos-kiszolgálóobjektum létrehozásához](#create-kerberos-server-object) szükséges lépéseket a szervezet minden olyan tartományában és erdőjében, amely Az Azure AD-felhasználókat tartalmaz.

## <a name="known-behavior"></a>Ismert viselkedés

A FIDO-val való bejelentkezés le van tiltva, ha a jelszó lejárt. A elvárás van részére felhasználó -hoz orrgazdaság -uk jelszó előtt lét képes -hoz fatörzs -ban használ FIDO.

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha a szolgáltatás megtekintése közben szeretné megosztani a visszajelzéseket, vagy problémákba ütközik, az alábbi lépésekkel megoszthatja a Windows Visszajelzési központ alkalmazáson keresztül:

1. Indítsa el **a Visszajelzési központot,** és győződjön meg arról, hogy be van jelentkezve.
1. Visszajelzés küldése a következő kategorizálás alatt:
   - Kategória: Biztonság és adatvédelem
   - Alkategória: FIDO
1. A naplók rögzítéséhez használja a **probléma újbóli létrehozását**

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-this-work-in-my-on-premises-environment"></a>Működik ez a helyszíni környezetben?

Ez a szolgáltatás nem működik tiszta helyszíni Active Directory tartományi szolgáltatások (AD DS) környezetben.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A szervezetnek kétfaktoros hitelesítésre van szüksége az erőforrások eléréséhez. Mit tehetek, hogy támogassa ezt a követelményt?

A biztonsági kulcsok számos formatényezőből származnak. Lépjen kapcsolatba az eszköz gyártójával, és beszélhesse meg, hogyan engedélyezhetők az eszközeik pin-kóddal vagy biometrikus adatokkal.

### <a name="can-admins-set-up-security-keys"></a>A rendszergazdák beállíthatnak biztonsági kulcsokat?

Dolgozunk ezen a képességen az általános elérhetőség (GA) a funkció.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Hol találhatok megfelelő biztonsági kulcsokat?

[FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Mit tegyek, ha elveszítem a biztonsági kulcsot?

A kulcsokat eltávolíthatja az Azure Portalról, ha a **Biztonsági adatok** lapra navigál, és eltávolítja a biztonsági kulcsot.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nem tudom használni a FIDO-t közvetlenül azután, hogy létrehoztam egy hibrid Azure AD-illesztésű gépet

Ha tiszta telepítése hibrid Azure AD-hez csatlakozott gép, miután a tartomány hoz és újraindítási folyamat be kell jelentkeznie egy jelszót, és várja meg a szabályzat szinkronizálását, mielőtt a FIDO-t használni a bejelentkezéshez.

- Ellenőrizze az aktuális állapotát `dsregcmd /status` a parancsablakbe való beírással, és ellenőrizze, hogy mind az *AzureAdJoined,* mind *a DomainJoined* igen t mutat-e. *YES*
- Ez a késleltetés a tartományhoz csatlakozó eszközök ismert korlátozása, és nem FIDO-specifikus.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Nem tudok SSO-t az NTLM hálózati erőforráshoz, miután bejelentkeztem a FIDO-val, és hitelesítő adatokat kaptam

Győződjön meg arról, hogy elegendő tartományvezérlő van javítva ahhoz, hogy időben válaszoljon az erőforrás-kérelem kiszolgálásához. Annak ellenőrzéséhez, hogy a szolgáltatást futtató tartományvezérlő látható-e, tekintse át a kimenetét. `nltest /dsgetdc:contoso /keylist /kdc`

## <a name="next-steps"></a>További lépések

[További információ a jelszó nélküli nyelvről](concept-authentication-passwordless.md)
