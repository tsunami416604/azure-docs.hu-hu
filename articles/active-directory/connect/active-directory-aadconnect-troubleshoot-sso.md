---
title: 'Az Azure Active Directory Connect: Zökkenőmentes egyszeri bejelentkezés hibaelhárítása |} Microsoft Docs'
description: Ez a témakör ismerteti az Azure Active Directory zökkenőmentes egyszeri bejelentkezést hibaelhárítása
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 4df60668f6b9aa0afb2203fa59788c47e2ffaefb
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110889"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Hibaelhárítás az Azure Active Directory zökkenőmentes egyszeri bejelentkezést.

Ez a cikk segít hibaelhárítási kapcsolatos Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezést (zökkenőmentes SSO) kapcsolatos gyakori problémákra vonatkozó információkat.

## <a name="known-issues"></a>Ismert problémák

- Néhány esetben zökkenőmentes SSO engedélyezése is 30 percig tarthat.
- Tiltsa le, majd engedélyezze újra a zökkenőmentes egyszeri Bejelentkezést a tenant, ha felhasználók nem kapnak az egyszeri bejelentkezést keretein belül a gyorsítótárazott Kerberos jegyek általában érvényes 10 óra lejárt.
- Edge böngésző támogatása nem érhető el.
- Zökkenőmentes SSO sikeres, ha a felhasználó nem rendelkezik kijelölni **bejelentkezve szeretnék maradni**. Ezt a viselkedést, mert a SharePoint és a onedrive vállalati verzió leképezési forgatókönyvek nem működnek.
- Verzió 16.0.8730.xxxx alábbi Office-ügyfelek nem támogatják a nem interaktív bejelentkezés az zökkenőmentes egyszeri Bejelentkezést. Ezek az ügyfelek felhasználók adja meg a felhasználónevek, de nem jelszóval jelentkezik be.
- Zökkenőmentes SSO privát böngészés módban a Firefox nem működik.
- Zökkenőmentes SSO nem működik az Internet Explorerben, amikor a fokozott védett mód engedélyezve van.
- Zökkenőmentes SSO nem működik a mobilböngészők iOS és Android rendszeren.
- Ha egy felhasználó része túl sok csoportok Active Directory, a felhasználó Kerberos-jegy valószínűleg túl nagy feldolgozni, és ennek hatására a zökkenőmentes SSO sikertelen lesz. Az Azure AD HTTPS-kéréseket rendelkezhet fejléc mérete legfeljebb 16 KB-os; Kerberos-jegyek kell sokkal kisebb, mint más Azure AD-összetevők, például a cookie-k olyan sorszámát. Azt javasoljuk, csökkentse a felhasználó csoporttagságait, majd próbálja meg újból.
- Ha 30 vagy több Active Directory-erdőre van szinkronizálást, zökkenőmentes SSO keresztül az Azure AD Connect nem engedélyezhető. Megoldás is [manuális módszerrel engedélyezze az](#manual-reset-of-azure-ad-seamless-sso) a bérlő a funkciót.
- Az Azure AD szolgáltatás URL-cím hozzáadása (https://autologon.microsoftazuread-sso.com) a megbízható helyek zónához helyett a helyi intranet zónához *megakadályozza a felhasználókat ebben*.
- Használatának letiltása a **RC4_HMAC_MD5** titkosítási típus a Kerberos, az Active Directory beállításaiban zökkenőmentes SSO megszakad. A Csoportházirendkezelés-szerkesztő eszközben ellenőrizze, hogy a házirend értéke **RC4_HMAC_MD5** alatt **számítógép konfigurációja -> Windows-beállítások -> biztonsági beállítások -> helyi házirendek -> biztonsági beállítások - > "Hálózati biztonság: konfigurálja a Kerberos engedélyezett titkosítási típusok"** "Engedélyezett".

## <a name="check-status-of-feature"></a>A szolgáltatás állapotának ellenőrzése

Győződjön meg arról, hogy a zökkenőmentes SSO-funkció még **engedélyezve** a tenant. Állapotát megnyitásával ellenőrizheti a **az Azure AD Connect** ablaktábláján a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ: az Azure AD Connect ablaktábla](./media/active-directory-aadconnect-sso/sso10.png)

Keresztül kattintva megtekintheti az összes az AD-erdőkkel, amelyeken engedélyezve van a zökkenőmentes SSO-e.

![Az Azure Active Directory felügyeleti központ: zökkenőmentes SSO ablaktábla](./media/active-directory-aadconnect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Bejelentkezési hiba okai az Azure Active Directory felügyeleti központban (prémium szintű licencre van szüksége)

Ha a bérlő az Azure AD Premium licenchez társítva, is megtalálhatja a [bejelentkezési tevékenységei jelentése](../active-directory-reporting-activity-sign-ins.md) a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ: bejelentkezések jelentés](./media/active-directory-aadconnect-sso/sso9.png)

Keresse meg a **Azure Active Directory** > **bejelentkezések** a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/), majd válassza ki a felhasználó bejelentkezési tevékenységet. Keresse meg a **SIGN-IN hibakód** mező. A mező értékének leképezése a hiba okát is megadva és megoldás szerint a következő táblázat használatával:

|Bejelentkezési hiba kódja|Bejelentkezési hiba okát is megadva|Megoldás:
| --- | --- | ---
| 81001 | A felhasználó Kerberos-jegye túl nagy. | Csökkentse a felhasználó csoporttagságait, majd próbálkozzon újra.
| 81002 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegy. | Tekintse meg a [ellenőrzőlista hibaelhárítási](#troubleshooting-checklist).
| 81003 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegy. | Tekintse meg a [ellenőrzőlista hibaelhárítási](#troubleshooting-checklist).
| 81004 | A Kerberos-hitelesítési kísérlet meghiúsult. | Tekintse meg a [ellenőrzőlista hibaelhárítási](#troubleshooting-checklist).
| 81008 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegy. | Tekintse meg a [ellenőrzőlista hibaelhárítási](#troubleshooting-checklist).
| 81009 | Nem sikerült érvényesíteni a felhasználó Kerberos-jegy. | Tekintse meg a [ellenőrzőlista hibaelhárítási](#troubleshooting-checklist).
| 81010 | A zavartalan egyszeri bejelentkezés meghiúsult, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. | A felhasználónak be kell jelentkeznie a vállalati hálózaton belüli tartományhoz csatlakoztatott eszközről.
| 81011 | Nem található a felhasználói objektum, a felhasználó Kerberos-jegy található információk alapján. | Az Azure AD Connect segítségével a felhasználó adatait az Azure AD-be szinkronizálja.
| 81012 | A felhasználó megpróbál bejelentkezni az Azure AD eltér a felhasználót, hogy az eszköz van bejelentkezve. | A felhasználónak be kell jelentkeznie egy másik eszközről.
| 81013 | Nem található a felhasználói objektum, a felhasználó Kerberos-jegy található információk alapján. |Az Azure AD Connect segítségével a felhasználó adatait az Azure AD-be szinkronizálja. 

## <a name="troubleshooting-checklist"></a>Hibaelhárítási ellenőrzőlista

A következő ellenőrzőlista segítségével zökkenőmentes egyszeri Bejelentkezéssel kapcsolatos problémák elhárítása:

- Győződjön meg arról, hogy a zökkenőmentes SSO szolgáltatás engedélyezve van-e az Azure AD Connectben. Ha nem engedélyezi a szolgáltatást (például mert a blokkolt port), győződjön meg arról, hogy minden a [Előfeltételek](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites) helyen.
- Ha úgy állította be mind [az Azure AD Join](../active-directory-azureadjoin-overview.md) és zökkenőmentes egyszeri Bejelentkezést a tenant, gondoskodjon arról, hogy a problémát nem az Azure AD Join. Egyszeri bejelentkezés az Azure AD Join szándéka elsőbbséget élvez zökkenőmentes SSO, ha az eszköz az Azure AD-ben regisztrált és a tartományhoz. Az Azure AD Join egyszeri bejelentkezési modellel a felhasználó egy bejelentkezési csempe, amely szerint a "Csatlakoztatott Windows" láthatja.
- Győződjön meg arról, hogy az Azure AD URL-CÍMÉT (https://autologon.microsoftazuread-sso.com) része a felhasználó intranetes zónájának beállításai közül.
- Győződjön meg arról, hogy a vállalati eszköz csatlakozik-e az Active Directory-tartományhoz.
- Győződjön meg arról, hogy a felhasználó bejelentkezik a az eszközt egy Active Directory tartományi fiók keresztül.
- Győződjön meg arról, hogy a felhasználó fiókjának van egy Active Directory erdőből, ahol zökkenőmentes SSO lett beállítva.
- Győződjön meg arról, hogy az eszköz a vállalati hálózathoz csatlakozik.
- Győződjön meg arról, hogy az eszköz az Active Directory és a tartományvezérlők idő szinkronizálva van, és megfelelően vannak-e egymáshoz képest öt percen belül.
- Győződjön meg arról, hogy a `AZUREADSSOACCT` számítógépfiók jelen és engedélyezett minden zökkenőmentes SSO engedélyezni kívánt Active Directory-erdőben. 
- Az eszközön a meglévő Kerberos-jegyek lista használatával a `klist` parancsot a parancssorból. Győződjön meg arról, hogy a kiadott jegyek a `AZUREADSSOACCT` számítógépfiók találhatók. A felhasználói Kerberos-jegyek érvényesek általában 10 óra. Lehetséges, hogy különböző beállítások az Active Directoryban.
- Ha le van tiltva, és újra engedélyezik a tenant zökkenőmentes SSO, felhasználók nem kapnak az egyszeri bejelentkezést keretein belül a gyorsítótárazott Kerberos-jegyek lejárt.
- Törli a meglévő Kerberos-jegyet az eszközről használatával a `klist purge` parancsot, majd próbálkozzon újra.
- Annak megállapításához, hogy vannak-e a JavaScript-problémákat, tekintse át az a böngésző-konzol naplói (a **fejlesztői eszközök**).
- Tekintse át a [tartomány a tartományvezérlő naplók](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Tartomány a tartományvezérlő naplók

Ha engedélyezi a sikeres naplózást, a tartományvezérlő, akkor minden alkalommal, amikor egy felhasználó bejelentkezik zökkenőmentes SSO keresztül, egy biztonsági bejegyzés rögzített az eseménynaplóban talál. A biztonsági események található a következő lekérdezés segítségével. (Eseményt **4769** számítógép fiókhoz tartozó **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>A szolgáltatás kézi alaphelyzetbe állítás

Hibaelhárítás nem oldották problémát, a bérlő a szolgáltatás kézi alaphelyzetbe. Kövesse az alábbi lépéseket a helyszíni kiszolgálón ahol az Azure AD Connect futtatja.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>1. lépés: A zökkenőmentes SSO PowerShell modul importálása

1. Töltse le és telepítse a [Microsoft Online Services bejelentkezési segéd](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Töltse le és telepítse a [64 bites Azure Active Directory-modul Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Keresse meg a `%programfiles%\Microsoft Azure Active Directory Connect` mappát.
4. Ez a parancs segítségével a zökkenőmentes SSO PowerShell modul importálása: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>2. lépés:, Amelyen a zökkenőmentes SSO engedélyezve van az Active Directory-erdők listájának lekérdezése

1. PowerShell futtatása rendszergazdaként. A PowerShellben hívás `New-AzureADSSOAuthenticationContext`. Amikor a rendszer kéri, adja meg a bérlő globális rendszergazda hitelesítő adatait.
2. Hívás `Get-AzureADSSOStatus`. Ez a parancs biztosít a listában az Active Directory-erdők (nézze meg a "Tartományok" lista) a, amelyhez ez a funkció engedélyezve van.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>3. lépés: Zökkenőmentes SSO minden egyes Active Directory erdőhöz, ahol a szolgáltatás kiépítését tiltsa le.

1. Hívás `$creds = Get-Credential`. Amikor a rendszer kéri, adja meg a tartományi rendszergazda hitelesítő adatait a megfelelő Active Directory-erdőben.
2. Hívás `Disable-AzureADSSOForest -OnPremCredentials $creds`. Ez a parancs eltávolítja a `AZUREADSSOACCT` az adott Active Directory-erdő a helyi tartományvezérlő számítógépfiókját.
3. Ismételje meg az előző lépést minden Active Directory-erdőben, ahol a szolgáltatás kiépítését.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. lépés: Zökkenőmentes SSO engedélyezése minden Active Directory-erdőhöz

1. Hívás `Enable-AzureADSSOForest`. Amikor a rendszer kéri, adja meg a tartományi rendszergazda hitelesítő adatait a megfelelő Active Directory-erdőben.
2. Ismételje meg az előző lépést minden Active Directory-erdőben, ahol szeretné beállítani a szolgáltatást.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. lépés A tenant funkció engedélyezése

A tenant funkció bekapcsolásához hívja `Enable-AzureADSSO` , és írja be **igaz** , a `Enable:` kérdés.
