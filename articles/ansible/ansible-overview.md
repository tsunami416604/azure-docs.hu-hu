---
title: Az Ansible használata az Azure-ral
description: Az Ansible használatának használatába a felhőkiépítés, a konfigurációkezelés és az alkalmazástelepítések automatizálása.
keywords: ansible, azure, devops, áttekintés, felhőalapú ellátás, konfigurációkezelés, alkalmazástelepítés, ansible modulok, ansible forgatókönyvek
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193565"
---
# <a name="using-ansible-with-azure"></a>Az Ansible használata az Azure-ral

[Az Ansible](https://www.ansible.com) egy nyílt forráskódú termék, amely automatizálja a felhőkiépítést, a konfigurációkezelést és az alkalmazástelepítéseket. Az Ansible használatával virtuális gépeket, tárolókat és hálózati és teljes felhőalapú infrastruktúrákat hozhat létre. Is, Ansible lehetővé teszi, hogy automatizálja a központi és konfigurációs erőforrások a környezetben.

Ez a cikk az Ansible azure-beli használatának néhány előnyét ismerteti.

## <a name="ansible-playbooks"></a>Ansible forgatókönyvek

[Az Ansible forgatókönyvei](https://docs.ansible.com/ansible/latest/playbooks.html) lehetővé teszik, hogy az Ansible-t irányítsa a környezet konfigurálásához. A forgatókönyvek kódolva yaml használatával vannak kódolva, hogy ember által olvashatóak legyenek. Az Oktatóanyagok szakasz számos példát ad a forgatókönyvek használatával az Azure-erőforrások telepítéséhez és konfigurálásához. 

## <a name="ansible-modules"></a>Ansible modulok

Ansible tartalmaz egy kíséret -ból [Ansible elemek](https://docs.ansible.com/ansible/latest/modules_by_category.html) amit van fuss közvetlenül -ra távoli házigazdák vagy keresztül [forgatókönyv.](https://docs.ansible.com/ansible/latest/playbooks.html) A felhasználók létrehozhatják saját moduljaikat. A modulok a rendszer erőforrásainak – például szolgáltatások, csomagok vagy fájlok – vezérlésére vagy rendszerparancsok végrehajtására szolgálnak.

Az Azure-szolgáltatásokkal való interakcióhoz az Ansible [egy Ansible felhőmodul-csomagot](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure)tartalmaz. Ezek a modulok lehetővé teszik az azure-beli infrastruktúra létrehozását és vezénylését. 

## <a name="migrate-existing-workload-to-azure"></a>Meglévő munkaterhelés áttelepítése az Azure-ba

Miután az Ansible használatával definiálja az infrastruktúrát, alkalmazhatja az alkalmazás forgatókönyvét, amely lehetővé teszi, hogy az Azure szükség szerint automatikusan méretezzen a környezetet. 

## <a name="automate-cloud-native-application-in-azure"></a>A natív felhőalapú alkalmazások automatizálása az Azure-ban

Az Ansible lehetővé teszi, hogy automatizálja a natív felhőalapú alkalmazásokat az Azure-ban az Azure mikroszolgáltatásaival, például az [Azure Functions](https://azure.microsoft.com//services/functions/) és [a Kubernetes használatával az Azure-ban.](https://azure.microsoft.com/services/container-service/kubernetes/)  

## <a name="manage-deployments-with-dynamic-inventory"></a>Központi telepítések kezelése dinamikus készlettel

Dinamikus [készletfunkciója](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) révén az Ansible lehetővé teszi a készlet lekérése az Azure-erőforrásokból. Ezután megcímkézheti a meglévő Azure-központi telepítéseket, és kezelheti a címkézett központi telepítések ansible keresztül.

## <a name="additional-azure-marketplace-options"></a>További Azure Piactér-beállítások

Az [Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) egy Azure Marketplace-lemezkép a Red Hat. 

Az Ansible Tower egy webalapú felhasználói felület és irányítópult az Ansible számára, amely a következő funkciókkal rendelkezik:

* Lehetővé teszi a szerepköralapú hozzáférés-vezérlés, a feladatütemezés és a grafikus készletkezelés definiálását. 
* Rest API-t és CLI-t tartalmaz, így a Towerbe beillesztheti a meglévő eszközöket és folyamatokat. 
* Támogatja a forgatókönyv-futtatások valós idejű kimenetét. 
* Titkosítja a hitelesítő adatokat – például az Azure- és az SSH-kulcsokat –, így hitelesítő adatok felfedése nélkül delegálhat feladatokat.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible modul és verziómátrix az Azure-hoz

Az Ansible egy modulcsomagot tartalmaz az Azure-erőforrások kiépítéséhez és konfigurálásahoz. Ezek az erőforrások közé tartoznak a virtuális gépek, méretezési csoportok, hálózati szolgáltatások és a tárolószolgáltatások. [Az Ansible mátrix](./ansible-matrix.md) felsorolja az Ansible modulok az Azure és az Ansible verziók, amelyek ben a szállítás.

## <a name="next-steps"></a>További lépések

- [Rövid útmutató: Az Azure Ansible megoldássablonjának üzembe helyezése a CentOS rendszerbe](./ansible-deploy-solution-template.md)
- [Rövid útmutató: Linuxos virtuális gépek konfigurálása az Azure-ban az Ansible használatával](./ansible-install-configure.md)