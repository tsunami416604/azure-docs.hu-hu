---
title: Hibaelhárítás az Azure Active Directoryban Enterprise State Roaming beállítások |} A Microsoft Docs
description: Biztosít a rendszergazdák feltett kérdésekre adott válaszokat beállításairól és az alkalmazás data Sync szolgáltatással rendelkezhet.
services: active-directory
keywords: Vállalati állapot barangolási beállításokat, a windows-felhő, vállalati állapothordozás kapcsolatos gyakori kérdésekre
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: devices
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: ef10f45cbe02bd364563be4b0d62f9c491848452
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097944"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Az Azure Active Directoryban hibaelhárítási Enterprise State Roaming beállítások

Ez a témakör információt nyújt az Enterprise State Roaming problémák diagnosztizálására és megoldására, és ismert problémák listáját tartalmazza.

## <a name="preliminary-steps-for-troubleshooting"></a>Első lépések a hibaelhárításhoz 

Hibaelhárítás megkezdése előtt győződjön meg arról, hogy a felhasználó és eszköz konfigurált megfelelően, és, hogy a követelményeinek, Enterprise State Roaming teljesülnek-e az eszköz és a felhasználó által. 

1. Windows 10-es, a legújabb frissítéseket, és a egy minimális 1511-es verziója (operációs rendszer buildszáma 10586 vagy újabb) telepítve van az eszközön. 
1. Az eszköz kapcsolódik az Azure AD tartományhoz kell csatlakoznia, vagy hibrid Azure AD-csatlakoztatott. További információkért lásd: [beszerzése egy eszközt az Azure AD vezérlése alá](overview.md).
1. Ügyeljen arra, hogy **Enterprise State Roaming** engedélyezve van a bérlő Azure AD-ben leírtak szerint [ahhoz Enterprise State Roaming](enterprise-state-roaming-enable.md). Engedélyezheti a barangolás, az összes felhasználó számára, vagy csak felhasználók kiválasztott csoportja számára.
1. A felhasználó már kell rendelni egy Azure Active Directory Premium-licencet.  
1. Az eszköz újra kell indítani, és a felhasználónak be kell jelentkeznie a újra Enterprise State Roaming funkcióhoz férhet hozzá.

## <a name="information-to-include-when-you-need-help"></a>Ha segítségre van szüksége adatokat
Ha az alábbi útmutatót a probléma nem oldódik meg, forduljon a támogatási. Amikor kapcsolatba lép a őket, az alábbi információkat:

* **Általános hibaleírást**: vannak-e a felhasználó által látott hibaüzenetek? Ha ott nem hibaüzenet nélküli hibát, a nem várt viselkedést, hogy észrevette a részletes leírása Milyen funkciók engedélyezettek a szinkronizálási szolgáltatás, és mi az a felhasználó szinkronizálása várt? Van több funkció nem szinkronizálja, vagy azt elkülönül egy?
* **Érintett felhasználók** – szinkronizálási működő/sikertelen egy felhasználó vagy több felhasználó van? Hány eszközt felhasználónként van szó? Nem szinkronizálja ezek mindegyike vagy némelyike szinkronizálását, és néhány nem szinkronizálja?
* **A felhasználó adatai** – milyen identitás az a felhasználó, jelentkezzen be az eszköz használatával? Hogyan, a felhasználó bejelentkezik az eszközre? Azok azok egy része számára engedélyezett a szinkronizálás a kijelölt biztonsági csoport? 
* **Az eszköz adatai** – az eszköz Azure AD-hez csatlakoztatott vagy tartományhoz csatlakoztatott? Milyen build van az eszköz? Mik a legújabb frissítéseket?
- **Dátum / idő / időzóna** – mi volt a pontos dátum és idő, a hibaüzenet vonatkozott (beleértve az időzónát)?

Például ez az információ segít megoldani a problémát a lehető leggyorsabban.

## <a name="troubleshooting-and-diagnosing-issues"></a>Hibaelhárítás és a problémák diagnosztizálása
Ez a szakasz hogyan Enterprise State Roaming kapcsolatos problémák diagnosztizálására és javaslatokat nyújt.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Ellenőrizze a szinkronizálás és a "Beállítások szinkronizálása" beállítások lap 

1. Csatlakozás a Windows 10 rendszerű Számítógépeken az olyan tartományhoz, amely engedélyezi a vállalati Állapothordozás, után jelentkezzen be munkahelyi fiókjával. Lépjen a **beállítások** > **fiókok** > **a szinkronizálási beállítások** és győződjön meg róla, hogy szinkronizálás és az egyéni beállításokat, és hogy felső részén a beállítások lap azt jelzi, hogy a munkahelyi fiókjával való szinkronizálását. Ellenőrizze, hogy ugyanazt a fiókot is használja a bejelentkezési fiókját **beállítások** > **fiókok** > **az adatok**. 
1. Ellenőrizze a Szinkronizáló működését több gép közötti azáltal, hogy néhány módosítást az eredeti gépen, például a tálca áthelyezése a képernyő jobb vagy felső részén. Figyelje meg, a második gép öt percen belül propagálja a módosítást. 

  * Zárolás és a zárolás feloldásához (Win + L) a képernyő a egy szinkronizálási események indítása segíthetnek.
  * Meg kell jelentkezett ugyanazzal a fiókkal, a szinkronizáláshoz mindkét számítógépeken működnek –, a felhasználói fiók és a gép fiók nem Enterprise State Roaming kötődik.

**Lehetséges probléma**: Ha a vezérlőelemek a **beállítások** lap nem érhető el, és az üzenetet látja, "egyes Windows-szolgáltatások érhetők el csak egy Microsoft-fiókjával vagy a munkahelyi fiók használata." A probléma esetlegesen felmerülő eszközökhöz, amelyek akár tartományhoz csatlakoztatott és a regisztrált az Azure ad-hez, de az eszköz még nem még sikeresen hitelesítést az Azure ad-hez. Egy lehetséges oka, hogy az eszköz szabályzatot kell alkalmazni, de ez az alkalmazás aszinkron módon történik, és néhány órával késhet. 

### <a name="verify-the-device-registration-status"></a>Az eszköz regisztrációs állapotának ellenőrzése

Az eszköz regisztrálása az Azure ad-vel Enterprise State Roaming igényel. Bár nem kifejezetten az Enterprise State Roaming, az alábbi utasításokat követve segítségével győződjön meg arról, hogy a Windows 10-es ügyfél regisztrálva van, és erősítse meg az ujjlenyomatát, az Azure AD-beállítások URL-CÍMÉT, NGC állapotát, és egyéb információkat.

1.  Nyissa meg a megvont jogosultságú parancssort. Ehhez a Windows, nyissa meg a Futtatás indítója (Win + R), és írja be a "cmd" megnyitásához.
2.  A parancssor megnyitása után írja be a "*dsregcmd.exe/status*".
3.  Várt kimenet a **AzureAdJoined** mező értéke legyen "Igen" **WamDefaultSet** mező értéke legyen "Igen" és a **WamDefaultGUID** mező értékét egy GUID Azonosítót kell lennie. a "(Azure ad)" végén.

**Lehetséges probléma**: **WamDefaultSet** és **AzureAdJoined** egyaránt mező értéke "Nem" rendelkeznek, az eszköz volt a tartományhoz csatlakoztatott és az Azure AD-ben regisztrált és nem szinkronizálja az eszköz. Ez látható, ha az eszköz is kell várnia a alkalmazni lehessen a házirend vagy az eszköz a hitelesítés sikertelen, amikor csatlakozik az Azure ad-ben. A felhasználó jogosult az a alkalmazni lehessen a szabályzat néhány óráig várnia. További hibaelhárítási lépéseket tartalmazhatnak automatikus regisztráció újrapróbálkozás kijelentkezés és a biztonsági, illetve a feladat a Feladatütemező indítása. Bizonyos esetekben futtatása "*dsregcmd.exe /leave*" egy rendszergazda jogú parancssori ablakban, a rendszer újraindítása, és próbálkozzon újra a regisztrációs segíthet a probléma megoldásához.


**Lehetséges probléma**: mezőjére **SettingsUrl** üres és nem szinkronizálja az eszközt. A felhasználó előfordulhat, hogy utoljára bejelentkezett az eszközt ahhoz Enterprise State Roaming engedélyezve lett az Azure Active Directory portálon. Indítsa újra az eszközt, és a felhasználói bejelentkezési rendelkezik. Szükség esetén a portálon, próbálja letiltását és újraengedélyezését, felhasználók, előfordulhat, hogy szinkronizálási beállítások és a vállalati alkalmazások adatainak rendszergazdának kellene. Ha újra engedélyezi, indítsa újra az eszközt, és a felhasználói bejelentkezési rendelkezik. Ha ez nem oldja meg a problémát, **SettingsUrl** hibás eszköz tanúsítvány esetén üres is lehet. Ebben az esetben fut "*dsregcmd.exe /leave*" egy rendszergazda jogú parancssori ablakban, a rendszer újraindítása, és próbálkozzon újra a regisztrációs segíthet a probléma megoldásához.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming és a multi-factor Authentication hitelesítés 

Bizonyos körülmények között az Enterprise State Roaming sikertelen lehet szinkronizálja az adatokat, ha az Azure multi-factor Authentication hitelesítés van konfigurálva. További részleteket az alábbi tüneteket figyelje, tekintse meg a támogatási dokumentum [KB3193683](https://support.microsoft.com/kb/3193683). 

**Lehetséges probléma**: Ha az eszköz az Azure Active Directory portálon a többtényezős hitelesítés megkövetelése van konfigurálva, előfordulhat, hogy átadja a beállítások szinkronizálása közben jelentkezik be egy Windows 10 rendszerű eszköz jelszó használatával. Multi-factor Authentication konfigurációjától az ilyen típusú célja egy Azure-rendszergazdai fiók védelme érdekében. Rendszergazda felhasználók továbbra is lehet tudni bejelentkezni a Windows 10-es eszközeiket a Microsoft Passport for Work PIN-KÓDJÁNAK vagy többtényezős hitelesítés elvégzése közben: más Azure-szolgáltatásokhoz az Office 365-höz hasonló szinkronizálni.

**Lehetséges probléma**: szinkronizálás meghiúsulhat, ha a rendszergazda konfigurálja az Active Directory összevonási szolgáltatások multi-factor Authentication feltételes hozzáférési szabályzatot, és az eszközön a hozzáférési jogkivonat lejár. Győződjön meg arról, hogy jelentkezzen be, és jelentkezzen ki, használja a Microsoft Passport for Work PIN-kód, vagy fejezze be a multi-factor Authentication az Office 365-höz hasonló más Azure-szolgáltatások használata közben.

### <a name="event-viewer"></a>Eseménynapló

Speciális hibaelhárításhoz, az Eseménynapló használható hibaüzenetek megkereséséhez. Ezek az alábbi táblázat ismerteti. Az eseményeket az Eseménynapló területen található > alkalmazások és szolgáltatásnaplók > **Microsoft** > **Windows** > **SettingSync Azure-beli** és identitással kapcsolatos problémáinak szinkronizálási **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Ismert problémák

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Szinkronizálás nem működik az eszközökön, az alkalmazások oldalon be mobileszköz-kezelési szoftver használatával

A Windows 10 Évfordulós frissítés (1607-es verzió) rendszerű eszközökre van hatással. Az Event ID 6013 80070259 hiba az eseménynaplóban a SettingSync-Azure naplók alatt, gyakran látható.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10-es v1607 ügyfél rendelkezik a 2016. augusztus 23-összegző frissítés ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Az Internet Explorer Kedvencek szinkronizálja a

A Windows 10. novemberi frissítés (1511-es verzió) rendszerű eszközökre van hatással.

**Javasolt művelet**  
Győződjön meg arról, hogy a Windows 10-es v1511 ügyfél rendelkezik a 2016. július összegző frissítés ([KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Téma nem szinkronizálja, valamint a Windows Information Protection által védett adatok 

A védett adatokhoz adatszivárgás megelőzése érdekében [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) nem szinkronizálja az Enterprise State Roaming keresztül a Windows 10 Évfordulós frissítést használó eszközök esetében.

**Javasolt művelet**  
Nincs. Windows jövőbeli frissítései megoldhatja a problémát.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Dátum, idő és a területi beállítások nem szinkronizálhatók a tartományhoz csatlakoztatott eszköz 
  
Tartományhoz csatlakoztatott eszközök szinkronizálása a beállítás dátum, idő és a régió nem fog tapasztalni: automatikus idő. Automatikus ideje a más dátum, idő és régió beállításainak felülbírálása, és ezeket a beállításokat a szinkronizálni kívánt nem okoznak. 

**Javasolt művelet**  
Nincs. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>A felhasználói fiókok felügyelete kéri, jelszavak szinkronizálása során

Hatással van a Windows 10. novemberi frissítés (1511-es verzió) rendszerű eszközökhöz, egy vezeték nélküli hálózati adapterrel van konfigurálva szinkronizálja a jelszavakat.

**Javasolt művelet**  
Ellenőrizze, hogy a Windows 10-es v1511 ügyfél rendelkezik a kumulatív frissítés ([KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Szinkronizálás nem működik az intelligens kártyás bejelentkezés használó eszközök

Ha próbál bejelentkezni a Windows-eszközök egy intelligens kártya vagy virtuális intelligens kártya, a szinkronizálási beállítások fognak tovább működni.     

**Javasolt művelet**  
Nincs. Windows jövőbeli frissítései megoldhatja a problémát.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Vállalati hálózat elhagyása után nem szinkronizálja a tartományhoz csatlakoztatott eszköz     

Regisztrált az Azure AD-tartományhoz csatlakozott eszközökkel szinkronizálási hiba tapasztalhat, ha az eszköz nem külső helyszínen lévő huzamosabb ideig, és nem tudja végrehajtani a tartományi hitelesítéshez.

**Javasolt művelet**  
Csatlakoztassa az eszközt a vállalati hálózathoz, hogy a szinkronizálási folytathatja.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Az Azure AD-csatlakoztatás eszköz nem szinkronizálja, és a felhasználó rendelkezik-e a vegyes megkülönbözteti a kis egyszerű felhasználónév.

Ha a felhasználó rendelkezik, egy nagybetű UPN (pl. helyett a username felhasználónév) és a felhasználó frissítette a Windows 10-Build 10586 14393, amely egy Azure AD-csatlakoztatás eszközön, a felhasználó-eszköz szinkronizálása sikertelen lehet. 

**Javasolt művelet**  
A felhasználónak kell elhagyása, és csatlakozzon újra az eszközt a felhőbe. Ebben az esetben jelentkezzen be a helyi rendszergazdai felhasználó, és nyissa meg az eszköz elhagyása **beállítások** > **rendszer** > **kapcsolatos** , és válassza ki a "kezelés vagy válassza le a munkahelyi vagy iskolai rendszerhez". A fájlok közül, majd az Azure AD Join az eszköz ismét a karbantartás **beállítások** > **rendszer** > **kapcsolatos** , és válassza "munkahelyi csatlakozás vagy Iskolai". Továbbra is az eszköz csatlakoztatása az Azure Active Directory és a folyamat befejezése.

A törlési lépésben a következő tisztítás fájlok:
- A Settings.dat `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- A mappában található összes fájl `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Ez a felhasználó nem tud bejelentkezni, mert ez a fiók jelenleg le van tiltva 6065:80070533-es Azonosítójú esemény  

Az eseménynaplóban alatt SettingSync és hibakeresési naplók Ez a hiba látható, ha a felhasználói hitelesítő adatok lejártak. Ezenkívül azt előfordulhatnak, ha a bérlő automatikusan nem rendelkezett AzureRMS kiépítve. 

**Javasolt művelet**  
Az első esetben a felhasználó hitelesítő adatait, és a bejelentkezési frissítése az eszközhöz az új hitelesítő adatokkal rendelkezik. A AzureRMS probléma megoldásához, folytassa a felsorolt lépéseket [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Eseményazonosító 1098: Hiba: 0xCAA5001C jogkivonat broker művelet sikertelen volt  

Az eseménynaplóban az AAD/műveleti naplók alapján ez a hiba lehetséges, hogy láthatók az esemény 1104: AAD-felhő AP beépülő modul hívása Get token a következő hibát: 0xC000005F. Ez a probléma akkor fordul elő, ha vannak, hiányzó engedélyeket vagy tulajdonosi attribútumokkal.  

**Javasolt művelet**  
Folytassa a felsorolt lépésekkel [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>További lépések

Áttekintéséhez lásd: [vállalati állapothordozás áttekintése](enterprise-state-roaming-overview.md).
