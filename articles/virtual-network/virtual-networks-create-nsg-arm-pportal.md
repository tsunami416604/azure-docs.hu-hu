---
title: "Hozzon létre a hálózati biztonsági csoport – az Azure portálon |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre és telepíthet a hálózati biztonsági csoportok az Azure portál használatával."
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
ms.openlocfilehash: 865032f350735d35668bb199ccf1ef3f0fae81de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-network-security-groups-using-the-azure-portal"></a>Hozza létre a hálózati biztonsági csoportokat az Azure portál használatával

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Ez a cikk a Resource Manager-alapú üzemi modellt ismerteti. Emellett [NSG-k létrehozása a klasszikus üzembe helyezési modellel](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

A minta az alábbi parancsok várt már létrehozott egy egyszerű környezetben PowerShell a fenti forgatókönyv alapján. Ha szeretné a parancsokat a jelen dokumentum megjelenített, először összeállítása a tesztkörnyezetben üzembe helyezésével [sablon](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), kattintson a **az Azure telepítéséhez**, cserélje le az alapértelmezett paraméterértékek, ha szükséges, és kövesse az utasításokat a portálon. Az alábbi használata lépéseket **RG-NSG** az erőforráscsoport a sablon telepítve van a neveként.

## <a name="create-the-nsg-frontend-nsg"></a>Az NSG-előtérbeli NSG létrehozása
Létrehozásához a **NSG-előtérbeli** NSG látható a fenti forgatókönyvben kövesse az alábbi lépéseket.

1. Egy böngészőből keresse fel a http://portal.azure.com címet, majd jelentkezzen be az Azure-fiókjával, ha szükséges.
2. Kattintson a **Tallózás >** > **hálózati biztonsági csoportok**.
   
    ![Azure portál – NSG-k](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. Az a **hálózati biztonsági csoportok** panelen kattintson a **Hozzáadás**.
   
    ![Azure portál – NSG-k](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. Az a **hálózati biztonsági csoport létrehozása** panelen, hozzon létre egy NSG nevű *NSG-előtér* a a *RG-NSG* erőforráscsoportban, és kattintson **létrehozása**.
   
    ![Azure portál – NSG-k](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Szabályok létrehozása egy létező NSG-ben
Azure-portálról egy meglévő NSG-szabályok létrehozására, kövesse az alábbi lépéseket.

1. Kattintson a **Tallózás >** > **hálózati biztonsági csoportok**.
2. Az NSG-ket, kattintson a **NSG-előtérbeli** > **bejövő biztonsági szabályok**
   
    ![Azure portál – NSG-előtér](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. A közül **bejövő biztonsági szabályok**, kattintson a **Hozzáadás**.
   
    ![Azure portál – szabály hozzáadása](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. A a **Hozzáadás bejövő biztonsági szabály** panelen nevű szabályt létrehozni *web-szabály* prioritását *200* keresztül hozzáférést *TCP* portra *80* e bármelyik virtuális Gépet a forrás-, és kattintson **OK**. Figyelje meg, hogy ezek a beállítások a legtöbb alapértelmezett értékei lesznek már.
   
    ![Azure portál – szabálybeállításai](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Néhány másodpercen belül megjelenik az új szabály az NSG.
   
    ![Azure portál – új szabály](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Ismételje meg a 6-hozzon létre egy bejövő forgalomra vonatkozó szabály nevű *rdp-szabály* prioritással *250* keresztül hozzáférést *TCP* portra *3389-es* forrásból bármely virtuális géphez.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>NSG hozzárendelése az előtérben levő alhálózathoz
1. Kattintson a **Tallózás >** > **erőforráscsoportok** > **RG-NSG**.
2. Az a **RG-NSG** paneljén kattintson **...**   >  **TestVNet**.
   
    ![Azure portál – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. Az a **beállítások** panelen kattintson a **alhálózatok** > **előtér** > **hálózati biztonsági csoport** > **NSG-előtér**.
   
    ![Azure portál – az alhálózati beállítások](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Az a **előtér** panelen kattintson a **mentése**.
   
    ![Azure portál – az alhálózati beállítások](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Az NSG-háttérrendszer NSG létrehozása
Létrehozásához a **NSG-háttérrendszer** NSG-t, és rendelje hozzá azt a **háttér** alhálózati, kövesse az alábbi lépéseket.

1. Ismételje meg a [létrehozása az NSG-előtérbeli NSG](#Create-the-NSG-FrontEnd-NSG) létrehozni egy NSG nevű *NSG-háttérrendszer*
2. Ismételje meg a [egy meglévő NSG-szabályok létrehozására](#Create-rules-in-an-existing-NSG) létrehozásához a **bejövő** szabályok az alábbi táblázatban.
   
   | Bejövő forgalomra vonatkozó szabály | Kimenő forgalomra vonatkozó szabály |
   | --- | --- |
   | ![Azure portál – bejövő forgalomra vonatkozó szabály](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure portál – kimenő forgalomra vonatkozó szabály](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Ismételje meg a [társít a NSG a FrontEnd alhálózathoz](#Associate-the-NSG-to-the-FrontEnd-subnet) társítja a **NSG-háttérrendszer** NSG a **háttér** alhálózati.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [meglévő NSG-k kezelése](virtual-network-manage-nsg-arm-portal.md)
* [Naplózás engedélyezése](virtual-network-nsg-manage-log.md) az NSG-ket.

