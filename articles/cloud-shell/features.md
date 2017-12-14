---
title: "Azure Cloud rendszerhéj funkciói bash |} Microsoft Docs"
description: "Az Azure felhőalapú rendszerhéj Bash funkcióinak áttekintése"
services: Azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: juluk
ms.openlocfilehash: 24b4f92a0a575939d3cf10fc8ec2fe877316f82c
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="features--tools-for-bash-in-azure-cloud-shell"></a>Szolgáltatások & Azure Cloud rendszerhéj Bash eszközei

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Szolgáltatások & eszközei [PowerShell](features-powershell.md) is rendelkezésre áll.

A felhő rendszerhéj felhőkörnyezetben bash `Ubuntu 16.04 LTS`.

## <a name="features"></a>Szolgáltatások

### <a name="secure-automatic-authentication"></a>Biztonságos automatikus hitelesítés

A felhő rendszerhéj bash biztonságosan és automatikusan hitelesíti felhasználóifiók-hozzáférés az Azure CLI 2.0.

### <a name="ssh-into-azure-linux-virtual-machines"></a>SSH-ból az Azure Linux virtuális gépek

Linux virtuális gép létrehozása az Azure CLI 2.0 létrehozhat egy alapértelmezett SSH-kulcs és elhelyezheti a `$Home` könyvtár. SSH elhelyezéséhez kulcsoknak `$Home` lehetővé teszi, hogy közvetlenül SSH-kapcsolatok Azure Linux virtuális gépekhez közvetlenül a felhő rendszerhéj. Kulcsok tartják acc_<user>.img a fájlmegosztáson, ajánlott eljárások használatával, vagy a megosztás hozzáférés a fájlmegosztáshoz vagy kulcsok használata.

### <a name="home-persistence-across-sessions"></a>$Home adatmegőrzési munkamenetei között

A munkamenetek között a fájlok továbbra is fennáll, felhőalapú rendszerhéj bemutatja, hogyan csatlakoztatása az Azure fájlmegosztások első indításkor.
Ezt követően felhő rendszerhéj automatikusan elvégzi a tároló (mint csatlakoztatott `$Home\clouddrive`) minden későbbi.
Ezenkívül a felhőalapú rendszerhéj Bash a `$Home` directory, az Azure fájlmegosztásban egy .img megőrződjenek.
Fájlok kívüli `$Home` és a gép állapota nem maradnak meg a munkamenetek között.

[Ismerje meg a felhő rendszerhéj Bash fájlok tárolásakor.](persisting-shell-storage.md)

## <a name="tools"></a>Eszközök

|Kategória   |Név   |
|---|---|
|Linux-eszközök            |Bash<br> SH<br> tmux<br> Dig<br>               |
|Azure-eszközök            |[Az Azure CLI 2.0](https://github.com/Azure/azure-cli) és [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard) <br> [Service Fabric parancssori felület](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) <br> [blobxfer](https://github.com/Azure/blobxfer#blobxfer) |
|A szerkesztő szövege           |VIM<br> nano<br> emacs       |
|A verziókövetési rendszerrel         |git                    |
|Buildet            |Ellenőrizze<br> maven<br> npm<br> a pip         |
|Tárolók             |[A docker parancssori felület](https://github.com/docker/cli)/[Docker gép](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [DC/OS PARANCSSORI FELÜLET](https://github.com/dcos/dcos-cli)         |
|Adatbázisok              |MySQL-ügyfél<br> PostgreSql-ügyfél<br> [Az Sqlcmd segédprogram használatával](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [MSSQL-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Egyéb                  |iPython ügyfél<br> [Felhő Foundry parancssori felület](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)|

## <a name="language-support"></a>Nyelvi támogatás

|Nyelv   |Verzió   |
|---|---|
|.NET       |2.0.0       |
|Indítás         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|PowerShell |[6.0 (béta)](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 és 3.5-ös (alapértelmezett)|

## <a name="next-steps"></a>Következő lépések
[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md) <br>
[További tudnivalók az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/)
