---
title: Oktatóanyag – az Azure Spring Cloud üzembe helyezése a Virtual Network szolgáltatásban
description: Azure Spring Cloud üzembe helyezése virtuális hálózaton (v-net Injection).
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/21/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1e16c984e48c11961dba0c977d3bdbddbd6bdf36
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400319"
---
# <a name="tutorial-deploy-azure-spring-cloud-in-azure-virtual-network-vnet-injection"></a>Oktatóanyag: az Azure Spring Cloud üzembe helyezése az Azure Virtual Networkben (VNet Injection)

**Ez a cikk a következőkre vonatkozik:** ✔️ Java ✔️ C #

Ez az oktatóanyag azt ismerteti, hogyan helyezhet üzembe egy Azure Spring Cloud Service-példányt a virtuális hálózaton. Ezt néha VNet injekciónak is nevezzük.  

Az üzembe helyezés a következőket teszi lehetővé:

* Az Azure Spring Cloud apps és a Service Runtime elkülönítése az internetről a vállalati hálózaton
* Azure Spring Cloud-interakció a helyszíni adatközpontokban és/vagy más virtuális hálózatokban lévő Azure-szolgáltatásokban található rendszerekkel
* Az Azure Spring Cloud bejövő és kimenő hálózati kommunikációjának szabályozása az ügyfelek számára

## <a name="prerequisites"></a>Előfeltételek
Regisztrálnia kell az Azure Spring Cloud erőforrás-szolgáltatót a *Microsoft. AppPlatform* és a *Microsoft. tárolószolgáltatás* utasítás szerint az [erőforrás-szolgáltató regisztrálása a Azure Portal](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) vagy a következő az CLI-parancs futtatásával:

```azurecli
az provider register --namespace Microsoft.AppPlatform
az provider register --namespace Microsoft.ContainerService
```
## <a name="virtual-network-requirements"></a>Virtuális hálózati követelmények
Az Azure Spring Cloud Service-példány üzembe helyezéséhez használt virtuális hálózatnak az alábbi követelményeknek kell megfelelnie:

* **Hely**: a virtuális hálózatnak ugyanazon a helyen kell lennie, mint az Azure Spring Cloud Service-példánynak.
* **Előfizetés**: a virtuális hálózatnak ugyanabban az előfizetésben kell lennie, mint az Azure Spring Cloud Service-példánnyal.
* **Alhálózatok**: a virtuális hálózatnak két, Azure Spring Cloud Service-példányhoz dedikált alhálózatot kell tartalmaznia: 
    * Egy a Service Runtime számára
    * Az egyik a Spring boot Service-alkalmazásaihoz. 
    * Az alhálózatok és az Azure Spring Cloud Service-példányok között egy-az-egyhez kapcsolat áll fenn. Minden egyes telepített szolgáltatási példányhoz új alhálózatot kell használnia, és mindegyik alhálózat csak egyetlen szolgáltatási példányt tartalmazhat.
* **Címterület**: a CIDR akár **/28** -ot is letilt a szolgáltatás futásidejű alhálózata és a Spring boot Service-alhálózati alkalmazások alhálózata esetében.
* **Útválasztási táblázat**: az alhálózatok nem rendelkezhetnek meglévő útválasztási táblázattal.

Az alábbi eljárások ismertetik a virtuális hálózat telepítését, amely az Azure Spring Cloud példányát tartalmazza.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása
Ha már rendelkezik virtuális hálózattal az Azure Spring Cloud Service-példány üzemeltetéséhez, ugorjon az 1., 2. és 3. lépésre. A 4. lépésből elkezdheti a virtuális hálózat alhálózatának előkészítését.

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet. Az Azure Marketplace-en válassza a **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.

1. A **virtuális hálózat létrehozása** párbeszédpanelen adja meg vagy válassza ki a következő adatokat:

    |Beállítás          |Érték                                             |
    |-----------------|--------------------------------------------------|
    |Előfizetés     |Válassza ki előfizetését.                         |
    |Erőforráscsoport   |Válassza ki az erőforráscsoportot, vagy hozzon létre egy újat.  |
    |Name             |Adja meg az *Azure-Spring-Cloud-vnet*                   |
    |Hely         |Válassza ki az **USA keleti** régióját                                |

1. Kattintson a **Tovább gombra: IP-címek >**. 
 
1. IPv4-címterület esetén írja be a következőt: 10.1.0.0/16.

1. Válassza az **alhálózat hozzáadása** lehetőséget, majd adja meg a *Service-Runtime-alhálózatot* az alhálózat **neve** és a 10.1.0.0/28 esetében az **alhálózat-címtartomány** mezőben. Ezután kattintson az **Add** (Hozzáadás) gombra.

1. Válassza az **alhálózat hozzáadása** újra lehetőséget, majd adja meg az **alhálózat nevét** és az **alhálózati címtartományt**, például: *alkalmazások – alhálózat* és 10.1.1.0/28.  Kattintson a **Hozzáadás** parancsra.

1. Kattintson a **Felülvizsgálat + létrehozás** elemre. Hagyja a többi értéket alapértelmezettként, majd kattintson a **Létrehozás** gombra.

## <a name="grant-service-permission-to-the-virtual-network"></a>Szolgáltatás engedélyének megadása a virtuális hálózat számára

Válassza ki a korábban létrehozott *Azure-Spring-Cloud-vnet* virtuális hálózatot.

1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget, majd válassza a **Hozzáadás > szerepkör-hozzárendelés hozzáadása** elemet.

    ![A v-net hozzáférés-vezérlése](./media/spring-cloud-v-net-injection/access-control.png)

2. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen adja meg vagy válassza ki az alábbi adatokat:

    |Beállítás  |Érték                                             |
    |---------|--------------------------------------------------|
    |Szerepkör     |**Tulajdonos** kiválasztása                                  |
    |Válassza ezt:   |Adja meg az *Azure Spring Cloud erőforrás-szolgáltatót*      |

    Ezután válassza az *Azure Spring Cloud erőforrás-szolgáltató* elemet, majd kattintson a **Mentés** gombra.

    ![Azure Spring Cloud erőforrás-szolgáltató engedélyezése a-net-hez](./media/spring-cloud-v-net-injection/grant-azure-spring-cloud-resource-provider-to-vnet.png)

Ezt a következő parancs futtatásával is elvégezheti:

```azurecli
VIRTUAL_NETWORK_RESOURCE_ID=`az network vnet show \
    --name ${NAME_OF_VIRTUAL_NETWORK} \
    --resource-group ${RESOURCE_GROUP_OF_VIRTUAL_NETWORK} \
    --query "id" \
    --output tsv`

az role assignment create \
    --role "Owner" \
    --scope ${VIRTUAL_NETWORK_RESOURCE_ID} \
    --assignee e8de9221-a19c-4c81-b814-fd37c6caf9d2
```

## <a name="deploy-azure-spring-cloud-service-instance-in-the-virtual-network"></a>Azure Spring Cloud Service-példány üzembe helyezése a virtuális hálózaton

1. Nyissa meg a Azure Portalt a következő használatával: https://portal.azure.com .

1. A felső keresőmezőbe keressen az **Azure Spring Cloud** kifejezésre, és válassza az **Azure Spring Cloud** lehetőséget az eredményből.

1. Az **Azure Spring Cloud** oldalon válassza a **+ Hozzáadás** lehetőséget.

1. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon. 

1. Válassza ki ugyanazt az erőforráscsoportot és régiót, mint a virtuális hálózatot.

1. A  **szolgáltatás részletei** területen válassza az *Azure-Spring-Cloud-vnet* lehetőséget.

1. Válassza a **hálózatkezelés** fület, és válassza ki a következőket:

    |Beállítás                                |Érték                                             |
    |---------------------------------------|--------------------------------------------------|
    |Üzembe helyezés saját virtuális hálózaton     |Válassza az **Igen** lehetőséget.                                    |
    |Virtuális hálózat                        |*Azure-Spring-Cloud-vnet* kiválasztása                  |
    |Szolgáltatás futásidejű alhálózata                 |*Service-Runtime-alhálózat* kiválasztása                   |
    |Spring boot Service-alhálózati alkalmazások alhálózata   |*Alkalmazások kiválasztása – alhálózat*                              |

    ![Létrehozási hálózatkezelés lap](./media/spring-cloud-v-net-injection/creation-blade-networking-tab.png)

1. Kattintson az **Áttekintés és létrehozás** elemre.

1. Ellenőrizze a specifikációkat, majd kattintson a **Létrehozás** gombra.

    ![A specifikációk ellenőrzése](./media/spring-cloud-v-net-injection/verify-specifications.png)

Az üzembe helyezést követően két további erőforráscsoport jön létre az előfizetésben az Azure Spring Cloud Service-példány hálózati erőforrásainak üzemeltetéséhez.  Navigáljon a **kezdőlapra** , majd válassza ki az **erőforráscsoportok** elemet a felső menüpontban a következő új erőforráscsoportok megkereséséhez.

A (z) *AP-SVC-rt_ {szolgáltatási példány neve} _ {szolgáltatás-példány terület}* nevű erőforráscsoport hálózati erőforrásokat tartalmaz a szolgáltatási futtatókörnyezethez.

  ![Szolgáltatás futtatókörnyezete](./media/spring-cloud-v-net-injection/service-runtime-resource-group.png)

Az *AP-App_ {szolgáltatási példány neve} _ {Service instance region}* nevű erőforráscsoport hálózati erőforrásokat tartalmaz a szolgáltatás példányának Spring boot Service-alkalmazásaihoz.

  ![Alkalmazások erőforráscsoport](./media/spring-cloud-v-net-injection/apps-resource-group.png)

Ezek a hálózati erőforrások a fent létrehozott virtuális hálózathoz csatlakoznak.

  ![V-net csatlakoztatott eszközzel](./media/spring-cloud-v-net-injection/vnet-with-connected-device.png)

   > [!Important]
   > Az erőforráscsoportok teljes mértékben az Azure Spring Cloud Service szolgáltatással kezelhetők. Ne törölje kézzel az erőforrást, vagy ne módosítsa a-t.

## <a name="limitations"></a>Korlátozások

A kis alhálózat-tartomány menti az IP-címeket, de korlátozásokat biztosít az Azure Spring Cloud által megtartható alkalmazás-példányok maximális számánál. 

| Alkalmazás-alhálózat CIDR | Összes IP-cím | Elérhető IP-címek | Alkalmazás-példányok maximális száma                                        |
| --------------- | --------- | ------------- | ------------------------------------------------------------ |
| /28             | 16        | 8             | <p> Alkalmazás 1 maggal: 96 <br/> Alkalmazás 2 maggal: 48<br/>  Alkalmazás 3 maggal: 32 <br/> Alkalmazás 4 maggal: 24 </p> |
| /27             | 32        | 24            | <p> Alkalmazás 1 maggal: 228<br/> Alkalmazás 2 maggal: 144<br/>  Alkalmazás 3 maggal: 96 <br/>  Alkalmazás 4 maggal: 72</p> |
| /26             | 64        | 56            | <p> Alkalmazás 1 maggal: 500<br/> Alkalmazás 2 maggal: 336<br/>  Alkalmazás 3 maggal: 224<br/>  Alkalmazás 4 maggal: 168</p> |
| /25             | 128       | 120           | <p> Alkalmazás 1 maggal: 500<br> Alkalmazás 2 maggal: 500<br>  Alkalmazás 3 maggal: 480<br>  Alkalmazás 4 maggal: 360</p> |
| /24             | 256       | 248           | <p> Alkalmazás 1 maggal: 500<br/> Alkalmazás 2 maggal: 500<br/>  Alkalmazás 3 maggal: 500<br/>  Alkalmazás 4 maggal: 500</p> |

Az alhálózatok esetében 5 IP-címet az Azure foglal le, és az Azure Spring Cloud legalább 4 címet igényel. Legalább 9 IP-címet kötelező megadni, így a/29 és/30 nem működőképes.

A szolgáltatás-futtatókörnyezeti alhálózat esetében a minimális méret a/28, és ez nem befolyásolja az alkalmazások példányainak számát.
## <a name="next-steps"></a>Következő lépések

[Alkalmazás üzembe helyezése az Azure Spring Cloud-ban a VNet](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/02-deploy-application-to-azure-spring-cloud-in-your-vnet.md)

## <a name="see-also"></a>Lásd még

- [Az Azure Spring Cloud hibaelhárítása a VNET-ben](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/05-troubleshooting-azure-spring-cloud-in-vnet.md)
- [Az Azure Spring Cloud VNET való futtatásának felhasználói feladatai](https://github.com/microsoft/vnet-in-azure-spring-cloud/blob/master/06-customer-responsibilities-for-running-azure-spring-cloud-in-vnet.md)
