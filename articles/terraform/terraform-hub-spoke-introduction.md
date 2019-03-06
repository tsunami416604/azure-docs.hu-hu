---
title: Eseményközpont létrehozásához, küllős a hibrid hálózati topológiák a terraform használatával Azure-ban
description: Az oktatóanyag egy teljes hibrid hálózatok referenciaarchitektúráját létrehozása az Azure-ban a Terraform ábrázoló
services: terraform
ms.service: terraform
keywords: terraform, hub and spoke, networks, hybrid networks, devops, virtual machine, azure,  vnet peering, network virtual appliance
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 25ba7016c10fc13357b403efae6e4996de59b624
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411501"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Oktatóanyag: Eseményközpont létrehozásához, küllős a hibrid hálózati topológiák a terraform használatával Azure-ban

Az oktatóanyag-sorozat bemutatja, hogyan megvalósítása az Azure-ban a Terraform használata egy [küllős hálózati topológiák](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Egy küllős topológiában módja a számítási feladatok elkülönítésére, miközben a közös szolgáltatások megosztása. Ezen szolgáltatások közé tartoznak az identitások és a biztonság. A hub egy virtuális hálózat (VNet), amely központi kapcsolódási pontként a helyszíni hálózathoz funkcionál. A küllők az agyhoz kapcsolódó virtuális hálózatok. Megosztott szolgáltatások az agyi vannak telepítve, miközben egyéni munkaterhelések topológiájú hálózatokat belül vannak telepítve.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Küllős topológiájú hibrid hálózati architektúra forrásanyagok elrendezéséhez HCL (HashiCorp Language) használata
> * Use Terraform to create hub network appliance resources
> * Eseményközpont-hálózat létrehozása az Azure-ban, hogy működjön, az összes erőforrás közös pontja a Terraform használata
> * Hozzon létre egyéni munkaterhelések küllő virtuális hálózatok az Azure-ban a Terraform használata
> * A Terraform használatával szeretne létrehozni, átjárók és helyszíni, Azure-hálózatok közötti kapcsolat
> * A Terraform használata topológiájú hálózatokat a virtuális hálózatok közötti társviszony létrehozása

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha még nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) megkezdése előtt.

- **Terraform telepítése és konfigurálása**: Virtuális gépek és más Azure-infrastruktúra kiépítése [Terraform telepítése és konfigurálása](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Küllős topológia architektúra

A küllős topológiában a hub egy virtuális hálózathoz. A virtuális hálózat a helyszíni hálózathoz való csatlakozási lehetőségek központi helye funkcionál. A küllők az agyhoz kapcsolódó virtuális hálózatok, és a számítási feladatok elkülönítésére használhatók. A helyszíni adatközpont és az agy közötti forgalom egy ExpressRoute- vagy VPN-átjárókapcsolaton keresztül halad át. Az alábbi képen láthatja az összetevők egy küllős topológiában:

![Küllős topológia architektúra az Azure-ban](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>A küllős topológia előnyei

A küllős hálózati topológia módja a számítási feladatok elkülönítésére, miközben a közös szolgáltatások megosztása. Ezen szolgáltatások közé tartoznak az identitások és a biztonság. A hub egy virtuális hálózathoz, amely központi kapcsolódási pontként a helyszíni hálózathoz funkcionál. A küllők az agyhoz kapcsolódó virtuális hálózatok. Megosztott szolgáltatások az agyi vannak telepítve, miközben egyéni munkaterhelések topológiájú hálózatokat belül vannak telepítve. Íme néhány a küllős hálózati topológia előnyei:

- **Költségmegtakarítás** központosítja azokat a szolgáltatásokat, amelyek több számítási feladat megoszthatók egy helyen. Ezek közé tartozik a virtuális hálózati berendezések és a DNS-kiszolgálók.
- **Az előfizetési korlátok leküzdése** a különböző előfizetésekből virtuális társhálózatok létesítésével a központi agyhoz.
- **Kockázatok elkülönítése** a központi IT (SecOps, InfraOps) és a számítási feladatok között (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>A küllős architektúra gyakori használati

Az egy küllős architektúra gyakori használati a következők:

- Számos ügyfelünk különböző környezetekben üzembe helyezett számítási feladatok. Ezekben a környezetekben például fejlesztési, tesztelési és éles környezetben. Sokszor ezeket a feladatokat kell például a DNS, Azonosítók, NTP vagy AD DS szolgáltatásokat. Ezek a megosztott szolgáltatások az agyi virtuális hálózat is kell elhelyezni. Ezzel a módszerrel egy adott küllőre elszigetelés minden környezetben van telepítve.
- Számítási feladatok nem igénylik az egymással való kapcsolatot, de a megosztott szolgáltatásokhoz való hozzáférés szükséges.
- Olyan vállalatok, amelyek a biztonsági szempontokhoz központi felügyeletet igényelnek.
- Olyan vállalatok, amelyek elkülönített felügyeleti igényel minden egyes küllőben a számítási feladatokhoz.

## <a name="preview-the-demo-components"></a>A bemutató összetevők előzetes verzió

Minden oktatóanyag-sorozat használata során különböző összetevők különböző Terraform parancsfájlok vannak definiálva. A létrehozott és telepített bemutató architektúra a következő összetevőkből áll:

- **Helyszíni hálózat**. Egy helyi magánhálózat és a egy szervezet fut. Küllős topológiájú referenciaarchitektúra az Azure-beli virtuális hálózat szimulálására szolgál egy a helyszíni hálózathoz.

- **VPN-eszköz**. Egy VPN-eszköz vagy szolgáltatás a helyszíni hálózathoz külső kapcsolatot biztosít. A VPN-eszköz egy hardver készülék vagy valamilyen szoftvermegoldás lehet. 

- **Agyi virtuális hálózat**. A központ az a központi pont a helyszíni hálózattal való kapcsolat és a egy helyen szolgáltatások működtetéséhez. Ezek a szolgáltatások a küllő virtuális hálózatok tárolt különféle számítási feladatok tudják használni.

- **Átjáró-alhálózat**. A virtuális hálózati átjárók ugyanazon az alhálózaton tartanak.

- **Küllő virtuális hálózatok**. A küllőkkel elszigetelhetőek a számítási feladatok saját virtuális hálózataikban, így más küllőktől elkülönülten kezelhetőek. Minden számítási feladat több szintet tartalmazhat, amelyek alhálózatait Azure-terheléselosztók kapcsolják össze. 

- **Virtuális társhálózatok létesítése**. Két virtuális hálózat között társviszony-létesítési kapcsolat segítségével csatlakoztathatók. A társviszonykapcsolatok a virtuális hálózatok közötti nem tranzitív, alacsony késleltetésű kapcsolatok. Társviszonyba a virtuális hálózatok váltanak forgalmat egymás között az Azure gerinchálózatra használatával anélkül, hogy az útválasztók. Egy küllős hálózati topológiában virtuális hálózatok közötti társviszony segítségével csatlakozzon az eseményközpont minden egyes küllőhöz kapcsolni. Az ugyanabban a régióban, vagy különböző régiókban található virtuális hálózatok társviszonyt.

## <a name="create-the-directory-structure"></a>A könyvtárstruktúra létrehozása

Hozzon létre tartalmazó könyvtárt a Terraform konfigurációs fájlokat szeretne a bemutatóra.

1. Keresse fel az [Azure Portalt](http://portal.azure.com).

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

1. A Cloud Shellben, nyisson meg egy új fájlt `main.tf`.

    ```bash
    code main.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```JSON
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Mentse a fájlt, és zárja be a szerkesztőt.

## <a name="create-the-variables-file"></a>A változók fájl létrehozása

Közös változókat, amelyek különböző parancsfájlok között Terraform konfigurációs fájljának létrehozása.

1. A Cloud Shellben, nyisson meg egy új fájlt `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Másolja az alábbi kódot a szerkesztőbe:

    ```JSON
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
> [A helyszíni virtuális hálózat létrehozása terraform az Azure-ban](./terraform-hub-spoke-on-prem.md)
