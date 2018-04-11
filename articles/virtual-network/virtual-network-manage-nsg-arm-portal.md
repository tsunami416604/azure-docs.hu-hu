---
title: Az Azure portál használatával NSG-k kezelése |} Microsoft Docs
description: Megtudhatja, hogyan kezelheti a meglévő NSG-ket az Azure portál használatával.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2018
---
# <a name="manage-nsgs-using-the-portal"></a>A portál használatával NSG-k kezelése

> [!div class="op_single_selector"]
> * [Portál](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Azure CLI](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md). Ez a cikk a Microsoft azt javasolja, hogy a klasszikus üzembe helyezési modellel helyett az új telepítések esetén a Resource Manager telepítési modell használatát bemutatja.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Információk beolvasása
Megtekintheti a meglévő NSG-ket, szabályok lekérdezni egy meglévő NSG-t, és megtudhatja, milyen erőforrásokat egy NSG társítva.

### <a name="view-existing-nsgs"></a>Meglévő NSG-k megtekintése

Előfizetés az összes meglévő NSG-ket megtekintéséhez kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel a(z) http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.

2. Kattintson a **Tallózás >** > **hálózati biztonsági csoportok**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Az NSG-ket listáját a **hálózati biztonsági csoportok** panelen.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Az erőforráscsoport nézet NSG-k

Az NSG-k listájának megtekintéséhez a **RG-NSG** erőforrás csoportjában hajtsa végre az alábbi lépéseket:

1. Kattintson a **erőforráscsoportok >** > **RG-NSG** > **...** .

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. Az erőforrások listájához, keresse meg elemek megjelenítése a NSG ikonra, ahogy az a **erőforrások** az alábbi panelen.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>A szabályok egy NSG listázása

Az NSG nevű szabályainak megtekintéséhez **NSG-előtérbeli**, kövesse az alábbi lépéseket:

1. Az a **hálózati biztonsági csoportok** panelen, vagy a **erőforrások** panelen látható a fenti kattintson **NSG-előtér**.

2. Az a **beállítások** lapra, majd **bejövő biztonsági szabályok**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. A **bejövő biztonsági szabályok** panel alább látható módon jelenik meg.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. Az a **beállítások** lapra, majd **kimenő biztonsági szabályok** a kimenő szabályok megtekintéséhez.

    > [!NOTE]
    > Alapértelmezett szabályok megtekintéséhez kattintson a **alapértelmezett szabályok** ikon, amely azokat a szabályokat jeleníti meg a panel tetején.
    >

### <a name="view-nsgs-associations"></a>Az NSG-társításának megtekintéséhez

Milyen erőforrások megtekintése a **NSG-előtérbeli** NSG társítása a, kövesse az alábbi lépéseket:

1. Az a **hálózati biztonsági csoportok** panelen, vagy a **erőforrások** panelen látható a fenti kattintson **NSG-előtér**.

2. Az a **beállítások** lapra, majd **alhálózatok** milyen alhálózatok társítva a NSG megtekintéséhez.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. Az a **beállítások** lapra, majd **hálózati illesztőt** megtekintéséhez a hálózati adapterek Mik az NSG társítva.

## <a name="manage-rules"></a>Szabályok kezelése
Szabályok hozzáadása egy meglévő NSG, szerkesztheti a meglévő szabályokat, és törölje a szabályokat.

### <a name="add-a-rule"></a>Szabály hozzáadása
Hozzáadása egy szabály, amely lehetővé teszi **bejövő** forgalmának portra **443-as** bármely számítógépről történő a **NSG-előtér** NSG, kövesse az alábbi lépéseket:

1. Az a **hálózati biztonsági csoportok** panelen, vagy a **erőforrások** panelen látható a fenti kattintson **NSG-előtér**.
2. Az a **beállítások** lapra, majd **bejövő biztonsági szabályok**.
3. Az a **bejövő biztonsági szabályok** panelen kattintson a **Hozzáadás**. Ezt követően a a **Hozzáadás bejövő biztonsági szabály** panelen alább látható módon, töltse ki az értékeket, és kattintson a **OK**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Néhány másodperc múlva figyelje meg, az új szabály az **bejövő biztonsági szabályok** panelen.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Szabály módosítása
A szabály a bejövő adatforgalom engedélyezésére a fenti létrehozott módosítása a **Internet** csak, hajtsa végre az alábbi lépéseket:

1. Az a **hálózati biztonsági csoportok** panelen, vagy a **erőforrások** panelen látható a fenti kattintson **NSG-előtér**.
2. Az a **beállítások** fülre, kattintson a fenti létrehozott szabály.
3. Az a **engedélyezése https** panelen, módosítsa a **forrás** tulajdonság alább látható módon, és kattintson **mentése**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Szabály törlése

A fentiekben létrehozott szabály törléséhez kövesse az alábbi lépéseket:

1. Az a **hálózati biztonsági csoportok** panelen, vagy a **erőforrások** panelen látható a fenti kattintson **NSG-előtér**.
2. Az a **beállítások** fülre, kattintson a fenti létrehozott szabály.
3. Az a **engedélyezése https** panelen kattintson a **törlése**, és kattintson a **Igen**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Társítások kezelése
Az NSG alhálózatokra és hálózati adapterek is hozzárendelhető. Is is leválasztja az összes erőforrásból, amelyekhez társítva vannak a NSG.

### <a name="associate-an-nsg-to-a-nic"></a>Társít egy NSG egy hálózati adapter
Rendelje hozzá a a **NSG-előtérbeli** NSG a **TestNICWeb1** hálózati adapter, kövesse az alábbi lépéseket:

1. Az a **hálózati biztonsági csoportok** panelen, vagy a **erőforrások** panelen látható a fenti kattintson **NSG-előtér**.
2. Az a **beállítások** lapra, majd **hálózati illesztőt** > **társítása** > **TestNICWeb1**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>A társítást egy NSG-t a hálózati Adapterhez

Leválasztja a **NSG-előtér** az NSG-t a **TestNICWeb1** hálózati adapter, kövesse az alábbi lépéseket:

1. Az Azure-portálon kattintson **erőforráscsoportok >** > **RG-NSG** > **...**   >  **TestNICWeb1**.

2. Az a **TestNICWeb1** paneljén kattintson **biztonsági módosítása...**   >  **Nincs**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> A hálózati adapterről bármely létező NSG társítása használhatja ezt a panelt.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Az NSG alhálózatból származó leválasztani

Leválasztja a **NSG-előtér** az NSG-t a **előtér** alhálózati, kövesse az alábbi lépéseket:

1. Az Azure-portálon kattintson **erőforráscsoportok >** > **RG-NSG** > **...**   >  **TestVNet**.

2. Az a **beállítások** panelen kattintson a **alhálózatok** > **előtér** > **hálózati biztonsági csoport**  >  **Nincs**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. Az a **előtér** panelen kattintson a **mentése**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Társít egy NSG alhálózathoz

Rendelje hozzá a a **NSG-előtérbeli** NSG a **FronEnd** alhálózat ebben az esetben kövesse az alábbi lépéseket:

1. Az Azure-portálon kattintson **erőforráscsoportok >** > **RG-NSG** > **...**   >  **TestVNet**.
2. Az a **beállítások** panelen kattintson a **alhálózatok** > **előtér** > **hálózati biztonsági csoport** > **NSG-előtér**.
3. Az a **előtér** panelen kattintson a **mentése**.

> [!NOTE]
> Hozzá lehet rendelni egy NSG alhálózathoz thh NSG-t a **beállítások** panelen.
>

## <a name="delete-an-nsg"></a>Az NSG törlése
Az NSG csak törölheti, ha nem kapcsolódik semmilyen erőforráshoz. Ha törölni szeretne egy NSG-t, az alábbi lépésekkel:.

1. Az Azure-portálon kattintson **erőforráscsoportok >** > **RG-NSG** > **...**   >  **NSG-előtérbeli**.
2. Az a **beállítások** panelen kattintson a **hálózati illesztőt**.
3. Ha egyetlen hálózati adapterrel felsorolt, kattintson a hálózati adapter, és hajtsa végre a 2. lépés a [leválasztani a hálózati Adapterhez egy NSG](#Dissociate-an-NSG-from-a-NIC).
4. Ismételje meg a 3. lépés az egyes hálózati adapterhez.
5. Az a **beállítások** panelen kattintson a **alhálózatok**.
6. Ha nincsenek felsorolva alhálózatok, kattintson az alhálózat, és hajtsa végre a 2. és 3 [leválasztani az NSG alhálózatból származó](#Dissociate-an-NSG-from-a-subnet).
7. Bal-görget a **NSG-előtér** panelen kattintson a **törlése** > **Igen**.

    ![Azure portál – NSG-k](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>További lépések
* [Naplózás engedélyezése](virtual-network-nsg-manage-log.md) az NSG-ket.
