---
title: "Virtuálisgép-méretezési csoportok – áttekintés | Microsoft Docs"
description: "További információk a virtuálisgép-méretezési csoportokról"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/25/2017
ms.author: guybo
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 54673cbc69e418bdbe7cb5791dfb5d20e7ebcb9f
ms.openlocfilehash: 2294587fd3f978a3f8383112ece329b47307db7a
ms.lasthandoff: 02/28/2017


---
# <a name="what-are-virtual-machine-scale-sets-in-azure"></a>Mik azok a virtuálisgép-méretezési csoportok az Azure-ban?
A virtuálisgép-méretezési csoportok olyan Azure Compute-erőforrások, amelyek azonos virtuális gépek csoportjainak üzembe helyezésére és felügyeletére használhatók. Az egyformán konfigurált virtuális gépeket tartalmazó virtuálisgép-méretezési csoportok a valódi automatikus méretezés támogatására készültek (miközben nincs szükség virtuális gépek üzembe helyezésére), és mint olyanok, megkönnyítik a nagy számítási igényű, „big data” típusú és tárolóalapú számítási feladatokra koncentráló nagyméretű szolgáltatások kiépítését.

A számítási erőforrások horizontális fel- és leskálázását igénylő alkalmazások esetében a méretezési műveletek implicit módon oszlanak el a tartalék- és frissítési tartományok között. A virtuálisgép-méretezési csoportokról bővebb bevezetést [ebben az Azure-blogbejegyzésben](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/) talál.

A virtuálisgép-méretezési csoportokkal kapcsolatban további információkat tudhat meg az alábbi videókból:

* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)  
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (A virtuálisgép-méretezési csoportokról Guy Bowerman mesél)

## <a name="creating-and-managing-vm-scale-sets"></a>Virtuálisgép-méretezési csoportok létrehozása és felügyelete
Ha virtuálisgép-méretezési csoportokat szeretne létrehozni az [Azure Portálon](https://portal.azure.com), válassza az *új* lehetőséget, majd írja be a keresőmezőbe a „méretezés” szót. Az eredmények között megjelenik a „Virtuálisgép-méretezési csoport” kifejezés. Ezután kitöltheti a méretezési csoport testreszabásához és üzembe helyezéséhez szükséges mezőket. A portálon alapszintű automatikus méretezési szabályokat is beállíthat a processzorhasználat alapján.

A virtuálisgép-méretezési csoportok megadását és üzembe helyezését – az egyedi Azure Resource Manager-alapú virtuális gépekhez hasonlóan – JSON-sablonok és [REST API-k](https://msdn.microsoft.com/library/mt589023.aspx) segítségével is elvégezheti. Ezért lehetőség van bármilyen szabványos Azure Resource Manager-alapú üzembe helyezési módszer használatára. A sablonokról további információkat az [Authoring Azure Resource Manager templates](../azure-resource-manager/resource-group-authoring-templates.md) (Azure Resource Manager-sablonok készítése) című témakörben talál.

A virtuálisgép-méretezési csoportokhoz tartozó példasablonkészlet az Azure-gyorssablonok GitHub-adattárában található [itt.](https://github.com/Azure/azure-quickstart-templates) (olyan sablonokat keressen, amelyek címében szerepel a *vmss* szó).

E sablonok információs lapjain talál egy, a portálon keresztüli üzembe helyezési funkcióra mutató gombot. A virtuálisgép-méretezési csoport üzembe helyezéséhez kattintson erre a gombra, majd a portálon írjon be minden szükséges paramétert. Ha nem biztos abban, hogy egy erőforrás támogatja-e a kis- és nagybetűs karaktereket, célszerű mindig kisbetűket és számokat használni a paraméterértékekben. Itt talál továbbá egy hasznos, részletekbe menő videót a virtuálisgép-méretezési csoportsablonról:

[VM Scale Set Template Dissection](https://channel9.msdn.com/Blogs/Azure/VM-Scale-Set-Template-Dissection/player) (Virtuálisgép-méretezési csoportsablon részletesen)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Virtuálisgép-méretezési csoport horizontális fel- és leskálázása
Ha egy Virtuálisgép-méretezési csoportban növelni vagy csökkenteni szeretné a virtuális gépek számát, módosítsa a *capacity* tulajdonság értékét, majd alkalmazza újra a sablont. Ezzel az egyszerű módszerrel könnyűszerrel írható meg egy saját testreszabott méretezési réteg, ha olyan egyedi méretezési eseményeket szeretne meghatározni, amelyeket nem támogat az Azure-alapú automatikus skálázás.

Ha a kapacitás módosításához újraalkalmaz egy sablont, akkor lehetőség van egy sokkal kisebb sablon megadására, amely csak az „SKU” tulajdonságcsomagot tartalmazza a frissített kapacitással. Erre [itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) talál példát.

Az automatikusan méretezett méretezési csoportok létrehozásának lépéseiről bővebben az [Automatically Scale Machines in a Virtual Machine Scale Set](virtual-machine-scale-sets-windows-autoscale.md) (Gépek automatikus méretezése egy virtuálisgép-méretezési csoportban) című témakörben olvashat.

## <a name="monitoring-your-vm-scale-set"></a>Virtuálisgép-méretezési csoport figyelése
Az [Azure-portálon](https://portal.azure.com) megtalálható a méretezési csoportok listája, valamint olyan alapvető tulajdonságok és műveletek, mint a csoportban szereplő virtuális gépek listája és az erőforrás-használati grafikon. A virtuálisgép-méretezési csoportok további részleteit az [Azure erőforrás-kezelővel](https://resources.azure.com) tekintheti meg. A virtuálisgép-méretezési csoportok a Microsoft.Compute erőforrásai, vagyis ezen a webhelyen tekintheti meg őket az alábbi hivatkozások kibontásával:

**Előfizetések -> saját előfizetés -> resourceGroups -> szolgáltatók -> Microsoft.Compute -> virtualMachineScaleSets -> saját virtuálisgép-méretezési csoport -> stb.**

## <a name="vm-scale-set-scenarios"></a>Virtuálisgép-méretezési csoportok használatának esetei
Ez a szakasz a virtuálisgép-méretezési csoportok használatának néhány tipikus esetét sorolja fel. Ezek az esetek néhány magasabb szintű Azure-szolgáltatás (például a Batch, a Service Fabric, az Azure Container Service) használatakor merülhetnek fel.

* **RDP/SSH a virtuálisgép-méretezési csoport példányaihoz** – A rendszer egy virtuálisgép-méretezési csoportot hozott létre egy virtuális hálózaton belül, és a méretezési csoportba tartozó egyes virtuális gépekhez nem osztott ki nyilvános IP-címeket. Ez azért is lehet hasznos, mert a számítási gridben általában véve nem szeretné viselni az egyes nyilvános IP-címek minden állapot nélküli erőforráshoz való kiosztásával járó költségeket és munkaterhelést, és a virtuális hálózatában lévő más erőforrásokról (köztük azokról, amelyek nyilvános IP-címekkel rendelkeznek, például a terheléselosztókról és a különálló virtuális gépekről) könnyűszerrel kapcsolódhat ezekhez a virtuális gépekhez.
* **Csatlakozás virtuális gépekhez NAT-szabályok használatával** – Létrehozhat egy nyilvános IP-címet, hozzárendelheti egy terheléselosztóhoz, majd megadhat egy bejövő NAT-készletet, amely az IP-címen található portokat hozzárendeli a virtuálisgép-méretezési csoportban lévő virtuális gépen található porthoz. Példa:
  
  | Forrás | Forrásport | Cél | Célport |
  | --- | --- | --- | --- |
  |  Nyilvános IP-cím |50000-es port |vmss\_0 |22-es port |
  |  Nyilvános IP-cím |50001-es port |vmss\_1 |22-es port |
  |  Nyilvános IP-cím |50002-es port |vmss\_2 |22-es port |
  
   Az alábbiakban egy példát láthat egy virtuálisgép-méretezési csoport létrehozására, amely NAT-szabályokkal engedélyezi az SSH-kapcsolatot az egyetlen nyilvános IP-címet használó méretezési csoportban lévő összes virtuális géphez: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)
  
   Itt pedig egy másik példa ugyanerre az RDP és a Windows esetében: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)
* **Csatlakozás virtuális gépekhez „jumpbox” segítségével** – Ha ugyanabban a virtuális hálózatban hoz létre egy virtuálisgép-méretezési csoportot és egy különálló virtuális gépet, a különálló virtuális gép és a virtuálisgép-méretezési csoport virtuális gépei kapcsolódhatnak egymáshoz a virtuális hálózat/alhálózat által megadott belső IP-címeik használatával. Ha létrehoz egy nyilvános IP-címet, majd hozzárendeli a különálló virtuális géphez, RDP és SSH segítségével kapcsolódhat a különálló virtuális géphez, majd arról a gépről a virtuálisgép-méretezési csoport példányaihoz. Bizonyára észrevette, hogy egy egyszerű virtuálisgép-méretezési csoport eleve biztonságosabb, mint alapértelmezett konfigurációjában egy nyilvános IP-címmel rendelkező egyszerű különálló virtuális gép.
  
   Ez a sablon például egy egyszerű méretezési csoportot helyez üzembe önálló virtuális géppel: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-jumpbox)
* **Terheléselosztás a virtuálisgép-méretezési csoport példányaiban** – Ha virtuális gépek számítási fürtjére a ciklikus időszeletelés módszerével szeretne munkát továbbítani, lehetőség van egy Azure-terheléselosztó megfelelő,&4;. rétegbeli terheléselosztási szabályokkal való konfigurálására. Mintavételeket adhat meg annak ellenőrzésére, hogy az alkalmazása a portok megadott protokollal, intervallummal és kérelmi útvonallal való pingelésével fut-e. Az Azure [Application Gateway](https://azure.microsoft.com/services/application-gateway/) szintén támogatja a méretezési csoportokat, illetve a&7;. rétegbeli és az annál kifinomultabb terheléselosztási forgatókönyveket.
  
   Az alábbi példa egy Apache webkiszolgálókat futtató virtuálisgép-méretezési csoport létrehozását mutatja be, amely terheléselosztót használ az egyes virtuális gépek terhelésének elosztása érdekében: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-ubuntu-web-ssl) (tekintse meg a Microsoft.Network/loadBalancers erőforrástípust és a networkProfile és extensionProfile profilokat a virtualMachineScaleSet méretezési csoportban)
* **Virtuálisgép-méretezési csoport üzembe helyezése számítási fürtként egy PaaS-fürtkezelőben** – A virtuálisgép-méretezési csoportokat néha következő generációs feldolgozói szerepkörnek is nevezik. A leírás érvényes, azonban fennáll a veszélye annak, hogy összekeveri a méretezési csoport funkcióit a PaaS v1 feldolgozói szerepkör funkcióival. A virtuálisgép-méretezési csoportok bizonyos értelemben valódi „feldolgozói szerepkört” vagy feldolgozói erőforrást biztosítanak, amelyben platform- és futásidő-független, testreszabható és az Azure Resource Manager IaaS szolgáltatásába integrálható általános számítási erőforrást nyújtanak.
  
   Bár egy PaaS v1-alapú feldolgozói szerepkör a platform-/futásidő-támogatás tekintetében korlátozott (csak Windows platformú lemezképekkel használható), magában foglal olyan szolgáltatásokat is, mint a virtuális IP-címek felcserélése, konfigurálható frissítési beállítások, futásidő- és alkalmazástelepítés-specifikus beállítások, amelyek vagy nem érhetők *még* el a virtuálisgép-méretezési csoportokban, vagy más magasabb szintű PaaS-szolgáltatások, például Service Fabric részeként vásárolhatók meg. Mindezt szem előtt tartva a virtuálisgép-méretezési csoportok PaaS-szolgáltatást támogató infrastruktúrának is tekinthetők. Vagyis a PaaS megoldások, például a Service Fabric vagy az olyan fürtkezelők, mint a Mesos, méretezhető számítási rétegként épülhetnek rá a virtuálisgép-méretezési csoportokra.
  
   Erre a megközelítésre példa az Azure Container Service, amely egy tárlóvezénylővel rendelkező méretezési csoportokon alapuló fürtöt helyez üzembe: [https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos).

## <a name="vm-scale-set-performance-and-scale-guidance"></a>A virtuálisgép-méretezési csoportok teljesítményével és skálázásával kapcsolatos útmutató
* Ha olyan méretezési csoportokat használ, amelyekben nem felügyelt lemezek találhatóak, tárfiókonként legfeljebb csak 20 virtuális gépet tervezzen (kivéve, ha az *overprovision* tulajdonságot „false” értékűre állította, amely esetben 40 gépet is megadhat). Felügyelt lemezek esetén ezek a tárfiók-korlátozások nem érvényesek.
* Ha nem felügyelt lemezeket tartalmazó méretezési csoportokat használ, minél tágabb tartományból válassza ki a tárfiókok nevének kezdőbetűit. Az [Azure Quickstart templates](https://github.com/Azure/azure-quickstart-templates/) (Azure gyorsindítási sablonok) webhelyen található, virtuálisgép-méretezési csoportokkal kapcsolatos példasablonok számos példát szolgáltatnak e művelet elvégzésnek módjára.
* Ha nem felügyelt lemezeket tartalmazó egyéni virtuális gépeket használ, egyetlen tárfiókban legfeljebb csak 40 virtuális gépet tervezzen virtuálisgép-méretezési csoportonként. Mielőtt elkezdhetné a virtuálisgép-méretezési csoport üzembe helyezését, a tárfiókba kell másolnia a lemezképet. A felügyelt lemezeket tartalmazó méretezési csoportok akár 100 egyéni rendszerképen alapuló virtuális gépet is támogatnak. További információkat a gyakori kérdések között találhat.
* Virtuális hálózatonként legfeljebb csak 4096 virtuális gépet tervezzen.
* A létrehozható virtuális gépek számának a telepítést végrehajtó régióban érvényes magkvóta szab határt. Előfordulhat, hogy kapcsolatba kell lépnie az ügyfélszolgálattal a számítási kvótahatár további növelése érdekében, még akkor is, ha a felhőszolgáltatásokhoz vagy az IaaS v1-szolgáltatáshoz használt magok esetében magas a határérték. A kvóta lekérdezéséhez futtathatja az Azure parancssori felületének `azure vm list-usage` parancsát, vagy a `Get-AzureRmVMUsage` PowerShell-parancsot (ha a PowerShell 1.0-nál korábbi verziót használ, használja a `Get-AzureVMUsage` parancsot).

## <a name="vm-scale-set-frequently-asked-questions"></a>A virtuálisgép-méretezési csoportokkal kapcsolatos gyakori kérdések
**K.** Hány virtuális gépet tartalmazhat egy virtuálisgép-méretezési csoport?

**V.** Felügyelt lemezek esetén a méretezési csoport 0–1000, platformrendszerképen alapuló virtuális gépet, vagy 0–100, egyéni rendszerképen alapuló virtuális gépet tartalmazhat. Nem felügyelt lemezek esetén (amikor Ön határozza meg a tárfiókokat) a korlát 100 virtuális gép platformrendszerképek és legfeljebb 40 egyéni rendszerképek esetében (alapértelmezés szerint pedig 20, ha az *overprovision* tulajdonság „false” értékre van állítva), mivel az egyéni rendszerképek egyetlen tárfiókra korlátozódnak.

**K.** Támogatott az adatlemezek használata a virtuálisgép-méretezési csoportokon belül?

**V.** Igen. A felügyelt tárfiókkal definiált méretezési csoportok meghatározhatnak egy csatlakoztatott adatmeghajtókból álló konfigurációt, amely a csoport összes virtuális gépére érvényes. A nem felügyelt tárfiókkal definiált méretezési csoportok nem tartalmaznak csatlakoztatott adatmeghajtókat. Egyéb adattárolási lehetőségek:

* Azure Files (SMB-megosztásos meghajtók)
* Operációs rendszer meghajtója
* Ideiglenes meghajtó (helyi, nem Azure Storage-alapú)
* Azure-adatszolgáltatás (például Azure-táblák, Azure-blobok)
* Külső adatszolgáltatás (például távoli adatbázis)

**K.** Mely Azure-régiók támogatják a virtuálisgép-méretezési csoportokat?

**V.** Mindegyik régió támogatja a virtuálisgép-méretezési csoportokat.

**K.** Hogyan lehet egyéni lemezképekből virtuálisgép-méretezési csoportot létrehozni?

**V.** Hagyja üresen (vagy hagyja ki) a vhdContainers tulajdonságot, és adja meg a rendszerkép-tulajdonság URI azonosítóját. Például: [201-vmss-windows-customimage].(https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-customimage)


**K.** Ha a virtuálisgép-méretezési csoportom kapacitását 20-ról 15-re csökkentem, mely virtuális gépek lesznek eltávolítva?

**V.** A rendszer egyenlő arányban távolítja el a virtuális gépeket a méretezési csoportból a frissítési és tartalék tartományok egészében, a rendelkezésre állás maximalizálása érdekében. A rendszer a legmagasabb azonosítóval rendelkező virtuális gépeket távolítja el először.

**K.** Mi történik, ha ezután 15-ről 18-ra növelem a kapacitást?

**V.** Ha 18-ra növeli a kapacitást, akkor a rendszer 3 új virtuális gépet hoz létre. A rendszer minden alkalommal a legmagasabb előző értéktől növeli a virtuálisgép-példány azonosítóját (például 20, 21, 22). A virtuális gépek a tartalék és frissítési tartományok között oszlanak el.

**K.** Ha több bővítményt használok egy virtuálisgép-méretezési csoportban, van lehetőség végrehajtási sorrend kényszerítésére?

**V.** Közvetlenül nem, bár a customScript bővítmény esetében a szkript megvárhatja egy másik bővítmény futtatásának befejezését ([például a bővítménynapló figyelésével](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). További útmutató a végrehajtási sorrendről az alábbi blogbejegyzésben található: [Extension Sequencing in Azure VM Scale Sets](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/) (Végrehajtási sorrendbe állítás Azure-alapú virtuálisgép-méretezési csoportokban).

**K.** Használhatók virtuálisgép-méretezési csoportokat Azure rendelkezésre állási csoportokkal?

**V.** Igen. A virtuálisgép-méretezési csoport egy implicit rendelkezésre állási csoport 5 tartalék és 5 frissítési tartománnyal. A virtualMachineProfile alatt nincs szükség további konfigurálásra. A 100-nál több virtuális gépet tartalmazó virtuálisgép-méretezési csoportok több „elhelyezési csoportra” is kiterjednek, amelyek több rendelkezésre állási csoportnak felelnek meg. A virtuális gépek rendelkezésre állási csoportja létrejöhet ugyanabban a virtuális hálózatban, mint a virtuálisgép-méretezési csoport. Az egyik gyakran alkalmazott konfiguráció a rendelkezésre állási csoportba helyezi a vezérlő csomópont azon virtuális gépeit, amelyek gyakran igényelnek egyéni konfigurálást, és a méretezési csoportba helyezi az adatcsomópontokat.

