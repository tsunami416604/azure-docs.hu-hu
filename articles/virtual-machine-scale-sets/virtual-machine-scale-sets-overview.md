---
title: Azure virtuálisgép-méretezési csoportok – Áttekintés | Microsoft Docs
description: További információk az Azure virtuálisgép-méretezési csoportokról
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2017
ms.author: negat
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64c4b309d214b91c759ff93ad659b9c69320bea7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2018
---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Mik azok a virtuálisgép-méretezési csoportok az Azure-ban?
A virtuálisgép-méretezési csoportok olyan számítási Azure-erőforrások, amelyek egymással teljesen azonos virtuális gépek csoportjainak üzembe helyezésére és felügyeletére használhatók. Az egyformán konfigurált virtuális gépeket tartalmazó méretezési csoportok a valódi automatikus méretezés támogatására készültek – szükségtelenné téve a virtuális gépek előzetes kiépítését. Ezzel is egyszerűsödik a nagy számítási igényű, nagy adatmennyiséget kezelő és tárolóalapú számítási feladatokra koncentráló nagyméretű szolgáltatások kiépítése.

A számítási erőforrások horizontális fel- és leskálázását igénylő alkalmazások esetében a méretezési műveletek implicit módon oszlanak el a tartalék- és frissítési tartományok között. A méretezési csoportokra vonatkozó részletesebb bevezetést [ebben az Azure-blogbejegyzésben](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/) talál.

A méretezési csoportokkal kapcsolatban további információkat tudhat meg az alábbi videókból:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)  
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (A virtuálisgép-méretezési csoportokról Guy Bowerman mesél)

## <a name="creating-and-managing-scale-sets"></a>Méretezési csoportok létrehozása és kezelése
Ha skálázási csoportokat szeretne létrehozni az [Azure Portalon](https://portal.azure.com), válassza az **Erőforrás létrehozása** lehetőséget, majd írja be a keresőmezőbe a **skálázás** szót. Az eredmények között megjelenik a **Virtual machine scale set** (Virtuálisgép-méretezési csoport) kifejezés. Ezután kitöltheti a méretezési csoport testreszabásához és üzembe helyezéséhez szükséges mezőket. A portálon alapszintű automatikus méretezési szabályokat is beállíthat a processzorhasználat alapján. A méretezési csoport kezeléséhez az Azure Portalt, az [Azure PowerShell-parancsmagokat](virtual-machine-scale-sets-windows-manage.md) vagy az Azure CLI 2.0-t használhatja.

A [rendelkezésre állási zónákban](virtual-machine-scale-sets-use-availability-zones.md) méretezési csoportok helyezhetők üzembe.

A méretezési csoportok megadását és üzembe helyezését – az egyedi Azure Resource Manager-alapú virtuális gépekhez hasonlóan – JSON-sablonok és [REST API-k](https://msdn.microsoft.com/library/mt589023.aspx) segítségével is elvégezheti. Ezért lehetőség van bármilyen szabványos Azure Resource Manager-alapú üzembe helyezési módszer használatára. A sablonokról további információkat az [Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md) (Azure Resource Manager-sablonok készítése) című témakörben talál.

A virtuálisgép-méretezési csoportokhoz tartozó példasablonkészlet az [Azure-gyorssablonok GitHub-adattárában](https://github.com/Azure/azure-quickstart-templates) található. (Olyan sablonokat keressen, amelyek címében szerepel a **vmss** szó.)

A gyors üzembe helyezési útmutató példasablonjainak esetében az egyes sablonok leírásában egy „telepítés az Azure-ba” gomb hivatkozik a portál telepítő funkciójára. A virtuálisgép-méretezési csoport üzembe helyezéséhez kattintson erre a gombra, majd a portálon írjon be minden szükséges paramétert. 


## <a name="autoscale"></a>Automatikus méretezés
Az alkalmazás állandó teljesítményének fenntartása érdekében automatikusan növelheti vagy csökkentheti a méretezési csoportban lévő virtuálisgép-példányok számát. Ez az automatikus méretezési lehetőség csökkenti a kezelési terhelést a méretezési csoport monitorozásakor és finomhangolásakor, ahogy az ügyféligények idővel változnak. Szabályokat teljesítmény-mérőszámok, alkalmazásválaszok vagy meghatározott ütemezés alapján lehet meghatározni, és a méretezési csoport szükség szerint automatikusan elvégzi a méretezést.

Az alapvető automatikus méretezési szabályokhoz gazdagépalapú teljesítmény-mérőszámokat használhat, például a CPU-használatot és a lemez I/O adatait. Ezek a gazdagépalapú mérőszámok automatikusan, további ügynökök vagy bővítmények telepítése és konfigurálása nélkül használatba vehetők. A gazdagépalapú mérőszámokat használó automatikus méretezési szabályok a következő eszközökkel hozhatók létre:

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md)
- [Azure CLI 2.0](virtual-machine-scale-sets-autoscale-cli.md)

Részletesebb teljesítmény-mérőszámok használatához telepítheti és konfigurálhatja az Azure diagnosztikai bővítményét a méretezési csoportjában található virtuálisgép-példányokon. Az Azure diagnosztikai bővítményével további teljesítmény-mérőszámokat gyűjthet be az egyes virtuálisgép-példányokról, például memóriahasználati adatokat. Ezeket a teljesítmény-mérőszámokat a rendszer streamként továbbítja egy Azure Storage-fiókba, és automatikus méretezési szabályokat lehet létrehozni a feldolgozásukhoz. További információt az Azure diagnosztikai bővítmény [Linux virtuális gépen](../virtual-machines/linux/diagnostic-extension.md) vagy [Windows virtuális gépen](../virtual-machines/windows/ps-extensions-diagnostics.md) való engedélyezésével kapcsolatos cikkekben talál.

Az alkalmazás teljesítményének monitorozásához telepíthet és konfigurálhat az alkalmazásban egy kis méretű kialakítási csomagot, hogy használni tudja az App Insights programot. Az alkalmazás válaszidejének vagy a munkamenetek számának részletes teljesítmény-mérőszámai ezután streamként továbbíthatóak az alkalmazásból. Így magához az alkalmazásszintű teljesítményhez hozhat létre meghatározott küszöbértékekkel rendelkező automatikus méretezési szabályokat. Az App Insights programmal kapcsolatos további információért lásd: [Mi az Application Insights?](../application-insights/app-insights-overview.md).


## <a name="manually-scaling-a-scale-set-out-and-in"></a>Méretezési csoport horizontális fel- és leskálázása manuálisan
Egy méretezési csoport kapacitásának manuális módosításához az Azure Portalon kattintson a **Méretezés** szakaszra a **Beállítások** területen. 

A méretezési csoport kapacitásának módosításához használja az [Azure CLI](https://github.com/Azure/azure-cli) **scale** parancsát a parancssorban. Ha például 10 virtuális gépre szeretné beállítani egy méretezési csoport kapacitását, használja a következő parancsot:

```bash
az vmss scale -g resourcegroupname -n scalesetname --new-capacity 10 
```

Egy méretezési csoportba tartozó virtuális gépek számának PowerShell-lel való megadásához használja az **Update-AzureRmVmss** parancsot:

```PowerShell
$vmss = Get-AzureRmVmss -ResourceGroupName resourcegroupname -VMScaleSetName scalesetname  
$vmss.Sku.Capacity = 10
Update-AzureRmVmss -ResourceGroupName resourcegroupname -Name scalesetname -VirtualMachineScaleSet $vmss
```

Ha egy Azure Resource Manager-sablonnal szeretné egy méretezési csoportban növelni vagy csökkenteni a virtuális gépek számát, módosítsa a **capacity** tulajdonság értékét, majd alkalmazza újra a sablont. Ezzel az egyszerű módszerrel könnyedén integrálhatók a méretezési csoportok az automatikus Azure-méretezéssel, vagy megírható egy saját, testreszabott méretezési réteg, ha olyan egyedi méretezési eseményeket kell meghatároznia, amelyeket az automatikus Azure-méretezés nem támogat. 

Ha a kapacitás módosításához újraalkalmaz egy Azure Resource Manager-sablont, akkor lehetőség van egy sokkal kisebb sablon megadására, amely csak az **SKU** tulajdonságcsomagot tartalmazza a frissített kapacitással. [Például:](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)


## <a name="monitoring-your-scale-set"></a>A méretezési csoport figyelése
Az [Azure Portalon](https://portal.azure.com) megtalálható a méretezési csoportok listája, valamint a hozzájuk tartozó tulajdonságok. A portál a felügyeleti műveleteket is támogatja. Végrehajthat felügyeleti műveleteket a méretezési csoportokon és méretezési csoportok egyes virtuális gépein is. A portál biztosít egy testre szabható erőforrás-használati diagramot is. 

Ha meg kell tekintenie vagy szerkesztenie kell egy Azure-erőforrás mögöttes JSON-definícióját, akkor az [Azure Resource Explorert](https://resources.azure.com) is használhatja. A méretezési csoportok a Microsoft.Compute Azure-beli erőforrás-szolgáltató erőforrásai. Ezen a webhelyen tekintheti meg őket az alábbi hivatkozások kibontásával:

**Előfizetések** > **saját előfizetés** > **resourceGroups** > **szolgáltatók** > **Microsoft.Compute** > **virtualMachineScaleSets** > **saját méretezési csoport** &gt; stb.

## <a name="scale-set-scenarios"></a>Méretezési csoportok használatának esetei
Ez a szakasz a méretezési csoportok használatának néhány tipikus esetét sorolja fel. Ezek az esetek néhány magasabb szintű Azure-szolgáltatás (például a Batch, a Service Fabric és a Container Service) használatakor merülhetnek fel.

* **Csatlakozás az RDP vagy az SSH segítségével a méretezési csoport példányaihoz**: A rendszer egy méretezési csoportot hozott létre egy virtuális hálózaton belül, és a méretezési csoportba tartozó egyes virtuális gépekhez nem osztott ki nyilvános IP-címeket. Ez a házirend elkerüli az egyes nyilvános IP-címeknek a számítási grid összes csomópontjához való kiosztásával járó költségeket és munkaterhelést. Ha nem igényel közvetlen külső kapcsolatokat a méretezési csoport virtuális gépeihez, akkor beállíthatja a méretezési csoportot úgy, hogy automatikusan nyilvános IP-címet osszon ki az új virtuális gépeknek. Ezekhez a virtuális gépekhez csatlakozhat a virtuális hálózat más erőforrásairól is (például terheléselosztókról, önálló virtuális gépekről), amelyekhez lehet nyilvános IP-címeket kiosztani. 
* **Csatlakozás virtuális gépekhez NAT-szabályok használatával**: Létrehozhat egy nyilvános IP-címet, hozzárendelheti egy terheléselosztóhoz, majd megadhat egy bejövő NAT-készletet. Ezek a műveletek az IP-címen található portokat hozzárendelik a méretezési csoportban lévő virtuális gépen található porthoz. Például:
  
  | Forrás | Forrásport | Cél | Célport |
  | --- | --- | --- | --- |
  |  Nyilvános IP-cím |50000-es port |vmss\_0 |22-es port |
  |  Nyilvános IP-cím |50001-es port |vmss\_1 |22-es port |
  |  Nyilvános IP-cím |50002-es port |vmss\_2 |22-es port |
  
   [Ebben a példában](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat) a NAT-szabályok úgy vannak megadva, hogy engedélyezzék az SSH-kapcsolatot az egyetlen nyilvános IP-címet használó méretezési csoportban lévő összes virtuális géphez.
  
   [Ez a példa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat) pedig ugyanezt mutatja be RDP és Windows használata esetén.
* **Csatlakozás virtuális gépekhez „jumpbox” segítségével**: Ha ugyanabban a virtuális hálózatban hoz létre egy méretezési csoportot és egy különálló virtuális gépet, a különálló virtuális gép és a méretezési csoport virtuális gépei kapcsolódhatnak egymáshoz a virtuális hálózat/alhálózat által megadott belső IP-címek használatával. Ha létrehoz egy nyilvános IP-címet, majd hozzárendeli a különálló virtuális géphez, RDP és SSH segítségével kapcsolódhat a különálló virtuális géphez. Ezután arról a gépről csatlakozhat a méretezési csoport példányaihoz. Bizonyára észrevette, hogy egy egyszerű méretezési csoport eleve biztonságosabb, mint alapértelmezett konfigurációjában egy nyilvános IP-címmel rendelkező, egyszerű különálló virtuális gép.
  
   [Ez a sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox) például egy egyszerű méretezési csoportot helyez üzembe önálló virtuális géppel. 
* **Terheléselosztás a méretezési csoport példányaiban**: Ha virtuális gépek számítási fürtjére a ciklikus időszeletelés módszerével szeretne munkát továbbítani, lehetőség van egy Azure-terheléselosztó megfelelő, 4. rétegbeli terheléselosztási szabályokkal való konfigurálására. A portok megadott protokollal, intervallummal és kérelmi útvonallal való pingelésével mintavételeket adhat meg annak ellenőrzésére, hogy az alkalmazása fut-e. Az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) szintén támogatja a méretezési csoportokat, illetve a 7. rétegbeli és az annál kifinomultabb terheléselosztási forgatókönyveket.
  
   [Az alábbi példa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) egy Apache-webkiszolgálókat futtató méretezési csoport létrehozását mutatja be, amely terheléselosztót használ az egyes virtuális gépek terhelésének elosztása érdekében. (Tekintse meg a Microsoft.Network/loadBalancers erőforrástípust, valamint a networkProfile és extensionProfile profilt a virtualMachineScaleSet méretezési csoportban.)

   [Ez a linuxos példa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-app-gateway) és [ez a windowsos példa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-app-gateway) az Application Gatewayt használja.  

* **Méretezési csoport üzembe helyezése számítási fürtként egy PaaS-fürtkezelőben**: A méretezési csoportokat néha következő generációs feldolgozói szerepkörnek is nevezik. Noha a leírás érvényes, fennáll a veszélye annak, hogy összekeveri a méretezési csoport funkcióit az Azure Cloud Services funkcióival. A méretezési csoportok bizonyos értelemben valódi feldolgozói szerepkört vagy feldolgozói erőforrást biztosítanak. Használhatók platform- és futásidő-független, testre szabható és az Azure Resource Manager IaaS szolgáltatásába integrálható általános számítási erőforrásként.
  
   Egy Cloud Services-beli feldolgozói szerepkör a platform-/futásidő-támogatás tekintetében korlátozott (csak Windows platformú lemezképekkel használható). Magában foglal azonban olyan szolgáltatásokat is, mint a virtuális IP-címek felcserélése, konfigurálható frissítési beállítások, futásidő- és alkalmazástelepítés-specifikus beállítások. Ezek a szolgáltatások vagy nem érhetők *még* el a virtuálisgép-méretezési csoportokban, vagy más magasabb szintű PaaS-szolgáltatások, például az Azure Service Fabric részeként érhetők el. A méretezési csoportok PaaS-szolgáltatást támogató infrastruktúrának is tekinthetők. A [Service Fabrichoz](https://azure.microsoft.com/services/service-fabric/) hasonló PaaS-megoldások erre az infrastruktúrára épülnek.
  
   A megközelítést bemutató [példában](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) az [Azure Container Service](https://azure.microsoft.com/services/container-service/) egy tárolóvezénylővel rendelkező méretezési csoportokon alapuló fürtöt helyez üzembe.

## <a name="scale-set-performance-and-scale-guidance"></a>A virtuálisgép-méretezési csoportok teljesítményével és skálázásával kapcsolatos útmutató
* A méretezési csoportok legfeljebb 1000 virtuális gépet támogatnak. Ha a saját VM-rendszerképeit hozza létre és tölti fel, a korlát 300. A nagy méretezési csoportok használatánál megfontolandó szempontokról a [nagyméretű virtuálisgép-méretezési csoportok használatát](virtual-machine-scale-sets-placement-groups.md) ismertető cikkben olvashat.
* A méretezési csoporthoz nem szükséges előzetesen Azure tárfiókot létrehozni. A méretezési csoportok támogatják az Azure Managed Disks használatát, amely megszünteti a tárfiókonkénti lemezek számával kapcsolatos teljesítményproblémákat. További információ: [Azure virtuálisgép-méretezési csoportok és felügyelt lemezek](virtual-machine-scale-sets-managed-disks.md).
* Érdemes megfontolni az Azure Premium Storage használatát az Azure Storage helyett a virtuális gépek gyorsabb és kiszámíthatóbb üzembehelyezési ideje és a jobb I/O-teljesítmény érdekében.
* A létrehozható virtuális gépek számának a telepítést végrehajtó régióban érvényes vCPU-kvóta szab határt. Előfordulhat, hogy kapcsolatba kell lépnie az ügyfélszolgálattal a számítási kvótahatár további növelése érdekében, még akkor is, ha az Azure Cloud Serviceshez használt vCPU-k esetében magas a határérték. A kvóta lekérdezéséhez futtathatja a következő Azure CLI-parancsot: `az vm list-usage`. Vagy futtassa ezt a PowerShell-parancsot: `Get-AzureRmVMUsage`.

## <a name="frequently-asked-questions-for-scale-sets"></a>A méretezési csoportokkal kapcsolatos gyakori kérdések
**K.** Hány virtuális gépet tartalmazhat egy méretezési csoport?

**V.** A méretezési csoport 0–1000, platformrendszerképen alapuló virtuális gépet, vagy 0–300, egyéni rendszerképen alapuló virtuális gépet tartalmazhat. 

**K.** Támogatott az adatlemezek használata a méretezési csoportokon belül?

**V.** Igen. A méretezési csoportok meghatározhatnak egy csatlakoztatott adatlemezekből álló konfigurációt, amely a csoport összes virtuális gépére érvényes. További információ: [Azure-beli méretezési csoportok és csatlakoztatott adatlemezek](virtual-machine-scale-sets-attached-disks.md). Egyéb adattárolási lehetőségek:

* Azure Files (SMB-megosztásos meghajtók)
* Operációs rendszer meghajtója
* Ideiglenes meghajtó (helyi, nem Azure Storage-alapú)
* Azure-adatszolgáltatás (például Azure-táblák, Azure-blobok)
* Külső adatszolgáltatás (például távoli adatbázis)

**K.** Mely Azure-régiók támogatják a méretezési csoportokat?

**V.** Mindegyik régió támogatja a méretezési csoportokat.

**K.** Hogyan lehet egyéni rendszerképekből méretezési csoportot létrehozni?

**V.** Hozzon létre egy felügyelt lemezt az egyéni rendszerkép VHD-fájlja alapján, és hivatkozzon arra a méretezési csoport sablonjában. [Például:](https://github.com/chagarw/MDPP/tree/master/101-vmss-custom-os)

**K.** Ha a méretezési csoportom kapacitását 20-ról 15-re csökkentem, mely virtuális gépek lesznek eltávolítva?

**V.** A rendszer egyenlő arányban távolítja el a virtuális gépeket a méretezési csoportból a frissítési és tartalék tartományok egészében, a rendelkezésre állás maximalizálása érdekében. A rendszer a legmagasabb azonosítóval rendelkező virtuális gépeket távolítja el először.

**K.** Mi történik, ha ezután 15-ről 18-ra növelem a kapacitást?

**V.** Ha 18-ra növeli a kapacitást, akkor a rendszer 3 új virtuális gépet hoz létre. A rendszer minden alkalommal a legmagasabb előző értéktől növeli a virtuálisgép-példány azonosítóját (például: 20, 21, 22). A virtuális gépek a tartalék és frissítési tartományok között oszlanak el.

**K.** Ha több bővítményt használok egy méretezési csoportban, van lehetőség végrehajtási sorrend kényszerítésére?

**V.** Közvetlenül nem, bár a customScript bővítmény esetében a szkript megvárhatja egy másik bővítmény futtatásának befejezését. A végrehajtási sorrendről az [Azure-alapú virtuálisgép-méretezési csoportok végrehajtási sorrendjének beállítását](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) ismertető blogbejegyzésben talál további információt.

**K.** Használhatok virtuálisgép-méretezési csoportokat Azure rendelkezésre állási csoportokkal?

**V.** Igen. A méretezési csoport egy implicit rendelkezésre állási csoport öt tartalék és öt frissítési tartománnyal. A 100-nál több virtuális gépet tartalmazó méretezési csoportok több *elhelyezési csoportra* is kiterjednek, amelyek több rendelkezésre állási csoportnak felelnek meg. További információ az elhelyezési csoportokról: [Nagyméretű virtuálisgép-méretezési csoportok használata](virtual-machine-scale-sets-placement-groups.md). A virtuális gépek rendelkezésre állási csoportja létrejöhet ugyanabban a virtuális hálózatban, mint a virtuálisgép-méretezési csoport. Az egyik gyakran alkalmazott konfiguráció egy rendelkezésre állási csoportba helyezi a vezérlő csomópont virtuális gépeit (ezek gyakran igényelnek egyéni konfigurálást), és a méretezési csoportba helyezi az adatcsomópontokat.

A méretezési csoportokra vonatkozó kérdésekre további válaszok [az Azure virtuálisgép-méretezési csoportokkal kapcsolatos gyakori kérdésekben](virtual-machine-scale-sets-faq.md) találhatók.
