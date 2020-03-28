---
title: Csatlakozás Windows Server virtuális géphez felügyelt tartományhoz | Microsoft dokumentumok
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat egy Windows Server-virtuális géphez egy Azure Active Directory tartományi szolgáltatások által felügyelt tartományhoz.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2020
ms.author: iainfou
ms.openlocfilehash: f853d6d59a4c23b7b52a2a0ba800ace58c997f6e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481585"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Oktatóanyag: Csatlakozás Windows Server rendszerű virtuális géphez felügyelt tartományhoz

Az Azure Active Directory tartományi szolgáltatások (Azure AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint a tartományhoz való csatlakozás, a csoportházirend, az LDAP, a Kerberos/NTLM-hitelesítés, amelyek teljes mértékben kompatibilisek a Windows Server Active Directoryval. Az Azure AD DS felügyelt tartományhasználatával tartománycsatlakozási funkciókat és felügyeletet biztosíthat az Azure-beli virtuális gépekhez. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Windows Server virtuális gép, majd csatlakozzon hozzá egy Azure AD DS felügyelt tartományhoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Windows Server virtuális gép létrehozása
> * A Windows Server virtuális gép csatlakoztatása egy Azure virtuális hálózathoz
> * Csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományához

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő erőforrásokra van szükség:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Az előfizetéshez társított Azure Active Directory-bérlő, amely et egy helyszíni könyvtárral vagy egy csak felhőbeli könyvtárral szinkronizált.
    * Szükség esetén [hozzon létre egy Azure Active Directory-bérlőt,][create-azure-ad-tenant] vagy [társítson egy Azure-előfizetést a fiókjához.][associate-azure-ad-tenant]
* Az Azure Active Directory tartományi szolgáltatások felügyelt tartomány a konfigurált és konfigurált az Azure AD-bérlő.
    * Szükség esetén [hozzon létre és konfiguráljon egy Azure Active Directory tartományi szolgáltatások példányát.][create-azure-ad-ds-instance]
* Egy felhasználói fiók, amely az Azure AD DS felügyelt tartomány ának része.
    * Győződjön meg arról, hogy az Azure AD Connect jelszókivonat-szinkronizálás vagy önkiszolgáló jelszó-visszaállítás történt, így a fiók képes bejelentkezni az Azure AD DS felügyelt tartományba.
* Az Azure AD DS virtuális hálózatában üzembe helyezett Azure Bastion gazdagép.
    * Szükség esetén [hozzon létre egy Azure-bástya-gazdat.][azure-bastion]

Ha már rendelkezik egy virtuális gép, amely tartományhoz szeretne csatlakozni, ugorjon a szakaszhoz, hogy [csatlakozzon a virtuális gép hez az Azure AD DS felügyelt tartományhoz.](#join-the-vm-to-the-azure-ad-ds-managed-domain)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Ebben az oktatóanyagban hozzon létre egy Windows Server virtuális gép, hogy csatlakozzon az Azure AD DS felügyelt tartományhoz az Azure Portalon keresztül. Első lépésekhez először jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-windows-server-virtual-machine"></a>Windows Server virtuális gép létrehozása

Ha meg szeretné tekinteni, hogyan csatlakozhat egy számítógéphez egy Azure AD DS felügyelt tartományhoz, hozzon létre egy Windows Server virtuális gép. Ez a virtuális gép egy Azure virtuális hálózathoz csatlakozik, amely kapcsolatot biztosít az Azure AD DS felügyelt tartományához. Az Azure AD DS felügyelt tartományhoz való csatlakozás folyamata megegyezik egy rendszeres helyszíni Active Directory tartományi szolgáltatások tartományhoz való csatlakozással.

Ha már rendelkezik egy virtuális gép, amely tartományhoz szeretne csatlakozni, ugorjon a szakaszhoz, hogy [csatlakozzon a virtuális gép hez az Azure AD DS felügyelt tartományhoz.](#join-the-vm-to-the-azure-ad-ds-managed-domain)

1. Az Azure Portal menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.

1. Az **Első lépések területen**válassza a Windows Server **2016 Datacenter**lehetőséget.

    ![Válassza ki, hogy hozzon létre egy Windows Server 2016 Datacenter virtuális gép az Azure Portalon](./media/join-windows-vm/select-vm-image.png)

1. Az **Alapok ablakban** konfigurálja a virtuális gép alapvető beállításait. Hagyja meg az Alapértelmezett értékeket a *Rendelkezésre állási,* *a Kép*és a *Méret beállításhoz.*

    | Paraméter            | Ajánlott érték   |
    |----------------------|-------------------|
    | Erőforráscsoport       | Erőforráscsoport, például *myResourceGroup* kiválasztása vagy létrehozása |
    | Virtuális gép neve | Adja meg a virtuális gép nevét, például *a myVM-et* |
    | Régió               | Válassza ki a régiót, amelyben létre szeretné hozni a virtuális gépét, például *az USA keleti régióját* |
    | Felhasználónév             | Adja meg a helyi rendszergazdai fiók hoz létre a virtuális gép, például *azureuser* |
    | Jelszó             | Adja meg, majd erősítse meg a biztonságos jelszót a helyi rendszergazda számára, hogy hozzon létre a virtuális gép. Ne adja meg a tartományi felhasználói fiók hitelesítő adatait. |

1. Alapértelmezés szerint az Azure-ban létrehozott virtuális gépek rdp használatával érhetők el az internetről. Ha az RDP engedélyezve van, automatikus bejelentkezési támadások valószínűleg előfordulnak, ami letilthatja a közös nevű fiókokat, például *a rendszergazda* vagy *a rendszergazda* több sikertelen, egymást követő bejelentkezési kísérlet miatt.

    Az RDP csak akkor engedélyezhető, ha szükséges, és engedélyezett IP-tartományokra korlátozva. Ez a konfiguráció segít a virtuális gép biztonságának javításában, és csökkenti a potenciális támadások területét. Vagy hozzon létre és használjon egy Azure Bastion-állomást, amely csak az Azure Portalon keresztül engedélyezi a hozzáférést a TLS-en keresztül. Az oktatóanyag következő lépésében egy Azure-bástya-gazdagép használatával biztonságosan csatlakozhat a virtuális géphez.

    Egyelőre tiltsa le a virtuális gép közvetlen RDP-kapcsolatait.

    A **Nyilvános bejövő portok csoportban**válassza a *Nincs*lehetőséget.

1. Ha elkészült, válassza a **Tovább: Lemezek**lehetőséget.
1. Az **operációs rendszer lemeztípusának**legördülő menüjében válassza a *Szabványos SSD*lehetőséget, majd válassza a **Tovább: Hálózat lehetőséget.**
1. A virtuális gépnek csatlakoznia kell egy Azure virtuális hálózati alhálózathoz, amely képes kommunikálni az Azure AD DS felügyelt tartományában üzembe helyezett alhálózattal. Azt javasoljuk, hogy egy Azure AD DS felügyelt tartomány telepítve van a saját dedikált alhálózat. Ne telepítse a virtuális gép ugyanabban az alhálózatban, mint az Azure AD DS felügyelt tartomány.

    A virtuális gép üzembe helyezésének és a megfelelő virtuális hálózati alhálózathoz való csatlakozásának két fő módja van:
    
    * Hozzon létre egy, vagy válasszon ki egy meglévő alhálózatot ugyanabban a virtuális hálózatban, mint az Azure AD DS felügyelt tartomány a telepített.
    * Válasszon egy alhálózatot egy azure-beli virtuális hálózatban, amely [az Azure virtuális hálózati társviszony-létesítésével][vnet-peering]csatlakozik hozzá.
    
    Ha olyan virtuális hálózati alhálózatot választ, amely nem csatlakozik az Azure AD DS-példány alhálózatához, nem csatlakozhat a virtuális géphez a felügyelt tartományhoz. Ebben az oktatóanyagban hozzon létre egy új alhálózatot az Azure virtuális hálózatában.

    A **Hálózat ablaktáblán** válassza ki azt a virtuális hálózatot, amelyben az Azure AD DS által felügyelt tartomány telepítve van, például *az aaads-vnet*
1. Ebben a példában a meglévő *aaads-alhálózat* látható, amely az Azure AD DS felügyelt tartomány csatlakozik. Ne csatlakoztassa a virtuális gép ét erről az alhálózatról. Ha alhálózatot szeretne létrehozni a virtuális géphez, válassza **az Alhálózat-konfiguráció kezelése lehetőséget.**

    ![Válassza ki az alhálózati konfiguráció kezelését az Azure Portalon](./media/join-windows-vm/manage-subnet.png)

1. A virtuális hálózat ablakának bal oldali menüjében válassza a **Címterület lehetőséget.** A virtuális hálózat egyetlen címterülettel jön *létre: 10.0.1.0/24*, amelyet az alapértelmezett alhálózat használ.

    Adjon hozzá egy további IP-címtartományt a virtuális hálózathoz. A címtartomány mérete és a ténylegesen használandó IP-címtartomány a már üzembe helyezett egyéb hálózati erőforrásoktól függ. Az IP-címtartomány nem fedheti át az Azure-ban vagy a helyszíni környezetben meglévő címtartományokat. Győződjön meg arról, hogy az IP-címtartomány mérete elég nagy ahhoz, hogy az alhálózatba telepíteni kívánt virtuális gépek száma.

    A következő példában egy további *10.0.2.0/24* IP-címtartomány kerül hozzáadásra. Ha készen áll, válassza a **Mentés gombot.**

    ![További virtuális hálózati IP-címtartomány hozzáadása az Azure Portalon](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Ezután a virtuális hálózati ablak bal oldali menüjében válassza az **Alhálózatok**lehetőséget, majd válassza a **+ Alhálózat** lehetőséget az alhálózat hozzáadásához.

1. Válassza a **+ Alhálózat**lehetőséget, majd adja meg az alhálózat nevét, például a *felügyeletet.* Adjon meg **egy címtartományt (CIDR-blokk),** például *10.0.2.0/24.* Győződjön meg arról, hogy ez az IP-címtartomány nem fedi át más azure-vagy helyszíni címtartományokat. Hagyja a többi beállítást alapértelmezett értékként, majd kattintson **az OK gombra.**

    ![Alhálózati konfiguráció létrehozása az Azure Portalon](./media/join-windows-vm/create-subnet.png)

1. Az alhálózat létrehozása néhány másodpercet vesz igénybe. Létrehozása után válassza az *X* lehetőséget az alhálózati ablak bezárásához.
1. A **Virtuális gép** létrehozásához a Hálózat ablaktáblán válassza ki a legördülő menüből létrehozott alhálózatot, például a felügyelet *menüből.* Ismét győződjön meg arról, hogy válassza ki a megfelelő alhálózatot, és ne telepítse a virtuális gép ugyanabban az alhálózatban, mint az Azure AD DS felügyelt tartományban.
1. Hagyja a többi beállítást alapértelmezett értékként, majd válassza a **Kezelés**lehetőséget.
1. Állítsa **a rendszerindítási diagnosztikát** *Ki*beállításra. Hagyja a többi beállítást alapértelmezett értékként, majd válassza **a Véleményezés + létrehozás**lehetőséget.
1. Tekintse át a virtuális gép beállításait, majd válassza **a Létrehozás gombot.**

A virtuális gép létrehozása néhány percet vesz igénybe. Az Azure Portalon a központi telepítés állapotát mutatja. Miután a virtuális gép készen áll, válassza **az Ugrás az erőforrásra**lehetőséget.

![A virtuális gép erőforrásának megugrása az Azure Portalon sikeres létrehozása után](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Csatlakozás a Windows Server virtuális géphez

A virtuális gépek biztonságos csatlakozásához használjon egy Azure-bastion gazdagépet. Az Azure Bastion segítségével egy felügyelt állomás üzembe kerül a virtuális hálózatba, és webalapú RDP- vagy SSH-kapcsolatokat biztosít a virtuális gépekhez. A virtuális gépekhez nincs szükség nyilvános IP-címekre, és nem kell megnyitnia a hálózati biztonsági csoport szabályait a külső távoli forgalomhoz. Virtuális gépekhez csatlakozik az Azure Portalon keresztül a webböngészőből.

Ha egy megerősített gazdaton keresztül szeretne csatlakozni a virtuális géphez, hajtsa végre az alábbi lépéseket:

1. A virtuális **gép Áttekintő** ablaktábláján válassza a **Csatlakozás**lehetőséget, majd **a Bástya**lehetőséget.

    ![Csatlakozás Windows virtuális géphez az Azure Portalon található Bastion használatával](./media/join-windows-vm/connect-to-vm.png)

1. Adja meg az előző szakaszban megadott virtuális gép hitelesítő adatait, majd válassza a **Csatlakozás lehetőséget.**

   ![Csatlakozás a Bástyagazdagépen keresztül az Azure Portalon](./media/join-windows-vm/connect-to-bastion.png)

Szükség esetén engedélyezze a webböngésző számára, hogy előugró ablakokat nyisson meg a Bástya-kapcsolat megjelenítéséhez. Néhány másodpercet vesz igénybe, hogy a kapcsolatot a virtuális gép.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományához

A virtuális gép létrehozása és az Azure Bastion használatával létrehozott webalapú RDP-kapcsolat most csatlakozzon a Windows Server virtuális géphez az Azure AD DS felügyelt tartományához. Ez a folyamat megegyezik egy hagyományos helyszíni Active Directory tartományi szolgáltatások tartományhoz csatlakozó számítógépszámítógéppel.

1. Ha **a Kiszolgálókezelő** alapértelmezés szerint nem nyílik meg, amikor bejelentkezik a virtuális gépre, válassza a **Start** menüt, majd a **Kiszolgálókezelő parancsot.**
1. A **Kiszolgálókezelő** ablak bal oldali ablaktáblájában válassza a **Helyi kiszolgáló**lehetőséget. A jobb oldali ablaktábla **Tulajdonságok csoportjában** válassza a **Munkacsoport lehetőséget.**

    ![Nyissa meg a Kiszolgálókezelőt a virtuális gépen, és szerkessze a munkacsoport-tulajdonságot](./media/join-windows-vm/server-manager.png)

1. A **Rendszer tulajdonságai** ablakban válassza a **Módosítás** lehetőséget az Azure AD DS felügyelt tartományhoz való csatlakozáshoz.

    ![A munkacsoport vagy a tartomány tulajdonságainak módosítása](./media/join-windows-vm/change-domain.png)

1. A **Tartomány** mezőben adja meg az Azure AD DS felügyelt tartományának nevét, például *aaddscontoso.com,* majd kattintson az **OK gombra.**

    ![Adja meg a csatlakozni kívánt Azure AD DS felügyelt tartományt](./media/join-windows-vm/join-domain.png)

1. Adja meg a tartományhoz való csatlakozáshoz szükséges tartományhitelesítő adatokat. Használja a hitelesítő adatokat egy felhasználó, amely része az Azure AD DS felügyelt tartományban. A fióknak az Azure AD DS felügyelt tartományának vagy az Azure AD-bérlőnek kell lennie – az Azure AD-bérlőhöz társított külső könyvtárakfiókjai nem hitelesíthetők megfelelően a tartományhoz való csatlakoztatási folyamat során. A fiók hitelesítő adatai az alábbi módokon adhatók meg:

    * **UPN formátum** (ajánlott) – Adja meg a felhasználói fiók egyszerű felhasználónév (UPN) utónevét az Azure AD-ben konfigurálva. Például a felhasználó *contosoadmin* upn-utótagja `contosoadmin@aaddscontoso.onmicrosoft.com`a . Van néhány gyakori használati eset, amikor az egyszerű stafétaformátum megbízhatóan használható a tartományba való bejelentkezéshez a *SAMAccountName* formátum helyett:
        * Ha a felhasználó UPN előtagja hosszú, például *deehasareallylongname,* akkor a *SAMAccountName* automatikusan létrehozható lehet.
        * Ha több felhasználó rendelkezik ugyanazt az UPN-előtagot az Azure AD-bérlőben, például *a dee,* előfordulhat, hogy a *SAMAccountName* formátum automatikusan létrejön.
    * **SAMAccountName formátum** - Írja be a fiók nevét a *SAMAccountName* formátumban. A user *contosoadmin* *SAMAccountName neve* `AADDSCONTOSO\contosoadmin`például a .

1. Az Azure AD DS felügyelt tartományához való csatlakozás néhány másodpercet vesz igénybe. Ha elkészült, a következő üzenet üdvözli Önt a tartományban:

    ![Üdvözli a tartomány](./media/join-windows-vm/join-domain-successful.png)

    A folytatáshoz kattintson az **OK** gombra.

1. Az Azure AD DS felügyelt tartományhoz való csatlakozás folyamatának befejezéséhez indítsa újra a virtuális gép.

> [!TIP]
> A PowerShell használatával tartományhoz csatlakozhat egy virtuális géphez az [Add-Computer][add-computer] parancsmaggal. A következő példa csatlakozik az *AADDSCONTOSO tartományhoz,* majd újraindítja a virtuális gép. Amikor a rendszer kéri, adja meg az Azure AD DS felügyelt tartományának részét vevő felhasználó hitelesítő adatait:
>
> `Add-Computer -DomainName AADDSCONTOSO -Restart`
>
> A tartományhoz való csatlakozáshoz anélkül, hogy csatlakozna hozzá, és manuálisan konfigurálja a kapcsolatot, használhatja a [Set-AzVmAdDomainExtension][set-azvmaddomainextension] Azure PowerShell-parancsmag.

Miután a Windows Server virtuális gép újraindult, az Azure AD DS felügyelt tartományban alkalmazott szabályzatok a virtuális gépre kerül. Most már a megfelelő tartományi hitelesítő adatokkal is bejelentkezhet a Windows Server virtuális gépbe.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő oktatóanyagban ezzel a Windows Server virtuális géptel telepíti az Okat, amelyek lehetővé teszik az Azure AD DS felügyelt tartományfelügyeletét. Ha nem szeretné folytatni ezt az oktatóanyag-sorozatot, tekintse át az alábbi törlési lépéseket [a virtuális gép törléséhez.](#delete-the-vm) Ellenkező esetben [folytassa a következő oktatóanyaggal.](#next-steps)

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>A virtuális géphez való csatlakozás kioldása az Azure AD DS felügyelt tartományából

Ha el szeretné távolítani a virtuális gép az Azure AD DS felügyelt tartományból, kövesse a lépéseket újra [csatlakozni a virtuális gép egy tartományhoz.](#join-the-vm-to-the-azure-ad-ds-managed-domain) Ahelyett, hogy csatlakozna az Azure AD DS felügyelt tartományához, válassza a munkacsoporthoz való csatlakozást, például az alapértelmezett *MUNKACSOPORTot.* A virtuális gép újraindítása után a számítógép-objektum törlődik az Azure AD DS felügyelt tartományból.

Ha törli a virtuális gép nem csatlakozik a tartományból, egy árva számítógép-objektum marad az Azure AD DS.If [you delete the VM](#delete-the-vm) without unjoining from the domain, a orphaned computer object is left in Azure AD DS.

### <a name="delete-the-vm"></a>A virtuális gép törlése

Ha nem fogja használni ezt a Windows Server virtuális gép, törölje a virtuális gép az alábbi lépésekkel:

1. A bal oldali menüben válassza **az Erőforráscsoportok** lehetőséget
1. Válassza ki az erőforráscsoportot, például *a myResourceGroup*.
1. Válassza ki a virtuális gép, például *a myVM*, majd válassza **a Törlés**lehetőséget. Az erőforrás törlésének megerősítéséhez válassza az **Igen** lehetőséget. A virtuális gép törlése néhány percet vesz igénybe.
1. A virtuális gép törlésekor jelölje ki az operációs rendszer lemezét, a hálózati csatoló kártyát és a *myVM-előtaggal* rendelkező egyéb erőforrásokat, és törölje őket.

## <a name="troubleshoot-domain-join-issues"></a>Tartománycsatlakozási problémák elhárítása

A Windows Server virtuális gép sikeresen csatlakozik az Azure AD DS felügyelt tartományhoz, ugyanúgy, mint egy hagyományos helyszíni számítógép csatlakozna egy Active Directory tartományi szolgáltatások tartományhoz. Ha a Windows Server virtuális gép nem tud csatlakozni az Azure AD DS felügyelt tartományhoz, az azt jelzi, hogy kapcsolati vagy hitelesítő adatokkal kapcsolatos probléma van. Tekintse át az alábbi hibaelhárítási szakaszokat, hogy sikeresen csatlakozzon a felügyelt tartományhoz.

### <a name="connectivity-issues"></a>Csatlakozási problémák

Ha nem jelenik meg olyan üzenet, amely hitelesítő adatokat kér a tartományhoz való csatlakozáshoz, kapcsolódási probléma lép fel. A virtuális gép nem tudja elérni az Azure AD DS felügyelt tartománya a virtuális hálózaton.

A hibaelhárítási lépések mindegyikének megkísérlése után próbáljon meg újra csatlakozni a Windows Server virtuális géphez a felügyelt tartományhoz.

* Ellenőrizze, hogy a virtuális gép ugyanahhoz a virtuális hálózathoz csatlakozik-e, amelyben az Azure AD DS engedélyezve van, vagy társviszony-létesített hálózati kapcsolattal rendelkezik.Verify the VM is connected to the same virtual network that Azure AD DS is enabled in, or has a peered network connection.
* Próbálja meg pingelni a felügyelt tartomány `ping aaddscontoso.com`DNS-tartománynevét, például a .
    * Ha a pingelési kérelem sikertelen, próbálja meg pingelni a felügyelt tartomány IP-címeit, például `ping 10.0.0.4`a . A környezet IP-címe jelenik meg a *Tulajdonságok* lapon, amikor kiválasztja az Azure AD DS felügyelt tartományt az Azure-erőforrások listájából.
    * Ha pingeli az IP-címet, de a tartományt nem, akkor a DNS helytelenül van konfigurálva. Ellenőrizze, hogy a felügyelt tartomány IP-címei a virtuális hálózat DNS-kiszolgálóiként vannak-e konfigurálva.
* Próbálja meg kiüríteni a DNS-feloldó gyorsítótárát a virtuális gépen a `ipconfig /flushdns` paranccsal.

### <a name="credentials-related-issues"></a>Hitelesítő adatokkal kapcsolatos problémák

Ha egy kérdés jelenik meg, amely hitelesítő adatokat kér a tartományhoz való csatlakozáshoz, de a hitelesítő adatok megadása után egy hiba, a virtuális gép képes csatlakozni az Azure AD DS felügyelt tartományhoz. A megadott hitelesítő adatok nem teszik lehetővé a virtuális gép számára, hogy csatlakozzanak az Azure AD DS felügyelt tartományához.

A hibaelhárítási lépések mindegyikének megkísérlése után próbáljon meg újra csatlakozni a Windows Server virtuális géphez a felügyelt tartományhoz.

* Győződjön meg arról, hogy a megadott felhasználói fiók az Azure AD DS felügyelt tartományhoz tartozik.
* Győződjön meg arról, hogy a fiók az Azure AD DS felügyelt tartomány vagy az Azure AD-bérlő része. Az Azure AD-bérlőhöz társított külső könyvtárakból származó fiókok nem hitelesíthetők megfelelően a tartományhoz való csatlakozás folyamata során.
* Próbálja meg az upn formátumot használni `contosoadmin@aaddscontoso.onmicrosoft.com`a hitelesítő adatok megadásához, például . Ha sok felhasználó ugyanazt az UPN-előtagot a bérlőben, vagy ha az UPN-előtag túl hosszú, a *SAMAccountName* a fiókjához lehet automatikusan generált. Ezekben az esetekben a fiók *SAMAccountName* formátuma eltérhet attól, amit a helyszíni tartományban elvár vagy használ.
* Ellenőrizze, hogy engedélyezte-e a [jelszó-szinkronizálást][password-sync] a felügyelt tartománnyal. E konfigurációs lépés nélkül a szükséges jelszókiterületek nem lesznek jelen az Azure AD DS felügyelt tartományban a bejelentkezési kísérlet megfelelő hitelesítéséhez.
* Várja meg, amíg befejeződik a jelszó-szinkronizálás. Ha egy felhasználói fiók jelszavát módosítja, az Azure AD automatikus háttérszinkronizálása frissíti a jelszót az Azure AD DS-ben. Némi időt vesz igénybe, amíg a jelszó elérhető vé válik a tartományhoz való csatlakozáshoz.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Windows Server virtuális gép létrehozása
> * Csatlakozás a Windows Server virtuális géphez egy Azure virtuális hálózathoz
> * Csatlakozzon a virtuális géphez az Azure AD DS felügyelt tartományához

Az Azure AD DS felügyelt tartomány felügyeletéhez konfiguráljon egy felügyeleti virtuális gép az Active Directory felügyeleti központ (ADAC) használatával.

> [!div class="nextstepaction"]
> [Felügyeleti eszközök telepítése felügyeleti virtuális gépre](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
