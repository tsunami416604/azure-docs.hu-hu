---
title: Linux virtuális gépek az Azure CLI 1.0 Key Vault beállítása |} Microsoft Docs
description: Hogyan állítható be Key Vault az Azure Resource Manager virtuális gép az Azure CLI 1.0 való használatra.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: a9225429e878415334b0c8a66777902395606d63
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30911502"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>A virtuális gépek az Azure Resource Manager az Azure CLI 1.0 a Key Vault beállítása
Az Azure Resource Manager-készletben, a titkos kulcsokat vagy tanúsítványokat van modellezve a Key Vault erőforrás-szolgáltató által biztosított erőforrásokhoz. Az Azure Key Vault kapcsolatos további információkért lásd: [Mi az Azure Key Vault?](../../key-vault/key-vault-whatis.md) Ahhoz, hogy az Azure Resource Manager virtuális gépekkel, használandó kulcstároló a *EnabledForDeployment* be kell állítani a Key Vault tulajdonságot igaz értékre. Ehhez a különböző ügyfelek részére. Ez a cikk bemutatja, hogyan használható az Azure virtuális gépek Key Vault beállítása.

## <a name="cli-versions-to-complete-the-task"></a>A feladat befejezéséhez használható CLI-verziók
A feladat a következő parancssori felület verziók egyikével hajthatja végre.

- [Az Azure CLI 1.0](#quick-commands) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel (a cikk)
- [Azure CLI 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – a Resource Management üzemi modellhez tartozó parancssori felületek következő generációját képviseli.

## <a name="use-cli-10-to-set-up-key-vault"></a>Állítsa be a Key Vault CLI 1.0 használatával
Kulcstároló létrehozása a parancssori felület (CLI) használatával, lásd: [kezelése Key Vault parancssori felület használatával](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Parancssori felület 1.0 kell létrehoznia a key vault a központi telepítési házirend hozzárendelése előtt. Majd hozzárendelheti a házirendet a következő paranccsal:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>A sablonok segítségével Key Vault beállítása
Amikor egy sablont használ, meg kell adnia a `enabledForDeployment` tulajdonságot `true` a Key Vault erőforrás.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Más beállításokat konfigurálhatja, ha a sablonok használatával hozzon létre egy kulcstartót, lásd: [hozzon létre egy kulcstartót](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
