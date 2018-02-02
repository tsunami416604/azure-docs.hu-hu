---
title: "Csatlakozás Azure verem Azure VPN-nel"
description: "Hogyan Azure verem virtuális hálózatok csatlakoztatása VPN-kapcsolattal Azure virtuális hálózatairól."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: 5d963fe8b1b576768156500af39254f45939f90d
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>Csatlakozás Azure verem Azure VPN-nel

*A következőkre vonatkozik: Azure verem integrált rendszerek*

Ez a cikk bemutatja, hogyan hozzon létre egy--webhelyek közötti VPN egy virtuális hálózatot az Azure-ban Azure verem egy virtuális hálózathoz csatlakozni.

### <a name="connection-diagram"></a>Kapcsolati diagram
Az alábbi ábra bemutatja, mi a kapcsolat konfigurációs kell hasonlítania ezután:

![Pont-pont VPN-kapcsolat konfigurációja](media/azure-stack-connect-vpn/image2.png)

### <a name="before-you-begin"></a>Előkészületek
Fejezze be a kapcsolat konfigurációját, győződjön meg arról, hogy a következő elemek megkezdése előtt:

* Egy Azure verem integrált rendszerek (több csomópontos) központi telepítés, amely közvetlenül kapcsolódik az internethez. Ez azt jelenti, hogy a külső nyilvános IP-címtartományt a nyilvános interneten keresztül közvetlenül elérhető kell lennie.
* Egy érvényes Azure-előfizetéshez.  Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egy [ingyenes Azure-fiókot Itt](https://azure.microsoft.com/free/?b=17.06).

## <a name="network-example-values-table"></a>Hálózati példa értékek tábla
A hálózati példa értékek táblázat használt minta értékeket ebben a cikkben. Ezeket az értékeket is használhat, vagy olvassa el a jobb megértése érdekében ebben a cikkben szereplő példák őket.

**Hálózati példa értékek tábla**
|   |Azure Stack|Azure|
|---------|---------|---------|
|Virtuális hálózat neve     |Azs-VNet|AzureVNet |
|Virtuális hálózat címtere |10.1.0.0/16|10.100.0.0/16|
|Alhálózat neve     |Előtér|Előtér|
|Alhálózati címtartomány|10.1.0.0/24 |10.100.0.0/24 |
|Átjáró alhálózata     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>A hálózati erőforrások létrehozása az Azure-ban

Először hoz létre a hálózati erőforrások az Azure-bA. Az alábbi utasítások alapján bemutatják, hogyan hozhat létre az erőforrások használatával a [Azure-portálon](http://portal.azure.com/).

### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása

1. Jelentkezzen be a [Azure-portálon](http://portal.azure.com/) Azure-fiókjával.
2. Válassza ki a felhasználói portál **új**.
3. Ugrás a **piactér**, majd válassza ki **hálózati**.
4. Válassza ki **virtuális hálózati**.
5. Olvassa el a hálózati konfiguráció táblából azonosítsa azokat az értékeket az Azure- **neve**, **Címtéren**, **alhálózati név**, és **alhálózati cím tartomány**.
6. A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy ha már rendelkezik egy, **meglévő**.
7. Válassza ki a **hely** a vnet.  A példában szereplő értékeket használata, válassza ki a **USA keleti régiója** , vagy használjon egy másik helyre, ha szeretné.
8. Válassza a **Rögzítés az irányítópulton** lehetőséget.
9. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Hozza létre az átjáró-alhálózatot.
1. Nyissa meg a létrehozott virtuális hálózati erőforráshoz (**AzureVNet**) az irányítópulton.
2. Az a **beállítások** szakaszban jelölje be **alhálózatok**.
3. Válassza ki **átjáróalhálózatot** hozzáadása egy átjáró-alhálózatot a virtuális hálózathoz.
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**.
   Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.
5. Az a **-címtartományt** mezőbe a címe **10.100.1.0/24**.
6. Válassza ki **OK** az átjáró alhálózatának létrehozásához.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
1. Válassza ki az Azure-portálon **új**.  
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról a hálózati erőforrások **virtuális hálózati átjáró**.
4. A **neve**, típus **Azure-GW**.
5. Virtuális hálózat kiválasztásához jelölje ki a **virtuális hálózati**. Válassza ki **AzureVnet** a listából.
6. Válassza ki **nyilvános IP-cím**. Ha a **nyilvános IP-cím kiválasztása** szakasz megnyílik, jelölje be **hozzon létre új**.
7. A **neve**, típus **Azure-GW-PiP**, majd válassza ki **OK**.
8. Alapértelmezés szerint a **VPN-típus**, **útválasztó-alapú** van kiválasztva.
    Tartsa a **útválasztó-alapú** VPN-típus.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Rögzítheti az irányítópulton az erőforrást. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró létrehozása

1. Válassza ki az Azure-portálon **új**. 
4. Ugrás a **piactér**, majd válassza ki **hálózati**.
5. Válassza ki a listáról az erőforrások **helyi hálózati átjáró**.
6. A **neve**, típus **Azs-GW**.
7. A **IP-cím**, írja be a nyilvános IP-cím a Azure verem virtuális hálózati átjáró, amely korábban a hálózati konfiguráció táblázatban szerepel.
8. A **Címterület**, az Azure-vermet, írja be a **10.1.0.0/24** és **10.1.1.0/24** címterének a **AzureVNet**.
9. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyes-e, majd válassza ki **létrehozása**.

## <a name="create-the-connection"></a>A kapcsolat létrehozása
1. Válassza ki a felhasználói portál **új**. 
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról az erőforrások **kapcsolat**.
4. Az a **alapvető** beállítások szakaszban, a a **kapcsolattípus**, válassza a **pont-pont (IPSec)**.
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** szakaszban jelölje be **virtuális hálózati átjáró**, majd válassza ki **Azure-GW**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **Azs-GW**.
8. A **kapcsolatnév**, típus **Azure-Azs**.
9. A **megosztott kulcsot (PSK)**, típus **12345**. Ha egy másik értéket választja, ne feledje, hogy az informatikai *kell* felel meg a megosztott kulcs, amely hoz létre a kapcsolat másik végén. Kattintson az **OK** gombra.
10. Tekintse át a **összegzés** szakaszt, és válassza ki **OK**.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Létrehoz egy virtuális gépet az Azure-ban, és a Virtuálisgép-alhálózatot a virtuális hálózat helyezése.

1. Válassza ki az Azure-portálon **új**.
2. Ugrás a **piactér**, majd válassza ki **számítási**.
3. A virtuálisgép-lemezképeket, jelölje ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** szakaszban, a **neve**, típus **AzureVM**.
5. Írjon be egy érvényes felhasználónevet és jelszót. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** szakaszban, a virtuális gép méretét, az adott példány, majd válassza ki és **válasszon**.
8. Az a **beállítások** szakaszban elfogadhatja az alapértelmezett beállításokat. Győződjön meg arról, hogy a **AzureVnet** virtuális hálózat van kiválasztva, és ellenőrizze, hogy az alhálózat **10.100.0.0/24**. Kattintson az **OK** gombra.
9. Tekintse át a beállításokat a a **összegzés** szakaszt, és válassza ki **OK**.

## <a name="create-the-network-resources-in-azure-stack"></a>A hálózati erőforrások létrehozása az Azure-verem
A hálózati erőforrások következő Azure verem létrehozása.

### <a name="sign-in-as-a-user"></a>Jelentkezzen be egy olyan felhasználó nevében
A szolgáltatás-rendszergazdáknak is jelentkezzen be egy olyan felhasználó nevében a tervek, ajánlatok és-előfizetések, amelyek a felhasználók használhatja. Ha még nem rendelkezik egy, [felhasználói fiók létrehozása](azure-stack-add-new-user-aad.md) regisztráció előtt.

### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása
1. Felhasználói fiók használata a felhasználói portálra történő bejelentkezéshez.
2. Válassza ki a felhasználói portál **új**.

    ![Új virtuális hálózat létrehozása](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Ugrás a **piactér**, majd válassza ki **hálózati**.
4. Válassza ki **virtuális hálózati**.
5. A **neve**, **Címtéren**, **alhálózati név**, és **alhálózati címtartományt**, használja a hálózati konfiguráció táblából értékeket.
6. A **előfizetés**, az előfizetés korábban létrehozott jelenik meg.
7. A **erőforráscsoport**, hozzon létre egy erőforráscsoportot, vagy ha már rendelkezik egy, válassza ki a **meglévő**.
8. Ellenőrizze az alapértelmezett helyet.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása
1. Az irányítópulton nyissa meg a létrehozott Azs-virtuális hálózat virtuális hálózati erőforrás.
2. Az a **beállítások** szakaszban jelölje be **alhálózatok**.
3. A virtuális hálózati átjáró-alhálózatot hozzáadásához válassza **Átjáróalhálózatot**.
   
    ![Átjáró alhálózatának hozzáadása](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Alapértelmezés szerint az alhálózat neve legyen **GatewaySubnet**.
   Átjáró alhálózatok olyan speciális. A megfelelő működéshez kell használniuk a *GatewaySubnet* nevét.
5. A **-címtartományt**, győződjön meg arról, hogy a cím **10.1.1.0/24**.
6. Válassza ki **OK** az átjáró alhálózatának létrehozásához.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
1. Válassza a verem Azure portál **új**. 
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról a hálózati erőforrások **virtuális hálózati átjáró**.
4. A **neve**, típus **Azs-GW**.
5. Válassza ki a **virtuális hálózati** elem egy virtuális hálózatot választ.
   Válassza ki **Azs-VNet** a listából.
6. Válassza ki a **nyilvános IP-cím** menüpont. Ha a **nyilvános IP-cím kiválasztása** szakasz megnyílik, jelölje be **hozzon létre új**.
7. A **neve**, típus **a PiP GW Azs**, majd válassza ki **OK**.
8.  Alapértelmezés szerint a **VPN-típus**, **útválasztó-alapú** van kiválasztva.
    Tartsa a **útválasztó-alapú** VPN-típus.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Rögzítheti az irányítópulton az erőforrást. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása
A fogalmát egy *helyi hálózati átjáró* Azure verem kissé eltérő, mint az Azure-telepítés.

Az Azure-telepítés a helyi hálózati átjáró egy (az adott helyen felhasználói) helyszíni fizikai eszköz, a virtuális hálózati átjáró az Azure-ban való kapcsolódáshoz használt jelöli. Azure-készletben a kapcsolat mindkét végén a virtuális hálózati átjárók!

Egy gondolja, hogy a több általános módja, hogy a helyi hálózati átjáró erőforrás mindig jelzi a távoli átjáró, a kapcsolat másik végén. 

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró létrehozása
1. Jelentkezzen be a verem Azure portálra.
2. Válassza ki a felhasználói portál **új**.
3. Ugrás a **piactér**, majd válassza ki **hálózati**.
4. Válassza ki a listáról az erőforrások **helyi hálózati átjáró**.
5. A **neve**, típus **Azure-GW**.
6. A **IP-cím**, írja be a virtuális hálózati átjáró nyilvános IP-címét az Azure-ban **Azure-GW-PiP**. Ez a cím jelenik meg a korábban a hálózati konfiguráció táblázatban.
7. A **Címterület**, a létrehozott Azure virtuális hálózat a címtér, írja be a következőt **10.100.0.0/24** és **10.100.1.0/24**.
8. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyes-e, majd válassza ki **létrehozása**.

### <a name="create-the-connection"></a>A kapcsolat létrehozása
1. Válassza ki a felhasználói portál **új**.
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról az erőforrások **kapcsolat**.
4. Az a **alapjai** beállítások szakaszban, a a **kapcsolattípus**, jelölje be **pont-pont (IPSec)**.
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** szakaszban jelölje be **virtuális hálózati átjáró**, majd válassza ki **Azs-GW**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **Azure-GW**.
8. A **kapcsolatnév**, típus **Azs-Azure**.
9. A **megosztott kulcsot (PSK)**, típus **12345**, majd válassza ki **OK**.
10. Az a **összegzés** szakaszban jelölje be **OK**.

### <a name="create-a-vm"></a>Virtuális gép létrehozása
A VPN-kapcsolaton keresztül átvitt adatok ellenőrzéséhez szüksége küldeni és fogadni az adatokat a VPN-alagúton keresztül minden futó virtuális gépek létrehozásához. 

1. Válassza ki az Azure-portálon **új**.
2. Ugrás a **piactér**, majd válassza ki **számítási**.
3. A virtuálisgép-lemezképeket, jelölje ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** részben, a **neve**, típus **Azs-VM**.
5. Írjon be egy érvényes felhasználónevet és jelszót. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** részben, a példány, jelölje be a virtuális gép méretét, és válassza ki **válasszon**.
8. Az a **beállítások** területen fogadja el az alapértelmezett beállításokat. Győződjön meg arról, hogy a **Azs-VNet** virtuális hálózat van kiválasztva. Ellenőrizze, hogy az alhálózat **10.1.0.0/24**. Válassza ki **OK**.
9. Az a **összegzés** szakaszt, tekintse át a beállításokat, és válassza ki **OK**.


## <a name="test-the-connection"></a>A kapcsolat tesztelése
Most, hogy a helyek kapcsolat létrejött, ellenőriznie kell, hogy az átfutó forgalom kaphat. Ellenőrizze, hogy jelentkezzen be Azure verem létrehozott virtuális gépek közül. Ezt követően Pingelje meg a virtuális gép, amelyet az Azure-ban hozott létre. 

Győződjön meg arról, hogy a helyek kapcsolaton keresztül a forgalom küldése, Pingelje meg a virtuális gép távoli alhálózaton, nem a VIP közvetlen IP-címet (DIP) címét. Ehhez az szükséges, a DIP cím, a kapcsolat másik végén található. A későbbi használat céljából menteni.

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>Jelentkezzen be a felhasználó Azure verem VM
1. Jelentkezzen be a verem Azure portálra.
2. Válassza ki a bal oldali navigációs sáv **virtuális gépek**.
3. A virtuális gépek listájának megtekintéséhez keresse **Azs-VM** korábban létrehozott, és állítsa be azt.
4. A szakasz a virtuális gépet, kattintson a **Connect**, majd nyissa meg a Azs-VM.rdp fájlt.
   
     ![A Csatlakozás gombra](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy emelt szintű **Windows PowerShell** ablak.
7. Írja be az **ipconfig /all** parancsot.
8. A kimenetben keresse meg a **IPv4-cím**, majd mentse a cím későbbi használatra. Ez az a cím, amely pingelést az Azure-ból. Példa környezetben, a cím az **10.1.0.4**, de a környezetében előfordulhat, hogy különböző. Alá kell tartozniuk a **10.1.0.0/24** korábban létrehozott alhálózati.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>Jelentkezzen be a bérlő Azure-ban
1. Jelentkezzen be az Azure portálra.
2. A bal oldali navigációs sávon kattintson **virtuális gépek**.
3. A virtuális gépek listájának található **Azure-VM** korábban létrehozott, és állítsa be azt.
4. A szakasz a virtuális gépet, kattintson a **Connect**.
5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy emelt szintű **Windows PowerShell** ablak.
7. Írja be az **ipconfig /all** parancsot.
8. Egy IPv4-címet, amely kell megjelennie **10.100.0.0/24**. Példa környezetben, a cím az **10.100.0.4**, de lehet, hogy a cím különböző.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. A virtuális gépről az Azure-ban Pingelje meg a virtuális gép Azure-készletben, az alagúton keresztül. Ehhez az szükséges, a DIP Azs-virtuális gépről rögzített pingelést.
   Példa környezetben, ami **10.1.0.4**, de ügyeljen arra, hogy pingelni a címre, amelyet a tesztkörnyezetben. Az alábbi képernyőfelvételen hasonló egy eredmény kell megjelennie:
   
    ![Sikeres ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. A távoli virtuális gépről a válasz azt jelzi, hogy a sikeres vizsgálat! A virtuális gép ablak bezárása A kapcsolat tesztelésére, próbálja meg más típusú adatátvitelek például egy fájl másolása.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Adatátviteli statisztika megtekintése az átjárókapcsolaton keresztül
Ha szeretné tudni, hogy mennyi adatot továbbítja a helyek kapcsolaton keresztül, ezek az információk érhető el a **kapcsolat** szakasz. Ez a vizsgálat módja is egy másik győződjön meg arról, hogy az imént a telefonjára küldött ping ténylegesen merült fel a VPN-kapcsolaton keresztül.

1. Amíg a felhasználó Azure verem virtuális gépre van bejelentkezve, a felhasználói fiók használata a felhasználói portálra történő bejelentkezéshez.
2. Nyissa meg a **összes erőforrás**, majd válassza ki a **Azs-Azure** kapcsolat. **Kapcsolatok** jelenik meg.
4. Az a **kapcsolat** részben, a statisztikája **adatok** és **kimenő adatforgalmat** jelennek meg. Az alábbi képernyőképen a nagy mennyiségű rendelnek a további fájlok átvitele. Néhány nullától eltérő értéket kell megjelennie.
   
    ![Bejövő és kimenő adatforgalom](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>További lépések

[Alkalmazások telepítése Azure és az Azure verem](azure-stack-solution-pipeline.md)
