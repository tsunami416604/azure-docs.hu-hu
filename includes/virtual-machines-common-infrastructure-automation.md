---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: efca5c4155372456100126671e7d5cd1ccf0cbbc
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53027909"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Az Azure-beli virtuális gépek infrastruktúra automatizálási eszközök használata
Hozzon létre, és kezeli az Azure-beli virtuális gépek (VM) egységes módon végrehajtott, ipari méretekben, valamilyen automatizált általában van szükség. Nincsenek számos eszközöket és megoldásokat, amelyek lehetővé teszik a teljes Azure-infrastruktúra üzembe helyezés és felügyelet életciklusának automatizálásához. Ez a cikk egy részét az infrastruktúra automatizálási eszközöket, amelyek is használhatja az Azure-ban mutatja be. Ezek az eszközök általában pontosan a következő módszerek egyikét:

- A virtuális gépek konfigurálásának automatizálása
    - Eszközök közé tartozik a [Ansible](#ansible), [Chef](#chef), és [Puppet](#puppet).
    - Adott virtuális gép testreszabása az eszközök között található [a cloud-init](#cloud-init) Linux rendszerű virtuális gépekhez, [PowerShell Desired State Configuration (DSC)](#powershell-dsc), és a [Azure egyéni szkriptek futtatására szolgáló bővítmény](#azure-custom-script-extension) összes Azure virtuális gépek.
 
- Infrastruktúra-kezelés automatizálása
    - Eszközök közé tartozik a [Packer](#packer) automatizálhatja az egyéni virtuális gép rendszerkép létrejött, és [Terraform](#terraform) automatizálhatja az infrastruktúra folyamatot hozhat létre.
    - [Az Azure Automation](#azure-automation) műveleteket hajthat végre az Azure-ban és a helyszíni infrastruktúra között.

- Alkalmazás központi telepítése és a továbbítás automatizálása
    - Ilyenek például [Azure DevOps-szolgáltatásokkal](#azure-devops-services) és [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Az Ansible](https://www.ansible.com/) automatizálási motor konfigurációkezelés, a virtuális gép létrehozása és alkalmazások központi telepítése. Az Ansible ügynök nélküli modellt alkalmaz, általában az SSH-kulcsokkal hitelesítheti és kezelheti a célgéphez való. Forgatókönyvek, az Ansible modulok meghatározott feladatok végrehajtásához rendelkezésre álló számos konfigurációs feladatok vannak definiálva. További információkért lásd: [hogyan Ansible működik](https://www.ansible.com/how-ansible-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- [Ansible telepítése és konfigurálása Linux rendszeren használható az Azure-ral](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Hozzon létre egy Linux rendszerű virtuális gép](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Linux rendszerű virtuális gép kezelése](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) van egy automatizálási platform, amely segít a meghatározása, hogy az infrastruktúra úgy van beállítva, telepítheti és kezelheti. További összetevőket tartalmaz a Chef Habitat az infrastruktúra helyett az alkalmazás életciklusának menedzsment, és a Chef InSpec, amelynek segítségével automatizálhatja biztonsági és a házirend követelményeinek való megfelelés. Chef az ügyfelek egy vagy több központi Chef-kiszolgálókkal, tárolhatja és kezelheti a konfigurációkat a célszámítógépekre telepíti. További információkért lásd: [Chef egy áttekintés](https://docs.chef.io/chef_overview.html).

Az alábbiak végrehajtásának módját ismerheti meg:

- [Chef telepítése az Azure Marketplace-ről automatizálása](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Chef telepítése Windows és az Azure virtuális gépek létrehozása](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) nagyvállalati használatra kész automatizálási platform, amely a szállítási és üzembe helyezési folyamatának kezeli. Ügynök telepítve van a célszámítógépen futtatni, amelyek meghatározzák a kívánt konfigurációk az Azure-infrastruktúra jegyzékek Puppet Master és a virtuális gépek. Puppet integrálható más megoldásokkal, például a Jenkins és a GitHub nagyobb fejlesztési és üzemeltetési munkafolyamathoz. További információkért lásd: [hogyan Puppet működik](https://puppet.com/product/how-puppet-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- [Üzembe helyezése az Azure Marketplace-ről Puppet](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. A cloud-init nevezzük az első rendszerindítás során, mert nincsenek további lépéseket vagy szükséges ügynökök a alkalmazni a konfigurációt.  További információt a megfelelő formázása a `#cloud-config` fájlokat, tekintse meg a [a cloud-init dokumentációs oldalának](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` fájlok Base64 kódolású szöveges fájlok.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

 Aktívan dolgozunk a támogatott Linux disztribúció partnereink ahhoz, hogy engedélyezve van a cloud-init lemezkép érhető el az Azure Marketplace-en. Ezek a lemezképek, győződjön meg a cloud-init-környezetekben, és konfigurációkat és a virtuális gépek és a virtual machine scale sets zökkenőmentesen működjön. Az alábbi táblázat ismerteti az aktuális engedélyezve van a cloud-init lemezképek rendelkezésre állás az Azure platformon:

| Közzétevő | Ajánlat | SKU | Verzió | a cloud-init kész
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |legújabb |igen | 
|Canonical |UbuntuServer |14.04.5-LTS |legújabb |igen |
|CoreOS |CoreOS |Stable |legújabb |igen |
|OpenLogic |CentOS |7-CI |legújabb |előzetes verzió |
|RedHat |RHEL |7-RAW-CI |legújabb |előzetes verzió |

Ismerje meg, az Azure cloud-Init használatával kapcsolatos további részletek:

- [A cloud-init támogatja a Linux rendszerű virtuális gépek az Azure-ban](../articles/virtual-machines/linux/using-cloud-init.md)
- [Próbálja ki az automatikus Virtuálisgép-konfiguráció, a cloud-init használatával oktatóanyag](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) egy felügyeleti platform cél gépek konfigurációjának meghatározása. DSC linuxon keresztül is használható a [Open Management Infrastructure (OMI) kiszolgáló](https://collaboration.opengroup.org/omi/).

DSC-konfigurációk határozza meg, mi a telepíti egy számítógépre, és a gazdagép konfigurálása. A helyi Configuration Manager (LCM) Konfigurálása motor dolgozza fel a kért műveletek leküldött beállításai minden célként megadott csomóponton fut. Egy lekéréses kiszolgálót egy webszolgáltatás, amelyet a DSC-konfigurációk és a kapcsolódó erőforrások tárolására egy központi gazdagépen futtatja. A lekéréses kiszolgálón kommunikál a LCM motor minden cél gazdagépre, adja meg a szükséges konfigurációk és megfelelőségi jelentést.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy egyszerű DSC-konfiguráció](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Konfiguráljon egy DSC lekéréses kiszolgálót](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Linuxhoz készült DSC használata](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Az Azure egyéni szkriptek futtatására szolgáló bővítmény
Az Azure egyéni szkriptek bővítménye az [Linux](../articles/virtual-machines/linux/extensions-customscript.md) vagy [Windows](../articles/virtual-machines/windows/extensions-customscript.md) letölti és végrehajtja a parancsfájlok az Azure virtuális gépekhez. A bővítmény is használhatja, ha létrehoz egy virtuális Gépet, vagy a virtuális gép után bármikor használatban van. 

Parancsfájlok letölthető az Azure storage vagy bármely nyilvános helyre, például egy GitHub-adattárból. Az egyéni Szkriptbővítménnyel és parancsfájlokat írhat bármilyen nyelven, amely a forrás virtuális Gépet futtat. Ezek a parancsfájlok segítségével telepíthet alkalmazásokat, és konfigurálja a virtuális Gépet igény szerint. Hitelesítő adatok biztonságos, bizalmas adatokat, például jelszavak védett konfigurációban is tárolhatók. Ezek a hitelesítő adatok csak a virtuális gép lesznek visszafejtve.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Linux rendszerű virtuális gép létrehozása az Azure CLI-vel és az egyéni szkriptbővítménnyel](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Windows virtuális gép létrehozása az Azure PowerShell-lel és az egyéni szkriptbővítménnyel](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Csomagoló
[Csomagoló](https://www.packer.io) automatizálja a buildelési folyamat egy egyéni Virtuálisgép-rendszerképet az Azure-ban való létrehozásakor. Packer használatával határozza meg az operációs rendszer, és futtassa a konfiguráció utáni parancsfájlok, amelyek testre szabhatja a virtuális gép saját igényeinek. A beállítása után a virtuális gép, egy felügyelt lemez rendszerképének majd rögzített. Csomagolói automatizálja a folyamatot a forrás virtuális gép, hálózati és tárolási erőforrások létrehozása, és konfigurációs parancsfájlok futtatása, majd hozza létre a Virtuálisgép-lemezkép.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Linux Virtuálisgép-rendszerkép létrehozása az Azure-ban a Packer használatával](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Windows Virtuálisgép-rendszerkép létrehozása az Azure-ban a Packer használatával](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[A Terraform](https://www.terraform.io) egy automation-eszköz, amely lehetővé teszi, hogy adja meg, és hozzon létre egy teljes Azure-infrastruktúra egyetlen sablon formátuma nyelv – a HashiCorp konfigurációs nyelv (HCL). A terraform használatával hálózati, tárolási és a egy adott megoldás a Virtuálisgép-erőforrások létrehozásának folyamata automatizáló sablonok határozza meg. Használhatja a meglévő Terraform-sablonok az Azure-ral más platformok biztosítani a konzisztenciát és az infrastruktúra üzembe helyezés egyszerűsítéséhez anélkül, hogy az Azure Resource Manager-sablon konvertálása.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Terraform telepítése és konfigurálása az Azure-ral](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Hozzon létre egy Azure-infrastruktúra a terraform használatával](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Az Azure Automation](https://azure.microsoft.com/services/automation/) forgatókönyveket használ a megcélzott virtuális gépeken feladatokhoz feldolgozni. Az Azure Automation a meglévő virtuális gépek kezelése helyett infrastruktúrák kialakítására szolgáló szolgál. Az Azure Automation is Linux és Windows virtuális gépek, valamint a helyi virtuális vagy fizikai gépeken a hibrid runbook-feldolgozók között futtatható. Runbookok verziókövetési adattár, például a GitHub is tárolható. Ezek a runbookok majd futtathatja manuálisan vagy egy meghatározott ütemezés szerint.

Az Azure Automation Desired State Configuration (DSC) szolgáltatás lehetővé teszi, hogy hogyan kell konfigurálni a virtuális gépek adott halmazát az-meghatározások létrehozása emellett. DSC majd biztosítja, hogy a alkalmazni a szükséges konfigurációval, és a virtuális gép konzisztens marad. Az Azure Automation DSC Windows- és Linux gép futtat.

Az alábbiak végrehajtásának módját ismerheti meg:

- [PowerShell-runbook létrehozásával](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Hibrid Runbook-feldolgozó használata kezelheti a helyszíni erőforrásokhoz](../articles/automation/automation-hybrid-runbook-worker.md).
- [Az Azure Automation DSC használata](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Az Azure DevOps-szolgáltatásokkal](https://www.visualstudio.com/team-services/) olyan eszközöket tartalmazza, amelyek segítséget nyújtanak a megosztások és -kód, használja az automatizált buildekig és hozzon létre egy teljes folyamatos integrációs és fejlesztési (CI/CD) folyamatot nyomon követése. Az Azure DevOps-szolgáltatásokkal integrálja a Visual Studio és az egyéb szerkesztők használatának leegyszerűsítése érdekében. Az Azure DevOps-szolgáltatásokkal is létrehozhat és Azure virtuális gépek konfigurálása és őket, majd kód üzembe helyezése.

További információk:

- [Az Azure DevOps-szolgáltatásokkal](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[A Jenkins](https://www.jenkins.io) segíti üzembe helyezése és tesztelheti az alkalmazásokat, és a kód kézbesítésre automatizált folyamatokat hozhat létre egy folyamatos integrációs kiszolgáló. Beépülő modulok, a core Jenkins platform bővítése érdekében több száz, és számos más termékek és megoldások webhookok használatával is integrálhatja. Manuálisan telepítse a Jenkins-beli virtuális gépen, futtassa a Jenkins Docker-tárolókon belül vagy előregyártott Azure Marketplace-rendszerkép használata.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Fejlesztési infrastruktúra létrehozása egy Linux rendszerű virtuális gépen az Azure-ban a Jenkins, GitHub és Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>További lépések
Az infrastruktúra automatizálási eszközök használata az Azure-ban számos különböző lehetőség van. A megoldás, amely legjobban megfelel az igényeinek megfelelően, és a környezet végezheti rendelkezik. Az első lépéseket, és próbálja meg az egyes Azure-bA beépített eszközök, megtudhatja, hogyan automatizálhatja testreszabása egy [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) virtuális Gépet.
