---
title: Képességek az Azure MFA - Azure Active Directory biztosításához használja a meglévő hálózati házirend-kiszolgálók
description: Felhőalapú kétlépéses ellenőrzési-képességeket adhat a meglévő hitelesítési infrastruktúrájának
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95d19068e482722bf6cd01e44d27c2719bc419a3
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564531"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>A meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication

A hálózati házirend-kiszolgáló (NPS) kiterjesztése az Azure MFA felhőalapú MFA képességek hozzáadása a meglévő kiszolgálók használatával hitelesítési infrastruktúráját. NPS bővítményével is hozzáadhat telefonhívás, szöveges üzenet vagy telefonos alkalmazás ellenőrzés a meglévő hitelesítési folyamat telepítése, konfigurálása és karbantartása az új kiszolgálók nélkül. 

Ez a bővítmény olyan szervezeteknek, amelyek számára védelmet kíván biztosítani a VPN-kapcsolatok az Azure MFA-kiszolgáló üzembe helyezése nélkül lett létrehozva. Az NPS-bővítményének eleget kell tennie az adapter beállításai között, adja meg a hitelesítés második tényezőjét, a RADIUS- és Azure MFA felhőalapú összevont vagy a szinkronizált felhasználók.

Az Azure MFA NPS bővítményével használatakor a hitelesítési folyamat a következő összetevőket tartalmazza: 

1. **NAS/VPN-kiszolgáló** kéréseket fogad VPN-ügyfelek és a hálózati házirend-kiszolgálók a RADIUS-kérelmeket alakítja azokat. 
2. **Hálózati házirend-kiszolgáló** a RADIUS-kéréseket az elsődleges hitelesítés végrehajtásához az Active Directory csatlakozik, és minden telepített bővítmények, sikeres befejezés esetén továbbítja.  
3. **NPS-bővítményének** elindítja a másodlagos hitelesítésre az Azure MFA kérést. Ha a bővítmény megkapja a választ, és ha sikeres az MFA-hitelesítést, a hálózati házirend-kiszolgáló azáltal, hogy a biztonsági jogkivonatokat, amelyek tartalmazzák az MFA-jogcímet a hitelesítési kérelem befejeződött, Azure STS által kiadott.  
4. **Az Azure MFA** kommunikál az Azure Active Directoryval, a felhasználói adatok beolvasása és a egy ellenőrzési módszert úgy konfigurálva, hogy a felhasználó használja a másodlagos hitelesítést hajt végre.

A következő ábra szemlélteti a magas szintű hitelesítési kérelem folyamat: 

![Hitelesítési folyamatábrája](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

Az NPS-bővítményt a redundancia, automatikusan kezeli, így Önnek nem kell speciális konfigurációra.

Tetszőleges számú Azure MFA-kompatibilis hálózati házirend-kiszolgálók igény szerint hozhat létre. Ha több kiszolgálót is telepít, a különbség az ügyféltanúsítványt a velük kell használnia. Minden egyes kiszolgáló egy tanúsítvány létrehozása azt jelenti, hogy minden egyes tanúsítvány frissítheti egyenként, és nem kell foglalkoznia állásidő a kiszolgálók közötti.

A VPN-kiszolgálók irányíthatja a hitelesítési kéréseket, így azok kell figyelembe venni az új Azure MFA-kompatibilis hálózati házirend-kiszolgálókat.

## <a name="prerequisites"></a>Előfeltételek

Az NPS-bővítményének hivatott meglévő infrastruktúrájával együttműködve. Ellenőrizze, hogy rendelkezik az alábbi előfeltételek megkezdése előtt.

### <a name="licenses"></a>Licencek

Az Azure MFA NPS bővítményével rendelkező ügyfelek számára érhető el [licencek az Azure multi-factor Authentication](multi-factor-authentication.md) (az Azure AD prémium, EMS vagy egy önálló MFA-licencet része). Fogyasztásalapú licenceinek például az Azure MFA felhasználónkénti vagy hitelesítési licencekkel nem kompatibilisek az NPS-bővítményt. 

### <a name="software"></a>Szoftver

A Windows Server 2008 R2 SP1 vagy újabb.

### <a name="libraries"></a>Kódtárak

Ezek a kódtárak kiterjesztésű automatikusan települnek.

- [Visual C++ újraterjeszthető csomag a Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [A Microsoft Azure Active Directory modul a Windows PowerShell 1.1.166.0-s](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

A Microsoft Azure Active Directory modul a Windows PowerShell telepítve van, ha ezt még nem létezik, a telepítési folyamat részeként futtatja konfigurációs parancsfájl használatával. Hiba esetén nem kell előre a modul telepítéséhez, ha nem telepítette.

### <a name="azure-active-directory"></a>Azure Active Directory

Mindenki számára elérhetővé teheti az NPS-bővítményének szinkronizálnia kell az Azure Active Directory, Azure AD Connect használatával, és a multi-factor Authentication regisztrálva kell lennie.

Amikor telepíti a bővítményt, az Azure AD-bérlő szükség van a címtár azonosítója és a rendszergazdai hitelesítő adatokat. A címtár-azonosító, az annak a [az Azure portal](https://portal.azure.com). Jelentkezzen be rendszergazdaként, válassza ki a **Azure Active Directory** ikonra a bal oldalon, majd válassza ki **tulajdonságok**. Másolja a GUID Azonosítóját a **címtár-azonosító** mezőbe, majd mentse azt. A GUID használják a bérlő Azonosítóját, amikor az NPS-bővítményének telepítése.

![Keresse meg az Azure Active Directory-tulajdonságok területen címtár-azonosító](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A hálózati házirend-kiszolgálónak képesnek kell lennie kommunikálni a következő URL-címek a 80-as és 443-as portokon keresztül.

* https://adnotifications.windowsazure.com  
* https://login.microsoftonline.com

Ezenkívül a következő URL-kapcsolat végrehajtásához szükséges a [beállítása a PowerShell parancsfájl használatával adapter](#run-the-powershell-script)

- https://login.microsoftonline.com
- https://provisioningapi.microsoftonline.com
- https://aadcdn.msauth.net

## <a name="prepare-your-environment"></a>A környezet előkészítése

Az NPS-bővítményének telepítése előtt érdemes környezet előkészítése a hálózati, a hitelesítési forgalom kezeléséhez.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Az NPS szerepkör a tartományhoz csatlakoztatott kiszolgálón engedélyezése

A hálózati házirend-kiszolgáló Azure Active Directory csatlakozik, és hitelesíti a az MFA-kérelmeket. Válasszon egy kiszolgálót ehhez a szerepkörhöz. Azt javasoljuk, hogy egy kiszolgálót, amely nem kezeli az egyéb szolgáltatások érkező kéréseket kiválasztása, mert az NPS-bővítményének jelez, bármilyen kérelmeket, amelyek nem RADIUS által jelzett hibákat. A hálózati házirend-kiszolgáló az elsődleges és másodlagos authentication-kiszolgálóval, a környezetnek; kell állítani proxy RADIUS-kérelmeket egy másik kiszolgáló nem érhető el.

1. Nyissa meg a kiszolgálón a **adja hozzá szerepkörök és szolgáltatások varázsló** a Kiszolgálókezelő rövid menüből.
2. Válasszon **szerepköralapú vagy szolgáltatásalapú telepítés** a telepítés típusát.
3. Válassza ki a **hálózati házirend- és elérési szolgáltatások** kiszolgálói szerepkört. Előfordulhat, hogy felugró ablak tájékoztatja, hogy ez a szerepkör futtatásához szükséges szolgáltatásokat.
4. Kövesse a varázsló utasításait, amíg a Megerősítés lapon. Válassza az **Install** (Telepítés) lehetőséget.

Most, hogy a hálózati házirend-kiszolgáló számára kijelölt kiszolgáló van, ezt a kiszolgálót a VPN-megoldás bejövő RADIUS-kéréseinek kezelésére is konfigurálnia kell.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Az NPS-kiszolgálóval való kommunikációhoz a VPN-megoldás konfigurálása

Attól függően, melyik VPN-megoldást használ a RADIUS-hitelesítési szabályzat konfigurálásához szükséges lépések eltérhetnek. Konfigurálja a házirendet, a hálózati házirend-kiszolgáló RADIUS-kiszolgálóra mutassanak.

### <a name="sync-domain-users-to-the-cloud"></a>Szinkronizálási tartományi felhasználói a felhőbe

Ez a lépés már valószínűleg a bérlő teljes, de, fontos ellenőrizni, hogy az Azure AD Connect szinkronizálja az adatbázist nemrég.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Válassza ki **az Azure Active Directory** > **az Azure AD Connect**
3. Győződjön meg arról, hogy a szinkronizálási állapota **engedélyezve** , és hogy a legutóbbi szinkronizálás kevesebb, mint egy órával ezelőtt történt.

Ha elindít egy új ciklikus szinkronizálási, us utasításokat kell az [Azure AD Connect szinkronizálása: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Határozza meg, mely a felhasználók használhatják a hitelesítési módszerek

Nincsenek két tényező befolyásolja, hogy mely hitelesítési módszerek érhetők el egy hálózati házirend-kiszolgáló a konzolbővítmény üzembe helyezése:

1. Az a RADIUS-ügyfél között használt jelszó-titkosítási algoritmus (VPN-, Netscaler kiszolgáló, vagy egyéb) és az NPS-kiszolgálókon.
   - **A PAP** a felhőben, az Azure MFA hitelesítési módszereket támogatja: telefonhívás, egyirányú SMS, mobilalkalmazásbeli értesítés és mobilalkalmazásbeli ellenőrző kód.
   - **CHAPv2** és **EAP** telefonhívás- és mobilalkalmazás-értesítés támogatja.
2. A beviteli módok, hogy az ügyfélalkalmazás (VPN-, Netscaler kiszolgáló, vagy egyéb) képes kezelni. Például rendelkezik néhány azt jelenti, hogy a felhasználó egy ellenőrző kódot SMS vagy mobilalkalmazás írja be a VPN-ügyfél?

Központi telepítésekor az NPS-bővítményt, a tényezők segítségével kiértékelheti, hogy melyik módszer a felhasználók számára érhető el. Ha a RADIUS-ügyfél a PAP FUNKCIÓT támogatja, de az ügyfél UX nem rendelkezik egy ellenőrző kódot a beviteli mezőket, majd telefonhívás- és mobilalkalmazás-értesítés a két módon támogatott.

Is [tiltsa le a nem támogatott hitelesítési módszerek](howto-mfa-mfasettings.md#verification-methods) az Azure-ban.

### <a name="register-users-for-mfa"></a>Regisztrálja a felhasználókat a multi-factor Authentication

Mielőtt üzembe helyezése és az NPS-bővítményének használatához, felhasználókat a kétlépéses ellenőrzés végrehajtásához szükség van a multi-factor Authentication regisztrálni kell. Több azonnal a bővítményt, az üzembe helyezés teszteléséhez legalább egy tesztet fiók szükséges, amely teljes mértékben a multi-factor Authentication regisztrálva.

Az első lépések tesztfiók tegye a következőket:
1. Jelentkezzen be a [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) test-fiókkal. 
2. Kövesse az utasításokat követve állítsa be egy ellenőrzési módszert.
3. Hozzon létre egy feltételes hozzáférési szabályzat vagy [módosíthatja a felhasználói állapot](howto-mfa-userstates.md) kétlépéses ellenőrzés megkövetelése a test-fiók. 

A felhasználók emellett kövesse az alábbi lépéseket, mielőtt NPS-bővítményéhez hitelesíteni tudja regisztrálni.

## <a name="install-the-nps-extension"></a>Az NPS-bővítményének telepítése

> [!IMPORTANT]
> Az NPS-bővítményének telepítése egy másik kiszolgálóra, mint a VPN-hozzáférési pont.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Töltse le és telepítse az NPS-bővítményt az Azure MFA számára

1. [Töltse le az NPS-bővítményének](https://aka.ms/npsmfa) a Microsoft letöltőközpontból.
2. A bináris fájlt másolja a konfigurálni kívánt hálózati házirend-kiszolgáló.
3. Futtatás *setup.exe* , és kövesse a telepítési utasításokat. Ha hibákat észlel, ellenőrizze, hogy az Előfeltételek szakaszban a két kódtárak telepítése sikeresen befejeződött.

#### <a name="upgrade-the-nps-extension"></a>Az NPS-bővítményének frissítése

Amikor frissíti egy meglévő NPS-bővítményének telepítéséhez elkerülése érdekében az alapjául szolgáló kiszolgáló újraindítását a következő lépéseket:

1. Távolítsa el a meglévő verziót
1. Az új telepítő futtatása
1. A hálózati házirend-kiszolgáló (IAS) szolgáltatás újraindítása

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása

A telepítő létrehoz egy PowerShell-parancsfájlt ezen a helyen: `C:\Program Files\Microsoft\AzureMfa\Config` (ahol a C:\ az a telepítési meghajtó). Ez a PowerShell-szkript minden egyes futtatásakor a következő műveleteket hajtja végre:

- Hozzon létre egy önaláírt tanúsítványt.
- Társítsa a szolgáltatásnévnek az Azure ad-ben a tanúsítvány nyilvános kulcsát.
- Store a tanúsítvány a helyi számítógép cert tárolójában.
- A tanúsítvány titkos kulcsához hálózati felhasználó számára hozzáférést biztosít.
- Indítsa újra a hálózati házirend-kiszolgáló.

Hacsak nem szeretné a saját (helyett az önaláírt tanúsítványokat, a PowerShell-parancsfájl által létrehozott) tanúsítványokat használ, futtassa a PowerShell-parancsfájl a telepítés befejezéséhez. A bővítmény telepítése több kiszolgálón, ha mindegyik a saját tanúsítvánnyal kell rendelkeznie.

1. Windows PowerShell futtatása rendszergazdaként.
2. Módosítsa a könyvtárakat.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. A telepítő által létrehozott PowerShell-szkript futtatásához.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Jelentkezzen be az Azure AD-rendszergazdaként.
5. PowerShell kérni fogja a bérlő azonosítója. A címtár-azonosító GUID az Előfeltételek szakaszban az Azure Portalról másolt használja.
6. PowerShell a sikert jelző üzenet jeleníti meg, ha a parancsfájl elkészült.  

Ismételje meg ezeket a lépéseket minden olyan további hálózati házirend-kiszolgálókat, amelyeket szeretne a terheléselosztás beállítása.

> [!NOTE]
> Ha a PowerShell-parancsfájlt a tanúsítványok generálása helyett a saját tanúsítványok használ, győződjön meg arról, a hálózati házirend-kiszolgáló elnevezési egyezmény igazítása. A tulajdonos nevének kell **CN =\<TenantID\>, OU = Microsoft NPS-bővítményének**. 

## <a name="configure-your-nps-extension"></a>Az NPS-bővítményének konfigurálása

Ez a szakasz tartalmazza a kialakítási szempontokat és javaslatokat sikeres NPS-bővítmény központi telepítésekhez.

### <a name="configuration-limitations"></a>Konfigurációs korlátozások

- Az Azure MFA NPS-bővítményének nem tartalmazza az eszközök, felhasználók és a beállítások az MFA-kiszolgáló áttelepítése a felhőbe. Ebből kifolyólag javasoljuk, hogy a bővítmény használata az új központi telepítéseknél, nem pedig meglévő üzemelő példányt. Ha a bővítmény egy meglévő üzemelő példányt használ, a felhasználóknak rendelkezniük végrehajtásához igazolás felfelé újra adatokkal való feltöltéséhez MFA adataikat a felhőben.  
- Az NPS-bővítményt használja az egyszerű felhasználónév a helyszíni Active Directoryból a másodlagos Outlookhoz végrehajtásához az Azure MFA azonosítására A bővítmény beállítható úgy, hogy egy másik azonosító például a másodlagos bejelentkezési Azonosítót vagy egyéni Active Directory mező nem egyszerű felhasználónév használata. További információkért lásd: a cikk [speciális konfigurációs beállítások az NPS-bővítményéhez a multi-factor Authentication](howto-mfa-nps-extension-advanced.md).
- Nem minden titkosítási protokollokkal támogatja az összes ellenőrzési módszert.
   - **A PAP** támogatja a telefonhívás, egyirányú SMS, mobilalkalmazásbeli értesítés és mobilalkalmazásbeli ellenőrző kód
   - **CHAPv2** és **EAP** támogatja a telefonhívás- és mobilalkalmazás-értesítés

### <a name="control-radius-clients-that-require-mfa"></a>Vezérlő RADIUS-ügyfelek, amelyek a többtényezős hitelesítés

Miután engedélyezte a többtényezős hitelesítés egy RADIUS-ügyfél, az NPS-bővítményének használatával, az ügyfél összes hitelesítések hajthatok végre MFA szükségesek. Ha szeretné az MFA engedélyezése egyes RADIUS-ügyfelek, de nem más, két hálózati házirend-kiszolgálók konfigurálása, és a bővítmény telepítése csak az egyiket. Konfigurálja a többtényezős hitelesítés-kérelmeket küldjön a hálózati házirend-kiszolgáló konfigurálva a bővítményt a kívánt RADIUS-ügyfelek és más RADIUS-ügyfelek a bővítmény nincs konfigurálva a hálózati házirend-kiszolgálóra.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>A felhasználók számára, amely nincs regisztrálva az MFA-kiszolgáló előkészítése

Ha a multi-factor Authentication nem regisztrált felhasználók, megadhatja, hogy mi történik, ha megpróbálnak hitelesíteni. A beállításjegyzék-beállítással *REQUIRE_USER_MATCH* a beállításjegyzékbeli elérési út *HKLM\Software\Microsoft\AzureMFA* funkció viselkedését vezérlő. Ez a beállítás olyan egyetlen konfigurációs beállítással rendelkezik:

| Kulcs | Érték | Alapértelmezett |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | Nincs beállítva (egyenértékű, True) |

Ez a beállítás célja határozza meg, mi a teendő, ha a felhasználó nincs regisztrálva az MFA-hoz. Ha a kulcs nem létezik, nincs beállítva, vagy igaz értékre van állítva, a felhasználó nincs regisztrálva, majd a bővítmény nem tud az MFA-hitelesítést. Ha a kulcs értéke FALSE, és a felhasználó nincs regisztrálva, a hitelesítés folytatódik, többtényezős hitelesítés végrehajtása nélkül. Ha egy felhasználó az MFA-ban regisztrálva van, hitelesíteniük kell az MFA akkor is, ha REQUIRE_USER_MATCH hamis értékre van állítva.

Ha szeretné létrehozni ezt a kulcsot, és állítsa be hamis értékre Bár a felhasználók bevezetése, és nem az összes lehet regisztrálni az Azure MFA-kiszolgáló még. Azonban mivel a kulcs beállítása lehetővé teszi a felhasználóknak, a multi-factor Authentication jelentkezzen be a nem regisztrált, távolítsa el ezt a kulcsot az éles környezetben történő előtt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hogyan ellenőrizhetem, hogy az ügyféltanúsítvány a várt módon van-e telepítve?

Keresse meg az önaláírt tanúsítványt hozott létre a telepítő a tanúsítványtár, és ellenőrizze, hogy a titkos kulccsal rendelkezik felhasználónak adott engedélyek **hálózati szolgáltatás**. A tanúsítvány tulajdonosának nevével rendelkezik **CN \<tenantid\>, OU = Microsoft NPS-bővítményének**

Által létrehozott önaláírt tanúsítványokat a *AzureMfaNpsExtnConfigSetup.ps1* parancsfájlt is rendelkezik érvényességi élettartama pedig két év. Annak ellenőrzése, hogy a tanúsítvány telepítve van-e, amikor is ellenőrizze, hogy a tanúsítvány nem járt le.

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hogyan ellenőrizhetem, hogy az ügyféltanúsítvány társítva, és az Azure Active Directory-bérlő?

Nyissa meg a PowerShell-parancssort, és futtassa a következő parancsokat:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Ezek a parancsok nyomtassa ki minden olyan tanúsítványt, amelyet a bérlő társítása az NPS-bővítményének példányát a PowerShell-munkamenetben. Az ügyféltanúsítvány exportálása a titkos kulcs nélkül "Base-64 kódolású X.509(.cer)" fájlként szerint keresse meg a tanúsítványt, és összehasonlíthatja a lista és a PowerShell.

A következő parancs létrehoz egy .cer formátumú a "C:" meghajtón "npscertificate" nevű fájlt.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Ez a parancs futtatása után nyissa meg a C meghajtóhoz, keresse meg a fájlt, és kattintson rá duplán. Nyissa meg a részleteket, és görgessen le a "ujjlenyomata", hasonlítsa össze az ehhez a kiszolgálón telepített tanúsítvány ujjlenyomatával. A tanúsítvány-ujjlenyomatok egyeznie kell.

Érvényes –, és érvényes – addig, amíg az időbélyegek, amelyek emberek számára olvasható formátumban, nyilvánvaló misfits kiszűréséhez, ha a parancs visszaadja az egynél több tanúsítvány is használható.

-------------------------------------------------------------

### <a name="why-cant-i-sign-in"></a>Miért tud tudok bejelentkezni?

Ellenőrizze, hogy a jelszó még nem járt le. Az NPS-bővítményének jelszavak módosítását nem támogatja a bejelentkezési munkafolyamat részeként. További segítségért forduljon a szervezet informatikai személyzetet tart fenn.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Miért hibásak a saját kérések az ADAL-jogkivonathiba?

Ez a hiba miatt számos oka lehet. Hibaelhárítás elősegítése érdekében tegye a következőket:

1. Indítsa újra az NPS-kiszolgálót.
2. Győződjön meg arról, hogy az ügyféltanúsítvány a várt módon telepítve van.
3. Győződjön meg arról, hogy a tanúsítvány az Azure AD-bérlője társítva.
4. Ellenőrizze, hogy a https://login.microsoftonline.com/ elérhető-e a bővítményt futtató kiszolgálóról.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Miért nem sikerült a hitelesítés a HTTP-naplókat, hogy a felhasználó nem található meg a hiba?

Ellenőrizze, hogy az AD Connect fut, valamint, hogy a felhasználó megtalálható-e a Windows Active Directory és az Azure Active Directoryban.

-------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Miért látok HTTP-csatlakozási hibák a naplókban az összes saját hitelesítések sikertelenek?

Ellenőrizze, hogy a https://adnotifications.windowsazure.com elérhető-e az NPS-bővítményt futtató kiszolgálóról.

-------------------------------------------------------------

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Miért hitelesítés nem működik, annak ellenére, hogy megtalálható-e egy érvényes tanúsítványt?

Ha az előző számítógép-tanúsítvány lejárt, és a egy új tanúsítvány lett létrehozva, a lejárt tanúsítványok törölni kell. Lejárt tanúsítványokat problémákat okozhatnak az NPS-bővítmény indítása kellene.

Ha rendelkezik egy érvényes tanúsítványt, ellenőrizze a helyi számítógépfiók tanúsítvány Store MMC használatával, és győződjön meg arról, a tanúsítvány nem ment át annak lejárati dátuma. Újonnan érvényes tanúsítványok létrehozásához futtassa újra a lépéseket a szakaszában "[a PowerShell-parancsprogrammal](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>A TLS/SSL-protokollok és titkosítócsomagok kezelése

Javasoljuk, hogy régebbi és gyengébb titkosítási csomagok letiltása vagy eltávolítása, ha a szervezete által megkövetelt. A tennivalókat az [AD FS által használt SSL/TLS-protokollok és titkosítócsomagok kezelését](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) ismertető rész tartalmazza.

## <a name="next-steps"></a>További lépések

- Konfigurálása alternatív bejelentkezési azonosítók, vagy állítsa be a kivételek listáján, a kétlépéses ellenőrzést végző nem szabad IP-címek [speciális konfigurációs beállítások az NPS-bővítményéhez a multi-factor Authentication](howto-mfa-nps-extension-advanced.md)

- Ismerje meg, hogyan integrálható a [távoli asztali átjáró](howto-mfa-nps-extension-rdg.md) és [VPN-kiszolgálók](howto-mfa-nps-extension-vpn.md) az NPS-bővítményének használata

- [Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](howto-mfa-nps-extension-errors.md)
