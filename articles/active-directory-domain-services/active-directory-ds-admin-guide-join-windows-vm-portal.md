---
title: 'Az Azure Active Directory tartományi szolgáltatások: A Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz |} Microsoft Docs'
description: Windows Server virtuális gép csatlakoztatása az Azure Active Directory tartományi szolgáltatások
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.component: domains
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: dadc20cdee68730fa1d81dd86b3ffa0b0022a5b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34586953"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server virtuális gépek csatlakoztatása felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan telepítse a Windows Server virtuális gépet az Azure portál használatával. Ezután bemutatja hogyan csatlakoztathatók a virtuális gépet az Azure Active Directory tartományi szolgáltatások (az Azure AD DS) által felügyelt tartományokhoz.

## <a name="step-1-create-a-windows-server-virtual-machine"></a>1. lépés: A Windows Server virtuális gép létrehozása
A virtuális hálózat, amelyen engedélyezve vannak az Azure Active Directory tartományi szolgáltatások tartományhoz csatlakoztatott Windows virtuális gép létrehozása, tegye a következőket:

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com).
2. Válassza ki a bal oldali ablaktábla tetején **új**.
3. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.

    ![A Windows Server 2016 Datacenter hivatkozás](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Az a **alapjai** ablaktáblán, a varázsló a virtuális gép az alapvető beállításainak konfigurálása.

    ![Az alapvető beállítások panelen](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > A felhasználónév és jelszó itt vannak egy helyi rendszergazdai fiókhoz, amellyel jelentkezzen be a virtuális gépre. Válasszon ki egy erős jelszót a virtuális gép jelszavát találgatásos támadások elleni védelméhez. Ne adja meg itt egy tartományi felhasználói fiók hitelesítő adatait.
    >

5. Válassza ki a **mérete** a virtuális géphez. Válassza ki, ha további méretek **összes** , vagy módosítsa a **lemez típusát támogatott** szűrő.

    ![A "Méret kiválasztása" ablak](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Az a **beállítások** ablaktáblán válassza ki a virtuális hálózatot az Azure Active Directory tartományi szolgáltatások által kezelt tartomány van telepítve. Válassza ki azt, amelyik központilag telepítik a felügyelt tartományok különböző alhálózatokhoz. A további beállításoknál hagyja az alapértelmezett beállításokat, majd válassza ki **OK**.

    ![A virtuális gép virtuális hálózati beállításait](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Válassza ki a megfelelő virtuális hálózatot és az alhálózatot.**
    >
    > Válassza ki vagy a virtuális hálózatot, a felügyelt tartományok van telepítve, vagy egy virtuális hálózatot, amely csatlakozik, a virtuális hálózati társviszony-létesítés. Ha egy nem csatlakoztatott virtuális hálózati választja, a virtuális gép nem csatlakoztathatja a felügyelt tartományra.
    >
    > Azt javasoljuk, hogy a felügyelt tartományok telepítése egy dedikált alhálózatba. Ezért nem válassza ki az alhálózatot, amelyben engedélyezve vannak a felügyelt tartományok.

7. A további beállításoknál hagyja az alapértelmezett beállításokat, majd válassza ki **OK**.
8. Az a **beszerzési** lapon tekintse át a beállításokat, és válassza ki **OK** a virtuális gép telepítéséhez.
9. A virtuális gép üzembe helyezéséhez az Azure-portál irányítópultjának van rögzítve.

    ![Kész](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. A telepítés befejezése után megtekintheti a virtuális gép kapcsolatos információkat a **áttekintése** lap.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>2. lépés: A Windows Server virtuális gép csatlakozás a helyi rendszergazda fiók használatával
Ezután csatlakoztassa az újonnan létrehozott Windows Server virtuális gép csatlakoztatása a tartományhoz. A virtuális gép létrehozásakor adott helyi rendszergazdai hitelesítő adatokat használja.

Csatlakozzon a virtuális géphez, tegye a következőket:

1. Az a **áttekintése** ablaktáblán válassza előbb **Connect**.  
    A távoli asztal protokoll (RDP) fájl létrehozása, és le.

    ![Windows virtuális géphez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer felszólítja, válassza ki a **Connect**.
3. A bejelentkezési parancssorba írja be a **helyi rendszergazdai hitelesítő adatokat**, amely a virtuális gép létrehozásakor adott meg (például *localhost\mahesh*).
4. Ha megjelenik egy tanúsítványfigyelmeztetés, a bejelentkezési folyamat során, a kapcsolat folytatása kiválasztásával **Igen** vagy **Folytatás**.

Ezen a ponton akkor kell bejelentkeznie az újonnan létrehozott Windows rendszerű virtuális gép a helyi rendszergazdai hitelesítő adataival. A következő lépés, hogy a virtuális gép csatlakoztatása a tartományhoz.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>3. lépés: A Windows Server virtuális gépek csatlakoztatása az Azure AD DS által kezelt tartomány
A Windows Server virtuális gép csatlakoztatása az Azure AD DS által felügyelt tartomány, tegye a következőket:

1. Csatlakoztassa a Windows Server virtuális Gépet, a"2." Az a **Start** nyissa meg **Kiszolgálókezelő**.
2. A bal oldali ablaktáblán, a **Kiszolgálókezelő** ablakban válassza ki **helyi kiszolgáló**.

    ![A Kiszolgálókezelő ablakban a virtuális gépen](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. A **tulajdonságok**, jelölje be **munkacsoport**. 
4. Az a **Rendszertulajdonságok** ablakban válassza ki **módosítás** a tartományhoz való csatlakozáshoz.

    ![A Rendszertulajdonságok párbeszédpanel](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. Az a **tartomány** mezőben, adja meg az Azure Active Directory tartományi szolgáltatások által kezelt tartomány nevét, majd válassza ki **OK**.

    ![Adja meg a tartományt, amelyet egyesíteni](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. A tartományhoz való csatlakozásnál a hitelesítő adatok megadását kéri. Győződjön meg arról, hogy a hitelesítő adatokat adjon meg egy *az Azure Active Directory-Tartományvezérlők rendszergazdák csoporthoz tartozó felhasználói*. Csak a csoport tagjai jogosultak gépeket csatlakoztatni a felügyelt tartományra.

    ![A Windows rendszerbiztonsági ablakban, a hitelesítő adatok megadása](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Hitelesítő adatokat adhat meg a következő módon:

   * **Egyszerű**: (ajánlott), adja meg az egyszerű felhasználónév (UPN) utótag a felhasználói fiók az Azure AD-be. Ebben a példában a felhasználói UPN-utótagját *bob* van *bob@domainservicespreview.onmicrosoft.com*.

   * **SAMAccountName formátum**: SAMAccountName formátumban is adja meg a fiók nevét. Ebben a példában a felhasználó *bob* volna meg kell adnia *CONTOSO100\bob*.

     > [!TIP]
     > **Azt javasoljuk, hogy az egyszerű felhasználónév formátumban adja meg a hitelesítő adatokat.**
     >
     > Ha a felhasználói UPN-előtag túl hosszú (például *joehasareallylongname*), a SAMAccountName előfordulhat, hogy automatikusan generált. Ha több felhasználó azonos UPN-előtagot (például *bob*) az Azure AD-bérlő a SAMAccountName formátuma lehet a szolgáltatás által automatikusan generált. Ezekben az esetekben az UPN-formátum segítségével megbízhatóan bejelentkezni a tartományba.
     >

8. Miután sikeresen csatlakozott a tartományhoz, akkor a következő üzenet üdvözli a tartományhoz.

    ![Üdvözli a tartományhoz](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. A tartományhoz való csatlakozás befejezéséhez indítsa újra a virtuális gép.

## <a name="troubleshoot-joining-a-domain"></a>Csatlakozás egy tartományhoz hibaelhárítása
### <a name="connectivity-issues"></a>Csatlakozási problémák
Ha a virtuális gép nem található a tartomány, próbálja meg a következőket:

* Győződjön meg arról, hogy a virtuális gép csatlakozik-e az azonos virtuális hálózatban, az Azure Active Directory tartományi szolgáltatások engedélyezését. Ha az nincs csatlakoztatva, a virtuális gép nem tud csatlakozni a tartományhoz, és ezért nem tud csatlakozni a tartományhoz.

* Győződjön meg arról, hogy a virtuális gép olyan virtuális hálózaton, amely ezután csatlakozik a virtuális hálózat, amelyen engedélyezve vannak az Azure Active Directory tartományi szolgáltatások.

* Próbálja meg a tartomány pingelni a tartománynév, a felügyelt tartomány használatával (például *ping contoso100.com*). Ha nem sikerül, akkor próbálja meg Pingelje meg az IP-címek, a tartományhoz, amely az oldalon megjelenített ahol engedélyezte az Azure Active Directory tartományi szolgáltatások (például *ping 10.0.0.4*). Ha tudni pingelni a IP-címet, de nem a tartomány, DNS előfordulhat, hogy nem megfelelően van konfigurálva. Ellenőrizze, hogy az IP-címek, a tartomány a virtuális hálózat DNS-kiszolgálóként konfigurált.

* Próbálja meg a DNS-feloldási gyorsítótárból, a virtuális gépen (*ipconfig/flushdns*).

Ha egy ablakban jelenik meg, a tartományhoz való csatlakozásnál a hitelesítő adatokat kér, nincs kapcsolódási problémák.

### <a name="credentials-related-issues"></a>Hitelesítő adatok kapcsolatos problémák
Ha hiba történt a hitelesítő adatokkal, és nem tud csatlakozni a tartományhoz, próbálja meg a következők közül:

* Próbálja az UPN-formátum használatával adja meg a hitelesítő adatokat. Ha több felhasználó az Ön bérelt szolgáltatásának azonos UPN előtaggal, vagy ha az UPN-előtag túlságosan hosszú, a fiók SAMAccountName lehet automatikusan generált. Ezért a fiók SAMAccountName formátumát eltérhet mi várható és használatához el a helyszíni tartományban.

* Próbálja meg használni, amelyhez tartozik egy felhasználói fiók hitelesítő adatait. a *AAD DC rendszergazdák* csoport.

* Győződjön meg arról, hogy [engedélyezve a jelszó-szinkronizálás](active-directory-ds-getting-started-password-sync.md) megfelel-e az első lépésekről szóló útmutatót leírt lépéseket.

* Győződjön meg arról, hogy a felhasználó egyszerű Felhasználónevét az Azure AD-be (például *bob@domainservicespreview.onmicrosoft.com*) való bejelentkezéshez.

* Győződjön meg arról, hogy amíg a jelszó-szinkronizálás befejeződött, a várt az első lépésekről szóló útmutatót megadottak szerint.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure Active Directory tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Egy Azure Active Directory tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
