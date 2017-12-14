# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Infrastruktúra-automatizálási eszközeivel használata virtuális gépek Azure-ban
Szeretne létrehozni, és az Azure virtuális gépek (VM) kezelése a konzisztens módon, valamilyen automatizált művelettel általában van szükség. Sok eszközök és megoldásokat, amelyek lehetővé teszik, hogy automatizálja a teljes Azure-infrastruktúra központi telepítés és kezelés életciklusának vannak. Ez a cikk be néhány az infrastruktúra automatizálási eszközeivel, melyekkel az Azure-ban. Ezek az eszközök általában elfér a következő módszerek egyikét:

- A virtuális gépek automatizálásához
    - Eszközök közé tartozik [Ansible](#ansible), [Chef](#chef), és [Puppet](#puppet).
    - Néhány virtuális gép testreszabása jellemző eszköz [felhő inicializálás](#cloud-init) Linux virtuális gépekhez, [PowerShell kívánt állapot konfigurációs szolgáltatása (DSC)](#powershell-dsc), és a [Azure egyéni parancsprogramok futtatására szolgáló bővítmény](#azure-custom-script-extension) összes Az Azure virtuális gépeken.
 
- Automatizált infrastruktúra felügyelete
    - Eszközök közé tartozik [csomagoló](#packer) egyéni VM automatizálásához lemezképet hoz létre, és [Terraform](#terraform) automatizálhatja az infrastruktúra összeállítása folyamat.
    - [Azure Automation szolgáltatásbeli](#azure-automation) az Azure és a helyszíni infrastruktúra különböző műveletek végezhetők.

- Alkalmazás központi telepítése és a szállítási automatizálása
    - Példák [Visual Studio Team Services](#visual-studio-team-services) és [Jenkins](#jenkins).


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) egy automatizálási motor konfigurációs, virtuális gép létrehozása, vagy az alkalmazás központi telepítése. Ansible ügynök nélküli modellt alkalmaz, általában az SSH-kulcsokat, és kezelheti a célszámítógépekre hitelesítéséhez. Runbookok Ansible modullistából egyedi feladatokat hajthat végre számos konfigurációs feladatok vannak definiálva. További információkért lásd: [hogyan Ansible működik](https://www.ansible.com/how-ansible-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- [Telepítse és konfigurálja a Ansible Linux rendszeren való használathoz az Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Alapszintű virtuális gép létrehozása](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Hozzon létre egy teljes virtuális gép környezetet, beleértve az erőforrások támogató](../articles/virtual-machines/linux/ansible-create-complete-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) van használható automatizálási platform, amelynek segítségével határozza meg, hogyan infrastruktúráját van konfigurálva, telepítheti és kezelheti. A további összetevők Chef élőhely része az infrastruktúra helyett az alkalmazás-életciklus automation, és Chef InSpec, amelyek segítségével automatizálhatja biztonsági és a házirend követelményeinek való megfelelés. Chef az ügyfelek a célszámítógépen, egy vagy több központi Chef kiszolgálókból álló tárolására és kezelésére a konfigurációk telepítése. További információkért lásd: [Chef egy áttekintése](https://docs.chef.io/chef_overview.html).

Az alábbiak végrehajtásának módját ismerheti meg:

- [Központi telepítése a Chef automatizálhatja az Azure piactérről](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Chef telepítse a Windows és az Azure virtuális gépek létrehozása](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) egy vállalati használatra kész automatizálási platform, amely kezeli a kézbesítési és telepítési folyamatának. Ügynök telepítve van a célszámítógépen ahhoz, hogy Puppet fő jegyzékfájlokban, amelyek meghatározzák a szükséges Konfigurációkezelő az Azure infrastruktúra futtatásához és a virtuális gépek. Puppet integrálható egy továbbfejlesztett devops munkafolyamat például Jenkins és GitHub megoldásait. További információkért lásd: [hogyan Puppet működik](https://puppet.com/product/how-puppet-works).

Az alábbiak végrehajtásának módját ismerheti meg:

- [Az Azure piactérről Puppet telepítése](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Felhő inicializálás](https://cloudinit.readthedocs.io) Linux virtuális gép testreszabása, először elinduló széles körben használt módszer. Felhő inicializálás használhatja csomagok és a fájlok írási, vagy a felhasználók és biztonsági beállításainak. Neve a rendszerindítási folyamat során a felhő inicializálás, mert nincsenek további lépéseket vagy a konfiguráció alkalmazásához szükséges ügynökök.  Helyes formátumban módjáról további információ a `#cloud-config` fájlok, tekintse meg a [felhő-inicializációs dokumentációs oldalát](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`fájlok Base64 kódolású szöveget fájlok.

Felhő inicializálás terjesztéseket is használható. Például, hogy ne használjon **apt-get-telepítés** vagy **yum telepítése** csomag telepítéséhez. Helyette megadhatja a telepítendő csomagok listája. Felhő inicializálás automatikusan használja a natív csomag felügyeleti eszköz a distro választja.

 Folyamatosan dolgozunk a Linux distro hitelesített Partnereinkkel együtt kell biztosítani a felhő inicializálás engedélyezve képek érhető el az Azure piactéren. Ezeket a lemezképeket elérhetővé teheti a felhő inicializálás és konfigurációk zökkenőmentesen működjön együtt a virtuális gépek és virtuálisgép-méretezési készlet. Az alábbi táblázat ismerteti az Azure platformon aktuális felhő inicializálás engedélyezve képek elérhetőségét:

| Közzétevő | Ajánlat | SKU | Verzió | felhő inicializálás kész
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-ES LTS VERZIÓ |legújabb |igen | 
|Canonical |UbuntuServer |14.04.5-LTS |legújabb |igen |
|CoreOS |CoreOS |Stable |legújabb |igen |
|OpenLogic |CentOS |7-KONFIGURÁCIÓELEM |legújabb |előzetes verzióban |
|RedHat |RHEL |7-NYERS-KONFIGURÁCIÓELEM |legújabb |előzetes verzióban |

Ismerje meg a felhő inicializálás Azure kapcsolatos további részletek:

- [Felhő inicializálás támogatja a Linux virtuális gépek Azure-ban](../articles/virtual-machines/linux/using-cloud-init.md)
- [Automatikus Virtuálisgép-konfiguráció használata felhő inicializálás oktatóanyag próbálja](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>A PowerShell DSC
[PowerShell kívánt állapot konfigurációs szolgáltatása (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) olyan felügyeleti platform célszámítógépekre konfigurációjának meghatározása. A DSC használata Linux keresztül a [Open Management Infrastructure (OMI) kiszolgáló](https://collaboration.opengroup.org/omi/).

A DSC-konfigurációk határozza meg, mi egy számítógépre telepítéséhez és a gazdagép konfigurálása. A helyi Configuration Manager (LCM) motor dolgozza fel a kért műveletek megnyomott konfigurációk alapján minden cél csomóponton fut. A lekérési kiszolgálójával egy webszolgáltatás, amelyet a DSC-konfigurációk és a kapcsolódó erőforrások egy központi gazdagépen futtatja. A lekérési kiszolgálójával kommunikál a LCM motor adja meg a konfigurálást, és a megfelelőségi jelentés minden egyes megcélzott gazdagépen.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Alapszintű DSC-konfiguráció létrehozása](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [A DSC lekérési kiszolgálójával konfigurálása](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [A DSC használata Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Az Azure egyéni parancsprogramok futtatására szolgáló bővítmény
Az Azure egyéni parancsprogramok futtatására szolgáló bővítmény a [Linux](../articles/virtual-machines/linux/extensions-customscript.md) vagy [Windows](../articles/virtual-machines/windows/extensions-customscript.md) és hajtanak végre a parancsfájlok Azure virtuális gépeken. A bővítmény segítségével használhatja, ha a virtuális gép létrehozása, vagy a virtuális gép követően használatban van. 

Parancsfájlok letölthető az Azure storage vagy bármely nyilvános helyre, például egy GitHub-tárházban. Az egyéni parancsprogramok futtatására szolgáló bővítmény bármilyen nyelven, a forrás virtuális Gépen futó parancsfájlokat írhat ki. Ezek a parancsfájlok segítségével telepíthet alkalmazásokat, és konfigurálja a virtuális gép tetszés szerint. Hitelesítő adatok védelme érdekében bizalmas adatokat, például jelszavakat védett konfigurációban tárolható. Ezek a hitelesítő adatok csak a virtuális Gépen belül lesznek visszafejtve.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Linux virtuális gép létrehozása az Azure parancssori felület segítségével, és használja az egyéni parancsprogramok futtatására szolgáló bővítmény](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Windows virtuális gép létrehozása az Azure PowerShell és az egyéni parancsprogramok futtatására szolgáló bővítmény használatára](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Csomagoló
[Csomagoló](https://www.packer.io) a felépítési folyamat automatizálja az egyéni Virtuálisgép-lemezkép az Azure-ban való létrehozásakor. Csomagoló segítségével határozza meg az operációs rendszer, és futtassa a konfiguráció utáni parancsfájlok, amelyek testre szabhatja a virtuális gép igényeinek. Konfigurálása után a virtuális gép majd felügyelt lemez lemezképként van rögzítve. Csomagoló automatizálja a folyamat létrehozása a forrás virtuális gép, hálózati és tárolási erőforrásokat, konfigurációs parancsfájlok futtatása, és ezután hozzon létre a Virtuálisgép-lemezkép.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Az Azure Linux Virtuálisgép-lemezkép létrehozásához használja a csomagoló](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Az Azure-ban Windows Virtuálisgép-lemezkép létrehozásához használja a csomagoló](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) automatizálási eszköz, amely lehetővé teszi, hogy adja meg, és hozzon létre egy teljes Azure-infrastruktúra ugyanazt a sablont formátum nyelv – a HashiCorp konfigurációs nyelvi (Hardverkompatibilitási). A Terraform adja meg, amely hálózati, tárolási és Virtuálisgép-erőforrások, az adott alkalmazáshoz megoldások létrehozásának folyamata automatizálható sablonokat. Segítségével a meglévő Terraform sablonok a más platformok, az Azure-ral biztosítják a konzisztenciát és egyszerűsítse az infrastruktúra telepítése anélkül, hogy az Azure Resource Manager sablon átalakítása.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Telepítse és konfigurálja a Terraform az Azure-ral](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Hozzon létre egy Azure-infrastruktúra Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation szolgáltatásbeli](https://azure.microsoft.com/services/automation/) használja a runbookok feldolgozásához feladatokhoz a virtuális gépeken célozhat meg. Azure Automation szolgáltatásbeli meglévő virtuális gépek kezeléséhez helyett létrehozhat egy infrastruktúrát használja. Azure Automation szolgáltatásbeli futtathatja a Linux és Windows virtuális gépek, valamint a hibrid forgatókönyv-feldolgozók – helyszíni virtuális vagy fizikai gépek között. A verziókövetési tárházat, például a Githubon Runbookok tárolhatja. Ezek a runbookok követően futtathatja manuálisan vagy egy meghatározott ütemezés szerint.

Azure Automation szolgáltatásbeli is biztosít, amely lehetővé teszi, hogyan kell konfigurálni a virtuális gépek adott halmazát definícióinak létrehozásához szükséges konfiguráló (DSC) szolgáltatás. A DSC majd biztosítja a szükséges konfiguráció alkalmazása és konzisztens marad a virtuális Gépet. Az Azure Automation DSC futó Windows- és Linux rendszerű gépek.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Létrehozhat egy PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Hibrid forgatókönyv-feldolgozók segítségével helyszíni erőforrások kezelése](../articles/automation/automation-hybrid-runbook-worker.md).
- [Azure Automation DSC használata](../articles/automation/automation-dsc-getting-started.md).


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Vonja össze a szolgáltatások](https://www.visualstudio.com/team-services/) eszközöket tartalmazza, amelyek segítséget nyújtanak a megosztások és követése code, automatikus buildek használja, és hozzon létre egy teljes folyamatos integrációt és fejlesztési (CI/CD) folyamat csomagja. Team Services integrálható a Visual Studio és egyéb szerkesztők használati leegyszerűsítése érdekében. Team Services is létrehozása és konfigurálása az Azure virtuális gépek és majd kód telepítése őket.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Hozzon létre egy folyamatos integrációt folyamat Team Services](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) folyamatos integrációt kiszolgáló, amely segít telepíteni és alkalmazások tesztelése és kód kézbesítésre automatizált folyamatok létrehozása. A core Jenkins platform kiterjeszteni beépülő modulok több száz, és sok más termékek és webhookokkal megoldások is integrálhatja. Manuálisan Jenkins telepítése egy Azure virtuális gépen, a Jenkins futtatni egy Docker-tároló vagy egy előre elkészített Azure piactér lemezképpel.

Az alábbiak végrehajtásának módját ismerheti meg:

- [Létrehozhat egy fejlesztési infrastruktúrát a Linux virtuális gép az Azure-ban Jenkins, a Githubon és a Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Következő lépések
Az Azure-infrastruktúra automatizálási eszközeivel használandó számos különböző lehetőség áll rendelkezésre. Lehetősége van a szabadságot, hogy a megoldás, amely a leginkább megfelel az igényeinek és a környezet használata. Első lépések, majd próbálja meg az egyes Azure beépített eszközök, lásd: a testreszabása automatizálása a [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) vagy [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) virtuális gép.
