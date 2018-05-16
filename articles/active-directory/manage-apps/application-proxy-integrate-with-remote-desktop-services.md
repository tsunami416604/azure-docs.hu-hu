---
title: Az Azure AD alkalmazás Proxy távoli asztal közzététele |} Microsoft Docs
description: Alapvető tudnivalók az Azure AD-alkalmazásproxy összekötők ismerteti.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: barbkess
ms.custom: it-pro
ms.reviewer: harshja
ms.openlocfilehash: 4c3b88d9f522e28189bcb746905d35dced9d31cc
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Az Azure AD alkalmazásproxy távoli asztal közzététele

Távoli asztali szolgáltatás és az Azure AD-alkalmazásproxy működnek együtt az alkalmazottak számára a vállalati hálózat elhagyja a termelékenység növelése érdekében. 

Ez a cikk a célközönség van:
- Aktuális alkalmazásproxy az ügyfelek, akik a helyszíni alkalmazások a távoli asztali szolgáltatások használatával közzétételével további alkalmazásokat a végfelhasználók számára biztosítani szeretné.
- Aktuális távoli asztali szolgáltatások felhasználói szeretné csökkenteni a támadási felületet, illetve azok központi telepítésének az Azure AD-alkalmazásproxy használatával. Ebben a forgatókönyvben biztosít a kétlépéses ellenőrzést, és a feltételes hozzáférés-vezérlést korlátozott számú RDS

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Hogyan alkalmazásproxy beleilleszkedik a normál távoli asztali szolgáltatások telepítése

A szabványos távoli asztali szolgáltatások telepítése különböző távoli asztal szerepkör-szolgáltatások a Windows Server rendszert futtató tartalmazza. Megnézi a [távoli asztali szolgáltatások architektúrája](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture), több központi telepítési lehetőség áll rendelkezésre. Más távoli asztali szolgáltatások telepítési lehetőségeket, ellentétben a [távoli asztali szolgáltatások telepítése az Azure AD alkalmazásproxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (az alábbi ábrán is látható) az összekötő szolgáltatást futtató kiszolgáló állandó kimenő kapcsolattal rendelkezik. Más központi hagyja nyitva bejövő kapcsolatok terheléselosztó keresztül.

![Alkalmazásproxy helyezkedik el, a távoli asztali szolgáltatások virtuális gép és a nyilvános internet közötti](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Egy távoli asztali szolgáltatások telepítése esetén a távoli asztali webes szerepkör és a távoli asztali átjáró szerepkör futtatásához, internetre irányuló gépeken. Ezeket a végpontokat érhetők el a következő okok miatt:
- Távoli asztali webes biztosít a felhasználó egy nyilvános végpontot, jelentkezzen be, és tekintse meg a különböző helyszíni alkalmazásokhoz és asztali számítógépek eléréséhez. Egy erőforrás választja, akkor egy RDP-kapcsolat jön létre, a natív alkalmazással az operációs rendszer.
- A képbe távoli asztali átjáró elérhető lesz, ha egy felhasználó elindítja a távoli ASZTAL kapcsolaton keresztül. A távoli asztali átjáró titkosított keresztül az internetről érkező RDP-forgalmát kezeli, és a következőkből fordítja le a helyi kiszolgálóra, amely a felhasználó csatlakozik. Ebben a forgatókönyvben a távoli asztali átjáró fogadja a forgalmat az Azure AD-alkalmazásproxy származik.

>[!TIP]
>Ha még nem telepített távoli asztali szolgáltatások előtt, vagy megkezdése előtt további információra van szüksége, megtudhatja, hogyan [zökkenőmentesen telepíteni a távoli asztali szolgáltatások az Azure Resource Manager és az Azure piactér](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Követelmények

- A távoli asztali webes és a távoli asztali átjáró végpontok ugyanazon a számítógépen, valamint egy közös legfelső szintű helyen kell lennie. Távoli asztali webes és a távoli asztali átjáró az alkalmazásproxy egyetlen alkalmazás táblaként vannak közzétéve, hogy egy egyszeri bejelentkezéses felhasználói élmény biztosítása a két alkalmazás között lehet.

- Ön már rendelkezik [központilag telepített távoli asztali szolgáltatások](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure), és [engedélyezve van az alkalmazásproxy](application-proxy-enable.md).

- Ez a forgatókönyv azt feltételezi, hogy a végfelhasználók nyissa meg az Internet Explorer keresztül a Windows 7 vagy Windows 10 asztali gépek, a távoli asztali weblap keresztül csatlakozó. Ha más operációs rendszerek támogatásához van szüksége, tekintse meg [támogatja az egyéb ügyfél-konfiguráció](#support-for-other-client-configurations).

- Az Internet Explorer a távoli asztali szolgáltatások ActiveX-bővítmény engedélyezéséhez.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>A közös távoli asztali szolgáltatások és a Proxy forgatókönyv telepítése

Miután beállította a távoli asztali szolgáltatások és a környezet az Azure AD alkalmazásproxy, kövesse a két megoldás kombinálhatók. Ezeket a lépéseket ismerteti a webalkalmazás számára is elérhető távoli asztali szolgáltatások végpontokat (a távoli asztali webes és a távoli asztali átjáró) közzététele alkalmazásokként és majd irányítja a forgalmat a proxyn keresztül történő Ugrás a távoli asztali szolgáltatások segítségével.

### <a name="publish-the-rd-host-endpoint"></a>A távoli asztali állomás végpont közzététele

1. [Alkalmazásproxy új alkalmazás közzététele](application-proxy-publish-azure-portal.md) a következő értékekkel:
   - Belső URL-címe: https://\<rdhost\>.com /, ahol \<rdhost\> , amely a távoli asztali webes és a távoli asztali átjáró közös gyökere.
   - Külső URL-címe: Ez a mező automatikusan feltöltődik értékkel az alkalmazás neve alapján, de módosíthatja azt. A felhasználók kerül az URL-cím RDS elérésekor
   - Előhitelesítési módszer: az Azure Active Directory
   - URL-cím fejlécek lefordítani: nincs
2. Felhasználók hozzárendelése a távoli asztali közzétett alkalmazást. Ellenőrizze, hogy minden hozzáférhetnek a távoli asztali szolgáltatások, túl.
3. Hagyja meg az egyszeri bejelentkezési módszer alkalmazására vonatkozó **az Azure AD az egyszeri bejelentkezés le van tiltva**. A felhasználó felkérést egyszer az Azure AD és a távoli asztali webes egyszer hitelesítéséhez, de rendelkezik egyszeri bejelentkezéshez a távoli asztali átjáró.
4. Ugrás a **Azure Active Directory** > **App regisztrációk** > *az alkalmazás* > **beállítások**.
5. Válassza ki **tulajdonságok** , és frissítse a **kezdőlapot URL-cím** mező úgy, hogy a távoli asztali webes végpontjának mutasson (például a https://\<rdhost\>.com/RDWeb).

### <a name="direct-rds-traffic-to-application-proxy"></a>Alkalmazásproxy közvetlen RDS-forgalom

A távoli asztali szolgáltatások környezetbe rendszergazdaként csatlakozhat, és módosítsa a távoli asztali átjárókiszolgáló neve a telepítéshez. Ez a konfiguráció biztosítja, hogy kapcsolatok nyissa meg az Azure AD alkalmazásproxy szolgáltatáson keresztül.

1. Csatlakozzon a távoli asztali szolgáltatások kiszolgálója a távoli asztali Kapcsolatszervező szerepkör.
2. Indítsa el **Kiszolgálókezelő**.
3. Válassza ki **távoli asztali szolgáltatások** a bal oldali ablaktáblán.
4. Válassza az **Áttekintés** lehetőséget.
5. A telepítési áttekintés részben jelölje ki a legördülő menüből, és válassza **központi telepítési tulajdonságok szerkesztése**.
6. A távoli asztali átjáró lapon módosítsa a **kiszolgálónév** mezőjét, amely a távoli asztali állomás végpont alkalmazásproxy a külső URL-címet.
7. Módosítsa a **bejelentkezési használata** mezőről **jelszó-hitelesítés**.

  ![A távoli asztali szolgáltatások telepítési tulajdonságok képernyő](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Futtassa ezt a parancsot a gyűjtemény. Cserélje le *\<yourcollectionname\>* és *\<proxyfrontendurl\>* a saját adataival. Ez a parancs lehetővé teszi, hogy az egyszeri bejelentkezés a távoli asztali webes és a távoli asztali átjáró között, és optimalizálja a teljesítmény:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Példa:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```

9. Ellenőrizze az egyéni RDP-tulajdonságok módosítása, valamint a gyűjtemény RDWeb a letöltött RDP-fájl tartalmának megtekintése, futtassa a következő parancsot:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Most, hogy a távoli asztal konfigurálását, az Azure AD-alkalmazásproxy RDS internetre részeként átvette A nyilvános internet felé néző végpontok eltávolíthatja a távoli asztali webes és a távoli asztali átjáró gépeken.

## <a name="test-the-scenario"></a>A forgatókönyv teszteléséhez

Tesztelje a forgatókönyvet, és az Internet Explorer, a Windows 7 vagy 10 számítógépen.

1. Nyissa meg a külső URL-cím beállítása, vagy az alkalmazás keresése a [MyApps panel](https://myapps.microsoft.com).
2. A rendszer felkéri az Azure Active Directory hitelesítéséhez. Olyan fiókot, amelyet az alkalmazáshoz rendelt használjon.
3. A rendszer felkéri a távoli asztali webes hitelesítés.
4. Ha a távoli asztali szolgáltatások hitelesítés sikeres, válassza ki a asztali vagy a kívánt alkalmazás, és megkezdi a munkát.

## <a name="support-for-other-client-configurations"></a>Az egyéb ügyfél-konfiguráció támogatása

A cikkben ismertetett konfigurálása a Windows 7-es vagy 10, amelyek az Internet Explorer és a távoli asztali szolgáltatások ActiveX-bővítmény a felhasználók számára. Ha szeretné, azonban támogathatja a más operációs rendszerek és a böngészők. A különbség az Ön által használt hitelesítési módszert.

| Hitelesítési módszer | Támogatott ügyfél-konfigurációja |
| --------------------- | ------------------------------ |
| Előhitelesítés    | Windows 7/10 Internet Explorer + a távoli asztali szolgáltatások ActiveX bővítmény használatával |
| Átengedés | Bármely más operációs rendszer, amely támogatja a Microsoft távoli asztal alkalmazás |

Az előhitelesítési folyamat kínál a közvetlenül csatlakoztatott adatfolyam-nál több biztonsági szempontból előnyökkel járhat. Az Azure AD hitelesítési szolgáltatások, mint az egyszeri bejelentkezés, a feltételes hozzáférés és a kétlépéses ellenőrzést előhitelesítési használhatja a helyszíni erőforrások. Akkor is győződjön meg arról, hogy csak hitelesített forgalom éri el a hálózaton.

Áteresztő hitelesítés használatára van ebben a cikkben ismertetett lépéseket csak két módosításai:
1. A [közzététel a távoli asztali állomás végpont](#publish-the-rd-host-endpoint) 1. lépés:, a előhitelesítési módszer beállítása **csatlakoztatott**.
2. A [alkalmazásproxy-forgalom közvetlen távoli asztali szolgáltatások](#direct-rds-traffic-to-application-proxy), hagyja ki teljesen a 8. lépés.

## <a name="next-steps"></a>További lépések

[Az Azure AD alkalmazásproxy SharePoint távoli hozzáférés engedélyezése](application-proxy-integrate-with-sharepoint-server.md)  
[Biztonsági szempontok az alkalmazások az Azure AD-alkalmazásproxy használatával távelérése](application-proxy-security.md)
