---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 4259868ff3b3c9ca9f9818532acd7e865e0300d7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77205722"
---
Hozzon létre, és kezeli az Azure-beli virtuális gépek (VM) egységes módon végrehajtott, ipari méretekben, valamilyen automatizált általában van szükség. Nincsenek számos eszközöket és megoldásokat, amelyek lehetővé teszik a teljes Azure-infrastruktúra üzembe helyezés és felügyelet életciklusának automatizálásához. Ez a cikk egy részét az infrastruktúra automatizálási eszközöket, amelyek is használhatja az Azure-ban mutatja be. Ezek az eszközök általában pontosan a következő módszerek egyikét:

- A virtuális gépek konfigurálásának automatizálása
    - Az eszközök közé tartozik a [Ansible](#ansible), a [Chef](#chef)és a [Puppet](#puppet).
    - A virtuális gépek testreszabására szolgáló eszközök közé tartozik a [Cloud-init](#cloud-init) a linuxos virtuális gépekhez, a [PowerShell kívánt állapotának konfigurálása (DSC)](#powershell-dsc)és az [Egyéni Azure-szkriptek bővítménye](#azure-custom-script-extension) az összes Azure-beli virtuális géphez.
 
- Infrastruktúra-kezelés automatizálása
    - Az eszközök tartalmazzák a [csomagolót](#packer) az egyéni virtuálisgép-rendszerkép-buildek automatizálására és az infrastruktúra- [Terraform](#terraform) automatizálására.
    - A [Azure Automation](#azure-automation) az Azure-ban és a helyszíni infrastruktúrában is végezhet műveleteket.

- Alkalmazás központi telepítése és a továbbítás automatizálása
    - Ilyenek például az [Azure DevOps Services](#azure-devops-services) és a [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
A [Ansible](https://www.ansible.com/) egy automatizálási motor a konfiguráció felügyeletéhez, a virtuális gépek létrehozásához vagy az alkalmazások telepítéséhez. Az Ansible ügynök nélküli modellt alkalmaz, általában az SSH-kulcsokkal hitelesítheti és kezelheti a célgéphez való. Forgatókönyvek, az Ansible modulok meghatározott feladatok végrehajtásához rendelkezésre álló számos konfigurációs feladatok vannak definiálva. További információ: [how Ansible Works](https://www.ansible.com/how-ansible-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- A [Ansible telepítése és konfigurálása Linux rendszeren az Azure](../articles/ansible/ansible-install-configure.md)-hoz való használatra.
- [Hozzon létre egy linuxos virtuális gépet](../articles/ansible/ansible-create-vm.md).
- [Linux rendszerű virtuális gép kezelése](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
A [Chef](https://www.chef.io/) egy automatizálási platform, amely segít meghatározni az infrastruktúra konfigurálását, üzembe helyezését és kezelését. További összetevőket tartalmaz a Chef Habitat az infrastruktúra helyett az alkalmazás életciklusának menedzsment, és a Chef InSpec, amelynek segítségével automatizálhatja biztonsági és a házirend követelményeinek való megfelelés. Chef az ügyfelek egy vagy több központi Chef-kiszolgálókkal, tárolhatja és kezelheti a konfigurációkat a célszámítógépekre telepíti. További információ: [Chef áttekintése](https://docs.chef.io/chef_overview.html).

Az alábbiak végrehajtásának módját ismerheti meg:

- [A Chef automatizálásának üzembe helyezése az Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Telepítse a cheft Windows rendszeren, és hozzon létre Azure virtuális gépeket](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
A [Puppet](https://www.puppet.com) egy nagyvállalati használatra kész Automation platform, amely kezeli az alkalmazások kézbesítési és telepítési folyamatát. Ügynök telepítve van a célszámítógépen futtatni, amelyek meghatározzák a kívánt konfigurációk az Azure-infrastruktúra jegyzékek Puppet Master és a virtuális gépek. Puppet integrálható más megoldásokkal, például a Jenkins és a GitHub nagyobb fejlesztési és üzemeltetési munkafolyamathoz. További információkért lásd: a [Puppet működése](https://puppet.com/products/how-puppet-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- [A báb üzembe helyezése az Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. A cloud-init nevezzük az első rendszerindítás során, mert nincsenek további lépéseket vagy szükséges ügynökök a alkalmazni a konfigurációt.  A `#cloud-config` fájlok megfelelő formázásáról a [Cloud-init dokumentációs webhelyén](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)talál további információt.  `#cloud-config` a fájlok Base64 kódolású szövegfájlok.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

Aktívan dolgozunk a támogatott Linux disztribúció partnereink ahhoz, hogy engedélyezve van a cloud-init lemezkép érhető el az Azure Marketplace-en. Ezek a lemezképek, győződjön meg a cloud-init-környezetekben, és konfigurációkat és a virtuális gépek és a virtual machine scale sets zökkenőmentesen működjön. Ismerje meg, az Azure cloud-Init használatával kapcsolatos további részletek:

- [Cloud-init támogatás az Azure-beli linuxos virtuális gépekhez](../articles/virtual-machines/linux/using-cloud-init.md)
- [Próbálja ki a Cloud-init használatával történő automatizált virtuálisgép-konfigurációval kapcsolatos oktatóanyagot](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
A [PowerShell desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) egy felügyeleti platform, amely meghatározza a célszámítógépek konfigurációját. A DSC-t Linuxon is használhatja a [nyílt kezelési infrastruktúra (a (z) (a)-kiszolgáló](https://collaboration.opengroup.org/omi/)használatával.

DSC-konfigurációk határozza meg, mi a telepíti egy számítógépre, és a gazdagép konfigurálása. A helyi Configuration Manager (LCM) Konfigurálása motor dolgozza fel a kért műveletek leküldött beállításai minden célként megadott csomóponton fut. Egy lekéréses kiszolgálót egy webszolgáltatás, amelyet a DSC-konfigurációk és a kapcsolódó erőforrások tárolására egy központi gazdagépen futtatja. A lekéréses kiszolgálón kommunikál a LCM motor minden cél gazdagépre, adja meg a szükséges konfigurációk és megfelelőségi jelentést.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy alapszintű DSC-konfigurációt](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [DSC lekéréses kiszolgáló konfigurálása](/powershell/scripting/dsc/pull-server/pullserver).
- [A Linux rendszerhez készült DSC használata](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Az Azure egyéni szkriptek futtatására szolgáló bővítmény
A [Linux](../articles/virtual-machines/linux/extensions-customscript.md) vagy a [Windows rendszerhez](../articles/virtual-machines/windows/extensions-customscript.md) készült egyéni parancsfájl-bővítmény letölti és végrehajtja az Azure-beli virtuális gépeken futó parancsfájlokat. A bővítmény is használhatja, ha létrehoz egy virtuális Gépet, vagy a virtuális gép után bármikor használatban van. 

Parancsfájlok letölthető az Azure storage vagy bármely nyilvános helyre, például egy GitHub-adattárból. Az egyéni Szkriptbővítménnyel és parancsfájlokat írhat bármilyen nyelven, amely a forrás virtuális Gépet futtat. Ezek a parancsfájlok segítségével telepíthet alkalmazásokat, és konfigurálja a virtuális Gépet igény szerint. Hitelesítő adatok biztonságos, bizalmas adatokat, például jelszavak védett konfigurációban is tárolhatók. Ezek a hitelesítő adatok csak a virtuális gép lesznek visszafejtve.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy linuxos virtuális gépet az Azure CLI-vel, és használja az egyéni szkriptek bővítményét](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Hozzon létre egy Windows rendszerű virtuális gépet Azure PowerShell, és használja az egyéni szkriptek bővítményét](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Csomagoló
A [csomagoló](https://www.packer.io) automatizálja a létrehozási folyamatot, amikor létrehoz egy egyéni virtuálisgép-rendszerképet az Azure-ban. Packer használatával határozza meg az operációs rendszer, és futtassa a konfiguráció utáni parancsfájlok, amelyek testre szabhatja a virtuális gép saját igényeinek. A beállítása után a virtuális gép, egy felügyelt lemez rendszerképének majd rögzített. Csomagolói automatizálja a folyamatot a forrás virtuális gép, hálózati és tárolási erőforrások létrehozása, és konfigurációs parancsfájlok futtatása, majd hozza létre a Virtuálisgép-lemezkép.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Linuxos virtuálisgép-rendszerkép létrehozása az Azure-ban a csomagoló használatával](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Hozzon létre egy Windows rendszerű virtuálisgép-rendszerképet az Azure-ban a csomagoló használatával](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
A [Terraform](https://www.terraform.io) egy automatizálási eszköz, amely lehetővé teszi egy teljes Azure-infrastruktúra definiálását és létrehozását egyetlen sablon formátumának nyelvével – a HashiCorp konfigurációs nyelvét (HCL). A terraform használatával hálózati, tárolási és a egy adott megoldás a Virtuálisgép-erőforrások létrehozásának folyamata automatizáló sablonok határozza meg. Használhatja a meglévő Terraform-sablonok az Azure-ral más platformok biztosítani a konzisztenciát és az infrastruktúra üzembe helyezés egyszerűsítéséhez anélkül, hogy az Azure Resource Manager-sablon konvertálása.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Terraform telepítése és konfigurálása az Azure-](../articles/virtual-machines/linux/terraform-install-configure.md)ban.
- [Hozzon létre egy Azure-infrastruktúrát a Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) a runbookok használatával dolgozza fel a célként megadott virtuális gépeken található feladatokat. Az Azure Automation a meglévő virtuális gépek kezelése helyett infrastruktúrák kialakítására szolgáló szolgál. Az Azure Automation is Linux és Windows virtuális gépek, valamint a helyi virtuális vagy fizikai gépeken a hibrid runbook-feldolgozók között futtatható. Runbookok verziókövetési adattár, például a GitHub is tárolható. Ezek a runbookok majd futtathatja manuálisan vagy egy meghatározott ütemezés szerint.

Az Azure Automation Desired State Configuration (DSC) szolgáltatás lehetővé teszi, hogy hogyan kell konfigurálni a virtuális gépek adott halmazát az-meghatározások létrehozása emellett. DSC majd biztosítja, hogy a alkalmazni a szükséges konfigurációval, és a virtuális gép konzisztens marad. Az Azure Automation DSC Windows- és Linux gép futtat.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy PowerShell-runbook](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Hibrid Runbook-feldolgozók használata helyszíni erőforrások kezeléséhez](../articles/automation/automation-hybrid-runbook-worker.md).
- [Azure Automation DSC használata](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
Az [Azure DevOps Services](https://www.visualstudio.com/team-services/) olyan eszközkészlet, amely segítséget nyújt a kódok megosztásában és nyomon követésében, az automatizált buildek létrehozásában, valamint egy teljes folyamatos integrációs és fejlesztési (CI/CD) folyamat létrehozásában. Az Azure DevOps-szolgáltatásokkal integrálja a Visual Studio és az egyéb szerkesztők használatának leegyszerűsítése érdekében. Az Azure DevOps-szolgáltatásokkal is létrehozhat és Azure virtuális gépek konfigurálása és őket, majd kód üzembe helyezése.

További információk:

- [Azure DevOps-szolgáltatások](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
A [Jenkins](https://www.jenkins.io) egy folyamatos integrációs kiszolgáló, amely megkönnyíti az alkalmazások üzembe helyezését és tesztelését, valamint automatizált folyamatokat hozhat létre a kód kézbesítéséhez. Beépülő modulok, a core Jenkins platform bővítése érdekében több száz, és számos más termékek és megoldások webhookok használatával is integrálhatja. Manuálisan telepítse a Jenkins-beli virtuális gépen, futtassa a Jenkins Docker-tárolókon belül vagy előregyártott Azure Marketplace-rendszerkép használata.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Fejlesztési infrastruktúra létrehozása Linux rendszerű virtuális gépen az Azure-ban a Jenkins, a GitHub és a Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Következő lépések
Az infrastruktúra automatizálási eszközök használata az Azure-ban számos különböző lehetőség van. A megoldás, amely legjobban megfelel az igényeinek megfelelően, és a környezet végezheti rendelkezik. A kezdéshez és az Azure-ba beépített eszközök kipróbálásához lásd: [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) vagy [Windows rendszerű](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) virtuális gép testreszabásának automatizálása.
