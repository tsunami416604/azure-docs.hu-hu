---
title: "A vész-helyreállítási részletezési futtassa egy másodlagos helyszíni helyre az Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "További tudnivalók a vész-helyreállítási részletezési fut az Azure Site Recovery szolgáltatással másodlagos helyszíni helyre"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 02/07/2018
ms.author: raynew
ms.openlocfilehash: 2e5f8dce1ca2f728d15161622fb9ff2afb4b6c86
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="run-a-disaster-recovery-drill-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>A vész-helyreállítási részletezési futtatása Hyper-V virtuális gépek egy másodlagos helyszíni helyre

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás vész-helyreállítási stratégiát infrastruktúrája azzal segíti a kezelése és koordinálása replikációjának, feladatátvételének és feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Az oktatóanyag bemutatja, hogyan futtathat egy vész-helyreállítási részletezési Hyper-V virtuális gépek esetén a másodlagos helyszíni helyre. A System Center Virtual Machine Manager (VMM) magánfelhő-alapú Hyper-V virtuális gépek kell kezelni. A részletezési érvényesíti a replikációs stratégiájának adatvesztés vagy leállás nélkül, és nincs hatással, hogy az hatással lenne az éles környezetben. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő a feladatátvételi teszt előfeltételei
> * Egyetlen gép feladatátvételi teszt futtatása


## <a name="prerequisites"></a>Előfeltételek

- A hálózati beállítások néhány feladatátvételi teszt futtathatja a másodlagos helyen. A feladatátvétel egy hálózati nélkül futtathatja egy meglévő hálózattal, vagy lehetővé teszik a Site Recovery automatikusan hozzon létre egy tesztelési hálózatot. 
**Ha egy meglévő éles hálózathoz a feladatátvételi teszthez használni kívánt:**:
    - Az elsődleges virtuális gép akkor, ha a feladatátvételi tesztet le kell állítani. Ellenkező esetben két virtuális gépek ugyanazzal az identitással fog futni az ugyanazon a hálózaton egy időben. 
    - Ha módosítja a teszt virtuális gépek, a módosítások elvesznek amikor távolítja el a feladatátvételi tesztet. Módosításokat a rendszer nem replikálja vissza az elsődleges virtuális gép.
    - A termelési számítási feladatokhoz állásidő tesztelését egy üzemi hálózat okoz. Kérje meg a felhasználókat, hogy nem használja a kapcsolódó alkalmazások, ha a vész-helyreállítási részletezési folyamatban van. 
- A replika teszt hálózati felel meg a teszt feladatátvételhez használt VMM logikai hálózat típusa nem szükséges. Azonban néhány kombináció nem működnek. Például ha a replika használ a DHCP- és a VLAN-alapú elkülönítés, nem használható Windows Hálózatvirtualizálás a feladatátvételi teszt hálózatában, mivel azt IP-címkészletet. 
- Azt javasoljuk, hogy nem használja a kiválasztott hálózati hálózatra való leképezés, a feladatátvételi teszthez.


## <a name="run-a-test-failover-for-a-vm"></a>A virtuális gépek feladatátvételi teszt futtatása

1. A **replikált elemek**, kattintson a virtuális gép > **feladatátvételi teszt**.
2. A **feladatátvételi teszt**, adja meg, hogyan teszt virtuális gépek csatlakoznak a hálózatokhoz a feladatátvételi teszt után. Azt javasoljuk, hogy a jelen oktatóanyag hagyja, hogy a Site Recovery automatikusan hozzon létre egy tesztelési hálózatot. [További információk](site-recovery-test-failover-vmm-to-vmm.md#network-options-in-site-recovery).
3. A feladatátvételi művelet elindításához kattintson az **OK** gombra. Nyomon követni a a **feladatok** fülre.
4. Miután a feladatátvétel befejeződött, győződjön meg arról, hogy a teszt virtuális gép sikeresen elindul-e.
5. Amikor elkészült, kattintson a **karbantartása a feladatátvételi teszt**. Ezzel törli a teszt virtuális gépek és a feladatátvételi teszt során létrehozott hálózatok.
6. A **megjegyzések**, és a feladatátvételi teszttel kapcsolatos megfigyelések feljegyzéséhez mentéséhez. 


## <a name="next-steps"></a>További lépések

[Éles feladatátvétel futtatása](tutorial-vmm-to-vmm-failover-failback.md)






