---
title: Azure-beli VIRTUÁLIS GÉPEK HA SAP NW-hoz Windows rendszeren, Azure NetApp-fájlokkal (SMB)| Microsoft dokumentumok
description: Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken Windows rendszeren, SAP-alkalmazásokhoz készült Azure NetApp-fájlokkal (SMB)
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591353"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken Windows rendszeren, SAP-alkalmazásokhoz készült Azure NetApp-fájlok (SMB)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Ez a cikk bemutatja, hogyan telepítheti, konfigurálhatja a virtuális gépeket, telepítheti a fürtkeretrendszert, és hogyan telepíthet egy magas rendelkezésre állású SAP NetWeaver 7.50 rendszert Windows virtuális gépekre az [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) [SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) használatával.  

Ebben a cikkben az adatbázisréteg nem szerepel részletesen. Feltételezzük, hogy az Azure [virtuális hálózat](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) már létrejött.  

Először olvassa el a következő SAP-megjegyzéseket és dokumentumokat:

* [Az Azure NetApp Files dokumentációja][anf-azure-doc] 
* Sap Megjegyzés [1928533][1928533], amely a következőket tartalmazza:  
  * Az SAP-szoftverek telepítéséhez támogatott Azure virtuálisgép-méretek listája
  * Fontos kapacitásadatok az Azure virtuális gépek méretéhez
  * Támogatott SAP szoftverek, operációs rendszer (OS) és adatbázis-kombinációk
  * Szükséges SAP kernel verzió a Windows hoz a Microsoft Azure-on
* Az SAP Note [2015553][2015553] felsorolja az SAP által támogatott SAP-szoftvertelepítések előfeltételeit az Azure-ban.
* Az SAP Note [2178632][2178632] részletes információkat tartalmaz az Azure-ban az SAP-hoz jelentett összes figyelési metrikáról.
* Az SAP Note [1999351][1999351] további hibaelhárítási információkat tartalmaz az SAP-hoz kiadott Azure továbbfejlesztett figyelési bővítményhez.
* Az SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) felsorolja az SMB 3.x protokoll SAP által támogatott hitelesítés- és hitelesítés- és hitelesítési szolgáltatásának előfeltételeit.
* Az SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) hibaelhárítási információkat tartalmaz az AL11 SAP-tranzakció windows 2012-es és 2016-os lassú futtatásához.
* Az SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) információkat tartalmaz a Windows Server en lévő, SMB 3.0 protokollal rendelkező fájlmegosztás áttetsző feladatátvételi szolgáltatásáról.
* Az SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) javaslata (a 8.3-as névgenerálás kikapcsolása) a fájlrendszer gyenge teljesítményének/hibáinak kezelésére az adatelérések során.
* [Az SAP NetWeaver magas rendelkezésre állásának telepítése Windows feladatátvételi fürtre és fájlmegosztásra sap ASCS/SCS-példányok számára az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Az Azure Virtual Machines magas rendelkezésre állású architektúrája és forgatókönyvei az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Mintavételi port hozzáadása az ASCS-fürt konfigurációjában](sap-high-availability-installation-wsfc-file-share.md)
* [(A)SCS-példány telepítése feladatátvevő fürtön](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [SMB-kötet létrehozása az Azure NetApp Files számára](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [NetApp SAP-alkalmazások a Microsoft Azure-ban az Azure NetApp-fájlok használatával][anf-sap-applications-azure]

## <a name="overview"></a>Áttekintés

Az SAP új megközelítést és a fürt megosztott lemezei alternatíváját fejlesztette ki egy SAP ASCS/SCS-példány Windows feladatátvételi fürtön történő fürtözéséhez. A fürt megosztott lemezeinek használata helyett smb-fájlmegosztást használhat az SAP globális gazdafájljainak központi telepítéséhez. Az Azure NetApp Files támogatja az SMBv3-at (az NFS-sel együtt) az Active Directory használatával az NTFS ACL-lel. Az Azure NetApp Files automatikusan magas rendelkezésre állású (mivel ez egy PaaS-szolgáltatás). Ezek a funkciók nagyszerű lehetőséget kínálnak az Azure NetApp Files-nak az SAP globális SMB fájlmegosztásának üzemeltetésére.  
Az [Azure Active Directory (AD) tartományi szolgáltatások](https://docs.microsoft.com/azure/active-directory-domain-services/overview) és az Active Directory tartományi szolgáltatások [(AD DS)](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) is támogatottak. Meglévő Active Directory-tartományvezérlőket használhat az Azure NetApp-fájlokkal. A tartományvezérlők lehetnek az Azure-ban virtuális gépként, vagy a helyszínen ExpressRoute vagy S2S VPN keresztül. Ebben a cikkben egy Azure-beli virtuális gép tartományvezérlőjét fogjuk használni.  
Az SAP Netweaver központi szolgáltatások magas rendelkezésre állása (HA) megosztott tárolást igényel. Ennek eléréséhez a Windows, eddig szükséges volt, hogy építsenek vagy SOFS-fürt vagy használja fürt megosztott lemez s / w, mint a SIOS. Most már elérhető az SAP Netweaver HA az Azure NetApp-fájlokban telepített megosztott tárterület használatával. Az Azure NetApp-fájlok használata a megosztott tárolóhoz szükségtelenné teszi a SOFS vagy a SIOS használatát.  

> [!NOTE]
> Az SAP ASCS/SCS-példányok fájlmegosztás használatával történő fürtözése az SAP NetWeaver 7.40 (és újabb), az SAP Kernel 7.49 (és újabb) számára támogatott.  

![SAP ASCS/SCS HA architektúra SMB megosztással](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Az SMB-fájlmegosztás előfeltételei a következők:
* SMB 3.0 (vagy újabb) protokoll.
* Az Active Directory hozzáférés-vezérlési listáinak (ACL-ek) beállítása az Active Directory felhasználói csoportjaihoz és a számítógép$ számítógép-objektumhoz.
* A fájlmegosztásnak HA-kompatibilisnek kell lennie.

Az SAP Central szolgáltatások megosztását ebben a referenciaarchitektúrában az Azure NetApp Files kínálja:

![SAP ASCS/SCS HA architektúra SMB megosztással](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása és csatlakoztatása az Azure NetApp-fájlokhoz

Hajtsa végre a következő lépéseket az Azure NetApp-fájlok használatának előkészítéseként.  

1. Az Azure [NetApp-fájlok regisztrálása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register) lépéseinek végrehajtásához kövesse  
2. Hozzon létre Azure NetApp-fiókot a [NetApp-fiók létrehozása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account) című részben leírt lépések végrehajtásával  
3. Kapacitáskészlet beállítása a [Kapacitáskészlet beállítása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool) című terület utasításainak megfelelően
4. Az Azure NetApp Files-erőforrásoknak delegált alhálózatban kell el, hogy legyenek azok. Kövesse az [alhálózat delegálása az Azure NetApp-fájlok](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) ban a delegált alhálózat létrehozásához kövesse az utasításokat.  

> [!IMPORTANT]
> SMB-kötet létrehozása előtt létre kell hoznia az Active Directory-kapcsolatokat. Tekintse át az [Active Directory-kapcsolatok ra vonatkozó követelményeket.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)  

5. Active Directory-kapcsolat létrehozása az [Active Directory-kapcsolat létrehozása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection) című dokumentumban leírtak szerint  
6. SMB Azure NetApp-fájlok SMB-kötetének létrehozása az [SMB-kötet hozzáadása](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume) című szövegutasításait követve  
7. Csatlakoztassa az SMB-kötetet a Windows virtuális gépen.

> [!TIP]
> Az Azure NetApp-fájlok kötet csatlakoztatására vonatkozó utasításokat, ha az [Azure Portalon](https://portal.azure.com/#home) az Azure NetApp-fájlok objektumra navigál, kattintson a **Kötetek** panelre, majd az **Utasítások csatlakoztatására.**  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Az SAP HA infrastruktúrájának előkészítése Windows feladatátvevő fürt használatával 

1. [A szükséges DNS-IP-címek beállítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Statikus IP-címek beállítása az SAP virtuális gépekhez.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)
3. [Állítson be egy statikus IP-címet az Azure belső terheléselosztóhoz.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e)
4. [Állítsa be az alapértelmezett ASCS/SCS terheléselosztási szabályokat az Azure belső terheléselosztóhoz.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c)
5. [Módosítsa az ASCS/SCS alapértelmezett terheléselosztási szabályait az Azure belső terheléselosztóhoz.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716)
6. [Windows virtuális gépek hozzáadása a tartományhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Rendszerleíró bejegyzések hozzáadása az SAP ASCS/SCS-példány mindkét fürtcsomópontján](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Windows Server feladatátvevő fürt beállítása SAP ASCS/SCS-példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Ha Windows Server 2016-ot használ, javasoljuk, hogy konfigurálja az [Azure Cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)szolgáltatást.


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Sap ASCS-példány telepítése mindkét csomópontra

Az SAP következő szoftverére van szüksége:
   * SAP Szoftverkiépítési Kezelő (SWPM) telepítőeszköz verziója SPS25 vagy újabb.
   * SAP Kernel 7.49 vagy újabb
   * Hozzon létre egy virtuális állomásnevet (fürthálózat nevét) a fürtözött SAP ASCS/SCS-példányhoz, ahogy azt [a Fürtözött SAP ASCS/SCS-példány virtuális állomásnevének létrehozása című](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097)része ismerteti.

> [!NOTE]
> Az SAP ASCS/SCS-példányok fájlmegosztás használatával történő fürtözése az SAP NetWeaver 7.40 (és újabb), az SAP Kernel 7.49 (és újabb) számára támogatott.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>ASCS/SCS-példány telepítése az első ASCS/SCS fürtcsomópontra

1. Telepítsen egy SAP ASCS/SCS-példányt az első fürtcsomópontra. Indítsa el az SAP SWPM telepítőeszközt, majd keresse meg a következő t: **Product** > **DBMS** > Installation > Application Server ABAP (vagy Java) > High-Availability System > ASCS/SCS-példány > első fürtcsomópont.  

2. Válassza a **Fájlmegosztási fürt lehetőséget** az SWPM fürtmegosztási konfigurációjaként.  
3. Amikor a **rendszersap-fürt paramétereinek**lépésében rákérdez, adja meg a **fájlmegosztás is.**  Ebben a példában az SMB megosztási állomásneve **anfsmb-9562**. 

> [!IMPORTANT]
> Ha a szükséges ellenőrző eredménye az SWPM-ben azt mutatja, hogy a folyamatos rendelkezésre állási szolgáltatás állapota nem teljesült, akkor a Késleltetett hibaüzenet utasításait követve lehet [kezelni, amikor olyan megosztott mappához próbál hozzáférni, amely már nem létezik a Windows rendszerben](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

> [!TIP]
> Ha a szükséges ellenőrző eredménye az SWPM-ben a swap-méret állapota nem teljesült, a SWAP-méretet úgy módosíthatja, hogy a Sajátgép>rendszer tulajdonságai>teljesítménybeállítások> speciális> virtuális memória> módosítása elemre navigál.  

4. Konfiguráljon egy SAP-fürterőforrást, a `SAP-SID-IP` mintavételi portot a PowerShell használatával. Hajtsa végre ezt a konfigurációt az SAP ASCS/SCS fürtcsomópontok egyikén, a [Mintavételi port konfigurálása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)című részben leírtak szerint.

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>ASCS/SCS-példány telepítése a második ASCS/SCS fürtcsomópontra

1. Telepítsen egy SAP ASCS/SCS-példányt a második fürtcsomópontra. Indítsa el az SAP SWPM telepítőeszközt, majd keresse meg **a Termék** > **DBMS** > Installation > Application Server ABAP (vagy Java) > a nagy rendelkezésre állású rendszer > ASCS/SCS-példányt > további fürtcsomópontot.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>DBMS-példány és SAP-alkalmazáskiszolgálók telepítése

Végezze el az SAP telepítését a következők telepítésével:

   * DbMS-példány  
   * Elsődleges SAP-alkalmazáskiszolgáló  
   * Egy további SAP-alkalmazáskiszolgáló  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Az SAP ASCS/SCS-példány feladatátvételének tesztelése 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Feladatátvétel az A fürtcsomópontról a B fürtcsomópontra és vissza
Ebben a tesztforgatókönyvben a fürtcsomópont sapascs1-et A csomópontként, a fürtsapascs2-t pedig B csomópontként fogjuk utalni.

1. Ellenőrizze, hogy a fürterőforrások futnak-e az A csomóponton. ![](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Indítsa újra az A fürtcsomópontot. Az SAP-fürt erőforrásai a B ![fürtcsomópontra kerülnek.](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Zárolási bejegyzési vizsgálat

1.Ellenőrizze, hogy az SAP enqueue replikációs kiszolgáló (ERS) aktív-e  
2. Jelentkezzen be az SAP-rendszerbe, hajtsa végre az SU01 tranzakciót, és nyisson meg egy felhasználói azonosítót változásmódban. Ez létrehoz egy SAP lock bejegyzést.  
3. Az SAP-rendszerbe bejelentkezve jelenítse meg a zárolási bejegyzést az ST12 tranzakcióra való navigálással.  
4. Az A fürtcsomópontból a B fürtcsomópontba származó ASCS-erőforrások átvétele.  
5. Ellenőrizze, hogy az SAP ASCS/SCS-fürt erőforrásfeladat-átvétele előtt létrehozott zárolási bejegyzés megmarad-e.  

![3. ábra: A zárolási bejegyzés a feladatátvételi teszt után is megmarad](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

További információ: [Hibaelhárítás az ASCS-ben az ASCS-ben az ERS-szel](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>További lépések

* [Az Azure virtuális gépek tervezése és megvalósítása az SAP-hoz][planning-guide]
* [Az Azure virtuális gépek üzembe helyezése az SAP-hoz][deployment-guide]
* [Az Azure virtual machines DBMS üzembe helyezése az SAP-hoz][dbms-guide]
* Az SAP vészhelyreállításának magas rendelkezésre állásának és tervezésének megismerése 
* HANA az Azure-ban (nagy példányok), lásd: [SAP HANA (nagy példányok) magas rendelkezésre állású és vész-helyreállítási az Azure-ban.](hana-overview-high-availability-disaster-recovery.md)
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és tervezze meg az SAP HANA vészutáni helyreállítását az Azure virtuális gépeken, olvassa el [az SAP HANA magas rendelkezésre állását az Azure virtuális gépeken (VM-ek) című témakört.][sap-hana-ha]
