---
title: Hub-és küllős hálózat ellenőrzése az Azure-beli Terraform
description: Oktatóanyag a hub és a küllős hálózati topológia ellenőrzéséhez az összes, egymáshoz csatlakoztatott virtuális hálózattal.
services: terraform
ms.service: azure
keywords: Terraform, hub és küllő, hálózatok, hibrid hálózatok, devops, virtuális gépek, Azure, vnet-társítás,
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: e35af0fcf4a8f1f8f0446be44fe5b0bb6eeec693
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169712"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Oktatóanyag: Hub-és küllős hálózat ellenőrzése az Azure-beli Terraform

Ebben a cikkben a sorozat előző cikkében létrehozott Terraform-fájlokat hajtja végre. Ennek eredményeképpen a bemutató virtuális hálózatok közötti kapcsolat érvényesítése történik.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A HCL (HashiCorp Language) használata a hub-VNet megvalósításához küllős topológiában
> * Terraform-csomag használata a telepítendő erőforrások ellenőrzéséhez
> * Erőforrások létrehozása az Azure-ban a Terraform alkalmazással
> * A különböző hálózatok közötti kapcsolat ellenőrzése
> * Az összes erőforrás megsemmisítése a Terraform használatával

## <a name="prerequisites"></a>Előfeltételek

1. [Hozzon létre egy sugaras hibrid hálózati topológiát az Azure-beli Terraform](./terraform-hub-spoke-introduction.md).
1. Helyszíni [virtuális hálózat létrehozása az Azure-beli Terraform](./terraform-hub-spoke-on-prem.md).
1. [Hozzon létre egy hub virtuális hálózatot a Terraform az Azure-ban](./terraform-hub-spoke-hub-network.md).
1. [Hozzon létre egy hub virtuális hálózati készüléket a Terraform az Azure-ban](./terraform-hub-spoke-hub-nva.md).
1. [Hozzon létre egy küllős virtuális hálózatot a Terraform az Azure-ban](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Konfiguráció ellenőrzése

Az [Előfeltételek](#prerequisites)teljesítése után ellenőrizze, hogy a megfelelő konfigurációs fájlok találhatók-e.

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

1. A parancs futtatásával ellenőrizze, hogy `.tf` az előző oktatóanyagokban létrehozott konfigurációs fájlok szerepelnek-e a listáján: `ls`

    ![Terraform bemutató konfigurációs fájljai](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Az erőforrások üzembe helyezése

1. A Terraform-szolgáltató inicializálása:
    
    ```bash
    terraform init
    ```
    
    ![Példa a "Terraform init" parancs eredményeire](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. A következő `terraform plan` parancs futtatásával tekintheti meg a telepítés hatását a végrehajtás előtt:

    ```bash
    terraform plan
    ```
    
    ![Példa a "Terraform Plan" parancs eredményeire](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. A megoldás üzembe helyezése:

    ```bash
    terraform apply
    ```
    
    `yes` Ha a rendszer kéri, hogy erősítse meg a telepítést.

    ![Példa a "Terraform Apply" parancs eredményeire](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>A hub VNet és küllős virtuális hálózatok tesztelése

Ez a szakasz bemutatja, hogyan tesztelheti a kapcsolatot a szimulált helyszíni környezet és a hub VNet között.

1. A Azure Portal keresse meg a **helyszíni-vnet-RG** erőforráscsoportot.

1. Az **helyszíni-vnet-RG** lapon válassza ki a **helyszíni-VM**nevű virtuális gépet.

1. Kattintson a **Csatlakozás** gombra.

1. A **virtuális gép helyi fiókja használatával**történő szövegbevitel mellett másolja az **SSH** -parancsot a vágólapra.

1. A Linux. Ehhez futtassa `ssh` csatlakozhat a szimulált helyszíni környezetet. Használja a `on-prem.tf` paraméter fájlban megadott jelszót.

1. Futtassa a `ping` parancsot a Jumpbox virtuális géphez való kapcsolat teszteléséhez a hub VNet:

   ```bash
   ping 10.0.0.68
   ```

1. Futtassa a `ping` parancsot a Jumpbox virtuális gépekkel való kapcsolat teszteléséhez minden egyes küllőn:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Az SSH-munkamenetből a **helyszíni-VM** virtuális gépen való kilépéshez írja &lt;be `exit` a parancsot, majd nyomja le az ENTER billentyűt >.

## <a name="troubleshoot-vpn-issues"></a>VPN-problémák elhárítása

A VPN-hibák megoldásával kapcsolatos további információkért tekintse meg a [hibrid VPN-kapcsolat hibaelhárítása](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn)című cikket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az oktatóanyag-sorozatban létrehozott erőforrásokat.

1. Távolítsa el a tervben deklarált erőforrásokat:

    ```bash
    terraform destroy
    ```

    Ha `yes` a rendszer felszólítja az erőforrások eltávolításának megerősítésére, írja be a következőt:.

1. Könyvtárak módosítása a szülő könyvtárba:

    ```bash
    cd ..
    ```

1. Törölje a `hub-scope` könyvtárat (beleértve az összes fájlját):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform Azure-beli használatáról](/azure/terraform)