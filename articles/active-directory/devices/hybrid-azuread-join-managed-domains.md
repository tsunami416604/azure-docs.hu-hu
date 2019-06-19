---
title: Hibrid Azure Active Directory-csatlakozás a felügyelt tartományok konfigurálása |} A Microsoft Docs
description: További tudnivalók a hibrid Azure Active Directory-csatlakozás a felügyelt tartományok konfigurálása.
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
ms.openlocfilehash: b24888934d7e89a13b1b07b7138be476575fc306
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204614"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Oktatóanyag: Hibrid Azure Active Directory-csatlakozás a felügyelt tartományok konfigurálása

A felhasználó a szervezetben, mint például egy eszköz egy védeni kívánt core identitás. Használhatja egy eszközidentitást az erőforrások védelméhez, bármikor és bárhonnan. Ezt a célt és eszközidentitások és kezelnie azokat az Azure Active Directoryban (Azure AD) az alábbi módszerek egyikének használatával végezheti el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az eszközöket az Azure AD felhasználói termelékenység egyszeri bejelentkezés (SSO) a lehető legnagyobbra növeli a felhőalapú és helyszíni erőforrások. A felhőbeli és helyszíni erőforrásokhoz való hozzáférés gondoskodhat [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md) egyszerre.

Ebben az oktatóanyagban elsajátíthatja, hogyan konfigurálja az Active Directory-tartományhoz csatlakoztatott számítógépeket eszközök hibrid Azure AD joinnal felügyelt környezetben. 

Lehet felügyelt környezetben keresztül telepített [Jelszókivonat-szinkronizálás (nál)](../hybrid/whatis-phs.md) vagy [átmenő hitelesítés (ESP)](../hybrid/how-to-connect-pta.md) a [zökkenőmentes egyszeri bejelentkezést](../hybrid/how-to-connect-sso.md). Ezekben az esetekben nincs szükség, hogy konfiguráljon egy összevonási kiszolgálót a hitelesítéshez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy ismeri a következő cikkeket:

- [Mi az eszközidentitás?](overview.md)
- [A hibrid Azure AD join megvalósítás tervezése](hybrid-azuread-join-plan.md)
- [Hibrid Azure AD JOIN szabályozott ellenőrzés módjáról.](hybrid-azuread-join-control.md)

> [!NOTE]
> Az Azure AD nem támogatja a felügyelt tartományok intelligens kártyákkal vagy tanúsítványokkal.

Ez a cikk a forgatókönyv konfigurálásához lesz szüksége a [az Azure AD Connect legújabb verziójának](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 vagy újabb) telepítve van.

Győződjön meg arról, hogy az Azure AD Connect rendelkezik-e szinkronizálva a számítógép-objektumokat szeretné a hibrid Azure AD-hez az Azure AD-eszközök. Ha a számítógép-objektumok adott szervezeti egységben (OU) tartozik, a szervezeti egységek szinkronizálása az Azure AD Connectben is konfigurálnia kell. Számítógép-objektumok szinkronizálása az Azure AD Connect használatával kapcsolatos további tudnivalókért lásd: [szűrése az Azure AD Connect használatával konfigurálása](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

1\.1.819.0 verzióval kezdve az Azure AD Connect varázslót tartalmaz, amely segítségével hibrid Azure AD-csatlakozás konfigurálása. A varázsló jelentősen leegyszerűsíti a konfigurációs folyamat. A varázsló konfigurálja a szolgáltatás kapcsolódási pontok (SCP) az eszközök regisztrációjával kapcsolatos.

Ebben a cikkben a konfigurációs lépések alapján a varázsló segítségével az Azure AD Connectben.

Hibrid Azure AD-csatlakozás eszközök férjenek hozzá a következő Microsoft erőforrásokhoz a vállalati hálózaton belül van szükség:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Ha használja, vagy a közvetlen egyszeri bejelentkezés használatához)

Ha a szervezet az interneten keresztül egy kimenő proxy hozzáférésre van szüksége, a Microsoft azt javasolja [végrehajtása az webes Proxy automatikus felderítési (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) engedélyezése, eszközregisztráció az Azure ad-ben a Windows 10 rendszerű számítógépeket. Ha konfigurálása és kezelése WPAD problémák merülnek fel, tekintse meg [automatikus észlelési hibaelhárítása](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Ha WPAD, és meg kell Proxybeállítások konfigurálása a számítógépen nem használja, érdemes tehát verziótól kezdve a Windows 10 1709-es. További információkért lásd: [WinHTTP konfigurálása beállításait a csoportházirend-objektum (GPO) segítségével](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Proxybeállítások a számítógépen, a WinHTTP-beállítások segítségével konfigurál, ha minden olyan számítógéphez, nem tud kapcsolódni a konfigurált proxy nem fogja tudni csatlakozni az internethez.

Ha a szervezet az interneten keresztül egy hitelesített kimenő proxy hozzáférésre van szüksége, győződjön meg arról, hogy a Windows 10-es számítógépek és a kimenő proxy sikeresen hitelesítik. Windows 10-számítógépeket regisztrálni az eszközt a számítógép környezetében futnak, konfigurálnia kell kimenő proxy hitelesítése számítógép-környezet használatával. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

A hibrid Azure AD-csatlakozások Azure AD Connecttel való konfigurálásához a következők szükségesek:

- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatait
- Minden erdő Vállalati rendszergazdai hitelesítő adatokkal

**A hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect használatával:**

1. Indítsa el az Azure AD Connect, és válassza ki **konfigurálása**.

   ![Üdvözlőképernyő](./media/hybrid-azuread-join-managed-domains/11.png)

1. Az a **további feladatok** lapon jelölje be **eszközbeállítások konfigurálása**, majd válassza ki **tovább**.

   ![További feladatok](./media/hybrid-azuread-join-managed-domains/12.png)

1. Az a **áttekintése** lapon jelölje be **tovább**.

   ![Áttekintés](./media/hybrid-azuread-join-managed-domains/13.png)

1. A **Csatlakozás az Azure AD szolgáltatáshoz** oldalon adja meg az Azure AD-bérlőhöz tartozó globális rendszergazdai hitelesítő adatokat.  

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-managed-domains/14.png)

1. Az a **eszközbeállítások** lapon jelölje be **konfigurálása hibrid Azure AD-csatlakozás**, majd válassza ki **tovább**.

   ![Eszközbeállítások](./media/hybrid-azuread-join-managed-domains/15.png)

1. Az a **SCP** lapon az egyes erdőkhöz, ahol azt szeretné, hogy az az SCP konfigurálása, a következő lépéseket, és válassza az Azure AD Connect **tovább**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Válassza ki az erdőt.
   1. Válassza ki a hitelesítési szolgáltatást.
   1. Válassza ki **Hozzáadás** a vállalati rendszergazdai hitelesítő adatokat.

1. Az a **eszköz-operációsrendszerek** lapon, az operációs rendszerek, amellyel az eszközöket az Active Directory-környezet használatban, majd válassza ki és **tovább**.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-managed-domains/17.png)

1. Az a **konfigurálásra kész** lapon jelölje be **konfigurálása**.

   ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-managed-domains/19.png)

1. Az a **konfiguráció befejezéséhez** lapon jelölje be **kilépési**.

   ![A konfigurálás befejeződött](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows korábbi verziójú eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök az eszközök a Windows korábbi verziójú, a következőket kell tennie:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- Közvetlen egyszeri bejelentkezés konfigurálása
- Microsoft Workplace Join a Windows korábbi verziójú számítógépek telepítése

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

o fejezze be a Windows korábbi verziójú eszközök hibrid Azure AD-csatlakozás és a tanúsítványokra elkerülése érdekében hitelesítik magukat az Azure ad-ben az eszközök, küldhet egy szabályzatot a tartományhoz csatlakoztatott eszközökre a következő URL-címek hozzáadása a helyi intranetzónához az Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Is engedélyeznie kell a **lehetővé teszik a frissítések állapotsor keresztül parancsfájl** a felhasználó helyi intranet zónába.

### <a name="configure-seamless-sso"></a>Közvetlen egyszeri bejelentkezés konfigurálása

Hibrid Azure AD-csatlakozás a Windows korábbi verziójú eszközök egy felügyelt tartomány sikeres végrehajtásához, amely használja a [nál]... /Hybrid/whatis-PHS.MD) vagy [ESP](../hybrid/how-to-connect-pta.md) az Azure AD felhőalapú hitelesítési módszerként is el kell [közvetlen egyszeri bejelentkezés konfigurálása](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Microsoft Workplace Join a Windows korábbi verziójú számítógépek telepítése

A Windows korábbi verziójú eszközök regisztrálása, telepíteni kell a szervezetek [Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek](https://www.microsoft.com/download/details.aspx?id=53554). A Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek esetében érhető el a Microsoft Download Center.

A csomag például egy szoftverterjesztési rendszer segítségével telepíthet [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). A csomag a standard szintű beavatkozás nélküli telepítés lehetőségről a támogatja a `quiet` paraméter. A Configuration Manager aktuális ágának előnyöket kínál, hiszen nyomon követheti a befejezett regisztrációk például korábbi verzióihoz képest.

A telepítő, amely a felhasználó nevében a rendszer létrehoz egy ütemezett feladatot. A feladat akkor aktiválódik, ha a felhasználó bejelentkezik a Windows. A feladat csendes csatlakoztatja az eszközt az Azure ad-ben a felhasználói hitelesítő adatok használatával, miután az Azure AD hitelesíti.

## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Az Azure-bérlőhöz, az eszköz regisztrációs állapotának ellenőrzéséhez használhatja a **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** parancsmagot a [Azure Active Directory PowerShell-modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ha a **Get-MSolDevice** parancsmaggal ellenőrizheti a szolgáltatás részletei:

- Egy objektumot a **Eszközazonosító** , amely megfelel a Windows ügyfél léteznie kell Azonosítójával.
- A **DeviceTrustType** beállítás értékének **tartományhoz csatlakoztatottnak** kell lennie. Ez a beállítás megegyezik a a **hibrid Azure AD-csatlakoztatott** az állapot a **eszközök** oldal az Azure AD portálon.
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
