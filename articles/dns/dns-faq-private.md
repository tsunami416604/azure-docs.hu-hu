---
title: Az Azure Private DNS – gyakori kérdések
description: Az Azure saját DNS kapcsolatos gyakori kérdések
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/12/2019
ms.author: victorh
ms.openlocfilehash: 480cf22491dbbfcb9fe1961b5c9a7aa6fe12a0cb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274034"
---
# <a name="azure-private-dns-faq"></a>Az Azure Private DNS – gyakori kérdések

> [!IMPORTANT]
> Privát Azure DNS jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="does-azure-dns-support-private-domains"></a>Támogatja az Azure DNS saját tartományok?

Saját tartomány támogatása az Azure saját DNS-zónák funkció használata támogatott. Privát DNS-zónák kezelése jól ismert eszközökkel, mint az Azure DNS-zónák az internet felé néző történik. A megadott virtuális hálózaton belül csak a feloldható zajlik. További információkért lásd: a [áttekintése](private-dns-overview.md).

Információk az egyéb belső DNS-beállítások az Azure-ban: [névfeloldás virtuális gépek és szerepkörpéldányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Működni fog az Azure DNS Private Zones Azure-régióban?

Igen. Saját zónák DNS-feloldás Azure-régiók közötti virtuális hálózatok között támogatott. Saját zónák nélkül is a virtuális társhálózatok explicit módon működik. A virtuális hálózatok kell megadni, feloldási virtuális hálózatok a privát zónához. Az ügyfeleknek kell előfordulhat, hogy a virtuális hálózatok társviszonyba állíthatók, a TCP/HTTP-adatforgalom megvalósulását egyik régióból egy másikba.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Az Internet kapcsolat van a virtuális hálózatok privát zónák szükséges?

Nem. Privát zónák működik együtt a virtuális hálózatok. Ügyfelek ezek segítségével virtuális gépeket vagy egyéb erőforrások belül és azok a virtuális hálózatok között a tartományok kezelése. Internetkapcsolat nem szükséges a névfeloldáshoz.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>A megoldás több virtuális hálózatokban használható privát ugyanabban a zónában?

Igen. Társíthatja legfeljebb 1000 virtuális hálózatok csak egy privát zónához.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-linked-virtual-network-to-a-private-zone"></a>Egy másik előfizetéshez tartozó virtuális hálózat lehet hozzáadni, a társított virtuális hálózat privát zónák?

Igen. A virtuális hálózatok és a privát DNS-zóna írási művelet engedéllyel kell rendelkeznie. Az írási engedély adható számos RBAC-szerepkört. Például a klasszikus hálózati közreműködő RBAC szerepkör rendelkezik írási engedéllyel a virtuális hálózatokhoz. További információ az RBAC-szerepkörök: [szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>A privát zónák automatikusan regisztrált virtuális gép DNS-rekordok automatikusan törölve lesznek a virtuális gép törlésekor?

Igen. Ha törli a társított virtuális hálózaton belüli virtuális gépek az automatikus regisztrációnak engedélyezve van, a regisztrált rekordok automatikusan törlődnek.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>A privát zónák társított virtuális hálózaton keresztül egy automatikusan regisztrált virtuális gép rekord törölhetők manuálisan?

Igen. Az automatikus regisztrált DNS-rekordok felülírhatja a manuálisan létrehozott DNS-rekordot a zónában. A következő kérdést és választ címe ebben a témakörben.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Mi történik, amikor megpróbálom manuálisan hozzon létre új DNS-rekord egy privát zónához társított virtuális hálózat automatikusan regisztrált meglévő virtuális gépként az azonos állomásnévvel rendelkező?

Próbálja meg manuálisan hozzon létre új DNS-rekord egy privát zónához, amely rendelkezik az azonos állomásnévvel meglévő, automatikusan regisztrált virtuális gépként a társított virtuális hálózaton. Ha így tesz, az új DNS-rekord felülírja az automatikusan regisztrált virtuális gép rekord. Ha törli a kézzel létrehozott DNS-rekordot a zóna újra megpróbálja a törlés sikeres lesz. Az automatikus regisztráció mindaddig, amíg a virtuális gép még létezik, és a egy magánhálózati IP-Címmel csatlakoztatott újra jelentkezik. A DNS-rekord nem hozza létre újra automatikusan a zónában.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Mi történik, ha azt a privát zónák társított virtuális hálózat leválasztása? A virtuális hálózat automatikusan regisztrált virtuális gép rekordját törlődni fognak a zóna túl?

Igen. Leválasztja a privát zónák társított virtuális hálózat, frissítse a DNS-zónához társított virtuális hálózati kapcsolat eltávolításához. Ez a folyamat automatikusan regisztrált virtuális gép rekordokat a zóna törlődnek.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Mi történik, ha egy társított virtuális hálózaton egy privát zónához kapcsolódó töröljük? Rendelkezünk a privát zónát, hogy a virtuális hálózat leválasztása a zónából társított virtuális hálózati manuálisan frissíteni?

Igen. A törlési művelet sikeres, a privát zónák leválasztása nélkül társított virtuális hálózat törlésekor. De a virtuális hálózat nem automatikusan leválasztása a privát zónából, ha van ilyen. Manuálisan kell megszünteti a virtuális hálózattal a saját zóna. Ebből kifolyólag leválasztása a virtuális hálózaton lévő a privát zónák azok törlése előtt.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>DNS-feloldás az alapértelmezett teljes Tartományneve (internal.cloudapp.net) használatával továbbra is működni fognak még akkor is, ha a privát zónák (például private.contoso.com) van csatolva a virtuális hálózat?

Igen. Saját zónák nem lecseréli a alapértelmezett DNS-megoldások az Azure által biztosított internal.cloudapp.net zóna használatával. További szolgáltatások vagy a fejlesztés érhető el. E használ, az Azure által biztosított internal.cloudapp.net, illetve a saját privát zónák, használja a feloldás kívánt zóna teljes Tartománynevét.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>A DNS-utótagot a társított virtuális hálózaton belüli virtuális gépek változik, valamint a saját zóna?

Nem. Az alapértelmezett Azure által biztosított utótag változatlan marad a virtuális gépek, a társított virtuális hálózat DNS-utótag ("*. internal.cloudapp.net"). A privát zónák a a virtuális gépeken manuálisan módosíthatja a DNS-utótagot.

## <a name="what-are-the-usage-limits-for-azure-private-dns"></a>Mik azok a használati korlátok az Azure saját DNS-hez?

Az alábbi alapértelmezett korlátok vonatkoznak az Azure saját DNS használata esetén.

| Resource | Alapértelmezett korlát |
| --- | --- |
|Privát DNS-zónák előfizetésenként|1000|
|Rekordhalmazok száma a privát DNS-zóna|25,000|
|Rekordok száma rekordhalmaz|20|
|Saját DNS-zóna egy virtuális hálózati kapcsolatok|1000|
|Automatikus regisztráció a privát DNS-zónák Kapcsolatcsoportonként virtuális hálózatok engedélyezve|100|
|Virtuális hálózat is első kapcsolódó automatikus regisztrációjának engedélyezése a privát DNS-zónák száma|1|
|Privát DNS-zónák is első társított virtuális hálózat száma|1000|

## <a name="is-there-portal-support-for-private-zones"></a>Van-e a saját zónák portal-támogatást?

Igen, és a privát zónák már létrehozott API-k, PowerShell, CLI és SDK-k segítségével az Azure Portalon láthatók.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>A meglévő privát DNS-zónák miért nem jelennek meg az új portálja?

Frissítés előzetes kiadás részeként azt egy új erőforrás-modellt a saját DNS-zónák le szállítani. A meglévő privát DNS-zónák kell migrálható az új erőforrás-modellje, mielőtt ezeket is megjelennek az új portál nyújt lehetőséget. Lásd az alábbi új erőforrás-modellje áttelepítése útmutatást.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hogyan migrálhatom a meglévő privát DNS-zónák az új modellre?
Határozottan javasoljuk, hogy telepít át az új erőforrás modellre minél hamarabb. Örökölt erőforrás-modellje is támogatott lesz, azonban további szolgáltatásokat nem kell kidolgozni felül ezt a modellt. A jövőben tervezzük az új erőforrás-modellje értéke kivezetjük. A meglévő privát DNS-kiszolgáló áttelepítése új erőforrás-modellje zónák tekintse át[áttelepítési útmutató az Azure DNS privát zónái](private-dns-migration-guide.md).

## <a name="next-steps"></a>További lépések

- [További információ az Azure saját DNS](private-dns-overview.md)