---
title: "Az Azure Active Directory tartományi szolgáltatások: A Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz |} Microsoft Docs"
description: "A Windows Server virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: maheshu
ms.openlocfilehash: 1ea3f7271bd165bf42d520e4a0267a80dcca58d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server virtuális gépek csatlakoztatása felügyelt tartományokhoz
Ez a cikk bemutatja, hogyan telepítse a Windows Server virtuális gépet az Azure portál használatával. Ezután bemutatja hogyan csatlakoztathatók a virtuális gépet az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

## <a name="step-1-create-the-windows-server-virtual-machine"></a>1. lépés: A Windows Server virtuális gép létrehozása
A következő lépésekkel, amelyben engedélyezve van a Azure AD tartományi szolgáltatások a virtuális hálózathoz csatlakozó Windows virtuális gép létrehozása.

1. Jelentkezzen be az Azure Portalra a [http://portal.azure.com](http://portal.azure.com) webhelyen.
2. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.
3. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet.

    ![Válassza ki a lemezképet](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)
4. Az alapszintű beállításokat a virtuális gép a **alapjai** a varázsló.

    ![Konfigurálja az alapbeállításokat a virtuális gép](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > A felhasználónév és jelszó itt egy helyi rendszergazdai fiók használatával jelentkezzen be a virtuális gép van. Válasszon ki egy erős jelszót a virtuális gép jelszavát találgatásos támadások elleni védelméhez. Ne adja meg itt egy tartományi felhasználói fiók hitelesítő adatait.
    >

5. Válassza ki a **mérete** a virtuális géphez. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt.

    ![A virtuális gép méretének kiválasztása](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Az a **beállítások** a varázsló, jelölje be a virtuális hálózat az Azure AD tartományi szolgáltatások által felügyelt tartomány van telepítve. Válassza ki az egyik központilag telepítik a felügyelt tartományok különböző alhálózatokhoz. A további beállításoknál hagyja az alapértelmezett beállításokat, és kattintson **OK**.

    ![Válassza ki a virtuális gép virtuális hálózatot](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Válassza ki a megfelelő virtuális hálózatot és az alhálózatot.**
    > Vagy a felügyelt tartományok van telepítve a virtuális hálózatot választ, vagy egy virtuális hálózatot, virtuális használatával csatlakoztatott hálózati társviszony-létesítés. Ha egy nem csatlakoztatott virtuális hálózati választja, a virtuális gép nem csatlakoztathatja a felügyelt tartományra.
    > Azt javasoljuk, hogy a felügyelt tartományok telepítése egy dedikált alhálózatba. Ezért nem válassza ki az alhálózatot, amelyben engedélyezve vannak a felügyelt tartományok.

7. Az a **beszerzési** lapon tekintse át a beállításokat, majd kattintson **OK** a virtuális gép telepítéséhez.
8. A virtuális gép üzembe helyezéséhez az Azure-portál irányítópultjának van rögzítve.

    ![Kész](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
9. A telepítés befejezése után a virtuális gép információkat láthatja a **áttekintése** lap.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-using-the-local-administrator-account"></a>2. lépés: Csatlakozás a Windows Server virtuális gép a helyi rendszergazda fiók használatával
Most az újonnan létrehozott Windows Server virtuális gép csatlakoztatása a tartományhoz való csatlakozás. A virtuális gép létrehozásakor megadott helyi rendszergazdai hitelesítő adatokat használja.

A következő lépésekkel csatlakozzon a virtuális géphez.

1. Kattintson a **Connect** gombra a **áttekintése** lap. A távoli asztal protokoll (RDP) fájl létrehozása, és le.

    ![Windows virtuális géphez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra.
3. A bejelentkezési parancssorba írja be a **helyi rendszergazdai hitelesítő adatokat**, amely a virtuális gép létrehozása a megadott. Például ebben a példában "localhost\mahesh" már használtuk.
4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Kattintson az Igen gombra, vagy folytassa a kapcsolat.

Ezen a ponton be kell jelentkeznie az újonnan létrehozott Windows rendszerű virtuális gép helyi rendszergazdai hitelesítő adatokkal való. A következő lépés, hogy a virtuális gép csatlakoztatása a tartományhoz.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-aad-ds-managed-domain"></a>3. lépés: Csatlakozás az AAD-felügyelt tartományi szolgáltatások a Windows Server virtuális gépen
Hajtsa végre az alábbi lépéseket a Windows Server virtuális gép csatlakoztatása az AAD-Directory tartományi szolgáltatások által felügyelt tartományokhoz.

1. Csatlakoztassa a Windows Server a 2. A kezdőképernyőről nyissa meg a **Kiszolgálókezelő**.
2. Kattintson a **helyi kiszolgáló** a Kiszolgálókezelő ablakban a bal oldali ablaktáblán.

    ![Indítsa el a Kiszolgálókezelőt a virtuális gépen](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)
3. Kattintson a **munkacsoport** alatt a **tulajdonságok** szakasz. Az a **Rendszertulajdonságok** lapot, kattintson a **módosítása** a tartományhoz való csatlakozáshoz.

    ![Rendszer Tulajdonságok lap](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)
4. Adja meg az Azure AD tartományi szolgáltatások által kezelt tartomány a tartomány nevét a **tartomány** szövegmezőben kattintson **OK**.

    ![Adja meg a tartományt, amelyet egyesíteni](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)
5. A tartományhoz való csatlakozásnál a hitelesítő adatok megadását kéri. Győződjön meg arról, hogy **adja meg a hitelesítő adatait az aad-ben DC rendszergazdák tartozó felhasználó** csoport. Csak a csoport tagjai jogosultak gépeket csatlakoztatni a felügyelt tartományra.

    ![Adja meg a tartományhoz való csatlakozás hitelesítő adatait](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)
6. Hitelesítő adatokat adhat meg a következő módon:

   * **UPN-formátum: (ajánlott)** adja meg a felhasználói fiókhoz az egyszerű Felhasználónévi utótagot az Azure AD-be. Ebben a példában a "bob" felhasználói UPN-utótagját van "bob@domainservicespreview.onmicrosoft.com".
   * **SAMAccountName formátum:** SAMAccountName formátumban is adja meg a fiók nevét. Ebben a példában a felhasználó "bob" kell "CONTOSO100\bob" adja meg.

     > [!TIP]
     > **Azt javasoljuk, hogy az egyszerű felhasználónév formátumban adja meg a hitelesítő adatokat.**
     > Ha a felhasználói UPN-előtag túl hosszú (például az "joehasareallylongname"), a SAMAccountName lehet automatikusan generált. Ha több felhasználó azonos UPN-előtagot (például Bálint) az Azure AD-bérlő rendelkezik, azok SAMAccountName formátuma lehet a szolgáltatás által automatikusan generált. Ezekben az esetekben az UPN-formátum használható megbízhatóan bejelentkezni a tartományba.
     >

7. Tartományhoz való csatlakozást befejezését követően megjelenik a következő üzenet, és Üdvözli Önt a tartományban. Indítsa újra a virtuális gépet, a tartományhoz csatlakoztatás művelet elvégzéséhez.

    ![Üdvözli a tartományhoz](./media/active-directory-domain-services-admin-guide/join-domain-done.png)


## <a name="troubleshooting-domain-join"></a>Hibaelhárítás a tartományhoz való csatlakozást
### <a name="connectivity-issues"></a>Csatlakozási problémák
Ha a virtuális gép nem található a tartomány, tekintse meg a következő hibaelhárítási lépéseket:

* Győződjön meg arról, hogy a virtuális gép csatlakozik ugyanahhoz a virtuális hálózathoz, mint a tartományi szolgáltatások engedélyezése. Ha nem, akkor a virtuális gép nem tud csatlakozni a tartományhoz, és ezért nem tud csatlakozni a tartományhoz.
* Helyezze a virtuális gép egy virtuális hálózatot, amely ezután csatlakozik a virtuális hálózat, amelyen engedélyezve van a tartományi szolgáltatások.
* Próbálja pingelni a tartományban, a tartománynév, a felügyelt tartomány (például "ping contoso100.com") használatával. Ha nem sikerül, akkor próbálja meg Pingelje meg az IP-címek, amelyen engedélyezve van az Azure AD tartományi szolgáltatásokat az oldalon megjelenített tartomány (például "Pingelje meg 10.0.0.4"). Ha tudni pingelni a IP-címet, de nem a tartomány, DNS esetleg nem megfelelően van konfigurálva. Ellenőrizze, hogy az IP-címek, a tartomány a virtuális hálózat DNS-kiszolgálóként konfigurációja.
* Próbálja meg a DNS-feloldási gyorsítótárból a virtuális gépen ("ipconfig/flushdns" paranccsal).

Ha a párbeszédpanelt, amely a tartományhoz való csatlakozásnál a hitelesítő adatokat kér, nincs kapcsolódási problémák.

### <a name="credentials-related-issues"></a>Hitelesítő adatok kapcsolatos problémák
Ha hiba történt a hitelesítő adatokkal, és nem tud csatlakozni a tartományhoz tekintse meg az alábbi lépéseket.

* Próbálja az UPN-formátum használatával adja meg a hitelesítő adatokat. Ha több felhasználó az Ön bérelt szolgáltatásának azonos UPN előtaggal, vagy ha az UPN-előtag túlságosan hosszú, a fiók SAMAccountName lehet automatikusan generált. Ezért a fiók SAMAccountName formátumát eltérhet mi várható és használatához el a helyszíni tartományban.
* Próbálja meg a "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók hitelesítő adatait.
* Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást](active-directory-ds-getting-started-password-sync.md) az első lépéseket ismertető útmutató lépéseinek megfelelően.
* Győződjön meg arról, hogy a felhasználó egyszerű Felhasználónevét az Azure AD-be (például "bob@domainservicespreview.onmicrosoft.com") való bejelentkezéshez.
* Győződjön meg arról, hogy amíg a jelszó-szinkronizálás végrehajtásához az első lépések útmutató a várt.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
