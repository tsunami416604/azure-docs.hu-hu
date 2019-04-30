---
title: Megismerheti a közös IP-címek az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogyan Azure DevTest Labs megosztott IP-címet használ a nyilvános IP-címek a labor virtuális gépek eléréséhez szükséges minimalizálása érdekében.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e7080901118dde33ed07c8a80f254b9b0d2e221c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623007"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Megismerheti a közös IP-címek az Azure DevTest Labs szolgáltatásban

Az Azure DevTest Labs lehetővé teszi, hogy a labor virtuális gépeken megosztása a nyilvános IP-címek a virtuális gépek egyéni tesztkörnyezet elérése szükséges a lehető legkevesebb azonos nyilvános IP-cím.  Ez a cikk bemutatja, hogyan megosztott IP-címek munkahelyi és a kapcsolódó konfigurációs beállításokat.

## <a name="shared-ip-setting"></a>A megosztott IP-beállításai

Labor létrehozása, ha egy virtuális hálózat alhálózatán található.  Alapértelmezés szerint ez az alhálózat jön létre a **engedélyezése megosztott nyilvános IP-cím** beállítása *Igen*.  Ez a konfiguráció egyetlen nyilvános IP-címet, a teljes alhálózathoz tartozó hoz létre.  Virtuális hálózatok és alhálózatok konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-vnet.md).

![Új labor-alhálózat](media/devtest-lab-shared-ip/lab-subnet.png)

Meglévő labs engedélyezheti ezt a lehetőséget választva **Konfigurace a zásady > virtuális hálózat**. Ezután válasszon ki egy virtuális hálózatot a listából, és válassza a **engedélyezése megosztott nyilvános IP-cím** kijelölt alhálózat. Ha nem szeretne nyilvános IP-cím megosztása a labor virtuális gépeken is letilthatja ezt a beállítást minden olyan tesztkörnyezetben.

Létrehozott összes virtuális gépet a labor alapértelmezett egy megosztott IP-cím használatával.  A virtuális gép létrehozásakor ez a beállítás be kell tartani a **speciális beállítások** panel **IP-címének konfigurációja**.

![Új virtuális gép](media/devtest-lab-shared-ip/new-vm.png)

- **Megosztva:** Létrehozott összes virtuális gép **megosztott** vannak elhelyezve, egy erőforráscsoportot (RG). Egyetlen IP-címet, amely az RG és az összes virtuális gép az rg használnak az IP-cím van hozzárendelve.
- **Nyilvános:** Minden virtuális gép létrehozása a saját IP-címmel rendelkezik, és a saját erőforráscsoport jön létre.
- **Privát:** Minden létrehozott virtuális gép egy magánhálózati IP-címet használ. Nem tud csatlakozni a virtuális gép közvetlenül az internetről a távoli asztalon keresztül.

Amikor egy virtuális Gépet a megosztott IP-cím engedélyezve van hozzáadva az alhálózathoz, a DevTest Labs automatikusan ad hozzá egy terheléselosztót a virtuális gép, és hozzárendeli egy nyilvános IP-cím, a TCP-port száma a virtuális gép RDP-portra továbbítja.  

## <a name="using-the-shared-ip"></a>A megosztott IP-cím használatával

- **Linux-felhasználók:** Ssh-KAPCSOLATOT a virtuális gép IP-címét vagy teljesen minősített tartománynevét, majd kettőspontot, majd a port használatával. Ha például az alábbi képen az RDP-cím a virtuális Géphez való csatlakozáshoz van `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Virtuális gép példájához](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-felhasználók:** Válassza ki a **Connect** gombra egy előre konfigurált RDP-fájl letöltése és eléri a virtuális Gépet az Azure Portalon.

## <a name="next-steps"></a>További lépések

* [Laborszabályzatok definiálása az Azure DevTest Labs szolgáltatásban](devtest-lab-set-lab-policy.md)
* [Virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-vnet.md)





