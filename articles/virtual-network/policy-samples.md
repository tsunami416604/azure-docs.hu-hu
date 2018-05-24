---
title: Policy-sablonminták | Microsoft Docs
description: Azure Policy-sablonminták virtuális hálózathoz.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 05/02/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 2b8766a5353b015030872176e9032034afb7cb9d
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2018
---
# <a name="azure-policy-sample-templates-for-virtual-network"></a>Azure Policy-sablonminták virtuális hálózathoz

A következő táblázat [Azure Policy](../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-sablonmintákra mutató hivatkozásokat tartalmaz. A minták az [Azure Policy mintaadattárában](https://github.com/Azure/azure-policy) találhatók.

| | |
|---|---|
|**Hálózat**||
| [NSG X minden hálózati adapteren](../azure-policy/scripts/nsg-on-nic.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy minden virtuális hálózati adapterhez egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [NSG X minden alhálózaton](../azure-policy/scripts/nsg-on-subnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy minden virtuális alhálózathoz egy adott hálózati biztonsági csoportot kell használni. A használandó hálózati biztonsági csoport azonosítóját Ön határozza meg. |
| [Útválasztási táblázat nélkül](../azure-policy/scripts/no-user-def-route-table.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  |Megakadályozza, hogy a virtuális hálózatok útválasztási táblázattal legyenek üzembe helyezve. |
| [Jóváhagyott alhálózat használata a virtuális gépek hálózati adaptereihez](../azure-policy/scripts/use-approved-subnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott alhálózatot kell használniuk. A jóváhagyott alhálózat azonosítóját Ön határozza meg. |
| [Jóváhagyott virtuális hálózat használata a virtuális gépek hálózati adaptereihez](../azure-policy/scripts/use-approved-vnet-vm-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy a hálózati adaptereknek egy jóváhagyott virtuális hálózatot kell használniuk. A jóváhagyott virtuális hálózat azonosítóját Ön határozza meg. |
|**Monitorozás**||
| [Diagnosztikai beállítás naplózása](../azure-policy/scripts/audit-diag-setting.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Naplózza, ha a diagnosztikai beállítások nincsenek engedélyezve a megadott erőforrástípusokhoz. Ön adja meg azt az erőforrástípus-tömböt, amelyhez ellenőrizni szeretné, hogy engedélyezve vannak-e a diagnosztikai beállítások. |
|**Elnevezési és szövegkonvenciók**||
| [Több névminta engedélyezése](../azure-policy/scripts/allow-multiple-name-patterns.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Számos névminta egyike használatának engedélyezése az erőforrásokhoz. |
| [Hasonló minta szükséges](../azure-policy/scripts/enforce-like-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Az erőforrások neveinek meg kell felelnie egy minta *hasonló* feltételének. |
| [Egyező minta szükséges](../azure-policy/scripts/enforce-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Az erőforrások neveinek meg kell egyezniük egy adott elnevezési mintával. |
| [A címke és a minta egyezése szükséges](../azure-policy/scripts/enforce-tag-match-pattern.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | A címkék értékének igazodniuk kell valamely szövegmintához. |
|**Címkék**||
| [Számlázási címkék szabályzatának kezdeményezése](../azure-policy/scripts/billing-tags-policy-init.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | A költséghely és a terméknév címkének a megadott értékekkel kell rendelkeznie. A példa beépített szabályzatokat használ a szükséges címkék hozzáadásához és előírásához. A címkék szükséges értékeit Ön adja meg.  |
| [Címke és a hozzá tartozó érték kényszerítése erőforráscsoportok esetében](../azure-policy/scripts/enforce-tag-rg.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Címke és érték szükséges az erőforráscsoportokhoz. Ön adja meg a kötelező címkenevet és -értéket.  |
| [Címke és a hozzá tartozó érték kényszerítése](../azure-policy/scripts/enforce-tag-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Kötelező megadni hozzá a címkenevet és -értéket. Ön adja meg a kikényszerítendő címkenevet és -értéket.  |
| [Címke és a címke alapértelmezett értékének alkalmazása](../azure-policy/scripts/apply-tag-def-val.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Hozzáadja a megadott címkenevet és -értéket, ha a címke nincs megadva. Az alkalmazandó címkenevet és -értéket Ön határozza meg.  |
|**Általános**||
| [Engedélyezett helyek](../azure-policy/scripts/allowed-locs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Előírja, hogy az erőforrások kizárólag az engedélyezett helyeken helyezhetők üzembe. Az engedélyezett helyek tömbjét Ön határozza meg.  |
| [Engedélyezett erőforrástípusok](../azure-policy/scripts/allowed-res-types.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Biztosítja, hogy kizárólag a jóváhagyott erőforrástípusok legyenek üzembe helyezve. Az engedélyezett erőforrástípusok tömbjét Ön határozza meg.  |
| [Nem engedélyezett erőforrástípusok](../azure-policy/scripts/not-allowed-res-type.md?toc=%2fazure%2fvirtual-network%2ftoc.json) | Megakadályozza a megadott erőforrástípusok üzembe helyezését. A letiltandó erőforrástípusok tömbjét Ön határozza meg.  |