---
title: Hálózati konfigurációk testreszabása feladatátvevő virtuális géphez | Microsoft dokumentumok
description: Áttekintést nyújt a hálózati konfigurációk testreszabása egy feladatátvevő virtuális gép az Azure Site Recovery használatával az Azure Site Recovery replikációja.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292858"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>A cél Azure-beli virtuális gép hálózati konfigurációjának testreszabása

Ez a cikk útmutatást nyújt a hálózati konfigurációk testreszabásához a cél Azure virtuális gépen (VM), amikor replikálja és helyreállíta az Azure virtuális gépeket egyik régióból a másikba az [Azure Site Recovery](site-recovery-overview.md)használatával.

## <a name="before-you-start"></a>Előkészületek

Ismerje meg, hogyan biztosít a Site Recovery vészhelyreállítást ebben a [forgatókönyvben.](azure-to-azure-architecture.md)

## <a name="supported-networking-resources"></a>Támogatott hálózati erőforrások

A feladatátvevő virtuális gép hez a következő kulcsfontosságú erőforrás-konfigurációkat biztosíthatja az Azure virtuális gépek replikálása közben:

- [Belső terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [Nyilvános IP-cím](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Hálózati biztonsági csoport](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) mind az alhálózathoz, mind a hálózati adapterhez

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy előre megtervezi a helyreállítási oldali konfigurációkat.
- Hozza létre előre a hálózati erőforrásokat. Adja meg bemenetként, hogy az Azure Site Recovery szolgáltatás tiszteletben tarthassa ezeket a beállításokat, és győződjön meg arról, hogy a feladatátvevő virtuális gép betartja ezeket a beállításokat.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Feladatátvétel testreszabása és feladatátvételi hálózati konfigurációk tesztelése

1. Nyissa meg a **Replikált elemek et.** 
2. Válassza ki a kívánt Azure virtuális gép.
3. Válassza **a Számítási és hálózati lehetőséget,** majd a **Szerkesztés**lehetőséget. Figyelje meg, hogy a hálózati adapter konfigurációs beállításai tartalmazzák a megfelelő erőforrásokat a forrásnál. 

     ![A feladatátvevő hálózati konfigurációk testreszabása](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Válasszon egy tesztfeladatátvételi virtuális hálózatot. Választhat, hogy üresen hagyja, és válasszon egyet a teszt feladatátvételkor.
5. A feladatátvevő hálózat: Válassza ki a **Szerkesztés lehetőséget** a konfigurálni kívánt hálózati adapter közelében. A következő panelen, amely megnyílik, válassza ki a megfelelő előre létrehozott erőforrásokat a teszt feladatátvételi és feladatátvételi helyen.

    ![A hálózati adapter konfigurációjának szerkesztése](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Válassza **az OK gombot.**

Site Recovery most tiszteletben tartja ezeket a beállításokat, és biztosítja, hogy a feladatátvételi virtuális gép csatlakozik a kiválasztott erőforrása a megfelelő hálózati adapteren keresztül.

Amikor elindítja a teszt feladatátvétel helyreállítási terv segítségével, mindig megkérdezi az Azure virtuális hálózat. Ez a virtuális hálózat lesz használható a teszt feladatátvétel a gépek, amelyek nem rendelkeznek a teszt feladatátvételi beállítások előre konfigurálva.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="unable-to-view-or-select-a-resource"></a>Nem lehet megtekinteni vagy kijelölni egy erőforrást

Ha nem tud hálózati erőforrást kijelölni vagy megtekinteni, hajtsa után a következő ellenőrzéseket és feltételeket:

- A hálózati erőforrás célmezője csak akkor engedélyezett, ha a forrás virtuális gép rendelkezik egy megfelelő bemenettel. Ez azon az elven alapul, hogy egy vész-helyreállítási forgatókönyv, szeretné, hogy a forrás pontos vagy egy kicsinyített verziója.
- Minden egyes hálózati erőforrás, néhány szűrőt alkalmaznak a legördülő listában annak biztosítása érdekében, hogy a feladatátvevő virtuális gép csatolja magát a kiválasztott erőforráshoz, és a feladatátvétel megbízhatósága megmarad. Ezek a szűrők ugyanazokon a hálózati feltételeken alapulnak, amelyeket a forrásvirtuális gép konfigurálásakor ellenőriznek.

Belső terheléselosztó-ellenőrzések:

- A terheléselosztó előfizetése és régiója és a cél virtuális gép azonosnak kell lennie.
- A belső terheléselosztóhoz társított virtuális hálózatnak és a célvirtuális gép virtuális gépének azonosnak kell lennie.
- A cél virtuális gép nyilvános IP-termékváltozatés a belső terheléselosztó termékváltozatának meg kell egyeznie.
- Ha a cél virtuális gép úgy van beállítva, hogy egy rendelkezésre állási zónába kerüljön, ellenőrizze, hogy a terheléselosztó zónaredundáns-e vagy bármely rendelkezésre állási zóna része. (Az alapvető termékváltozatterhelés-elosztók nem támogatják a zónákat, és ebben az esetben nem jelennek meg a legördülő listában.)
- Győződjön meg arról, hogy a belső terheléselosztó rendelkezik egy előre létrehozott háttérkészlet és előtér-konfiguráció.

Nyilvános IP-cím:

- A nyilvános IP-cím és a cél virtuális gép előfizetésének és régiójának azonosnak kell lennie.
- A cél virtuális gép nyilvános IP-termékváltozatés a belső terheléselosztó termékváltozatának meg kell egyeznie.

Network security group (Hálózati biztonsági csoport):
- A hálózati biztonsági csoport előfizetésének és régiójának és a célvirtuális gépnek meg kell egyeznie.


> [!WARNING]
> Ha a cél virtuális gép egy rendelkezésre állási csoporthoz van társítva, majd társítania kell a nyilvános IP-cím és a belső terheléselosztó ugyanazon termékváltozat, hogy a másik virtuális gép nyilvános IP-és belső terheléselosztó a rendelkezésre állási készletben. Ha nem, a feladatátvétel nem biztos, hogy sikerül.
