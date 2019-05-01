---
title: Az Ansible-lel az Azure-ral |} A Microsoft Docs
description: Az Ansible segítségével automatizálja a felhőbeli üzembe helyezést, a konfigurációkezelést és az alkalmazások központi telepítésének bemutatása.
keywords: ansible, azure, devops, overview, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 54d66a0ac425a9a0a63c91317ead0e02ecf9452c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726018"
---
# <a name="using-ansible-with-azure"></a>Az Ansible-lel az Azure-ral

[Az Ansible](https://www.ansible.com) egy nyílt forráskódú megoldás, amely automatizálja a felhőbeli üzembe helyezést, a konfigurációkezelést és az alkalmazások központi telepítéseit. Az Ansible-lel üzembe virtuális gépeket, tárolókat és hálózati, és végezze el a felhőalapú infrastruktúrák. Is az Ansible lehetővé teszi, hogy automatizálja a központi telepítési és konfigurációs az erőforrásoknak a környezetben.

Ez a cikk néhány előnyeit, az Ansible-lel az Azure-ral alapvető áttekintést nyújt.

## <a name="ansible-playbooks"></a>Ansible-forgatókönyvek

[Ansible-forgatókönyvek](https://docs.ansible.com/ansible/latest/playbooks.html) teszi lehetővé a környezet konfigurálásához az Ansible közvetlen. Forgatókönyvek vannak kódolva úgy, hogy a természetes nyelven olvasható YAML. Az oktatóanyagok szakaszban számos példákat forgatókönyvek segítségével telepítheti és konfigurálhatja az Azure-erőforrások. 

## <a name="ansible-modules"></a>Az Ansible-modulok

Az Ansible tartalmaz egy [Ansible modulok](https://docs.ansible.com/ansible/latest/modules_by_category.html) közvetlenül távoli állomások vagy annak segítségével futtatott [forgatókönyvek](https://docs.ansible.com/ansible/latest/playbooks.html). Felhasználók a saját modulokat hozhatnak létre. Modulok segítségével szabályozhatja a rendszererőforrások – például a szolgáltatások, csomagok vagy - fájlokat, vagy hajtsa végre a rendszer parancsokat.

Az Azure szolgáltatással való interakcióhoz, az Ansible tartalmaz egy [Ansible felhőalapú modulok](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Ezek a modulok lehetővé teszik hozhat létre és koordinálhatja az Azure-beli infrastruktúra. 

## <a name="migrate-existing-workload-to-azure"></a>Meglévő számítási feladatok migrálása az Azure-bA

Az Ansible segítségével határozza meg az infrastruktúra, miután az alkalmazás forgatókönyv, így szükség szerint automatikusan skálázhatja a környezetét az Azure is alkalmazhat. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizálhatja az Azure-beli natív alkalmazás

Az Ansible lehetővé teszi az Azure-ban például az Azure mikroszolgáltatás-alapú natív felhőalkalmazásokat automatizálását [Azure Functions](https://azure.microsoft.com//services/functions/) és [Kubernetes az Azure-ban](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>A dinamikus központi telepítések felügyeletéhez szükséges

-N keresztül annak [dinamikus készlet](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) szolgáltatás, az Ansible lehetővé teszi a pull-készlet az Azure-erőforrások. Ezután a meglévő Azure-környezetek címke és az Ansible segítségével ezeket a címkézett központi telepítések felügyeletéhez szükséges.

## <a name="additional-azure-marketplace-options"></a>Azure Marketplace-en további beállítások

A [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) van az Azure Marketplace-rendszerképpel, by Red Hat. 

Az Ansible Tower egy webalapú felhasználói felület és egy irányítópultot az Ansible, amely a következő funkciókkal rendelkezik:

* Lehetővé teszi a szerepköralapú hozzáférés-vezérlés, a feladatütemezés és a grafikus leltárkezelés meghatározása érdekében. 
* Tartalmaz egy REST API-t és a parancssori felület, így Tower beilleszthet meglévő eszközökkel és folyamatokkal. 
* Támogatja a forgatókönyv-futtatások valós idejű kimenetét. 
* Hitelesítő adatai – Azure és az SSH-kulcsok – például titkosítja, így anélkül, hogy a hitelesítő adatok feladatokat delegálhat.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Az Ansible modul és verzió mátrix az Azure-hoz

Az Ansible modulok használatát számos kiépítési és konfigurálása az Azure-erőforrások tartalmazza. Ezek az erőforrások közé tartozik a virtuális gépek, a méretezési csoportokat, hálózati szolgáltatások és tárolószolgáltatások. A [Ansible mátrix](./ansible-matrix.md) az Ansible-modulok felsorolja az Azure és az Ansible-verziók, amelyben szállításra.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Az Azure-ban, CentOS, az Ansible megoldássablon telepítése](./ansible-deploy-solution-template.md)
- [Rövid útmutató: Az Azure-ban az Ansible konfigurálása a Linux rendszerű virtuális gépek](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)