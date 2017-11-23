---
title: "Az Azure MFA képességek biztosítása a meglévő hálózati házirend-kiszolgálók használatával |} Microsoft Docs"
description: "A hálózati házirend-kiszolgáló bővítmény, az Azure multi-factor Authentication egy olyan egyszerű megoldás felhőalapú kétlépéses vericiation-képességeket adhat a meglévő hitelesítési infrastruktúra."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: ebd6109fdae00da9e6dc1fc456573327d521e7e9
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>A meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-factor Authentication

A hálózati házirend-kiszolgáló (NPS) bővítményt, az Azure MFA MFA felhőalapú képességek hozzáadása a meglévő kiszolgálók használata hitelesítési infrastruktúráját. A hálózati házirend-kiszolgáló kiterjesztésű adhat hozzá a telefonhívás, szöveges üzenetet vagy telefonszám alkalmazás ellenőrzése a meglévő hitelesítési folyamat telepítése, konfigurálása és karbantartása az új kiszolgálók nélkül. 

A bővítmény szervezeteknek, amelyek a VPN-kapcsolatok védendő az Azure MFA kiszolgáló központi telepítése nélkül lett létrehozva. A hálózati házirend-kiszolgáló bővítmény eleget kell egy adaptert, RADIUS és az Azure MFA felhőalapú arra, hogy egy második tényezős hitelesítési közötti összevont, vagy a szinkronizált felhasználók.

A hálózati házirend-kiszolgáló bővítmény használata az Azure MFA számára, a hitelesítési folyamat a következő összetevőket tartalmazza: 

1. **NAS/VPN-kiszolgáló** kéréseket fogad a VPN-ügyfelek és alakítja azokat a hálózati házirend-kiszolgáló RADIUS-kérelmeket. 
2. **Hálózati házirend-kiszolgáló** kapcsolódik az Active Directory, az elsődleges hitelesítés elvégzéséhez a RADIUS-kérelmeket, és sikeres, akkor továbbítja a kérelmet a telepített bővítmények.  
3. **Hálózati házirend-kiszolgáló bővítmény** elindítja a másodlagos hitelesítés az Azure MFA kérelmet. Miután a bővítményt a választ kap, és az MFA-kérdést sikeres, ha a hitelesítési kérelmet befejezné azzal, hogy biztosít a hálózati házirend-kiszolgáló biztonsági jogkivonatokat, amelyek közé tartozik egy MFA jogcímet, Azure STS ki.  
4. **Az Azure MFA** az Azure Active Directoryval, a felhasználói adatok beolvasása kommunikál, és végrehajtja a másodlagos hitelesítést úgy konfigurálva, hogy a felhasználó hitelesítési módszerrel.

A következő ábra szemlélteti a magas szintű hitelesítési kérelem folyamata: 

![Hitelesítési folyamatábrája](./media/multi-factor-authentication-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

A hálózati házirend-kiszolgáló bővítmény redundancia, automatikusan kezeli, így nincs szükség speciális konfigurációra.

Tetszőleges számú Azure MFA-kompatibilis hálózati házirend-kiszolgáló kiszolgálók szükség szerint hozhat létre. Ha több kiszolgálót telepíti, azokat a különbség az ügyfél-tanúsítványt kell használnia. Kiszolgálónként egy tanúsítvány létrehozása azt jelenti, hogy minden tanúsítvány frissítheti egyenként, és nem kell foglalkoznia állásidő minden kiszolgálóra.

VPN-kiszolgálók hitelesítési kérelmek továbbítani, így azok tisztában kell lennie az Azure MFA-kompatibilis hálózati házirend-kiszolgáló új kiszolgálók.

## <a name="prerequisites"></a>Előfeltételek

A hálózati házirend-kiszolgáló bővítmény arra szolgál, hogy a meglévő infrastruktúra használata. Győződjön meg arról, hogy a következő előfeltételek megkezdése előtt.

### <a name="licenses"></a>Licencek

A hálózati házirend-kiszolgáló bővítmény, az Azure MFA számára érhető el az ügyfeleknek [Azure multi-factor Authentication licenceinek](multi-factor-authentication.md) (az Azure AD prémium, EMS vagy egy önálló MFA-licencet része). Fogyasztás alapján licenceinek többek között az Azure MFA felhasználónként vagy a hitelesítési licencek nem kompatibilisek-e a hálózati házirend-kiszolgáló bővítményt. 

### <a name="software"></a>Szoftver

Windows Server 2008 R2 SP1 vagy újabb.

### <a name="libraries"></a>Szalagtárak

Ezek a könyvtárak a rendszer automatikusan telepíti a kiterjesztéssel.

-   [Visual C++ újraterjeszthető csomag a Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Active Directory modul Windows Powershellhez készült Azure 1.1.166.0 verziója](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

A Microsoft Active Directory modul Windows Powershellhez készült Azure telepítve van, ha még nincs jelen, a telepítési folyamat részeként futtatja konfigurációs parancsfájl használatával. Nincs szükség a modul időben telepítésére, ha még nincs telepítve.

### <a name="azure-active-directory"></a>Azure Active Directory

Mindenki számára a hálózati házirend-kiszolgáló kiterjesztéssel kell szinkronizálva az Azure Active Directory, az Azure AD Connect használatával, és a multi-factor Authentication regisztrálva kell lennie.

A bővítmény telepítésekor szüksége a könyvtár azonosítója és rendszergazdai hitelesítő adatokat az Azure AD-bérlő. A könyvtár-azonosítója a található a [Azure-portálon](https://portal.azure.com). Jelentkezzen be rendszergazdaként, válassza ki a **Azure Active Directory** ikonra a bal oldalon, majd válassza ki **tulajdonságok**. A GUID-azonosítóját a **könyvtár-azonosítója** és mentse azt. A GUID használják a bérlő azonosítója, amikor a hálózati házirend-kiszolgáló-bővítményének telepítése.

![A könyvtár-azonosító az Azure Active Directoryban található](./media/multi-factor-authentication-nps-extension/find-directory-id.png)

## <a name="prepare-your-environment"></a>A környezet előkészítése

A hálózati házirend-kiszolgáló-bővítményének telepítése előtt érdemes környezet a hitelesítési forgalom kezelésére előkészítése.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Az NPS szerepkör a tartományhoz csatlakozó kiszolgálón engedélyezése

A hálózati házirend-kiszolgáló csatlakozik az Azure Active Directory, és ezzel hitelesíti a többtényezős hitelesítési kérelmeket. Válasszon egy kiszolgálót ehhez a szerepkörhöz. Azt javasoljuk, hogy kiválasztása egy kiszolgálót, amely nem kezeli az egyéb szolgáltatások érkező kéréseket, mert a hálózati házirend-kiszolgáló bővítmény kérelmeket, amelyek nem RADIUS hibákat jelez. A hálózati házirend-kiszolgáló a környezetben; elsődleges és másodlagos hitelesítés-kiszolgálóként kell beállítása proxy RADIUS-kérelmeket egy másik kiszolgáló nem képes.

1. A kiszolgálón nyissa meg a **hozzáadása szerepkörök és szolgáltatások varázsló** a Kiszolgálókezelő gyors üzembe helyezés menüből.
2. Válasszon **szerepköralapú vagy szolgáltatásalapú telepítés** a telepítés típusát.
3. Válassza ki a **hálózati házirend- és elérési szolgáltatások** kiszolgálói szerepkört. Előfordulhat, hogy előugró ablak arról, ez a szerepkör futtatásához szükséges szolgáltatásokat.
4. Kövesse a varázsló utasításait, amíg a Jóváhagyás lap. Válassza ki **telepítése**.

Most, hogy a hálózati házirend-kiszolgálójának kijelölt kiszolgáló van, ezt a kiszolgálót a VPN-megoldás a bejövő RADIUS-kérelmeket kezelnek is konfigurálnia kell.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>A VPN-megoldás kommunikálni a hálózati házirend-kiszolgáló konfigurálása

Attól függően, hogy melyik VPN megoldást használ a RADIUS hitelesítési házirend konfigurálásához szükséges lépések eltérhetnek. Konfigurálja a házirendet, hogy a RADIUS hálózati házirend-kiszolgálóra mutasson.

### <a name="sync-domain-users-to-the-cloud"></a>Szinkronizálási tartományi felhasználói a felhőbe

Ez a lépés már lehet, hogy a bérlő befejeződött, de jó ellenőrizze, hogy az Azure AD Connect szinkronizálása megtörtént-e az adatbázis nemrég.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Válassza ki **az Azure Active Directory** > **az Azure AD Connect**
3. Győződjön meg arról, hogy a szinkronizálási állapota **engedélyezve** és az utolsó szinkronizálás nem kisebb, mint egy óra telt el.

Ha egy új ciklikus szinkronizálás velünk utasításokat indítsa kell a [az Azure AD Connect szinkronizálása: Feladatütemező](../active-directory/connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>A felhasználók hitelesítési módok meghatározása

Két tényező befolyásolja, hogy mely hitelesítési módszerek állnak rendelkezésre az NPS-bővítmény központi telepítése történik:

1. A RADIUS-ügyfél között használt jelszó titkosítási algoritmus (VPN-profilok, Netscaler kiszolgáló, vagy egyéb) és a hálózati házirend-kiszolgálókat.
   - **PAP** a felhőben Azure MFA hitelesítési módszereket támogatja: telefonhívás, a egyirányú SMS-üzenet, a mobilalkalmazáson keresztüli értesítések és a mobilalkalmazás ellenőrzőkódjának.
   - **CHAPv2** és **EAP** támogatja a telefonhívás, és értesítést a mobilalkalmazásban.
2. A beviteli módok, az ügyfélalkalmazás (VPN-profilok, Netscaler kiszolgáló, vagy más) képes kezelni. Például a VPN-ügyfél néhány azt jelenti, hogy a felhasználó adja meg a szöveg- vagy mobilalkalmazás ellenőrzőkódja van?

Az NPS-bővítmény telepítésekor a ezek a tényezők segítségével kiértékelheti, hogy mely módszerek állnak rendelkezésre a felhasználók számára. Ha a RADIUS-ügyfél a PAP FUNKCIÓT támogatja, de az ügyfél UX nem rendelkezik a beviteli mezők az ellenőrző kódot, majd telefonhívás és a mobilalkalmazásban megjelenő értesítést a két módon támogatott.

Is [tiltsa le a nem támogatott hitelesítési módszerek](multi-factor-authentication-whats-next.md#selectable-verification-methods) az Azure-ban.

### <a name="enable-users-for-mfa"></a>Lehetővé teszi a felhasználók a multi-factor Authentication

A teljes hálózati házirend-kiszolgáló-bővítmény telepítése előtt kell többtényezős hitelesítés engedélyezése a felhasználók számára szeretne végezni a kétlépéses ellenőrzést. Több azonnal módon telepítheti azt a bővítmény teszteléséhez kell legalább egy olyan fiókot, amely teljes mértékben regisztrálva a többtényezős hitelesítés.

A lépések teszt fiók tegye a következőket:
1. Jelentkezzen be [https://aka.ms/mfasetup](https://aka.ms/mfasetup) teszt-fiókkal. 
2. Kövesse az utasításokat a hitelesítési módszer beállítása.
3. Vagy hozzon létre egy feltételes hozzáférési házirend vagy [módosíthatja a felhasználói állapot](multi-factor-authentication-get-started-user-states.md) kétlépéses ellenőrzés megkövetelése a test-fiók. 

A felhasználók is kell kövesse az alábbi lépéseket ahhoz, azok képes hitelesíteni a hálózati házirend-kiszolgáló kiterjesztésű regisztrálásához.

## <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló-kiterjesztés telepítése

> [!IMPORTANT]
> A hálózati házirend-kiszolgáló bővítmény telepíthető a VPN-csatlakozási pont eltérő kiszolgálóra.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Töltse le és telepítse az NPS-bővítmény az Azure MFA számára

1.  [Töltse le a hálózati házirend-kiszolgáló bővítményt](https://aka.ms/npsmfa) a Microsoft letöltőközpontból.
2.  A bináris fájlt másolja a konfigurálni kívánt hálózati házirend-kiszolgáló.
3.  Futtatás *setup.exe* és a telepítési utasításokat. Ha hibákba ütközik, ellenőrizze, hogy a két könyvtárak Előfeltételek szakaszában sikeresen megtörtént.

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása

A telepítő létrehoz egy PowerShell-parancsfájlt ezen a helyen: `C:\Program Files\Microsoft\AzureMfa\Config` (ahol a C:\ az a telepítési meghajtó). A PowerShell-parancsfájl a következő műveleteket hajtja végre:

-   Hozzon létre egy önaláírt tanúsítványt.
-   Társítsa az egyszerű szolgáltatásnév Azure ad-val a tanúsítvány nyilvános kulcsát.
-   A tanúsítvány a helyi számítógép-tanúsítvány tárolójában tárolja.
-   Adjon hozzáférést a tanúsítvány titkos kulcsához hálózati felhasználó számára.
-   Indítsa újra a hálózati házirend-kiszolgáló.

Hacsak nem szeretné a saját (helyett az önaláírt tanúsítványokat, amely a PowerShell-parancsfájlt hoz létre) tanúsítványokat használ, futtassa a PowerShell-parancsfájl a telepítés befejezéséhez. A bővítmény telepítése több kiszolgálón, ha minden egyes saját tanúsítvánnyal kell rendelkeznie.

1. A Windows PowerShell futtatása rendszergazdaként.
2. Módosítsa a könyvtárat.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. A telepítő által PowerShell-parancsprogram futtatásához.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Jelentkezzen be az Azure AD-rendszergazdaként.
5. PowerShell kérni fogja a bérlő azonosítójával. A Directory azonosító GUID, az Azure-portálon az Előfeltételek című részben másolt használja.
6. PowerShell egy üzenetet jeleníti meg, ha a parancsfájl befejeződött.  

Ismételje meg ezeket a lépéseket a semmilyen további NPS-kiszolgálókon, amelyet a terheléselosztás beállítása.

>[!NOTE]
>Ha a tanúsítványok a PowerShell-parancsfájllal generálása helyett használhatja a saját tanúsítványait, győződjön meg arról, hogy a hálózati házirend-kiszolgáló elnevezés szabály szerint rendezve. A tulajdonos nevének kell **CN =\<TenantID\>, OU = a Microsoft hálózati házirend-kiszolgáló bővítmény**. 

## <a name="configure-your-nps-extension"></a>A hálózati házirend-kiszolgáló-kiterjesztés konfigurálása

Ez a szakasz a kialakítási szempontokat és javaslatokat sikeres hálózati házirend-kiszolgáló bővítmény központi telepítéseket tartalmazza.

### <a name="configuration-limitations"></a>Konfiguráció korlátozásai

- Az Azure MFA számára a hálózati házirend-kiszolgáló bővítmény nem tartalmazza az eszközök, felhasználók és a beállítások áttelepítéséhez az MFA kiszolgáló a felhőbe. Ezért javasoljuk, hogy az új központi telepítéséhez, nem pedig meglévő központi telepítési bővítményének használatával. A bővítmény egy meglévő telepítéshez használja, ha a felhasználók rendelkeznek igazolása-fel újra az MFA adatait a felhőben feltöltéséhez végrehajtásához.  
- Használja az egyszerű felhasználónév a helyszíni Active Directoryból a hálózati házirend-kiszolgáló kiterjesztést a másodlagos Outlookhoz végrehajtásához az Azure MFA azonosításhoz A bővítmény beállítható úgy, hogy a másodlagos bejelentkezési Azonosítóval vagy eltérő UPN egyéni Active Directory mező egy másik azonosítót. Lásd: [speciális konfigurációs beállítások az NPS-bővítmény a multi-factor Authentication](multi-factor-authentication-advanced-vpn-configurations.md) további információt.
- Nem minden titkosítási protokollokkal támogatja az összes ellenőrzési módszert.
   - **PAP** támogatja a telefonhívás, a egyirányú SMS-üzenet, a mobilalkalmazáson keresztüli értesítések és a mobilalkalmazás ellenőrzőkódjának
   - **CHAPv2** és **EAP** telefonhívás és a mobilalkalmazáson keresztüli értesítések támogatása

### <a name="control-radius-clients-that-require-mfa"></a>Többtényezős Hitelesítést igénylő vezérlő RADIUS-ügyfelek

Egy RADIUS-ügyfél a hálózati házirend-kiszolgáló bővítmény használatával engedélyezi az MFA Használatát, ha ennél az ügyfélnél összes hitelesítések kell többtényezős Hitelesítést végezni. Ha engedélyezi az MFA Használatát az egyes RADIUS-ügyfelek, de mások számára nem szeretné, két hálózati házirend-kiszolgálók konfigurálása, és csak az egyiket a bővítmény telepítését. Konfigurálja a RADIUS-ügyfelek megkövetelő kérelmeket küldeni a hálózati házirend-kiszolgáló, a kiterjesztéssel konfigurálni kívánt és más RADIUS-ügyfelek számára a hálózati házirend-kiszolgáló nincs konfigurálva a bővítményt.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>A felhasználók számára, amelyek nincsenek regisztrálva, a multi-factor Authentication előkészítése

Ha a multi-factor Authentication nem regisztrált felhasználók, azt is meghatározhatja, mi történik, ha mégis megpróbálják hitelesítéséhez. A beállításjegyzék-beállítást használja *REQUIRE_USER_MATCH* a beállításjegyzékbeli elérési út *HKLM\Software\Microsoft\AzureMFA* szolgáltatás működését. Ezzel a beállítással rendelkezik egyetlen konfigurációs beállítás:

| Kulcs | Érték | Alapértelmezett |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | IGAZ/HAMIS | Nincs beállítva (egyenértékű TRUE) |

Ez a beállítás célja határozza meg, mi a teendő, ha a felhasználó nincs regisztrálva az MFA szolgáltatásra. Ha a kulcs nem létezik, nincs beállítva vagy az értéke igaz, és a felhasználó nincs regisztrálva, majd a bővítmény nem sikerül az MFA-kérdést. Ha a kulcs hamis értékre van állítva, és a felhasználó nincs regisztrálva, a hitelesítés folytatódik MFA végrehajtása nélkül. Ha a felhasználó regisztrálta az MFA, hitelesítenie kell a multi-factor Authentication szolgáltatás akkor is, ha REQUIRE_USER_MATCH hamis értékre van állítva.

Ha szeretné létrehozni ezt a kulcsot, és állítsa FALSE, amíg a felhasználó bevezetése, és nem lehet regisztrálni az Azure MFA még. Azonban mivel a kulcs beállítása lehetővé teszi a felhasználók, amelyek nincsenek regisztrálva az MFA szolgáltatásra való bejelentkezéshez, el kell távolítania ezt a kulcsot éles előtt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hogyan ellenőrizhetem, hogy az ügyféltanúsítványt a várt módon van-e telepítve?

Keresse meg az önaláírt tanúsítvány, a telepítő a tanúsítványtároló, és ellenőrizze, hogy a titkos kulccsal rendelkezik felhasználónak adott engedélyek által **hálózati szolgáltatás**. A tanúsítvány rendelkezik egy tulajdonosneve **CN \<tenantid\>, OU = a Microsoft hálózati házirend-kiszolgáló bővítmény**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hogyan ellenőrizhetem, hogy az ügyfél-tanúsítvány társítva a bérlő az Azure Active Directoryban?

Nyissa meg a PowerShell-parancssort, és futtassa a következő parancsokat:

```
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 
```

Ezek a parancsok a hálózati házirend-kiszolgáló bővítmény példányát a PowerShell-munkamenetben a bérlő társítása minden tanúsítvány nyomtassa ki. Az ügyfél-tanúsítvány exportálása a titkos kulcs nélküli "Base-64 kódolású X.509(.cer)" fájlként által keresse meg a tanúsítványt, és összehasonlíthatja a lista és a PowerShell.

Érvényes-származó, érvényes-amíg időbélyegeket, amelyek emberek számára olvasható formátumban, nyilvánvaló misfits kiszűrésére, ha a parancs visszaadja egynél több tanúsítvány is használható.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Miért van saját kérések hiba miatt sikertelenül működő ADAL token?

Ez a hiba lehetséges oka, több oka. Hibaelhárítás elősegítése érdekében tegye a következőket:

1. Indítsa újra a hálózati házirend-kiszolgáló.
2. Győződjön meg arról, hogy adott ügyféltanúsítványt várt módon van-e telepítve.
3. Győződjön meg arról, hogy a tanúsítvány társítva a bérlője Azure ad-val.
4. Győződjön meg arról, hogy https://login.microsoftonline.com/ a bővítményt futtató kiszolgáló elérhető-e.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Miért nem sikerül a hitelesítési hiba történt a HTTP-naplókban figyelmezteti a felhasználókat arra, hogy a felhasználó nem található?

Győződjön meg arról, hogy fut-e AD Connectet, és, hogy a felhasználó megtalálható a Windows Active Directory és az Azure Active Directory is.

------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Miért látom HTTP-hibák rögzít minden a hitelesítések sikertelenek lesznek csatlakozás?

Ellenőrizze, hogy a https://adnotifications.windowsazure.com elérhető-e az NPS-bővítményt futtató kiszolgálóról.


## <a name="next-steps"></a>Következő lépések

- Bejelentkezés másik azonosítók konfigurálása, vagy állítsa be a kivételek listájához a kétlépéses ellenőrzést végző nem szabad IP-címekről [speciális konfigurációs beállítások az NPS-bővítmény a többtényezős hitelesítés](nps-extension-advanced-configuration.md)

- Ismerje meg, hogyan integrálható [távoli asztali átjáró](nps-extension-remote-desktop-gateway.md) és [VPN-kiszolgálók](nps-extension-vpn.md) használ a hálózati házirend-kiszolgáló kiterjesztést

- [Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](multi-factor-authentication-nps-errors.md)
