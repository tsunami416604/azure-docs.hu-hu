---
title: A VMware-szolgáltatás támogatási mátrixa/fizikai vészhelyreállítás az Azure Site Recovery szolgáltatásban
description: Összefoglalja a VMware virtuális gépek és a fizikai kiszolgáló azure-ba történő vész-helyreállítási támogatásának támogatását az Azure Site Recovery használatával.
ms.topic: conceptual
ms.date: 2/24/2020
ms.openlocfilehash: 02448754abd92eab9e095a5eaff10861f8b5e5e4
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606035"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Támogatási mátrix a VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vészhelyreállításához

Ez a cikk összefoglalja a támogatott összetevők és beállítások a vész-helyreállítási VMware virtuális gépek és a fizikai kiszolgálók az Azure-ba az [Azure Site Recovery](site-recovery-overview.md)használatával.

- [További információ](vmware-azure-architecture.md) a VMware VM/physical server vész-helyreállítási architektúrájáról.
- Kövesse [oktatóanyagainkat,](tutorial-prepare-azure.md) hogy kipróbálja a katasztrófa utáni helyreállítást.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

**Forgatókönyv** | **Részletek**
--- | ---
A VMware virtuális gépek vészhelyreállítása | Helyszíni VMware virtuális gépek replikálása az Azure-ba. Ezt a forgatókönyvet az Azure Portalon vagy a [PowerShell](vmware-azure-disaster-recovery-powershell.md)használatával telepítheti.
Fizikai kiszolgálók vészhelyreállítása | Helyszíni Windows/Linux fizikai kiszolgálók replikálása az Azure-ba. Ezt a forgatókönyvet az Azure Portalon telepítheti.

## <a name="on-premises-virtualization-servers"></a>Helyszíni virtualizálási kiszolgálók

**Kiszolgáló** | **Követelmények** | **Részletek**
--- | --- | ---
vCenter Server | 6.7-es, 6.5-ös, 6.0-s vagy 5.5-ös verzió | Azt javasoljuk, hogy használja a vCenter-kiszolgáló a vész-helyreállítási telepítés.
vSphere házigazdák | 6.7-es, 6.5-ös, 6.0-s vagy 5.5-ös verzió | Azt javasoljuk, hogy a vSphere-állomások és a vCenter-kiszolgálók ugyanabban a hálózatban találhatók, mint a folyamatkiszolgáló. Alapértelmezés szerint a folyamatkiszolgáló a konfigurációs kiszolgálón fut. [További információ](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Hely-helyreállítási konfigurációs kiszolgáló

A konfigurációs kiszolgáló egy helyszíni gép, amely a Site Recovery összetevőit futtatja, beleértve a konfigurációs kiszolgálót, a folyamatkiszolgálót és a fő célkiszolgálót.

- VMware virtuális gépek esetén a konfigurációs kiszolgálót egy OVF-sablon letöltésével állíthatja be egy VMware vm létrehozásához.
- Fizikai kiszolgálók esetén manuálisan kell beállítani a konfigurációs kiszolgálógépet.

**Összetevő** | **Követelmények**
--- |---
Processzormagok | 8
RAM | 16 GB
Lemezek száma | 3 lemez<br/><br/> A lemezek közé tartozik az operációs rendszer lemeze, a kiszolgáló gyorsítótárának feldolgozása és a feladat-visszavétel megőrzési meghajtója.
Szabad lemezterület | 600 GB hely a folyamatkiszolgáló gyorsítótárának.
Szabad lemezterület | 600 GB hely a megőrzési meghajtó számára.
Operációs rendszer  | Windows Server 2012 R2 vagy Windows Server 2016 asztali környezetben <br/><br> Ha azt tervezi, hogy a készülék beépített fő tárolóját használja feladat-visszavételhez, győződjön meg arról, hogy az operációs rendszer verziója azonos vagy magasabb, mint a replikált elemek.|
Operációs rendszer területi beállítása | Angol (en-us)
[PowerCLI között](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Nem szükséges a konfigurációs kiszolgáló [9.14-es](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) vagy újabb verziójához.
Windows Server-szerepkörök | Az Active Directory tartományi szolgáltatások engedélyezésének lehetővé tétele; Internet Information Services (IIS) vagy Hyper-V.
Csoportházirendek| - A parancssorhoz való hozzáférés megakadályozása. <br/> - Hozzáférés megakadályozása a rendszerleíró adatbázis szerkesztő eszközök. <br/> - Megbízhatósági logika a fájlmellékletekhez. <br/> - Kapcsolja be a Script Execution. <br/> - [További információ](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Győződjön meg róla, hogy:<br/><br/> - Nincs már meglévő alapértelmezett webhelye <br/> - Engedélyezése [névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Engedélyezése [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) beállítás  <br/> - Nincs már létező weboldal / app hallgatása port 443<br/>
Hálózati adapter típusa | VMXNET3 (VMware vm-ként telepítve)
IP-cím típusa | Statikus
Portok | 443 használt vezérlő csatorna vezénylési<br/>9443 adatátvitelhez

## <a name="replicated-machines"></a>Replikált gépek

A Site Recovery támogatja a támogatott gépen futó munkaterhelés ek replikációját.

> [!Note]
> Az alábbi táblázat a BIOS-rendszerindítással rendelkező gépek támogatását sorolja fel. Az UEFI-alapú gépek támogatása a [Tárolás](#storage) részben található.

**Összetevő** | **Részletek**
--- | ---
A gép beállításai | Az Azure-ra replikáló gépeknek meg kell felelniük [az Azure követelményeinek.](#azure-vm-requirements)
Gépmunkaterhelés | A Site Recovery támogatja a támogatott gépen futó munkaterhelés ek replikációját. [További információ](https://aka.ms/asr_workload).
Gép neve | Győződjön meg arról, hogy a gép megjelenítendő neve nem tartozik az [Azure számára fenntartott erőforrásnevekbe](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-reserved-resource-name)<br/><br/> A logikai kötetnevek nem érzékenyek a kis- és nagybetűkre. Győződjön meg arról, hogy az eszközön nincs két kötet azonos nevű. Pl.: A "voLUME1", "volume1" nevű kötetek nem védhetők az Azure Site Recovery használatával.
Windows Server 2019 | A [34-es összegző](https://support.microsoft.com/help/4490016) frissítőcsomag (a Mobilitási szolgáltatás 9.22-es verziója) által támogatott.
64 bites Windows Server 2016 | Server Core, Server with Desktop Experience esetén támogatott.
Windows Server 2012 R2 / Windows Server 2012 | Támogatott.
Windows Server 2008 R2 SP1 szervizcsomaggal. | Támogatott.<br/><br/> A Mobility service agent [9.30-as](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) verziójából [szervizverezési veremfrissítésre (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítésre](https://support.microsoft.com/help/4474419) van szükség a Windows 2008 R2 SP1 vagy újabb rendszert futtató gépeken. Az SHA-1 nem támogatott 2019 szeptemberétől, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon telepíthető/frissít. További információ az [SHA-2 frissítésről és követelményekről.](https://aka.ms/SHA-2KB)
Windows Server 2008 SP2 vagy újabb verzió (64 bites/32 bites) |  Csak az áttelepítéshez támogatott. [További információ](migrate-tutorial-windows-server-2008.md).<br/><br/> A Mobility service agent [9.30-as](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) verziójából [szervizverezési veremfrissítésre (SSU)](https://support.microsoft.com/help/4493730) és [SHA-2 frissítésre](https://support.microsoft.com/help/4474419) van szükség Windows 2008 SP2 rendszerű gépeken. Az ISHA-1 nem támogatott 2019 szeptemberétől, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon telepíthető/frissít. További információ az [SHA-2 frissítésről és követelményekről.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Windows 10, Windows 8.1, Windows 8 | Támogatott.
64 bites Windows 7 SP1 | A [36-os összegző](https://support.microsoft.com/help/4503156) frissítőcsomag (a Mobilitási szolgáltatás 9.22-es verziója) által támogatott. </br></br> A Mobility service agent [9.30-tól](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) [szervizveremfrissítésre (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítésre](https://support.microsoft.com/help/4474419) van szükség a Windows 7 SP1 rendszerű gépeken.  Az SHA-1 nem támogatott 2019 szeptemberétől, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon telepíthető/frissít. További információ az [SHA-2 frissítésről és követelményekről.](https://support.microsoft.com/en-us/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
Linux | Csak a 64 bites rendszer támogatott. A 32 bites rendszer nem támogatott.<br/><br/>Minden Linux-kiszolgálón telepítve kell lennie [a Linux Integration Services (LIS) összetevőinek.](https://www.microsoft.com/download/details.aspx?id=55106) A tesztfeladat-átvétel/feladatátvétel után el kell indítania a kiszolgálót az Azure-ban. Ha a LIS-összetevők hiányoznak, győződjön meg arról, hogy telepítse az [összetevőket,](https://www.microsoft.com/download/details.aspx?id=55106) mielőtt engedélyezné a replikációt a gépek indítása az Azure-ban. <br/><br/> A Site Recovery vezényli a feladatátvételt linuxos kiszolgálók futtatásához az Azure-ban. A Linux-szállítók azonban korlátozhatják a támogatást csak olyan terjesztési verziókra, amelyek még nem érték el az élettartam a végét.<br/><br/> Linux disztribúciókon csak a disztribúciós alverzió-kiadás/-frissítés részét tartalmazó készletkernelek támogatottak.<br/><br/> A védett gépek frissítése a főbb Linux-disztribúciós verziókban nem támogatott. Frissítés, a replikáció letiltása, az operációs rendszer frissítése, majd a replikáció ismételt engedélyezése.<br/><br/> [További információ](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) a Linux és a nyílt forráskódú technológiák azure-beli támogatásáról.
Linux Red Hat Enterprise | 5.2–5.11</b><br/> 6.1–6.10</b> </br> 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1 <br/> A Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10 rendszert futtató kiszolgálókon nincs előre telepítve [a Linux Integration Services (LIS) összetevő.](https://www.microsoft.com/download/details.aspx?id=55106) Győződjön meg arról, hogy telepítse az [összetevőket,](https://www.microsoft.com/download/details.aspx?id=55106) mielőtt engedélyezné a replikációt a gépek indítása az Azure-ban.
Linux: CentOS | 5.2–5.11</b><br/> 6.1–6.10</b><br/> 7,0-7,6<br/> <br/> 8,0-8,1<br/><br/> A CentOS 5.2-5.11 & 6.1-6.10 rendszert futtató kiszolgálókon nincs előre telepítve [a Linux Integration Services (LIS) összetevő.](https://www.microsoft.com/download/details.aspx?id=55106) Győződjön meg arról, hogy telepítse az [összetevőket,](https://www.microsoft.com/download/details.aspx?id=55106) mielőtt engedélyezné a replikációt a gépek indítása az Azure-ban.
Ubuntu | Ubuntu 14.04 LTS kiszolgáló [(a támogatott kernelverziók áttekintése)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS kiszolgáló [(a támogatott kernelverziók áttekintése)](#ubuntu-kernel-versions) </br> Ubuntu 18.04 LTS kiszolgáló [(a támogatott kernelverziók áttekintése)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(tekintse át a támogatott kernel verziókat)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(a támogatott kernelverziók áttekintése)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(a támogatott kernelverziók áttekintése)](#suse-linux-enterprise-server-15-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> A replikált gépek frissítése a SUSE Linux Enterprise Server 11 SP3 sp4-ről SP4-re nem támogatott. A frissítéshez tiltsa le a replikációt, és engedélyezze újra a frissítést követően.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.6, 7.7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> A Red Hat-kompatibilis kernel vagy a Törhetetlen Vállalati Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) futtatása

> [!Note]
> Az Azure Site Recovery csak a [hosszú távú karbantartási csatornát (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) támogatja.  [A féléves csatornakiadások](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) jelenleg nem támogatottak.

### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel verziók

**Támogatott kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
14.04 LTS | [9.32][9.32 UR] | 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
14.04 LTS | [9.31][9.31 UR] | 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
14.04 LTS | [9.30][9.30 UR] | 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
14.04 LTS | [9.29][9.29 UR]| 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
|||
16.04 LTS | [9.32][9.32 UR] | 4.4.0-21-generic a 4.4.0-171-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-74-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1066-azúr|
16.04 LTS | [9.31][9.31 UR] | 4.4.0-21-generic a 4.4.0-170-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-72-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1063-azúr|
16.04 LTS | [9.30][9.30 UR] | 4.4.0-21-generic a 4.4.0-166-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-66-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1061-azúr|
16.04 LTS | [9.29][9.29 UR] | 4.4.0-21-generic a 4.4.0-164-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-64-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1059-azúr|
|||
18.04 LTS | [9.32][9.32 UR]| 4.15.0-20-generic a 4.15.0-74-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-37-generic </br> 5.3.0-19-generic-5.3.0-24-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1028-azúr </br> 5.3.0-1007-azure-5.3.0-1009-azúr|
18.04 LTS | [9.31][9.31 UR]| 4.15.0-20-generic a 4.15.0-72-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-37-generic </br> 5.3.0-19-generic-5.3.0-24-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1025-azúr </br> 5.3.0-1007-azúr|
18.04 LTS | [9.30][9.30 UR] | 4.15.0-20-generic a 4.15.0-66-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-32-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1023-azúr|
18.04 LTS | [9.29][9.29 UR] | 4.15.0-20-generic a 4.15.0-62-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-27-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1018-azúr|

### <a name="debian-kernel-versions"></a>Debian kernel verziók


**Támogatott kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
Debian, 7. | [9.29,][9.29 UR] [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR]| 3.2.0-4-amd64 -hoz 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.30,][9.30 UR] [9.31,][9.31 UR] [9.32][9.32 UR] | 3.16.0-4-amd64 -hoz 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 -hoz 4.9.0-0.bpo.11-amd64, 4.9.0-0.bpo.4-amd64 |
Debian 8 | [9.29][9.29 UR] | 3.16.0-4-amd64 -hoz 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 -hoz 4.9.0-0.bpo.9-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64, 4.9.0-0.bpo.9-amd64, 4.9.0-0.bpo. |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>A SUSE Linux Enterprise Server 12 támogatott kernelverziói

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.28][9.28 UR] | SP1 3.12.49-11-default érték 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.117-default</br></br>SP3 4.4.73-5-default érték 4.4.180-94.100-alapértelmezett</br></br>SP3 4.4.138-4.7-azure -tól 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.29-default</br>SP4 4.12.14-6.3-azure-tól 4.12.14-6.23-azure-ig |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default érték 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default to 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure -tól 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure-tól 4.12.14-6.15-azure-ig |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default érték 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default érték 4.4.178-94.91-alapértelmezett</br></br>SP3 4.4.138-4.7-azure -tól 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure -tól 4.12.14-6.9-azúr |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default érték 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default to 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default to 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default to 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default érték 4.4.176-94.88-alapértelmezett</br></br>SP3 4.4.138-4.7-azure -tól 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default to 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure -tól 4.12.14-6.9-azúr |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>A SUSE Linux Enterprise Server 15 támogatott kernelverziói

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 és 15 SP1 | [9.32](https://support.microsoft.com/help/4550047/) | Minden készlet SUSE 15 és 15 kernelek támogatottak. </br></br> 4.12.14-5.5-azure-tól 4.12.14-8.22-azure

## <a name="linux-file-systemsguest-storage"></a>Linux fájlrendszerek/vendégtároló

**Összetevő** | **Támogatott**
--- | ---
Fájlrendszerek | ext3, ext4, XFS, BTRFS (a táblázat szerint alkalmazandó feltételek)
Kötetkezelő | - LvM támogatott.<br/> - A /boot on LVM a Mobility szolgáltatás [31-es frissítőcsomagjának](https://support.microsoft.com/help/4478871/) (a Mobility szolgáltatás 9.20-as verziójának) támogatásával érhető el. Ez nem támogatott a korábbi Mobilitási szolgáltatás verzióiban.<br/> - Több operációsrendszer-lemez nem támogatott.
Paravirtualizált tárolóeszközök | A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.
Többvárólistás blokk I/O-eszközök | Nem támogatott.
Fizikai szerverek a HP CCISS tárolóvezérlővel | Nem támogatott.
Eszköz/csatlakoztatási pont elnevezési konvenciója | Az eszköz nevének vagy csatlakoztatási pontjának nevének egyedinek kell lennie.<br/> Győződjön meg arról, hogy nincs két eszköz/csatlakoztatási pont kis- és nagybetűket megkülönböztető neve. Például az eszközök elnevezése az azonos virtuális gép, mint *az eszköz1* és *az Eszköz1* nem támogatott.
Könyvtárak | Ha a Mobility szolgáltatás 9.20-as verziónál korábbi verzióját futtatja (a [31. összegző frissítőcsomagban](https://support.microsoft.com/help/4478871/)jelenik meg), akkor a következő korlátozások érvényesek:<br/><br/> - Ezeknek a könyvtáraknak (ha külön partícióként/fájlrendszerként vannak beállítva) ugyanazon az operációsrendszer-lemezen kell lenniük a forráskiszolgálón: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - A /boot könyvtárnak lemezpartíción kell lennie, nem LVM-kötetnek.<br/><br/> A 9.20-as verziótól kezdve ezek a korlátozások nem érvényesek. 
Rendszerindító könyvtár | - A rendszerindító lemezek nem lehetnek GPT partícióformátumban. Ez egy Azure-architektúra korlátozás. A GPT-lemezek adatlemezként támogatottak.<br/><br/> A virtuális gép több rendszerindító lemeze nem támogatott<br/><br/> - A /boot egy LVM köteten több lemezen nem támogatott.<br/> - A rendszerindító lemez nélküli gép nem replikálható.
Szabad helyigény| 2 GB a /root partíción <br/><br/> 250 MB a telepítési mappában
XFSv5 | Az XFS fájlrendszerek XFSv5 funkciói, például a metaadat-ellenőrzőösszeg támogatottak (Mobilitási szolgáltatás 9.10-es verziójától).<br/> A xfs_info segédprogrammal ellenőrizze a partíció XFS szuperblokkját. Ha `ftype` 1-re van állítva, akkor az XFSv5 funkciók használatban vannak.
Btrfs | A BTRFS a Mobility szolgáltatás [34.24.](https://support.microsoft.com/help/4490016) A BTRFS nem támogatott, ha:<br/><br/> - A BTRFS fájlrendszer alvolumezete a védelem engedélyezése után megváltozik.</br> - A BTRFS fájlrendszer több lemezre van elosztva.</br> - A BTRFS fájlrendszer támogatja a RAID-et.

## <a name="vmdisk-management"></a>Virtuális gép/lemez kezelése

**Művelet** | **Részletek**
--- | ---
Lemez átméretezése replikált virtuális gépen | A forrás virtuális gép feladatátvétel előtt, közvetlenül a virtuális gép tulajdonságai. Nem kell letiltani/újra engedélyezni a replikációt.<br/><br/> Ha módosítja a forrás virtuális gép feladatátvétel után, a módosítások nem rögzíti.<br/><br/> Ha módosítja a lemez méretét az Azure virtuális gép feladatátvétel után, ha feladat-vissza, Site Recovery létrehoz egy új virtuális gép a frissítéseket.
Lemez hozzáadása replikált virtuális géphez | Nem támogatott.<br/> Tiltsa le a virtuális gép replikációját, adja hozzá a lemezt, majd engedélyezze újra a replikációt.

## <a name="network"></a>Network (Hálózat)

**Összetevő** | **Támogatott**
--- | ---
Hálózati hálózati adapterek összeállása | VMware virtuális gépek esetén támogatott. <br/><br/>Fizikai számítógép-replikáció esetén nem támogatott.
Host hálózati VLAN | Igen.
Gazdahálózat IPv4 | Igen.
Hálózati IPv6 gazdagép | Nem.
Vendég/kiszolgáló hálózati hálózati adapterek összeállása | Nem.
Vendég/kiszolgáló hálózati IPv4 | Igen.
Vendég/kiszolgáló hálózati IPv6 | Nem.
Vendég/kiszolgáló hálózat statikus IP-címe (Windows) | Igen.
Vendég/kiszolgáló hálózat statikus IP (Linux) | Igen. <br/><br/>A virtuális gépek úgy vannak konfigurálva, hogy a DHCP-t használják feladat-visszavételkor.
Vendég/kiszolgáló hálózattöbb hálózati adapter | Igen.


## <a name="azure-vm-network-after-failover"></a>Azure virtuálisgép-hálózat (feladatátvétel után)

**Összetevő** | **Támogatott**
--- | ---
Azure ExpressRoute | Igen
ILB | Igen
Elb | Igen
Azure Traffic Manager | Igen
Több hálózati adapter | Igen
Fenntartott IP-cím | Igen
IPv4 | Igen
Forrás IP-címének megőrzése | Igen
Az Azure virtuális hálózati szolgáltatásának végpontjai<br/> | Igen
Gyorsított hálózatkezelés | Nem

## <a name="storage"></a>Storage
**Összetevő** | **Támogatott**
--- | ---
Dinamikus lemez | Az operációs rendszer lemeze alaplemeznek kell lennie. <br/><br/>Az adatlemezek lehetnek dinamikus lemezek
Docker-lemez konfigurációja | Nem
GazdaNFS | Igen a VMware<br/><br/> Fizikai kiszolgálók esetében nem
Állomás SAN (iSCSI/FC) | Igen
Állomás vSAN | Igen a VMware<br/><br/> N/A fizikai kiszolgálókhoz
Gazdatöbbutas (MPIO) | Igen, tesztelt Microsoft DSM, EMC PowerPath 5.7 SP4, EMC PowerPath DSM claRiiON esetén
Virtuális gazdakötetek (VVols) | Igen a VMware<br/><br/> N/A fizikai kiszolgálókhoz
Vendég/kiszolgáló VMDK | Igen
Vendég/kiszolgáló megosztott fürtlemeze | Nem
Vendég/kiszolgáló titkosított lemez | Nem
Vendég/kiszolgáló NFS | Nem
Vendég/kiszolgáló iSCSI | Áttelepítéshez - Igen<br/>Vész-helyreállítási - Nem, az iSCSI feladat-visszavétela a virtuális gép hez csatolt lemezként
Vendég/kiszolgáló SMB 3.0 | Nem
Vendég/kiszolgáló RDM | Igen<br/><br/> N/A fizikai kiszolgálókhoz
Vendég/kiszolgáló lemez > 1 TB | Igen, a lemeznek 1024 MB-nál nagyobbnak kell lennie<br/><br/>Akár 8192 GB felügyelt lemezekre történő replikálásesetén (9,26-os verziótól)<br></br> Akár 4095 GB tárfiókokra történő replikálásesetén
Vendég/kiszolgáló lemez 4K logikai és 4k fizikai szektormérettel | Nem
Vendég/kiszolgáló lemez 4K logikai és 512 bájtos fizikai szektormérettel | Nem
Vendég/kiszolgáló kötet csíkozott lemezzel >4 TB <br/><br/>Logikai kötetkezelés (LVM)| Igen
Vendég/kiszolgáló – Tárolóhelyek | Nem
Vendég/kiszolgáló gyors annektált lemez hozzáadása/eltávolítása | Nem
Vendég/kiszolgáló – lemez kizárása | Igen
Vendég/kiszolgáló többutas (MPIO) | Nem
Vendég/kiszolgáló GPT-partíciók | Öt partíció t támogat a [37-es összegző frissítőcsomag](https://support.microsoft.com/help/4508614/) (a Mobility szolgáltatás 9.25-ös verziója) által. Korábban négy támogatott.
ReFS | A rugalmas fájlrendszert a 9.23-as vagy újabb mobilitási szolgáltatás támogatja
Vendég/kiszolgáló EFI/UEFI rendszerindítás | - Támogatott Windows Server 2012 vagy újabb, SLES 12 SP4 és RHEL 8.0 a mobilügynök 9.30-as verziótól kezdve<br/> - A biztonságos UEFI rendszerindítás típusa nem támogatott.

## <a name="replication-channels"></a>Replikációs csatornák

|**A replikáció típusa**   |**Támogatott**  |
|---------|---------|
|Kiszervezett adatátvitel (ODX)    |       Nem  |
|Offline vetés        |   Nem      |
| Azure Data Box | Nem

## <a name="azure-storage"></a>Azure Storage tárterület

**Összetevő** | **Támogatott**
--- | ---
Helyileg redundáns tárolás | Igen
Georedundáns tárolás | Igen
Írásvédett georedundáns tárolás (RA-GRS) | Igen
Hűvös tárolás | Nem
Forró tárolás| Nem
Blokkblobok | Nem
Inaktív titkosítás (SSE)| Igen
Inaktív titkosítás (CMK)| Igen (a PowerShell Az 3.3.0 modulon keresztül)
Prémium szintű Storage | Igen
Importálási/exportálási szolgáltatás | Nem
Azure Storage tűzfalak virtuális hálózatokhoz | Igen.<br/> A céltároló/gyorsítótártári fiókon konfigurálva (replikációs adatok tárolására szolgál).
Általános célú v2-es tárfiókok (gyakori és ritka elérésű rétegek) | Igen (A tranzakciós költségek lényegesen magasabbak a V2 esetében a V1-hez képest)

## <a name="azure-compute"></a>Azure-számítás

**Szolgáltatás** | **Támogatott**
--- | ---
Rendelkezésre állási csoportok | Igen
Rendelkezésre állási zónák | Nem
Hub | Igen
Felügyelt lemezek | Igen

## <a name="azure-vm-requirements"></a>Azure virtuálisgép-követelmények

Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az ebben a táblázatban összegzett Azure virtuális gép követelményeinek. Ha a Site Recovery a replikáció előfeltételeinek ellenőrzését futtatja, az ellenőrzés sikertelen lesz, ha a követelmények némelyike nem teljesül.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | Ellenőrizze a replikált gépek [támogatott operációs rendszereit.](#replicated-machines) | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemez mérete | Akár 2048 GB. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Akár 8192 GB felügyelt lemezre történő replikálásesetén (9,26-os verziótól)<br></br>Akár 4095 GB tárfiókba replikáláskor| Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott VHD | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A gép replikációjának engedélyezése előtt le kell tiltani a BitLocker szolgáltatást. |
a virtuális gép neve | 1 és 63 karakter között.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse a számítógép tulajdonságainak értékét a Site Recovery szolgáltatásban.

## <a name="resource-group-limits"></a>Erőforráscsoport korlátai

Ha meg szeretné tudni, hogy hány virtuális gép védhető egyetlen erőforráscsoport alatt, olvassa el az [előfizetési korlátokról és kvótákról szóló cikket.](/azure/azure-resource-manager/management/azure-subscription-service-limits#resource-group-limits)

## <a name="churn-limits"></a>Lemorzsolódási határértékek

Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza.
- Ezek a korlátok a tesztjeinken alapulnak, de nem fedik le az összes lehetséges alkalmazás I/O kombinációt.
- A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak.
- A legjobb eredmény érdekében javasoljuk, hogy futtassa a [Deployment Planner eszközt,](site-recovery-deployment-planner.md)és végezzen átfogó alkalmazástesztelést tesztfeladat-átvételek használatával az alkalmazás valós teljesítményképének leképezéséhez.

**Replikációs cél** | **Átlagos forráslemez I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |    Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB lemezenként


**Forrásadat-változás** | **Felső korlát**
---|---
Adatváltozás csúcsértéke az összes lemezen virtuális gépenként | 54 MB/s
Folyamatkiszolgáló által támogatott napi adatváltozás maximuma | 2 TB

- Ezek átlagos értékek, amelyek 30 százalékos I/O-átfedést feltételeznek.
- A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján.
- Ezek a számok egy körülbelül öt perces tipikus lemaradást feltételeznek. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.

## <a name="vault-tasks"></a>Trezorfeladatok

**Művelet** | **Támogatott**
--- | ---
Tároló áthelyezése erőforráscsoportok között | Nem
Tároló áthelyezése az előfizetéseken belül és között | Nem
Tároló, hálózat, Azure-beli virtuális gépek áthelyezése erőforráscsoportok között | Nem
A tárhely, a hálózat, az Azure-beli virtuális gépek áthelyezése az előfizetéseken belül és azok között. | Nem


## <a name="obtain-latest-components"></a>A legújabb összetevők beszerzése

**Név** | **Leírás** | **Részletek**
--- | --- | ---
Konfigurációs kiszolgáló | Telepített helyszíni.<br/> Koordinálja a helyszíni VMware-kiszolgálók vagy fizikai gépek és az Azure közötti kommunikációt. | - További információ a konfigurációs [kiszolgálóról.](vmware-physical-azure-config-process-server-overview.md)<br/> - További információ a legújabb verzióra való [frissítésről.](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)<br/> - További információ a konfigurációs kiszolgáló [beállításáról.](vmware-azure-deploy-configuration-server.md)
Folyamatkiszolgáló | Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón.<br/> Fogadja a replikációs adatokat, gyorsítótárazással, tömörítéssel és titkosítással optimalizálja azokat, és elküldi azokat az Azure-ba.<br/> A központi telepítés növekedésével további folyamatkiszolgálókat adhat hozzá a nagyobb mennyiségű replikációs forgalom kezeléséhez. | - További információ a [folyamatkiszolgálóról.](vmware-physical-azure-config-process-server-overview.md)<br/> - További információ a legújabb verzióra való [frissítésről.](vmware-azure-manage-process-server.md#upgrade-a-process-server)<br/> - További információ a kibővített folyamatkiszolgálók [beállításáról.](vmware-physical-large-deployment.md#set-up-a-process-server)
Mobility szolgáltatás | A replikálni kívánt VMware virtuális gépre vagy fizikai kiszolgálókra telepítve.<br/> Koordinálja a helyszíni VMware-kiszolgálók/fizikai kiszolgálók és az Azure közötti replikációt.| - További információ a Mobilitás [szolgáltatásról.](vmware-physical-mobility-service-overview.md)<br/> - További információ a legújabb verzióra való [frissítésről.](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal)<br/>



## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan](tutorial-prepare-azure.md) készítheti elő az Azure-t a VMware virtuális gépek vészutáni helyreállítására.

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
