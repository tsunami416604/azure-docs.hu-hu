---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 9cbc48d8bca2f7491d0464be1c5bd64054927dc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77608740"
---
Az Azure virtuális gépek (VM-ek) létrehozása és kezelése egységes módon, nagy méretekben, valamilyen automatizálási forma általában kívánatos. Számos olyan eszköz és megoldás létezik, amelyek lehetővé teszik az Azure-infrastruktúra teljes üzembe helyezésének és felügyeleti életciklusának automatizálását. Ez a cikk bemutatja az Azure-ban használható infrastruktúra-automatizálási eszközöket. Ezek az eszközök általában illeszkednek az alábbi megközelítések egyikébe:

- Virtuális gépek konfigurációjának automatizálása
    - Az eszközök közé tartozik [az Ansible](#ansible), [a Chef](#chef)és a [Puppet](#puppet).
    - A virtuális gépek testreszabására jellemző eszközök közé tartozik a linuxos virtuális [gépekfelhő-init,](#cloud-init) a [PowerShell kívánt állapotkonfigurációja (DSC)](#powershell-dsc)és az [Azure Custom Script Extension](#azure-custom-script-extension) az összes Azure virtuális géphez.
 
- Infrastruktúra-kezelés automatizálása
    - Az eszközök közé tartozik a [Packer](#packer) az egyéni virtuálisgép-lemezképek automatizálásához, valamint a [Terraform](#terraform) az infrastruktúra-létrehozási folyamat automatizálásához.
    - [Az Azure Automation](#azure-automation) műveleteket hajthat végre az Azure-ban és a helyszíni infrastruktúrában.

- Az alkalmazások telepítésének és kézbesítésének automatizálása
    - Ilyen például az [Azure DevOps Services](#azure-devops-services) és a [Jenkins.](#jenkins)

## <a name="ansible"></a>Ansible
[Az Ansible](https://www.ansible.com/) egy automatizálási motor a konfigurációkezeléshez, a virtuális gép létrehozásához vagy az alkalmazások üzembe helyezéséhez. Az Ansible egy ügynök nélküli modellt használ, általában SSH-kulcsokkal a célgépek hitelesítéséhez és kezeléséhez. A konfigurációs feladatok a forgatókönyvekben vannak definiálva, és számos Ansible modul áll rendelkezésre bizonyos feladatok elvégzéséhez. További információ: [Hogyan működik az Ansible.](https://www.ansible.com/how-ansible-works)

Az alábbiak végrehajtásának módját ismerheti meg:

- [Telepítse és konfigurálja az Ansible-t Linuxon az Azure-ral való használatra.](../articles/ansible/ansible-install-configure.md)
- [Hozzon létre egy Linux virtuális gép](../articles/ansible/ansible-create-vm.md).
- [Linuxos virtuális gép kezelése](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[A Chef](https://www.chef.io/) egy automatizálási platform, amely segít meghatározni, hogyan van konfigurálva, üzembe helyezve és kezelve az infrastruktúra. További összetevők közé tartozott a Chef Habitat az alkalmazások életciklusának automatizálásához az infrastruktúra helyett, valamint a Chef InSpec, amely segít a biztonsági és házirend-követelményeknek való megfelelés automatizálásában. A chef-ügyfelek a célgépekre vannak telepítve, egy vagy több központi chef-kiszolgálóval, amelyek a konfigurációkat tárolják és kezelik. További információt a Chef áttekintése című [témakörben talál.](https://docs.chef.io/chef_overview.html)

Az alábbiak végrehajtásának módját ismerheti meg:

- [A Chef Automate üzembe helyezése az Azure Piactérről.](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)
- [Telepítse a Chef alkalmazást Windowsra, és hozzon létre Azure-virtuális gépeket.](../articles/chef/chef-automation.md)


## <a name="puppet"></a>Puppet
[A Puppet](https://www.puppet.com) egy nagyvállalati automatizálási platform, amely kezeli az alkalmazások kézbesítési és telepítési folyamatát. Az ügynökök a célgépeken vannak telepítve, hogy a Puppet Master olyan jegyzékeket futtathasson, amelyek meghatározzák az Azure-infrastruktúra és a virtuális gépek kívánt konfigurációját. A Puppet más megoldásokkal, például a Jenkinsszel és a GitHubbal is integrálható a továbbfejlesztett devops-munkafolyamat érdekében. További információ: [How Puppet works](https://puppet.com/products/how-puppet-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- [A Puppet üzembe helyezése az Azure Piactérről.](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview)


## <a name="cloud-init"></a>Cloud-init
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítási folyamat során van meghívva, nincsenek további lépések vagy szükséges ügynökök a konfiguráció alkalmazásához.  A `#cloud-config` fájlok megfelelő formázásáról a [cloud-init dokumentációs webhelyén olvashat bővebben.](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)  `#cloud-config`fájlok szöveges fájlok kódolt base64.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

Aktívan együttműködünk a jóváhagyott Linux disztribúciós partnereinkkel annak érdekében, hogy felhőalapú init-kompatibilis lemezképek álljanak rendelkezésre az Azure piactéren. Ezek a lemezképek teszik a felhő-init központi telepítések és konfigurációk zökkenőmentesen működnek a virtuális gépek és a virtuális gép méretezési készletek. További részletek az Azure-beli felhőalapú szolgáltatásokról:

- [Az Azure-ban található Linux-virtuális gépek felhőalapú init támogatása](../articles/virtual-machines/linux/using-cloud-init.md)
- [Próbálkozzon egy oktatóanyaggal az automatikus virtuális gép konfigurációjával a cloud-init használatával.](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md)


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell kívánt állapotkonfiguráció (DSC)](/powershell/scripting/dsc/overview/overview) egy felügyeleti platform a célgépek konfigurációjának meghatározásához. A DSC Linuxon is használható az [Open Management Infrastructure (OMI) kiszolgálón](https://collaboration.opengroup.org/omi/)keresztül.

A DSC-konfigurációk határozzák meg, hogy mit telepítsenek a számítógépre, és hogyan konfigurálják az állomást. A helyi configuration manager (LCM) motor fut minden célcsomóponton, amely a kért műveleteket a leadott konfigurációk alapján dolgozza fel. A lekéréses kiszolgáló olyan webszolgáltatás, amely egy központi állomáson fut a DSC-konfigurációk és a kapcsolódó erőforrások tárolására. A lekéréses kiszolgáló minden célállomáslcm-motorjával kommunikál, hogy biztosítsa a szükséges konfigurációkat, és jelentést nyújtson a megfelelőségről.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy egyszerű DSC-konfigurációt.](/powershell/scripting/dsc/quickstarts/website-quickstart)
- [DSC lekéréses kiszolgáló konfigurálása](/powershell/scripting/dsc/pull-server/pullserver).
- [Használja a DSC-t Linuxra.](/powershell/scripting/dsc/getting-started/lnxgettingstarted)


## <a name="azure-custom-script-extension"></a>Azure egyéni szkriptek futtatására szolgáló bővítmény
Az Azure Custom Script Extension for [Linux](../articles/virtual-machines/linux/extensions-customscript.md) vagy [Windows](../articles/virtual-machines/windows/extensions-customscript.md) letölti és végrehajtja a parancsfájlokat az Azure virtuális gépeken. Használhatja a bővítményt, amikor virtuális gép létrehozása, vagy bármikor a virtuális gép használata után. 

A parancsfájlok letölthetők az Azure storage-ból vagy bármely nyilvános helyről, például egy GitHub-tárházból. Az egyéni parancsfájl-bővítmény, parancsfájlok bármilyen nyelven, amely fut a forrás virtuális gépen. Ezek a parancsfájlok alkalmazások telepítésére vagy a virtuális gép igény szerint konfigurálására használhatók. A hitelesítő adatok védelme érdekében a bizalmas adatok, például a jelszavak védett konfigurációban tárolhatók. Ezeket a hitelesítő adatokat csak a virtuális gépen belül fejti vissza.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy Linux virtuális gép az Azure CLI és használja az egyéni parancsfájl-bővítményt.](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json)
- [Hozzon létre egy Windows virtuális gép az Azure PowerShell és használja az egyéni parancsfájl-bővítményt.](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json)


## <a name="packer"></a>Csomagoló
[A Packer](https://www.packer.io) automatizálja a létrehozási folyamatot, amikor egyéni virtuálisgép-lemezképet hoz létre az Azure-ban. Packer segítségével határozza meg az operációs rendszer és a konfiguráció utáni parancsfájlok, amelyek testre szabják a virtuális gép az önspecifikus igényeknek. Konfigurálás után a virtuális gép ezután felügyelt lemezlemez-lemezlemezként lesz rögzítve. Packer automatizálja a folyamatot, hogy a forrás virtuális gép, a hálózati és tárolási erőforrások, konfigurációs parancsfájlok futtatása, majd létrehozza a virtuális gép lemezképet.

Az alábbiak végrehajtásának módját ismerheti meg:

- [A Packer segítségével linuxos virtuálisgép-lemezképet hozhat létre az Azure-ban.](../articles/virtual-machines/linux/build-image-with-packer.md)
- [A Packer segítségével hozzon létre egy Windows virtuálisgép-lemezképet az Azure-ban.](../articles/virtual-machines/windows/build-image-with-packer.md)


## <a name="terraform"></a>Terraform
[A Terraform](https://www.terraform.io) egy automatizálási eszköz, amely lehetővé teszi egy teljes Azure-infrastruktúra definiálását és létrehozását egyetlen sablonformátum-nyelvvel – a HashiCorp konfigurációs nyelvvel (HCL). A Terraform segítségével olyan sablonokat határozhat meg, amelyek automatizálják a hálózati, tárolási és virtuálisgép-erőforrások létrehozásának folyamatát egy adott alkalmazásmegoldáshoz. Használhatja a meglévő Terraform sablonok más platformokon az Azure-ral a konzisztencia biztosításához és egyszerűsítheti az infrastruktúra üzembe helyezését anélkül, hogy egy Azure Resource Manager sablont kell konvertálni.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Telepítse és konfigurálja a Terraformot az Azure-ral.](../articles/terraform/terraform-install-configure.md)
- [Hozzon létre egy Azure-infrastruktúrát a Terraform segítségével.](../articles/terraform/terraform-create-complete-vm.md)


## <a name="azure-automation"></a>Azure Automation
[Az Azure Automation](https://azure.microsoft.com/services/automation/) runbookok használatával dolgozza fel a megcélzott virtuális gépeken lévő feladatokat. Az Azure Automation a meglévő virtuális gépek kezelésére szolgál, nem pedig egy infrastruktúra létrehozására. Az Azure Automation linuxos és Windows-alapú virtuális gépeken, valamint a helyszíni virtuális vagy fizikai gépeken is futtatható egy hibrid runbook-feldolgozóval. A Runbookok tárolhatók egy forrásvezérlő tárházban, például a GitHubon. Ezek a runbookok ezután manuálisan vagy egy meghatározott ütemezés szerint futtathatók.

Az Azure Automation egy kívánt állapotkonfigurációs (DSC) szolgáltatást is biztosít, amely lehetővé teszi, hogy definíciókat hozzon létre egy adott virtuális gépkészlet konfigurálásának módjára. A DSC ezután biztosítja, hogy a szükséges konfiguráció alkalmazása és a virtuális gép konzisztens maradjon. Az Azure Automation DSC Windows és Linux gépeken is fut.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy PowerShell-runbookot.](../articles/automation/automation-first-runbook-textual-powershell.md)
- [A hibrid Runbook-feldolgozó segítségével kezelheti a helyszíni erőforrásokat.](../articles/automation/automation-hybrid-runbook-worker.md)
- [Használja az Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md)szolgáltatást.


## <a name="azure-devops-services"></a>Azure DevOps Services
[Az Azure DevOps-szolgáltatások](https://www.visualstudio.com/team-services/) olyan eszközök gyűjteménye, amelyek segítségével megoszthatja és nyomon követheti a kódot, automatikus buildeket használhat, és teljes körű folyamatos integrációs és fejlesztési (CI/CD) folyamatot hozhat létre. Az Azure DevOps Services integrálható a Visual Studio és más szerkesztők, hogy egyszerűsítse a használatot. Az Azure DevOps Services azure-beli virtuális gépeket is létrehozhat és konfigurálhat, majd kódot helyezhet üzembe rájuk.

További információk:

- [Az Azure DevOps-szolgáltatások](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
A [Jenkins](https://www.jenkins.io) egy folyamatos integrációs kiszolgáló, amely segít az alkalmazások telepítésében és tesztelésében, valamint automatikus folyamatok létrehozásában a kódkézbesítéshez. Több száz dugó kiterjeszteni az alapvető Jenkins platform, és akkor is integrálható sok más termékek és megoldások révén webhooks. Manuálisan telepítheti a Jenkinst egy Azure-beli virtuális gépre, futtathatja a Jenkinst egy Docker-tárolóból, vagy használhat egy előre elkészített Azure Marketplace-rendszerképet.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy fejlesztési infrastruktúrát egy Linux virtuális gépen az Azure-ban a Jenkins, a GitHub és a Docker segítségével.](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md)


## <a name="next-steps"></a>További lépések
Az Azure-ban számos különböző lehetőség van az infrastruktúra-automatizálási eszközök használatára. Szabadon használhatja az igényeinek és környezetének leginkább megfelelő megoldást. Első lépések, és próbálja meg az Azure-ba beépített eszközök némelyikét, olvassa el, hogyan automatizálhatja a [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) vagy a [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) virtuális gép testreszabását.
