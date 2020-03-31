---
title: Red Hat-számítási feladatok az Azure-ban – áttekintés | Microsoft dokumentumok
description: Ismerje meg az Azure-ban elérhető Red Hat termékajánlatokat.
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 718447e1dbf597af4349eab0be78a2bb544dec90
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970170"
---
# <a name="red-hat-workloads-on-azure"></a>Red Hat-számítási feladatok az Azure-ban

A Red Hat-számítási feladatok at az Azure-beli ajánlatok széles választéka támogatja. A Red Hat Enterprise Linux (RHEL) lemezképek az RHEL számítási feladatok középpontjában állnak, csakúgy, mint a Red Hat Update Infrastructure (RHUI).

## <a name="red-hat-enterprise-linux-images"></a>Red Hat Enterprise Linux képek

Az Azure RHEL-képek széles választékát kínálja az Azure-ban. Ezek a képek két különböző licencelési modellen keresztül érhetők el: a felosztó-kirovó és a bring-your-own-subscription (BYOS) rendszeren keresztül. Az azure-beli új RHEL-képek akkor kerülnek közzétételre, amikor szükség esetén új RHEL-verziók jelennek meg és frissülnek az életciklusuk során.

### <a name="pay-as-you-go-images"></a>Felosztó-ki-ki-go képek

Az Azure számos RHEL felosztó-kiosztó lemezképet kínál. Ezek a képek megfelelően jogosultak RHEL és csatolt ák a frissítési forrás (Red Hat Update Infrastructure). Ezek a képek prémium díjat számítanak fel az RHEL jogosultságért és frissítésekért. RHEL pay-as-you-go kép változatok közé tartozik:

* Standard RHEL.
* RHEL AZ SAP-nak.
* RHEL az SAP magas rendelkezésre állású és frissítési szolgáltatások.

Előfordulhat, hogy használatalapú képeket szeretne használni, ha nem szeretné, hogy külön fizessen a megfelelő számú előfizetésért.

### <a name="red-hat-gold-images"></a>Red Hat Gold képek

Az Azure red hat`rhel-byos`arany képeket is kínál ( ). Ezek a képek hasznosak lehetnek azoknak az ügyfeleknek, akik már rendelkeznek Red Hat-előfizetéssel, és szeretnék használni őket az Azure-ban. Az Azure-ban való használat előtt engedélyeznie kell a Red Hat Cloud Access meglévő Red Hat-előfizetéseit. Ezekhez a képekhez való hozzáférés automatikusan megtörténik, ha a Red Hat-előfizetések engedélyezve vannak a Cloud Access szolgáltatásban, és megfelelnek a jogosultsági követelményeknek. Ezek a képek segítségével az ügyfél elkerülheti a kettős számlázást, amely a használatalapú képek használatából eredhet.
* Ismerje meg, hogyan [engedélyezheti Red Hat-előfizetéseit az Azure-ral a Cloud Access szolgáltatáshoz.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* Ismerje meg, hogyan [keresheti meg a Red Hat Gold Images az Azure Portalon, az Azure CLI-t vagy a PowerShell-parancsmagban.](./byos.md)

> [!NOTE]
> A kettős számlázás akkor merül fel, amikor a felhasználó kétszer fizet az RHEL-előfizetésekért. Ez a forgatókönyv általában akkor fordul elő, ha egy ügyfél a Red Hat Subscription-Manager segítségével jogosultságot csatol egy RHEL használatalapú fizetéses virtuális géphez. Például egy ügyfél, aki az Előfizetés-Manager segítségével csatolja a jogosultságot az SAP-csomagok egy RHEL használatalapú fizetéses kép közvetetten kétszeresszámlázással, mert kétszer fizetrhel. Egyszer fizetnek a felosztó-kirovó prémium díjon keresztül, egyszer pedig az SAP-előfizetésükön keresztül. Ez a forgatókönyv nem történik meg a BYOS-lemezkép-felhasználókkal.

### <a name="generation-2-images"></a>2. generációs képek

A 2. generációs virtuális gépek (VM-ek) az 1. További információt a [Generation 2 dokumentációjában](https://docs.microsoft.com/azure/virtual-machines/linux/generation-2)talál. Az RHEL-lemezkép szempontjából a legfontosabb különbség az, hogy a 2. A rendszerindítási idő alatt a fő rendszertöltő rekord (MBR) helyett GUID partíciós táblát (GPT) is használnak. A GPT használata többek között lehetővé teszi a 2 TB-nál nagyobb operációs rendszer lemezméretét. Ezenkívül az [Mv2 sorozatú virtuális gépek](../../mv2-series.md) csak a 2.

RHEL Generation 2 lemezképek érhetők el az Azure Marketplace-en. Keresse meg a "gen2" a rendszerkép termékváltozatában az Azure CLI használatakor megjelenő összes lemezképek listájában. Lépjen a virtuális gép üzembe helyezésének **speciális** lapjára a 2.

## <a name="red-hat-update-infrastructure"></a>A Red Hat frissítési infrastruktúrája

Az Azure csak a használatra szánt RHEL-virtuális gépekhez biztosít Red Hat frissítési infrastruktúrát. Az RHUI gyakorlatilag a Red Hat CDN-ek tükre, de csak az Azure felosztó-kioldott RHEL virtuális gépek számára érhető el. Hozzáféréssel rendelkezik a megfelelő csomagokhoz attól függően, hogy melyik RHEL-lemezképet telepítette. Például egy RHEL az SAP-lemezkép az SAP-csomagok mellett az alap RHEL-csomagok.

### <a name="rhui-update-behavior"></a>RHUI frissítési viselkedés

Az RHUI frissítéshez alapértelmezés szerint az REL legújabb alverziójához csatlakoztatott RHEL-lemezképek `yum update` futtatásakor. Ez a viselkedés azt jelenti, hogy egy RHEL 7.4 virtuális `yum update` gép rhel 7.7-re frissíthető, ha egy művelet fut rajta. Ez a viselkedés a tervezés RHUI. A frissítési viselkedés csökkentése érdekében váltson a normál RHEL-adattárakról [a kiterjesztett frissítési támogatási tárolókra.](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)

## <a name="next-steps"></a>További lépések

* További információ az [RHEL-képekről az Azure-ban.](./redhat-images.md)
* További információ a [Red Hat update infrastruktúráról.](./redhat-rhui.md)
* Tudjon meg többet a [Red Hat Gold Image (`rhel-byos`) ajánlatról.](./byos.md)
