---
title: Oktatóanyag – Hub és küllős hibrid hálózati topoológia létrehozása az Azure-ban a Terraform használatával
description: Oktatóanyag, amely bemutatja, hogyan hozhat létre egy teljes hibrid hálózati referenciaarchitektúrát az Azure-ban a Terraform használatával
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77472179"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Oktatóanyag: Hozzon létre egy hub ot és a küllős hibrid hálózati topológiát az Azure-ban a Terraform használatával

Ez az oktatóanyag-sorozat bemutatja, hogyan valósítható meg a Terraform az Azure-ban egy [központi és küllős hálózati topológiában.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 

A hub és a küllős topológia a számítási feladatok elkülönítésének egyik módja a közös szolgáltatások megosztása közben. Ezek a szolgáltatások közé tartozik az identitás és a biztonság. A hub egy virtuális hálózat (VNet), amely központi csatlakozási pontként működik egy helyszíni hálózathoz. A küllők az agyhoz kapcsolódó virtuális hálózatok. A megosztott szolgáltatások a hubon vannak telepítve, míg az egyes munkaterhelések a küllős hálózatokon belül vannak telepítve.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * HCL (HashiCorp Language) használatával hub és küllős hibrid hálózati referenciaarchitektúra-erőforrásokat hoz létre
> * Hub hálózati berendezés erőforrásainak létrehozása a Terraform segítségével
> * A Terraform segítségével hubhálózatot hozhat létre az Azure-ban, hogy az összes erőforrás közös pontjaként működjön
> * A Terraform segítségével egyedi számítási feladatokat hozhat létre küllővirtuális hálózatként az Azure-ban
> * A Terraform segítségével átjárókés kapcsolatok létesítése a helyszínen és az Azure-hálózatok között
> * A Terraform segítségével virtuális kapcsolatlétesítések létrehozása küllős hálózatokhoz

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés:** Ha még nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiókot,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) mielőtt elkezdené.

- **Terraform telepítése és konfigurálása**: Virtuális gépek és egyéb infrastruktúra kiépítése az Azure-ban, [a Terraform telepítése és konfigurálása](terraform-install-configure.md)

## <a name="hub-and-spoke-topology-architecture"></a>Hub és küllős topológia architektúra

A hub és a küllős topológia, a hub egy virtuális hálózat. A virtuális hálózat a helyszíni hálózattal való kapcsolat központi pontjaként működik. A küllők az agyhoz kapcsolódó virtuális hálózatok, és a számítási feladatok elkülönítésére használhatók. A helyszíni adatközpont és az agy közötti forgalom egy ExpressRoute- vagy VPN-átjárókapcsolaton keresztül halad át. Az alábbi képen a hub és a küllős topológia összetevői láthatók:

![Hub és küllős topológia architektúra az Azure-ban](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>A hub és a küllős topológia előnyei

A hub és a küllős hálózati topológia a gyakori szolgáltatások megosztása során a számítási feladatok elkülönítésének egyik módja. Ezek a szolgáltatások közé tartozik az identitás és a biztonság. A hub egy virtuális hálózat, amely központi csatlakozási pontként működik egy helyszíni hálózathoz. A küllők az agyhoz kapcsolódó virtuális hálózatok. A megosztott szolgáltatások a hubon vannak telepítve, míg az egyes munkaterhelések a küllős hálózatokon belül vannak telepítve. Íme néhány előnye a hub és a küllős hálózati topológia:

- **Költségmegtakarítás** a szolgáltatások egyetlen helyen történő központosításával, amelyet több számítási feladat is megoszthat. Ezek a számítási feladatok közé tartoznak a hálózati virtuális készülékek és a DNS-kiszolgálók.
- **Az előfizetési korlátok leküzdése** a különböző előfizetésekből virtuális társhálózatok létesítésével a központi agyhoz.
- **Kockázatok elkülönítése** a központi IT (SecOps, InfraOps) és a számítási feladatok között (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>A hub és a küllős architektúra tipikus felhasználási

A hub és a küllős architektúra tipikus felhasználási elemei a következők:

- Sok ügyfél különböző környezetekben üzembe helyezett munkaterhelésekkel rendelkezik. Ezek a környezetek közé tartozik a fejlesztés, tesztelés és az éles környezet. Ezek a számítási feladatok sokszor meg kell osztania a szolgáltatásokat, például a DNS, IDS, NTP vagy AD DS. Ezek a megosztott szolgáltatások a központi virtuális hálózatban helyezhetők el. Így minden környezet egy küllőre van telepítve az elkülönítés fenntartása érdekében.
- Olyan számítási feladatok, amelyek nem igényelnek kapcsolatot egymással, de hozzáférést igényelnek a megosztott szolgáltatásokhoz.
- Azok a vállalatok, amelyek a biztonsági szempontok központi ellenőrzését igénylik.
- Olyan vállalatok, amelyek az egyes küllők munkaterheléseihez elkülönített felügyeletet igényelnek.

## <a name="preview-the-demo-components"></a>A bemutató összetevőinek előnézete

A sorozat egyes oktatóanyagai során a különböző összetevők különböző Terraform parancsfájlokban vannak definiálva. A létrehozott és üzembe helyezett bemutató architektúra a következő összetevőkből áll:

- **A helyszíni hálózathoz.** Egy szervezettel működő magánhelyi hálózat. A hub és a küllős referencia architektúra, az Azure-ban egy virtuális hálózat szimulálására egy helyszíni hálózat.

- **VPN-eszköz**. A VPN-eszköz vagy szolgáltatás külső kapcsolatot biztosít a helyszíni hálózathoz. A VPN-eszköz lehet hardvereszköz vagy szoftvermegoldás. 

- **Agyi virtuális hálózat**. A hub a helyszíni hálózattal való kapcsolat központi pontja, valamint a szolgáltatások üzemeltetésének helye. Ezeket a szolgáltatásokat a küllővirtuális hálózatokban üzemeltetett különböző számítási feladatok is fellehet használni.

- **Átjáró-alhálózat**. A virtuális hálózat átjárói ugyanabban az alhálózatban vannak elrendezve.

- **Küllő virtuális hálózatok**. A küllőkkel elszigetelhetőek a számítási feladatok saját virtuális hálózataikban, így más küllőktől elkülönülten kezelhetőek. Minden számítási feladat több szintet tartalmazhat, amelyek alhálózatait Azure-terheléselosztók kapcsolják össze. 

- **Virtuális hálózat társviszony-létesítése**. Két virtuális hálózat társviszony-létesítési kapcsolaton keresztül csatlakoztatható. A társviszonykapcsolatok a virtuális hálózatok közötti nem tranzitív, alacsony késleltetésű kapcsolatok. A társviszony-létesítés t követően a virtuális hálózatok az Azure gerinchálózatának használatával cserélnek forgalmat, anélkül, hogy útválasztóra lenne szükség. A hub és a küllős hálózati topológia, virtuális társviszony-létesítés segítségével csatlakoztassa a hub minden küllők. Társhálózati társhálózatok ugyanabban a régióban, vagy különböző régiókban.

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

Hozza létre azt a könyvtárat, amely a Terraform konfigurációs fájljait tartalmazza a bemutatóhoz.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg [az Azure Cloud Shell](/azure/cloud-shell/overview)t. Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

    ![Cloud Shell-parancssor](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Lépjen be a `clouddrive` könyvtárba.

    ```bash
    cd clouddrive
    ```

1. Hozzon létre egy `hub-spoke` nevű könyvtárat.

    ```bash
    mkdir hub-spoke
    ```

1. Lépjen be az új könyvtárba:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Az Azure-szolgáltató deklarálása

Hozza létre az Azure-szolgáltatót deklaráló Terraform konfigurációs fájlt.

1. A Cloud Shellben nyisson `main.tf`meg egy új nevű fájlt.

    ```bash
    code main.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Mentse a fájlt, és zárja be a szerkesztőt.

## <a name="create-the-variables-file"></a>A változófájl létrehozása

Hozza létre a Terraform konfigurációs fájlt a különböző parancsfájlok között használt gyakori változókhoz.

1. A Cloud Shellben nyisson `variables.tf`meg egy új nevű fájlt.

    ```bash
    code variables.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Mentse a fájlt, és zárja be a szerkesztőt.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Helyszíni virtuális hálózat létrehozása a Terraform segítségével az Azure-ban](./terraform-hub-spoke-on-prem.md)