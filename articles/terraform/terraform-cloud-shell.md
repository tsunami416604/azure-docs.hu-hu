---
title: "Azure-felhőbe rendszerhéj Terraform használata"
description: "Az Azure felhőalapú rendszerhéj Terraform használatával egyszerűsíthető a hitelesítés és a konfigurációt."
keywords: "terraform, devops, állítsa be, a virtuális gép, hálózati, tárolási, modulok méretezése"
ms.service: virtual-machines-linux
author: dcaro
ms.author: dcaro
ms.date: 10/19/2017
ms.topic: article
ms.openlocfilehash: 5157066086f1bdfa580c1946942bda4505e48935
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="terraform-cloud-shell-development"></a>Fejlesztési Terraform felhő rendszerhéj 

Terraform Windows vagy Linux nagyszerű például a Terminálszolgáltatások vagy a Bash macOS Bash parancssori. A Terraform fut a Bash konfigurációja élménye a [Azure Cloud rendszerhéj](/azure/cloud-shell/overview) néhány egyedi előnyökkel jár a fejlesztési ciklus felgyorsítása érdekében.

Fogalmak cikkben funkciókat, amelyek segítenek Terraform parancsfájlokat írhat, amelyek telepítse az Azure felhőalapú rendszerhéj ismerteti.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Automatikus hitelesítő adatok konfigurálása

Terraform telepítve, és azonnal elérhető a felhő rendszerhéj. Terraform parancsfájlok hitelesítéséhez az Azure-ral, ha jelentkezett be a felhő rendszerhéj további konfiguráció nélkül infrastruktúra kezelése érdekében. Automatikus hitelesítési megkerüli kell manuálisan hozzon létre egy Active Directory szolgáltatás egyszerű, és az Azure Terraform szolgáltató változók konfigurálását.


## <a name="using-modules-and-providers"></a>Modulok és szolgáltatók használatával

Azure Terraform modulok adatokkal eléréséhez és módosításához szükséges lévő erőforrásokat az Azure-előfizetéssel kell rendelkeznie. A felhő rendszerhéj használatakor Azure Terraform modulok használata a felhő rendszerhéj a parancsfájlok adja hozzá a következő kódot:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

A felhő rendszerhéj szükséges értékeket adja át a `azurerm` környezeti változók esetén-szolgáltató a `terraform` parancssori felület parancsait.

## <a name="other-cloud-shell-developer-tools"></a>Más felhőalapú rendszerhéj fejlesztői eszközök

Fájlok és a rendszerhéj állapotok megőrizni az Azure Storage felhőalapú rendszerhéj-munkamenetek között. Használjon [Azure Tártallózó](/azure/vs-azure-tools-storage-manage-with-storage-explorer) másolása és fájlok feltöltése a felhőalapú rendszerhéj a helyi számítógépről.

Az Azure CLI 2.0 érhető el a felhő rendszerhéj, és kiváló eszköz a konfiguráció tesztelése és ellenőrzése után a munkáját a `terraform apply` vagy `terraform destroy` befejeződik.


## <a name="next-steps"></a>További lépések

[Hozzon létre egy kis méretű fürtöt, a modul beállításjegyzékkel](terraform-create-vm-cluster-module.md)
[használatával egyéni Hardverkompatibilitási kis méretű fürt létrehozása](terraform-create-vm-cluster-with-infrastructure.md)
