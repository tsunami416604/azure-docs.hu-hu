---
title: "Az Azure-beli Linux rendszerű virtuális gépek áttekintése | Microsoft Docs"
description: "A cikk az Azure Compute, Storage és Networking szolgáltatások Linux virtuális gépekkel történő használatát ismerteti."
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
ms.openlocfilehash: cef9abddf980c695040e99995eb325eeb182fad4
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="azure-and-linux"></a>Az Azure és a Linux
A Microsoft Azure integrált nyilvános felhőszolgáltatások (például elemzési, virtuálisgép-, adatbázis-, mobil-, hálózati, tárolási és webes szolgáltatások) egyre bővülő gyűjteménye&mdash;ideális a megoldások üzemeltetéséhez.  A Microsoft Azure egy skálázható számítási platformot kínál, amely lehetővé teszi, hogy csak a valóban használt funkciókért fizessen, és csak akkor, amikor használja azokat, mindezt helyszíni hardverberuházások nélkül.  Az Azure készen áll a megoldások vertikális és horizontális skálázásakor, bármilyen méretre is lenne szüksége az ügyfelek igényeinek kiszolgálásához.

Ha már ismeri az Amazon AWS különböző funkcióit, tekintse meg az Azure-t és az AWS-t összehasonlító [definícióleképezési dokumentumot](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Régiók
A Microsoft Azure-erőforrások világszerte több földrajzi régió között oszlanak meg.  Egy „régió” több adatközpontot jelöl ugyanazon a földrajzi területen.  34 általánosan elérhető régiónk van világszerte, és további 4 már be van jelentve. Mivel folyamatosan növeljük a globális lefedettségünket, frissített listát vezetünk a meglévő és az újonnan bejelentett régiókról.

* [Azure-régiók](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Rendelkezésre állás
Bejelentettünk egy iparágvezető, 99,9%-os elérhetőséget biztosító egypéldányos virtuálisgép-szolgáltatói szerződést, amelynek az a feltétele, hogy az üzembe helyezett virtuális gép összes lemezén prémium szintű tárolást használjon.  Ahhoz, hogy az üzembe helyezett példány megfeleljen a standard 99,95%-os virtuálisgép-szolgáltatói szerződésünknek, legalább még két virtuális gépet kell üzembe helyeznie a számítási feladatok futtatásához egy rendelkezésre állási csoporton belül. Ezzel biztosítja, hogy a virtuális gépek több tartalék tartomány között legyenek elosztva az adatközpontjainkban, valamint az őket futtató gazdagépeknek különböző karbantartási időszakaik legyenek. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/)-ban található.

## <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezek a háttérben végzik az Azure Storage-fiók létrehozását és kezelését, hogy Önnek ne kelljen foglalkoznia a tárfiók skálázási korlátaival. Egyszerűen csak adja meg a lemez méretét és teljesítményszintjét (Standard vagy Prémium), és az Azure létrehozza és felügyeli a lemezt Ön helyett. Akkor sem kell a használt tárterület miatt aggódnia, ha lemezeket ad hozzá vagy fel-/leskálázza a virtuális gépet. Ha új virtuális gépeket hoz létre, [használja az Azure CLI 2.0-t](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) vagy az Azure Portalt a felügyelt operációs rendszerrel és adatlemezekkel rendelkező virtuális gépek létrehozásához. Ha a virtuális gépei nem felügyelt lemezeket tartalmaznak, [átalakíthatja a virtuális gépeket, hogy felügyelt lemezek támogassák őket](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az egyéni rendszerképeit Azure-régiónként egyetlen tárfiókban is felügyelheti, és a használatukkal több száz virtuális gépet hozhat létre egy adott előfizetésben. A felügyelt lemezekkel kapcsolatos további információért tekintse meg [a felügyelt lemezek áttekintésével kapcsolatos cikket](../windows/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Azure-beli virtuális gépek és példányok
A Microsoft Azure több, különböző partnerek által biztosított és kezelt, népszerű Linux-disztribúció futtatását is támogatja.  Az Azure Marketplace-en olyan disztribúciókat találhat, mint a Red Hat Enterprise, a CentOS, a SUSE Linux Enterprise, a Debian, az Ubuntu, a CoreOS, a RancherOS, a FreeBSD és még sok más. Folyamatosan együttműködünk a különböző-Linux közösségekkel, hogy még tovább bővíthessük az [Azure által támogatott Linux-disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) listáját.

Ha a kívánt Linux-disztribúció jelenleg nem szerepel a katalógusban, „Saját Linux használata” típusú virtuális gépet is használhat. Ehhez [hozzon létre egy Linux VHD-t, és töltse fel az Azure-ba](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Az Azure-beli virtuális gépekkel sokféle számítástechnikai megoldást helyezhet gyorsan üzembe. Gyakorlatilag bármilyen számítási feladatot és nyelvet üzembe helyezhet szinte az összes operációs rendszeren – legyen az Windows, Linux vagy egy egyénileg létrehozott operációs rendszer az egyre bővülő partnerlistánkról. Még mindig nem találja, amit keres?  Ne aggódjon – a saját helyszíni rendszerképeit is használhatja.

## <a name="vm-sizes"></a>A virtuális gépek mérete
Amikor üzembe helyez egy virtuális gépet az Azure-ban, ki kell választania egy virtuálisgép-méretet a számítási feladathoz megfelelő méretsorozatok közül. A méret emellett hatással van a virtuális gép feldolgozási teljesítményére, valamint a memória- és tárhelykapacitására is. A számlázás a virtuális gép futásideje és a lefoglalt erőforrások fogyasztása alapján történik. A [virtuálisgép-méretek](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) teljes listája.

Az alábbiakban alapvető irányelveket talál a virtuális gép méretének kiválasztásához a sorozataink egyikéből (A, D, DS, G és GS).
* Az A-sorozat virtuális gépei kedvező árú, belépő szintű virtuális gépek a kisebb számítási feladatokhoz és a fejlesztési/tesztelési forgatókönyvekhez. Széles körben elérhetők az összes régióban, és csatlakozhatnak a virtuális gépek számára elérhető standard erőforrásokhoz, illetve használhatják azokat.
* Az A-sorozat méretei (A8 – A11) speciális, nagy számításigényű konfigurációk, és megfelelőek a nagy teljesítményű feldolgozási fürttel rendelkező alkalmazásokhoz.
* A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb vCPU-nkénti memóriaarányt, valamint az ideiglenes lemezteljesítményhez SSD meghajtókat kínálnak.
* A Dv2-sorozat a D-sorozat legújabb verziója, amely nagyobb teljesítményű processzorokat kínál. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haskell) processzoron alapul, és a teljesítménye az Intel Turbo Boost Technology 2.0 alkalmazásával akár 3,2 GHz-re is növelhető. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.
* A G-sorozat virtuális gépei kínálják a legtöbb memóriát, és az Intel Xeon E5 V3 család processzorait tartalmazó gazdagépeken futnak.

Megjegyzés: A DS- és a GS-sorozat virtuális gépei hozzáférhetnek a Premium Storage-hoz, amely SSD-alapú, nagy teljesítményű és kis késésű tárolást biztosít az I/O-igényes számítási feladatokhoz. A Premium Storage szolgáltatás csak bizonyos régiókban érhető el. Részletes információ:

* [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-beli virtuális gépek számítási feladataihoz](../windows/premium-storage.md)

## <a name="automation"></a>Automatizálás
A megfelelő DevOps-kultúra megteremtéséhez minden infrastruktúrának kódoltnak kell lennie.  Ha minden infrastruktúra kódolt formában létezik, egyszerűen létrehozható újra (Phoenix Servers).  Az Azure az összes fontosabb automatizáló eszközt használja, köztük az Ansible, a Chef, a SaltStack és a Puppet eszközöket.  Az Azure továbbá saját automatizáló eszközökkel is rendelkezik:

* [Azure-sablonok](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure VMAccess](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Az Azure bevezeti a [cloud-init](http://cloud-init.io/) támogatását a legtöbb Linux-disztribúcióban, amely támogatja azt.  A Canonical Ubuntu-alapú virtuális gépei jelenleg az üzembe helyezéskor alapértelmezés szerint engedélyezik a cloud-init eszközt.  A Red Hats RHEL, a CentOS és a Fedora támogatják a cloud-init eszközt, a RedHat által karbantartott Azure-rendszerképeken viszont nincs telepítve.  A cloud-init RedHat termékcsaládba tartozó operációs rendszeren való használatához létre kell hoznia egy egyéni rendszerképet, amelyre telepítve van a cloud-init.

* [A cloud-init használata Azure-beli Linux rendszerű virtuális gépeken](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quotas"></a>Kvóták
Minden Azure-előfizetésre alapértelmezett kvótakorlátozások vonatkoznak, amelyek akadályt jelenthetnek, ha a projektjéhez nagy számú virtuális gépet szeretne üzembe helyezni. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép.  A kvótakorlátozások gyorsan és egyszerűen megemelhetők egy, a korlátozás emelését kérvényező támogatási jegy benyújtásával.  További információ a kvótakorlátozásokról:

* [Az Azure-előfizetés szolgáltatásokra vonatkozó korlátozásai](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partnerek
A Microsoft szorosan együttműködik a partnereivel, hogy az elérhető rendszerképek biztosan frissítve és optimalizálva legyenek az Azure futtatókörnyezetre.  A partnereinkkel kapcsolatos további információkért tekintse meg az alább felsorolt piactéri oldalaikat.

* Linux az Azure-on – [Támogatott disztribúciók](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?search=%27SUSE%27)
* Redhat – [Azure Marketplace – RedHat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical – [Azure Marketplace – Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian – [Azure Marketplace – Debian 8 „Jessie”](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD – [Azure Marketplace – FreeBSD 10.3](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/)
* CoreOS – [Azure Marketplace – CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS – [Azure Marketplace – RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami – [Azure-hoz készült Bitnami Library](https://azure.bitnami.com/)
* Mesosphere – [Azure Marketplace – Mesosphere DC/OS az Azure-on](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker – [Azure Marketplace – Azure Container Service a Docker Swarmmal](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins – [Azure Marketplace – CloudBees Jenkins Platform](https://azure.microsoft.com/marketplace/partners/cloudbees/jenkins-platformjenkins-platform/)

## <a name="getting-started-with-linux-on-azure"></a>A Linuxon futó Azure használatának első lépései
Az Azure használatának megkezdéséhez szüksége lesz egy Azure-fiókra, telepített Azure CLI-re, valamint egy nyilvános és titkos SSH-kulcspárra.

### <a name="sign-up-for-an-account"></a>Fiók létrehozása
Az Azure Cloud használatának első lépése egy Azure-fiók létrehozása.  A kezdéshez nyissa meg az [Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/) oldalt.

### <a name="install-the-cli"></a>A CLI telepítése
Az új Azure-fiókkal azonnal megkezdheti az Azure Portal használatát, amely egy webalapú felügyeleti panel.  Az Azure Cloud parancssori felületen történő kezeléséhez telepítse a következőt: `azure-cli`.  Telepítse az [Azure CLI 2.0-t](/cli/azure/install-azure-cli) a Mac vagy Linux munkaállomásra.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása
Ezen a ponton rendelkezik egy Azure-fiókkal, az Azure webportállal és az Azure CLI-vel.  A következő lépés egy SSH-kulcspár létrehozása, amellyel SSH-n keresztül, jelszó használata nélkül jelentkezhet be Linuxba.  [Hozzon létre SSH-kulcsokat a Linux és a Mac rendszerben](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), hogy lehetővé tegye a jelszó nélküli bejelentkezést, és növelje a biztonságot.

### <a name="create-a-vm-using-the-cli"></a>Virtuális gép létrehozása a parancssori felületről
A Linux rendszerű virtuális gépek parancssori felületről való létrehozása gyors módja a virtuális gépek üzembe helyezésének, anélkül, hogy el kellene hagynia a jelenleg használt terminált.  Minden, amit meg lehet adni a webportálon, elérhető egy parancssori jelzővel vagy kapcsolóval is.  

* [Linux rendszerű virtuális gép létrehozása a parancssori felületről](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="create-a-vm-in-the-portal"></a>Virtuális gép létrehozása a portálon
A Linux rendszerű virtuális gépek Azure webportálon való létrehozásával egyszerűen, néhány kattintással választhat a különböző beállítások között az üzembe helyezés előtt.  A parancssori jelzők vagy kapcsolók használata helyett a különböző lehetőségeket és beállításokat egy jól átlátható webes elrendezésben tekintheti meg.  Minden, ami elérhető a parancssori felületen, elérhető a portálon is.

* [Linux rendszerű virtuális gép létrehozása a Portal használatával](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="login-using-ssh-without-a-password"></a>Bejelentkezés jelszó nélkül, SSH használatával
A virtuális gép most már az Azure-ban fut, és Ön készen áll a bejelentkezésre.  Az SSH-n keresztüli jelszavas bejelentkezés nem biztonságos, és sok időt vesz igénybe.  Az SSH-kulcsok használata a legbiztonságosabb és a leggyorsabb mód a bejelentkezésre.  Amikor a Linux rendszerű virtuális gépet a portálon vagy a parancssori felületen hozza létre, kétféle hitelesítés közül választhat.  Ha kiválaszt egy jelszót az SSH számára, az Azure úgy konfigurálja a virtuális gépet, hogy engedélyezze a jelszavas bejelentkezést.  Ha az SSH nyilvános kulcs használatát választja, az Azure úgy konfigurálja a virtuális gépet, hogy csak az SSH-kulcsokkal való bejelentkezést engedélyezze, és letiltja a jelszavas bejelentkezést. Ha azzal szeretné védeni a Linux rendszerű virtuális gépet, hogy csak SSH-kulcsokkal engedélyezi a bejelentkezést, a virtuális gép portálon vagy parancssori felületen való létrehozásakor használja az SSH nyilvános kulcs lehetőséget.

## <a name="related-azure-components"></a>Kapcsolódó Azure-összetevők
## <a name="storage"></a>Storage
* [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)
* [Lemez hozzáadása Linux rendszerű virtuális géphez az azure-cli használatával](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Adatlemez csatlakoztatása Linux rendszerű virtuális géphez az Azure Portalon](attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="networking"></a>Hálózat
* [A Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)
* [IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Portok nyitása egy Linux rendszerű virtuális géphez az Azure Portalon](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Teljes tartománynév létrehozása az Azure Portalon](portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="containers"></a>Tárolók
* [Virtuális gépek és tárolók az Azure-ban](containers.md)
* [Az Azure Container Service bemutatása](../../container-service/container-service-intro.md)
* [Az Azure Container Service-fürt üzembe helyezése](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Következő lépések
A cikk a Linux gépek Azure-on való használatáról nyújtott áttekintést.  Következő lépésként hozzon létre néhány virtuális gépet!

* [Fedezze fel egyre bővülő mintaszkriptlistánkat a gyakori feladatokhoz az Azure CLI-n keresztül](cli-samples.md)
