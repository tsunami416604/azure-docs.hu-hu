---
title: Hibrid Azure Active Directory-csatlakozás konfigurálása összevont tartományokhoz | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakozást az összevont tartományokhoz.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a61c89199c89f09b5cc0e553dbbf48655ad1b6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239097"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>A hibrid Azure Active Directory-csatlakozás konfigurálása összevont tartományokhoz

A szervezet egyik felhasználójához hasonlóan az eszköz is egy alapvető identitás, amelyet védeni szeretne. Az eszköz identitásával bármikor és bárhol megvédheti az erőforrásokat. Ezt a célt úgy érheti el, hogy eszközidentitásokat hoz létre, és kezeli őket az Azure Active Directoryban (Azure AD) az alábbi módszerek egyikével:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az eszközök Azure AD-be való bejuttatása maximalizálja a felhasználók hatékonyságát egyszeri bejelentkezéssel (SSO) a felhőben és a helyszíni erőforrásokban. A [feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md) egyszerre biztosíthatja a felhőbeli és a helyszíni erőforrásokhoz való hozzáférést.

Az összevont környezetben rendelkeznie kell egy identitásszolgáltató, amely támogatja a következő követelményeket. Ha az Active Directory összevonási szolgáltatások (AD FS) használatával összevont környezetet használ, akkor az alábbi követelmények már támogatottak.

- **WIAORMULTIAUTHN állítás:** Ez a jogcím a hibrid Azure AD-csatlakozás hoz a Windows down-level eszközök.
- **WS-Trust protokoll:** Ez a protokoll a Windows aktuális hibrid Azure AD-hez csatlakozott eszközeinek az Azure AD-vel való hitelesítéséhez szükséges.
  AD FS használata esetén a következő WS-Trust végpontokat kell engedélyeznie:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Mind **az adfs/services/trust/2005/windowstransport,** mind **az adfs/services/trust/13/windowstransport** csak intranetes végpontként engedélyezhető, és NEM lehet extranetes végpontként kitenni a webalkalmazás-proxyn keresztül. A WS-Trust Windows végpontok letiltásáról a [WS-Trust Windows végpontok letiltása a proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)című témakörben olvashat bővebben. A > **Szolgáltatásvégpontok**csoportban láthatja, hogy az **Service**AD FS felügyeleti konzolon keresztül milyen végpontok vannak engedélyezve.

Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja a hibrid Azure AD-csatlakozást az Active Directoryhoz tartományhoz csatlakozott számítógépek eszközeihez összevont környezetben az AD FS használatával.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * Windows-lepusztult eszközök engedélyezése
> * A regisztráció ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy ismeri ezeket a cikkeket:

- [Mi az az eszközidentitás?](overview.md)
- [A hibrid Azure AD-csatlakozási megvalósítás megtervezése](hybrid-azuread-join-plan.md)
- [A hibrid Azure AD-csatlakozás ellenőrzött érvényesítése](hybrid-azuread-join-control.md)

Az oktatóanyagban ismertetett forgatókönyvhöz az alábbiakra van szükség:

- Windows Server 2012 R2 és AD FS
- [Az Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0-s vagy újabb verziója

Az 1.1.819.0-s verziótól kezdve az Azure AD Connect tartalmaz egy varázslót, amely a hibrid Azure AD-csatlakozás konfigurálásához használható. A varázsló jelentősen leegyszerűsíti a konfigurációs folyamatot. A kapcsolódó varázsló:

- A szolgáltatáscsatlakozási pontok (SC-k) konfigurálása az eszköz regisztrációhoz
- Igazolja a meglévő Azure AD-beli függő entitás megbízhatóságát
- Frissíti az Azure AD megbízhatósági jogcímszabályait

A jelen cikkkonfigurációs lépések az Azure AD Connect varázsló használatán alapulnak. Ha az Azure AD Connect egy korábbi verziója van telepítve, a varázsló használatához frissítenie kell azt 1.1.819-es vagy újabb verzióra. Ha az Azure AD Connect legújabb verziójának telepítése nem lehetséges, olvassa el, [hogyan konfigurálhatja manuálisan a hibrid Azure AD-csatlakozást.](hybrid-azuread-join-manual.md)

A hibrid Azure AD-csatlakozáshoz az eszközöknek a szervezet hálózatán belül a következő Microsoft-erőforrásokhoz kell hozzáférniük:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- A szervezet biztonsági jogkivonat-szolgáltatása (STS) (összevont tartományok hoz)
- `https://autologon.microsoftazuread-sso.com`(Ha zökkenőmentes egyszeri bejelentkezést használ vagy tervez használni)

A Windows 10 1803-tól kezdve, ha a pillanatnyi hibrid Azure AD-csatlakozás egy összevont környezethez az AD FS használatával sikertelen, az Azure AD Connect-re támaszkodunk a számítógép-objektum szinkronizálásához az Azure AD-ben, amelyet később a hibrid Azure AD-csatlakozás eszközregisztrációjának befejezéséhez használnak. Ellenőrizze, hogy az Azure AD Connect szinkronizálta-e az on-di azon eszközök számítógép-objektumait, amelyekhibrid Azure AD-t szeretnék csatlakoztatni az Azure AD-hez. Ha a számítógép-objektumok adott szervezeti egységekhez (Szervezeti egységekhez) tartoznak, konfigurálnia kell az oUs-okat az Azure AD Connect szinkronizálásához. Ha többet szeretne tudni arról, hogyan szinkronizálhatja a számítógép-objektumokat az Azure AD Connect használatával, olvassa el a [Szűrés konfigurálása az Azure AD Connect használatával](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)című témakört.

Ha a szervezetnek egy kimenő proxyn keresztül internet-hozzáférésre van szüksége, a Microsoft azt javasolja, hogy [a Web Proxy Auto-Discovery (WPAD) implementálása](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) engedélyezze a Windows 10-es számítógépeket az azure-beli AD-vel való eszközregisztrációhoz. Ha problémákat tapasztal a WPAD konfigurálásával és kezelésével kapcsolatban, olvassa el az [Automatikus észlelés – hibaelhárítás című témakört.](/previous-versions/tn-archive/cc302643(v=technet.10)) 

Ha nem használja a WPAD-et, és proxybeállításokat szeretne konfigurálni a számítógépen, ezt a Windows 10 1709 rendszerrel kezdődően teheti meg. További információt a [WinHTTP-beállítások konfigurálása csoportházirend-objektummal című témakörben talál.](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)

> [!NOTE]
> Ha a számítógép proxybeállításait A WinHTTP-beállításokkal adja meg, a konfigurált proxyhoz nem csatlakozó számítógépek nem tudnak csatlakozni az internethez.

Ha a szervezetnek egy hitelesített kimenő proxyn keresztül kell hozzáférnie az internethez, győződjön meg arról, hogy a Windows 10-es számítógépek sikeresen hitelesíthetik magukat a kimenő proxyn. Mivel a Windows 10-es számítógépek számítógépkörnyezethasználatával futtatják az eszközregisztrációt, a kimenő proxyhitelesítést a számítógép környezetében kell konfigurálni. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

Annak ellenőrzéséhez, hogy az eszköz hozzá tud-e férni a fenti Microsoft-erőforrásokhoz a rendszerfiók alatt, használhatja a [Test Device Registration Connectivity](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) parancsfájlt.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

A hibrid Azure AD-csatlakozás konfigurálásához az Azure AD Connect használatával a következőkre van szükség:

- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatai  
- Az egyes erdők vállalati rendszergazdai hitelesítő adatai
- Az AD FS-rendszergazda hitelesítő adatai

**Hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect használatával:**

1. Indítsa el az Azure AD Connect et, és válassza **a Konfigurálás**lehetőséget.

   ![Üdvözlőképernyő](./media/hybrid-azuread-join-federated-domains/11.png)

1. A **További feladatok** lapon válassza az **Eszközbeállítások konfigurálása**lehetőséget, majd a **Tovább**gombot.

   ![További feladatok](./media/hybrid-azuread-join-federated-domains/12.png)

1. Az **Áttekintés** lapon válassza a **Tovább lehetőséget.**

   ![Áttekintés](./media/hybrid-azuread-join-federated-domains/13.png)

1. A Csatlakozás az **Azure AD-hez** lapon adja meg az Azure AD-bérlő globális rendszergazdájának hitelesítő adatait, majd válassza a **Tovább**gombot.

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-federated-domains/14.png)

1. Az **Eszközbeállítások** lapon válassza a **Hibrid Azure AD-csatlakozás konfigurálása**lehetőséget, majd a **Tovább**gombot.

   ![Eszközbeállítások](./media/hybrid-azuread-join-federated-domains/15.png)

1. Az **SCP** lapon hajtsa végre a következő lépéseket, majd válassza a **Tovább**gombot:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Válassza ki az erdőt.
   1. Válassza ki a hitelesítési szolgáltatást. Az **AD FS-kiszolgálót** csak akkor válassza, ha a szervezet kizárólag Windows 10-ügyfelekkel rendelkezik, és konfigurálta a számítógép/eszköz szinkronizálását, vagy ha a szervezet zökkenőmentes egyszeri bejelentkezést használ.
   1. A Vállalati rendszergazdai hitelesítő adatok megadásához válassza a **Hozzáadás** lehetőséget.

1. Az **Eszköz operációs rendszerek** lapon jelölje ki az okat az operációs rendszereket, amelyeket az Active Directory-környezetben lévő eszközök használnak, majd kattintson a **Tovább**gombra.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-federated-domains/17.png)

1. Az **Összevonás konfigurációja** lapon adja meg az AD FS-rendszergazda hitelesítő adatait, majd válassza a **Tovább**gombot.

   ![Összevonás konfigurálása](./media/hybrid-azuread-join-federated-domains/18.png)

1. A **Konfigurálásra kész** lapon válassza a **Konfigurálás**lehetőséget.

   ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-federated-domains/19.png)

1. A **Konfiguráció befejeződése** lapon válassza a **Kilépés**lehetőséget.

   ![A konfigurálás befejeződött](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows-lepusztult eszközök engedélyezése

Ha a tartományhoz csatlakozó eszközök némelyike lecsökkentablakokból álló Windows-eszköz, a következőket kell tennie:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- A Microsoft Workplace Join telepítése windowsos downlevel számítógépekhez

> [!NOTE]
> A Windows 7 támogatása 2020. További információért [a Windows 7 támogatása véget ért.](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

A Windows lecsökkenteszközök hibrid Azure AD-csatlakozásának sikeres befejezéséhez és a tanúsítványkérések elkerülése érdekében, amikor az eszközök hitelesítése az Azure AD-re, leküldéses szabályzatot a tartományhoz csatlakozó eszközökre, hogy a következő URL-címeket adja hozzá a helyi intranetzónához az Internet Explorerben:

- `https://device.login.microsoftonline.com`
- A szervezet STS-e (összevont tartományok hoz)
- `https://autologon.microsoftazuread-sso.com`(A zökkenőmentes Egyszeri bejelentkezéshez)

A frissítések **engedélyezése az állapotsorhoz parancsfájlon keresztül** is engedélyeznie kell a felhasználó helyi intranetzónájában.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>A Microsoft Workplace Join telepítése windowsos downlevel számítógépekhez

A Windows lepusztult eszközök regisztrálásához a szervezeteknek telepíteniük kell [a Microsoft Workplace Join programot nem Windows 10 rendszerű számítógépekre.](https://www.microsoft.com/download/details.aspx?id=53554) A Microsoft Workplace Join nem Windows 10 rendszerű számítógépekhez a Microsoft letöltőközpontban érhető el.

A csomagot a [Microsoft Endpoint Configuration Manager](/configmgr/)hez hasonló szoftverelosztó rendszerrel telepítheti. A csomag támogatja a szabványos csendes `quiet` telepítési lehetőségeket a paraméterrel. A Configuration Manager jelenlegi ága előnyöket kínál a korábbi verziókhoz képest, például a befejezett regisztrációk nyomon követésére.

A telepítő létrehoz egy ütemezett feladatot a rendszeren, amely a felhasználói környezetben fut. A feladat akkor indul el, amikor a felhasználó bejelentkezik a Windows rendszerbe. A feladat csendben csatlakozik az eszközhöz az Azure AD-vel a felhasználói hitelesítő adatok használatával, miután hitelesíti az Azure AD-vel.

## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Az eszköznek az Azure-bérlőbeli regisztrációs állapotát a **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** parancsmaggal lehet ellenőrizni az [Azure Active Directory PowerShell-modulban](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ha a **Get-MSolDevice** parancsmag segítségével ellenőrzi a szolgáltatás részleteit:

- Léteznie kell egy olyan objektumnak, amelynek **eszközazonosítója** megegyezik a Windows-ügyfél azonosítójával.
- A **DeviceTrustType** beállítás értékének **tartományhoz csatlakoztatottnak** kell lennie. Ez a beállítás megegyezik a **hibrid Azure AD-hez csatlakozott** állapot az Azure AD-portálon **eszközök** alatt.
- A feltételes hozzáférésben használt eszközök esetében az **Engedélyezve** értékértéknek **Igaznak,** a **DeviceTrustLevel** értéknek pedig **kezelhetőnek**kell lennie.

**A szolgáltatás részleteinek ellenőrzése:**

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
1. Adja `Connect-MsolService` meg az Azure-bérlőhöz való csatlakozáshoz.  
1. Írja be a `get-msoldevice -deviceId <deviceId>` (igen) kifejezést.
1. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha problémákat tapasztal a tartományhoz csatlakozó Windows-eszközökhibrid Azure AD-csatlakozásával kapcsolatban, olvassa el a következő témakört:

- [Hibrid Azure AD-csatlakozás a Windows aktuális eszközeihez – problémamegoldás](troubleshoot-hybrid-join-windows-current.md)
- [Hibrid Azure AD-csatlakozás a Windows lecsökkenteszközökhöz – problémamegoldás](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [kezelheti az eszközidentitásokat az Azure Portal használatával.](device-management-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
