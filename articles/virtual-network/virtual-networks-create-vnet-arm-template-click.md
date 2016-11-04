---
title: Virtuális hálózat létrehozása ARM-sablonnal | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre virtuális hálózatot ARM-sablonnal | Resource Manager.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial

---
# Virtuális hálózat létrehozása ARM-sablonnal
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a dokumentum azt írja le, hogy Resource Manager üzemi modellel hogyan hozhatók létre virtuális hálózatok. A [virtuális hálózat létrehozása a klasszikus üzembe helyezési modellel](virtual-networks-create-vnet-classic-pportal.md) is lehetséges.

Ismertetjük, hogy miként tölthet le és módosíthat egy meglévő ARM-sablont a GitHubból, és miként helyezheti üzembe a sablont a GitHubból, PowerShellből és az Azure parancssori felületéről.

Ha közvetlenül a GitHubból helyezi üzembe az ARM-sablont, változtatások nélkül, ugorjon a [sablon telepítése a githubból](#deploy-the-arm-template-by-using-click-to-deploy) lépésre.

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[!INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]

<!--HONumber=Sep16_HO4-->


