---
title: "Linux virtuális gépek Azure-ban – áttekintés |} Microsoft Docs"
description: "A Linux virtuális gépek Azure számítási, tárolási és hálózati szolgáltatások ismerteti."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: timlt
editor: 
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/14/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: ae4b8f423489bf417f1086368db9b1043cd7f396
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="azure-and-linux"></a>Azure és Linux
Microsoft Azure integrált nyilvános egyre bővülő gyűjteménye a webes és a felhőalapú szolgáltatások elemzés, virtuális gépek, adatbázisok, mobil-, hálózati, tárolási, beleértve&mdash;épp ezért tökéletes választás a megoldások üzemeltetéséhez.  A Microsoft Azure egy skálázható számítási platformot kínál, amely lehetővé teszi, hogy csak a valóban használt funkciókért fizessen, és csak akkor, amikor használja azokat, mindezt helyszíni hardverberuházások nélkül.  Az Azure készen áll a megoldások vertikális és horizontális skálázásakor, bármilyen méretre is lenne szüksége az ügyfelek igényeinek kiszolgálásához.

Ha jártas Amazon meg különböző funkcióhoz AWS, tekintse meg az Azure vs AWS [definition leképezési dokumentum](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Régiók
A Microsoft Azure-erőforrások több földrajzi régióban a világ különböző pontjain.  Egy "régió" azonos földrajzi területen több különböző adatközponthoz jelöli.  Egy további 4 régiók bejelentette a világszerte kell 34 régiók általánosan elérhető. Mivel jelenleg továbbra is a globális érvényességének - bontsa ki azt karbantartása meglévő és újonnan frissített listáját bejelentette régiók.

* [Azure-régiók](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Rendelkezésre állás
Azt jelentette be egy iparágban vezető egypéldányos virtuális gép szolgáltatásiszint-megállapodás 99,9 %-a megadott központi telepítése a virtuális gép összes lemeze a prémium szintű Storage.  Ahhoz, hogy a környezetet, hogy a virtuális gép szolgáltatásiszint-szerződés a szabványos 99,95 % jogosultsághoz, továbbra is szeretné a rendelkezésre állási csoportok belül munkaterhelést futtató két vagy több virtuális gépek telepítéséhez. Ezzel biztosíthatja, hogy a virtuális gépek vannak elosztva a adatközpontokban több tartalék tartományok között, valamint a különböző karbantartási időszakok gazdagépekre telepített. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)-ban található.

## <a name="managed-disks"></a>Felügyelt lemezek

Által kezelt lemezeken Azure Storage leírók fiók létrehozását és kezelését a háttérben, és biztosítja, nem kell aggódnia a méretezhetőségének korlátai, a tárfiók. Egyszerűen adja meg, a lemez mérete és a teljesítményszintet (Standard vagy prémium), és az Azure hoz létre, és az Ön kezeli a lemezt. Akkor sem kell a használt tárterület miatt aggódnia, ha lemezeket ad hozzá vagy fel-/leskálázza a virtuális gépet. Ha hoz létre új virtuális gépek, [használja az Azure CLI 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy az Azure-portálon létrehozott virtuális gépet felügyelt operációsrendszer- és adatlemezek. Ha a virtuális gépek nem felügyelt lemezzel rendelkezik, akkor [alakítsa át a virtuális gépek biztonsági felügyelt lemezzel](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az egyéni rendszerképeit Azure-régiónként egyetlen tárfiókban is felügyelheti, és a használatukkal több száz virtuális gépet hozhat létre egy adott előfizetésben. A felügyelt lemezekkel kapcsolatos további információért tekintse meg [a felügyelt lemezek áttekintésével kapcsolatos cikket](../windows/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Az Azure Virtual Machines- & példányok
A Microsoft Azure támogatja futtató számos népszerű Linux terjesztésekről megadott és a partnerei számos tartja fenn.  Megtalálja azokat a terjesztéseket, mint az Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, freebsd rendszerű, az Azure piactéren. Aktívan dolgozunk az hozzáadása még több változatban is elkészíti a különböző Linux Közösségek a [Azure által támogatott Linux Disztribúciókkal](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) listája.

Ha az előnyben részesített Linux distro választott nincs, a rendszer jelenleg a katalógusban "helyezheti a saját Linux" virtuális gép által [létrehozása és feltöltése az Azure Linux virtuális merevlemez](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure virtuális gépek telepítése számos különböző megoldásokat számítástechnikai gyors módon teszik lehetővé. Szinte bármilyen munkaterhelést és szinte bármilyen operációs rendszer – Windows, Linux, a ismeretcikkekkel is telepíthet, vagy egy egyéni létre egyet a partnerek egyre bővülő listáját. Még nem látja éppen megtekintett?  Ne aggódjon, mert a helyszíni is helyezheti a saját lemezképek.

## <a name="vm-sizes"></a>Virtuálisgép-méretek
Amikor telepít egy Azure-ban, válassza ki a Virtuálisgép-méretet méretek a sorozata, amely lehetővé teszi a munkaterhelés egyik fog. A mérete befolyásolja a feldolgozási teljesítmény, a memória és a tárolási kapacitás a virtuális gép is. A virtuális gép fut, és a kiosztott erőforrásokat fel idő alapján kell fizetni. Teljes listáját [a virtuális gépek méretét](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az alábbiakban néhány alapvető útmutatók kijelölése az adatsorozat (A, D, DS, G és GS) egyik Virtuálisgép-méretet.
* A-sorozatú virtuális gépek a könnyű munkaterhelések és szolgáltatásának fejlesztési/tesztelési kezdő virtuális gépek áron érték. Hogy széles körben elérhető, amelyek minden régióban, és csatlakozzon, és virtuális gépek számára elérhető összes szabványos erőforrások.
* (A8 - A11) A-sorozatú értékek különleges számítási intenzív konfigurációk nagy teljesítményű számítástechnikai fürt alkalmazásoknál.
* A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb magonkénti memóriaarányt, valamint az ideiglenes lemezteljesítményhez SSD meghajtókat kínálnak.
* Dv2-sorozat, a D sorozat legújabb verziója, nagyobb teljesítményű CPU szolgáltatásokat. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációját alapul 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haskell) processzor, és az Intel Turbo program technológia 2.0 folytathatja legfeljebb 3,2 GHz-es. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.
* A G-sorozat virtuális gépei kínálják a legtöbb memóriát, és az Intel Xeon E5 V3 család processzorait tartalmazó gazdagépeken futnak.

Megjegyzés: DS-méretek és GS sorozatnak virtuális gépek hozzáférjen a prémium szintű Storage, mert az SSD biztonsági nagy teljesítményű, alacsony késésű tárolási i/o-igényes munkaterhelések esetében. A Premium Storage szolgáltatás csak bizonyos régiókban érhető el. Részletes információ:

* [Prémium szintű Storage: Nagy teljesítményű tárolást az Azure virtuális gépek terheléséhez](../windows/premium-storage.md)

## <a name="automation"></a>Automatizálás
Egy megfelelő DevOps kulturális környezet eléréséhez összes infrastruktúra kódot kell lennie.  Ha minden él, az infrastruktúra kód is könnyen lehet újra (Phoenix kiszolgálók).  Azure például Ansible, Chef, SaltStack és Puppet tooling eszköz fő automation működik.  Azure automation a saját eszközt használunk erre is rendelkezik:

* [Azure-sablonok](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Az Azure vmaccess bővítmény](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure támogatás bevezetéséről [felhő inicializálás](http://cloud-init.io/) között, amelyek támogatják ezt a legtöbb Linux-Disztribúciókkal.  Canonical tartozó Ubuntu virtuális gépek jelenleg a felhő inicializálás alapértelmezés szerint engedélyezve vannak telepítve.  Piros kalap RHEL, CentOS és Fedora támogatja a felhő inicializálás, azonban az Azure-rendszerképek tartja fenn RedHat nem rendelkezik telepített felhő inicializálás.  Felhő inicializálás egy RedHat termékcsalád operációs rendszer használatához létre kell hoznia egy egyéni lemezképet felhő inicializálás telepítve.

* [Az Azure Linux virtuális gépeken futó felhőalapú inicializálás használatával](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>Kvóták
Minden Azure-előfizetéssel rendelkezik alapértelmezett kvótát, amely jelentős hatással lehet a virtuális gépek nagy számú a projekt telepítését. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép.  Kvóták is gyorsan következik be, és könnyen egy támogatási jegy maximális kérő tervátalakítási növelheti.  További részletekért a kvótakorlát:

* [Azure-előfizetés szolgáltatásra vonatkozó korlátozások](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partnerek
Microsoft szorosan együttműködik olyan partnereink számára, győződjön meg a rendelkezésre álló képeket frissítése, és egy Azure futtatókörnyezetet optimalizálva.  Partnereink további információt az alábbi piactér lapok ellenőrizze.

* Az Azure - Linux [által támogatott Disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* SUSE - [Azure piactér - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat - [Azure piactér - RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure piactér - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure piactér – Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* Freebsd rendszerű - [Azure piactér - freebsd rendszerű 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS - [Azure piactér - CoreOS (stabil)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure piactér - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami könyvtár az Azure-bA](https://azure.bitnami.com/)
* Mesosphere - [Azure piactér - Mesosphere DC/OS az Azure-on](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure piactér – a Docker Swarm Azure Tárolószolgáltatás](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure piactér - CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>Ismerkedés az Azure-on Linux
Az Azure-fiók, az Azure parancssori felület telepítése és SSH nyilvános és titkos kulcsok párban kell Azure használatának megkezdéséhez.

### <a name="sign-up-for-an-account"></a>Fiók létrehozása
Az első lépés az Azure-felhő használatával Azure-fiókot regisztrálni.  Lépjen a [Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/) első lépések lap.

### <a name="install-the-cli"></a>A parancssori felület telepítése
Az új Azure-fiókjával, és elkezdheti, azonnal az Azure-portál használatával, amely egy webalapú felügyeleti panel.  Az Azure felhőben keresztül a parancssori kezeléséhez telepítse a `azure-cli`.  Telepítse a [Azure CLI 2.0](/cli/azure/install-azure-cli) a Mac vagy Linux számítógépen.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
Most, hogy az Azure-fiók, az Azure web, és az Azure parancssori felület.  A következő lépés, ha az SSH-kulcspárral, amellyel SSH ból Linuxba jelszó nélkül.  [SSH-kulcsok létrehozása Linux és Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) jelszó nélküli bejelentkezéseket és a nagyobb biztonság engedélyezése.

### <a name="create-a-vm-using-the-cli"></a>Virtuális gép létrehozása a parancssori felületről
A Linux virtuális gép létrehozása a parancssori felület módja a gyors telepítése egy virtuális Gépet anélkül, hogy a Terminálszolgáltatások dolgozik.  Megadhatja a webportálon rendelkezésre álló parancssori jelző vagy kapcsolón keresztül.  

* [Linux virtuális gép létrehozása a parancssori felület](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>Hozzon létre egy virtuális Gépet a portálon
Linux virtuális gép létrehozása az Azure web portal alkalmazásban módja a könnyen pontot, majd kattintson a telepítés gombra a különböző lehetőségeit.  Vagy parancssori kapcsolók helyett is töltött webes elrendezést a különböző lehetőségek és beállítások megtekintéséhez.  Minden elérhető a parancssori felületen keresztül érhető el a portálon.

* [Linux virtuális gép létrehozása a portálon](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>Bejelentkezés SSH-jelszó nélkül
A virtuális gép most már fut az Azure-on, és készen áll-e jelentkezni.  Jelszavak SSH-kapcsolaton keresztül bejelentkezési használata nem biztonságos és sok időt vesz igénybe.  SSH-kulcsok használata a legbiztonságosabb módja, valamint a leggyorsabban használatát a bejelentkezéshez.  Amikor hoz létre, Linux virtuális gép a portál vagy a parancssori felületen keresztül, két hitelesítés lehetősége van.  Ha az SSH egy jelszót, Azure konfigurálja a virtuális gép keresztül jelszavak bejelentkezések engedélyezéséhez.  Ha úgy döntött, hogy nyilvános SSH-kulcsot használ, az Azure konfigurálja a virtuális gép csak engedélyezi az SSH-kulcsok keresztül bejelentkezések, és letiltja a jelszavas bejelentkezéseket. A Linux virtuális gép biztonságos SSH-kulcs bejelentkezések csak tételével, használja az SSH nyilvános kulcs lehetőséggel a portál vagy a CLI-t a virtuális gépek létrehozása során.

## <a name="related-azure-components"></a>Az Azure kapcsolódó összetevők
## <a name="storage"></a>Storage
* [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)
* [Vegyen fel egy lemezt egy Linux virtuális Gépet az azure parancssori felület használatával](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Hogyan lehet adatlemezt csatolni egy Linux virtuális Gépet az Azure-portálon](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Hálózat
* [Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)
* [IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Linux virtuális gép portok megnyitása az Azure-ban](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Hozzon létre egy teljesen minősített tartománynevet az Azure-portálon](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>Tárolók
* [Virtuális gépek és a tárolók az Azure-ban](containers.md)
* [Azure Tárolószolgáltatás bemutatása](../../container-service/container-service-intro.md)
* [Az Azure Container Service-fürt üzembe helyezése](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Következő lépések
Most már rendelkezik Azure-on Linux áttekintését.  A következő lépés az, hogy férhet hozzá, és néhány virtuális gépek létrehozása!

* [Mintaparancsfájlok egyre bővülő listáját általános feladatokhoz felfedezés AzureCLI keresztül](cli-samples.md)
