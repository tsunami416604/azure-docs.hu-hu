---
title: Az Azure Site Recovery újdonságai
description: Az Azure Site Recovery szolgáltatás új szolgáltatásainak és legújabb frissítéseinek összegzését tartalmazza.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257432"
---
# <a name="whats-new-in-site-recovery"></a>A Site Recovery újdonságai

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás folyamatosan frissül és továbbfejleszthető. A cikk a legújabb kiadásokról, az új funkciókról és az új tartalmakról nyújt tájékoztatást. Ez az oldal rendszeresen frissül.

Az [Azure frissítési](https://azure.microsoft.com/updates/?product=site-recovery) csatornájában követheti és előfizethet a Site Recovery frissítési értesítéseire.

## <a name="supported-updates"></a>Támogatott frissítések

A Site Recovery összetevők esetében az N-4 verziókat támogatjuk, ahol az N a legújabb verzió. Ezeket az alábbi táblázat foglalja össze:

**Frissítés** |  **Egyesített beállítás** | **Konfigurációs kiszolgáló ova** | **Mobilitási szolgáltató ügynök** | **Hely-helyreállítási szolgáltató** | **Helyreállítási szolgáltatások ügynöke**
--- | --- | --- | --- | --- | ---
[45. gazdulta](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[43. összesítés](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[42. összesítés](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[41. gazdulta](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[40. összesítés](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[További információ](service-updates-how-to.md) a frissítések telepítéséről és támogatásáról.

> [!NOTE]
> A 44-es összesítő frissítőcsomag nem jelenik meg a táblázatban, mert nem tartalmazta a webhely-helyreállítási szolgáltatók és -ügynökök frissítéseit.

## <a name="updates-march-2020"></a>Frissítések (2020. március)

### <a name="update-rollup-45"></a>Összegző frissítőcsomag 45

[A 45.](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery)

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A site recovery ügynökök és szolgáltatók frissítései az összesítésben részletezett módon.
**Problémajavítások/fejlesztések** | Számos javítások és fejlesztések részletesen az összesítés.

## <a name="updates-january-2020"></a>Frissítések (2020. január)

### <a name="update-rollup-44"></a>Összegző frissítőcsomag 44

[A 44.összegző frissítőcsomag](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A webhely-helyreállítási szolgáltatók és -ügynökök nem kaptak frissítést.
**Problémajavítások/fejlesztések** | Számos javítások és fejlesztések részletesen az összesítés.

### <a name="azure-vmware-disaster-recovery"></a>Az Azure VMware vész-helyreállítási

Az Azure virtuális gépei mostantól támogatják a virtuális gépeket, amelyek lehetővé teszik az ügyfél által felügyelt kulcsokkal való inaktív titkosítást. [További információ](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Összegző frissítőcsomag 43

[A 43-as összegző frissítőcsomag](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A hely-helyreállítási ügynökök és szolgáltatók frissítései (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)


## <a name="updates-november-2019"></a>Frissítések (2019. november)

### <a name="update-rollup-42"></a>Összegző frissítőcsomag 42

[A 42.összegző frissítőcsomag](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A hely-helyreállítási ügynökök és szolgáltatók frissítései (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az Azure virtuális gép vész-helyreállítási új funkcióit a táblázat foglalja össze.

**Szolgáltatás** | **Részletek**
--- | ---
**Uefi** | A Site Recovery mostantól támogatja az UEFI-alapú rendszerindító architektúrával rendelkező Azure-virtuális gépek vészutáni helyreállítását.
**Linux** | A Site Recovery mostantól támogatja a Linuxot futtató Azure-beli virtuális gépeket az Azure Disk Encryption (ADE) szolgáltatással.
**2. generációs** | Az összes 2-es generációs Azure-beli virtuális gép már támogatott a vészhelyreállításhoz.
**Régiók** | Most már engedélyezheti a vészhelyreállítást az Azure-beli virtuális gépek hez a norvégiai geoban.

### <a name="vmware-to-azure-disaster-recovery"></a>Vészhelyreállítás VMware-ről Azure-ba

A VMware és az Azure vész-helyreállítási új funkcióit a táblázat foglalja össze.

**Szolgáltatás** | **Részletek**
--- | ---
**Uefi** | A Site Recovery mostantól támogatja az UEFI-alapú rendszerindító architektúrával rendelkező VMware virtuális gépek vészutáni helyreállítását.<br/><br/> A támogatott operációs rendszerek közé tartozik a Windows Server 2019, a Windows Server 2016, a Windows Server 2012 R2, a Windows Server 2012, az SLES 12 SP4, az RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Frissítés a karbantartási verem frissítéséhez/SHA-2

Az Azure-beli virtuális gépek másodlagos régióba, illetve a helyszíni VMware virtuális gépekhez vagy fizikai kiszolgálókhoz az Azure-ba való vészhelyreállítása esetén vegye figyelembe a következőket:

- A Mobility szolgáltatás bővítmény (Azure-beli virtuális gépek esetén) és a Mobility service agent (VMware/fizikai gépek esetén) 9.30.5407.1-es verziójából egyes gépi operációs rendszereknek a karbantartási verem frissítését és az SHA-2-t kell futtatniuk. A részleteket az alábbi táblázat mutatja.
- Telepítse a frissítést és az SHA-2-t a csatolt KB-nak megfelelően. Az SHA-1 nem támogatott 2019 szeptemberétől, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon telepíthető/frissít.
- További információ az [SHA-2 frissítésről és követelményekről.](https://aka.ms/SHA-2KB)

**Operációs rendszer** | **Azure VM** | **VMware VM/fizikai gép**
--- | --- | ---
**Windows 2008 R2 SP1** | [A verem karbantartásának frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [A verem karbantartásának frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [A verem karbantartásának frissítése](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [A verem karbantartásának frissítése](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [A verem karbantartásának frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [A verem karbantartásának frissítése](https://support.microsoft.com/help/4490628)<br/> [SHA-2.](https://support.microsoft.com/help/4474419)



## <a name="updates-october-2019"></a>Frissítések (2019. október)

### <a name="update-rollup-41"></a>Összegző frissítőcsomag 41

[A 41.összegző frissítőcsomag](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A hely-helyreállítási ügynökök és szolgáltatók frissítései (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)



### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az Azure virtuális gép vész-helyreállítási új funkcióit a táblázat foglalja össze.

**Szolgáltatás** | **Részletek**
--- | ---
**Feladatátvételi beállítások tesztelése** | Tesztfeladat-átvétel beállításakor most már konfigurálhatja a teszt feladatátvételi virtuális gép és a hálózat beállításait, beleértve az IP-címet, az NSG-t, a belső terheléselosztást és az egyes számítógép-hálózati adapterek nyilvános IP-címét. Ezek a beállítások nem kötelezőek, és nem módosítják az aktuális viselkedést. Ha nem konfigurálja ezeket a beállításokat, a teszt feladatátvétel időpontjában választhat egy Azure virtuális hálózatot. [További információ](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Helyreállítási tervek** | A helyreállítási tervek mostantól 100 virtuális gépre vannak korlátozva a feladatátvétel megbízhatóságának biztosítása érdekében.

### <a name="vmware-to-azure-disaster-recovery"></a>Vészhelyreállítás VMware-ről Azure-ba

A VMware és az Azure vész-helyreállítási új funkcióit a táblázat foglalja össze.

**Szolgáltatás** | **Részletek**
--- | ---
**Helyreállítási tervek** | A helyreállítási tervek mostantól 100 virtuális gépre vannak korlátozva a feladatátvétel megbízhatóságának biztosítása érdekében.


## <a name="updates-september-2019"></a>Frissítések (2019. szeptember)

### <a name="update-rollup-40"></a>Összegző frissítőcsomag 40

[A 40.összegző frissítőcsomag](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A hely-helyreállítási ügynökök és szolgáltatók frissítései (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)




### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az Azure virtuális gép vész-helyreállítási új funkcióit a táblázat foglalja össze.

**Szolgáltatás** | **Részletek**
--- | ---
**Karbantartás a feladat-visszavétel után** | Miután a rendszer átjutott a másodlagos Azure-ra, majd visszanem felelt az elsődleges régiónak, a Site Recovery automatikusan törli a másodlagos régióban lévő gépeket. Nem kell manuálisan törölni a virtuális gépeket és a hálózati adaptereket.
**A tesztfeladat-átvétel megőrzi az IP-címet** | Most már megtarthatja a forrás virtuális gép IP-címét egy vész-helyreállítási gyakorlat során, és válasszon egy statikus IP-címet egy teszt feladatátvételhez.

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló vészhelyreállítása

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
Új folyamatkiszolgálói riasztások | Új folyamatkiszolgálói riasztásokat adtunk hozzá. [További információ](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Hyper-V vészhelyreállítás

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
Tárfiók | A Site Recovery mostantól támogatja a storage-fiókok használatát, amelyek tűzfala engedélyezve van a Hyper-V és az Azure vész-helyreállítási.  Kiválaszthatja a tűzfal-kompatibilis tárfiókok célfiókként, vagy a gyorsítótár-tároló. Ha tűzfallal kompatibilis fiókot használ, győződjön meg arról, hogy engedélyezi a megbízható Microsoft-szolgáltatások engedélyezését.<br/><br/> Ez a System Center VMM-mel rendelkező vagy anélkül lévő Hyper-V virtuális gépek esetében támogatott.


## <a name="updates-august-2019"></a>Frissítések (2019. augusztus)

### <a name="update-rollup-39"></a>Összegző frissítőcsomag 39

[A 39.összegző frissítőcsomag](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A hely-helyreállítási ügynökök és szolgáltatók frissítései (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az Azure virtuális gép vész-helyreállítási új funkcióit a táblázat foglalja össze.

**Szolgáltatás** | **Részletek**
--- | ---
**Titkosítás Az Azure AD nélkül** | Az Azure AD-alkalmazás nélküli titkosítás mostantól támogatott az Azure VM-replikáció windowsos felügyelt lemezekre.
**Átvételi idő hálózati erőforrásai** | Ha egy másik régióba, most már csatolhatja a hálózati erőforrás-beállítások (NSG-k, terheléselosztás, nyilvános IP-cím) egy virtuális géphez.

## <a name="updates-july-2019"></a>Frissítések (2019. július)

### <a name="update-rollup-38"></a>Összegző frissítőcsomag 38

[A 38-as összegző frissítőcsomag](https://support.microsoft.com/help/4513507/) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A hely-helyreállítási ügynökök és szolgáltatók frissítései (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="general"></a>Általános kérdések

A Site Recovery mostantól támogatja az általános célú v2-es tárfiókok használatát a gyorsítótár-tárhoz vagy a céltárolóhoz. Korábban csak a v1 támogatott.

### <a name="vmware-to-azure-disaster-recovery"></a>Vészhelyreállítás VMware-ről Azure-ba

Most már replikálhatja a lemezeket legfeljebb 8 TB-ig, ha felügyelt lemezekkel replikálja egy Azure virtuális gépre.


## <a name="updates-june-2019"></a>Frissítések (2019. június)

### <a name="update-rollup-37"></a>Összegző frissítőcsomag 37

[A 37.összegző frissítőcsomag](https://support.microsoft.com/help/4508614/) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A hely-helyreállítási ügynökök és szolgáltatók frissítései (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló vészhelyreállítása

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**GPT-partíciók** | A 37-es összegző frissítőcsomagtól (a mobilszolgáltatás 9.25.5241.1-es verziója) kezdve legfeljebb öt GPT-partíciótámogatott az UEFI-ben. A frissítés előtt négy támogatott.



## <a name="updates-may-2019"></a>Frissítések (2019. május)

### <a name="update-rollup-36"></a>Összegző frissítőcsomag 36

[A 36.](https://support.microsoft.com/help/4503156)

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A site recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)

### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Hozzáadott lemezek replikálása** | Engedélyezze a replikációt az Azure-beli virtuális géphez hozzáadott adatlemezekszámára, amelyek már engedélyezve vannak a vészhelyreállításhoz. [További információ](azure-to-azure-enable-replication-added-disk.md).
**Automatikus frissítések** | A vész-helyreállítási engedélyezve lévő Azure-szolgáltatásokon futó Mobility szolgáltatásbővítmény automatikus frissítéseinek konfigurálásakor most már kiválaszthat egy meglévő automatizálási fiókot, amelyet a Site Recovery által létrehozott alapértelmezett fiók használata helyett használhat. [További információ](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló vészhelyreállítása

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Kiszolgálófigyelés feldolgozása** | A helyszíni VMware virtuális gépek és fizikai kiszolgálók vészhelyreállítása érdekében figyelheti és elháríthatja a folyamatkiszolgálóval kapcsolatos problémákat a kiszolgáló állapotjelentésével és riasztásaival. [További információ](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Frissítések (2019. március)

### <a name="update-rollup-35"></a>Összegző frissítőcsomag 35

[A 35.](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery)

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A site recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon)
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon)

### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló vészhelyreállítása

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Felügyelt lemezek** | A helyszíni VMware virtuális gépek és a fizikai kiszolgálók replikációja mostantól közvetlenül az Azure-ban felügyelt lemezekre történik. A helyszíni adatokat a rendszer egy gyorsítótár-tárfiókba küldi az Azure-ban, és helyreállítási pontokjönnek létre a felügyelt lemezeka célhelyen. Ez biztosítja, hogy nem kell több céltár-fiókot kezelnie.
**Konfigurációs kiszolgáló** | A Site Recovery mostantól támogatja a több hálózati adapterrel rendelkező konfigurációs kiszolgálókat. További adapterek hozzáadása a konfigurációs kiszolgáló virtuális gép, mielőtt regisztrálna a konfigurációs kiszolgáló a tárolóban. Ha később hozzáadja, újra regisztrálnia kell a kiszolgálót a tárolóban.


## <a name="updates-february-2019"></a>Frissítések (2019. február)

### <a name="update-rollup-34"></a>Összegző frissítőcsomag 34

[A 34.összegző frissítőcsomag](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).


### <a name="update-rollup-33"></a>Összegző frissítőcsomag 33

[A 33-as összegző frissítőcsomag](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).


### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Hálózatleképezés** | Az Azure Virtuálisgép vész-helyreállítási, most már használhatja bármely rendelkezésre álló célhálózat, ha engedélyezi a replikációt.
**Standard SSD** | Most már beállíthatja a vészhelyreállítást az Azure virtuális gépekhez [standard SSD-lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)használatával.
**Tárolóhelyek – Közvetlen** | Az Azure VM-alkalmazásokon futó alkalmazások vész-helyreállítási beállításához a [Storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) használatával magas rendelkezésre állás érdekében.  A Közvetlen tárolóhelyek (S2D) és a Site Recovery használatával átfogó védelmet nyújt az Azure virtuális gép számítási feladatainak. Az S2D lehetővé teszi egy vendégfürt üzemeltetését az Azure-ban. Ez különösen akkor hasznos, ha a virtuális gép kritikus alkalmazást üzemeltet, például AZ SAP ASCS-réteget, az SQL Servert vagy a kibővített fájlkiszolgálót.


### <a name="vmwarephysical-server-disaster-recovery"></a>VMware/fizikai kiszolgáló vészhelyreállítása

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux BRTFS fájlrendszer** | A Site Recovery mostantól támogatja a VMware virtuális gépek BRTFS fájlrendszerrel való replikációját. A replikáció nem támogatott, ha:<br/><br/>- A BTRFS fájlrendszer alkötete a replikáció engedélyezése után megváltozik.<br/><br/>- A fájlrendszer több lemezre van elosztva.<br/><br/>- A BTRFS fájlrendszer támogatja a RAID-et.
**Windows Server 2019** | A Windows Server 2019 rendszert futtató gépek támogatása.


## <a name="updates-january-2019"></a>Frissítések (2019. január)


### <a name="accelerated-networking-azure-vms"></a>Gyorsított hálózatkezelés (Azure virtuális gépek)

A gyorsított hálózatkezelés lehetővé teszi az egygyökérű I/O-virtualizációt (SR-IOV) a virtuális géphez, javítva a hálózati teljesítményt. Ha engedélyezi a replikációt egy Azure virtuális gép, Site Recovery észleli, hogy a gyorsított hálózatkezelés engedélyezve van. Ha ez az, miután a feladatátvevő site recovery automatikusan konfigurálja a gyorsított hálózati az Azure VM célreplikán, [windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) os és [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms)rendszeren is.

[További információ](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Összegző frissítőcsomag 32

[A 32.](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery)

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).

### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | A RedHat Workstation 6/7, valamint az Ubuntu, a Debian és a SUSE új kernelverzióival bővült.
**Tárolóhelyek – Közvetlen** | A Site Recovery támogatja az Azure virtuális gépeket a Közvetlen tárolóhely (S2D) használatával.

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware virtuális gépek/fizikai kiszolgálók vészhelyreállítása

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | A Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 és Oracle Linux 7.6, valamint az Ubuntu, debian és SUSE új kernelverzióihoz került támogatás.


### <a name="update-rollup-31"></a>Összegző frissítőcsomag 31

[A 31.](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).

### <a name="vmware-vmsphysical-servers-replication"></a>VMware virtuális gépek/fizikai kiszolgálók replikációja

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** |  Az Oracle Linux 6.8, Oracle Linux 6.9 és Oracle Linux 7.0 támogatással bővült a Red Hat-kompatibilis kernel, valamint a Törhetetlen Enterprise Kernel (UEK) Release 5.
**Lvm** | Az LVM és LVM2 kötetek támogatása hozzáadva.<br/><br/> A lemezpartíciókon és lvm-köteteken található /boot könyvtár mostantól támogatott.
**Könyvtárak** | A rendszer támogatást nyújtott a külön partícióként vagy olyan fájlrendszerként beállított könyvtárakhoz, amelyek nem ugyanazon a rendszerlemezen találhatók:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Dinamikus lemezek támogatása hozzáadva.
**Feladatátvétel** | Továbbfejlesztett feladatátvételi idő vmware virtuális gépek, ahol storvsc és vsbus nem rendszerindító illesztőprogramok.
**UEFI-támogatás** | Az Azure virtuális gépek nem támogatják az UEFI rendszerindítási típust. Most már áttelepítheti a helyszíni fizikai kiszolgálókat az UEFI-vel az Azure-ba a Site Recovery segítségével. A Site Recovery úgy telepíti át a kiszolgálót, hogy a rendszerindítási típust bios-ra konvertálja az áttelepítés előtt. A Site Recovery korábban csak virtuális gépekre vonatkozóan támogatta ezt az átalakítást. A Windows Server 2012 vagy újabb rendszert futtató fizikai kiszolgálók hoz támogatás érhető el.

### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Az Oracle Linux 6.8, Oracle Linux 6.9 és Oracle Linux 7.0 támogatással bővült a Red Hat-kompatibilis kernel, valamint a Törhetetlen Enterprise Kernel (UEK) Release 5.
**Linux BRTFS fájlrendszer** | Az Azure-beli virtuális gépek támogatottak.
**Azure virtuális gépek a rendelkezésre állási zónákban** | Engedélyezheti a replikációt egy másik régióba a rendelkezésre állási zónákban üzembe helyezett Azure virtuális gépek számára. Most már engedélyezheti a replikációt egy Azure virtuális gép, és állítsa be a feladatátvételi cél egy virtuális gép példány, egy virtuális gép egy rendelkezésre állási csoportban, vagy egy virtuális gép egy rendelkezésre állási zónában. A beállítás nincs hatással a replikációra. [Olvassa el](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) a bejelentést.
**Tűzfallal kompatibilis tároló (portál/PowerShell)** | Tűzfallal [rendelkező tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-network-security)hoz hozzáadott támogatás.<br/><br/> A tűzfallal rendelkező tárfiókoknem felügyelt lemezeivel rendelkező Azure-virtuális gépekreplikálhatók egy másik Azure-régióba vészhelyreállításhoz.<br/><br/> A tűzfallal rendelkező tárfiókok at nem felügyelt lemezek céltárfiókjaként használhatja.<br/><br/> A portálon támogatott és a PowerShell használatával.

## <a name="updates-december-2018"></a>Frissítések (2018. december)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>A Mobility szolgáltatás (Azure-beli virtuális gépek) automatikus frissítései

A Site Recovery lehetőséget adott a Mobilitás szolgáltatás bővítmény automatikus frissítéséhez. A Mobility szolgáltatás bővítmény telepítve van minden Egyes Azure virtuális gép replikált Site Recovery. Ha engedélyezi a replikációt, megadhatja, hogy a Site Recovery kezelje-e a bővítmény frissítéseit.

A frissítések nem igényelnek virtuális gép újraindítását, és nincsenek hatással a replikációra. [További információ](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Díjkalkulátor az Azure virtuális gép vész-helyreállítási

Az Azure-beli virtuális gépek vészhelyreállítása a virtuális gépek licencelési költségeit, valamint a hálózati és tárolási költségeket. Az Azure [díjkalkulátort](https://aka.ms/a2a-cost-estimator) biztosít, amely segít kitalálni ezeket a költségeket. A Site Recovery mostantól egy [példadíjszabási becslést](https://aka.ms/a2a-cost-estimator) ad, amely egy háromrétegű alkalmazás on-ra épül egy mintatelepítést, amely hat virtuális gépet használ 12 standard HDD-lemezzel és 6 prémium szintű SSD-lemezzel.

- A minta feltételezi, hogy az adatok változása 10 GB naponta standard, és 20 GB prémium.
- Az adott központi telepítéshez módosíthatja a változókat a költségek becsléséhez.
- Megadhatja a virtuális gépek számát, a felügyelt lemezek számát és típusát, valamint a virtuális gépek várható teljes adatváltozási sebességét.
- Ezenkívül a sávszélesség-költségek becsléséhez tömörítési tényezőt is alkalmazhat.

[Olvassa el](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) a bejelentést.


## <a name="updates-october-2018"></a>Frissítések (2018. október)

### <a name="update-rollup-30"></a>Összegző frissítőcsomag 30

[A 30.összegző frissítőcsomag](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).

### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási
Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Régió támogatása** | Site Recovery támogatás hozzáadva az Australia Central 1 és az Australia Central 2 esetében.
**A lemeztitkosítás támogatása** | Az Azure-lemeztitkosítással (ADE) titkosított Azure-beli virtuális gépek vész-helyreállítási támogatásával bővült az Azure AD alkalmazás. [További információ](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Lemez kizárása** | Uninitialized lemezek most automatikusan ki vannak zárva az Azure VM replikációja során.
**Tűzfallal kompatibilis tároló (PowerShell)** | Tűzfallal [rendelkező tárfiókok](https://docs.microsoft.com/azure/storage/common/storage-network-security)hoz hozzáadott támogatás.<br/><br/> A tűzfallal rendelkező tárfiókoknem felügyelt lemezeivel rendelkező Azure-virtuális gépekreplikálhatók egy másik Azure-régióba vészhelyreállításhoz.<br/><br/> A tűzfallal rendelkező tárfiókok at nem felügyelt lemezek céltárfiókjaként használhatja.<br/><br/> Csak a PowerShell használatával támogatott.


### <a name="update-rollup-29"></a>Összegző frissítőcsomag 29

[A 29.összegző frissítőcsomag](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).


## <a name="updates-august-2018"></a>Frissítések (2018. augusztus)

### <a name="update-rollup-28"></a>Összegző frissítőcsomag 28

[A 28.összegző frissítőcsomag](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) a következő frissítéseket tartalmazza.

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).

### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási
Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Hozzáadva a RedHat Enterprise Linux 6.10 támogatása; CentOS 6.10.<br/><br/>
**Felhőtámogatás** | Támogatott vész-helyreállítási Azure-beli virtuális gépek a németországi felhőben.
**Előfizetések közötti vészhelyreállítás** | Az Azure-beli virtuális gépek replikálásának támogatása az egyik régióban egy másik régióba egy másik előfizetésben, ugyanazon Az Azure Active Directory-bérlőn belül. [További információ](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/fizikai kiszolgáló vészhelyreállítása
Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás hozzáadva a RedHat Enterprise Linux 6.10, CentOS 6.10-hez.<br/><br/> A GUID partíciós tábla (GPT) partícióstílusát használó Linux-alapú virtuális gépek már támogatottak. További információkért tekintse át az [Azure VM gyIK-et.](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks)
**Vész-helyreállítási virtuális gépek áttelepítés után** | A vész-helyreállítási egy másodlagos régióban egy helyszíni VMware virtuális gép áttelepített az Azure-ba, anélkül, hogy távolítsa el a mobilitási szolgáltatás a virtuális gépen a replikáció engedélyezése előtt.
**Windows Server 2008** | A Windows Server 2008 R2/2008 64 és 32 bites rendszert futtató áttelepítési gépek támogatása.<br/><br/> Csak áttelepítés (replikáció és feladatátvétel). A feladat-visszavétel nem támogatott.

## <a name="updates-july-2018"></a>Frissítések (2018. július)

### <a name="update-rollup-27-july-2018"></a>Összegző frissítőcsomag (2018. július)

[A 27.](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery)

**Frissítés** | **Részletek**
--- | ---
**Szolgáltatók és ügynökök** | A Site Recovery ügynökök és szolgáltatók frissítése (az összesítésben részletezett módon).
**Problémajavítások/fejlesztések** | Számos javítás és javítás (az összesítésben részletezett módon).

### <a name="azure-vm-disaster-recovery"></a>Az Azure Virtuálisgép vész-helyreállítási

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás hozzáadva a Red Hat Enterprise Linux 7.5-höz.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>VMware VM/fizikai kiszolgáló vészhelyreállítása

Az ebben a hónapban hozzáadott funkciók a táblázatban vannak összefoglalva.

**Szolgáltatás** | **Részletek**
--- | ---
**Linux-támogatás** | Támogatás hozzáadva a Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12-hez.



## <a name="next-steps"></a>További lépések

Az [Azure](https://azure.microsoft.com/updates/?product=site-recovery) Updates oldalon naprakészen tarthatja frissítéseinket.
