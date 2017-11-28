---
title: "Az Azure Active Directory Connect: Zökkenőmentes egyszeri bejelentkezés hibaelhárítása |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure Active Directory zökkenőmentes egyszeri bejelentkezést hibaelhárítása"
services: active-directory
keywords: "Mi az Azure AD Connect telepítés Active Directory szükséges összetevőket az Azure AD, SSO, egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: b4efb457a58d8b54c9ebb126a8d84fdef01b3847
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Hibaelhárítás az Azure Active Directory zökkenőmentes egyszeri bejelentkezést.

Ez a cikk segít hibaelhárítási kapcsolatos Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezést (zökkenőmentes SSO) kapcsolatos gyakori problémákra vonatkozó információkat.

## <a name="known-problems"></a>Ismert problémák

- Néhány esetben zökkenőmentes SSO engedélyezése is 30 percig tarthat.
- Edge böngésző támogatása nem érhető el.
- Office-ügyfelekhez, különösen a megosztott számítógép esetben indítása hatására a felhasználók bejelentkezés extra kér. Gyakran a felhasználónevek, de nem a jelszavát kell megadni.
- Zökkenőmentes SSO sikeres, ha a felhasználó nem rendelkezik kijelölni **bejelentkezve szeretnék maradni**. Ezt a viselkedést, mert a SharePoint és a onedrive vállalati verzió leképezési forgatókönyvek nem működnek.
- Zökkenőmentes SSO privát böngészés módban a Firefox nem működik.
- Zökkenőmentes SSO nem működik az Internet Explorerben, amikor a fokozott védett mód engedélyezve van.
- Zökkenőmentes SSO nem működik a mobilböngészők iOS és Android rendszeren.
- Ha 30 vagy több Active Directory-erdőre van szinkronizálást, zökkenőmentes SSO keresztül az Azure AD Connect nem engedélyezhető. Megoldás is [manuális módszerrel engedélyezze az](#manual-reset-of-azure-ad-seamless-sso) a bérlő a funkciót.
- Az Azure AD szolgáltatás URL-címek (https://autologon.microsoftazuread-sso.com, https://aadg.windows.net.nsatc.net) hozzáadása a megbízható helyek zónában helyett a helyi intranet zónához *megakadályozza a felhasználókat ebben*.

## <a name="check-the-status-of-the-feature"></a>A szolgáltatás állapotának ellenőrzése

Győződjön meg arról, hogy a zökkenőmentes SSO-funkció még **engedélyezve** a tenant. Állapotát megnyitásával ellenőrizheti a **az Azure AD Connect** ablaktábláján a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ: az Azure AD Connect ablaktábla](./media/active-directory-aadconnect-sso/sso10.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Bejelentkezési hiba okai az Azure Active Directory felügyeleti központban (prémium szintű licencre van szüksége)

Ha a bérlő az Azure AD Premium licenchez társítva, is megtalálhatja a [bejelentkezési tevékenységei jelentése](../active-directory-reporting-activity-sign-ins.md) a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ: bejelentkezések jelentés](./media/active-directory-aadconnect-sso/sso9.png)

Keresse meg a **Azure Active Directory** > **bejelentkezések** a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/), majd válassza ki a felhasználó bejelentkezési tevékenységet. Keresse meg a **SIGN-IN hibakód** mező. A mező értékének leképezése a hiba okát is megadva és megoldás szerint a következő táblázat használatával:

|Bejelentkezési hiba kódja|Bejelentkezési hiba okát is megadva|Megoldás:
| --- | --- | ---
| 81001 | A felhasználó Kerberos-jegye túl nagy. | Csökkentse a felhasználói csoport tagságát, és próbálkozzon újra.
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
- Győződjön meg arról, hogy mindkét az Azure AD URL-címhez (https://autologon.microsoftazuread-sso.com és https://aadg.windows.net.nsatc.net) a felhasználó Intranet zóna beállítások részeként.
- Győződjön meg arról, hogy a vállalati eszköz csatlakozik-e az Active Directory-tartományhoz.
- Győződjön meg arról, hogy a felhasználó bejelentkezik a az eszközt egy Active Directory tartományi fiók keresztül.
- Győződjön meg arról, hogy a felhasználó fiókjának van egy Active Directory erdőből, ahol zökkenőmentes SSO lett beállítva.
- Győződjön meg arról, hogy az eszköz a vállalati hálózathoz csatlakozik.
- Győződjön meg arról, hogy az eszköz az Active Directory és a tartományvezérlők idő szinkronizálva van, és megfelelően vannak-e egymáshoz képest öt percen belül.
- Az eszközön a meglévő Kerberos-jegyek lista használatával a `klist` parancsot a parancssorból. Győződjön meg arról, hogy a kiadott jegyek a `AZUREADSSOACCT` számítógépfiók találhatók. A felhasználói Kerberos-jegyek érvényesek általában 12 óra. Lehetséges, hogy különböző beállítások az Active Directoryban.
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
