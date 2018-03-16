---
title: "Az Azure piactér lemezkép segítségével felügyelt szolgáltatás identitású Terraform Linux virtuális gép létrehozása"
description: "Az erőforrások egyszerűen telepítse az Azure Managed Service identitás- és távoli állapotának felügyeleti Terraform Linux virtuális gép létrehozásához használja a Piactéri lemezképhez."
keywords: "terraform, devops, MSI, virtuális gép, a távoli állapot, az azure"
author: VaijanathB
manager: rloutlaw
ms.author: tarcher
ms.date: 3/12/2018
ms.topic: article
ms.openlocfilehash: ce8a3e8b813bf4224a1fd77d60ec30f2f8e080a7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="use-an-azure-marketplace-image-to-create-a-terraform-linux-virtual-machine-with-managed-service-identity"></a>Az Azure piactér lemezkép segítségével felügyelt szolgáltatás identitású Terraform Linux virtuális gép létrehozása

A cikkből megtudhatja, hogyan használható egy [Terraform Piactéri lemezképhez](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.terraform?tab=Overview) létrehozásához egy `Ubuntu Linux VM (16.04 LTS)` a legújabb [Terraform](https://www.terraform.io/intro/index.html) verziója telepítve, és segítségével konfigurálható: [felügyelt Szolgáltatásidentitás () MSI-fájl)](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Ez a rendszerkép is konfigurálja a távoli háttérkiszolgálón engedélyezése [távoli állapotának](https://www.terraform.io/docs/state/remote.html) felügyelet Terraform használatával. A Terraform Piactéri lemezképhez megkönnyíti, hogy az első lépéseiben Terraform Azure (percben), Terraform manuálisan telepíteni és konfigurálni hitelesítés nélkül. 

Nincsenek a Terraform VM-lemezkép szoftver költségek. Csak az Azure hardver használati díjak értékelni a kiosztott virtuális gép mérete alapján kell fizetnie. A számítási díjakat további információkat itt talál a [Linux virtuális gépek árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="prerequisites"></a>Előfeltételek
Terraform Linux virtuális gép létrehozásához, Azure-előfizetéssel kell rendelkeznie. Ha még nem rendelkezik egy, lásd: [ma létrehozása az ingyenes Azure-fiókjával](https://azure.microsoft.com/free/).  

## <a name="create-your-terraform-virtual-machine"></a>A Terraform virtuális gép létrehozása 

Az alábbiakban egy példányát a Linux Terraform virtuális gép létrehozásához szükséges lépéseket. 

1. Navigáljon a [hozzon létre egy erőforrást](https://ms.portal.azure.com/#create/hub) listázása az Azure portálon.
2. Keresse meg `Terraform` a a `Search the Marketplace` keresősáv. Válassza ki a `Terraform` sablont. Válassza ki a **létrehozása** a Terraform Részletek lap alsó jobb oldali gomb.
![helyettesítő szöveg](media\terraformmsi.png)
3. Az alábbi szakaszok nyújtanak bemenet minden egyes, a varázsló lépéseit (**a jobb oldali számba**) a Terraform Linux virtuális gép létrehozásához.  Az alábbiakban az egyes lépéseket konfigurálásához szükséges bemeneti adatok

## <a name="details-in-create-terraform-tab"></a>A részletek Terraform lap létrehozása

Az alábbiakban a részletes adatait, hozzon létre Terraform lapon kell megadni.

a. **Alapvető beállítások**
    
* **Név**: a Terraform virtuális gép nevét.
* **Felhasználónév**: első fiók bejelentkezhet azonosítóját.
* **Jelszó**: első fiók jelszavát (használható nyilvános SSH-kulcs jelszó helyett).
* **Előfizetés**: Ha több előfizetéssel rendelkezik, válassza ki a amelyiken a gép létrehozását és számlázva van. Ehhez az előfizetéshez erőforrás-létrehozási jogosultsággal kell rendelkeznie.
* **Erőforráscsoport**: hozhat létre egy új vagy meglévő csoport használata.
* **Hely**: válassza ki a legjobban megfelelő adatközpont. Általában az adatközpont, amely rendelkezik az adatok, vagy a helynév leggyorsabb hálózati hozzáférési legközelebb.

b. **További beállítások**

* Mérete: A virtuális gép mérete.
* Virtuális gép lemeztípus: Válasszon az SSD és HDD

c. **Összegző Terraform**

* Győződjön meg arról, hogy az összes megadott adatok helyesek. 

d. **Buy**

* A kiépítés elindításához kattintson a döntést. Hivatkozás a tranzakció feltételeit valósul meg. A virtuális gép nem rendelkezik a kiszolgáló méretét a mérete lépésben kiválasztott számítási túl a további díjakat.

A Terraform Virtuálisgép-lemezkép a következő lépéseket végzi el.

* Hoz létre a virtuális gép identitása, az Ubuntu 16.04 LTS lemezképen alapuló hozzárendelt rendszer
* Telepíti az MSI-bővítmény engedélyezéséhez OAuth-jogkivonat Azure-erőforrások adja ki a virtuális gépen
* Az RBAC engedélyek hozzárendelése a felügyelt identitása, az erőforráscsoport tulajdonosi jogosultságok megadása
* Létrehoz egy Terraform sablon mappát (tfTemplate)
* Előre konfigurálja a Terraform távoli állapotát az Azure-háttérrendszerrel

## <a name="how-to-access-and-configure-linux-terraform-virtual-machine"></a>Eléréséről és Linux Terraform virtuális gép konfigurálása

A virtuális gép létrehozása után is bejelentkezik az ssh protokoll használatával. A 3. lépés a szöveg shell felületén alapjai szakaszában létrehozott fiók hitelesítő adatait használja. Windows, egy SSH-ügyfél eszköz, például letöltheti [Putty](http://www.putty.org/)

Ha használta `SSH` szeretne csatlakozni a virtuális gép, hozzá kell rendelnie az egész előfizetésre közreműködői engedélyekkel szolgáltatás identitása kezeli a virtuális gépen. Közreműködő engedély segít MSI Terraform erőforrások kívül a Virtuálisgép-csoport létrehozásához használja a virtuális gépen. Ez a művelet könnyen érhet el, hogy a parancsfájl végrehajtása után. Ez a parancs ne.

`. ~/tfEnv.sh`

Az előző parancsfájl [v 2.0 AZ CLI interaktív bejelentkezés](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest#interactive-log-in) mechanizmus hitelesítéséhez az Azure-ral, és rendelje hozzá a virtuális gép felügyelt Szolgáltatásidentitás közreműködői engedélyt az egész előfizetésre. 

 A virtuális Gépnek legyen Terraform távoli állapotának háttér létrehozott, és engedélyezze a Terraform üzemelő példányon, remoteState.tf fájlt másolja a legfelső szintű Terraform parancsfájlok tfTemplate könyvtárból kell.  

 `cp  ~/tfTemplate/remoteState.tf .`

 További állapot távfelügyelettel kapcsolatos [Itt](https://www.terraform.io/docs/state/remote.html). A tárelérési kulcs fel van fedve ebben a fájlban, és a forrás vezérlőbe alaposan ellenőrizni kell.  

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan állítható be Terraform Linux virtuális gépek Azure-on. Az alábbiakban néhány további erőforrások tudhat meg többet az Azure-on Terraform. 

 [A Microsoft.com Terraform Hub](https://docs.microsoft.com/azure/terraform/)  
 [Terraform Azure-szolgáltató dokumentáció](http://aka.ms/terraform)  
 [Terraform Azure szolgáltató forrás](http://aka.ms/tfgit)  
 [Terraform Azure modulok](http://aka.ms/tfmodules)
 

















