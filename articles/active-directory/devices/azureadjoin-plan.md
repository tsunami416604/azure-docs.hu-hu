---
title: Az Azure Active Directory (Azure AD) való csatlakozás megvalósítás tervezése |} A Microsoft Docs
description: Ismerteti a lépéseket, amelyekre szükség van a megvalósítása az Azure AD-hez csatlakoztatott eszközök a környezetben.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a299c5cf59a3c5b7a994303c9d85783ecfbb424
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201890"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Útmutató: Az Azure AD-csatlakozás implementálásának megtervezése


Az Azure AD join eszközök csatlakoztatása közvetlenül az Azure ad-ben csatlakoztatása a helyszíni Active Directory, miközben gondoskodik a felhasználók számára a hatékony és biztonságos nélkül teszi lehetővé. Az Azure AD join a nagyvállalatok igényeire felkészített ipari méretekben és a hatókörrel rendelkező üzemelő példányok esetében.   

Ez a cikk az Azure AD join implementáció megtervezésébe a szükséges információkat biztosít.

 
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a [bemutatása az Eszközfelügyelet az Azure Active Directory](../device-management-introduction.md).



## <a name="plan-your-implementation"></a>A megvalósítás tervezése

A hibrid Azure AD-megvalósítás megtervezése, meg kell ismerkednie az:

|   |   |
|---|---|
|![Jelölőnégyzet][1]|A forgatókönyvek áttekintése|
|![Jelölőnégyzet][1]|Tekintse át a személyazonosság-infrastruktúra|
|![Jelölőnégyzet][1]|Az Eszközfelügyelet felmérése|
|![Jelölőnégyzet][1]|Megismerheti az alkalmazások és erőforrások szempontjai|
|![Jelölőnégyzet][1]|Az üzembe helyezési lehetőségek ismertetése|
|![Jelölőnégyzet][1]|Konfigurálja a vállalati állapothordozás|
|![Jelölőnégyzet][1]|Feltételes hozzáférés konfigurálása|







## <a name="review-your-scenarios"></a>A forgatókönyvek áttekintése 

Hibrid Azure AD-csatlakozás lehet, hogy az egyes elsődleges forgatókönyvek, amíg az Azure AD-csatlakozás lehetővé teszi a Windows-és felhőközpontú modell felé történő átmenet. Ha azt tervezi, korszerűsítheti az eszközök felügyelete, és csökkentheti informatikai költségeit eszközzel kapcsolatos, az Azure AD-csatlakozás kiindulási alapként nagyszerű célok elérése érdekében.  

 
Az Azure AD-csatlakozás kell figyelembe vennie, ha az Ön céljaihoz igazítása a következő feltételek:

- A felhasználók vannak bevezetése a Microsoft 365, a hatékonyságnövelő programcsomagot.

- Azt szeretné, hogy eszközöket kezeljen az egy felhőalapú Eszközkezelési megoldás.

- Szeretné egyszerűsíteni a földrajzilag elosztott felhasználók eszközkiépítési.

- Azt tervezi, hogy az alkalmazás-infrastruktúra modernizálása.
 

 

## <a name="review-your-identity-infrastructure"></a>Tekintse át a személyazonosság-infrastruktúra  

Az Azure AD join, felügyelt és összevont környezetben is működik.  


### <a name="managed-environment"></a>Felügyelt környezetben

Lehet felügyelt környezetben keresztül telepített [Jelszókivonat-szinkronizálás](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) vagy [haladnak keresztül hitelesítést](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) a zökkenőmentes egyszeri bejelentkezést.

Ezekben az esetekben nincs szükség, hogy konfiguráljon egy összevonási kiszolgálót a hitelesítéshez.


### <a name="federated-environment"></a>Összevont környezetben

Egy összevont környezetben, amely támogatja a WS-Trust, mind a WS-Fed protokollok Identitásszolgáltatóként kell rendelkeznie:

- **WS-Fed:** Eszköz csatlakoztatása az Azure ad-ben Ez a protokoll szükséges.

- **WS-Trust:** Ez a protokoll megadása kötelező bejelentkezni egy az Azure AD-csatlakoztatott eszközön. 

Ha az identitásszolgáltató nem támogatja ezeket a protokollokat, az Azure AD-csatlakozás nem natív módon működik. A Windows 10-es 1809 verziótól kezdve a felhasználók bejelentkezhet egy Azure AD-csatlakoztatott eszközre a SAML-alapú identitásszolgáltatóval keresztül [jelentkezzen be a Windows 10-es webalkalmazás](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Webes bejelentkezés jelenleg csak előzetes funkció.


### <a name="smartcards-and-certificate-based-authentication"></a>Intelligens kártyák és a Tanúsítványalapú hitelesítés

Intelligens kártyák vagy Tanúsítványalapú hitelesítés nem használható eszközök csatlakoztatása az Azure ad-ben. Intelligens kártyák azonban használható az Azure AD-csatlakoztatott eszközök bejelentkezni, ha az AD FS konfigurálva van.

**Javaslat:** Alkalmazzon Windows Hello for Business erős, a jelszó nélküli hitelesítés Windows 10 rendszerű eszközökre.


### <a name="user-configuration"></a>Felhasználó konfigurációja

Ha hoz létre, hogy a felhasználók a:

- **A helyszíni Active Directory**, szinkronizálja őket az Azure AD-t kell [az Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 

- **Az Azure AD**, nincs további beállítást nem szükséges.

A helyszíni UPN-EK, amelyek nem azonosak az Azure AD UPN-EK nem támogatottak az Azure AD-csatlakoztatott eszközökön. Ha a felhasználók egy a helyszíni egyszerű Felhasználónévvel, meg kell terveznie elsődleges Felhasználónevük átvált az Azure ad-ben.



## <a name="assess-your-device-management"></a>Az Eszközfelügyelet felmérése

### <a name="supported-devices"></a>Támogatott eszközök

Az Azure AD join:

- Ez a tulajdonság csak a Windows 10-eszközökre vonatkozik. 

- Nem alkalmazható a Windows- vagy más operációs rendszer korábbi verzióiban. Ha Windows 7 vagy 8.1 rendszerű eszközökön, frissítenie kell az Azure AD-csatlakozás telepítése Windows 10-re.

- FIPS-módban TPM Modullal rendelkező eszközökön nem támogatott.
 
**Javaslat:** Mindig használja a legújabb Windows 10-es kiadásra frissített funkciókat.


### <a name="management-platform"></a>Felügyeleti platform

Az Azure AD-csatlakoztatott eszközök kezelési alapján egy MDM-platformmal, például az Intune) és a mobileszköz-kezelési CSP-k. Windows 10-es rendelkezik egy beépített MDM-ügynököt, amely az összes kompatibilis MDM-megoldás működik.

Kétféleképpen kezelése az Azure AD-hez csatlakoztatott eszközök esetében:

- **Csak a mobileszköz-kezelési** -eszköz kizárólag egy mobileszköz-kezelési szolgáltató, például az Intune által felügyelt. Minden szabályzat az MDM-regisztrációs folyamat részeként érkeznek. Az Azure AD Premium vagy EMS-ügyfelek az MDM-regisztráció egy automatikus lépés, amely egy Azure AD-csatlakozás része.

- **Megosztott kezelés** – az MDM-szolgáltató és az SCCM által kezelt eszköz. Ebben a megközelítésben az SCCM-ügynök telepítve van egy MDM által felügyelt eszközök felügyeletéhez bizonyos szempontokat.

Mivel az Azure AD-hez eszközök nem csatlakoznak a helyszíni Active Directory, a csoport szabályzatok nem támogatottak.


Csoportházirendek használja, ha a mobileszköz-kezelési házirend paritásos kiértékelése használatával a [MDM áttelepítési elemző eszköz (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Tekintse át a támogatott és nem támogatott szabályzatok meghatározni, hogy MDM-megoldás csoportházirendek helyett használhatja. Nem támogatott szabályzatok vegye figyelembe a következőket:

- A nem támogatott szabályzatok van szükség, az Azure AD-hez, eszközöket vagy felhasználókat?

- Azok a nem támogatott szabályzatok alkalmazható a felhőben üzembe helyezési driven?

Ha az MDM-megoldáson keresztül az Azure AD-alkalmazásgyűjtemény nem érhető el, hozzáadhatja azt a leírtak szerint [Azure Active Directory-integráció MDM-mel](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Megosztott kezelés – SCCM segítségével felügyelheti az eszközöket a bizonyos elemeinek keresztül az MDM-platformmal kézbesítési házirendek. A Microsoft Intune lehetővé teszi, hogy a megosztott kezelési az SCCM. További információkért lásd: [megosztott kezelés Windows 10-es eszközök](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Ha használ egy kivételével az Intune mobileszköz-kezelési termékkel, ellenőrizni kell a megfelelő közös felügyeleti forgatókönyvek MDM-szolgáltatónál.

**Javaslat:** Fontolja meg a mobileszköz-kezelési, csak a webszolgáltatások az Azure AD-hez csatlakoztatott eszközök.



## <a name="understand-considerations-for-applications-and-resources"></a>Megismerheti az alkalmazások és erőforrások szempontjai

Azt javasoljuk, hogy a migrálás a helyszíni alkalmazások felhőbe jobb felhasználó élményt és hozzáférés-vezérlés. Azonban az Azure AD-hez eszközök zökkenőmentes hozzáférést biztosíthat a, mind a helyszíni és felhőalapú alkalmazásokhoz. További információkért lásd: [hogyan a helyszíni erőforrások működik az Azure AD SSO-hez csatlakoztatott eszközök](azuread-join-sso.md).

Az alábbi szakaszok tartalmazzák a különböző típusú alkalmazások és erőforrások szempontjai.

### <a name="cloud-based-applications"></a>Felhőalapú alkalmazások

Egy alkalmazás az Azure AD-alkalmazásgyűjtemény ad hozzá, ha a felhasználók egyszeri Bejelentkezéssel keresztül az Azure AD-csatlakoztatott eszközök kapnak. További konfiguráció nélkül nem szükséges. Egyszeri bejelentkezés a Microsoft Edge és a Chrome böngészők is, a felhasználók kapnak. A Chrome, üzembe kell helyeznie a [Windows 10-es fiókok bővítmény](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Win32-alkalmazások, amelyek:

- A webes fiók Manager (WAM) alapulnak, jogkivonat-kérelmeket egyszeri Bejelentkezést is szerezni az Azure AD-csatlakoztatott eszközök esetében. 

- Ne használjon WAM előfordulhat, hogy kérése a felhasználóktól a hitelesítéshez. 


### <a name="on-premises-web-applications"></a>A helyszíni webalkalmazások

Ha az alkalmazások egyéni beépített és/vagy a helyszínen üzemeltetett kell azokat a megbízható helyek a böngésző hozzá:

- Integrált Windows-hitelesítés működjön engedélyezése 
- Nem-kérés egyszeri bejelentkezési felhasználói élményt nyújtson a felhasználók. 

Ha az AD FS használja, lásd: [ellenőrzése és felügyelete egyszeri bejelentkezéssel az AD FS-sel](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Javaslat:** Vegye figyelembe a felhőben (például Azure) üzemeltetési és integrálása az Azure AD-hez jobb felhasználói élményt.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Örökölt protokollok hagyatkoznia a helyszíni alkalmazások

Ha az eszköz egy olyan tartományvezérlőre, amely hozzáféréssel rendelkezik felhasználók get egyszeri bejelentkezés az Azure AD-hez csatlakoztatott eszközök. 

**Javaslat:** Üzembe helyezése [Azure AD alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ahhoz, hogy biztonságos hozzáférést ezekhez az alkalmazásokhoz.


### <a name="on-premises-network-shares"></a>A helyszíni hálózati megosztások

A felhasználók rendelkeznek az Azure AD-csatlakoztatott eszközök egyszeri bejelentkezés, ha egy eszköz egy helyszíni tartományvezérlővel hozzáféréssel rendelkezik.

### <a name="printers"></a>Nyomtatók

A nyomtatók, üzembe kell helyeznie [hibrid felhő nyomtatási](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) nyomtatók felderítéséhez használt Azure ad-hez csatlakoztatott eszközök esetében. 

Nyomtatók nem található meg a csak felhőalapú környezetekben, miközben a felhasználók is használhatja a nyomtatók UNC elérési út közvetlenül adja hozzá őket. 


### <a name="on-premises-applications-relying-on-machine-authentication"></a>A helyi számítógép-hitelesítés a függő alkalmazásokat

Az Azure AD-csatlakoztatott eszközök nem támogatják a helyszíni alkalmazások hagyatkoznia a számítógép-hitelesítés. 

**Javaslat:** Fontolja meg ezek az alkalmazások kivonása és a modern alternatívák áthelyezése.

### <a name="remote-desktop-services"></a>Távoli asztali szolgáltatások

Az Azure AD-csatlakoztatott eszközök távoli asztali kapcsolatot igényel a gazdagépen vagy az Azure AD-hez csatlakoztatott kell vagy hibrid Azure AD-hez. A távoli asztal nem csatlakozó vagy nem Windows-eszközről nem támogatott. További információkért lásd: [távoli az Azure ad Connect csatlakozó számítógépek](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)


## <a name="understand-your-provisioning-options"></a>Az üzembe helyezési lehetőségek ismertetése

Az Azure AD-csatlakozás a következő módszerekkel helyezheti üzembe a:

- **Önkiszolgáló OOBE/beállításaiban** - módban az önkiszolgáló felhasználók go keresztül az Azure AD-csatlakozás feldolgozása vagy során Windows Out kezdőélmény (OOBE) vagy a Windows beállításait. További információkért lásd: [a munkahelyi eszköz csatlakoztatása a szervezet hálózati](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 

- **Windows Autopilot** – Windows Autopilot lehetővé teszi, hogy az üzem előtti konfigurációs eszközök OOBE egy Azure AD-csatlakozás végrehajtásához az egyenletesebb biztosítása érdekében. További információkért lásd: a [áttekintése a Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 

- **Csoportos regisztrálás** -tömeges beléptetéssel-driven rendszergazda az Azure AD-csatlakozás a tömeges kiépítési eszköz használatával konfigurálhatja az eszközöket. További információkért lásd: [Windows-eszközök csoportos regisztrálása](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
 


A következő három módszer összehasonlítása 

 
||A telepítő önkiszolgáló|Windows Autopilot|Tömeges regisztrálás|
|---|---|---|---|
|Állítsa be a felhasználói műveletet igényel|Igen|Igen|Nem|
|Informatikai erőfeszítésekre van szükség|Nem|Igen|Igen|
|Alkalmazható folyamatok|Kezdőélmény & beállításai|Csak a Kezdőélmény|Csak a Kezdőélmény|
|Helyi rendszergazdai jogosultság szükséges az elsődleges felhasználó|Igen, alapértelmezés szerint|Konfigurálható|Nem|
|Eszköz OEM-támogatásra van szüksége|Nem|Igen|Nem|
|Támogatott verziók|1511+|1709+|1703+|
 
Válassza ki a központi telepítési módszer vagy módszerek áttekintése a fenti táblázatban, és mindkét módszerrel bevezetésének az alábbi szempontokat megtekintésével:  

- Azok a felhasználók műszaki lelkes haladhat végig a telepítő maguknak? 

    - Önkiszolgáló is működik ezeknek a felhasználóknak ajánlott. Fontolja meg a Windows Autopilot a felhasználói élmény fokozása érdekében.  

- Azok a felhasználók a távoli vagy helyi vállalati belül? 

    - Önkiszolgáló vagy a távoli felhasználók számára zökkenőmentes telepítéshez ajánlott Autopilot munka. 
 
- Részesíti előnyben a felhasználó közreműködésével vagy egy rendszergazda által felügyelt konfigurációt? 

    - Csoportos regisztrálás üzembe helyezés beállítása az eszközökön a felhasználók átadása előtt készített felügyeleti jobban működik.     

- Tegye vásárolhatja meg eszközök 1-2 számítógépgyártók (OEM), vagy rendelkezik egy széles terjesztési eszköz OEM?  

    - Ha vásárol a korlátozott OEM-ek is támogató Autopilot, szorosabban együttműködnek az Autopilot révén kihasználhatja. 
 

## <a name="configure-your-device-settings"></a>Az eszközök beállításainak konfigurálása

Az Azure portal lehetővé teszi, hogy szabályozható az üzembe helyezés az Azure AD-hez csatlakoztatott eszközök a szervezetben. A kapcsolódó beállítások konfigurálása a **Azure Active Directory oldalon**válassza `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>A felhasználók eszközöket csatlakoztathatnak az Azure AD-hez

Ezt a beállítást **összes** vagy **kijelölt** a telepítés hatóköre alapján, és akik számára engedélyezni állíthatja be az Azure AD a csatlakoztatott eszköz. 

![A felhasználók eszközöket csatlakoztathatnak az Azure AD-hez](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>További helyi rendszergazdák az Azure AD-tartományhoz csatlakozott eszközökön

Válasszon **kijelölt** , és kiválasztja a minden Azure AD-csatlakoztatott eszközök a helyi rendszergazdák csoporthoz hozzáadni kívánt felhasználókat. 

![További helyi rendszergazdák az Azure AD-tartományhoz csatlakozott eszközökön](./media/azureadjoin-plan/02.png)


### <a name="require-multi-factor-auth-to-join-devices"></a>Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése

Válassza ki **"Igen** ha közben az eszköz csatlakoztatása az Azure AD MFA végrehajtásához felhasználók van szüksége. A felhasználók, eszközök csatlakoztatása az Azure AD MFA használatával a magán az eszközön 2nd factor válik.

![Eszközök csatlakoztatása a többtényezős hitelesítés megkövetelése](./media/azureadjoin-plan/03.png)




## <a name="configure-your-mobility-settings"></a>A mobilitási beállításainak konfigurálása

A mobilitási beállítások konfigurálása előtt először adjon hozzá egy MDM-szolgáltató lehet.

**Egy MDM-szolgáltató hozzáadása az**:

1. Az a **Azure Active Directory oldalon**, a a **kezelés** területén kattintson `Mobility (MDM and MAM)`. 

2. Kattintson a **alkalmazás hozzáadása**.

3. Válassza ki az MDM-szolgáltatót a listából.

    ![Alkalmazás hozzáadása](./media/azureadjoin-plan/04.png)

Válassza ki az MDM-szolgáltatóban adja meg a kapcsolódó beállításokat. 

### <a name="mdm-user-scope"></a>MDM-felhasználói hatókör

Válassza ki **néhány** vagy **összes** a telepítés hatóköre alapján. 

![MDM-felhasználói hatókör](./media/azureadjoin-plan/05.png)

A hatókör alapján, az alábbiak történnek az alábbi lehetőségek közül: 

- **Felhasználó szerepel a mobileszköz-kezelési hatókör**: Ha az Azure AD Premium előfizetéssel rendelkezik, és az Azure AD-csatlakozás automatizált MDM-regisztráció. Az összes hatókörön belüli felhasználók rendelkeznie kell a megfelelő licenccel a mobileszköz-kezelést. MDM-regisztráció ebben a forgatókönyvben sikertelen lesz, ha az Azure AD-csatlakozás is vissza lesz állítva.
    
- **Felhasználó nem szerepel a mobileszköz-kezelési hatókör**: Ha a felhasználók nem szerepelnek a mobileszköz-kezelési hatókör, az Azure AD-csatlakozás semmilyen MDM-regisztráció nélkül befejeződött. Az eredmény egy nem felügyelt eszközt.


### <a name="mdm-urls"></a>MDM URL-címek

Nincsenek három URL-címek, amelyek kapcsolódnak az MDM-konfiguráció:

- MDM használati feltételeinek URL-címe

- MDM-felderítési URL-cím 

- MDM megfelelőségi URL-címe


![Alkalmazás hozzáadása](./media/azureadjoin-plan/06.png)


Minden egyes URL-címnek egy előre meghatározott alapértelmezett értéket. Ha ezek a mezők üresek, lépjen kapcsolatba az MDM-szolgáltató további információt.

### <a name="mam-settings"></a>MAM-beállításai

Az Azure AD-csatlakozás a MAM nem vonatkozik. 


## <a name="configure-enterprise-state-roaming"></a>Konfigurálja a vállalati állapothordozás

Ha szeretné engedélyezni, állapothordozás az Azure ad-hez, hogy a felhasználók a beállítások szinkronizálhatja különböző eszközökön, lásd: [engedélyezése vállalati állapot központi az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable). 

**Az ajánlás**: Engedélyezze ezt a beállítást akkor is a hibrid Azure AD-csatlakoztatott eszközök.


## <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Ha rendelkezik egy MDM-szolgáltató konfigurált az Azure AD-hez csatlakoztatott eszközök, a szolgáltató megőrzendő tartalomként jelöli meg az eszköz akkor megfelelő, amint az eszköz felügyelet alatt áll. 

![Megfelelő eszköz](./media/azureadjoin-plan/46.png)

Használhatja ezt a megvalósítást, [feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés szükséges a felügyelt eszközök](../conditional-access/require-managed-devices.md).




## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure ad-vel az első futás közben új Windows 10-es eszköz csatlakoztatása](azuread-joined-devices-frx.md)
> [a munkahelyi eszköz csatlakoztatása a vállalati hálózaton](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)


<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
