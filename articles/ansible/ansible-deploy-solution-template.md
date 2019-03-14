---
title: Az Azure-ban, CentOS, az Ansible megoldássablon telepítése
description: Ismerje meg, hogyan eszközök konfigurálva az Azure-ral együtt az Azure-ban üzemeltetett CentOS virtuális gépeken az Ansible megoldássablon telepítése.
ms.service: azure
keywords: az ansible, azure, devops, megoldássablon, virtuális gép felügyelt identitások azure-erőforrások, a centos, a red hat
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 01/28/2019
ms.openlocfilehash: 6dfc1c0f9dcf29d9cf24df19fbd043c71852aeac
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57790959"
---
# <a name="deploy-the-ansible-solution-template-for-azure-to-centos"></a>Az Azure-ban, CentOS, az Ansible megoldássablon telepítése
Az Ansible megoldássablon az Azure-hoz készült Ansible és a egy csomag az eszközök konfigurálva az Azure-ral együtt CentOS virtuális gépeken az Ansible-példány konfigurálása. Az eszközök a következőket foglalják magukban:

- **Ansible az Azure-modulokat** – a [Ansible az Azure-modulokat](./ansible-matrix.md) vannak, amelyek lehetővé teszik létrehozása és kezelése az Azure-beli infrastruktúra-csomag. Ezek a modulok legújabb verziója van üzembe helyezve, alapértelmezés szerint. A megoldássablon – központi telepítés során megadhatja a verziószámot, amely a környezetnek megfelelő.
- **Az Azure parancssori felület (CLI) 2.0-s** – a [Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) van egy platformfüggetlen parancssori felülete Azure-erőforrások kezeléséhez. 
- **Felügyelt identitások az Azure-erőforrások** – a [felügyelt identitások az Azure-erőforrások](/azure/active-directory/managed-identities-azure-resources/overview) funkció tárgyalja biztonságos kezelése mellett felhőalapú alkalmazás hitelesítő adatait.

## <a name="prerequisites"></a>Előfeltételek
- **Azure-előfizetés** – Ha nem rendelkezik Azure-előfizetéssel, első lépésként hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="deploy-the-ansible-solution-template-from-the-azure-marketplace"></a>Az Azure Marketplace-ről az Ansible megoldássablon telepítése

1. Keresse meg a [megoldássablon az Ansible az Azure Marketplace-en](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Válassza ki **első most**.

1. Megjelenik egy ablak, amely a használati feltételeket, adatvédelmi szabályzatot és használata az Azure piactér használati részletes. Válassza a **Folytatás** elemet.

1. Az Azure-portálon jelenik meg, és az Ansible-oldal leírja a megoldássablon jeleníti meg. Kattintson a **Létrehozás** gombra.

1. Az a **létrehozása Ansible** lapon több lapot látja. Az a **alapjai** lapra, adja meg a szükséges információkat:

    - **Név** – adja meg a nevét, az Ansible-példányhoz. Bemutató céljára, a név `ansiblehost` szolgál.
    - **Felhasználónév:** – adja meg a felhasználónevet, amely hozzáférhet az Ansible-példányba. Bemutató céljára, a név `ansibleuser` szolgál.
    - **Hitelesítés típusa:** -válassza **jelszó** vagy **nyilvános SSH-kulcs**. A bemutatóhoz én **nyilvános SSH-kulcs** van kiválasztva.
    - **Jelszó** és **jelszó megerősítése** – Ha **jelszó** a **hitelesítési típus**, ezeket az értékeket adja meg a jelszót.
    - **Nyilvános SSH-kulcs** – Ha **nyilvános SSH-kulcs** a **hitelesítési típus**, adja meg a nyilvános RSA-kulcsot az egysoros formátumban - kezdve `ssh-rsa`.
    - **Előfizetés** – válassza ki az Azure-előfizetését a legördülő listából.
    - **Erőforráscsoport** – a legördülő listából válasszon ki egy meglévő erőforráscsoportot, vagy válasszon **új létrehozása** és a egy új erőforráscsoport nevének megadásához. Egy új erőforráscsoport neve: bemutatóhoz én `ansiblerg` szolgál.
    - **Hely** – az adott forgatókönyvnek megfelelő legördülő listából válassza ki a helyet.

    ![Az Ansible alapbeállítások az Azure portál lapja](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Kattintson az **OK** gombra.

1. Az a **további beállítás** lapra, adja meg a szükséges információkat:

    - **Méret** – az Azure portal, az alapértelmezett érték egy standard méretű. Adjon meg egy másik méretet, amely adott forgatókönyvnek megfelelő, válassza a nyílra kattintva különböző méretű listájának megjelenítéséhez.
    - **Virtuális merevlemez típusa** – ezek közül bármelyikre **SSD** (prémium szintű Solid-State meghajtó) vagy **HDD** (merevlemez-meghajtó). A bemutatóhoz én **SSD** van kiválasztva a teljesítmény előnyeit. További információ az egyes lemezes tárolás, az ilyen típusú: a következő cikkeket:
        - [Virtuális gépekhez való nagy teljesítményű Premium Storage és felügyelt lemezek](/azure/virtual-machines/windows/premium-storage)
        - [Standard SSD Managed Disks-Azure-beli virtuális gépek számítási feladataihoz](/azure/virtual-machines/windows/disks-standard-ssd)
    - **Nyilvános IP-cím** – adja meg ezt a beállítást, ha azt szeretné, a virtuális gépet a virtuális gép kívülről folytatott kommunikációhoz. Az alapértelmezett érték az új nyilvános IP-címet, amelynek a neve `ansible-pip`. Adjon meg egy másik IP-címet, a nyilat az attribútumok – például a nevét, Termékváltozat és hozzárendelését, az IP-címet adjon meg. 
    - **Tartománynévcímke** – adja meg a virtuális gép a nyilvános tartomány nevét. A névnek kell lennie az egyedi és teljesítenie névadási követelmények vonatkoznak. Adjon meg egy nevet a virtuális gép kapcsolatos további információkért lásd: [Azure-erőforrások elnevezési konvenciói](/azure/architecture/best-practices/naming-conventions).
    - **Az Ansible verzió** – adjon meg egy verziószámot vagy az érték `latest` üzembe helyezéséhez a legújabb verzióra. Válassza ki az információs ikon melletti **Ansible verzió** elérhető verziókról további információkat.

    ![Az Ansible további beállításokat az Azure portál lapja](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Kattintson az **OK** gombra.

1. Az a **Ansible-integrációs beállítások** lapra, adja meg a hitelesítés típusát. Azure-erőforrások védelmével kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Az Ansible-integráció beállításai az Azure portál lapja](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Kattintson az **OK** gombra.

1. A **összefoglalás** lap megjeleníti az érvényesítési folyamat látható és listázása az Ansible üzemelő példány a megadott feltételeknek. A lap alján lehetővé teszi, hogy **a sablon és paraméterek letöltése** Azure támogatott nyelvekhez és platformokhoz való használatra. 

    ![Az Ansible összegző lap az Azure portal lapja](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Kattintson az **OK** gombra.

1. Ha a **létrehozás** lap megjelenik, válassza ki **OK** Ansible telepítése.

1. Válassza ki a **értesítések** ikon a portál oldala az Ansible üzembe helyezésének nyomon tetején. Az üzembe helyezés befejezése után jelölje ki a **erőforráscsoport megnyitása**. 

    ![Az Ansible összegző lap az Azure portal lapja](./media/ansible-deploy-solution-template/portal-ansible-setup-complete.png)

1. Az erőforráscsoport oldalán az Ansible-állomás IP-címének lekéréséhez, és jelentkezzen be az Ansible-lel az Azure erőforrások kezeléséhez.

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"] 
> [Linux rendszerű virtuális gép létrehozása az Azure-ban az Ansible használatával](/azure/virtual-machines/linux/ansible-create-vm)
