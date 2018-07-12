---
title: Klasszikus Linuxos virtuális gépek rendelkezésre állási csoportok |} A Microsoft Docs
description: Konfigurálja a rendelkezésre állási csoport egy új vagy meglévő Linux rendszerű virtuális gépek a klasszikus üzemi modellben az Azure portal és az Azure PowerShell használatával.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38308089"
---
# <a name="how-to-configure-an-availability-set-for-linux-virtual-machines-in-the-classic-deployment-model"></a>Rendelkezésre állási csoport Linux rendszerű virtuális gépek a klasszikus üzemi modellben konfigurálása
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [rendelkezésre állási csoportjainak konfigurálása](../../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets) a Resource Managerben üzemelő példányokhoz.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

[!INCLUDE [virtual-machines-common-classic-configure-availability](../../../../includes/virtual-machines-common-classic-configure-availability.md)]