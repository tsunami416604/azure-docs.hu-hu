---
title: Vállalati állapotroaming – problémamegoldás az Azure Active Directoryban
description: Válaszok a rendszergazdák által a beállításokkal és az alkalmazásadatok szinkronizálásával kapcsolatos egyes kérdésekre.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8ce24aeb665a7f99326e83dbe18d020e1b6196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672347"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Vállalati állapotroaming-beállítások elhárítása az Azure Active Directoryban

Ez a témakör a vállalati állapotroaminggal kapcsolatos problémák elhárításáról és diagnosztizálásáról, valamint az ismert problémák listájáról nyújt tájékoztatást.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Ez a cikk a 2015 júliusában Windows 10 rendszerrel indított Microsoft Edge Legacy HTML-alapú böngészőre vonatkozik. A cikk nem vonatkozik az új Microsoft Edge Chromium-alapú böngésző re január 15-én, 2020. Az új Microsoft Edge szinkronizálási viselkedéséről további információt a Microsoft Edge Sync című cikkben [talál.](/deployedge/microsoft-edge-enterprise-sync)

## <a name="preliminary-steps-for-troubleshooting"></a>A hibaelhárítás előzetes lépései 

A hibaelhárítás megkezdése előtt ellenőrizze, hogy a felhasználó és az eszköz megfelelően van-e konfigurálva, és hogy az eszköz és a felhasználó megfelel-e a vállalati állapotroaming összes követelményének. 

1. Az eszközre telepítve van a Windows 10 a legújabb frissítésekkel és egy minimális 1511-es verzióval (OS Build 10586 vagy újabb). 
1. Az eszköz az Azure AD-hez vagy hibrid Azure AD-hez csatlakozott. További információ: [Hogyan juthat be egy eszköz az Azure AD ellenőrzése alatt.](overview.md)
1. Győződjön meg arról, hogy a **vállalati állapotroaming** engedélyezve van a bérlő számára az Azure AD-ben a [Vállalati állapotroaming engedélyezéséhez](enterprise-state-roaming-enable.md)című részben leírtak szerint. Engedélyezheti a barangolást az összes felhasználó, vagy csak egy kiválasztott felhasználói csoport számára.
1. A felhasználó hoz egy Azure Active Directory Premium-licencet.  
1. Az eszközt újra kell indítani, és a felhasználónak újra be kell jelentkeznie a vállalati állapotroaming funkcióinak eléréséhez.

## <a name="information-to-include-when-you-need-help"></a>A segítségkéréshez mellékelendő információk
Ha nem tudja megoldani a problémát az alábbi útmutatással, forduljon támogatási mérnökeinkhez. Amikor kapcsolatba lép velük, adja meg a következő információkat:

* **A hiba általános leírása**: Vannak-e hibaüzenetek a felhasználó által? Ha nem volt hibaüzenet, írja le részletesen a váratlan viselkedést. Milyen funkciók vannak engedélyezve a szinkronizáláshoz, és mit vár a felhasználó a szinkronizáláshoz? Több funkció nem szinkronizál, vagy egy- re van elkülönítve?
* **A felhasználók érintettek** – A szinkronizálás működik/nem működik egy felhasználó vagy több felhasználó esetében? Felhasználónként hány eszköz érintett? Mindegyik nem szinkronizál, vagy néhány közülük szinkronizálás, és néhány nem szinkronizál?
* **A felhasználóval kapcsolatos információk** – Milyen identitást használ a felhasználó az eszközre való bejelentkezéshez? Hogyan jelentkezik be a felhasználó az eszközre? A szinkronizált biztonsági csoport részét képezik? 
* **Az eszközre vonatkozó információk** – Ez az eszköz az Azure AD-hez vagy tartományhoz csatlakozott? Milyen builden van az eszköz? Melyek a legújabb frissítések?
* **Dátum / Idő / Időzóna** - Mi volt a pontos dátum és idő látta a hibát (tartalmazza az időzóna)?

Ezen információk megsegítése segít megoldani a problémát a lehető leggyorsabban.

## <a name="troubleshooting-and-diagnosing-issues"></a>Hibaelhárítás és a problémák diagnosztizálása

Ez a szakasz javaslatokat ad a vállalati állapotroaminggal kapcsolatos problémák elhárításához és diagnosztizálásához.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>A szinkronizálás ellenőrzése és a "Beállítások szinkronizálása" lap 

1. Miután csatlakozott a Windows 10-es számítógépéhez egy olyan tartományhoz, amely engedélyezze az Enterprise State Roaming szolgáltatást, jelentkezzen be a munkahelyi fiókjával. Nyissa meg a **Beállítások** > **fiókok** > **szinkronizálása A beállítások szinkronizálása lehetőséget,** és ellenőrizze, hogy a szinkronizálás és az egyes beállítások be vannak-e kapcsolva, és hogy a beállítások lap tetején megjelenik-e a munkahelyi fiókkal való szinkronizálás. Erősítse meg ugyanazt a fiókot is használják, mint a bejelentkezési fiókot **beállítások** > **számlák** > **Az info**. 
1. Az eredeti gépen végzett módosítások, például a tálca áthelyezése a képernyő jobb vagy felső oldalára történő áthelyezésével több gépen is működik-e. Nézze meg, hogy a változás öt percen belül terjed a második gépre. 

   * A képernyő zárolása és feloldása (Win + L) segíthet a szinkronizálás elindításában.
   * A szinkronizáláshoz mindkét számítógépen ugyanazzal a fiókkal kell bejelentkeznie – mivel az Enterprise State Roaming a felhasználói fiókhoz van kötve, nem pedig a gépfiókhoz.

**Lehetséges probléma**: Ha a **Beállítások** lap vezérlői nem érhetők el, és a következő üzenet jelenik meg: "Egyes Windows-szolgáltatások csak Microsoft-fiók vagy munkahelyi fiók használata esetén érhetők el." Ez a probléma előfordulhat, hogy az eszközök, amelyek be vannak állítva, hogy a tartományhoz csatlakozott és regisztrált az Azure AD,, de az eszköz még nem sikeresen hitelesített az Azure AD-ben. Ennek egyik lehetséges oka az, hogy az eszközházirendet alkalmazni kell, de ez az alkalmazás aszinkron módon történik, és néhány órával késhet. 

### <a name="verify-the-device-registration-status"></a>Az eszköz regisztrációs állapotának ellenőrzése

A vállalati állapotroaming megköveteli, hogy az eszköz regisztrálva legyen az Azure AD-vel. Bár nem kifejezetten az Enterprise State Roaming, az alábbi utasításokat követve segíthet megerősíteni, hogy a Windows 10-ügyfél regisztrálva van, és erősítse meg az ujjlenyomatot, az Azure AD-beállítások URL-címét, az NGC-állapotot és egyéb információkat.

1. Nyissa meg a parancssort nem emelt. Ehhez a Windows, nyissa meg a Run launcher (Win + R), és írja be a "cmd" megnyitásához.
1. Miután a parancssor megvan nyitva, írja be a "*dsregcmd.exe /status " parancsot.*
1. A várt kimenet esetén az **AzureAdJoined** mezőértéknek "YES", **a WamDefaultSet** mezőértéknek "IGEN"-nek kell lennie, és a **WamDefaultGUID** mezőértéknek a végén "(AzureAd)" guid azonosítónak kell lennie.

**Lehetséges probléma:** **A WamDefaultSet** és az **AzureAdJoined** egyaránt "NEM" értékkel rendelkezik a mezőértékben, az eszköz tartományhoz lett csatlakoztatva és regisztrálva az Azure AD-vel, és az eszköz nem szinkronizálódik. Ha ez jelenik meg, előfordulhat, hogy az eszköznek meg kell várnia a szabályzat alkalmazását, vagy az eszköz hitelesítése nem sikerült, amikor az Azure AD-hez csatlakozik. Előfordulhat, hogy a felhasználónak várnia kell néhány órát a házirend alkalmazására. Egyéb hibaelhárítási lépések közé tartozhat az automatikus regisztráció újbóli megkísérlése a kijelentkezés, majd a visszaírás, illetve a feladat indítása a Feladatütemezőben. Bizonyos esetekben a "*dsregcmd.exe /leave*" parancssori ablakban való futtatása, újraindítása és a regisztráció ismételt megkísérlése segíthet a probléma megoldásában.

**Potenciális probléma**: A **SettingsUrl** mezője üres, és az eszköz nem szinkronizálódik. Előfordulhat, hogy a felhasználó utoljára még azelőtt jelentkezett be az eszközre, hogy az Enterprise State Roaming engedélyezve lett volna az Azure Active Directory portalon. Indítsa újra az eszközt, és a felhasználó jelentkezzen be. Szükség esetén a portálon próbálja meg, hogy a rendszergazda keresse meg az **Azure Active Directory** > **eszközök** > **vállalati központi szolgáltatásának letiltását,** és engedélyezze **újra, hogy a felhasználók szinkronizálhatják a beállításokat és az alkalmazásadatokat az eszközök között.** Az újraengedélyezés után indítsa újra az eszközt, és jelentkezzen be a felhasználóval. Ha ez nem oldja meg a problémát, **a SettingsUrl** üres lehet, ha rossz eszköztanúsítvány van. Ebben az esetben a "*dsregcmd.exe /leave*" parancs használata egy emelt szintű parancssori ablakban, újraindítás és a regisztráció ismételt megkísérlése segíthet a probléma megoldásában.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Vállalati állapotú barangolás és többtényezős hitelesítés 

Bizonyos feltételek mellett a vállalati állapotroaming nem szinkronizálhatja az adatokat, ha az Azure többtényezős hitelesítés konfigurálva van. Ezekről a tünetekről további információt a [KB3193683.](https://support.microsoft.com/kb/3193683) 

**Potenciális probléma:** Ha az eszköz úgy van beállítva, hogy többtényezős hitelesítést igényeljen az Azure Active Directory portálon, előfordulhat, hogy nem szinkronizálja a beállításokat, miközben jelszóval jelentkezik be egy Windows 10-es eszközre. Az ilyen típusú többtényezős hitelesítési konfiguráció célja az Azure-rendszergazdai fiók védelme. A rendszergazda felhasználók továbbra is szinkronizálhatnak úgy, hogy bejelentkeznek a Windows 10-es eszközeikre a Microsoft Passport for Work PIN-kódjukkal, vagy kitölthetik a többtényezős hitelesítést, miközben más Azure-szolgáltatásokat, például az Office 365-öt is elérnek.

**Potenciális probléma**: A szinkronizálás sikertelen lehet, ha a rendszergazda konfigurálja az Active Directory összevonási szolgáltatások többtényezős hitelesítésfeltételes hozzáférési házirendjét, és az eszközön lévő hozzáférési jogkivonat lejár. Győződjön meg arról, hogy a Microsoft Passport for Work PIN-kóddal vagy a többtényezős hitelesítés sel jelentkezik be és jelentkezik ki, miközben más Azure-szolgáltatásokhoz, például az Office 365-höz is hozzáfér.

### <a name="event-viewer"></a>Eseménynapló

A speciális hibaelhárításhoz az Eseménynapló segítségével konkrét hibákat találhat. Ezeket az alábbi táblázat dokumentálja. Az események az Eseménynapló > alkalmazások és szolgáltatások naplói > **Microsoft** > **Windows** > **SettingSync-Azure** és a **Microsoft** > **Windows** > **AAD**szinkronizálásával kapcsolatos identitással kapcsolatos problémák között találhatók.

## <a name="known-issues"></a>Ismert problémák

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>A szinkronizálás nem működik olyan eszközökön, amelyeken az alkalmazások oldalirányúan vannak telepítve az MDM-szoftver használatával

A Windows 10 évfordulós frissítését (1607-es verzió) futtató eszközöket érinti. Az Eseménynaplóban a SettingSync-Azure naplók alatt a 80070259-es hibaazonosítójú 6013-as azonosító gyakran látható.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1607-es ügyfél rendelkezik az augusztus 23, 2016 kumulatív frissítéssel[(KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Az Internet Explorer kedvencek nem szinkronizálódnak

A Windows 10 novemberi frissítését (1511-es verzió) futtató eszközöket érinti.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1511-es ügyfél rendelkezik a 2016 júliusi kumulatív frissítéssel[(KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>A téma nem szinkronizál, valamint a Windows információvédelemmel védett adatok 

Az adatszivárgás megelőzése érdekében a [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) által védett adatok nem szinkronizálódnak a Vállalati állapotroaming szolgáltatáson keresztül a Windows 10 évfordulós frissítést használó eszközök számára.

**Javasolt művelet**  
Nincs. A Windows jövőbeli frissítései megoldhatják ezt a problémát.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>A dátum-, idő- és területbeállítások nem szinkronizálódnak a tartományhoz csatlakozó eszközön 
  
A tartományhoz csatlakozó eszközök szinkronizálása nem fog a Dátum, az Idő és a Régió beállításhoz: automatikus idő. Az automatikus idő használata felülírhatja a többi dátum-, idő- és területbeállítást, és a beállítások szinkronizálásának mellőzése miatt. 

**Javasolt művelet**  
Nincs. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Felhasználói fiókok felhasználói felületének –kérésea jelszavak szinkronizálásakor

A Windows 10 novemberi frissítését (1511-es verzió) futtató eszközöket a jelszavak szinkronizálására beállított vezeték nélküli hálózati adapterrel érinti.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1511-es ügyfél rendelkezik a kumulatív frissítéssel[(KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>A szinkronizálás nem működik olyan eszközökön, amelyek intelligens kártyát használnak a bejelentkezéshez

Ha intelligens vagy virtuális intelligens kártyával próbál bejelentkezni a Windows-eszközre, a beállítások szinkronizálása nem fog működni.     

**Javasolt művelet**  
Nincs. A Windows jövőbeli frissítései megoldhatják ezt a problémát.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>A tartományhoz csatlakozó eszköz nem szinkronizál, miután elhagyta a vállalati hálózatot     

Az Azure AD-be regisztrált tartományhoz csatlakozott eszközök szinkronizálási hibát tapasztalhatnak, ha az eszköz hosszabb ideig a helyszínen kívül van, és a tartományhitelesítés nem fejezhető be.

**Javasolt művelet**  
Csatlakoztassa az eszközt egy vállalati hálózathoz, hogy a szinkronizálás folytatódjon.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Az Azure AD-hez csatlakozott eszköz nem szinkronizál, és a felhasználó egy vegyes eset egyszerű felhasználónév.

Ha a felhasználó vegyes eset-UPN-t (például felhasználónév helyett felhasználónév) rendelkezik, és a felhasználó egy Azure AD-hez csatlakozó eszközön van, amely a Windows 10 Build 10586-ról 14393-ra frissített, előfordulhat, hogy a felhasználó eszköze nem szinkronizálódik. 

**Javasolt művelet**  
A felhasználónak le kell térnie az eszközhöz, majd újra csatlakoznia kell a felhőhöz. Ehhez jelentkezzen be helyi rendszergazdaként, és bontsa az eszköz csatlakoztatását a **Beállítások** > rendszer**beállítási lehetőségéhez,** **System** > és válassza a "Munkahelyi vagy iskolai kapcsolat kezelése vagy leválasztása" lehetőséget. Tisztítsa meg az alábbi fájlokat, majd az Azure AD Csatlakozzon ismét az eszközhöz a **Beállítások** > **rendszer** > **be,** és válassza a "Csatlakozás a munkához vagy az iskolához" lehetőséget. Folytassa az eszköz csatlakoztatását az Azure Active Directoryhoz, és fejezze be a folyamatot.

A karbantartás lépésben törölje a következő fájlokat:
- Settings.dat a`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- A mappa alatti összes fájl`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>6065-ös azonosítójú esemény: 80070533 Ez a felhasználó nem tud bejelentkezni, mert ez a fiók jelenleg le van tiltva  

Az Eseménynaplóban a SettingSync/Debug naplók alatt ez a hiba akkor látható, ha a felhasználó hitelesítő adatai lejártak. Emellett akkor fordulhat elő, ha a bérlő nem automatikusan azureRMS kiépített. 

**Javasolt művelet**  
Az első esetben a felhasználó frissítse a hitelesítő adatait, és jelentkezzen be az eszközre az új hitelesítő adatokkal. Az AzureRMS-probléma megoldásához folytassa a [KB3193791.](https://support.microsoft.com/kb/3193791) 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>1098-as azonosítójú esemény: Hiba: 0xCAA5001C Token broker művelet nem sikerült  

Az Eseménynaplóban az AAD/Operational naplók alatt ez a hiba az 1104-es esemény: AAD Cloud AP beépülő modul bekérő get token visszaadott hiba: 0xC000005F. Ez a probléma akkor fordul elő, ha hiányoznak az engedélyek vagy a tulajdonosi attribútumok.  

**Javasolt művelet**  
Folytassa a [KB3196528 kb3196528.](https://support.microsoft.com/kb/3196528)  

## <a name="next-steps"></a>További lépések

Áttekintést a [vállalati állami barangolás áttekintése című témakörben talál.](enterprise-state-roaming-overview.md)
