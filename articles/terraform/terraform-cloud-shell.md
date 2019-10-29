---
title: Oktatóanyag – a Terraform Azure Cloud Shell konfigurálása
description: A Terraform Azure Cloud Shell-lel való használatával egyszerűsíthető a hitelesítés és a sablonok konfigurációja.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 693ed462fb1ba3dfed079e8ae97152732c771253
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969592"
---
# <a name="tutorial-configure-azure-cloud-shell-for-terraform"></a>Oktatóanyag: a Terraform Azure Cloud Shell konfigurálása

A Terraform egy bash parancssorból is működik macOS, Windows vagy Linux rendszeren. A Terraform-konfigurációk futtatása a [Azure Cloud Shell](/azure/cloud-shell/overview) bash-felületén egyedülálló előnyökkel jár. Ez az oktatóanyag bemutatja, hogyan írhat az Azure-ba üzembe helyezhető Terraform-szkripteket Cloud Shell használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

A Terraform telepítve van, és azonnal elérhető a Cloud Shellben. A Cloud Shellbe bejelentkezve a Terraform-szkriptek az Azure-ban hitelesítenek, így további konfiguráció nélkül felügyelhető az infrastruktúra. Az automatikus hitelesítés két manuális folyamatot mellőz:
- Active Directory egyszerű szolgáltatásnév létrehozása.
- Az Azure Terraform-szolgáltató változóinak konfigurálása.


## <a name="using-modules-and-providers"></a>Modulok és szolgáltatók használata

Az Azure Terraform-modulok hitelesítő adatokat igényelnek az Azure-erőforrások eléréséhez és módosításához. A Cloud Shell Terraform-moduljainak használatához adja hozzá a következő kódot:


```hcl
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

A Cloud Shell az `azurerm` szükséges értékeit környezeti változókon keresztül adja be a `terraform` parancssori parancsok használata során.

## <a name="other-cloud-shell-developer-tools"></a>Egyéb Cloud Shell fejlesztői eszközök

A fájlok és a rendszerhéj-állapotok megmaradnak az Azure Storage-ben a Cloud Shell-munkamenetek között. Az [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) használatával másolhatja és töltheti fel a fájlokat a helyi számítógépről a Cloud Shellbe.

Az Azure CLI elérhető a Cloud Shellben, és igen jól használható a konfigurációk tesztelésére és az elvégzett munka ellenőrzésére a `terraform apply` és `terraform destroy` parancsok végrehajtása után.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kisméretű virtuálisgép-fürt létrehozása a modul beállításjegyzékének használatával](terraform-create-vm-cluster-module.md)