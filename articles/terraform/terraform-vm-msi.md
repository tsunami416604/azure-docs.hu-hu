---
title: Az Azure piactér lemezkép segítségével felügyelt szolgáltatás identitású Terraform Linux virtuális gép létrehozása
description: A Szolgáltatásidentitás felügyelt és a Távfelügyelet állapota, egyszerűen telepítse az Azure erőforrások Terraform Linux virtuális gép létrehozásához használja a Piactéri lemezképhez.
keywords: terraform, devops, MSI, virtuális gép, a távoli állapot, az azure
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: 5f0ee2904c1072a5ad8c5f7ae1c90e649cc4813c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Az Azure piactér lemezkép segítségével felügyelt szolgáltatás identitású Terraform Linux virtuális gép létrehozása

Ez a cikk bemutatja, hogyan használható egy [Terraform Piactéri lemezképhez](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) az Ubuntu Linux virtuális gép létrehozása (16.04 LTS) a legújabb [Terraform](https://www.terraform.io/intro/index.html) verziója telepítve, és segítségével konfigurálható: [felügyelt Identitás (MSI) szolgáltatás](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Ez a rendszerkép is konfigurálja a távoli háttérrendszeréhez [távoli állapot](https://www.terraform.io/docs/state/remote.html) felügyelet Terraform használatával. 

A Terraform Piactéri lemezképhez egyszerűen Terraform az Azure, nem kell manuálisan telepíteni és konfigurálni Terraform első lépéseiben. 

Nincsenek a Terraform VM-lemezkép szoftver költségek. Csak az Azure hardver használati díjak ellátott virtuális gép mérete alapján értékelni kell fizetnie. A számítási díjakat kapcsolatos további információkért tekintse meg a [Linux virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Előfeltételek
Linux Terraform virtuális gép létrehozásához, Azure-előfizetéssel kell rendelkeznie. Ha még nem rendelkezik egy, lásd: [ma létrehozása az ingyenes Azure-fiókjával](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>A Terraform virtuális gép létrehozása 

A lépések Linux Terraform virtuális gép példány létrehozásához a következők: 

1. Az Azure-portálon lépjen a [hozzon létre egy erőforrást](https://ms.portal.azure.com/#create/hub) listázása.

2. Az a **keresése a piactéren** keresősávban, keressen a **Terraform**. Válassza ki a **Terraform** sablont. 

3. A jobb alsó Terraform részleteket tartalmazó lapot, jelölje ki a **létrehozása** gombra.

    ![Terraform virtuális gép létrehozása](media\terraformmsi.png)

4. A következő szakaszokban bemeneti adatokat az egyes a Terraform Linux virtuális gép létrehozása a varázsló lépéseit. A következő szakasz a bemeneti adatok, amelyek szükségesek ahhoz, hogy konfigurálja az egyes lépéseket sorolja fel.

## <a name="details-on-the-create-terraform-tab"></a>A Terraform létrehozása lap részletei

A következő adatokat adja meg a **létrehozása Terraform** lapon:

1. **Alapvető beállítások**
    
   * **Név**: a Terraform virtuális gép nevét.
   * **Felhasználónév**: az első fiók bejelentkezhet azonosítóját.
   * **Jelszó**: az első fiók jelszavát. (Használhat nyilvános SSH-kulcs jelszó helyett.)
   * **Előfizetés**: az előfizetés, amelyre a gép létrehozását és számlázva van. Ehhez az előfizetéshez erőforrás-létrehozási jogosultsággal kell rendelkeznie.
   * **Erőforráscsoport**: egy új vagy meglévő erőforráscsoportot.
   * **Hely**: az adatközponthoz, amely a legjobban megfelelő. Általában az adatközpont, amelyen az adatok, vagy a másik pedig a fizikai hely leggyorsabb hálózati hozzáférési legközelebb.

2. **További beállítások**

   * **Méret**: a virtuális gép méretét. 
   * **Virtuális gép lemeztípus**: SSD és HDD.

3. **Összegző Terraform**

   * Győződjön meg arról, hogy az összes megadott adatok helyességét. 

4. **Buy**

   * Válassza ki a telepítési folyamat elindításához **megvásárlása**. Hivatkozás a tranzakció feltételeit valósul meg. A virtuális gép nem rendelkezik a kiszolgáló méretét, amely a mérete lépésben kiválasztott számítási túl a további díjakat.

A Terraform Virtuálisgép-lemezkép hajtja végre az alábbi lépéseket:

* Alapértelmezett identitás, az Ubuntu 16.04 LTS lemezkép alapján hoz létre egy virtuális Gépet.
* Az MSI-bővítmény telepítése engedélyezéséhez OAuth-jogkivonat Azure-erőforrások adja ki a virtuális Gépet.
* Az RBAC engedélyeket rendeli hozzá a felügyelt identitása, az erőforráscsoport tulajdonosi jogosultságok megadása.
* Létrehoz egy Terraform sablon mappát (tfTemplate).
* Előre konfigurálja az Azure biztonsági Terraform távoli államnak célból.

## <a name="access-and-configure-a-linux-terraform-virtual-machine"></a>Hozzáférés és a Linux Terraform virtuális gép konfigurálása

A virtuális gép létrehozása után is bejelentkezik az ssh protokoll használatával. A 3. lépés a szöveg shell felületén "alapvető beállítások" szakaszban létrehozott fiók hitelesítő adatait használja. Windows, egy SSH-ügyfél eszköz, például letöltheti [Putty](http://www.putty.org/).

Miután SSH a virtuális géphez történő csatlakozáshoz, hozzá kell rendelnie a közreműködői engedélyekkel az egész előfizetésre hozzá felügyelt szolgáltatást a virtuális gépen. 

Közreműködő engedély segít MSI Terraform erőforrások kívül a Virtuálisgép-csoport létrehozásához használja a virtuális gépen. Ez a művelet könnyen egyszer parancsfájl futtatásával érhet el. Használja az alábbi parancsot:

`. ~/tfEnv.sh`

Az előző parancsfájl használja a [v 2.0 AZ CLI interaktív bejelentkezés](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) mechanizmus hitelesítéséhez az Azure-ral, és rendelje hozzá a virtuális gépet felügyelt Szolgáltatásidentitás közreműködői engedélyt az egész előfizetésre. 

 A virtuális Gépnek legyen a Terraform távoli állapot háttérből. Engedélyezze a Terraform üzemelő példányon, másolja a remoteState.tf fájlt tfTemplate könyvtárból a legfelső szintű Terraform parancsfájlok.  

 `cp  ~/tfTemplate/remoteState.tf .`

 Állapot távfelügyelettel kapcsolatos további információkért lásd: [ezen a lapon Terraform távoli állapotára vonatkozó](https://www.terraform.io/docs/state/remote.html). A tárelérési kulcs fel van fedve ebben a fájlban, és az adatforrás-vezérlő előtt commiting Terraform konfigurációs fájlok zárhatók ki kell.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan állíthat be egy Terraform Linux virtuális gépet az Azure. Az alábbiakban néhány további források további tudnivalók az Azure-on Terraform: 

 [A Microsoft.com Terraform Hub](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure-szolgáltató dokumentáció](http://aka.ms/terraform)  
 [Terraform Azure szolgáltató forrás](http://aka.ms/tfgit)  
 [Terraform Azure modulok](http://aka.ms/tfmodules)
 

















