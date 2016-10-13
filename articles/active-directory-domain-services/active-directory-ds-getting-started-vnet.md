<properties
    pageTitle="Azure AD tartományi szolgáltatások: Virtuális hálózat létrehozása vagy kiválasztása | Microsoft Azure"
    description="Első lépések az Azure Active Directory tartományi szolgáltatások használatával"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>


# Virtuális hálózat létrehozása vagy kiválasztása az Azure AD tartományi szolgáltatásokhoz

## Irányelvek Azure-alapú virtuális hálózat kiválasztásához
> [AZURE.NOTE] **Előkészületek**: Tekintse át a [Hálózati megfontolások az Azure AD tartományi szolgáltatásokhoz](active-directory-ds-networking.md) című dokumentumot.


## 2. feladat: Azure-alapú virtuális hálózat létrehozása
A következő konfigurálási feladat egy Azure-alapú virtuális hálózat és azon belül egy alhálózat létrehozása. Engedélyezze az Azure AD tartományi szolgáltatásokat a virtuális hálózatának ezen az alhálózatán. Ezt a lépést kihagyhatja, ha egy már létező virtuális hálózatot szeretne használni.

> [AZURE.NOTE] Győződjön meg arról, hogy az Azure AD tartományi szolgáltatásokkal való használathoz létrehozott vagy kiválasztott Azure-alapú virtuális hálózat az Azure AD tartományi szolgáltatások által támogatott Azure-régióhoz tartozik. [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.

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

## 3. feladat – Az Azure AD tartományi szolgáltatások engedélyezése
A következő konfigurációs feladat [az Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started-enableaadds.md).



<!--HONumber=Oct16_HO1-->


