---
title: Felhasználói virtuálisgép-lemezkép létrehozása az Azure Piactérhez
description: A felhasználói virtuálisgép-lemezkép létrehozásához szükséges lépések és hivatkozások listája.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 49db8c6717cd26886c3b49f8c99fdd2b08e8713d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278007"
---
# <a name="create-a-user-vm-image"></a>Felhasználói virtuális gép rendszerképének létrehozása

Ez a cikk ismerteti a két általános virtuális merevlemezről nem felügyelt lemezkép létrehozásához szükséges két általános lépés.  A hivatkozások végigvezetik az egyes lépésein: rögzítse a képet és általánosítsa a képet.


## <a name="capture-the-vm-image"></a>A virtuális gép képének rögzítése

A következő cikkben található utasításokat a hozzáférési megközelítésnek megfelelő virtuális gép rögzítéséről olvashatja:

-  PowerShell: [Nem felügyelt virtuálisgép-lemezkép létrehozása Azure virtuális gépről](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [Virtuális gép vagy virtuális merevlemez lemezképének létrehozása](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtuális gépek - Rögzítés](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>A virtuális gép lemezképének általánosítása

Mivel a felhasználói lemezképet egy korábban általános virtuális merevlemezről hozta létre, azt is általánossá kell tenni.  Ismét válassza ki a következő cikket, amely megfelel a hozzáférési mechanizmus.  (Lehet, hogy már általánossá tette a lemezt, amikor rögzítette.)

-  PowerShell: [A virtuális gép általánosítása](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [2. lépés: Virtuálisgép-lemezkép létrehozása](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtuális gépek - Általánosítás](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>További lépések

Ezután [hozzon létre egy tanúsítványt,](cpp-create-key-vault-cert.md) és tárolja egy új Azure Key Vaultban.  Ez a tanúsítvány a virtuális gép biztonságos Rendszer-eredményeképpen való kapcsolat létrehozásához szükséges.
