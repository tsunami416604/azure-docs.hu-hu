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
ms.openlocfilehash: 6ea885dfd1dd38e0811606cd67bf0a1730b0c824
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110640"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Oktatóanyag: Hibrid Azure Active Directory-csatlakozás a felügyelt tartományok konfigurálása

Hasonló módon a felhasználó számára egy eszköz a védeni kívánt is használhatja az erőforrások védelméhez, bármikor és bárhonnan, egy másik core identitás. A cél oszloptárat és eszközidentitások kezelése az Azure ad-ben a következő módszerek egyikének használatával végezheti el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az Azure AD-be való bevonással maximalizálható a felhasználók munkahatékonysága, köszönhetően az egyszeri bejelentkezésnek (SSO), amely a felhőbeli és a helyszíni erőforrásokhoz is hozzáférést nyújt. Egy időben, hozzáférés gondoskodhat a felhőbeli és helyszíni erőforrások [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md).

Ebben az oktatóanyagban megismerheti, hogyan konfigurálhatja a hibrid Azure AD-csatlakozás AD-tartományhoz csatlakoztatott számítógépeket eszközök felügyelt környezetben. 

Lehet felügyelt környezetben keresztül telepített [jelszó Jelszókivonat szinkronizálása (nál)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) vagy [haladnak keresztül hitelesítést (ESP)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) a [zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).
Ezekben az esetekben nincs szükség, hogy konfiguráljon egy összevonási kiszolgálót a hitelesítéshez.

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése
> * Hibaelhárítás

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy Ön járatos az alábbi témakörökben:

- [Bevezetés a eszköz Identitáskezelés az Azure Active Directoryban](../device-management-introduction.md)
- [A hibrid Azure Active Directory-csatlakozás megvalósításának tervezése](hybrid-azuread-join-plan.md)
- [Hibrid Azure AD JOIN szabályozott ellenőrzés módjáról.](hybrid-azuread-join-control.md)

> [!NOTE]
> Az Azure AD nem támogatja intelligens kártyákkal vagy tanúsítványokkal felügyelt tartományokban.

A cikkben ismertetett forgatókönyv konfiguráláshoz telepíteni kell az [Azure AD Connect legfrissebb](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0-s vagy újabb) verzióját.

Ellenőrizze, hogy az Azure AD Connect szinkronizálta-e a számítógép-objektumokat az Azure AD-hez hibrid módon csatlakoztatni kívánt eszközök közül. Ha a számítógép-objektumok bizonyos szervezeti egységekhez (OU) tartoznak, akkor az OU-k szinkronizálását is konfigurálni kell az Azure AD Connectben. Azure AD Connect használatával számítógép-objektumok szinkronizálásának további tudnivalókért lásd a cikk [a szűrés konfigurálása az Azure AD Connect használatával](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Az 1.1.819.0-s verziótól kezdve az Azure AD Connectben egy varázsló segíti a hibrid Azure AD-csatlakozások konfigurálását. Ez a varázsló jelentősen leegyszerűsíti a konfigurálási folyamatot. A kapcsolódó varázsló konfigurálja a szolgáltatáskapcsolódási pontokat (SCP) az eszközregisztrációhoz.

Az ebben a cikkben szereplő konfigurációs lépések ezen a varázslón alapulnak.

A hibrid Azure AD-csatlakozáshoz szükséges, hogy az eszközök hozzáférjenek a következő Microsoft-erőforrásokhoz a szervezeti hálózaton:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Ha közvetlen egyszeri bejelentkezést használ, vagy tervezi annak használatát)

Ha a szervezet az interneten keresztül egy kimenő proxy hozzáférésre van szüksége, a Microsoft azt javasolja [végrehajtása az webes Proxy automatikus felderítési (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) lehetővé teszik a Windows 10-es számítógépek ehhez eszközregisztráció az Azure ad-ben. Ha problémái vannak az konfigurálása és kezelése WPAD, lépjen a [automatikus észlelési hibaelhárítási](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Ha nem használ WPAD, és a proxybeállítások megadására a számítógépen, ezért kezdve a Windows 10 1709-es teheti [egy csoportházirend-objektum (GPO) segítségével a WinHTTP-beállítások konfigurálása](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Ha a proxybeállításokat a számítógépen, a WinHTTP-beállításokkal, számítógépeket nem lehet kapcsolódni a konfigurált proxy a meghiúsul csatlakozni az internethez.

Ha a szervezet egy hitelesített kimenő proxy használatát írja elő az internethez való csatlakozáshoz, gondoskodni kell arról, hogy a Windows 10 rendszerű számítógépek sikeres hitelesítést tudjanak végezni a kimenő proxyval. Mivel a Windows 10-es számítógépek az eszközregisztrációt gépi kontextusban futtatják, a kimenő proxy hitelesítését is gépi kontextus használatával kell konfigurálni. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához.

## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

A hibrid Azure AD-csatlakozások Azure AD Connecttel való konfigurálásához a következők szükségesek:

- Globális rendszergazdai hitelesítő adatok az Azure AD-bérlőhöz.  
- Az egyes erdőkre vonatkozó vállalati rendszergazdai hitelesítő adatok.

**Hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect használatával:**

1. Indítsa el az Azure AD Connectet és kattintson a **Konfigurálás** elemre.

   ![Üdvözlőképernyő](./media/hybrid-azuread-join-managed-domains/11.png)

1. A **További feladatok** oldalon válassza az **Eszközbeállítások konfigurálása** elemet, majd kattintson a **Tovább** gombra.

   ![További feladatok](./media/hybrid-azuread-join-managed-domains/12.png)

1. Az **Áttekintés** oldalon kattintson a **Tovább** gombra.

   ![Áttekintés](./media/hybrid-azuread-join-managed-domains/13.png)

1. A **Csatlakozás az Azure AD szolgáltatáshoz** oldalon adja meg az Azure AD-bérlőhöz tartozó globális rendszergazdai hitelesítő adatokat.  

   ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-managed-domains/14.png)

1. Az **Eszközbeállítások** oldalon válassza a **Hibrid Azure AD-csatlakozás konfigurálása** lehetőséget, és kattintson a **Tovább** gombra.

   ![Eszközbeállítások](./media/hybrid-azuread-join-managed-domains/15.png)

1. Az **SCP** oldalon végezze el az alábbi lépéseket az összes konfigurálni kívánt erdőre vonatkozóan, majd kattintson a **Tovább** gombra:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Válassza ki az erdőt.
   1. Válassza ki a hitelesítési szolgáltatást.
   1. Kattintson a **Hozzáadás** gombra a vállalati rendszergazdai hitelesítő adatok megadásához.

1. Az **Eszközök operációs rendszerei** oldalon jelölje ki az Active Directory-környezethez tartozó eszközökön használt operációs rendszereket, majd kattintson a **Tovább** gombra.

   ![Eszköz operációs rendszere](./media/hybrid-azuread-join-managed-domains/17.png)

1. A **Konfigurálásra kész** oldalon kattintson a **Konfigurálás** gombra.

   ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-managed-domains/19.png)

1. **A konfigurálás befejeződött** oldalon kattintson a **Kilépés** gombra.

   ![A konfigurálás befejeződött](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök között korábbi verziójú Windows-eszközök is vannak, végezze el a következőket:

- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz
- Közvetlen egyszeri bejelentkezés (SSO) konfigurálása
- Microsoft Workplace Join a Windows alacsonyabb szintű számítógépek telepítése

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

A korábbi verziójú Windows-eszközök sikeres hibrid Azure AD-csatlakoztatásához, valamint az eszközök Azure AD-hitelesítésekor megjelenő tanúsítványkérések elkerüléséhez leküldhet egy szabályzatot a tartományhoz csatlakozó eszközökre, amely hozzáadja az alábbi URL-címeket a helyi intranet zónához az Internet Explorerben:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Emellett engedélyezni kell **az állapotsor szkriptekkel való frissítését** a felhasználó helyi intranetes zónájában.

### <a name="configure-seamless-sso"></a>Közvetlen egyszeri bejelentkezés konfigurálása

Sikeresen befejeződött a hibrid Azure AD join a Windows régebbi verziójú eszközök által használt felügyelt tartományban való [jelszó Jelszókivonat szinkronizálása (nál)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) vagy [haladnak keresztül hitelesítést (ESP)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) az Azure AD-felhők hitelesítési mód, ezenkívül meg kell adnia [közvetlen egyszeri bejelentkezés konfigurálása](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Microsoft Workplace Join a Windows alacsonyabb szintű számítógépek telepítése

Windows régebbi verziójú eszközök regisztrálása, telepíteni kell a szervezetek [Microsoft munkahelyi csatlakoztatás Windows 10-számítógépek](https://www.microsoft.com/download/details.aspx?id=53554) elérhető a Microsoft Download Center.

A csomag például egy szoftverterjesztési rendszer segítségével telepíthet [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). A csomag a standard szintű beavatkozás nélküli telepítés beállítások quiet paraméter támogatja. A Configuration Manager aktuális ágának előnyöket kínál, hiszen nyomon követheti a befejezett regisztrációk például korábbi verzióihoz képest.

A telepítő, amely a felhasználó nevében a rendszer létrehoz egy ütemezett feladatot. A feladat akkor aktiválódik, ha a felhasználó bejelentkezik a Windows. A feladat csendes csatlakoztatja az eszközt az Azure AD-felhasználói hitelesítő adatok az Azure AD-hitelesítés után.

## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Az eszköznek az Azure-bérlőbeli regisztrációs állapotát a **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** parancsmaggal lehet ellenőrizni az **[Azure Active Directory PowerShell-modulban](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

A **Get-MSolDevice** parancsmag használatához a következők szükségesek:

- Egy objektumot a **Eszközazonosító** , amely megfelel a Windows ügyfél léteznie kell Azonosítójával.
- A **DeviceTrustType** beállítás értékének **tartományhoz csatlakoztatottnak** kell lennie. Ez egyenértékű az Azure AD-portál Eszközök oldalán található **Hibrid Azure AD-hez csatlakoztatott** értékkel.
- Az érték **engedélyezve** kell lennie **igaz** és **DeviceTrustLevel** kell lennie **felügyelt** feltételes hozzáférés az által használt eszközök.

**A szolgáltatás részleteinek ellenőrzése:**

1. Nyissa meg a **Windows PowerShellt** rendszergazdaként.
1. Írja be a `Connect-MsolService` parancsot az Azure-bérlőhöz való csatlakozáshoz.  
1. Gépelje be: `get-msoldevice -deviceId <deviceId>`.
1. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.

## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha problémákat tapasztal a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásával kapcsolatban, tekintse át a következő cikkeket:

- [Jelenlegi Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Korábbi verziójú Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>További lépések

- Az Azure AD portálon eszközidentitások kezelésével kapcsolatos további információkért lásd: [az Azure portal használatával eszközidentitások kezelése](device-management-azure-portal.md).
