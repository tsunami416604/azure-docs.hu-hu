---
title: Azure által felügyelt munkaállomások – Azure Active Directory telepítése
description: Ismerje meg, hogyan helyezhet üzembe biztonságos, Azure által felügyelt munkaállomások illetéktelen behatolás vagy biztonsági sérülés kiszolgálóhiba miatt kockázatának csökkentése érdekében.
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
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550483"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Egy biztonságos, Azure által felügyelt munkaállomás üzembe helyezése

Most, hogy Ön [biztonságos munkaállomások ismertetése](concept-azure-managed-workstation.md), ideje a központi telepítési folyamat megkezdéséhez. Ez az útmutató a definiált profilokat hozhat létre, amely a fokozott biztonság érdekében a kezdetektől munkaállomás használja.

![A biztonságos munkaállomás üzembe helyezés](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

A megoldás telepítése előtt ki kell választania egy profilt. A központi telepítés egyszerre több profil használatával, és hozzárendelheti azokat a címkék vagy a csoportok.
> [!NOTE]
> A profilok bármelyikét igényei szerint a követelmények vonatkoznak. Áthelyezheti egy másik profilhoz, ha hozzárendeli az Intune-ban.

| Profil | Alacsony | Továbbfejlesztett | Magas | Specializált | Védett | Izolált |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Az Azure AD-felhasználó | Igen | Igen | Igen | Igen | Igen | Igen |
| Intune-managed | Igen | Igen | Igen | Igen | Igen | Igen |
| Eszköz – az Azure AD-ban regisztrálva | Igen |  |  |  |  | |   |
| Eszköz – Azure AD-hez |   | Igen | Igen | Igen | Igen | Igen |
| Intune-ban a alkalmazni alapvető biztonsági |   | Igen <br> (Speciális) | Igen <br> (HighSecurity) | Igen <br> (NCSC) | Igen <br> (Védett) |  NA |
| Hardver megfelel-e a Windows 10-es biztonságos szabványok |   | Igen | Igen | Igen | Igen | Igen |
| A Microsoft Defender ATP engedélyezése |   | Igen  | Igen | Igen | Igen | Igen |
| A rendszergazdai jogosultságok eltávolítása |   |   | Igen  | Igen | Igen | Igen |
| Üzembe helyezés, a Microsoft az Autopilot használata |   |   | Igen  | Igen | Igen | Igen |
| Csak az Intune által telepített alkalmazások |   |   |   | Igen | Igen |Igen |
| Jóváhagyott listához korlátozott URL-címek |   |   |   | Igen | Igen |Igen |
| Internet (bejövő vagy kimenő) letiltva |   |   |   |  |  |Igen |

## <a name="license-requirements"></a>Licenckövetelmények

Az útmutatóban bemutatott fogalmak fel, hogy a Microsoft 365 nagyvállalati E5 csomag vagy egy azzal egyenértékű Termékváltozat. Az útmutató a javaslatok némelyike az alacsonyabb termékváltozatok kell végrehajtani. További információkért lásd: [Microsoft 365 Enterprise licencelés](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Automatizálhatja a licenc-kiépítés, érdemes lehet [Csoportalapú licencelés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) a felhasználók számára.

## <a name="azure-active-directory-configuration"></a>Az Azure Active Directory konfigurálása

Az Azure Active Directory (Azure AD) kezeli a felhasználók, csoportok és a rendszergazdai munkaállomások eszközök. Identitás-szolgáltatások és a funkciókat engedélyezni kell az [rendszergazdai fiók](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

A biztonságos munkaállomás rendszergazdai fiók létrehozásakor elérhetővé teszi a fiókhoz a jelenlegi munkaállomásra. Ellenőrizze, hogy ismert biztonságos eszköz használata a kezdeti konfiguráció és az összes globální konfiguraci. Az első élmény jellegű támadásoknak való kitettség csökkentése érdekében fontolja meg a következő a [útmutatást, hogy a kártevőszoftver-fertőzések](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Azt is be kell állítania a multi-factor authentication, legalább a rendszergazdák számára. Lásd: [felhőalapú MFA üzembe helyezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) útmutatásért végrehajtását.

### <a name="azure-ad-users-and-groups"></a>Az Azure AD-felhasználók és csoportok

1. Az Azure Portalon keresse meg a **Azure Active Directory** > **felhasználók** > **új felhasználó**.
1. Az eszköz-rendszergazdai létrehozásának lépéseit a [hozzon létre felhasználói oktatóanyag](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Enter:
   * **Név** – biztonságos felügyeleti munkaállomás
   * **Felhasználónév** - `secure-ws-admin@identityitpro.com`
   * **Címtárbeli szerepkör** - **korlátozott rendszergazda** , és válassza ki a **Intune-rendszergazda** szerepkör.
1. Kattintson a **Létrehozás** gombra.

Ezután létrehozhat két csoportot: munkaállomás felhasználók és eszközök munkaállomás.

Az Azure Portalon keresse meg a **Azure Active Directory** > **csoportok** > **új csoport**.

1. A munkaállomás-felhasználók csoport konfigurálása érdemes [Csoportalapú licencelés](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) kiépítés licencek felhasználóknak történő automatizálásához.
1. A munkaállomás-felhasználók csoporthoz adja meg:
   * **Csoport típusa** -biztonság
   * **Csoport neve** -munkaállomások felhasználóinak biztonságossá tétele
   * **Tagság típusa** – hozzárendelt
1. A biztonságos munkaállomás rendszergazda felhasználó hozzáadása: `secure-ws-admin@identityitpro.com`
1. Biztonságos munkaállomások felügyel, más felhasználók is hozzáadhat.
1. Kattintson a **Létrehozás** gombra.

1. A munkaállomás eszközök csoporthoz adja meg:
   * **Csoport típusa** -biztonság
   * **Csoport neve** -munkaállomások biztonságossá tétele
   * **Tagság típusa** – hozzárendelt
1. Kattintson a **Létrehozás** gombra.

### <a name="azure-ad-device-configuration"></a>Az Azure AD-eszközök konfigurálása

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Adja meg, akik csatlakozhat az Azure AD-eszközök

Az eszközök beállítása az Active Directory lehetővé teszi a felügyeleti biztonsági csoport az eszközök csatlakoztatása a tartományhoz való konfigurálásához. Ez a beállítás az Azure Portalról konfigurálása:

1. Lépjen a **Azure Active Directory** > **eszközök** > **eszközbeállítások**.
1. Válasszon **kijelölt** alatt **felhasználók eszközöket csatlakoztathatnak az Azure AD**, majd válassza a "Biztonságos munkaállomás-felhasználók" csoportot.

#### <a name="removal-of-local-admin-rights"></a>A helyi rendszergazdai jogosultságok eltávolítása

Ez a metódus szükséges, hogy a virtuális IP-CÍMEK, a DevOps és a biztonságos szintű munkaállomások felhasználóinak nem rendelkezik rendszergazdai jogokkal a gépen. Ez a beállítás az Azure Portalról konfigurálása:

1. Lépjen a **Azure Active Directory** > **eszközök** > **eszközbeállítások**.
1. Válassza ki **nincs** alatt **további helyi rendszergazdák az Azure AD-hez csatlakoztatott eszközök**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése

További megerősítésére a folyamat eszközök csatlakoztatása az Azure ad-hez:

1. Lépjen a **Azure Active Directory** > **eszközök** > **eszközbeállítások**.
1. Válassza ki **Igen** alatt **a többtényezős hitelesítés megkövetelése eszközök csatlakoztatásához**.
1. Kattintson a **Mentés** gombra.

#### <a name="configure-mdm"></a>Mobileszköz-kezelési konfigurálása

Az Azure Portalról:

1. Keresse meg a **az Azure Active Directory** > **mobilitás (MDM és MAM)**  > **Microsoft Intune-ban**.
1. Módosítsa a **MDM felhasználói hatókör** beállítást **összes**.
1. Kattintson a **Mentés** gombra.

Ezeket a lépéseket minden olyan eszközt az Intune-nal kezelését teszi lehetővé. További információkért lásd: [Intune-ban a rövid útmutató: A Windows 10-es eszközök automatikus regisztrációjának beállítása](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Konfigurációs és megfelelőségi szabályzatok Intune-ban létrehozhat egy későbbi lépésben.

#### <a name="azure-ad-conditional-access"></a>Az Azure ad-beli feltételes hozzáférés

Az Azure ad-beli feltételes hozzáférés segítségével a megfelelő eszközökre korlátozza a kiemelt jogosultságú rendszergazdai feladatokat. Az előre meghatározott tagjai a **biztonságos munkaállomások felhasználóinak** csoport van szükség a multi-factor authentication végrehajtása a felhőbeli alkalmazásokhoz való bejelentkezéskor. Ajánlott eljárás, hogy a vészelérési fiókok zárni a szabályzatból. További információkért lásd: [vészelérési fiókok kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Feltételes hozzáférés konfigurálása az Azure Portalról:

1. Lépjen a **Azure Active Directory** > **feltételes hozzáférési** > **új szabályzat**.
1. Enter:
   * **Név** – biztonságos eszköz házirend szükséges
   * Hozzárendelések
     * **Felhasználók és csoportok**
       * Például - **felhasználók és csoportok** – válassza ki a **biztonságos munkaállomások felhasználóinak** korábban létrehozott csoportot.
       * Kizárása - **felhasználók és csoportok** – válassza ki a szervezet a vészelérési fiókok.
     * **Felhőalapú alkalmazások** – például **az összes felhőalapú alkalmazások**.
    * Hozzáférés-vezérlés
      * **Engedélyezés** – ki **hozzáférést** választógombot.
        * **Többtényezős hitelesítés megkövetelése**.
        * **Megfelelőként megjelölt eszköz megkövetelése**.
        * Több vezérlő – **az összes kijelölt vezérlő megkövetelése**.
    * Házirend - engedélyezése **a**.

Lehetősége van az országok, akkor nem elérhetik a vállalati erőforrások letiltó szabályzatokat hozhat létre. IP helyalapú feltételes hozzáférési szabályzatokkal kapcsolatos további információkért lásd: [az Azure Active Directory feltételes hozzáférés a helyfeltétel](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Intune-konfiguráció

### <a name="configure-enrollment-status"></a>Regisztrációs állapot konfigurálása

Fontos győződjön meg arról, hogy a biztonságos munkaállomás tiszta megbízható eszköz. Új eszközök megvásárlásakor akkor is mereven, hogy azok gyári beállítása [Windows 10 Pro S módban](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), amely korlátozza a biztonsági rések ellátásilánc-kezelési során. Miután egy eszköz megkapta a szállítótól, használhatja az Autopilot módosítani, S módról. Az alábbi útmutatás részletesen az átalakítási folyamat alkalmazásával.

Győződjön meg arról, hogy az eszközök teljes konfigurálása használata előtt, hogy az Intune lehetővé teszi **letiltja az eszköz használatát, alkalmazások és a profilok telepítéséig**.

Az a **az Azure portal**:
1. Lépjen a **a Microsoft Intune** > **eszközregisztráció** > **Windows regisztrációs** > **beléptetés állapota Oldal** > **alapértelmezett** > **beállítások**.
1. Állítsa be **alkalmazás profil telepítés állapotának megjelenítése** való **Igen**.
1. Állítsa be **letiltja az eszköz használatát, alkalmazások és a profilok telepítéséig** való **Igen**.

### <a name="create-an-autopilot-deployment-profile"></a>AutoPilot üzembehelyezési profil létrehozása

Miután létrehozott egy eszközcsoportot, létre kell hoznia egy deployment-profil az Autopilot-eszközök konfigurálásához.

Az Intune-ban az Azure Portalon:

1. Válassza ki **eszközregisztráció** > **Windows regisztrációs** > **Deployment-profilok** > **profil létrehozása** .
1. Enter:
   * Név – **munkaállomás üzembe helyezési profil biztonságos**.
   * Leírás – **biztonságos munkaállomások telepítési**.
   * Állítsa be **minden megcélzott eszközök átalakítása Autopilot** való **Igen**. Ez a beállítás gondoskodik arról, hogy a lista minden eszköz regisztrálva az Autopilot deployment szolgáltatással. A regisztráció feldolgozása 48 órát is igénybe vehet.
1. Kattintson a **Tovább** gombra.
   * A **üzembe helyezési mód**, válassza a **helyi üzembe helyezése (előzetes verzió)** . Ehhez a profilhoz eszközök társítva a felhasználó regisztrálja az eszközt. Az eszköz regisztrálásához felhasználói hitelesítő adatokra van szükség.
   * A **csatlakozzon az Azure AD szolgáltatásba** mezőben meg kell jelennie **Azure AD-hez** és szürkén jelennek meg.
   * Válassza ki a felhasználói fiók típusa Langugage (régió), **standard**. 
1. Kattintson a **Tovább** gombra.
   * Hatókörcímke akkor válassza, ha Ön rendelkezik egy előre konfigurált.
1. Kattintson a **Tovább** gombra.
1. Válasszon **hozzárendelések** > **hozzárendelése** > **kijelölt csoportok**. A **válassza ki a befoglalandó csoportokat**, válassza a **biztonságos munkaállomások felhasználóinak**.
1. Kattintson a **Tovább** gombra.
1. Válassza a **Létrehozás** gombot a profil létrehozásához. Az AutoPilot üzembehelyezési profil most már hozzárendelhető az eszközökhöz.

### <a name="configure-windows-update"></a>Windows Update konfigurálása

Naprakészen tartja a Windows 10-es az egyik a legfontosabb műveletet is végezhet. Windows biztonságos állapotban tartani, üzembe helyezése egy frissítési kör kezelheti a számítógépére, hogy a frissítések alkalmazása munkaállomásokra. 

Ez az útmutató azt javasolja, hogy egy új frissítési kör létrehozása és módosítása az alábbi alapértelmezett beállításokkal:

Az Azure Portalon:

1. Lépjen a **a Microsoft Intune** > **szoftverfrissítések** > **Windows 10-es frissítési körök**.
1. Enter:
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

1. Kattintson a **Létrehozás** gombra.
1. Az a **hozzárendelések** lapon maradva adja hozzá a **munkaállomások biztonságossá** csoport.

További információ a Windows Update-szabályzatok: [házirend CSP - frissítés](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>A Windows Defender ATP Intune-integráció

A Windows Defender ATP-ben és a Microsoft Intune együttesen segít megelőzni a biztonsági incidenseket. Azok a problémák hatását is korlátozhatja. ATP képességeket biztosít a valós fenyegetések észlelése, valamint kiterjedt vizsgálati és naplózási a végponti eszközök engedélyezése.

A Windows Defender ATP-ben és az Intune-integráció konfigurálásához nyissa meg az Azure Portalon.

1. Keresse meg a **a Microsoft Intune** > **eszközmegfelelőség** > **Windows Defender ATP**.
1. A 1. lépésében **konfigurálása a Windows Defender ATP**válassza **csatlakoztatása a Windows Defender ATP és a Microsoft Intune a Windows Defender biztonsági központ**.
1. A Windows Defender biztonsági központban:
   1. Válassza ki **beállítások** > **speciális szolgáltatások**.
   1. A **a Microsoft Intune kapcsolatot**, válassza a **a**.
   1. Válassza ki **beállítások mentése**.
1. A kapcsolat létrejötte után térjen vissza az Intune, és válassza ki **frissítése** tetején.
1. Állítsa be **csatlakozás Windows 10.0.15063 eszközök verzió vagy újabb a Windows Defender ATP** való **a**.
1. Kattintson a **Mentés** gombra.

További információkért lásd: [Windows Defender komplex veszélyforrások elleni védelem](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>A korlátozott munkaállomás profil véglegesítése

A megoldás a megerősítés sikeres végrehajtásához töltse le, és hajtsa végre a megfelelő parancsfájlt. A letöltési hivatkozások keresése a kívánt **szint profil**:

| Profil | Letöltési hely | Fájlnév |
| --- | --- | --- |
| Alacsony biztonságú | – |  – |
| Fokozott biztonság | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| A fokozott biztonságot  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializált | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC – Windows 10-es (1803) SecurityBaseline.ps1 |
| Speciális megfelelőségi * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Védett | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Speciális megfelelőségi egy parancsfájlt, amely kényszeríti a Windows 10-es NCSC SecurityBaseline megtalálható a speciális konfigurációs.

Miután a parancsfájl sikeresen befejeződik, profilok és szabályzatok az Intune-ban is hajtsa végre a frissítéseket. A parancsfájlok bővített és biztonságos profilok házirendek és profilok létrehozása az Ön számára, de a hozzá kell rendelnie a szabályzatot a **munkaállomások biztonságossá** csoport.

* Itt látható, hogy hol találhatók az Intune eszközkonfigurációs profilok a parancsfájlok által létrehozott: **Az Azure portal** > **a Microsoft Intune** > **eszközkonfiguráció** > **profilok**.
* Itt látható, hogy hol találhatók az Intune-eszközök a parancsfájlok által létrehozott eszközmegfelelőségi szabályzatok: **Az Azure portal** > **a Microsoft Intune** > **Eszközmegfelelőség** > **házirendek**.

Tekintse át a parancsfájlok által végrehajtott módosítások, exportálhatja a profilokat. Ezzel a módszerrel megadhatja, hogy további megerősítés, van szükség, a SECCON dokumentációjában leírt módon.

Az Intune-ban exportálási parancsfájl futtatása `DeviceConfiguration_Export.ps1` származó a [eszközkonfiguráció GiuHub tárház](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) összes aktuális Intune-profil exportálása.

## <a name="additional-configurations-and-hardening-to-consider"></a>További konfigurációs beállítások és a megfontolandó biztonságának megerősítése

Itt útmutatást követve üzembe helyezte a biztonságos munkaállomás. Azonban is érdemes további vezérlők. Példa:

* más böngészőkkel történő hozzáférés korlátozása
* kimenő HTTP engedélyezése
* Jelölje be a webhelyek
* egyéni PowerShell-parancsfájlok futtatásakor használt engedélyek beállítása

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>A tűzfal-konfigurációs szolgáltató (CSP) a szabályok beállítása

A bejövő és kimenő szabályok kezelési további módosításokat végezheti el az engedélyezett és letiltott végpontok igény szerint. A biztonságos munkaállomás felvértezni továbbra is, mint a korlátozás az összes bejövő és kimenő forgalom megtagadásához, is lazábbá tehető. Gyakori és a megbízható webhelyek közé tartozik az engedélyezett kimenő helyeket adhat hozzá. További információkért lásd: [tűzfal-konfigurációs szolgáltatás](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Korlátozott alapértelmezett ajánlások a következők:

* Az összes bejövő megtagadása
* Összes kimenő megtagadása

Kezeli a Spamhaus projekt [tartomány letiltása lista (dupla)](https://www.spamhaus.org/dbl/): a leghasznosabb kiindulási pontként használandó blokkolása.

### <a name="manage-local-applications"></a>Helyi alkalmazások kezelése

A biztonságos munkaállomás valóban megerősített állapotba helyezi át, ha helyi alkalmazás törlődik, beleértve a csomagot a hatékonyságnövelő alkalmazásokat. Itt, az alapértelmezett böngésző Chrome hozzáadása és módosítása a böngésző és a modulok lehetővé teszi egy felhasználó korlátozza az Intune-nal.

#### <a name="deploy-applications-using-intune"></a>Alkalmazások üzembe helyezése az Intune használatával

Bizonyos esetekben olyan alkalmazások, mint a Google Chrome böngésző szükséges a biztonságos munkaállomáson. Az alábbi példa tartalmazza azokat az eszközöket a biztonsági csoport Chrome telepítésére vonatkozó utasításokat tartalmaz **munkaállomások biztonságossá**.

1. Töltse le az offline [Windows 64‑bit Chrome köteg](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Bontsa ki a fájlokat, és jegyezze fel a helyét a `GoogleChromeStandaloneEnterprise64.msi` fájlt.
1. Az a **az Azure portal** keresse meg a **a Microsoft Intune** > **ügyfélalkalmazás** > **alkalmazások**  >  **Hozzáadása**.
1. A **alkalmazástípus**, válassza a **üzleti**.
1. A **alkalmazáscsomag-fájl**, jelölje be a `GoogleChromeStandaloneEnterprise64.msi` a kinyert helyről fájlt, és válassza ki **OK**.
1. A **alkalmazásadatok**, adjon meg egy leírást és a közzétevő. Kattintson az **OK** gombra.
1. Válassza a **Hozzáadás** lehetőséget.
1. Az a **hozzárendelések** lapon jelölje be **regisztrált eszközökhöz elérhető** alatt **hozzárendelés-típus**.
1. A **belefoglalt csoportok**, adja hozzá a **munkaállomások biztonságossá** csoport.
1. Válassza ki **OK**, majd válassza ki **mentése**.

További útmutatást a Chrome-beállítások konfigurálása, lásd: [Chrome böngészőben kezelése a Microsoft Intune-nal](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Az egyéni alkalmazások a vállalati portál konfigurálása

Egy biztonságos módban alkalmazástelepítés korlátozódik, az Intune céges portálon. Azonban Microsoft Store-hozzáférés a portál telepítése szükséges. A biztonságos megoldás akkor is használhatja a vállalati portálon keresztül egy offline üzemmód összes eszközre.

Egy Intune által felügyelt másolatot készít a [céges portál](https://docs.microsoft.com/Intune/store-apps-company-portal-app) igény szerinti hozzáférést biztosít a további eszközök küldhet a biztonságos munkaállomások felhasználóinak.

Szüksége lehet telepíteni a Windows 32 bites alkalmazások és más alkalmazások, amelynek a telepítési igényelnek speciális előkészített. Ezekben az esetekben a [Microsoft win32-tartalom előkészítő eszköze](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) megadhat egy azonnal használható `.intunewin` formátumfájl telepítéséhez.

### <a name="use-powershell-to-create-custom-settings"></a>Hozzon létre egyéni beállításokat a PowerShell használatával

PowerShell gazdagép kezelési lehetőségek bővítése céljából is használható. Ez a példaszkript állít be alapértelmezett háttér a gazdagépen. Egy olyan funkció, amely szintén az Azure portal és a profilok keresztül érhető el. A példaszkript szolgálja ki, csak a PowerShell-funkcióit mutatja be.

Szüksége lehet a biztonságos munkaállomásokon beállítása néhány egyéni vezérlők és a beállításokat. Ebben a példában a munkaállomás hátterének módosítása a Powershell lehetőség használatával könnyedén azonosíthatja az eszköz használatra kész, a biztonságos munkaállomásként.

A [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) parancsfájl a Microsoft Scripting Center lehetővé teszi, hogy a Windows betöltéséhez, ez [ingyenes, általános háttérkép](https://i.imgur.com/OAJ28zO.png) az elindításához.

1. Töltse le a szkriptet a helyi eszközre.
1. Frissítse a customerXXXX és a háttérkép a letöltési helyre. Ebben a példában a háttér customerXXXX lecseréljük.  
1. Keresse meg a **az Azure portal** > **a Microsoft Intune** > **eszközkonfiguráció** > **PowerShell parancsfájlok** > **Hozzáadás**.
1. Adjon meg egy **neve** a parancsfájl, és adja meg a **parancsfájl helye**.
1. Válassza ki **konfigurálása**.
   1. Állítsa be **futtassa ezt a szkriptet a hitelesítő adatok használatával a bejelentkezett** való **Igen**.
   1. Kattintson az **OK** gombra.
1. Kattintson a **Létrehozás** gombra.
1. Válassza ki **hozzárendelések** > **válassza ki a csoportokat**.
   1. Adja meg a biztonsági csoport **munkaállomások biztonságossá**.
   1. Kattintson a **Mentés** gombra.

## <a name="enroll-and-validate-your-first-device"></a>Regisztrálása és az első eszköz ellenőrzése

1. Az eszköz regisztrálása, szüksége van a következő információkat:
   * **Sorozatszám** – az eszköz váz található.
   * **Windows-termékazonosító** - csoportban található **rendszer** > **kapcsolatos** a Windows-beállítások menüjében.
   * Futtathat [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) tartalmazó CSV-kivonat fájl összes szükséges információt az eszközregisztráció beolvasásához.
   
     Futtatás `Get-WindowsAutoPilotInfo – outputfile device1.csv` a kimenetben az adatokat egy CSV-fájl, amelyet importálhat az Intune-ban.

     > [!NOTE]
     > A szkriptnek szüksége van a emelt szintű jogosultságokkal rendelkeznek. Fut a távoli aláírva. A `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` parancs engedélyezi a szkript megfelelő működéséhez.

   * Ezt az információt gyűjthet jelentkezzen be a Windows 10-es verzió 1809 vagy magasabb eszközre. A hardver forgalmazójával is lehetővé teszi ezt az információt.
1. Az a **az Azure portal**, lépjen a **a Microsoft Intune** > **eszközregisztráció** > **Windows regisztrációs**  >  **Eszközök – kezelése a Windows Autopilot-eszközök**.
1. Válassza ki **importálás** , és válassza ki a CSV-fájlban.
1. Az eszköz hozzáadása a **munkaállomások biztonságossá** biztonsági csoportot.
1. Lépjen a konfigurálni kívánt Windows 10-es eszközön **Windows beállítások** > **frissítés és biztonság** > **helyreállítási**.
   1. Válassza a **Ismerkedés** alatt **alaphelyzetbe állítása a PC-s**.
   1. Kövesse az utasításokat, alaphelyzetbe állítása, és konfigurálja újra az eszköz konfigurált profil és a megfelelőségi szabályzatok.

Miután konfigurálta az eszközt, végezze el a felülvizsgálatot, és ellenőrizze a konfigurációban. Győződjön meg arról, hogy az első eszköz megfelelően van konfigurálva a központi telepítés folytatása előtt.

## <a name="assign-and-monitor"></a>Hozzárendelése és figyelése

Eszközök és felhasználók hozzárendelése, meg kell feleltetni a [profilok kiválasztott](https://docs.microsoft.com/intune/device-profile-assign) a biztonsági csoportnak. Minden új felhasználók, akik engedélyeket a szolgáltatás a biztonsági csoporthoz is hozzá kell adni.

Nyomon követheti a profilok [Intune profil figyelési](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [a Microsoft Intune](https://docs.microsoft.com/intune/index).
* Megismerheti [Azure ad-ben](https://docs.microsoft.com/azure/active-directory/index).
