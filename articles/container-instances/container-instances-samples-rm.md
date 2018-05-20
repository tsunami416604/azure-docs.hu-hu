---
title: Az Azure Resource Manager sablon minták – Azure tároló példányok
description: Azure Resource Manager-sablon minták Azure tároló-példányok
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure-tároló példányok Azure Resource Manager-sablonok

Az alábbi minta sablonok tároló példányok különféle konfigurációk telepítése.

Telepítési lehetőségek, tekintse meg a [telepítési](#deployment) szakasz. Ha szeretné-e saját sablonokat, az Azure-tároló példányok létrehozása [Resource Manager-sablonra való hivatkozást] [ ref] sablon formázása és a rendelkezésre álló tulajdonságok részletezi.

## <a name="sample-templates"></a>A minta-sablonok

| | |
|-|-|
| **Alkalmazások** ||
| [Wordpress][app-wp] | Létrehoz egy WordPress-webhelyek és a MySQL-adatbázis egy tároló-példányt. A WordPress-webhely tartalmát és a MySQL-adatbázis tárolja a rendszer, az Azure-fájlokat megosztani. |
| [Az SQL Server és az IIS MS NAV][app-nav] | Központilag telepíti egy Windows-tárolóhoz egy teljes funkciókészletet kínáló önálló Dynamics NAV vagy Dynamics 365 üzleti központi környezetben. |
| **Kötetek** ||
| [emptyDir][vol-emptydir] | Két Linux-tárolók egy emptyDir köteten osztozó telepíti. |
| [GitRepo][vol-gitrepo] | A Linux-tároló, amely egy GitHub-tárház klónokat, és azt csatlakoztatja a kötetként telepíti. |
| [titkos kulcs][vol-secret] | Telepíti a Linux-tárolók egy PFX-tanúsítvány titkos kötetként csatlakoztatva a. |
| **Hálózat** ||
| [UDP-kitett tároló][net-udp] | Telepíti a Windows vagy Linux tároló, amely elérhetővé teszi az UDP-port. |
| [Linux-tárolóban nyilvános IP-cím][net-publicip] | Egy nyilvános IP-cím keresztül érhető el egyetlen Linux tárolót telepíti. |
| **Azure-erőforrások** ||
| [Azure Storage-fiók létrehozása és a fájlok megosztása][az-files] | Egy tároló-példányt az Azure parancssori felület használatával hozzon létre egy tárfiókot és az Azure-fájlok megosztások.

## <a name="deployment"></a>Környezet

A Resource Manager-sablonok erőforrásokat üzembe helyezi több lehetőség közül választhat:

[Az Azure parancssori felület][deploy-cli]

[Az Azure PowerShell][deploy-powershell]

[Azure-portálon][deploy-portal]

[REST API-N][deploy-rest]

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