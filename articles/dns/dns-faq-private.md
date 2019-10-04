---
title: Azure Private DNS – gyakori kérdések
description: Gyakran ismételt kérdések az Azure saját DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: fca7359f9fa54899bb72be3b939e1a1839dbfbd1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155711"
---
# <a name="azure-private-dns-faq"></a>Azure Private DNS – gyakori kérdések

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

## <a name="does-azure-dns-support-private-domains"></a>Támogatja a Azure DNS a privát tartományokat?

A privát tartományok támogatása az Azure saját DNS zónák funkció használatával támogatott. Saját DNS zónák kezelése az internetre irányuló Azure DNS zónákkal megegyező eszközökkel történik. Csak a megadott virtuális hálózatokon belülről oldhatók fel. További információ: [Áttekintés](private-dns-overview.md).

További információ az Azure egyéb belső DNS-lehetőségeiről: a [virtuális gépek és a szerepkör példányainak névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Azure DNS Private Zones fog működni az Azure-régiók között?

Igen. Az Azure-régiók közötti virtuális hálózatok közötti DNS-feloldáshoz a magánhálózati zónák támogatottak. A privát zónák még a virtuális hálózatok explicit módon való társítása nélkül is működnek. Az összes virtuális hálózatot feloldási virtuális hálózatként kell megadni a privát zónához. Előfordulhat, hogy a virtuális hálózatokat az egyik régióról a másikra kell irányítani a TCP/HTTP-forgalomhoz.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Kapcsolódik-e az internethez a privát zónákhoz szükséges virtuális hálózatoktól?

Nem. A privát zónák a virtuális hálózatokkal együtt működnek. A használatával a virtuális gépekhez vagy más erőforrásokhoz tartozó tartományokat kezelheti a virtuális hálózatok között. A névfeloldáshoz nincs szükség internetkapcsolatra.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Használható-e ugyanaz a privát zóna a megoldáshoz több virtuális hálózat esetében is?

Igen. Akár 1000 virtuális hálózatot is hozzárendelhet egyetlen privát zónával.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Hozzá lehet adni egy másik előfizetéshez tartozó virtuális hálózatot csatolt virtuális hálózatként egy privát zónához?

Igen. Írási művelet engedéllyel kell rendelkeznie a virtuális hálózatokon és a magánhálózati DNS-zónában. Az írási engedély több RBAC-szerepkör számára is megadható. A klasszikus hálózati közreműködő RBAC szerepkör például írási engedéllyel rendelkezik a virtuális hálózatokhoz. A RBAC szerepköreivel kapcsolatos további információkért lásd: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>A rendszer automatikusan törli a virtuális gép DNS-rekordjait egy privát zónában a virtuális gép törlésekor?

Igen. Ha olyan virtuális gépet töröl, amelyen engedélyezve van az automatikus regisztráció, a rendszer automatikusan törli a regisztrált rekordokat.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>A virtuális gépek automatikusan regisztrálhatók egy privát zónában egy csatolt virtuális hálózatból manuálisan?

Igen. Az automatikusan regisztrált DNS-rekordokat a zónában manuálisan létrehozott DNS-rekorddal írhatja felül. A következő kérdés és válasz címe ebben a témakörben.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Mi történik, ha olyan új DNS-rekordot próbálok létrehozni egy privát zónában, amely ugyanazzal az állomásnévvel rendelkezik, mint egy társított virtuális hálózatban automatikusan regisztrált meglévő virtuális gép?

Megpróbál manuálisan létrehozni egy új DNS-rekordot egy olyan privát zónában, amely ugyanazzal az állomásnévvel rendelkezik, mint egy meglévő, automatikusan regisztrált virtuális gép egy társított virtuális hálózaton. Ha ezt teszi, az új DNS-rekord felülírja a virtuális gép automatikusan regisztrált rekordját. Ha újra megpróbálja törölni ezt a manuálisan létrehozott DNS-rekordot a zónából, a törlés sikeres lesz. Az automatikus regisztráció újra megtörténik, amíg a virtuális gép továbbra is létezik, és rendelkezik a hozzá tartozó magánhálózati IP-címmel. A DNS-rekord automatikusan újra létrejön a zónában.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Mi történik, ha leválaszt egy csatolt virtuális hálózatot egy privát zónából? A virtuális hálózatból automatikusan regisztrált virtuálisgép-rekordok el lesznek távolítva a zónából?

Igen. Egy csatolt virtuális hálózat privát zónából való leválasztásához frissítse a DNS-zónát a társított virtuális hálózati kapcsolat eltávolításához. Ebben a folyamatban az automatikusan regisztrált virtuális gépek rekordjai törlődnek a zónából.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Mi történik, ha törölünk egy privát zónához csatolt csatolt virtuális hálózatot? Manuálisan kell frissíteni a privát zónát a virtuális hálózatnak a zónában lévő csatolt virtuális hálózatként való leválasztásához?

Igen. Ha olyan csatolt virtuális hálózatot töröl, amely nem kapcsolódik le egy privát zónából, a törlési művelet sikeres lesz. A virtuális hálózat azonban nem lesz automatikusan lekapcsolva a privát zónából, ha van ilyen. A virtuális hálózatot manuálisan le kell kapcsolni a privát zónából. Ezért a törlés előtt törölje a virtuális hálózatot a saját zónából.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>A DNS-feloldás az alapértelmezett FQDN (internal.cloudapp.net) használatával akkor is működik, ha egy privát zóna (például private.contoso.com) egy virtuális hálózathoz van csatolva?

Igen. A privát zónák nem helyettesítik az alapértelmezett DNS-felbontásokat az Azure által biztosított internal.cloudapp.net zónával. További szolgáltatásként vagy fejlesztésként is elérhető. Függetlenül attól, hogy az Azure által biztosított internal.cloudapp.net vagy a saját privát zónára támaszkodik, használja a feloldani kívánt zóna teljes tartománynevét.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>A rendszer a társított virtuális hálózaton belüli virtuális gépek DNS-utótagját a privát zónára módosítja?

Nem. A társított virtuális hálózatban lévő virtuális gépek DNS-utótagja az alapértelmezett Azure által biztosított utótag ("*. internal.cloudapp.net") marad. Ezt a DNS-utótagot manuálisan is módosíthatja a virtuális gépeken a saját zónában.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Milyen korlátozások vonatkoznak az Azure saját DNS használatára?

Az Azure saját DNS használatakor a következő alapértelmezett korlátozások érvényesek.

| Resource | Alapértelmezett korlát |
| --- | --- |
|saját DNS zónák/előfizetés|1000|
|Rekordhalmazok száma saját DNS zónában|25,000|
|Rekord/rekordhalmaz|20|
|Privát DNS-zóna Virtual Network hivatkozásai|1000|
|A virtuális hálózatok privát DNS-zónákon keresztül, automatikus regisztráció engedélyezve|100|
|Azon privát DNS-zónák száma, amelyekhez a virtuális hálózat elérhetővé válik, és engedélyezve van az automatikus regisztráció|1|
|Saját DNS-zónák száma, amelyekhez a virtuális hálózat kapcsolódhat|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Van portál-támogatás a privát zónákhoz?

Igen, és az API-kkal, a PowerShell-lel, a CLI-vel és az SDK-kkal már létrehozott privát zónák láthatók a Azure Portal.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Miért nem jelennek meg a meglévő privát DNS-zónák az új portálon?

A frissítés előzetes verziójának részeként új erőforrás-modellt szállítottunk a privát DNS-zónákhoz. A meglévő privát DNS-zónákat át kell telepíteni az új erőforrás-modellbe, mielőtt ezek megjelenhetnek az új portál felületén. Az új erőforrás-modellre való áttelepítéssel kapcsolatos információkért lásd alább.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hogyan áttelepíteni a meglévő magánhálózati DNS-zónákat az új modellre?
Javasoljuk, hogy a lehető leghamarabb telepítse át az új erőforrás-modellre. A régi erőforrás-modell támogatott lesz, azonban a modellen felül további funkciók nem lesznek kifejlesztve. A jövőben azt szeretnénk, hogy az új erőforrás-modell mellett is elavultak legyenek. A meglévő privát DNS-zónák új erőforrás-modellre való áttelepítésével kapcsolatos útmutatásért lásd:[Azure DNS privát zónák áttelepítési útmutatója](private-dns-migration-guide.md).

## <a name="next-steps"></a>További lépések

- [További információ az Azure saját DNS](private-dns-overview.md)