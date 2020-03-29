---
title: Távoli asztal közzététele az Azure AD alkalmazásproxyval | Microsoft dokumentumok
description: Ismerteti az Azure AD alkalmazásproxy-összekötők alapjait.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67108461"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Távoli asztal közzététele az Azure AD alkalmazásproxyval

A Távoli asztali szolgáltatás és az Azure AD alkalmazásproxy együttműködik a vállalati hálózattól távol lévő dolgozók termelékenységének javítása érdekében. 

A cikk célközönsége a következő:
- A jelenlegi alkalmazásproxy-ügyfelek, akik több alkalmazást szeretnének kínálni a végfelhasználóknak a helyszíni alkalmazások távoli asztali szolgáltatásokon keresztül történő közzétételével.
- A távoli asztali szolgáltatások jelenlegi ügyfelei, akik az Azure AD alkalmazásproxy használatával szeretnék csökkenteni a telepítés támadási felületét. Ez a forgatókönyv a távoli asztali kapcsolatokkal kapcsolatos ellenőrzések korlátozott készletét biztosítja a kétlépéses ellenőrzés és a feltételes hozzáférés szabályozása számára.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Hogyan illeszkedik az alkalmazásproxy a szabványos RDS-telepítésbe?

A távoli asztali szolgáltatások szabványos központi telepítése a Windows Server rendszeren futó távoli asztali szerepkör-szolgáltatásokat tartalmaz. A [Távoli asztali szolgáltatások architektúráját](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)tekintve több telepítési lehetőség is rendelkezésre áll. Más RDS-telepítési lehetőségekkel ellentétben az [RDS-telepítés az Azure AD alkalmazásproxyval](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (az alábbi ábrán látható) állandó kimenő kapcsolattal rendelkezik az összekötő szolgáltatást futtató kiszolgálóról. Más központi telepítések nyitva hagyják a bejövő kapcsolatokat egy terheléselosztón keresztül.

![Az alkalmazásproxy az RDS virtuális gép és a nyilvános internet között helyezkedik el](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Távoli asztali szolgáltatások központi telepítésében a Távoli asztali webes szerepkör és a Távoli asztali átjáró szerepkör fut az internetre néző gépeken. Ezek a végpontok a következő okok miatt jelennek meg:
- A Távoli asztali web nyilvános végpontot biztosít a felhasználónak a bejelentkezéshez és a különböző helyszíni alkalmazások és asztali számítógépek megtekintéséhez. Egy erőforrás kiválasztásakor egy RDP-kapcsolat jön létre a natív alkalmazás az operációs rendszer használatával.
- A Távoli asztali átjáró akkor kerül képbe, amikor a felhasználó elindítja az RDP-kapcsolatot. A Távoli asztali átjáró kezeli az interneten keresztül érkező titkosított RDP-forgalmat, és lefordítja azt arra a helyszíni kiszolgálóra, amelyhez a felhasználó csatlakozik. Ebben a forgatókönyvben a forgalom a Távoli asztali átjáró fogadása az Azure AD alkalmazásproxy származik.

>[!TIP]
>Ha még nem telepítette az RDS-t, vagy további információra van szüksége a kezdés előtt, ismerje meg, hogyan [telepítheti zökkenőmentesen az RDS-t az Azure Resource Manager rel és az Azure Marketplace-en.](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)

## <a name="requirements"></a>Követelmények

- Ne használja a Távoli asztali webes ügyféltől eltérő ügyfelet, mivel a webes ügyfél nem támogatja az alkalmazásproxyt.

- Mind a Távoli asztali webes, mind a Távoli asztali átjáró végpontjainak ugyanazon a számítógépen kell elhelyezkedniük, és közös gyökérrel kell rendelkezniük. A Távoli asztali web- és Távoli asztali átjáró egyetlen alkalmazásként, alkalmazásproxyval együtt közzétételre kerülnek, így a két alkalmazás között egyetlen bejelentkezési élményben lehet része.

- Már üzembe kellett volna [helyeznie az RDS](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)szolgáltatást, és [engedélyeznie kellett az alkalmazásproxyt.](application-proxy-add-on-premises-application.md)

- Ebben a forgatókönyvben azt feltételezi, hogy a végfelhasználók a Távoli asztali számítógépen keresztül csatlakozó Windows 7 vagy Windows 10 asztali számítógépeken keresztül jutnak el. Ha más operációs rendszereket is támogatnia kell, olvassa el [a Támogatás más ügyfélkonfigurációktámogatása (Támogatás más ügyfélkonfigurációkhoz) (Támogatás más ügyfélkonfigurációkhoz) (Támogatás más ügyfélkonfigurációkhoz) (Támogatás más ügyfélkonfigurációkhoz) témakörben](#support-for-other-client-configurations)

- A Távoli asztali web közzétételekor ajánlott ugyanazt a belső és külső teljes tartománynát használni. Ha a belső és a külső teljes tartományna eltérő, akkor tiltsa le a kérelemfejléc-fordítást, hogy az ügyfél ne kapjon érvénytelen hivatkozásokat. 

- Az Internet Explorer ben engedélyezze az RDS ActiveX bővítményt.

- Az Azure AD előhitelesítési folyamat, a felhasználók csak a RemoteApp és az asztalok ablaktáblában közzétett erőforrásokhoz **csatlakozhatnak.** A felhasználók nem tudnak csatlakozni az asztalhoz a **Csatlakozás távoli számítógéphez** ablaktáblán keresztül.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>A közös RDS- és alkalmazásproxy-forgatókönyv telepítése

Az RDS és az Azure AD alkalmazásproxy beállítása után a környezetben, kövesse a lépéseket a két megoldás egyesítéséhez. Ezek a lépések végigvezetik a két webes rds-végpont (Távoli asztali webes és Távoli asztali átjáró) alkalmazásként történő közzétételén, majd a Távoli asztali szolgáltatások forgalmának az alkalmazásproxyn keresztül történő irányítását.

### <a name="publish-the-rd-host-endpoint"></a>A Távoli asztali üzemeltetje végpontjának közzététele

1. [Új alkalmazásproxy-alkalmazás közzététele](application-proxy-add-on-premises-application.md) a következő értékekkel:
   - Belső `https://\<rdhost\>.com/`URL:, `\<rdhost\>` hol található a Távoli asztali webes és távoli asztali átjáró közös gyökérzete.
   - Külső URL: Ez a mező automatikusan kitöltődik az alkalmazás neve alapján, de módosíthatja. A felhasználók erre az URL-címre lépnek, amikor hozzáférnek az RDS-hez.
   - Előhitelesítési módszer: Azure Active Directory
   - URL-fejlécek fordítása: Nem
2. Felhasználók hozzárendelése a közzétett Távoli asztali alkalmazáshoz. Győződjön meg róla, hogy minden hozzáférhet rds is.
3. Hagyja meg az alkalmazás egyszeri bejelentkezési metódusát, mert az **Azure AD egyszeri bejelentkezése le van tiltva.** A felhasználók nak kell hitelesíteni egyszer az Azure AD és egyszer a Távoli asztali web, de egyszeri bejelentkezés a Távoli asztali átjáró.
4. Válassza az **Azure Active Directory**, majd az **Alkalmazásregisztrációk**lehetőséget. Válassza ki az alkalmazást a listából.
5. A **Kezelés csoportban**válassza a **Márkajelzés**lehetőséget.
6. Frissítse a **kezdőlap URL-mezőjét** úgy, hogy `https://\<rdhost\>.com/RDWeb`a Távoli asztali webes végpontra mutasson (például ).

### <a name="direct-rds-traffic-to-application-proxy"></a>RdS-forgalom közvetlen forgalmaaz alkalmazásproxyhoz

Csatlakozzon a távoli asztali szolgáltatások központi telepítéséhez rendszergazdaként, és módosítsa a központi telepítés Távoli asztali átjárókiszolgálójának nevét. Ez a konfiguráció biztosítja, hogy a kapcsolatok az Azure AD alkalmazásproxy szolgáltatáson keresztül haladjanak.

1. Csatlakozzon a Távoli asztali kapcsolatszervező szerepkört futtató RDS-kiszolgálóhoz.
2. Indítsa el **a Kiszolgálókezelőt**.
3. A bal oldali ablaktáblában válassza a **Távoli asztali szolgáltatások lehetőséget.**
4. Válassza az **Áttekintés** lehetőséget.
5. A Telepítés áttekintése csoportban válassza a legördülő menüt, és válassza a **Telepítési tulajdonságok szerkesztése parancsot.**
6. A Távoli asztali átjáró lapon módosítsa a **Kiszolgáló név** mezőjét az Alkalmazásproxy távoli asztali gazdagép végpontjához beállított külső URL-címre.
7. Módosítsa a **Bejelentkezési metódus** mezőt **Jelszó-hitelesítés**re.

   ![Telepítési tulajdonságok képernyő az RDS-en](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Futtassa ezt a parancsot minden gyűjteményhez. Cserélje * \<le\> agyűjteménynevét* és * \<a\> proxyfrontendurl-t* a saját adataira. Ez a parancs egyszeri bejelentkezést tesz lehetővé a Távoli asztali webes és távoli asztali átjáró között, és optimalizálja a teljesítményt:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Például:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >A fenti parancs a backtick a "'nrequire".

9. Az egyéni RDP-tulajdonságok módosításának ellenőrzéséhez, valamint a gyűjtemény RDWeb szolgáltatásából letöltött RDP-fájl tartalmának megtekintéséhez futtassa a következő parancsot:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Most, hogy konfigurálta a távoli asztalt, az Azure AD alkalmazásproxy átvette az RDS internetfelé néző összetevőjeként. Eltávolíthatja a távoli asztali webes és távoli asztali átjárógépeken lévő többi nyilvános internetfelé néző végpontot.

## <a name="test-the-scenario"></a>A forgatókönyv tesztelése

Tesztelje a forgatókönyvet az Internet Explorer rel Windows 7 vagy 10 rendszerű számítógépen.

1. Nyissa meg a beállított külső URL-címet, vagy keresse meg az alkalmazást a [MyApps panelen.](https://myapps.microsoft.com)
2. A rendszer kéri, hogy hitelesítse magát az Azure Active Directoryban. Az alkalmazáshoz rendelt fiók használata.
3. A program megkéri, hogy hitelesítse magát a Távoli asztali weben.
4. Miután a TÁVOLI asztali hitelesítés sikeres volt, kiválaszthatja a kívánt asztalt vagy alkalmazást, és elkezdheti a munkát.

## <a name="support-for-other-client-configurations"></a>Egyéb ügyfélkonfigurációk támogatása

A cikkben ismertetett konfiguráció a Windows 7 vagy 10 rendszerű felhasználók számára készült, az Internet Explorer és az RDS ActiveX bővítmény segítségével. Szükség esetén azonban más operációs rendszereket vagy böngészőket is támogathat. A különbség a használt hitelesítési módszerben van.

| Hitelesítési módszer | Támogatott ügyfélkonfiguráció |
| --------------------- | ------------------------------ |
| Előhitelesítés    | Windows 7/10 az Internet Explorer + RDS ActiveX bővítmény használatával |
| Áthaladás | Bármely más operációs rendszer, amely támogatja a Microsoft Remote Desktop alkalmazást |

Az előhitelesítési folyamat több biztonsági előnyt kínál, mint az áthaladási folyamat. Az előhitelesítés hez használhatja az Azure AD hitelesítési funkciók, például egyszeri bejelentkezés, feltételes hozzáférés és a helyszíni erőforrások kétlépéses ellenőrzése. Azt is biztosítja, hogy csak a hitelesített forgalom éri el a hálózatot.

Az áteresztő hitelesítés használatához csak két módosítás van a cikkben felsorolt lépésekben:
1. A [Távoli asztali üzemeltetje állomás végpontjának](#publish-the-rd-host-endpoint) **1.**
2. A [Közvetlen RDS-forgalom alkalmazásproxy](#direct-rds-traffic-to-application-proxy), hagyja lépés 8 teljesen.

## <a name="next-steps"></a>További lépések

[Távoli hozzáférés engedélyezése a SharePointhoz az Azure AD-alkalmazásproxy segítségével](application-proxy-integrate-with-sharepoint-server.md)  
[Biztonsági szempontok az alkalmazások távoli eléréséhez az Azure AD alkalmazásproxy használatával](application-proxy-security.md)
