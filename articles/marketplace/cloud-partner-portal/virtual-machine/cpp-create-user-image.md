---
title: Felhasználói virtuálisgép-rendszerkép létrehozása az Azure Marketplace-en
description: A felhasználói virtuálisgép-rendszerkép létrehozásához szükséges lépéseket és hivatkozásokat sorolja fel.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: e63f09dc538c5e66b244826cf3b5f92ac388b6a9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818699"
---
# <a name="create-a-user-vm-image"></a>Felhasználói virtuális gép rendszerképének létrehozása

Ez a cikk a nem felügyelt rendszerképek általánosított virtuális merevlemezről történő létrehozásához szükséges két általános lépést ismerteti.  A hivatkozásokat a következő lépésekben találja: rögzítse a lemezképet, és általánosítsa a lemezképet.


## <a name="capture-the-vm-image"></a>A virtuális gép rendszerképének rögzítése

Az alábbi cikk utasításait követve rögzítheti a virtuális gépet, amely megfelel a hozzáférési módszernek:

-  PowerShell: nem [felügyelt virtuális gép rendszerképének létrehozása Azure-beli virtuális](../../../virtual-machines/windows/capture-image-resource.md) gépről
-  Azure CLI: [virtuális gép vagy VHD rendszerképének létrehozása](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines – rögzítés](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>A virtuálisgép-rendszerkép általánosítása

Mivel egy korábban általánosított VHD-fájlból generálta a felhasználói képet, azt is általánosítva kell megadnia.  Ezután válassza ki a következő cikket, amely megfelel az Ön hozzáférési mechanizmusának.  (Lehetséges, hogy már elvégezte a lemez általános beolvasását.)

-  PowerShell: [a virtuális gép általánosítása](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [2. lépés: virtuális gép rendszerképének létrehozása](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines – általánosítás](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>További lépések

Ezután létre fog [hozni egy tanúsítványt](cpp-create-key-vault-cert.md) , és azt egy új Azure Key Vault tárolja.  Ez a tanúsítvány szükséges a virtuális géphez való biztonságos WinRM-kapcsolatok létrehozásához.
