---
title: Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat egy Windows Server rendszerű virtuális géphez Azure Active Directory Domain Services felügyelt tartományhoz.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 4753cc9a98cd59c0c5d446b3d92280aabfb72c12
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474693"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Oktatóanyag: Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz

Azure Active Directory Domain Services (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM hitelesítés, amely teljes mértékben kompatibilis a Windows Server Active Directoryekkel. Az Azure AD DS felügyelt tartománya lehetővé teszi a tartományhoz való csatlakozást és a felügyeletet az Azure-beli virtuális gépekhez (VM). Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows Server rendszerű virtuális gépet, majd hogyan csatlakozhat egy Azure AD DS felügyelt tartományhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Windows Server rendszerű virtuális gép létrehozása
> * Kapcsolódás a Windows Server-alapú virtuális GÉPHEZ egy Azure-beli virtuális hálózathoz
> * A virtuális gép csatlakoztatása az Azure AD DS felügyelt tartományhoz

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra lesz szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory Domain Services példányt][create-azure-ad-ds-instance].
* Egy felhasználói fiók, amely tagja az Azure ad *DC-rendszergazdák* csoportnak az Azure ad-bérlőben.
    * Győződjön meg arról, hogy Azure AD Connect jelszó-kivonat szinkronizálása vagy az önkiszolgáló jelszó-visszaállítás végrehajtása megtörtént, hogy a fiók be tudja jelentkezni az Azure AD DS felügyelt tartományba.

Ha már rendelkezik egy olyan virtuális géppel, amelyet tartományhoz szeretne csatlakoztatni, ugorjon a szakaszra a [virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásához](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban egy Windows Server rendszerű virtuális gépet hoz létre, amely az Azure Portal használatával csatlakozik az Azure AD DS felügyelt tartományhoz. Első lépésként jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Hozzon létre egy Windows Server virtuális gépet

Ha szeretné megtekinteni, hogyan csatlakoztatható egy számítógép egy Azure AD DS felügyelt tartományhoz, hozzon létre egy Windows Server rendszerű virtuális gépet. Ez a virtuális gép egy Azure-beli virtuális hálózathoz csatlakozik, amely kapcsolatot biztosít az Azure AD DS felügyelt tartományával. Az Azure AD DS felügyelt tartományhoz való csatlakozás folyamata megegyezik a normál helyszíni Active Directory tartományi szolgáltatások tartományhoz való csatlakozással.

Ha már rendelkezik egy olyan virtuális géppel, amelyet tartományhoz szeretne csatlakoztatni, ugorjon a szakaszra a [virtuális gép Azure AD DS felügyelt tartományhoz való csatlakoztatásához](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. A Azure Portal menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget.

1. Az első **lépések**területen válassza a **Windows Server 2016 Datacenter**lehetőséget.

    ![Válassza a Windows Server 2016 Datacenter rendszerű virtuális gép létrehozását a Azure Portal](./media/join-windows-vm/select-vm-image.png)

1. Az **alapvető** beállítások ablakban adja meg a virtuális gép alapvető beállításait. Hagyja meg az alapértelmezett *beállításokat a rendelkezésre állási beállítások*, a *képek*és a *méretek*számára.

    | Paraméter            | Ajánlott érték   |
    |----------------------|-------------------|
    | Erőforráscsoport       | Válasszon ki vagy hozzon létre egy erőforráscsoportot, például *myResourceGroup* |
    | Virtuális gép neve | Adja meg a virtuális gép nevét, például *myVM* |
    | Régió               | Válassza ki azt a régiót, amelyben létre szeretné hozni a virtuális gépet, például: *USA keleti* régiója |
    | Felhasználónév             | Adjon meg egy felhasználónevet a virtuális gépen létrehozandó helyi rendszergazdai fiókhoz, például: *Azureus* |
    | Jelszó             | Adja meg és erősítse meg a virtuális gépen a helyi rendszergazda számára létrehozott biztonságos jelszót. Ne határozzon meg tartományi felhasználói fiók hitelesítő adatait. |

1. Alapértelmezés szerint az Azure-ban létrehozott virtuális gépek nem érhetők el az internetről. Ez a konfiguráció segít javítani a virtuális gép biztonságát, és csökkenti a lehetséges támadási területét. Az oktatóanyag következő lépéseként csatlakoznia kell a virtuális géphez a távoli asztal protokoll (RDP) használatával, majd csatlakoztatnia kell a Windows Servert az Azure AD DS felügyelt tartományhoz.

    Ha az RDP engedélyezve van, előfordulhat, hogy az automatikus bejelentkezési támadások valószínűleg megtörténnek, ami letilthatja az olyan fiókokat, amelyek közös névvel rendelkeznek *, mint például a rendszergazda vagy a* *rendszergazda* , több sikertelen bejelentkezési kísérlet miatt. Az RDP-t csak szükség esetén kell engedélyezni, és az engedélyezett IP-címtartományok körére korlátozódik. A Azure Security Center részeként [elérhető Azure][jit-access] -beli virtuálisgép-hozzáférés lehetővé teszi ezeket a rövid életű, korlátozott RDP-munkameneteket. [Létrehozhat és használhat egy Azure Bastion-gazdagépet (amely jelenleg előzetes][azure-bastion] verzióban érhető el) ahhoz, hogy csak az SSL protokollon keresztül engedélyezze a hozzáférést a Azure Portal.

    Ebben az oktatóanyagban manuálisan engedélyezze a virtuális gép RDP-kapcsolatait.

    A **nyilvános bejövő portok**területen válassza a **kiválasztott portok engedélyezésének**lehetőségét. A **bejövő portok kiválasztása**legördülő menüben válassza az *RDP (3389)* lehetőséget.

1. Ha elkészült, válassza a **Tovább: lemezek**elemet.
1. Az **operációsrendszer-lemez típusa**legördülő menüben válassza a *standard SSD*, majd a **Tovább: hálózatkezelés**lehetőséget.
1. A virtuális gépnek csatlakoznia kell egy olyan Azure-beli virtuális hálózati alhálózathoz, amely képes kommunikálni az Azure AD DS felügyelt tartományához tartozó alhálózattal. Javasoljuk, hogy egy Azure AD DS felügyelt tartományt a saját dedikált alhálózatán telepítsen. Ne telepítse a virtuális gépet ugyanabban az alhálózatban, mint az Azure AD DS felügyelt tartományát.

    A virtuális gép üzembe helyezésének két fő módja van, és csatlakozhat a megfelelő virtuális hálózati alhálózathoz:
    
    * Hozzon létre egy meglévő alhálózatot, vagy válasszon ki egy olyan virtuális hálózatot, amelyben az Azure AD DS felügyelt tartománya telepítve van.
    * Válasszon egy alhálózatot egy Azure-beli virtuális hálózatban, amely az [Azure Virtual Network][vnet-peering]-társítás használatával csatlakozik hozzá.
    
    Ha olyan virtuális hálózati alhálózatot választ, amely nem kapcsolódik az Azure AD DS-példány alhálózatához, nem csatlakoztathatja a virtuális gépet a felügyelt tartományhoz. Ebben az oktatóanyagban hozzunk létre egy új alhálózatot az Azure Virtual Networkben.

    A **hálózatkezelés** ablaktáblán válassza ki azt a virtuális hálózatot, amelyben az Azure AD DS felügyelt tartománya telepítve van, például *aaads-vnet*
1. Ebben a példában a meglévő *aaads-alhálózat* látható, hogy az Azure AD DS felügyelt tartománya csatlakoztatva van. Ne kapcsolódjon a virtuális géphez ehhez az alhálózathoz. Ha alhálózatot szeretne létrehozni a virtuális géphez, válassza az **alhálózat konfigurációjának kezelése**lehetőséget.

    ![Válassza az alhálózat konfigurációjának kezelését a Azure Portal](./media/join-windows-vm/manage-subnet.png)

1. A virtuális hálózat ablak bal oldali menüjében válassza a **címterület**lehetőséget. A virtuális hálózat egy *10.0.1.0/24*-es címtartomány használatával jön létre, amelyet az alapértelmezett alhálózat használ.

    Adjon hozzá egy további IP-címtartományt a virtuális hálózathoz. A címtartomány mérete és a használni kívánt IP-címtartomány a már telepített többi hálózati erőforrástól függ. Az IP-címtartomány nem fedi át az Azure-ban vagy a helyszíni környezetben lévő összes meglévő címtartományt. Győződjön meg arról, hogy az IP-címtartomány elég nagy méretű az alhálózatba telepítendő virtuális gépek számához.

    A következő példában egy *10.0.2.0/24* további IP-címtartomány van hozzáadva. Ha elkészült, válassza a **Mentés**lehetőséget.

    ![További virtuális hálózati IP-címtartomány hozzáadása a Azure Portal](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Ezután a virtuális hálózat ablak bal oldali menüjében válassza az **alhálózatok**elemet, majd válassza az **+ alhálózat** lehetőséget az alhálózat hozzáadásához.

1. Válassza az **+ alhálózat**lehetőséget, majd adja meg az alhálózat nevét (például *felügyelet*). Adja meg a **címtartományt (CIDR blokk)** , például *10.0.2.0/24*. Győződjön meg arról, hogy ez az IP-címtartomány nem fedi át a többi meglévő Azure-vagy helyszíni címtartományt. Hagyja meg a többi beállítást alapértelmezett értékként, majd kattintson az **OK gombra**.

    ![Alhálózat-konfiguráció létrehozása a Azure Portalban](./media/join-windows-vm/create-subnet.png)

1. Az alhálózat létrehozása eltarthat néhány másodpercig. A létrehozás után válassza az X- *et* az alhálózat ablak bezárásához.
1. A **hálózatkezelés** ablaktáblán a virtuális gép létrehozásához válassza ki a létrehozott alhálózatot a legördülő menüből, például a *felügyelet*lehetőséget. Győződjön meg arról, hogy a megfelelő alhálózatot választja, és ne telepítse a virtuális gépet ugyanabban az alhálózatban, mint az Azure AD DS felügyelt tartománya.
1. Hagyja meg a többi beállítást alapértelmezett értékként, majd válassza a **felügyelet**lehetőséget.
1. Állítsa *ki*a **rendszerindítási diagnosztikát** . Hagyja meg a többi beállítást alapértelmezett értékként, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.
1. Tekintse át a virtuális gép beállításait, majd kattintson a **Létrehozás**gombra.

A virtuális gép létrehozása néhány percet vesz igénybe. A Azure Portal az üzemelő példány állapotát jeleníti meg. Ha a virtuális gép elkészült, válassza az **Ugrás az erőforráshoz**lehetőséget.

![A virtuális gép erőforrásának megugrása a Azure Portal a sikeres létrehozás után](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Kapcsolódás a Windows Server rendszerű virtuális géphez

Most csatlakozzon az újonnan létrehozott Windows Server rendszerű virtuális géphez RDP használatával, és csatlakozzon az Azure AD DS felügyelt tartományhoz. Használja azokat a helyi rendszergazdai hitelesítő adatokat, amelyeket a virtuális gép előző lépésben való létrehozásakor adott meg, nem pedig a meglévő tartományi hitelesítő adatokat.

1. Az **Áttekintés** panelen válassza a **kapcsolat**lehetőséget.

    ![Kapcsolódás a Windows rendszerű virtuális géphez a Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Válassza az RDP- *fájl letöltésének*lehetőségét. Mentse ezt az RDP-fájlt a böngészőjébe.
1. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.
1. Adja meg az előző lépésben megadott helyi rendszergazdai hitelesítő adatokat a virtuális gép létrehozásához, például *localhost\azureuser*
1. Ha a bejelentkezési folyamat során megjelenik a tanúsítványra vonatkozó figyelmeztetés, válassza az **Igen** lehetőséget, vagy **folytassa** a csatlakozást.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>A virtuális gép csatlakoztatása az Azure AD DS felügyelt tartományhoz

Ha létrehozta a virtuális gépet, és létrehoz egy RDP-kapcsolatot, most csatlakoztassa a Windows Server rendszerű virtuális gépet az Azure AD DS felügyelt tartományhoz. Ez a folyamat megegyezik egy normál helyszíni Active Directory tartományi szolgáltatások tartományhoz csatlakozó számítógéppel.

1. Ha a **Kiszolgálókezelő** alapértelmezés szerint nem nyílik meg, amikor bejelentkezik a virtuális gépre, válassza a **Start** menüt, majd a **Kiszolgálókezelő**elemet.
1. A **Kiszolgálókezelő** ablak bal oldali paneljén válassza a **helyi kiszolgáló**lehetőséget. A jobb oldali ablaktábla **Tulajdonságok** területén válassza a **munkacsoport**lehetőséget.

    ![Nyissa meg a Kiszolgálókezelő eszközt a virtuális gépen, és szerkessze a munkacsoport tulajdonságot](./media/join-windows-vm/server-manager.png)

1. A **rendszer tulajdonságai** ablakban válassza a **módosítás** lehetőséget az Azure AD DS felügyelt tartományhoz való csatlakozáshoz.

    ![Válassza a munkacsoport vagy a tartomány tulajdonságainak módosítását](./media/join-windows-vm/change-domain.png)

1. A **tartomány** mezőben adja meg az Azure AD DS felügyelt tartomány nevét, például *contoso.com*, majd kattintson **az OK gombra**.

    ![Az Azure AD DS felügyelt tartományának meghatározása a csatlakozáshoz](./media/join-windows-vm/join-domain.png)

1. Adja meg a tartományhoz való csatlakozáshoz szükséges tartományi hitelesítő adatokat. Használja az *Azure ad DC-rendszergazdák* csoportjához tartozó felhasználó hitelesítő adatait. Csak a csoport tagjai jogosultak a gépek Azure AD DS felügyelt tartományhoz való csatlakoztatására. A fióknak az Azure AD DS felügyelt tartományhoz vagy Azure AD-bérlőhöz kell tartoznia – az Azure AD-bérlőhöz társított külső könyvtárak fiókjai nem tudnak helyesen hitelesíteni a tartományhoz való csatlakozás során. A fiók hitelesítő adatai a következő módszerek egyikével adhatók meg:

    * **UPN formátuma** (ajánlott) – Itt adhatja meg az Azure ad-ben konfigurált felhasználói fiókhoz tartozó egyszerű felhasználónév (UPN) utótagot. Például a felhasználó *CONTOSOADMIN* UPN-utótagja `contosoadmin@contoso.onmicrosoft.com`. Létezik néhány gyakori felhasználási eset, ha az UPN formátuma megbízhatóan használható a tartományba való bejelentkezéshez a *sAMAccountName* formátum helyett:
        * Ha a felhasználó UPN-előtagja hosszú (például *deehasareallylongname*), akkor előfordulhat, hogy a *sAMAccountName* automatikusan létrejön.
        * Ha több felhasználó ugyanazzal az UPN-előtaggal rendelkezik az Azure AD-bérlőben, például a *Dee*-ben, akkor előfordulhat, hogy az *sAMAccountName* -formátuma automatikusan létrejön.
    * **SAMAccountName formátum** – adja meg a fiók nevét a *sAMAccountName* formátumban. Például a felhasználói contosoadmin *SAMAccountName* `CONTOSO\contosoadmin`.

1. Az Azure AD DS felügyelt tartományhoz való csatlakozás néhány másodpercig tart. Ha elkészült, a következő üzenet üdvözli Önt a tartományban:

    ![Üdvözli a tartomány](./media/join-windows-vm/join-domain-successful.png)

    A folytatáshoz kattintson az **OK** gombra.

1. Az Azure AD DS felügyelt tartományhoz való csatlakozás folyamatának befejezéséhez indítsa újra a virtuális gépet.

> [!TIP]
> Tartományhoz csatlakoztathat egy virtuális gépet a PowerShell használatával a [Add-Computer][add-computer] parancsmaggal. A következő példa csatlakozik a *contoso* tartományhoz, majd újraindítja a virtuális gépet. Ha a rendszer kéri, adja meg az *Azure ad DC-rendszergazdák* csoportjához tartozó felhasználó hitelesítő adatait:
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Ha tartományhoz szeretne csatlakozni egy virtuális géphez a csatlakoztatása nélkül, és manuálisan konfigurálja a kapcsolatot, használhatja a [set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell parancsmagot.

A Windows Server rendszerű virtuális gép újraindítása után az Azure AD DS felügyelt tartományban alkalmazott házirendeket a rendszer leküldi a virtuális géphez. Most már bejelentkezhet a Windows Server rendszerű virtuális gépre is a megfelelő tartományi hitelesítő adatok használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő oktatóanyagban ezt a Windows Server rendszerű virtuális gépet használja az Azure AD DS felügyelt tartomány felügyeletét lehetővé teszi a felügyeleti eszközök telepítéséhez. Ha nem szeretné folytatni az oktatóanyag-sorozatot, tekintse át a következő karbantartási lépéseket az [RDP letiltásához](#disable-rdp) vagy [a virtuális gép törléséhez](#delete-the-vm). Ellenkező esetben [folytassa a következő oktatóanyaggal](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Csatlakozás a virtuális géphez az Azure AD DS felügyelt tartományból

Ha el szeretné távolítani a virtuális gépet az Azure AD DS felügyelt tartományból, kövesse újra a lépéseket a [virtuális gép tartományhoz való csatlakoztatásához](#join-the-vm-to-the-azure-ad-ds-managed-domain). Az Azure AD DS felügyelt tartományhoz való csatlakozás helyett válassza a munkacsoporthoz való csatlakozást, például az alapértelmezett *munkacsoportot*. A virtuális gép újraindítása után a számítógép-objektum el lesz távolítva az Azure AD DS felügyelt tartományból.

Ha [törli a virtuális gépet](#delete-the-vm) a tartományból való leválasztás nélkül, egy árva számítógép-objektum marad az Azure AD DS.

### <a name="disable-rdp"></a>RDP letiltása

Ha továbbra is az ebben az oktatóanyagban létrehozott Windows Server-alapú virtuális gépet használja a saját alkalmazások vagy munkaterhelések futtatásához, hívja meg az RDP-t az interneten keresztül. A biztonság javítása és a támadás kockázatának csökkentése érdekében az RDP-t le kell tiltani az interneten keresztül. Ha le szeretné tiltani az RDP-t a Windows Server rendszerű virtuális gépen az interneten keresztül, hajtsa végre a következő lépéseket:

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki az erőforráscsoportot, például *myResourceGroup*.
1. Válassza ki a virtuális gépet, például *myVM*, majd válassza a *hálózatkezelés*lehetőséget.
1. A hálózati biztonsági csoport **bejövő hálózati biztonsági szabályok** területén válassza ki azt a szabályt, amely engedélyezi az RDP-t, majd válassza a **Törlés**lehetőséget. Eltarthat néhány másodpercig a bejövő biztonsági szabály eltávolításához.

### <a name="delete-the-vm"></a>A virtuális gép törlése

Ha nem használja ezt a Windows Server-alapú virtuális gépet, törölje a virtuális gépet a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.
1. Válassza ki az erőforráscsoportot, például *myResourceGroup*.
1. Válassza ki a virtuális gépet, például *myVM*, majd válassza a **Törlés**lehetőséget. Válassza az **Igen** lehetőséget az erőforrás törlésének megerősítéséhez. A virtuális gép törlése néhány percet vesz igénybe.
1. A virtuális gép törlésekor válassza ki az operációsrendszer-lemezt, a hálózati adaptert és minden más erőforrást a *myVM-* előtaggal, és törölje azokat.

## <a name="troubleshoot-domain-join-issues"></a>A tartományhoz való csatlakozással kapcsolatos problémák elhárítása

A Windows Server rendszerű virtuális gépnek sikeresen csatlakoznia kell az Azure AD DS felügyelt tartományhoz, ugyanúgy, mint a normál helyi számítógép Active Directory tartományi szolgáltatások tartományhoz. Ha a Windows Server rendszerű virtuális gép nem tud csatlakozni az Azure AD DS felügyelt tartományhoz, az azt jelzi, hogy van-e kapcsolat vagy hitelesítő adatokkal kapcsolatos probléma. A felügyelt tartomány sikeres csatlakoztatásához tekintse át a következő hibaelhárítási szakaszt.

### <a name="connectivity-issues"></a>Kapcsolódási problémák

Ha nem kap olyan kérést, amely megkéri a hitelesítő adatok megadását a tartományhoz való csatlakozáshoz, fennáll a kapcsolódási probléma. A virtuális gép nem tudja elérni az Azure AD DS felügyelt tartományát a virtuális hálózaton.

A hibaelhárítási lépések elvégzése után próbáljon újra csatlakozni a Windows Server rendszerű virtuális géphez a felügyelt tartományhoz.

* Ellenőrizze, hogy a virtuális gép ugyanahhoz a virtuális hálózathoz csatlakozik-e, amelyhez az Azure AD DS engedélyezve van, vagy rendelkezik-e társ hálózati kapcsolattal.
* Próbálja meg pingelni a felügyelt tartomány DNS-tartománynevét, például `ping contoso.com`.
    * Ha a pingelési kérelem sikertelen, próbálja meg pingelni a felügyelt tartomány IP-címeit, például `ping 10.0.0.4`. A környezet IP-címe a *Tulajdonságok* lapon jelenik meg, amikor kiválasztja az Azure AD DS felügyelt tartományt az Azure-erőforrások listájából.
    * Ha pingelni tudja az IP-címet, de nem a tartományt, a DNS helytelenül van konfigurálva. Győződjön meg arról, hogy a felügyelt tartomány IP-címei a virtuális hálózat DNS-kiszolgálóiként vannak konfigurálva.
* Próbálja meg a DNS-feloldó gyorsítótár kiürítését a virtuális gépen a `ipconfig /flushdns` parancs használatával.

### <a name="credentials-related-issues"></a>Hitelesítő adatokkal kapcsolatos problémák

Ha olyan kérést kap, amely megkéri a hitelesítő adatok megadását a tartományhoz való csatlakozáshoz, de a hitelesítő adatok megadása után hibaüzenet jelenik meg, a virtuális gép képes csatlakozni az Azure AD DS felügyelt tartományhoz. A megadott hitelesítő adatok nem teszik lehetővé, hogy a virtuális gép csatlakozzon az Azure AD DS felügyelt tartományhoz.

A hibaelhárítási lépések elvégzése után próbáljon újra csatlakozni a Windows Server rendszerű virtuális géphez a felügyelt tartományhoz.

* Győződjön meg arról, hogy a megadott felhasználói fiók az *HRE DC-rendszergazdák* csoport tagja.
* Győződjön meg arról, hogy a fiók az Azure AD DS felügyelt tartományhoz vagy az Azure AD-bérlőhöz tartozik. Az Azure AD-bérlőhöz társított külső könyvtárak fiókjai nem tudják megfelelően hitelesíteni magukat a tartományhoz való csatlakozás során.
* Próbálja meg az UPN formátumot használni a hitelesítő adatok megadásához, például `contosoadmin@contoso.onmicrosoft.com`. Ha sok felhasználó rendelkezik ugyanazzal az UPN-előtaggal a bérlőben, vagy ha az UPN-előtag túl hosszú, akkor előfordulhat, hogy a fiók *sAMAccountName* automatikusan létrejön. Ezekben az esetekben előfordulhat, hogy a fiók *sAMAccountName* formátuma eltér a helyszíni tartományban várttól vagy használattól.
* Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást][password-sync] a felügyelt tartományhoz. A konfigurációs lépés nélkül a szükséges jelszó-kivonatok nem jelennek meg az Azure AD DS felügyelt tartományában, hogy megfelelően hitelesítse a bejelentkezési kísérletet.
* Várjon, amíg a jelszó-szinkronizálás be nem fejeződik. A felhasználói fiók jelszavának módosításakor az Azure AD automatikus háttérben történő szinkronizálása frissíti az Azure AD DSban található jelszót. Időbe telik, amíg a jelszó elérhetővé válik a tartományhoz való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Windows Server rendszerű virtuális gép létrehozása
> * Kapcsolódás a Windows Server-alapú virtuális GÉPHEZ egy Azure-beli virtuális hálózathoz
> * A virtuális gép csatlakoztatása az Azure AD DS felügyelt tartományhoz

Az Azure AD DS felügyelt tartományának felügyeletéhez konfigurálja a felügyeleti virtuális gépet a Active Directory felügyeleti központ (ADAC) használatával.

> [!div class="nextstepaction"]
> [Felügyeleti eszközök telepítése felügyeleti virtuális gépen](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
