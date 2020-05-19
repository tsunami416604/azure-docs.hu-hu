---
title: Az Azure Lab Services tűzfalbeállításai
description: Megtudhatja, hogyan határozhatja meg a tesztkörnyezetben a virtuális gépek nyilvános IP-címét és portszámát, hogy információkat lehessen hozzáadni a tűzfalszabályok számára.
author: emaher
ms.author: enewman
ms.date: 05/15/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: dcb8c0d5304f052dbe7f14386425ce67ae3b79a6
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589533"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Az Azure Lab Services tűzfalbeállításai

Minden szervezet vagy iskola a saját hálózatát úgy állítja be, hogy a legjobban megfelel az igényeiknek.  Időnként olyan tűzfalszabályok beállítását is magában foglalja, amelyek letiltják a RDP protokoll (RDP) vagy a Secure Shell-(SSH-) kapcsolatokat a saját hálózatán kívüli gépekhez.  Mivel Azure Lab Services a nyilvános felhőben fut, néhány további konfigurálásra lehet szükség ahhoz, hogy a tanulók hozzáférhessenek a virtuális géphez a campus hálózatról való csatlakozáskor.

Mindegyik labor egyetlen nyilvános IP-címet és több portot használ.  Az összes virtuális gép, valamint a sablon VM és a tanuló virtuális gépek is ezt a nyilvános IP-címet fogják használni.  A nyilvános IP-cím nem változik a labor életében.  Az egyes virtuális gépek azonban eltérő portszámot kapnak.  A portszámok száma 49152 és 65535 között lehet.  A nyilvános IP-cím és portszám együttes használata az oktató és a tanulók megfelelő virtuális géphez való összekapcsolására szolgál.  Ez a cikk azt ismerteti, hogyan lehet megkeresni a labor által használt adott nyilvános IP-címet.  Ez az információ a bejövő és a kimenő tűzfalszabályok frissítésére használható, így a tanulók hozzáférhetnek a virtuális gépekhez.

>[!IMPORTANT]
>Minden tesztkörnyezet egy másik nyilvános IP-címmel fog rendelkezni.

## <a name="find-public-ip-for-a-lab"></a>Nyilvános IP-cím keresése laborhoz

Az egyes laborok nyilvános IP-címei a labor Services Lab-fiók **minden Labs** lapján találhatók.  Az **összes Labs** -lap megkeresésének módjával kapcsolatban lásd: [Labs megtekintése labor-fiókban](manage-labs.md#view-labs-in-a-lab-account).  

> [!div class="mx-imgBorder"]
> ![Minden Labs-lap](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>A nyilvános IP-cím nem jelenik meg, ha a tesztkörnyezet sablonjának gépe még nincs közzétéve.

## <a name="conclusion"></a>Összegzés

Most már tudjuk, hogy a tesztkörnyezet nyilvános IP-címe.  A rendszer a nyilvános IP-cím és a 49152-65535-es porttartomány esetében is létrehozhatja a bejövő és kimenő szabályokat a szervezet tűzfala számára.  A szabályok frissítése után a tanulók hozzáférhetnek a virtuális gépekhez anélkül, hogy a hálózati tűzfal blokkolja a hozzáférést.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [A labor hálózatának összekötése egy társ virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képgyűjtemény csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása labor tulajdonosként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet beállításainak megtekintése](how-to-configure-firewall-settings.md)
- [A labor egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)
