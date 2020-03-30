---
title: Rövid útmutató – Az Azure Ansible megoldássablonjának üzembe helyezése a CentOS-ba
description: Ebben a rövid útmutatóban megtudhatja, hogyan telepítheti az Ansible-megoldássablont az Azure-ban üzemeltetett CentOS virtuális gépen, valamint az Azure-ral való együttműködésre konfigurált eszközöket.
keywords: ansible, azúr, devops, megoldássablon, virtuális gép, felügyelt identitások az azure-erőforrásokhoz, centos, red hat
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: cc3a6c1f0ef36b51b62e6aa58f317aee13149589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193786"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Rövid útmutató: Az Azure Ansible megoldássablonjának üzembe helyezése a CentOS rendszerbe

Az Azure-hoz készült Ansible megoldássablon egy Ansible-példány konfigurálására szolgál egy CentOS virtuális gépen, az Ansible és az Azure-ral való együttműködésre konfigurált eszközcsomag mellett. Az eszközök a következőket foglalják magukban:

- **Ansible modulok az Azure-hoz** – Az [Ansible modulok az Azure-hoz](./ansible-matrix.md) olyan modulok, amelyek lehetővé teszik, hogy hozzon létre és kezelje az azure-ban. A modulok legújabb verziója alapértelmezés szerint telepítve van. A megoldássablon központi telepítési folyamata során azonban megadhatja a környezetének megfelelő verziószámot.
- **Azure parancssori felület (CLI) 2.0** – Az [Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) egy platformfüggetlen parancssori élmény az Azure-erőforrások kezeléséhez. 
- **felügyelt identitások az Azure-erőforrások –** A [felügyelt identitások az Azure-erőforrások](/azure/active-directory/managed-identities-azure-resources/overview) funkció foglalkozik azzal a kérdéssel, a felhőbeli alkalmazások hitelesítő adatainak biztonságos.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Az Ansible megoldássablon telepítése

1. Tallózással keresse meg az [Ansible megoldássablont az Azure Marketplace-en.](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview)

1. Válassza **a GET IT NOW lehetőséget.**

1. Megjelenik egy ablak, amely részletezi a használati feltételeket, az adatvédelmi szabályzatot és az Azure Piactér használati feltételeit. Válassza a **Folytatás** elemet.

1. Megjelenik az Azure Portal, és megjeleníti az Ansible lapot, amely leírja a megoldássablont. Kattintson a **Létrehozás** gombra.

1. Az **Ansible létrehozása** lapon több lap látható. Az **Alapok** lapon adja meg a szükséges adatokat:

   - **Név** – Adja meg az Ansible-példány nevét. A demo célokra `ansiblehost` a neve használatos.
   - **Felhasználónév:** - Adja meg azt a felhasználónevet, amely nek hozzáférése lesz az Ansible példányhoz. A demo célokra `ansibleuser` a neve használatos.
   - **Hitelesítés típusa:** - Válassza a **Jelszó** vagy az **SSH nyilvános kulcsot.** A bemutató céljából az **SSH nyilvános kulcs** van kiválasztva.
   - **Jelszó** és **jelszó megerősítése** – Ha a **Jelszó** a **hitelesítéshez típus**t választja, adja meg a jelszót ezekhez az értékekhez.
   - **SSH nyilvános kulcs** – Ha az **SSH nyilvános kulcsot** választja a hitelesítés **típushoz,** `ssh-rsa`adja meg az RSA nyilvános kulcsát az egysoros formátumban - kezdve a.
   - **Előfizetés** – Válassza ki az Azure-előfizetést a legördülő listából.
   - **Erőforráscsoport** – Válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza **az Új létrehozása lehetőséget,** és adjon meg nevet egy új erőforráscsoportnak. A bemutató célokra egy új `ansiblerg` erőforráscsoport neve használatos.
   - **Hely** – Válassza ki a helyszínt a forgatókönyvnek megfelelő legördülő listából.

     ![Az Azure Portal lap az Ansible alapbeállításaihoz](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Válassza **az OK gombot.**

1. A **További beállítások** lapon adja meg a szükséges adatokat:

   - **Méret** – Az Azure Portal alapértelmezés szerint egy szabványos méretű. Ha az adott forgatókönyvnek megfelelő, eltérő méretet szeretne megadni, a nyíllal megjelenítheti a különböző méretű listát.
   - **VM-lemez típusa** – Válassza az **SSD** (Premium SSD Drive) vagy **a HDD** (merevlemez-meghajtó) lehetőséget. A demo célokra az **SSD** a teljesítményelőnyeihez van kiválasztva. Az egyes lemezes tárolásról az alábbi cikkekben talál további információt:
       - [Virtuális gépekhez való nagy teljesítményű Premium Storage és felügyelt lemezek](/azure/virtual-machines/windows/premium-storage)
       - [Szabványos SSD-felügyelt lemezek az Azure virtuális gép számítási feladatához](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Nyilvános IP-cím** – Adja meg ezt a beállítást, ha a virtuális gépen kívülről szeretne kommunikálni a virtuális géppel. Az alapértelmezett érték egy új nyilvános `ansible-pip`IP-cím, amelynek neve van. Másik IP-cím megadásához jelölje ki a nyíl, adja meg az adott IP-cím attribútumait, például nevét, termékváltozatát és hozzárendelését. 
   - **Tartománynév-címke** – Adja meg a virtuális gép nyilvános tartománynevét. A névnek egyedinek kell lennie, és meg kell felelnie az elnevezési követelményeknek. A virtuális gép nevének megadásáról további információt az [Azure-erőforrások elnevezési konvenciói című](/azure/architecture/best-practices/resource-naming)témakörben talál.
   - **Ansible version** - Adja meg a `latest` verziószámot vagy a legújabb verzió üzembe helyezéséhez megadott értéket. Az Elérhető verziókkal kapcsolatos további információkért válassza az **Ansible verzió** melletti információs ikont.

     ![Az Azure Portal lap az Ansible további beállításaihoz](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Válassza **az OK gombot.**

1. Az **Ansible Integrációs beállítások** lapon adja meg a hitelesítés típusát. Az Azure-erőforrások védelméről további információt a [Mi az Azure-erőforrások felügyelt identitásai című témakörben](/azure/active-directory/managed-identities-azure-resources/overview)talál.

    ![Az Azure Portal lap az Ansible integrációs beállításaihoz](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Válassza **az OK gombot.**

1. Az **Összefoglaló** lap megjeleníti az ellenőrzési folyamatot, és felsorolja az Ansible központi telepítéséhez megadott feltételeket. A lap alján található hivatkozás lehetővé teszi **a sablon és a paraméterek letöltését a** támogatott Azure-nyelvekhez és platformokhoz. 

     ![Az Azure Portal lap az Ansible Summary laphoz](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Válassza **az OK gombot.**

1. Amikor megjelenik a **Létrehozás** lap, az Ansible telepítéséhez válassza **az OK gombot.**

1. Az **Ansible** központi telepítésének nyomon követéséhez válassza az Értesítések ikont a portállap tetején. A telepítés befejezése után válassza az **Ugrás az erőforráscsoportra**lehetőséget. 

     ![Az Azure Portal lap az Ansible Summary laphoz](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Az erőforráscsoport-lapon lekell szereznie az Ansible-gazdagép IP-címét, és jelentkezzen be az Azure-erőforrások Ansible használatával történő kezeléséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Rövid útmutató: Linuxos virtuális gép konfigurálása az Azure-ban az Ansible használatával](./ansible-create-vm.md)