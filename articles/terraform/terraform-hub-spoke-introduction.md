---
title: Sugaras hibrid hálózati topológia létrehozása az Azure-beli Terraform
description: Az oktatóanyag bemutatja, hogyan hozhat létre egy teljes hibrid hálózati hivatkozási architektúrát az Azure-ban a Terraform használatával
services: terraform
ms.service: azure
keywords: Terraform, hub és küllő, hálózatok, hibrid hálózatok, devops, virtuális gép, Azure, vnet-társítás, hálózati virtuális berendezés
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 5c2a61dd9da6d233a4b1410042f2125a1c300758
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173454"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Oktatóanyag: Sugaras hibrid hálózati topológia létrehozása az Azure-beli Terraform

Ez az oktatóanyag-Sorozat azt mutatja be, hogyan használható a Terraform az Azure-ban [sugaras hálózati topológia](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)megvalósítására. 

A sugaras topológia a számítási feladatok elkülönítésének módja a közös szolgáltatások megosztása során. Ezek a szolgáltatások identitást és biztonságot tartalmaznak. A hub egy virtuális hálózat (VNet), amely központi kapcsolódási pontként funkcionál egy helyszíni hálózat számára. A küllők az agyhoz kapcsolódó virtuális hálózatok. A megosztott szolgáltatások üzembe helyezése a központban történik, míg az egyes számítási feladatok a küllős hálózatokon belül vannak telepítve.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A HCL (HashiCorp Language) használatával állapítsa meg a hub és küllős hibrid hálózati referenciák architektúrájának erőforrásait
> * A Terraform használata a hub hálózati berendezés erőforrásainak létrehozásához
> * A Terraform használatával hozzon létre hub-hálózatot az Azure-ban, hogy az összes erőforráshoz közös pontként működjön
> * Egyéni számítási feladatok létrehozása a Terraform használatával küllős virtuális hálózatok az Azure-ban
> * Átjárók és kapcsolatok létrehozása a helyszíni és az Azure-hálózatok között a Terraform használatával
> * A Terraform használata VNet-társítások létrehozásához küllős hálózatokon

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha még nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

- **A Terraform telepítése és konfigurálása**: Virtuális gépek és egyéb infrastruktúra kiépítése az Azure-ban, [Terraform telepítése és konfigurálása](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Hub és küllős topológia architektúrája

A hub és a küllős topológiában a hub egy VNet. A VNet központi kapcsolódási pontként működik a helyszíni hálózathoz. A küllők az agyhoz kapcsolódó virtuális hálózatok, és a számítási feladatok elkülönítésére használhatók. A helyszíni adatközpont és az agy közötti forgalom egy ExpressRoute- vagy VPN-átjárókapcsolaton keresztül halad át. Az alábbi ábrán egy sugaras topológiában található összetevők láthatók:

![Sugaras topológiai architektúra az Azure-ban](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>A hub és a küllős topológia előnyei

A sugaras hálózati topológia lehetővé teszik a számítási feladatok elkülönítését a közös szolgáltatások megosztása során. Ezek a szolgáltatások identitást és biztonságot tartalmaznak. A hub egy VNet, amely központi kapcsolódási pontként funkcionál egy helyszíni hálózat számára. A küllők az agyhoz kapcsolódó virtuális hálózatok. A megosztott szolgáltatások üzembe helyezése a központban történik, míg az egyes számítási feladatok a küllős hálózatokon belül vannak telepítve. Íme néhány előny a hub és a küllő hálózati topológiához:

- Költségmegtakarítás a szolgáltatások központosításával egyetlen helyen, amely több munkaterheléssel is megosztható. Ezek a számítási feladatok közé tartoznak a hálózati virtuális berendezések és a DNS-kiszolgálók.
- **Az előfizetési korlátok leküzdése** a különböző előfizetésekből virtuális társhálózatok létesítésével a központi agyhoz.
- **Kockázatok elkülönítése** a központi IT (SecOps, InfraOps) és a számítási feladatok között (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>A hub és a küllő architektúra jellemző felhasználási módjai

A hub és a küllős architektúra tipikus felhasználási módjai a következők:

- Számos ügyfél különböző környezetekben üzembe helyezett munkaterhelésekkel rendelkezik. Ilyen környezetek például a fejlesztés, a tesztelés és a gyártás. Sokszor ezeknek a munkaterheléseknek olyan szolgáltatásokat kell megosztaniuk, mint a DNS, az AZONOSÍTÓk, az NTP vagy a AD DS. Ezek a megosztott szolgáltatások a hub VNet helyezhetők el. Így minden környezet üzembe helyezése egy küllőn történik, hogy fenntartsa az elkülönítést.
- Olyan munkaterhelések, amelyeknek nincs szükségük egymáshoz való kapcsolódásra, de a megosztott szolgáltatásokhoz való hozzáférésre van szükségük.
- A biztonsági szempontok központi felügyeletét igénylő vállalatok.
- Azok a vállalatok, amelyekhez elkülönített felügyelet szükséges a munkaterhelésekhez az egyes küllők esetében.

## <a name="preview-the-demo-components"></a>Bemutató-összetevők előzetes verziójának megtekintése

A sorozat egyes oktatóanyagai során a különböző összetevők külön Terraform-parancsfájlokban vannak meghatározva. A létrehozott és üzembe helyezett bemutató architektúra a következő összetevőkből áll:

- **Helyszíni hálózat**. Egy szervezettel működő helyi hálózat. A hub és a küllős hivatkozási architektúra esetében az Azure-beli VNet egy helyszíni hálózat szimulálására szolgál.

- **VPN-eszköz**. A VPN-eszköz vagy-szolgáltatás külső kapcsolatot biztosít a helyszíni hálózattal. A VPN-eszköz lehet hardveres készülék vagy szoftveres megoldás. 

- **Agyi virtuális hálózat**. A központ a helyszíni hálózathoz való kapcsolódás központi pontja, valamint a szolgáltatások üzemeltetésének helye. Ezeket a szolgáltatásokat a küllő virtuális hálózatok tárolt különböző számítási feladatok felhasználhatják.

- **Átjáró-alhálózat**. A VNet-átjárók ugyanabban az alhálózatban vannak tárolva.

- **Küllő virtuális hálózatok**. A küllőkkel elszigetelhetőek a számítási feladatok saját virtuális hálózataikban, így más küllőktől elkülönülten kezelhetőek. Minden számítási feladat több szintet tartalmazhat, amelyek alhálózatait Azure-terheléselosztók kapcsolják össze. 

- **Virtuális társhálózatok létesítése**. A két virtuális hálózatok egy társ-létesítési kapcsolat használatával csatlakoztatható. A társviszonykapcsolatok a virtuális hálózatok közötti nem tranzitív, alacsony késleltetésű kapcsolatok. Miután összeállította a virtuális hálózatok, az Azure-gerincen keresztül cseréli át a forgalmat, anélkül, hogy útválasztót kellene használnia. Egy sugaras hálózati topológiában a VNet-társítás a hub minden küllőhöz való összekapcsolására szolgál. A társ-virtuális hálózatok ugyanabban a régióban vagy különböző régiókban lehet.

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

Hozza létre azt a könyvtárat, amely a Terraform konfigurációs fájljait tartalmazza a bemutatóhoz.

1. Keresse fel az [Azure Portalt](https://portal.azure.com).

1. Nyissa meg az [Azure Cloud Shellt](/azure/cloud-shell/overview). Ha még nem választott ki környezetet, válassza a **Bash** környezetet.

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

1. A Cloud Shellban nyisson meg egy nevű `main.tf`új fájlt.

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

## <a name="create-the-variables-file"></a>A változók fájljának létrehozása

Hozza létre a Terraform konfigurációs fájlját a különböző parancsfájlokban használt általános változókhoz.

1. A Cloud Shellban nyisson meg egy nevű `variables.tf`új fájlt.

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
> [Helyszíni virtuális hálózat létrehozása az Azure-beli Terraform](./terraform-hub-spoke-on-prem.md)
