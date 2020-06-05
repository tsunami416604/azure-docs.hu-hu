---
title: 'Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót a Azure Portal segítségével, és tesztelheti a NAT szolgáltatást
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417837"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása a Azure Portal használatával és a NAT szolgáltatás tesztelése

Ebben az oktatóanyagban létrehoz egy NAT-átjárót az Azure-beli virtuális gépek kimenő kapcsolatának biztosításához. A NAT-átjáró teszteléséhez üzembe kell helyeznie egy forrás-és cél virtuális gépet. Tesztelje a NAT-átjárót úgy, hogy kimenő kapcsolatokat végez a forrás és a cél virtuális gép között a nyilvános IP-címhez.  Ez az oktatóanyag a forrás és a cél két különböző virtuális hálózatban való üzembe helyezését végzi el, csak az egyszerűség kedvéért.

Ha szeretné, hajtsa végre ezeket a lépéseket az [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) vagy a [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) használatával a portál helyett.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomhoz

A következő lépésekben végigvezeti Önt a teljes tesztkörnyezet konfigurálásán és a tesztek végrehajtásán. Kezdjük a forrással, amely a későbbi lépésekben létrehozott NAT Gateway-erőforrást fogja használni.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Egy virtuális gép üzembe helyezése és a NAT-átjáró használata előtt létre kell hozni az erőforráscsoportot és a virtuális hálózatot.

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | USA 2. keleti régiója      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Forrás virtuális gép létrehozása

Most létrehozunk egy virtuális gépet a NAT szolgáltatás használatához. Ez a virtuális gép egy nyilvános IP-címmel rendelkezik, amely egy példány szintű nyilvános IP-címet használ, hogy lehetővé tegye a virtuális gép elérését. A NAT szolgáltatás a flow irányának ismerete, és az alapértelmezett internetes célhelyet váltja fel az alhálózaton. A virtuális gép nyilvános IP-címe nem használható kimenő kapcsolatokhoz.

A NAT-átjáró teszteléséhez rendeljen egy nyilvános IP-cím típusú erőforrást egy példány szintű nyilvános IP-címhez, hogy a virtuális gép kívülről is hozzáférhessen. Ez a címe csak a teszthez való hozzáféréshez használatos.  Bemutatjuk, hogy a NAT szolgáltatás milyen elsőbbséget élvez a többi kimenő lehetőséggel szemben.

Azt is megteheti, hogy nyilvános IP-cím nélkül hozza létre ezt a virtuális gépet, és egy olyan virtuális gépet hoz létre, amelyet Jumpbox használ nyilvános IP-cím nélkül.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **Ubuntu Server 18,04 LTS**lehetőséget, vagy keressen rá az **Ubuntu Server 18,04 LTS** kifejezésre a piactéren.

2. A **virtuális gép létrehozása**területen adja meg vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myResourceGroupNAT**lehetőséget.
   - **Példány részletei**  >  **Virtuális gép neve**: adja meg a **myVMsource**.
   - **Példány részletei**  >  Régió > válassza az **USA 2. keleti** **régióját** .
   - **Rendszergazdai fiók**  >  **Hitelesítés megadása**: válassza a **jelszó**lehetőséget.
   - **Rendszergazdai fiók** > adja meg a **felhasználónevet**, a **jelszót**és a **Jelszó megerősítése** információt.
   - **Bejövő portok szabályai**  >  **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portok szabályai**  >  **Bejövő portok kiválasztása**: válassza az **SSH (22)** lehetőséget.
   - Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.

3. A **hálózatkezelés** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **myVnetsource**
   - **Alhálózat**: **mySubnetsource**
   - **Nyilvános IP-** > válassza az **új létrehozása**lehetőséget.  A **nyilvános IP-cím létrehozása** ablakban adja meg a **MyPublicIPsourceVM** **nevet a név** mezőben. Válassza a **standard** lehetőséget az **SKU**számára. Hagyja meg a többi értéket az alapértelmezett beállítások között, és kattintson az **OK**gombra.
   - **NIC hálózati biztonsági csoport**: válassza az **alapszintű**lehetőséget.
   - **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portok kiválasztása**: az **SSH** megerősítése lehetőség van kiválasztva.

4. A felügyelet **lap** **figyelés**területén a **rendszerindítási diagnosztika** beállítása **kikapcsolva**értékre.

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, és kattintson a **Létrehozás**gombra.

## <a name="create-the-nat-gateway"></a>NAT-átjáró létrehozása

Használhat egy vagy több nyilvános IP-cím-erőforrást, nyilvános IP-előtagot vagy mindkettőt a NAT-átjáróval. Hozzáadunk egy nyilvános IP-erőforrást, egy nyilvános IP-előtagot és egy NAT-átjáró erőforrást.

Ez a szakasz részletesen ismerteti, hogyan hozhatja létre és konfigurálhatja a NAT-szolgáltatás következő összetevőit a NAT-átjáró erőforrásának használatával:
  - Egy nyilvános IP-címkészlet és egy nyilvános IP-előtag, amelyet a NAT-átjáró erőforrása lefordított kimenő folyamatokhoz használ.
  - Módosítsa az üresjárati időkorlátot az alapértelmezett 4 perctől 10 percre.

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **hálózat**  >  **nyilvános IP-címe**lehetőséget, vagy keressen **nyilvános IP-címet** a piactér keresésében. 

2. A **nyilvános IP-cím létrehozása**lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | IP-verzió | Válassza az **IPv4** lehetőséget.
    | Termékváltozat | Válassza a **standard**lehetőséget.
    | Name | Adja meg a **myPublicIPsource**. |
    | Előfizetés | Válassza ki az előfizetését.|
    | Erőforráscsoport | Válassza a **myResourceGroupNAT**lehetőséget. |
    | Hely | Válassza az **USA 2. keleti régiója** lehetőséget.|

3. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Létrehozás**lehetőséget.

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **nyilvános IP-előtag**lehetőséget, vagy keressen **nyilvános IP-előtagot** a piactér keresésében.

2. A **nyilvános IP-előtag létrehozása**területen adja meg vagy válassza ki az alábbi értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: **myResourceGroupNAT** kiválasztása>
   - **Példány részletei**  >  **Név**: adja meg a **myPublicIPprefixsource**.
   - **Példány részletei**  >  Régió: válassza az **USA 2. keleti** **régiója**lehetőséget.
   - **Példány részletei**  >  **Előtag mérete**: Select **/31 (2 cím)**

3. Hagyja változatlanul az alapértelmezett beállításokat, majd válassza a **felülvizsgálat + létrehozás**lehetőséget.

4. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.


### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró erőforrásának létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **NAT-átjáró**lehetőséget, vagy keressen rá a **NAT-átjáróra** a piactér keresésében.

2. A **hálózati címfordítási (NAT-) átjáró létrehozása**területen adja meg vagy válassza ki az alábbi értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myResourceGroupNAT**lehetőséget.
   - **Példány részletei**  >  **NAT-átjáró neve**: adja meg a **myNATgateway**.
   - **Példány részletei**  >  Régió: válassza az **USA 2. keleti** **régiója**lehetőséget.
   - **Példány részletei**  >  **Üresjárati időkorlát (perc)**: adja meg a **10**értéket.
   - Válassza a **nyilvános IP-cím** lapot, vagy válassza a **Tovább: nyilvános IP-cím**elemet.

3. A **nyilvános IP-cím** lapon adja meg vagy válassza ki a következő értékeket:
   - **Nyilvános IP-címek**: válassza a **myPublicIPsource**lehetőséget.
   - **Nyilvános IP-előtagok**: válassza a **myPublicIPprefixsource**lehetőséget.
   - Válassza az **alhálózat** lapot, vagy válassza a **Tovább: alhálózat**elemet.

4. Az **alhálózat** lapon adja meg vagy válassza ki a következő értékeket:
   - **Virtual Network**: válassza a **myResourceGroupNAT**  >  **myVnetsource**elemet.
   - **Alhálózat neve**: válassza a **mySubnetsource**melletti jelölőnégyzetet.

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

A NAT szolgáltatás mostantól az összes internetes célhelyre irányuló kimenő forgalmat használja.  Nem szükséges konfigurálni a UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Cél előkészítése a kimenő forgalomhoz

Most létrehozunk egy célhelyet a NAT szolgáltatás által lefordított kimenő forgalomhoz, hogy tesztelni lehessen.


## <a name="virtual-network-and-parameters-for-destination"></a>A virtuális hálózat és a célhelyhez tartozó paraméterek

Mielőtt üzembe helyezi a virtuális GÉPET a célhelyen, létre kell hoznia egy virtuális hálózatot, amelyben a cél virtuális gép található. Az alábbi lépések ugyanazok, mint a forrás virtuális gép esetében, és néhány kisebb módosítással teszi elérhetővé a cél végpontot.

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | USA 2. keleti régiója      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Cél virtuális gép létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **Ubuntu Server 18,04 LTS**lehetőséget, vagy keressen rá az **Ubuntu Server 18,04 LTS** kifejezésre a piactéren.

2. A **virtuális gép létrehozása**területen adja meg vagy válassza ki a következő értékeket az **alapok** lapon:
   - **Előfizetés**  >  **Erőforráscsoport**: válassza a **myResourceGroupNAT**lehetőséget.
   - **Példány részletei**  >  **Virtuális gép neve**: adja meg a **myVMdestination**.
   - **Példány részletei**  >  Régió > válassza az **USA 2. keleti** **régióját** .
   - **Rendszergazdai fiók**  >  **Hitelesítés megadása**: válassza a **jelszó**lehetőséget.
   - **Rendszergazdai fiók** > adja meg a **felhasználónevet**, a **jelszót**és a **Jelszó megerősítése** információt.
   - **Bejövő portok szabályai**  >  **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portok szabályai**  >  **Bejövő portok kiválasztása**: válassza az **SSH (22)** és a **http (80)** lehetőséget.
   - Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés**lehetőséget.

3. A **hálózatkezelés** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **myVnetdestination**
   - **Alhálózat**: **mySubnetdestination**
   - **Nyilvános IP-** > válassza az **új létrehozása**lehetőséget.  A **nyilvános IP-cím létrehozása** ablakban adja meg a **MyPublicIPdestinationVM** **nevet a név** mezőben. Válassza a **standard** elemet az **SKU**számára. Hagyja meg a többi értéket az alapértelmezett beállítások között, és kattintson az **OK**gombra.
   - **NIC hálózati biztonsági csoport**: válassza az **alapszintű**lehetőséget.
   - **Nyilvános bejövő portok**: válassza a **kiválasztott portok engedélyezése**lehetőséget.
   - **Bejövő portok kiválasztása**: az **SSH** és a **http** megerősítése jelölőnégyzet be van jelölve.

4. A felügyelet **lap** **figyelés**területén a **rendszerindítási diagnosztika** beállítása **kikapcsolva**értékre.

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, majd kattintson a **Létrehozás**gombra.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló és tesztelési tartalom előkészítése a célként megadott virtuális gépen

Először fel kell deríteni a cél virtuális gép IP-címét. 

1. A portál bal oldalán válassza az **erőforráscsoportok**lehetőséget.
2. Válassza a **myResourceGroupNAT**lehetőséget.
3. Válassza a **myVMdestination**lehetőséget.
4. Az **Áttekintés**területen másolja a **nyilvános IP-cím** értéket, és illessze be a Jegyzettömbbe, így a virtuális gép eléréséhez használhatja azt.

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a cél virtuális gépre

Nyisson meg egy [Azure Cloud Shell](https://shell.azure.com) a böngészőben. Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Másolja ki és illessze be az alábbi parancsokat a bejelentkezés után.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

Ezek a parancsok frissítik a virtuális gépet, telepítik az Nginx-et, és létrehozunk egy 100-es kilobájtos fájlt. Ezt a fájlt a rendszer a forrás virtuális gépről a NAT szolgáltatás használatával kéri le.

A célként megadott virtuális géppel zárjuk be az SSH-munkamenetet.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először fel kell deríteni a forrás virtuális gép IP-címét.

1. A portál bal oldalán válassza az **erőforráscsoportok**lehetőséget.
2. Válassza a **myResourceGroupNAT**lehetőséget.
3. Válassza a **myVMsource**lehetőséget.
4. Az **Áttekintés**területen másolja a **nyilvános IP-cím** értéket, és illessze be a Jegyzettömbbe, így a virtuális gép eléréséhez használhatja azt.

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy Jegyzettömbbe, hogy azt a következő lépésekben használhatja. Jelezze, hogy ez a forrás virtuális gép.

### <a name="log-into-source-vm"></a>Bejelentkezés a forrás virtuális gépre

Nyisson meg egy új lapot a böngészőben [Azure Cloud Shellhoz](https://shell.azure.com) .  Használja az előző lépésben lekért IP-címet az SSH-val a virtuális gépre. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Másolja és illessze be a következő parancsokat a NAT szolgáltatás tesztelésének előkészítéséhez.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Ezzel a paranccsal frissítheti a virtuális gépet, telepítheti a go [-t, telepítheti a](https://github.com/rakyll/hey) githubról, és frissítheti a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT szolgáltatás ellenőrzése

Ha bejelentkezett a forrás virtuális gépre, a **curl** és a **Hey** használatával is létrehozhat KÉRÉSEKET a cél IP-címhez.

Az 100-kilobájtos fájl beolvasásához használja a curlt.  Cserélje le az **\<ip-address-destination>** alábbi példában a korábban másolt cél IP-címet.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

A **Hey**használatával több kérelem is létrehozható. Ismét cserélje le **\<ip-address-destination>** a helyére a korábban másolt cél IP-címet.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Ezzel a paranccsal az 100-es kérések, 10 párhuzamosan, 30 másodperces időkorlát és a TCP-kapcsolat újbóli használata nélkül hozhatók majd igénybe.  Minden kérelem 100 KB-ot fog beolvasni.  A Futtatás végén a **Hey** jelentést készít arról, hogy milyen jól működik a NAT szolgáltatás.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Válassza ki a NAT-átjárót tartalmazó erőforráscsoport- **myResourceGroupNAT** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozta a forrás és a cél virtuális gépet, majd tesztelte a NAT-átjárót.

Tekintse át a Azure Monitor mérőszámait a NAT szolgáltatás működésének megtekintéséhez. Problémák diagnosztizálása, például az elérhető SNAT-portok erőforrás-kimerülése.  A SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címek vagy nyilvános IP-előtag-erőforrások hozzáadásával vagy mindkettővel.

- Tudnivalók a [Virtual Network NAT](./nat-overview.md) -ról
- További információ a [NAT-átjáró erőforrásáról](./nat-gateway-resource.md).
- Rövid útmutató a [NAT-átjáró erőforrásának Azure CLI](./quickstart-create-nat-gateway-cli.md)-vel történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure PowerShell használatával](./quickstart-create-nat-gateway-powershell.md)történő üzembe helyezéséhez.
- Útmutató a NAT- [átjáró erőforrásának Azure Portal használatával](./quickstart-create-nat-gateway-portal.md)történő üzembe helyezéséhez.

> [!div class="nextstepaction"]

