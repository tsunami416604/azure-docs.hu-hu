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
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: cdb2bda0c3f7e73b115c2609c3f229c633093bdc
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="azure-and-linux"></a>Az Azure és a Linux
A Microsoft Azure integrált nyilvános felhőszolgáltatások (például elemzési, virtuálisgép-, adatbázis-, mobil-, hálózati, tárolási és webes szolgáltatások) egyre bővülő gyűjteménye&mdash;ideális a megoldások üzemeltetéséhez.  A Microsoft Azure egy skálázható számítási platformot kínál, amely lehetővé teszi, hogy csak a valóban használt funkciókért fizessen, és csak akkor, amikor használja azokat, mindezt helyszíni hardverberuházások nélkül.  Az Azure készen áll a megoldások vertikális és horizontális skálázásakor, bármilyen méretre is lenne szüksége az ügyfelek igényeinek kiszolgálásához.

Ha már ismeri az Amazon AWS különböző funkcióit, tekintse meg az Azure-t és az AWS-t összehasonlító [definícióleképezési dokumentumot](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

## <a name="regions"></a>Régiók
A Microsoft Azure-erőforrások világszerte több földrajzi régió között oszlanak meg.  Egy „régió” több adatközpontot jelöl ugyanazon a földrajzi területen. Az Azure jelenleg (2017. november) 36 általánosan elérhető régióval rendelkezik világszerte, és további 6 már be van jelentve. A meglévő és az újonnan bejelentett régiók naprakész listája a következő oldalon található:

* [Azure-régiók](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Rendelkezésre állás
Az Azure bejelentett egy iparágvezető, 99,9%-os elérhetőséget biztosító egypéldányos virtuálisgép-szolgáltatói szerződést, amelynek az a feltétele, hogy az üzembe helyezett virtuális gép összes lemezén prémium szintű tárolást használjon.  Ahhoz, hogy az üzembe helyezett példány megfeleljen a standard 99,95%-os virtuálisgép-szolgáltatói szerződésnek, legalább még két virtuális gépet kell üzembe helyeznie a számítási feladatok futtatásához egy rendelkezésre állási csoporton belül. A rendelkezésre állási csoport biztosítja, hogy a virtuális gépek több tartalék tartomány között legyenek elosztva az Azure-adatközpontokban, valamint az őket futtató gazdagépeknek különböző karbantartási időszakaik legyenek. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

## <a name="managed-disks"></a>Felügyelt lemezek

A felügyelt lemezek a háttérben végzik az Azure Storage-fiók létrehozását és kezelését, hogy Önnek ne kelljen foglalkoznia a tárfiók skálázási korlátaival. Adja meg a lemez méretét és teljesítményszintjét (Standard vagy Prémium), és az Azure létrehozza és felügyeli a lemezt. Nem kell a használt tárterület miatt aggódnia, amikor lemezeket ad hozzá, vagy fel-/leskálázza a virtuális gépet. Ha új virtuális gépeket hoz létre, [használja az Azure CLI 2.0-t](quick-create-cli.md) vagy az Azure Portalt a felügyelt operációs rendszerrel és adatlemezekkel rendelkező virtuális gépek létrehozásához. Ha a virtuális gépei nem felügyelt lemezeket tartalmaznak, [átalakíthatja a virtuális gépeket, hogy felügyelt lemezek támogassák őket](convert-unmanaged-to-managed-disks.md).

Az egyéni rendszerképeit Azure-régiónként egyetlen tárfiókban is felügyelheti, és a használatukkal több száz virtuális gépet hozhat létre egy adott előfizetésben. A felügyelt lemezekkel kapcsolatos további információért tekintse meg [a felügyelt lemezek áttekintésével kapcsolatos cikket](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Azure-beli virtuális gépek és példányok
A Microsoft Azure több, különböző partnerek által biztosított és kezelt, népszerű Linux-disztribúció futtatását is támogatja.  Az Azure Marketplace-en olyan disztribúciókat találhat, mint a Red Hat Enterprise, a CentOS, a SUSE Linux Enterprise, a Debian, az Ubuntu, a CoreOS, a RancherOS, a FreeBSD és még sok más. A Microsoft folyamatosan együttműködink a különböző-Linux közösségekkel, hogy még tovább bővíthesse az [Azure által támogatott Linux-disztribúciók](endorsed-distros.md) listáját.

Ha a kívánt Linux-disztribúció jelenleg nem szerepel a katalógusban, „Saját Linux használata” típusú virtuális gépet is használhat. Ehhez [hozzon létre egy Linux VHD-t, és töltse fel az Azure-ba](create-upload-generic.md).

Az Azure-beli virtuális gépekkel sokféle számítástechnikai megoldást helyezhet gyorsan üzembe. Gyakorlatilag bármilyen számítási feladatot és nyelvet üzembe helyezhet szinte az összes operációs rendszeren – legyen a Windows, Linux vagy egy egyénileg létrehozott operációs rendszer az egyre bővülő partnerlistáról. Még mindig nem találja, amit keres?  Ne aggódjon – a saját helyszíni rendszerképeit is használhatja.

## <a name="vm-sizes"></a>A virtuális gépek mérete
A használt virtuális gép [mérete](sizes.md) a futtatni kívánt számítási feladatok mennyiségétől függ. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Az Azure számos különböző méretet kínál különféle felhasználási módokhoz.

Az Azure [óradíjas rendszerben](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) számláz, a virtuális gép mérete és az operációs rendszer alapján. Nem egész órák esetében az Azure csak a használt perceket számlázza. A tárhely árazása és felszámítása külön történik.

## <a name="automation"></a>Automatizálás
A megfelelő DevOps-kultúra megteremtéséhez minden infrastruktúrának kódoltnak kell lennie.  Ha minden infrastruktúra kódolt formában létezik, egyszerűen létrehozható újra (Phoenix Servers).  Az Azure az összes fontosabb automatizáló eszközt használja, köztük az Ansible, a Chef, a SaltStack és a Puppet eszközöket.  Az Azure továbbá saját automatizáló eszközökkel is rendelkezik:

* [Azure-sablonok](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Az Azure bevezeti a [cloud-init](http://cloud-init.io/) támogatását a legtöbb Linux-disztribúcióban, amely támogatja azt.  A Canonical Ubuntu-alapú virtuális gépei jelenleg az üzembe helyezéskor alapértelmezés szerint engedélyezik a cloud-init eszközt.  A Red Hat's RHEL, a CentOS és a Fedora támogatják a cloud-init eszközt, a RedHat által karbantartott Azure-rendszerképeken azonban jelenleg nincs telepítve.  A cloud-init RedHat termékcsaládba tartozó operációs rendszeren való használatához létre kell hoznia egy egyéni rendszerképet, amelyre telepítve van a cloud-init.

* [A cloud-init használata Azure-beli Linux rendszerű virtuális gépeken](using-cloud-init.md)

## <a name="quotas"></a>Kvóták
Minden Azure-előfizetésre alapértelmezett kvótakorlátozások vonatkoznak, amelyek akadályt jelenthetnek, ha a projektjéhez nagy számú virtuális gépet szeretne üzembe helyezni. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép.  A kvótakorlátozások gyorsan és egyszerűen megemelhetők egy, a korlátozás emelését kérvényező támogatási jegy benyújtásával.  További információ a kvótakorlátozásokról:

* [Az Azure-előfizetés szolgáltatásokra vonatkozó korlátozásai](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partnerek
A Microsoft szorosan együttműködik a partnereivel, hogy az elérhető rendszerképek biztosan frissítve és optimalizálva legyenek az Azure futtatókörnyezetre.  További információk az Azure-partnerekről az alábbi hivatkozásokon találhatók:

* Linux az Azure-on – [Támogatott disztribúciók](endorsed-distros.md)
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
Ezen a ponton rendelkezik egy Azure-fiókkal, az Azure webportállal és az Azure CLI-vel.  A következő lépés egy SSH-kulcspár létrehozása, amellyel SSH-n keresztül, jelszó használata nélkül jelentkezhet be Linuxba.  [Hozzon létre SSH-kulcsokat a Linux és a Mac rendszerben](mac-create-ssh-keys.md), hogy lehetővé tegye a jelszó nélküli bejelentkezést, és növelje a biztonságot.

### <a name="create-a-vm-using-the-cli"></a>Virtuális gép létrehozása a parancssori felületről
A Linux rendszerű virtuális gépek parancssori felületről való létrehozása gyors módja a virtuális gépek üzembe helyezésének, anélkül, hogy el kellene hagynia a jelenleg használt terminált.  Minden, amit meg lehet adni a webportálon, elérhető egy parancssori jelzővel vagy kapcsolóval is.  

* [Linux rendszerű virtuális gép létrehozása a parancssori felületről](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Virtuális gép létrehozása a portálon
A Linux rendszerű virtuális gépek Azure webportálon való létrehozásával egyszerűen, néhány kattintással választhat a különböző beállítások között az üzembe helyezés előtt.  A parancssori jelzők vagy kapcsolók használata helyett a különböző lehetőségeket és beállításokat egy jól átlátható webes elrendezésben tekintheti meg.  Minden, ami elérhető a parancssori felületen, elérhető a portálon is.

* [Linux rendszerű virtuális gép létrehozása a Portal használatával](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Bejelentkezés jelszó nélkül, SSH használatával
A virtuális gép most már az Azure-ban fut, és Ön készen áll a bejelentkezésre.  Az SSH-n keresztüli jelszavas bejelentkezés nem biztonságos, és sok időt vesz igénybe.  Az SSH-kulcsok használata a legbiztonságosabb és a leggyorsabb mód a bejelentkezésre.  Amikor a Linux rendszerű virtuális gépet a portálon vagy a parancssori felületen hozza létre, kétféle hitelesítés közül választhat.  Ha kiválaszt egy jelszót az SSH számára, az Azure úgy konfigurálja a virtuális gépet, hogy engedélyezze a jelszavas bejelentkezést.  Ha az SSH nyilvános kulcs használatát választja, az Azure úgy konfigurálja a virtuális gépet, hogy csak az SSH-kulcsokkal való bejelentkezést engedélyezze, és letiltja a jelszavas bejelentkezést. Ha azzal szeretné védeni a Linux rendszerű virtuális gépet, hogy csak SSH-kulcsokkal engedélyezi a bejelentkezést, a virtuális gép portálon vagy parancssori felületen való létrehozásakor használja az SSH nyilvános kulcs lehetőséget.

## <a name="related-azure-components"></a>Kapcsolódó Azure-összetevők
## <a name="storage"></a>Tárolás
* [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)
* [Lemez hozzáadása Linux rendszerű virtuális géphez az azure-cli használatával](add-disk.md)
* [Adatlemez csatlakoztatása Linux rendszerű virtuális géphez az Azure Portalon](attach-disk-portal.md)

## <a name="networking"></a>Hálózat
* [A Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)
* [IP-címek az Azure-ban](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Portok nyitása egy Linux rendszerű virtuális géphez az Azure Portalon](nsg-quickstart.md)
* [Teljes tartománynév létrehozása az Azure Portalon](portal-create-fqdn.md)

## <a name="containers"></a>Tárolók
* [Virtuális gépek és tárolók az Azure-ban](containers.md)
* [Az Azure Container Service bemutatása](../../container-service/container-service-intro.md)
* [Az Azure Container Service-fürt üzembe helyezése](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>További lépések
A cikk a Linux gépek Azure-on való használatáról nyújtott áttekintést.  Következő lépésként hozzon létre néhány virtuális gépet!

* [Fedezze fel az egyre bővülő mintaszkriptlistát a gyakori feladatokhoz az Azure CLI-n keresztül](cli-samples.md)
