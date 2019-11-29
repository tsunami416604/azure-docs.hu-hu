---
title: Azure által felügyelt munkaállomások üzembe helyezése – Azure Active Directory
description: Ismerje meg, hogyan helyezhet üzembe biztonságos, Azure által felügyelt munkaállomásokat a helytelen konfiguráció vagy a biztonság megsértése miatti szerződésszegés kockázatának csökkentése érdekében.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: d713dd968956f5bcc93e7b53ed2d7801e5d7bec2
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561928"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Biztonságos, Azure által felügyelt munkaállomás üzembe helyezése

Most, hogy [megértette a biztonságos munkaállomásokat](concept-azure-managed-workstation.md), itt az ideje, hogy megkezdje az üzembe helyezés folyamatát. Ezzel az útmutatóval meghatározott profilok használatával hozhat létre olyan munkaállomást, amely biztonságosabb az indítástól.

![Biztonságos munkaállomás üzembe helyezése](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

A megoldás üzembe helyezése előtt válasszon ki egy profilt. Egyszerre több profilt is használhat egy központi telepítésben, és hozzárendelheti őket címkékkel vagy csoportokkal.

> [!NOTE]
> Igény szerint alkalmazza a profilok bármelyikét. A másik profilba való áthelyezéshez rendeljen hozzá Microsoft Intune.

| Profil | Alacsony | Továbbfejlesztett | Magas | Specializált | Biztosított | Izolált |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Felhasználó az Azure AD-ben | Igen | Igen | Igen | Igen | Igen | Igen |
| Intune által felügyelt | Igen | Igen | Igen | Igen | Igen | Igen |
| Eszköz – Azure AD regisztrálva | Igen |  |  |  |  | |   |
| Eszköz – Azure AD-hez csatlakoztatott |   | Igen | Igen | Igen | Igen | Igen |
| Az Intune biztonsági alapterve alkalmazva |   | Igen <br> Bővített | Igen <br> (HighSecurity) | Igen <br> (NCSC) | Igen <br> Biztosított | n/a |
| A hardver megfelel a biztonságos Windows 10-es szabványoknak |   | Igen | Igen | Igen | Igen | Igen |
| Microsoft Defender ATP engedélyezve |   | Igen  | Igen | Igen | Igen | Igen |
| Rendszergazdai jogosultságok eltávolítása |   |   | Igen  | Igen | Igen | Igen |
| Üzembe helyezés a Microsoft Autopilot használatával |   |   | Igen  | Igen | Igen | Igen |
| Csak az Intune által telepített alkalmazások |   |   |   | Igen | Igen |Igen |
| Jóváhagyott listára korlátozott URL-címek |   |   |   | Igen | Igen |Igen |
| Internet Letiltva (bejövő/kimenő) |   |   |   |  |  |Igen |

> [!NOTE]
> A Secure Workstation Orientációs **eszközei** a profilokhoz és a házirendekhez lesznek hozzárendelve. A felhasználók nem alkalmazhatják közvetlenül rájuk a szabályzatokat, így az eszközök megosztása (megosztott eszközök) érvénybe lép. Ha egy biztonságos munkaállomás nincs megosztva az üzemelő példányban, vagy egyéni felhasználói házirendekre van szükség, a felhasználói házirend-profilok hozzárendelése a felhasználóhoz és az eszközhöz is hozzárendelhető. 

## <a name="license-requirements"></a>Licenckövetelmények

Az útmutatóban szereplő fogalmak feltételezik, hogy Microsoft 365 Nagyvállalati verzió E5 vagy egy egyenértékű SKU van. A jelen útmutatóban szereplő javaslatok közül néhányat alacsonyabb SKU-kal lehet megvalósítani. További információ: [Microsoft 365 nagyvállalati verzió licencelés](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

A licencek kiépítés automatizálásához gondolja át a felhasználók [csoport alapú licencelését](../users-groups-roles/licensing-groups-assign.md) .

## <a name="azure-active-directory-configuration"></a>Azure Active Directory konfiguráció

Azure Active Directory (Azure AD) a rendszergazdai munkaállomásokhoz tartozó felhasználókat, csoportokat és eszközöket kezeli. Az Identity Services és szolgáltatások engedélyezése [rendszergazdai fiókkal](../users-groups-roles/directory-assign-admin-roles.md).

A biztonságos munkaállomás-rendszergazdai fiók létrehozásakor a fiók elérhetővé válnak az aktuális munkaállomáson. Győződjön meg arról, hogy ismert biztonságos eszközt használ ehhez a kezdeti konfigurációhoz és az összes globális konfigurációhoz. Ha csökkenteni szeretné a támadási expozíciót az első alkalommal, vegye figyelembe az [útmutatást a kártevők elleni fertőzések megelőzése](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection)érdekében.

A többtényezős hitelesítés megkövetelése legalább a rendszergazdák számára. Lásd: [FELHŐALAPÚ MFA üzembe helyezése](../authentication/howto-mfa-getstarted.md) a megvalósítási útmutatóban.

### <a name="azure-ad-users-and-groups"></a>Azure AD-felhasználók és-csoportok

1. A Azure Portal keresse meg **Azure Active Directory** > **felhasználó** > **új felhasználót**.
1. Hozza létre az eszköz rendszergazdáját a [felhasználói oktatóanyag létrehozása](https://docs.microsoft.com/Intune/quickstart-create-user)című témakör lépéseit követve.
1. Be

   * **Név** – biztonságos munkaállomás rendszergazdája
   * **Felhasználónév** - `secure-ws-admin@identityitpro.com`
   * A **címtár-szerepkör** - **korlátozott rendszergazda** , és válassza ki az **Intune rendszergazdai** szerepkört.

1. Kattintson a **Létrehozás** gombra.

Ezután két csoportot hoz létre: munkaállomás-felhasználók és munkaállomás-eszközök.

A Azure Portal keresse meg **Azure Active Directory** > **csoport** > **új csoportot**.

1. A munkaállomás-felhasználók csoport esetében előfordulhat, hogy a felhasználók számára a licencek kiosztásának automatizálásához [csoport alapú licencelést](../users-groups-roles/licensing-groups-assign.md) szeretne konfigurálni.
1. A munkaállomás-felhasználók csoportnál adja meg a következőt:

   * **Csoport típusa** – biztonság
   * **Csoportnév** – biztonságos munkaállomás-felhasználók
   * **Tagság típusa** – hozzárendelve

1. Adja hozzá a biztonságos munkaállomás rendszergazda felhasználóját: `secure-ws-admin@identityitpro.com`
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

1. Lépjen **Azure Active Directory** > **eszközök** > **eszközbeállítások menüpontra**.
1. Ha a felhasználók lehetőségre van **kiválasztva** , **csatlakozhatnak az eszközökhöz az Azure ad**-ben, majd a "biztonságos munkaállomás-felhasználók" csoportot.

#### <a name="removal-of-local-admin-rights"></a>Helyi rendszergazdai jogosultságok eltávolítása

Ehhez a módszerhez az szükséges, hogy a VIP-, DevOps-és biztonságos munkaállomások felhasználói ne rendelkezzenek rendszergazdai jogokkal a gépen. A beállítás konfigurálása a Azure Portal:

1. Lépjen **Azure Active Directory** > **eszközök** > **eszközbeállítások menüpontra**.
1. Válassza a **nincs** lehetőséget **a további helyi rendszergazdák területen az Azure ad-hez csatlakoztatott eszközökön**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Többtényezős hitelesítés megkövetelése az eszközök csatlakoztatásához

Az eszközök Azure AD-hez való csatlakoztatásának további megerősítése:

1. Lépjen **Azure Active Directory** > **eszközök** > **eszközbeállítások menüpontra**.
1. Válassza az **Igen** lehetőséget a **többtényezős hitelesítés megkövetelése az eszközökhöz való csatlakozáshoz**.
1. Kattintson a **Mentés** gombra.

#### <a name="configure-mobile-device-management"></a>A mobileszköz-kezelés konfigurálása

A Azure Portal:

1. Keresse meg **Azure Active Directory** > **Mobility (Mdm és MAM)**  > **Microsoft Intune**.
1. Módosítsa a **Mdm felhasználói hatókörét** az **összes**értékre.
1. Kattintson a **Mentés** gombra.

Ezek a lépések lehetővé teszik bármely eszköz Intune-nal való kezelését. További információ [: az Intune gyors üzembe helyezésének beállítása a Windows 10-es eszközök automatikus regisztrálásához](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). Az Intune konfigurációs és megfelelőségi szabályzatait egy későbbi lépésben hozza létre.

#### <a name="azure-ad-conditional-access"></a>Azure AD feltételes hozzáférés

Az Azure AD feltételes hozzáférés segítségével a megfelelő eszközökre korlátozhatja a Kiemelt felügyeleti feladatokat. A **biztonságos munkaállomás-felhasználók** csoport előre definiált tagjainak a többtényezős hitelesítés végrehajtásához kell végrehajtaniuk a felhőalapú alkalmazásokba való bejelentkezéskor. Az ajánlott eljárás a vészhelyzeti hozzáférési fiókok kizárása a szabályzatból. További információ: [a vészhelyzeti hozzáférési fiókok kezelése az Azure ad-ben](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Intune-konfiguráció

### <a name="configure-enrollment-status"></a>Beléptetési állapot konfigurálása

Fontos, hogy a biztonságos munkaállomás megbízható tiszta eszköz legyen. Új eszközök vásárlása esetén ragaszkodhat ahhoz, hogy a gyári beállítások a [Windows 10 Pro-ra, S üzemmódban](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode)legyenek, ami korlátozza a biztonsági rések kockázatát az ellátási lánc kezelése során. Miután megkapta az eszközt a szállítótól, az Autopilot használatával módosíthatja azt S módból. Az alábbi útmutató részletesen ismerteti az átalakítási folyamat alkalmazását.

Annak biztosítása érdekében, hogy az eszközök a használat előtt teljesen konfigurálva legyenek, az Intune lehetővé teszi az eszközök **használatának blokkolását, amíg az összes alkalmazás és profil nincs telepítve**.

A **Azure Portal**:

1. Lépjen a **Microsoft Intune** > **eszközök** regisztrálása > **Windows-regisztráció** > **beléptetés állapota lap** > **alapértelmezett** > **Beállítások**elemre.
1. Állítsa az **alkalmazás-profil telepítési folyamatát** **Igen**értékre.
1. Állítsa be az **eszköz használatát, amíg az összes alkalmazás és profil nem települ** az **Igen**értékre.

### <a name="create-an-autopilot-deployment-profile"></a>Autopilot Deployment-profil létrehozása

Az eszközcsoport létrehozása után létre kell hoznia egy központi telepítési profilt az Autopilot-eszközök konfigurálásához.

Az Intune-ban a Azure Portalban:

1. Válassza az **eszközök beléptetése** > **Windows-beléptetés** > **üzembe helyezési profilok** > a **profil létrehozása**lehetőséget.
1. Be

   * Név – **biztonságos munkaállomás telepítési profilja**.
   * Leírás – **biztonságos munkaállomások üzembe helyezése**.
   * Állítsa az **összes megcélzó eszköz konvertálása az Autopilot** -re beállítást **Igen**értékre. Ez a beállítás biztosítja, hogy a listában szereplő összes eszköz regisztrálva legyen az Autopilot Deployment szolgáltatásban. 48 óra engedélyezése a regisztráció feldolgozásához.

1. Kattintson a **Tovább** gombra.

   * Az **üzembe helyezési mód**beállításnál válassza a **saját üzembe helyezés (előzetes verzió)** lehetőséget. Az ezzel a profillal rendelkező eszközök az eszközt regisztráló felhasználóhoz vannak társítva. Az eszköz regisztrálásához felhasználói hitelesítő adatokra van szükség. Fontos megjegyezni, hogy az eszközök **Öntelepítési** módban való üzembe helyezése lehetővé teszi a laptopok közös modellben való üzembe helyezését. A felhasználói hozzárendelés addig nem történik meg, amíg az eszköz első alkalommal nem lesz hozzárendelve a felhasználóhoz. Ennek eredményeképpen minden olyan felhasználói házirend, például a BitLocker nem lesz engedélyezve, amíg a felhasználó-hozzárendelés be nem fejeződik. A biztonságos eszközökre való bejelentkezéssel kapcsolatos további információkért lásd a [kiválasztott profilok](https://docs.microsoft.com/intune/device-profile-assign)című témakört.
   * Az Azure AD-hez való **csatlakozáshoz** a box-ban be kell mutatni az **Azure ad** -t, és szürkén kell kinéznie.
   * Válassza ki a nyelvet (régió), a felhasználói fiók típusát **standard**értéket. 

1. Kattintson a **Tovább** gombra.

   * Válassza ki a hatókör címkét, ha előre konfigurált egyet.

1. Kattintson a **Tovább** gombra.
1. Válassza a **hozzárendelések** >  > **kiválasztott csoportok** **számára** elemet. A **felvenni kívánt csoportok kiválasztása**területen válassza a **biztonságos munkaállomások**lehetőséget.
1. Kattintson a **Tovább** gombra.
1. Válassza a **Létrehozás** gombot a profil létrehozásához. Az Autopilot Deployment-profil mostantól elérhető az eszközökhöz való hozzárendeléshez.

Az Autopilot eszköz regisztrálása egy másik felhasználói élményt nyújt az eszköz típusától és szerepköreitől függően. Az üzembe helyezési példában egy olyan modellt mutatunk be, amelyben a biztonságos eszközök tömeges üzembe helyezhetők, és megoszthatók, de ha első alkalommal használják, az eszköz hozzá van rendelve egy felhasználóhoz. További információ: az [Intune Autopilot-eszközök regisztrációja](https://docs.microsoft.com/intune/device-enrollment).

### <a name="configure-windows-update"></a>Windows Update konfigurálása

A Windows 10-es naprakészen tartása az egyik legfontosabb dolog. Ahhoz, hogy a Windows biztonságos állapotban maradjon, üzembe kell helyeznie egy frissítési gyűrűt a frissítések a munkaállomásokra való alkalmazásának ütemének kezeléséhez. 

Ez az útmutató azt javasolja, hogy hozzon létre egy új frissítési gyűrűt, és módosítsa a következő alapértelmezett beállításokat:

Az Azure Portalon:

1. Nyissa meg **Microsoft Intune** > **szoftverfrissítések** > **Windows 10 frissítési**körök lehetőséget.
1. Be

   * Név – az **Azure által felügyelt munkaállomás frissítései**
   * Karbantartási csatorna – **Windows Insider – gyors**
   * Minőségi frissítés halasztása (nap) – **3**
   * Szolgáltatási frissítés halasztási időtartama (nap) – **3**
   * Automatikus frissítési viselkedés – **a végfelhasználói vezérlés nélküli automatikus telepítés és újraindítás**
   * A Windows-frissítések szüneteltetésének tiltása a felhasználó számára – **Letiltás**
   * A felhasználó jóváhagyásának megkövetelése a munkaidőn kívüli újraindításhoz – **kötelező**
   * A felhasználó újraindításának engedélyezése ( **lekért** újraindítás) – kötelező
   * A felhasználók újraindításának átállítása automatikus újraindítás után (nap) – **3**
   * Késleltetett újraindítási emlékeztető (nap) – **3**
   * Függőben lévő újraindítások határidejének megadása (nap) – **3**

1. Kattintson a **Létrehozás** gombra.
1. A **hozzárendelések** lapon adja hozzá a **biztonságos munkaállomások** csoportot.

Windows Update házirendekkel kapcsolatos további információkért lásd: [házirend CSP-Update](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integráció

A Windows Defender ATP és a Microsoft Intune együttműködve segít megelőzni a biztonsági réseket. Emellett korlátozhatják a szabálysértések hatását is. Az ATP-képességek valós idejű észlelést biztosítanak, valamint lehetővé teszik a végponti eszközök széles körű naplózását és naplózását.

A Windows Defender ATP és az Intune integrálásának konfigurálásához lépjen a Azure Portal.

1. Tallózással keresse meg **Microsoft Intune** > **eszköz megfelelőségét** > a **Windows Defender ATP**-t.
1. Az 1. lépésben a **Windows DEFENDER ATP konfigurálása**területen válassza a Windows Defender **ATP összekapcsolását a windows Defender Security Center Microsoft Intune**lehetőségre.
1. A Windows Defender Security Centerban:

   1. Válassza a **beállítások** > **Speciális funkciók**lehetőséget.
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
| Alacsony biztonság | – | – |
| Fokozott biztonság | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809). ps1 |
| Magas biztonság | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809). ps1 |
| Specializált | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Speciális megfelelőség * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10 (1803). ps1 |
| Biztosított | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline. ps1 |

\* a speciális megfelelőség egy olyan parancsfájl, amely a NCSC-Windows10 SecurityBaseline megadott speciális konfigurációt kényszeríti.

A szkript sikeres végrehajtása után frissítheti a profilokat és a szabályzatokat az Intune-ban. A továbbfejlesztett és biztonságos profilok parancsfájljai szabályzatokat és profilokat hoznak létre Önnek, de hozzá kell rendelnie a szabályzatot a **biztonságos munkaállomások** csoportjához.

* Itt találja a parancsfájlok által létrehozott Intune-eszköz konfigurációs profilokat: **Azure Portal** > **Microsoft Intune** > **Device Configuration** > **Profiles**.
* Itt találja a parancsfájlok által létrehozott Intune-eszközök megfelelőségi szabályzatait: **Azure Portal** > **Microsoft Intune** > **eszköz megfelelőségi** > **szabályzatok**.

A parancsfájlok által végzett módosítások áttekintéséhez exportálhatja a profilokat. Így meghatározhat további megerősítést, amely a [SECCON dokumentációjában](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)ismertetett módon szükséges.

Futtassa az Intune-adatexportálási parancsfájlt `DeviceConfiguration_Export.ps1` a [DeviceConfiguration GiuHub adattárból](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) az összes aktuális Intune-profil exportálásához.

## <a name="additional-configurations-and-hardening-to-consider"></a>További konfigurációk és megerősítés a megfontoláshoz

Az itt található útmutatást követve biztonságos munkaállomást helyezett üzembe. Érdemes azonban további vezérlőket is figyelembe venni. Példa:

* másodlagos böngészőkhöz való hozzáférés korlátozása
* kimenő HTTP engedélyezése
* webhelyek kijelölésének tiltása
* engedélyek beállítása egyéni PowerShell-parancsfájlok futtatásához

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Szabályok beállítása a tűzfal konfigurációs szolgáltatójában (CSP)

Az engedélyezett és a letiltott végpontok esetében szükség szerint további módosításokat végezhet a bejövő és a kimenő szabályok kezelésében. Ahogy folytatja a biztonságos munkaállomás megerősítését, megszüntetheti a korlátozást, amely megtagadja az összes bejövő és kimenő forgalmat. Az engedélyezett kimenő helyek hozzáadásával általános és megbízható webhelyeket is hozzáadhat. További információ: [tűzfal konfigurációs szolgáltatás](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

A korlátozó URL-forgalom kezelése a következőket tartalmazza:

* A biztonságos munkaállomás-profil parancsfájljában beállított összes bejövő forgalom tiltása.
* A kijelölt Azure-és Microsoft-szolgáltatások, például a Azure Cloud Shell és az Azure-jelszó alaphelyzetbe állításának engedélyezése, kivéve az összes kimenő forgalmat.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Ha a Blokkolási szabályok részletesebb ismertetését szeretné biztosítani, tekintse meg a Spamhaus-projektet, amely karbantartja [a tartományi tiltólista (kétsoros)](https://www.spamhaus.org/dbl/): egy jó erőforrást, amelyet speciális szabályokként kíván használni, hogy a rendszer blokkolja a helyeket.

### <a name="manage-local-applications"></a>Helyi alkalmazások kezelése

A biztonságos munkaállomás valóban megerősített állapotba kerül, amikor a helyi alkalmazások el lesznek távolítva, beleértve a hatékonyságnövelő alkalmazásokat is. Itt a Chrome-ot az alapértelmezett böngészőként adja hozzá, és az Intune-nal korlátozhatja a felhasználók azon képességét, hogy módosíthassák a böngészőt és a beépülő modulokat.

#### <a name="deploy-applications-using-intune"></a>Alkalmazások üzembe helyezése az Intune-nal

Bizonyos helyzetekben a biztonságos munkaállomáson olyan alkalmazások szükségesek, mint a Google Chrome böngésző. Az alábbi példa bemutatja, hogyan telepíthet Chrome-eszközöket a biztonsági csoport **biztonságos munkaállomásokon**.

1. Töltse le a [Windows 64 bites offline telepítő Chrome-csomagot](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Bontsa ki a fájlokat, és jegyezze fel a `GoogleChromeStandaloneEnterprise64.msi` fájl helyét.
1. A **Azure Portal** tallózással keresse meg **Microsoft Intune** > **ügyfélalkalmazások** > **alkalmazások** > **Hozzáadás**lehetőséget.
1. Az **alkalmazás típusa** **területen válassza az üzletági lehetőséget**.
1. Az **alkalmazáscsomag fájl**területen válassza ki a `GoogleChromeStandaloneEnterprise64.msi` fájlt a kinyert helyről, és kattintson az **OK gombra**.
1. Az **alkalmazásadatok területen adja**meg a leírást és a közzétevőt. Kattintson az **OK** gombra.
1. Válassza a **Hozzáadás** lehetőséget.
1. A **hozzárendelések** lapon válassza a **rendelkezésre álló lehetőséget a regisztrált eszközökhöz** a **hozzárendelés típusa**alatt.
1. A **befoglalt csoportok**területen adja hozzá a **biztonságos munkaállomások** csoportot.
1. Válassza **az OK**, majd a **Mentés**lehetőséget.

A Chrome-beállítások konfigurálásával kapcsolatos további információkért lásd: a [Chrome böngésző kezelése a Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>A vállalati portál konfigurálása egyéni alkalmazásokhoz

Biztonságos módban az alkalmazás telepítése az Intune vállalati portálra korlátozódik. A portál telepítésének azonban a Microsoft Storehoz való hozzáférésre van szüksége. A biztonságos megoldásban a vállalati portált offline módban is elérhetővé teheti az összes eszköz számára.

A [céges portál](https://docs.microsoft.com/Intune/store-apps-company-portal-app) Intune által felügyelt példánya igény szerinti hozzáférést biztosít a biztonságos munkaállomások felhasználói számára leküldhető további eszközökhöz.

Előfordulhat, hogy telepítenie kell a Windows 32 bites alkalmazásokat vagy más alkalmazásokat, amelyek üzembe helyezéséhez speciális előkészületek szükségesek. Ilyen esetekben a [Microsoft Win32 Content PREP eszköz](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) használatra kész `.intunewin` formátumú fájlt biztosít a telepítéshez.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>A feltételes hozzáférés csak a biztonságos munkaállomás hozzáférését engedélyezi Azure Portal

Az Azure AD lehetővé teszi az Azure Cloud Management portál felügyeletét és korlátozását, valamint azt, hogy ki és mi férhet hozzá. A [feltételes hozzáférés](../conditional-access/overview.md) engedélyezése biztosítja, hogy csak a biztonságos munkaállomás tudja kezelni vagy megváltoztatni az erőforrásokat. Fontos, hogy ennek a funkciónak a üzembe helyezése során vegye figyelembe, hogy a [vészhelyzeti hozzáférés](../users-groups-roles/directory-emergency-access.md) funkciói csak szélsőséges esetekben és a szabályzattal felügyelt fiók esetében használhatók.

> [!NOTE]
> Létre kell hoznia egy felhasználói csoportot, és fel kell vennie a vészhelyzeti felhasználót, amely megkerülheti a feltételes hozzáférési szabályzatot. Példánkban van egy **Emergency BreakGlass** nevű biztonsági csoport

1. Keresse meg a **Azure Portal** > **Microsoft Intune** > **feltételes hozzáférési szabályzatok** > **új**szabályzatot.
1. Adja meg a szabályzat **nevét** .
1. **Felhasználói és csoportok** kiválasztása > **felhasználók és csoportok kiválasztása** 
1. Jelölje be a > **Directory-szerepkörök** **belefoglalása** jelölőnégyzetet, > Válassza ki a szerepköröket > globális rendszergazda, Kiemelt szerepkörű rendszergazda, privilegizált hitelesítő rendszergazda, biztonsági rendszergazda, megfelelőségi rendszergazda, feltételes hozzáférésű rendszergazda, alkalmazás-rendszergazda, Felhőbeli alkalmazás rendszergazdája, Intune szolgáltatás-rendszergazda
1. Válassza a **kizárás** > a **felhasználók és csoportok** kiválasztása lehetőséget > Válassza ki a **kizárt felhasználók kiválasztása** > válassza ki a **vészhelyzeti BreakGlass** csoportot.
1. Válassza ki a **felhőalapú alkalmazásokat vagy műveleteket** > válassza ki **az összes felhőalapú alkalmazást**
1. Válassza ki a **feltételeket** > válassza az **eszközök platformok** kiválasztása lehetőséget > válassza az **Igen** > válassza az **eszközök kiválasztása** lehetőséget, majd válassza a **Windows** lehetőséget.
1. Jelölje be a **hozzáférés-vezérlés** > válassza a **hozzáférés engedélyezése** **Igen** lehetőséget > válassza az **eszköz megfelelőként való megjelölésének megkövetelése**lehetőséget. 
1. Válassza **a** **házirend engedélyezése** > be lehetőséget
 
Ez a házirend-beállítás biztosítja, hogy a rendszergazdáknak megfelelő Windows-eszközt kell használniuk, amelyet az Intune és a WDATP állít be. 

A szervezeteknek érdemes megfontolniuk a régi hitelesítési protokollok blokkolását is a környezetükben. A feladat több módon is elvégezhető, így az örökölt hitelesítési protokollok blokkolásával kapcsolatos további információkért tekintse meg a következő cikket [: a régi hitelesítés letiltása az Azure ad-be feltételes hozzáféréssel](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Egyéni beállítások létrehozása a PowerShell használatával

A PowerShell használatával is kiterjesztheti a gazdagépek felügyeleti képességeit. Ez a példa parancsfájl alapértelmezett hátteret állít be a gazdagépen. Ez a funkció a Azure Portal és a profilokon keresztül is elérhető. A példaként szolgáló parancsfájl csak a PowerShell működésének szemléltetésére szolgál.

Előfordulhat, hogy egyéni vezérlőket és beállításokat kell beállítania a biztonságos munkaállomásokon. Ez a példa a munkaállomás hátterét úgy módosítja, hogy a PowerShell használatával könnyen azonosítható az eszköz használatra kész, biztonságos munkaállomásként.

A Microsoft Scripting Center [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) parancsfájlja lehetővé teszi, hogy a Windows betöltse ezt az [ingyenes, általános háttérképet](https://i.imgur.com/OAJ28zO.png) indításkor.

1. Töltse le a szkriptet egy helyi eszközre.
1. Frissítse a háttérkép customerXXXX és letöltési helyét. A példánkban a customerXXXX a háttérre cseréljük.
1. Keresse meg a **Azure Portal** > **Microsoft Intune** > **eszköz konfigurációja** > **PowerShell-parancsfájlok** > **Hozzáadás**lehetőséget.
1. Adja meg a parancsfájl **nevét** , és adja meg a **parancsfájl helyét**.
1. Válassza a **Konfigurálás**lehetőséget.
   1. **A parancsfájl futtatásának beállítása a bejelentkezett hitelesítő adatokkal** **Igen**értékre.
   1. Kattintson az **OK** gombra.
1. Kattintson a **Létrehozás** gombra.
1. Válassza a **hozzárendelések** lehetőséget > **csoportok kiválasztása lehetőséget**.
   1. Adja hozzá a biztonsági csoport **biztonságos munkaállomásait**.
   1. Kattintson a **Mentés** gombra.

## <a name="enroll-and-validate-your-first-device"></a>Az első eszköz regisztrálása és ellenőrzése

1. Az eszköz regisztrálásához a következő információkra lesz szüksége:
   * **Sorozatszám** – az eszköz vázán található.
   * **Windows-termékazonosító** – a **rendszer** ** > a** Windows beállításai menüből.
   * A [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) futtatásával lekérhet egy CSV-kivonatot tartalmazó fájlt az eszközök regisztrálásához szükséges összes információval.
   
     `Get-WindowsAutoPilotInfo – outputfile device1.csv` futtatásával exportálhatja az adatokat CSV-fájlként, amelyet importálhat az Intune-ba.

     > [!NOTE]
     > A szkripthez emelt szintű jogosultságok szükségesek. Távoli aláírásként fut. A `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` parancs lehetővé teszi, hogy a parancsfájl megfelelően fusson.

   * Ezeket az adatokat összegyűjtheti a Windows 10 1809-es vagy újabb verziójára való bejelentkezéssel. A hardver-viszonteladó is megadhatja ezeket az információkat.
1. A **Azure Portal**válassza a **Microsoft Intune** > **eszközök** regisztrálása > **Windows-beléptetés** > **eszközök – Windows Autopilot-eszközök kezelése**lehetőséget.
1. Válassza az **Importálás** lehetőséget, és válassza ki a CSV-fájlt.
1. Adja hozzá az eszközt a **biztonságos munkaállomások** biztonsági csoportjához.
1. A konfigurálni kívánt Windows 10-es eszközön nyissa meg a **Windows beállításai** > **frissítés & biztonsági** > **helyreállítás**lehetőséget.
   1. Válassza az első **lépések** lehetőséget **a számítógép alaphelyzetbe állítása**elemre.
   1. Kövesse az utasításokat az eszköz alaphelyzetbe állításához és újrakonfigurálásához a konfigurált profil-és megfelelőségi szabályzatokkal.

Az eszköz konfigurálása után végezze el a felülvizsgálatot, és ellenőrizze a konfigurációt. Ellenőrizze, hogy az első eszköz megfelelően van-e konfigurálva a telepítés folytatása előtt.

## <a name="assign-devices"></a>Eszközök kiosztása

Az eszközök és a felhasználók hozzárendeléséhez le kell képeznie a [kiválasztott profilokat](https://docs.microsoft.com/intune/device-profile-assign) a biztonsági csoportra. A szolgáltatáshoz engedélyeket igénylő összes új felhasználót is fel kell venni a biztonsági csoportba.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>A Sentinel és a Windows Defender ATP használata a biztonsági incidensek monitorozásához és megválaszolásához

A biztonságos munkaállomás központi telepítésének figyelése a [Sentinel] engedélyezésével és a [fenyegetések és a biztonsági rések kezelésével](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) valósítható meg, az útmutató nem nyújt teljes körű fenyegetést a fenyegetések ellen, de jó általános erőfeszítést tesz a lehetséges biztonsági incidensek monitorozására és reagálására.

Az **Azure Sentinel** az alábbiakat fogja használni: 

* Adatok gyűjtése az Intune-ból, a Azure Portalból és az Azure AD-ből a felhasználók és eszközök figyeléséhez
* A felhasználó-és eszköz-konfiguráció gyanús tevékenységének vizsgálata
* A WDATP használatával megvizsgálhatja a biztonsági vizsgálatokat

A Sentinel monitorozásához az szükséges, hogy az összekötők az adatforrásokhoz, például az Azure AD-hez legyenek beállítva.

1. A **Azure Portal**lépjen az **Azure Sentinel (előzetes verzió)** > válassza a **Hozzáadás** lehetőséget.
1. Az **Azure sentinelhez hozzáadandó munkaterület** kiválasztása lapon válassza az **Új munkaterület létrehozása** lehetőséget.
1. Be
   * **Log Analytics munkaterület** – "biztonságos munkaállomás figyelése"
   * **Előfizetés** – válassza ki az aktív előfizetését
   * **Erőforráscsoport** – válassza az * * új * * > biztonságos munkaállomás RG > **OK**
   * **Hely** – válassza ki az üzemelő példányhoz legjobban illeszkedő helyet
   * **Egységár** – válasszon **GB-onként (2018)**
1. Kattintson az **OK** gombra.

Ezután az elérhető biztonságos munkaállomás-adatforrásokat a figyeléshez fogjuk kapcsolni.

1. A **Azure Portal**lépjen az **Azure Sentinel munkaterületre** > válassza a **biztonságos munkaállomás figyelése** munkaterületet.
1. **Adatösszekötők** kiválasztása
1. Az előfeltétel áttekintése után válassza ki **Azure Active Directory** > az összekötő oldal megnyitása >. Folytassa a konfigurálással, és válassza a **Kapcsolódás** az Azure ad bejelentkezési naplóihoz, valamint az Azure ad-naplókhoz lehetőséget.
1. Az előfeltétel áttekintése után válassza az **Azure-tevékenység** > az összekötő > megnyitása lapot. Folytassa az Azure-Tevékenységnaplók konfigurálásával > Válassza ki az előfizetést > válassza a **Kapcsolódás** lehetőséget.

Ahogy az adatokat a Sentinel gyűjti, a **Azure Portal**lehetőség kiválasztásával megfigyelheti a tevékenységeket, az **Azure Sentinel áttekintése című témakört** . 

A **Windows DEFENDER ATP (WDATP)** szolgáltatását a következőre fogjuk használni:

* A biztonsági rések és a helytelen konfigurációk folyamatos megfigyelése és figyelése
* A WDATP használatával rangsorolhatja a dinamikus fenyegetéseket a vadonban
* A biztonsági rések a végpontok észlelésével és válaszával (EDR) kapcsolatos riasztásokkal való korrelációja
* A számítógép szintű biztonsági rések azonosítása a vizsgálat során az irányítópult használatával
* Szervizelések leküldése az Intune-ba

Állítsa be a [DEFENDER ATP-irányítópultját](https://securitycenter.windows.com/machines). Útmutató a [veszélyforrások & a sebezhetőségi kezelés irányítópultjának áttekintéséhez](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Alkalmazások figyelése a Microsoft monitoring Agent (MMA) használatával
A speciális munkaállomástól kezdődően az App Locker engedélyezve van az alkalmazások munkaállomáson való figyelésére. Ahhoz, hogy a figyelés integrálva legyen a Log Analytics munkaterületen, az MMA-ügynököt és a konfigurációt követni kell. 

> [!NOTE]
> A speciális munkaállomás-profil tartalmazza az AppLocker figyelési házirendjeit. A szabályzatok üzembe helyezése az ügyfélen futó alkalmazások tevékenységének figyeléséhez szükséges.

Az MMA-ügynök üzembe helyezése az Intune PowerShell-parancsfájllal

1. Töltse le a telepítési [parancsfájlt egy helyi eszközre](https://aka.ms/securedworkstationgit).
1. A paraméterek, a **$WorkSpaceID** és a **$WorkSpaceKey** frissítése
1. Keresse meg a **Azure Portal** > **Microsoft Intune** > **eszköz konfigurációja** > **PowerShell-parancsfájlok** > **Hozzáadás**lehetőséget.
1. Adja meg a parancsfájl **nevét** , és adja meg a **parancsfájl helyét**.
1. Válassza a **Konfigurálás**lehetőséget.
   1. **A parancsfájl futtatásának beállítása a bejelentkezett hitelesítő adatokkal** **Igen**értékre.
   1. Kattintson az **OK** gombra.
1. Kattintson a **Létrehozás** gombra.
1. Válassza a **hozzárendelések** lehetőséget > **csoportok kiválasztása lehetőséget**.
   1. Adja hozzá a biztonsági csoport **biztonságos munkaállomásait**.
   1. Kattintson a **Mentés** gombra.

Ezután be kell állítania Log Analytics az új naplók fogadásához
1. A **Azure Portal**nyissa meg a **log Analytics munkaterületet** > Select-"Secure Workstation monitoring"
1. Válassza a **Speciális beállítások** > **az** adat > **Windows-eseménynaplók** lehetőséget.
1. Az **események gyűjtése a következő eseménynaplókban** 
1. Be
   * "Microsoft-Windows-AppLocker/EXE és DLL" > a **tájékoztató** kijelölésének kiválasztása
   * "Microsoft-Windows-AppLocker/MSI és script" > a **tájékoztató** kijelölésének kiválasztása
   * "Microsoft-Windows-AppLocker/csomagolt alkalmazás – központi telepítés" **> kijelölésének** kikapcsolása
   * "Microsoft-Windows-AppLocker/csomagolt alkalmazás – végrehajtás" **> kijelölésének** kikapcsolása
1. Kattintson a **Mentés** gombra

Az alkalmazás naplózása a kiválasztott Log Analytics munkaterületen lesz elérhető.

## <a name="monitoring"></a>Monitoring

* Ismerje meg, hogyan [derítheti fel a fenyegetéseket az Azure Sentinel](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) használatával
* [Incidensek vizsgálata az Azure Sentineltel](https://docs.microsoft.com/azure/sentinel/tutorial-investigate-cases)
* [Automatizált veszélyforrásokkal kapcsolatos válaszok beállítása az Azure Sentinelben](https://docs.microsoft.com/azure/sentinel/tutorial-respond-threats-playbook)
* Az [expozíciós pontszám](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score) áttekintésének ismertetése
* [Biztonsági javaslat](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation) áttekintése
* Biztonsági [szervizelések](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) kezelése
* [Végpontok észlelésének és válaszának](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response) kezelése
* Profilok figyelése az [Intune-profilok figyelésével](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Következő lépések

* További információ a [Microsoft Intuneról](https://docs.microsoft.com/intune/index).
* Az [Azure ad](../index.yml)megismerése.
* A [Microsoft Defender komplex veszélyforrások elleni védelem](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) használata
* Az [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/) felderítése
