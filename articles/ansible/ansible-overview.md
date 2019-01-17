---
title: Az Ansible-lel az Azure-ral
description: Az Ansible segítségével automatizálja a felhőbeli üzembe helyezést, a konfigurációkezelést és az alkalmazások központi telepítésének bemutatása.
ms.service: ansible
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: overview
ms.openlocfilehash: 55dcda953454ce3eb4e19dabbf198f886d028180
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357930"
---
# <a name="ansible-with-azure"></a>Ansible az Azure-ral

[Az Ansible](https://www.ansible.com) egy nyílt forráskódú megoldás, amely automatizálja a felhőbeli üzembe helyezést, a konfigurációkezelést és az alkalmazások központi telepítéseit. Az Ansible-lel üzembe virtuális gépeket, tárolókat és hálózati, és végezze el a felhőalapú infrastruktúrák. Emellett az Ansible segítségével automatizálhatja a központi telepítési és konfigurációs az erőforrásoknak a környezetben.

Ez a cikk néhány előnyeit, az Ansible-lel az Azure-ral alapvető áttekintést nyújt.

## <a name="ansible-playbooks"></a>Ansible-forgatókönyvek

[Ansible-forgatókönyvek](https://docs.ansible.com/ansible/latest/playbooks.html) az Ansible konfigurálása, üzembe helyezési és vezénylési nyelvi vannak. Leírhatja egy szabályzatot, a távoli rendszerek kényszeríteni szeretné, vagy egy általános informatikai folyamatban ismertetett lépések. Egy forgatókönyv létrehozásakor érdemes használatával YAML, amelyhez konfigurációt vagy egy folyamatot a modelljét határozza meg.

## <a name="ansible-modules"></a>Az Ansible-modulok

Az Ansible tartalmaz egy [Ansible modulok](https://docs.ansible.com/ansible/latest/modules_by_category.html) , amely képes közvetlenül távoli gazdagépeken vagy végrehajthatók [forgatókönyvek](https://docs.ansible.com/ansible/latest/playbooks.html). Felhasználók a saját modulok is létrehozhat. Modulok segítségével szabályozhatja a rendszererőforrások – például a szolgáltatások, csomagok vagy - fájlokat, vagy hajtsa végre a rendszer parancsokat.

Az Azure szolgáltatással való interakcióhoz, az Ansible tartalmaz egy [Ansible felhőalapú modulok](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) , amely biztosítja az eszközöket, egyszerűen hozzon létre és koordinálhatja az Azure-beli infrastruktúra. 

## <a name="migrate-existing-workload-to-azure"></a>Meglévő számítási feladatok migrálása az Azure-bA

Az Ansible segítségével határozza meg az infrastruktúra, miután az alkalmazás forgatókönyv, így szükség szerint automatikusan skálázhatja a környezetét az Azure is alkalmazhat. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizálhatja az Azure-beli natív alkalmazás

Az Ansible lehetővé teszi az Azure-ban például az Azure mikroszolgáltatás-alapú natív felhőalkalmazásokat automatizálását [Azure Functions](https://azure.microsoft.com//services/functions/) és [Kubernetes az Azure-ban](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>A dinamikus központi telepítések felügyeletéhez szükséges
-N keresztül annak [dinamikus készlet](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) szolgáltatás, az Ansible lehetővé teszi a pull-készlet az Azure-erőforrások. Ezután a meglévő Azure-környezetek címke és az Ansible segítségével ezeket a címkézett központi telepítések felügyeletéhez szükséges.

## <a name="additional-azure-marketplace-options"></a>Azure Marketplace-en további beállítások
A [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) by Red Hat Azure Marketplace-lemezkép segítségével a szervezetek nagy mértékű informatikai automatizálást és összetett központi telepítések felügyeletéhez szükséges üzemeltethetnek fizikai, virtuális és felhőbeli infrastruktúrákon. Az Ansible Tower, hogy látható-e, a vezérlés, a biztonsági és a hatékonyság a mai nagyvállalatok számára szükséges további szintet biztosítanak képességeket tartalmaz. Az Ansible Tower titkosítja a hitelesítő adatok, például az Azure és az SSH-kulcsokat, így delegálhat feladatok a kevésbé tapasztalt alkalmazottak számára a kockázata, hogy a hitelesítő adatok nélkül.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Az Ansible modul és verzió mátrix az Azure-hoz
Az Ansible tartalmaz egy számot, amelyek közvetlenül a távoli gazdagépeken vagy forgatókönyvek keresztül is végrehajthatók.
A [Ansible modul és verzió mátrix](./ansible-matrix.md) sorolja fel az Ansible-modulokat az Azure-ban, amely az Azure felhőbeli erőforrások, például a virtuális gép, hálózati és tárolószolgáltatások építhető. 

## <a name="next-steps"></a>További lépések
- [Az Ansible konfigurálása](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Linux rendszerű virtuális gép létrehozása](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
