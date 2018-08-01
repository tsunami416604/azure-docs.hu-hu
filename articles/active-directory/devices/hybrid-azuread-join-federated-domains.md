---
title: Hogyan hibrid konfigurálása Azure Active Directoryhoz csatlakoztatott eszközök |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a hibrid Azure Active Directoryhoz csatlakoztatott eszközökön.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 1aa5c0f259fdb5fce449bc86f9cf293801b4770a
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369169"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Oktatóanyag: A hibrid Azure Active Directory-csatlakozás összevont tartományok konfigurálása

Hasonló módon a felhasználó eszköz neve lesz a védelmét, és az erőforrások védelméhez bármely időben és helyen is használja egy másik identitás. A cél, és az eszközök identitásokat az Azure AD a következő módszerek egyikének használatával végezheti el:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Biztosítja az eszközök Azure ad-hez, a felhőalapú és helyszíni erőforrások a felhasználók hatékonyságát egyszeri bejelentkezés (SSO) maximalizálása meg. Egy időben, hozzáférés gondoskodhat a felhőbeli és helyszíni erőforrások [feltételes hozzáférési](../active-directory-conditional-access-azure-portal.md).

Ebben az oktatóanyagban elsajátíthatja, hogy az AD FS használatával összevont eszközök hibrid Azure AD joinnal konfigurálása.

> [!div class="checklist"]
> * Hibrid Azure AD-csatlakozás konfigurálása
> * Windows régebbi verziójú eszközök engedélyezése
> * A regisztráció-ellenőrzés
> * Hibaelhárítás


## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag feltételezi, hogy Ön ismeri a:

-  [Az Eszközfelügyelet az Azure Active Directory bemutatása](../device-management-introduction.md)

-  [A hibrid Azure Active Directory join megvalósítás tervezése](hybrid-azuread-join-plan.md)



Ebben az oktatóanyagban a forgatókönyv konfigurálásához lesz szüksége:

- A Windows Server 2012 R2 AD FS-sel

- [Az Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 verzió vagy újabb verziója. 
 

1.1.819.0 verzióval kezdve az Azure AD Connect biztosít a hibrid Azure AD-csatlakozás konfigurálása varázsló segítségével. A varázsló lehetővé teszi a jelentősen megkönnyíti a konfigurációs folyamat. A kapcsolódó varázsló:

- Konfigurálja a szolgáltatás kapcsolódási pontok (SCP) az eszközök regisztrációjával kapcsolatos

- Biztonsági másolatot a meglévő Azure AD függő entitás megbízhatóságának készít

- Az Azure AD-megbízhatóság jogcímszabályainak frissítése

A konfigurációs lépéseket ebben a cikkben a varázsló alapulnak. Ha telepítve van az Azure AD Connect egy régebbi verzióját, frissítenie kell azt 1.1.819 vagy újabb. Ha az Azure AD Connect legújabb verziójának telepítése lehetőség nem az Ön számára, lásd: [manuális konfigurálása az eszközregisztrációs](../device-management-hybrid-azuread-joined-devices-setup.md).

Hibrid Azure AD-csatlakozás az eszközök férjenek hozzá a következő Microsoft erőforrásokhoz a vállalati hálózaton belül van szükség:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- A szervezet STS (összevont tartományok)
- https://autologon.microsoftazuread-sso.com (Ha még nem használ vagy tervez használni a közvetlen egyszeri bejelentkezés)

Ha a szervezet az interneten keresztül egy kimenő proxy hozzáférésre van szüksége, kezdve a Windows 10 1709-es, akkor is Proxybeállítások konfigurálása a számítógépen egy csoportházirend-objektum (GPO) használatával. Ha a számítógép bármit régebbi, mint a Windows 10 1709-es fut, meg kell valósítani Proxy automatikus felderítési WPAD (Web) engedélyezéséhez a Windows 10 rendszerű számítógépekkel eszközregisztráció az Azure ad-ben. 

Ha a szervezet az interneten keresztül egy hitelesített kimenő proxy hozzáférésre van szüksége, győződjön meg arról, hogy a Windows 10-es számítógépek és a kimenő proxy sikeresen hitelesítik. Windows 10-es számítógépek számítógép-környezet használatával történő eszközregisztrációval futtatni, mivel, használatával a számítógép-környezet kimenő proxy-hitelesítés konfigurálása szükséges. Nyomon követheti a kimenő proxy-szolgáltatóhoz a követelményeknek. 


## <a name="configure-hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás konfigurálása

Egy Azure AD Connect használatával a hibrid Azure AD-csatlakozás konfigurálásához lesz szüksége:

- Az Azure AD-bérlő globális rendszergazdájának hitelesítő adatait.  

- A vállalati rendszergazdájának hitelesítő adatait az egyes erdők.

- Az AD FS-rendszergazda hitelesítő adatait. 


**Egy Azure AD Connect használatával hibrid Azure AD join beállítása:**

1. Indítsa el az Azure AD Connect, és kattintson a **konfigurálása**.

    ![Üdvözöljük](./media/hybrid-azuread-join-federated-domains/11.png)

2. Az a **további feladatok** lapon jelölje be **eszközbeállítások konfigurálása**, és kattintson a **tovább**. 

    ![További feladatok](./media/hybrid-azuread-join-federated-domains/12.png)

3. Az a **áttekintése** kattintson **tovább**. 

    ![Áttekintés](./media/hybrid-azuread-join-federated-domains/13.png)

4. Az a **az Azure AD Connect** lapon adja meg az Azure AD-bérlő globális rendszergazdájának hitelesítő adatait, és kattintson a **tovább**.   

    ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-federated-domains/14.png)

5. Az a **eszközbeállítások** lapon jelölje be **konfigurálása hibrid Azure AD-csatlakozás**, és kattintson a **tovább**. 

    ![Eszközbeállítások](./media/hybrid-azuread-join-federated-domains/15.png)

6. Az a **SCP** lapon hajtsa végre az alábbi lépéseket, és kattintson a **tovább**: 

    ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

    a. Válassza ki az erdőhöz.

    b. Válassza ki a hitelesítési szolgáltatást.

    c. Kattintson a **Hozzáadás** a vállalati rendszergazdai hitelesítő adatokat.


7. Az a **eszköz-operációsrendszerek** lapon válassza ki az Active Directory-környezet eszközei által használt operációs rendszereket, és kattintson a **tovább**. 

    ![Eszköz operációs rendszere](./media/hybrid-azuread-join-federated-domains/17.png)

8. Az a **összevonási konfiguráció** lapon adja meg az AD FS-rendszergazda hitelesítő adatait, és kattintson a **tovább**. 

    ![Az összevonás konfigurálása](./media/hybrid-azuread-join-federated-domains/18.png)

9. Az a **konfigurálásra kész** kattintson **konfigurálása**. 

    ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-federated-domains/19.png)

10. Az a **konfiguráció befejezéséhez** kattintson **kilépési**. 

    ![A konfigurálás befejeződött](./media/hybrid-azuread-join-federated-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>Windows régebbi verziójú eszközök engedélyezése

Ha Windows régebbi verziójú eszközök, a tartományhoz csatlakoztatott eszközök némelyike kell tennie:

- Eszközbeállítások frissítése
 
- Az eszköz regisztrálása a helyi intranet beállításainak konfigurálása


### <a name="update-device-settings"></a>Eszközbeállítások frissítése 

Windows régebbi verziójú eszközök regisztrálása, győződjön meg arról, hogy a felhasználók regisztrálhatják az eszközeiket az Azure ad-ben az Eszközbeállítások között vannak beállítva, kell. Az Azure Portalon találhatja meg a beállítás:

`Home > [Name of your tenant] > Devices - Device settings`  


    
A következő szabályzatot kell beállítani **összes**: **a felhasználók regisztrálhatják eszközeiket az Azure ad-vel**

![Eszközök regisztrálása](./media/hybrid-azuread-join-federated-domains/23.png)


### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Az eszköz regisztrálása a helyi intranet beállításainak konfigurálása

Sikeresen befejeződött a hibrid Azure AD-csatlakozás a Windows régebbi verziójú eszközök, és elkerülése érdekében tanúsítvány utasításokat, ha hitelesítik az eszközök hitelesítése az Azure AD-szabályzat leküldése a tartományhoz csatlakoztatott eszközök a következő URL-címek hozzáadása a Helyi Intranet az Internet Explorerben. zóna:

- `https://device.login.microsoftonline.com`

- `https://device.login.microsoftonline.com`

- A szervezet biztonsági jogkivonat-szolgáltatás (STS - összevont tartományok)

- `https://autologon.microsoftazuread-sso.com` (a közvetlen egyszeri bejelentkezés).

Ezenkívül engedélyeznie kell **lehetővé teszik a frissítések állapotsor keresztül parancsfájl** a felhasználó helyi intranet zónába.



## <a name="verify-the-registration"></a>A regisztráció-ellenőrzés

Az Azure-bérlőhöz, az eszköz regisztrációs állapotának ellenőrzéséhez használhatja a **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** parancsmagot a  **[Azure Active Directory PowerShell-modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

Használatakor a **Get-MSolDevice** parancsmaggal ellenőrizheti a szolgáltatás részletei:

- Egy objektumot a **eszközazonosító** , amely megfelel a Windows ügyfél léteznie kell Azonosítójával.
- Az érték **DeviceTrustType** kell **tartományhoz csatlakozó**. Ez a **hibrid Azure AD-csatlakoztatott** állapot az Azure AD portálon eszközök lapján.
- Az érték **engedélyezve** kell lennie **igaz** feltételes hozzáférés az által használt eszközök. 


**Ellenőrizze a szolgáltatás részletei:**

1. Nyissa meg **Windows PowerShell** rendszergazdaként.

2. Típus `Connect-MsolService` az Azure-bérlőhöz való kapcsolódáshoz.  

3. Gépelje be: `get-msoldevice -deviceId <deviceId>`.

6. Ellenőrizze, hogy **engedélyezve** értékre van állítva **igaz**.





## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha problémákat tapasztal a hibrid befejezése az Azure AD join tartományhoz csatlakoztatott Windows-eszközök, lásd:

- [Aktuális Windows-eszközök hibrid Azure AD joinnal hibaelhárítása](../device-management-troubleshoot-hybrid-join-windows-current.md)
- [Hibaelhárítás Windows régebbi verziójú eszközök hibrid Azure AD joinnal](../device-management-troubleshoot-hybrid-join-windows-legacy.md)



## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Konfigurálás hibrid Azure Active Directory-csatlakozás a felügyelt tartományok](hybrid-azuread-join-managed-domains.md)
> [hibrid Azure Active Directory-csatlakozás kézi konfigurálása](../device-management-hybrid-azuread-joined-devices-setup.md)




<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
