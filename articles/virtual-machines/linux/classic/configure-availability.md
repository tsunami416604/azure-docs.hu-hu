---
title: "Klasszikus Linux virtuális gépek rendelkezésre állási készletek |} Microsoft Docs"
description: "Konfigurálja a rendelkezésre állási készlet egy új vagy meglévő Linux virtuális gép a klasszikus üzembe helyezési modellel, az Azure portál és az Azure PowerShell használatával."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: b8624315-beca-4ec7-8441-2e98b166b548
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: cynthn
ms.openlocfilehash: 9b63913c00bfe64920560b826f9daa2998c5ba93
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="how-to-configure-an-availability-set-for-linux-virtual-machines-in-the-classic-deployment-model"></a>Rendelkezésre állási készlet Linux virtuális gépek a klasszikus üzembe helyezési modellel konfigurálása
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [rendelkezésre állási csoportok konfigurálása](../../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets) a Resource Manager üzembe helyezések.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [virtual-machines-common-classic-configure-availability](../../../../includes/virtual-machines-common-classic-configure-availability.md)]