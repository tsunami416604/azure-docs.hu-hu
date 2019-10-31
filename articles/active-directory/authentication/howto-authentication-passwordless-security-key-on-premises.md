---
title: Jelszó nélküli biztonsági kulcs bejelentkezés a helyszíni erőforrásokra (előzetes verzió) – Azure Active Directory
description: Jelszó nélküli biztonsági kulcs engedélyezése a helyszíni erőforrásokhoz (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164860"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>Jelszó nélküli biztonsági kulcs engedélyezése a helyszíni erőforrásokhoz (előzetes verzió)

Ez a dokumentum az **Azure ad** -hez csatlakoztatott és a **hibrid Azure ad-hez csatlakoztatott** Windows 10 rendszerű eszközökön a helyi erőforrásokhoz való jelszavas hitelesítés engedélyezését összpontosítja. Ez a funkció zökkenőmentes egyszeri bejelentkezést (SSO) biztosít a helyszíni erőforrásokhoz a Microsoft-kompatibilis biztonsági kulcsok használatával.

|     |
| --- |
| A FIDO2 biztonsági kulcsai a Azure Active Directory nyilvános előzetes verziója. További információ az előzetes verziókról: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Egyszeri bejelentkezés a helyszíni erőforrásokhoz FIDO2-kulcsok használatával

Azure Active Directory (AD) kiállíthat egy vagy több Active Directory-tartomány Kerberos-jegyét megadó jegyet (TGT). Ez a funkció lehetővé teszi, hogy a felhasználók modern hitelesítő adatokkal jelentkezzenek be a Windowsba, mint például a FIDO2 biztonsági kulcsai és a hagyományos Active Directory-alapú erőforrások A Kerberos szolgáltatási jegyeit és az engedélyezést továbbra is a helyszíni Active Directory tartományvezérlők ellenőrzik.

Az Azure AD Kerberos-kiszolgáló objektum a helyszíni Active Directory lesz létrehozva, majd biztonságosan közzé kell tenni a Azure Active Directory. Az objektum nincs társítva fizikai kiszolgálókhoz. Ez egyszerűen egy olyan erőforrás, amelyet a Azure Active Directory használhat arra, hogy a Active Directory-tartomány számára Kerberos-jegyeket biztosítson a jegyek (TGT) létrehozásához.

![TGT és PRT beszerzése az Azure AD-ből és AD DS](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. A felhasználó egy FIDO2 biztonsági kulccsal jelentkezik be a Windows 10-es eszközre, és hitelesíti magát az Azure AD-ben.
1. Az Azure AD ellenőrzi a felhasználó helyszíni AD-tartományának megfelelő Kerberos-kiszolgálói kulcs címtárát.
   1. Az Azure AD egy Kerberos-TGT hoz létre a felhasználó helyszíni AD-tartományához. A TGT csak a felhasználó SID-azonosítóját tartalmazza. A TGT nem tartalmaz engedélyezési adatértéket.
1. A TGT az Azure AD elsődleges frissítési jogkivonatával (PRT) együtt visszaadja az ügyfélnek.
1. Az ügyfélszámítógép kapcsolatba lép egy helyszíni AD-tartományvezérlővel, és a részleges TGT kereskedik egy teljesen formázott TGT.
1. Az ügyfélgépen már van egy Azure AD-PRT és egy teljes Active Directory TGT, és a Felhőbeli és a helyszíni erőforrások elérésére is képes.

## <a name="requirements"></a>Követelmények

A szervezeteknek el kell végezniük a [jelszó nélküli biztonsági kulcs aláírásának engedélyezése Windows 10-es eszközökön (előzetes verzió)](howto-authentication-passwordless-security-key.md) című cikkben ismertetett lépéseket a jelen cikk lépéseinek elvégzése előtt.

A szervezeteknek meg kell felelniük a következő szoftver-követelményeknek.

- Az eszközöknek a Windows 10 belső Build 18945-es vagy újabb verzióját kell futtatniuk
- Frissítés a [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect) legújabb verziójára
- A Windows Server 2016/2019 rendszerű tartományvezérlők teljes mértékben kijavították a helyszíni hitelesítési kérések terhelését.

### <a name="supported-scenarios"></a>Támogatott esetek

- A forgatókönyv egyszerre támogatja az egyszeri bejelentkezést (SSO):
   - Felhőbeli erőforrások, például Office 365 és más SAML-kompatibilis alkalmazások.
   - Helyszíni erőforrások és Windows-integrált hitelesítés webhelyekhez, beleértve az IIS-hitelesítést igénylő webhelyeket és SharePoint-webhelyeket, illetve az NTLM-hitelesítést használó erőforrásokat.

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek nem támogatottak:

- A Windows Server Active Directory tartományi szolgáltatások (AD DS) tartományhoz csatlakoztatott (csak helyszíni eszközök) központi telepítése **nem támogatott**.
- Az RDP-, VDI-és Citrix-forgatókönyvek **nem támogatottak** a biztonsági kulcs használatával.
- Az S/MIME **nem támogatott** a biztonsági kulcs használatával.
- A "Run as" **nem támogatott** a biztonsági kulcs használatával.
- A biztonsági kulccsal való bejelentkezés **nem támogatott**a kiszolgálóra.

## <a name="create-kerberos-server-object"></a>Kerberos-kiszolgáló objektum létrehozása

A rendszergazdák PowerShell-eszközöket fognak használni a Azure AD Connect-kiszolgálóról egy Azure AD Kerberos-kiszolgáló objektum létrehozásához a helyszíni címtárban. Ezeket a lépéseket a szervezet minden olyan tartományában és erdője számára futtatnia kell, amely tartalmazza az Azure AD-felhasználókat.

1. Frissítsen a Azure AD Connect legújabb verziójára. Az utasítások feltételezik, hogy már konfigurálta Azure AD Connect a hibrid környezet támogatásához.
1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort a Azure AD Connect-kiszolgálón, és navigáljon a C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\
1. A következő PowerShell-parancsok futtatásával hozzon létre egy új Azure AD Kerberos-kiszolgálói objektumot a helyszíni Active Directory tartományban és Azure Active Directory bérlőben.

> [!NOTE]
> Cserélje le a "contoso.corp.com" kifejezést a következő példában a helyszíni Active Directory tartománynevére.

```PowerShell
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

Az újonnan létrehozott Azure AD Kerberos-kiszolgálót az alábbi parancs használatával tekintheti meg és ellenőrizheti. 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Ez a parancs kiírja az Azure AD Kerberos-kiszolgáló tulajdonságait. A tulajdonságok áttekintésével ellenőrizheti, hogy minden jó sorrendben van-e.

| Tulajdonság | Leírás |
| --- | --- |
| ID (Azonosító) | Az AD-tartományvezérlő objektum egyedi azonosítója. Ezt az azonosítót más néven "slot"-nak vagy "ág-AZONOSÍTÓnak" is nevezzük. |
| DomainDnsName | A Active Directory-tartomány DNS-tartományneve. |
| ComputerAccount | Az Azure AD Kerberos-kiszolgáló objektum számítógépfiók-objektuma (a tartományvezérlő). |
| Felhasználóifiók | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsát birtokló letiltott felhasználói fiók objektum. A fiók megkülönböztető neve a következő lesz: <br> CN = krbtgt_AzureAD, CN = felhasználók, < tartomány – DN > |
| Verziószám | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsának verziószáma. A verzió a kulcs létrehozásakor lesz hozzárendelve. Ekkor a rendszer a kulcs elforgatásakor minden alkalommal megnöveli a verziót. A növekmények a replikálási metaadatokon alapulnak, és valószínűleg nagyobbak lesznek, mint egy. A kezdeti verzió például 192272 lehet. A kulcs első elforgatásakor a verzió a 212621-as értékre léphet. A legfontosabb, hogy ellenőrizze, hogy a helyszíni objektum és a CloudKeyVersion a Felhőbeli objektumhoz tartozó verziószáma azonos-e. |
| KeyUpdatedOn | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsának és létrehozásának dátuma és időpontja. |
| KeyUpdatedFrom | Az a tartományvezérlő, ahol az Azure AD Kerberos-kiszolgáló TGT-titkosítási kulcsa utoljára frissült. |
| CloudId | Az Azure AD-objektumból származó azonosító. Meg kell egyeznie a fenti AZONOSÍTÓval. |
| CloudDomainDnsName | Az Azure AD-objektum DomainDnsName. Meg kell egyeznie a fenti DomainDnsName. |
| CloudKeyVersion | Az Azure AD-objektum verziószáma. A fenti verziónak meg kell egyeznie. |
| CloudKeyUpdatedOn | Az Azure AD-objektum KeyUpdatedOn. Meg kell egyeznie a fenti KeyUpdatedOn. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Az Azure AD Kerberos-kiszolgáló kulcsának elforgatása

Az Azure AD Kerberos-kiszolgáló titkosítási krbtgt kulcsait rendszeresen el kell forgatni. Javasoljuk, hogy ugyanazt az ütemtervet kövesse, amelyet az összes többi Active Directory-tartomány vezérlő krbtgt kulcsának elforgatásához használ.

> [!WARNING]
> Vannak olyan eszközök, amelyek elforgatják a krbtgt kulcsokat, azonban a dokumentumban említett eszközöket kell használniuk az Azure AD Kerberos-kiszolgáló krbtgt kulcsainak elforgatásához. Ez biztosítja, hogy a kulcsok naprakészek legyenek a helyszíni AD-ben és az Azure AD-ben is.

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Az Azure AD Kerberos-kiszolgáló eltávolítása

Ha szeretné visszaállítani a forgatókönyvet, és távolítsa el az Azure AD Kerberos-kiszolgálót mind a helyszíni Active Directory, mind Azure Active Directory, futtassa a következő parancsot.  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Több erdőből és több tartományból álló forgatókönyvek

Az Azure AD Kerberos-kiszolgáló objektum az Azure AD-ben KerberosDomain objektumként jelenik meg. Az egyes helyszíni Active Directory tartományok egyetlen KerberosDomain-objektumként jelennek meg az Azure AD-ben.

Például a szervezet rendelkezik egy Active Directory erdővel, amely két tartománnyal rendelkezik, a contoso.com és a fabrikam.com. Ha úgy dönt, hogy engedélyezi az Azure AD számára, hogy a teljes erdőre vonatkozó Kerberos-jegyeket adjanak ki (TGT), akkor az Azure AD-ben két KerberosDomain-objektum lesz. Egy KerberosDomain objektum a contoso.com és egy fabrikam.com-hez. Ha több Active Directory erdővel rendelkezik, az egyes erdők mindegyik tartományához egy KerberosDomain-objektum fog rendelkezni.

Futtatnia kell a [Kerberos-kiszolgáló objektum létrehozása](#create-kerberos-server-object) című témakör lépéseit a szervezet minden olyan tartományában és erdőben, amely tartalmazza az Azure ad-felhasználókat.

## <a name="known-behavior"></a>Ismert viselkedés

Ha a jelszó lejárt, a rendszer letiltja a t. A várt érték a felhasználó számára a jelszó alaphelyzetbe állítása, mielőtt be tudná jelentkezni a következő használatával:.

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha meg szeretné osztani a visszajelzéseket, vagy problémákba ütközik a funkció megtekintése közben, ossza meg a Windows visszajelzési központ alkalmazáson keresztül.

1. Indítsa el a **visszajelzési** központot, és ellenőrizze, hogy be van-e jelentkezve.
1. Küldjön visszajelzést a következő kategorizálás alá:
   1. Kategória: biztonság és adatvédelem
   1. Alkategória:
1. A naplók rögzítéséhez használja a következő lehetőséget: a **probléma újbóli létrehozása**

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-this-work-in-my-on-premises-environment"></a>Működik ez a helyszíni környezetben?

Ez a funkció nem működik tiszta helyszíni Active Directory tartományi szolgáltatások (AD DS) környezetben.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A szervezetem két faktoros hitelesítést igényel az erőforrások eléréséhez, Mire használhatom ezt a követelményt?

A biztonsági kulcsok különféle formában jelennek meg. Vegye fel a kapcsolatot az eszköz gyártójával, és beszéljen arról, hogy az eszközük hogyan engedélyezhető PIN-kód vagy biometrikus azonosító használatával második tényezőként.

### <a name="can-admins-set-up-security-keys"></a>A rendszergazdák beállítják a biztonsági kulcsokat?

Ezen funkció általánosan elérhetővé vált ezen a lehetőségen.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Hol találhatom meg a megfelelő biztonsági kulcsokat?

[FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Mi a teendő, ha elveszítem a biztonsági kulcsot?

A kulcsokat a Azure Portalból távolíthatja el, ha a biztonsági adatok lapra navigál, és eltávolítja a biztonsági kulcsot.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nem tudom azonnal használni a következőt a hibrid Azure AD-hez csatlakoztatott gép létrehozása után

Ha a hibrid Azure AD-hez csatlakoztatott gép tiszta telepítését végzi, a tartományhoz való csatlakozás és az újraindítás után be kell jelentkeznie egy jelszóval, és várnia kell a házirend szinkronizálására, mielőtt a rendszer használni tudná a bejelentkezést.

- Az aktuális állapot ellenőrzéséhez írja be `dsregcmd /status` egy parancssorablakba, és győződjön meg arról, hogy mindkét `AzureAdJoined` és `DomainJoined` `YES`látható.
- Ez a késés a tartományhoz csatlakoztatott eszközök ismert korlátozása, és nem adott meg.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Nem tudom beolvasni az SSO-t az NTLM hálózati erőforráshoz a parancssori felülettel való bejelentkezés után, és hitelesítő adatok kérése

Győződjön meg arról, hogy elegendő tartományvezérlő van, hogy az erőforrás-kérelem kiszolgálásához időben válaszoljon. Annak vizsgálatához, hogy látható-e a szolgáltatást futtató tartományvezérlő, ellenőrizze a Nltest/dsgetdc kimenetét: contoso/keylist/KDC.

## <a name="next-steps"></a>Következő lépések

[További információ a jelszóval nem rendelkező](concept-authentication-passwordless.md)
