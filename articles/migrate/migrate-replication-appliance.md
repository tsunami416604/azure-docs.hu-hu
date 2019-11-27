---
title: Azure Migrate replikációs berendezés architektúrája
description: Áttekintést nyújt az ügynök-alapú áttelepítés Azure Migrate replikációs berendezéséről.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186553"
---
# <a name="replication-appliance"></a>Replikációs berendezés

Ez a cikk ismerteti a Azure Migrate által használt replikációs berendezést: a kiszolgáló értékelése a VMware virtuális gépek, fizikai gépek és magán-vagy nyilvános Felhőbeli virtuális gépek Azure-ba történő áttelepítésekor az ügynökön alapuló Migrálás használatával. 

Az eszköz az [Azure Migrate](migrate-overview.md) hub-ban érhető el. A központ natív eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint az egyéb Azure-szolgáltatásokból és a független szoftvergyártók származó eszközökről.


## <a name="appliance-overview"></a>Berendezések áttekintése

A replikációs berendezés egyetlen helyszíni gépen van üzembe helyezve VMware virtuális gépként vagy fizikai kiszolgálóként. A következőket futtatja:
- **Replikációs berendezés**: a replikációs berendezés koordinálja a kommunikációt, és felügyeli az adatreplikációt a helyszíni VMWare virtuális gépek és az Azure-ba replikált fizikai kiszolgálók esetében.
- **Process Server**: a folyamat kiszolgálója, amely alapértelmezés szerint telepítve van a replikációs berendezésen, és a következő műveleteket végzi el:
    - **Replikációs átjáró**: replikációs átjáróként működik. Replikációs adatok fogadása a replikációhoz engedélyezett gépekről. A replikációs adatokat a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és elküldi az Azure-nak.
    - **Ügynök telepítője**: a mobilitási szolgáltatás leküldéses telepítését végzi. Ezt a szolgáltatást minden olyan helyszíni gépen telepíteni és futtatni kell, amelyet át szeretne replikálni az áttelepítéshez.

## <a name="appliance-deployment"></a>Berendezések üzembe helyezése

**Üzembe helyezés mint** | **Használatban** | **Részletek**
--- | --- |  ---
VMware virtuális gép | Általában a VMware virtuális gépek áttelepítésekor használatos a Azure Migrate áttelepítési eszközzel az ügynök-alapú áttelepítés használatával. | A Azure Migrate központból letöltheti a petesejtek sablonját, és importálhatja a vCenter Serverba a készülék virtuális gépe létrehozásához.
Fizikai gép | Helyszíni fizikai kiszolgálók áttelepítésekor használatos, ha nem rendelkezik VMware-infrastruktúrával, vagy ha nem tud létrehozni VMware virtuális gépet egy PETESEJT-sablonnal. | A szoftver telepítőjének letöltése a Azure Migrate központból, és a berendezés számítógépének beállításához futtassa.

## <a name="appliance-deployment-requirements"></a>Berendezések telepítési követelményei

[Tekintse át](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) a központi telepítésre vonatkozó követelményeket.



## <a name="appliance-license"></a>Készülék licence
A készülékhez tartozik egy Windows Server 2016 próbaverziós licenc, amely 180 napig érvényes. Ha a próbaidőszak le van zárva, javasoljuk, hogy töltsön le és helyezzen üzembe egy új készüléket, vagy aktiválja a készülék virtuális gépe operációs rendszerének licencét.

## <a name="replication-process"></a>Replikációs folyamat

1. Amikor engedélyezi a replikációt egy virtuális géphez, a kezdeti replikáció az Azure Storage-ba megkezdődik a megadott replikációs házirend használatával. 
2. A forgalom az Azure Storage nyilvános végpontjait az interneten keresztül replikálja. A helyek közötti virtuális magánhálózati (VPN) kapcsolaton keresztüli replikálása nem támogatott a helyszíni helyről az Azure-ba.
3. A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A rendszer naplózza a gép nyomon követett módosításait.
4. A kommunikáció a következőképpen történik:
    - A virtuális gépek a replikálás kezeléséhez a HTTPS 443 bejövő porton keresztül kommunikálnak a replikációs berendezéssel.
    - A replikációs berendezés az Azure-ba irányuló replikációt a HTTPS 443 kimenő porton keresztül hangolja össze.
    - A virtuális gépek replikációs adatküldést küldenek a (replikációs berendezésen futó) folyamat-kiszolgálónak a HTTPS 9443 bejövő porton. Ez a port módosítható.
    - A Process Server replikációs adatokat fogad, optimalizálja és titkosítja, majd az Azure Storage-ba küldi az 443-as porton keresztül.
5. A replikációs adatnaplók először egy gyorsítótárbeli Storage-fiókba helyezik az Azure-ban. A rendszer feldolgozza ezeket a naplókat, és az adattárolást egy Azure-beli felügyelt lemez tárolja.

![Architektúra](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Berendezések frissítése

A készüléket manuálisan frissítheti az Azure Migrate hub-ból. Javasoljuk, hogy mindig futtassa a legújabb verziót.

1. Azure Migrate > kiszolgálók > Azure Migrate: kiszolgáló-értékelés, infrastruktúra-kiszolgálók, kattintson a **konfigurációs kiszolgálók**elemre.
2. A **konfigurációs kiszolgálók**területen megjelenik egy hivatkozás az **ügynök verziójában** , ha a replikációs berendezés új verziója érhető el. 
3. Töltse le a telepítőt a replikációs berendezést végző gépre, és telepítse a frissítést. A telepítő észleli a készüléken futó aktuális verziót.
 
## <a name="next-steps"></a>Következő lépések

[Ismerje meg, hogyan](tutorial-assess-vmware.md#set-up-the-appliance-vm) állíthatja be a készüléket a VMware rendszerhez.
[Ismerje meg, hogyan](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) állíthatja be a készüléket a Hyper-V-hez.

