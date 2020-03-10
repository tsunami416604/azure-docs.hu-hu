---
title: Oktatóanyag – a Terraform Azure Cloud Shell konfigurálása
description: Ebben az oktatóanyagban a Terraform és a Azure Cloud Shell használatával egyszerűsítheti a hitelesítés és a sablon konfigurációját.
keywords: Azure devops Terraform Cloud Shell
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 3a9db1143ba07b549a271d53d610e0a4853467c6
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945336"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Oktatóanyag: a Terraform Azure Cloud Shell konfigurálása

A Terraform egy bash parancssorból is működik macOS, Windows vagy Linux rendszeren. A Terraform-konfigurációk futtatása a [Azure Cloud Shell](/azure/cloud-shell/overview) bash-felületén egyedülálló előnyökkel jár. Ez az oktatóanyag bemutatja, hogyan írhat az Azure-ba üzembe helyezett Terraform-szkripteket Cloud Shell használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

A Terraform telepítve van, és azonnal elérhető a Cloud Shellben. A Terraform-parancsfájlok az Azure-ba való bejelentkezéskor hitelesítik magukat, ha további konfiguráció nélkül szeretné felügyelni az infrastruktúrát az Cloud Shellba. Az automatikus hitelesítés két manuális folyamatot mellőz:
- Active Directory egyszerű szolgáltatás létrehozása
- Az Azure Terraform-szolgáltató változóinak konfigurálása


## <a name="use-modules-and-providers"></a>Modulok és szolgáltatók használata

Az Azure Terraform-modulok hitelesítő adatokat igényelnek az Azure-erőforrások eléréséhez és módosításához. A Cloud Shell Terraform-moduljainak használatához adja hozzá a következő kódot:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
    # The "feature" block is required for AzureRM provider 2.x. 
    # If you are using version 1.x, the "features" block is not allowed.
    version = "~>2.0"
    features {}
}
```

A Cloud Shell környezeti változók használatával átadja a `azurerm` szolgáltató szükséges értékeit, ha az `terraform` CLI-parancsokat használja.

## <a name="other-cloud-shell-developer-tools"></a>Egyéb Cloud Shell fejlesztői eszközök

A fájlok és a rendszerhéj-állapotok megmaradnak az Azure Storage-ben a Cloud Shell-munkamenetek között. A [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) használatával másolhat és tölthet fel fájlokat a helyi számítógépről Cloud shell.

Az Azure CLI Cloud Shell érhető el, és kiváló eszköz a konfigurációk teszteléséhez és a munka ellenőrzéséhez `terraform apply` vagy `terraform destroy` befejeződése után.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kisméretű virtuálisgép-fürt létrehozása a modul beállításjegyzékének használatával](terraform-create-vm-cluster-module.md)