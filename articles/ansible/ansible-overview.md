---
title: "Az Azure-ral Ansible használatával"
description: "Bevezetés a Ansible segítségével automatizálja a felhőalapú létesítése, kezelési és alkalmazások központi telepítéseit."
ms.service: ansible
keywords: "ansible, azure, devops, áttekintése, felhő kiépítéséhez, kezelési, alkalmazástelepítés, ansible modulok, ansible playbooks"
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="ansible-with-azure"></a>Az Azure-ral Ansible

[Ansible](http://www.ansible.com) egy nyílt forráskódú megoldás, amely automatizálja a felhőalapú létesítése, kezelési és alkalmazások központi telepítéseit. Ansible segítségével használhatók a virtuális gépek, a tárolók és a hálózati és végezze el a felhőalapú infrastruktúrák. Ezenkívül Ansible lehetővé teszi, hogy automatizálja a központi telepítési és konfigurációs az erőforrásoknak a környezetben.

Ez a cikk az Azure-ral Ansible használatának előnyeit némelyike alapvető áttekintést nyújt.

## <a name="ansible-playbooks"></a>Ansible playbooks

[Ansible playbooks](http://docs.ansible.com/ansible/latest/playbooks.html) Ansible tartozó konfigurációs, a központi telepítés és a vezénylési nyelvi vannak. A kívánt házirendet, a távoli rendszerek kényszerítéséhez vagy egy olyan általános informatikai folyamat is leírják. A forgatókönyv létrehozásakor ezt megteheti használatával YAM, amely konfiguráció vagy a folyamat a modelljét határozza meg.

## <a name="ansible-modules"></a>Ansible modulok

Ansible tartalmaz olyan [Ansible modulok](http://docs.ansible.com/ansible/latest/modules_by_category.html) , amely közvetlenül a távoli állomáson, vagy segítségével hajtható végre [playbooks](http://docs.ansible.com/ansible/latest/playbooks.html). Felhasználók emellett létrehozhatják saját modulok. Modulok rendszererőforrások – például a szolgáltatások, a csomagok és a fájl - vagy hajtsa végre a rendszer parancsok használható.

Az Azure-szolgáltatásokkal, Ansible tartalmaz olyan [Ansible felhő modulok](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) , egyszerűen hozzon létre, és az Azure-beli infrastruktúra levezényelni a eszközöket biztosít. 

## <a name="migrate-existing-workload-to-azure"></a>Meglévő alkalmazások és szolgáltatások áttelepítése az Azure-bA

Miután Ansible segítségével határozza meg az infrastruktúra, Azure szükség szerint automatikusan átméretezi a környezettől és az alkalmazás alkalmazástervezési is alkalmazhat. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizálhatja az Azure-felhő natív alkalmazás

Ansible lehetővé teszi, hogy automatizálja a felhő-natív alkalmazások az Azure-ban, mint az Azure mikroszolgáltatások [Azure Functions](https://azure.microsoft.com//services/functions/) és [Azure Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Dinamikus leltár központi telepítések felügyeletéhez szükséges
Keresztül a [dinamikus készlet](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) , Ansible funkciójával lekéréses leltárhoz az Azure-erőforrások. A meglévő Azure-környezetekhez címkét, majd ezek Ansible címkézett környezeteit kezelheti.

## <a name="additional-azure-marketplace-options"></a>További Azure piactér-beállítások
A [Ansible torony](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) Red Hat Azure piactér lemezkép segítségével a szervezetek méretezése informatikai automatizálási és összetett központi telepítések felügyeletéhez szükséges különböző fizikai, virtuális, és a felhőalapú infrastruktúrák. Ansible torony kínálja látható, a vezérlő, a biztonság és a hatékonyság mai vállalatok számára szükséges további szintet biztosítanak. Ansible torony titkosítja a hitelesítő adatok, például az Azure és az SSH-kulcsokat, így delegálhat feladatok kevésbé tapasztalt alkalmazottak számára a hitelesítő adatok kitettségének kockázata nélkül.

## <a name="next-steps"></a>További lépések
- [Ansible konfigurálása](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Linux virtuális gép létrehozása](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)