---
title: Az Azure Resource Manager sablonmintái
description: Az Azure Resource Manager-sablonminták keresése az Azure Container-példányok különböző konfigurációkban történő üzembe helyezéséhez
ms.topic: article
ms.date: 03/07/2019
ms.openlocfilehash: a8f3c81c539562a3c56e4822cf6e4df77d04928f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981646"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager-sablonok Azure-tárolópéldányokhoz

A következő mintasablonok tárolópéldányokat telepítenek különböző konfigurációkban.

A telepítési lehetőségeket a Központi telepítés című [szakaszban](#deployment) talál. Ha saját sablonokat szeretne létrehozni, az Azure Container Instances Resource Manager sablon referencia-sablonformátumra és a rendelkezésre álló tulajdonságokra [hivatkozik.][ref]

## <a name="sample-templates"></a>Példasablonok

| | |
|-|-|
| **Alkalmazások** ||
| [WordPress][app-wp] | WordPress webhelyet és annak MySQL adatbázisát egy tárolócsoportban hozza létre. A WordPress webhely tartalma és a MySQL-adatbázis egy Azure Files megosztáson marad. Is létrehoz egy alkalmazás átjáró, hogy ki a nyilvános hálózati hozzáférést a WordPress. |
| [MS NAV SQL Server és IIS rendszerrel][app-nav] | Egyetlen Windows-tárolót telepít teljes értékű, önálló Dynamics NAV / Dynamics 365 Business Central környezettel. |
| **Kötetek** ||
| [emptyDir][vol-emptydir] | Két Linux-tárolót telepít, amelyek egy emptyDir köteten osztoznak. |
| [gitRepo][vol-gitrepo] | Telepítegy Linux-tárolót, amely klónozza a GitHub-tárfelhasználta, és csatolja azt kötetként. |
| [titkos kód][vol-secret] | Linux-tárolót telepít titkos kötetként csatlakoztatott PFX-tanúsítványzal. |
| **Hálózat** ||
| [UDP-expozíciónak kitett tároló][net-udp] | Telepíti az UDP-portot elérhető Windows- vagy Linux-tárolót. |
| [Linux-tároló nyilvános IP-címvel][net-publicip] | Egyetlen, nyilvános IP-n keresztül elérhető Linux-tárolót telepít. |
| [Tárolócsoport üzembe helyezése virtuális hálózattal (előzetes verzió)][net-vnet] | Új virtuális hálózat, alhálózat, hálózati profil és tárolócsoport telepítése. |
| **Azure-erőforrások** ||
| [Azure Storage-fiók és fájlok megosztásának létrehozása][az-files] | Az Azure CLI egy tárolópéldányban egy tárfiók és egy Azure Files megosztás létrehozásához.

## <a name="deployment"></a>Környezet

Számos lehetősége van az erőforrások Erőforrás-kezelő sablonokkal történő üzembe helyezésére:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure-portál][deploy-portal]

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
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
[deploy-portal]: ../azure-resource-manager/templates/deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-rest]: ../azure-resource-manager/templates/deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
