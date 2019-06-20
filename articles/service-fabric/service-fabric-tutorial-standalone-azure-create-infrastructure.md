---
title: Az oktatóanyag az infrastruktúra hoz létre egy Service Fabric-fürt Azure virtuális gépeken – az Azure Service Fabric |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja az Azure-beli Virtuálisgép-infrastruktúra beállítása egy Service Fabric-fürt futtatására.
services: service-fabric
documentationcenter: .net
author: v-vasuke
manager: jpconnock
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/25/2019
ms.author: v-vasuke
ms.custom: mvc
ms.openlocfilehash: b5f2f77b3caed483aed1736bd510096d44329284
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276005"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Oktatóanyag: Service Fabric-fürt futtatására Azure-beli Virtuálisgép-infrastruktúra létrehozása

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Oktatóanyag-sorozat Azure virtuális gépeken futó önálló fürt létrehozása, majd telepíteni rá egy alkalmazást.

Ez az oktatóanyag egy sorozat első része. Ebben a cikkben az Azure virtuális gép önálló Service Fabric-fürt üzemeltetéséhez szükséges erőforrások létrehozásához. A további cikkekben telepíteni fogja az önálló Service Fabric-csomagot, majd egy mintaalkalmazást a fürtön, végül pedig törli a fürtöt.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * AzureVM példányok létrehozása
> * A biztonsági csoport módosítása
> * Bejelentkezés az egyik példányba
> * A példány előkészítése a Service Fabrichez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre.  Ha még nem rendelkezik fiókkal, folytassa a [az Azure portal](https://portal.azure.com) hozhat létre egyet.

## <a name="create-azure-virtual-machine-instances"></a>Azure virtuálisgép-példányok létrehozása

1. Jelentkezzen be az Azure Portalon, és válassza **virtuális gépek** (nem a virtuális gépek (klasszikus)).

   ![Az Azure portal virtuális gép][az-console]

2. Válassza ki a **Hozzáadás** gombra, amely, megnyílik a **hozzon létre egy virtuális gépet** űrlap.

3. Az a **alapjai** lapra, válassza az előfizetésben és erőforráscsoportban csoport azt szeretné, hogy a (javasolt egy új erőforráscsoportot használ).

4. Módosítsa a **kép** írja be a **Windows Server 2016 Datacenter**. 
 
5. Módosítsa a példány **mérete** való **Standard DS2 v2**. Állítsa be a rendszergazda **felhasználónév** és **jelszó**, Mik ezek megállapítva.

6. Hagyja a **bejövőport-szabályok** most; letiltott konfigurálunk, a következő szakaszban azokat.

7. Az a **hálózatkezelés** fülre, hozzon létre egy új **virtuális hálózat** , és jegyezze fel a neve.

8. Következő lépésként állítsa a **hálózati adapter hálózati biztonsági csoport** való **speciális**. Hozzon létre egy új biztonsági csoportot, figyelembe véve a nevét, és hozza létre a következő szabályokat, hogy a TCP-forgalom bármilyen forrásból származó:

   ![sf-inbound][sf-inbound]

   * Port `3389`, RDP és ICMP (kapcsolat).
   * Portok `19000-19003`, a Service Fabrichez.
   * Portok `19080-19081`, a Service Fabrichez.
   * Port `8080`, a webes böngésző kéréseket.

   > [!TIP]
   > A Service Fabricben a virtuális gépek összekapcsolásához az infrastruktúrát futtató virtuális gépeknek ugyanazokkal a hitelesítő adatokkal kell rendelkezniük.  Két gyakori módja van a konzisztens hitelesítő adatok elérésének: csatlakoztassa mindet ugyanahhoz a tartományhoz, vagy állítsa be ugyanazt a rendszergazdai jelszót az összes virtuális gépen. Szerencsére az Azure lehetővé teszi, hogy az összes virtuális gép ugyanazon **virtuális hálózati** könnyedén kapcsolat, így azt róla, hogy az összes példány van ugyanazon a hálózaton.

9. Adjon meg egy másik szabályt. Állítsa be a kívánt forrás **Szolgáltatáscímke** , és állítsa a forrás-szolgáltatáscímke **VirtualNetwork**. A Service Fabric a következő portokat kell a fürtön belüli kommunikációra nyitva van szükség: 135,137-139,445,20001-20031,20606-20861.

   ![vnet-inbound][vnet-inbound]

10. A többi beállítást az alapértelmezett állapotban elfogadhatók. Tekintse át igény szerint, és indítsa újra a virtuális gép.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>A Service Fabric-fürt több példány létrehozása

Indítsa el a két további **virtuális gépek**, figyeljen arra, hogy ugyanazokat a beállításokat az előző szakaszban leírt karbantartása. Különösen karbantartása rendszergazdai felhasználónevet és jelszót. A **virtuális hálózat** és **hálózati adapter hálózati biztonsági csoport** nem kell újból; válassza ki a már létrehozottakat a legördülő menüből. Az egyes üzembe helyezni a példány néhány percig is eltarthat.

## <a name="connect-to-your-instances"></a>Csatlakozás a példányok

1. Válasszon egyet a példányok a **virtuális gép** szakaszban.

2. Az a **áttekintése** fülre, jegyezze fel a *privát* IP-címet. Kattintson a **Connect**.

3. Az a **RDP** fülre, vegye figyelembe, hogy használjuk-e a nyilvános IP-cím és a 3389-es, amely tudjuk pontosabban korábban megnyitott. Töltse le az RDP-fájlt.
 
4. Nyissa meg az RDP-fájlt, amikor a rendszer kéri, és adja meg a felhasználónevet és jelszót adott meg a virtuális gép beállítása.

5. Miután csatlakozott egy példánnyal, ellenőrizze, hogy kell futott a távoli beállításjegyzék, az SMB engedélyezéséhez és a szükséges portok megnyitása SMB és a távoli beállításjegyzék.

   Az SMB engedélyezéséhez azt a PowerShell-parancsot:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. A tűzfalban lévő portok megnyitására ez a PowerShell-parancs szolgál:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Ismételje meg ezt a folyamatot a többi példány, újra megállapítva, magánhálózati IP-címet.

## <a name="verify-your-settings"></a>A beállítások ellenőrzése

1. Ellenőrizze a hálózati kapcsolat, csatlakoznia kell a virtuális gépek RDP-vel egyikét.

2. Nyissa meg a **parancssor** a belül a virtuális Gépeket, majd a ping paranccsal csatlakozhat egy virtuális gépről a másikra, és cserélje le az alábbi IP-cím egy magánhálózati IP-címek korábban feljegyzett (nem az IP-címét a virtuális gép csatlakozik már).

   ```
   ping 172.31.20.163
   ```

   Ha a kimenetben a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` szöveg ismétlődik négyszer, akkor a példányok közötti kapcsolat működik.

3. Most ellenőrizze az SMB-megosztás működését a következő paranccsal:

   ```
   net use * \\172.31.20.163\c$
   ```

   A parancs kimenetének a következőnek kell lennie: `Drive Z: is now connected to \\172.31.20.163\c$.`.


   Most már a példányok megfelelően a Service fabric előkészítve.

## <a name="next-steps"></a>További lépések

A sorozat első részében, az útmutatóból megtudhatta, hogyan három Azure-beli Virtuálisgép-példányok indítása, és beszerezheti őket a Service Fabric-telepítés konfigurálása:

> [!div class="checklist"]
> * Hozzon létre egy Azure-beli Virtuálisgép-példányok
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
