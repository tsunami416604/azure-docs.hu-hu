---
title: Az Azure Lab Services tűzfalbeállításai
description: Ismerje meg, hogyan határozhatja meg a nyilvános IP-címet és a port számtartománya a virtuális gépek egy tesztkörnyezetben, így információkat lehet hozzáadni a tűzfalszabályokat.
author: emaher
ms.author: enewman
ms.date: 02/14/2020
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: fbd45af0c9b94f04fdaad9d9b5c8214a91a8db91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77443455"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Az Azure Lab Services tűzfalbeállításai

Minden szervezet vagy iskola saját hálózatot hoz létre az igényeiknek leginkább megfelelő módon.  Ez néha magában foglalja a távoli asztali protokoll (RDP) vagy a Secure Shell (ssh) kapcsolatot a saját hálózaton kívüli gépekkel létesítő tűzfalszabályok beállítását.  Mivel az Azure Lab Services a nyilvános felhőben fut, néhány további konfigurációra lehet szükség ahhoz, hogy a diákok hozzáférhessenek a virtuális gépükhöz, amikor az egyetemi hálózatról csatlakoznak.

Minden tesztkörnyezet egyetlen nyilvános IP-címet és több portot használ.  Minden virtuális gép, mind a sablon virtuális gép, mind a tanuló virtuális gépek, ezt a nyilvános IP-címet fogja használni.  A nyilvános IP-cím nem változik a labor életében.  Azonban minden virtuális gép lesz egy másik portszámát.  A portszámok 49152 és 65535 között mozognak.  A nyilvános IP-cím és a portszám kombinációjával az oktató és a diákok a megfelelő virtuális géphez csatlakoztatható.  Ez a cikk ismerteti, hogyan lehet megtalálni a labor által használt adott nyilvános IP-címet.  Ez eket az információkat a bejövő és kimenő tűzfalszabályok frissítéséhez lehet használni, hogy a diákok hozzáférhessenek a virtuális gépekhez.

>[!IMPORTANT]
>Minden tesztkörnyezet nek más nyilvános IP-címe lesz.

## <a name="find-public-ip-for-a-lab"></a>Nyilvános IP-cím keresése egy laborhoz

Az egyes tesztkörnyezetek nyilvános IP-címei a Lab Services laborfiók **Minden laborja** lapján vannak felsorolva.  Az **Összes laborok** lap megkeresésének módjáról a [laborok laborfiókban történő kezeléséről.](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)  

> [!div class="mx-imgBorder"]
> ![Az összes laboroldal](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Nem fogja látni a nyilvános IP-címet, ha a tesztkörnyezet sablongépmég nincs közzétéve.

## <a name="conclusion"></a>Összegzés

Most már tudjuk a labor nyilvános IP címét.  Bejövő és kimenő szabályok hozhatók létre a szervezet tűzfala számára a nyilvános IP-címhez és a 49152-65535 porttartományhoz.  A szabályok frissítése után a diákok hozzáférhetnek a virtuális gépekhez anélkül, hogy a hálózati tűzfal blokkolja a hozzáférést.

## <a name="next-steps"></a>További lépések
Lásd az alábbi cikkeket:

- [A tesztkörnyezeti hely kiválasztásának engedélyezése a tesztkörnyezet létrehozója számára](allow-lab-creator-pick-lab-location.md)
- [A labor hálózatának csatlakoztatása egyenrangú virtuális hálózattal](how-to-connect-peer-virtual-network.md)
- [Megosztott képtár csatolása laborhoz](how-to-attach-detach-shared-image-gallery.md)
- [Felhasználó hozzáadása tesztkörnyezet tulajdonosaként](how-to-add-user-lab-owner.md)
- [Tesztkörnyezet tűzfalbeállításainak megtekintése](how-to-configure-firewall-settings.md)
- [Tesztkörnyezet egyéb beállításainak konfigurálása](how-to-configure-lab-accounts.md)
