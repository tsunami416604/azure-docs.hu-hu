---
title: Hibrid Azure Active Directory-csatlakozás összevont tartományok konfigurálása |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directory-csatlakozás összevont tartományokban.
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
ms.openlocfilehash: 738b4f47054081f0fb1b1a530bdf21cbf07a7726
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204699"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Oktatóanyag: Hibrid Azure Active Directory-csatlakozás összevont tartományok konfigurálása

A felhasználó a szervezetben, mint például egy eszköz egy védeni kívánt core identitás. Használhatja egy eszközidentitást az erőforrások védelméhez, bármikor és bárhonnan. Ezt a célt és eszközidentitások és kezelnie azokat az Azure Active Directoryban (Azure AD) az alábbi módszerek egyikének használatával végezheti el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az eszközöket az Azure AD felhasználói termelékenység egyszeri bejelentkezés (SSO) a lehető legnagyobbra növeli a felhőalapú és helyszíni erőforrások. A felhőbeli és helyszíni erőforrásokhoz való hozzáférés gondoskodhat [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md) egyszerre.

Ebben az oktatóanyagban megismerheti, hogyan Active Directory-tartományhoz csatlakoztatott számítógépeket eszközök hibrid Azure AD-csatlakozás konfigurálása összevont környezetben az Active Directory összevonási szolgáltatások (AD FS) használatával.

> [!NOTE]
> Ha az összevont környezetekben eltérő Active Directory összevonási szolgáltatások az identitásszolgáltató használja, biztosítania kell, hogy az identitásszolgáltató támogatja-e a WS-Trust protokollal. WS-Trust kötelező hitelesítéséhez a Windows jelenlegi hibrid Azure AD-hez csatlakoztatott eszközök Azure AD-val. Ha a Windows korábbi verziójú eszközök hibrid Azure AD-csatlakozás kívánt, az identitásszolgáltató támogatnia kell a WIAORMULTIAUTHN jogcímet. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * Windows korábbi verziójú eszközök engedélyezése
> * A regisztráció ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy you'e ismeri a következő cikkeket:

- [Mi az eszközidentitás?](overview.md)
- [A hibrid Azure AD join megvalósítás tervezése](hybrid-azuread-join-plan.md)
- [Hibrid Azure AD JOIN szabályozott ellenőrzés módjáról.](hybrid-azuread-join-control.md)

Az oktatóanyagban ismertetett forgatókönyvhöz az alábbiakra van szükség:

- Windows Server 2012 R2 és AD FS
- [Az Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 verzió vagy újabb

1\.1.819.0 verzióval kezdve az Azure AD Connect varázslót tartalmaz, amely segítségével hibrid Azure AD-csatlakozás konfigurálása. A varázsló jelentősen leegyszerűsíti a konfigurációs folyamat. A kapcsolódó varázsló:

- Konfigurálja a szolgáltatás kapcsolódási pontok (SCP) az eszközök regisztrációjával kapcsolatos
- Igazolja a meglévő Azure AD-beli függő entitás megbízhatóságát
- Frissíti az Azure AD megbízhatósági jogcímszabályait

Ebben a cikkben a konfigurációs lépések alapján az Azure AD Connect varázsló használatával. Ha telepítve van az Azure AD Connect egy korábbi verzióját, frissítenie kell azt 1.1.819 vagy később használja a varázslót. Az Azure AD Connect legújabb verziójának telepítése nem megfelelő megoldás, ha [hogyan konfigurálhatja manuálisan a hibrid Azure AD-csatlakozás](hybrid-azuread-join-manual.md).

Hibrid Azure AD-csatlakozás eszközök férjenek hozzá a következő Microsoft erőforrásokhoz a vállalati hálózaton belül van szükség:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- A szervezet biztonsági jogkivonat-szolgáltatás (STS) (összevont tartományok) esetén
- `https://autologon.microsoftazuread-sso.com` (Ha használja, vagy a közvetlen egyszeri bejelentkezés használatához)

Kezdve a Windows 10 1803, ha összevont környezetben az AD FS használatával a azonnali hibrid Azure AD-csatlakozás sikertelen lesz, azt támaszkodnak az Azure AD Connect szinkronizálja a számítógép-objektum ezt követően véve az eszközregisztráció az Azure hibrid Azure AD-ben AD-csatlakozás. Győződjön meg arról, hogy az Azure AD Connect rendelkezik-e szinkronizálva a számítógép-objektumokat szeretné a hibrid Azure AD-hez az Azure AD-eszközök. Ha a számítógép-objektumok adott szervezeti egységben (OU) tartozik, a szervezeti egységek szinkronizálása az Azure AD Connectben is konfigurálnia kell. Számítógép-objektumok szinkronizálása az Azure AD Connect használatával kapcsolatos további tudnivalókért lásd: [szűrése az Azure AD Connect használatával konfigurálása](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Ha a szervezet az interneten keresztül egy kimenő proxy hozzáférésre van szüksége, a Microsoft azt javasolja [végrehajtása az webes Proxy automatikus felderítési (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) engedélyezése, eszközregisztráció az Azure ad-ben a Windows 10 rendszerű számítógépeket. Ha konfigurálása és kezelése WPAD problémák merülnek fel, tekintse meg [automatikus észlelési hibaelhárítása](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Ha nem használ WPAD, és konfigurálja a proxybeállításokat a számítógépen kívánja, érdemes tehát verziótól kezdve a Windows 10 1709-es. További információkért lásd: [egy csoportházirend-objektum (GPO) segítségével a konfigurálása WinHTTP beállítások](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Proxybeállítások a számítógépen, a WinHTTP-beállítások segítségével konfigurál, ha minden olyan számítógéphez, nem tud kapcsolódni a konfigurált proxy nem fogja tudni csatlakozni az internethez.

Ha a szervezet az interneten keresztül egy hitelesített kimenő proxy hozzáférésre van szüksége, győződjön meg arról, hogy a Windows 10-es számítógépek és a kimenő proxy sikeresen hitelesítik. Windows 10-számítógépeket regisztrálni az eszközt a számítógép környezetében futnak, konfigurálnia kell kimenő proxy hitelesítése számítógép-környezet használatával. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

A hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect használatával, az alábbiak szükségesek:

- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatait  
- Minden erdő Vállalati rendszergazdai hitelesítő adatokkal
- Az AD FS-rendszergazda hitelesítő adatait

**A hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect használatával**:

1. Indítsa el az Azure AD Connect, és válassza ki **konfigurálása**.

   ![Üdvözlőképernyő](./media/hybrid-azuread-join-federated-domains/11.png)

1. Az a **további feladatok** lapon jelölje be **eszközbeállítások konfigurálása**, majd válassza ki **tovább**.

   ![További feladatok](./media/hybrid-azuread-join-federated-domains/12.png)

1. Az a **áttekintése** lapon jelölje be **tovább**.

   ![Áttekintés](./media/hybrid-azuread-join-federated-domains/13.png)

1. Az a **az Azure AD Connect** lapon adja meg az Azure AD-bérlő globális rendszergazdájának hitelesítő adatait, és válassza ki **tovább**.

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-federated-domains/14.png)

1. Az a **eszközbeállítások** lapon jelölje be **konfigurálása hibrid Azure AD-csatlakozás**, majd válassza ki **tovább**.

   ![Eszközbeállítások](./media/hybrid-azuread-join-federated-domains/15.png)

1. Az a **SCP** lapon, majd válassza ki és kövesse az alábbi lépéseket **tovább**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Válassza ki az erdőt.
   1. Válassza ki a hitelesítési szolgáltatást. Ki kell választania **AD FS-kiszolgáló** , kivéve ha a szervezete rendelkezik kizárólag a Windows 10-ügyfelek és a számítógép/eszköz szinkronizálásának konfigurálása, vagy a szervezet használja a közvetlen egyszeri bejelentkezés.
   1. Válassza ki **Hozzáadás** a vállalati rendszergazdai hitelesítő adatokat.

1. Az a **eszköz-operációsrendszerek** lapon, válassza ki az operációs rendszerek, amelyek az eszközök az Active Directory-környezetet használ, majd válassza ki **tovább**.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-federated-domains/17.png)

1. Az a **összevonási konfiguráció** lapon adja meg az AD FS-rendszergazda hitelesítő adatait, és válassza ki **tovább**.

   ![Összevonás konfigurálása](./media/hybrid-azuread-join-federated-domains/18.png)

1. Az a **konfigurálásra kész** lapon jelölje be **konfigurálása**.

   ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-federated-domains/19.png)

1. Az a **konfiguráció befejezéséhez** lapon jelölje be **kilépési**.

   ![A konfigurálás befejeződött](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows korábbi verziójú eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök az eszközök a Windows korábbi verziójú, a következőket kell tennie:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- Microsoft Workplace Join a Windows korábbi verziójú számítógépek telepítése

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

Fejezze be a Windows korábbi verziójú eszközök hibrid Azure AD-csatlakozás és tanúsítványokra elkerülése érdekében hitelesítik magukat az Azure ad-ben az eszközök, küldhet egy szabályzatot a tartományhoz csatlakoztatott eszközökre a következő URL-címek hozzáadása a helyi intranetzónához az Internet Explorer:

- `https://device.login.microsoftonline.com`
- A szervezet STS (az összevont tartományok)
- `https://autologon.microsoftazuread-sso.com` (A közvetlen egyszeri bejelentkezés)

Is engedélyeznie kell a **lehetővé teszik a frissítések állapotsor keresztül parancsfájl** a felhasználó helyi intranet zónába.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Microsoft Workplace Join a Windows korábbi verziójú számítógépek telepítése

A Windows korábbi verziójú eszközök regisztrálása, telepíteni kell a szervezetek [Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek](https://www.microsoft.com/download/details.aspx?id=53554). A Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek esetében érhető el a Microsoft Download Center.

A csomag például egy szoftverterjesztési rendszer segítségével telepíthet [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). A csomag a standard szintű beavatkozás nélküli telepítés lehetőségről a támogatja a `quiet` paraméter. A Configuration Manager aktuális ágának előnyöket kínál, hiszen nyomon követheti a befejezett regisztrációk például korábbi verzióihoz képest.

A telepítő, amely a felhasználó nevében a rendszer létrehoz egy ütemezett feladatot. A feladat akkor aktiválódik, ha a felhasználó bejelentkezik a Windows. A feladat csendes csatlakoztatja az eszközt az Azure ad-ben a felhasználói hitelesítő adatok használatával, miután az Azure AD hitelesíti.

## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Az Azure-bérlőhöz, az eszköz regisztrációs állapotának ellenőrzéséhez használhatja a **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** parancsmagot a [Azure Active Directory PowerShell-modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ha a **Get-MSolDevice** parancsmaggal ellenőrizheti a szolgáltatás részletei:

- Egy objektumot a **Eszközazonosító** , amely megfelel a Windows ügyfél léteznie kell Azonosítójával.
- A **DeviceTrustType** beállítás értékének **tartományhoz csatlakoztatottnak** kell lennie. Ez a beállítás megegyezik a **hibrid Azure AD-csatlakoztatott** állapot szerint **eszközök** az Azure AD portálon.
- Feltételes hozzáférés, az értéke az által használt eszközök **engedélyezve** kell **igaz** és **DeviceTrustLevel** kell **felügyelt**.

**Ellenőrizze a szolgáltatás részletei**:

1. Nyissa meg a Windows Powershellt rendszergazdaként.
1. Adja meg `Connect-MsolService` az Azure-bérlőhöz való kapcsolódáshoz.  
1. Írja be a `get-msoldevice -deviceId <deviceId>` (igen) kifejezést.
1. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD joinnal befejezése problémákat tapasztal, tekintse meg:

- [Hibrid Azure AD-csatlakozás aktuális Windows-eszközök hibáinak elhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Hibaelhárítás korábbi verziójú Windows-eszközökhöz a hibrid Azure AD-csatlakozás](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [eszközidentitások kezelése az Azure portal használatával](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
