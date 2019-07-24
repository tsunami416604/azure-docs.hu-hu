---
title: Azure által felügyelt munkaállomások üzembe helyezése – Azure Active Directory
description: Ismerje meg, hogyan helyezhet üzembe biztonságos, Azure által felügyelt munkaállomásokat a helytelen konfiguráció vagy a biztonság megsértése miatti szerződésszegés kockázatának csökkentése érdekében.
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
ms.openlocfilehash: 90687d0229d3ad74c287bb4aff4885dc26932e40
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227270"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Biztonságos, Azure által felügyelt munkaállomás üzembe helyezése

Most, hogy megértette a [biztonságos](concept-azure-managed-workstation.md)munkaállomásokat, itt az ideje, hogy megkezdje az üzembe helyezés folyamatát. Ezzel az útmutatóval meghatározott profilok használatával hozhat létre olyan munkaállomást, amely biztonságosabb az indítástól.

![Biztonságos munkaállomás üzembe helyezése](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

A megoldás üzembe helyezése előtt ki kell választania egy profilt. Egyszerre több profilt is használhat egy központi telepítésben, és hozzárendelheti őket címkékkel vagy csoportokkal.
> [!NOTE]
> Igény szerint alkalmazza a profilok bármelyikét. Az Intune-ban való hozzárendeléssel másik profilba is áthelyezheti.

| Profil | Alacsony | Továbbfejlesztett | Magas | Specializált | Biztosított | Izolált |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Felhasználó az Azure AD-ben | Igen | Igen | Igen | Igen | Igen | Igen |
| Intune-managed | Igen | Igen | Igen | Igen | Igen | Igen |
| Eszköz – Azure AD regisztrálva | Igen |  |  |  |  | |   |
| Eszköz – Azure AD-hez csatlakoztatott |   | Igen | Igen | Igen | Igen | Igen |
| Az Intune biztonsági alapterve alkalmazva |   | Igen <br> Bővített | Igen <br> (HighSecurity) | Igen <br> (NCSC) | Igen <br> Biztosított |  NA |
| A hardver megfelel a biztonságos Windows 10-es szabványoknak |   | Igen | Igen | Igen | Igen | Igen |
| Microsoft Defender ATP engedélyezve |   | Igen  | Igen | Igen | Igen | Igen |
| Rendszergazdai jogosultságok eltávolítása |   |   | Igen  | Igen | Igen | Igen |
| Üzembe helyezés a Microsoft Autopilot használatával |   |   | Igen  | Igen | Igen | Igen |
| Csak az Intune által telepített alkalmazások |   |   |   | Igen | Igen |Igen |
| Jóváhagyott listára korlátozott URL-címek |   |   |   | Igen | Igen |Igen |
| Internet Letiltva (bejövő/kimenő) |   |   |   |  |  |Igen |

## <a name="license-requirements"></a>Licenckövetelmények

Az útmutatóban szereplő fogalmak feltételezik, hogy Microsoft 365 Nagyvállalati verzió E5 vagy egy egyenértékű SKU van. A jelen útmutatóban szereplő javaslatok közül néhányat alacsonyabb SKU-kal lehet megvalósítani. További információ: [Microsoft 365 nagyvállalati verzió licencelés](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

A licencek kiépítés automatizálásához gondolja át a felhasználók [csoport alapú licencelését](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) .

## <a name="azure-active-directory-configuration"></a>Azure Active Directory konfiguráció

Azure Active Directory (Azure AD) a rendszergazdai munkaállomásokhoz tartozó felhasználókat, csoportokat és eszközöket kezeli. [Rendszergazdai fiókkal](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)kell engedélyeznie az Identity Servicest és a szolgáltatásokat.

A biztonságos munkaállomás-rendszergazdai fiók létrehozásakor a fiók elérhetővé válnak az aktuális munkaállomáson. Győződjön meg arról, hogy ismert biztonságos eszközt használ ehhez a kezdeti konfigurációhoz és az összes globális konfigurációhoz. Ha csökkenteni szeretné a támadási expozíciót az első alkalommal, vegye figyelembe az útmutatást a kártevők elleni [fertőzések megelőzése](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)érdekében.

A többtényezős hitelesítést is meg kell követelnie, legalább a rendszergazdái számára. Lásd: [FELHŐALAPÚ MFA üzembe helyezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) a megvalósítási útmutatóban.

### <a name="azure-ad-users-and-groups"></a>Azure AD-felhasználók és-csoportok

1. A Azure Portal keresse meg **Azure Active Directory** > **felhasználók** > **új felhasználó**elemet.
1. Hozza létre az eszköz rendszergazdáját a [felhasználói oktatóanyag létrehozása](https://docs.microsoft.com/Intune/quickstart-create-user)című témakör lépéseit követve.
1. Be
   * **Név** – biztonságos munkaállomás rendszergazdája
   * **Felhasználónév** - `secure-ws-admin@identityitpro.com`
   * **Címtár-szerepkör** - –**korlátozott rendszergazda** , és válassza ki az **Intune rendszergazdai** szerepkört.
1. Kattintson a **Létrehozás** gombra.

Ezután két csoportot hoz létre: munkaállomás-felhasználók és munkaállomás-eszközök.

A Azure Portal keresse meg **Azure Active Directory** > **csoportok** > **új csoportot**.

1. A munkaállomás-felhasználók csoport esetében előfordulhat, hogy a felhasználók számára a licencek kiosztásának automatizálásához [csoport alapú licencelést](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) szeretne konfigurálni.
1. A munkaállomás-felhasználók csoportnál adja meg a következőt:
   * **Csoport típusa** – biztonság
   * **Csoportnév** – biztonságos munkaállomás-felhasználók
   * **Tagság típusa** – hozzárendelve
1. Adja hozzá a biztonságos munkaállomás rendszergazda felhasználóját:`secure-ws-admin@identityitpro.com`
1. Hozzáadhat más felhasználókat is, akik a biztonságos munkaállomásokat kezelik.
1. Kattintson a **Létrehozás** gombra.

1. A munkaállomás-eszközök csoportban adja meg a következőt:
   * **Csoport típusa** – biztonság
   * **Csoport neve** – biztonságos munkaállomások
   * **Tagság típusa** – hozzárendelve
1. Kattintson a **Létrehozás** gombra.

### <a name="azure-ad-device-configuration"></a>Azure AD-eszköz konfigurációja

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Az eszközök Azure AD-hez való csatlakoztatásának megadására szolgáló felhasználó

Konfigurálja úgy a Active Directory eszközök beállítását, hogy a rendszergazdai biztonsági csoport az eszközök tartományhoz való csatlakoztatását engedélyezze. A beállítás konfigurálása a Azure Portal:

1. Lépjen **Azure Active Directory** > eszközökeszközbeállítások > **menüpontra**.
1. Ha a felhasználók lehetőségre van **kiválasztva** , csatlakozhatnak az eszközökhöz az **Azure ad**-ben, majd a "biztonságos munkaállomás-felhasználók" csoportot.

#### <a name="removal-of-local-admin-rights"></a>Helyi rendszergazdai jogosultságok eltávolítása

Ehhez a módszerhez az szükséges, hogy a VIP-, DevOps-és biztonságos munkaállomások felhasználói ne rendelkezzenek rendszergazdai jogokkal a gépen. A beállítás konfigurálása a Azure Portal:

1. Lépjen **Azure Active Directory** > eszközökeszközbeállítások > **menüpontra**.
1. Válassza a **nincs** lehetőséget **a további helyi rendszergazdák területen az Azure ad-hez csatlakoztatott eszközökön**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Többtényezős hitelesítés megkövetelése az eszközök csatlakoztatásához

Az eszközök Azure AD-hez való csatlakoztatásának további megerősítése:

1. Lépjen **Azure Active Directory** > eszközökeszközbeállítások > **menüpontra**.
1. Válassza az **Igen** lehetőséget a többtényezős **hitelesítés megkövetelése az eszközökhöz való csatlakozáshoz**.
1. Kattintson a **Mentés** gombra.

#### <a name="configure-mdm"></a>MDM konfigurálása

A Azure Portal:

1. Keresse meg **Azure Active Directory** > **mobilitás (Mdm és MAM)**  > **Microsoft Intune**.
1. Módosítsa a **Mdm felhasználói hatókörét** az **összes**értékre.
1. Kattintson a **Mentés** gombra.

Ezek a lépések lehetővé teszik bármely eszköz Intune-nal való kezelését. További információ: az Intune [gyors üzembe helyezési útmutatója: Automatikus regisztráció beállítása Windows 10-es eszközökhöz](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Az Intune konfigurációs és megfelelőségi szabályzatait egy későbbi lépésben hozza létre.

#### <a name="azure-ad-conditional-access"></a>Azure AD feltételes hozzáférés

Az Azure AD feltételes hozzáférés segítségével a megfelelő eszközökre korlátozhatja a Kiemelt felügyeleti feladatokat. A **biztonságos munkaállomás-felhasználók** csoport előre definiált tagjainak a többtényezős hitelesítés végrehajtásához kell végrehajtaniuk a felhőalapú alkalmazásokba való bejelentkezéskor. Az ajánlott eljárás a vészhelyzeti hozzáférési fiókok kizárása a szabályzatból. További információ: [a vészhelyzeti hozzáférési fiókok kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Feltételes hozzáférés konfigurálása a Azure Portalról:

1. Lépjen **Azure Active Directory** > a**feltételes hozzáférés** > **új szabályzata**elemre.
1. Be
   * **Név** – biztonságos eszköz szükséges házirend
   * Hozzárendelések
     * **Felhasználók és csoportok**
       * Include- **Users és groups** – válassza ki a korábban létrehozott **biztonságos munkaállomás-felhasználók** csoportot.
       * Kizárás – **felhasználók és csoportok** – válassza ki a szervezet vészhelyzeti hozzáférési fiókjait.
     * **Cloud apps** – **minden felhőalapú alkalmazás**belefoglalása.
    * Hozzáférés-vezérlés
      * **Engedélyezés** – válassza a **hozzáférési választógomb megadása** gombot.
        * **Többtényezős hitelesítés**megkövetelése.
        * Az **eszköz megfelelőként való**megjelölésének megkövetelése.
        * Több vezérlő esetén – **az összes kijelölt vezérlő**megkövetelése.
    * Engedélyezze **a**szabályzatot.

Lehetősége van olyan házirendek létrehozására, amelyek letiltják azokat az országokat, amelyeken a felhasználók nem férnek hozzá a vállalati erőforrásokhoz. Az IP-hely alapú feltételes hozzáférési házirendekkel kapcsolatos további információkért tekintse [meg a feltételes hozzáférés Azure Active Directory hely](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)feltételét.

## <a name="intune-configuration"></a>Intune-konfiguráció

### <a name="configure-enrollment-status"></a>Beléptetési állapot konfigurálása

Fontos, hogy a biztonságos munkaállomás megbízható tiszta eszköz legyen. Új eszközök vásárlása esetén ragaszkodhat ahhoz, hogy a gyári beállítások a [Windows 10 Pro-ra, S üzemmódban](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)legyenek, ami korlátozza a biztonsági rések kockázatát az ellátási lánc kezelése során. Miután megkapta az eszközt a szállítótól, az Autopilot használatával módosíthatja azt S módból. Az alábbi útmutató részletesen ismerteti az átalakítási folyamat alkalmazását.

Annak biztosítása érdekében, hogy az eszközök a használat előtt teljesen konfigurálva legyenek, az Intune lehetővé teszi az eszközök **használatának blokkolását, amíg az összes alkalmazás és profil nincs telepítve**.

A **Azure Portal**:
1. Ugrás a **Microsoft Intune** > **eszköz** > beléptetése a**Windows-regisztráció** > beléptetési**állapotára lap** > **alapértelmezett** > **beállításai**.
1. Állítsa az **alkalmazás-profil telepítési folyamatát** **Igen**értékre.
1. Állítsa be az **eszköz használatát, amíg az összes alkalmazás és profil nem települ** az **Igen**értékre.

### <a name="create-an-autopilot-deployment-profile"></a>AutoPilot üzembehelyezési profil létrehozása

Az eszközcsoport létrehozása után létre kell hoznia egy központi telepítési profilt az Autopilot-eszközök konfigurálásához.

Az Intune-ban a Azure Portalban:

1. Válassza **az eszközök** > beléptetése**Windows-regisztráció** > **üzembe helyezési profilok** > **profil létrehozása**lehetőséget.
1. Be
   * Név – **biztonságos munkaállomás telepítési profilja**.
   * Leírás – **biztonságos munkaállomások üzembe helyezése**.
   * Állítsa az **összes megcélzó eszköz konvertálása az Autopilot** -re beállítást **Igen**értékre. Ez a beállítás biztosítja, hogy a listában szereplő összes eszköz regisztrálva legyen az Autopilot Deployment szolgáltatásban. A regisztráció feldolgozása 48 órát is igénybe vehet.
1. Kattintson a **Tovább** gombra.
   * Az **üzembe helyezési mód**beállításnál válassza a **saját üzembe helyezés (előzetes verzió)** lehetőséget. Az ezzel a profillal rendelkező eszközök az eszközt regisztráló felhasználóhoz vannak társítva. Az eszköz regisztrálásához felhasználói hitelesítő adatokra van szükség. Fontos megjegyezni, hogy az eszközök öntelepítési módban való üzembe  helyezése lehetővé teszi a laptopok közös modellben való üzembe helyezését. A felhasználói hozzárendelés addig nem történik meg, amíg az eszköz első alkalommal nem lesz hozzárendelve a felhasználóhoz. Ennek eredményeképpen minden olyan felhasználói házirend, például a BitLocker nem lesz engedélyezve, amíg a felhasználó-hozzárendelés be nem fejeződik. A biztonságos eszközökre való bejelentkezéssel kapcsolatos további információkért lásd a [kiválasztott profilok](https://docs.microsoft.com/intune/device-profile-assign)című témakört.
   * Az Azure AD-hez való csatlakozáshoz a box-ban be kell mutatni az **Azure ad** - **t** , és szürkén kell kinéznie.
   * Válassza ki a Langugage (régió), a felhasználói fiók típusa **standard**értéket. 
1. Kattintson a **Tovább** gombra.
   * Válassza ki a hatókör címkét, ha előre konfigurált egyet.
1. Kattintson a **Tovább** gombra.
1. Válassza ki**a** > **kijelölt csoportokhoz**hozzárendelni kívánt **hozzárendeléseket** > . A **felvenni kívánt csoportok kiválasztása**területen válassza a **biztonságos munkaállomás-felhasználók**lehetőséget.
1. Kattintson a **Tovább** gombra.
1. Válassza a **Létrehozás** gombot a profil létrehozásához. Az AutoPilot üzembehelyezési profil most már hozzárendelhető az eszközökhöz.

Az Autopilot eszköz regisztrálása egy másik felhasználói élményt nyújt az eszköz típusától és szerepköreitől függően. Az üzembe helyezési példában egy olyan modellt mutatunk be, amelyben a biztonságos eszközök tömeges üzembe helyezhetők, és megoszthatók, de ha első alkalommal használják, az eszköz hozzá van rendelve egy felhasználóhoz. További információ: az [Intune Autopilot-eszközök regisztrációja](https://docs.microsoft.com/intune/device-enrollment).

### <a name="configure-windows-update"></a>Windows Update konfigurálása

A Windows 10-es naprakészen tartása az egyik legfontosabb dolog. Ahhoz, hogy a Windows biztonságos állapotban maradjon, üzembe kell helyeznie egy frissítési gyűrűt a frissítések a munkaállomásokra való alkalmazásának ütemének kezeléséhez. 

Ez az útmutató azt javasolja, hogy hozzon létre egy új frissítési gyűrűt, és módosítsa a következő alapértelmezett beállításokat:

Az Azure Portalon:

1. Ugrás a **Microsoft Intune** > **szoftverfrissítések** > **Windows 10-es frissítési gyűrűkre**.
1. Be
   * Név – az **Azure Managed Workstation frissítései**
   * Karbantartási csatorna – **Windows Insider – gyors**
   * Minőségi frissítés halasztása (nap) – **3**
   * Szolgáltatási frissítés halasztási időtartama (nap) – **3**
   * Automatikus frissítési viselkedés – **a végfelhasználói vezérlés nélküli automatikus telepítés és újraindítás**
   * A Windows-frissítések szüneteltetésének tiltása a felhasználó számára – **Letiltás**
   * A felhasználó jóváhagyásának megkövetelése a munkaidőn kívüli újraindításhoz – **kötelező**
   * A felhasználó újraindításának engedélyezése (lekért újraindítás) – **kötelező**
   * A felhasználók újraindításának átállítása automatikus újraindítás után (nap) – **3**
   * Késleltetett újraindítási emlékeztető (nap) – **3**
   * Függőben lévő újraindítások határidejének megadása (nap) – **3**

1. Kattintson a **Létrehozás** gombra.
1. A **hozzárendelések** lapon adja hozzá a **biztonságos** munkaállomások csoportot.

További információ a Windows Update házirendekről: [Policy CSP-Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integráció

A Windows Defender ATP és a Microsoft Intune együttműködve segít megelőzni a biztonsági réseket. Emellett korlátozhatják a szabálysértések hatását is. Az ATP-képességek valós idejű észlelést biztosítanak, valamint lehetővé teszik a végponti eszközök széles körű naplózását és naplózását.

A Windows Defender ATP és az Intune integrálásának konfigurálásához lépjen a Azure Portal.

1. Tallózással keresse meg **Microsoft Intune** > **eszköz megfelelőségének** > **Windows Defender ATP**-t.
1. Az 1. lépésben a **Windows DEFENDER ATP konfigurálása**területen válassza a Windows Defender ATP összekapcsolását a **windows Defender Security Center Microsoft Intune**lehetőségre.
1. A Windows Defender Security Centerban:
   1. Válassza a **Beállítások** > **Speciális funkciók**lehetőséget.
   1. **Microsoft Intune-kapcsolatok**esetében válassza **a be**lehetőséget.
   1. Válassza a **Beállítások mentése**lehetőséget.
1. A kapcsolatok létrejötte után térjen vissza az Intune-ba, és válassza a felül a **frissítés** lehetőséget.
1. Állítsa be a Windows **-eszközök csatlakoztatása 10.0.15063 és újabb verzióját a Windows DEFENDER ATP** -be **a**következőre:.
1. Kattintson a **Mentés** gombra.

További információ: a [Windows Defender komplex veszélyforrások elleni védelem](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Munkaállomás-profil megkeményedésének befejezése

A megoldás megerősítésének sikeres befejezéséhez töltse le és hajtsa végre a megfelelő parancsfájlt. Keresse meg a letöltési hivatkozásokat a kívánt **profil szintjéhez**:

| Profil | Letöltési hely | Filename |
| --- | --- | --- |
| Alacsony biztonság | – |  – |
| Fokozott biztonság | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Magas biztonság  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809). ps1 |
| Specializált | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Speciális megfelelőség * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Biztosított | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*A speciális megfelelőség egy olyan parancsfájl, amely kikényszeríti a NCSC Windows10 SecurityBaseline megadott speciális konfigurációt.

A szkript sikeres végrehajtása után frissítheti a profilokat és a szabályzatokat az Intune-ban. A továbbfejlesztett és biztonságos profilok parancsfájljai szabályzatokat és profilokat hoznak létre az Ön számára, de hozzá kell rendelnie a szabályzatot a **biztonságos** munkaállomások csoportjához.

* Itt találja a parancsfájlok által létrehozott Intune-eszköz konfigurációs profilokat: **Azure Portal** **Microsoft Intune eszköz**konfigurációsprofiljai > . >  > 
* Itt találja a parancsfájlok által létrehozott Intune-eszközök megfelelőségi szabályzatait: **Azure Portal**Microsoft Intune eszközmegfelelőségi > szabályzatai. >  > 

A parancsfájlok által végzett módosítások áttekintéséhez exportálhatja a profilokat. Így meghatározhat további megerősítést, amely a SECCON dokumentációjában ismertetett módon szükséges.

Futtassa az Intune-adatexportálási parancsfájlt `DeviceConfiguration_Export.ps1` a [DeviceConfiguration GiuHub adattárból](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) az összes aktuális Intune-profil exportálásához.

## <a name="additional-configurations-and-hardening-to-consider"></a>További konfigurációk és megerősítés a megfontoláshoz

Az itt található útmutatást követve biztonságos munkaállomást helyezett üzembe. Érdemes azonban további vezérlőket is figyelembe venni. Példa:

* másodlagos böngészőkhöz való hozzáférés korlátozása
* kimenő HTTP engedélyezése
* webhelyek kijelölésének tiltása
* engedélyek beállítása egyéni PowerShell-parancsfájlok futtatásához

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Szabályok beállítása a tűzfal konfigurációs szolgáltatójában (CSP)

Az engedélyezett és a letiltott végpontok esetében szükség szerint további módosításokat végezhet a bejövő és a kimenő szabályok kezelésében. Ahogy folytatja a biztonságos munkaállomás megerősítését, megszüntetheti a korlátozást, amely megtagadja az összes bejövő és kimenő forgalmat. Az engedélyezett kimenő helyek hozzáadásával általános és megbízható webhelyeket is hozzáadhat. További információ: [tűzfal konfigurációs szolgáltatás](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Az alapértelmezett korlátozott javaslatok a következők:

* Az összes bejövő megtagadása
* Az összes kimenő megtagadása

A Spamhaus-projekt karbantartja [a tartomány blokkolási listáját (kétsoros)](https://www.spamhaus.org/dbl/): egy jó erőforrást, amelyet kiindulási pontként használ a helyek blokkolásához.

### <a name="manage-local-applications"></a>Helyi alkalmazások kezelése

A biztonságos munkaállomás valóban megerősített állapotba kerül, amikor a helyi alkalmazások el lesznek távolítva, beleértve a hatékonyságnövelő alkalmazásokat is. Itt a Chrome-ot az alapértelmezett böngészőként adja hozzá, és az Intune-nal korlátozhatja a felhasználók azon képességét, hogy módosíthassák a böngészőt és a beépülő modulokat.

#### <a name="deploy-applications-using-intune"></a>Alkalmazások üzembe helyezése az Intune-nal

Bizonyos helyzetekben a biztonságos munkaállomáson olyan alkalmazások szükségesek, mint a Google Chrome böngésző. Az alábbi példa bemutatja, hogyan telepíthet Chrome-eszközöket a biztonsági csoport **biztonságos**munkaállomásokon.

1. Töltse le a [Windows 64 bites offline telepítő Chrome-csomagot](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Bontsa ki a fájlokat, és jegyezze fel a `GoogleChromeStandaloneEnterprise64.msi` fájl helyét.
1. A **Azure Portal** tallózással keresse **meg Microsoft Intune** > **ügyfélalkalmazások** > **alkalmazások** > **Hozzáadás**lehetőséget.
1. Az **alkalmazás típusa** **területen válassza az**üzletági lehetőséget.
1. Az **alkalmazáscsomag fájl**területen válassza ki a `GoogleChromeStandaloneEnterprise64.msi` fájlt a kibontott helyről, és kattintson az **OK gombra**.
1. Az **alkalmazásadatok területen adja**meg a leírást és a közzétevőt. Kattintson az **OK** gombra.
1. Válassza a **Hozzáadás** lehetőséget.
1. A hozzárendelések lapon válassza a **rendelkezésre álló** lehetőséget a regisztrált eszközökhöz a **hozzárendelés típusa**alatt.
1. A **befoglalt csoportok**területen adja hozzá a **biztonságos** munkaállomások csoportot.
1. Válassza **az OK**, majd a **Mentés**lehetőséget.

A Chrome-beállítások konfigurálásával kapcsolatos további útmutatásért lásd: a [Chrome böngésző kezelése Microsoft Intune](https://support.google.com/chrome/a/answer/9102677)használatával.

#### <a name="configuring-the-company-portal-for-custom-apps"></a>A vállalati portál konfigurálása egyéni alkalmazásokhoz

Biztonságos módban az alkalmazás telepítése az Intune vállalati portálra korlátozódik. A portál telepítésének azonban a Microsoft Storehoz való hozzáférésre van szüksége. A biztonságos megoldásban a vállalati portált offline módban is elérhetővé teheti az összes eszköz számára.

A [céges portál](https://docs.microsoft.com/Intune/store-apps-company-portal-app) Intune által felügyelt példánya igény szerinti hozzáférést biztosít a biztonságos munkaállomások felhasználói számára leküldhető további eszközökhöz.

Előfordulhat, hogy telepítenie kell a Windows 32 bites alkalmazásokat vagy más alkalmazásokat, amelyek üzembe helyezéséhez speciális előkészületek szükségesek. Ilyen esetekben a [Microsoft Win32 Content PREP eszköz](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) használatra kész `.intunewin` formátumú fájlt tud biztosítani a telepítéshez.

### <a name="use-powershell-to-create-custom-settings"></a>Egyéni beállítások létrehozása a PowerShell használatával

A PowerShell használatával is kiterjesztheti a gazdagépek felügyeleti képességeit. Ez a példa parancsfájl alapértelmezett hátteret állít be a gazdagépen. Ez a funkció a Azure Portal és a profilokon keresztül is elérhető. A példaként szolgáló parancsfájl csak a PowerShell működésének szemléltetésére szolgál.

Előfordulhat, hogy egyéni vezérlőket és beállításokat kell beállítania a biztonságos munkaállomásokon. Ez a példa a munkaállomás hátterét úgy módosítja, hogy a PowerShell használatával könnyen azonosítható az eszköz használatra kész, biztonságos munkaállomásként.

A Microsoft Scripting Center [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) parancsfájlja lehetővé teszi, hogy a Windows betöltse ezt az [ingyenes, általános háttérképet](https://i.imgur.com/OAJ28zO.png) az indításkor.

1. Töltse le a szkriptet egy helyi eszközre.
1. Frissítse a háttérkép customerXXXX és letöltési helyét. A példánkban a customerXXXX a háttérre cseréljük.  
1. Tallózással keresse meg a **Azure Portal** > **Microsoft Intune** > **eszköz konfigurációja** > **PowerShell-parancsfájlok** > **Hozzáadás**lehetőséget.
1. Adja meg a parancsfájl **nevét** , és adja meg a **parancsfájl helyét**.
1. Válassza a **Konfigurálás**lehetőséget.
   1. **A parancsfájl futtatásának beállítása a bejelentkezett hitelesítő adatokkal** **Igen**értékre.
   1. Kattintson az **OK** gombra.
1. Kattintson a **Létrehozás** gombra.
1. Válassza  > a hozzárendelések**csoportok lehetőséget**.
   1. Adja hozzá a biztonsági csoport **biztonságos**munkaállomásait.
   1. Kattintson a **Mentés** gombra.

## <a name="enroll-and-validate-your-first-device"></a>Az első eszköz regisztrálása és ellenőrzése

1. Az eszköz regisztrálásához a következő információkra lesz szüksége:
   * **Sorozatszám** – az eszköz vázán található.
   * **Windows-termékazonosító** – a **rendszer** > a Windows beállításai menüben található a Rendszerinformáció területen.
   * A [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) futtatásával lekérhet egy CSV-kivonatot tartalmazó fájlt az eszközök regisztrálásához szükséges összes információval.
   
     A `Get-WindowsAutoPilotInfo – outputfile device1.csv` futtatásával exportálhatja az adatokat CSV-fájlként, amelyet importálhat az Intune-ba.

     > [!NOTE]
     > A szkripthez emelt szintű jogosultságok szükségesek. Távoli aláírásként fut. A `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` parancs lehetővé teszi, hogy a parancsfájl megfelelően fusson.

   * Ezeket az adatokat összegyűjtheti a Windows 10 1809-es vagy újabb verziójára való bejelentkezéssel. A hardver-viszonteladó is megadhatja ezeket az információkat.
1. A **Azure Portal**nyissa meg a **Microsoft Intune** > **eszközök** > beléptetése**Windows** > -beléptetési**eszközök – Windows Autopilot-eszközök kezelése**című részt.
1. Válassza az **Importálás** lehetőséget, és válassza ki a CSV-fájlt.
1. Adja hozzá az eszközt a **biztonságos** munkaállomások biztonsági csoportjához.
1. A konfigurálni kívánt Windows 10-es eszközön nyissa meg a **Windows beállítások** > **frissítés & biztonsági** > **helyreállítás**lehetőséget.
   1. Válassza az első **lépések** lehetőséget **a számítógép**alaphelyzetbe állítása elemre.
   1. Kövesse az utasításokat az eszköz alaphelyzetbe állításához és újrakonfigurálásához a konfigurált profil-és megfelelőségi szabályzatokkal.

Az eszköz konfigurálása után végezze el a felülvizsgálatot, és ellenőrizze a konfigurációt. Ellenőrizze, hogy az első eszköz megfelelően van-e konfigurálva a telepítés folytatása előtt.

## <a name="assign-and-monitor"></a>Hozzárendelés és figyelés

Az eszközök és a felhasználók hozzárendeléséhez le kell képeznie a [kiválasztott profilokat](https://docs.microsoft.com/intune/device-profile-assign) a biztonsági csoportra. A szolgáltatáshoz engedélyeket igénylő összes új felhasználót is fel kell venni a biztonsági csoportba.

A profilokat az [Intune-profilok figyelésével](https://docs.microsoft.com/intune/device-profile-monitor)figyelheti.

## <a name="next-steps"></a>További lépések

* További információ a [Microsoft Intuneról](https://docs.microsoft.com/intune/index).
* Az [Azure ad](https://docs.microsoft.com/azure/active-directory/index)megismerése.
