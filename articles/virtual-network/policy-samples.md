---
title: Policy-sablonminták | Microsoft Docs
description: Azure Policy-sablonminták virtuális hálózathoz.
services: virtual-network
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 8228da778e6cf6a874868ef0206092913908352d
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64694293"
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Azure Policy-sablonminták virtuális hálózathoz

A következő táblázat hivatkozásokat tartalmaz [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mintákat. A minták az [Azure Policy mintaadattárában](https://github.com/Azure/azure-policy) találhatók.

| | |
|---|---|
|**Hálózat**||
| [NSG X minden hálózati adapteren](../governance/policy/samples/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy minden virtuális hálózati adapterhez egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [NSG X minden alhálózaton](../governance/policy/samples/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy minden virtuális alhálózathoz egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [Útválasztási táblázat nélkül](../governance/policy/samples/no-user-defined-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Megakadályozza, hogy a virtuális hálózatok útválasztási táblázattal legyenek üzembe helyezve. |
| [Jóváhagyott alhálózat használata a virtuális gépek hálózati adaptereihez](../governance/policy/samples/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott alhálózatot kell használniuk. A jóváhagyott alhálózat azonosítóját Ön határozza meg. |
| [Jóváhagyott virtuális hálózat használata a virtuális gépek hálózati adaptereihez](../governance/policy/samples/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott virtuális hálózatot kell használniuk. A jóváhagyott virtuális hálózat azonosítóját Ön határozza meg. |
|**Monitorozás**||
| [Diagnosztikai beállítás naplózása](../governance/policy/samples/audit-diagnostic-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Naplózza, ha a diagnosztikai beállítások nincsenek engedélyezve a megadott erőforrástípusokhoz. Ön adja meg azt az erőforrástípus-tömböt, amelyhez ellenőrizni szeretné, hogy engedélyezve vannak-e a diagnosztikai beállítások. |
|**Elnevezési és szövegkonvenciók**||
| [Több névminta engedélyezése](../governance/policy/samples/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Számos névminta egyike használatának engedélyezése az erőforrásokhoz. |
| [Hasonló minta szükséges](../governance/policy/samples/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Az erőforrások neveinek meg kell felelnie egy minta *hasonló* feltételének. |
| [Egyező minta szükséges](../governance/policy/samples/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Az erőforrások neveinek meg kell egyezniük egy adott elnevezési mintával. |
| [A címke és a minta egyezése szükséges](../governance/policy/samples/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | A címkék értékének igazodniuk kell valamely szövegmintához. |
|**Címkék**||
| [Számlázási címkék szabályzatának kezdeményezése](../governance/policy/samples/billing-tags-policy-initiative.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | A költséghely és a terméknév címkének a megadott értékekkel kell rendelkeznie. A példa beépített szabályzatokat használ a szükséges címkék hozzáadásához és előírásához. A címkék szükséges értékeit Ön adja meg.  |
| [Címke és a hozzá tartozó érték kényszerítése erőforráscsoportok esetében](../governance/policy/samples/enforce-tag-on-resource-groups.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Címke és érték szükséges az erőforráscsoportokhoz. Ön adja meg a kötelező címkenevet és -értéket.  |
| [Címke és a hozzá tartozó érték kényszerítése](../governance/policy/samples/enforce-tag-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kötelező megadni hozzá a címkenevet és -értéket. Ön adja meg a kikényszerítendő címkenevet és -értéket.  |
| [Címke és a címke alapértelmezett értékének alkalmazása](../governance/policy/samples/apply-tag-default-value.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Hozzáadja a megadott címkenevet és -értéket, ha a címke nincs megadva. Az alkalmazandó címkenevet és -értéket Ön határozza meg.  |
|**Általános**||
| [Engedélyezett helyek](../governance/policy/samples/allowed-locations.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy az erőforrások kizárólag az engedélyezett helyeken helyezhetők üzembe. Az engedélyezett helyek tömbjét Ön határozza meg.  |
| [Engedélyezett erőforrástípusok](../governance/policy/samples/allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Biztosítja, hogy kizárólag a jóváhagyott erőforrástípusok legyenek üzembe helyezve. Az engedélyezett erőforrástípusok tömbjét Ön határozza meg.  |
| [Nem engedélyezett erőforrástípusok](../governance/policy/samples/not-allowed-resource-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Megakadályozza a megadott erőforrástípusok üzembe helyezését. A letiltandó erőforrástípusok tömbjét Ön határozza meg.  |