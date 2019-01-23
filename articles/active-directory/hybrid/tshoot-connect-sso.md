---
title: 'Az Azure Active Directory Connect: Közvetlen egyszeri bejelentkezés hibaelhárítása |} A Microsoft Docs'
description: Ez a témakör ismerteti az Azure Active Directory zökkenőmentes egyszeri bejelentkezés hibaelhárítása
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/24/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 369b3d2e35b20aac7067c444a7057df6d8320b24
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472934"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés hibaelhárítása

Ez a cikk hibaelhárítási információkat a vonatkozó Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezés (közvetlen egyszeri bejelentkezés) gyakori problémákkal kapcsolatban.

## <a name="known-issues"></a>Ismert problémák

- Néhány esetben közvetlen egyszeri bejelentkezés engedélyezése eltarthat akár 30 percig.
- Ha letiltja, és engedélyezze újra a közvetlen egyszeri bejelentkezés a bérlő, felhasználók, hogy lejárt a gyorsítótárazott Kerberos jegyek általában érvényes 10 órát, amíg nem az egyszeri bejelentkezés használatát fognak kapni.
- A Microsoft Edge böngésző támogatása nem érhető el.
- Közvetlen egyszeri bejelentkezés sikeres, ha a felhasználó nem rendelkezik ki **bejelentkezve szeretnék maradni**. Ez a viselkedés miatt [SharePoint és onedrive vállalati verzió leképezés forgatókönyvek](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nem működnek.
- Office 365 Win32-ügyfelek (Outlook, Word, Excel és mások) vagy újabb verzió 16.0.8730.xxxx rendelkező nem interaktív folyamat használatával támogatott. Egyéb verziói nem támogatottak; Ezen verziók felhasználók megadnia a felhasználónevek, de nem jelszavak jelentkezik be. A onedrive-on, aktiválnia kell a [OneDrive csendes config funkció](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) csendes bejelentkezési élményt.
- Közvetlen egyszeri bejelentkezés privát böngészési módban, a Firefox nem működik.
- Közvetlen egyszeri bejelentkezés az Internet Explorer nem működik, ha fokozott védett üzemmód be van kapcsolva.
- Közvetlen egyszeri bejelentkezés mobilböngészőkön iOS és Android rendszeren nem működik.
- Ha egy felhasználó az Active Directory túl sok csoport része, a felhasználó Kerberos-jegye nagy valószínűséggel el fog feldolgozni túl nagy, és ennek hatására a közvetlen egyszeri bejelentkezés meghiúsul. Az Azure AD-HTTPS-kérelmek fejlécek 50 KB; maximális mérettel rendelkezhet Kerberos-jegyet kell ezt a korlátot, például a cookie-k más Azure AD-összetevők (általában 2 – 5 KB) befogadásához kisebbnek kell lennie. Azt javasoljuk, hogy csökkentse a felhasználói csoporttagságok, majd próbálkozzon újra.
- Ha 30 vagy több Active Directory-erdők már szinkronizálást, nem engedélyezhető a közvetlen egyszeri bejelentkezés az Azure AD Connecten keresztül. Áthidaló megoldásként is [manuális módszerrel engedélyezze](#manual-reset-of-the-feature) a funkció a bérlőn.
- Az Azure AD szolgáltatás URL-cím hozzáadása (https://autologon.microsoftazuread-sso.com) a megbízható helyek zónához helyett a helyi intranetes zóna *letiltja a felhasználók bejelentkezésének*.
- Közvetlen egyszeri bejelentkezés használja a **RC4_HMAC_MD5** Kerberos titkosítási típus. Használatának letiltása a **RC4_HMAC_MD5** az Active Directory-beállítások a titkosítási típus megszakítja a közvetlen egyszeri bejelentkezés. A Csoportházirendkezelés-szerkesztő eszközben ellenőrizze, hogy a házirend értékét **RC4_HMAC_MD5** alatt **számítógép konfigurációja -> Windows-beállítások -> biztonsági beállítások -> helyi házirendek -> biztonsági beállítások - > "Hálózati biztonság: A Kerberos használható titkosítási típusok konfigurálása"** van **engedélyezve**. Arról, hogy a közvetlen egyszeri bejelentkezés továbbá nem használhatja más titkosítási típusok, ezért győződjön meg arról, hogy azok **le van tiltva**.

## <a name="check-status-of-feature"></a>A szolgáltatás állapotának ellenőrzése

Győződjön meg arról, hogy a közvetlen egyszeri bejelentkezés funkció továbbra is **engedélyezve** a bérlő. Ellenőrizheti az állapotot a a **az Azure AD Connect** ablaktábláján a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Azure Active Directory felügyeleti központ: Az Azure AD Connect panel](./media/tshoot-connect-sso/sso10.png)

Kattintson végig, amelyeken engedélyezve van a közvetlen egyszeri bejelentkezés az összes AD-erdőkkel megtekintéséhez.

![Azure Active Directory felügyeleti központ: Zökkenőmentes egyszeri bejelentkezés panel](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Bejelentkezési hibák okai az Azure Active Directory felügyeleti központban (prémium licencre van szükség)

Ha a bérlő az Azure AD Premium licenccel rendelkezik, akkor is megjeleníthető a [bejelentkezési tevékenységek jelentésének](../reports-monitoring/concept-sign-ins.md) a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Azure Active Directory felügyeleti központ: Bejelentkezések jelentés](./media/tshoot-connect-sso/sso9.png)

Keresse meg a **Azure Active Directory** > **bejelentkezések** a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/), majd válassza ki egy adott felhasználó bejelentkezési tevékenységet. Keresse meg a **bejelentkezési hiba kódja** mező. A mező értékét leképezése a hiba oka és a megoldás a következő táblázat használatával:

|Bejelentkezési hiba kódja|Jelentkezzen be a hiba oka|Megoldás:
| --- | --- | ---
| 81001 | A felhasználó Kerberos-jegye túl nagy. | Csökkentse a felhasználó csoporttagságait, majd próbálkozzon újra.
| 81002 | Nem érvényesíthető a felhasználó Kerberos-jegye. | Tekintse meg a [hibaelhárítási ellenőrzőlista](#troubleshooting-checklist).
| 81003 | Nem érvényesíthető a felhasználó Kerberos-jegye. | Tekintse meg a [hibaelhárítási ellenőrzőlista](#troubleshooting-checklist).
| 81004 | A Kerberos-hitelesítési kísérlet meghiúsult. | Tekintse meg a [hibaelhárítási ellenőrzőlista](#troubleshooting-checklist).
| 81008 | Nem érvényesíthető a felhasználó Kerberos-jegye. | Tekintse meg a [hibaelhárítási ellenőrzőlista](#troubleshooting-checklist).
| 81009 | Nem érvényesíthető a felhasználó Kerberos-jegye. | Tekintse meg a [hibaelhárítási ellenőrzőlista](#troubleshooting-checklist).
| 81010 | A zavartalan egyszeri bejelentkezés meghiúsult, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. | A felhasználónak kell bejelentkezni a vállalati hálózaton belüli tartományhoz csatlakoztatott eszközről.
| 81011 | Nem található a felhasználói objektum, a felhasználó Kerberos-jegye található információk alapján. | Az Azure AD Connect használatával az Azure AD-be a felhasználói adatok szinkronizálásához.
| 81012 | A felhasználó megpróbál bejelentkezni az Azure AD eltér a felhasználó bejelentkezett az eszközre. | A felhasználónak kell bejelentkezni egy másik eszközről.
| 81013 | Nem található a felhasználói objektum, a felhasználó Kerberos-jegye található információk alapján. |Az Azure AD Connect használatával az Azure AD-be a felhasználói adatok szinkronizálásához. 

## <a name="troubleshooting-checklist"></a>Hibaelhárítási ellenőrzőlista

A következő ellenőrzőlista segítségével zökkenőmentes egyszeri Bejelentkezéssel kapcsolatos problémák elhárítása:

- Győződjön meg arról, hogy a közvetlen egyszeri bejelentkezés funkció engedélyezve van-e az Azure AD Connectben. Ha nem engedélyezi a szolgáltatást (például mert a blokkolt port), győződjön meg arról, hogy minden a [Előfeltételek](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) helyen.
- Ha engedélyezte, mindkettő [az Azure AD Join](../active-directory-azureadjoin-overview.md) és a bérlő közvetlen egyszeri bejelentkezés győződjön meg arról, hogy a probléma nem az Azure AD Joint. Egyszeri bejelentkezés az Azure AD Join elsőbbséget élvez a közvetlen egyszeri bejelentkezés Ha az eszköz az Azure AD-ben regisztrált és a tartományhoz. Az egyszeri bejelentkezés az Azure AD-csatlakozás a felhasználónál bejelentkezési csempe, amely szerint a "Windows a csatlakoztatott".
- Ellenőrizze, hogy az Azure ad-ben URL-cím (https://autologon.microsoftazuread-sso.com) része a felhasználó intranetes zóna beállításait.
- Győződjön meg arról, hogy a vállalati eszközök az Active Directory-tartományhoz csatlakozik. Az eszköz _nem_ kell lennie [Azure AD-csatlakoztatás](../active-directory-azureadjoin-overview.md) működéséhez zökkenőmentes egyszeri bejelentkezéshez.
- Győződjön meg arról, hogy a felhasználó be van jelentkezve az eszköz egy Active Directory tartományi fiók használatával.
- Győződjön meg arról, hogy a felhasználó fiókjának van az Active Directory-erdő, ahol a közvetlen egyszeri bejelentkezés lett beállítva.
- Győződjön meg arról, hogy az eszköz a vállalati hálózathoz csatlakozik.
- Győződjön meg arról, hogy az eszköz időt szinkronizálva van-e az idő az Active Directory és a tartományvezérlők, és megfelelően vannak-e egymáshoz képest öt percen belül.
- Ügyeljen arra, hogy a `AZUREADSSOACCT` számítógépfiók jelen, és engedélyezve van minden AD-erdőben, amely azt szeretné, hogy közvetlen egyszeri bejelentkezés engedélyezve van. Ha a számítógépfiók törölve lett, vagy nincs megadva, akkor használhatja [PowerShell-parancsmagok](#manual-reset-of-the-feature) újra létre kell hoznia őket.
- Az eszközön a meglévő Kerberos-jegyet lista használatával a `klist` parancsot egy parancssorból. Ellenőrizze, hogy a kiadott jegyek a `AZUREADSSOACCT` számítógépfiók találhatók. Felhasználók Kerberos-jegyekhez érvényesek általában 10 óra. Előfordulhat, hogy a különböző beállítások Active Directoryban.
- Ha le van tiltva, és újra engedélyezni a bérlő közvetlen egyszeri bejelentkezés, felhasználó nem kap az egyszeri bejelentkezés használatát, amíg a gyorsítótárazott Kerberos-jegyek lejárt.
- Meglévő Kerberos-jegyet az eszközről a végleges törlése a `klist purge` parancsot, és próbálkozzon újra.
- Annak megállapításához, hogy vannak-e a JavaScript-problémákat, tekintse át az a böngésző-konzol naplói (a **fejlesztői eszközök**).
- Tekintse át a [domain controller naplók](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Domain controller naplók

Ha sikeres a naplózás a tartományvezérlőn, majd minden alkalommal, amikor egy felhasználó bejelentkezik keresztül közvetlen egyszeri bejelentkezés engedélyezése egy biztonsági bejegyzés az eseménynaplóba van rögzítve. Ezek a biztonsági események a következő lekérdezéssel keresheti. (Esemény **4769** a számítógép fiókjához társított **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>A szolgáltatás kézi alaphelyzetbe állítás

Hibaelhárítási oldották meg, ha manuálisan alaphelyzetbe állíthatja a funkció a bérlő. Kövesse ezeket a lépéseket a helyszíni kiszolgálón, ahol futtatja az Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>1. lépés: A zökkenőmentes egyszeri bejelentkezési PowerShell-modul importálása

1. Először töltse le és telepítse [az Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Keresse meg a `%programfiles%\Microsoft Azure Active Directory Connect` mappát.
3. A zökkenőmentes egyszeri bejelentkezési PowerShell-modul importálása a következő paranccsal: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>2. lépés: Listájának megtekintéséhez, amelyen a közvetlen egyszeri bejelentkezés engedélyezve van az Active Directory-erdők

1. Futtassa a Powershellt rendszergazdaként. A PowerShellben hívás `New-AzureADSSOAuthenticationContext`. Amikor a rendszer kéri, adja meg a bérlő globális rendszergazdai hitelesítő adatokkal.
2. Hívás `Get-AzureADSSOStatus`. Ez a parancs biztosít az Active Directory-erdők, (tekintse meg a "Tartományok" listája) listája a, amelyhez ez a funkció engedélyezve van.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>3. lépés: Minden egyes Active Directory-erdőben, amennyiben beállította a szolgáltatást a közvetlen egyszeri bejelentkezés letiltása

1. Hívás `$creds = Get-Credential`. Amikor a rendszer kéri, adja meg a tartományi rendszergazda hitelesítő adatai a megfelelő Active Directory-erdőben.

    >[!NOTE]
    >A tartományi rendszergazda felhasználónév, a felhasználó egyszerű neve (UPN) a megadott használjuk (johndoe@contoso.com) vagy a tartomány minősített sam-fiók neve (contoso\budaipeter vagy contoso.com\johndoe) formátumban, a megfelelő AD-erdőben található. Ha minősített sam-fiók tartománynevet használ, a felhasználónevet, tartományt jelölő része használjuk [keresse meg a tartományvezérlő, a tartományi rendszergazda DNS-sel](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Ha Ehelyett használjon egyszerű felhasználónév azt [lefordíthatja minősített sam-fiók tartománynevét](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) előtt a megfelelő tartományvezérlő keresésekor.

2. Hívás `Disable-AzureADSSOForest -OnPremCredentials $creds`. Ezzel a paranccsal eltávolítható az `AZUREADSSOACCT` az adott erdő Active Directory a helyszíni tartományvezérlő számítógépfiókja.
3. Ismételje meg a fenti lépéseket minden egyes Active Directory-erdőben, amennyiben beállította a szolgáltatást.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. lépés: Közvetlen egyszeri bejelentkezés engedélyezése az Active Directory erdőhöz

1. Hívás `Enable-AzureADSSOForest`. Amikor a rendszer kéri, adja meg a tartományi rendszergazda hitelesítő adatai a megfelelő Active Directory-erdőben.

   >[!NOTE]
   >A tartományi rendszergazda felhasználónév, a felhasználó egyszerű neve (UPN) a megadott használjuk (johndoe@contoso.com) vagy a tartomány minősített sam-fiók neve (contoso\budaipeter vagy contoso.com\johndoe) formátumban, a megfelelő AD-erdőben található. Ha minősített sam-fiók tartománynevet használ, a felhasználónevet, tartományt jelölő része használjuk [keresse meg a tartományvezérlő, a tartományi rendszergazda DNS-sel](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Ha Ehelyett használjon egyszerű felhasználónév azt [lefordíthatja minősített sam-fiók tartománynevét](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) előtt a megfelelő tartományvezérlő keresésekor.

2. Ismételje meg az előző lépést minden egyes Active Directory-erdőben, ahol szeretné beállítani a szolgáltatást.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. lépés A bérlő a funkció engedélyezése

A bérlő a funkció bekapcsolásához hívás `Enable-AzureADSSO -Enable $true`.
