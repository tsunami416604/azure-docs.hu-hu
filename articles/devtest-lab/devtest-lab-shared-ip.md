---
title: "Megosztott IP-címek az Azure DevTest Labs megértése |} Microsoft Docs"
description: "Ismerje meg, hogyan Azure DevTest Labs megosztott IP-címeket használó minimalizálása érdekében a nyilvános IP-címek, a labor virtuális gépek eléréséhez szükséges."
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: casoper
ms.openlocfilehash: 9f6e1980bf5ea5b41da98a135d89f1c5159921a7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Megosztott IP-címek az Azure DevTest Labs ismertetése

Az Azure DevTest Labs lehetővé teszi, hogy a labor virtuális gépeken megosztani az azonos nyilvános IP-címet a virtuális gépek egyes labor eléréséhez szükséges nyilvános IP-címek számának minimálisra csökkentése.  Ez a cikk ismerteti, hogyan megosztott IP-címek munkahelyi és a kapcsolódó beállítási lehetőségekre.

## <a name="shared-ip-setting"></a>A megosztott IP-beállítása

Ha a labor létrehozása a virtuális hálózati alhálózat helyezkedik el.  Alapértelmezés szerint ez az alhálózat jön létre **engedélyezése megosztott nyilvános IP-cím** beállítása *Igen*.  Ez a konfiguráció egyetlen nyilvános IP-cím, a teljes alhálózathoz tartozó hoz létre.  Virtuális hálózatok és alhálózatok konfigurálásával kapcsolatos további információkért lásd: [virtuális hálózat konfigurálása az Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Új labor alhálózat](media/devtest-lab-shared-ip/lab-subnet.png)

Meglévő labs, engedélyezheti a beállítás kiválasztásával **konfigurációs és házirendek > virtuális hálózatok**. Ezután egy virtuális hálózatot válasszon a listából, és válassza a **engedélyezése megosztott nyilvános IP-cím** a kijelölt alhálózathoz. Letilthatja ezt a beállítást, a laborban is, ha nem szeretné a nyilvános IP-cím megosztani a labor virtuális gépeken.

Bármely a labor beállítást, hogy egy megosztott IP-cím használatával létrehozott virtuális gépeket.  A virtuális gép létrehozásakor a beállítás figyelhető meg a **speciális beállítások** részen **IP-címkonfigurációt**.

![Új virtuális gép](media/devtest-lab-shared-ip/new-vm.png)

- **Megosztott:** létrehozandó virtuális gépeinek **megosztott** bekerülnek egy erőforráscsoportban (RG). Egy egyetlen IP-cím hozzá van rendelve, az adott Entitáshoz és a RG virtuális gépeinek fogja használni az IP-címet.
- **Nyilvános:** minden virtuális gép létrehozása a saját IP-címmel rendelkezik, és a saját erőforráscsoportban jön létre.
- **Saját:** minden virtuális Géphez létrejön egy privát IP-címet használja. Nem lehet csatlakozni a virtuális gép közvetlenül a távoli asztal az internetről.

Virtuális gép megosztott IP engedélyezve és az alhálózati ad hozzá, amikor a DevTest Labs automatikusan hozzáadja a virtuális gép egy terheléselosztó és rendeli hozzá a nyilvános IP-cím, az TCP-portszámot, a virtuális gép RDP-portjára továbbítja.  

## <a name="using-the-shared-ip"></a>A megosztott IP használatával

- **Linux-felhasználók:** SSH-kapcsolatot a virtuális Gépet, az IP-címét vagy teljesen minősített tartománynevét, majd egy kettőspontot, a port követ. Például az alábbi képen a RDP csatlakozni a virtuális gép címe `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Virtuális gép – példa](media/devtest-lab-shared-ip/vm-info.png)

- **Windows-felhasználók:** válassza ki a **Connect** gomb az Azure portál egy előre konfigurált RDP-fájl letöltésére, és a virtuális gép eléréséhez.

## <a name="next-steps"></a>Következő lépések

* [Labor házirendeket definiálhat az Azure DevTest Labs szolgáltatásban](devtest-lab-set-lab-policy.md)
* [A Azure DevTest Labs szolgáltatásban virtuális hálózat konfigurálása](devtest-lab-configure-vnet.md)





