---
title: Közzététel a távoli asztal az Azure AD-alkalmazásproxyval |} A Microsoft Docs
description: Az Azure AD-alkalmazásproxy összekötőit alapjait ismerteti.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: barbkess
ms.custom: it-pro
ms.reviewer: harshja
ms.openlocfilehash: 2afe75045444fbc0ca36ee1cfca3d96f5b218ab0
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53135491"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Az Azure AD-alkalmazásproxy a távoli asztal közzététele

Távoli asztali szolgáltatás és az Azure AD-alkalmazásproxy együttesen az erről a vállalati hálózati alkalmazottak a termelékenység növelése. 

Ez a cikk célközönségét a következő:
- Aktuális alkalmazásproxy felhasználók számára a végfelhasználók számára további alkalmazások tegye közzé a helyszíni alkalmazások távoli asztali szolgáltatások használatával.
- Aktuális távoli asztali szolgáltatások felhasználói szeretné csökkenteni a támadási felületet, az üzembe helyezés az Azure AD-alkalmazásproxy használatával. Ebben a forgatókönyvben egy részéhez kétlépéses ellenőrzést, és a feltételes hozzáférés-vezérlést biztosít az RDS

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Hogyan alkalmazásproxy megfelel a normál távoli asztali szolgáltatások telepítése

Egy normál távoli asztali szolgáltatások telepítése magában foglalja a különféle távoli asztali szerepkör-szolgáltatások Windows Server rendszeren futó. Megnézzük a [távoli asztali szolgáltatások architektúra](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), több üzembe helyezési lehetőség van. Ellentétben más távoli asztali szolgáltatások telepítési lehetőségek a [RDS-példány üzembe az Azure AD-alkalmazásproxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (az alábbi ábrán látható) állandó kimenő kapcsolat az összekötő szolgáltatást futtató kiszolgáló tartozik. Más központi, nyissa meg a bejövő kapcsolatok a terheléselosztón keresztül hagyja.

![Az alkalmazásproxy helyezkedik el a távoli asztali szolgáltatások virtuális gép és a nyilvános internet közötti](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Az RDS-példány üzembe a távoli asztali webes szerepkör és a távoli asztali átjáró szerepkör futtassa az internetkapcsolattal rendelkező gépek. Ezeket a végpontokat érhetők el a következő okok miatt:
- A távoli asztali webes biztosít a felhasználó jelentkezik be, és megtekintheti a különböző helyszíni alkalmazások és asztali számítógépek eléréséhez egy nyilvános végpontot. Erőforrás kiválasztásakor, egy RDP-kapcsolat jön létre a natív alkalmazás használatával az operációs rendszeren.
- Ebbe a képbe a távoli asztali átjáró érhető el, miután a felhasználó elindítja az RDP-kapcsolat. A távoli asztali átjáró titkosított keresztül az internetről érkező RDP-forgalmat kezeli, és a helyszíni kiszolgálónak, amely a felhasználó csatlakozik, hogy a rendszer lefordítja azt. Ebben a forgatókönyvben a távoli asztali átjáró fogadja a forgalmat az Azure AD-alkalmazásproxy származik.

>[!TIP]
>Ha nem léptetett életbe a távoli asztali szolgáltatások előtt, vagy további információra, mielőtt elkezdené, megtudhatja, hogyan [zökkenőmentesen üzembe helyezése az Azure Resource Manager és az Azure Marketplace-en RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Követelmények

- Eltérő a távoli asztali webes ügyféllel,-ügyfél használata, mivel a webes ügyfél nem támogatja az alkalmazásproxy.

- A távoli asztali Web- és a távoli asztali átjáró végpontok található ugyanarra a gépre, és a közös legfelső szintű kell lennie. A távoli asztali Web- és a távoli asztali átjáró, az alkalmazásproxy használatával egyetlen alkalmazásként tesszük közzé, hogy a két alkalmazás között egyszeri bejelentkezés használatát is használhat.

- Már rendelkezik [telepített távoli asztali szolgáltatások](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), és [engedélyezte az alkalmazásproxyt](application-proxy-add-on-premises-application.md).

- Ez a forgatókönyv azt feltételezi, hogy a végfelhasználóknak meg az Internet Explorer a Windows 7 vagy Windows 10 rendszerű asztali számítógépek, amelyek a távoli asztali webes oldalán keresztül csatlakoznak. Ha más operációs rendszerek támogatásához van szüksége, tekintse meg [támogatja a többi ügyfél-konfiguráció](#support-for-other-client-configurations).

- Ha a közzététel a távoli asztali webes ajánlott az azonos belső és külső teljes Tartománynevet használja. Ha a belső és külső teljes tartománynevek eltér majd le kell tiltania fejléc fordítási kérelmek az ügyfél érvénytelen kapcsolatok fogadása. 

- Az Internet Explorer a távoli asztali szolgáltatások ActiveX bővítmény engedélyezéséhez.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>A közös a távoli asztali szolgáltatások és -Proxy forgatókönyv megvalósításához

RDS és Azure AD-alkalmazásproxy a környezetének beállítása után lépésekkel úgy, hogy a két megoldás. Ezek a lépések bemutatják, közzététele a két webes irányuló távoli asztali szolgáltatások végpontot (távoli asztali Web- és az RD átjáró) alkalmazásokként, és ezután irányítja a forgalmat a go alkalmazásproxyn keresztül a távoli asztali szolgáltatások.

### <a name="publish-the-rd-host-endpoint"></a>Közzététel a távoli asztali állomás végpont

1. [Egy új Application Proxy-alkalmazások közzététele](application-proxy-add-on-premises-application.md) a következő értékeket:
   - Belső URL-címe: `https://\<rdhost\>.com/`, ahol `\<rdhost\>` , amelyek a távoli asztali Web- és a távoli asztali átjáró közös gyökere.
   - Külső URL-címe: Ezt a mezőt automatikusan kitölti a rendszer az alkalmazás neve alapján, de módosíthatja azokat. A felhasználók halad át az URL-cím RDS elérésekor
   - Előhitelesítési módszer: az Azure Active Directory
   - Lefordítja az URL-fejlécek: nem
2. Felhasználók hozzárendelése a közzétett távoli asztali alkalmazást. Ellenőrizze, hogy az összes férhet hozzá, RDS, túl.
3. Hagyja, az alkalmazás egyszeri bejelentkezési módszer **az Azure AD egyszeri bejelentkezés le van tiltva**. A felhasználók számára a rendszer felkéri egyszer az Azure ad-hez, és egyszer a távoli asztali webes hitelesítéséhez, de rendelkezik egyszeri bejelentkezéshez, a távoli asztali átjáró.
4. Lépjen a **Azure Active Directory** > **Alkalmazásregisztrációk** > *az alkalmazás* > **beállításai**.
5. Válassza ki **tulajdonságok** és frissítheti a **kezdőlap URL-címe** mezőt, mutasson a távoli asztali webes végpontra (például `https://\<rdhost\>.com/RDWeb`).

### <a name="direct-rds-traffic-to-application-proxy"></a>Az alkalmazásproxy közvetlen RDS-forgalom

Rendszergazdaként az RDS-környezethez csatlakozik, és módosítsa a távoli asztali átjáró kiszolgáló nevét az üzembe helyezéshez. Ez a konfiguráció biztosítja, hogy a kapcsolatok az Azure AD-alkalmazásproxy szolgáltatáson keresztül halad.

1. Csatlakozzon a távoli asztali szolgáltatások kiszolgáló fut a távoli asztali Kapcsolatszervező szerepkör.
2. Indítsa el a **Kiszolgálókezelő**.
3. Válassza ki **távoli asztali szolgáltatások** a a bal oldali panelen.
4. Válassza az **Áttekintés** lehetőséget.
5. Az üzembe helyezés – áttekintés szakaszban válassza a legördülő menüből, majd **központi telepítési tulajdonságok szerkesztése**.
6. A távoli asztali átjáró lapon módosítsa a **kiszolgálónév** mezőt a külső URL-címet állíthat be a távoli asztali állomás végpont az Alkalmazásproxyban.
7. Módosítsa a **bejelentkezési módszer** mezőt **jelszavas hitelesítés**.

  ![A távoli asztali szolgáltatások telepítési tulajdonságai képernyő](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Futtassa a következő parancsot az egyes gyűjtemények. Cserélje le *\<yourcollectionname\>* és *\<proxyfrontendurl\>* a saját adataira. Ez a parancs lehetővé teszi, hogy az egyszeri bejelentkezés a távoli asztali Web- és a távoli asztali átjáró között, és optimalizálja a teljesítmény:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Példa:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
>[!NOTE]
>A fenti parancsot használja egy használni kívánt szintaxiskiemelést a "" nrequire ".

9. Ellenőrizze az egyéni RDP-tulajdonságok módosítása, valamint megtekintheti a gyűjteményhez RDWeb a letöltött RDP-fájl tartalmát, futtassa a következő parancsot:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Most, hogy konfigurálta a távoli asztali, az Azure AD-alkalmazásproxy RDS internet felé néző összetevőjeként átvette A nyilvános internet felé néző végpontok a távoli asztali Web- és a távoli asztali átjáró gépeken távolíthatja el.

## <a name="test-the-scenario"></a>A forgatókönyv tesztelése

Tesztelje a forgatókönyvet, az Internet Explorer a Windows 7 vagy 10 számítógépen.

1. Megnyithatja a külső URL-cím beállítása, vagy az alkalmazását a [MyApps panel](https://myapps.microsoft.com).
2. A rendszer felkéri az Azure Active Directory hitelesítést. Az alkalmazáshoz hozzárendelt fiókot használnia.
3. A rendszer felkéri a távoli asztali webes hitelesítés.
4. Ha a távoli asztali szolgáltatások hitelesítés sikeres, válassza ki a desktopban vagy a kívánt alkalmazást, és használatának megkezdése.

## <a name="support-for-other-client-configurations"></a>Egyéb ügyfél-konfigurációk támogatása

A következő cikkben ismertetett konfigurációs van, a felhasználók számára a Windows 7-es vagy 10, az Internet Explorer és a távoli asztali szolgáltatások ActiveX-bővítményt. Kell, azonban, hogy támogatják-e más operációs rendszert vagy böngészőt. A különbség az Ön által használt hitelesítési módszert.

| Hitelesítési módszer | Támogatott ügyfél-konfiguráció |
| --------------------- | ------------------------------ |
| Előhitelesítés    | Windows 7/10 az Internet Explorer + RDS ActiveX-bővítmény használata |
| Átengedés | Minden más operációs rendszer, amely támogatja a Microsoft távoli asztal alkalmazás |

Az előhitelesítési folyamat további biztonsági előnyöket kínál, mint a csatlakoztatott folyamatot. Az előhitelesítési használhatja az Azure AD hitelesítési szolgáltatások, mint az egyszeri bejelentkezés, a feltételes hozzáférés és a kétlépéses ellenőrzést a helyszíni erőforrásokhoz. Akkor is győződjön meg arról, hogy csak hitelesített forgalom éri el a hálózat.

Átmenő hitelesítés használatához van ebben a cikkben felsorolt lépések csak két módosításai:
1. A [közzététel a távoli asztali állomás végpont](#publish-the-rd-host-endpoint) 1. lépés, állítsa az előhitelesítési módszer **csatlakoztatott**.
2. A [Application Proxy-forgalom közvetlen RDS](#direct-rds-traffic-to-application-proxy), hagyja ki teljesen a 8. lépés.

## <a name="next-steps"></a>További lépések

[Távoli hozzáférés a Sharepointhoz, az Azure AD-alkalmazásproxy engedélyezése](application-proxy-integrate-with-sharepoint-server.md)  
[Az Azure AD-alkalmazásproxy használatával távolról fér hozzá az alkalmazásokhoz kapcsolódó biztonsági szempontok](application-proxy-security.md)
