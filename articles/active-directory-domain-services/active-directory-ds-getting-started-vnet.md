<properties
    pageTitle="Azure AD tartományi szolgáltatások: Virtuális hálózat létrehozása vagy kiválasztása | Microsoft Azure"
    description="Ismerkedés az Azure Active Directory tartományi szolgáltatásokkal (előzetes verzió)"
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
    ms.date="04/25/2016"
    ms.author="maheshu"/>

# Azure AD tartományi szolgáltatások *(előzetes verzió)* – Virtuális hálózat létrehozása vagy kiválasztása

## Irányelvek Azure-alapú virtuális hálózat kiválasztásához
Az Azure AD tartományi szolgáltatásokkal használni kívánt virtuális hálózat kiválasztásakor tartsa szem előtt az alábbi irányelveket:

- Olyan virtuális hálózatot válasszon ki, amely az Azure AD tartományi szolgáltatások által támogatott régióban található. [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.

- Ha egy már meglévő virtuális hálózatot szeretne használni, győződjön meg arról, hogy a használandó hálózat regionális virtuális hálózat. Azok a virtuális hálózatok, amelyek az örökölt affinitáscsoport-mechanizmust alkalmazzák, nem használhatók az Azure AD tartományi szolgáltatásokkal. Az [örökölt virtuális hálózatokat regionális virtuális hálózatokba kell migrálnia](../virtual-network/virtual-networks-migrate-to-regional-vnet.md).

- Ha egy már meglévő virtuális hálózatot szeretne használni, győződjön meg arról, hogy a használandó virtuális hálózathoz nincsenek egyéni DNS-kiszolgálók konfigurálva. Az Azure AD tartományi szolgáltatások nem támogatják az egyéni/saját DNS-kiszolgálókat.

- Ha egy már meglévő virtuális hálózatot szeretne használni, győződjön meg arról, hogy a virtuális hálózatban nincs azonos nevű, már meglévő tartomány. Tegyük fel például, hogy a kiválasztott virtuális hálózatban már van egy contoso.com nevű tartomány. Tegyük fel továbbá, hogy Ön egy, a Azure AD tartományi szolgáltatások által kezelt, azonos nevű (például contoso.com) tartományt próbál engedélyezni a virtuális hálózaton. Ebben az esetben az Azure AD tartományi szolgáltatások engedélyezése közben hiba fog történni. A hibát a virtuális hálózatban tapasztalható névütközés okozza. Ebben az esetben az Azure AD tartományi szolgáltatások által kezelt tartomány beállításához másik nevet kell használnia. A másik lehetséges megoldás az, hogy leépíti a meglévő tartományt, majd ezután folytatja a Azure AD tartományi szolgáltatások engedélyezését.

- Válassza ki azt a virtuális hálózatot, amelyben jelenleg üzemelnek/üzemelni fognak azok a virtuális gépek, amelyeknek hozzá kell férniük az Azure AD tartományi szolgáltatásokhoz. A szolgáltatás engedélyezése után nem lehet a tartományi szolgáltatásokat másik virtuális hálózatba áthelyezni.

- Az Azure AD tartományi szolgáltatások nem támogatják az Azure Resource Manager használatával létrehozott virtuális hálózatokat. Ha az Azure AD tartományi szolgáltatásokat Azure Resource Manager használatával létrehozott virtuális hálózatban szeretné használni, akkor [a klasszikus virtuális hálózatot összekötheti egy ARM-alapú virtuális hálózattal](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).


## 2. feladat: Azure-alapú virtuális hálózat létrehozása
A következő konfigurációs feladat annak az Azure-alapú virtuális hálózatnak a létrehozása, amelyben engedélyezni szeretné az Azure AD tartományi szolgáltatásokat. Ezt a lépést kihagyhatja, ha egy már létező virtuális hálózatot szeretne használni.

> [AZURE.NOTE] Győződjön meg arról, hogy az Azure AD tartományi szolgáltatásokkal való használathoz létrehozott vagy kiválasztott Azure-alapú virtuális hálózat az Azure AD tartományi szolgáltatások által támogatott Azure-régióhoz tartozik. [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások.

Jegyezze fel a virtuális hálózat nevét, hogy valóban a megfelelő virtuális hálózatot válassza majd ki, amikor egy későbbi konfigurációs lépésben engedélyezi az Azure AD tartományi szolgáltatásokat.

A következő konfigurációs lépések végrehajtásával hozza létre azt az Azure-alapú virtuális hálózatot, amelyben engedélyezni szeretné az Azure AD tartományi szolgáltatásokat.

1. Nyissa meg a **klasszikus Azure-portált** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Válassza ki a **Hálózatok** csomópontot a bal oldali panelen.

3. Kattintson a lap alján lévő feladatpanelen található **ÚJ** gombra.

    ![Virtuális hálózatok csomópont](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. A **Hálózati szolgáltatások** csomópontban kattintson a **Virtuális hálózat** gombra.

5. Kattintson a **Gyors létrehozás** gombra a virtuális hálózat létrehozásához.

    ![Virtuális hálózat – gyors létrehozás](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Adjon **nevet** a virtuális hálózatnak. A **címtartományt** vagy a **virtuális gépek maximális számát** is konfigurálhatja a létrehozandó hálózathoz. A DNS-kiszolgáló beállítását a „Nincs” értéken hagyhatja. Ez a beállítás az Azure AD tartományi szolgáltatások engedélyezése után frissülni fog.

7. Ügyeljen arra, hogy támogatott Azure-régiót kell kiválasztania a **Hely** legördülő menüben. [Az Azure régiói](https://azure.microsoft.com/regions/#services/) lapon találja azoknak az Azure-régióknak a felsorolását, amelyekben elérhetők az Azure AD tartományi szolgáltatások. Ez egy fontos lépés. Ha a kiválasztott virtuális hálózat az Azure AD tartományi szolgáltatások által nem támogatott Azure-régióban van, akkor nem fog sikerülni a szolgáltatás engedélyezése a kiválasztott virtuális hálózatban.

8. Kattintson a **Virtuális hálózat létrehozása** gombra a virtuális hálózat létrehozásához.

    ![Hozzon létre virtuális hálózatot az Azure AD tartományi szolgáltatásokhoz.](./media/active-directory-domain-services-getting-started/create-vnet.png)

<br>

## 3. feladat – Az Azure AD tartományi szolgáltatások engedélyezése
A következő konfigurációs feladat [az Azure AD tartományi szolgáltatások engedélyezése](active-directory-ds-getting-started-enableaadds.md).



<!--HONumber=Jun16_HO2-->


