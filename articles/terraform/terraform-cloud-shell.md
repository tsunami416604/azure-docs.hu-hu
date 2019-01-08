---
title: A Terraform használata az Azure Cloud Shell-lel
description: A Terraform Azure Cloud Shell-lel való használatával egyszerűsíthető a hitelesítés és a sablonok konfigurációja.
services: terraform
ms.service: terraform
keywords: terraform, devops, méretezési csoport, virtuális gép, hálózat, tároló, modulok
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/19/2017
ms.openlocfilehash: af71cf07ea12e605f57d34b530fcbcd61838128b
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54074532"
---
# <a name="terraform-cloud-shell-development"></a>Cloud Shell-alapú Terraform-fejlesztések 

A Terraform nagyszerűen használható a Bash-parancssorból, például a macOS Terminal vagy a Bash on Windows vagy Linux felületekről. A Terraform-konfigurációk [Azure Cloud Shell](/azure/cloud-shell/overview) Bash-felületéről való futtatása néhány egyedi előnyt biztosít a fejlesztési ciklus felgyorsításához.

Ez a fogalmakat ismertető cikk azokat a Cloud Shell-funkciókat ismerteti, amelyekkel Terraform-szkriptek írhatók az Azure-ban való üzembe helyezéshez.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="automatic-credential-configuration"></a>Hitelesítő adatok automatikus konfigurálása

A Terraform telepítve van, és azonnal elérhető a Cloud Shellben. A Cloud Shellbe bejelentkezve a Terraform-szkriptek az Azure-ban hitelesítenek, így további konfiguráció nélkül felügyelhető az infrastruktúra. Az automatikus hitelesítésnek köszönhetően nem kell manuálisan létrehozni egy Active Directory-szolgáltatásnevet, illetve konfigurálni az Azure Terraform szolgáltatói változóit.


## <a name="using-modules-and-providers"></a>Modulok és szolgáltatók használata

Az Azure Terraform-moduloknak hitelesítő adatokra van szükségük az Azure-előfizetésben lévő erőforrások eléréséhez és módosításához. A Cloud Shell használata esetén az Azure Terraform-modulok Cloud Shellben való használatához egészítse ki a szkripteket a következő kóddal:

```tf
# Configure the Microsoft Azure Provider
provider "azurerm" {
}
```

A Cloud Shell az `azurerm` szükséges értékeit környezeti változókon keresztül adja be a `terraform` parancssori parancsok használata során.

## <a name="other-cloud-shell-developer-tools"></a>Egyéb Cloud Shell fejlesztői eszközök

A fájlok és a rendszerhéj-állapotok megmaradnak az Azure Storage-ben a Cloud Shell-munkamenetek között. Az [Azure Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer) használatával másolhatja és töltheti fel a fájlokat a helyi számítógépről a Cloud Shellbe.

Az Azure CLI elérhető a Cloud Shellben, és igen jól használható a konfigurációk tesztelésére és az elvégzett munka ellenőrzésére a `terraform apply` és `terraform destroy` parancsok végrehajtása után.


## <a name="next-steps"></a>További lépések

[Kisméretű virtuálisgép-fürt létrehozása a modul-beállításjegyzék használatával](terraform-create-vm-cluster-module.md)
[Kisméretű virtuálisgép-fürt létrehozása egyéni HCL használatával](terraform-create-vm-cluster-with-infrastructure.md)
