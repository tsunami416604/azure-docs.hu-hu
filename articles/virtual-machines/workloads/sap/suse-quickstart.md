---
title: A Microsoft Azure SUSE Linux rendszerű virtuális gépek SAP NetWeaver tesztelése |} A Microsoft Docs
description: Az SAP NetWeaver tesztelése Microsoft Azure-beli SUSE Linux-alapú virtuális gépeken
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 032ab2a221f64d01af25056a4eff3ee3384de0c3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157224"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Az SAP NetWeaver futtatása Microsoft Azure-beli SUSE Linux-alapú virtuális gépeken
Ez a cikk ismerteti a különböző dolgot figyelembe kell venni a Microsoft Azure SUSE Linux rendszerű virtuális gépek (VM) az SAP NetWeaver futtatja. 2016. május 19 SAP NetWeaver SUSE Linux rendszerű virtuális gépek az Azure-ban hivatalosan támogatott. Linux-verziók, SAP-kernel-verzióknál és egyéb előfeltételeket minden részletei találhatók SAP Megjegyzés 1928533 "Azure-beli SAP-alkalmazások: Támogatott termékek és Azure-beli Virtuálisgép-típusok ".
SAP használata Linux rendszerű virtuális gépek kapcsolatos további dokumentáció itt található: [SAP használata Linux rendszerű virtuális gépek (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A következő információk segítségére néhány nehézségek elkerülése érdekében.

## <a name="suse-images-on-azure-for-running-sap"></a>Az SAP futtatása az Azure SUSE rendszerképek
SAP NetWeaver futtatását az Azure-ban, használja a SUSE Linux Enterprise Server SLES 12 (SPx) vagy az SLES for SAP – a Lásd még az SAP-jegyzetnek 1928533. A SUSE rendszerképet az Azure Marketplace-en ("SLES 11 SP3 SAP CAL"), de a lemezkép nem célja általános használatra. Ne használja ezt a képet, mert számára van fenntartva a [SAP Cloud Appliance Library](https://cal.sap.com/) megoldás.  

Használja az Azure Resource Manager deployment keretrendszer összes az Azure-ban kell. Keressen a SUSE SLES képek és verziók az Azure PowerShell vagy az Azure parancssori felület (CLI) használatával, használja az alábbi parancsokat. Ezután használhatja a kimenet például határozhat meg az operációsrendszer-képet egy JSON-sablon üzembe helyezéséhez egy új SUSE Linux rendszerű virtuális Gépet.
A PowerShell-parancsok, és újabb verzióira érvényesek az Azure PowerShell-verzió 1.0.1.

Bár továbbra is a standard SLES-rendszerképek használatához az SAP-telepítések, azt javasoljuk, hogy az új SLES SAP lemezképek használják. Ezek a lemezképek, most már elérhetők az Azure lemezkép-katalógusában. További információ a lemezképek találhatók a megfelelő [Azure Piactéri weblapja]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) vagy a [kapcsolatos SLES for SAP SUSE – gyakori kérdések weblapot]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Keresse meg a létező gyártók, beleértve a SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Keresse meg a meglévő ajánlatok SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Keresse meg a SUSE SLES-ajánlatok:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Keresse meg a SLES Termékváltozat egy adott verzióját:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>WALinuxAgent SUSE virtuális gépek telepítése
Az ügynök WALinuxAgent nevű az Azure piactéren elérhető SLES képek részét képezi. Azt manuálisan (például a SLES-OS virtuális merevlemez (VHD) a helyszíni feltöltésekor) telepítésével kapcsolatos információkért lásd:

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "enhanced monitoring"
SAP "enhanced monitoring" kötelező futtatásának előfeltétele, hogy az SAP az Azure-ban. Ellenőrizze a részleteket az SAP-ben, vegye figyelembe 2191498 "az SAP az Azure Linux rendszeren: A kibővített figyelési".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Azure-adatlemezek csatolása az Azure Linux VM
Soha nem csatlakoztatási Azure adatlemezeket az Azure Linux VM segítségével az eszköz azonosítójával. Ehelyett használja a univerzálisan egyedi azonosítót (UUID). Legyen óvatos az adatlemezek csatlakoztatása az Azure, a grafikus eszközök például használatakor. A bejegyzések a /etc/fstab ellenőrizni.

Az eszköz azonosítójával a probléma, hogy változhat, de majd lefagy a az Azure virtuális gép lehet, hogy a rendszerindítási folyamat során. A probléma megoldásához hozzáadhat a /etc/fstab nofail paraméter. Azonban ügyeljen az nofail mert alkalmazások előfordulhat, hogy a csatlakoztatási pont, mielőtt használja, és előfordulhat, hogy írja, a legfelső szintű fájlrendszer, abban az esetben, ha egy külső Azure-adatlemez nem lett csatlakoztatva van, a rendszerindítás során.

Csatlakoztatási UUID-n keresztül az egyetlen kivétel van csatlakoztatása hibaelhárítás céljából, operációsrendszer-lemezt, az az alábbi szakaszban leírtak szerint.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>SUSE virtuális gépek, amelyek nem érhető el többé hibaelhárítása
Előfordulhat, hogy olyan helyzetekben, ahol az Azure SUSE virtuális gépek lefagy a rendszerindítási folyamat (például a lemezek csatlakoztatásával kapcsolatos hiba). A probléma az Azure Portalon az Azure Virtual Machines v2-ben a rendszerindítási diagnosztikai szolgáltatás használatával ellenőrizheti. További információkért lásd: [rendszerindítási diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Egyik módja a probléma megoldásához, hogy az operációsrendszer-lemez csatolása a sérült virtuális gépről egy másik SUSE virtuális géphez az Azure-ban. Majd módosításokat megfelelő /etc/fstab szerkesztése vagy eltávolítása a hálózati udev szabályokat, például a következő szakaszban leírtak szerint.

Nincs egyik lényeges tudnivaló, érdemes figyelembe venni. Az operációsrendszer-lemez ugyanazon UUID mindig forráskönyvtárának több SUSE virtuális gépek, az ugyanazon az Azure Marketplace-lemezképből (például az SLES 11 SP4) üzembe helyezéséhez okoz. Ezért UUID segítségével egy operációsrendszer-lemez csatolása a virtuális gépről egy másik, amely az Azure Marketplace-en azonos képtalálatok két azonos az UUID azonosítók használatával lett telepítve. Két, azonos az UUID azonosítók ok a virtuális gép hibaelhárításhoz használt, a csatlakoztatott és sérült operációsrendszer-lemez helyett az eredeti operációsrendszer-lemez rendszerindítást.

A problémák elkerülése érdekében két módja van:

* Egy másik Azure Marketplace-rendszerkép használata a a hibaelhárító virtuális Géphez (például SLES 11 SPx SLES 12 helyett).
* Ne csatlakoztassa egy másik virtuális gépről a sérült operációsrendszer-lemezt UUID – használja az egyéb.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>SUSE virtuális gépek a helyszínről az Azure-bA feltöltése
A SUSE virtuális gépek a helyszínről az Azure-bA feltöltendő lépések leírását lásd: [SLES- vagy opensuse-alapú virtuális gép előkészítése Azure-](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ha szeretne feltölteni egy virtuális Gépet anélkül, hogy a megszüntetési lépés végén (például, hogy tartsa egy meglévő SAP-telepítést, valamint a gazdagép neve), ellenőrizze a következőket:

* Győződjön meg arról, hogy az operációsrendszer-lemez csatlakoztatva van-e az UUID azonosító, és nem az eszköz azonosítójával. Csak a /etc/fstab UUID módosítása nem elegendő a rendszerlemez számára. Emellett ne felejtse el a rendszertöltő YaST vagy /boot/grub/menu.lst szerkesztésével igazíthatja.
* Ha a SUSE operációsrendszer-lemez a VHDX formátumot használja, és alakíthatja át a VHD feltöltése az Azure-ba, akkor valószínű, hogy a hálózati eszköz a változik eth0 eth1. A problémák elkerülése érdekében, hogy rendszer betöltése során az Azure-ban később állítsa vissza eth0 leírtak szerint [kijavítása eth0 a klónozott SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Amellett, hogy mi az cikkben ismertetett azt javasoljuk, hogy a fájl eltávolítása:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Az Azure Linux-ügynök (waagent) elkerülése érdekében potenciális problémákat, mindaddig, amíg nem áll több hálózati adapterrel is telepítheti.

## <a name="deploying-a-suse-vm-on-azure"></a>Azure-beli SUSE virtuális gépek üzembe helyezése
Új SUSE virtuális gépek az új Azure Resource Manager-modellben a JSON-sablon fájlok használatával kell létrehoznia. A sablon JSON-fájl létrehozása után telepítheti a virtuális gép PowerShell helyett a következő CLI-parancs használatával:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
A sablon JSON-fájlok kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../../../resource-group-authoring-templates.md) és [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/).

Az Azure klasszikus parancssori felület és Azure Resource Managerrel kapcsolatos további információkért lásd: [használata az Azure klasszikus parancssori felület Mac, Linux és Windows az Azure Resource Manager](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Az SAP-licenc- és kulcs
A hivatalos SAP Azure-beli hitelesítésre egy új mechanizmust alapján számítja ki az SAP az SAP-licenccel használt hardverkulcsra váltásával jelent meg. Győződjön meg arról, hogy módosítani kell a korábban az SAP-kernel, az új algoritmust használja. Linux-SAP kernel korábbi verziók nem tartalmazta a kód megváltoztatására. Ezért, bizonyos esetekben (például az Azure virtuális gép átméretezése), az SAP hardverkulcsra váltásával megváltozott, és az SAP-licenc volt már nem érvényes. A megoldás az újabb SAP Linux-kernelek vannak van megadva.  A részletes SAP kernel javítások SAP-jegyzetnek 1928533 vannak dokumentálva.

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf csomag / lehetőségeire adm
SUSE "sapconf", amely kezeli az SAP-specifikus beállítások készletét nevű csomagot biztosít. Ez a csomag leírása, és hogyan telepítheti, és kapcsolatos további információkért lásd:  [Készítse elő a SUSE Linux Enterprise Server, SAP-rendszerek futtatása sapconf segítségével](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) és [sapconf mi vagy a SUSE Linux Enterprise Server előkészítése az SAP-rendszerek?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Addig is van egy új eszközt, amely "sapconf - lehetőségeire adm" váltja fel. Ez az eszköz két hivatkozásokon további információ található egyik:

- SLES dokumentációjában az sap-hana "lehetőségeire-adm" profilban találhatók [Itt](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_saptune.html) 

- Az SAP-feladatokat a "lehetőségeire-adm" - finomhangolási rendszerek található [Itt](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) fejezet 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>Az NFS-megosztások elosztott SAP-telepítés esetén
Ha rendelkezik egy elosztott telepítés – például, amelyre telepíteni az adatbázis és az SAP-alkalmazáskiszolgálók a különálló virtuális gépek – megoszthatja a /sapmnt könyvtár keresztül hálózati fájlrendszer (NFS). Ha problémába ütközik a telepítési lépéseket /sapmnt az NFS-megosztás létrehozása után, ellenőrizze, hogy ha a megosztás "no_root_squash" van beállítva.

## <a name="logical-volumes"></a>Logikai kötetek
Múltbeli időpont Ha több Azure-beli adat-lemezre (például az SAP-adatbázist), a logikai nagy egy szükséges volt javasolt Raid felügyeleti eszköz MDADM használata, mivel Linux logikai kötet kezelő (LVM) nem lett teljes körűen érvényesítve még az Azure-ban. Hogyan állítható be az Azure-on Linux RAID mdadm használatával kapcsolatban lásd: [szoftveres RAID linuxon konfigurálása](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Addig is tól 2016. május elejére Linux a Logical Volume Manager teljes körűen támogatja az Azure-ban, és MDADM alternatívájaként használható. Az Azure-ban LVM kapcsolatos további információkért olvassa el:  
[LVM konfigurálása az Azure-beli Linuxos virtuális gépre](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Az Azure SUSE-adattára
Ha rendelkezik hozzáféréssel rendelkező probléma a standard szintű Azure SUSE-tárházba, a parancs segítségével állítsa alaphelyzetbe. Az ilyen problémák akkor fordulhat elő, ha egy privát operációsrendszer-lemezkép létrehozása egy Azure-régióban, és másolja a lemezképet más Azure-régióban a saját operációsrendszer-lemezkép alapuló új virtuális gépek telepítéséhez. Futtassa a következő parancsot a virtuális Gépen belül:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome asztali
Ha egyetlen virtuális gép – többek között az SAP grafikus felhasználói felületen belül teljes SAP bemutató rendszer telepítéséhez a Gnome desktop használni kívánt böngészőt, és az SAP-felügyeleti konzol--& gt használatával Ez mutató telepíteni kell az Azure SLES lemezképek:

   Az SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   A SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Az Oracle Linux, a felhőben az SAP támogatása
Nincs a támogatási korlátozás az Oracle Linux rendszeren virtualizált környezetekben. Bár ez a támogatás korlátozás nem egy Azure-ra vonatkozó témakör, fontos megérteni. SAP SUSE vagy a Red Hat, az Azure például nyilvános felhő nem támogatja a Oracle. Addig is fut, az Azure-ban Oracle DB teljes mértékben támogatott az SAP, Oracle Linux rendszeren (lásd az SAP Megjegyzés 1928533). Ha egyéb kombinációi szükség, lépjen kapcsolatba az Oracle közvetlenül.

