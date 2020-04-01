---
title: Hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt tartományokhoz | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakozás felügyelt tartományokhoz.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239111"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Oktatóanyag: A hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt tartományokhoz

Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory (Azure AD) csatlakozást az Active Directoryhoz tartományhoz csatlakozó eszközökhöz. Ez a módszer támogatja a felügyelt környezet, amely magában foglalja a helyszíni Active Directory és az Azure AD.

A szervezet egyik felhasználójához hasonlóan az eszköz is egy alapvető identitás, amelyet védeni szeretne. Az eszköz identitásával bármikor és bárhol megvédheti az erőforrásokat. Ezt a célt az Azure AD-ben az eszközidentitások kezelésével érheti el. Használja az alábbi módszerek egyikét:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Ez a cikk a hibrid Azure AD-csatlakozásra összpontosít.

Az eszközök Azure AD-be való bejuttatása maximalizálja a felhasználók hatékonyságát egyszeri bejelentkezéssel (SSO) a felhőben és a helyszíni erőforrásokban. A [feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md) egyszerre biztosíthatja a felhőbeli és a helyszíni erőforrásokhoz való hozzáférést.

Felügyelt környezetet [jelszókivonat-szinkronizálás (PHS)](../hybrid/whatis-phs.md) vagy [átmenő hitelesítéssel (PTA)](../hybrid/how-to-connect-pta.md) helyezhet üzembe [zökkenőmentes egyszeri bejelentkezéssel.](../hybrid/how-to-connect-sso.md) Ezek a forgatókönyvek nem igénylik az összevonási kiszolgáló hitelesítésre való konfigurálását.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 vagy újabb)
- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatai
- Az egyes erdők vállalati rendszergazdai hitelesítő adatai

Ismerkedjen meg ezekkel a cikkekkel:

- [Mi az az eszközidentitás?](overview.md)
- [Útmutató: A hibrid Azure Active Directory-csatlakozás megvalósításának megtervezése](hybrid-azuread-join-plan.md)
- [Hibrid Azure AD-csatlakozás szabályozott ellenőrzése](hybrid-azuread-join-control.md)

> [!NOTE]
> Az Azure AD nem támogatja az intelligens kártyákat vagy tanúsítványokat a felügyelt tartományokban.

Ellenőrizze, hogy az Azure AD Connect szinkronizálta-e az on-di azon eszközök számítógép-objektumait, amelyekhibrid Azure AD-t szeretnék csatlakoztatni az Azure AD-hez. Ha a számítógép-objektumok adott szervezeti egységekhez (Szervezeti egységekhez) tartoznak, konfigurálja az oUs-okat az Azure AD Connect szinkronizálásához. Ha többet szeretne tudni arról, hogyan szinkronizálhatja a számítógép-objektumokat az Azure AD Connect használatával, olvassa el a [Szervezeti egység alapú szűrés című témakört.](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)

Az 1.1.819.0-s verziótól kezdve az Azure AD Connect tartalmaz egy varázslót a hibrid Azure AD-csatlakozás konfigurálásához. A varázsló jelentősen leegyszerűsíti a konfigurációs folyamatot. A varázsló konfigurálja a szolgáltatás csatlakozási pontjait (SPS) az eszköz regisztrációjára.

A jelen cikkkonfigurációs lépések az Azure AD Connect varázsló használatán alapulnak.

A hibrid Azure AD-csatlakozáshoz az eszközöknek a szervezet hálózatán belül a következő Microsoft-erőforrásokhoz kell hozzáférniük:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Ha zökkenőmentes egyszeri bejelentkezést használ vagy tervez használni)

Ha a szervezetnek egy kimenő proxyn keresztül internet-hozzáférésre van szüksége, javasoljuk, hogy [a Web Proxy Auto-Discovery (WPAD) implementálása](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) engedélyezze a Windows 10-es számítógépeket az azure-beli AD-vel való eszközregisztrációhoz. A WPAD konfigurálásával és kezelésével kapcsolatos problémák megoldásáról az [Automatikus észlelés hibaelhárítása című témakörben nyújt témakört.](/previous-versions/tn-archive/cc302643(v=technet.10))

Ha nem használja a WPAD-et, a Windows 10 1709-es rendszerrel kezdődően konfigurálhatja a proxybeállításokat a számítógépen. További információt a [Csoportházirend-csoport házirend-csoport házirend-szervezet által telepített WinHTTP-proxybeállítások](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)című témakörben talál.

> [!NOTE]
> Ha a számítógép proxybeállításait A WinHTTP-beállításokkal adja meg, a konfigurált proxyhoz nem csatlakozó számítógépek nem tudnak csatlakozni az internethez.

Ha a szervezetnek egy hitelesített kimenő proxyn keresztül kell hozzáférnie az internethez, győződjön meg arról, hogy a Windows 10-es számítógépek sikeresen hitelesíthetik magukat a kimenő proxyn. Mivel a Windows 10-es számítógépek számítógépkörnyezethasználatával futtatják az eszközregisztrációt, konfigurálják a kimenő proxyhitelesítést a számítógép környezetében. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

Ellenőrizze, hogy az eszköz hozzáférhet-e a fenti Microsoft-erőforrásokhoz a rendszerfiók alatt a [Test Device Registration Connectivity](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) parancsfájl használatával.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

Hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect használatával:

1. Indítsa el az Azure AD Connect et, és válassza **a Konfigurálás**lehetőséget.

   ![Üdvözlőképernyő](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. A **További feladatok párbeszédpanelen**válassza **az Eszközbeállítások konfigurálása**lehetőséget, majd a **Tovább**gombot.

   ![További feladatok](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Az **Áttekintés csoportban**válassza a **Tovább**lehetőséget.

   ![Áttekintés](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. A **Csatlakozás az Azure AD-hez**alkalmazásban adja meg az Azure AD-bérlő globális rendszergazdájának hitelesítő adatait.  

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. Az **Eszközbeállítások párbeszédpanelen**válassza **a Hibrid Azure AD-csatlakozás konfigurálása**lehetőséget, majd a **Tovább**gombot.

   ![Eszközbeállítások](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Az **SCP-konfigurációban**minden olyan erdőesetében, ahol az Azure AD Connect konfigurálni szeretné az SCP-t, hajtsa végre a következő lépéseket, majd válassza a **Tovább**gombot.

   1. Válassza ki az **Erdő**lehetőséget.
   1. Válasszon **egy hitelesítési szolgáltatást**.
   1. A Vállalati rendszergazdai hitelesítő adatok megadásához válassza a **Hozzáadás** lehetőséget.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Az **Eszközoperációs rendszerekben**jelölje ki az okat az operációs rendszereket, amelyeket az Active Directory-környezetben az eszközök használnak, majd kattintson a **Tovább**gombra.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. A **Konfigurálás kész területen**válassza a **Konfigurálás**lehetőséget.

   ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. A **Konfiguráció befejeződése csoportban**válassza **a Kilépés**lehetőséget.

   ![A konfigurálás befejeződött](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakozó eszközök némelyike windowsos lecsökkentő szintű eszköz, a következőket kell tennie:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- Zökkenőmentes egyszeri bejelentkezés konfigurálása
- A Microsoft Workplace Join telepítése a Windows down-level számítógépekhez

> [!NOTE]
> A Windows 7 támogatása 2020. További információt a [Windows 7 támogatásának vége című témakörben talál.](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

A Windows-alapú alsószintű eszközök hibrid Azure AD-csatlakozásának befejezéséhez és a tanúsítványkérések elkerülése érdekében, amikor az eszközök hitelesítése az Azure AD-re, leküldéses házirendet a tartományhoz csatlakozó eszközökre, hogy a következő URL-címeket adja hozzá a helyi intranetzónához az Internet Explorerben:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

A frissítések **engedélyezése az állapotsorhoz parancsfájlon keresztül** is engedélyeznie kell a felhasználó helyi intranetzónájában.

### <a name="configure-seamless-sso"></a>Zökkenőmentes egyszeri bejelentkezés konfigurálása

A Windows-alapú lepusztult eszközök hibrid Azure AD-csatlakozásának befejezéséhez egy olyan felügyelt tartományban, amely [jelszókivonat-szinkronizálást](../hybrid/whatis-phs.md) vagy [átadó hitelesítést](../hybrid/how-to-connect-pta.md) használ Az Azure AD felhőalapú hitelesítési módszerként, zökkenőmentes egyszeri bejelentkezést is [konfigurálnia](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)kell.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>A Microsoft Workplace Join telepítése a Windows down-level számítógépekhez

A Windows-alapú rendszerű eszközök regisztrálásához a szervezeteknek telepíteniük kell [a Microsoft Workplace Join programot nem Windows 10 rendszerű számítógépekre.](https://www.microsoft.com/download/details.aspx?id=53554) A Microsoft Workplace Join nem Windows 10 rendszerű számítógépekhez a Microsoft letöltőközpontban érhető el.

A csomagot a [Microsoft Endpoint Configuration Manager](/configmgr/)hez hasonló szoftverelosztó rendszerrel telepítheti. A csomag támogatja a szabványos csendes `quiet` telepítési lehetőségeket a paraméterrel. A Configuration Manager jelenlegi verziója a korábbi verziókkal szemben kínál előnyöket, például a befejezett regisztrációk nyomon követését.

A telepítő létrehoz egy ütemezett feladatot a rendszeren, amely a felhasználói környezetben fut. A feladat akkor indul el, amikor a felhasználó bejelentkezik a Windows rendszerbe. A feladat csendben csatlakozik az eszközhöz az Azure AD-vel a felhasználói hitelesítő adatok használatával, miután hitelesíti az Azure AD-vel.

## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Ellenőrizze az eszköz regisztrációs állapotát az Azure-bérlőben a **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** használatával. Ez a parancsmag az [Azure Active Directory PowerShell-modulban](/powershell/azure/install-msonlinev1?view=azureadps-2.0)található.

Ha a **Get-MSolDevice** parancsmag segítségével ellenőrzi a szolgáltatás részleteit:

- Léteznie kell egy olyan objektumnak, amelynek **eszközazonosítója** megegyezik a Windows-ügyfél azonosítójával.
- A **DeviceTrustType** értéke **tartományhoz csatlakozott**. Ez a beállítás megegyezik a **hibrid Azure AD-hez csatlakozott** állapot az Azure AD portál **eszközök** lapján.
- A feltételes hozzáférésben használt eszközök esetében az **Engedélyezve érték** **igaz,** a **DeviceTrustLevel** pedig **a Rendszer kezelhető.**

A szolgáltatás részleteinek ellenőrzése:

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
1. Adja `Connect-MsolService` meg az Azure-bérlőhöz való csatlakozáshoz.  
1. Írja be a `get-msoldevice -deviceId <deviceId>` (igen) kifejezést.
1. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha a tartományhoz csatlakozó Windows-eszközökhöz való hibrid Azure AD-csatlakozással kapcsolatos problémákat tapasztal, olvassa el a következő témakört:

- [Hibrid Azure Active Directory-csatlakozású eszközök hibáinak elhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Hibrid Azure Active Directory-csatlakozás oka az alulról nem vett eszközökre](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

A következő cikkre lépve megtudhatja, hogyan kezelheti az eszközidentitásokat az Azure Portal használatával.
> [!div class="nextstepaction"]
> [Eszközidentitások kezelése](device-management-azure-portal.md)
