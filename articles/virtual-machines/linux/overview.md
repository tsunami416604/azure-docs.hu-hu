---
title: A Linux rendszerű virtuális gépek áttekintése az Azure-ban
description: Az Azure-beli Linux rendszerű virtuális gépek áttekintése.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a62cb11e862bb8733e2758297d82aa7732b2d739
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87082464"
---
# <a name="linux-virtual-machines-in-azure"></a>Linux rendszerű virtuális gépek az Azure-ban

Az Azure Virtual Machines (VM) az Azure által kínált különböző típusú [, igény szerinti, méretezhető számítási erőforrások](/azure/architecture/guide/technology-choices/compute-decision-tree) egyike. Virtuális gépet általában akkor érdemes választani, ha a számítási környezet átfogóbb vezérlésére van szüksége annál, amelyet az egyéb lehetőségek kínálnak. Ez a cikk bemutatja, hogy mit kell szem előtt tartania egy virtuális gép létrehozása előtt, valamint hogy hogyan hozhatja létre és kezelheti azt.

Az Azure VM a virtualizálás rugalmasságát biztosítja anélkül, hogy a virtuális gépet futtató fizikai hardvereket kellene vásárolnia és karbantartania. A virtuális gép karbantartásához azonban így is szükséges elvégeznie néhány feladatot, például a virtuális gépen futó szoftver konfigurálását, javítását és telepítését.

Az Azure virtuális gépek különféle módon használhatóak. Néhány példa:

* **Fejlesztés és tesztelés** – Az Azure virtuális gépek gyors és egyszerű módot biztosítanak az alkalmazások programozásához és teszteléséhez szükséges adott konfigurációjú számítógépek létrehozásához.
* **Felhőbeli alkalmazások** – Mivel az alkalmazások iránti igény ingadozhaz, gazgaságosabb lehet őket egy virtuális gépen futtatni az Azure szolgáltatásban. A további virtuális gépekért csak akkor kell fizetnie, amikor szüksége van rájuk, amikor pedig nincs, akkor leállíthatja őket.
* **Bővített adatközpont** – Az Azure virtuális hálózatokon futó virtuális gépek könnyedén összekapcsolhatók a szervezet hálózatához.

Az alkalmazás által használt virtuális gépek száma az Ön igényei szerint skálázható.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Mit kell átgondolnom egy virtuális gép létrehozása előtt?
Az Azure-ban futó alkalmazás-infrastruktúrák kiépítésekor mindig számos [kialakítási szempontot](/azure/architecture/reference-architectures/n-tier/windows-vm) kell figyelembe venni. A kezdés előtt a virtuális gépek következő tulajdonságait fontos átgondolni:

* Az alkalmazás erőforrásainak nevei
* Az erőforrások tárolásának helye
* A virtuális gép mérete
* A létrehozható virtuális gépek maximális száma
* A virtuális gépen futó operációs rendszer
* A virtuális gép indítás utáni konfigurációja
* A virtuális gép által igényelt kapcsolódó erőforrások

### <a name="locations"></a>Helyek
Az Azure-ban létrehozott minden erőforrás világszerte több [földrajzi régió](https://azure.microsoft.com/regions/) között oszlik meg. A virtuális gépek létrehozásakor a régiót általában **helynek** nevezik. A virtuális gépek esetében a hely adja meg, hogy a virtuális merevlemezek hol tárolódnak.

Az alábbi táblázatban az elérhető helyek listájának megismeréséhez olvasható néhány módszer.

| Metódus | Leírás |
| --- | --- |
| Azure Portal |Egy virtuális gép létrehozásakor válasszon egy helyet a listából. |
| Azure PowerShell |Használja a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsot. |
| REST API |Használja a [Helyek listázása](/rest/api/resources/subscriptions) műveletet. |
| Azure CLI |Használja az [az account list-locations](/cli/azure/account?view=azure-cli-latest) műveletet. |

## <a name="availability"></a>Rendelkezésre állás
Az Azure bejelentett egy iparágvezető, 99,9%-os elérhetőséget biztosító egypéldányos virtuálisgép-szolgáltatói szerződést, amelynek az a feltétele, hogy az üzembe helyezett virtuális gép összes lemezén prémium szintű tárolást használjon.  Ahhoz, hogy az üzembe helyezett példány megfeleljen a standard 99,95%-os virtuálisgép-szolgáltatói szerződésnek, legalább még két virtuális gépet kell üzembe helyeznie a számítási feladatok futtatásához egy rendelkezésre állási csoporton belül. A rendelkezésre állási csoport biztosítja, hogy a virtuális gépek több tartalék tartomány között legyenek elosztva az Azure-adatközpontokban, valamint az őket futtató gazdagépeknek különböző karbantartási időszakaik legyenek. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

## <a name="vm-size"></a>Virtuális gép mérete
A használt virtuális gép [mérete](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a futtatni kívánt számítási feladatok mennyiségétől függ. A választott méret határoz meg olyan tényezőket, mint a feldolgozókapacitás, a memória és a tárolókapacitás. Az Azure számos különböző méretet kínál különféle felhasználási módokhoz.

Az Azure [óradíjat](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) számít fel a virtuális gép méretétől és az operációs rendszertől függően. Nem egész órák esetében az Azure csak a használt perceket számlázza. A tárhely árazása és felszámítása külön történik.

## <a name="vm-limits"></a>A virtuális gépekre korlátai
Minden előfizetésre alapértelmezett [kvótakorlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak, amelyek akadályt jelenthetnek, ha a projektjéhez nagy számú virtuális gépet szeretne üzembe helyezni. A jelenlegi határérték minden előfizetés esetében régiónként 20 virtuális gép. A határértékek megemelhetők [egy emelést kérvényező támogatási jegy benyújtásával](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Managed Disks

A felügyelt lemezek a háttérben végzik az Azure Storage-fiók létrehozását és kezelését, hogy Önnek ne kelljen foglalkoznia a tárfiók skálázási korlátaival. Adja meg a lemez méretét és teljesítményszintjét (Standard vagy Prémium), és az Azure létrehozza és felügyeli a lemezt. Nem kell a használt tárterület miatt aggódnia, amikor lemezeket ad hozzá, vagy fel-/leskálázza a virtuális gépet. Ha új virtuális gépeket hoz létre, [használja az Azure CLI-t](quick-create-cli.md) vagy az Azure Portalt felügyelt operációs rendszerrel és adatlemezekkel rendelkező virtuális gépek létrehozásához. Ha a virtuális gépei nem felügyelt lemezeket tartalmaznak, [átalakíthatja a virtuális gépeket, hogy felügyelt lemezek támogassák őket](convert-unmanaged-to-managed-disks.md).

Az egyéni rendszerképeit Azure-régiónként egyetlen tárfiókban is felügyelheti, és a használatukkal több száz virtuális gépet hozhat létre egy adott előfizetésben. A felügyelt lemezekkel kapcsolatos további információért tekintse meg [a felügyelt lemezek áttekintésével kapcsolatos cikket](../linux/managed-disks-overview.md).

## <a name="distributions"></a>Disztribúciók 
A Microsoft Azure több, különböző partnerek által biztosított és kezelt, népszerű Linux-disztribúció futtatását is támogatja.  Az Azure Marketplace-en olyan disztribúciókat találhat, mint a Red Hat Enterprise, a CentOS, a SUSE Linux Enterprise, a Debian, az Ubuntu, a CoreOS, a RancherOS, a FreeBSD és még sok más. A Microsoft folyamatosan együttműködink a különböző-Linux közösségekkel, hogy még tovább bővíthesse az [Azure által támogatott Linux-disztribúciók](endorsed-distros.md) listáját.

Ha a kívánt Linux-disztribúció jelenleg nem szerepel a katalógusban, „Saját Linux használata” típusú virtuális gépet is használhat. Ehhez [hozzon létre egy Linux VHD-t, és töltse fel az Azure-ba](create-upload-generic.md).

A Microsoft szorosan együttműködik a partnereivel, hogy az elérhető rendszerképek biztosan frissítve és optimalizálva legyenek az Azure futtatókörnyezetre.  További információk az Azure-partnerekről az alábbi hivatkozásokon találhatók:

* Linux az Azure-on – [Támogatott disztribúciók](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?search=suse%20sles&page=1)
* Red Hat – [Azure Marketplace – Red Hat Enterprise Linux 8,1](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux81-ARM)
* Canonical – [Azure Marketplace – Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian – [Azure Marketplace – Debian 8 „Jessie”](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.debian)
* FreeBSD – [Azure Marketplace – freebsd 10,4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS – [Azure Marketplace – Container Linux by CoreOS](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS)
* RancherOS – [Azure Marketplace – RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami – [Azure-hoz készült Bitnami Library](https://azure.bitnami.com/)
* Mesosphere – [Azure Marketplace – Mesosphere DC/OS az Azure-on](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker – [Azure Marketplace – Docker-rendszerképek](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins – [Azure Marketplace – CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

A megfelelő DevOps-kultúra megteremtéséhez minden infrastruktúrának kódoltnak kell lennie.  Ha az összes infrastruktúra kód életbe kerül, akkor könnyen létrehozható.  Az Azure az összes fontosabb automatizáló eszközt használja, köztük az Ansible, a Chef, a SaltStack és a Puppet eszközöket.  Az Azure továbbá saját automatizáló eszközökkel is rendelkezik:

* [Azure-sablonok](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](../extensions/vmaccess.md)

Az Azure támogatja a [Cloud-init](https://cloud-init.io/) használatát a legtöbb Linux-disztribúcióban, amely támogatja azt.  Aktívan dolgozunk a támogatott linuxos disztribúciós partnereinkkel, hogy elérhetők legyenek a Cloud-init-lemezképek az Azure piactéren. Ezek a lemezképek a felhő-init üzembe helyezések és konfigurációk zökkenőmentesen működnek a virtuális gépekkel és a virtuálisgép-méretezési csoportokkal.

* [A cloud-init használata Azure-beli Linux rendszerű virtuális gépeken](using-cloud-init.md)

## <a name="storage"></a>Tárolás
* [A Microsoft Azure Storage bemutatása](../../storage/common/storage-introduction.md)
* [Lemez hozzáadása Linux rendszerű virtuális géphez az azure-cli használatával](add-disk.md)
* [Adatlemez csatlakoztatása Linux rendszerű virtuális géphez az Azure Portalon](attach-disk-portal.md)

## <a name="networking"></a>Hálózatkezelés
* [A Virtual Network áttekintése](../../virtual-network/virtual-networks-overview.md)
* [IP-címek az Azure-ban](../../virtual-network/public-ip-addresses.md)
* [Portok nyitása egy Linux rendszerű virtuális géphez az Azure Portalon](nsg-quickstart.md)
* [Teljes tartománynév létrehozása az Azure Portalon](portal-create-fqdn.md)


## <a name="next-steps"></a>További lépések

Hozza létre az első virtuális gépet!

- [Portál](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
