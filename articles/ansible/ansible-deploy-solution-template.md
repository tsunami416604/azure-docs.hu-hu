---
title: Rövid útmutató – az Azure Ansible-megoldási sablonjának üzembe helyezése a CentOS-ben
description: Ebből a rövid útmutatóból megtudhatja, hogyan helyezheti üzembe a Ansible-megoldás sablonját az Azure-ban üzemeltetett CentOS virtuális gépen, valamint az Azure-hoz való együttműködésre konfigurált eszközöket.
keywords: Ansible, Azure, devops, megoldás sablon, virtuális gép, felügyelt identitások Azure-erőforrásokhoz, CentOS, Red Hat
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: ebd01411ff27b0e95be21ef147098a90b6f62742
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74156331"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Gyors útmutató: az Azure Ansible-megoldási sablonjának üzembe helyezése a CentOS-ben

Az Azure Ansible-megoldási sablonja úgy lett kialakítva, hogy egy Ansible-példányt konfiguráljon egy CentOS virtuális gépen, valamint a Ansible-t és az Azure-hoz való együttműködésre konfigurált eszközkészletet. Az eszközök a következőket foglalják magukban:

- **Ansible-modulok az Azure** -hoz – az Azure-hoz készült [Ansible-modulok](./ansible-matrix.md) olyan modulok, amelyek lehetővé teszik az infrastruktúra létrehozását és kezelését az Azure-ban. A modulok legújabb verziója alapértelmezés szerint telepítve van. A megoldás-sablon telepítési folyamata során azonban megadhatja a környezetének megfelelő verziószámot.
- **Azure parancssori felület (CLI) 2,0** – az [Azure CLI 2,0](/cli/azure/?view=azure-cli-latest) egy platformfüggetlen parancssori felület az Azure-erőforrások kezeléséhez. 
- **felügyelt identitások az Azure-erőforrásokhoz** – az [Azure-erőforrások felügyelt identitásai](/azure/active-directory/managed-identities-azure-resources/overview) szolgáltatás a Cloud Application hitelesítő adatok biztonságos megőrzésének problémájára nyújt megoldást.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>A Ansible-megoldás sablonjának üzembe helyezése

1. Keresse meg az [Ansible-megoldás sablont az Azure piactéren](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Válassza a **Letöltés most**lehetőséget.

1. Megjelenik egy ablak, amely részletezi a használati feltételeket, az adatvédelmi szabályzatot és az Azure Marketplace használati feltételeit. Válassza a **Folytatás** elemet.

1. Megjelenik a Azure Portal, és megjeleníti a Ansible ismertető oldalt. Kattintson a **Létrehozás** gombra.

1. A **Ansible létrehozása** lapon több lap jelenik meg. Az **alapvető beállítások** lapon adja meg a szükséges adatokat:

   - **Név** – adja meg a Ansible-példány nevét. Bemutató célokra a `ansiblehost` nevet használjuk.
   - **Felhasználónév:** -adja meg azt a felhasználónevet, amelynek hozzáférése lesz a Ansible-példányhoz. Bemutató célokra a `ansibleuser` nevet használjuk.
   - **Hitelesítés típusa:** válassza a **jelszó** vagy az **SSH nyilvános kulcs**lehetőséget. Bemutató céljára az **SSH nyilvános kulcs** van kiválasztva.
   - **Jelszó és jelszó** **megerősítése** – ha a **Hitelesítés típusa**beállításnál a **jelszó** lehetőséget választja, adja meg a jelszót ezekhez az értékekhez.
   - **Nyilvános SSH-kulcs** – ha a **hitelesítéshez**a **nyilvános SSH-kulcs** lehetőséget választja, adja meg az RSA nyilvános kulcsát az egysoros formátumban – kezdve a következővel: `ssh-rsa`.
   - **Előfizetés** – válassza ki az Azure-előfizetését a legördülő listából.
   - **Erőforráscsoport** – válasszon ki egy meglévő erőforráscsoportot a legördülő listából, vagy válassza az **új létrehozása** lehetőséget, és adja meg az új erőforráscsoport nevét. Bemutató céljából egy `ansiblerg` nevű új erőforráscsoportot használ a rendszer.
   - **Hely** – válassza ki az adott forgatókönyvnek megfelelő helyet a legördülő listából.

     ![A Ansible alapszintű beállításainak Azure Portal lapja](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Kattintson az **OK** gombra.

1. A **További beállítások** lapon adja meg a szükséges adatokat:

   - **Méret** – a Azure Portal alapértelmezett méretre van kiterjedően. Az adott forgatókönyvhöz tartozó eltérő méret megadásához válassza a nyilat a különböző méretek listájának megjelenítéséhez.
   - Virtuálisgép- **lemez típusa** – válassza az **SSD** (prémium SSD-meghajtó) vagy a **HDD** (merevlemez-meghajtó) lehetőséget. Bemutató céljára az **SSD** a teljesítménybeli előnyökre van kiválasztva. Az ilyen típusú lemezes tárolással kapcsolatos további információkért tekintse meg a következő cikkeket:
       - [Virtuális gépekhez való nagy teljesítményű Premium Storage és felügyelt lemezek](/azure/virtual-machines/windows/premium-storage)
       - [standard SSD Managed Disks Azure-beli virtuális gépek számítási feladataihoz](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Nyilvános IP-cím** – ezt a beállítást akkor válassza, ha a virtuális gépen kívülről szeretne kommunikálni a virtuális géppel. Az alapértelmezett érték egy új nyilvános IP-cím, amelynek a neve `ansible-pip`. Másik IP-cím megadásához válassza a nyílra az adott IP-cím attribútumait, például a nevét, az SKU-t és a hozzárendelést. 
   - **Tartománynév felirata** – adja meg a virtuális gép nyilvános tartománynevét. A névnek egyedinek kell lennie, és meg kell felelnie a névadási követelményeknek. A virtuális gép nevének megadásával kapcsolatos további információkért lásd: az [Azure-erőforrások elnevezési konvenciói](/azure/architecture/best-practices/resource-naming).
   - **Ansible verziója** – megadhatja a verziószámot vagy a `latest` értéket a legújabb verzió telepítéséhez. Az elérhető verziókról további információt a **Ansible verziója** melletti információ ikonra kattintva talál.

     ![Azure Portal lapon a további beállítások Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Kattintson az **OK** gombra.

1. A **Ansible-integráció beállításai** lapon adja meg a hitelesítési típust. További információ az Azure-erőforrások védelméről: [Mi az Azure-erőforrások felügyelt identitásai?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Azure Portal lap a Ansible-integrációs beállításokhoz](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Kattintson az **OK** gombra.

1. Az **Összefoglalás** lapon látható az ellenőrzési folyamat, valamint a Ansible-telepítés megadott feltételeinek listázása. A lap alján található hivatkozásra kattintva **letöltheti a sablont és a paramétereket** a támogatott Azure-nyelvekkel és-platformokkal való használatra. 

     ![Azure Portal lap a Ansible összegzése lapon](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Kattintson az **OK** gombra.

1. Amikor megjelenik a **Létrehozás** lap, kattintson az **OK gombra** a Ansible telepítéséhez.

1. A Ansible központi telepítésének nyomon követéséhez válassza a portál lap tetején található **értesítések** ikont. Miután az üzembe helyezés befejeződött, válassza az **Ugrás az erőforráscsoporthoz**lehetőséget. 

     ![Azure Portal lap a Ansible összegzése lapon](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Az erőforráscsoport lapon szerezze be a Ansible-gazdagép IP-címét, és jelentkezzen be az Azure-erőforrások kezeléséhez a Ansible használatával.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Gyors útmutató: linuxos virtuális gép konfigurálása az Azure-ban az Ansible használatával](/azure/virtual-machines/linux/ansible-create-vm)