---
title: Az Azure Resource Manager-sablonminták – Azure Container Instances szolgáltatásban
description: Az Azure Resource Manager-sablonminták az Azure Container Instances szolgáltatásban
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: danlep
ms.openlocfilehash: e825e0bdd08db0e9c1b51c09859aba2e7c716f91
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48856465"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances szolgáltatásban az Azure Resource Manager-sablonokkal

Az alábbi mintasablonok üzembe különböző konfigurációkban a container Instances szolgáltatásban.

A központi telepítési lehetőségekről a [üzembe helyezési](#deployment) szakaszban. Ha szeretné-e a saját sablonok, az Azure Container Instances létrehozása [Resource Manager sablonreferenciája] [ ref] részletesen sablon formátuma és a rendelkezésre álló tulajdonságok.

## <a name="sample-templates"></a>Mintasablonok

| | |
|-|-|
| **Alkalmazások** ||
| [A WordPress][app-wp] | Létrehoz egy tárolópéldányt egy WordPress-webhely létrehozása és a MySQL-adatbázis. A WordPress-webhely tartalmát és a MySQL-adatbázis tárolja az Azure-fájlok megosztása. |
| [Az SQL Server- és IIS MS NAV][app-nav] | Üzembe helyez egy egyetlen Windows-tárolóban egy teljes körű önálló Dynamics NAV vagy Dynamics 365 Business Central-környezetben. |
| **Kötetek** ||
| [az emptyDir][vol-emptydir] | Az emptyDir köteten osztozó két Linux-tárolók üzembe helyezése. |
| [A GitRepo][vol-gitrepo] | Üzembe helyez egy Linux-tároló, amely klónozza a GitHub-tárházba, és csatlakoztatja, kötetként. |
| [Titkos kulcs][vol-secret] | Üzembe helyezett egy Linux-tárolót egy PFX-tanúsítványt, mint titkos kötet csatlakoztatva van. |
| **Hálózat** ||
| [UDP-elérhetővé tett tárolók][net-udp] | Üzembe helyez egy Windows vagy Linux-alapú tárolót, amely közzéteszi az UDP-port. |
| [Nyilvános IP-Címmel rendelkező Linux-tárolóban][net-publicip] | Üzembe helyez egy egyetlen nyilvános IP-cím-en keresztül elérhető Linux-tárolót. |
| **Azure-erőforrások** ||
| [Azure Storage-fiók létrehozása és fájlok megosztása][az-files] | Tárolópéldány az Azure CLI használatával hozzon létre egy tárfiókot és egy Azure-fájlmegosztást.

## <a name="deployment"></a>Környezet

Resource Manager-sablonok az erőforrások üzembe helyezésének számos lehetősége van:

[Az Azure CLI][deploy-cli]

[Azure PowerShell-lel][deploy-powershell]

[Az Azure Portalon][deploy-portal]

[REST API-VAL][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups