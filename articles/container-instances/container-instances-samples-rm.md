---
title: Azure Resource Manager sablon mintái
description: Azure Container Instances különböző konfigurációkban való üzembe helyezéséhez Azure Resource Manager sablon mintáinak megkeresése
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: ddea823525002662363d61a795f34d6094c897d0
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533445"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Container Instances Azure Resource Manager sablonjai

A következő sablonfájlok különböző konfigurációkban helyezik üzembe a Container instances-példányokat.

Az üzembe helyezési lehetőségekért tekintse meg a [központi telepítés](#deployment) című szakaszt. Ha saját sablonokat szeretne létrehozni, a Azure Container Instances [Resource Manager-sablon dokumentációja][ref] és a rendelkezésre álló tulajdonságok.

## <a name="sample-templates"></a>Példasablonok

| | |
|-|-|
| **Alkalmazások** ||
| [WordPress][app-wp] | Létrehoz egy WordPress-webhelyet és a MySQL-adatbázisát egy tároló csoportban. A WordPress-webhely tartalma és a MySQL-adatbázis egy Azure Files megosztásban marad. Létrehoz egy Application Gateway-t is, amely nyilvános hálózati hozzáférést tesz elérhetővé a WordPress számára. |
| [MS NAV SQL Server és IIS][app-nav] | Egyetlen Windows-tárolót helyez üzembe a teljes funkcionalitású, önálló Dynamics NAV/Dynamics 365 üzleti központi környezettel. |
| **Kötetek** ||
| [emptyDir][vol-emptydir] | Két Linux-tárolót telepít, amelyek osztoznak egy emptyDir-köteten. |
| [Gitrepo típusú][vol-gitrepo] | Üzembe helyez egy GitHub-tárházat klónozott Linux-tárolót, és kötetként csatlakoztatja azt. |
| [titkos][vol-secret] | Egy Linux-tárolót helyez üzembe egy titkos kötetként csatlakoztatott PFX-tanúsítványsal. |
| **Hálózat** ||
| [UDP-vel feltett tároló][net-udp] | Egy olyan Windows vagy Linux rendszerű tároló üzembe helyezése, amely egy UDP-portot tesz elérhetővé. |
| [Linux-tároló nyilvános IP-címmel][net-publicip] | Egyetlen linuxos tárolót helyez üzembe egy nyilvános IP-címen keresztül. |
| [Tároló-csoport üzembe helyezése virtuális hálózattal (előzetes verzió)][net-vnet] | Üzembe helyez egy új virtuális hálózatot, alhálózatot, hálózati profilt és tároló csoportot. |
| **Azure-erőforrások** ||
| [Azure Storage-fiók és-fájlmegosztás létrehozása][az-files] | Az Azure CLI-t használja egy tároló példányban egy Storage-fiók és egy Azure Files-megosztás létrehozásához.

## <a name="deployment"></a>Környezet

Az erőforrások Resource Manager-sablonokkal való üzembe helyezésének számos lehetősége van:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure Portal][deploy-portal]

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
