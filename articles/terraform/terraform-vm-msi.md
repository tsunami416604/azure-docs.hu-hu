---
title: Egy Azure Marketplace-rendszerképpel egy Terraformot használó linuxos virtuális gépet hozhat létre egy felügyelt identitás használatával
description: Egy Marketplace-rendszerképpel egy Terraformot használó linuxos virtuális gépet hozhat létre egy felügyelt identitás és a Remote State Management használatával az erőforrások Azure-ban történő egyszerű üzembe helyezéséhez.
services: terraform
ms.service: azure
keywords: terraform, devops, MSI, virtuális gép, távoli állapot, azure
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 3/12/2018
ms.openlocfilehash: a1a980e1f8b004c4a3dba53e4f83367022074c7c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007959"
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-identities-for-azure-resources"></a>Egy Azure Marketplace-rendszerképpel egy Terraformot használó linuxos virtuális gépet hozhat létre az Azure-erőforrások felügyelt identitásaival

Ebből a cikkből megtudhatja, hogyan hozhat létre Ubuntu Linux rendszerű virtuális gépet (16.04 LTS) egy [Terraform Marketplace-rendszerkép](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) használatával, a legújabb [Terraform](https://www.terraform.io/intro/index.html)-verzió az [Azure-erőforrások felügyelt identitásaival](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) való telepítése és konfigurálása mellett. Ez a rendszerkép távoli hátteret konfigurál a [távoli állapot](https://www.terraform.io/docs/state/remote.html) a Terraform használatával történő felügyeletének lehetővé tételéhez. 

A Terraform Marketplace-rendszerkép megkönnyíti a Terraform a Terraform manuális telepítése és konfigurálása nélküli használatának megkezdését az Azure-ban. 

Nem tartoznak szoftvermódosítások ehhez a Terraform virtuálisgép-rendszerképhez. Csak a kiépített virtuális gép mérete alapján kiértékelt Azure-os hardverhasználati díjakért kell fizetnie. A számítási díjakról további információt a [Linux rendszerű virtuális gépek díjszabását tartalmazó oldalon](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) talál.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt Terraformot használó linuxos virtuális gépet hozhatna létre, egy Azure-előfizetéssel kell rendelkeznie. Ha még nincs előfizetése, [hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>A Terraformot használó virtuális gép létrehozása 

Itt találja a Terraformot használó linuxos virtuális gép egy példányának létrehozására szolgáló lépéseket: 

1. Az Azure Portalon nyissa meg az [Erőforrás létrehozása](https://ms.portal.azure.com/#create/hub) listát.

2. A **Keresés a piactéren** keresőmezőben keressen a **Terraform** kifejezésre. Válassza ki a **Terraform** sablont. 

3. A jobb alsó részen, a Terraform részleteit tartalmazó lapon válassza ki a **Létrehozás** gombot.

    ![Terraformot használó virtuális gép létrehozása](media/terraformmsi.png)

4. A következő szakaszok a Terraformot használó linuxos virtuális gép létrehozására szolgáló varázsló egyes lépéseinél megadott értékeket tartalmazzák. A következő szakasz a lépések konfigurálásához szükséges értékeket tartalmazza.

## <a name="details-on-the-create-terraform-tab"></a>A Terraform létrehozására szolgáló lapon lévő részletek

Írja be a következő részleteket a **Terraform létrehozására szolgáló** lapon:

1. **Alapvető beállítások**
    
   * **Név**: A Terraform virtuális gép neve.
   * **Felhasználónév**: Az első fiók bejelentkezési azonosítója.
   * **Jelszó**: Az első fiók jelszava. (Jelszó helyett használhat nyilvános SSH-kulcsot is.)
   * **Előfizetés**: Az előfizetés, amelyen a gép létrehozása és a számlázás is. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
   * **Erőforráscsoport**: Egy új vagy meglévő erőforráscsoportot.
   * **Hely**: Az Adatközpont leginkább megfelelő. Általában ez a legtöbb adattal rendelkező vagy – a gyorsabb hálózati hozzáférés érdekében – a fizikai helyéhez legközelebbi adatközpont.

2. **További beállítások**

   * **Méret**: A virtuális gép méretét. 
   * **Virtuális merevlemez típusa**: SSD vagy HDD.

3. **A Terraform összegzése**

   * Győződjön meg arról, hogy helyesen írta be az összes információt. 

4. **Vásárlás**

   * A kiépítési folyamat megkezdéséhez válassza a **Vásárlás** lehetőséget. A tranzakció feltételeiben szerepel egy hivatkozás. A virtuális gép a méretezési lépésben kiválasztott kiszolgálóméret számítási költségein túl nem jár további díjakkal.

A Terraformot használó virtuálisgép-rendszerkép a következő lépéseket végzi el:

* Létrehoz egy virtuális gépet az Ubuntu 16.04 LTS rendszerképen alapuló, rendszer által hozzárendelt identitással.
* Telepíti az MSI-bővítményt a virtuális gépre, hogy az Azure-erőforrásokhoz OAuth-jogkivonatokat lehessen kiadni.
* RBAC-engedélyeket rendel a felügyelt identitáshoz, és az erőforráscsoportnak tulajdonosi jogosultságokat ad.
* Létrehoz egy Terraform-sablonmappát (tfTemplate).
* Előre konfigurál egy távoli Terraform-állapotot az Azure-háttérrel.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Egy Terraformot használó linuxos virtuális gép elérése és konfigurálása

A virtuális gép létrehozása után SSH-val bejelentkezhet rá. A 3. lépés „Alapvető beállítások” szakaszában létrehozott fiókhitelesítő adatokat használja a szöveges parancssorhoz. Windows rendszeren letölthet egy SSH-ügyféleszközt (például a [Puttyt](https://www.putty.org/)).

Miután SSH-val csatlakozott a virtuális géphez, közreműködői engedélyeket kell adnia az Azure-erőforrások felügyelt identitásai számára a teljes előfizetéshez a virtuális gépen. 

A virtuális gépen a közreműködői engedély segít az MSI-nek, hogy a Terraform használatával erőforrásokat hozzon létre a virtuális gép erőforráscsoportján kívül. Egy szkript egyszeri futtatásával ezt a műveletet könnyedén elvégezheti. Használja az alábbi parancsot:

`. ~/tfEnv.sh`

Az előző szkript az [AZ CLI 2.0 interaktív bejelentkezési](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) mechanizmust használja az Azure-ral való hitelesítéshez, és a virtuális gép felügyelt identitása közreműködői engedélyének a teljes előfizetésben történő hozzárendeléséhez. 

 A virtuális gép távoli Terraform-állapotú háttérrel rendelkezik. Ha engedélyezni szeretné a Terraform-környezeten, másolja a remoteState.tf fájlt a tfTemplate könyvtárból a Terraform-szkriptek gyökérkönyvtárába.  

 `cp  ~/tfTemplate/remoteState.tf .`

 A távoliállapot-felügyeletről további információt a [távoli Terraform-állapottal kapcsolatos oldalon](https://www.terraform.io/docs/state/remote.html) talál. Ebben a fájlban látható a tárelérési kulcs, és a Terraform-konfigurációs fájlok verziókövetésbe való véglegesítése előtt ki kell zárni azt.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerte, hogyan állíthat be Terraformot használó linuxos virtuális gépet az Azure-ban. Íme néhány további segédlet, amelyek segítségével többet tudhat meg a Terraform az Azure-on történő használatáról: 

 [Terraform Hub a Microsoft.com webhelyen](https://docs.microsoft.com/azure/terraform/)  
 [Terraform: Azure szolgáltatói dokumentáció](https://aka.ms/terraform)  
 [Terraform: Azure-szolgáltatói forrás](https://aka.ms/tfgit)  
 [Terraform: Azure-modulok](https://aka.ms/tfmodules)
 

















