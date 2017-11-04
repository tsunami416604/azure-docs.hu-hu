---
title: "Hozzon létre egy virtuálisgép-méretezési csoportban az Azure portál használatával |} Microsoft Docs"
description: "Telepítse a méretezési csoportok Azure-portál használatával."
keywords: "Virtuálisgép-méretezési csoportok"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: madhana
editor: tysonn
tags: azure-resource-manager
ms.assetid: 9c1583f0-bcc7-4b51-9d64-84da76de1fda
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc52368a1a14ad094e7ab9180b90a9aa4ccdb6d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-the-azure-portal"></a>A virtuálisgép-méretezési készlet létrehozása az Azure-portálon
Az oktatóanyag bemutatja, hogyan könnyen létrehozni egy virtuálisgép-méretezési csoportban csak néhány perc múlva, az Azure portál használatával. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="choose-the-vm-image-from-the-marketplace"></a>A virtuális gép rendszerképének kiválasztása a piactérről
A portálról könnyedén telepítheti egy méretezési CentOS, CoreOS, Debian, Ubuntu Server, más Linux lemezképek és a Windows Server-lemezképekben készletben.

Első lépésként nyissa meg a [Azure-portálon](https://portal.azure.com) egy webböngészőben. Kattintson a **új**, keresse meg **méretezési**, majd válassza ki a **virtuálisgép-méretezési csoport** bejegyzést:

![az Azure virtuálisgép-méretezési csoport portál keresési](./media/virtual-machine-scale-sets-portal-create/portal-search.png)

## <a name="create-the-scale-set"></a>A méretezési csoport létrehozása
Az alapértelmezett beállításokat használja, és gyorsan hozhat létre a méretezési készlet.

* Adjon meg egy nevet a méretezési.  
Ez a név lesz a méretezési elé terheléselosztó FQDN alapja, ezért győződjön meg arról, hogy az összes Azure neve nem egyedi.

* Válassza ki a kívánt operációs rendszer típusát.

* Adjon meg a kívánt felhasználónevet, és jelölje ki a inkább hitelesítés típusát.  
Ha úgy dönt, hogy a jelszó, kell lennie legalább 12 karakter hosszú, és három négy következő bonyolultsági megfeleljen: egy kisbetű, egy nagybetű, egy számot és egy speciális karaktert. További információk a [felhasználónév- és jelszókövetelményekről](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm). Ha úgy dönt, **nyilvános SSH-kulcs**, akkor a nyilvános kulcsot kell arra, hogy csak Beillesztés *nem* a titkos kulcsot:

* Válassza ki **Igen** vagy **nem** való **meghaladja a 100 példányok skálázás engedélyezése**.  
Ha igen, a virtuális gépek méretezési csoportjának is kiterjedhetnek több elhelyezési csoporttal. További információkért lásd: [ebben a dokumentációban](./virtual-machine-scale-sets-placement-groups.md).

* Ügyeljen arra, hogy válassza ki a megfelelő **példány mérete**.  
További információ a virtuálisgép-méretek: vagy [Windows Virtuálisgép-méretek](..\virtual-machines\windows\sizes.md) vagy [Linux Virtuálisgép-méretek](..\virtual-machines\linux\sizes.md).

* Adja meg a kívánt erőforrás csoport nevét és helyét.  
Ha a régiót és **példány mérete** támogatja a rendelkezésre állási zónák a **rendelkezésre állási zónák** mező engedélyezve van. További információ a rendelkezésre állási zónák: Ez [áttekintése](../availability-zones/az-overview.md) cikk.

* Adja meg a kívánt tartománynév címkéje (a teljes Tartománynevét a terheléselosztó előtt a méretezési alapja).  
Ezt a címkét az összes Azure egyedinek kell lenni.

* Válassza ki a kívánt operációs rendszer lemezképét, a példányszám és a gép méretét.

* Válassza ki a kívánt lemez típusa: kezelni vagy sem.  
További információkért lásd: [ebben a dokumentációban](./virtual-machine-scale-sets-managed-disks.md). Ha úgy dönt, hogy a méretezési készletben több elhelyezési csoportok span, ez a beállítás nem lesz elérhető mert felügyelt lemezes méretezési csoportok elhelyezési csoportok számára szükséges.

* Engedélyezze vagy tiltsa le az automatikus skálázási, és konfigurálása, ha engedélyezve van.

![Rákérdezés létrehozása az Azure virtuális gép méretezési készlet portálon](./media/virtual-machine-scale-sets-portal-create/portal-create.png)

## <a name="connect-to-a-vm-in-the-scale-set"></a>Csatlakoztassa a virtuális Gépet, a méretezési csoportban lévő
Ha úgy döntött, hogy a skála beállítani egy elhelyezési csoport korlátozza, majd a méretezési érvénybe léptetett konfigurálva ahhoz, hogy a méretezési készletben, egyszerűen csatlakozhat (Ha nem, a méretezési készletben, akkor valószínűleg nem kell létrehozni egy jumpbox ugyanazon a virtuális gépek csatlakozni NAT-szabályok  a méretezési csoport virtuális hálózatban). Lássák, lépjen a `Inbound NAT Rules` a terheléselosztót a méretezési lapján:

![Azure virtuálisgép-méretezési készlet portál nat-szabályok](./media/virtual-machine-scale-sets-portal-create/portal-nat-rules.png)

A méretezési készletben, ezek a NAT-szabályok használata a virtuális gépek kapcsolódhat. Például Windows skálázási készletének, hogy nincs egy NAT-szabály a bejövő portot 50000, sikerült csatlakozni az, hogy a gép RDP-kapcsolaton keresztül a `<load-balancer-ip-address>:50000`. A Linux-méretezési csoport, akkor kapcsolódnának a paranccsal `ssh -p 50000 <username>@<load-balancer-ip-address>`.

## <a name="next-steps"></a>Következő lépések
Méretezési telepítéséről dokumentáció a parancssori beállítja, a következő témakörben: [ebben a dokumentációban](virtual-machine-scale-sets-cli-quick-create.md).

Skála telepítéséről dokumentáció PowerShell sorozata, lásd: [ebben a dokumentációban](virtual-machine-scale-sets-windows-create.md).

Visual Studio dokumentációja történő telepítéséhez a skála sorozata, lásd: [ebben a dokumentációban](virtual-machine-scale-sets-vs-create.md).

Általános dokumentáció, tekintse meg a [dokumentációjának áttekintése lapon a méretezési készlet](virtual-machine-scale-sets-overview.md).

Általános információkért tekintse meg a [méretezési csoportok fő kezdőlapjának](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

