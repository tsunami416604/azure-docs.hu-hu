---
title: Felhasználói Virtuálisgép-lemezkép létrehozása az Azure Marketplace-en
description: A lépéseket és a egy felhasználói Virtuálisgép-lemezkép létrehozásához szükséges hivatkozásokat sorolja fel.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 0005ab517d38903b87889b67449569495e396265
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938333"
---
# <a name="create-a-user-vm-image"></a>Felhasználói virtuális gép rendszerképének létrehozása

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
