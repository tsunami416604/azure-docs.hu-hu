---
title: 'Az Azure Active Directory tartományi szolgáltatások: A Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományokhoz |} A Microsoft Docs'
description: A Windows Server virtuális gépek csatlakoztatása az Azure Active Directory tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: ergreenl
ms.openlocfilehash: 41e7f0ea8616b25eac43ecff7802e0f9e1c08050
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180704"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server virtuális gépek csatlakoztatása felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan helyezhet üzembe egy Windows Server virtuális gépet az Azure portal használatával. Ezután bemutatja hogyan kell a virtuális gép csatlakoztatása az Azure Active Directory Domain Services (Azure AD DS) a felügyelt tartományhoz.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>1. lépés: Hozzon létre egy Windows Server virtuális gépet
A virtuális hálózatot, amelyben engedélyezte az Azure AD DS tartományhoz csatlakozó Windows virtuális gép létrehozásához tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a bal oldali ablaktábla tetején **új**.
3. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.

    ![A Windows Server 2016 Datacenter-hivatkozás](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Az a **alapjai** ablaktábla a varázsló a virtuális gép alapszintű beállításainak konfigurálása.

    ![Az alapvető beállítások panel](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > A felhasználónév és jelszó itt van egy helyi rendszergazdai fiók, amellyel jelentkezzen be a virtuális gép. Válasszon ki egy erős jelszót a virtuális gép jelszava találgatásos támadások elleni védelmét. Ne adjon meg egy tartományi felhasználói fiók hitelesítő adatait.
    >

5. Válassza ki a **mérete** a virtuális gép. További méretek megtekintéséhez válassza **az összes megtekintése** vagy módosítsa a **támogatott lemeztípus** szűrőt.

    ![A "Méret kiválasztása" panelen](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Az a **beállítások** ablaktáblán válassza ki a virtuális hálózatot, amely az Azure AD DS által felügyelt tartomány üzembe lesz helyezve. Válasszon egy másik alhálózatot, amely a felügyelt tartományra van üzembe helyezve. A további beállításoknál hagyja változatlanul az alapértelmezett beállításokat, és válassza ki **OK**.

    ![A virtuális gép virtuális hálózati beállítások](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Válassza ki a megfelelő virtuális hálózatot és alhálózatot.**
    >
    > Válassza ki vagy a virtuális hálózatot, amely a felügyelt tartomány üzembe lesz helyezve, vagy egy virtuális hálózatot, amelyhez csatlakozik, a virtuális hálózati társviszony-létesítés. Ha egy virtuális hálózati adapterrrel, a virtuális gép nem csatlakozik a felügyelt tartományhoz.
    >
    > Javasoljuk, hogy a felügyelt tartomány egy dedikált alhálózatban. Ezért nem válassza ki az alhálózatot, amelyben engedélyezte a felügyelt tartományra.

7. A további beállításoknál hagyja változatlanul az alapértelmezett beállításokat, és válassza ki **OK**.
8. Az a **beszerzési** lapon tekintse át a beállításokat, és válassza ki **OK** a virtuális gép üzembe helyezése.
9. A virtuális gép üzembe helyezése az Azure portal irányítópultján van rögzítve.

    ![Kész](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Az üzembe helyezés befejezése után megtekintheti a virtuális Géppel kapcsolatos információkat a **áttekintése** lapot.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>2. lépés: Csatlakozás a Windows Server virtuális gépet a helyi rendszergazdai fiók használatával
Ezután csatlakozhat a tartományhoz csatlakozni az újonnan létrehozott Windows Server virtuális gép. A virtuális gép létrehozásakor megadott, a helyi rendszergazdai hitelesítő adatok használata.

A virtuális gép csatlakozik, hajtsa végre az alábbi lépéseket:

1. Az a **áttekintése** ablaktáblán válassza előbb **Connect**.  
    Egy Remote Desktop Protocol (.rdp) fájlt a rendszer létrehoz és letölt.

    ![Windows virtuális gép eléréséhez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.
3. Adja meg a **helyi rendszergazdai hitelesítő adatokkal**, amely a virtuális gép létrehozásakor megadott (például *localhost\mahesh*).
4. Ha a bejelentkezési folyamat során a tanúsítványra vonatkozó figyelmeztetést látja, válassza ki a **Igen** vagy **Folytatás** való csatlakozáshoz.

Ezen a ponton be kell bejelentkeznie az újonnan létrehozott Windows virtuális gépre a helyi rendszergazdai hitelesítő adataival. A következő lépés, hogy a virtuális gép csatlakoztatása a tartományhoz.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>3. lépés: A Windows Server virtuális gép csatlakoztatása az Azure AD DS által felügyelt tartományhoz
A Windows Server virtuális gép csatlakoztatása az Azure AD DS által felügyelt tartományhoz, a következő lépéseket:

1. A Windows Server rendszerű virtuális gépek csatlakozni, ahogyan az "2. lépés." Az a **Start** nyissa meg **Kiszolgálókezelő**.
2. A bal oldali panelen, a **Kiszolgálókezelő** ablakban válassza **helyi kiszolgáló**.

    ![A Kiszolgálókezelő ablakban a virtuális gépen](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. A **tulajdonságok**válassza **munkacsoport**.
4. Az a **Rendszertulajdonságok** ablakban válassza **módosítása** a tartományhoz való csatlakozáshoz.

    ![A Rendszertulajdonságok párbeszédpanel](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. Az a **tartomány** mezőbe, majd válassza ki és adja meg a nevét, az Azure AD DS által felügyelt tartomány **OK**.

    ![Adja meg a tartományt, amelyet egyesíteni kell](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. A tartományhoz való csatlakozásnál a hitelesítő adatok megadását kéri. A hitelesítő adatok használata a *az Azure Active Directory-Tartományvezérlők rendszergazdák csoporthoz tartozó felhasználó*. Csak a csoport tagjai jogosultság a gépeket csatlakoztathatja a felügyelt tartományhoz.

    ![A Windows rendszerbiztonsági ablakban a hitelesítő adatok megadása](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Hitelesítő adatok a következő módszerek egyikével adhatja meg:

   * **Egyszerű felhasználónév formátumú**: (Ajánlott) Adja meg a felhasználó egyszerű felhasználónév (UPN) utótagot a felhasználói fiók, az Azure ad-ben konfigurált. Ebben a példában a felhasználói UPN-utótagját *bob* van *bob@domainservicespreview.onmicrosoft.com*.

   * **SAMAccountName formátum**: A SAMAccountName formátum is megadhat a fiók nevét. Ebben a példában a felhasználó *bob* meg kellene *CONTOSO100\bob*.

     > [!TIP]
     > **Azt javasoljuk, hogy az egyszerű felhasználónév formátumban adja meg hitelesítő adatait.**
     >
     > Ha a felhasználó egyszerű Felhasználónevét előtag túl hosszú (például *joehasareallylongname*), a SAMAccountName lehet, hogy automatikusan létrehozott. Ha több felhasználó rendelkezik ugyanazon UPN előtaggal (például *bob*) az Azure AD-bérlőhöz, a SAMAccountName formátum lehet a szolgáltatás által automatikusan generált. Ezekben az esetekben az egyszerű felhasználónév formátumú segítségével megbízhatóan bejelentkezni a tartományba.
     >

8. Miután sikeresen csatlakozott egy tartományhoz, akkor a következő üzenet üdvözli a tartományhoz.

    ![Üdvözli a tartományhoz](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. A tartományhoz való csatlakozás befejezéséhez indítsa újra a virtuális gépet.

## <a name="troubleshoot-joining-a-domain"></a>A tartományhoz való csatlakozás hibaelhárítása
### <a name="connectivity-issues"></a>Csatlakozási problémák
Nem található a tartomány a virtuális gép esetén próbálja meg következő hibaelhárítási lépéseket:

* Ellenőrizze, hogy a virtuális gép az Azure Active Directory tartományi szolgáltatások engedélyezve van a virtuális hálózathoz csatlakozik. Ellenkező esetben a virtuális gép nem tud csatlakozni, vagy a tartományhoz.

* Ellenőrizze, hogy a virtuális gép, amely ezután csatlakozik a virtuális hálózat az Azure Active Directory tartományi szolgáltatások engedélyezve van a virtuális hálózaton van-e.

* Próbálja meg megpingelni a felügyelt tartomány DNS-tartomány nevét (például *contoso100.com pingelni*). Ha ezt nem tudja, próbálja meg megpingelni a IP-címeket, a tartományhoz, amely megjelenik az oldalon ahol engedélyezve van az Azure Active Directory tartományi szolgáltatások (például *10.0.0.4 pingelni*). Ha az IP-címet, de a tartományhoz nem pingelhető, DNS előfordulhat, hogy megfelelően konfigurálva. Ellenőrizze, hogy az IP-címeket, annak a tartománynak a virtuális hálózat DNS-kiszolgálóként konfigurált.

* Próbálja ki a virtuális gépen, a DNS-feloldási gyorsítótár-ürítés (*ipconfig/flushdns paranccsal*).

Ha egy ablakban jelenik meg, a tartományhoz való csatlakozásnál a hitelesítő adatokat kér, nem rendelkezik a kapcsolati hibák.

### <a name="credentials-related-issues"></a>Hitelesítő adatokkal kapcsolatos problémák
Ha gondjai adódnak a hitelesítő adatokat, és nem lehet csatlakozni a tartományhoz, próbálja meg következő hibaelhárítási lépéseket:

* Próbálja ki az egyszerű felhasználónév formátumban adja meg hitelesítő adatait. Ha azonos egyszerű Felhasználónévvel előtaggal a bérlőben sok felhasználó, vagy ha túl hosszú az egyszerű Felhasználónévi előtagját, a fiók a SAMAccountName lehet automatikusan létrehozott. Ezekben az esetekben a SAMAccountName formátum, a fiók lehet eltérő mit hoz várható, vagy a helyszíni tartományban.

* Próbálja meg használni, amelyhez tartozik egy felhasználói fiók hitelesítő adatait a *AAD DC rendszergazdák* csoport.

* Ellenőrizze, hogy rendelkezik [engedélyezve van a jelszó-szinkronizálás](active-directory-ds-getting-started-password-sync.md) a felügyelt tartományra.

* Ellenőrizze, hogy már használta a felhasználó egyszerű Felhasználónevét az Azure ad-ben konfigurált (például *bob@domainservicespreview.onmicrosoft.com*) való bejelentkezéshez.

* Várjon, amíg a jelszó-szinkronizálás befejeződött, az első lépésekről szóló útmutatót megadottak szerint.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure Active Directory tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Az Azure AD DS által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
