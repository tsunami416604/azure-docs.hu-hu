---
title: Azure MFA-képességek biztosítása az NPS használatával – Azure Active Directory
description: Felhőalapú kétlépéses ellenőrzési funkciók hozzáadása a meglévő hitelesítési infrastruktúrához
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
ms.openlocfilehash: 43f355f22774477466d2965cef02adcc4ec4f497
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908853"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Meglévő NPS-infrastruktúra integrálása az Azure Multi-Factor Authentication

Az Azure MFA hálózati házirend-kiszolgáló (NPS) bővítménye felhőalapú MFA-képességeket biztosít a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló bővítmény használatával telefonhívást, szöveges üzenetet vagy telefonos alkalmazást is hozzáadhat a meglévő hitelesítési folyamathoz anélkül, hogy új kiszolgálókat kellene telepítenie, konfigurálnia és karbantartani. 

Ez a bővítmény olyan szervezetek számára lett létrehozva, amelyek az Azure MFA-kiszolgáló telepítése nélkül szeretnék védelemmel ellátni a VPN-kapcsolatokat. A hálózati házirend-kiszolgáló bővítmény adapterként működik a RADIUS és a felhőalapú Azure MFA között, hogy az összevont vagy szinkronizált felhasználók hitelesítésének második tényezője legyen.

Az Azure MFA NPS-bővítményének használatakor a hitelesítési folyamat a következő összetevőket tartalmazza: 

1. A **NAS/VPN-kiszolgáló** fogadja a VPN-ügyfelektől érkező kéréseket, és RADIUS-kérelmekre konvertálja azokat a hálózati házirend-kiszolgálók 
2. A **hálózati házirend-kiszolgáló** csatlakozik a Active Directoryhoz a RADIUS-kérelmek elsődleges hitelesítésének végrehajtásához, és a sikeres művelet után átadja a kérést a telepített bővítményeknek.  
3. A **hálózati házirend-kiszolgáló bővítmény** a másodlagos hitelesítésre vonatkozó kérelmet indít az Azure MFA-nak. Ha a bővítmény megkapja a választ, és ha az MFA-kérdés sikeres, akkor a hitelesítési kérést úgy hajtja végre, hogy a hálózati házirend-kiszolgálót olyan biztonsági jogkivonatokkal biztosítja, amelyekben az Azure STS által kiadott MFA-jogcím szerepel.  
4. Az **Azure MFA** Azure Active Directory a felhasználó adatainak lekérésére és a másodlagos hitelesítés elvégzésére a felhasználóhoz konfigurált ellenőrzési módszer használatával.

A következő ábra a magas szintű hitelesítési kérelmek folyamatát szemlélteti: 

![Hitelesítési folyamat diagramja](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Az üzembe helyezés megtervezése

A hálózati házirend-kiszolgáló bővítménye automatikusan kezeli a redundanciát, így nincs szükség speciális konfigurációra.

Annyi Azure MFA-kompatibilis hálózati házirend-kiszolgálót hozhat létre, amennyire csak szüksége van. Ha több kiszolgálót is telepít, akkor mindegyikhez meg kell egyeznie a különbözeti ügyféltanúsítvány használatával. Az egyes kiszolgálókhoz tartozó tanúsítványok létrehozása azt jelenti, hogy az egyes tanúsítványokat egyenként is frissítheti, és nem kell aggódnia az összes kiszolgáló leállásával szemben.

A VPN-kiszolgálók átirányítják a hitelesítési kéréseket, így tudniuk kell az új Azure MFA-kompatibilis NPS-kiszolgálókat.

## <a name="prerequisites"></a>Előfeltételek

A hálózati házirend-kiszolgáló bővítmény célja, hogy működjön a meglévő infrastruktúrával. Mielőtt elkezdené, ellenőrizze, hogy rendelkezik-e az alábbi előfeltételekkel.

### <a name="licenses"></a>Licencek

Az Azure MFA NPS-bővítménye az [azure multi-Factor Authentication-licenccel](multi-factor-authentication.md) rendelkező ügyfelek számára érhető el (prémium szintű Azure ad, EMS vagy egy MFA önálló licenccel). Az Azure MFA-hoz készült, például felhasználónként vagy hitelesítési licenccel rendelkező, fogyasztáson alapuló licencek nem kompatibilisek a hálózati házirend-kiszolgáló bővítménnyel. 

### <a name="software"></a>Szoftverek

Windows Server 2008 R2 SP1 vagy újabb.

### <a name="libraries"></a>Kódtárak

Ezek a kódtárak automatikusan települnek a bővítménnyel.

- [Vizualizációs C++ terjeszthető csomagok a visual Studio 2013 (x64) rendszerhez](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft Azure Active Directory modul a Windows PowerShell-verzió 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

A Windows PowerShell Microsoft Azure Active Directory modulja telepítve van, ha még nem létezik, a telepítési folyamat részeként futtatott konfigurációs parancsfájllal. Ha még nincs telepítve, a modult nem kell telepítenie az idő előtt.

### <a name="azure-active-directory"></a>Azure Active Directory

A hálózati házirend-kiszolgáló bővítményt használó mindenki számára Azure AD Connect használatával kell szinkronizálni Azure Active Directory, és az MFA-hoz kell regisztrálni.

A bővítmény telepítésekor szüksége lesz az Azure AD-bérlő címtár-AZONOSÍTÓra és rendszergazdai hitelesítő adataira. A címtár AZONOSÍTÓját a [Azure Portalban](https://portal.azure.com)találja. Jelentkezzen be rendszergazdaként. Keresse meg és válassza ki a **Azure Active Directory**, majd válassza a **Tulajdonságok**lehetőséget. Másolja a GUID azonosítót a **címtár-azonosító** mezőbe, és mentse. Ezt a GUID azonosítót használja bérlői azonosítóként a hálózati házirend-kiszolgáló bővítményének telepítésekor.

![A címtár AZONOSÍTÓjának megkeresése Azure Active Directory tulajdonságok területen](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az NPS-kiszolgálónak képesnek kell lennie kommunikálni a következő URL-címekkel a 80-es és a 443-es porton keresztül.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Emellett a következő URL-címekhez való kapcsolódás szükséges az [adapter megadott PowerShell-parancsfájl használatával](#run-the-powershell-script) történő végrehajtásához.

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>A környezet előkészítése

A hálózati házirend-kiszolgáló bővítmény telepítése előtt elő szeretné készíteni a környezetet a hitelesítési forgalom kezeléséhez.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Hálózati házirend-kiszolgáló szerepkör engedélyezése tartományhoz csatlakoztatott kiszolgálón

Az NPS-kiszolgáló csatlakozik Azure Active Directoryhoz, és hitelesíti az MFA-kérelmeket. Válasszon egy kiszolgálót ehhez a szerepkörhöz. Azt javasoljuk, hogy olyan kiszolgálót válasszon, amely nem kezeli más szolgáltatások kéréseit, mert a hálózati házirend-kiszolgáló bővítmény hibát jelez a nem RADIUS-kérelmeknél. A hálózati házirend-kiszolgálót a környezet elsődleges és másodlagos hitelesítési kiszolgálójának kell beállítania; a RADIUS-kérések nem adhatók át másik kiszolgálónak.

1. A kiszolgálón nyissa meg a **szerepkörök és szolgáltatások hozzáadása varázslót** a Kiszolgálókezelő Gyorsindítás menüjéből.
2. Válassza a **szerepkör-alapú vagy a szolgáltatáson alapuló telepítést** a telepítési típushoz.
3. Válassza ki a **hálózati házirend-és elérési szolgáltatások** kiszolgálói szerepkört. Előfordulhat, hogy egy ablak felugró ablakban értesíti a szükséges szolgáltatásokat a szerepkör futtatásához.
4. Folytassa a varázslót a megerősítési oldalig. Válassza az **Install** (Telepítés) lehetőséget.

Most, hogy már rendelkezik egy kiszolgálóval a hálózati házirend-kiszolgáló számára, konfigurálnia kell a kiszolgálót a bejövő RADIUS-kérések kezelésére is a VPN-megoldásból.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>VPN-megoldás konfigurálása a hálózati házirend-kiszolgálóval való kommunikációhoz

Attól függően, hogy melyik VPN-megoldást használja, a RADIUS-hitelesítési házirend konfigurálásának lépései eltérőek. Konfigurálja ezt a házirendet úgy, hogy az a RADIUS NPS-kiszolgálóra mutasson.

### <a name="sync-domain-users-to-the-cloud"></a>Tartományi felhasználók szinkronizálása a felhővel

Lehetséges, hogy ez a lépés már készen áll a bérlőre, de érdemes megnéznie, hogy a Azure AD Connect nemrég szinkronizálta-e az adatbázisokat.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként.
2. Válassza ki **Azure Active Directory** > **Azure ad Connect**
3. Ellenőrizze, hogy a szinkronizálási állapot **engedélyezve** van-e, és hogy az utolsó szinkronizálás kevesebb mint egy órával ezelőtt történt-e.

Ha új szinkronizálási kört kell kiindulnia, akkor [Azure ad Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)utasításait.

### <a name="determine-which-authentication-methods-your-users-can-use"></a>A felhasználók által használható hitelesítési módszerek meghatározása

Két tényező befolyásolja, hogy mely hitelesítési módszerek érhetők el egy NPS-bővítmény üzembe helyezésével:

1. A RADIUS-ügyfél (VPN, NetScaler-kiszolgáló vagy más) és az NPS-kiszolgálók között használt jelszó-titkosítási algoritmus.
   - A **pap** az Azure MFA összes hitelesítési módszerét támogatja a felhőben: telefonhívást, egyirányú szöveges üzenetet, a Mobile App notificationt, az esküt és a mobileszköz-ellenőrző kódot.
   - A **CHAPv2** és az **EAP** támogatja a telefonhívást és a Mobile apps-értesítést.

      > [!NOTE]
      > A hálózati házirend-kiszolgáló bővítmény központi telepítésekor ezeket a tényezőket használva kiértékelheti, hogy mely módszerek érhetők el a felhasználók számára. Ha a RADIUS-ügyfél támogatja a PAP-t, de az ügyfél UX nem tartalmaz bemeneti mezőket az ellenőrző kódhoz, akkor a telefonhívás és a Mobile App Notification a két támogatott lehetőség.
      >
      > Továbbá, ha a VPN-ügyfél UX támogatja a beviteli mezőt, és konfigurálta a hálózati hozzáférési házirendet – a hitelesítés sikeres lehet, de a hálózati házirendben konfigurált egyik RADIUS-attribútum sem lesz alkalmazva a hálózati elérési eszközre sem. mint az RRAS-kiszolgáló, sem a VPN-ügyfél. Ennek eredményeképpen előfordulhat, hogy a VPN-ügyfél több hozzáférésre van szükség, mint amennyire csak szeretné.
      >

2. Az ügyfélalkalmazás (VPN, NetScaler-kiszolgáló vagy más) által kezelhető bemeneti metódusok. A VPN-ügyfél például bizonyos módon lehetővé teszi, hogy a felhasználó szöveges vagy mobil alkalmazásból származó ellenőrző kódot írjon be?

A nem [támogatott hitelesítési módszereket letilthatja](howto-mfa-mfasettings.md#verification-methods) az Azure-ban.

### <a name="register-users-for-mfa"></a>Felhasználók regisztrálása MFA-hoz

A hálózati házirend-kiszolgáló bővítmény üzembe helyezése és használata előtt a kétlépéses ellenőrzés végrehajtásához szükséges felhasználókat regisztrálni kell az MFA-hoz. A bővítmény üzembe helyezése során még egyszer kell tesztelni, legalább egy olyan teszt fiókra van szükség, amely teljesen regisztrálva van a Multi-Factor Authentication számára.

A következő lépésekkel kérhet le egy teszt fiókot:

1. Jelentkezzen be [https://aka.ms/mfasetup](https://aka.ms/mfasetup) egy teszt-fiókkal.
2. Kövesse az utasításokat az ellenőrzési módszer beállításához.
3. [Hozzon létre egy feltételes hozzáférési szabályzatot](howto-mfa-getstarted.md#create-conditional-access-policy) a többtényezős hitelesítés megköveteléséhez a teszt fiókhoz.

## <a name="install-the-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének telepítése

> [!IMPORTANT]
> Telepítse a hálózati házirend-kiszolgáló bővítményt egy másik kiszolgálóra, mint a VPN-hozzáférési pont.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Töltse le és telepítse az Azure MFA NPS-bővítményét

1. [Töltse le a hálózati házirend-kiszolgáló bővítményt](https://aka.ms/npsmfa) a Microsoft letöltőközpontból.
2. Másolja a bináris fájlt a konfigurálni kívánt hálózati házirend-kiszolgálóra.
3. Futtassa a *Setup. exe fájlt* , és kövesse a telepítési utasításokat. Ha hibákba ütközik, ellenőrizze, hogy az előfeltételi szakasz két könyvtára sikeresen telepítve lett-e.

#### <a name="upgrade-the-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének frissítése

Ha egy meglévő NPS-bővítmény frissítését végzi, a mögöttes kiszolgáló újraindításának elkerüléséhez hajtsa végre a következő lépéseket:

1. A meglévő verzió eltávolítása
1. Az új telepítő futtatása
1. A hálózati házirend-kiszolgáló (IAS) szolgáltatás újraindítása

### <a name="run-the-powershell-script"></a>A PowerShell-parancsprogram futtatása

A telepítő létrehoz egy PowerShell-parancsfájlt a következő helyen: `C:\Program Files\Microsoft\AzureMfa\Config` (ahol a C:\ a telepítési meghajtója). Ez a PowerShell-parancsfájl minden futtatáskor végrehajtja a következő műveleteket:

- Hozzon létre egy önaláírt tanúsítványt.
- Rendelje hozzá a tanúsítvány nyilvános kulcsát az egyszerű szolgáltatásnév számára az Azure AD-ben.
- Tárolja a tanúsítványt a helyi számítógép tanúsítvány-tárolójában.
- Hozzáférés biztosítása a tanúsítvány titkos kulcsához a hálózati felhasználó számára.
- Indítsa újra a hálózati házirend-kiszolgálót.

Ha nem szeretne saját tanúsítványokat használni (a PowerShell-parancsfájl által létrehozott önaláírt tanúsítványok helyett), futtassa a PowerShell-parancsfájlt a telepítés befejezéséhez. Ha több kiszolgálóra telepíti a bővítményt, mindegyiknek saját tanúsítvánnyal kell rendelkeznie.

1. Futtassa a Windows PowerShellt rendszergazdaként.
2. Módosítsa a címtárakat.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Futtassa a telepítő által létrehozott PowerShell-szkriptet.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Jelentkezzen be az Azure AD-be rendszergazdaként.
5. PowerShell-kérések a bérlői AZONOSÍTÓhoz. Használja az előfeltételek szakaszban található Azure Portalból másolt címtár-azonosító GUID azonosítót.
6. A PowerShell a parancsfájl befejeződése után sikert jelző üzenetet jelenít meg.  

Ismételje meg ezeket a lépéseket minden olyan további hálózati házirend-kiszolgálón, amelyet be szeretne állítani a terheléselosztáshoz.

Ha az előző számítógép-tanúsítvány lejárt, és új tanúsítvány lett létrehozva, törölje a lejárt tanúsítványokat. A lejárt tanúsítványok miatt a hálózati házirend-kiszolgáló bővítménnyel kapcsolatos problémák merülhetnek fel.

> [!NOTE]
> Ha saját tanúsítványokat használ a PowerShell-parancsfájllal történő tanúsítványok létrehozása helyett, akkor győződjön meg arról, hogy a hálózati házirend-kiszolgáló elnevezési konvencióhoz vannak igazítva. A tulajdonos nevének a **CN =\<TenantID\>, OU = Microsoft NPS bővítménynek**kell lennie. 

### <a name="microsoft-azure-government-additional-steps"></a>További lépések Microsoft Azure Government

Azure Government felhőt használó ügyfelek esetén a következő további konfigurációs lépésekre van szükség az egyes NPS-kiszolgálókon:

1. Nyissa meg a **Rendszerleíróadatbázis-szerkesztőt** az NPS-kiszolgálón.
1. Nyissa meg a `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa` címet. Állítsa be a következő kulcs értékeit:

    | Beállításkulcs       | Value (Díj) |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. Ismételje meg az előző két lépést az egyes NPS-kiszolgálók beállításkulcs-értékeinek beállításához.
1. Indítsa újra a hálózati házirend-kiszolgáló szolgáltatást az egyes NPS-kiszolgálókon.

    A minimális hatás érdekében minden hálózati házirend-kiszolgálót ki kell kapcsolni az NLB-rotációból, és várnia kell, amíg az összes kapcsolat el nem telik.

### <a name="certificate-rollover"></a>Tanúsítvány-rollover

A hálózati házirend-kiszolgáló bővítmény kiadási 1.0.1.32 a több tanúsítvány olvasása mostantól támogatott. Ezzel a képességgel a lejáratuk előtt megkönnyítheti a működés közbeni tanúsítványok frissítését. Ha a szervezete a hálózati házirend-kiszolgáló bővítmény korábbi verzióját futtatja, frissítsen a 1.0.1.32 vagy újabb verzióra.

A `AzureMfaNpsExtnConfigSetup.ps1` parancsfájl által létrehozott tanúsítványok 2 évig érvényesek. Az informatikai szervezeteknek meg kell figyelniük a lejárati tanúsítványokat. A hálózati házirend-kiszolgáló kiterjesztéséhez tartozó tanúsítványok a helyi számítógép tanúsítványtárolójában, a személyes és a parancsfájlhoz megadott bérlői AZONOSÍTÓra kerülnek.

Ha egy tanúsítvány a lejárati dátumra közeledik, egy új tanúsítványt kell létrehozni a lecseréléséhez.  Ez a folyamat a `AzureMfaNpsExtnConfigSetup.ps1` újbóli futtatásával, és a kéréskor megegyező bérlői azonosító megtartásával valósítható meg. Ezt a folyamatot meg kell ismételni a környezet minden NPS-kiszolgálóján.

## <a name="configure-your-nps-extension"></a>A hálózati házirend-kiszolgáló bővítményének konfigurálása

Ez a szakasz tervezési szempontokat és javaslatokat tartalmaz a hálózati házirend-bővítmények sikeres üzembe helyezéséhez.

### <a name="configuration-limitations"></a>Konfigurációs korlátozások

- Az Azure MFA NPS-bővítménye nem tartalmaz olyan eszközöket, amelyekkel a felhasználók és beállítások áttelepíthetők az MFA-kiszolgálóról a felhőbe. Ezért javasoljuk, hogy a meglévő telepítés helyett a bővítményt használja az új központi telepítésekhez. Ha a bővítményt egy meglévő üzemelő példányon használja, a felhasználóknak újra kell végezniük az MFA adatainak a felhőben való feltöltéséhez.  
- A hálózati házirend-kiszolgáló bővítmény a helyszíni Active Directoryból származó UPN-t használja az Azure MFA-felhasználó azonosításához a másodlagos hitelesítés végrehajtásához. A bővítmény konfigurálható úgy, hogy más azonosítót használjon, például alternatív bejelentkezési azonosítót vagy egyéni Active Directory mezőt, amely nem egyszerű felhasználónév. További információkért tekintse meg a [multi-Factor Authentication hálózati házirend-bővítményének speciális konfigurációs beállításait](howto-mfa-nps-extension-advanced.md)ismertető cikket.
- Nem minden titkosítási protokoll támogatja az összes ellenőrzési módszert.
   - A **pap** támogatja a telefonhívást, egyirányú szöveges üzenetet, a Mobile App notificationt és a Mobile App ellenőrző kódját
   - A **CHAPv2** és az **EAP** támogatásának telefonos hívása és a Mobile App Notification

### <a name="control-radius-clients-that-require-mfa"></a>Az MFA-t igénylő RADIUS-ügyfelek szabályozása

Miután engedélyezte az MFA-t egy RADIUS-ügyfél számára a hálózati házirend-kiszolgáló bővítmény használatával, az ügyfél összes hitelesítése szükséges az MFA végrehajtásához. Ha bizonyos RADIUS-ügyfelek esetében engedélyezni szeretné az MFA-t, de mások nem, akkor két hálózati házirend-kiszolgálót is beállíthat, és a bővítményt csak az egyikre telepítheti. Konfigurálja azokat a RADIUS-ügyfeleket, amelyeknek az MFA-t szeretné megkövetelni a bővítménysel konfigurált hálózati házirend-kiszolgálónak küldött kérések küldéséhez, valamint más RADIUS-ügyfeleket az NPS-kiszolgálóhoz, amely nincs beállítva a bővítménnyel.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Az MFA-ban nem regisztrált felhasználók előkészítése

Ha az MFA-ban nem regisztrált felhasználók vannak, akkor megadhatja, hogy mi történjen a hitelesítéskor. A szolgáltatás működésének vezérléséhez használja a beállításjegyzék elérési útja *HKLM\Software\Microsoft\AzureMFA* *REQUIRE_USER_MATCH* beállításjegyzékbeli beállítását. Ez a beállítás egyetlen konfigurációs lehetőséggel rendelkezik:

| Jelmagyarázat | Value (Díj) | Alapértelmezett |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | IGAZ/HAMIS | Nincs beállítva (megegyezik az igaz értékkel) |

Ennek a beállításnak a célja annak meghatározása, hogy mi a teendő, ha egy felhasználó nincs regisztrálva az MFA-hoz. Ha a kulcs nem létezik, nincs beállítva, vagy igaz értékre van állítva, és a felhasználó nincs regisztrálva, akkor a bővítmény meghiúsul az MFA-kérdésben. Ha a kulcs hamis értékre van állítva, és a felhasználó nincs regisztrálva, a hitelesítés az MFA végrehajtása nélkül folytatódik. Ha egy felhasználó regisztrálva van az MFA-ban, akkor is hitelesítenie kell magát az MFA-val, ha REQUIRE_USER_MATCH hamis értékre van állítva.

Dönthet úgy, hogy létrehozza ezt a kulcsot, és FALSE (hamis) értékre állítja a felhasználók bevezetését, és az Azure MFA még nem minden esetben regisztrálható. Mivel azonban a kulcs beállítása lehetővé teszi, hogy az MFA-ban nem regisztrált felhasználók bejelentkezzenek, el kell távolítania ezt a kulcsot az éles környezetbe való belépés előtt.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="nps-extension-health-check-script"></a>NPS-bővítmény állapot-ellenőrzési parancsfájlja

A következő parancsfájl a TechNet Gallery webhelyen érhető el, amely alapszintű állapot-ellenőrzési lépéseket hajt végre a hálózati házirend-kiszolgáló bővítményének hibaelhárításakor.

[MFA_NPS_Troubleshooter. ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Hogyan ellenőrizze, hogy az ügyféltanúsítvány a várt módon van-e telepítve?

Keresse meg a telepítő által a tanúsítvány-tárolóban létrehozott önaláírt tanúsítványt, és ellenőrizze, hogy a titkos kulcs rendelkezik-e a felhasználói **hálózati szolgáltatáshoz**megadott engedélyekkel. A tanúsítvány tulajdonosának neve **CN \<tenantid\>, OU = Microsoft NPS bővítmény**

A *AzureMfaNpsExtnConfigSetup. ps1* parancsfájl által létrehozott önaláírt tanúsítványok érvényességi ideje két év is lehet. A tanúsítvány telepítésének ellenőrzésekor azt is ellenőriznie kell, hogy a tanúsítvány nem járt-e le.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Hogyan tudom ellenőrizni, hogy az ügyféltanúsítvány társítva van-e a bérlőhöz a Azure Active Directory?

Nyisson meg egy PowerShell-parancssort, és futtassa a következő parancsokat:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Ezek a parancsok kinyomtatják a bérlőhöz társító összes tanúsítványt a PowerShell-munkamenetben lévő NPS-bővítmény példányával. Keresse meg a tanúsítványt úgy, hogy az ügyfél tanúsítványát "Base-64 kódolt X. 509 (. cer)" fájlként exportálja a titkos kulcs nélkül, és összehasonlítja a listával a PowerShell-lel.

A következő parancs létrehoz egy "npscertificate" nevű fájlt a "C:" meghajtón a Format. cer fájlban.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

A parancs futtatása után lépjen a C meghajtóra, keresse meg a fájlt, és kattintson rá duplán. Lépjen a részletek elemre, és görgessen le az "ujjlenyomat" kifejezésre, hasonlítsa össze a kiszolgálón telepített tanúsítvány ujjlenyomatát. A tanúsítvány ujjlenyomatai megfelelnek egyeznie kell.

Az érvényes – és az érvényes – csak az emberi olvashatóságot eredményező időbélyegek esetében használható a nyilvánvaló Misfits kiszűrésére, ha a parancs egynél több tanúsítványt ad vissza.

---

### <a name="why-cannot-i-sign-in"></a>Miért nem tudok bejelentkezni?

Győződjön meg arról, hogy a jelszó még nem járt le. A hálózati házirend-kiszolgáló bővítmény nem támogatja a jelszavak módosítását a bejelentkezési munkafolyamat részeként. További segítségért forduljon a szervezet informatikai részlegéhez.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Miért nem sikerül a kérések ADAL-jogkivonat hibája?

Ezt a hibát számos ok okozhatja. A következő lépésekkel segíthet a hibaelhárításban:

1. Indítsa újra a hálózati házirend-kiszolgálót.
2. Ellenőrizze, hogy az ügyféltanúsítvány a várt módon van-e telepítve.
3. Ellenőrizze, hogy a tanúsítvány társítva van-e a bérlőhöz az Azure AD-ben.
4. Ellenőrizze, hogy a https://login.microsoftonline.com/ elérhető-e a bővítményt futtató kiszolgálóról.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Miért sikertelen a hitelesítés a HTTP-naplókban, hogy a felhasználó nem található?

Ellenőrizze, hogy az AD-kapcsolat fut-e, és hogy a felhasználó szerepel-e a Windows Active Directoryban és a Azure Active Directory is.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Miért látok HTTP-csatlakozási hibákat a naplókban az összes saját hitelesítéssel?

Ellenőrizze, hogy a https://adnotifications.windowsazure.com elérhető-e az NPS-bővítményt futtató kiszolgálóról.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Miért nem működik a hitelesítés, annak ellenére, hogy érvényes tanúsítvány van jelen?

Ha az előző számítógép-tanúsítvány lejárt, és új tanúsítvány lett létrehozva, törölje a lejárt tanúsítványokat. A lejárt tanúsítványok miatt a hálózati házirend-kiszolgáló bővítménnyel kapcsolatos problémák merülhetnek fel.

Annak ellenőrzéséhez, hogy érvényes tanúsítvánnyal rendelkezik-e, ellenőrizze a helyi számítógépfiók tanúsítványtárolóját az MMC használatával, és győződjön meg arról, hogy a tanúsítvány nem felelt meg a lejárati dátumnak. Egy újonnan érvényes tanúsítvány létrehozásához futtassa újra a "[PowerShell-parancsfájl futtatása](#run-the-powershell-script)" szakasz lépéseit.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>A TLS/SSL-protokollok és titkosítócsomagok kezelése

Javasoljuk, hogy a régebbi és a gyengébb titkosítási csomagokat tiltsa le, vagy távolítsa el, ha a szervezet nem igényli. A tennivalókat az [AD FS által használt SSL/TLS-protokollok és titkosítócsomagok kezelését](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) ismertető rész tartalmazza.

### <a name="additional-troubleshooting"></a>További hibaelhárítás

További hibaelhárítási útmutató és lehetséges megoldások találhatók az [Azure-multi-Factor Authentication hálózati házirend-kiszolgáló bővítményében található hibaüzenetek feloldása](howto-mfa-nps-extension-errors.md)című cikkben.

## <a name="next-steps"></a>Következő lépések

- Konfiguráljon alternatív azonosítókat a bejelentkezéshez, vagy állítson be olyan IP-címekre vonatkozó kivételeket, amelyek nem hajtják végre a kétlépéses ellenőrzést a [hálózati házirend-kiszolgáló bővítményének speciális konfigurációs beállításaiban multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Ismerje meg, hogyan integrálhatja a [Távoli asztali átjáró](howto-mfa-nps-extension-rdg.md) és a [VPN-kiszolgálókat](howto-mfa-nps-extension-vpn.md) a hálózati házirend-kiszolgáló bővítmény használatával

- [Hibaüzenetek által jelzett problémák megszüntetése az Azure Multi-Factor Authentication NPS-bővítményéből](howto-mfa-nps-extension-errors.md)
