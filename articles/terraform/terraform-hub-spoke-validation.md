---
title: Oktatóanyag – Hub és küllős hálózat ellenőrzése az Azure-ban a Terraform használatával
description: Oktatóanyag a hub és a küllős hálózati topológia érvényesítéséhez az összes egymáshoz csatlakoztatott virtuális hálózattal.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159204"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Oktatóanyag: Hub és küllős hálózat ellenőrzése az Azure-ban a Terraform használatával

Ebben a cikkben a sorozat előző cikkében létrehozott terraform-fájlokat hajtja végre. Az eredmény a demo virtuális hálózatok közötti kapcsolat ellenőrzése.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * HCL (HashiCorp language) használata a Hub Virtuális hálózat hubküllős topológiában való megvalósításához
> * A Terraform terv használata a telepítendő erőforrások ellenőrzéséhez
> * A Terraform alkalmazás sal hozza létre az erőforrásokat az Azure-ban
> * A különböző hálózatok közötti kapcsolat ellenőrzése
> * Használja terraform, hogy elpusztítsa az összes erőforrást

## <a name="prerequisites"></a>Előfeltételek

1. [Hozzon létre egy hub ot és a küllős hibrid hálózati topológiát a Terraform segítségével az Azure-ban.](./terraform-hub-spoke-introduction.md)
1. [Hozzon létre helyszíni virtuális hálózatot a Terraform segítségével az Azure-ban.](./terraform-hub-spoke-on-prem.md)
1. [Hozzon létre egy központi virtuális hálózatot a Terraform segítségével az Azure-ban.](./terraform-hub-spoke-hub-network.md)
1. [Hozzon létre egy központi virtuális hálózati berendezést a Terraform segítségével az Azure-ban.](./terraform-hub-spoke-hub-nva.md)
1. [Hozzon létre egy küllővirtuális hálózatokat a Terraform segítségével az Azure-ban.](./terraform-hub-spoke-spoke-network.md)

## <a name="verify-your-configuration"></a>A konfiguráció ellenőrzése

Az [előfeltételek](#prerequisites)befejezése után ellenőrizze, hogy vannak-e jelen a megfelelő konfigurációs fájlok.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg [az Azure Cloud Shell](/azure/cloud-shell/overview)t. Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd hub-spoke
    ```

1. Futtassa a `ls` parancsot annak ellenőrzéséhez, hogy az `.tf` előző oktatóanyagokban létrehozott konfigurációs fájlok szerepelnek-e a listában:

    ![Terraform demo konfigurációs fájlok](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Az erőforrások üzembe helyezése

1. A Terraform szolgáltató inicializálása:
    
    ```bash
    terraform init
    ```
    
    ![Példa a "terraform init" parancs eredményeire](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Futtassa a `terraform plan` parancsot a telepítés végrehajtásának megtekintéséhez:

    ```bash
    terraform plan
    ```
    
    ![Példa a "terraform plan" parancs eredményeire](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. A megoldás üzembe helyezése:

    ```bash
    terraform apply
    ```
    
    Adja `yes` meg, amikor a rendszer kéri a telepítés megerősítését.

    ![Példa a "terraform apply" parancs eredményeire](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>A központi virtuális hálózat és a küllővirtuális hálózatok tesztelése

Ez a szakasz bemutatja, hogyan tesztelhet kapcsolatot a szimulált helyszíni környezetből a központi virtuális hálózatra.

1. Az Azure Portalon keresse meg az **onprem-vnet-rg** erőforráscsoportot.

1. Az **onprem-vnet-rg** lapon válassza ki az **onprem-vm**nevű virtuális gépét.

1. Kattintson a **Csatlakozás** gombra.

1. A Virtuális **gép helyi fiókjával történő bejelentkezés**című szöveg mellett másolja az **ssh** parancsot a vágólapra.

1. Egy Linux-parancssorból futtassa `ssh` a szimulált helyszíni környezethez való csatlakozáshoz. Használja a `on-prem.tf` paraméterfájlban megadott jelszót.

1. Futtassa a parancsot a `ping` hub virtuális hálózatában lévő jumpbox virtuális géphez való kapcsolódás teszteléséhez:

   ```bash
   ping 10.0.0.68
   ```

1. Futtassa a parancsot az `ping` egyes küllőkben lévő jumpbox virtuális gépekhez való kapcsolódás teszteléséhez:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Az **onprem-vm** virtuális gépen az ssh `exit` munkamenetből &lt;való kilépéshez írja be és nyomja le az Enter>.

## <a name="troubleshoot-vpn-issues"></a>VPN-problémák elhárítása

A VPN-hibák elhárításáról a [Hibrid VPN-kapcsolat – problémaelkövetés című](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn)cikkben olvashat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az oktatóanyag-sorozatban létrehozott erőforrásokat.

1. Távolítsa el a tervben deklarált erőforrásokat:

    ```bash
    terraform destroy
    ```

    Adja `yes` meg, amikor a rendszer az erőforrások eltávolításának megerősítését kéri.

1. Könyvtárak módosítása szülőkönyvtárra:

    ```bash
    cd ..
    ```

1. Törölje `hub-scope` a könyvtárat (beleértve az összes fájlját):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [További információ a Terraform azure-beli használatáról](/azure/terraform)