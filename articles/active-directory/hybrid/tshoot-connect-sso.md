---
title: 'Azure Active Directory Connect: Hibaelhárítás zökkenőmentes egyszeri bejelentkezéssel | Microsoft dokumentumok'
description: Ez a témakör az Azure Active Directory zökkenőmentes egyszeri bejelentkezéshibáinak elhárítását ismerteti
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759748124893a8f906a4bc336f835546202b0b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049497"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés – problémamegoldása

Ez a cikk segítséget nyújt az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezés (zökkenőmentes egyszeri bejelentkezés) gyakori problémáival kapcsolatos hibaelhárítási információk megtalálásában.

## <a name="known-issues"></a>Ismert problémák

- Néhány esetben a zökkenőmentes egyszeri bejelentkezés engedélyezése akár 30 percet is igénybe vehet.
- Ha letiltja, majd újra engedélyezi a zökkenőmentes egyszeri bejelentkezést a bérlőn, a felhasználók nem kapják meg az egyszeri bejelentkezési élményt, amíg a gyorsítótárazott Kerberos-jegyeik, amelyek általában 10 óráig érvényesek, le nem járnak.
- Ha a zökkenőmentes egyszeri bejelentkezés sikeres, a felhasználónak nincs lehetősége a **Bejelentkezve maradva**lehetőséget választani. E miatt a SharePoint és a [OneDrive leképezési forgatókönyvei](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nem működnek.
- A 16.0.8730.xxxx és újabb verziókkal rendelkező Office 365 Win32-ügyfelek (Outlook, Word, Excel és mások) nem interaktív folyamattal támogatottak. Más verziók nem támogatottak; ezeken a verziókon a felhasználók a bejelentkezéshez beírják a felhasználónevüket, de a jelszavukat nem. A OneDrive-hoz aktiválnia kell a [OneDrive csendes konfigurációs funkcióját](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) a csendes bejelentkezési élmény érdekében.
- A zökkenőmentes egyszeri bejelentkezés nem működik privát böngészési módban a Firefoxon.
- A zökkenőmentes egyszeri bejelentkezés nem működik az Internet Explorer programban, ha a fokozottan védett mód be van kapcsolva.
- A zökkenőmentes egyszeri bejelentkezés nem működik mobil böngészőkben iOS és Android rendszeren.
- Ha egy felhasználó túl sok csoport tagja az Active Directoryban, a felhasználó Kerberos-jegye valószínűleg túl nagy lesz a feldolgozáshoz, és ez a zökkenőmentes egyszeri bejelentkezés sikertelensítéséhez vezet. Az Azure AD HTTPS-kérelmek legfeljebb 50 KB méretű fejlécekkel rendelkezhetnek; A Kerberos-jegyeknek kisebbnek kell lenniük, mint ez a korlát, hogy más Azure AD-összetevők (általában 2 – 5 KB) is megfeleljenek, például a cookie-k. Javasoljuk, hogy csökkentse a felhasználó csoporttagságait, és próbálkozzon újra.
- Ha 30 vagy több Active Directory-erdőt szinkronizál, nem engedélyezheti a zökkenőmentes egyszeri bejelentkezést az Azure AD Connecten keresztül. Kerülő megoldásként [manuálisan engedélyezheti](#manual-reset-of-the-feature) a funkciót a bérlőn.
- Az Azure AD szolgáltatás`https://autologon.microsoftazuread-sso.com`URL-címének ( ) hozzáadása a Helyi intranet zóna helyett a Megbízható helyek zónához megakadályozza, hogy a *felhasználók bejelentkezjenek.*
- A seamless sso támogatja a Kerberos AES256_HMAC_SHA1, AES128_HMAC_SHA1 és RC4_HMAC_MD5 titkosítási típusait. Ajánlott, hogy az AzureADSSOAcc$ fiók titkosítási típusa AES256_HMAC_SHA1, vagy az AES-típusok és az RC4 a nagyobb biztonság érdekében. A titkosítási típus a fiók msDS-SupportedEncryptionTypes attribútumában található az Active Directoryban.  Ha az AzureADSSOAcc$ fiók titkosítási típusa RC4_HMAC_MD5, és módosítani szeretné az AES-titkosítási típusok egyikére, győződjön meg arról, hogy először az AzureADSSOAcc$ fiók Kerberos-visszafejtési kulcsát görgeti át a vonatkozó kérdés [gyIK-dokumentumban](how-to-connect-sso-faq.md) leírtak szerint, ellenkező esetben a zökkenőmentes egyszeri bejelentkezés nem fog megtörténni.

## <a name="check-status-of-feature"></a>A szolgáltatás állapotának ellenőrzése

Győződjön meg arról, hogy a zökkenőmentes egyszeri bejelentkezés funkció továbbra is **engedélyezve** van a bérlőn. Az állapot ot az Azure Active Directory felügyeleti [központjában](https://aad.portal.azure.com/)található **Azure AD Connect** ablaktáblában ellenőrizheti.

![Azure Active Directory felügyeleti központ: Az Azure AD Connect ablaktábla](./media/tshoot-connect-sso/sso10.png)

Kattintson át a zökkenőmentes egyszeri bejelentkezéshez engedélyezett összes AD-erdő megtekintéséhez.

![Azure Active Directory felügyeleti központ: Zökkenőmentes egyszeri bejelentkezés ablaktábla](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Bejelentkezési hibák okai az Azure Active Directory felügyeleti központban (prémium szintű licencre van szüksége)

Ha a bérlő rendelkezik egy Azure AD [Premium-licenc](../reports-monitoring/concept-sign-ins.md) társítva, akkor is tekintse meg a bejelentkezési tevékenység jelentést az [Azure Active Directory felügyeleti központ.](https://aad.portal.azure.com/)

![Azure Active Directory felügyeleti központ: Bejelentkezések jelentés](./media/tshoot-connect-sso/sso9.png)

Tallózással keresse meg az **Azure Active Directory** > **bejelentkezések** az [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com/), és válassza ki egy adott felhasználó bejelentkezési tevékenységét. Keresse meg a **SIGN-IN ERROR CODE mezőt.** A mező értékét az alábbi táblázat segítségével rendelje hozzá a hiba okához és megoldásához:

|Bejelentkezési hibakód|Bejelentkezési hiba oka|Megoldás:
| --- | --- | ---
| 81001 | A felhasználó Kerberos-jegye túl nagy. | Csökkentse a felhasználó csoporttagságait, majd próbálkozzon újra.
| 81002 | Nem lehet érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81003 | Nem lehet érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81004 | A Kerberos-hitelesítési kísérlet meghiúsult. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81008 | Nem lehet érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81009 | Nem lehet érvényesíteni a felhasználó Kerberos-jegyét. | Tekintse meg a [hibaelhárítási ellenőrzőlistát](#troubleshooting-checklist).
| 81010 | A zavartalan egyszeri bejelentkezés meghiúsult, mert a felhasználó Kerberos-jegye lejárt vagy érvénytelen. | A felhasználónak a vállalati hálózaton belül egy tartományhoz csatlakozó eszközről kell bejelentkeznie.
| 81011 | Nem található a felhasználói objektum a felhasználó Kerberos-jegyében szereplő információk alapján. | Az Azure AD Connect használatával szinkronizálhatja a felhasználó adatait az Azure AD-vel.
| 81012 | Az Azure AD-be bejelentkezni próbáló felhasználó eltér az eszközre bejelentkezett felhasználótól. | A felhasználónak egy másik eszközről kell bejelentkeznie.
| 81013 | Nem található a felhasználói objektum a felhasználó Kerberos-jegyében szereplő információk alapján. |Az Azure AD Connect használatával szinkronizálhatja a felhasználó adatait az Azure AD-vel. 

## <a name="troubleshooting-checklist"></a>Hibaelhárítási ellenőrzőlista

Az alábbi ellenőrzőlista segítségével elháríthatja a zökkenőmentes egyszeri bejelentkezésproblémáit:

- Győződjön meg arról, hogy a zökkenőmentes egyszeri bejelentkezés funkció engedélyezve van az Azure AD Connect. Ha nem tudja engedélyezni a funkciót (például egy blokkolt port miatt), győződjön meg arról, hogy az összes [előfeltétel](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) a helyén van.
- Ha engedélyezte az [Azure AD-csatlakozást](../active-directory-azureadjoin-overview.md) és a zökkenőmentes egyszeri bejelentkezést a bérlőn, győződjön meg arról, hogy a probléma nem az Azure AD Join. Egyszeri bejelentkezés az Azure AD-s csatlakozás elsőbbséget élvez a zökkenőmentes egyszeri bejelentkezéssel szemben, ha az eszköz regisztrálva van az Azure AD-vel és a tartományhoz csatlakozott. Az Azure AD Join egyszeri bejelentkezésével a felhasználó egy "Windowshoz csatlakoztatva" feliratú bejelentkezési csempét lát.
- Győződjön meg arról,`https://autologon.microsoftazuread-sso.com`hogy az Azure AD URL-cím ( ) része a felhasználó intranet-zóna beállításait.
- Győződjön meg arról, hogy a vállalati eszköz csatlakozik az Active Directory tartományhoz. Az eszköz _nem_ kell [azure AD csatlakozott](../active-directory-azureadjoin-overview.md) a zökkenőmentes egyszeri bejelentkezés működéséhez.
- Győződjön meg arról, hogy a felhasználó egy Active Directory tartományi fiókon keresztül van bejelentkezve az eszközre.
- Győződjön meg arról, hogy a felhasználó fiókja olyan Active Directory erdőből származik, ahol a zökkenőmentes egyszeri bejelentkezés be van állítva.
- Győződjön meg arról, hogy az eszköz csatlakozik a vállalati hálózathoz.
- Győződjön meg arról, hogy az eszköz ideje szinkronizálva van az Active Directoryban és a tartományvezérlőkön töltött idővel, és hogy öt percen belül egymástól vannak.Ensure that the device's time is synchronized with the time in both Directory and the domain controllers, and they are within five minutes of other.
- Győződjön `AZUREADSSOACC` meg arról, hogy a számítógépfiók minden olyan AD-erdőben jelen van és engedélyezve van, amelyet engedélyezni szeretne a zökkenőmentes egyszeri bejelentkezéshez. Ha a számítógépfiók törölve lett, vagy hiányzik, [a PowerShell-parancsmagokkal](#manual-reset-of-the-feature) újra létrehozhatja őket.
- Sorolja fel a meglévő Kerberos-jegyeket az eszközön a `klist` parancssorból származó parancs segítségével. Győződjön meg arról, hogy a `AZUREADSSOACC` számítógépfiókhoz kiadott jegyek jelen vannak. A felhasználók Kerberos-jegyei általában 10 óráig érvényesek. Lehet, hogy az Active Directory ban más a beállítás.
- Ha letiltotta és újra engedélyezte a zökkenőmentes egyszeri bejelentkezést a bérlőn, a felhasználók nem kapják meg az egyszeri bejelentkezési élményt, amíg a gyorsítótárazott Kerberos-jegyeik le nem járnak.
- A parancs segítségével ürítse `klist purge` ki a meglévő Kerberos-jegyeket az eszközről, majd próbálkozzon újra.
- Annak megállapításához, hogy vannak-e JavaScripttel kapcsolatos problémák, tekintse át a böngésző konzolnaplóit (a **Fejlesztői eszközök csoportban).**
- Tekintse át a [tartományvezérlő naplóit](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Tartományvezérlő-naplók

Ha engedélyezi a sikeres naplózást a tartományvezérlőn, akkor minden alkalommal, amikor egy felhasználó bejelentkezik a zökkenőmentes egyszeri bejelentkezésen keresztül, a rendszer biztonsági bejegyzést rögzít az eseménynaplóban. Ezeket a biztonsági eseményeket a következő lekérdezéssel találhatja meg. (Keresse meg az **AzureADSSOAcc$** számítógépfiókhoz társított **4769-es** eseményt.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>A funkció manuális alaphelyzetbe állítása

Ha a hibaelhárítás nem segített, manuálisan alaphelyzetbe állíthatja a szolgáltatást a bérlőn. Kövesse az alábbi lépéseket a helyszíni kiszolgálón, ahol az Azure AD Connect fut.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>1. lépés: A zökkenőmentes SSO PowerShell modul importálása

1. Először töltse le és telepítse az [Azure AD PowerShellt.](https://docs.microsoft.com/powershell/azure/active-directory/overview)
2. Tallózással keresse meg a `%programfiles%\Microsoft Azure Active Directory Connect` mappát.
3. Importálja a zökkenőmentes SSO PowerShell `Import-Module .\AzureADSSO.psd1`modult a következő paranccsal: .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>2. lépés: Azon Active Directory-erdők listájának beszereznie, amelyeken a zökkenőmentes egyszeri bejelentkezés engedélyezve van

1. Futtassa a PowerShellt rendszergazdaként. A PowerShellben `New-AzureADSSOAuthenticationContext`hívja a . Amikor a rendszer kéri, adja meg a bérlő globális rendszergazdai hitelesítő adatait.
2. Hívja `Get-AzureADSSOStatus`a hívást. Ez a parancs tartalmazza az Active Directory-erdők listáját (nézd meg a "Tartományok" listát), amelyen ez a szolgáltatás engedélyezve van.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>3. lépés: A zökkenőmentes egyszeri bejelentkezés letiltása minden olyan Active Directory-erdőben, ahol beállította a szolgáltatást

1. Hívja `$creds = Get-Credential`a hívást. Amikor a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazda hitelesítő adatainak felhasználónevét a SAM-fiók névformátumában kell megadni (contoso\johndoe vagy contoso.com\johndoe). A felhasználónév tartományrészét használjuk a tartományvezérlő DNS-t használó tartományvezérlőjének megkeresésére.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Védett felhasználók csoport tagja. Ha igen, a művelet sikertelen lesz.

2. Hívja `Disable-AzureADSSOForest -OnPremCredentials $creds`a hívást. Ez a parancs `AZUREADSSOACC` eltávolítja a számítógépfiókot az adott Active Directory-erdő helyszíni tartományvezérlőjéről.
3. Ismételje meg az előző lépéseket minden olyan Active Directory-erdőben, ahol beállította a szolgáltatást.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>4. lépés: Zökkenőmentes egyszeri bejelentkezés engedélyezése minden Active Directory-erdőhöz

1. Hívja `Enable-AzureADSSOForest`a hívást. Amikor a rendszer kéri, adja meg a kívánt Active Directory-erdő tartományi rendszergazdai hitelesítő adatait.

   > [!NOTE]
   >A tartományi rendszergazda hitelesítő adatainak felhasználónevét a SAM-fiók névformátumában kell megadni (contoso\johndoe vagy contoso.com\johndoe). A felhasználónév tartományrészét használjuk a tartományvezérlő DNS-t használó tartományvezérlőjének megkeresésére.

   >[!NOTE]
   >A használt tartományi rendszergazdai fiók nem lehet a Védett felhasználók csoport tagja. Ha igen, a művelet sikertelen lesz.

2. Ismételje meg az előző lépést minden olyan Active Directory-erdőesetében, ahol be szeretné állítani a szolgáltatást.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>5. lépés A szolgáltatás engedélyezése a bérlőn

A szolgáltatás bérlőn való bekapcsolásához hívja a hívást. `Enable-AzureADSSO -Enable $true`
