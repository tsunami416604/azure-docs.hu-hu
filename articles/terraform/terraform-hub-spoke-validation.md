---
title: Az Azure-ban a terraform egy küllős hálózati ellenőrzése
description: Küllős hálózati topológiák virtuális hálózatokkal ellenőrzése az oktatóanyagban egy másik csatlakozik.
services: terraform
ms.service: azure
keywords: terraform, hub and spoke, networks, hybrid networks, devops, virtual machine, azure,  vnet peering,
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 157be65a19a1f790b911aa9d861c5f18fc8c0813
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58006178"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Oktatóanyag: Az Azure-ban a terraform egy küllős hálózati ellenőrzése

Ebben a cikkben hajtsa végre az oktatóanyag-sorozatban az előző cikkben létrehozott terraform fájlokat. Ez a bemutató virtuális hálózatok közötti kapcsolat egy érvényesítése.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Küllős topológia implementálása az agyi virtuális hálózat HCL (HashiCorp Language) használata
> * Ellenőrizze az erőforrások üzembe helyezni a Terraform csomag használatával
> * Használja a Terraform alkalmazni az erőforrások létrehozása az Azure-ban
> * A különböző hálózatok közötti kapcsolat ellenőrzése
> * Szüntesse meg az összes erőforrást a Terraform használata

## <a name="prerequisites"></a>Előfeltételek

1. [Eseményközpont létrehozásához, küllős a hibrid hálózati topológiák az Azure-beli terraform](./terraform-hub-spoke-introduction.md).
1. [A helyszíni virtuális hálózat létrehozása az Azure-beli terraform](./terraform-hub-spoke-on-prem.md).
1. [Hub virtuális hálózat létrehozása az Azure-beli terraform](./terraform-hub-spoke-hub-network.md).
1. [A hub virtuális hálózati berendezés létrehozása terraform az Azure-ban](./terraform-hub-spoke-hub-nva.md).
1. [A terraform használatával Azure-beli virtuális hálózatok létrehozásához egy adott küllőre](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>A konfiguráció ellenőrzése

Befejezése után a [Előfeltételek](#prerequisites), győződjön meg arról, hogy jelen-e a megfelelő konfigurációs fájlok.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview). Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd hub-spoke
    ```

1. Futtassa a `ls` paranccsal ellenőrizheti, hogy a `.tf` az előző oktatóanyagok létrehozott konfigurációs fájlok találhatók:

    ![A Terraform bemutató konfigurációs fájlok](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Az erőforrások üzembe helyezése

1. A Terraform szolgáltató inicializálása:
    
    ```bash
    terraform init
    ```
    
    ![Példa eredmények "terraform init" parancs](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Futtassa a `terraform plan` paranccsal tekintheti meg az üzembe helyezés végrehajtása előtt a hatás:

    ```bash
    terraform plan
    ```
    
    ![Példa eredmények "terraform csomag" parancs](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. A megoldás üzembe helyezéséhez:

    ```bash
    terraform apply
    ```
    
    Adja meg `yes` az üzembe helyezés megerősítéséhez.

    ![Példa eredmények "terraform alkalmazás" parancs](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Tesztelje az agyi virtuális hálózat és a küllő virtuális hálózatok

Ez a szakasz bemutatja, hogyan tesztelheti a kapcsolat a szimulált helyszíni környezet és az agyi virtuális hálózat.

1. Az Azure Portalon keresse meg a **rendszert-vnet-rg** erőforráscsoportot.

1. Az a **rendszert-vnet-rg** lapra, válassza ki a virtuális gép nevű **rendszert virtuális**.

1. Kattintson a **Csatlakozás** gombra.

1. Szövege mellett **bejelentkezés a virtuális gép helyi fiókjával**, másolatot a **ssh** parancsot a vágólapra.

1. A Linux. Ehhez futtassa `ssh` csatlakozhat a szimulált helyszíni környezetet. A megadott jelszó használata a `on-prem.tf` alkalmazásparaméter-fájlt.

1. Futtassa a `ping` parancsot az agyi virtuális hálózat a jumpbox virtuális Géphez való kapcsolódás tesztelése:

   ```bash
   ping 10.0.0.68
   ```

1. Futtassa a `ping` parancsot minden egyes küllőben a jumpbox virtuális gépek kapcsolatának teszteléséhez:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Kilép az ssh-munkamenetet a a **rendszert virtuális** virtuális gépet, adja meg `exit` nyomja le az ENTER &lt;Enter >.

## <a name="troubleshoot-vpn-issues"></a>VPN-hibák elhárítása

VPN-hibák megoldása kapcsolatos információkért lásd: a cikk [egy hibrid VPN-kapcsolat hibaelhárítása](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az oktatóanyag-sorozatban létrehozott erőforrásokat.

1. A csomag deklarálva az erőforrások eltávolítása:

    ```bash
    terraform destroy
    ```

    Adja meg `yes` az erőforrások az eltávolítás megerősítéséhez.

1. Módosítsa a könyvtárakat a szülő könyvtár:

    ```bash
    cd ..
    ```

1. Törölje a `hub-scope` könyvtárat (például az összes ahhoz tartozó fájlokat):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Tudjon meg többet az Azure-ban a Terraform használatával](/azure/terraform)