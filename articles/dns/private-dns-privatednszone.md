---
title: Mi az Azure DNS privát zóna
description: MagánDNS-zóna áttekintése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: a951bc07c4a8ed42b1c116332d13674656bbaafd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646795"
---
# <a name="what-is-a-private-azure-dns-zone"></a>Mi az a privát Azure DNS-zóna?

Az Azure Private DNS megbízható, biztonságos DNS-szolgáltatást biztosít a virtuális hálózat tartományneveinek kezeléséhez és feloldásához anélkül, hogy egyéni DNS-megoldást kellene hozzáadnia. A magánDNS-zónák használatával használhatja a saját egyéni tartománynevek helyett az Azure által biztosított nevek ma elérhető. 

A magándns-zónában lévő rekordok nem oldhatók fel az internetről. A magándns-zóna DNS-feloldása csak a hozzá kapcsolt virtuális hálózatokról működik.

Privát DNS-zónát virtuális hálózati kapcsolatok létrehozásával kapcsolhat egy vagy több virtuális [hálózathoz.](./private-dns-virtual-network-links.md)
Azt is [engedélyezheti,](./private-dns-autoregistration.md) hogy az automatikus regisztrációs szolgáltatás automatikusan kezelje a virtuális hálózatban telepített virtuális gépek DNS-rekordjainak életciklusát.

## <a name="limits"></a>Korlátok

Ha meg szeretné tudni, hogy hány privát DNS-zónát hozhat létre egy előfizetésben, és hány rekordkészletet támogat egy privát DNS-zónában, tekintse meg az [Azure DNS-korlátait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="restrictions"></a>Korlátozások

* Az egycímkű saját DNS-zónák nem támogatottak. A privát DNS-zónának két vagy több címkével kell rendelkeznie. Például contoso.com két címkével van elválasztva egy adott kép. Egy privát DNS-zóna legfeljebb 34 címkével rendelkezhet.
* Privát DNS-zónában nem hozhat létre zónadelegálásokat (NS-rekordokat). Ha gyermektartományt kíván használni, közvetlenül létrehozhatja a tartományt magánDNS-zónaként, és összekapcsolhatja a virtuális hálózattal anélkül, hogy a szülőzónából névkiszolgáló-delegálást állítana be.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan hozhat létre privát zónát az Azure DNS-ben az [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* Az Azure DNS-ben a privát zónákkal megvalósítható gyakori [privát zónaforgatókönyvekről](./private-dns-scenarios.md) olvashat.

* Az Azure DNS-ben a privát zónákkal kapcsolatos gyakori kérdésekről és válaszokról, beleértve az adott típusú műveleteknél elvárható viselkedést, olvassa el a [Privát DNS gyakori kérdések című témakört.](./dns-faq-private.md)
