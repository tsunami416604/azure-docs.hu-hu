---
title: Oktatóanyag – Az Azure Cloud Shell konfigurálása a Terraformhoz
description: Ebben az oktatóanyagban a Terraform és az Azure Cloud Shell használatával egyszerűsítheti a hitelesítést és a sablonkonfigurációt.
keywords: azure devops terraform felhőhéj
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945336"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Oktatóanyag: Az Azure Cloud Shell konfigurálása a Terraformhoz

A Terraform jól működik a Bash parancssorból macOS, Windows vagy Linux rendszerben. A Terraform-konfigurációk futtatása az [Azure Cloud Shell](/azure/cloud-shell/overview) Bash-élményében néhány egyedi előnnyel jár. Ez az oktatóanyag bemutatja, hogyan írhat Terraform-parancsfájlokat, amelyek a Cloud Shell használatával üzembe helyeződnek az Azure-ba.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

A Terraform telepítve van, és azonnal elérhető a Cloud Shellben. A Terraform-parancsfájlok az Azure-ral hitelesítik magukat, amikor bejelentkeznek a Cloud Shellbe, és további konfiguráció nélkül kezelik az infrastruktúrát. Az automatikus hitelesítés két manuális folyamatot megkerül:
- Active Directory egyszerű szolgáltatáslétrehozása
- Az Azure Terraform szolgáltatóváltozóinak konfigurálása


## <a name="use-modules-and-providers"></a>Modulok és szolgáltatók használata

Az Azure Terraform-modulok hoz létre hitelesítő adatokat az Azure-erőforrások eléréséhez és módosításához. A Terraform modulok Cloud Shellben való használatához adja hozzá a következő kódot:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

A Cloud Shell a `azurerm` CLI-parancsok bármelyikének használataesetén `terraform` átadja a szolgáltató szükséges értékeit a környezeti változókon keresztül.

## <a name="other-cloud-shell-developer-tools"></a>Egyéb Cloud Shell fejlesztői eszközök

A fájlok és a rendszerhéj-állapotok megmaradnak az Azure Storage-ben a Cloud Shell-munkamenetek között. Az [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) segítségével fájlokat másolhat és tölthet fel a Cloud Shell-be a helyi számítógépről.

Az Azure CLI a Cloud Shellben érhető el, és nagyszerű `terraform apply` eszköz `terraform destroy` a konfigurációk tesztelésére és a munka ellenőrzésére a munka után vagy befejezése után.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kis virtuálisgép-fürt létrehozása a modulbeállítási jegyzék használatával](terraform-create-vm-cluster-module.md)