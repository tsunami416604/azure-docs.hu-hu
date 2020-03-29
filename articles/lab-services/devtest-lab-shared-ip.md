---
title: Megosztott IP-címek ismertetése az Azure DevTest Labs ben | Microsoft dokumentumok
description: Ismerje meg, hogy az Azure DevTest Labs hogyan használja a megosztott IP-címeket a tesztkörnyezet virtuális gépeinek eléréséhez szükséges nyilvános IP-címek minimalizálása érdekében.
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
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65236866"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>A megosztott IP-címek ismertetése az Azure DevTest Labs ben

Az Azure DevTest Labs lehetővé teszi, hogy a tesztkörnyezetvirtuális gépek ugyanazt a nyilvános IP-címet, hogy minimálisra csökkentsék az egyes tesztkörnyezet virtuális gépek eléréséhez szükséges nyilvános IP-címek száma.  Ez a cikk ismerteti, hogyan működnek a megosztott IP-k és a kapcsolódó konfigurációs beállítások.

## <a name="shared-ip-setting"></a>Megosztott IP-beállítás

Amikor létrehoz egy tesztkörnyezetet, az egy virtuális hálózat alhálózatában jön létre.  Alapértelmezés szerint ez az alhálózat úgy jön létre, hogy a **Megosztott nyilvános IP engedélyezése** érték *igen*.  Ez a konfiguráció egy nyilvános IP-címet hoz létre a teljes alhálózathoz.  A virtuális hálózatok és alhálózatok konfigurálásáról a [Virtuális hálózat konfigurálása az Azure DevTest Labs ben](devtest-lab-configure-vnet.md)című témakörben talál további információt.

![Új tesztkörnyezet-alhálózat](media/devtest-lab-shared-ip/lab-subnet.png)

Meglévő laborok esetén ezt a beállítást a **Konfiguráció és házirendek > virtuális hálózatok**lehetőséget választva engedélyezheti. Ezután válasszon ki egy virtuális hálózatot a listából, és válassza **a MEGOSZTOTT NYILVÁNOS IP engedélyezése lehetőséget** a kiválasztott alhálózathoz. Ezt a beállítást bármely tesztkörnyezetben is letilthatja, ha nem szeretne megosztani egy nyilvános IP-címet a tesztkörnyezet virtuális gépei között.

Az ebben a tesztkörnyezetben létrehozott virtuális gépek alapértelmezés szerint egy megosztott IP használatával.  A virtuális gép létrehozásakor ez a beállítás az **IP-cím konfigurációja**csoport **Speciális beállítások** lapján figyelhető meg.

![Új virtuális gép](media/devtest-lab-shared-ip/new-vm.png)

- **Megosztva:** A **megosztottként** létrehozott összes virtuális gép egy erőforráscsoportba (RG) kerül. Egyetlen IP-cím van rendelve az adott RG-hez, és az RG-ben lévő összes virtuális gép ezt az IP-címet fogja használni.
- **Nyilvános:** Minden létrehozott virtuális gép saját IP-címmel rendelkezik, és a saját erőforráscsoportban jön létre.
- **Privát:** Minden létrehozott virtuális gép privát IP-címet használ. Ehhez a virtuális géphez nem tud csatlakozni közvetlenül az internetről a Távoli asztal lal.

Amikor egy virtuális gép megosztott IP-engedélyezve van az alhálózathoz, DevTest Labs automatikusan hozzáadja a virtuális gép egy terheléselosztó, és hozzárendelegy TCP-port számát a nyilvános IP-cím, továbbítja az RDP-port a virtuális gép.  

## <a name="using-the-shared-ip"></a>A megosztott IP használata

- **Linux felhasználók:** SSH a virtuális gép az IP-cím vagy a teljesen minősített tartománynév, majd egy kettőspont, majd a port. Például az alábbi képen a virtuális géphez való csatlakozáshoz szükséges RDP-cím . `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`

  ![Példa virtuális gépre](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-felhasználók:** Válassza ki a **Csatlakozás** gombot az Azure Portalon egy előre konfigurált RDP-fájl letöltéséhez és a virtuális gép eléréséhez.

## <a name="next-steps"></a>További lépések

* [Laborszabályzatok definiálása az Azure DevTest Labsben](devtest-lab-set-lab-policy.md)
* [Virtuális hálózat konfigurálása az Azure DevTest Labs ben](devtest-lab-configure-vnet.md)





