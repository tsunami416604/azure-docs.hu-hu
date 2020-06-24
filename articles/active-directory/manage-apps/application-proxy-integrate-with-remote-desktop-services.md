---
title: Távoli asztal közzététele Azure AD alkalmazás proxyval | Microsoft Docs
description: Az Azure AD Application Proxy-összekötők alapjaira terjed ki.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/23/2019
ms.author: kenwith
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34f3dcd607a7417932912528167a1120dbfd9b4f
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764519"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Távoli asztal közzététele az Azure-ban AD Application Proxy

Távoli asztal szolgáltatás és az Azure AD Application Proxy együttműködik a vállalati hálózattól elmaradó munkavállalók termelékenységének növelésével. 

A cikk célközönsége a következő:
- A jelenlegi alkalmazásproxy azon ügyfelei, akik több alkalmazást szeretnének nyújtani a végfelhasználók számára a helyszíni alkalmazások Távoli asztali szolgáltatások használatával történő közzétételével.
- A jelenlegi Távoli asztali szolgáltatások ügyfelek, akik az Azure AD Application Proxy használatával szeretnék csökkenteni az üzembe helyezés támadási felületét. Ez a forgatókönyv a kétlépéses ellenőrzés és a feltételes hozzáférés-vezérlés korlátozott készletét biztosítja az RDS-hez.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Hogyan illeszkedik az alkalmazásproxy a szabványos RDS-telepítéshez

A standard szintű RDS-telepítés a Windows Serveren futó különböző Távoli asztal szerepkör-szolgáltatásokat tartalmazza. A [Távoli asztali szolgáltatások architektúrában](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)több üzembe helyezési lehetőség is van. A többi távoli asztali telepítési lehetőségtől eltérően az [Azure ad Application proxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (az alábbi ábrán látható) RDS üzemelő példány állandó kimenő kapcsolatban áll az összekötő szolgáltatást futtató kiszolgálóval. Más központi telepítések egy terheléselosztó használatával hagyják el a nyitott bejövő kapcsolatokat.

![Az alkalmazásproxy az RDS-alapú virtuális gép és a nyilvános internet között helyezkedik el](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Az RDS-alapú környezetben a távoli asztali webes szerepkör és a RD-átjáró szerepkör az internetre irányuló gépeken fut. Ezek a végpontok a következő okok miatt érhetők el:
- A távoli asztali webes szolgáltatás a felhasználó számára nyilvános végpontot biztosít a bejelentkezéshez, és megtekinti a különböző helyszíni alkalmazásokat és asztalokat, amelyeket elérheti. Egy erőforrás kiválasztásakor létrejön egy RDP-kapcsolat a natív alkalmazás használatával az operációs rendszeren.
- Ha a felhasználó elindítja az RDP-kapcsolatot, a RD-átjáró bekerül a képbe. A RD-átjáró kezeli az interneten keresztül érkező titkosított RDP-forgalmat, és lefordítja azt a helyszíni kiszolgálóra, amelyhez a felhasználó csatlakozik. Ebben az esetben a RD-átjáró kapott forgalom az Azure-AD Application Proxy származik.

>[!TIP]
>Ha még nem telepítette az RDS-t, vagy további információra van szüksége az elkezdése előtt, ismerkedjen meg az [rds Azure Resource Manager és az Azure Marketplace szolgáltatással való zökkenőmentes üzembe helyezésével](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Követelmények

- Használjon a Távoli asztal webes ügyfélprogramtól eltérő ügyfelet, mert a webes ügyfél nem támogatja az alkalmazásproxy használatát.

- A távoli asztali webes és RD-átjáró végpontoknak ugyanazon a gépen és közös gyökérrel kell elhelyezkedniük. A távoli asztali webes és RD-átjáró egyetlen alkalmazásként jelennek meg az alkalmazásproxy használatával, így a két alkalmazás között egyszeri bejelentkezéses élményben lehet része.

- Ha már [telepítette az RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)-t, és [engedélyezte az alkalmazásproxy használatát](application-proxy-add-on-premises-application.md).

- Ez a forgatókönyv azt feltételezi, hogy a végfelhasználók a távoli asztali weblapon keresztül csatlakozó Windows 7 vagy Windows 10 rendszerű asztali számítógépeken haladnak végig az Internet Explorerben. Ha más operációs rendszereket kell támogatnia, tekintse meg [a más ügyfél-konfigurációk támogatása](#support-for-other-client-configurations)című témakört.

- A távoli asztali webes közzététel során ajánlott ugyanazt a belső és külső FQDN-t használni. Ha a belső és külső teljes tartománynevek eltérnek, akkor le kell tiltania a kérelem fejlécének fordítását, hogy az ügyfél ne kapjon érvénytelen hivatkozásokat. 

- Az Internet Explorerben engedélyezze az RDS ActiveX-bővítményt.

- Az Azure AD előhitelesítési folyamata esetében a felhasználók csak a **RemoteApp-és asztali számítógépek** ablaktáblán a számukra közzétett erőforrásokhoz tudnak csatlakozni. A felhasználók nem tudnak csatlakozni az asztalhoz a **Kapcsolódás távoli számítógéphez** panel használatával.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>A közös RDS-és alkalmazásproxy-forgatókönyv üzembe helyezése

Miután beállította az RDS-t és az Azure-AD Application Proxy a környezetéhez, kövesse a két megoldás összevonásának lépéseit. Ezek a lépések bemutatják, hogyan teheti közzé a két webes RDS-végpontot (a távoli asztali webes és RD-átjáró) alkalmazásként, majd átirányítja a forgalmat az RDS-en keresztül az alkalmazásproxy eléréséhez.

### <a name="publish-the-rd-host-endpoint"></a>A távoli asztali munkamenetgazda végpontjának közzététele

1. [Egy új alkalmazásproxy-alkalmazás közzététele](application-proxy-add-on-premises-application.md) a következő értékekkel:
   - Belső URL-cím: `https://\<rdhost\>.com/` , ahol `\<rdhost\>` a a távoli asztali webes és RD-átjáró megosztás általános gyökere.
   - Külső URL-cím: a program automatikusan kitölti ezt a mezőt az alkalmazás neve alapján, de módosíthatja is. A felhasználók az RDS-hez való hozzáféréskor ezt az URL-címet fogják megnyitni.
   - Előhitelesítési módszer: Azure Active Directory
   - URL-fejlécek fordítása: nem
2. Felhasználók kiosztása a közzétett RD-alkalmazáshoz. Győződjön meg arról, hogy mindegyiknek van hozzáférése az RDS-hez.
3. Hagyja letiltva az alkalmazás egyszeri bejelentkezési módszerét az **Azure ad egyszeri bejelentkezéssel**. A felhasználóknak egyszer kell hitelesíteniük magukat az Azure AD-ben és egyszer a RD Web-ben, de egyszeri bejelentkezéssel kell RD-átjáró.
4. Válassza a **Azure Active Directory**lehetőséget, majd az **alkalmazások regisztrációját**. Válassza ki az alkalmazást a listából.
5. A **kezelés**területen válassza a **branding (védjegyezés**) lehetőséget.
6. Frissítse a **Kezdőlap URL-címe** mezőt, hogy az a távoli asztali webes végpontra mutasson (például `https://\<rdhost\>.com/RDWeb` ).

### <a name="direct-rds-traffic-to-application-proxy"></a>RDS-forgalom közvetlen átvitele az Application proxyba

Kapcsolódjon az RDS üzembe helyezéséhez rendszergazdaként, és módosítsa a központi telepítés RD-átjáró-kiszolgálójának nevét. Ez a konfiguráció biztosítja, hogy a kapcsolatok az Azure AD Application Proxy szolgáltatáson keresztül lépjenek át.

1. Kapcsolódjon a RD-kapcsolatszervező szerepkört futtató RDS-kiszolgálóhoz.
2. Indítsa el a **Kiszolgálókezelő alkalmazást**.
3. A bal oldali ablaktáblán válassza a **Távoli asztali szolgáltatások** lehetőséget.
4. Válassza az **Áttekintés** lehetőséget.
5. A központi telepítés áttekintése szakaszban válassza a legördülő menüt, majd válassza a **telepítési Tulajdonságok szerkesztése**lehetőséget.
6. A RD-átjáró lapon módosítsa a **kiszolgáló neve** mezőt arra a külső URL-címre, amelyet a távoli asztali munkamenetgazda-végponthoz beállított a alkalmazásproxy alkalmazásban.
7. Módosítsa a **bejelentkezési módszer** mezőt **jelszó-hitelesítésre**.

   ![Központi telepítés tulajdonságai képernyő az RDS-ben](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Futtassa ezt a parancsot az egyes gyűjteményekhez. Cserélje le *\<yourcollectionname\>* a és a értékét *\<proxyfrontendurl\>* a saját adataira. Ez a parancs lehetővé teszi az egyszeri bejelentkezést a távoli asztali webes és RD-átjáró között, és optimalizálja a teljesítményt:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Például:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >A fenti parancs egy kezdő használ a következőben: "nrequire".

9. Az egyéni RDP-tulajdonságok módosításának ellenőrzéséhez, valamint a gyűjtemény RDWeb letöltött RDP-fájl tartalmának megtekintéséhez futtassa a következő parancsot:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Most, hogy konfigurálta a távoli asztal, az Azure ad Application proxy az RDS internet felé irányuló összetevője vette át. A távoli asztali web-és RD-átjáró-gépeken is eltávolíthatja a többi nyilvános internetre irányuló végpontot.

## <a name="test-the-scenario"></a>A forgatókönyv tesztelése

Tesztelje a forgatókönyvet az Internet Explorerrel egy Windows 7 vagy 10 rendszerű számítógépen.

1. Nyissa meg az Ön által beállított külső URL-címet, vagy keresse meg az alkalmazást a [MyApps panelen](https://myapps.microsoft.com).
2. A rendszer a Azure Active Directory hitelesítését kéri. Használjon olyan fiókot, amelyet az alkalmazáshoz rendelt.
3. A rendszer arra kéri, hogy végezzen hitelesítést a RD Web-ben.
4. Az RDS-hitelesítés sikeressége után kiválaszthatja a kívánt asztali vagy alkalmazást, és megkezdheti a munkát.

## <a name="support-for-other-client-configurations"></a>Más ügyfél-konfigurációk támogatása

A jelen cikkben ismertetett konfiguráció a Windows 7 vagy 10 rendszerű felhasználók számára készült, az Internet Explorer és az RDS ActiveX bővítmény segítségével. Ha azonban más operációs rendszereket vagy böngészőket is támogat. A különbség a használt hitelesítési módszer.

| Hitelesítési módszer | Támogatott ügyfél-konfiguráció |
| --------------------- | ------------------------------ |
| Előhitelesítés    | Windows 7/10 Internet Explorer + RDS ActiveX bővítmény használatával |
| Csatlakoztatott | Minden más operációs rendszer, amely támogatja a Microsoft Távoli asztal alkalmazást |

Az előhitelesítési folyamat több biztonsági előnyt kínál, mint az áteresztő folyamat. Az előzetes hitelesítéssel az Azure AD-hitelesítési funkciókat, például az egyszeri bejelentkezést, a feltételes hozzáférést, valamint a helyszíni erőforrások kétlépéses ellenőrzését is használhatja. Győződjön meg arról is, hogy csak a hitelesített forgalom éri el a hálózatot.

Az áteresztő hitelesítés használatához a cikkben felsorolt lépések csak két módosítással rendelkeznek:
1. A [Távoli asztali gazdagép végpontjának](#publish-the-rd-host-endpoint) (1. lépés) közzétételéhez állítsa az előhitelesítési módszert az **áteresztés**értékre.
2. Az [alkalmazás-proxy felé irányuló közvetlen RDS-forgalom](#direct-rds-traffic-to-application-proxy)esetében hagyja ki a 8. lépést teljes mértékben.

## <a name="next-steps"></a>További lépések

[Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD-alkalmazásproxy segítségével](application-proxy-integrate-with-sharepoint-server.md)  
[Biztonsági megfontolások az alkalmazások távoli eléréséhez az Azure AD Application Proxy használatával](application-proxy-security.md)
