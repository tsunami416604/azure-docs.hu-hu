---
title: Azure által felügyelt munkaállomások telepítése – Azure Active Directory
description: Ismerje meg, hogyan helyezhet üzembe biztonságos, Azure által felügyelt munkaállomásokat a helytelen konfiguráció vagy a biztonsági rés miatti megszakadás kockázatának csökkentése érdekében.
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
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672605"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Biztonságos, Azure által felügyelt munkaállomás üzembe helyezése

Most, hogy már tisztában van a [biztonságos munkaállomások,](concept-azure-managed-workstation.md)itt az ideje, hogy megkezdje a telepítési folyamatot. Ezzel az útmutatással definiált profilok at hozhat létre egy munkaállomás, amely a kezdetektől biztonságosabb.

![Biztonságos munkaállomás telepítése](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

A megoldás üzembe helyezése előtt jelöljön ki egy profilt. Egyszerre több profilt is használhat a központi telepítésben, és címkékkel vagy csoportokkal rendelheti hozzá őket.

> [!NOTE]
> Alkalmazza a profilok bármelyikét az Igényeinek megfelelően. Egy másik profilba áthelyezheti, ha hozzárendeli a Microsoft Intune-ban.

| Profil | Alacsony | Továbbfejlesztett | Magasság | Specializált | Biztosított | Elkülönített |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Felhasználó az Azure AD-ben | Igen | Igen | Igen | Igen | Igen | Igen |
| Intune által felügyelt | Igen | Igen | Igen | Igen | Igen | Igen |
| Eszköz – Regisztrált Azure AD | Igen |  |  |  |  | |   |
| Eszköz – Az Azure AD csatlakozott |   | Igen | Igen | Igen | Igen | Igen |
| Az Intune biztonsági alapkonfigurációja alkalmazva |   | Igen <br> (Továbbfejlesztett) | Igen <br> (Magas Biztonsági) | Igen <br> (NCSC) | Igen <br> (Biztonságos) | NA |
| A hardver megfelel a biztonságos Windows 10 szabványoknak |   | Igen | Igen | Igen | Igen | Igen |
| Microsoft Defender ATP engedélyezve |   | Igen  | Igen | Igen | Igen | Igen |
| Az admin jogok eltávolítása |   |   | Igen  | Igen | Igen | Igen |
| Telepítés a Microsoft Autopilot használatával |   |   | Igen  | Igen | Igen | Igen |
| Csak az Intune által telepített alkalmazások |   |   |   | Igen | Igen |Igen |
| Jóváhagyott listára korlátozott URL-címek |   |   |   | Igen | Igen |Igen |
| Internet blokkolva (bejövő/kimenő) |   |   |   |  |  |Igen |

> [!NOTE]
> A biztonságos munkaállomás-vezérlőeszközök profilokés házirendek lesznek hozzárendelve. **devices** A felhasználók nem alkalmazhatják a házirendeket közvetlenül rájuk, így az eszközmegosztás (megosztott eszközök) érvényben maradnak. Ha egy biztonságos munkaállomás nincs megosztva a központi telepítésben, vagy egyéni felhasználói házirendek szükségesek, a felhasználói házirend-profilok hozzárendelése hozzárendelhető a felhasználóhoz és az eszközhöz. 

## <a name="license-requirements"></a>Licenckövetelmények

Az útmutatóban tárgyalt fogalmak feltételezik, hogy microsoft 365 Enterprise E5 vagy azzal egyenértékű termékváltozat. Az útmutatóban szereplő javaslatok némelyike alacsonyabb sk-ekkel valósítható meg. További információt a [Microsoft 365 Enterprise licenceléscímű témakörben talál.](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)

A licenckiépítés automatizálásához fontolja meg a [felhasználók csoportalapú licencelését.](../users-groups-roles/licensing-groups-assign.md)

## <a name="azure-active-directory-configuration"></a>Az Azure Active Directory konfigurációja

Az Azure Active Directory (Azure AD) kezeli a felhasználókat, csoportokat és eszközöket a rendszergazdai munkaállomásokhoz. Engedélyezze az identitásszolgáltatásokat és -szolgáltatásokat [rendszergazdai fiókkal.](../users-groups-roles/directory-assign-admin-roles.md)

A biztonságos munkaállomás-rendszergazdai fiók létrehozásakor a fiókot az aktuális munkaállomásnak teszi elérhetővé. Győződjön meg arról, hogy egy ismert biztonságos eszközt használ a kezdeti konfiguráció és az összes globális konfiguráció. Az első felhasználói élmény támadási expozíciójának csökkentése érdekében kövesse a [rosszindulatú programok okozta fertőzések megelőzéséhez adott útmutatást.](/windows/security/threat-protection/intelligence/prevent-malware-infection)

Többtényezős hitelesítés tanuskodhat, legalábbis a rendszergazdák számára. A megvalósítási útmutatóért [lásd: Felhőalapú mfa telepítése.](../authentication/howto-mfa-getstarted.md)

### <a name="azure-ad-users-and-groups"></a>Azure AD-felhasználók és -csoportok

1. Az Azure Portalon keresse meg az **Azure Active Directory** > **felhasználói** > **új felhasználót.**
1. Hozza létre az eszköz rendszergazdáját a [Felhasználói létrehozása oktatóanyag](/Intune/quickstart-create-user)lépéseit követve.
1. Adja meg a következőt:

   * **Név** – Biztonságos munkaállomás-rendszergazda
   * **Felhasználónév** - `secure-ws-admin@identityitpro.com`
   * **Directory role** - **Korlátozott rendszergazda,** és válassza ki az **Intune rendszergazdai** szerepkört.

1. Kattintson a **Létrehozás** gombra.

Ezután hozzon létre két csoportot: munkaállomás-felhasználókat és munkaállomás-eszközöket.

Az Azure Portalon keresse meg az **Azure Active Directory** > **csoportok** > **új csoportját.**

1. A munkaállomás-felhasználók csoport esetében célszerű lehet [a csoportalapú licencelést](../users-groups-roles/licensing-groups-assign.md) úgy konfigurálni, hogy automatizálja a licencek felhasználók számára történő kiépítését.
1. A munkaállomás felhasználói csoportjához írja be a következőt:

   * **Csoport típusa** - Biztonság
   * **Csoport neve** - Biztonságos munkaállomás-felhasználók
   * **Tagság típusa** - Hozzárendelve

1. Adja hozzá a biztonságos munkaállomás-rendszergazda felhasználóját:`secure-ws-admin@identityitpro.com`
1. Hozzáadhat más felhasználókat is, akik biztonságos munkaállomásokat kezelnek.
1. Kattintson a **Létrehozás** gombra.
1. A munkaállomás-eszközök csoportba írja be a következőt:

   * **Csoport típusa** - Biztonság
   * **Csoport neve** - Biztonságos munkaállomások
   * **Tagság típusa** - Hozzárendelve

1. Kattintson a **Létrehozás** gombra.

### <a name="azure-ad-device-configuration"></a>Az Azure AD-eszköz konfigurációja

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Annak megadása, hogy ki csatlakozhat eszközökhöz az Azure AD-hez

Konfigurálja az eszközök beállítását az Active Directoryban úgy, hogy a felügyeleti biztonsági csoport csatlakozhasson az eszközökhöz a tartományhoz. A beállítás konfigurálása az Azure Portalról:

1. Nyissa meg az Azure Active > **Directory-eszközök** > **eszközbeállításait.** **Azure Active Directory**
1. Válassza **a Kiválasztott** csoportban A felhasználók **csatlakozhatnak eszközök az Azure AD,** majd válassza ki a "Biztonságos munkaállomás felhasználói" csoportot.

#### <a name="removal-of-local-admin-rights"></a>A helyi rendszergazdai jogok eltávolítása

Ez a módszer megköveteli, hogy a VIP, DevOps és biztonságos szintű munkaállomások felhasználói ne legyenek rendszergazdai jogosultságaik a gépeiken. A beállítás konfigurálása az Azure Portalról:

1. Nyissa meg az Azure Active > **Directory-eszközök** > **eszközbeállításait.** **Azure Active Directory**
1. Válassza a **Nincs** lehetőséget **az Azure AD-hez csatlakozó eszközök További helyi rendszergazdák csoportjában.**

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Eszközök csatlakoztatásának megkövetelése többtényezős hitelesítésre

Az eszközök Azure AD-hez való csatlakoztatásának folyamatának további erősítése:

1. Nyissa meg az Azure Active > **Directory-eszközök** > **eszközbeállításait.** **Azure Active Directory**
1. Az **Eszközök csatlakoztatásához többtényezős hitelesítés megkövetelése**csoport **igen** választógombot.
1. Kattintson a **Mentés** gombra.

#### <a name="configure-mobile-device-management"></a>Mobileszköz-kezelés konfigurálása

Az Azure portalról:

1. Tallózás az **Azure Active Directory** > **Mobility (MDM és MAM)** > **Microsoft Intune**.
1. Módosítsa az **MDM felhasználói hatókörének** beállítását **Mind (Összes)** beállításra.
1. Kattintson a **Mentés** gombra.

Ezek a lépések lehetővé teszik, hogy bármilyen eszközt kezeljen az Intune-nal. További információt az [Intune rövid útmutatója: Automatikus regisztráció beállítása Windows 10-es eszközökhöz](/Intune/quickstart-setup-auto-enrollment)című témakörben talál. Az Intune konfigurációs és megfelelőségi szabályzatait egy későbbi lépésben hozza létre.

#### <a name="azure-ad-conditional-access"></a>Azure AD-beli feltételes hozzáférés

Az Azure AD feltételes hozzáférés segítségével korlátozhatja a kiemelt felügyeleti feladatokat a megfelelő eszközökre. A **Biztonságos munkaállomás-felhasználók** csoport előre definiált tagjainak többtényezős hitelesítést kell végrehajtaniuk, amikor bejelentkeznek a felhőalapú alkalmazásokba. Ajánlott eljárás, hogy kizárja a vészhelyzeti hozzáférési fiókokat a szabályzatból. További információ: [Vészelérési fiókok kezelése az Azure AD-ben.](../users-groups-roles/directory-emergency-access.md)

## <a name="intune-configuration"></a>Intune-konfiguráció

### <a name="configure-enrollment-status"></a>Regisztráció állapotának konfigurálása

Fontos, hogy a biztonságos munkaállomás megbízható, tiszta eszköz legyen. Új eszközök vásárlásakor ragaszkodhat ahhoz, hogy gyárilag [S módban windows 10 Pro-ra](/Windows/deployment/Windows-10-pro-in-s-mode)vannak beállítva, ami korlátozza a biztonsági réseknek való kitettséget az ellátási lánc kezelése során. Miután megkapta az eszközt a szállítójától, az Autopilot segítségével s módból módosíthatja azt. Az alábbi útmutató részletesen ismerteti az átalakítási folyamat alkalmazását.

Annak érdekében, hogy az eszközök használat előtt teljesen konfigurálva legyenek, az Intune lehetővé teszi az **eszközhasználatának letiltását mindaddig, amíg az összes alkalmazás és profil telepítve nem lesz.**

Az **Azure portalról:**

1. Nyissa meg a **Microsoft Intune-eszköz** > **regisztrációja** > **Windows-regisztráció** > állapota**lap** > **alapértelmezett** > beállításai**lapot.**
1. Állítsa az **Alkalmazásprofil telepítésének folyamata megjelenítése** **igen**beállításra.
1. Állítsa be **az eszközhasználat blokkolása lehetőséget, amíg az összes alkalmazás és profil telepítve nem lesz** Az **Igen**.

### <a name="create-an-autopilot-deployment-profile"></a>AutoPilot üzembehelyezési profil létrehozása

Az eszközcsoport létrehozása után létre kell hoznia egy központi telepítési profilt az Autopilot eszközök konfigurálásához.

Az Intune-ban az Azure Portalon:

1. Válassza **az Eszközigénylés** > **központi telepítési** > **profilok** > **létrehozása lehetőséget.**
1. Adja meg a következőt:

   * Név – **Biztonságos munkaállomás-telepítési profil**.
   * Leírás - **Biztonságos munkaállomások telepítése**.
   * Állítsa a **Minden megcélzott eszköz átalakítása az Autopilotra** beállítást **Igen** értékre. Ez a beállítás biztosítja, hogy a listában lévő összes eszköz regisztrálva legyen az Autopilot üzembehelyezési szolgáltatásban. A regisztráció feldolgozása 48 órát is igénybe vehet.

1. Válassza a **Tovább lehetőséget.**

   * **A Központi telepítés módban**válassza az **Öntelepítés (előzetes verzió)** lehetőséget. Az ilyen profillal rendelkező eszközök az eszközt beiratkozó felhasználóhoz vannak társítva. Az eszköz regisztrálásához felhasználói hitelesítő adatokra van szükség. Fontos megjegyezni, hogy egy eszköz telepítése az **önüzembe mód** lehetővé teszi, hogy a laptopok egy megosztott modellben. Nem történik felhasználói hozzárendelés, amíg az eszköz nincs hozzárendelve a felhasználóhoz az első alkalommal. Ennek eredményeképpen az olyan felhasználói házirendek, mint például a BitLocker, nem lesznek engedélyezve, amíg egy felhasználói hozzárendelés be nem fejeződik. A biztonságos eszközre való bejelentkezésről a [kijelölt profilok](/intune/device-profile-assign)című témakörben talál további információt.
   * Az **Azure AD-hez való csatlakozás mint** mező ben az Azure **AD-hez csatlakozott,** és szürkén jelenik meg.
   * Válassza ki a nyelvet (régió), felhasználói fiók **típusszabvány**. 

1. Válassza a **Tovább lehetőséget.**

   * Ha előre konfigurált egy hatókörcímkét, jelöljön ki egy hatókörcímkét.

1. Válassza a **Tovább lehetőséget.**
1. Válassza **a Hozzárendelések hozzárendelése** > a kijelölt**csoportokhoz** > **Selected Groups**lehetőséget. A **Felvenni kívánt csoportok kiválasztása**csoportban válassza a Biztonságos **munkaállomások**lehetőséget.
1. Válassza a **Tovább lehetőséget.**
1. Válassza a **Létrehozás** gombot a profil létrehozásához. Az AutoPilot üzembehelyezési profil most már hozzárendelhető az eszközökhöz.

Az Autopilot ban való eszközregisztráció az eszköz típusától és szerepkörétől függően más felhasználói élményt biztosít. A központi telepítési példában bemutatjuk a modell, ahol a biztonságos eszközök tömegesen telepített és megosztható, de ha először használják, az eszköz egy felhasználóhoz van rendelve. További információ: [Intune Autopilot device enrollment](/intune/device-enrollment).

### <a name="configure-windows-update"></a>A Windows Update konfigurálása

A Windows 10 naprakészen tartása az egyik legfontosabb dolog, amit tehet. A Windows biztonságos állapotban tartásához egy frissítési gyűrűt kell telepítenie a frissítések munkaállomásokra alkalmazott ütemének kezeléséhez. 

Ez az útmutató azt javasolja, hogy hozzon létre egy új frissítési gyűrűt, és módosítsa a következő alapértelmezett beállításokat:

Az Azure Portalon:

1. Látogasson el a **Microsoft Intune** > **szoftverfrissítésére, a** > **Windows 10 Update Rings rendszerre.**
1. Adja meg a következőt:

   * Név – **Azure által felügyelt munkaállomás-frissítések**
   * Karbantartási csatorna - **Windows Insider - Gyors**
   * Minőségfrissítéshalasztás (nap) - **3**
   * Funkciófrissítési halasztási időszak (nap) - **3**
   * Automatikus frissítési viselkedés – **Automatikus telepítés és újraindítás végfelhasználói vezérlés nélkül**
   * A Windows-frissítések szüneteltetésének letiltása – **Blokk**
   * A felhasználó jóváhagyásának megkövetelése a munkaidőn kívüli újraindításhoz – **Kötelező**
   * A felhasználó újraindításának engedélyezése (aktív újraindítás) - **Kötelező**
   * Átmenet a felhasználók számára, hogy részt újraindítás után egy automatikus újraindítás (nap) - **3**
   * Szundi bekapcsolva újraindítás emlékeztető (nap) - **3**
   * Függőben lévő újraindítások (napok) beállítása - **3**

1. Kattintson a **Létrehozás** gombra.
1. A **Hozzárendelések** lapon adja hozzá a **Biztonságos munkaállomások** csoportot.

A Windows Update irányelveiről a [Házirend kript.](/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>A Windows Defender ATP Intune integrációja

A Windows Defender ATP és a Microsoft Intune együttműködik a biztonsági rések megelőzése érdekében. Korlátozhatják a jogsértések hatását is. Az ATP-képességek valós idejű fenyegetésészlelést biztosítanak, valamint lehetővé teszik a végponti eszközök kiterjedt naplózását és naplózását.

A Windows Defender ATP és az Intune integrációjának konfigurálásához nyissa meg az Azure Portalt.

1. Tallózással keresse meg a **Microsoft Intune-eszköz** > **megfelelősége** > Windows Defender**ATP**.
1. A Windows **Defender ATP konfigurálása**csoport **1.**
1. A Windows Defender biztonsági központban:

   1. Válassza a **Beállítások** > **speciális szolgáltatásai t**.
   1. **Microsoft Intune-kapcsolat**esetén válassza **a Be**lehetőséget.
   1. Válassza a **Beállítások mentése** lehetőséget.

1. A kapcsolat létrejötte után térjen vissza az Intune-ba, és a lap tetején válassza a **Frissítés** lehetőséget.
1. Adja meg a **10.0.15063 vagy újabb verziójú windowsos eszközök csatlakoztatása a következőhöz: Windows Defender ATP** beállításnál a **Be** értéket.
1. Kattintson a **Mentés** gombra.

További információt a [Windows Defender komplex veszélyforrások elleni védelem című témakörben talál.](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)

### <a name="finish-workstation-profile-hardening"></a>Munkaállomás-profil megerősítésének befejezése

A megoldás sikeres megerősítésének sikeres befejezéséhez töltse le és hajtsa végre a megfelelő parancsfájlt. Keresse meg a letöltési linkeket a kívánt **profil szinten:**

| Profil | Letöltési hely | Fájlnév |
| --- | --- | --- |
| Alacsony biztonság | N/A | N/A |
| Fokozott biztonság | https://aka.ms/securedworkstationgit | Továbbfejlesztett munkaállomás-Windows10-(1809).ps1 |
| Magas szintű biztonság | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializált | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline.ps1 |
| Speciális megfelelőség* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Biztosított | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*A specialized compliance egy parancsfájl, amely az NCSC Windows10 SecurityBaseline speciális konfigurációját kényszeríti ki.

A parancsfájl sikeres végrehajtása után frissítheti a profilokat és a házirendeket az Intune-ban. A továbbfejlesztett és a biztonságos profilok parancsfájljai házirendeket és profilokat hoznak létre, de a házirendet hozzá kell rendelnie a **Biztonságos munkaállomások** eszközcsoportjához.

* Itt található a parancsfájlok által létrehozott Intune-eszköz konfigurációs profiljai: **Azure portal** > **Microsoft Intune-eszköz** > **konfigurációs** > **profilok.**
* Itt található a parancsfájlok által létrehozott Intune-eszköz megfelelőségi szabályzata: **Azure Portal** > **Microsoft Intune-eszközmegfelelőségi** > **Device Compliance** > **szabályzatok.**

A parancsfájlok által végrehajtott módosítások ellenőrzéséhez exportálhatja a profilokat. Így további edzéseket is megállapíthat, amelyek a [SECCON dokumentációjában](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)vázoltak szerint szükségesek lehetnek.

Futtassa az Intune-adatok exportálási parancsfájlját `DeviceConfiguration_Export.ps1` a [DeviceConfiguration GiuHub tárházból](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) az összes aktuális Intune-profil exportálásához.

## <a name="additional-configurations-and-hardening-to-consider"></a>További konfigurációk és edzés, hogy fontolja meg

Az itt található útmutatást követve biztonságos munkaállomást telepített. Azonban érdemes további vezérlőket is figyelembe venni. Példa:

* hozzáférés korlátozása alternatív böngészőkre
* kimenő HTTP engedélyezése
* webhelyek kijelölésének letiltása
* egyéni PowerShell-parancsfájlok futtatásához szükséges engedélyek beállítása

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Szabályok beállítása a tűzfalkonfigurációs szolgáltatóban (CSP)

Szükség szerint további módosításokat végezhet a bejövő és a kimenő szabályok kezelésében az engedélyezett és a blokkolt végpontokhoz. Ahogy továbbra is megkeményíti a biztonságos munkaállomást, lazíthatja a korlátozást, amely megtagadja az összes bejövő és kimenő forgalmat. Hozzáadhat engedélyezett kimenő helyeket a közös és megbízható webhelyekhez. További információt a [Tűzfal konfigurációs szolgáltatása című témakörben talál.](/Windows/client-management/mdm/firewall-csp)

A korlátozó URL-forgalomkezelés a következőket tartalmazza:

* Minden bejövő forgalom megtagadása – A Biztonságos munkaállomás profil parancsfájlban állítható be.
* Az összes kimenő letagadni, kivéve a kiválasztott Azure- és Microsoft-szolgáltatásokat, beleértve az Azure Cloud Shellt, valamint az Azure Password Reset lehetővé teszi.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Ha részletesebb adatokat szeretne biztosítani a blokkolási szabályokhoz, akkor a Spamhaus projektre hivatkozhat, amely a [domain blokklistát (DBL)](https://www.spamhaus.org/dbl/)tartja fenn: egy jó forrás, amelyet speciális szabálykészletként használhat a webhelyek blokkolására.

### <a name="manage-local-applications"></a>Helyi alkalmazások kezelése

A biztonságos munkaállomás a helyi alkalmazások eltávolításakor valóban megerősített állapotba kerül, beleértve a hatékonyságnövelő alkalmazásokat is. Itt a Chrome-ot adja hozzá alapértelmezett böngészőként, és az Intune használatával korlátozhatja a felhasználó azon képességét, hogy módosítsa a böngészőt és annak beépülő moduljait.

#### <a name="deploy-applications-using-intune"></a>Alkalmazások telepítése az Intune használatával

Bizonyos esetekben olyan alkalmazásokra van szükség, mint a Google Chrome böngésző a biztonságos munkaállomáson. A következő példa a Chrome telepítését ismerteti a Biztonságos munkaállomások biztonsági csoport **eszközeire.**

1. Töltse le a Windows 64 bites offline telepítő [Chrome-csomagát.](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Bontsa ki a fájlokat, `GoogleChromeStandaloneEnterprise64.msi` és jegyezze fel a fájl helyét.
1. Az **Azure Portalon** keresse meg a **Microsoft Intune-alkalmazások** > **Client apps** > **hozzáadása** > **tallózással.**
1. Az **Alkalmazástípusa csoportban**válassza **az Üzletági vonal**lehetőséget.
1. Az **Alkalmazáscsomag-fájl csoportban**jelölje ki a `GoogleChromeStandaloneEnterprise64.msi` fájlt a kibontott helyről, és kattintson az OK **gombra.**
1. Az **Alkalmazásinformációk**csoportban adja meg a leírást és a közzétevőt. Válassza **az OK gombot.**
1. Válassza a **Hozzáadás** lehetőséget.
1. A **Hozzárendelések** lapon válassza a **Hozzárendeléstípusa**csoportban válassza **a Regisztrált eszközökszámára elérhető** lehetőséget.
1. A **Belefoglalt csoportok csoportban**adja hozzá a **Biztonságos munkaállomások** csoportot.
1. Válassza **az OK**gombot, majd a Mentés **gombot.**

A Chrome-beállítások konfigurálásáról a [Chrome-böngésző kezelése a Microsoft Intune-nal](https://support.google.com/chrome/a/answer/9102677)című témakörben olvashat bővebben.

#### <a name="configuring-the-company-portal-for-custom-apps"></a>A vállalati portál konfigurálása egyéni alkalmazásokhoz

Biztonságos módban az alkalmazás telepítése az Intune vállalati portáljára korlátozódik. A portál telepítéséhez azonban hozzá kell férni a Microsoft Store áruházhoz. A biztonságos megoldásban a vállalati portált offline módban teheti elérhetővé minden eszköz számára.

A [céges portál](/Intune/store-apps-company-portal-app) Intune által felügyelt példánya igény szerinti hozzáférést biztosít további eszközökhöz, amelyek leküldéses leküldéses leküldéses a biztonságos munkaállomások felhasználóihoz.

Előfordulhat, hogy telepítenie kell a Windows 32 bites alkalmazásokat vagy más olyan alkalmazásokat, amelyek központi telepítése speciális előkészületeket igényel. Ilyen esetekben a [Microsoft win32 tartalomelőkészítő eszköz](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) kész `.intunewin` formátumú formátumfájlt biztosít a telepítéshez.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Feltételes hozzáférés, amely csak biztonságos munkaállomás-hozzáférést biztosít az Azure Portalhoz

Az Azure AD lehetővé teszi az Azure felhőkezelési portálon elérhető szolgáltatások kezelését és korlátozását. A [feltételes hozzáférés](../conditional-access/overview.md) engedélyezése biztosítja, hogy csak a biztonságos munkaállomás tudja kezelni vagy módosítani az erőforrásokat. Alapvető fontosságú, hogy a szolgáltatás telepítése során fontolja meg, ha [a vészhelyzeti hozzáférési](../users-groups-roles/directory-emergency-access.md) funkció csak szélsőséges esetekben használható, és a fiók házirenden keresztül kezelt.

> [!NOTE]
> Létre kell hoznia egy felhasználói csoportot, és meg kell adnia a sürgősségi felhasználót, amely megkerülheti a feltételes hozzáférési házirendet. Példánkban van egy biztonsági csoport neve **Emergency BreakGlass**

1. Tallózással keresse meg az **Azure Portal** > **Microsoft Intune** > **feltételes hozzáférés – házirendek** > **új szabályzatát.**
1. Adja meg a házirend **nevét.**
1. **Felhasználó és csoportok** > kiválasztása**Felhasználók és csoportok kijelölése** 
1. Válassza a**Címtárszerepkörök** **felvétele** > jelölőnégyzetet > Válassza ki azokat a szerepköröket, > globális rendszergazda, kiemelt szerepkörcinát, kiemelt hitelesítési rendszergazda, biztonsági rendszergazda, megfelelőségi rendszergazda, feltételes hozzáférés-rendszergazda, alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, Intune-szolgáltatás-rendszergazda
1. Válassza **a Kizárás** > Felhasználók és **csoportok** kiválasztása > Válassza ki a kizárt felhasználók **kiválasztása** > Válassza ki a **Segélytörés-csoport** csoportját.
1. Válassza a **Felhőalapú alkalmazások at vagy műveleteket** > **Az összes felhőalkalmazás kijelölése**
1. Válassza ki a **feltételeket** > Válassza ki **az eszközplatformokat,** > válassza ki a konfigurálást, > **Válassza ki az Eszköz kiválasztása platformokat,** válassza ki a **Windows rendszert** **Yes**
1. Jelölje be a **Access-vezérlők >** Válassza ki **a Hozzáférési engedély** lehetőséget **Igen** > válassza a **Megfelelőként megjelölt eszköz megkövetelése**lehetőséget. 
1. A **Házirend** > engedélyezése**a**
 
Ez a házirend-készlet biztosítja, hogy a rendszergazdáknak az Intune és a WDATP által beállított megfelelő Windows-eszközt kell használniuk. 

A szervezeteknek meg kell fontolniuk az örökölt hitelesítési protokollok blokkolását a környezetükben. A feladat többféleképpen is elvégezhető, az örökölt hitelesítési protokollok blokkolásával kapcsolatos további információkért tekintse meg a Következő cikket: [Hogyan: Régebbi hitelesítés blokkolása az Azure AD-hez feltételes hozzáféréssel.](../conditional-access/block-legacy-authentication.md)

### <a name="use-powershell-to-create-custom-settings"></a>Egyéni beállítások létrehozása a PowerShell használatával

A PowerShell segítségével is bővítheti a gazdagép felügyeleti képességeit. Ez a példaparancsfájl alapértelmezett hátteret állít be az állomáson. Ez egy olyan képesség, amely az Azure Portalon és a profilokon keresztül is elérhető. A példaparancsfájl csak a PowerShell-funkciók szemléltetésére szolgál.

Előfordulhat, hogy be kell állítania néhány egyéni vezérlőt és beállítást a biztonságos munkaállomásokon. Ez a példa módosítja a munkaállomás hátterét a PowerShell azon képességének használatával, hogy könnyen azonosítsa az eszközt használatra kész, biztonságos munkaállomásként.

A Microsoft Scripting Center [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) parancsfájlja lehetővé teszi a Windows számára, hogy ezt az [ingyenes, általános háttérképet](https://i.imgur.com/OAJ28zO.png) indításkor betöltse.

1. Töltse le a parancsfájlt egy helyi eszközre.
1. Frissítse az ügyfélXXXX és a letöltési helyét a háttérképet. A példánkban az ügyfélXXXX-t háttérre cseréljük.
1. Tallózással keresse meg az **Azure PortalOn** > **A Microsoft Intune-eszköz** > **konfigurációs** > **PowerShell-parancsfájlok** > **hozzáadása**.
1. Adja meg a parancsfájl **nevét,** és adja meg a **parancsfájl helyét**.
1. Válassza a **Konfigurálás** lehetőséget.
   1. Állítsa be **A parancsfájl futtatása a bejelentkezett hitelesítő adatokkal** **beállítással igen**beállításra.
   1. Válassza **az OK gombot.**
1. Kattintson a **Létrehozás** gombra.
1. **Hozzárendelések:** > **Csoportok kiválasztása.**
   1. Adja hozzá a **Biztonságos munkaállomások**biztonsági csoportot.
   1. Kattintson a **Mentés** gombra.

## <a name="enroll-and-validate-your-first-device"></a>Az első eszköz regisztrálása és érvényesítése

1. Az eszköz regisztrálásához a következő adatokra van szükség:
   * **Sorozatszám** - megtalálható a készülék alvázán.
   * **Windows termékazonosító** – a Windows Beállítások **menürendszer** > **– beállítási** parancsa alatt található.
   * A [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) futtatásával lejuthat egy CSV-kivonatfájlra, amely tartalmazza az eszközregisztrációhoz szükséges összes információt.
   
     Futtassa `Get-WindowsAutoPilotInfo – outputfile device1.csv` az adatok ncv-fájlként történő kimenetéhez, amelyet importálhat az Intune-ba.

     > [!NOTE]
     > A parancsfájl emelt szintű jogokat igényel. Ez fut a távoli aláírt. A `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` parancs lehetővé teszi a parancsfájl megfelelő futtatását.

   * Ezeket az információkat a Windows 10 1809-es vagy újabb verziójába bejelentkezve gyűjtheti össze. A hardverviszonteladó is meg tudja adni ezt az információt.
1. Az **Azure Portalon**nyissa meg a Microsoft > **Intune-eszközigénylési** > **Windows-igénylési** > **eszközök – A Windows Autopilot-eszközök kezelése**című lehetőséget. **Microsoft Intune**
1. Válassza **az Importálás** lehetőséget, és válassza ki a CSV-fájlt.
1. Adja hozzá az eszközt a **Biztonságos munkaállomások** biztonsági csoportjához.
1. A konfigurálni kívánt Windows 10-eszközön nyissa meg a **Windows beállítások** > **frissítése & biztonsági** > **helyreállítás című webhelyet.**
   1. Válassza **az Első lépések** lehetőséget a Számítógép **alaphelyzetbe állítása csoportban.**
   1. Kövesse az utasításokat az eszköz alaphelyzetbe állításához és újrakonfigurálásához a profil- és megfelelőségi házirendek konfigurálásával.

Az eszköz konfigurálása után végezze el az ellenőrzést, és ellenőrizze a konfigurációt. Ellenőrizze, hogy az első eszköz megfelelően van-e konfigurálva a központi telepítés folytatása előtt.

## <a name="assign-devices"></a>Eszközök hozzárendelése

Eszközök és felhasználók hozzárendeléséhez le kell képeznie a [kijelölt profilokat](/intune/device-profile-assign) a biztonsági csoporthoz. Minden új felhasználót, akinek engedélye van a szolgáltatáshoz, hozzá kell adni a biztonsági csoporthoz is.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>A Sentinel és a Windows Defender ATP használata a biztonsági incidensek figyelésére és az azokra való reagálásra

A biztonságos munkaállomás-telepítés figyelése a [Sentinel] engedélyezésével és [a fenyegetés- és sebezhetőség kezelésének](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) kihasználásával valósítható meg.

Az **Azure Sentinel** segítségével: 

* Adatok gyűjtése az Intune-ból, az Azure Portalról és az Azure AD-ről a felhasználók és eszközök figyeléséhez
* Segítség a gyanús felhasználói és eszközkonfigurációs tevékenységek vizsgálatához
* És hajt a képességét, hogy vizsgálja meg a biztonsági vizsgálatok segítségével WDATP

A Sentinel figyelése megköveteli, hogy az adatforrások, például az Azure AD összekötők beállítása.

1. Az **Azure Portalon**nyissa meg az **Azure Sentinel (előzetes verzió)** > válassza a **Hozzáadás lehetőséget**
1. A Válassza ki a **munkaterületet hozzáadni az Azure Sentinel** Válassza **hozzon létre egy új munkaterületet**
1. Adja meg a következőt:
   * **Log Analytics-munkaterület** – "Biztonságos munkaállomás-figyelés"
   * **Előfizetés** – Aktív előfizetés kiválasztása
   * **Erőforráscsoport** – válassza ki az Új** > Biztonságos munkaállomás RG > **Ok**
   * **Hely** – Válassza ki azt a földrajzilag leginkább alkalmas helyet, amely megfelel a telepítésnek.
   * **Árszint** – **GB-onkénti kiválasztás (2018)**
1. Válassza az **Ok gombot.**

Ezután csatlakoztatjuk a rendelkezésre álló biztonságos munkaállomás-adatforrásokat a figyeléshez.

1. Az **Azure Portalon**nyissa meg az **Azure Sentinel munkaterületet** > válassza ki a **biztonságos munkaállomás figyelési** munkaterületet.
1. **Adatösszekötők kijelölése**
1. Válassza **az Azure Active Directory** > Open Connector Page > az előfeltétel áttekintése után. Folytassa a konfigurációt, és válassza a **Csatlakozás** lehetőséget az Azure AD bejelentkezési naplókhoz, valamint az Azure AD naplózási naplókhoz.
1. Válassza az **Azure-tevékenység** > Összekötő lap megnyitása > az előfeltétel áttekintése után. Folytassa az Azure-tevékenységnaplók konfigurálása > az előfizetés kiválasztása > válassza ki a **csatlakozást**

A Sentinel által gyűjtött adatok nak köszönhetően az **Azure Portal**kiválasztásával megfigyelheti a tevékenységeket, nyissa meg az Azure Sentinel **áttekintése című témakört.** 

A **Windows Defender ATP (WDATP)** segítségével:

* A biztonsági rések és a helytelen konfigurációk folyamatos megfigyelése és figyelése
* Használja a WDATP-t a dinamikus fenyegetések rangsorolására a vadonban
* A biztonsági rések korrelációjának meghajtása végpontészlelési és -válaszriasztásokkal
* Az irányítópult használata a gépszintű biztonsági rés azonosítására a vizsgálatok során
* Kiküldése az Intune-ba

Konfigurálja a [Defender ATP irányítópultját](https://securitycenter.windows.com/machines). Útmutató a [Threat & a biztonsági réskezelésének irányítópultján – áttekintés.](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights)

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Alkalmazástevékenység figyelése a Microsoft Monitoring Agent (MMA) használatával
A Specialized munkaállomástól kezdve az alkalmazástároló engedélyezve van az alkalmazástevékenység figyelésére egy munkaállomáson. Ahhoz, hogy a figyelés integrálható legyen a Log Analytics-munkaterületbe, egy MMA-ügynököt és konfigurációt kell követnie. 

> [!NOTE]
> A Specialized munkaállomás-profil tartalmazza az AppLocker figyelési szabályzatait. A házirendek telepítése szükséges az ügyfélalkalmazás-tevékenység figyeléséhez

Az MMA-ügynök telepítése Az Intune PowerShell-parancsfájllal

1. Töltse le a [telepítőparancsfájlt egy helyi eszközre.](https://aka.ms/securedworkstationgit)
1. A paraméterek **frissítése, $WorkSpaceID** és **$WorkSpaceKey**
1. Tallózással keresse meg az **Azure PortalOn** > **A Microsoft Intune-eszköz** > **konfigurációs** > **PowerShell-parancsfájlok** > **hozzáadása**.
1. Adja meg a parancsfájl **nevét,** és adja meg a **parancsfájl helyét**.
1. Válassza a **Konfigurálás** lehetőséget.
   1. Állítsa be **A parancsfájl futtatása a bejelentkezett hitelesítő adatokkal** **beállítással igen**beállításra.
   1. Válassza **az OK gombot.**
1. Kattintson a **Létrehozás** gombra.
1. **Hozzárendelések:** > **Csoportok kiválasztása.**
   1. Adja hozzá a **Biztonságos munkaállomások**biztonsági csoportot.
   1. Kattintson a **Mentés** gombra.

Ezután be kell állítania a Log Analytics szolgáltatást az új naplók fogadásához
1. Az **Azure Portalon**nyissa meg a **Log Analytics-munkaterület** > Select – "Biztonságos munkaállomás-figyelés" című részt.
1. Válassza a **Speciális beállítások** > **– Adatok A** > **Windows eseménynaplóinak kiválasztása**
1. Az **Események gyűjtése a következő eseménynaplókból** 
1. Adja meg a következőt:
   * "Microsoft-Windows-AppLocker/EXE és DLL" > Az **információs** kijelölés megszüntetése
   * "Microsoft-Windows-AppLocker/MSI and Script" > Az **információs** kijelölés megszüntetése
   * "Microsoft-Windows-AppLocker/Packaged app-Deployment" > Az **információs** elem kijelölését
   * "Microsoft-Windows-AppLocker/Packaged app-execution" > Unselect **Információs**
1. **Mentés** kiválasztása

Az alkalmazásnaplózás elérhető lesz a kiválasztott Log Analytics-munkaterületen.

## <a name="monitoring"></a>Figyelés

* Ismerje meg, hogyan észlelheti a [fenyegetéseket az Azure Sentinel segítségével](/azure/sentinel/tutorial-detect-threats)
* [Incidensek kivizsgálása az Azure Sentinellel](/azure/sentinel/tutorial-investigate-cases)
* [Automatikus fenyegetésre adott válaszok beállítása az Azure Sentinelben](/azure/sentinel/tutorial-respond-threats-playbook)
* Az [expozíciós pontszám áttekintése](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* [Biztonsági javaslat áttekintése](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Biztonsági [szervizelések](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) kezelése
* [Végpontészlelés és -válasz](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response) kezelése
* Az [Intune-profilfigyeléssel](/intune/device-profile-monitor)rendelkező profilok figyelése .

## <a name="next-steps"></a>További lépések

* További információ a [Microsoft Intune-ról.](/intune/index)
* Ismerje meg [az Azure AD szolgáltatást.](../index.yml)
* A [Microsoft Defender komplex veszélyforrások elleni védelemmel való kapcsolat](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Fedezze fel [az Azure Sentinelt](/azure/sentinel/)
