---
title: "Active Directory Domain Services: Virtuális hálózat létrehozása vagy kiválasztása | Microsoft Docs"
description: "Az Azure Active Directory Domain Services engedélyezése a klasszikus Azure portál használatával"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 13ab1608-e3d8-40de-9f7b-9b5b42199af4
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 457519b00b65b0157effe2d4aba033a1c99852e8
ms.contentlocale: hu-hu
ms.lasthandoff: 06/30/2017


---
<a id="create-or-select-a-virtual-network-for-azure-active-directory-domain-services" class="xliff"></a>

# Virtuális hálózat létrehozása vagy kiválasztása az Azure Active Directory Domain Services-hez
<a id="before-you-begin" class="xliff"></a>

## Előkészületek
Tekintse át a [Hálózati megfontolások az Azure Active Directory Domain Services-hez](active-directory-ds-networking.md) című dokumentumot.

<a id="task-2-create-an-azure-virtual-network" class="xliff"></a>

## 2. feladat: Azure-alapú virtuális hálózat létrehozása
A következő konfigurálási feladat egy Azure-alapú virtuális hálózat és azon belül egy alhálózat létrehozása. Engedélyezze az Azure Active Directory Domain Services-t a virtuális hálózatának ezen az alhálózatán. Ha létező virtuális hálózatot szeretne használni, kihagyhatja ezt a lépést.

> [!NOTE]
> Győződjön meg arról, hogy az Active Directory Domain Services-szel való használathoz létrehozott vagy kiválasztott Azure-alapú virtuális hálózat az Azure Active Directory Domain Services által támogatott Azure-régióhoz tartozik. Annak megállapításához, hogy az Azure Active Directory Domain Services mely régiókban érhető el, lásd: [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/).
>
>Jegyezze fel a virtuális hálózat nevét annak érdekében, hogy valóban a megfelelő virtuális hálózatot válassza majd ki, amikor egy későbbi konfigurációs lépésben engedélyezi az Azure Active Directory Domain Services-t.


Kövesse az alábbi konfigurációs utasításokat egy olyan Azure virtuális hálózat létrehozása érdekében, amelyben engedélyezni kívánja az Azure Active Directory Domain Services-t:

1. Nyissa meg a [klasszikus Azure portált](https://manage.windowsazure.com).
2. A bal oldali panelen válassza ki a **Networks** (Hálózatok) elemet.

    ![Networks (Hálózatok) csomópont](./media/active-directory-domain-services-getting-started/networks-node.png)  
    Ekkor megnyílik a **Virtual Networks** (Virtuális hálózatok) ablak.
3. Kattintson a lap alján lévő feladatpanelen található **New** (Új) gombra.

    ![Virtual Networks (Virtuális hálózatok) ablak](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. Kattintson a **Network Services** (Hálózati szolgáltatások), majd a **Virtual Network** (Virtuális hálózat) elemre.

    ![Virtuális hálózat – gyors létrehozás](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
5. Kattintson a **Quick Create** (Gyors létrehozás) elemre egy virtuális hálózat létrehozásához.

6. A **Name** (Név) mezőben adjon nevet a virtuális hálózatnak, és – ha szükségesnek ítéli –, tegye a következőket:
    * Választhat, hogy **címtartományt** vagy a **virtuális gépek maximális számát** konfigurálja a létrehozandó hálózathoz.
    * A **DNS-kiszolgáló** beállítását a **Nincs** értéken hagyhatja. Az Azure Active Directory Domain Services engedélyezése után frissítheti a beállítást.
7. A **hely** legördülő listájában válasszon egy támogatott Azure-régiót.  
    Annak megállapításához, hogy az Azure Active Directory Domain Services mely régiókban érhető el, lásd: [Azure-szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/).
8. Kattintson a **Virtuális hálózat létrehozása** gombra a virtuális hálózat létrehozásához.

    ![Virtuális hálózat létrehozása az Azure Active Directory Domain Services-hez](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. A virtuális hálózat létrehozása után jelölje ki a virtuális hálózatot, és kattintson a **Konfigurálás** elemre.

    ![Alhálózat létrehozása](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. A **Virtual Network címterei** alatt kattintson **alhálózat felvétele** elemre, majd adja meg az **AaddsSubnet** nevű alhálózatot.

    ![Alhálózat létrehozása az Azure Active Directory Domain Services-hez](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

11. Az alhálózat létrehozásához kattintson a **Mentés** gombra.


<a id="next-step" class="xliff"></a>

## Következő lépés
[3. feladat: Az Active Directory Domain Services engedélyezése](active-directory-ds-getting-started-enableaadds.md)

