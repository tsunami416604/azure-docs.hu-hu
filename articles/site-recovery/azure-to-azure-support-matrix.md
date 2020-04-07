---
title: Az Azure VM vész-helyreállítási szolgáltatásának támogatási mátrixa az Azure Site Recovery szolgáltatással
description: Összefoglalja az Azure-beli virtuális gépek vész-helyreállítási támogatását egy másodlagos régióba az Azure Site Recovery segítségével.
ms.topic: article
ms.date: 01/10/2020
ms.author: raynew
ms.openlocfilehash: f61f32ddc0a1cc6575907bc72522228b77552947
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673810"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Az Azure virtuális gép vész-helyreállítási mátrixának támogatási mátrixa az Azure-régiók között

Ez a cikk összefoglalja az Azure-beli virtuális gépek egyik Azure-régióból a másikba történő vész-helyreállítási támogatásának és előfeltételeit az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="deployment-method-support"></a>Telepítési módszer támogatása

**Környezet** |  **Támogatás**
--- | ---
**Azure Portal** | Támogatott.
**PowerShell** | Támogatott. [További információ](azure-to-azure-powershell.md)
**REST API** | Támogatott.
**parancssori felület** | Egyelőre nem támogatott


## <a name="resource-support"></a>Erőforrás-támogatás

**Erőforrás-művelet** | **Részletek**
--- | ---
**Tárolók áthelyezése erőforráscsoportok között** | Nem támogatott
**Számítási/tárolási/hálózati erőforrások áthelyezése erőforráscsoportok között** | Nem támogatott.<br/><br/> Ha áthelyez egy virtuális gép vagy a kapcsolódó összetevők, például a tároló/hálózat után a virtuális gép replikálása, le kell tiltania, majd újra engedélyeznie kell a replikációt a virtuális gép.
**Azure-beli virtuális gépek replikálása egyik előfizetésből a másikba vészhelyreállításhoz** | Ugyanebben az Azure Active Directory-bérlőben támogatott.
**Virtuális gépek áttelepítése a támogatott földrajzi fürtökön belüli régiók között (előfizetéseken belül és azok között)** | Ugyanebben az Azure Active Directory-bérlőben támogatott.
**Virtuális gépek áttelepítése ugyanabban a régióban** | Nem támogatott.

## <a name="region-support"></a>Régiós támogatás

A virtuális gépek replikálhatók és helyreállíthatók ugyanazon földrajzi fürt bármely két régiója között. A földrajzi fürtök meghatározása az adatok késésének és szuverenitásának szem előtt tartása.


**Földrajzi halmaz** | **Azure-régiók**
-- | --
Amerika | Kanada keleti része, Kanada közép-, USA középső középső régiója, USA nyugati középső régiója, USA keleti régiója 2, USA nyugati régiója 2, USA középső része, USA északi középső régiója
Európa | Egyesült Királyság nyugati, egyesült királyságbeli dél, észak-európai, nyugat-európai, dél-afrikai nyugat, észak-afrikai, norvégiai kelet, Norvégia Nyugat
Ázsia | Dél-India, Közép-India, Nyugat-India, Délkelet-Ázsia, Kelet-Ázsia, Kelet-Japán, Nyugat-Japán, Korea Central, Korea South
Ausztrália    | Ausztrália Kelet, Ausztrália Délkelet, Ausztrália Központi, Ausztrália Közép 2
Azure Government    | US GOV Virginia, US GOV Iowa, US GOV Arizona, US GOV Texas, US DOD East, US DOD Central
Németország    | Németország Közép, Németország északkelet
Kína | Kína Kelet, Kína Észak,Kína Észak2, Kína Kelet2
Korlátozott régiók az országon ként beálló vészhelyreállításhoz |Németország Észak fenntartva Németország nyugat-közép, Svájc Nyugat fenntartva Svájc Észak, Franciaország Dél fenntartva Franciaország Központi, Egyesült Arab Emírségek központi korlátozott Egyesült Arab Emírségek északi ügyfelek

>[!NOTE]
>
> - **Brazília déli ,** replikálhatja, és feladatátvételi át ezeket a régiókat: Dél-Közép-USA, USA nyugati régiója, USA keleti régiója 2, USA nyugati régiója, USA nyugati régiója 2 és USA északi középső régiója.
> - Brazília déli csak forrásrégióként használható, ahonnan a virtuális gépek replikálhatók a Site Recovery használatával. Nem működhet célterületként. Ennek oka a földrajzi távolságok miatti késési problémák. Vegye figyelembe, hogy ha a feladatátvételi brazíliai déli forrásrégióból egy cél, feladat-visszavétel a célrégiótól délre.
> - Olyan régiókon belül dolgozhat, amelyekhez megfelelő hozzáféréssel rendelkezik.
> - Ha a régió, amelyben létre szeretne hozni egy tárolót, nem jelenik meg, győződjön meg arról, hogy az előfizetés rendelkezik-e az adott régióban erőforrások létrehozásához való hozzáféréssel.
> - Ha nem látja a földrajzi fürtön belüli régiót, amikor engedélyezi a replikációt, győződjön meg arról, hogy az előfizetés rendelkezik az adott régióban virtuális gépek létrehozásához szükséges engedélyekkel.



## <a name="cache-storage"></a>Gyorsítótár

Ez a táblázat összefoglalja a Site Recovery által a replikáció során használt gyorsítótár-tárfiók támogatását.

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Általános célú V2 tárfiókok (gyakori és ritka elérésű szint) | Támogatott | A GPv2 használata nem ajánlott, mert a V2 tranzakciós költségei lényegesen magasabbak, mint a V1 tárfiókok.
Prémium szintű Storage | Nem támogatott | A szabványos tárfiókok gyorsítótár-tároláshoz használatosak a költségek optimalizálása érdekében.
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha tűzfallal engedélyezett gyorsítótár-tárfiókot vagy céltárfiókot használ, győződjön meg arról, hogy ["Megbízható Microsoft-szolgáltatások engedélyezése"](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).<br></br>Győződjön meg arról is, hogy engedélyezi a hozzáférést a vnet legalább egy alhálózatához.


## <a name="replicated-machine-operating-systems"></a>Replikált gépi operációs rendszerek

A Site Recovery támogatja az ebben a szakaszban felsorolt operációs rendszereket futtató Azure virtuális gépek replikációját. Kérjük, vegye figyelembe, hogy ha egy már replikáló gépet a későbbiekben frissítenek (vagy visszaminősítenek) egy másik fő rendszermagra, le kell tiltania a replikációt, és újra engedélyeznie kell a replikációt a frissítés után.

### <a name="windows"></a>Windows


**Operációs rendszer** | **Részletek**
--- | ---
Windows Server 2019 | Server Core, Server with Desktop Experience esetén támogatott.
Windows Server 2016  | Támogatott Server Core, Kiszolgáló asztali élménnyel.
Windows Server 2012 R2 | Támogatott.
Windows Server 2012 | Támogatott.
Windows Server 2008 R2 SP1/SP2 szervizcsomag | Támogatott.<br/><br/> Az Azure virtuális gépekhez készült Mobility szolgáltatásbővítmény [9.30-as](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) verziójából [windowsos karbantartási veremfrissítést (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítést](https://support.microsoft.com/help/4474419) kell telepítenie a Windows Server 2008 R2 SP1/SP2 rendszert futtató gépekre.  Az SHA-1 nem támogatott 2019 szeptemberétől, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon telepíthető/frissít. További információ az [SHA-2 frissítésről és követelményekről.](https://aka.ms/SHA-2KB)
Windows 10 (x64) | Támogatott.
Windows 8.1 (x64) | Támogatott.
Windows 8 (x64) | Támogatott.
Windows 7 (x64) SP1 szervizcsomaggal | Az Azure virtuális gépekhez készült Mobility szolgáltatásbővítmény [9.30-as](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) verziójából telepítenie kell egy Windows [karbantartási veremfrissítést (SSU)](https://support.microsoft.com/help/4490628) és [SHA-2 frissítést](https://support.microsoft.com/help/4474419) a Windows 7 SP1 rendszert futtató gépeken.  Az SHA-1 nem támogatott 2019 szeptemberétől, és ha az SHA-2 kód aláírása nincs engedélyezve, az ügynökbővítmény nem a várt módon telepíthető/frissít.. További információ az [SHA-2 frissítésről és követelményekről.](https://aka.ms/SHA-2KB)



#### <a name="linux"></a>Linux

**Operációs rendszer** | **Részletek**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6,[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8.0](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery), 8.1
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 8.0, 8.1
Ubuntu 14.04 LTS kiszolgáló | [Támogatott kernelverziók](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS szerver | [Támogatott kernelverzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu szerverek jelszóalapú hitelesítést és bejelentkezést, valamint a felhő-init csomag konfigurálása felhőbeli virtuális gépek, előfordulhat, hogy a jelszó-alapú bejelentkezés le van tiltva a feladatátvétel (attól függően, hogy a cloudinit konfiguráció). A jelszóalapú bejelentkezés újra engedélyezhető a virtuális gépen a támogatás > hibaelhárítás > beállítások menüjében található jelszó alaphelyzetbe állításával (az Azure Portalon lévő virtuális gép feladatátvételi feladatátvételi rendszeréből.
Ubuntu 18.04 LTS szerver | [Támogatott kernelverzió](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Debian, 7. | [Támogatott kernelverziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Támogatott kernelverziók](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Támogatott kernel verziók)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 15 | 15 és 15 SP1. [(Támogatott kernel verziók)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3 szervizcsomag<br/><br/> A replikálógépek sp3-ról SP4-re történő frissítése nem támogatott. Ha egy replikált gépet frissítettek, le kell tiltania a replikációt, és újra engedélyeznie kell a replikációt a frissítés után.
SUSE Linux Enterprise Server 11 | SP4 szervizcsomag
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, [7.6, 7.7.7](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) <br/><br/> A Red Hat-kompatibilis kernel vagy a Törhetetlen Vállalati Kernel Release 3, 4 & 5 (UEK3, UEK4, UEK5) futtatása


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Támogatott Ubuntu kernelverziók Az Azure virtuális gépekhez

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
14.04 LTS | 9.32| 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
14.04 LTS | 9.31 | 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
14.04 LTS | 9.30 | 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
14.04 LTS | 9.29 | 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azúrkék 4.15.0-1045-azúr |
|||
16.04 LTS | 9.32 | 4.4.0-21-generic a 4.4.0-171-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-74-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1066-azúr|
16.04 LTS | 9.31 | 4.4.0-21-generic a 4.4.0-170-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-72-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1063-azúr|
16.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-generic a 4.4.0-166-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-66-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1061-azúr|
16.04 LTS | 9.29 | 4.4.0-21-generic a 4.4.0-164-generic,<br/>4.8.0-34-generic 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic a 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-64-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure-tól 4.13.0-1018-azúr <br/>4.15.0-1012-azure-tól 4.15.0-1059-azúr|
|||
18.04 LTS | 9.32| 4.15.0-20-generic a 4.15.0-74-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-37-generic </br> 5.3.0-19-generic-5.3.0-24-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1028-azúr </br> 5.3.0-1007-azure-5.3.0-1009-azúr|
18.04 LTS | 9.31| 4.15.0-20-generic a 4.15.0-72-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-37-generic </br> 5.3.0-19-generic-5.3.0-24-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1025-azúr </br> 5.3.0-1007-azúr|
18.04 LTS | [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-generic a 4.15.0-66-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-32-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1023-azúr|
18.04 LTS | [9.29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-generic a 4.15.0-64-generic </br> 4.18.0-13-generic a 4.18.0-25-generic </br> 5.0.0-15-generic-to 5.0.0-29-generic </br> 4.15.0-1009-azúrkék 4.15.0-1037-azúr </br> 4.18.0-1006-azúrkék 4.18.0-1025-azúr </br> 5.0.0-1012-azure-5.0.0-1020-azúr|


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Támogatott Debian kernel verziók Az Azure virtuális gépekhez

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
Debian, 7. | 9.28,9.29,9.30,9.31 | 3.2.0-4-amd64 -hoz 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.29,9.30,9.31 | 3.16.0-4-amd64 -hoz 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 -hoz 4.9.0-0.bpo.11-amd64, 4.9.0-0.bpo.4-amd64 |
Debian 8 | 9.28 | 3.16.0-4-amd64 -hoz 3.16.0-10-amd64, 4.9.0-0.bpo.4-amd64 -hoz 4.9.0-0.bpo.9-amd64, 4.9.0-0.bpo.4-amd64 to 4.9.0-0.bpo.9-amd64, 4.9.0-0.bpo.9-amd64, 4.9.0-0.bpo. |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Támogatott SUSE Linux Enterprise Server 12 kernelverziók Az Azure virtuális gépekhez

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.32 | Minden [készlet SUSE 12 SP1, SP2, SP3,SP4 kernelek támogatottak.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure -hoz 4.4.180-4.31-azúr,</br>4.12.14-6.3-azure-tól 4.12.14-6.34-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.31 | Minden [készlet SUSE 12 SP1, SP2, SP3,SP4 kernelek támogatottak.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure -hoz 4.4.180-4.31-azúr,</br>4.12.14-6.3-azure-tól 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.30 | Minden [készlet SUSE 12 SP1, SP2, SP3,SP4 kernelek támogatottak.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure -hoz 4.4.180-4.31-azúr,</br>4.12.14-6.3-azure-tól 4.12.14-6.29-azure  |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.29 | Minden [készlet SUSE 12 SP1, SP2, SP3,SP4 kernelek támogatottak.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_12)</br></br> 4.4.138-4.7-azure -hoz 4.4.180-4.31-azúr,</br>4.12.14-6.3-azure-tól 4.12.14-6.23-azure  |

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Támogatott SUSE Linux Enterprise Server 15 kernelverziók Az Azure virtuális gépekhez

**Kiadás** | **Mobilitási szolgáltatás verziója** | **Kernel verziója** |
--- | --- | --- |
SUSE Linux Enterprise Server 15 és 15 SP1 | 9.32 | Minden [készlet SUSE 15 és 15 kernelek támogatottak.](https://wiki.microfocus.com/index.php/SUSE/SLES/Kernel_versions#SUSE_Linux_Enterprise_Server_15)</br></br> 4.12.14-5.5-azure-tól 4.12.14-8.22-azure |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikált gépek - Linux fájlrendszer/vendégtároló

* Fájlrendszerek: ext3, ext4, XFS, BTRFS
* Mennyiségi vezető: LVM2
* Többutas szoftver: Eszközleképező


## <a name="replicated-machines---compute-settings"></a>Replikált gépek – számítási beállítások

**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Méret | Bármilyen Azure virtuális gép mérete legalább 2 PROCESSZORmaggal és 1 GB RAM-mal | Ellenőrizze [az Azure virtuális gépméretét.](../virtual-machines/windows/sizes.md)
Rendelkezésre állási csoportok | Támogatott | Ha engedélyezi a replikációt egy Azure-beli virtuális gép az alapértelmezett beállításokkal, a forrásrégió beállításai alapján automatikusan létrejön egy rendelkezésre állási készlet. Ezeket a beállításokat módosíthatja.
Rendelkezésre állási zónák | Támogatott |
Hibrid használat -előny (HUB) | Támogatott | Ha a forrás virtuális gép rendelkezik egy HUB-licenc engedélyezve van, a teszt feladatátvétel vagy a virtuális gép feladatátvételis használja a HUB-licencet.
Virtuálisgép-méretezési csoportok | Nem támogatott |
Azure-galéria képei - A Microsoft közzétette | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Azure Gallery-képek – Harmadik fél által közzétett | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
Egyéni képek - Harmadik fél közzétett | Támogatott | Támogatott, ha a virtuális gép támogatott operációs rendszeren fut.
A Site Recovery használatával áttelepített virtuális gépek | Támogatott | Ha egy VMware virtuális gép vagy fizikai gép átlett helyezve az Azure-ba a Site Recovery használatával, el kell távolítania a gépen futó Mobility szolgáltatás régebbi verzióját, és újra kell indítania a gépet, mielőtt replikálna egy másik Azure-régióba.
RBAC-házirendek | Nem támogatott | Szerepköralapú hozzáférés-vezérlési (RBAC) házirendek virtuális gépeken nem replikálódik a feladatátvevő virtuális gép a célrégióban.
Bővítmények | Nem támogatott | A bővítmények nem replikálódnak a feladatátvevő virtuális gépre a célrégióban. A feladatátvétel után manuálisan kell telepíteni.

## <a name="replicated-machines---disk-actions"></a>Replikált gépek – lemezműveletek

**Művelet** | **Részletek**
-- | ---
Lemez átméretezése replikált virtuális gépen | A forrás virtuális gép feladatátvétel előtt támogatott. Nem kell letiltani/újra engedélyezni a replikációt.<br/><br/> Ha módosítja a forrás virtuális gép feladatátvétel után, a módosítások nem rögzítik.<br/><br/> Ha a feladatátvétel után módosítja a lemez méretét az Azure virtuális gépen, a módosításokat a Site Recovery nem rögzíti, és a feladat-visszavétel az eredeti virtuális gép méretére lesz.
Lemez hozzáadása replikált virtuális géphez | Támogatott

## <a name="replicated-machines---storage"></a>Replikált gépek - tárolás

Ez a táblázat összegezte az Azure VM operációs rendszer lemezének, adatlemezének és ideiglenes lemezének támogatását.

- Fontos, hogy a virtuális gép lemez korlátait és céljait a [Linux](../virtual-machines/linux/disk-scalability-targets.md) és [a Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépek teljesítményproblémák elkerülése érdekében.
- Ha az alapértelmezett beállításokkal telepíti a telepítést, a Site Recovery automatikusan lemezeket és tárfiókokat hoz létre a forrásbeállítások alapján.
- Ha testre szab, győződjön meg róla, hogy betartja az irányelveket.

**Összetevő** | **Támogatás** | **Részletek**
--- | --- | ---
Az operációs rendszer lemezének maximális mérete | 2048 GB | [További információ](../virtual-machines/windows/managed-disks-overview.md) a virtuális gép lemezeiről.
Ideiglenes lemez | Nem támogatott | Az ideiglenes lemez mindig ki van zárva a replikációból.<br/><br/> Ne tároljon állandó adatokat az ideiglenes lemezen. [További információ](../virtual-machines/windows/managed-disks-overview.md).
Adatlemez maximális mérete | 8192 GB felügyelt lemezek esetén<br></br>4095 GB nem felügyelt lemezek esetén|
Adatlemez minimális mérete | Nincs korlátozás a nem felügyelt lemezekre. 2 GB felügyelt lemezek esetén |
Adatlemez maximális száma | Akár 64, egy adott Azure virtuális gép méretének támogatásával összhangban | [További információ](../virtual-machines/windows/sizes.md) a virtuális gép méretekről.
Adatlemez-váltási sebesség | Lemezenként legfeljebb 10 Mb/s a prémium szintű tárolás érdekében. A normál tárolás esetén lemezenként legfeljebb 2 Mb/s lehet. | Ha a lemezen az átlagos adatváltozási sebesség folyamatosan magasabb, mint a maximális érték, a replikáció nem fog felzárkózni.<br/><br/>  Ha azonban a maximális értéket szórványosan túllépi, a replikáció felzárkózhat, de előfordulhat, hogy kissé késleltetett helyreállítási pontok jelennek meg.
Adatlemez - normál tárfiók | Támogatott |
Adatlemez – prémium szintű tárfiók | Támogatott | Ha egy virtuális gép lemezek között elosztva prémium szintű és normál szintű tárfiókok, kiválaszthatja a különböző céltárfiók minden lemez, annak érdekében, hogy a célrégióban azonos tárolási konfigurációval rendelkezik.
Felügyelt lemez - szabványos | Az Azure-régiókban, ahol az Azure Site Recovery támogatott támogatott. |
Felügyelt lemez - prémium | Az Azure-régiókban, ahol az Azure Site Recovery támogatott támogatott. |
Standard SSD | Támogatott |
Redundancia | Az LRS és a GRS támogatott.<br/><br/> A ZRS nem támogatott.
Hűvös és meleg tárolás | Nem támogatott | A virtuálisgép-lemezek nem támogatottak a hűvös és a gyors tárolás esetén
Tárolóhelyek | Támogatott |
Titkosítás inaktív helyen (SSE) | Támogatott | SSE az alapértelmezett beállítás a tárfiókok.
Titkosítás inaktív helyen (CMK) | Támogatott | A felügyelt lemezek en mind a szoftveres, mind a HSM-kulcsok támogatottak
Azure lemeztitkosítás (ADE) Windows operációs rendszerhez | Felügyelt lemezekkel rendelkező virtuális gépek számára támogatott. | A nem felügyelt lemezeket használó virtuális gépek nem támogatottak. <br/><br/> A HSM által védett kulcsok nem támogatottak. |
Azure disk encryption (ADE) linuxos operációs rendszerhez | Felügyelt lemezekkel rendelkező virtuális gépek számára támogatott. | A nem felügyelt lemezeket használó virtuális gépek nem támogatottak. <br/><br/> A HSM által védett kulcsok nem támogatottak. |
Gyors hozzáadás    | Támogatott | A replikált Azure virtuális géphez hozzáadott adatlemez replikációjának engedélyezése a felügyelt lemezeket használó virtuális gépek számára támogatott.
Gyors eltávolítás lemez    | Nem támogatott | Ha eltávolítja az adatlemezt a virtuális gépről, le kell tiltania a replikációt, és újra engedélyeznie kell a replikációt a virtuális gép számára.
Lemez kizárása | Támogatás: A [Konfiguráláshoz a PowerShell](azure-to-azure-exclude-disks.md) használatával kell rendelkeznie. |    Az ideiglenes lemezek alapértelmezés szerint ki vannak zárva.
Tárolóhelyek – Közvetlen  | Összeomlási konzisztens helyreállítási pontok esetén támogatott. Az alkalmazáskonzisztens helyreállítási pontok nem támogatottak. |
Kibővített fájlkiszolgáló  | Összeomlási konzisztens helyreállítási pontok esetén támogatott. Az alkalmazáskonzisztens helyreállítási pontok nem támogatottak. |
DRBD | A DRBD-telepítés részét használó lemezek nem támogatottak. |
LRS | Támogatott |
GRS | Támogatott |
RA-GRS | Támogatott |
ZRS | Nem támogatott |
Hűvös és forró tárolás | Nem támogatott | A virtuális gép lemezei nem támogatottak a hűvös és a gyors tárolásesetén
Azure Storage-tűzfalak virtuális hálózatokhoz  | Támogatott | Ha korlátozza a virtuális hálózati hozzáférést a tárfiókokhoz, engedélyezze [a Megbízható Microsoft-szolgáltatások engedélyezése](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)szolgáltatást.
Általános célú V2-s tárfiókok (gyakori és ritka elérésű szint) | Támogatott | A tranzakciós költségek jelentősen növekednek az általános célú V1-es tárfiókokhoz képest
2. generáció (UEFI rendszerindítás) | Támogatott

>[!IMPORTANT]
> A teljesítménnyel kapcsolatos problémák elkerülése érdekében győződjön meg arról, hogy kövesse a virtuális gép lemezméretezhetőségét és teljesítménycéljait [Linux](../virtual-machines/linux/disk-scalability-targets.md) vagy [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuális gépekhez. Ha az alapértelmezett beállításokat használja, a Site Recovery a forráskonfiguráció alapján hozza létre a szükséges lemezeket és tárfiókokat. Ha testreszabja és kiválasztja a saját beállításait, kövesse a lemez méretezhetőségét és teljesítménycéljait a forrás virtuális gépekhez.

## <a name="limits-and-data-change-rates"></a>Korlátok és adatváltozási arányok

Az alábbi táblázat a Site Recovery korlátait foglalja össze.

- Ezek a korlátok a tesztjeinken alapulnak, de nyilvánvalóan nem fedik le az összes lehetséges alkalmazásI/O kombinációt.
- A tényleges eredmények az alkalmazás I/O-mixtől függően változhatnak.
- Két korlátot kell figyelembe venni, lemezenként adatváltozás és a virtuális gép adatváltozása.

**Tárolási cél** | **Átlagos forráslemez I/O** |**Forráslemez átlagos adatváltozása** | **Forráslemez teljes napi adatváltozása**
---|---|---|---
Standard szintű Storage | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 8 KB    | 2 MB/s | Lemezenként 168 GB
Prémium szintű P10 vagy P15 lemez | 16 KB | 4 MB/s |    Lemezenként 336 GB
Prémium szintű P10 vagy P15 lemez | 32 KB vagy több | 8 MB/s | Lemezenként 672 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 8 KB    | 5 MB/s | Lemezenként 421 GB
Prémium szintű P20, P30, P40 vagy P50 lemez | 16 KB vagy több |20 MB/s | 1684 GB lemezenként

## <a name="replicated-machines---networking"></a>Replikált gépek - hálózatépítés
**Beállítás** | **Támogatás** | **Részletek**
--- | --- | ---
Hálózati adapter | Egy adott Azure virtuális gép méretéhez támogatott maximális szám | Hálózati adapterek jönnek létre, amikor a virtuális gép jön létre a feladatátvétel során.<br/><br/> A feladatátvevő virtuális gép hálózati adaptereinek száma attól függ, hogy a hálózati adapterek száma a forrás virtuális gép, amikor a replikáció engedélyezve volt. Ha a replikáció engedélyezése után hozzáad vagy eltávolít egy hálózati adaptert, az nem befolyásolja a replikált virtuális gép hálózati adaptereinek számát a feladatátvétel után. Azt is vegye figyelembe, hogy a hálózati adapterek sorrendje a feladatátvétel után nem garantált, hogy megegyezik az eredeti sorrendben.
Internetes Load Balancer | Támogatott | Társítsa az előre konfigurált terheléselosztót egy Azure Automation-parancsfájl használatával egy helyreállítási tervben.
Belső terheléselosztó | Támogatott | Társítsa az előre konfigurált terheléselosztót egy Azure Automation-parancsfájl használatával egy helyreállítási tervben.
Nyilvános IP-cím | Támogatott | Társítson egy meglévő nyilvános IP-címet a hálózati adapterhez. Vagy hozzon létre egy nyilvános IP-címet, és társítsa azt a hálózati adapter egy Azure Automation-parancsfájl használatával egy helyreállítási tervben.
NSG a nic-en | Támogatott | Társítsa az NSG-t a hálózati adapterrel egy Azure Automation-parancsfájl használatával egy helyreállítási tervben.
NSG az alhálózaton | Támogatott | Társítsa az NSG-t az alhálózathoz egy Azure Automation-parancsfájl használatával egy helyreállítási tervben.
Fenntartott (statikus) IP-cím | Támogatott | Ha a hálózati adapter a forrás virtuális gép egy statikus IP-címet, és a cél alhálózat rendelkezik ugyanazt az IP-címet elérhető, hozzá van rendelve a feladatátvételi virtuális gép.<br/><br/> Ha a cél alhálózat nem rendelkezik ugyanazt az IP-címet elérhető, az egyik rendelkezésre álló IP-címek az alhálózat a virtuális gép számára van fenntartva.<br/><br/> Rögzített IP-címet és alhálózatot is megadhat a **Replikált elemek** > **beállításai** > **számítási és hálózati** > **hálózati csatolókban.**
Dinamikus IP-cím | Támogatott | Ha a hálózati adapter a forrás dinamikus IP-címzés, a virtuális gép a virtuális gép által felügyelt hálózati adapter is dinamikus alapértelmezés szerint.<br/><br/> Szükség esetén ezt módosította rögzített IP-címre.
Több IP-cím | Nem támogatott | Ha több IP-címmel rendelkező hálózati adaptert tartalmazó virtuális gép feladatátvételt akkor csak a forrásrégióban lévő hálózati adapter elsődleges IP-címe marad meg. Több IP-cím hozzárendeléséhez hozzáadhat virtuális gépeket egy [helyreállítási tervhez,](recovery-plan-overview.md) és csatolhat egy parancsfájlt, amely további IP-címeket rendel a tervhez, vagy manuálisan vagy parancsfájllal is elláthatja a módosítást feladatátvétel után.
Traffic Manager     | Támogatott | A Traffic Manager előre konfigurálhatja, hogy a forgalom a forrásrégió végpontjára rendszeresen, és a célrégió végpontjára irányítva feladatátvétel esetén.
Azure DNS | Támogatott |
Egyéni DNS    | Támogatott |
Nem hitelesített proxy | Támogatott | [További információ](site-recovery-azure-to-azure-networking-guidance.md)
Hitelesített proxy | Nem támogatott | Ha a virtuális gép hitelesített proxyt használ a kimenő kapcsolathoz, nem replikálható az Azure Site Recovery használatával.
VPN-helyek közötti kapcsolat a helyszíni<br/><br/>(ExpressRoute-tal vagy anélkül)| Támogatott | Győződjön meg arról, hogy az UdRs és nsg-ek úgy vannak konfigurálva, hogy a site recovery forgalom nem irányítható a helyszíni. [További információ](site-recovery-azure-to-azure-networking-guidance.md)
VNET-net kapcsolat    | Támogatott | [További információ](site-recovery-azure-to-azure-networking-guidance.md)
Virtuális hálózati szolgáltatásvégpontok | Támogatott | Ha korlátozza a virtuális hálózati hozzáférést a tárfiókokhoz, győződjön meg arról, hogy a megbízható Microsoft-szolgáltatások hozzáférhetnek a tárfiókhoz.
Gyorsított hálózatkezelés | Támogatott | A gyorsított hálózatkezelést engedélyezni kell a forrásvirtuális gépen. [További információ](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>További lépések
- Olvassa el az Azure virtuális gépek replikálásával [kapcsolatos hálózati útmutatót.](site-recovery-azure-to-azure-networking-guidance.md)
- Üzembe helyezése vész-helyreállítási [replikálásával Azure virtuális gépek.](site-recovery-azure-to-azure.md)
