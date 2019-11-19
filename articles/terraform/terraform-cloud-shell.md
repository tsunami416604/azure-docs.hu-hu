---
title: Oktatóanyag – a Terraform Azure Cloud Shell konfigurálása
description: A Terraform Azure Cloud Shell-lel való használatával egyszerűsíthető a hitelesítés és a sablonok konfigurációja.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: db9edfadbe01edc1ee9df09c284e3895ee11f3d3
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159128"
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
}
```

A Cloud Shell környezeti változók használatával átadja a `azurerm` szolgáltató szükséges értékeit, ha az `terraform` CLI-parancsokat használja.

## <a name="other-cloud-shell-developer-tools"></a>Egyéb Cloud Shell fejlesztői eszközök

A fájlok és a rendszerhéj-állapotok megmaradnak az Azure Storage-ben a Cloud Shell-munkamenetek között. A [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) használatával másolhat és tölthet fel fájlokat a helyi számítógépről Cloud shell.

Az Azure CLI Cloud Shell érhető el, és kiváló eszköz a konfigurációk teszteléséhez és a munka ellenőrzéséhez `terraform apply` vagy `terraform destroy` befejeződése után.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kisméretű virtuálisgép-fürt létrehozása a modul beállításjegyzékének használatával](terraform-create-vm-cluster-module.md)
