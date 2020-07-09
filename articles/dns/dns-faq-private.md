---
title: Azure Private DNS – gyakori kérdések
description: Ebből a cikkből megismerheti az Azure-saját DNSokkal kapcsolatos gyakori kérdéseket
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "76939422"
---
# <a name="azure-private-dns-faq"></a>Azure Private DNS – gyakori kérdések

Az Azure Private DNS szolgáltatással kapcsolatos gyakori kérdések a következők.

## <a name="does-azure-dns-support-private-domains"></a>Támogatja a Azure DNS a privát tartományokat?

A privát tartományok az Azure saját DNS zónák funkció használatával támogatottak. Saját DNS zónák csak a megadott virtuális hálózatokon belül oldhatók fel. További információ: [Áttekintés](private-dns-overview.md).

További információ az Azure egyéb belső DNS-lehetőségeiről: a [virtuális gépek és a szerepkör példányainak névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Az Azure saját DNS zónák működnek az Azure-régiók között?

Igen. Az Azure-régiók közötti virtuális hálózatok közötti DNS-feloldáshoz a magánhálózati zónák támogatottak. A privát zónák még a virtuális hálózatok explicit módon való társítása nélkül is működnek. Az összes virtuális hálózatot hozzá kell kapcsolni a magánhálózati DNS-zónához.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Kapcsolódik-e az internethez a privát zónákhoz szükséges virtuális hálózatoktól?

Nem. A privát zónák a virtuális hálózatokkal együtt működnek. A használatával a virtuális gépekhez vagy más erőforrásokhoz tartozó tartományokat kezelheti a virtuális hálózatok között. A névfeloldáshoz nincs szükség internetkapcsolatra.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Használható-e ugyanaz a privát zóna a megoldáshoz több virtuális hálózat esetében is?

Igen. A privát DNS-zónák több ezer virtuális hálózattal is összekapcsolhatók. További információ: [Azure DNS korlátok](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Egy másik előfizetéshez tartozó virtuális hálózat egy privát zónához van csatolva?

Igen. Írási művelet engedéllyel kell rendelkeznie a virtuális hálózatokon és a magánhálózati DNS-zónában. Az írási engedély több RBAC-szerepkör számára is megadható. Például a klasszikus hálózati közreműködő RBAC szerepkör írási engedéllyel rendelkezik a virtuális hálózatokhoz, és saját DNS zónák közreműködői szerepkör írási engedéllyel rendelkezik a magánhálózati DNS-zónákhoz. A RBAC szerepköreivel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>A rendszer automatikusan törli a virtuális gép DNS-rekordjait egy privát zónában a virtuális gép törlésekor?

Igen. Ha olyan virtuális gépet töröl, amelyen engedélyezve van az automatikus regisztráció, a rendszer automatikusan törli a regisztrált rekordokat.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>A virtuális gépek automatikusan regisztrálhatók egy privát zónában egy csatolt virtuális hálózatból manuálisan?

Igen. Az automatikusan regisztrált DNS-rekordokat a zónában manuálisan létrehozott DNS-rekorddal írhatja felül. A következő kérdés és válasz címe ebben a témakörben.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Mi történik, ha olyan új DNS-rekordot próbálok létrehozni egy privát zónában, amely ugyanazzal az állomásnévvel rendelkezik, mint egy társított virtuális hálózatban automatikusan regisztrált meglévő virtuális gép?

Megpróbál manuálisan létrehozni egy új DNS-rekordot egy olyan privát zónában, amely ugyanazzal az állomásnévvel rendelkezik, mint egy meglévő, automatikusan regisztrált virtuális gép egy társított virtuális hálózaton. Ha ezt teszi, az új DNS-rekord felülírja a virtuális gép automatikusan regisztrált rekordját. Ha újra megpróbálja törölni ezt a manuálisan létrehozott DNS-rekordot a zónából, a törlés sikeres lesz. Az automatikus regisztráció újra megtörténik, amíg a virtuális gép továbbra is létezik, és rendelkezik a hozzá tartozó magánhálózati IP-címmel. A DNS-rekord automatikusan újra létrejön a zónában.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Mi történik, ha leválaszt egy csatolt virtuális hálózatot egy privát zónából? A virtuális hálózatból automatikusan regisztrált virtuálisgép-rekordok el lesznek távolítva a zónából?

Igen. Egy csatolt virtuális hálózat privát zónából való leválasztásához frissítse a DNS-zónát a társított virtuális hálózati kapcsolat eltávolításához. Ebben a folyamatban az automatikusan regisztrált virtuális gépek rekordjai törlődnek a zónából.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Mi történik, ha törölünk egy privát zónához csatolt csatolt virtuális hálózatot? Manuálisan kell frissíteni a privát zónát a virtuális hálózatnak a zónában lévő csatolt virtuális hálózatként való leválasztásához?

Nem. Ha először töröl egy csatolt virtuális hálózatot egy privát zónából való leválasztás nélkül, a törlési művelet sikeres lesz, és a rendszer automatikusan törli a DNS-zónára mutató hivatkozásokat.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>A DNS-feloldás az alapértelmezett FQDN (internal.cloudapp.net) használatával akkor is működik, ha egy privát zóna (például private.contoso.com) egy virtuális hálózathoz van csatolva?

Igen. A privát zónák nem cserélik le az alapértelmezett Azure által biztosított internal.cloudapp.net zónát. Függetlenül attól, hogy az Azure által biztosított internal.cloudapp.net vagy a saját privát zónára támaszkodik, használja a feloldani kívánt zóna teljes tartománynevét.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>A rendszer a társított virtuális hálózaton belüli virtuális gépek DNS-utótagját a privát zónára módosítja?

Nem. A társított virtuális hálózatban lévő virtuális gépek DNS-utótagja az alapértelmezett Azure által biztosított utótag ("*. internal.cloudapp.net") marad. Ezt a DNS-utótagot manuálisan is módosíthatja a virtuális gépeken a saját zónában.
Az utótag módosításával kapcsolatos útmutatásért lásd: [dinamikus DNS használata a gazdagépek regisztrálásához a saját DNS-kiszolgálón](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Mik a Azure DNS privát zónák használati korlátai?

Azure DNS privát zónák használati korlátaival kapcsolatos részletekért tekintse meg a [Azure DNS korlátozásait](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) .

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Miért nem jelennek meg a meglévő privát DNS-zónák az új portálon?

Ha a meglévő privát DNS-zónát az előzetes verziójú API-val hozták létre, ezeket a zónákat át kell telepítenie az új erőforrás-modellbe. Az előzetes API-val létrehozott saját DNS zónák nem jelennek meg az új portálon. Az új erőforrás-modellre való áttelepítéssel kapcsolatos információkért lásd alább.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hogyan áttelepíteni a meglévő magánhálózati DNS-zónákat az új modellre?

Javasoljuk, hogy a lehető leghamarabb telepítse át az új erőforrás-modellre. A régi erőforrás-modell támogatott lesz, azonban a modellen felül további funkciók nem lesznek kifejlesztve. A jövőben azt szeretnénk, hogy az új erőforrás-modell mellett is érvénytelenítse azt. A meglévő privát DNS-zónák új erőforrás-modellre való áttelepítésével kapcsolatos útmutatásért lásd:[Azure DNS privát zónák áttelepítési útmutatója](private-dns-migration-guide.md).

## <a name="next-steps"></a>További lépések

- [További információ az Azure saját DNS](private-dns-overview.md)
