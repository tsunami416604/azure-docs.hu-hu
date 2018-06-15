---
title: Klasszikus Linux virtuális gépek rendelkezésre állási készletek |} Microsoft Docs
description: Konfigurálja a rendelkezésre állási készlet egy új vagy meglévő Linux virtuális gép a klasszikus üzembe helyezési modellel, az Azure portál és az Azure PowerShell használatával.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: b8624315-beca-4ec7-8441-2e98b166b548
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: cynthn
ms.openlocfilehash: c5ca51f854e2358dc57286759d5b219e0d954a15
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842370"
---
# <a name="how-to-configure-an-availability-set-for-linux-virtual-machines-in-the-classic-deployment-model"></a>Rendelkezésre állási készlet Linux virtuális gépek a klasszikus üzembe helyezési modellel konfigurálása
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [rendelkezésre állási csoportok konfigurálása](../../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets) a Resource Manager üzembe helyezések.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [virtual-machines-common-classic-configure-availability](../../../../includes/virtual-machines-common-classic-configure-availability.md)]