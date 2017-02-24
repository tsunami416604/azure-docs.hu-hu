---
title: "Helyek közötti VPN-kapcsolat létrehozása két virtuális hálózat között eltérő Azure Stack PoC-környezetekben | Microsoft Docs"
description: "Lépésről lépésre haladó eljárás, amelynek segítségével a felhőrendszergazdák helyek közötti VPN-kapcsolatot hozhatnak létre két egycsomópontos POC-környezet között a TP2-ben."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/08/2017
ms.author: scottnap
translationtype: Human Translation
ms.sourcegitcommit: 5104c7996de9dc0597e65be31c28a9aaa1243a90
ms.openlocfilehash: d324290caf5b5a085a2daf67e541c295dffda732


---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-poc-environments"></a>Helyek közötti VPN-kapcsolat létrehozása két virtuális hálózat között, eltérő Azure Stack PoC-környezetekben
## <a name="overview"></a>Áttekintés
Ez a cikk helyek közötti VPN-kapcsolat létrehozásának módját mutatja be két eltérő Azure Stack Proof-of-Concept- (POC-) környezetben található két virtuális hálózat között. A kapcsolatok konfigurálása közben megismeri a VPN-átjárók működését is az Azure Stackben.

> [!NOTE]
> Ez a dokumentum kifejezetten az Azure Stack TP2 POC-re vonatkozik.
> 
> 

### <a name="connection-diagram"></a>Kapcsolati diagram
Az alábbi diagram a várt konfigurációt mutatja be a konfigurálás befejezését követően:

![](media/azure-stack-create-vpn-connection-one-node-tp2/image1.png)

### <a name="before-you-begin"></a>Előkészületek
A sikeres végrehajtás érdekében, a konfigurálás megkezdése előtt győződjön meg arról, hogy rendelkezik az alábbi elemekkel:

* Két olyan kiszolgáló, amely megfelel az [Azure Stack üzembehelyezési előfeltételek](azure-stack-deploy.md) című dokumentumban meghatározott Azure Stack POC-hardverkövetelményeknek és a további előfeltételeknek.
* Azure Stack Technical Preview 2 üzembehelyezési csomag.

## <a name="deploy-the-poc-environments"></a>A POC-környezetek üzembe helyezése
A konfigurálás végrehajtásához két Azure Stack POC-környezet üzembe helyezését fogja elvégezni.

* Minden egyes üzembe helyezett POC esetében követheti [Az Azure Stack POC üzembe helyezése](azure-stack-run-powershell-script.md) című cikkben megadott utasításokat.
  A jelen dokumentumban a POC-környezetekre általánosan *POC1* és *POC2* néven utalunk.

## <a name="configure-quotas-for-compute-network-and-storage"></a>Számítási, hálózati és tárolási kvóták konfigurálása
Mindenekelőtt a számítási, hálózati és tárolási *kvóták* konfigurálását kell elvégezni. Ezen szolgáltatásokhoz egy *csomag*, majd egy *ajánlat* társítható, amelyre a bérlők előfizethetnek.

> [!NOTE]
> A lépéseket minden egyes Azure Stack POC-környezet esetében végre kell hajtani.
> 
> 

A szolgáltatáskvóták létrehozása eltér a TP1-ben megismertektől. A kvótáknak a TP2-ben való létrehozásának lépései a <http://aka.ms/mas-create-quotas> címen találhatók. Ebben a gyakorlatban elfogadhatja az összes alapértelmezett kvótabeállítást.

## <a name="create-a-plan-and-offer"></a>Csomag és Ajánlat létrehozása
A [Csomagok](azure-stack-key-features.md) egy vagy több szolgáltatás csoportjai. Szolgáltatóként csomagokat hozhat létre, amelyeket kiajánlhat a bérlőinek. A bérlői előfizethetnek az ajánlataira, hogy használhassák a bennük foglalt csomagokat és szolgáltatásokat.

> [!NOTE]
> A lépéseket minden egyes Azure Stack POC-környezet esetében végre kell hajtani.
> 
> 

1. Először is hozzon létre egy Csomagot. Ehhez kövesse a [Csomag létrehozása](azure-stack-create-plan.md) című online cikkben ismertetett lépéseket.
2. Hozzon létre egy Ajánlatot az [Ajánlat létrehozása az Azure Stackben](azure-stack-create-offer.md) cikkben ismertetett lépéseket követve.
3. Jelentkezzen be a portálra bérlői rendszergazdaként, és [fizessen elő az Ön által létrehozott Ajánlatra](azure-stack-subscribe-plan-provision-vm.md).

## <a name="create-the-network-resources-in-poc-1"></a>A hálózati erőforrások létrehozása a POC 1-ben
Most ténylegesen is létre fogja hozni a konfiguráció beállításához szükséges erőforrásokat. A teendőket az alábbi lépések foglalják össze. Az utasítások az erőforrásoknak a portál használatával történő létrehozását mutatják be, de az a PowerShell használatával is megvalósítható.

![](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="log-in-as-a-tenant"></a>Bejelentkezés bérlőként
A szolgáltatás-rendszergazda bejelentkezhet bérlőként azon csomagok, ajánlatok és előfizetések teszteléséhez, amelyeket a bérlői használhatnak. Ha még nem rendelkezik ilyennel, [hozzon létre egy bérlői fiókot](azure-stack-add-new-user-aad.md) a bejelentkezés előtt.

### <a name="create-the-virtual-network--vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása
1. Jelentkezzen be a bérlői fiókkal.
2. Az Azure Portalon kattintson az **Új** elemre.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)
3. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
4. A menüben kattintson a **Virtuális hálózat** elemre.
5. Kattintson az erőforrást ismertető panel aljának közelében található **Létrehozás** gombra. Írja be az alábbi értékeket a megfelelő mezőkbe a táblázatnak megfelelően.
   
   | **Mező** | **Érték** |
   | --- | --- |
   | Név |vnet-01 |
   | Címtér |10.0.10.0/23 |
   | Alhálózat neve |subnet-01 |
   | Alhálózati címtartomány |10.0.10.0/24 |
6. Most a korábban létrehozott Előfizetést kell látnia az **Előfizetés** mezőben.
7. Az Erőforráscsoport területen létrehozhat egy új erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza a **Meglévő használata** elemet.
8. Ellenőrizze az alapértelmezett helyet.
9. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Hozza létre az átjáró-alhálózatot.
1. Nyissa meg az imént létrehozott virtuálishálózat-erőforrást (Vnet-01) az irányítópultról.
2. A Beállítások panelen válassza az **Alhálózatok** elemet.
3. Kattintson az **Átjáró alhálózata** lehetőségre egy átjáró-alhálózatnak a virtuális hálózathoz történő hozzáadásához.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**.
   Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.11.0/24**.
6. Az átjáró-alhálózat létrehozásához kattintson a **Létrehozás** gombra.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
1. Az Azure Portalon kattintson az **Új** elemre.
   
2. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
3. A hálózati erőforrások listájában válassza a **Virtuális hálózati átjáró** elemet.
4. Tekintse át a leírást, majd kattintson a **Létrehozás** gombra.
5. A **Név** mezőbe írja be a következőt: **GW1**.
6. Virtuális hálózat kiválasztásához kattintson a **Virtuális hálózat** elemre.
   A listán válassza a **Vnet-01** elemet.
7. Kattintson a **Nyilvános IP-cím** menüpontra. A megnyíló **Nyilvános IP-cím választása** panelen kattintson az **Új létrehozása** parancsra.
8. A **Név** mezőbe írja be a **GW1-PiP** nevet, majd kattintson az **OK** gombra.
9. Az **Átjáró típusa** mezőben alapértelmezés szerint a **VPN** lehetőség van kiválasztva. Ne módosítsa ezt a beállítást.
10. Az **VPN típusa** mezőben alapértelmezés szerint a **Útvonalalapú** lehetőség van kiválasztva.
    Ne módosítsa ezt a beállítást.
11. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Ha kívánja, az erőforrást rögzítheti az Irányítópulton. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása
A *helyi hálózati átjárók* megvalósítása ebben az Azure Stack értékelési telepítésben kissé eltér egy tényleges Azure-telepítéstől.

Az Azure-hoz hasonlóan itt is létezik a helyi hálózati átjáró fogalma. Az Azure-telepítés esetén azonban a helyi hálózati átjárók helyi (bérlői) fizikai eszközöket képviselnek, amelyeket a virtuális hálózati átjárókhoz történő kapcsolódásra használhat az Azure-ban. A jelen Azure Stack értékelési telepítés esetében viszont a kapcsolat mindkét végén virtuális hálózati átjárók találhatók.

Általánosabb értelemben úgy gondoljon erre, hogy a helyi hálózati átjáró erőforrás minden esetben a kapcsolat másik végén lévő távoli átjárót jelzi. A POC tervezésének módja miatt a helyi hálózati átjáró nyilvános IP-címeként kell megadni a külső hálózati adapter IP-címét a másik POC NAT virtuális gépén. Ezt követően NAT-leképezéseket fog létrehozni a NAT virtuális gépén annak ellenőrzésére, hogy mindkét végpont megfelelően csatlakozik-e.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Az IP-cím beszerzése a NAT virtuális gépének külső adapteréről
1. Jelentkezzen be a POC2 Azure Stack fizikai gépére.
2. Nyomja le a [Windows billentyű] + R billentyűkombinációt a **Futtatás** menü megnyitásához, írja be az **mstsc** kifejezést, majd nyomja le az Enter billentyűt.
3. A **Számítógép** mezőbe írja be a **MAS-BGPNAT01** nevet, majd kattintson a  **Csatlakozás** gombra.
4. Kattintson a Start menüre, kattintson a jobb gombbal a **Windows PowerShell** elemre, majd válassza a **Futtatás rendszergazdaként** lehetőséget.
5. Írja be az **ipconfig /all** parancsot.
6. Keresse meg a helyi hálózathoz csatlakoztatott Ethernet-adaptert, és jegyezze fel annak IPv4-címét. Ebben a példakörnyezetben a cím **10.16.167.195**, de az Öné ettől eltérő lesz.
7. Jegyezze fel ezt az IP-címet. Ezt fogja használni a POC1-ben létrehozott helyi hálózati átjáró erőforrás nyilvános IP-címeként.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrás létrehozása
1. Jelentkezzen be a POC1 Azure Stack fizikai gépére.
2. A **Számítógép** mezőbe írja be a **MAS-CON01** nevet, majd kattintson a **Csatlakozás** gombra.
3. Az Azure Portalon kattintson az **Új** elemre.
   
4. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
5. Az erőforrások listájában válassza a **Helyi hálózati átjáró** elemet.
6. A **Név** mezőbe írja be a következőt: **POC2-GW**.
7. Most még nem ismeri a másik átjáró IP-címét, de ez nem jelent problémát, mivel később itt módosíthatja azt. Az **IP-cím** mezőbe ezúttal írja be a következőt: **10.16.167.195**.
8. A **Címtér** mezőbe írja be a POC2-ben létrehozandó virtuális hálózat címterét. Ez a következő lesz: **10.0.20.0/23**. Írja be ezt az értéket.
9. Ellenőrizze, hogy az **Előfizetés**, **Erőforráscsoport** és **Hely** mező értéke helyes-e, majd kattintson a **Létrehozás** gombra.

### <a name="create-the-connection"></a>Kapcsolat létrehozása
1. Az Azure Portalon kattintson az **Új** elemre.
   
2. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
3. Az erőforrások listájában válassza a **Kapcsolat** elemet.
4. Az **Alapszintű** beállításpanelen a **Kapcsolat típusa** beállításnál válassza a **Helyek közötti (IPSec)** lehetőséget.
5. Jelölje ki az **Előfizetés**, **Erőforráscsoport** és **Hely** mezőt, majd kattintson az **OK** gombra.
6. A **Beállítások** panelen válassza ki a korábban létrehozott **Virtuális hálózati átjáró** (**GW1**) elemet.
7. Válassza ki a korábban létrehozott **Helyi hálózati átjáró** (**POC2-GW**) elemet.
8. A **Kapcsolat neve** mezőbe írja be a következőt: **POC1-POC2**.
9. A **Megosztott kulcs (PSK)** mezőbe írja be az **12345** értéket, majd kattintson az **OK** gombra.

### <a name="create-a-vm"></a>Virtuális gép létrehozása
A VPN-kapcsolaton áthaladó adatok ellenőrzéséhez minden egyes POC esetében virtuális gépekkel kell elküldeni és fogadni az adatokat. Most létrehoz egy virtuális gépet a POC1-ben, és a virtuális hálózat virtuálisgép-alhálózatára helyezi azt.

1. Az Azure Portalon kattintson az **Új** elemre.
   
2. A piactér menüjében válassza a **Virtuális gépek** lehetőséget.
3. A virtuálisgép-rendszerképek listájában válassza a **Windows Server 2012 R2 Datacenter** rendszerképet.
4. Az **Alapvető beállítások** panel **Név** mezőjébe írja be a következőt: **VM01**.
5. Írjon be érvényes felhasználónevet és jelszót. Ezt a fiókot fogja használni a virtuális gépre történő bejelentkezéshez a létrehozását követően.
6. Adja meg az **Előfizetés**, **Erőforráscsoport** és **Hely** mező értékét, majd kattintson az **OK** gombra.
7. A **Méret** panelen válassza ki a példány virtuálisgép-méretét, majd kattintson a **Kiválasztás** gombra.
8. A **Beállítások** panelen elfogadhatja az alapértelmezett értékeket; győződjön meg azonban arról, hogy a kiválasztott virtuális hálózat a **VNET-01**, az alhálózat beállítása pedig **10.0.10.0/24** értékű. Kattintson az **OK** gombra.
9. Az **Összefoglalás** panelen tekintse át a beállításokat, és kattintson az **OK** gombra.

## <a name="create-the-network-resources-in-poc-2"></a>A hálózati erőforrások létrehozása a POC 2-ben
### <a name="log-in-as-a-tenant"></a>Bejelentkezés bérlőként
A szolgáltatás-rendszergazda bejelentkezhet bérlőként azon csomagok, ajánlatok és előfizetések teszteléséhez, amelyeket a bérlői használhatnak. Ha még nem rendelkezik ilyennel, [hozzon létre egy bérlői fiókot](azure-stack-add-new-user-aad.md) a bejelentkezés előtt.

### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása
1. Jelentkezzen be a bérlői fiókkal.
2. Az Azure Portalon kattintson az **Új** elemre.
   
3. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
4. A menüben kattintson a **Virtuális hálózat** elemre.
5. Kattintson az erőforrást ismertető panel aljának közelében található **Létrehozás** gombra. Írja be a következő értékeket a táblázatban látható megfelelő mezőkbe.
   
   | **Mező** | **Érték** |
   | --- | --- |
   | Név |vnet-02 |
   | Címtér |10.0.20.0/23 |
   | Alhálózat neve |subnet-02 |
   | Alhálózati címtartomány |10.0.20.0/24 |
6. Most a korábban létrehozott Előfizetést kell látnia az **Előfizetés** mezőben.
7. Az Erőforráscsoport területen létrehozhat egy új erőforráscsoportot, vagy ha már rendelkezik ilyennel, válassza a **Meglévő használata** elemet.
8. Ellenőrizze az alapértelmezett **helyet**. Ha kívánja, a könnyebb hozzáférés érdekében a virtuális hálózatot rögzítheti az Irányítópulton.
9. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Hozza létre az átjáró-alhálózatot.
1. Nyissa meg az imént létrehozott virtuálishálózat-erőforrást (**Vnet-02**) az Irányítópultról.
2. A **Beállítások** panelen válassza az **Alhálózatok** elemet.
3. Kattintson az **Átjáró alhálózata** lehetőségre egy átjáró-alhálózatnak a virtuális hálózathoz történő hozzáadásához.
   
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**.
   Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.
5. A **Címtartomány** mezőbe írja be a következőt: **10.0.20.0/24**.
6. Az átjáró-alhálózat létrehozásához kattintson a **Létrehozás** gombra.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
1. Az Azure Portalon kattintson az **Új** elemre.
   
2. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
3. A hálózati erőforrások listájában válassza a **Virtuális hálózati átjáró** elemet.
4. Tekintse át a leírást, majd kattintson a **Létrehozás** gombra.
5. A **Név** mezőbe írja be a következőt: **GW2**.
6. Kattintson a **Virtuális hálózat** elemre egy virtuális hálózat kiválasztásához.
   A listán válassza a **Vnet-02** elemet.
7. Kattintson a **Nyilvános IP-cím** lehetőségre. A megnyíló **Nyilvános IP-cím választása** panelen kattintson az **Új létrehozása** parancsra.
8. A **Név** mezőbe írja be a **GW2-PiP** nevet, majd kattintson az **OK** gombra.
9. Az **Átjáró típusa** mezőben alapértelmezés szerint a **VPN** lehetőség van kiválasztva. Ne módosítsa ezt a beállítást.
10. Az **VPN típusa** mezőben alapértelmezés szerint a **Útvonalalapú** lehetőség van kiválasztva.
    Ne módosítsa ezt a beállítást.
11. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Ha kívánja, az erőforrást rögzítheti az Irányítópulton. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása
#### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Az IP-cím beszerzése a NAT virtuális gépének külső adapteréről
1. Jelentkezzen be a POC1 Azure Stack fizikai gépére.
2. Nyomja le, és tartsa lenyomva a [Windows billentyű] + R billentyűkombinációt a **Futtatás** menü megnyitásához, írja be az **mstsc** kifejezést, majd nyomja le az Enter billentyűt.
3. A **Számítógép** mezőbe írja be a **MAS-BGPNAT01** nevet, majd kattintson a  **Csatlakozás** gombra.
4. Kattintson a Start menüre, kattintson a jobb gombbal a **Windows PowerShell** elemre, majd válassza a **Futtatás rendszergazdaként** lehetőséget.
5. Írja be az **ipconfig /all** parancsot.
6. Keresse meg a helyi hálózathoz csatlakoztatott Ethernet-adaptert, és jegyezze fel annak IPv4-címét. Ebben a példakörnyezetben a cím **10.16.169.131**, de az Öné ettől eltérő lesz.
7. Jegyezze fel ezt az IP-címet. Később ezt fogja használni a POC1-ben létrehozott helyi hálózati átjáró erőforrás nyilvános IP-címeként.

#### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró erőforrás létrehozása
1. Jelentkezzen be a POC2 Azure Stack fizikai gépére.
2. A **Számítógép** mezőbe írja be a **MAS-CON01** nevet, majd kattintson a **Csatlakozás** gombra.
3. Az Azure Portalon kattintson az **Új** elemre.
   
4. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
5. Az erőforrások listájában válassza a **Helyi hálózati átjáró** elemet.
6. A **Név** mezőbe írja be a következőt: **POC1-GW**.
7. Most szüksége lesz a POC1 virtuális hálózati átjárójának nyilvános IP-címére. Az **IP-cím** mezőbe írja be a következőt: **10.16.169.131**.
8. A **Címtér** mezőbe írja be a POC1 **Vnet-01** hálózatának **10.0.0.0/16** címterét.
9. Ellenőrizze, hogy az **Előfizetés**, **Erőforráscsoport** és **Hely** mező értéke helyes-e, majd kattintson a **Létrehozás** gombra.

## <a name="create-the-connection"></a>Kapcsolat létrehozása
1. Az Azure Portalon kattintson az **Új** elemre.
   
2. A piactér menüjében válassza a **Hálózatkezelés** lehetőséget.
3. Az erőforrások listájában válassza a **Kapcsolat** elemet.
4. Az **Alapszintű** beállításpanelen a **Kapcsolat típusa** beállításnál válassza a **Helyek közötti (IPSec)** lehetőséget.
5. Jelölje ki az **Előfizetés**, **Erőforráscsoport** és **Hely** mezőt, majd kattintson az **OK** gombra.
6. A **Beállítások** panelen válassza ki a korábban létrehozott **Virtuális hálózati átjáró** (**GW1**) elemet.
7. Válassza ki a korábban létrehozott **Helyi hálózati átjáró** (**POC1-GW**) elemet.
8. A **Kapcsolat neve** mezőbe írja be a következőt: **POC2-POC1**.
9. A **Megosztott kulcs (PSK)** mezőbe írja be az **12345** értéket. Ha eltérő értéket választ, ne feledje, hogy annak meg KELL felelnie a POC1-ben létrehozott megosztott kulcs értékének. Kattintson az **OK** gombra.

## <a name="create-a-vm"></a>Virtuális gép létrehozása
Most létrehoz egy virtuális gépet a POC1-ben, és a virtuális hálózat virtuálisgép-alhálózatára helyezi azt.

1. Az Azure Portalon kattintson az **Új** elemre.
   
2. A piactér menüjében válassza a **Virtuális gépek** lehetőséget.
3. A virtuálisgép-rendszerképek listájában válassza a **Windows Server 2012 R2 Datacenter** rendszerképet.
4. Az **Alapvető beállítások** panel **Név** mezőjébe írja be a következőt: **VM02**.
5. Írjon be érvényes felhasználónevet és jelszót. Ezt a fiókot fogja használni a virtuális gépre történő bejelentkezéshez a létrehozását követően.
6. Adja meg az **Előfizetés**, **Erőforráscsoport** és **Hely** mező értékét, majd kattintson az **OK** gombra.
7. A **Méret** panelen válassza ki a példány virtuálisgép-méretét, majd kattintson a **Kiválasztás** gombra.
8. A Beállítások panelen elfogadhatja az alapértelmezett értékeket; győződjön meg azonban arról, hogy a kiválasztott virtuális hálózat a **VNET-02**, az alhálózat beállítása pedig **20.0.0.0/24** értékű. Kattintson az **OK** gombra.
9. Az **Összefoglalás** panelen tekintse át a beállításokat, és kattintson az **OK** gombra.

## <a name="configure-the-nat-vm-in-each-poc-for-gateway-traversal"></a>Az egyes POC-k NAT virtuális gépének konfigurálása az átjáróátjáráshoz
Mivel a POC önmagában is használható rendszerként, és elkülönül a fizikai gazdagép üzembe helyezési hálózatától, az a „külső” VIP-hálózat, amelyhez az átjárók csatlakoznak, valójában nem külső hálózat, hanem hálózati címfordítás (NAT) használatával egy útválasztó mögé van elrejtve. Az útválasztó tulajdonképpen egy olyan Windows Server rendszerű virtuális gép (**MAS-BGPNAT01**), amely a POC-infrastruktúrában az Útválasztás és távelérés szolgáltatás (RAAS) szerepkört futtatja. Ahhoz, hogy a helyek közötti VPN-kapcsolat mindkét végén csatlakozásra kész legyen, el kell végeznie a NAT konfigurálását az MAS-BGPNAT01 virtuális gépen. Ehhez egy olyan statikus NAT-leképezést kell létrehoznia, amely a BGPNAT virtuális gép külső felületét a VPN-kapcsolat létesítéséhez szükséges portokhoz tartozó Edge-átjárókészlet virtuális IP-címére képezi le.

> [!NOTE]
> Erre a konfigurációra kizárólag POC-környezetek esetében van szükség.
> 
> 

### <a name="configure-nat"></a>NAT konfigurálása
MINDKÉT POC-környezet esetén ezeket a lépéseket kell követnie.

1. Jelentkezzen be a POC1 Azure Stack fizikai gépére.
2. Nyomja le, és tartsa lenyomva a [Windows billentyű] + R billentyűkombinációt a **Futtatás** menü megnyitásához, írja be az **mstsc** kifejezést, majd nyomja le az **Enter** billentyűt.
3. A **Számítógép** mezőbe írja be a **MAS-BGPNAT01** nevet, majd kattintson a **Csatlakozás** gombra.
4. Kattintson a Start menüre, kattintson a jobb gombbal a **Windows PowerShell** elemre, majd válassza a **Futtatás rendszergazdaként** lehetőséget.
5. Írja be az **ipconfig /all** parancsot.
6. Keresse meg a helyi hálózathoz csatlakoztatott Ethernet-adaptert, és jegyezze fel annak IPv4-címét. Ebben a példakörnyezetben a cím **10.16.169.131** (pirossal bekarikázva), de az Öné ettől eltérő lesz.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image16.png)
7. Írja be a következő PowerShell-parancsot az IKE-hitelesítésben részt vevő portok külső NAT-címének kijelöléséhez. Ne felejtse el a környezetnek megfelelően módosítani az IP-címet.
   
       Add-NetNatExternalAddress -NatName BGPNAT -IPAddress 10.16.169.131 -PortStart 499 -PortEnd 501
8. A következőkben egy statikus NAT-leképezést fog létrehozni, amely az átjáró nyilvános IP-címére képezi le a külső címet az IPSEC-alagút 1. fázisának ISAKMP 500-as portjának leképezéséhez.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 500 -InternalPort 500
> [!NOTE] 
> Az `-InternalAddress` paraméter a korábban létrehozott virtuális hálózati átjáró nyilvános IP-címe.  Ennek az IP-címnek a megkereséséhez tekintse meg a Virtuális hálózati átjáró panel tulajdonságait, és ott keresse meg a Nyilvános IP-cím értékét.       

9. Végül konfigurálnia kell azt a NAT-átjárást, amely a 4500-as portot használja a NAT-eszközökön keresztüli teljes IPEC-alagút sikeres létesítéséhez.
   
        Add-NetNatStaticMapping -NatName BGPNAT -Protocol UDP -ExternalIPAddress 10.16.169.131 -InternalIPAddress 192.168.102.1 -ExternalPort 4500 -InternalPort 4500
> [!NOTE] 
> Az `-InternalAddress` paraméter a korábban létrehozott virtuális hálózati átjáró nyilvános IP-címe.  Ennek az IP-címnek a megkereséséhez tekintse meg a Virtuális hálózati átjáró panel tulajdonságait, és ott keresse meg a Nyilvános IP-cím értékét.       

10. Ismételje meg az 1–9. lépést a POC2-ben is.

## <a name="test-the-connection"></a>A kapcsolat tesztelése
A helyek közötti kapcsolat létesítését követően ellenőrizze, hogy sikeresen zajlik-e azon keresztül az adatforgalom. Ez egy egyszerű feladat, mivel annak végrehajtása során csupán be kell jelentkeznie a POC-környezetek egyikében létrehozott virtuális gépek valamelyikére, és pingelnie kell a másik környezetben létrehozott virtuális gépet. Annak ellenőrzéséhez, hogy az adatforgalom valóban a helyek közötti kapcsolaton történik, meg kell győződnie arról, hogy a távoli alhálózaton lévő virtuális gép közvetlen IP-címét (DIP) pingeli, nem pedig a virtuális IP-címet. Ehhez meg kell keresnie, majd fel kell jegyeznie a kapcsolat másik végének címét.

### <a name="log-in-to-the-tenant-vm-in-poc1"></a>Bejelentkezés a bérlői virtuális gépre a POC1-ben
1. Jelentkezzen be a POC1 Azure Stack fizikai gépére, és jelentkezzen be a portálra egy bérlői fiókkal.
2. Kattintson a bal oldali navigációs sávban lévő **Virtuális gépek** elemre.
3. Keresse meg a korábban létrehozott **VM01** gépet a virtuális gépek listájában, és kattintson rá.
4. A virtuális gép paneljén kattintson a **Csatlakozás** elemre.
   
     ![](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Nyisson meg egy parancssort a virtuális gépen, és írja be az **ipconfig /all** parancsot.
6. A kimenetben keresse meg az **IPv4-cím** értékét, és jegyezze fel azt. Ezt a címet fogja pingelni a POC2-ből. Ebben a példakörnyezetben a cím **10.0.10.4**, de az Ön környezetében ettől eltérő lehet. De a korábban létrehozott **10.0.10.0/24** alhálózatba kell esnie.

### <a name="log-in-to-the-tenant-vm-in-poc2"></a>Jelentkezzen be a bérlő virtuális gépre a POC2-ben
1. Jelentkezzen be a POC2 Azure Stack fizikai gépére, és jelentkezzen be a portálra egy bérlő fiókkal.
2. Kattintson a bal oldali navigációs sávban lévő **Virtuális gépek** elemre.
3. Keresse meg a korábban létrehozott **VM02** gépet a virtuális gépek listájában, és kattintson rá.
4. A virtuális gép paneljén kattintson a **Csatlakozás** elemre.
   
5. Nyisson meg egy parancssort a virtuális gépen, és írja be az **ipconfig /all** parancsot.
6. Egy, a 10.0.20.0/24 alhálózatba eső IPv4-címet kell látnia. A példakörnyezetben a cím 10.0.20.4, de lehet, hogy az Öné más.
7. Most a POC2 környezetben lévő virtuális gépről pingelni szeretné a POC1 környezetben lévő virtuális gépet az alagúton keresztül. Ehhez pingelnie kell a VM01 gépről rögzített dedikált IP-címet.
   A példakörnyezetben ez 10.0.10.4, de győződjön meg róla, hogy a hálózatán feljegyzett címet pingelje. A következőhöz hasonló eredményt kell látnia:
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
8. A távoli virtuális gépről kapott válasz sikeres tesztet jelez! Bezárhatja a VM csatlakoztatása ablakot. Vagy megpróbálhat más adatátvitelt, például fájlmásolást végezni a kapcsolat teszteléséhez.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Adatátviteli statisztika megtekintése az átjárókapcsolaton keresztül
Ha tudni szeretné, mennyi adat halad át a helyek közötti kapcsolaton keresztül, ez az információ a Kapcsolat panelen érhető el. Ez a teszt annak ellenőrzéséhez is jó módszer, hogy az imént elküldött ping tényleg végighaladt-e a VPN-kapcsolaton.

1. Mialatt be van jelentkezve a bérlő virtuális gépre a POC2 környezetben, jelentkezzen be a **Microsoft Azure Stack POC portálra** a bérlő fiókjával.
2. Kattintson a **Tallózás** menüelemre, és válassza a **Kapcsolatok** lehetőséget.
3. Kattintson a listában a **POC2-POC1** kapcsolatra.
4. A Kapcsolat panelen láthatja a **Bejövő adatforgalom** és a **Kimenő adatforgalom** statisztikáit. A következő képernyőfelvételen néhány nagyobb számot lát, mint amelyeket csak a pingelés hozna létre. Ennek oka néhány további fájlátvitel. Néhány nem nulla értéket kell látnia itt.
   
    ![](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)




<!--HONumber=Feb17_HO2-->


