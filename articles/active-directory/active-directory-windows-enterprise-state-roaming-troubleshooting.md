---
title: "Az Azure Active Directoryban a vállalati Állapothordozás beállítások hibaelhárítása |} Microsoft Docs"
description: "Biztosítja a rendszergazdák feltett kérdésekre adott válaszok beállításait, valamint az alkalmazás adatszinkronizálás rendelkezhet."
services: active-directory
keywords: "Vállalati állapot barangolási beállításokat, a windows-felhő, gyakran ismételt kérdések a vállalati állapothordozás"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: 8ee3b523baf562b06bd5f7d652a431e1d4553d5c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>A vállalati Állapothordozás hibaelhárítási beállítások, az Azure Active Directoryban

Ez a témakör bemutatja, hogy miként problémákat a vállalati Állapothordozás diagnosztizálására és megoldására, és ismert problémák listája.

## <a name="preliminary-steps-for-troubleshooting"></a>Első lépések a hibaelhárításhoz 
Hibaelhárítás megkezdése előtt győződjön meg arról, hogy a felhasználó és eszköz rendelkezik megfelelően konfigurálva, és, hogy az adott vállalati Állapothordozás követelményeinek teljesülnek-e az eszköz és a felhasználó által. 

1. Windows 10, a legújabb frissítéseket, és egy minimális 1511-es verziója (operációsrendszer-verzióval 10586 vagy újabb) telepítve van az eszközön. 
2. Az eszköz az Azure AD csatlakoztatva, vagy hibrid az Azure AD-tartományhoz. További információkért lásd: [hogyan kérhet egy eszközt az Azure AD felügyelete alatt](device-management-introduction.md).
3. Győződjön meg arról, hogy **a vállalati Állapothordozás** engedélyezve van a bérlő számára az Azure ad-ben leírtak [a vállalati Állapothordozás engedélyezése](active-directory-windows-enterprise-state-roaming-enable.md). Engedélyezheti a központi, az összes felhasználó számára, vagy csak a kiválasztott csoport számára.
4. A felhasználó már kell hozzárendelni egy Azure Active Directory Premium licenc.  
25. Az eszköz újra kell indítani, és a felhasználónak ismét be kell jelentkeznie a vállalati Állapothordozás funkciók eléréséhez.

## <a name="information-to-include-when-you-need-help"></a>Ha segítségre van szüksége információk
Ha nem tudja megoldani a problémát az alábbi útmutatást, forduljon a Microsoft terméktámogatási szakemberek. Amikor kapcsolatba lép a őket, a következő információkat tartalmazza:

* **A hiba leírása általános**: vannak-e a felhasználó által látott hibaüzenetek? Ha nincs hibaüzenet, a nem várt viselkedést első fellépése, részletes leírása Mely szolgáltatások engedélyezve vannak a szinkronizálási szolgáltatás, és mi az a felhasználó szinkronizálása a rendszer? Több funkció nem szinkronizálás folyamatban, vagy azt elkülönül egy?
* **Az érintett felhasználók** – szinkronizálás működő vagy sikertelen egy felhasználó vagy több felhasználó van? Eszközök számáról játszó felhasználónként? Az összes nem szinkronizálása vagy némelyikük szinkronizálását, és néhány nem szinkronizálása?
* **A felhasználó adatai** – milyen identitás az a felhasználó bejelentkezni az eszköz használatával? Hogyan van a felhasználó bejelentkezik az eszközt? Vannak engedélyezett a szinkronizálás kijelölt biztonsági csoport egy részének? 
* **Az eszköz adatai** – az Azure AD-tartományhoz, illetve tartományhoz csatlakoztatott eszköz? Milyen build van az eszköz? Mik a legújabb frissítéseket?
- **Dátum és idő / időzóna típusú** – mi volt a pontos dátum és idő, a hiba látott (beleértve az időzóna)?

Például ez az információ segít nekünk minél gyorsabban tudja megoldani a problémát.

## <a name="troubleshooting-and-diagnosing-issues"></a>Hibaelhárítás és a problémák diagnosztizálása
Ez a szakasz a vállalati Állapothordozás kapcsolatos problémák diagnosztizálásához és javaslatokat nyújt.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Ellenőrizze a szinkronizálás és a "Beállítások szinkronizálása" beállítások lap 

1. Windows 10 rendszerű számítógép csatlakoztatása egy tartományhoz, amely konfigurálva van a vállalati Állapothordozás engedélyezése, után jelentkezzen munkahelyi fiókját. Ugrás a **beállítások** > **fiókok** > **a szinkronizálási beállítások** , és győződjön meg arról, hogy szinkronizálás és az egyéni beállításokat, és, hogy a beállítások oldal azt jelzi, hogy szinkronizálását végzi a munkahelyi fiókjával. Ellenőrizze, hogy ugyanazt a fiókot is használja a bejelentkezési fiókként **beállítások** > **fiókok** > **a információ**. 
2. Győződjön meg arról, hogy szinkronizálás azáltal, hogy néhány módosítást az eredeti számítógép, például a tálca áthelyezése a képernyő jobb oldali vagy felső oldalán a működik több számítógépen. Tekintse meg a változás propagálása a második géphez öt percen belül. 
  * Zárolás és a képernyő (Win + L) segítségével szinkronizációját feloldása.
  * Meg kell kell bejelentkezni a szinkronizáláshoz mindkét számítógépeken működéséhez – ugyanazt a fiókot, a vállalati Állapothordozás kapcsolódna a felhasználói fiók és a nem a számítógépfiókot.

**Lehetséges probléma**: Ha a vezérlőelemek a **beállítások** lap nem érhetők el, és láthatja az üzenet "egyes Windows-szolgáltatások csak érhető el, ha egy Microsoft-fiók vagy a munkahelyi fiókot használ." A probléma esetleg felmerülhetnek, az eszközök, amelyek akár tartományhoz csatlakoztatott és a regisztrált az Azure AD, de az eszköz még nem még sikeresen végzett hitelesítést az Azure ad Szolgáltatásba. Egy lehetséges oka, hogy az eszköz szabályzatot kell alkalmazni, de ez az alkalmazás aszinkron módon történik, és néhány órával késhet. 

### <a name="verify-the-device-registration-status"></a>Az eszköz regisztrációs állapotának ellenőrzése
A vállalati Állapothordozás használatához az eszközt regisztrálni kell az Azure AD. Bár a nem kizárólag a vállalati Állapothordozás, az alábbi utasításokat követve segítségével győződjön meg arról, hogy regisztrálva van-e a Windows 10-ügyfeleknek, és erősítse meg az ujjlenyomatot, az Azure AD beállításai URL-CÍMÉT, NGC állapotát, és más információkat.

1.  Nyissa meg a megvont jogosultságú parancssort. Ehhez a Windows, nyissa meg a Futtatás indító (Win + K), és írja be a "cmd" lehetőségre.
2.  Ha nyissa meg a parancssort, írja be a "*dsregcmd.exe/status*".
3.  Várt kimenet a **AzureAdJoined** mező értéknek kell lennie a "YES", **WamDefaultSet** mező értéknek kell lennie a "YES", és a **WamDefaultGUID** mező értékének kell végén a "(AzureAd)" GUID.

**Lehetséges probléma**: **WamDefaultSet** és **AzureAdJoined** is rendelkezik "a nem" értéket, az eszköz tartományhoz és az Azure AD-ben regisztrált, és az eszköz nem szinkronizálja. Ez látható, ha az eszköz lehet alkalmazni kívánt házirend várnia kell, vagy az eszköz a hitelesítés sikertelen, az Azure AD-csatlakozás során. Várjon néhány órát kell alkalmazni a házirendet a felhasználó lehet. További hibaelhárítási lépéseket tartalmazhatnak automatikus regisztráció újrapróbálkozás kijelentkezni, majd ismét az által, vagy a feladatot a Feladatütemező indítása. Néhány esetben fut "*dsregcmd.exe /leave*" egy rendszergazda jogú parancssori ablakban újraindul, majd próbálkozzon újra a regisztrációs segíthet a probléma megoldásához.


**Lehetséges probléma**: A mező **AzureAdSettingsUrl** üres, és az eszköz nem szinkronizálja. A felhasználó előfordulhat, hogy rendelkezik utoljára bejelentkezett az eszközt az Azure Active Directory portálon a vállalati Állapothordozás engedélyezése előtt. Indítsa újra az eszközt, és a felhasználói bejelentkezési adatokkal. Másik lehetőségként a portálon, próbálja rendelkezik az informatikai rendszergazda letiltását és újraengedélyezését a felhasználók is szinkronizálási beállítások és a vállalati alkalmazásadatok. Ha újra engedélyezi, indítsa újra az eszközt, és a felhasználói bejelentkezési adatokkal. Ha ez nem oldja meg a problémát, **AzureAdSettingsUrl** rossz eszköz tanúsítvány esetében üres is lehet. Ebben az esetben fut "*dsregcmd.exe /leave*" egy rendszergazda jogú parancssori ablakban újraindul, majd próbálkozzon újra a regisztrációs segíthet a probléma megoldásához.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>A vállalati Állapothordozás és a többtényezős hitelesítés 
Bizonyos körülmények között a vállalati Állapothordozás is tudni szinkronizálni az adatokat, ha az Azure többtényezős hitelesítés van beállítva. Ezek a problémák a további részletekért lásd: a támogatási dokumentum [KB3193683](https://support.microsoft.com/kb/3193683). 

**Lehetséges probléma**: Ha az eszköz az Azure Active Directory portálon többtényezős hitelesítés megkövetelése van konfigurálva, akkor lehetséges, hogy nem beállítások szinkronizálása közben jelszót Windows 10-eszközre való bejelentkezéskor. A multi-factor Authentication-konfigurációt az ilyen típusú kívánják védeni az Azure rendszergazdai fiókkal. Előfordulhat, hogy rendszergazda felhasználók továbbra is jelentkezik be a Windows 10-eszközökre, a Microsoft Passport for Work PIN KÓDJÁNAK vagy Office 365-höz hasonló más Azure-szolgáltatások használata közben a multi-factor Authentication elvégzésével szinkronizálása.

**Lehetséges probléma**: szinkronizálása sikertelen lehet, ha a rendszergazda konfigurálja az Active Directory összevonási szolgáltatások multi-factor Authentication feltételes hozzáférési szabályzatot, és az eszközön a hozzáférési jogkivonat lejár. Győződjön meg arról, hogy jelentkezzen be, és jelentkezzen ki, a Microsoft Passport for Work PIN KÓDJÁNAK használatával, vagy végezze el a multi-factor Authentication Office 365-höz hasonló más Azure-szolgáltatások elérése közben.

###<a name="event-viewer"></a>Eseménynapló
Speciális hibaelhárítás Eseménynapló segítségével bizonyos hibákat található. Ezek az alábbi táblázat ismerteti. Az eseményeket az Eseménynapló található > alkalmazások és szolgáltatásnaplók > **Microsoft** > **Windows** > **SettingSync** és az identitás-hibák a szinkronizálási **Microsoft** > **Windows** > **az Azure AD**.


## <a name="known-issues"></a>Ismert problémák

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Szinkronizálás nem működik az eszközökre, amelyeken alkalmazások közvetlenül telepített-kezelési szoftver használatával

A Windows 10 évforduló frissítés (verzió 1607) rendszert futtató eszközökre vonatkozik. Az eseménynaplóban a SettingSync-Azure naplók alatt az esemény azonosítója 6013 80070259 hibával gyakran előforduló.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1607 ügyfél a 23 megjelenésével 2016 augusztusától összegző frissítést ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Internet Explorer Kedvencek szinkronizálja a

A Windows 10 November frissítés (1511-es verziójú) rendszert futtató eszközökre vonatkozik.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1511 ügyfél a július 2016 összegző frissítést ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Téma nem szinkronizálása, valamint az adatok védelme a Windows információvédelem 

A védett adatok adatszivárgás megelőzése érdekében [Windows információvédelem](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) keresztül a vállalati Állapothordozás évforduló frissítés Windows 10-es eszközök nem szinkronizálja.



**Javasolt művelet**  
Nincs. A Windows jövőbeli frissítései megoldhatja a problémát.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Dátum, idő és terület beállítások szinkronizálja a tartományhoz csatlakoztatott eszközön 
  
Tartományhoz csatlakozó eszközök nem fog tapasztalni, szinkronizálás, a beállítás dátum, idő és terület: automatikus idő. Automatikus idő felülbírálhatják a más dátum, idő és terület beállításait, és ezeket a beállításokat nem szinkronizálandó vezethet. 

**Javasolt művelet**  
Nincs. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>A felhasználói fiókok felügyelete kéri, jelszavak szinkronizálásakor

Hatással van a Windows 10 November frissítés (1511-es verzió) rendszerű eszközöket a vezeték nélküli hálózati, amely szinkronizálja a jelszavakat.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10 v1511 ügyfél összegző frissítése ([KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Szinkronizálás nem működik az intelligens kártyás bejelentkezés használó eszközök
Jelentkezzen be a Windows-eszköz intelligens kártya vagy virtuális intelligens kártya használatával kísérli meg, ha a szinkronizálási beállítások fog tovább működni.     

**Javasolt művelet**  
Nincs. A Windows jövőbeli frissítései megoldhatja a problémát.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Vállalati hálózat elhagyása után nem szinkronizál-tartományhoz csatlakoztatott eszközön     
Tartományhoz csatlakozó eszközök regisztrálása az Azure AD-szinkronizálás hibája tapasztalhat, ha az eszköz telephelytől távoli huzamosabb ideig, és nem tudja végrehajtani a tartományi hitelesítéshez.

**Javasolt művelet**  
Csatlakoztassa az eszközt a vállalati hálózathoz, hogy a szinkronizálási folytathatja.

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Az Azure AD csatlakoztatott eszköz nem szinkronizálja, és a felhasználó rendelkezik-e egy vegyes eset egyszerű felhasználónév.
 Ha a felhasználó rendelkezik-e egy nagybetű UPN (pl. a felhasználónév helyett felhasználónév) és a felhasználó egy Azure AD csatlakoztatott eszközön, amely a Windows 10-Build 10586 14393 frissítette, a felhasználó-eszköz szinkronizálása sikertelen lehet. 

**Javasolt művelet**  
A felhasználó kell elhagyása, és csatlakozzon újra az eszköz a felhőre. Ehhez jelentkezzen be a helyi rendszergazdai felhasználó, és nyissa meg az eszköz elhagyása **beállítások** > **rendszer** > **kapcsolatos** , és jelölje ki "kezelése, vagy válassza le a munkahelyi vagy iskolai". Az alábbi fájlokat, majd az Azure AD Join az eszközt újra tisztítása **beállítások** > **rendszer** > **kapcsolatos** , majd válassza a "Csatlakozás a munkahelyi vagy iskolai". Továbbra is az eszköz csatlakoztatása az Azure Active Directory és a folyamat befejezéséhez.

A Lemezkarbantartó lépésben karbantartása a következő fájlokat:
- A Settings.dat`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- A mappában található összes fájl`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Eseményazonosító 6065:80070533 a felhasználók nem jelentkezhetnek be, mert ez a fiók jelenleg le van tiltva.  
A SettingSync/hibakeresési naplók az eseménynaplóban Ez a hiba látható, amikor a felhasználó hitelesítő adatainak tanúsítványa lejárt. Az emellett akkor fordulhat elő, amikor a bérlő automatikusan nincs kiépítve AzureRMS. 

**Javasolt művelet**  
Az első esetben a felhasználónak a hitelesítő adatokat és a bejelentkezési azonosító frissítése az eszközre, az új hitelesítő adatokkal rendelkezik. A AzureRMS probléma megoldásához, folytassa a felsorolt [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Eseményazonosító 1098: Hiba: 0xCAA5001C Token broker művelet végrehajtása sikertelen volt  
Az eseménynaplóban az aad-ben/műveleti naplói alatt, ez a hiba lehetséges, hogy láthatók az esemény 1104: az AAD-felhő AP beépülő modul hívás Get tokent hibát adott vissza: 0xC000005F. Ez a probléma akkor fordul elő, ha nincs engedélyeket, vagy tulajdonjogot attribútumok hiányzik.  

**Javasolt művelet**  
A felsorolt lépéseket folytatásához [KB3196528](https://support.microsoft.com/kb/3196528).  



## <a name="next-steps"></a>További lépések

- Használja a [User Voice fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) visszajelzést, és javaslatokat a vállalati Állapothordozás javítására.

- További információkért lásd: a [vállalati Állapothordozás áttekintése](active-directory-windows-enterprise-state-roaming-overview.md). 

## <a name="related-topics"></a>Kapcsolódó témakörök
* [Vállalati állapotának központi áttekintése](active-directory-windows-enterprise-state-roaming-overview.md)
* [Az Azure Active Directoryban a vállalati állapothordozás engedélyezése](active-directory-windows-enterprise-state-roaming-enable.md)
* [Beállítások és adatok hordozása – gyakori kérdések](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Csoport házirend és a mobileszköz-kezelési beállítások beállítások szinkronizálási szolgáltatás](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 központi beállításainak ismertetése](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
