---
title: "A Microsoft Azure SUSE Linux virtuális gépeken SAP NetWeaver tesztelése |} Microsoft Docs"
description: "Az SAP NetWeaver tesztelése Microsoft Azure-beli SUSE Linux-alapú virtuális gépeken"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: hermannd
ms.openlocfilehash: 072a70c1da74b3b50ad8c0a93ee3c079a724d81f
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Az SAP NetWeaver futtatása Microsoft Azure-beli SUSE Linux-alapú virtuális gépeken
Ez a cikk ismerteti a különböző tényezőt kell figyelembe, hogy a Microsoft Azure SUSE Linux virtuális gépek (VM) SAP NetWeaver futtatja. 2016. május 19-én SAP NetWeaver hivatalosan támogatott SUSE Linux virtuális gépek Azure-on. Linux-verziók, SAP kernel verziók és egyéb előfeltételeket minden részletek megtalálhatók a SAP Megjegyzés 1928533 "SAP-alkalmazások az Azure-on: a támogatott és az Azure Virtuálisgép-típusokon".
A Linux virtuális gépeken SAP kapcsolatos további dokumentáció itt található: [SAP használata a Linux virtuális gépek (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

A következő információk segítségére néhány nehézségek elkerülése érdekében.

## <a name="suse-images-on-azure-for-running-sap"></a>A SAP futó Azure SUSE lemezképek
SAP NetWeaver Azure-on futó, használja a SUSE Linux Enterprise Server SLES 12 (SPx) vagy SLES a SAP – a Lásd még: SAP Megjegyzés 1928533. A speciális SUSE lemezkép az Azure piactéren ("SLES 11 SP3 SAP CAL"), de a lemezkép nem általános használatra készült. Ne használja ezt a lemezképet, mert van fenntartva a [felhő készülék teszteléshez](https://cal.sap.com/) megoldás.  

Az Azure Resource Manager deployment keretrendszer használata az összes Azure-telepítés kell. Amelyet meg kíván keresni SUSE SLES képek és verziók az Azure PowerShell vagy az Azure parancssori felület (CLI) használatával, az alább látható parancsokat használhatja. Használhatja a kimeneti, például olyan operációsrendszer-lemezképet, egy JSON-sablon új SUSE Linux virtuális gép üzembe helyezéséhez.
PowerShell-parancsokkal is érvényes az Azure PowerShell-verzió 1.0.1-es és újabb verziók.

Habár a szokásos SLES képet használandó SAP telepítések továbbra is lehetséges, javasoljuk az új SLES SAP lemezképek használnia. Ezeket a lemezképeket a kép: Azure gyűjteményelem most érhető el. Ezeket a lemezképeket további információ található a megfelelő [Azure piactér lap]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) vagy a [SUSE gyakran ismételt kérdések weblap tudnivalók az SAP SLES]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Meglévő közzétevők, beleértve a SUSE keresése:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Keresse meg a SUSE meglévő ajánlatok:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* SUSE SLES ajánlatok keresése:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Keresse meg a SLES SKU egy adott verziójához:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>A virtuális gép SUSE WALinuxAgent telepítése
Az ügynök WALinuxAgent nevű az SLES lemezképeket az Azure piactéren részét képezi. Manuálisan (például egy SLES operációs rendszer virtuális merevlemez (VHD) a helyszíni feltöltésekor) telepítéséről további információkért lásd:

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "fokozott figyelés"
SAP "fokozott figyelés" feltétele kötelező Azure SAP futtatásához. Ellenőrizze a részleteket az SAP vegye figyelembe, hogy 2191498 "SAP lévő Linux rendelkező Azure: fokozott figyelés".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Az Azure Linux virtuális gép Azure adatlemez csatolása
Soha ne csatlakoztatási Azure adatlemezek egy Azure Linux virtuális gépre használatával az eszközazonosító. Ehelyett használja a univerzálisan egyedi azonosító (UUID). Legyen óvatos, amikor például a csatlakoztatási Azure-adatlemez, hogy grafikus eszközöket használja. Ellenőrizze a bejegyzéseket /etc/fstab.

A problémát az Eszközazonosítót, előfordulhat, hogy módosítsa azt, és majd a az Azure virtuális gép leállását előfordulhat, hogy a rendszerindítási folyamat során. A probléma orvoslása érdekében érdemes felvenni a nofail paraméter a /etc/fstab. De legyen óvatos nofail, mert alkalmazások használhatja a csatlakoztatási pontot előtt, és előfordulhat, hogy a legfelső szintű fájl rendszerbe írási, abban az esetben, ha egy külső az Azure data lemez nem lett csatlakoztatva a rendszerindítás során.

Csatlakoztatását a UUID keresztül az egyetlen kivétel van lemezcsatlakoztatás egy operációs rendszer hibaelhárítás céljából, az az alábbi szakaszban leírtak szerint.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>A SUSE virtuális gép, amely nem érhető el többé hibaelhárítása
Előfordulhat, hogy olyan helyzetekben, ahol az Azure virtuális gép SUSE lefagy a rendszerindítási folyamat során (például a lemezek csatlakoztatása kapcsolatos hiba). A probléma a rendszerindítási diagnosztikai funkciót használ az Azure-portálon az Azure virtuális gépek v2 ellenőrizheti. További információkért lásd: [rendszerindítási diagnosztika](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Egy megoldani a problémát módja a sérült VM az operációsrendszer-lemezképet csatlakoztatni másik SUSE virtuális gép az Azure-on. A következő szakaszban leírt módon végezze el például /etc/fstab szerkesztésével, vagy eltávolítani hálózati udev megfelelő módosításokat.

Nincs egyik lényeges tudnivaló, figyelembe kell venni. Az operációsrendszer-lemezképet a azonos UUID mindig csatolható több SUSE virtuális gépeket, a azonos Azure Piactéri lemezképből (például az SLES 11 SP4) telepítése hatására. Ezért az UUID segítségével egy másik virtuális Gépet, amely ugyanazt az Azure piactér kép eredményt két azonos UUID-k használatával lett telepítve az operációs rendszer lemez csatolása. Két, azonos UUID-k oka a virtuális gép felhasználási területe a hibakeresés, a csatlakoztatott és sérült operációsrendszer-lemez helyett az eredeti operációs rendszer lemezének fájlból történő rendszerindítás.

A problémák elkerülése érdekében két módja van:

* Használjon egy másik Azure piactér-lemezképet a hibaelhárítási virtuális gép (például SLES 11 SPx SLES 12 helyett).
* Ne csatlakoztassa a sérült operációsrendszer-lemez egy másik virtuális gép segítségével UUID--használata valami mással.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Az Azure-bA helyszíni SUSE VM feltöltése
Töltse fel az Azure-bA helyszíni SUSE VM lépések, olvassa el [SLES vagy openSUSE virtuális gép előkészítése Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Ha szeretne feltölteni a virtuális gépek a deprovision lépés végén nélkül (például, hogy tartsa egy SAP példánya, valamint az állomás neve), ellenőrizze a következő elemek:

* Győződjön meg arról, hogy az operációs rendszer lemezének csatlakoztatva van-e UUID és nem az eszközazonosító. Csak a /etc/fstab UUID módosítása nincs elég az operációsrendszer-lemezképet. Továbbá ne feledje igazítja a rendszertöltő YaST vagy /boot/grub/menu.lst szerkesztésével.
* Ha a VHDX formátum használata a SUSE operációsrendszer-lemez, és átalakíthatja a virtuális merevlemez feltöltése az Azure-ba, valószínű, hogy a hálózati eszköz állapotról eth0 eth1. Indítása során átnevezés, még az Azure-on később problémák elkerülése érdekében állítsa vissza eth0 leírtak [kijavítása az eth0 klónozott SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Mi a cikkben ismertetett, mellett azt javasoljuk, hogy a fájl eltávolítása:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Az Azure Linux ügynök (waagent) elkerülése végett lehetséges problémák kezeléséhez, mindaddig, amíg nincsenek több hálózati adapterrel is telepíthet.

## <a name="deploying-a-suse-vm-on-azure"></a>A SUSE Azure virtuális gép telepítése
Új SUSE virtuális gépek JSON-sablon fájlokat az új Azure Resource Manager-modell használatával kell létrehoznia. A sablon JSON-fájl létrehozása után a virtuális Gépet a következő paranccsal CLI PowerShell alternatívájaként telepítheti:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
JSON-sablon fájlokkal kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../../../resource-group-authoring-templates.md) és [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates/).

Parancssori felület és az Azure Resource Manager kapcsolatos további információkért lásd: [használja az Azure parancssori felület Mac, Linux és a Windows Azure Resource Manager](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>SAP licenc- és hardver kulcs
A hivatalos SAP-Azure hitelesítésszolgáltatóhoz egy új mechanizmus az SAP hardver az SAP-licenc használt kulcs kiszámításához jelent meg. Az SAP kernel kellett kell igazítani, hogy az új algoritmust használja. Linux-SAP kernel korábbi verziók nem tartalmazta a kód módosítása. Ezért, bizonyos esetekben (például az Azure virtuális gép átméretezésével), a SAP hardver kulcs megváltozott, és az SAP-licenc lett már nem érvényes. A megoldás által biztosított újabb SAP Linux kernelek.  A részletes SAP kernel javítások SAP Megjegyzés 1928533 ismerteti.

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE sapconf csomag / beállított adm
SUSE "sapconf", amely felügyeli az SAP-specifikus beállításokat készlete nevű csomagot biztosít. Ez a csomag funkciója, és hogyan kell telepíteni és használni kapcsolatos további információkért lásd: [sapconf segítségével készítse elő a SUSE Linux Enterprise Server SAP rendszerek futtatására](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) és [sapconf Újdonságok vagy előkészítése a SUSE Linux Enterprise Az SAP rendszert futtató kiszolgáló? ](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Nincs időközben egy új eszközt, amely a felváltja "sapconf - beállított adm". Egy talál további információkat erről az eszközről a két hivatkozás a következő:

- SLES "beállított-adm" profil sap-hana dokumentációjában található [Itt](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

- Az SAP-munkaterhelések az "beállított-adm" - hangolási rendszerek található [Itt](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) fejezet 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>Az NFS-megosztások SAP elosztott telepítés
Ha rendelkezik egy elosztott telepítés – például, ahová az adatbázis és az SAP alkalmazáskiszolgálók telepítése különálló virtuális gépek – megoszthatja a /sapmnt directory keresztül hálózati fájlrendszer (NFS). Ha problémába ütközik, a telepítés lépéseit az NFS-megosztás /sapmnt a létrehozása után, ellenőrizze, hogy ha "no_root_squash" be van állítva a megosztáshoz.

## <a name="logical-volumes"></a>Logikai kötetek
A múltban Ha több Azure adatok lemezre (például az SAP-adatbázist), a logikai nagy egy szükséges volt célszerű Raid-felügyeleti eszköz MDADM használni, mert a logikai kötet Manager Linux (LVM) nem lett teljesen érvényesítve még az Azure. Megtudhatja, hogyan állíthat be az Azure-on Linux RAID mdadm használatával, lásd: [szoftveres RAID Linux konfigurálása](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Időközben frissítésétől 2016. május elejére Linux logikai kötetkezelő teljes mértékben támogatja az Azure-on, és MDADM alternatívájaként használható. Az Azure-on LVM kapcsolatos további információkért olvassa el:  
[A Linux virtuális gép az Azure-ban LVM konfigurálása](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Azure SUSE-tárházat
Ha a hozzáférési probléma lehet a normál Azure SUSE tárházba, egy parancs segítségével állítsa alaphelyzetbe. Az ilyen problémák akkor fordulhat elő, ha egy Azure-régió, hozzon létre egy saját operációsrendszer-lemezképek és majd a lemezkép átmásolása a saját operációsrendszer-lemezképek alapuló új virtuális gépek telepítése egy másik Azure-régiót. A következő parancsot a virtuális Gépen belül:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome asztali
Ha a Gnome asztali belül egyetlen virtuális gép – egy SAP grafikus felhasználói Felülettel, beleértve a teljes SAP bemutató rendszer telepítéséhez használni kívánt böngésző és az SAP felügyeleti konzol--segítségével a mutatót telepítse az Azure SLES lemezképek:

   Az SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   A SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Oracle Linux a felhőben SAP támogatása
Nincs a virtualizált környezetet az Oracle Linux támogatás korlátozás. Ez a támogatás korlátozás, de nem egy Azure-specifikus témakör fontos megérteni. SAP nem támogatja a Oracle SUSE vagy nyilvános felhőben működő Azure például Red Hat. Addig is fut, az Azure-ban Oracle adatbázis teljes mértékben támogatja SAP, Oracle Linux (lásd az SAP Megjegyzés 1928533). Ha más kombináció szükség, lépjen kapcsolatba közvetlenül Oracle.

