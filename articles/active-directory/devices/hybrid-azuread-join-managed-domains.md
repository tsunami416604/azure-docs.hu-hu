---
title: Hibrid Azure Active Directory-csatlakozás a felügyelt tartományok konfigurálása |} A Microsoft Docs
description: További tudnivalók a hibrid Azure Active Directory-csatlakozás a felügyelt tartományok konfigurálása.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53be1d5728d681d2214677c00681c104a00e3946
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337329"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Oktatóanyag: Hibrid Azure Active Directory-csatlakozás a felügyelt tartományok konfigurálása

A felhasználókhoz hasonlóan az eszközök is olyan identitásokká válnak, amelyeket mindig, mindenhol védelem alatt tartunk, illetve felhasználunk az erőforrásaink védelméhez. Ezt úgy tehetjük meg, ha az eszközök identitásait bevonjuk az Azure AD-be, a következő módszerek egyikével:

- Azure AD-csatlakozás
- Hibrid Azure AD-csatlakozás
- Azure AD-regisztráció

Az Azure AD-be való bevonással maximalizálható a felhasználók munkahatékonysága, köszönhetően az egyszeri bejelentkezésnek (SSO), amely a felhőbeli és a helyszíni erőforrásokhoz is hozzáférést nyújt. Ugyanakkor a felhőbeli és a helyszíni erőforrások hozzáférése külön [feltételes hozzáférésekkel](../active-directory-conditional-access-azure-portal.md) is biztosítható.

Ezen oktatóanyagból megtudhatja, hogyan konfigurálhatja az eszközök hibrid Azure AD-csatlakozását a felügyelt tartományokban.

> [!div class="checklist"]
> * A hibrid Azure AD-csatlakozás konfigurálása
> * A korábbi verziójú Windows-eszközök engedélyezése
> * Csatlakoztatott eszközök ellenőrzése 
> * Hibaelhárítás 


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag feltételezi, hogy Ön járatos az alábbi témakörökben:
    
-  [Az Azure Active Directory eszközkezelésének alapjai](../device-management-introduction.md)
    
-  [A hibrid Azure Active Directory-csatlakozás megvalósításának tervezése](hybrid-azuread-join-plan.md)

-  [Az eszközök hibrid Azure AD-csatlakozásának vezérlése](hybrid-azuread-join-control.md)
  

Ez a cikk a forgatókönyv konfigurálásához lesz szüksége:

- A [az Azure AD Connect legújabb verziójának](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 vagy újabb) kell telepíteni. 

Ellenőrizze, hogy az Azure AD Connect szinkronizálta-e a számítógép-objektumokat az Azure AD-hez hibrid módon csatlakoztatni kívánt eszközök közül. Ha a számítógép-objektumok bizonyos szervezeti egységekhez (OU) tartoznak, akkor az OU-k szinkronizálását is konfigurálni kell az Azure AD Connectben.

Az 1.1.819.0-s verziótól kezdve az Azure AD Connectben egy varázsló segíti a hibrid Azure AD-csatlakozások konfigurálását. Ez a varázsló jelentősen leegyszerűsíti a konfigurálási folyamatot. A kapcsolódó varázsló konfigurálja a szolgáltatáskapcsolódási pontokat (SCP) az eszközregisztrációhoz.

Az ebben a cikkben szereplő konfigurációs lépések ezen a varázslón alapulnak. 

A hibrid Azure AD-csatlakozáshoz szükséges, hogy az eszközök hozzáférjenek a következő Microsoft-erőforrásokhoz a szervezeti hálózaton:  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- https://autologon.microsoftazuread-sso.com (Ha közvetlen egyszeri bejelentkezést használ, vagy tervezi annak használatát)

Ha a szervezet egy kimenő proxy használatát írja elő az internethez való csatlakozáshoz, a Windows 10 1709-es verziójától kezdve [a számítógép proxybeállításai egy csoportszabályzat-objektummal (GPO) konfigurálhatók](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/). Ha a számítógépen a Windows 10 1709-es verziójánál régebbi operációs rendszer fut, az Automatikus webproxykereső (WPA) protokoll használatával lehet engedélyezni, hogy a Windows 10 rendszerű számítógépek eszközregisztrációt végezzenek az Azure AD-ben. 

Ha a szervezet egy hitelesített kimenő proxy használatát írja elő az internethez való csatlakozáshoz, gondoskodni kell arról, hogy a Windows 10 rendszerű számítógépek sikeres hitelesítést tudjanak végezni a kimenő proxyval. Mivel a Windows 10-es számítógépek az eszközregisztrációt gépi kontextusban futtatják, a kimenő proxy hitelesítését is gépi kontextus használatával kell konfigurálni. A konfiguráció követelményeivel kapcsolatban forduljon a kimenő proxy szolgáltatójához. 



## <a name="configure-hybrid-azure-ad-join"></a>A hibrid Azure AD-csatlakozás konfigurálása

A hibrid Azure AD-csatlakozások Azure AD Connecttel való konfigurálásához a következők szükségesek:

- Globális rendszergazdai hitelesítő adatok az Azure AD-bérlőhöz.  

- Az egyes erdőkre vonatkozó vállalati rendszergazdai hitelesítő adatok.


**Hibrid Azure AD-csatlakozás konfigurálása az Azure AD Connect használatával:**

1. Indítsa el az Azure AD Connectet és kattintson a **Konfigurálás** elemre.

    ![Üdvözlőképernyő](./media/hybrid-azuread-join-managed-domains/11.png)

2. A **További feladatok** oldalon válassza az **Eszközbeállítások konfigurálása** elemet, majd kattintson a **Tovább** gombra. 

    ![További feladatok](./media/hybrid-azuread-join-managed-domains/12.png)

3. Az **Áttekintés** oldalon kattintson a **Tovább** gombra. 

    ![Áttekintés](./media/hybrid-azuread-join-managed-domains/13.png)

4. A **Csatlakozás az Azure AD szolgáltatáshoz** oldalon adja meg az Azure AD-bérlőhöz tartozó globális rendszergazdai hitelesítő adatokat.  

    ![Csatlakozás az Azure AD szolgáltatáshoz](./media/hybrid-azuread-join-managed-domains/14.png)

5. Az **Eszközbeállítások** oldalon válassza a **Hibrid Azure AD-csatlakozás konfigurálása** lehetőséget, és kattintson a **Tovább** gombra. 

    ![Eszközbeállítások](./media/hybrid-azuread-join-managed-domains/15.png)

6. Az **SCP** oldalon végezze el az alábbi lépéseket az összes konfigurálni kívánt erdőre vonatkozóan, majd kattintson a **Tovább** gombra: 

    ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

    a. Válassza ki az erdőt.

    b. Válassza ki a hitelesítési szolgáltatást.

    c. Kattintson a **Hozzáadás** gombra a vállalati rendszergazdai hitelesítő adatok megadásához.


7. Az **Eszközök operációs rendszerei** oldalon jelölje ki az Active Directory-környezethez tartozó eszközökön használt operációs rendszereket, majd kattintson a **Tovább** gombra. 

    ![Eszköz operációs rendszere](./media/hybrid-azuread-join-managed-domains/17.png)


8. A **Konfigurálásra kész** oldalon kattintson a **Konfigurálás** gombra. 

    ![Ready to configure (Konfigurálásra kész)](./media/hybrid-azuread-join-managed-domains/19.png)

9. **A konfigurálás befejeződött** oldalon kattintson a **Kilépés** gombra. 

    ![A konfigurálás befejeződött](./media/hybrid-azuread-join-managed-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök engedélyezése

Ha a tartományhoz csatlakoztatott eszközök között korábbi verziójú Windows-eszközök is vannak, végezze el a következőket:

- Eszközbeállítások frissítése
 
- A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

- Közvetlen egyszeri bejelentkezés (SSO) konfigurálása

- A korábbi verziójú Windows-eszközök vezérlése 


### <a name="update-device-settings"></a>Eszközbeállítások frissítése 

A korábbi verziójú Windows-eszközök regisztrálásának feltétele, hogy az eszközbeállításokban be legyen állítva, hogy a felhasználók regisztrálhassák az eszközöket az Azure AD-ben. Ez a beállítás az Azure Portalon a következő helyen található:

`Home > [Name of your tenant] > Devices - Device settings`  


    
A következő szabályzatot kell beállítani **összes**: **A felhasználók regisztrálhatják eszközeiket az Azure ad-vel**

![Eszközök regisztrálása](media/hybrid-azuread-join-managed-domains/23.png)



### <a name="configure-the-local-intranet-settings-for-device-registration"></a>A helyi intranet-beállítások konfigurálása az eszközregisztrációhoz

A korábbi verziójú Windows-eszközök sikeres hibrid Azure AD-csatlakoztatásához, valamint az eszközök Azure AD-hitelesítésekor megjelenő tanúsítványkérések elkerüléséhez leküldhet egy szabályzatot a tartományhoz csatlakozó eszközökre, amely hozzáadja az alábbi URL-címeket a helyi intranet zónához az Internet Explorerben:

- `https://device.login.microsoftonline.com`

- `https://autologon.microsoftazuread-sso.com`.

Emellett engedélyezni kell **az állapotsor szkriptekkel való frissítését** a felhasználó helyi intranetes zónájában.


### <a name="configure-seamless-sso"></a>Közvetlen egyszeri bejelentkezés konfigurálása

Sikeresen befejeződött a hibrid Azure AD join a Windows, a régebbi verziójú eszközök, az Azure AD átmenő hitelesítés (ESP) vagy a jelszó Jelszókivonat szinkronizálása (nál) segítségével felügyelt tartományban felhőalapú hitelesítési mód, ezenkívül meg kell adnia [konfigurálása Közvetlen egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature). 


### <a name="control-windows-down-level-devices"></a>A korábbi verziójú Windows-eszközök vezérlése 

A korábbi verziójú Windows-eszközök regisztrálásához a Letöltőközpontból le kell töltenie, majd telepítenie kell egy Windows Installer-csomagot (.msi). További információért kattintson [ide](hybrid-azuread-join-control.md#control-windows-down-level-devices). 


## <a name="verify-the-registration"></a>A regisztráció ellenőrzése

Az eszköznek az Azure-bérlőbeli regisztrációs állapotát a **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** parancsmaggal lehet ellenőrizni az **[Azure Active Directory PowerShell-modulban](/powershell/azure/install-msonlinev1?view=azureadps-2.0)**.

A **Get-MSolDevice** parancsmag használatához a következők szükségesek:

- Léteznie kell egy objektumnak, amelynek az **eszközazonosítója** megegyezik a Windows-ügyfél azonosítójával.
- A **DeviceTrustType** beállítás értékének **tartományhoz csatlakoztatottnak** kell lennie. Ez egyenértékű az Azure AD-portál Eszközök oldalán található **Hibrid Azure AD-hez csatlakoztatott** értékkel.
- A feltételes hozzáférésű eszközöknél az **Engedélyezve** beállításhoz az **Igaz** értéket kell megadni, a **DeviceTrustLevel** beállításhoz pedig a **Felügyelt** értéket. 


**A szolgáltatás részleteinek ellenőrzése:**

1. Nyissa meg a **Windows PowerShellt** rendszergazdaként.

2. Írja be a `Connect-MsolService` parancsot az Azure-bérlőhöz való csatlakozáshoz.  

3. Gépelje be: `get-msoldevice -deviceId <deviceId>`.

6. Ellenőrizze, hogy az **Engedélyezve** beállításhoz az **Igaz** érték van-e megadva.





## <a name="troubleshoot-your-implementation"></a>A megvalósítás hibaelhárítása

Ha problémákat tapasztal a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásával kapcsolatban, tekintse át a következő cikkeket:

- [Jelenlegi Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-current.md)
- [Korábbi verziójú Windows-eszközök hibrid Azure AD-csatlakozásának hibaelhárítása](troubleshoot-hybrid-join-windows-legacy.md)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A hibrid Azure Active Directory-csatlakozás konfigurálása összevont tartományokhoz](hybrid-azuread-join-federated-domains.md)
> [A hibrid Azure Active Directory-csatlakozás manuális konfigurálása](hybrid-azuread-join-manual.md)

