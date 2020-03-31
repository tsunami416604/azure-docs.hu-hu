---
title: Frissítések és összetevő-frissítések az Azure Site Recovery szolgáltatásban
description: Áttekintést nyújt az Azure Site Recovery szolgáltatás frissítéseiről és az összetevők frissítéséről.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257822"
---
# <a name="service-updates-in-site-recovery"></a>Szolgáltatásfrissítések a Hely-helyreállításban

Ez a cikk áttekintést nyújt az [Azure Site Recovery](site-recovery-overview.md) frissítéseiről, és ismerteti a Site Recovery-összetevők frissítését.

A Site Recovery rendszeresen közzéteszi a szolgáltatásfrissítéseket. A frissítések közé tartoznak az új funkciók, a támogatási fejlesztések, az összetevők frissítése és a hibajavítások. A legújabb funkciók és javítások előnyeinek kihasználása érdekében javasoljuk, hogy futassa a Site Recovery összetevőinek legújabb verzióit. 
 
 
## <a name="updates-support"></a>Frissítések támogatása

### <a name="support-statement-for-azure-site-recovery"></a>Az Azure Site Recovery támogatási nyilatkozata

Javasoljuk, hogy mindig frissítsen a legújabb összetevőkre:

**Az Azure Site Recovery összetevő minden új "N" verziójával, amely megjelent, az "N-4" alatti összes verzió tarol.** 

> [!IMPORTANT]
> A hivatalos támogatás > N-4 verzióról N verzióra való frissítésre szól. Ha például az N-6-on fut, először n-4-re kell frissítenie, majd n-re kell frissítenie.


### <a name="links-to-currently-supported-update-rollups"></a>A jelenleg támogatott összesítő frissítőcsomagokra mutató hivatkozások

 Tekintse át a [cikk](site-recovery-whats-new.md)legújabb összegző frissítőcsomagát (N verzió). Ne feledje, hogy a Site Recovery támogatja az N-4 verziókat.



## <a name="component-expiry"></a>Összetevő lejárata

A Site Recovery e-mailben (ha feliratkozott az e-mail értesítésekre) vagy a tároló irányítópultján a portálon értesíti a lejárt összetevőkről (vagy a lejárat közeledéséhez).

- Ezenkívül ha frissítések érhetők el, a portálon a forgatókönyv infrastruktúra nézetében megjelenik egy **elérhető frissítésgomb** az összetevő mellett. Ez a gomb átirányítja önt egy linkre a legújabb összetevő verzió letöltéséhez.
-  A Tárolók irányítópult-értesítései nem érhetők el, ha hyper-v virtuális gépeket replikál. 

Az e-mail értesítések küldése a következőképpen történt.

**Time** | **Frekvencia**
--- | ---
60 nappal az összetevő lejárata előtt | Egyszer kéthetente
Következő 53 nap | Hetente egyszer
Az elmúlt 7 napban | Naponta egyszer
Lejárat után | Egyszer kéthetente


### <a name="upgrading-outside-official-support"></a>A hivatalos támogatáson kívüli korszerűsítés

Ha az összetevő és a legújabb verzió közötti különbség nagyobb, mint négy, akkor ez támogatáson kívül nem támogatottnak minősül. Ebben az esetben frissítsen az alábbiak szerint: 

1. Frissítse a jelenleg telepített összetevőt a jelenlegi verzióra plusz négyre. Ha például a verzió 9,16, akkor frissítsen 9,20-ra.
2. Ezután frissítsen a következő kompatibilis verzióra. Tehát a példánkban a 9.16-ról 9.20-ra való frissítés után frissítsen 9,24-re. 

Kövesse ugyanazt a folyamatot az összes vonatkozó összetevő esetében.

### <a name="support-for-latest-operating-systemskernels"></a>A legújabb operációs rendszerek/kernelek támogatása

> [!NOTE]
> Ha ütemezett karbantartási időszakot, és újraindítás t tartalmaz, azt javasoljuk, hogy először frissítse a Site Recovery összetevőit, majd folytassa a karbantartási időszak ban az ütemezett tevékenységek többi részét.

1. Az operációs rendszer/kernel verziók frissítése előtt ellenőrizze, hogy a célverzió támogatott-e a Site Recovery szolgáltatásban. 

    - [Az Azure virtuális gép](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) támogatása.
    - [VMware/fizikai kiszolgáló](vmware-physical-azure-support-matrix.md#replicated-machines) támogatása
    - [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) támogatás.
2. Tekintse át [az elérhető frissítéseket,](site-recovery-whats-new.md) hogy megtudja, mit szeretne frissíteni.
3. Frissítsen a site recovery legújabb verziójára.
4. Frissítse az operációs rendszert/kernelt a szükséges verziókra.
5. Újraindít.


Ez a folyamat biztosítja, hogy a számítógép operációs rendszere a legújabb verzióra frissüljön, és hogy az új verzió támogatásához szükséges legújabb site recovery módosítások betöltődnek a számítógépre.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure-beli virtuális gépek vészhelyreállítása az Azure-ba

Ebben az esetben javasoljuk, hogy [engedélyezze az automatikus frissítéseket.](azure-to-azure-autoupdate.md) Engedélyezheti, hogy a Site Recovery a következőképpen kezelje a frissítéseket:

- A replikációengedélyezése folyamat során.
- A bővítmény frissítési beállításainak beállításával a tárolón belül.

Ha manuálisan szeretné kezelni a frissítéseket, tegye a következőket:

1. A tárolóban > **Replikált elemek**, kattintson erre az értesítésre a képernyő tetején: 
    
    **Új Site Recovery replikációs ügynök frissítés érhető el. Kattintson ide a ->telepítéséhez**

4. Jelölje ki azokat a virtuális gépeket, amelyekre alkalmazni szeretné a frissítést, majd kattintson az **OK**gombra.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>VMware VM/physical server vészhelyreállítás az Azure-ba

1. Az aktuális verzió és a [támogatási nyilatkozat](#support-statement-for-azure-site-recovery)alapján telepítse a frissítést először a helyszíni konfigurációs kiszolgálóra, az alábbi [utasítások szerint.](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server) 
2. Ha horizontális felskálázási folyamatkiszolgálói vannak, frissítse őket ezután [az alábbi utasítások szerint.](vmware-azure-manage-process-server.md#upgrade-a-process-server)
3. A mobilitási ügynök frissítése minden védett gépen, olvassa el [ezt a](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) cikket.

### <a name="reboot-after-mobility-service-upgrade"></a>Újraindítás a mobilitási szolgáltatás frissítése után

A Mobilitás szolgáltatás minden frissítése után újraindítás ajánlott, hogy a legújabb módosítások betöltődjenek a forrásgépre.

Az újraindítás nem kötelező, kivéve, ha a különbség az ügynök verziója az utolsó újraindítás során, és a jelenlegi verzió, nagyobb, mint négy.

A táblázatban látható példa bemutatja, hogyan működik ez.

|**Ügynök verziója (utolsó újraindítás)** | **Frissítés erre a kiadásra** | **Kötelező újraindítás?**|
|---------|---------|---------|
|9.16 |  9.18 | Nem kötelező|
|9.16 | 9.19 | Nem kötelező|
| 9.16 | 9.20 | Nem kötelező
 | 9.16 | 9.21 | Kötelező.<br/><br/> Frissítsen 9.20-ra, majd indítsa újra a frissítést a 9.21-es verzióra.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Hyper-V rendszerű virtuális gépek vészhelyreállítása az Azure-ba

### <a name="between-a-hyper-v-site-and-azure"></a>Hyper-V webhely és az Azure között

1. Töltse le a frissítést a Microsoft Azure webhely-helyreállítási szolgáltatóhoz.
2. Telepítse a szolgáltatót a Site Recovery szolgáltatásban regisztrált minden Hyper-V kiszolgálóra. Ha fürtöt futtat, frissítsen az összes fürtcsomóponton.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Egy helyszíni VMM-hely és az Azure között
1. Töltse le a frissítést a Microsoft Azure webhely-helyreállítási szolgáltatóhoz.
2. Telepítse a szolgáltatót a VMM-kiszolgálóra. Ha a VMM fürtben van telepítve, telepítse a szolgáltatót az összes fürtcsomópontra.
3. Telepítse a legújabb Microsoft Azure Recovery Services-ügynököt az összes Hyper-V állomásra vagy fürtcsomópontra.


## <a name="between-two-on-premises-vmm-sites"></a>Két helyszíni VMM-hely között
1. Töltse le a Microsoft Azure webhely-helyreállítási szolgáltató legújabb frissítését.
2. Telepítse a legújabb szolgáltatót a másodlagos helyreállítási helyet kezelő VMM-kiszolgálóra. Ha a VMM fürtben van telepítve, telepítse a szolgáltatót az összes fürtcsomópontra.
3. A helyreállítási hely frissítése után telepítse a szolgáltatót az elsődleges helyet kezelő VMM-kiszolgálóra.

## <a name="next-steps"></a>További lépések

Kövesse [az Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) oldalt az új frissítések és kiadások nyomon követéséhez.
