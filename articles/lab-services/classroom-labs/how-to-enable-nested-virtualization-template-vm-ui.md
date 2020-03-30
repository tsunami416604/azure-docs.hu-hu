---
title: Beágyazott virtualizálás engedélyezése sablonvirtuális gépen az Azure Lab Services (UI) szolgáltatásban | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre sablon virtuális gépet több virtuális gép belsejében.  Más szóval engedélyezze a beágyazott virtualizálást egy sablon virtuális gépen az Azure Lab Servicesben.
services: lab-services
author: emaher
ms.service: lab-services
ms.topic: article
ms.date: 1/23/2020
ms.author: enewman
ms.openlocfilehash: 10f8d2760474631fbcabac4d66139aedf4c7560c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503074"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-manually"></a>Beágyazott virtualizálás engedélyezése sablonos virtuális gépen manuálisan az Azure Lab Servicesben

A beágyazott virtualizáció lehetővé teszi, hogy több virtuális gép környezetet hozzon létre a tesztkörnyezet sablonvirtuális gépén belül. A sablon közzététele biztosítja a labor minden felhasználó számára egy virtuális gép beállítása több virtuális gép belül.  A beágyazott virtualizálásról és az Azure Lab Servicesről a [Beágyazott virtualizálás engedélyezése sablonalapú virtuális gépen az Azure Lab Servicesben című témakörben](how-to-enable-nested-virtualization-template-vm.md)olvashat bővebben.

Ez a cikk bemutatja, hogyan állíthatja be a beágyazott virtualizálást egy sablongépen az Azure Lab Servicesben a Windows-szerepkörök és -eszközök közvetlen használatával.  Van néhány dolog szükséges ahhoz, hogy egy osztály számára a beágyazott virtualizáció.  Az alábbi lépések ismertetik, hogyan manuálisan állíthat be egy Lab Services-gépsablont a Hyper-V-vel.  A lépések Windows Server 2016 vagy Windows Server 2019 rendszerhez készültek.  

>[!IMPORTANT]
>Válassza ki a **Nagy (beágyazott virtualizálás)** vagy **közepes (beágyazott virtualizálás)** a virtuális gép mérete a labor létrehozásakor.  A beágyazott virtualizáció egyébként nem fog működni.  

## <a name="enable-hyper-v-role"></a>Hyper-V szerepkör engedélyezése

Az alábbi lépések a Hyper-V Windows Server rendszeren történő engedélyezéséhez szükséges műveleteket ismertetik a Kiszolgálókezelő használatával.  A telepítés sikeres stakarása után a Hyper-V kezelője elérhető lesz az ügyfél virtuális gépek hozzáadásához, módosításához és törléséhez.

1. A **Kiszolgálókezelőben**kattintson az Irányítópult lapon a **Szerepkörök és szolgáltatások hozzáadása gombra.**
2. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
3. A **Telepítéstípus kiválasztása** lapon tartsa meg a szerepkör- vagy szolgáltatásalapú telepítés alapértelmezett beállítását, majd kattintson a **Tovább**gombra.
4. A **Célkiszolgáló kiválasztása** lapon válassza a Kiszolgáló kiválasztása a kiszolgálókészletből lehetőséget.   Az aktuális kiszolgáló már ki lesz jelölve.  Kattintson a Tovább gombra.
5. A **Kiszolgálói szerepkörök kiválasztása** lapon válassza a **Hyper-V**lehetőséget.  
6. A **Szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablakjelenik meg.  Válassza **a Felügyeleti eszközök belefoglalása lehetőséget (ha van ilyen)** lehetőséget.  Kattintson a **Szolgáltatások hozzáadása** gombra.
7. A **Kiszolgálói szerepkörök kiválasztása** lapon kattintson a **Tovább** gombra.
8. A **Szolgáltatások kiválasztása lapon**kattintson a **Tovább**gombra.
9. A **Hyper-V** lapon kattintson a **Tovább** gombra.
10. A **Virtuális váltások létrehozása** lapon fogadja el az alapértelmezett beállításokat, és kattintson a **Tovább**gombra.
11. A **Virtuálisgép áttelepítése** lapon fogadja el az alapértelmezett értékeket, és kattintson a **Tovább**gombra.
12. Az **Alapértelmezett raktárkészletek** lapon fogadja el az alapértelmezett értékeket, és kattintson a **Tovább**gombra.
13. A **Telepítési beállítások megerősítése** lapon szükség esetén válassza **a Célkiszolgáló automatikus újraindítása**lehetőséget.
14. Amikor megjelenik a **Szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablaka, kattintson az **Igen**gombra.
15. Kattintson a **Telepítés gombra.**
16. Várja meg, amíg a **Telepítés folyamata** lap jelzi, hogy a Hyper-V szerepkör befejeződött.  A készülék a telepítés közepén újraindulhat.
17. Kattintson a **Bezárás** gombra.

## <a name="enable-dhcp-role"></a>DHCP-szerepkör engedélyezése

Minden létrehozott Hyper-V ügyfél virtuális gépnek szüksége van egy IP-címre a NAT-hálózatban.  Később létrehozunk egy NAT hálózatot.  Az IP-címek hozzárendelésének egyik módja az állomás, ebben az esetben a tesztkörnyezet virtuálisgép-sablonja DHCP-kiszolgálóként való beállítása.  Az alábbiakban a DHCP-szerepkör engedélyezéséhez szükséges lépéseket talál.

1. A **Kiszolgálókezelőben**kattintson az **Irányítópult** lapon a **Szerepkörök és szolgáltatások hozzáadása gombra.**
2. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
3. A **Telepítési típus kiválasztása** lapon válassza **a Szerepkör- vagy szolgáltatásalapú telepítés** lehetőséget, majd kattintson a **Tovább**gombra.
4. A **Célkiszolgáló kiválasztása** lapon jelölje ki az aktuális kiszolgálót a kiszolgálókészletből, majd kattintson a **Tovább**gombra.
5. A **Kiszolgálói szerepkörök kiválasztása** lapon válassza a **DHCP-kiszolgáló**lehetőséget.  
6. A **Szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablakjelenik meg.  Válassza **a Felügyeleti eszközök belefoglalása lehetőséget (ha van ilyen)** lehetőséget.  Kattintson **a Szolgáltatások hozzáadása gombra.**

    >[!NOTE]
    >Előfordulhat, hogy egy érvényesítési hiba azt jelzi, hogy nem található statikus IP-cím.  Ez a figyelmeztetés figyelmen kívül hagyható a forgatókönyvünkhöz.

7. A **Kiszolgálói szerepkörök kiválasztása** lapon kattintson a **Tovább** gombra.
8. A **Szolgáltatások kiválasztása** lapon kattintson a **Tovább**gombra.
9. A **DHCP-kiszolgáló** lapon kattintson a **Tovább gombra.**
10. A **Telepítendő összetevők megerősítése** lapon kattintson a **Telepítés** parancsra.
11. Várja meg, amíg a **telepítés folyamata lap** jelzi, hogy a DHCP-szerepkör befejeződött.
12. Kattintson a Bezárás gombra.

## <a name="enable-routing-and-remote-access-role"></a>Útválasztás és távelérés szerepkör engedélyezése

1. A **Kiszolgálókezelőben**kattintson az **Irányítópult** lapon a **Szerepkörök és szolgáltatások hozzáadása gombra.**
2. Az **Alapismeretek** lapon kattintson a **Tovább** gombra.
3. A **Telepítési típus kiválasztása** lapon válassza **a Szerepkör- vagy szolgáltatásalapú telepítés** lehetőséget, majd kattintson a **Tovább**gombra.
4. A **Célkiszolgáló kiválasztása** lapon jelölje ki az aktuális kiszolgálót a kiszolgálókészletből, majd kattintson a **Tovább**gombra.
5. A **Kiszolgálói szerepkörök kiválasztása** lapon válassza a **Távelérés**lehetőséget. Kattintson az **OK** gombra.
6. A **Szolgáltatások kiválasztása** lapon kattintson a **Tovább**gombra.
7. A **Távelérés** lapon kattintson a **Tovább**gombra.
8. A **Szerepkör-szolgáltatások** lapon válassza az **Útválasztás**lehetőséget.
9. A **Szerepkörök és szolgáltatások hozzáadása varázsló** előugró ablakjelenik meg.  Válassza **a Felügyeleti eszközök belefoglalása lehetőséget (ha van ilyen)** lehetőséget.  Kattintson **a Szolgáltatások hozzáadása gombra.**
10. Kattintson a **Tovább** gombra.
11. A **Webkiszolgálói szerepkör (IIS)** lapon kattintson a **Tovább** gombra.
12. A **Szerepkör-szolgáltatások kiválasztása** lapon kattintson a **Tovább** gombra.
13. A **Telepítendő összetevők megerősítése** lapon kattintson a **Telepítés** parancsra.
14. Várja meg, amíg a **Telepítés folyamata** lap jelzi, hogy a távelérési szerepkör befejeződött.  
15. Kattintson a **Bezárás** gombra.

## <a name="create-virtual-nat-network"></a>Virtuális NAT-hálózat létrehozása

Most, hogy az összes szükséges szerepkör telepítve van, itt az ideje, hogy létrehozza a NAT-hálózatot.  A létrehozási folyamat magában foglalja egy kapcsoló és a NAT hálózat létrehozását.  A hálózati címfordítási (hálózati címfordítási) hálózat nyilvános IP-címet rendel a magánhálózaton lévő virtuális gépek egy csoportjához, hogy lehetővé tegye az internethez való csatlakozást.  A mi esetünkben a privát virtuális gépek csoportja lesz a beágyazott virtuális gépek.  A NAT-hálózat lehetővé teszi, hogy a beágyazott virtuális gépek kommunikáljanak egymással. A kapcsoló olyan hálózati eszköz, amely kezeli a hálózati forgalom fogadását és útválasztását.

### <a name="create-a-new-virtual-switch"></a>Új virtuális kapcsoló létrehozása

1. Nyissa meg a **Hyper-V Manager** alkalmazást a Windows felügyeleti eszközeitől.
2. Válassza ki az aktuális kiszolgálót a bal oldali navigációs menüben.
3. Kattintson **a Virtuálisváltás-kezelő gombra...** a **Hyper-V Manager**jobb oldalán található **Műveletek** menüből.
4. A **Virtual Switch Manager** előugró ablakban válassza a **Belső** lehetőséget a létrehozandó kapcsoló típusához.  Kattintson **a Virtuális kapcsoló létrehozása gombra.**
5. Az újonnan létrehozott virtuális kapcsoló, állítsa be a nevét, hogy valami emlékezetes.  Ebben a példában a "LabServicesSwitch" használatát fogjuk használni.  Kattintson az **OK** gombra.
6. Új hálózati adapter jön létre.  A név hasonló lesz a "vEthernet (LabServicesSwitch)"-hez.  A **Vezérlőpult**megnyitásának ellenőrzéséhez kattintson a **Hálózat és internet**menü Hálózati állapot és feladatok **megtekintése**parancsára.  A bal oldalon kattintson az **Adapterbeállítások módosítása gombra.**

### <a name="create-a-nat-network"></a>NAT-hálózat létrehozása

1. Nyissa meg az **Útválasztás és távelérés** eszközt a Windows felügyeleti eszközökből.
2. Jelölje ki a helyi kiszolgálót a bal oldali navigációs lapon.
3. Válassza a **Művelet** -> **konfigurálása és az Útválasztás és távelérés engedélyezése**lehetőséget.
4. Amikor **megjelenik az Útválasztás és távelérés kiszolgáló beállítása varázsló,** kattintson a **Tovább**gombra.
5. A **Konfiguráció lapon** válassza a **Hálózati címfordítás (NAT) konfigurációját.**  Kattintson a **Tovább** gombra.

    >[!WARNING]
    >Ne válassza a "Virtuális magánhálózati (VPN) hozzáférés és NAT" lehetőséget.

6. A **NAT Internetkapcsolat** lapon válassza az "Ethernet" lehetőséget.  Ne válassza a "vEthernet (LabServicesSwitch)" kapcsolatot, amelyet a Hyper-V Managerben hoztunk létre. Kattintson a **Tovább** gombra.
7. Kattintson a varázsló utolsó lapjának **Befejezés** gombjára.
8. Amikor megjelenik **a Szolgáltatás indítása** párbeszédpanel, kattintson a **Szolgáltatás indítása**gombra.
9. Várjon, amíg a szolgáltatás elindul.

## <a name="update-network-adapter-settings"></a>Hálózati adapter beállításainak frissítése

A hálózati adapter a korábban létrehozott NAT-hálózat alapértelmezett átjáró IP-címéhez használt IP-címhez lesz társítva.  Ebben a példában egy 192.168.0.1 IP-címet hozunk létre 255.255.255.0 alhálózati maszkkal.  A korábban létrehozott virtuális kapcsolót fogjuk használni.

1. Nyissa meg a **Vezérlőpultot**, kattintson a **Hálózat és internet**parancsra, majd a Hálózati állapot és feladatok **megtekintése**parancsra.
2. A bal oldalon kattintson az **Adapterbeállítások módosítása gombra.**  
3. A **Hálózati kapcsolatok** ablakban kattintson duplán a "vEthernet (LabServicesSwitch)" gombra a **vEthernet (LabServicesSwitch) állapot** részletei párbeszédpanel megjelenítéséhez.
4. Kattintson a **Tulajdonságok** gombra.
5. Válassza az **Internet Protocol Version 4 (TCP/IPv4)** elemet, és kattintson a **Tulajdonságok** gombra.
6. Az **Internet Protocol Version 4 (TCP/IPv4) tulajdonságai** párbeszédpanelen válassza **a Use the following IP address (Az alábbi IP-cím használata**) lehetőséget.  Az IP-címhez írja be a 192.168.0.1 értéket. Az alhálózati maszk mezőbe írja be a 255.255.255.0 értéket.  Hagyja üresen az alapértelmezett átjárót.  Hagyja üresen a DNS-kiszolgálókat is.

    >[!NOTE]
    > NAT hálózatunk kínálata CIDR jelölésben 192.168.0.0/24 lesz.  Ez 192.168.0.1 és 192.168.0.254 közötti használható IP-címek tartományát hozza létre.  Konvenció szerint az átjárók rendelkeznek az alhálózati tartomány első IP-címével.

7. Kattintson az OK gombra.

## <a name="create-dhcp-scope"></a>DHCP-hatókör létrehozása

A következő lépések a DHCP-hatókör hozzáadására vonatkozó utasítások.  Ebben a cikkben a NAT hálózat unk 192.168.0.0/24 CIDR jelölésben.  Ez 192.168.0.1 és 192.168.0.254 közötti használható IP-címek tartományát hozza létre.  A létrehozott hatókörnek a használható címek ezen tartományában kell lennie, kivéve a korábban már létrehozott IP-címet.

1. Nyissa **meg a Felügyeleti eszközöket,** és nyissa meg a **DHCP** felügyeleti eszközt.
2. A **DHCP** eszközben bontsa ki az aktuális kiszolgáló csomópontját, és válassza az **IPv4**lehetőséget.
3. A Művelet menüben válassza az **Új hatókör...**
4. Amikor megjelenik az **Új hatókör varázsló,** kattintson a **Tovább** gombra az **üdvözlőlapon.**
5. A **Hatókör neve** lapon írja be a "LabServicesDhcpScope" vagy a névhez való hasonló emlékezetes értéket.  Kattintson a **Tovább** gombra.
6. Az **IP-címtartomány** lapon adja meg a következő értékeket.

    - 192.168.0.100 a Kezdő IP-címhez
    - 192.168.0.200 a záró IP-cím
    - 24 a hossz
    - 255.255.255.0 az Alhálózati maszk

7. Kattintson a **Tovább** gombra.
8. A **Kizárások hozzáadása és késleltetés** lapon kattintson a **Tovább**gombra.
9. A **Címbérlet időtartama** lapon kattintson a **Tovább**gombra.
10. A **DHCP-beállítások konfigurálása** lapon válassza az **Igen lehetőséget, most szeretném konfigurálni ezeket a beállításokat.** Kattintson a **Tovább** gombra.
11. **Útválasztón (alapértelmezett átjáró)**
12. Add hozzá 192.168.0.1, ha még nem történt meg. Kattintson a **Tovább** gombra.
13. A **Tartománynév és DNS-kiszolgálók** lapon adja hozzá a 168.63.129.16 értéket DNS-kiszolgáló IP-címeként, ha még nem történt meg.  A 168.63.129.16 egy Azure statikus DNS-kiszolgáló IP-címe. Kattintson a **Tovább** gombra.
14. A **WINS-kiszolgálók** lapon kattintson a **Tovább gombra.**
15. A **Hatókör aktiválása** lapon válassza az Igen lehetőséget, most **aktiválni szeretném ezt a hatókört.**  Kattintson a **Tovább** gombra.
16. Az **Új hatókör varázsló befejezése** lapon kattintson a **Befejezés gombra.**

## <a name="conclusion"></a>Összegzés

Most a sablongép készen áll a Hyper-V virtuális gépek létrehozására.   A Hyper-V virtuális gépek létrehozásáról a Virtuális gép létrehozása a [Hyper-V-ben](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) című témakörben talál.  Tekintse meg a [Microsoft Evaluation Center t](https://www.microsoft.com/evalcenter/) is, amelyből megszeretné tekinteni az elérhető operációs rendszereket és szoftvereket.

## <a name="next-steps"></a>További lépések

A következő lépések gyakoriak a tesztkörnyezet beállításában.

- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemezés beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail regisztrációs linkek diákok](how-to-configure-student-usage.md#send-invitations-to-users)