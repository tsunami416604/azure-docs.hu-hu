---
title: "Azure AD tartományi szolgáltatások: Virtuális hálózat létrehozása vagy kiválasztása | Microsoft Docs"
description: "Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
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
ms.date: 10/03/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0617dbff2ba281091ba5775e7969edfead4d80c8


---
# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Virtuális hálózat létrehozása vagy kiválasztása az Azure AD tartományi szolgáltatásokhoz
## <a name="guidelines-to-select-an-azure-virtual-network"></a>Irányelvek Azure-alapú virtuális hálózat kiválasztásához
> [!NOTE]
> **Előkészületek**: Tekintse át a [Hálózati megfontolások az Azure AD tartományi szolgáltatásokhoz](active-directory-ds-networking.md) című dokumentumot.
> 
> 

## <a name="task-2-create-an-azure-virtual-network"></a>2. feladat: Azure-alapú virtuális hálózat létrehozása
A következő konfigurálási feladat egy Azure-alapú virtuális hálózat és azon belül egy alhálózat létrehozása. Engedélyezze az Azure AD tartományi szolgáltatásokat a virtuális hálózatának ezen az alhálózatán. Ezt a lépést kihagyhatja, ha egy már létező virtuális hálózatot szeretne használni.

> [!NOTE]
> Győződjön meg arról, hogy az Azure AD tartományi szolgáltatásokkal való használathoz létrehozott vagy kiválasztott Azure-alapú virtuális hálózat az Azure AD tartományi szolgáltatások által támogatott Azure-régióhoz tartozik. [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.
> 
> 

Jegyezze fel a virtuális hálózat nevét, hogy valóban a megfelelő virtuális hálózatot válassza majd ki, amikor egy későbbi konfigurációs lépésben engedélyezi az Azure AD tartományi szolgáltatásokat.

A következő konfigurációs lépések végrehajtásával hozza létre azt az Azure-alapú virtuális hálózatot, amelyben engedélyezni szeretné az Azure AD tartományi szolgáltatásokat.

1. Nyissa meg a **klasszikus Azure-portált** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
2. Válassza a **Networks** (Hálózat) csomópontot a bal oldali panelen.
   
    ![Hálózatok csomópont](./media/active-directory-domain-services-getting-started/networks-node.png)
3. Kattintson a lap alján lévő feladatpanelen található **ÚJ** gombra.
   
    ![Virtuális hálózatok csomópont](./media/active-directory-domain-services-getting-started/virtual-networks.png)
4. A **Hálózati szolgáltatások** csomópontban kattintson a **Virtuális hálózat** gombra.
5. Kattintson a **Gyors létrehozás** elemre egy virtuális hálózat létrehozásához.
   
    ![Virtuális hálózat – gyors létrehozás](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)
6. Adjon **nevet** a virtuális hálózatnak. A **címtartományt** vagy a **virtuális gépek maximális számát** is konfigurálhatja a létrehozandó hálózathoz. A **DNS-kiszolgáló** beállítását a „Nincs” értéken hagyhatja. Az Azure AD tartományi szolgáltatások engedélyezése után frissítheti a DNS-kiszolgáló beállítását.
7. Ügyeljen arra, hogy támogatott Azure-régiót kell kiválasztania a **Hely** legördülő menüben. [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.
8. Kattintson a **Virtuális hálózat létrehozása** gombra a virtuális hálózat létrehozásához.
   
    ![Hozzon létre virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/create-vnet.png)
9. A virtuális hálózat létrehozása után jelölje ki a virtuális hálózatot, és kattintson a **KONFIGURÁLÁS** fülre.
   
    ![Alhálózat létrehozása](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)
10. Lépjen a **Virtuális hálózat címterei** szakaszra. Kattintson az **Alhálózat hozzáadása** gombra, és adja meg az **AaddsSubnet** nevű alhálózatot. Az alhálózat létrehozásához kattintson a **Mentés** gombra.
    
    ![Hozzon létre egy alhálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)

<br>

## <a name="task-3-enable-azure-ad-domain-services"></a>3. feladat – Az Azure AD tartományi szolgáltatások engedélyezése
A következő konfigurációs feladat [az Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started-enableaadds.md).




<!--HONumber=Nov16_HO2-->


