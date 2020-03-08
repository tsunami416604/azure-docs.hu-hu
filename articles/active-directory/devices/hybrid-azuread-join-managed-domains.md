---
title: Hibrid Azure Active Directory csatlakozás konfigurálása a felügyelt tartományokhoz | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat hibrid Azure Active Directory illesztést a felügyelt tartományokhoz.
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
ms.openlocfilehash: 3407214d332cbd333fe019948d254e01d71197fb
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672257"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Oktatóanyag: A hibrid Azure Active Directory-csatlakozás konfigurálása felügyelt tartományokhoz

A szervezetben lévő felhasználóhoz hasonlóan az eszköz a védelemmel ellátni kívánt alapvető identitás. Az eszközök identitásával bármikor és bárhonnan biztosíthatja erőforrásait. Ezt a célt úgy hajthatja végre, hogy az eszköz identitásait és a Azure Active Directory (Azure AD) felügyeletét az alábbi módszerek egyikével látja el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Ha az eszközöket az Azure AD-be szeretné állítani, az egyszeri bejelentkezés (SSO) révén maximalizálja a felhasználók hatékonyságát a felhőben és a helyszíni erőforrásokban. A [feltételes hozzáféréssel](../active-directory-conditional-access-azure-portal.md) a felhőben és a helyszíni erőforrásokhoz is biztonságossá teheti a hozzáférést.

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat hibrid Azure AD-csatlakozást Active Directory tartományhoz csatlakoztatott számítógépek eszközeihez felügyelt környezetben. 

A felügyelt környezetek a [jelszó-kivonatolási szinkronizálással (PHS)](../hybrid/whatis-phs.md) vagy az átmenő [HITELESÍTÉSSEL (PTA ESP)](../hybrid/how-to-connect-pta.md) is üzembe helyezhetők, [zökkenőmentes egyszeri bejelentkezéssel](../hybrid/how-to-connect-sso.md). Ezekhez a forgatókönyvekhez nem szükséges összevonási kiszolgálót konfigurálni a hitelesítéshez.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy már ismeri a következő cikkeket:

- [Mi az az eszköz identitása?](overview.md)
- [Hibrid Azure AD JOIN megvalósításának megtervezése](hybrid-azuread-join-plan.md)
- [Hibrid Azure AD-csatlakozás vezérelt ellenőrzése](hybrid-azuread-join-control.md)

> [!NOTE]
> Az Azure AD nem támogatja a felügyelt tartományokban található intelligens kártyák vagy tanúsítványok használatát.

A jelen cikkben szereplő forgatókönyv konfigurálásához a Azure AD Connect (1.1.819.0 vagy újabb) [legújabb verziójára](https://www.microsoft.com/download/details.aspx?id=47594) van szükség.

Ellenőrizze, hogy a Azure AD Connect szinkronizálta-e azon eszközök számítógép-objektumait, amelyeket hibrid Azure AD-hez csatlakoztatni szeretne az Azure AD-hez. Ha a számítógép-objektumok meghatározott szervezeti egységekhez (OU-hoz) tartoznak, akkor a szervezeti egységeket is konfigurálnia kell Azure AD Connectban való szinkronizáláshoz. Ha többet szeretne megtudni a számítógép-objektumok Azure AD Connect használatával történő szinkronizálásáról, tekintse meg a [szűrés konfigurálása Azure ad Connect használatával](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)című témakört.

A 1.1.819.0 verziótól kezdődően a Azure AD Connect tartalmaz egy varázslót, amely használatával konfigurálhatja a hibrid Azure AD Joint. A varázsló jelentősen leegyszerűsíti a konfigurációs folyamatot. A varázsló konfigurálja az eszközök regisztrálásához szükséges szolgáltatáskapcsolódási pontokat.

A jelen cikkben szereplő konfigurációs lépések a varázsló Azure AD Connect-ben való használatán alapulnak.

A hibrid Azure AD-csatlakozáshoz az eszközöknek a szervezet hálózatán belüli alábbi Microsoft-erőforrásokhoz való hozzáférésre van szükségük:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (ha a vagy a-t használja, és nem szeretné használni a zökkenőmentes SSO-t)

Ha a szervezete egy kimenő proxyn keresztül fér hozzá az internethez, a Microsoft a [webproxy automatikus felderítésének (WPAD) megvalósítását](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) javasolja a Windows 10-es számítógépek Azure ad-vel való regisztrációjának engedélyezéséhez. Ha a WPAD konfigurálásával és kezelésével kapcsolatos problémákat tapasztal, tekintse meg az [automatikus észlelés hibaelhárítása](/previous-versions/tn-archive/cc302643(v=technet.10))című témakört. 

Ha nem használja a WPAD-t, és konfigurálnia kell a proxybeállításokat a számítógépen, a Windows 10 1709-es verziójától kezdve is megteheti. További információ: [WinHTTP-beállítások konfigurálása csoportházirend-objektummal (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Ha a proxybeállításokat a WinHTTP-beállítások használatával konfigurálja a számítógépen, a konfigurált proxyhoz nem csatlakoztatható számítógépek nem fognak csatlakozni az internethez.

Ha a szervezete hitelesített kimenő proxyn keresztül fér hozzá az internethez, meg kell győződnie arról, hogy a Windows 10 rendszerű számítógépek sikeresen hitelesíteni tudják a kimenő proxyt. Mivel a Windows 10 rendszerű számítógépek gépi környezettel futtatják az eszközök regisztrációját, a kimenő proxy hitelesítését a gépi környezet használatával kell konfigurálni. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

Annak ellenőrzéséhez, hogy az eszköz képes-e hozzáférni a fenti Microsoft-erőforrásokhoz a rendszerfiókban, használhatja a [teszt eszköz regisztrációjának kapcsolati](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) parancsfájlját.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

A hibrid Azure AD-csatlakozások Azure AD Connecttel való konfigurálásához a következők szükségesek:

- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatai
- Az egyes erdők vállalati rendszergazdai hitelesítő adatai

**Hibrid Azure AD-csatlakozás konfigurálása Azure AD Connect használatával:**

1. Indítsa el Azure AD Connect, majd válassza a **Konfigurálás**lehetőséget.

   ![Üdvözlőképernyő](./media/hybrid-azuread-join-managed-domains/11.png)

1. A **További feladatok** lapon válassza az **eszközbeállítások konfigurálása**lehetőséget, majd kattintson a **tovább**gombra.

   ![További feladatok](./media/hybrid-azuread-join-managed-domains/12.png)

1. Az **Áttekintés** lapon válassza a **tovább**lehetőséget.

   ![Áttekintés](./media/hybrid-azuread-join-managed-domains/13.png)

1. A **Csatlakozás az Azure AD szolgáltatáshoz** oldalon adja meg az Azure AD-bérlőhöz tartozó globális rendszergazdai hitelesítő adatokat.  

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-managed-domains/14.png)

1. Az **eszközbeállítások** lapon válassza a **hibrid Azure ad-csatlakozás konfigurálása**lehetőséget, majd kattintson a **tovább**gombra.

   ![Eszközbeállítások](./media/hybrid-azuread-join-managed-domains/15.png)

1. Az **SCP** lapon minden olyan erdőhöz, ahol Azure ad Connect szeretné KONFIGURÁLNI az scp-t, hajtsa végre az alábbi lépéseket, majd kattintson a **tovább**gombra:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Válassza ki az erdőt.
   1. Válassza ki a hitelesítési szolgáltatást.
   1. Válassza a **Hozzáadás** lehetőséget a vállalati rendszergazda hitelesítő adatainak megadásához.

1. Az **eszköz operációs rendszerek** lapján válassza ki azokat az operációs rendszereket, amelyeket az Active Directory környezetében lévő eszközök használnak, majd válassza a **tovább**lehetőséget.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-managed-domains/17.png)

1. A **konfigurálásra kész** lapon válassza a **Konfigurálás**lehetőséget.

   ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-managed-domains/19.png)

1. A **konfiguráció kész** lapon válassza a **Kilépés**lehetőséget.

   ![A konfigurálás befejeződött](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Régebbi Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök némelyike Windows régebbi verziójú eszközök, a következőket kell tennie:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- Zökkenőmentes SSO konfigurálása
- A Microsoft Workplace Join telepítése régebbi Windows rendszerű számítógépekhez

> [!NOTE]
> A Windows 7 támogatása 2020 január 14-én fejeződött be. További információ: [a Windows 7 támogatása befejeződött](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

A Windows régebbi verziójú eszközök hibrid Azure AD-csatlakozásának sikeres elvégzéséhez, valamint a tanúsítványok az Azure AD-be való hitelesítésének elkerülése érdekében leküldheti a szabályzatot a tartományhoz csatlakoztatott eszközökre, hogy hozzáadja a következő URL-címeket a helyi intranetes zónához az interneten Explorer

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Emellett engedélyeznie kell a **frissítések állapotát az állapotsoron** a felhasználó helyi intranet zónájában lévő parancsfájl használatával.

### <a name="configure-seamless-sso"></a>Zökkenőmentes SSO konfigurálása

Ha az Azure AD felhőalapú hitelesítési módszerként [PHS](../hybrid/whatis-phs.md) vagy [PTA](../hybrid/how-to-connect-pta.md) -t használó felügyelt tartományban a Windows régebbi VERZIÓJÚ eszközök hibrid Azure ad-csatlakozását szeretné sikeresen befejezni, akkor a [zökkenőmentes egyszeri bejelentkezést is konfigurálnia](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)kell.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>A Microsoft Workplace Join telepítése régebbi Windows rendszerű számítógépekhez

A Windows régebbi verziójú eszközök regisztrálásához a szervezeteknek telepíteniük kell [a Microsoft Workplace Joint a nem Windows 10 rendszerű számítógépekre](https://www.microsoft.com/download/details.aspx?id=53554). A nem Windows 10 rendszerű számítógépekhez készült Microsoft Workplace Join a Microsoft letöltőközpontban érhető el.

A csomagot központilag telepítheti egy szoftverterjesztési rendszer, például a [Microsoft Endpoint Configuration Manager](/configmgr/)használatával. A csomag támogatja a szabványos csendes telepítési beállításokat a `quiet` paraméterrel. A Configuration Manager aktuális ága a korábbi verziók előnyeit kínálja, mint például a befejezett regisztrációk nyomon követésének lehetősége.

A telepítő létrehoz egy ütemezett feladatot a felhasználói környezetben futó rendszeren. A feladat akkor aktiválódik, amikor a felhasználó bejelentkezik a Windowsba. A feladat csendesen csatlakoztatja az eszközt az Azure AD-vel az Azure AD-vel végzett hitelesítés után a felhasználói hitelesítő adatok használatával.

## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Az Azure-bérlő eszköz-regisztrációs állapotának ellenőrzéséhez használhatja a **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** parancsmagot a [Azure Active Directory PowerShell-modulban](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ha a **Get-MSolDevice** parancsmagot használja a szolgáltatás részleteinek megtekintéséhez:

- Léteznie kell egy olyan objektumnak **, amely megfelel a Windows** -ügyfél azonosítójának.
- A **DeviceTrustType** beállítás értékének **tartományhoz csatlakoztatottnak** kell lennie. Ez a beállítás megegyezik a **hibrid Azure ad-hez csatlakoztatott** állapottal az Azure ad-portál **eszközök** lapján.
- A feltételes hozzáférésben használt eszközök esetében az **engedélyezett** értéknek **igaznak** kell lennie, és a **DeviceTrustLevel** kell **kezelnie**.

**A szolgáltatás részleteinek ellenõrzése**:

1. Nyissa meg a Windows PowerShellt rendszergazdaként.
1. Adja meg `Connect-MsolService` az Azure-bérlőhöz való kapcsolódáshoz.  
1. Írja be a `get-msoldevice -deviceId <deviceId>` (igen) kifejezést.
1. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásának befejezésével kapcsolatos problémákat tapasztal, tekintse meg a következőt:

- [Hibrid Azure AD-csatlakozás a Windows aktuális eszközeihez – problémamegoldás](troubleshoot-hybrid-join-windows-current.md)
- [A hibrid Azure AD JOIN használata a Windows régebbi verziójú eszközökhöz](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [kezelheti az eszközök identitásait a Azure Portal használatával](device-management-azure-portal.md).
