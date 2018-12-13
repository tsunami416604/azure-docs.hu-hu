---
title: Felhasználói Virtuálisgép-lemezkép létrehozása az Azure Marketplace-en |} A Microsoft Docs
description: A lépéseket és a egy felhasználói Virtuálisgép-lemezkép létrehozásához szükséges hivatkozásokat sorolja fel.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/29/2018
ms.author: pbutlerm
ms.openlocfilehash: bf87856dc28e83fb1308f20613338b9bbfd8f896
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53197113"
---
# <a name="create-a-user-vm-image"></a>Felhasználói Virtuálisgép-lemezkép létrehozása

Ez a cikk ismerteti a két, nem felügyelt rendszerkép létrehozása általános virtuális merevlemezből szükséges általános lépéseket.  Végigvezeti az egyes lépések biztosított hivatkozások: a lemezképet rögzítené és a lemezkép általánossá tétele.


## <a name="capture-the-vm-image"></a>A virtuális gép lemezképének rögzítése

A rögzítés a virtuális gép, amely megfelel a hozzáférési módszer a következő cikkben szereplő útmutatások segítségével:

-  PowerShell: [Egy nem felügyelt Virtuálisgép-rendszerkép létrehozása Azure virtuális gépből](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Hogyan hozhat létre virtuális gépet vagy virtuális merevlemez képe](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtuális gépek – rögzítése](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>A Virtuálisgép-lemezkép általánossá tétele

A felhasználói lemezkép korábban általános virtuális merevlemezből hozott létre, mert azt kell is általánosítva van.  Újra válassza ki a következő cikkben, amely megfelel a hozzáférés-mechanizmus.  (, Előfordulhat, hogy rendelkezik már általánosítva a lemezt rögzített, amikor.)

-  PowerShell: [A virtuális gép általánosítása](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [2. lépés: Virtuális gép rendszerképének létrehozása](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtuális gépek – Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>További lépések

A rendszer ezután [hozzon létre egy tanúsítványt](cpp-create-key-vault-cert.md) és a egy új Azure Key Vaultban tárolhatók.  Ez a tanúsítvány szükség a Rendszerfelügyeleti webszolgáltatások biztonságos kapcsolatot a virtuális géphez.
