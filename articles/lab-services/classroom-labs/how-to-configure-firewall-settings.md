---
title: Az Azure Lab Services tűzfalbeállításai
description: Megtudhatja, hogyan határozhatja meg a tesztkörnyezetben a virtuális gépek nyilvános IP-címét és portszámát, hogy információkat lehessen hozzáadni a tűzfalszabályok számára.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443455"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Az Azure Lab Services tűzfalbeállításai

Minden szervezet vagy iskola a saját hálózatát úgy állítja be, hogy a legjobban megfelel az igényeiknek.  Időnként olyan tűzfalszabályok beállítását is magában foglalja, amelyek letiltják a RDP protokoll (RDP) vagy a Secure Shell-(SSH-) kapcsolatokat a saját hálózatán kívüli gépekhez.  Mivel Azure Lab Services a nyilvános felhőben fut, néhány további konfigurálásra lehet szükség ahhoz, hogy a tanulók hozzáférhessenek a virtuális géphez a campus hálózatról való csatlakozáskor.

Mindegyik labor egyetlen nyilvános IP-címet és több portot használ.  Az összes virtuális gép, valamint a sablon VM és a tanuló virtuális gépek is ezt a nyilvános IP-címet fogják használni.  A nyilvános IP-cím nem változik a labor életében.  Az egyes virtuális gépek azonban eltérő portszámot kapnak.  A portszámok száma 49152 és 65535 között lehet.  A nyilvános IP-cím és portszám együttes használata az oktató és a tanulók megfelelő virtuális géphez való összekapcsolására szolgál.  Ez a cikk azt ismerteti, hogyan lehet megkeresni a labor által használt adott nyilvános IP-címet.  Ez az információ a bejövő és a kimenő tűzfalszabályok frissítésére használható, így a tanulók hozzáférhetnek a virtuális gépekhez.

>[!IMPORTANT]
>Minden tesztkörnyezet egy másik nyilvános IP-címmel fog rendelkezni.

## <a name="find-public-ip-for-a-lab"></a>Nyilvános IP-cím keresése laborhoz

Az egyes laborok nyilvános IP-címei a labor Services Lab-fiók **minden Labs** lapján találhatók.  Az **összes Labs** -oldal megkeresésének módjával kapcsolatban lásd: a [Labs felügyelete labor-fiókban](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account).  

> [!div class="mx-imgBorder"]
> ![az összes Labs-oldal](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>A nyilvános IP-cím nem jelenik meg, ha a tesztkörnyezet sablonjának gépe még nincs közzétéve.

## <a name="conclusion"></a>Összegzés

Most már tudjuk, hogy a tesztkörnyezet nyilvános IP-címe.  A rendszer a nyilvános IP-cím és a 49152-65535-es porttartomány esetében is létrehozhatja a bejövő és kimenő szabályokat a szervezet tűzfala számára.  A szabályok frissítése után a tanulók hozzáférhetnek a virtuális gépekhez anélkül, hogy a hálózati tűzfal blokkolja a hozzáférést.

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi cikkeket:

- [Tesztkörnyezet helyének engedélyezése a labor Creator számára](allow-lab-creator-pick-lab-location.md)
- [A labor hálózatának összekötése egy társ virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
- [A labor egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)
