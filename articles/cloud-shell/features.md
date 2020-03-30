---
title: Az Azure Cloud Shell szolgáltatásai | Microsoft dokumentumok
description: Az Azure Cloud Shell funkcióinak áttekintése
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: damaerte
ms.openlocfilehash: 0aa71e4b78df8087093f183b146c525d2a8a0f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366240"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Funkciók & eszközök az Azure Cloud Shellhez

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

Az Azure Cloud `Ubuntu 16.04 LTS`Shell fut .

## <a name="features"></a>Szolgáltatások

### <a name="secure-automatic-authentication"></a>Biztonságos automatikus hitelesítés

A Cloud Shell biztonságosan és automatikusan hitelesíti a fiókhoz való hozzáférést az Azure CLI és az Azure PowerShell számára.

### <a name="home-persistence-across-sessions"></a>$HOME perzisztencia a munkamenetek között

A munkamenetek közötti fájlok megőrzéséhez a Cloud Shell végigvezeti önt egy Azure-fájlmegosztás csatolásán az első indításkor.
Miután elkészült, a Cloud Shell automatikusan csatolja a tárolót `$HOME\clouddrive`(csatlakoztatva) minden jövőbeli munkamenethez.
Emellett a `$HOME` könyvtár megmarad az Azure-fájlmegosztásban.Emellett a könyvtár is persistizált as an.img in your Azure File share.
A számítógépen `$HOME` és a gép állapotán kívül lévő fájlok nem maradnak meg a munkamenetek között. Ajánlott eljárásokat a titkos kulcsok, például az SSH-kulcsok tárolásakor. Az olyan szolgáltatások, mint [az Azure Key Vault, oktatóanyagokkal rendelkeznek a beállításhoz.](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites)

[További információ a felhőshellben továbbra is megőrzött fájlokról.](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Azure-meghajtó (Azure:)

A Cloud Shell Ben lévő PowerShell elindítja az Azure Drive(`Azure:`).
Az Azure-meghajtó lehetővé teszi az Azure-erőforrások, például a compute, a Network, a Storage stb.
Továbbra is használhatja a jól ismert [Azure PowerShell-parancsmagokkal](https://docs.microsoft.com/powershell/azure) ezeket az erőforrásokat, függetlenül attól, hogy a meghajtóban van-e.
Az Azure-erőforrásokon végrehajtott módosítások, akár közvetlenül az Azure Portalon, akár az Azure PowerShell-parancsmagokon keresztül, megjelennek az Azure-meghajtóban.  Futtathatja `dir -Force` az erőforrások frissítéséhez.

![](media/features-powershell/azure-drive.png)

### <a name="manage-exchange-online"></a>Exchange Online kezelése

A Cloud Shell ben lévő PowerShell az Exchange Online-modul privát buildjét tartalmazza.  Futtassa `Connect-EXOPSSession` az Exchange-parancsmagok levezetéséhez.

![](media/features-powershell/exchangeonline.png)

 Futtassa a `Get-Command -Module tmp_*` parancsot.
> [!NOTE]
> A modul nevének `tmp_`a programmal kell kezdődnie, ha ugyanazzal az előtaggal telepített modulokat, a parancsmagok is megjelennek. 

![](media/features-powershell/exchangeonlinecmdlets.png)

### <a name="deep-integration-with-open-source-tooling"></a>Mély integráció a nyílt forráskódú eszközökkel

A Cloud Shell előre konfigurált hitelesítést tartalmaz a nyílt forráskódú eszközökhöz, például a Terraform, az Ansible és a Chef InSpec eszközökhöz. Próbálja ki a példa forgatókönyvek.

## <a name="tools"></a>Eszközök

|Kategória   |Név   |
|---|---|
|Linux eszközök            |bash<br> zsh<br> Sh<br> tmux (angol)<br> Ásni<br>               |
|Azure-eszközök            |[Azure CLI](https://github.com/Azure/azure-cli) és [Klasszikus Azure CLI](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#writing-your-first-azcopy-command)<br> [Azure Functions parancssori felület](https://github.com/Azure/azure-functions-core-tools)<br> [Service Fabric parancssori felület](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)<br> [blobxfer](https://github.com/Azure/blobxfer)|
|Szövegszerkesztők           |kód (Cloud Shell szerkesztő)<br> vim<br> nano<br> emacs    |
|Verziókövetés         |git                    |
|Eszközök készítése            |make<br> maven<br> npm<br> Pip         |
|Containers             |[Docker Machine](https://github.com/docker/machine)<br> [Kubectl között](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Élén](https://github.com/kubernetes/helm)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|Adatbázisok              |MySQL kliens<br> PostgreSql kliens<br> [sqlcmd segédprogram](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Egyéb                  |iPython-ügyfél<br> [Felhőöntödei CLI](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible (Ansible)](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)<br> [Báb csavar](https://puppet.com/docs/bolt/latest/bolt.html)<br> [HashiCorp csomagoló](https://www.packer.io/)|

## <a name="language-support"></a>Nyelvi támogatás

|Nyelv   |Verzió   |
|---|---|
|.NET Core  |2.2.402       |
|Indítás         |1.9        |
|Java       |1.8        |
|Node.js    |8.16.0      |
|PowerShell |[7.0.0](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 és 3.5 (alapértelmezett)|

## <a name="next-steps"></a>További lépések
[Bash a Cloud Shell rövid útmutató](quickstart.md) <br>
[PowerShell a Cloud Shell rövid útmutatójában](quickstart-powershell.md) <br>
[További információ az Azure CLI-ről](https://docs.microsoft.com/cli/azure/) <br>
[További információ az Azure PowerShellről](https://docs.microsoft.com/powershell/azure/) <br>
