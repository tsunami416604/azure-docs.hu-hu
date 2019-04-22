---
title: Az Azure Advisorral az alkalmazás rendelkezésre állásának javítása |} A Microsoft Docs
description: Az Azure Advisor használata az Azure-környezetek, magas rendelkezésre állás javítása érdekében.
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 793c881d08e8feb038cc6e7ac82b7e95384e1b55
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699304"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Az Azure Advisorral az alkalmazás rendelkezésre állásának javítása

Az Azure Advisor segítségével győződjön meg arról, és javíthatja az üzleti szempontból kritikus fontosságú alkalmazások folytonosságát. Megjelenik a magas rendelkezésre állás – javaslatok az Advisor által a **magas rendelkezésre állású** az Advisor irányítópult lapon.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Virtuális gép hibatűrő képesség biztosítása

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor azonosítja a virtuális gépeket, amelyek nem részei rendelkezésre állási csoport, és javasolja áthelyezése egy rendelkezésre állási csoportba. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e az Azure-beli virtuálisgép-SLA. Kiválaszthatja, hogy hozzon létre egy rendelkezésre állási csoportot a virtuális gép vagy a virtuális gép hozzáadása egy meglévő rendelkezésre állási csoporthoz.

> [!NOTE]
> Ha egy rendelkezésre állási csoport létrehozása, hozzá kell adnia legalább egy virtuális gépet bele. Azt javasoljuk, hogy szolgáltatáskimaradások alkalmával, csoport, győződjön meg arról, hogy legalább egy gép beállítása két vagy több virtuális gépet egy rendelkezésre állási érhető el.

## <a name="ensure-availability-set-fault-tolerance"></a>Győződjön meg, hogy a rendelkezésre állási csoport hibatűrése

Az alkalmazás redundanciájának garantálása érdekében javasoljuk, hogy a virtuális gépeket legalább kettesével foglalja rendelkezésre állási csoportokba. Az Advisor egy egyetlen virtuális gépet tartalmazó rendelkezésre állási csoportok azonosítja, és javasolja, hogy egy vagy több virtuális gép hozzáadása. Ez a konfiguráció biztosítja, hogy vagy a tervezett vagy nem tervezett karbantartási események esetén legalább egy virtuális gép elérhető, és megfelel-e az Azure-beli virtuálisgép-SLA. Kiválaszthatja a virtuális gép létrehozása vagy meglévő virtuális gép hozzáadása a rendelkezésre állási csoporthoz.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Felügyelt lemezek használata az adatok megbízhatóságának javítása érdekében

A rendelkezésre állási csoport tárfiókokat vagy tárolási skálázási egységeket használó lemezekkel rendelkező virtuális gépek esetén nem hibatűrőek az egyetlen skálázási egységek hibáival leállások idején. Az Advisor azonosítja azokat a ezeket a rendelkezésre állási csoportok és javasoljuk, hogy az Azure Managed Disks-ba való migrálás. Ez biztosítja, hogy a rendelkezésre állási csoportban a különböző virtuális gépek lemezei hibapontok elkerülése érdekében a hibaérzékeny pont. 

## <a name="ensure-application-gateway-fault-tolerance"></a>Application gateway hibatűrő képesség biztosítása

Ez a javaslat az alapvető fontosságú alkalmazások, amelyek az application Gateway átjárók által az üzletmenet folytonossága biztosítja. Az Advisor azonosítja az application gateway-példány hibatűrése nem konfigurált, és azt sugallja, hogy elvégezhető javítási műveleteket. Az Advisor azonosítja a közepes vagy nagy egy példányban – az application Gateway átjárók, és legalább egy további példányok hozzáadása javasolja. Egyetlen vagy több instance kis application Gateway átjárókon azonosítja és a közepes vagy nagy SKU-ba való migrálás javasolja. Ezeket a műveleteket, ellenőrizze, hogy az application gateway-példány megfelelnek a jelenlegi SLA ezekhez az erőforrásokhoz javasol.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>A virtuális gép adatainak véletlen törlés elleni védelem

Virtuális gépek biztonsági mentésének beállításához az üzleti szempontból kritikus fontosságú adatok rendelkezésre állását biztosítja, és véletlen törlés és adatsérülések elleni védelmet nyújt. Az Advisor azonosítja a virtuális gépek, ahol a biztonsági mentés nem engedélyezett, és javasolja a biztonsági mentés engedélyezése. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ellenőrizze, hogy szakértői Azure-felhőben való hozzáférést, ha szükséges

Amikor egy üzleti szempontból kritikus fontosságú számítási feladatot futtat, fontos a technikai támogatáshoz, amikor szükség van. Az Advisor azonosítja a potenciális üzleti szempontból kritikus fontosságú előfizetéseket, amelyek nem rendelkeznek a támogatási csomagot a benne foglalt terméktámogatással, és javasolja, hogy olyan beállítás, amely tartalmazza a technikai támogatási telepítse.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Értesítés az Önt érintő problémái az Azure az Azure Service Health-riasztások létrehozása

Azt javasoljuk, hogy az Azure Service Health-riasztások beállítása értesítés az Azure-szolgáltatási problémák hatása a felhasználókra. [Az Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely során egy Azure-szolgáltatási probléma által érintett, személyre szabott útmutatást és támogatást nyújt. Az Advisor azonosítja, amelyekhez nincs riasztás konfigurálva az előfizetések, és javasolja, hogy hozzon létre egyet.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Rugalmasság a Traffic Manager-végpontok konfigurálása

Több végpont a TRAFFIC Manager-profilok élmény magasabb rendelkezésre állás, ha bármely megadott végpont nem sikerül. További különböző régiókban lévő végpontok elhelyezése javítja a szolgáltatás megbízhatóságát. Az Advisor azonosítja a Traffic Manager-profilok, ahol csak egy végpont van és javasolni a felhasználóknak legalább egy további végpont hozzáadása egy másik régióban.

Ha minden végpont a Traffic Manager-profil, amely konfigurálva van a közelségi útválasztás ugyanabban a régióban, a felhasználók el más régiókból tapasztalhatnak csatlakozási késések fordulhatnak elő. Hozzáadásával vagy a végpont áthelyezése egy másik régióba teljesítményjavítás általános, és adja meg a jobb rendelkezésre állás, ha egy adott régióban szereplő összes végpont sikertelen. Az Advisor azonosítja a Traffic Manager-profilok közelségi útválasztás, ahol az összes végpont ugyanabban a régióban vannak konfigurálva. Azt javasolja, hozzáadásával vagy a végpont áthelyezése egy másik Azure-régióban.

Ha a Traffic Manager-profil földrajzi útválasztásra van konfigurálva, majd adatforgalmat végpontokra alapján meghatározott régióban. Ha meghibásodik egy régióban, nem nincs előre definiált feladatátvétel. Kellene egy végpontot, ahol van konfigurálva a regionális csoportosítás "Az összes (globális)" eldobott forgalom elkerülése érdekében, és szolgáltatás rendelkezésre állásának javítása. Az Advisor azonosítja a Traffic Manager-profilok földrajzi útválasztásának konfigurált ahol nincs úgy konfigurálva, hogy a regionális csoportosítás rendelkezik, mint az "Összes (globális)" végpontja van. Azt javasolja, hogy a végpont "az összes (globális) a konfiguráció módosítása.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Helyreállítható törlés használata az Azure Storage-fiók mentéséhez és helyreállításához véletlen felülírása vagy a törlés után

Engedélyezése [helyreállítható törlési](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) a tárfiókon, hogy a blobok átmenet helyreállíthatóan törölt állapotból helyett folyamatban véglegesen törölve törölve. Amikor a rendszer felülírja azok adatait egy helyreállíthatóan törölt pillanatkép menteni az állapotot a felülírt adatok jön létre. A helyreállítható törlés használata lehetővé teszi, hogy van-e véletlen törlések helyreállítása, vagy felülírja. Az Advisor azonosítja az Azure Storage-fiókok, amelyek nem rendelkeznek engedélyezhető a helyreállítható törlés, és engedélyezi azt javasolja.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Konfigurálja a VPN-átjáró aktív-aktív, a kapcsolat rugalmassága

Aktív-aktív konfigurációban a VPN-átjáró mindkét példányát meghatározzák az S2S VPN-alagutat a helyszíni VPN-eszközre. Ha egy tervezett karbantartási esemény vagy nem tervezett esemény történik az egyik átjárópéldány, forgalom fognak váltani a másik aktív IPsec-alagút automatikusan. Az Azure Advisor azonosítja azokat, amelyek nem aktív-aktív VPN-átjárók és javasolt a magas rendelkezésre állásra konfigurálja.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Az éles számítási feladatok futtatása termelési VPN-átjárók használatával

Az Azure Advisor minden VPN-átjárók, amelyek az alapszintű Termékváltozat keressen, és a egy éles Termékváltozat inkább használatát javasoljuk. Az alapszintű Termékváltozat fejlesztési és tesztelési célokra tervezték. Éles termékváltozatok alagút, BGP-támogatás, aktív-aktív konfigurációban lehetőségeket, egyéni Ipsec/IKE-házirendet, és magasabb stabilitását és rendelkezésre állási megnövelt számú kínálnak.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Magas rendelkezésre állás – javaslatok az Advisor elérése

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), majd nyissa meg [Advisor](https://aka.ms/azureadvisordashboard).

2.  Az Advisor irányítópultján kattintson a **magas rendelkezésre állású** fülre.

## <a name="next-steps"></a>További lépések

Advisor-javaslatok kapcsolatos további információkért lásd:
* [Bevezetés az Azure Advisor](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor díjakkal kapcsolatos ajánlások](advisor-cost-recommendations.md)
* [Advisor-teljesítményajánlások](advisor-performance-recommendations.md)
* [Az Advisor biztonsági javaslatok](advisor-security-recommendations.md)

