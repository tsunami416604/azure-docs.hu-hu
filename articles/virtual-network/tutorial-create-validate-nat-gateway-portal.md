---
title: 'Oktatóanyag: NAT-átjáró létrehozása és tesztelése – Azure portal'
titlesuffix: Azure Virtual Network NAT
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre NAT-átjárót az Azure Portal használatával, és hogyan tesztelhatheti a NAT-szolgáltatást
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 7c26487018b9632b5780dcd7c56ab36f286f8cac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80060001"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Oktatóanyag: NAT-átjáró létrehozása az Azure Portalon, és a NAT-szolgáltatás tesztelése

Ebben az oktatóanyagban egy NAT-átjárót hoz létre, amely kimenő kapcsolatot biztosít az Azure-beli virtuális gépek számára. A NAT-átjáró teszteléséhez telepítsen egy forrás- és célvirtuális gépet. A NAT-átjáró tikkolni foghatja egy nyilvános IP-címhez való kimenő kapcsolat létesítésével a forrástól a cél virtuális gépig.  Ez az oktatóanyag csak az egyszerűség kedvéért telepíti a forrást és a célt két különböző virtuális hálózatba ugyanabban az erőforráscsoportban.

Ha szeretné, ezeket a lépéseket az [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) vagy az [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) használatával teheti meg a portál helyett.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="prepare-the-source-for-outbound-traffic"></a>A forrás előkészítése a kimenő forgalomra

Végigvezetjük a teljes tesztkörnyezet konfigurációjának és a tesztek végrehajtásának a következő lépésekben. Kezdjük a forrás, amely a nat átjáró erőforrást hozunk létre a későbbi lépésekben.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Mielőtt üzembe helyezne egy virtuális gépet, és használhatja a NAT-átjárót, létre kell hoznunk az erőforráscsoportot és a virtuális hálózatot.

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroupNAT |
| **\<virtuális hálózat neve>** | myVNetsource          |
| **\<régiónév>**          | USA 2. keleti régiója      |
| **\<IPv4-címtér>**   | 192.168.0.0\16          |
| **\<alhálózat-név>**          | mySubnetsource        |
| **\<alhálózati címtartomány>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Forrásvirtuális gép létrehozása

Most hozzon létre egy virtuális gép a NAT szolgáltatás használatához. Ez a virtuális gép egy nyilvános IP-cím ként használható példányszintű nyilvános IP-cím, amely lehetővé teszi a virtuális gép eléréséhez. A NAT-szolgáltatás áramlásirány-függő, és lecseréli az alhálózat alapértelmezett internetes célját. A virtuális gép nyilvános IP-címe nem lesz használva a kimenő kapcsolatokhoz.

A NAT-átjáró teszteléséhez egy nyilvános IP-címerőforrást rendelünk hozzá példányszintű nyilvános IP-címként a virtuális gép kívülről történő eléréséhez. Ez a cím csak a teszthez való hozzáféréshez használható.  Bemutatjuk, hogy a NAT szolgáltatás hogyan élvez elsőbbséget a többi kimenő beállítással szemben.

A virtuális gépet nyilvános IP-cím nélkül is létrehozhatja, és létrehozhat egy másik virtuális gépet, amelyet nyilvános IP-cím nélkül használhat gyakorlatként.

1. A portál bal felső részén válassza az Erőforrás létrehozása**Számítási** > **Ubuntu Server 18.04 LTS** **lehetőséget,** > vagy keressen az **Ubuntu Server 18.04 LTS elemre** a Marketplace-keresésben.

2. A **Virtuális gép létrehozása**csoportban írja be vagy jelölje ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupNAT lehetőséget.**
   - **Példány részletei** > **Virtuális gép neve**: írja **myVMsource**.
   - **A példány részletei** > **régió** > válassza az USA **keleti régióját 2**.
   - **Rendszergazdai fiók** > **hitelesítése írja be:** Válassza a **Jelszó**lehetőséget.
   - **Rendszergazdai fiók** > Adja meg a **Felhasználónevet**, **a Jelszót**és a **Jelszó megerősítése** adatokat.
   - **Bejövő portszabályok** > **Bejövő portok :** A kijelölt **portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok** > **Bejövő portok kiválasztása**: Válassza az **SSH (22)** lehetőséget
   - Válassza a **Hálózat** lapot, vagy válassza **a Tovább: Lemezek**, majd a **Tovább: Hálózat**lehetőséget.

3. A **Hálózat** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **myVnetsource**
   - **Alhálózat**: **mySubnetsource**
   - **Nyilvános IP >** Válassza **az Új létrehozása lehetőséget.**  A **Nyilvános IP-cím létrehozása** ablakban írja be a **myPublicIPsourceVM** értéket a **Név** mezőbe. Válassza a **Termékváltozat** **Standard** lehetőséget. Hagyja a többit az alapértelmezett értéken, és kattintson az **OK**gombra.
   - **Hálózati adapter hálózati biztonsági csoportja:** Válassza **az Alapszintű**lehetőséget.
   - **Nyilvános bejövő portok**: Válassza **a Kijelölt portok engedélyezése**lehetőséget.
   - **Bejövő portok kiválasztása**: Az **SSH** megerősítése van kiválasztva.

4. A **Kezelés** lap **Figyelés**csoportjában állítsa a **rendszerindítási diagnosztikát** **Ki**beállításra.

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, és kattintson a **Létrehozás gombra.**

## <a name="create-the-nat-gateway"></a>A NAT-átjáró létrehozása

Használhatja egy vagy több nyilvános IP-cím erőforrások, nyilvános IP-előtagok, vagy mindkettő nat átjáró. Hozzáadjuk a nyilvános IP-erőforrást, a nyilvános IP-előtagot és a NAT átjáró-erőforrást.

Ez a szakasz részletezi, hogyan hozhat létre és konfigurálhat a NAT szolgáltatás következő összetevőit a NAT átjáróerőforrás használatával:
  - Nyilvános IP-készlet és nyilvános IP-előtag a NAT átjáró erőforrás által lefordított kimenő folyamatokhoz.
  - Módosítsa az alapjárati időoutot az alapértelmezett 4 percről 10 percre.

### <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

1.  > A portál bal felső részén válassza az Erőforrás**létrehozása nyilvános** > **IP-cím** **lehetőséget,** vagy keressen **nyilvános IP-címet** a Piactér-keresésben. 

2. A **Nyilvános IP-cím létrehozása**mezőbe írja be vagy jelölje ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | IP-verzió | Válassza az **IPv4** lehetőséget.
    | SKU | Válassza a **Normál**lehetőséget.
    | Név | Adja meg **a myPublicIPsource értéket.** |
    | Előfizetés | Válassza ki előfizetését.|
    | Erőforráscsoport | Válassza a **myResourceGroupNAT lehetőséget.** |
    | Hely | Válassza az **USA 2. keleti régiója** lehetőséget.|

3. Hagyja meg a többi alapértelmezést, és válassza a **Létrehozás lehetőséget.**

### <a name="create-a-public-ip-prefix"></a>Nyilvános IP-előtag létrehozása

1. A portál bal felső részén válassza az **Erőforrás** > **hálózati** > **nyilvános IP-előtag**létrehozása lehetőséget, vagy keressen **nyilvános IP-előtagot** a Piactér-keresésben.

2. A **Nyilvános IP-előtag létrehozása**csoportban írja be vagy jelölje ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupNAT lehetőséget**>
   - **Példány adatai** > **Név**: adja meg **a myPublicIPprefixsource**.
   - **Példány részletei** > **Régió**: Válassza az USA **keleti régiója 2**lehetőséget.
   - **Példány** > **részleteielőtag mérete**: Select **/31 (2 címek)**

3. Hagyja a többit az alapértelmezett értékekre, és válassza a **Véleményezés + create lehetőséget.**

4. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**


### <a name="create-a-nat-gateway-resource"></a>NAT-átjáró-erőforrás létrehozása

1. **Networking** > A portál bal felső részén válassza az **Erőforrás** > hálózati**hálózati hálózati átjáró**létrehozása lehetőséget, vagy keressen **NAT-átjárót** a Piactéri keresésben.

2. A **Hálózati címfordítás (NAT) átjáró létrehozása**csoportban írja be vagy jelölje ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupNAT lehetőséget.**
   - **Példány részletei** > **NAT átjáró neve**: adja meg **myNATgateway**.
   - **Példány részletei** > **Régió**: Válassza az USA **keleti régiója 2**lehetőséget.
   - **A példány részletei** > **tétlen időhosszabbítás (perc):**: írja be a **10**értéket.
   - Válassza a **Nyilvános IP** lapot, vagy válassza **a Tovább: Nyilvános IP**lehetőséget.

3. A **Nyilvános IP** lapon adja meg vagy válassza ki a következő értékeket:
   - **Nyilvános IP-címek**: Válassza a **myPublicIPsource lehetőséget.**
   - **Nyilvános IP-előtagok**: Válassza a **myPublicIPprefixsource lehetőséget.**
   - Válassza az **Alhálózat** lapot, vagy válassza **a Tovább: Alhálózat**lehetőséget.

4. Az **Alhálózat** lapon adja meg vagy jelölje ki a következő értékeket:
   - **Virtuális hálózat**: Válassza a **myResourceGroupNAT** > **myVnetsource lehetőséget.**
   - **Alhálózat neve**: Jelölje ki a **mySubnetsource melletti jelölőnégyzetet.**

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**

Az internetes célállomásokra irányuló összes kimenő forgalom most már a NAT szolgáltatást használja.  Nem szükséges az UDR konfigurálása.


## <a name="prepare-destination-for-outbound-traffic"></a>Úti cél előkészítése a kimenő forgalomra

Most hozzon létre egy céla a kimenő forgalom által lefordított NAT szolgáltatás lehetővé teszi, hogy tesztelje azt.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtuális hálózat és a célparaméterei

Mielőtt üzembe helyezne egy virtuális gépet a célhoz, létre kell hoznunk egy virtuális hálózatot, ahol a cél virtuális gép található. Az alábbiakban ugyanazokat a lépéseket, mint a forrás virtuális gép néhány kisebb változás, hogy a célvégpont.

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroupNAT |
| **\<virtuális hálózat neve>** | myVNetdestination          |
| **\<régiónév>**          | USA 2. keleti régiója      |
| **\<IPv4-címtér>**   | 192.168.0.0\16          |
| **\<alhálózat-név>**          | mySubnetdestination        |
| **\<alhálózati címtartomány>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Célvirtuális gép létrehozása

1. A portál bal felső részén válassza az Erőforrás létrehozása**Számítási** > **Ubuntu Server 18.04 LTS** **lehetőséget,** > vagy keressen az **Ubuntu Server 18.04 LTS elemre** a Marketplace-keresésben.

2. A **Virtuális gép létrehozása**csoportban írja be vagy jelölje ki a következő értékeket az **Alapok** lapon:
   - **Előfizetési** > **erőforráscsoport**: Válassza a **myResourceGroupNAT lehetőséget.**
   - **Példány részletei** > **Virtuális gép neve**: adja **myVMdestination**.
   - **A példány részletei** > **régió** > válassza az USA **keleti régióját 2**.
   - **Rendszergazdai fiók** > **hitelesítése írja be:** Válassza a **Jelszó**lehetőséget.
   - **Rendszergazdai fiók** > Adja meg a **Felhasználónevet**, **a Jelszót**és a **Jelszó megerősítése** adatokat.
   - **Bejövő portszabályok** > **Bejövő portok :** A kijelölt **portok engedélyezése**lehetőséget.
   - **Bejövő portszabályok** > **Bejövő portok kiválasztása:** Válassza **az SSH (22)** és a HTTP **(80) lehetőséget.**
   - Válassza a **Hálózat** lapot, vagy válassza **a Tovább: Lemezek**, majd a **Tovább: Hálózat**lehetőséget.

3. A **Hálózat** lapon győződjön meg arról, hogy a következők vannak kiválasztva:
   - **Virtuális hálózat**: **myVnetdestination**
   - **Alhálózat**: **mySubnetdestination**
   - **Nyilvános IP >** Válassza **az Új létrehozása lehetőséget.**  A **Nyilvános IP-cím létrehozása** ablakban írja be a **myPublicIPdestinationVM** értéket a **Név** mezőbe. Válassza a **Termékváltozat** **szabványát**. Hagyja a többit az alapértelmezett értéken, és kattintson az **OK**gombra.
   - **Hálózati adapter hálózati biztonsági csoportja:** Válassza **az Alapszintű**lehetőséget.
   - **Nyilvános bejövő portok**: Válassza **a Kijelölt portok engedélyezése**lehetőséget.
   - **Bejövő portok kiválasztása**: Az **SSH** és a **HTTP** megerősítése van kiválasztva.

4. A **Kezelés** lap **Figyelés**csoportjában állítsa a **rendszerindítási diagnosztikát** **Ki**beállításra.

5. Válassza az **Áttekintés + létrehozás** lehetőséget.

6. Tekintse át a beállításokat, és válassza a **Létrehozás gombot.**

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Webkiszolgáló előkészítése és a hasznos adatszolgáltatás tesztelése a célvirtuális gépen

Először is fel kell derítenünk a cél virtuális gép IP-címét. 

1. A portál bal oldalán válassza az **Erőforráscsoportok**lehetőséget.
2. Válassza a **myResourceGroupNAT lehetőséget.**
3. Válassza ki **a myVMdestination lehetőséget.**
4. Az **Áttekintés alkalmazásban**másolja a Nyilvános **IP-cím** értékét, és illessze be a jegyzettömbbe, így a virtuális gép eléréséhez használhatja.

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, hogy a következő lépésekben használhassa. Azt jelzi, hogy ez a cél virtuális gép.

### <a name="sign-in-to-destination-vm"></a>Bejelentkezés a célvirtuális gépre

Nyisson meg egy [Azure Cloud Shellt](https://shell.azure.com) a böngészőjében. Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

A bejelentkezés után másolja és illessze be a következő parancsokat.  

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

Ezek a parancsok frissítik a virtuális gépet, telepítik a nginx-et, és létrehoznak egy 100 KBytes fájlt. Ez a fájl a NAT szolgáltatás használatával a forrásvirtuális gépről lesz beolvasva.

Zárja be az SSH-munkamenetet a cél virtuális géppel.

## <a name="prepare-test-on-source-vm"></a>Teszt előkészítése a forrás virtuális gépen

Először is meg kell, hogy felfedezzék az IP-címét a forrás virtuális gép.

1. A portál bal oldalán válassza az **Erőforráscsoportok**lehetőséget.
2. Válassza a **myResourceGroupNAT lehetőséget.**
3. Válassza a **myVMsource lehetőséget.**
4. Az **Áttekintés alkalmazásban**másolja a Nyilvános **IP-cím** értékét, és illessze be a jegyzettömbbe, így a virtuális gép eléréséhez használhatja.

>[!IMPORTANT]
>Másolja a nyilvános IP-címet, majd illessze be egy jegyzettömbbe, hogy a következő lépésekben használhassa. Azt jelzi, hogy ez a forrás virtuális gép.

### <a name="log-into-source-vm"></a>Bejelentkezés a forrás virtuális gépbe

Nyisson meg egy új lapot az [Azure Cloud Shell](https://shell.azure.com) böngészőjében.  Használja az előző lépésben beolvasott IP-címet az SSH-hoz a virtuális géphez. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Másolja és illessze be a következő parancsokat a NAT-szolgáltatás tesztelésének előkészítéséhez.

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

Ez a parancs frissíti a virtuális gépet, telepíti az ugrást, telepíti [a hé](https://github.com/rakyll/hey) a GitHubról, és frissíti a rendszerhéj-környezetet.

Most már készen áll a NAT szolgáltatás tesztelésére.

## <a name="validate-nat-service"></a>NAT-szolgáltatás ellenőrzése

A forrásvirtuális gépbe bejelentkezve a **curl** és **a hey** segítségével kérelmeket hozhat létre a cél IP-címére.

A göndörség segítségével olvassa be a 100 kbytes fájlt.  Cserélje ** \<** le az alábbi példában megadott ip-cím-cél>a korábban másolt cél IP-címre.  A **--output** paraméter azt jelzi, hogy a beolvasott fájl el lesz vetve.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Azt is létrehozhat egy sor kérelmet a **hey**. Ismét cserélje le ** \<az ip-address-destination>** a korábban másolt cél IP-címre.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Ez a parancs 100 kérelmet hoz létre, 10-et egyidejűleg, 30 másodperces időtúllépéssel, és a TCP-kapcsolat újbóli felhasználása nélkül.  Minden kérelem 100 kbyte-ot fog lekérni.  Végén a futás, **hé** jelentést néhány statisztikát arról, hogy milyen jól a NAT szolgáltatás nem.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a NAT-átjárót és az összes kapcsolódó erőforrást. Jelölje ki a NAT-átjárót tartalmazó **myResourceGroupNAT** erőforráscsoportot, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban létrehozott egy NAT-átjárót, létrehozott egy forrás- és célvirtuális gép, majd tesztelte a NAT-átjárót.

Tekintse át a metrikákat az Azure Monitorban a NAT-szolgáltatás működésének megtekintéséhez. Diagnosztizálja az olyan problémákat, mint például a rendelkezésre álló SNAT-portok erőforrás-kimerülése.  Az SNAT-portok erőforrás-kimerülése könnyen kezelhető további nyilvános IP-címerőforrások vagy nyilvános IP-előtag-erőforrások vagy mindkettő hozzáadásával.

- További információ a [virtuális hálózati hálózati hálózati kapcsolatról](./nat-overview.md)
- További információ a [NAT átjáró-erőforrásról.](./nat-gateway-resource.md)
- Rövid útmutató a [NAT átjáróerőforrás Azure CLI használatával történő](./quickstart-create-nat-gateway-cli.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT átjáróerőforrás Azure PowerShell használatával történő](./quickstart-create-nat-gateway-powershell.md)üzembe helyezéséhez.
- Rövid útmutató a [NAT-átjáró-erőforrás Azure Portalon való](./quickstart-create-nat-gateway-portal.md)telepítéséhez.

> [!div class="nextstepaction"]

