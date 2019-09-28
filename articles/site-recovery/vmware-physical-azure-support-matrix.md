---
title: Támogatási mátrix a VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállításához a Azure Site Recovery használatával | Microsoft Docs
description: Összefoglalja a VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállításának támogatását az Azure-ban Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: raynew
ms.openlocfilehash: 0ff8fb7a2e03ccaccff4f84d77486238acba0ba5
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350233"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>A VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállítási mátrixának támogatása

Ez a cikk a VMware virtuális gépek és a fizikai kiszolgálók Azure-ba való vész-helyreállításának támogatott összetevőit és beállításait foglalja össze [Azure site Recovery](site-recovery-overview.md)használatával.

- [További](vmware-azure-architecture.md) információ a VMware VM/fizikai kiszolgáló vész-helyreállítási architektúráról.
- A vész-helyreállítás kipróbálásához kövesse az [oktatóanyagokat](tutorial-prepare-azure.md) .

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

**Forgatókönyv** | **Részletek**
--- | ---
A VMware virtuális gépek vész-helyreállítása | Helyszíni VMware virtuális gépek replikálása az Azure-ba. Ezt a forgatókönyvet a Azure Portal vagy a [PowerShell](vmware-azure-disaster-recovery-powershell.md)használatával is telepítheti.
Fizikai kiszolgálók vész-helyreállítása | Helyszíni Windows-/Linux-alapú fizikai kiszolgálók replikálása az Azure-ba. Ezt a forgatókönyvet a Azure Portal helyezheti üzembe.

## <a name="on-premises-virtualization-servers"></a>Helyszíni virtualizálási kiszolgálók

**Server** | **Követelmények** | **Részletek**
--- | --- | ---
vCenter Server | 6,7, 6,5, 6,0 vagy 5,5 verzió | Javasoljuk, hogy használjon egy vCenter-kiszolgálót a vész-helyreállítási környezetben.
vSphere-gazdagépek | 6,7, 6,5, 6,0 vagy 5,5 verzió | Azt javasoljuk, hogy a vSphere-gazdagépek és a vCenter-kiszolgálók ugyanabban a hálózatban legyenek, mint a Process Server. Alapértelmezés szerint a Process Server fut a konfigurációs kiszolgálón. [További információk](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery konfigurációs kiszolgáló

A konfigurációs kiszolgáló egy helyszíni számítógép, amely Site Recovery összetevőket futtat, beleértve a konfigurációs kiszolgálót, a feldolgozási kiszolgálót és a fő célkiszolgáló kiszolgálóját.

- VMware virtuális gépek esetén a konfigurációs kiszolgálót úgy állíthatja be, hogy letölt egy OVF-sablont egy VMware virtuális gép létrehozásához.
- Fizikai kiszolgálók esetében manuálisan állítsa be a konfigurációs kiszolgáló számítógépét.

**Összetevő** | **Követelmények**
--- |---
CPU-magok | 8
RAM | 16 GB
Lemezek száma | 3 lemez<br/><br/> A lemezek közé tartozik az operációsrendszer-lemez, a Process Server cache lemez és a visszatartási meghajtó a feladat-visszavételhez.
Lemez szabad területe | 600 GB lemezterület a folyamat kiszolgálói gyorsítótárához.
Lemez szabad területe | 600 GB lemezterület az adatmegőrzési meghajtó számára.
Operációs rendszer  | Windows Server 2012 R2 vagy Windows Server 2016 asztali felülettel |
Operációs rendszer területi beállítása | Angol (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | A konfigurációs kiszolgáló [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) -es vagy újabb verziójához nem szükséges. 
Windows Server-szerepkörök | Ne engedélyezze a Active Directory tartományi szolgáltatások; Internet Information Services (IIS) vagy Hyper-V. 
Csoportházirendek| – A parancssor elérésének tiltása. <br/> – A beállításjegyzék szerkesztési eszközeihez való hozzáférés megakadályozása. <br/> – A fájlmellékletek megbízhatósági logikája. <br/> – A parancsfájlok végrehajtásának bekapcsolása. <br/> - [tudj meg többet](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Győződjön meg róla, hogy:<br/><br/> -Még nincs alapértelmezett webhelye <br/> – [Névtelen hitelesítés](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) engedélyezése <br/> – [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) -beállítás engedélyezése  <br/> – Ne hallgassa meg előre meglévő webhelyet/alkalmazást a 443-as porton<br/>
Hálózati adapter típusa | VMXNET3 (VMware virtuális gépként való üzembe helyezéskor)
IP-cím típusa | Statikus tartalom
Portok | 443 a vezérlési csatorna koordinálásához használatos<br/>9443 adatátvitelhez

## <a name="replicated-machines"></a>Replikált gépek

Site Recovery támogatja a támogatott gépen futó munkaterhelések replikálását.

**Összetevő** | **Részletek**
--- | ---
Számítógép beállításai | Az Azure-ba replikált gépeknek meg kell felelniük az [Azure-követelményeknek](#azure-vm-requirements).
Gépi munkaterhelés | Site Recovery támogatja a támogatott gépen futó munkaterhelések replikálását. [További információk](https://aka.ms/asr_workload).
Windows | – Windows Server 2019 (a 34-es [kumulatív frissítés](https://support.microsoft.com/help/4490016) (a mobilitási szolgáltatás 9,22-es verziója) támogatott.<br/> – Windows Server 2016 (64 bites Server Core, kiszolgáló asztali felülettel)<br/> – Windows Server 2012 R2, Windows Server 2012<br/> – Windows Server 2008 R2, legalább SP1 szervizcsomaggal.<br/> -Windows Server 2008, 64 és 32-bit legalább SP2-vel]. Csak Migrálás esetén támogatott. [További információk](migrate-tutorial-windows-server-2008.md).<br/> -Windows 10, Windows 8,1, Windows 8, Windows 7 64-bit (támogatott a következő [36 kumulatív frissítéssel](https://support.microsoft.com/help/4503156) (a mobilitási szolgáltatás 9,22-es verziója). A Windows 7 RTM nem támogatott. 
Linux | Csak a 64 bites rendszer támogatott. a 32 bites rendszer nem támogatott.<br/><br/>Minden Linux-kiszolgálón telepítve kell lennie a [Linux Integration Services (lis) összetevőknek](https://www.microsoft.com/download/details.aspx?id=55106) . A feladatátvétel/feladatátvételi teszt után a kiszolgálót az Azure-ban kell elindítania. Ha a LIS-összetevők hiányoznak, győződjön meg arról, hogy az [összetevőket](https://www.microsoft.com/download/details.aspx?id=55106) telepíteni kell, mielőtt engedélyezné a gépek replikálását az Azure-ban. <br/><br/> Site Recovery összehangolja a feladatátvételt, hogy Linux-kiszolgálókat futtasson az Azure-ban. Előfordulhat azonban, hogy a Linux-szállítók csak olyan terjesztési verziókra korlátozzák a támogatást, amelyek nem értek véget az élettartamuk.<br/><br/> A Linux-disztribúciók esetében csak a terjesztési alverzió kiadásának vagy frissítésének részét képező állomány-kernelek támogatottak.<br/><br/> Nem támogatott a védett gépek frissítése a nagy Linux-disztribúciós verziók között. A frissítéshez tiltsa le a replikálást, frissítse az operációs rendszert, majd engedélyezze újra a replikációt.<br/><br/> [További](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) információ a Linux és a nyílt forráskódú technológiák támogatásáról az Azure-ban.
Linux Red Hat Enterprise | 5,2 – 5,11</b><br/> 6,1 – 6,10</b><br/> 7,0 – 7,6<br/> <br/> A Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.10-es verzióját futtató kiszolgálók nem rendelkeznek előre telepített [Linux Integration Services-(lis-) összetevőkkel](https://www.microsoft.com/download/details.aspx?id=55106) . Győződjön meg arról, hogy telepíti az [összetevőket](https://www.microsoft.com/download/details.aspx?id=55106) , mielőtt engedélyezi a gépek replikálását az Azure-ban.
Linux: CentOS | 5,2 – 5,11</b><br/> 6,1 – 6,10</b><br/> 7,0 – 7,6<br/> <br/> A CentOS 5.2-5.11 & 6.1-6.10 rendszert futtató kiszolgálókon nincs telepítve a [Linux Integration Services (lis) összetevő](https://www.microsoft.com/download/details.aspx?id=55106) . Győződjön meg arról, hogy telepíti az [összetevőket](https://www.microsoft.com/download/details.aspx?id=55106) , mielőtt engedélyezi a gépek replikálását az Azure-ban.
Ubuntu | Ubuntu 14,04 LTS [-kiszolgáló (a támogatott kernel-verziók áttekintése)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16,04 LTS [-kiszolgáló (a támogatott kernel-verziók áttekintése)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(a támogatott kernel-verziók áttekintése)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(a támogatott kernel-verziók áttekintése)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> Nem támogatottak a replikált gépek SUSE Linux Enterprise Server 11 SP3 szervizcsomagról SP4 verzióra való frissítése. A frissítéshez tiltsa le a replikációt, majd engedélyezze újra a frissítést.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6<br/><br/> A Red hat-kompatibilis kernel vagy a nem törhető vállalati kernel kiadásának futtatása 3, 4 & 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Ubuntu kernel-verziók


**Támogatott kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
14,04 LTS | [9,28][9.28 UR]| 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
14,04 LTS | [9,27][9.27 UR]| 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
14,04 LTS | [9.26][9.26 UR]| 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
14,04 LTS | [9.25][9.25 UR]  | 3.13.0-24 – általános – 3.13.0-169-Generic,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános 4.4.0-146-Generic,<br/>4.15.0-1023-Azure-4.15.0-1042-Azure |
|||
16.04 LTS | [9,28][9.28 UR] | 4.4.0-21 – általános – 4.4.0 – 159 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 58 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1055-Azure|
16.04 LTS | [9,27][9.27 UR] | 4.4.0-21 – általános – 4.4.0 – 154 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 54 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure – 4.15.0-1050 – Azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 50 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1045-Azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21 – általános 4.4.0-146-Generic,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 48 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1042-Azure|

### <a name="debian-kernel-versions"></a>Debian kernel-verziók


**Támogatott kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | [9,25][9.25 UR],[9,26][9.26 UR], [9,27][9.27 UR], [9,28][9.28 UR]| 3.2.0-4-amd64 to 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9,28][9.28 UR] | 3.16.0-4-amd64 – 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 – 4.9.0 -0. BPO. 9 – amd64 |
Debian 8 | [9,27][9.27 UR] | 3.16.0-4-amd64 – 3.16.0-9-amd64, 4.9.0 -0. BPO. 4 – amd64 – 4.9.0 -0. BPO. 9 – amd64 |
Debian 8 | [9,25][9.25 UR], [9,26][9.26 UR] | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 támogatott kernel-verzió

**Kiadási** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,28][9.28 UR] | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.117-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-Azure-4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.29 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.23-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,27][9.27 UR] | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.114-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-Azure-4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.19 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.15-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.109-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-Azure-4.4.178-4,28-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.16 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.104-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-Azure-4.4.176-4.25-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.13 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.9-Azure |

## <a name="linux-file-systemsguest-storage"></a>Linux-fájlrendszerek/Guest Storage

**Összetevő** | **Támogatott**
--- | ---
Fájlrendszerek | ext3, ext4, XFS
A Volume Manager | -Az LVM támogatott.<br/> -a/boot on LVM a [31. kumulatív frissítéssel](https://support.microsoft.com/help/4478871/) (a mobilitási szolgáltatás 9,20-es verziójával) kezdődően támogatott. A korábbi mobilitási szolgáltatás verzióiban nem támogatott.<br/> – Több operációsrendszer-lemez nem támogatott.
Paravirtualizált | A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.
Több üzenetsor-blokkoló IO-eszközök | Nem támogatott.
Fizikai kiszolgálók a HP CCISS Storage-vezérlővel | Nem támogatott.
Eszköz/csatlakoztatási pont elnevezési konvenciója | Az eszköz nevének vagy a csatlakoztatási pont nevének egyedinek kell lennie.<br/> Győződjön meg arról, hogy két eszköz/csatlakoztatási pont nem tartalmaz kis-és nagybetűket megkülönböztető neveket. Például a *device1* és a *device1* azonos virtuális géphez tartozó elnevezési eszközök nem támogatottak.
Könyvtárak | Ha a 9,20-es verziónál korábbi (a [31. kumulatív frissítésben](https://support.microsoft.com/help/4478871/)kiadott) mobilitási szolgáltatás verzióját futtatja, akkor ezek a korlátozások érvényesek:<br/><br/> – Ezeket a könyvtárakat (ha különálló partícióként/fájlrendszerként kell beállítani) a forráskiszolgáló ugyanazon operációsrendszer-lemezén kell lennie:/(root),/boot,/usr,/usr/local,/var,/etc.</br> – A/boot könyvtárának lemezes partíción kell lennie, és nem lehet LVM-kötet.<br/><br/> A 9,20-es verziótól kezdődően ezek a korlátozások nem érvényesek. 
Rendszerindítási könyvtár | – A rendszerindító lemezek nem lehetnek GPT-partíciós formátumban. Ez egy Azure-architektúra korlátozása. A GPT-lemezek adatlemezként támogatottak.<br/><br/> Egy virtuális gépen több rendszerindító lemez nem támogatott<br/><br/> – a/boot egy LVM köteten több lemezen nem támogatott.<br/> – A rendszerindító lemez nélküli gépet nem lehet replikálni.
Szabad lemezterületre vonatkozó követelmények| 2 GB a/root-partíción <br/><br/> 250 MB a telepítési mappában
XFSv5 | A XFS-fájlrendszerek (például a metaadatok ellenőrzőösszege) XFSv5 funkciói támogatottak (a mobilitási szolgáltatás 9,10-os verziója).<br/> A xfs_info segédprogram használatával keresse meg a partíció XFS-feloldóját. Ha `ftype` a értéke 1, akkor a XFSv5 funkciók használatban vannak.
BTRFS | A BTRFS a 34-es [kumulatív frissítéssel](https://support.microsoft.com/help/4490016) (a mobilitási szolgáltatás 9,22-es verziójával) támogatott. A BTRFS nem támogatott, ha:<br/><br/> – A védelem engedélyezése után a rendszer megváltoztatja a BTRFS fájlrendszer alkötetét.</br> – A BTRFS fájlrendszer több lemezre oszlik.</br> – A BTRFS fájlrendszer támogatja a RAID-t.

## <a name="vmdisk-management"></a>VIRTUÁLIS gépek/lemezek kezelése

**Művelet** | **Részletek**
--- | ---
Lemez átméretezése a replikált virtuális gépen | Támogatott.
Lemez hozzáadása a replikált virtuális gépen | Nem támogatott.<br/> Tiltsa le a virtuális gép replikációját, adja hozzá a lemezt, majd engedélyezze újra a replikációt.

## <a name="network"></a>Network (Hálózat)

**Összetevő** | **Támogatott**
--- | ---
Gazdagép hálózati hálózati ADAPTERek összevonása | VMware virtuális gépek esetén támogatott. <br/><br/>Fizikai gépek replikálásához nem támogatott.
Gazdagép hálózati VLAN-je | Igen.
Gazda hálózati IPv4 | Igen.
Gazda hálózati IPv6 | Nem.
Vendég/kiszolgáló hálózati ADAPTERek összevonása | Nem.
Vendég/kiszolgáló hálózati IPv4 | Igen.
Vendég/kiszolgáló hálózati IPv6 | Nem.
Vendég/kiszolgáló hálózati statikus IP-címe (Windows) | Igen.
Vendég/kiszolgáló hálózati statikus IP-címe (Linux) | Igen. <br/><br/>A virtuális gépek DHCP használatára vannak konfigurálva a feladat-visszavétel során.
Vendég/kiszolgáló hálózata több hálózati adapterrel | Igen.


## <a name="azure-vm-network-after-failover"></a>Azure VM-hálózat (feladatátvétel után)

**Összetevő** | **Támogatott**
--- | ---
Azure ExpressRoute | Igen
ILB | Igen
ELB | Igen
Azure Traffic Manager | Igen
Multi-NIC | Igen
Fenntartott IP-cím | Igen
IPv4 | Igen
Forrás IP-címének megőrzése | Igen
Azure Virtual Network szolgáltatásbeli végpontok<br/> | Igen
Felgyorsított hálózat | Nem

## <a name="storage"></a>Storage
**Összetevő** | **Támogatott**
--- | ---
Dinamikus lemez | Az operációsrendszer-lemeznek alaplemeznek kell lennie. <br/><br/>Az adatlemezek lehetnek dinamikus lemezek
Docker-lemez konfigurációja | Nem
NFS-gazdagép | Igen, VMware-hez<br/><br/> Nem a fizikai kiszolgálók esetében
Gazdagép SAN (iSCSI/FC) | Igen
Gazdagép vSAN | Igen, VMware-hez<br/><br/> N/A fizikai kiszolgálókhoz
Gazdagép többutas (MPIO) | Igen, tesztelték a Microsoft DSM, EMC PowerPath 5,7 SP4, EMC PowerPath DSM for CLARiiON
Gazdagép virtuális kötetei (VVols) | Igen, VMware-hez<br/><br/> N/A fizikai kiszolgálókhoz
Vendég/kiszolgáló VMDK | Igen
Vendég/kiszolgáló megosztott fürtözött lemeze | Nem
Vendég/kiszolgáló által titkosított lemez | Nem
Vendég/kiszolgáló NFS | Nem
Vendég/kiszolgáló iSCSI | Áttelepítéshez – igen<br/>Vész-helyreállítás esetén az iSCSI a virtuális géphez csatlakoztatott lemezként fogja felvenni a feladat-visszavételt
Vendég/kiszolgáló SMB 3,0 | Nem
Vendég/kiszolgáló RDM | Igen<br/><br/> N/A fizikai kiszolgálókhoz
Vendég/kiszolgáló lemez > 1 TB | Igen, a lemeznek nagyobbnak kell lennie, mint 1024 MB<br/><br/>Akár 8 192 GB a felügyelt lemezekre való replikáláskor (9,26-es verzió)<br></br> Akár 4 095 GB a Storage-fiókokba való replikáláskor
Vendég/kiszolgáló lemez 4K logikai és 4k fizikai szektor méretével | Nem
Vendég/kiszolgáló lemez 4K logikai és 512 bájtos fizikai szektor méretével | Nem
Vendég/kiszolgáló kötet szalagos lemezzel > 4 TB <br/><br/>Logikai kötet kezelése (LVM)| Igen
Vendég/kiszolgáló – tárolóhelyek | Nem
Vendég/kiszolgáló – gyors Hozzáadás/Eltávolítás lemez | Nem
Vendég/kiszolgáló – lemez kizárása | Igen
Vendég/kiszolgáló többutas (MPIO) | Nem
Vendég/kiszolgáló GPT-partíciók | Az 37-es [kumulatív frissítés](https://support.microsoft.com/help/4508614/) (a mobilitási szolgáltatás 9,25-es verziója) a következő öt partíciót támogatja:. Korábban csak négyet támogatott.
ReFS | A rugalmas fájlrendszer a mobilitási szolgáltatás 9,23-es vagy újabb verziójával támogatott
Vendég/kiszolgáló EFI/UEFI rendszerindítás | – A mobilitási szolgáltatás 9,13-es vagy újabb verziójának futtatása esetén támogatott.<br/> – A Windows Server 2012 vagy újabb rendszert futtató, VMware virtuális gépek vagy fizikai kiszolgálók áttelepítésekor támogatott az Azure-ban.<br/> – A virtuális gépeket csak áttelepítésre lehet replikálni. A helyszíni feladat-visszavétel nem támogatott.<br/> -Csak NTFS támogatott <br/> – A Secure UEFI rendszerindítási típus nem támogatott. <br/> – A lemezes szektor méretének 512 bájtnak kell lennie a fizikai szektorban.

## <a name="replication-channels"></a>Replikációs csatornák

|**Replikáció típusa**   |**Támogatott**  |
|---------|---------|
|Kiszervezett adatátvitel (ODX)    |       Nem  |
|Kapcsolat nélküli beültetés        |   Nem      |
| Azure Data Box | Nem

## <a name="azure-storage"></a>Azure Storage tárterület

**Összetevő** | **Támogatott**
--- | ---
Helyileg redundáns tárolás | Igen
Georedundáns tárolás | Igen
Olvasási hozzáférés – geo-redundáns tárolás | Igen
Ritkán használt tároló | Nem
Gyors tárolás| Nem
Blokkblobok | Nem
Titkosítás – Rest (SSE)| Igen
Prémium szintű Storage | Igen
Importálási/exportálási szolgáltatás | Nem
Azure Storage-tűzfalak a virtuális hálózatok | Igen.<br/> A cél Storage/cache Storage-fiókra van konfigurálva (a replikációs adattároláshoz használatos).
Általános célú v2 Storage-fiókok (gyakori és ritka elérésű szintek) | Igen (a tranzakciós költségek lényegesen magasabbak a v2-höz képest, mint a v1)

## <a name="azure-compute"></a>Azure-számítás

**Funkció** | **Támogatott**
--- | ---
Rendelkezésre állási csoportok | Igen
Rendelkezésre állási zónák | Nem
HUB | Igen
Felügyelt lemezek | Igen

## <a name="azure-vm-requirements"></a>Azure-beli virtuális gépekre vonatkozó követelmények

Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az ebben a táblázatban összefoglalt Azure-beli virtuálisgép-követelményeknek. Ha Site Recovery futtatja az előfeltétel-ellenőrzés replikálását, akkor az ellenőrzés sikertelen lesz, ha egyes követelmények nem teljesülnek.

**Összetevő** | **Követelmények** | **Részletek**
--- | --- | ---
Vendég operációs rendszer | A replikált gépek [támogatott operációs rendszereinek](#replicated-machines) ellenőrzése. | Az ellenőrzés sikertelen, ha nem támogatott.
Vendég operációs rendszer architektúrája | 64 bites. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációs rendszer lemezének mérete | Akár 2 048 GB-ig. | Az ellenőrzés sikertelen, ha nem támogatott.
Operációsrendszer-lemezek száma | 1 | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemezek száma | 64 vagy kevesebb. | Az ellenőrzés sikertelen, ha nem támogatott.
Adatlemez mérete | Akár 8 192 GB a felügyelt lemezre való replikáláskor (9,26-es verzió)<br></br>Akár 4 095 GB a Storage-fiókba való replikáláskor| Az ellenőrzés sikertelen, ha nem támogatott.
Hálózati adapterek | Több adapter is támogatott. |
Megosztott virtuális merevlemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
FC-lemez | Nem támogatott. | Az ellenőrzés sikertelen, ha nem támogatott.
BitLocker | Nem támogatott. | A számítógép replikálásának engedélyezése előtt le kell tiltani a BitLockert. |
a virtuális gép neve | 1 – 63 karakter.<br/><br/> Csak betűket, számokat és kötőjelet tartalmazhat.<br/><br/> A gép nevének betűvel vagy számmal kell kezdődnie és végződnie. |  Frissítse az értéket a Site Recovery számítógép tulajdonságai között.

## <a name="resource-group-limits"></a>Erőforráscsoport korlátai

Az egyetlen erőforráscsoport által védett virtuális gépek számának megismeréséhez tekintse meg az [előfizetés korlátai és kvótái](https://docs.microsoft.com/azure/azure-subscription-service-limits#resource-group-limits) című cikket.

## <a name="churn-limits"></a>Adatforgalom korlátai

Az alábbi táblázat az Azure Site Recovery korlátait tartalmazza. 
- Ezek a korlátok a tesztek alapján jelennek meg, de nem fedik le az alkalmazások összes lehetséges I/O-kombinációját.
- A tényleges eredmények a saját alkalmazásának I/O-műveletei alapján változhatnak.
- A legjobb eredmények elérése érdekében javasoljuk, hogy futtassa az [Deployment Planner eszközt](site-recovery-deployment-planner.md), és végezzen el nagy mennyiségű alkalmazást tesztelési feladatátvétel használatával, hogy az alkalmazás valódi teljesítményű képet kapjon.

**Replikációs cél** | **Forráslemez átlagos I/O-mérete** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB/lemez


**Forrásadat-változás** | **Felső korlát**
---|---
Átlagos adatváltozás virtuális gépenként| 25 MB/s
Adatváltozás csúcsértéke az összes lemezen virtuális gépenként | 54 MB/s
Folyamatkiszolgáló által támogatott napi adatváltozás maximuma | 2 TB

- Ezek átlagos értékek, amelyek 30 százalékos I/O-átfedést feltételeznek.
- A Site Recovery képes magasabb átviteli sebesség kezelésére az átfedési arány, a nagyobb írási méretek és a számítási feladatok tényleges I/O-viselkedése alapján.
- Ezek a számok körülbelül öt percet vesznek igénybe. Ez azt jelenti, hogy a feltöltést követő öt percben megtörténik az adat feldolgozása, és létrejön egy helyreállítási pont.

## <a name="vault-tasks"></a>Tár feladatai

**Művelet** | **Támogatott**
--- | ---
Tár áthelyezése az erőforráscsoportok között | Nem
Tár áthelyezése az előfizetések között | Nem
Tárterület, hálózat, Azure-beli virtuális gépek áthelyezése az erőforráscsoportok között | Nem
Helyezze át a tárterületet, a hálózatot és az Azure-beli virtuális gépeket az előfizetések között. | Nem


## <a name="obtain-latest-components"></a>Legújabb összetevők beszerzése

**Name** | **Leírás** | **Részletek**
--- | --- | ---
Konfigurációs kiszolgáló | A helyszíni telepítése.<br/> Koordinálja a helyszíni VMware-kiszolgálók, a fizikai gépek és az Azure közötti kommunikációt. | - A konfigurációs kiszolgáló [ismertetése](vmware-physical-azure-config-process-server-overview.md) .<br/> - [További](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) információ a legújabb verzióra való frissítésről.<br/> - [Tudnivalók a konfigurációs kiszolgáló beállításáról](vmware-azure-deploy-configuration-server.md) . 
Folyamatkiszolgáló | Alapértelmezés szerint telepítve van a konfigurációs kiszolgálón.<br/> Fogadja a replikációs adatokat, optimalizálja azt gyorsítótárazással, tömörítéssel és titkosítással, majd elküldi az Azure-nak.<br/> Az üzembe helyezés során további folyamat-kiszolgálókat adhat hozzá a replikációs forgalom nagyobb mennyiségének kezeléséhez. | - A folyamat kiszolgálójának [megismerése](vmware-physical-azure-config-process-server-overview.md) .<br/> - [További](vmware-azure-manage-process-server.md#upgrade-a-process-server) információ a legújabb verzióra való frissítésről.<br/> - [További információ a](vmware-physical-large-deployment.md#set-up-a-process-server) kibővíthető folyamat-kiszolgálók beállításáról.
Mobilitási szolgáltatás | A replikálni kívánt VMware virtuális gépen vagy fizikai kiszolgálókon telepítve van.<br/> A helyszíni VMware-kiszolgálók/fizikai kiszolgálók és az Azure közötti replikáció koordinálása.| - [Ismerje meg](vmware-physical-mobility-service-overview.md) a mobilitási szolgáltatást.<br/> - [További](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) információ a legújabb verzióra való frissítésről.<br/> 



## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan](tutorial-prepare-azure.md) készítheti elő az Azure-t a VMWare virtuális gépek vész-helyreállításához.

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
