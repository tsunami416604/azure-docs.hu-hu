---
title: A Ansible használata az Azure-ban
description: Bevezetés a Ansible használatára a felhőalapú üzembe helyezés, a konfiguráció-felügyelet és az alkalmazások központi telepítésének automatizálásához.
keywords: Ansible, Azure, devops, áttekintés, felhőalapú üzembe helyezés, konfigurálási felügyelet, alkalmazás-telepítés, Ansible modulok, Ansible forgatókönyvek
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b6b9656edc43f38a4cb005be53c33bb98781679c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241512"
---
# <a name="using-ansible-with-azure"></a>A Ansible használata az Azure-ban

A [Ansible](https://www.ansible.com) egy nyílt forráskódú termék, amely automatizálja a Felhőbeli üzembe helyezést, a konfiguráció felügyeletét és az alkalmazások központi telepítését. A Ansible használatával virtuális gépeket, tárolókat és hálózatokat építhet ki, és teljes körű felhőalapú infrastruktúrát is kiépítheti. Emellett a Ansible lehetővé teszi, hogy automatizálja a környezet erőforrásainak üzembe helyezését és konfigurálását.

Ez a cikk alapvető áttekintést nyújt a Ansible és az Azure használatának előnyeiről.

## <a name="ansible-playbooks"></a>Ansible forgatókönyvek

A [Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) -forgatókönyvek lehetővé teszik a környezet konfigurálását a Ansible. A forgatókönyvek kódolása a YAML használatával történik, hogy az ember számára olvasható legyen. Az oktatóanyagok szakasz számos példát kínál az Azure-erőforrások telepítésére és konfigurálására szolgáló forgatókönyvek használatára. 

## <a name="ansible-modules"></a>Ansible modulok

A Ansible olyan Ansible- [modulokat](https://docs.ansible.com/ansible/latest/modules_by_category.html) tartalmaz, amelyek közvetlenül távoli gazdagépeken vagy forgatókönyveken [keresztül futnak](https://docs.ansible.com/ansible/latest/playbooks.html). A felhasználók létrehozhatják saját moduljaikat. A modulok olyan rendszererőforrások szabályozására szolgálnak, mint például a szolgáltatások, a csomagok vagy a fájlok, illetve a rendszerparancsok végrehajtása.

Az Azure-szolgáltatásokkal való interakcióhoz a Ansible tartalmaz egy [Ansible felhőalapú modulokat](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Ezek a modulok lehetővé teszik az infrastruktúra létrehozását és koordinálását az Azure-ban. 

## <a name="migrate-existing-workload-to-azure"></a>Meglévő számítási feladatok migrálása az Azure-ba

Ha az Ansible-t használja az infrastruktúra definiálásához, akkor az alkalmazás forgatókönyvét az Azure automatikus skálázási környezetének igény szerint automatikusan méretezheti. 

## <a name="automate-cloud-native-application-in-azure"></a>Felhőalapú natív alkalmazás automatizálása az Azure-ban

A Ansible lehetővé teszi az Azure-beli felhőalapú alkalmazások automatizálását az Azure-szolgáltatások, például a [Azure functions](https://azure.microsoft.com//services/functions/) és az Azure-beli [Kubernetes](https://azure.microsoft.com/services/container-service/kubernetes/)használatával.  

## <a name="manage-deployments-with-dynamic-inventory"></a>Központi telepítések kezelése dinamikus leltárral

A Ansible [dinamikus leltározási](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) funkciójával lehetővé teszi a leltár lekérését az Azure-erőforrásokból. Ezután megcímkézheti a meglévő Azure-környezeteket, és kezelheti a címkézett központi telepítéseket a Ansible-on keresztül.

## <a name="additional-azure-marketplace-options"></a>További Azure Marketplace-lehetőségek

A [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) egy Red Hat által elérhető Azure Marketplace-rendszerkép. 

A Ansible Tower egy webalapú felhasználói felület és irányítópult a Ansible, amely a következő funkciókat tartalmazza:

* Lehetővé teszi a szerepköralapú hozzáférés-vezérlés, a feladatütemezés és a grafikus leltár kezelésének megadását. 
* A REST API és parancssori felülettel is rendelkezik, így a tornyot beillesztheti a meglévő eszközökbe és folyamatba. 
* Támogatja a forgatókönyvek futtatásának valós idejű kimenetét. 
* Titkosítja a hitelesítő adatokat – például az Azure-t és az SSH-kulcsokat –, így a hitelesítő adatok kihagyása nélkül delegálhatja a feladatokat.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Az Azure-hoz készült Ansible modul és Version Matrix

A Ansible tartalmazza az Azure-erőforrások üzembe helyezéséhez és konfigurálásához használható modulok csomagját. Ezen erőforrások közé tartoznak a virtuális gépek, a méretezési csoportok, a hálózati szolgáltatások és a tároló szolgáltatások. A [Ansible mátrix](./ansible-matrix.md) felsorolja az Azure-hoz készült Ansible-modulokat és a Ansible-verziókat.

## <a name="next-steps"></a>Következő lépések

- [Gyors útmutató: az Azure Ansible-megoldási sablonjának üzembe helyezése a CentOS-ben](./ansible-deploy-solution-template.md)
- [Gyors útmutató: Linux rendszerű virtuális gépek konfigurálása az Azure-ban a Ansible használatával](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)