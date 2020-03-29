---
title: Mi az Azure DNS privát zónáinak virtuális hálózati kapcsolatalerőforrása?
description: Az Azure DNS-magánzóna virtuális hálózati kapcsolat alerőforrásának áttekintése
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 9/24/2019
ms.author: rohink
ms.openlocfilehash: 9181ef93dfedbc28b297bef48a0bc37ba6d69798
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646761"
---
# <a name="what-is-a-virtual-network-link"></a>Mi az a virtuális hálózati kapcsolat?

Miután létrehozott egy privát DNS-zónát az Azure-ban, az nem érhető el azonnal egyetlen virtuális hálózatról sem. Ahhoz, hogy a hálózatban üzemeltetett virtuális gép hozzáférhessen a magánDNS-zónához, össze kell kapcsolnia egy virtuális hálózattal.
Ha egy magánDNS-zónát virtuális hálózattal szeretne összekapcsolni, létre kell hoznia egy virtuális hálózati kapcsolatot a privát DNS-zónában. Minden privát DNS-zóna virtuális hálózati kapcsolat gyermekerőforrásainak gyűjteményével rendelkezik. Ezek az erőforrások mindegyike egy virtuális hálózathoz való kapcsolatot jelent.

A virtuális hálózatot regisztrációs virtuális hálózatként vagy feloldó virtuális hálózatként csatolhatja egy magán DNS-zónához.

## <a name="registration-virtual-network"></a>Regisztráció virtuális hálózat

Amikor [kapcsolatot hoz létre egy](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) privát DNS-zóna és egy virtuális hálózat között, lehetősége van a virtuális gépek DNS-rekordjaiautomatikus [regisztrációjának](./private-dns-autoregistration.md) bekapcsolására. Ha ezt a lehetőséget választja, a virtuális hálózat a privát DNS-zóna regisztrációs virtuális hálózatává válik. A rendszer automatikusan dns-rekordot hoz létre a hálózatban üzembe helyezett virtuális gépekhez. A DNS-rekordok a virtuális hálózatban már üzembe helyezett virtuális gépekhez jönnek létre. A virtuális hálózat szempontjából a privát DNS-zóna lesz az adott virtuális hálózat regisztrációs zónája.
Egy privát DNS-zóna több regisztrációs virtuális hálózattal is rendelkezhet, azonban minden virtuális hálózathoz pontosan egy regisztrációs zóna tartozhat.

## <a name="resolution-virtual-network"></a>Megoldási virtuális hálózat

Ha privát DNS-zóna alatt hoz létre virtuális hálózati kapcsolatot, és úgy dönt, hogy nem engedélyezi a DNS-rekord automatikus regisztrációját, a virtuális hálózat csak megoldásként, csak virtuális hálózatként lesz kezelve. Az ilyen hálózatokban telepített virtuális gépek DNS-rekordjai nem jönnek létre automatikusan a csatolt magán DNS-zónában. Az ilyen hálózatokban üzembe helyezett virtuális gépek azonban sikeresen lekérdezhetik a DNS-rekordokat a privát DNS-zónából. Ezeket a rekordokat manuálisan is létrehozhatja, vagy más virtuális hálózatokból is feltöltheti, amelyek regisztrációs hálózatként vannak kapcsolva a privát DNS-zónával.
Egy privát DNS-zóna több felbontású virtuális hálózattal rendelkezhet, és egy virtuális hálózathoz több feloldási zóna is tartozhat.

## <a name="limits"></a>Korlátok

Ha meg szeretné tudni, hogy hány regisztrációs és feloldási hálózat, privát DNS-zónákra hivatkozva az [Azure DNS-korlátok című](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) témakörben található.

## <a name="other-considerations"></a>Egyéb szempontok

* A klasszikus telepítési modell használatával telepített virtuális hálózatok nem támogatottak.

* A magánDNS-zóna és a virtuális hálózat között csak egy kapcsolat hozhat létre.

* A magánDNS-zóna alatt található minden virtuális hálózati kapcsolatnak egyedi névvel kell rendelkeznie a magánDNS-zóna környezetében. Különböző privát DNS-zónákban azonos nevű hivatkozásokat használhat.

* Virtuális hálózati kapcsolat létrehozása után ellenőrizze a virtuális hálózati kapcsolat erőforrás "Kapcsolat állapota" mezőjét. A virtuális hálózat méretétől függően a kapcsolat működése és a kapcsolat állapota *befejezettre*változik néhány percig.

* Virtuális hálózat törlésekor a különböző magánDNS-zónákban társított összes virtuális hálózati kapcsolat és automatikusan regisztrált DNS-rekord automatikusan törlődik.

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogy miként kapcsolhat virtuális hálózatot magánDNS-zónához az [Azure Portal](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) használatával

* Ismerje meg, hogyan hozhat létre privát zónát az Azure DNS-ben az [Azure PowerShell](./private-dns-getstarted-powershell.md) vagy az [Azure CLI](./private-dns-getstarted-cli.md)használatával.

* Az Azure DNS-ben a privát zónákkal megvalósítható gyakori [privát zónaforgatókönyvekről](./private-dns-scenarios.md) olvashat.

* Az Azure DNS-ben a privát zónákkal kapcsolatos gyakori kérdésekről és válaszokról, beleértve az adott típusú műveleteknél elvárható viselkedést, olvassa el a [Privát DNS gyakori kérdések című témakört.](./dns-faq-private.md)
