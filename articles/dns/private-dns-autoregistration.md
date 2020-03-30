---
title: Mi az Azure DNS privát zónáinak automatikus regisztrációs szolgáltatása?
description: Az Azure DNS privát zónáinak automatikus regisztrációs szolgáltatásának áttekintése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9d1854b459e799d5cbb401de9ac717dd7d0fde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71961222"
---
# <a name="what-is-the-autoregistration-feature-of-azure-dns-private-zones"></a>Mi az Azure DNS privát zónáinak automatikus regisztrációs szolgáltatása?

Az Azure DNS-sel rendelkezik privát zónák automatikus regisztrációs szolgáltatás a virtuális hálózatban üzembe helyezett virtuális gépek DNS-rekordkezelésének fájdalmát kiveszi. Ha [egy virtuális hálózatot egy](./private-dns-virtual-network-links.md) magánDNS-zónával kapcsol össze, és engedélyezi az automatikus regisztrációt az összes virtuális gépen, a virtuális hálózatban telepített virtuális gépek DNS-rekordjai automatikusan létrejönnek a privát DNS-zónában. A továbbítási rekordok (A rekordok) mellett a rendszer automatikusan létrehozza a névkeresési rekordokat (PTR-rekordokat) is a virtuális gépekhez.
Ha további virtuális gépeket ad hozzá a virtuális hálózathoz, a virtuális gépek DNS-rekordjai is automatikusan létrejönnek a csatolt privát DNS-zónában.

Virtuális gép törlésekor a virtuális gép DNS-rekordjai automatikusan törlődnek a privát DNS-zónából.

Az automatikus regisztrációt az "Automatikus regisztráció engedélyezése" lehetőség kiválasztásával engedélyezheti, miközben virtuális hálózati kapcsolatot hoz létre.

![Automatikus regisztráció engedélyezése](./media/privatedns-concepts/enable-autoregistration.png)

## <a name="restrictions"></a>Korlátozások

* Az automatikus regisztráció csak virtuális gépekesetén működik. Az összes többi erőforrás, például a belső terheléselosztók stb., a virtuális hálózathoz kapcsolódó magán DNS-zónában manuálisan hozhat létre DNS-rekordokat.
* A DNS-rekordok csak az elsődleges virtuálisgép-hálózati adapterhez jönnek létre automatikusan. Ha a virtuális gépek egynél több hálózati adaptert használnak, manuálisan is létrehozhatja a DNS-rekordokat más hálózati adapterekhez.
* az IPv6 (AAAA rekordok) automatikus regisztrációja nem támogatott.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan hozhat létre privát zónát az Azure DNS-ben az [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy [az Azure CLI](./private-dns-getstarted-cli.md)használatával.

* Az Azure DNS-ben a privát zónákkal megvalósítható gyakori [privát zónaforgatókönyvekről](./private-dns-scenarios.md) olvashat.

* Az Azure DNS-ben a privát zónákkal kapcsolatos gyakori kérdésekről és válaszokról, beleértve az adott típusú műveleteknél elvárható viselkedést, olvassa el a [Privát DNS gyakori kérdések című témakört.](./dns-faq-private.md)
