---
title: Azure Private DNS – gyakori kérdések
description: Ebben a cikkben az Azure Private DNS-sel kapcsolatos gyakori kérdések
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939422"
---
# <a name="azure-private-dns-faq"></a>Azure Private DNS – gyakori kérdések

Az alábbiakban gyakran feltett kérdések merülnek fel az Azure privát DNS-ével kapcsolatban.

## <a name="does-azure-dns-support-private-domains"></a>Támogatja az Azure DNS a magántartományokat?

A magántartományok at az Azure Private DNS-zónák szolgáltatás támogatja. A magánDNS-zónák feloldhatók csak a megadott virtuális hálózatokon belül. További információt az [áttekintésben](private-dns-overview.md)talál.

Az Azure egyéb belső DNS-beállításairól a [virtuális gépek és szerepkörpéldányok névfeloldása című témakörben](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)talál további információt.

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Működni fognak az Azure Private DNS-zónái az Azure-régiókban?

Igen. A privát zónák az Azure-régiók virtuális hálózatai közötti DNS-feloldáshoz támogatottak. A privát zónák még a virtuális hálózatok kifejezett társviszony-létesítése nélkül is működnek. Az összes virtuális hálózatot a privát DNS-zónához kell kapcsolni.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Szükség van-e a privát zónákhoz a virtuális hálózatokból származó internetkapcsolatra?

Nem. A privát zónák a virtuális hálózatokkal együtt működnek. Ezekkel kezelheti a virtuális gépek vagy más erőforrások tartományait a virtuális hálózatokon belül és azok között. A névfeloldáshoz nincs szükség internetkapcsolatra.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Használható-e ugyanaz a privát zóna több virtuális hálózathoz a megoldáshoz?

Igen. Privát DNS-zónát több ezer virtuális hálózattal kapcsolhat össze. További információ: [Azure DNS-korlátozások](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Egy másik előfizetéshez tartozó virtuális hálózat összekapcsolható egy privát zónával?

Igen. Írási művelet engedéllyel kell rendelkeznie a virtuális hálózatokon és a privát DNS-zónában. Az írási engedély több RBAC szerepkörhöz is megadható. A Klasszikus hálózati közreműködő RBAC szerepkör például írási engedélyekkel rendelkezik a virtuális hálózatokhoz, a közreműködői szerepkör pedig írási engedélyekkel rendelkezik a privát DNS-zónákhoz. Az RBAC szerepkörökről további információt a [Szerepköralapú hozzáférés-vezérlés](../role-based-access-control/overview.md)című témakörben talál.

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Az automatikusan regisztrált virtuálisgép DNS-rekordjai automatikusan törlődnek a virtuális gép törlésekor?

Igen. Ha olyan összekapcsolt virtuális hálózaton belül töröl egy virtuális gépet, amelyen engedélyezve van az automatikus regisztráció, a regisztrált rekordok automatikusan törlődnek.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Törölhető-e manuálisan egy automatikusan regisztrált virtuálisgép-rekord egy privát zónában egy csatolt virtuális hálózatból?

Igen. Az automatikusan regisztrált DNS-rekordokat felülírhatja egy manuálisan létrehozott DNS-rekorddal a zónában. A következő kérdés és válasz foglalkozik ezzel a témával.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>Mi történik, ha manuálisan próbálok új DNS-rekordot létrehozni egy olyan privát zónába, amelynek ugyanaz az állomásneve, mint egy összekapcsolt virtuális hálózatban automatikusan regisztrált virtuális gépnek?

Megpróbál manuálisan létrehozni egy új DNS-rekordot egy olyan privát zónába, amelynek állomásneve megegyezik egy meglévő, automatikusan regisztrált virtuális géppel egy csatolt virtuális hálózatban. Ebben az esetben az új DNS-rekord felülírja az automatikusan regisztrált virtuálisgép-rekordot. Ha ismét megpróbálja törölni ezt a manuálisan létrehozott DNS-rekordot a zónából, a törlés sikeres. Az automatikus regisztráció újra megtörténik, amíg a virtuális gép még mindig létezik, és egy privát IP-cím van csatolva hozzá. A DNS-rekord automatikusan létrejön a zónában.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Mi történik, ha lekapcsoljuk a csatolt virtuális hálózatot egy privát zónáról? A virtuális hálózat automatikusan regisztrált virtuálisgép-rekordjai is törlődnek a zónából?

Igen. Ha egy csatolt virtuális hálózatot le szeretne választani egy privát zónáról, a DNS-zónát a társított virtuális hálózati kapcsolat eltávolításához kell frissíteni. Ebben a folyamatban az automatikusan regisztrált virtuálisgép-rekordok törlődnek a zónából.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>Mi történik, ha törölünk egy privát zónához kapcsolódó összekapcsolt virtuális hálózatot? Manuálisan kell frissítenünk a privát zónát, hogy lekapcsoljuk a virtuális hálózatot csatolt virtuális hálózatként a zónáról?

Nem. Ha egy csatolt virtuális hálózatot anélkül töröl, hogy először lelenne csatolva egy privát zónából, a törlési művelet sikeres lesz, és a DNS-zónára mutató hivatkozások automatikusan törlődnek.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Az alapértelmezett teljes tartománynév (internal.cloudapp.net) használatával a DNS-feloldás akkor is működik, ha egy privát zóna (például private.contoso.com) virtuális hálózathoz van csatolva?

Igen. A privát zónák nem helyettesítik az azure által biztosított internal.cloudapp.net zóna alapértelmezett. Akár az Azure által biztosított internal.cloudapp.net vagy a saját privát zónájában támaszkodik, használja annak a zónának a teljes tartománynhálózatát, amely ellen fel szeretné oldani.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>A kapcsolt virtuális hálózaton belüli virtuális gépek DNS-utótagja a privát zóna dns-utótagja lesz?

Nem. A kapcsolt virtuális hálózat ban lévő virtuális gépek DNS-utótagja marad az alapértelmezett Azure által biztosított utótag ("*.internal.cloudapp.net"). Ezt a DNS-utótagot manuálisan módosíthatja a virtuális gépeken a privát zóna utótagra.
Az utótag módosításához a Dinamikus [DNS használata állomásnevek regisztrálásához a saját DNS-kiszolgálón](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients) című részben talál útmutatást.

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Milyen használati korlátok vonatkoznak az Azure DNS-beli privát zónákra?

Az [Azure DNS-zónák](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) használati korlátainak részleteit az Azure DNS-re vonatkozó privát zónák használati korlátairól az Azure DNS-ben meghatározott korlátokért olvassa el.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Miért nem jelennek meg a meglévő privát DNS-zónák az új portálélményben?

Ha a meglévő privát DNS-zónát az előzetes verziójú API használatával hozták létre, át kell telepítenie ezeket a zónákat az új erőforrásmodellbe. Az előzetes VERZIÓVAL létrehozott privát DNS-zónák nem jelennek meg az új portálélményben. Az új erőforrásmodellre való áttelepítésről az alábbiakban olvashat.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>Hogyan telepíthetem át a meglévő privát DNS-zónákat az új modellbe?

Javasoljuk, hogy mihamarabb térjen át az új erőforrásmodellre. Az örökölt erőforrásmodell támogatott lesz, azonban a további funkciók nem lesznek kifejlesztve ezen a modellen. A jövőben szándékunkban áll, hogy elavultak javára új erőforrás-modell. A meglévő magánDNS-zónák új erőforrásmodellbe való áttelepítésével kapcsolatos útmutatásért tekintse meg[az Azure DNS privát zónáinak áttelepítési útmutatóját.](private-dns-migration-guide.md)

## <a name="next-steps"></a>További lépések

- [További információ az Azure Private DNS-ről](private-dns-overview.md)
