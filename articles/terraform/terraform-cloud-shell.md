---
title: Oktatóanyag – a Terraform Azure Cloud Shell konfigurálása
description: A Terraform Azure Cloud Shell-lel való használatával egyszerűsíthető a hitelesítés és a sablonok konfigurációja.
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 1259d5004bd547e33f65571333b6d0721d1253c0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74078729"
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
