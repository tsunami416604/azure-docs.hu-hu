---
title: Azure MFA-képességek biztosítása a n-ps használatával – Azure Active Directory
description: Felhőalapú kétlépéses ellenőrzési lehetőségek hozzáadása a meglévő hitelesítési infrastruktúrához
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c251569cfe6a2f27f86421ffe6a446ace52b435
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051151"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure Multi-Factor Authenticationnel

Az Azure MFA hálózati házirend-kiszolgáló (NPS) bővítménye felhőalapú MFA-képességeket ad a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A hálózati kiszolgáló kiterjesztéssel új kiszolgálók telepítése, konfigurálása és karbantartása nélkül adhat hozzá telefonhívást, sms-t vagy telefonos alkalmazásellenőrzést a meglévő hitelesítési folyamathoz. 

Ez a bővítmény olyan szervezetek számára készült, amelyek az Azure MFA-kiszolgáló telepítése nélkül szeretnék megvédeni a VPN-kapcsolatokat. A hálózati kiszolgáló bővítmény a RADIUS és a felhőalapú Azure MFA közötti adapterként működik, hogy egy második hitelesítési tényezőt biztosítson az összevont vagy szinkronizált felhasználók számára.

Ha a nps bővítményt használja az Azure MFA-hoz, a hitelesítési folyamat a következő összetevőket tartalmazza: 

1. **A NAS/VPN-kiszolgáló** kéréseket fogad a VPN-ügyfelektől, és azokat RADIUS-kérelmekké alakítja át a nps-kiszolgálókra. 
2. **A hálózat-működtető kiszolgáló** csatlakozik az Active Directoryhoz, hogy végrehajtsa az elsődleges hitelesítést a RADIUS-kérelmekhez, és sikeres en a kérést továbbítja a telepített bővítményeknek.  
3. **NPS-bővítmény** elindítja a másodlagos hitelesítésaz Azure MFA-nak irányuló kérelmet. Miután a bővítmény megkapja a választ, és ha az MFA-kihívás sikeres, befejezi a hitelesítési kérelmet azáltal, hogy a nps-kiszolgáló biztonsági jogkivonatokat, amelyek tartalmazzák az Azure STS által kiadott MFA-jogcímet.  
4. **Az Azure MFA** kommunikál az Azure Active Directoryval a felhasználó adatainak lekéréséhez, és végrehajtja a másodlagos hitelesítést a felhasználó számára konfigurált ellenőrzési módszerrel.

Az alábbi ábra ezt a magas szintű hitelesítési kérelemfolyamatot mutatja be: 

![Hitelesítési folyamatdiagram](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

A hálózati szolgáltatás-kezelő bővítmény automatikusan kezeli a redundanciát, így nincs szükség speciális konfigurációra.

Annyi Azure MFA-kompatibilis hálózati kiszolgálót hozhat létre, amennyire szüksége van. Ha több kiszolgálót telepít, mindegyikhez használjon különbségügyfél-tanúsítványt. Ha minden kiszolgálóhoz tanúsítványt hoz létre, az azt jelenti, hogy minden tanúsítványt külön-külön frissíthet, és nem kell aggódnia az összes kiszolgáló állásideje miatt.

A VPN-kiszolgálók továbbítják a hitelesítési kérelmeket, ezért tisztában kell lenniük az új Azure MFA-kompatibilis hálózati kiszolgálókkal.

## <a name="prerequisites"></a>Előfeltételek

A nps-bővítmény célja, hogy működjön együtt a meglévő infrastruktúrával. A kezdés előtt győződjön meg arról, hogy rendelkezik az alábbi előfeltételekkel.

### <a name="licenses"></a>Licencek

Az Azure MFA-bővítménye az [Azure többtényezős hitelesítéshez licencet](multi-factor-authentication.md) szolgáltató ügyfelek számára érhető el (az Azure AD Premium, EMS vagy egy MFA önálló licenc tartalmazza). Az Azure MFA-hoz, például felhasználónkénti vagy hitelesítési licencek fogyasztásalapú licencei nem kompatibilisek a nps-bővítménylel. 

### <a name="software"></a>Szoftverek

Windows Server 2008 R2 SP1 vagy újabb szervizcsomag.

### <a name="libraries"></a>Kódtárak

Ezek a könyvtárak automatikusan települnek a bővítménylel együtt.

- [Visual C++ terjeszthető csomagok a Visual Studio 2013-hoz (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory module for Windows PowerShell 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

A Microsoft Azure Active Directory module for Windows PowerShell telepítve van, ha még nincs jelen, egy konfigurációs parancsfájlon keresztül futtatja a telepítési folyamat részeként. Nincs szükség a modul idő előtti telepítésére, ha még nincs telepítve.

### <a name="azure-active-directory"></a>Azure Active Directory

Mindenki használja a n-ps-bővítményt kell szinkronizálni az Azure Active Directory az Azure AD Connect használatával, és regisztrálni kell a MFA.

A bővítmény telepítésekor szüksége van a címtár-azonosító és a rendszergazdai hitelesítő adatok az Azure AD-bérlő. A címtárazonosítót az [Azure Portalon](https://portal.azure.com)találja. Jelentkezzen be rendszergazdaként. Keresse meg és jelölje ki az **Azure Active Directoryt,** majd válassza a **Tulajdonságok**lehetőséget. Másolja a guid azonosítót a **címtárazonosító** mezőbe, és mentse. Ezt a GUID azonosítót használja bérlői azonosítóként a nps-bővítmény telepítésekor.

![A címtárazonosító megkeresése az Azure Active Directory-tulajdonságok alatt](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

A nps-kiszolgálónak képesnek kell lennie a következő URL-címekkel kommunikálni a 80-as és 443-as portokon keresztül.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Ezenkívül a következő URL-címekhez való kapcsolódás szükséges az adapter telepítésének befejezéséhez [a megadott PowerShell-parancsfájl használatával](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>A környezet előkészítése

A nps-bővítmény telepítése előtt fel szeretné készíteni a környezetet a hitelesítési forgalom kezelésére.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>A hálózati kiszolgáló szerepkör engedélyezése tartományhoz csatlakozó kiszolgálón

A n-ps-kiszolgáló csatlakozik az Azure Active Directoryhoz, és hitelesíti az MFA-kérelmeket. Válasszon egy kiszolgálót ehhez a szerepkörhöz. Azt javasoljuk, hogy olyan kiszolgálót válasszon, amely nem kezeli a más szolgáltatásokból érkező kéréseket, mert a nps-bővítmény hibákat okoz a nem RADIUS-kérelmeknél. A hálózat-működtető kiszolgálót a környezet elsődleges és másodlagos hitelesítési kiszolgálójaként kell beállítani; nem tud RADIUS-kérelmeket proxyolni egy másik kiszolgálóra.

1. A kiszolgálón nyissa meg a **Szerepkörök és szolgáltatások hozzáadása varázslót** a Kiszolgálókezelő rövid útmutató menüjéből.
2. Válassza **a szerepkör- vagy szolgáltatásalapú telepítést** a telepítési típushoz.
3. Válassza a **Hálózati házirend és az Access Services** kiszolgálói szerepkört. Egy ablak jelenhet meg, amely tájékoztatja a szerepkör futtatásához szükséges szolgáltatásokról.
4. Folytassa a varázslón keresztül a Megerősítés lapig. Válassza az **Install** (Telepítés) lehetőséget.

Most, hogy van egy kijelölt kiszolgálója a hálózati kiszolgálószámára, a kiszolgálót is úgy kell konfigurálnia, hogy kezelje a VPN-megoldásból érkező RADIUS-kérelmeket.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>A VPN-megoldás konfigurálása a hálózati kiszolgálóval való kommunikációhoz

Attól függően, hogy melyik VPN-megoldást használja, a RADIUS-hitelesítési házirend konfigurálásának lépései eltérőek lehetnek. Konfigurálja úgy ezt a házirendet, hogy a RADIUS hálózati házirend-kiszolgálóra mutasson.

### <a name="sync-domain-users-to-the-cloud"></a>Tartományi felhasználók szinkronizálása a felhővel

Ez a lépés már befejeződött a bérlőn, de érdemes kétszer ellenőrizni, hogy az Azure AD Connect nemrég szinkronizálta-e az adatbázisokat.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) rendszergazdaként.
2. Válassza az **Azure Active Directory** > **Azure AD Connect lehetőséget**
3. Ellenőrizze, hogy a szinkronizálási állapota **engedélyezve van-e,** és hogy az utolsó szinkronizálás kevesebb mint egy órával ezelőtt volt-e.

Ha egy új szinkronizálási körre van szüksége, akkor az [Azure AD Connect szinkronizálási: Ütemező](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)című útmutató utasításait.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Annak meghatározása, hogy a felhasználók milyen hitelesítési módszereket használhatnak

A nps-bővítmény telepítésével két tényező befolyásolja, hogy mely hitelesítési módszerek érhetők el:

1. A RADIUS-ügyfél (VPN, Netscaler-kiszolgáló vagy más) és a hálózati házirend-kiszolgálók között használt jelszótitkosítási algoritmus.
   - **A PAP** támogatja az Azure MFA összes hitelesítési módszerét a felhőben: telefonhívás, egyirányú szöveges üzenet, mobilalkalmazás-értesítés, OATH hardvertokenek és mobilalkalmazás-ellenőrző kód.
   - **A CHAPV2** és **az EAP** támogatja a telefonhívást és a mobilalkalmazás-értesítéseket.

      > [!NOTE]
      > A n-ps bővítmény telepítésekor használja ezeket a tényezőket, hogy kiértékelheti, mely módszerek érhetők el a felhasználók számára. Ha a RADIUS-ügyfél támogatja a PAP-t, de az ügyfél felhasználói felülete nem rendelkezik ellenőrző kód bemeneti mezőivel, akkor a telefonhívás és a mobilalkalmazás-értesítés a két támogatott lehetőség.
      >
      > Továbbá, ha a VPN-ügyfél felhasználói felülete támogatja a bemeneti mezőt, és konfigurálta a hálózati hozzáférési házirendet - a hitelesítés sikeres lehet, azonban a hálózati házirendben konfigurált RADIUS-attribútumok egyike sem lesz alkalmazva sem a hálózati hozzáférési eszközre, mint az RRAS-kiszolgáló, sem a VPN-ügyfél. Ennek eredményeképpen előfordulhat, hogy a VPN-ügyfél a kívántnál nagyobb vagy kevesebb hozzáféréssel rendelkezik.
      >

2. Az ügyfélalkalmazás (VPN, Netscaler-kiszolgáló vagy más) által kezelhető beviteli módok. Például a VPN-ügyfél rendelkezik valamilyen eszközzel, amely lehetővé teszi a felhasználó számára, hogy beírjon egy ellenőrző kódot egy szövegből vagy mobilalkalmazásból?

[Letilthatja a nem támogatott hitelesítési módszerek](howto-mfa-mfasettings.md#verification-methods) az Azure-ban.

### <a name="register-users-for-mfa"></a>Felhasználók regisztrálása az MFA-hoz

A nps-bővítmény telepítése és használata előtt a kétlépéses ellenőrzés végrehajtásához szükséges felhasználókat regisztrálni kell az MFA-hoz. A bővítmény üzembe helyezése közbeni teszteléséhez legalább egy olyan tesztfiókra van szükség, amely teljes mértékben regisztrálva van a többtényezős hitelesítéshez.

A tesztfiók elindításához kövesse az alábbi lépéseket:

1. Jelentkezzen be [https://aka.ms/mfasetup](https://aka.ms/mfasetup) egy tesztfiókkal.
2. Az ellenőrzési módszer beállításához kövesse az utasításokat.
3. [Hozzon létre egy feltételes hozzáférési szabályzatot,](howto-mfa-getstarted.md#create-conditional-access-policy) amely többtényezős hitelesítést igényel a tesztfiókhoz.

## <a name="install-the-nps-extension"></a>A nps-bővítmény telepítése

> [!IMPORTANT]
> Telepítse a nórás-bővítményt a VPN-hozzáférési ponttól eltérő kiszolgálóra.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>A nps-bővítmény letöltése és telepítése az Azure MFA-hoz

1. [Töltse le a nps bővítményt](https://aka.ms/npsmfa) a Microsoft letöltőközpontjából.
2. Másolja a bináris fájlt a konfigurálni kívánt hálózati házirend-kiszolgálóra.
3. *Futtassa a setup.exe programot,* és kövesse a telepítési utasításokat. Ha hibákat észlel, ellenőrizze, hogy az előfeltétel szakasz két könyvtára telepítése sikeresen megtörtént-e.

#### <a name="upgrade-the-nps-extension"></a>A nps-bővítmény frissítése

Meglévő nps-bővítmény telepítésekor az alapul szolgáló kiszolgáló újraindításának elkerülése érdekében hajtsa végre az alábbi lépéseket:

1. A meglévő verzió eltávolítása
1. Az új telepítő futtatása
1. A Hálózati házirend-kiszolgáló (IAS) szolgáltatás újraindítása

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása

A telepítő létrehoz egy PowerShell-parancsfájlt ezen a helyen: `C:\Program Files\Microsoft\AzureMfa\Config` (ahol a C:\ a telepítőmeghajtó). Ez a PowerShell-parancsfájl a következő műveleteket hajtja végre minden egyes futtatáskor:

- Hozzon létre egy önaláírt tanúsítványt.
- Társítsa a nyilvános kulcsot a tanúsítvány egyszerű szolgáltatás az Azure AD-n.
- Tárolja a tanúsítványt a helyi gépi tanúsítvány tárolójában.
- Hozzáférést biztosít a tanúsítvány személyes kulcsához a hálózati felhasználó számára.
- Indítsa újra a nps-t.

Hacsak nem szeretné használni a saját tanúsítványokat (ahelyett, hogy a PowerShell-parancsfájl által létrehozott önaláírt tanúsítványokat szeretné használni), futtassa a PowerShell-parancsfájlt a telepítés befejezéséhez. Ha a bővítményt több kiszolgálóra telepíti, mindegyiknek saját tanúsítvánnyal kell rendelkeznie.

1. Futtassa a Windows PowerShellt rendszergazdaként.
2. Könyvtárakat váltson.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Futtassa a telepítő által létrehozott PowerShell-parancsfájlt.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Jelentkezzen be az Azure AD-be rendszergazdaként.
5. A PowerShell a bérlői azonosítót kéri. Használja a címtárazonosító GUID, amely az Azure Portalról az előfeltételek szakaszban másolt.
6. A PowerShell sikeres üzenetet jelenít meg, amikor a parancsfájl befejeződött.  

Ismételje meg ezeket a lépéseket minden további, terheléselosztáshoz beállítani kívánt nps-kiszolgálón.

Ha az előző számítógép-tanúsítvány lejárt, és új tanúsítvány jött létre, törölnie kell a lejárt tanúsítványokat. A lejárt tanúsítványok problémákat okozhatnak a nps bővítmény indításakor.

> [!NOTE]
> Ha a powershell-parancsfájllal való tanúsítványok létrehozása helyett saját tanúsítványokat használ, győződjön meg arról, hogy azok a hálózati címzet elnevezési konvenciójának megfelelően igazodnak. A tulajdonos névnek **CN=\<\>TenantID ,OU=Microsoft NPS Extension**-nek kell lennie. 

### <a name="microsoft-azure-government-additional-steps"></a>A Microsoft Azure Government további lépései

Az Azure Government-felhőt használó ügyfelek számára a következő további konfigurációs lépések szükségesek minden hálózati kiszolgálón:

1. Nyissa meg a **Rendszerleíróadatbázis-szerkesztőt** a nemzeti házirend-kiszolgálón.
1. Nyissa meg a `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa` címet. Állítsa be a következő kulcsértékeket:

    | Beállításkulcs       | Érték |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Ismételje meg az előző két lépést az egyes nps-kiszolgálók beállítási kulcsainak beállításához.
1. Indítsa újra a nps szolgáltatást minden egyes n-ps-kiszolgálóhoz.

    A minimális hatás érdekében minden hálózati kiszolgálót egyenként vegye ki a hálózati terheléselosztás rotációjából, és várja meg, amíg az összes kapcsolat lefolyik.

### <a name="certificate-rollover"></a>Tanúsítvány váltása

A nps-bővítmény 1.0.1.32-es kiadásával több tanúsítvány olvasása is támogatott. Ez a funkció megkönnyíti a működésbe lépő tanúsítványfrissítéseket azok lejárata előtt. Ha a szervezet a n-ps bővítmény egy korábbi verzióját futtatja, frissítenie kell az 1.0.1.32-es vagy újabb verzióra.

A `AzureMfaNpsExtnConfigSetup.ps1` parancsfájl által létrehozott tanúsítványok 2 évig érvényesek. Az informatikai szervezeteknek figyelniük kell a tanúsítványok lejáratát. A nps-bővítmény tanúsítványai a Helyi számítógép tanúsítványtárolójában, a Személyes csoportban találhatók, és a parancsfájlnak megadott bérlői azonosítóhoz vannak kiadva.

Ha egy tanúsítvány közeledik a lejárati dátumhoz, új tanúsítványt kell létrehozni a lecseréléséhez.  Ez a folyamat úgy `AzureMfaNpsExtnConfigSetup.ps1` érhető el, hogy újra futtatja, és megtartja ugyanazt a bérlői azonosítót, amikor a rendszer kéri. Ezt a folyamatot meg kell ismételni a környezet minden nps-kiszolgálóján.

## <a name="configure-your-nps-extension"></a>A hálózati kiszolgálóbővítmény konfigurálása

Ez a szakasz tervezési szempontokat és javaslatokat tartalmaz a sikeres nps-bővítmény-telepítésekhez.

### <a name="configuration-limitations"></a>Konfigurációs korlátozások

- Az Azure MFA nps-bővítménye nem tartalmazza a felhasználók és a beállítások áttelepítéséhez szükséges eszközöket az MFA-kiszolgálóról a felhőbe. Emiatt azt javasoljuk, hogy a bővítmény t használja az új központi telepítések, nem pedig a meglévő központi telepítés. Ha a bővítményt egy meglévő központi telepítésen használja, a felhasználóknak újra el kell végezniük a próba-up ot, hogy feltöltsék az MFA-adatokat a felhőben.  
- A n-ps-bővítmény a helyszíni Active Directory upn az Azure MFA-n a másodlagos hitelesítés i. A bővítmény beállítható úgy, hogy egy másik azonosítót használjon, például alternatív bejelentkezési azonosítót vagy egyéni Active Directory-mezőt, amely nem upn. További információt a hálózati [címző bővítmény többtényezős hitelesítéshez](howto-mfa-nps-extension-advanced.md)való speciális beállítási lehetőségei című cikkben talál.
- Nem minden titkosítási protokoll támogatja az összes hitelesítési módszert.
   - **A PAP** támogatja a telefonhívást, az egyirányú szöveges üzenetet, a mobilalkalmazás-értesítést és a mobilalkalmazás-ellenőrző kódot
   - **A CHAPV2** és **az EAP** támogatása telefonhívás és mobilalkalmazás-értesítés

### <a name="control-radius-clients-that-require-mfa"></a>Többéves szükséges RADIUS-ügyfelek szabályozása

Miután engedélyezte az MFA-t egy RADIUS-ügyfél számára a hálózati házirend-kiszolgáló bővítmény használatával, az ügyfél összes hitelesítése szükséges az MFA végrehajtásához. Ha egyes RADIUS-ügyfelek számára engedélyezni szeretné az MFA-t, másokat azonban nem, konfigurálhat két hálózati kiszolgálót, és csak az egyikre telepítheti a bővítményt. Konfigurálja azokat a RADIUS-ügyfeleket, amelyeket meg kíván követelni az MFA-tól, hogy a bővítménylel konfigurált hálózati házirend-kiszolgálónak és a bővítményhez nem konfigurált hálózati házirend-kiszolgálónak küldjenek kérelmeket.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Felkészülés az on-t a többkori farendszerre nem regisztrált felhasználókra

Ha olyan felhasználókkal rendelkezik, akik nincsenek beírva az MFA-hoz, meghatározhatja, hogy mi történik, amikor megpróbálnak hitelesíteni. Használja a beállításjegyzék-beállítást *REQUIRE_USER_MATCH* a rendszerleíró adatbázis elérési útján *HKLM\Software\Microsoft\AzureMFA* a szolgáltatás viselkedésének szabályozásához. Ennek a beállításnak egyetlen konfigurációs lehetősége van:

| Kulcs | Érték | Alapértelmezett |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | IGAZ/HAMIS | Nincs beállítva (egyenértékű az IGAZ értékével) |

Ennek a beállításnak az a célja, hogy meghatározza, mi a teendő, ha egy felhasználó nincs bejelentve az MFA-hoz. Ha a kulcs nem létezik, nincs beállítva, vagy értéke TRUE, és a felhasználó nincs bejelentve, akkor a bővítmény sikertelen az MFA-kihívással. Ha a kulcs értéke FALSE, és a felhasználó nincs bevezetve, a hitelesítés az MFA végrehajtása nélkül folytatódik. Ha egy felhasználó be van jelentkezve az MFA-ban, akkor is hitelesítenie kell magát az MFA-val, ha REQUIRE_USER_MATCH ÉRTÉKE FALSE.

Dönthet úgy, hogy létrehozza ezt a kulcsot, és állítsa hamis, amíg a felhasználók bevezetési, és előfordulhat, hogy még nem minden regisztrált az Azure MFA még. Mivel azonban a kulcs beállítása lehetővé teszi, hogy a felhasználók, akik nem regisztrált a többenúj-érvényesítő bejelentkezni, távolítsa el ezt a kulcsot, mielőtt éles környezetbe.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="nps-extension-health-check-script"></a>NPS-bővítmény állapot-ellenőrző parancsfájlja

A következő parancsfájl a hálózati helytárak bővítményének hibaelhárításakor alapvető állapot-ellenőrzési lépések végrehajtásához érhető el.

[MFA_NPS_Troubleshooter.ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hogyan ellenőrizhetem, hogy az ügyféltanúsítvány a várt módon van-e telepítve?

Keresse meg a telepítő által a tanúsítványtárolóban létrehozott önaláírt tanúsítványt, és ellenőrizze, hogy a személyes kulcs rendelkezik-e a **HÁLÓZATI SZOLGÁLTATÁS**felhasználónak megadott engedélyekkel. A tanúsítvány tulajdonosneve **CN \<tenantid\>, OU = Microsoft NPS Extension**

Az *AzureMfaNpsExtnConfigSetup.ps1* parancsfájl által létrehozott önaláírt tanúsítványok érvényességi élettartama is két év. A tanúsítvány telepítésekor azt is ellenőriznie kell, hogy a tanúsítvány nem járt-e le.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hogyan ellenőrizhetem, hogy az ügyféltanúsítvány az Azure Active Directoryban van-e társítva a bérlőmhöz?

Nyissa meg a PowerShell parancssorát, és futtassa a következő parancsokat:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Ezek a parancsok a bérlőt a PowerShell-munkamenetben a hálózati kiszolgálóbővítmény példányával társító tanúsítványokat nyomtatnak ki. Keresse meg a tanúsítványt az ügyféltanúsítvány "Base-64 kódolású X.509(.cer)" fájlként a személyes kulcs nélkül történő exportálásával, és hasonlítsa össze a PowerShell listájával.

A következő parancs létrehoz egy "npscertificate" nevű fájlt a "C:" meghajtón .cer formátumban.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Miután futtatta ezt a parancsot, lépjen a C meghajtóra, keresse meg a fájlt, és kattintson duplán rá. Ugrás a részletekre, és görgessen le az "ujjlenyomat", hasonlítsa össze az ujjlenyomata a tanúsítvány telepítve van a szerveren, hogy ez. A tanúsítvány ujjlenyomatának egyeznie kell.

Az ember által olvasható formában lévő Érvényesség-tól és érvényes-érvényességig az időbélyegek a nyilvánvaló misfits kiszűrésére használhatók, ha a parancs egynél több tanúsítványt ad vissza.

---

### <a name="why-cannot-i-sign-in"></a>Miért nem tudok bejelentkezni?

Ellenőrizze, hogy a jelszó nem járt-e le. A n-ps bővítmény nem támogatja a jelszavak módosítását a bejelentkezési munkafolyamat részeként. További segítségért forduljon a szervezet informatikai személyzetéhez.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Miért hibásak a kéréseim ADAL token hibával?

Ennek a hibának több oka is lehet. Az alábbi lépésekkel elháríthatja a hibaelhárítást:

1. Indítsa újra a helyi házirend-kiszolgálót.
2. Ellenőrizze, hogy az ügyféltanúsítvány a várt módon van-e telepítve.
3. Ellenőrizze, hogy a tanúsítvány társítva van-e a bérlő höz az Azure AD-n.
4. Ellenőrizze, hogy a `https://login.microsoftonline.com/` elérhető-e a bővítményt futtató kiszolgálóról.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Miért sikertelen a hitelesítés a HTTP-naplókban, amely azt jelzi, hogy a felhasználó nem található?

Ellenőrizze, hogy fut-e az AD Connect, és hogy a felhasználó jelen van-e a Windows Active Directoryban és az Azure Active Directoryban.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Miért jelennek meg a HTTP-csatlakozási hibák a naplókban, ha az összes hitelesítés sikertelen?

Ellenőrizze, hogy a https://adnotifications.windowsazure.com elérhető-e az NPS-bővítményt futtató kiszolgálóról.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Miért nem működik a hitelesítés, annak ellenére, hogy érvényes tanúsítvány van jelen?

Ha az előző számítógép-tanúsítvány lejárt, és új tanúsítvány jött létre, törölnie kell a lejárt tanúsítványokat. A lejárt tanúsítványok problémákat okozhatnak a nps bővítmény indításakor.

Ha ellenőrizni szeretné, hogy rendelkezik-e érvényes tanúsítvánnyal, ellenőrizze a helyi számítógépfiók tanúsítványtárolóját az MMC használatával, és győződjön meg arról, hogy a tanúsítvány nem járt el a lejárati dátumon. Újonnan érvényes tanúsítvány létrehozásához futtassa újra a "[PowerShell-parancsfájl futtatása](#run-the-powershell-script)" szakaszlépéseit.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>A TLS/SSL-protokollok és titkosítócsomagok kezelése

Javasoljuk, hogy a régebbi és gyengébb titkosítási csomagokletiltást vagy eltávolítást, kivéve, ha a szervezet által előírt. A tennivalókat az [AD FS által használt SSL/TLS-protokollok és titkosítócsomagok kezelését](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) ismertető rész tartalmazza.

### <a name="additional-troubleshooting"></a>További hibaelhárítás

További hibaelhárítási útmutatás és lehetséges megoldások a [Hibaüzenetek feloldása az Azure többtényezős hitelesítéshez a hálózati kiszolgáló bővítményből.](howto-mfa-nps-extension-errors.md)

## <a name="next-steps"></a>További lépések

- [A Hálózati házirend-kiszolgáló áttekintése és konfigurálása Windows Server rendszerben](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- Alternatív azonosítók konfigurálása a bejelentkezéshez, vagy olyan ip-címhez való kivétellista beállítása, amely nem hajtvégre kétlépéses ellenőrzést [a hálózati címprotokoll-bővítmény többtényezős hitelesítéshez speciális konfigurációs beállításaiban](howto-mfa-nps-extension-advanced.md)

- A Távoli [asztali átjáró](howto-mfa-nps-extension-rdg.md) és [a VPN-kiszolgálók](howto-mfa-nps-extension-vpn.md) integrálása a hálózati kiszolgálóbővítmény használatával

- [Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](howto-mfa-nps-extension-errors.md)
