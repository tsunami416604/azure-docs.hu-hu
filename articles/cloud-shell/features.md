---
title: Az Azure Cloud Shell funkciói |} A Microsoft Docs
description: Az Azure Cloud Shellben lévő Bash funkcióinak áttekintése
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: juluk
ms.openlocfilehash: 9a6f7aeea799787becf0bb99cdfa08bed4e9d355
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48867459"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkciók és eszközök az Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Az Azure Cloud Shell futtat `Ubuntu 16.04 LTS`.

## <a name="features"></a>Szolgáltatások

### <a name="secure-automatic-authentication"></a>Automatikus hitelesítésre

A cloud Shell biztonságosan és automatikusan végzi a hitelesítést felhasználóifiók-hozzáférés az Azure CLI és az Azure PowerShell.

### <a name="home-persistence-across-sessions"></a>$HOME megőrzése munkamenetek között

A fájlok munkamenetek közötti megtartása, a Cloud Shell végigvezeti csatlakoztatása egy Azure-fájlmegosztást az alkalmazás első indításakor.
Ha befejeződött, a Cloud Shell automatikusan elvégzi a storage (meghajtóként csatlakoztatott `$HOME\clouddrive`) minden későbbi munkamenet során.
Ezenkívül a `$HOME` könyvtár, egy .img az Azure-fájlmegosztás a rendszer megőrzi.
Fájlok kívül `$HOME` és a gép állapota nem megmaradnak a munkamenetek között. Ajánlott eljárások akkor használja, ha az SSH-kulcsok például a titkos kulcsok tárolására. Szolgáltatásokat, mint például [Azure Key Vault rendelkezik a telepítő oktatóanyagok](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[További információ a Cloud Shellben fájlokat.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-meghajtó (Azure:)

A Cloud Shellbeli PowerShell elindul az Azure-meghajtó (`Azure:`).
Az Azure-meghajtó lehetővé teszi, hogy könnyen felderítés és az Azure erőforrások, például számítási, hálózati, tárolási stb. fájlrendszer navigációs hasonló navigációs.
Továbbra is a megszokott [Azure PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure) a meghajtó a függetlenül kezelhetők.
Az Azure-erőforrásokat, vagy közvetlenül az Azure Portalon vagy Azure PowerShell-parancsmagok használatával végrehajtott módosítások megjelennek az Azure-meghajtó.  Futtathat `dir -Force` az erőforrások frissítése.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>Szoros integráció nyílt forráskódú eszközök

A cloud Shell nyílt forráskódú eszközök, például a Terraform, az Ansible és az InSpec Chef előre konfigurált hitelesítést. Próbálja ki az a példában forgatókönyvek.

## <a name="tools"></a>Eszközök

|Kategória   |Name (Név)   |
|---|---|
|Linux rendszerű eszközök            |A bash<br> a zsh<br> SH<br> tmux<br> tájékozódjon<br>               |
|Azure-eszközök            |[Az Azure CLI](https://github.com/Azure/azure-cli) és [Azure klasszikus parancssori felület](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Service Fabric parancssori felület](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Szövegszerkesztő           |VIM<br> a nano<br> emacs<br> Kód       |
|Verziókövetés         |git                    |
|Eszközök létrehozása            |Győződjön meg arról<br> Maven<br> npm<br> a pip         |
|Containers             |[Docker CLI](https://github.com/docker/cli)/[a Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS PARANCSSORI FELÜLETTEL](https://github.com/dcos/dcos-cli)         |
|Adatbázisok              |MySQL-ügyfél<br> PostgreSql-ügyfél<br> [Az Sqlcmd segédprogram](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Egyéb                  |iPython ügyfél<br> [A cloud Foundry parancssori felület](https://github.com/cloudfoundry/cli)<br> [A Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Az Ansible](https://www.ansible.com/microsoft-azure)<br> [A Chef InSpec](https://www.chef.io/inspec/)|

## <a name="language-support"></a>Nyelvi támogatás

|Nyelv   |Verzió   |
|---|---|
|.NET Core  |2.0.0       |
|Indítás         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.1.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7-es és 3.5-ös (alapértelmezett)|

## <a name="next-steps"></a>További lépések
[A bash Cloud Shell rövid](quickstart.md) <br>
[A PowerShell Cloud Shell rövid](quickstart-powershell.md) <br>
[További tudnivalók az Azure CLI 2.0 használatával](https://docs.microsoft.com/cli/azure/) <br>
[További tudnivalók az Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
