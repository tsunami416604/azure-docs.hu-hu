---
title: Infrastruktúra létrehozása fürthöz az Azure virtuális gépein
description: Ebben az oktatóanyagban megtudhatja, hogyan állíthatja be az Azure virtuális gép infrastruktúráját egy Service Fabric-fürt futtatásához.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: 93a7e2507ab3a467ef83924479872694cae2dd5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614009"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Oktatóanyag: Hozzon létre Azure VM-infrastruktúrát egy Service Fabric-fürt üzemeltetéséhez

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban hozzon létre egy önálló fürtet az Azure virtuális gépeken, és telepítsen rá egy alkalmazást.

Ez az oktatóanyag egy sorozat első része. Ebben a cikkben az Azure virtuális gép erőforrások üzemeltetéséhez szükséges a Service Fabric önálló fürtje. A további cikkekben telepíteni fogja az önálló Service Fabric-csomagot, majd egy mintaalkalmazást a fürtön, végül pedig törli a fürtöt.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * AzureVM-példányok készletének létrehozása
> * A biztonsági csoport módosítása
> * Bejelentkezés az egyik példányba
> * A példány előkészítése a Service Fabrichez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre.  Ha még nem rendelkezik fiókkal, az [Azure Portalon](https://portal.azure.com) hozzon létre egyet.

## <a name="create-azure-virtual-machine-instances"></a>Azure virtuálisgép-példányok létrehozása

1. Jelentkezzen be az Azure Portalon, és válassza ki **a virtuális gépek** (nem virtuális gépek (klasszikus)).

   ![Az Azure Portal virtuális gépe][az-console]

2. Válassza a **Hozzáadás** gombot, amely megnyitja a **Virtuálisgép létrehozása űrlapot.**

3. Az **Alapok** lapon mindenképpen válassza ki a kívánt előfizetést és erőforráscsoportot (új erőforráscsoport használata ajánlott).

4. Módosítsa a **lemezkép** típusát **Windows Server 2016 Datacenter rendszerre.** 
 
5. Módosítsa a példány **méretét** **szabványos DS2 v2-re.** Állítsa be a rendszergazda **felhasználónevét** és **jelszavát,** megjegyezve, hogy mik azok.

6. A **bejövő portszabályokat** egyelőre letiltva hagyja; mi fogja beállítani azokat a következő szakaszban.

7. A **Hálózat** lapon hozzon létre egy új **virtuális hálózatot,** és vegye figyelembe a nevét.

8. Ezután állítsa a **hálózati adapter hálózati biztonsági csoportját** Speciális **beállításra.** Hozzon létre egy új biztonsági csoportot, amely feljegyzi a nevét, és hozza létre a következő szabályokat, hogy a TCP-forgalmat bármilyen forrásból engedélyezze:

   ![sf-bejövő][sf-inbound]

   * Port `3389`, RDP és ICMP (alapvető kapcsolat).
   * Portok `19000-19003`, service fabric.
   * Portok `19080-19081`, service fabric.
   * Port `8080`, webböngésző-kérésekhez.

   > [!TIP]
   > A Service Fabricben a virtuális gépek összekapcsolásához az infrastruktúrát futtató virtuális gépeknek ugyanazokkal a hitelesítő adatokkal kell rendelkezniük.  Két gyakori módja van a konzisztens hitelesítő adatok elérésének: csatlakoztassa mindet ugyanahhoz a tartományhoz, vagy állítsa be ugyanazt a rendszergazdai jelszót az összes virtuális gépen. Szerencsére az Azure lehetővé teszi, hogy az ugyanazon a **virtuális hálózaton** lévő összes virtuális gép könnyen csatlakozzon, így biztos leszünk abban, hogy minden példányunk ugyanazon a hálózaton lesz.

9. Adjon hozzá egy másik szabályt. Állítsa be a forrást **szolgáltatáscímkére,** és állítsa a forrásszolgáltatás címkéjét **VirtualNetwork**-re. A Service Fabric használatához a következő portokat kell megnyitni a fürtön belüli kommunikációhoz: 135 137-139 445 20001-20031,20606-20861.

   ![vnet-bejövő][vnet-inbound]

10. A többi beállítás alapértelmezett állapotában elfogadható. Tekintse át őket, ha úgy tetszik, majd indítsa el a virtuális gépet.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>További példányok létrehozása a Service Fabric-fürthöz

Indítson el még két **virtuális gépet,** hogy az előző szakaszban ismertetett beállításokat megtartsa. Különösen ugyanazt a rendszergazdai felhasználónevet és jelszót tartja fenn. A **virtuális hálózat** és a hálózati adapter hálózati biztonsági **csoportját** nem szabad újra létrehozni; válassza ki a már létrehozottakat a legördülő menüből. Az egyes példányok üzembe helyezése néhány percet is igénybe vehet.

## <a name="connect-to-your-instances"></a>Csatlakozás a példányokhoz

1. Válassza ki az egyik példányt a **Virtuálisgép** szakaszban.

2. Az **Áttekintés** lapon vegye figyelembe a *privát* IP-címet. Ezután kattintson a **Csatlakozás gombra.**

3. Az **RDP** lapon vegye figyelembe, hogy a nyilvános IP-címet és a 3389-es portot használjuk, amelyet korábban kifejezetten megnyitottunk. Töltse le az RDP-fájlt.
 
4. Nyissa meg az RDP-fájlt, és amikor a rendszer kéri, adja meg a virtuális gép beállításában megadott felhasználónevet és jelszót.

5. Miután csatlakozott egy példányhoz, ellenőriznie kell, hogy a távoli rendszerleíró adatbázis futott-e, engedélyeznie kell az SMB-t, és meg kell nyitnia az SMB és a távoli beállításjegyzék szükséges portjait.

   Az SMB engedélyezéséhez a PowerShell parancs a következő:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. A tűzfalban lévő portok megnyitására ez a PowerShell-parancs szolgál:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Ismételje meg ezt a folyamatot a többi példányesetében, ismét megjegyezve a privát IP-címeket.

## <a name="verify-your-settings"></a>A beállítások ellenőrzése

1. Az alapvető kapcsolat érvényesítéséhez csatlakozzon az RDP használatával az egyik virtuális géphez.

2. Nyissa meg a **parancssort** az adott virtuális gépből, majd a ping paranccsal csatlakozzon az egyik virtuális gépről a másikra, és cserélje le az alábbi IP-címet a korábban említett privát IP-címek egyikére (nem pedig annak a virtuális gépnek az IP-címére, amelyhez már csatlakozik).

   ```
   ping 172.31.20.163
   ```

   Ha a kimenetben a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` szöveg ismétlődik négyszer, akkor a példányok közötti kapcsolat működik.

3. Most ellenőrizze az SMB-megosztás működését a következő paranccsal:

   ```
   net use * \\172.31.20.163\c$
   ```

   A parancs kimenetének a következőnek kell lennie: `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Most a példányok megfelelően előkészített Service Fabric.

## <a name="next-steps"></a>További lépések

A sorozat első részében megtanulta, hogyan indíthat el három Azure virtuálisgép-példányt, és hogyan konfigurálhatja őket a Service Fabric telepítéséhez:

> [!div class="checklist"]
> * Azure virtuálisgép-példányok készletének létrehozása
> * A biztonsági csoport módosítása
> * Bejelentkezés az egyik példányba
> * A példány előkészítése a Service Fabrichez

A sorozat második részében konfigurálni fogja a Service Fabricet a fürtben.

> [!div class="nextstepaction"]
> [A Service Fabric telepítése](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
