---
title: Oktatóanyag – Linux rendszerű virtuális gép létrehozása felügyelt identitással az Azure Marketplace-rendszerképből a Terraform használatával
description: Terraform Linux rendszerű virtuális gép létrehozása felügyelt identitással és távoli állapottal az Azure Marketplace rendszerkép használatával
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: e00b674df35516da559339af8028c1ca1845b0db
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969790"
---
# <a name="tutorial-create-a-linux-vm-with-a-managed-identity-from-the-azure-marketplace-image-using-terraform"></a>Oktatóanyag: linuxos virtuális gép létrehozása felügyelt identitással az Azure Marketplace-rendszerképből a Terraform használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre Ubuntu Linux rendszerű virtuális gépet (16.04 LTS) egy [Terraform Marketplace-rendszerkép](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) használatával, a legújabb [Terraform](https://www.terraform.io/intro/index.html)-verzió az [Azure-erőforrások felügyelt identitásaival](/azure/active-directory/managed-service-identity/overview) való telepítése és konfigurálása mellett. Ez a rendszerkép távoli hátteret konfigurál a [távoli állapot](https://www.terraform.io/docs/state/remote.html) a Terraform használatával történő felügyeletének lehetővé tételéhez. 

A Terraform Marketplace-rendszerkép megkönnyíti a Terraform a Terraform manuális telepítése és konfigurálása nélküli használatának megkezdését az Azure-ban. 

Nem tartoznak szoftvermódosítások ehhez a Terraform virtuálisgép-rendszerképhez. A kiépített virtuális gép méretétől függően csak az Azure-beli hardver-használati díjakat kell fizetnie. 

A számítási díjakkal kapcsolatos további információkért tekintse meg a [Linux rendszerű virtuális gépek díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Előfeltételek
Linux Terraform virtuális gép létrehozása előtt Azure-előfizetéssel kell rendelkeznie. Ha még nincs előfizetése, [hozzon létre egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-vm"></a>Terraform virtuális gép létrehozása 

Az alábbi lépéseket követve hozhat létre egy linuxos Terraform VM-példányt: 

1. Az Azure Portalon nyissa meg az [Erőforrás létrehozása](https://ms.portal.azure.com/#create/hub) listát.

1. A **Keresés a piactéren** keresőmezőben keressen a **Terraform** kifejezésre. 

1. Válassza a **Létrehozás**lehetőséget. 

1. A következő szakaszokban a Terraform linuxos virtuális gép létrehozásához a varázsló egyes lépéseinek bemenetei szerepelnek. A következő szakasz a lépések konfigurálásához szükséges értékeket tartalmazza.

## <a name="details-on-the-create-terraform-tab"></a>A Terraform létrehozására szolgáló lapon lévő részletek

Írja be a következő részleteket a **Terraform létrehozására szolgáló** lapon:

1. **Alapvető beállítások**
    
   * **Name (név**): a Terraform virtuális gép neve.
   * **Felhasználónév**: Az első fiók bejelentkezési azonosítója.
   * **Jelszó**: Az első fiók jelszava. (Jelszó helyett használhat nyilvános SSH-kulcsot is.)
   * **Előfizetés**: Az az előfizetés, amelyen a gépet létrehozza és amelyen fizet érte. Ehhez az előfizetéshez erőforrás-létrehozási jogosultságokkal kell rendelkeznie.
   * **Erőforráscsoport**: Egy új vagy létező erőforráscsoport.
   * **Hely**: A legmegfelelőbb adatközpont. Általában ez a legtöbb adattal rendelkező vagy – a gyorsabb hálózati hozzáférés érdekében – a fizikai helyéhez legközelebbi adatközpont.

2. **További beállítások**

   * **Méret**: a virtuális gép mérete. 
   * **Virtuális gép lemeztípusa**: SSD vagy HDD.

3. **A Terraform összegzése**

   * Győződjön meg arról, hogy helyesen írta be az összes információt. 

4. **Vásárlás**

   * A kiépítési folyamat megkezdéséhez válassza a **Vásárlás** lehetőséget. A tranzakció feltételeiben szerepel egy hivatkozás. A virtuális gép nem rendelkezik a méret lépésben kiválasztott kiszolgáló méretétől számított további költségekkel.

A Terraform VM-rendszerkép a következő lépéseket hajtja végre:

* Létrehoz egy virtuális gépet az Ubuntu 16.04 LTS rendszerképen alapuló, rendszer által hozzárendelt identitással.
* Telepíti az Azure-erőforrások bővítmény felügyelt identitásait a virtuális gépen, hogy az OAuth-tokenek kiállíthatók legyenek az Azure-erőforrásokhoz.
* RBAC-engedélyeket rendel a felügyelt identitáshoz, és az erőforráscsoportnak tulajdonosi jogosultságokat ad.
* Létrehoz egy Terraform-sablonmappát (tfTemplate).
* Előre konfigurál egy távoli Terraform-állapotot az Azure-háttérrel.

## <a name="access-and-configure-a-linux-terraform-vm"></a>Linux Terraform virtuális gép elérése és konfigurálása

Miután létrehozta a virtuális gépet, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a virtuális gépre SSH használatával. Használja az előző szakaszban létrehozott fiók hitelesítő adatait. Windows rendszeren letölthet egy SSH-ügyféleszközt (például a [Puttyt](https://www.putty.org/)).

1. Közreműködői engedélyek megadása a teljes előfizetés számára a virtuális gépen található Azure-erőforrások felügyelt identitásához. 

    A közreműködői engedély segíti a virtuális gépen lévő Azure-erőforrások felügyelt identitását, hogy a Terraform használatával erőforrásokat hozzon létre a virtuálisgép-erőforráscsoport kívül. Hajtsa végre ezt a műveletet a következő parancsfájl futtatásával: 
    
    ```bash
    . ~/tfEnv.sh
    ```

    Ez a szkript az [Azure CLI interaktív bejelentkezési](/cli/azure/authenticate-azure-cli?view=azure-cli-latest#sign-in-interactively) mechanizmusát használja az Azure-beli hitelesítéshez. Ez a folyamat hozzárendeli a [felügyelt identitás közreműködői engedélyt](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) a teljes előfizetéshez. 

1. A virtuális gép távoli Terraform-állapotú háttérrel rendelkezik. Ha engedélyezni szeretné a Terraform-telepítésben, az `remoteState.tf`-fájlt át kell másolnia a Terraform-parancsfájlok gyökerébe.

    ```bash
    cp  ~/tfTemplate/remoteState.tf .
    ```

    További információ a távoli állapotadatok kezeléséről: [Terraform távoli állapot](https://www.terraform.io/docs/state/remote.html). A tárterület-hozzáférési kulcs ebben a fájlban van kitéve. Zárja ki a Terraform konfigurációs fájljainak a verziókövetésba való véglegesítése előtt.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Terraform az Azure-ban](/azure/ansible/)