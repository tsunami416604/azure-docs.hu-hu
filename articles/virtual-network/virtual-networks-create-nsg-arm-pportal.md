---
title: Hozzon létre egy hálózati biztonsági csoportot - Azure-portál |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és telepíthet a hálózati biztonsági csoportok az Azure portál használatával.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Hozzon létre egy hálózati biztonsági csoportot az Azure portál használatával

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [NSG-k létrehozása a klasszikus üzembe helyezési modellel](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Az NSG-előtérbeli NSG létrehozása
Létrehozásához a **NSG-előtérbeli** NSG-t, ahogy az az eset, kövesse az alábbi lépéseket:

1. Egy böngészőből keresse fel a(z) https://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Válassza ki **+ hozzon létre egy erőforrást >** > **hálózati biztonsági csoportok**.
   
    ![Azure portál – NSG-k](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. A **hálózati biztonsági csoportok**, jelölje be **Hozzáadás**.
   
    ![Azure portál – NSG-k](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. A **hálózati biztonsági csoport létrehozása**, hozzon létre egy NSG nevű *NSG-előtérbeli* a a *RG-NSG* erőforrás csoportot, és adja **létrehozása** .
   
    ![Azure portál – NSG-k](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Szabályok létrehozása egy létező NSG-ben
Azure-portálról egy meglévő NSG-szabályok létrehozására, végezze el a következő lépéseket:

1. Válassza ki **minden szolgáltatás**, majd keresse meg a **hálózati biztonsági csoportok**. Ha **hálózati biztonsági csoportok** jelenik meg, válassza ki azt.
2. Az NSG-ket, jelölje ki **NSG-előtérbeli** > **bejövő biztonsági szabályok**
   
    ![Azure portál – NSG-előtér](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. A közül **bejövő biztonsági szabályok**, jelölje be **Hozzáadás**.
   
    ![Azure portál – szabály hozzáadása](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. A **Hozzáadás bejövő biztonsági szabály**, nevű szabályt létrehozni *web-szabály* prioritását *200* keresztül hozzáférést *TCP* port*80* e bármelyik virtuális Gépet a forrás-, és válassza **OK**. Figyelje meg, hogy ezek a beállítások a legtöbb alapértelmezett értékei lesznek már.
   
    ![Azure portál – szabálybeállításai](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Néhány másodpercen belül megjelenik az új szabály az NSG.
   
    ![Azure portál – új szabály](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Ismételje meg a 6-hozzon létre egy bejövő forgalomra vonatkozó szabály nevű *rdp-szabály* prioritással *250* keresztül hozzáférést *TCP* portra *3389-es* forrásból bármely virtuális géphez.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>NSG hozzárendelése az előtérben levő alhálózathoz

1. Válassza ki **összes szolgáltatások >**, adja meg **erőforráscsoportok**, jelölje be **erőforráscsoportok** megjelenésekor, majd válassza ki **RG-NSG**.
2. A **RG-NSG**, jelölje be **...**   >  **TestVNet**.
   
    ![Azure portál – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. A **beállítások**, jelölje be **alhálózatok** > **előtér** > **hálózati biztonsági csoport**  >  **NSG-előtérbeli**.
   
    ![Azure portál – az alhálózati beállítások](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Az a **előtér** panelen válassza **mentése**.
   
    ![Azure portál – az alhálózati beállítások](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Az NSG-háttérrendszer NSG létrehozása
Létrehozásához a **NSG-háttérrendszer** NSG-t, és rendelje hozzá azt a **háttér** alhálózati, kövesse az alábbi lépéseket:

1. Az NSG nevű létrehozásához *NSG-háttérrendszer*, ismételje meg a [létrehozása az NSG-előtérbeli NSG](#Create-the-NSG-FrontEnd-NSG).
2. Létrehozásához a **bejövő** szabályok az alábbi, a táblázat a ismételje meg a [egy meglévő NSG-szabályok létrehozására](#Create-rules-in-an-existing-NSG).
   
   | Bejövő forgalomra vonatkozó szabály | Kimenő forgalomra vonatkozó szabály |
   | --- | --- |
   | ![Azure portál – bejövő forgalomra vonatkozó szabály](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure portál – kimenő forgalomra vonatkozó szabály](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Rendelje hozzá a a **NSG-háttérrendszer** NSG a **háttér** alhálózati, ismételje meg a [társít a NSG a FrontEnd alhálózathoz](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [meglévő NSG-k kezelése](manage-network-security-group.md)
* [Naplózás engedélyezése](virtual-network-nsg-manage-log.md) az NSG-ket.