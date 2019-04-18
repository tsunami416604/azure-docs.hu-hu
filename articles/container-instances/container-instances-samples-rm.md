---
title: Az Azure Resource Manager-sablonminták – Azure Container Instances szolgáltatásban
description: Az Azure Resource Manager-sablonminták az Azure Container Instances szolgáltatásban
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 3d73d05c64f4b4867c69a15089c19ab8c320b9a8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006476"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances szolgáltatásban az Azure Resource Manager-sablonokkal

Az alábbi mintasablonok üzembe különböző konfigurációkban a container Instances szolgáltatásban.

A központi telepítési lehetőségekről a [üzembe helyezési](#deployment) szakaszban. Ha szeretné-e a saját sablonok, az Azure Container Instances létrehozása [Resource Manager sablonreferenciája] [ ref] részletesen sablon formátuma és a rendelkezésre álló tulajdonságok.

## <a name="sample-templates"></a>Példasablonok

| | |
|-|-|
| **Alkalmazások** ||
| [WordPress][app-wp] | WordPress-webhelyek és a MySQL-adatbázis egy tárolócsoportot hoz létre. A WordPress-webhely tartalmát és a MySQL-adatbázis tárolja az Azure-fájlok megosztása. Is létrehoz egy application gateway nyilvános hálózati hozzáférés WordPress elérhetővé. |
| [Az SQL Server- és IIS MS NAV][app-nav] | Üzembe helyez egy egyetlen Windows-tárolóban egy teljes körű önálló Dynamics NAV vagy Dynamics 365 Business Central-környezetben. |
| **Kötetek** ||
| [az emptyDir][vol-emptydir] | Az emptyDir köteten osztozó két Linux-tárolók üzembe helyezése. |
| [gitRepo][vol-gitrepo] | Üzembe helyez egy Linux-tároló, amely klónozza a GitHub-tárházba, és csatlakoztatja, kötetként. |
| [Titkos kulcs][vol-secret] | Üzembe helyezett egy Linux-tárolót egy PFX-tanúsítványt, mint titkos kötet csatlakoztatva van. |
| **Hálózat** ||
| [UDP-elérhetővé tett tárolók][net-udp] | Üzembe helyez egy Windows vagy Linux-alapú tárolót, amely közzéteszi az UDP-port. |
| [Nyilvános IP-Címmel rendelkező Linux-tárolóban][net-publicip] | Üzembe helyez egy egyetlen nyilvános IP-cím-en keresztül elérhető Linux-tárolót. |
| [Egy tárolócsoport és a virtuális hálózat (előzetes verzió) üzembe helyezése][net-vnet] | Üzembe helyez egy új virtuális hálózat, alhálózat, a hálózati profil és a tárolócsoport. |
| **Azure-erőforrások** ||
| [Azure Storage-fiók létrehozása és fájlok megosztása][az-files] | Tárolópéldány az Azure CLI használatával hozzon létre egy tárfiókot és egy Azure-fájlmegosztást.

## <a name="deployment"></a>Környezet

Resource Manager-sablonok az erőforrások üzembe helyezésének számos lehetősége van:

[Az Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Az Azure Portalon][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
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
