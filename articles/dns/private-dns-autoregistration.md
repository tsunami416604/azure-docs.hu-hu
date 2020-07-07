---
title: Mi az Azure DNS privát zónák automatikus regisztrálási funkciója
description: Azure DNS privát zónák automatikus regisztrálási funkciójának áttekintése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "71961222"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Mi az Azure DNS privát zónák automatikus regisztrálási funkciója

Az Azure DNS Private Zones automatikus regisztráció funkciója a virtuális hálózatokban üzembe helyezett virtuális gépek DNS-rekordjainak felügyeletét veszi figyelembe. Ha a [virtuális hálózatot](./private-dns-virtual-network-links.md) egy magánhálózati DNS-zónával kapcsolja össze, és engedélyezi az automatikus regisztrációt az összes virtuális gépen, a virtuális hálózatban üzembe helyezett virtuális gépek DNS-rekordjai automatikusan létrejönnek a magánhálózati DNS-zónában. A megtekintett rekordok (A rekordok) továbbításán felül a névkeresési rekordok (PTR-rekordok) is automatikusan létrejönnek a virtuális gépekhez.
Ha további virtuális gépeket ad hozzá a virtuális hálózathoz, a virtuális gépek DNS-rekordjai is automatikusan létrejönnek a társított privát DNS-zónában.

Ha töröl egy virtuális gépet, a rendszer automatikusan törli a virtuális gép DNS-rekordjait a magánhálózati DNS-zónából.

Az automatikus regisztráció engedélyezése lehetőségre kattintva engedélyezheti a virtuális hálózati kapcsolat létrehozását.

![Automatikus regisztráció engedélyezése](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Korlátozások

* Az automatikus regisztráció csak a virtuális gépek esetében működik. Minden más erőforráshoz, például a belső terheléselosztóekhez, stb. a virtuális hálózathoz társított magánhálózati DNS-zónában manuálisan hozhat létre DNS-rekordokat.
* A DNS-rekordok csak az elsődleges virtuális gép hálózati adapteréhez jönnek létre automatikusan. Ha a virtuális gépek több hálózati ADAPTERrel rendelkeznek, a DNS-rekordokat manuálisan is létrehozhatja más hálózati adapterekhez.
* az IPv6 (AAAA rekordok) automatikus regisztrálása nem támogatott.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan hozhat létre saját zónát Azure DNS a [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* További információ [a privát zónák](./private-dns-scenarios.md) Azure DNS-beli privát zónákkal való használatával kapcsolatban.

* A Azure DNS privát zónákkal kapcsolatos gyakori kérdések és válaszok, beleértve a bizonyos típusú műveletek várható viselkedését, [saját DNS a gyakori](./dns-faq-private.md)kérdések című témakört.
