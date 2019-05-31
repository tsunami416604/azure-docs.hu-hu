---
title: Üzembe helyezése az Azure felügyelt munkaállomások – Azure Active Directory
description: Ismerje meg, hogyan helyezhet üzembe az Azure által kezelt biztonságos munkaállomásokra, illetéktelen behatolás vagy biztonsági sérülés kiszolgálóhiba miatt kockázatának csökkentése érdekében
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 242926c0821e4951d2a2bd2f858f63691baf1017
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307227"
---
# <a name="deploy-a-secure-workstation"></a>Egy biztonságos munkaállomás üzembe helyezése

Most, hogy megismerkedett [miért fontos munkaállomás hozzáférés biztonságossá tétele?](concept-azure-managed-workstation.md) kezdje meg a rendelkezésre álló eszközökkel üzembe helyezés ideje. Ez az útmutató a definiált profilok használatával hozzon létre egy munkaállomás, amely a kezdetektől biztonságosabb.

![A biztonságos munkaállomás üzembe helyezés](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

A megoldás üzembe helyezése előtt ki kell jelölni a profilt, amelyiket használni fog. Fontos megjegyezni, hogy minden, a kiválasztott profil alkalmazása, és helyezze át egy másik teheti a profilt az Intune-ban a követelmény alapján. Több profilt egy központi telepítésben használja egyszerre, és rendelve a címke vagy a csoport-hozzárendelést használ.

| Profil | Alacsony | Továbbfejlesztett | Magas | Specializált | Védett | Izolált |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Az Azure AD-felhasználó | Igen | Igen | Igen | Igen | Igen | Igen |
| Intune által felügyelt | Igen | Igen | Igen | Igen | Igen | Igen |
| Az Azure ad-ben regisztrált eszközök | Igen |  |  |  |  | |   |
| Az Azure AD-hoz csatlakoztatott eszköz |   | Igen | Igen | Igen | Igen | Igen |
| Intune-ban a alkalmazni alapvető biztonsági |   | Igen <br> (Speciális) | Igen <br> (HighSecurity) | Igen <br> (NCSC) | Igen <br> (Védett) |  n/a |
| Hardver megfelel-e a Windows 10-es biztonságos szabványok |   | Igen | Igen | Igen | Igen | Igen |
| A Microsoft Defender ATP engedélyezése |   | Igen  | Igen | Igen | Igen | Igen |
| A rendszergazdai jogosultságok eltávolítása |   |   | Igen  | Igen | Igen | Igen |
| Üzembe helyezés, a Microsoft az Autopilot használata |   |   | Igen  | Igen | Igen | Igen |
| Csak az Intune által telepített alkalmazások |   |   |   | Igen | Igen |Igen |
| Csak a jóváhagyott listához korlátozott URL-címek |   |   |   | Igen | Igen |Igen |
| Internet (bejövő/kimenő letiltva) |   |   |   |  |  |Igen |

## <a name="license-requirements"></a>Licenckövetelmények

A fogalmakat Ez az útmutató feltételezi, a Microsoft 365 nagyvállalati E5 csomag vagy egy azzal egyenértékű Termékváltozat. Az útmutató a javaslatok némelyike az alacsonyabb termékváltozatok kell végrehajtani. További információ található a [Microsoft 365 Enterprise licencelés](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Érdemes lehet konfigurálása [Csoportalapú licencelés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) automatikus üzembe helyezés a licencek, a felhasználók számára.

## <a name="azure-active-directory-configuration"></a>Az Azure Active Directory konfigurálása

Konfigurálása az Azure Active Directory (Azure AD) könyvtár, amely kezeli a felhasználók, csoportok, és a rendszergazdai munkaállomások eszközök igényel, identitás-szolgáltatások és a funkciókat engedélyezni egy [rendszergazdai fiók](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

A biztonságos munkaállomás rendszergazdai fiók létrehozásakor, a fiók az aktuális munkaállomásra van ki. Ajánlott végezhet el a kezdeti konfigurálásának és a egy ismert megbízható eszközről minden globální konfiguraci. Érdemes lehet a útmutatóval [kártevő szoftverrel való fertőzés megelőzése](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) a kockázata, hogy először az első élmény, a támadás csökkentése érdekében.

Szervezetek kell többtényezős hitelesítés megkövetelése, legalább a rendszergazdák számára. Lásd: [felhőalapú MFA üzembe helyezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) útmutatásért végrehajtását.

### <a name="azure-ad-users-and-groups"></a>Az Azure AD-felhasználók és csoportok

Az Azure Portalon keresse meg a **Azure Active Directory** > **felhasználók** > **új felhasználó**. [A biztonságos munkaállomás-felhasználó létrehozása](https://docs.microsoft.com/Intune/quickstart-create-user), akik lesz az eszköz-rendszergazdai.

* **Név** – biztonságos felügyeleti munkaállomás
* **Felhasználónév** - secure-ws-admin@identityitpro.com
* **Címtárbeli szerepkör** - **korlátozott rendszergazda** , és válassza ki a következő rendszergazdai szerepkör
   * **Intune-rendszergazda**
* **Létrehozás**

Hozunk létre két csoport egy, a felhasználók számára a munkaállomások és a egy magukat a munkaállomások számára. Az Azure Portalon keresse meg a **Azure Active Directory** > **csoportok** > **új csoport**

Első csoport munkaállomás-felhasználók számára. Érdemes lehet konfigurálása [Csoportalapú licencelés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) automatikus licencek felhasználóknak történő üzembe helyezés a csoportban lévő felhasználók számára.

* **Csoport típusa** -biztonság
* **Csoport neve** -munkaállomások felhasználóinak biztonságossá tétele
* **Tagság típusa** – hozzárendelt
* A biztonságos munkaállomás rendszergazda felhasználó hozzáadása a csoporthoz
   * secure-ws-admin@identityitpro.com
* Hozzáadhat a csoporthoz biztonságos munkaállomások felügyel, más felhasználók
* **Létrehozás**

Második csoport munkaállomás-eszközökhöz.

* **Csoport típusa** -biztonság
* **Csoport neve** -munkaállomások biztonságossá tétele
* **Tagság típusa** – hozzárendelt
* **Létrehozás**

### <a name="azure-ad-device-configuration"></a>Az Azure AD-eszközök konfigurálása

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Adja meg, akik csatlakozhat az Azure AD-eszközök

Az eszközök konfigurálásához, hogy a felügyeleti biztonsági csoport az eszközök csatlakoztatása a tartományhoz az Active Directory beállítása. Ezt a beállítást, az Azure Portalon konfigurálhatja, keresse meg a **Azure Active Directory** > **eszközök** > **eszközbeállítások**. Válasszon **kijelölt** alatt **felhasználók eszközöket csatlakoztathatnak az Azure AD** , és válassza ki a "Biztonságos munkaállomás-felhasználók" csoportot.

#### <a name="removal-of-local-admin-rights"></a>A helyi rendszergazdai jogosultságok eltávolítása

A bevezetés részeként munkaállomások felhasználóinak a virtuális IP-CÍMEK, a DevOps és a biztonságos szintű munkaállomások nincsenek rendszergazdai jogok lesz a számítógépükhöz. Ezt a beállítást, az Azure Portalon konfigurálhatja, keresse meg a **Azure Active Directory** > **eszközök** > **eszközbeállítások**. Válassza ki **nincs** alatt **további helyi rendszergazdák az Azure AD-hez csatlakoztatott eszközök**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése

Az Azure AD-eszközök csatlakoztatása a folyamat megerősítésére, keresse meg a **Azure Active Directory** > **eszközök** > **eszközbeállítások** Válasszon **Igen** alatt **a többtényezős hitelesítés megkövetelése eszközök csatlakoztatásához** majd **mentése**.

#### <a name="configure-mdm"></a>Mobileszköz-kezelési konfigurálása

Az Azure Portalon keresse meg a **Azure Active Directory** > **mobilitás (MDM és MAM)**  > **a Microsoft Intune**. Módosítsa a beállítást **MDM felhasználói hatókör** való **összes** válassza **mentése** , hogy lehetővé teszi bármely ebben a forgatókönyvben az Intune által kezelt eszköz. További információ a cikkben található [Intune-ban a rövid útmutató: A Windows 10-es eszközök automatikus regisztrációjának beállítása](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Az Intune konfigurációs és megfelelőségi szabályzatok egy későbbi lépésben hozunk létre.

#### <a name="azure-ad-conditional-access"></a>Azure AD feltételes hozzáférés

Az Azure AD feltételes hozzáférés is gondoskodnak az emelt szintű rendszergazdai feladatokkal a feltételeknek megfelelő eszközökön. Felhasználók tagjai kell, hogy meghatároztuk a **biztonságos munkaállomások felhasználóinak** csoporthoz kell adnia a multi-factor authentication végrehajtása a felhőbeli alkalmazásokhoz való bejelentkezéskor. Hogy kövesse az ajánlott eljárásokkal kapcsolatos útmutatás, és a vészelérési fiókok zárni a szabályzatból. További információ a cikkben található [vészelérési fiókok kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

Konfigurálható a feltételes hozzáférés az Azure Portalról, keresse meg a **Azure Active Directory** > **feltételes hozzáférési** > **új szabályzat**.

* **Név** – biztonságos eszköz házirend szükséges
* Hozzárendelések
   * **Felhasználók és csoportok**
      * Például - **felhasználók és csoportok** – válassza ki a **biztonságos munkaállomások felhasználóinak** korábban létrehozott csoportot
      * Kizárása - **felhasználók és csoportok** – válassza ki a szervezet a vészelérési fiókok
   * **Felhőalkalmazások**
      * Például - **az összes felhőalapú alkalmazások**
* Hozzáférés-vezérlés
   * **Engedélyezés** – ki **hozzáférést** választógomb
      * **Többtényezős hitelesítés megkövetelése**
      * **Megfelelőként megjelölt eszköz megkövetelése**
      * Több vezérlő – **az összes kijelölt vezérlő megkövetelése**
* Házirend - engedélyezése **a**

Szervezetek igény szerint lenne nem elérhetik a vállalati erőforrások letiltása országok házirendeket hozhat létre. További információ a IP-helyalapú feltételes hozzáférési házirendek a cikkben található [a helyfeltétel Mi az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Intune-konfiguráció

### <a name="configure-enrollment-status"></a>Regisztrációs állapot konfigurálása

Az ellátásilánc-kezelési leírtaknak biztosítása a biztonságos munkaállomás tiszta megbízható eszköz azt javasoljuk, hogy ha a szolgáltatást, amely az eszközök gyári értékre kell új eszközök [Windows 10 Pro S módban](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), amely korlátozza a és biztonsági résekről, ellátásilánc-kezelési során. Ha egy eszköz a nyertes érkezik, az eszköz az Autopilot használata S módról törlődni fog. Az alábbi útmutatás részletesen az átalakítási folyamat alkalmazásával.

Győződjön meg arról, hogy eszközök teljes körűen konfigurálva használata előtt szeretnénk. Az Intune lehetővé teszi **letiltja az eszköz használatát, alkalmazások és a profilok telepítéséig**. 

1. Az a **az Azure portal** keresse meg:
1. **A Microsoft Intune** > **eszközregisztráció** > **Windows regisztrációs** > **regisztrálási állapot oldal (előzetes verzió)**  >  **Alapértelmezett** > **beállítások**.
1. Állítsa be **alkalmazás profil telepítés állapotának megjelenítése** való **Igen**.
1. Állítsa be **letiltja az eszköz használatát, alkalmazások és a profilok telepítéséig** való **Igen**.

### <a name="create-an-autopilot-deployment-profile"></a>AutoPilot üzembehelyezési profil létrehozása

Miután létrehozott egy eszközcsoportot, üzembe helyezési profilt kell létrehoznia, így konfigurálhatja az Autopilot-eszközöket.

1. Az Intune-ban az Azure Portalon, válassza ki a **eszközregisztráció** > **Windows regisztrációs** > **Deployment-profilok**  >   **Profil létrehozása**.
1. Neveként adja meg **munkaállomás üzembe helyezési profil biztonságos**. Adja meg a leírást **biztonságos munkaállomások telepítési**.
1. Készlet az összes megcélzott eszköz yes Autopilot típusra konvertál. Ez a beállítás gondoskodik arról, hogy a lista minden eszköz regisztrálva az Autopilot deployment szolgáltatással.  A regisztráció feldolgozása 48 órát is igénybe vehet.
1. A központi telepítési üzemmódnál válassza **helyi üzembe helyezése (előzetes verzió)** . A felhasználó regisztrálja az eszközt a profilt használó eszközök hozzárendelve. Az eszköz regisztrálásához felhasználói hitelesítő adatokra van szükség.
1. Az Azure AD-be, mint a JOIN **Azure AD-hez** kell kiválasztani, és szürkén jelenik meg.
1. Válassza ki a beépített élmény (OOBE), állítsa be a következő beállítást, és hagyja az alapértelmezett értékre, és válassza ki **Ok**:
   1. Felhasználói fiók típusa: **Standard**
1. Válassza a **Létrehozás** gombot a profil létrehozásához. Az AutoPilot üzembehelyezési profil most már hozzárendelhető az eszközökhöz.
1. Válasszon **hozzárendelések** > **hozzárendelése** > **kijelölt csoportok**
   1. **Válassza ki a befoglalandó csoportokat** -munkaállomások felhasználóinak biztonságossá tétele

### <a name="configure-windows-update"></a>Windows Update konfigurálása

A legfontosabb témák teheti a szervezet egyik, tartsa naprakészen a Windows 10-es. Annak érdekében, hogy a Windows 10-es biztonságos állapotban tartani, fogjuk üzembe helyezni a tempójában, amikor frissítések alkalmazása az munkaállomások kezelése frissítési kört. Ez a konfiguráció megtalálható a **az Azure portal** > **a Microsoft Intune** > **szoftverfrissítések**  >  **Windows 10-es frissítési körök**.

Fogunk **létrehozás** egy új frissítési kör a következő beállításokkal megváltozott az alapértelmezett értékeket.

* Név – **Azure munkaállomás frissítéseinek felügyeletét.**
* Karbantartási csatorna - **Windows Insider – gyors**
* Minőségi frissítés halasztási (nap) – **3**
* Szolgáltatásfrissítések elhalasztása (nap) – **3**
* Automatikus frissítés viselkedése - **automatikus telepítés és újraindítás végfelhasználói irányítás nélkül**
* Windows-frissítések felfüggesztése – a felhasználó **letiltása**
* Indítsa újra a munkaidő - en kívül a felhasználó jóváhagyást igényel **szükséges**
* Lehetővé teszi a felhasználónak újra kell indítania (szerepet játszanak újraindítás) – **szükséges**
   * Felhasználók szerepet játszanak újraindítás áttérés után az automatikus újraindítás (nap) – **3**
   * Késleltetés szerepet játszanak újraindítás emlékeztető (nap) – **3**
   * Állítsa be a határidő függőben lévő újraindul (nap) – **3**

Kattintson a **létrehozás** ezután a a **hozzárendelések** lap hozzáadása a **munkaállomások biztonságossá** csoport kizárt csoportként.

További információ a Windows Update-szabályzatok található [házirend CSP - frissítés](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>A Windows Defender ATP Intune-integráció

A Windows Defender ATP-ben és a Microsoft Intune együttesen segít megelőzni a biztonsági incidenseket, és segít korlátozhatják a szabályok megsértése esetén alkalmazandók. A képességeket biztosítja a valós idejű észlelését. Az üzembe helyezés kiterjedt vizsgálat és a naplózás a végponti eszközök ATP is biztosít.

A Windows Defender ATP Intune-integráció konfigurálása az Azure Portalon, keresse meg a **a Microsoft Intune** > **eszközmegfelelőség** > **Windows Defender ATP-ben** .

1. A 1. lépésében **konfigurálása a Windows Defender ATP**, kattintson a **csatlakoztatása a Windows Defender ATP és a Microsoft Intune a Windows Defender biztonsági központ**.
1. A Windows Defender biztonsági központban:
   1. Válassza ki **beállítások** > **speciális funkciók**
   1. A **a Microsoft Intune kapcsolatot**, válassza a **a**
   1. Válassza ki **beállítások mentése**
1. A kapcsolat létrejötte után térjen vissza az Intune, és kattintson **frissítése** tetején.
1. Állítsa be **csatlakozás Windows 10.0.15063 eszközök verzió vagy újabb a Windows Defender ATP** való **a**.
1. **Mentés**

További információ a cikkben található [Windows Defender komplex veszélyforrások elleni védelem](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>A munkaállomás-profil vezethet be befejezése

A megoldás a megerősítés sikeres végrehajtásához töltse le, és hajtsa végre a parancsfájlt a kívánt alapján **szint profil** a következő diagram.

| Profil | Letöltési hely | Fájlnév |
| --- | --- | --- |
| Alacsony biztonságú | – |  – |
| Fokozott biztonság | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| A fokozott biztonságot  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializált | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC – Windows 10-es (1803) SecurityBaseline.ps1 |
| Speciális megfelelőségi * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Védett | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Speciális megfelelőségi * egy parancsfájlt, amely kényszeríti a Windows 10-es NCSC SecurityBaseline megtalálható a speciális konfigurációs.

A kiválasztott parancsfájlok sikeres végrehajtása után a profilok és szabályzatok frissítések lehet kapcsolódni a Microsoft Intune-ban. A parancsfájlok bővített és biztonságos profilok létrehozása a házirendek és profilok számára, de hozzá kell rendelnie a szabályzatot a **munkaállomások biztonságossá** csoport.

* Intune eszközkonfigurációs profilok a parancsfájlok által létrehozott megtalálható a **az Azure portal** > **a Microsoft Intune** > **eszközkonfiguráció**  >  **Profilok**.
* A parancsfájlok által létrehozott Intune-eszközmegfelelőségi szabályzatok megtalálható a **az Azure portal** > **a Microsoft Intune** > **Eszközmegfelelőség**  >  **Házirendek**.

Tekintse át a módosításokat is exportálhatja a profilok, és módosítások alkalmazása az exportfájl, ahogyan a SECCON dokumentációt és további korlátozására, amelyek alapján szükség.

Parancsfájl futtatása az Intune-ban adatok exportálása `DeviceConfiguration_Export.ps1` származó a [eszközkonfiguráció GiuHub tárház](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) biztosít az aktuális Exportálás az összes meglévő Intune-profilt.

## <a name="additional-configurations-and-hardening-to-consider"></a>További konfigurációs beállítások és a megfontolandó biztonságának megerősítése

Az útmutató egy biztonságos munkaállomás engedélyezve van, további vezérlők figyelembe kell venni, alternatív böngészők elérni, mint például kimenő HTTP engedélyezett és letiltott webhelyeket, valamint az egyéni PowerShell-szkript futtatásához.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>A tűzfal konfigurációszolgáltató (CSP) korlátozó bejövő és kimenő szabályok

További felügyeleti, kimenő és bejövő szabályok az engedélyezett és letiltott végpontok megfelelően frissíthető. Továbbra is a biztonságos munkaállomás erősíti, hogy helyezze át a korlátozás az összes bejövő és kimenő az alapértelmezett megtagadási és hozzáadása az engedélyezett helyek esetében a kimenő, hogy a gyakori és a megbízható webhelyek. A cikkben található a konfigurációs információkat a tűzfal-konfigurációs szolgáltató [tűzfal CSP](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Korlátozott alapértelmezett ajánlások a következők:

* Az összes bejövő megtagadása
* Összes kimenő megtagadása

A Spamhaus projekt listát tart fenn jó, hogy a szervezetek használhatják kiindulási pontként, más néven helyek blokkolásához [a tartomány letiltása lista (dupla)](https://www.spamhaus.org/dbl/).

### <a name="managing-local-applications"></a>Helyi alkalmazások kezelése

Helyi alkalmazások eltávolítása, beleértve a csomagot a hatékonyságnövelő alkalmazásokat fogja a biztonságos munkaállomás valóban megerősített állapotba helyezheti. Ebben a példában azt hozzáadja Chrome az alapértelmezett böngésző és módosítása a böngésző, beleértve a beépülő modulok korlátozhatják az Intune-nal.

#### <a name="deploy-applications-using-intune"></a>Alkalmazások üzembe helyezése az Intune használatával

Bizonyos esetekben olyan alkalmazások, mint a Google Chrome böngésző szükséges a biztonságos munkaállomáson. Az alábbi példa tartalmazza azokat az eszközöket a biztonsági csoport Chrome telepítésére vonatkozó utasításokat tartalmaz **munkaállomások biztonságossá** korábban létrehozott.

1. Töltse le az offline [Windows 64‑bit Chrome köteg](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Bontsa ki a fájlokat, és jegyezze fel a helyét a `GoogleChromeStandaloneEnterprise64.msi` telepítése az Intune-nal
1. Az a **az Azure portal** keresse meg a **a Microsoft Intune** > **ügyfélalkalmazás** > **alkalmazások**  >  **Hozzáadása**
1. A **alkalmazástípus**, válassza a **üzleti**
1. A **alkalmazáscsomag-fájl**, jelölje be a `GoogleChromeStandaloneEnterprise64.msi` a kinyert helyét, majd kattintson a **OK**
1. A **alkalmazásadatok**, adjon meg egy leírást és a közzétevő, és válassza a **OK**
1. Kattintson a **hozzáadása**
1. Az a **hozzárendelések** kiválasztása **regisztrált eszközökhöz elérhető** alatt **hozzárendelés-típus**
1. A **belefoglalt csoportok**, adja hozzá a **munkaállomások biztonságossá** korábban létrehozott csoportot
1. Kattintson a **OK** majd **mentése**

További útmutató a Chrome-beállítások konfigurálása a támogatási cikk található [Chrome böngészőben kezelése a Microsoft Intune-nal](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Az egyéni alkalmazások a vállalati portál konfigurálása

Biztonságos módban működnek az alkalmazások telepítéséről lesz korlátozva az Intune céges portálon. Azonban Microsoft Store-hozzáférés a portál telepítése szükséges. A biztonságos megoldás azt fogja, hogy a portál elérhető minden eszköz egy offline üzemmód a vállalati portál használatával.

Telepíti az Intune által felügyelt példányát a [céges portál](https://docs.microsoft.com/Intune/store-apps-company-portal-app) lehetővé teszi a felhasználók számára a védett munkaállomások igény szerinti le további eszközök leküldéses lehetővé teszi.

Egyes szervezetek fiókdíjat kell telepíteni a Windows 32 bites vagy egyéb előkészített, üzembe helyezéséhez igénylő alkalmazásokkal. Ezekhez az alkalmazásokhoz a [Microsoft win32-tartalom előkészítő eszköze](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) felhasználásra kész biztosít `.intunewin` formátumfájl telepítéséhez.

### <a name="setting-up-custom-settings-using-powershell"></a>Egyéni beállítások a PowerShell-lel beállítása

Egy példa bővíthetőséget tesz lehetővé a gazdagép-kezelés a PowerShell használatával is használjuk. A parancsprogram beállítja a gazdagépen egy alapértelmezett hátteret. Ez a funkció is profilok érhető el, és csak a funkció bemutatására szolgál.

A megoldás lehet biztonságos munkaállomásokon néhány egyéni vezérlők és a beállítások beállítása szükséges. Ebben a példában módosítjuk a Powershell segítségével tud könnyen azonosíthatja az eszköz használatra kész biztonságos munkaállomásként munkaállomás a háttérben. Példánkban PowerShell segítségével befejezheti a feladatot, miközben azt is elvégezhető az Azure Portalon.

Példánkban fogja használni a következő [ingyenes általános háttérkép](https://i.imgur.com/OAJ28zO.png) és a [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) a Microsoft Scripting Center, hogy a Windows betöltéséhez a háttérben a Start menüben.

1. Töltse le a szkriptet a helyi eszközre
1. A customerXXXX és a letöltési helye a hátteret szeretne használni a szkriptben, hogy a háttér-fájlt, és szeretné használni a központi telepítési mappa frissítése. Ebben a példában a háttér customerXXXX lecseréljük.  
1. Keresse meg a **az Azure portal** > **a Microsoft Intune** > **eszközkonfiguráció** > **PowerShell parancsfájlok** > **hozzáadása**
1. Adjon meg egy **neve** a parancsfájl, és adja meg a **parancsfájl helye** letöltött, hogy
1. Válassza ki **konfigurálása**
   1. Állítsa be **futtassa ezt a szkriptet a hitelesítő adatok használatával a bejelentkezett**, az **Igen**
   1. Kattintson az **OK** gombra
1. Kattintson a **Létrehozás** gombra.
1. Válassza ki **hozzárendelések** > **csoportok kiválasztása**
   1. Adja meg a biztonsági csoport **munkaállomások biztonságossá** korábban létrehozott, és kattintson a **mentése**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>Az előzetes verzió használatakor: Mobileszköz-kezelési biztonsági alaptervet a 2018. október

A Microsoft Intune biztonsági alapterv felügyeleti szolgáltatás biztosítása a rendszergazdák egy egyszerű módja egy közös alapvető biztonsági állapotáról kényszerítése vezetett be. Az alapkonfiguráció biztosít egy hasonló eszközökkel is egy zárolt bővített profil munkaállomás le.

A biztonságos munkaállomás megvalósítása, ez a alapvető nem használható, mert ütközik az biztonságos központi telepítését.

|   |   |   |
| :---: | :---: | :---: |
| Zárolási felett | Eszköz telepítése | Távoli asztali szolgáltatások |
| Alkalmazás-modul | Device Lock | Távfelügyelet |
| Alkalmazáskezelés | Event Log Service | A távoli eljáráshívás |
| Automatikus lejátszás | Felhasználói élményt | Keresés |
| BitLocker | Biztonsági rés kiaknázása | SmartScreen |
| Böngésző | Fájlkezelő | Rendszerkövetelmények|
| Kapcsolat | Internet Explorer | Wi-Fi |
| Hitelesítő adatok delegálása | A helyi házirendek-biztonsági beállítások | Windows Connection Manager |
| Credentials UI | MS biztonsági útmutató | Windows Defender|
| Adatvédelem | MSS örökölt | Windows Ink-munkaterület |
| A Device Guard | Energiagazdálkodási | Windows PowerShell |

Az előzetes funkcióval kapcsolatos további információk a cikkben található [Windows biztonsági Alapterv beállítások az Intune-ban](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Regisztrálása és az első eszköz ellenőrzése

1. Az eszköz regisztrálása, szüksége van a következő információkat:
   * **Sorozatszám** – az eszköz váz található
   * **Windows-termékazonosító** - csoportban található **rendszer** > **kapcsolatos** a Windows-beállítások menüjében.
   * Futó [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) biztosít az eszközök regisztrálásának kivonatoló CSV-fájl összes szükséges információt. 
      * Futtatás `Get-WindowsAutoPilotInfo – outputfile device1.csv` a kimenetben az adatokat az Intune-ba importálható a CSV-fájlként.

   > [!NOTE]
   > A parancsfájl lesz szükséges emelt szintű jogosultságokkal rendelkeznek, és a távoli Futtatás aláírva. A következő parancsot használhatja, hogy a szkript megfelelő működéséhez. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Jelentkezzen be a Windows 10-es verzió 1809 vagy magasabb eszközre a összegyűjteni ezeket az információkat gyűjthet, vagy a hardver forgalmazójával is megadja ezt az információt, amikor új eszközök sorrendjét.
1. A szükséges adatokat, és térjen vissza a **az Azure portal**. Navigáljon a **a Microsoft Intune** > **eszközregisztráció** > **Windows regisztrációs** >  **- eszközök Windows Autopilot-eszközök felügyeletére**, jelölje be **importálás**, és válassza ki a CSV-fájl létrehozásakor, vagy van megadva.
1. Adja hozzá a biztonsági csoporthoz a most regisztrált eszköz **munkaállomások biztonságossá** korábban létrehozott.
1. A Windows 10-eszközön való konfigurálásához, keresse meg a **Windows beállítások** > **frissítés és biztonság** > **helyreállítási**. Válassza a **Ismerkedés** alatt **alaphelyzetbe állítása a PC-s** , és kövesse az utasításokat, alaphelyzetbe állítása, és konfigurálja újra az eszközt a konfigurált profil és a megfelelőségi szabályzatok használatával.

Az eszköz konfigurálása után végezze el a felülvizsgálatot, és ellenőrizze a konfigurációban. Győződjön meg arról, az első eszköz megfelelően van-e konfigurálva a központi telepítés folytatása előtt.

## <a name="assignment-and-monitoring"></a>Hozzárendelése és figyelése

Eszközök és felhasználók hozzárendelése szükséges leképezése a [profilok kiválasztott](https://docs.microsoft.com/intune/device-profile-assign) , a biztonsági csoport és az összes olyan kap engedélyt a szolgáltatás új felhasználó lesz a biztonsági csoporthoz is hozzá kell adni.

A profilokat figyelés végezhető a figyelési [a Microsoft Intune-profilok](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>További lépések

[A Microsoft Intune](https://docs.microsoft.com/intune/index) dokumentációja

[Az Azure AD](https://docs.microsoft.com/azure/active-directory/index) dokumentációja