---
title: Az Azure Active Directory-csatlakozási megvalósítás megtervezése
description: A cikk ismerteti az Azure AD-hez csatlakozott eszközök környezetben való megvalósításához szükséges lépéseket.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bbecf0e365ba7a8424da775245181fa64c21f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672696"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Útmutató: Az Azure AD-csatlakozási megvalósítás megtervezése

Az Azure AD-csatlakozás lehetővé teszi, hogy közvetlenül csatlakozzon az Azure AD-hez anélkül, hogy csatlakoznia kellene a helyszíni Active Directoryhoz, miközben a felhasználók termelékenyek és biztonságosak maradnak. Az Azure AD-csatlakozás nagyvállalati használatra kész mind a nagyléptékű, mind a hatókörrel telepített központi telepítésekhez.   

Ez a cikk az Azure AD-csatlakozási megvalósítás megtervezéséhez szükséges információkat tartalmazza.
 
## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri az [Azure Active Directory eszközkezelés – bevezetés](../device-management-introduction.md)című dokumentumát.

## <a name="plan-your-implementation"></a>Tervezze meg a megvalósítást

Az Azure AD-csatlakozási megvalósítás megtervezéséhez ismerkedjen meg a következőkkel:

|   |   |
|---|---|
|![Jelölőnégyzet][1]|A forgatókönyvek áttekintése|
|![Jelölőnégyzet][1]|Tekintse át az identitás-infrastruktúrát|
|![Jelölőnégyzet][1]|Az eszközkezelés felmérése|
|![Jelölőnégyzet][1]|Az alkalmazásokra és az erőforrásokra vonatkozó szempontok ismertetése|
|![Jelölőnégyzet][1]|A kiépítési lehetőségek megismerése|
|![Jelölőnégyzet][1]|Vállalati állapotroaming konfigurálása|
|![Jelölőnégyzet][1]|Feltételes hozzáférés konfigurálása|

## <a name="review-your-scenarios"></a>A forgatókönyvek áttekintése 

Míg a hibrid Azure AD-csatlakozás előnyös lehet bizonyos forgatókönyvek, az Azure AD-csatlakozás lehetővé teszi, hogy áttérhet a felhő-első modell a Windows. Ha az eszközök felügyeletének modernizálását és az eszközökkel kapcsolatos informatikai költségek csökkentését tervezi, az Azure AD-csatlakozás nagyszerű alapot nyújt e célok eléréséhez.  
 
Ha a célok megfelelnek az alábbi feltételeknek, érdemes figyelembe venni az Azure AD-csatlakozást:

- A Microsoft 365-öt alkalmazza a felhasználók termelékenységi csomagjaként.
- Az eszközöket felhőalapú eszközfelügyeleti megoldással szeretné kezelni.
- Egyszerűsíteni szeretné az eszközkiépítést a földrajzilag elosztott felhasználók számára.
- Azt tervezi, hogy modernizálja az alkalmazás-infrastruktúra.

## <a name="review-your-identity-infrastructure"></a>Tekintse át az identitás-infrastruktúrát  

Az Azure AD-illesztés egyaránt, felügyelt és összevont környezetekkel működik.  

### <a name="managed-environment"></a>Felügyelt környezet

A felügyelt környezet [jelszókivonat-szinkronizálással](/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) vagy zökkenőmentes egyszeri bejelentkezéssel [történő áthaladási hitelesítéssel](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) telepíthető.

Ezek a forgatókönyvek nem igénylik az összevonási kiszolgáló hitelesítésre való konfigurálását.

### <a name="federated-environment"></a>Összevont környezet

Az összevont környezetnek rendelkeznie kell olyan identitásszolgáltatóval, amely támogatja a WS-Trust és a WS-Fed protokollokat is:

- **WS-Fed:** Ez a protokoll szükséges egy eszköz azure AD-hez való csatlakoztatásához.
- **WS-Bizalm:** Ez a protokoll az Azure AD-hez csatlakozott eszközre való bejelentkezéshez szükséges.

AD FS használata esetén a következő WS-Trust végpontokat kell engedélyeznie:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Ha az identitásszolgáltató nem támogatja ezeket a protokollokat, az Azure AD-csatlakozás nem működik natívan. 

>[!NOTE]
> Jelenleg az Azure AD-illesztés nem működik a [külső hitelesítési szolgáltatóval konfigurált AD FS 2019 elsődleges hitelesítési módszerként konfigurálva.](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary) Az Azure AD-csatlakozás elsődleges módszerként a jelszó-hitelesítést jelenti, ami ebben a forgatókönyvben hitelesítési hibákat eredményez


### <a name="smartcards-and-certificate-based-authentication"></a>Intelligens kártyák és tanúsítványalapú hitelesítés

Nem használhat intelligens kártyákat vagy tanúsítványalapú hitelesítést az eszközök Azure AD-hez való csatlakoztatásához. Az intelligens kártyák azonban az Azure AD-hez csatlakozott eszközökre való bejelentkezéshez is használhatók, ha konfigurálta az AD FS-t.

**Ajánlás:** Valósítsa meg a Windows Hello for Business alkalmazást a Windows 10-es eszközök erős, jelszó nélküli hitelesítéséhez.

### <a name="user-configuration"></a>Felhasználói konfiguráció

Ha felhasználókat hoz létre a következőkben:

- **A helyszíni Active Directory**ban szinkronizálnia kell őket az Azure AD-vel az [Azure AD-vel.](/azure/active-directory/hybrid/how-to-connect-sync-whatis) 
- **Az Azure AD,** nincs szükség további beállításra.

Az Azure AD-hez csatlakozott eszközök nem támogatják a helyszíni UPN-ek, amelyek eltérnek az Azure AD-upn-októl. Ha a felhasználók egy helyszíni UPN- t használnak, érdemes az elsődleges upn használatával az Azure AD-ben.

## <a name="assess-your-device-management"></a>Az eszközkezelés felmérése

### <a name="supported-devices"></a>Támogatott eszközök

Azure AD-csatlakozás:

- Csak Windows 10-es eszközökre vonatkozik. 
- Nem alkalmazható a Windows korábbi verzióira vagy más operációs rendszerekre. Ha Windows 7/8.1-es eszközökkel rendelkezik, frissítenie kell a Windows 10-re az Azure AD-csatlakozás központi telepítéséhez.
- FiPS módban a TPM-mel rendelkező eszközök nem támogatottak.
 
**Ajánlás:** Mindig a windows 10 legújabb kiadását használja a frissített funkciók előnyeinek kihasználásához.

### <a name="management-platform"></a>Felügyeleti platform

Az Azure AD-hez csatlakozó eszközök eszközkezelése egy MDM-platformon alapul, például az Intune-on és az MDM-központi szolgáltatókon. A Windows 10 beépített MDM-ügynökkel rendelkezik, amely minden kompatibilis MDM-megoldással működik.

> [!NOTE]
> A csoportházirendek nem támogatottak az Azure AD-hez csatlakoztatott eszközök, mivel nem kapcsolódnak a helyszíni Active Directoryhoz. Az Azure AD-hez csatlakozó eszközök kezelése csak MDM-en keresztül lehetséges

Az Azure AD-hez csatlakozó eszközök kezelésére két módszer létezik:

- **MDM-csak** – Az eszközt kizárólag egy Olyan MDM-szolgáltató kezeli, mint az Intune. Az mdm-regisztrációs folyamat részeként minden házirend kézbesítésre kerülnek. Az Azure AD Premium vagy EMS-ügyfelek számára az MDM-regisztráció egy automatikus lépés, amely egy Azure AD-csatlakozás része.
- **Társkezelés** – Az eszközt egy MDM-szolgáltató és az SCCM kezeli. Ebben a megközelítésben az SCCM-ügynök egy MDM által felügyelt eszközre van telepítve bizonyos szempontok felügyeletéhez.

Ha csoportházirendeket használ, értékelje ki az MDM-házirend-paritást az [MDM áttelepítéselemző eszközzel (MMAT).](https://github.com/WindowsDeviceManagement/MMAT) 

Tekintse át a támogatott és nem támogatott házirendeket, és állapítsa meg, hogy használhat-e MDM-megoldást a csoportházirendek helyett. Nem támogatott házirendek esetén vegye figyelembe az alábbiakat:

- Szükség van a nem támogatott szabályzatok az Azure AD-hez csatlakozott eszközökhöz vagy felhasználókhoz?
- A nem támogatott szabályzatok egy felhőalapú telepítésben alkalmazhatók?

Ha az MDM-megoldás nem érhető el az Azure AD alkalmazásgalérián keresztül, hozzáadhatja azt az Azure Active Directory és az [MDM integrációjában](/windows/client-management/mdm/azure-active-directory-integration-with-mdm)ismertetett folyamat ot követve. 

A közös felügyelet révén az SCCM segítségével kezelheti az eszközök bizonyos aspektusait, miközben a szabályzatok az MDM platformon keresztül kerülnek kézbesítésre. A Microsoft Intune lehetővé teszi az SCCM-mel való közös kezelést. A Windows 10-es eszközök közös kezeléséről a [Mi a közös kezelés?](/configmgr/core/clients/manage/co-management-overview) Ha az Intune-tól eltérő MDM-terméket használ, kérjük, érdeklődjön az MDM-szolgáltatónál a vonatkozó közös kezelési forgatókönyvekről.

**Ajánlás:** Fontolja meg az MDM csak az Azure AD-hez csatlakozott eszközök felügyeletét.

## <a name="understand-considerations-for-applications-and-resources"></a>Az alkalmazásokra és az erőforrásokra vonatkozó szempontok ismertetése

Javasoljuk, hogy a jobb felhasználói élmény és a hozzáférés-vezérlés érdekében migrálja az alkalmazásokat a helyszíni felhőbe. Az Azure AD-hez csatlakozó eszközök azonban zökkenőmentesen biztosíthatnak hozzáférést mind a helyszíni, mind a felhőalapú alkalmazásokhoz. További információ: Hogyan működik az egyszeri szolgáltatás a helyszíni erőforrások az [Azure AD-hez csatlakozott eszközökön.](azuread-join-sso.md)

A következő szakaszok a különböző típusú alkalmazások és erőforrások szempontjait sorolják fel.

### <a name="cloud-based-applications"></a>Felhőalapú alkalmazások

Ha egy alkalmazást hozzáad az Azure AD-alkalmazásgyűjteményhez, a felhasználók az Azure AD-hez csatlakozott eszközökön keresztül kapják meg az SSO-t. Nincs szükség további konfigurációra. A felhasználók sso-t kapnak mind a Microsoft Edge, mind a Chrome böngészőkben. A Chrome esetében telepítenie kell a [Windows 10-fiókok bővítményt.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) 

Minden Win32 alkalmazás, amely:

- Támaszkodhat webfiók-kezelő (WAM) a token kérelmek is kap egyszeri biztonsági felelős az Azure AD-hez csatlakozó eszközök. 
- Ne hagyatkozzon a WAM kérheti a felhasználók hitelesítését. 

### <a name="on-premises-web-applications"></a>Helyszíni webalkalmazások

Ha alkalmazásai egyedi környezetben készültek és/vagy helyszíni üzemeltetésűek, hozzá kell adnia őket a böngésző megbízható webhelyeihez a következőkhöz:

- A Windows integrált hitelesítésének engedélyezése 
- Adjon meg egy gyors egyszeri sso-élményt a felhasználóknak. 

Ha AD FS-t használ, olvassa [el az Egyszeri bejelentkezés ellenőrzése és kezelése az AD FS rendszerrel című témakört.](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)) 

**Ajánlás:** Fontolja meg a felhőben (például az Azure-ban) való üzemeltetést, és a jobb élmény érdekében integrálva az Azure AD-vel.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Örökölt protokollokra támaszkodó helyszíni alkalmazások

A felhasználók az Azure AD-hez csatlakozó eszközökről kapnak sso-t, ha az eszköz hozzáfér egy tartományvezérlőhöz. 

**Ajánlás:** Telepítse [az Azure AD App proxyt](/azure/active-directory/manage-apps/application-proxy) az alkalmazások biztonságos elérésének engedélyezéséhez.

### <a name="on-premises-network-shares"></a>Helyszíni hálózati megosztások

A felhasználók egyszeri sso az Azure AD-hez csatlakozott eszközök, ha egy eszköz hozzáfér egy helyszíni tartományvezérlő.

### <a name="printers"></a>Nyomtatók

Nyomtatók esetén [hibrid felhőalapú nyomtatást](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) kell telepítenie a nyomtatók Azure AD-hez csatlakozó eszközökön történő felderítéséhez. 

Bár a nyomtatók nem fedezhetők fel automatikusan csak felhőalapú környezetben, a felhasználók a nyomtatók UNC-elérési útvonalát is használhatják közvetlenül hozzáadásukhoz. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>A számítógép-hitelesítésre támaszkodó helyszíni alkalmazások

Az Azure AD-hez csatlakozó eszközök nem támogatják a számítógép-hitelesítésre támaszkodó helyszíni alkalmazásokat. 

**Ajánlás:** Fontolja meg az alkalmazások kivisszavonulását és a modern alternatívákra való áttérést.

### <a name="remote-desktop-services"></a>Távoli asztali szolgáltatások

Távoli asztali kapcsolat egy Azure AD-hez csatlakozó eszközök höz a gazdagép vagy Azure AD-hez vagy azure AD csatlakozott. A távoli asztal nem csatlakozott vagy nem Windows rendszerű eszközről nem támogatott. További információ: [Csatlakozás távoli Azure AD-hez csatlakozott számítógéphez](/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>A kiépítési lehetőségek megismerése

Az Azure AD-csatlakozást a következő módszerekkel építheti ki:

- **Önkiszolgáló oobe/settings** – Önkiszolgáló módban a felhasználók az Azure AD-illesztési folyamaton mennek keresztül a Windows kezdőélmény (OOBE) vagy a Windows-beállítások során. További információt a [Munkahelyi eszköz csatlakoztatása a szervezet hálózatához című témakörben talál.](/azure/active-directory/user-help/user-help-join-device-on-network) 
- **Windows Autopilot** – A Windows Autopilot lehetővé teszi az eszközök előzetes konfigurálását az OOBE zökkenőmentesebb élménye érdekében az Azure AD-csatlakozás végrehajtásához. További információt a [Windows Autopilot áttekintése című témakörben talál.](/windows/deployment/windows-autopilot/windows-10-autopilot) 
- **Tömeges regisztráció** – Tömeges regisztráció lehetővé teszi a rendszergazda által vezérelt Azure AD-csatlakozás használatával tömeges kiépítési eszköz eszközök konfigurálása. További információt a [Tömeges regisztráció Windows-eszközökre](/intune/windows-bulk-enroll)című témakörben talál.
 
Itt egy összehasonlítás a három megközelítés 
 
|   | Önkiszolgáló beállítás | Windows Autopilot | Csoportos regisztráció |
| --- | --- | --- | --- |
| Felhasználói beavatkozás megkövetelése a beállításhoz | Igen | Igen | Nem |
| Informatikai erőfeszítés szükséges | Nem | Igen | Igen |
| Alkalmazandó folyamatok | Oobe & beállítások | Csak kezdőélmény | Csak kezdőélmény |
| Helyi rendszergazdai jogok az elsődleges felhasználó számára | Igen, alapértelmezés szerint | Konfigurálható | Nem |
| Eszköz OEM-támogatásának megkövetelése | Nem | Igen | Nem |
| Támogatott verziók | 1511+ | 1709+ | 1703+ |
 
Válassza ki a telepítési megközelítést vagy a megközelítéseket a fenti táblázat áttekintésével és a következő szempontok áttekintésével mindkét megközelítés elfogadásához:  

- Vannak a felhasználók tech hozzáértés, hogy menjen át a beállítás magukat? 
   - Az önkiszolgáló szolgáltatás ezeknek a felhasználóknak a legjobban működik. A felhasználói élmény fokozása érdekében fontolja meg a Windows Autopilot programot.  
- A felhasználók távolivagy vállalati telephelyen belül vannak? 
   - Önkiszolgáló vagy Autopilot működik a legjobban a távoli felhasználók számára a gondtalan beállítás. 
- Felhasználó által vezérelt vagy rendszergazda által felügyelt konfigurációt szeretne előnyben részesíteni? 
   - A tömeges regisztráció jobban működik a rendszergazda által vezérelt telepítéshez, hogy az eszközöket a felhasználóknak való átadás előtt állítsa be.     
- Az 1-2 OEMS készülékről vásárol eszközöket, vagy az OEM-eszközök széles körű forgalmazásával rendelkezik?  
   - Ha korlátozott oem-ektől vásárol, akik szintén támogatják az Autopilot-ot, akkor az Autopilot-szal való szorosabb integráció előnyeit élvezheti. 

## <a name="configure-your-device-settings"></a>Az eszközbeállítások konfigurálása

Az Azure Portal lehetővé teszi az Azure AD-hez csatlakozott eszközök üzembe helyezésének szabályozását a szervezetben. A kapcsolódó beállítások konfigurálásához válassza az Azure `Devices > Device settings`Active Directory **(Azure Active Directory) (Azure Active Directory) (Azure Active Directory) (A) lehetőséget.**

### <a name="users-may-join-devices-to-azure-ad"></a>A felhasználók csatlakozhatnak az Azure AD-hez

Állítsa ezt a beállítást **az Összes** vagy **a Kiválasztott** a központi telepítés hatóköre és az alapján, hogy ki szeretné engedélyezni egy Azure AD-hez csatlakozott eszköz beállítását. 

![A felhasználók csatlakozhatnak az Azure AD-hez](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>További helyi rendszergazdák az Azure AD-hez csatlakozott eszközökön

Válassza **a Kijelölt** lehetőséget, és válassza ki azokat a felhasználókat, akiket hozzá szeretne adni a helyi rendszergazdák csoportjához az összes Azure AD-hez csatlakozott eszközön. 

![További helyi rendszergazdák az Azure AD-hez csatlakozott eszközökön](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Eszközök csatlakoztatásának többtényezős hitelesítésszükséges

Válassza az **"Igen,** ha a felhasználóknak többéves többfa-t kell végrehajtaniuk, miközben csatlakoznak az eszközökhöz az Azure AD-hez. Az eszközökhöz csatlakozó felhasználók az MFA használatával az Azure AD-hez, maga az eszköz lesz a 2.

![Eszközök csatlakoztatásának többtényezős hitelesítésszükséges](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>A mobilitási beállítások konfigurálása

A mobilitási beállítások konfigurálása előtt előfordulhat, hogy először hozzá kell adnia egy MDM-szolgáltatót.

**MDM-szolgáltató hozzáadása:**

1. Az Azure Active Directory **(Kezelés)** `Mobility (MDM and MAM)` **csoportban**kattintson a gombra. 
1. Kattintson **az Alkalmazás hozzáadása gombra.**
1. Válassza ki az MDM-szolgáltatót a listából.

   ![Alkalmazás hozzáadása](./media/azureadjoin-plan/04.png)

A kapcsolódó beállítások konfigurálásához válassza ki az MDM-szolgáltatót. 

### <a name="mdm-user-scope"></a>MDM felhasználói hatókör

Válassza a **Néhány** vagy **az Összes** lehetőséget a központi telepítés hatóköre alapján. 

![MDM felhasználói hatókör](./media/azureadjoin-plan/05.png)

A hatókör alapján az alábbiak egyike történik: 

- **A felhasználó MDM-hatókörben van:** Ha Rendelkezik Egy Azure AD Premium-előfizetéssel, az MDM-regisztráció automatikus, valamint az Azure AD-csatlakozás. Minden hatókörrel rendelkező felhasználónak rendelkeznie kell az MDM megfelelő licenccel. Ha ebben a forgatókönyvben az MDM-regisztráció sikertelen, az Azure AD-csatlakozás is vissza lesz állítva.
- **A felhasználó nem az MDM-hatókörben:** Ha a felhasználók nem az MDM-hatókörben vannak, az Azure AD-illesztés mdm-regisztráció nélkül fejeződik be. Ez nem felügyelt eszközt eredményez.

### <a name="mdm-urls"></a>MDM URL-címek

Az MDM-konfigurációhoz három URL kapcsolódik:

- MDM használati feltételeinek URL-címe
- MDM-felderítési URL-cím 
- MDM megfelelőségi URL-címe

![Alkalmazás hozzáadása](./media/azureadjoin-plan/06.png)

Minden URL-cím nek van egy előre definiált alapértelmezett értéke. Ha ezek a mezők üresek, további információért forduljon az MDM szolgáltatójához.

### <a name="mam-settings"></a>MAM-beállítások

A MAM nem vonatkozik az Azure AD-csatlakozásra. 

## <a name="configure-enterprise-state-roaming"></a>Vállalati állapotroaming konfigurálása

Ha engedélyezni szeretné az állapotroamingot az Azure AD-be, hogy a felhasználók szinkronizálhassák a beállításokat az eszközök között, olvassa el a [Vállalati állapotroaming engedélyezése az Azure Active Directoryban című témakört.](enterprise-state-roaming-enable.md) 

**Javaslat:** Engedélyezze ezt a beállítást még a hibrid Azure AD-hez csatlakozó eszközök esetén is.

## <a name="configure-conditional-access"></a>Feltételes hozzáférés konfigurálása

Ha rendelkezik egy MDM-szolgáltató konfigurálva az Azure AD-hez csatlakozott eszközök, a szolgáltató jelzők az eszköz megfelelő, amint az eszköz felügyelet alatt áll. 

![Megfelelő eszköz](./media/azureadjoin-plan/46.png)

Ezzel a megvalósítással [felügyelt eszközöket kérhet a felhőalapú alkalmazások hoz való hozzáféréshez a feltételes hozzáféréssel.](../conditional-access/require-managed-devices.md)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Csatlakozzon egy új Windows 10-es eszközhöz az Azure AD-vel az első futtatás](azuread-joined-devices-frx.md)
> során[Csatlakozzon munkahelyi eszközéhez a szervezet hálózatához](/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
