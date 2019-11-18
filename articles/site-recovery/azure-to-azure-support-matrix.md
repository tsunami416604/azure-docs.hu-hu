---
title: Támogatási mátrix az Azure-beli virtuális gépek vész-helyreállításához Azure Site Recovery
description: Összefoglalja az Azure-beli virtuális gépek vész-helyreállításának támogatását egy másodlagos régióba Azure Site Recovery használatával.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 267177dcdec25b8561b219ae79e40ce61af994df
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123843"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Támogatási mátrix az Azure-beli virtuális gépek Azure-régiók közötti vész-helyreállításához

Ez a cikk összefoglalja az Azure-beli virtuális gépeknek az egyik Azure-régióból a másikba való vész-helyreállításának támogatását és előfeltételeit a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="deployment-method-support"></a>Üzembe helyezési módszer támogatása

**Üzembe helyezés** |  **Támogatás**
--- | ---
**Azure Portal** | Támogatott.
**PowerShell** | Támogatott. [Részletek](azure-to-azure-powershell.md)
**REST API** | Támogatott.
**Parancssori felület** | Jelenleg nem támogatott


## <a name="resource-support"></a>Erőforrás-támogatás

**Erőforrás-művelet** | **Részletek**
--- | --- 
**Tárolók áthelyezése az erőforráscsoportok között** | Nem támogatott
**Számítási/tárolási/hálózati erőforrások áthelyezése az erőforráscsoportok között** | Nem támogatott.<br/><br/> Ha a virtuális gép replikálása után áthelyez egy virtuális gépet vagy a hozzá tartozó összetevőket, például a Storage/Network szolgáltatást, le kell tiltania, majd újra engedélyeznie kell a virtuális gép replikálását.
**Azure-beli virtuális gépek replikálása egyik előfizetésből a másikba vész-helyreállítás céljából** | Ugyanazon Azure Active Directory bérlőn belül támogatott.
**Virtuális gépek áttelepítése régiók között a támogatott földrajzi fürtökön belül (és az előfizetések között)** | Ugyanazon Azure Active Directory bérlőn belül támogatott.
**Virtuális gépek migrálása ugyanazon a régión belül** | Nem támogatott.

## <a name="region-support"></a>Régió támogatása

A virtuális gépeket replikálhatja és helyreállíthatja az azonos földrajzi fürtön belüli két régió között. A földrajzi fürtöket az adatkésés és a szuverenitás szem előtt tartásával határozzák meg.


**Földrajzi fürt** | **Azure-régiók**
-- | --
Államok | Kelet-Kanada, Közép-Kanada, az USA déli középső régiója, az USA nyugati középső régiója, az USA keleti régiója, USA 2. keleti régiója, USA nyugati régiója, USA 2. középső régiója, USA
Európa | Egyesült Királyság nyugati régiója, Egyesült Királyság déli régiója, Észak-Európa, Nyugat-Európa, Közép-Franciaország, Dél-Franciaország, Dél-Afrika nyugati régiója, Dél-Afrika északi régiója, Kelet-Norvégia, Norvégia nyugati régiója
Ázsia | Dél-India, Közép-India, Nyugat-India, Délkelet-Ázsia, Kelet-Ázsia, Kelet-Japán, Nyugat-Japán, Korea középső régiója, Dél-Korea, Egyesült Arab Emírségek, Észak-Ausztrália
Ausztrália   | Kelet-Ausztrália, Délkelet-Ausztrália, Ausztrália középső régiója, Ausztrália 2. középső régiója
Azure Government    | USA-beli államigazgatás – Virginia, USA-beli államigazgatás, Egyesült Államok 
Németország | Közép-Németország, Északkelet-Németország
Kína | Kelet-Kína, Észak-Kína, China North2, China készletek
Országon belüli vész-helyreállításra fenntartott korlátozott régiók |A Középnyugat-Németország számára fenntartott, Nyugat-Svájc számára fenntartott Észak-Svájc, Franciaország középső ügyfelei számára fenntartott Észak-Németország 

>[!NOTE]
>
> - **Dél-Brazíliában**a következő régiókba replikálhatja és átveheti a feladatokat: USA déli középső régiója, USA nyugati középső régiója, USA 2. keleti régiója, USA nyugati régiója, USA 2. nyugati régiója és az USA északi középső régiója.
> - A Dél-Brazília csak olyan forrás-régióként használható, amelyből a virtuális gépek a Site Recovery használatával replikálhatók. Nem működhet célként megadott régióként. Ez a földrajzi távolságok miatti késési problémák miatt fordul elő. Vegye figyelembe, hogy ha a feladat a Dél-Brazília régióból való átadását célozza meg, a feladat-visszavétel Dél-Brazíliában is támogatott.
> - Olyan régiókban is dolgozhat, amelyekhez megfelelő hozzáférése van.
> - Ha a régió, amelyben létre kívánja hozni a tárolót, nem jelenik meg, győződjön meg arról, hogy az előfizetése hozzáfér az adott régióban található erőforrások létrehozásához.
> - Ha a replikáció engedélyezésekor nem látja a földrajzi fürtön belüli régiót, győződjön meg arról, hogy az előfizetése rendelkezik a virtuális gépek létrehozásához szükséges engedélyekkel az adott régióban.



## <a name="cache-storage"></a>Gyorsítótár-tároló

Ez a táblázat a Site Recovery által a replikálás során használt cache Storage-fiók támogatását foglalja össze.

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Általános célú v2 Storage-fiókok (gyors és lassú elérésű szint) | Támogatott | A GPv2 használata nem ajánlott, mert a v2 tranzakciós költségei lényegesen magasabbak, mint v1 Storage-fiókok.
Prémium szintű Storage | Nem támogatott | A költségek optimalizálása érdekében a standard szintű Storage-fiókok használhatók a gyorsítótár-tároláshoz.
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha a tűzfalon engedélyezve lévő cache Storage-fiókot vagy a célként megadott Storage-fiókot használja, győződjön meg arról, hogy ["megbízható Microsoft-szolgáltatások engedélyezése"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Győződjön meg arról is, hogy engedélyezi a hozzáférést a forrás vnet legalább egy alhálózatához.


## <a name="replicated-machine-operating-systems"></a>Replikált gépi operációs rendszerek

Site Recovery támogatja az ebben a részben felsorolt operációs rendszereket futtató Azure-beli virtuális gépek replikálását.

### <a name="windows"></a>Windows


**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2019 | A Server Core és a Server asztali felhasználói felülettel támogatott.
Windows Server 2016  | Támogatott Server Core, kiszolgáló asztali felülettel.
Windows Server 2012 R2 | Támogatott.
Windows Server 2012 | Támogatott.
Windows Server 2008 R2 SP1/SP2 | Támogatott.<br/><br/> Az Azure-beli virtuális gépek mobilitási szolgáltatás-bővítményének 2019. x. x verziójának 9.30. x. x verziójától (a várt kiadástól kezdve, a Windows Server 2008 R2 SP1/SP2 verziót futtató számítógépeken telepíteni kell a Windows [karbantartási verem frissítését (SSU)](https://support.microsoft.com/help/4490628) és az [SHA-2 frissítést](https://support.microsoft.com/help/4474419) .  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésével és követelményeivel](https://aka.ms/SHA-2KB) kapcsolatban.
Windows Server 2008 SP2 | Az Azure-beli virtuális gépek mobilitási szolgáltatás-bővítményének 2019. x. x verziójának 9.30. x. x verziójától (a várt kiadástól számítva) telepítenie kell egy Windows- [karbantartási verem frissítését (SSU)](https://support.microsoft.com/help/4493730) és [SHA-2 frissítést](https://support.microsoft.com/help/4474419) a Windows Server 2008 SP2-t futtató számítógépeken.  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésével és követelményeivel](https://aka.ms/SHA-2KB) kapcsolatban.
Windows 10 (x64) | Támogatott.
Windows 8,1 (x64) | Támogatott.
Windows 8 (x64) | Támogatott.
Windows 7 (x64) SP1-től | A 9.30. x. x verziótól (a várt kiadás, amely az Azure virtuális gépek mobilitási szolgáltatásának 2019. novembertől kezdődően kezdődik), telepítenie kell egy Windows- [karbantartási verem frissítését (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítést](https://support.microsoft.com/help/4474419) a Windows 7 SP1 rendszert futtató gépeken.  Az SHA-1 nem támogatott szeptember 2019-tól, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynök bővítmény nem a várt módon fog települni/frissíteni. További információ az [SHA-2 frissítésével és követelményeivel](https://aka.ms/SHA-2KB) kapcsolatban.



#### <a name="linux"></a>Linux

**Operációs rendszer** | **Részletek**
--- | ---
Red Hat Enterprise Linux | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6,[7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery)
CentOS | 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, 7,7, 8,0
Ubuntu 14,04 LTS-kiszolgáló | [Támogatott kernel-verziók](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16,04 LTS-kiszolgáló | [Támogatott kernel-verzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> A jelszó-alapú hitelesítést és bejelentkezést használó Ubuntu-kiszolgálók, valamint a Cloud-init csomag a felhőalapú virtuális gépek konfigurálásához előfordulhat, hogy a jelszó-alapú bejelentkezés le van tiltva a feladatátvételen (a cloudinit konfigurációjától függően). A jelszó alapú bejelentkezés a virtuális gépen újra engedélyezhető, ha alaphelyzetbe állítja a jelszót a támogatási > hibaelhárítási > beállítások menüjében (a Azure Portal a feladatátvételen átadott virtuális gép).
Ubuntu 18,04 LTS-kiszolgáló | [Támogatott kernel-verzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian 7 | [Támogatott kernel-verziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Támogatott kernel-verziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Támogatott kernel-verziók)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> A replikáló gépek SP3-ről SP4-re való frissítése nem támogatott. Ha egy replikált gépet frissítettek, le kell tiltania a replikálást, és újra engedélyeznie kell a replikálást a frissítés után.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> A Red hat-kompatibilis kernel vagy a nem törhető vállalati kernel kiadásának futtatása 3, 4 & 5 (UEK3, UEK4, UEK5) 


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Támogatott Ubuntu kernel-verziók az Azure Virtual Machines szolgáltatáshoz

**Kiadási** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
14,04 LTS | 9,28 | 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
14,04 LTS | 9,27 | 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
14,04 LTS | 9,26 | 3.13.0-24 – általános – 3.13.0 – 170 – általános,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.15.0-1023-Azure-4.15.0-1045-Azure |
14,04 LTS | 9,25 | 3.13.0-24 – általános – 3.13.0-169-Generic,<br/>3.16.0-25 – általános – 3.16.0-77 – általános,<br/>3.19.0-18 – általános – 3.19.0 – 80 – általános,<br/>4.2.0-18 – általános – 4.2.0 – 42 – általános,<br/>4.4.0-21 – általános 4.4.0-146-Generic,<br/>4.15.0-1023-Azure-4.15.0-1042-Azure |
|||
16.04 LTS | [9,30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21 – általános – 4.4.0 – 166 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0-66 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1061-Azure|
16.04 LTS | 9,28 | 4.4.0-21 – általános – 4.4.0 – 159 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 58 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1055-Azure|
16.04 LTS | 9,27 | 4.4.0-21 – általános – 4.4.0 – 154 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0-42 – általános<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0-55 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1051-Azure|
16.04 LTS | 9,26 | 4.4.0-21 – általános – 4.4.0 – 148 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0-42 – általános<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 50 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1045-Azure|
16.04 LTS | 9,25 | 4.4.0-21 – általános 4.4.0-146-Generic,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13 – általános – 4.15.0 – 48 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1042-Azure|
16.04 LTS | 9,24 | 4.4.0-21 – általános – 4.4.0 – 143 – általános,<br/>4.8.0-34 – általános – 4.8.0 – 58 – általános,<br/>4.10.0-14 – általános – 4.10.0 – 42 – általános,<br/>4.11.0-13-Generic – 4.11.0-14 – általános,<br/>4.13.0-16 – általános – 4.13.0 – 45 – általános,<br/>4.15.0-13-Generic – 4.15.0-46 – általános<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-ról 4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1040-Azure|
|||
18,04 LTS | [9,29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20 – általános – 4.15.0 – 64 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 29 – általános </br> 4.15.0-1009-Azure-4.15.0-1037-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure – 5.0.0-1020 – Azure|
18,04 LTS | [9,30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20 – általános – 4.15.0-66 – általános </br> 4.18.0-13 – általános – 4.18.0 – 25 – általános </br> 5.0.0-15 – általános – 5.0.0 – 32 – általános </br> 4.15.0-1009-Azure-4.15.0-1037-Azure </br> 4.18.0-1006-Azure-4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1023-Azure

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Az Azure Virtual Machines által támogatott Debian kernel-verziók

**Kiadási** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
Debian 7 | 9.25, 9.26, 9.27, 9.28 | 3.2.0-4-amd64 to 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9,28 | 3.16.0-4-amd64 – 3.16.0-10-amd64, 4.9.0 -0. BPO. 4-amd64 – 4.9.0 -0. BPO. 9 – amd64 |
Debian 8 | 9,27 | 3.16.0-4-amd64 – 3.16.0-9-amd64, 4.9.0 -0. BPO. 4 – amd64 – 4.9.0 -0. BPO. 9 – amd64 |
Debian 8 | 9,25, 9,26 | 3.16.0-4-amd64 to 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Támogatott SUSE Linux Enterprise Server 12 kernel-verzió az Azure Virtual Machines szolgáltatásban

**Kiadási** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,28 | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.117-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-Azure-4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.29 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.23-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,27 | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.117-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-Azure-4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.24 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.18-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,26 | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.109-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-Azure-4.4.178-4,28-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.16 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9,25 | SP1 3.12.49-11 – alapértelmezett érték: 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69 – alapértelmezett érték: 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-default a 4.4.121-92.104-default</br></br>SP3 4.4.73-5 – alapértelmezett érték: 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-Azure-4.4.176-4.25-Azure</br></br>SP4 4.12.14-94.41 – alapértelmezett érték: 4.12.14-95.13 – alapértelmezett</br>SP4 4.12.14-6.3-Azure-4.12.14-6.9-Azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikált gépek – Linux fájlrendszer/vendég tárterület

* Fájlrendszerek: ext3, ext4, ReiserFS (csak SUSE Linux Enterprise Server esetén), XFS, BTRFS
* Volume Manager: LVM2
* Többutas szoftver: Device Mapper


## <a name="replicated-machines---compute-settings"></a>Replikált gépek – számítási beállítások

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Méret | Bármely Azure-beli VM-méret legalább 2 CPU-maggal és 1 GB RAM-mal | Ellenőrizze az Azure-beli [virtuális gépek méretét](../virtual-machines/windows/sizes.md).
Rendelkezésre állási csoportok | Támogatott | Ha az alapértelmezett beállításokkal engedélyezi egy Azure-beli virtuális gép replikálását, a rendszer automatikusan létrehoz egy rendelkezésre állási készletet a forrás régió beállításai alapján. Ezek a beállítások módosíthatók.
Rendelkezésre állási zónák | Támogatott |
Hybrid use Benefit (HUB) | Támogatott | Ha a forrásoldali virtuális gépen engedélyezve van a HUB-licenc, a feladatátvételi teszt vagy a feladatátvételi művelet a HUB-licencet is használja.
Virtual Machine Scale Sets | Nem támogatott |
Azure Gallery-lemezképek – Microsoft published | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Azure Gallery-rendszerképek – harmadik féltől származó közzététel | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Egyéni rendszerképek – harmadik féltől származó közzététel | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Site Recovery használatával áttelepített virtuális gépek | Támogatott | Ha a VMware rendszerű virtuális gépet vagy fizikai gépet áttelepítette az Azure-ba a Site Recovery használatával, el kell távolítania a gépen futó mobilitási szolgáltatás régebbi verzióját, majd újra kell indítania a gépet egy másik Azure-régióba való replikálás előtt.
RBAC szabályzatok | Nem támogatott | A virtuális gépeken a szerepköralapú hozzáférés-vezérlési (RBAC) házirendek nem replikálódnak a célként megadott régióban található feladatátvételi virtuális gépre.
Bővítmények | Nem támogatott | A bővítmények nem replikálódnak a célként megadott régióban található feladatátvételi virtuális gépre. A feladatátvételt követően kézzel kell telepíteni.

## <a name="replicated-machines---disk-actions"></a>Replikált gépek – lemezes műveletek

**Művelet** | **Részletek**
-- | ---
Lemez átméretezése a replikált virtuális gépen | Támogatott
Lemez hozzáadása egy replikált virtuális géphez | Támogatott

## <a name="replicated-machines---storage"></a>Replikált gépek – tárolás

Ez a táblázat az Azure VM operációsrendszer-lemez, az adatlemez és az ideiglenes lemez támogatását foglalja össze.

- Fontos, hogy megfigyelje a virtuálisgép-lemezek korlátait és a [Linux](../virtual-machines/linux/disk-scalability-targets.md) és [Windows rendszerű](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépekre vonatkozó célokat, hogy elkerülje a teljesítménnyel kapcsolatos problémákat.
- Ha az alapértelmezett beállításokkal telepíti a szolgáltatást, Site Recovery automatikusan létrehozza a lemezeket és a Storage-fiókokat a forrás beállításai alapján.
- Ha testreszabja, győződjön meg arról, hogy kövesse az irányelveket.

**Összetevő** | **Támogatás** | **Részletek**
--- | --- | ---
OPERÁCIÓSRENDSZER-lemez maximális mérete | 2048 GB | [További](../virtual-machines/windows/managed-disks-overview.md) információ a VM-lemezekről.
Ideiglenes lemez | Nem támogatott | Az ideiglenes lemez mindig ki van zárva a replikációból.<br/><br/> Ne tároljon állandó adatmennyiséget az ideiglenes lemezen. [További információ](../virtual-machines/windows/managed-disks-overview.md).
Adatlemez maximális mérete | 8192 GB felügyelt lemezekhez<br></br>4095 GB a nem felügyelt lemezekhez|
Adatlemez minimális mérete | Nem felügyelt lemezekre vonatkozó korlátozás. 2 GB a felügyelt lemezekhez | 
Adatlemez maximális száma | Akár 64-ig, egy adott Azure-beli virtuálisgép-méret támogatásával összhangban | [További](../virtual-machines/windows/sizes.md) információ a virtuális gépek méreteiről.
Adatlemez változási aránya | A Premium Storage esetében legfeljebb 10 MB/s lemezterület. A standard szintű tároláshoz legfeljebb 2 MB/s lemez adható meg. | Ha a lemez átlagos adatváltozási sebessége folyamatosan meghaladja a maximális értéket, a replikálás nem fog megjelenni.<br/><br/>  Ha azonban a maximális érték szórványosan túllépi a replikálást, a replikáció felmerülhet, de előfordulhat, hogy némileg késleltetett helyreállítási pontok jelenhetnek meg.
Adatlemez – szabványos Storage-fiók | Támogatott |
Adatlemez – Premium Storage-fiók | Támogatott | Ha a virtuális gép rendelkezik lemezekkel a prémium és a standard szintű Storage-fiókok között, kiválaszthat egy másik cél Storage-fiókot az egyes lemezekhez, így biztosítva, hogy ugyanaz a tárolási konfiguráció a céltartományban.
Felügyelt lemez – standard | Olyan Azure-régiókban támogatott, ahol a Azure Site Recovery támogatott. |
Felügyelt lemez – prémium | Olyan Azure-régiókban támogatott, ahol a Azure Site Recovery támogatott. |
Standard SSD | Támogatott |
Redundancia | A LRS és a GRS támogatottak.<br/><br/> A ZRS nem támogatott.
Ritka elérésű és gyors tárolás | Nem támogatott | A virtuálisgép-lemezek nem támogatottak a ritka elérésű és a gyors tárolásban
Tárolóhelyek | Támogatott |
Titkosítás nyugalmi állapotban (SSE) | Támogatott | Az SSE a Storage-fiókok alapértelmezett beállítása.   
Inaktív titkosítás (CMK) | Nem támogatott |   
Azure Disk Encryption (ADE) Windows operációs rendszerhez | Felügyelt lemezekkel rendelkező virtuális gépek esetén támogatott. A nem felügyelt lemezeket használó virtuális gépek nem támogatottak |
Azure Disk Encryption (ADE) Linux operációs rendszerhez | Nem támogatott |
Gyors Hozzáadás | Támogatott | A replikált Azure-beli virtuális géphez hozzáadott adatlemez replikálásának engedélyezése támogatott a felügyelt lemezeket használó virtuális gépek esetében.
Gyors lemez eltávolítása | Nem támogatott | Ha eltávolít egy adatlemezt a virtuális gépen, le kell tiltania a replikációt, és újra engedélyeznie kell a replikációt a virtuális gépen.
Lemez kizárása | Támogatja. A konfiguráláshoz a [PowerShellt](azure-to-azure-exclude-disks.md) kell használnia. |  Az ideiglenes lemezek alapértelmezés szerint ki vannak zárva.
Közvetlen tárolóhelyek  | Összeomlás-konzisztens helyreállítási pontok esetén támogatott. Az alkalmazás konzisztens helyreállítási pontjai nem támogatottak. |
Kibővíthető fájlkiszolgáló  | Összeomlás-konzisztens helyreállítási pontok esetén támogatott. Az alkalmazás konzisztens helyreállítási pontjai nem támogatottak. |
LRS | Támogatott |
GRS | Támogatott |
RA-GRS | Támogatott |
ZRS | Nem támogatott |
Ritka elérésű és gyors tárolás | Nem támogatott | A virtuális gépek lemezei nem támogatottak a ritka és a gyakori tárterületen
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha korlátozza a virtuális hálózati hozzáférést a Storage-fiókokhoz, engedélyezze a [megbízható Microsoft-szolgáltatások engedélyezését](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Általános célú v2 Storage-fiókok (a gyors és a lassú elérésű szint) | Támogatott | A tranzakciós költségek jelentősen növekednek az általános célú v1 Storage-fiókokkal szemben
2\. generációs (UEFI rendszerindítási) | Támogatott

>[!IMPORTANT]
> A teljesítményproblémák elkerülése érdekében győződjön meg arról, hogy a virtuálisgép-lemezek méretezhetőségére és teljesítményére vonatkozó célokat követ a [Linux](../virtual-machines/linux/disk-scalability-targets.md) vagy a [Windows rendszerű](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépek esetében. Ha az alapértelmezett beállításokat használja, Site Recovery létrehozza a szükséges lemezeket és a Storage-fiókokat a forrás konfigurációja alapján. Ha testreszabja és kiválasztja a saját beállításait, kövesse a forrásként szolgáló virtuális gépek lemezének méretezhetőségét és teljesítményére vonatkozó célokat.

## <a name="limits-and-data-change-rates"></a>Korlátok és adatváltozások aránya

Az alábbi táblázat Site Recovery korlátozásokat foglalja össze.

- Ezek a korlátok a tesztek alapján jelennek meg, de nyilvánvalóan nem fedik le az alkalmazások minden lehetséges I/O-kombinációját.
- A tényleges eredmények az alkalmazás I/O-kombinációja alapján változhatnak.
- A lemezes adatforgalom és a virtuális gépek adatváltozása esetében két korlátozást kell figyelembe venni.

**Tárolási cél** | **Átlagos forrás lemez I/O** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB  | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |  Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB/lemez

## <a name="replicated-machines---networking"></a>Replikált gépek – hálózatkezelés
**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Hálózati adapter | Egy adott Azure-beli virtuálisgép-méret által támogatott maximális szám | A hálózati adapterek akkor jönnek létre, amikor a virtuális gép létrejön a feladatátvétel során.<br/><br/> A feladatátvételi virtuális gépen lévő hálózati adapterek száma a forrás virtuális gépen lévő hálózati adapterek számától függ, amikor a replikáció engedélyezve volt. Ha a replikáció engedélyezése után ad hozzá vagy távolít el egy hálózati adaptert, az nem befolyásolja a replikált virtuális gépen lévő hálózati adapterek számát a feladatátvételt követően. Azt is vegye figyelembe, hogy a hálózati adapterek sorrendje a feladatátvételt követően nem garantált, hogy megegyezzen az eredeti rendeléssel.
Internetes Load Balancer | Támogatott | Az előre konfigurált terheléselosztó hozzárendelése egy Azure Automation parancsfájl használatával helyreállítási tervben.
Belső terheléselosztó | Támogatott | Az előre konfigurált terheléselosztó hozzárendelése egy Azure Automation parancsfájl használatával helyreállítási tervben.
Nyilvános IP-cím | Támogatott | Társítson egy meglévő nyilvános IP-címet a hálózati adapterhez. Vagy hozzon létre egy nyilvános IP-címet, és társítsa azt a hálózati adapterhez egy helyreállítási tervben Azure Automation parancsfájl használatával.
NSG a hálózati adapteren | Támogatott | Társítsa a NSG a hálózati adapterhez egy helyreállítási tervben Azure Automation parancsfájl használatával.
NSG az alhálózaton | Támogatott | Társítsa a NSG az alhálózathoz egy helyreállítási tervben Azure Automation parancsfájl használatával.
Fenntartott (statikus) IP-cím | Támogatott | Ha a forrás virtuális gépen lévő hálózati adapter statikus IP-címmel rendelkezik, és a célként megadott alhálózatnak ugyanaz az IP-címe, akkor a feladatátvételi virtuális géphez van rendelve.<br/><br/> Ha a célként megadott alhálózat nem rendelkezik a rendelkezésre álló IP-címmel, az alhálózat egyik elérhető IP-címe a virtuális gép számára van fenntartva.<br/><br/> A **replikált elemekben** rögzített IP-címet és alhálózatot is megadhat > **Beállítások** > a **számítási és hálózati** > **hálózati adapterek**.
Dinamikus IP-cím | Támogatott | Ha a forrás hálózati adaptere dinamikus IP-címzést tartalmaz, a feladatátvételt okozó virtuális gép hálózati adaptere alapértelmezés szerint is dinamikus.<br/><br/> Ha szükséges, módosíthatja a rögzített IP-címet.
Több IP-cím | Nem támogatott | Ha olyan virtuális gépet hajt végre, amelynek több IP-címmel rendelkező hálózati adaptere van, akkor a rendszer csak a forrás régióban lévő hálózati adapter elsődleges IP-címét tárolja. Több IP-cím hozzárendeléséhez hozzáadhat virtuális gépeket egy [helyreállítási tervhez](recovery-plan-overview.md) , és csatolhat egy parancsfájlt további IP-címek hozzárendeléséhez a csomaghoz, vagy a módosítást manuálisan vagy parancsfájllal is elvégezheti a feladatátvétel után. 
Traffic Manager     | Támogatott | A Traffic Manager előre konfigurálható úgy, hogy a forgalmat a rendszer rendszeres időközönként a forrás régiójában lévő végpontra irányítsa, a feladatátvétel esetén pedig a cél régióban lévő végpontra.
Azure DNS | Támogatott |
Egyéni DNS  | Támogatott |
Nem hitelesített proxy | Támogatott | [Részletek](site-recovery-azure-to-azure-networking-guidance.md)    
Hitelesített proxy | Nem támogatott | Ha a virtuális gép hitelesített proxyt használ a kimenő kapcsolathoz, nem replikálható Azure Site Recovery használatával.    
Helyszíni VPN-helyek közötti kapcsolat<br/><br/>(ExpressRoute vagy anélkül)| Támogatott | Győződjön meg arról, hogy a UDR és a NSG úgy vannak konfigurálva, hogy a Site Recovery forgalom ne legyen átirányítva a helyszíni környezetbe. [Részletek](site-recovery-azure-to-azure-networking-guidance.md)    
VNET – VNET-kapcsolatok | Támogatott | [Részletek](site-recovery-azure-to-azure-networking-guidance.md)  
Virtuális hálózati szolgáltatásvégpontok | Támogatott | Ha korlátozza a virtuális hálózati hozzáférést a Storage-fiókokhoz, győződjön meg arról, hogy a megbízható Microsoft-szolgáltatások hozzáférése engedélyezett a Storage-fiókhoz.
Gyorsított hálózatkezelés | Támogatott | A gyorsított hálózatkezelést engedélyezni kell a forrásoldali virtuális gépen. [További információ](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>További lépések
- Olvassa el az Azure-beli virtuális gépek replikálásához szükséges [hálózatkezelési útmutatót](site-recovery-azure-to-azure-networking-guidance.md) .
- A vész-helyreállítás üzembe helyezése az Azure-beli [virtuális gépek replikálásával](site-recovery-azure-to-azure.md).
