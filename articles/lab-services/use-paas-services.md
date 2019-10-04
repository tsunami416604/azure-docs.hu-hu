---
title: Platform--szolgáltatásként (PaaS) szolgáltatások használata az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Megtudhatja, hogyan használható a Platform--szolgáltatásként (fázis) modul az Azure DevTest Labs szolgáltatásban.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 865ae0b3f7a7965698a67183a4c820ba71f49cd8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833914"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Platform--szolgáltatásként (PaaS) szolgáltatások használata az Azure DevTest Labs szolgáltatásban
A környezetek funkciójával PaaS DevTest Labs szolgáltatásban létrehozott használata támogatott. DevTest Labs szolgáltatásban létrehozott környezetek előre konfigurált Azure Resource Manager-sablonok a Git-tárházat támogatja. Környezetek PaaS és az IaaS-erőforrásokat is tartalmazhat. Hozhat létre komplex rendszerek, amelyek az Azure-erőforrások, például virtuális gépek, adatbázisok, virtuális hálózatok és a Web apps szolgáltatásban, amely testre szabott együttműködve lehetővé teszik. Ezek a sablonok lehetővé teszik, egységes üzembe helyezést és a továbbfejlesztett verziókövetési környezetek felügyelete. 

A rendszer megfelelően beállítása lehetővé teszi, hogy a következő esetekben: 

- Fejlesztők számára, független és több környezethez
- Aszinkron módon különböző konfigurációkban tesztelése
- A sablon módosítás nélkül átmeneti és üzemi folyamatok integrálása
- Egyszerű kezelés és a költségek reporting rendelkező gépek fejlesztési és a tartalmazó környezeteket ugyanabban a labor javítja.  

A DevTest Labs erőforrás-szolgáltató a lab-felhasználó nevében hoz létre erőforrásokat, így nincs extra engedélyeket kell adni a lab-felhasználó számára a PaaS-erőforrások használatának engedélyezése. Az alábbi képen látható a Service Fabric-fürt egy környezetet a tesztkörnyezetben.

![Service Fabric-fürt-környezet](./media/create-environment-service-fabric-cluster/cluster-created.png)

A környezetek beállításával kapcsolatos további információk: [több virtuális gépes környezet és PaaS-erőforrások létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md). DevTest Labs rendelkezik, amelyek segítségével való csatlakozáshoz külső GitHub forrás saját magának nélkül hozhat létre környezeteket az Azure Resource Manager-sablonok, nyilvános tárházhoz. A nyilvános környezetben található további információ: [konfigurálása és használata a nyilvános környezetben az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-use-public-environments.md).

A nagy szervezetek is használnak a fejlesztői csapatok, például tesztelési környezetek testre szabott/izolált környezetben általában biztosítanak. Előfordulhat, hogy egy részleg vagy egy osztás belül az összes csoport által használt környezet. Előfordulhat, hogy az informatikai csoport szeretne adja meg a környezetek, amelyek a szervezet összes csapatok által használható.  

## <a name="customizations"></a>A testreszabások

#### <a name="sandbox"></a>Sandbox 
A lab tulajdonos szabhatja testre a tesztlabor-környezetekben, módosíthatja a felhasználói szerephez **olvasó** való **közreműködői** az erőforráscsoporton belül. Ez a lehetőség van a **labor beállítások** lap a **Konfigurace a zásady** a labor létrehozása. Ez a változás a szerepkör lehetővé teszi, hogy a felhasználó számára az adott környezeten belüli erőforrásainak hozzáadása vagy eltávolítása. Ha szeretné korlátozni a hozzáférést a további, az Azure házirendekkel. Ez a funkció lehetővé teszi, hogy testre szabhatja az erőforrások vagy konfigurációs anélkül, hogy a hozzáférést az előfizetés szintjén.

#### <a name="custom-tokens"></a>Egyéni jogkivonatok
Van néhány egyéni tesztkörnyezetben információt, amely kívül esik az erőforráscsoport és a jellemző környezeteket, amelyek hozzáférhetnek a sablont. Íme néhány őket: 

- Tesztkörnyezet hálózati azonosító
- Location egység
- A tárfiók, amelyben a Resource Manager-sablonok fájlokat tárolja. 
 
#### <a name="lab-virtual-network"></a>Labor virtuális hálózat
A [környezetek a labor virtuális hálózathoz csatlakozó](connect-environment-lab-virtual-network.md) cikk ismerteti, hogyan módosíthatja a Resource Manager-sablont szeretné használni a `$(LabSubnetId)` token. Környezet létrehozásakor a `$(LabSubnetId)` jogkivonat váltotta fel az első alhálózat be van jelölve, a **használja a virtuális gép létrehozása** beállítás **igaz**. Lehetővé teszi a környezetben használandó korábban létrehozott hálózatok. Ha ugyanazt a Resource Manager-sablonok a tesztelési környezetben átmeneti és éles környezetben használni szeretne, használja a `$(LabSubnetId)` egy Resource Manager-sablon paraméter alapértelmezett értékként. 

#### <a name="environment-storage-account"></a>Környezet Storage-fiók
DevTest Labs használatát támogatja [Resource Manager-sablonok beágyazott](../azure-resource-manager/resource-group-linked-templates.md). A [[tesztelési környezetben beágyazott Azure Resource Manager-sablonok üzembe helyezése](deploy-nested-template-environments.md) a cikk ismerteti, hogyan `_artifactsLocation` és `_artifactsLocationSasToken` egy URI-t egy Resource Manager-sablon létrehozása ugyanabban a mappában vagy egy beágyazott tokenek a fő sablon mappája. Ezek a jogkivonatok két kapcsolatos további információkért lásd: a **üzembe helyezési összetevők** szakaszában [Azure Resource Manager – ajánlott eljárások útmutatója](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md).

## <a name="user-experience"></a>Felhasználói élmény

## <a name="developer"></a>Fejlesztői
A fejlesztők számára a virtuális gép létrehozása ugyanabban a munkafolyamatban használatával hozzon létre egy adott környezetben. Válassza ki a környezetet, és a gép rendszerképén ezeket, és adja meg a szükséges információkat a sablonhoz szükséges. Minden fejlesztői környezet kellene lehetővé teszi a módosítások és a továbbfejlesztett belső ciklus hibakeresés központi telepítésére vonatkozóan. A környezet hozható létre tetszőleges időpontban, a legújabb sablon használatával.  Ez a funkció lehetővé teszi, hogy a környezetekkel sem kell semmisíteni, és újra létre kell hozni az állásidő csökkentése nem kell manuálisan létrehozásakor a rendszer, vagy helyreálljon a hibák tesztelése érdekében.  

### <a name="testing"></a>Tesztelés
DevTest Labs-környezetben a független vizsgálati egyedi kódot és konfigurációk aszinkron módon engedélyezése. Az általános gyakorlat, hogy állítsa be a környezetet, a kóddal, a lekéréses kérelem, és automatizált tesztelés megkezdése. Miután a automatizált tesztelés befejezésére való futtatását követően manuális tesztelése is hajtható végre az adott környezetben. Ez a folyamat általában a CI/CD-folyamat részeként történik. 

## <a name="management-experience"></a>Felügyeleti kezelőfelület

### <a name="cost-tracking"></a>Költségek nyomon követése
A költségek nyomon követését biztosító szolgáltatás tartalmazza a különböző környezetekben Azure-erőforrások általános részeként költség trend. Erőforrások által a költségek nem kezdetét vette az a környezetben a különböző erőforrások, de a környezet egyetlen költségek jelenik meg.

### <a name="security"></a>Biztonság
A DevTest Labs egy megfelelően konfigurált Azure-előfizetést is [férjenek hozzá az Azure-erőforrások kizárólag a labor keresztül](devtest-lab-add-devtest-user.md). A környezetek egy tesztlabor tulajdonosa lehetővé tehetik a felhasználók bármilyen más Azure-erőforrásokhoz való hozzáférés nélkül férhetnek hozzá a jóváhagyott konfigurációval PaaS-erőforrásokhoz. A forgatókönyvben, ahol a lab felhasználói környezetek testreszabása a labor tulajdonos lehetővé tehetik a közreműködői hozzáférés. A közreműködői hozzáférés lehetővé teszi, hogy a lab-felhasználó hozzáadása vagy eltávolítása az Azure-erőforrás csak a felügyelt erőforráscsoporton belül. Ez lehetővé teszi az egyszerűbb nyomon követése, és kezeléséhez engedélyezése a felhasználónak közreműködői hozzáférés szükséges az előfizetéshez.

### <a name="automation"></a>Automation
Az Automation kulcsfontosságú alkotóeleme a nagy méretű, hatékony ökoszisztéma is. Az Automation is kezeléséhez, vagy nyomon követi, több környezetet az előfizetések és laborok kezeléséhez szükséges.

### <a name="cicd-pipeline"></a>CI/CD-folyamat
DevTest Labs szolgáltatásban létrehozott PaaS-szolgáltatások segítségével növelheti a CI/CD-folyamat a kellene a fókuszban lévő központi telepítések, ahol hozzáférésének a Lab.

## <a name="next-steps"></a>További lépések
Részletes információ a környezetekről a következő cikkekben talál: 

- 
- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Konfigurálhatja és használhatja a nyilvános környezetben az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-use-public-environments.md)
- [Önálló Service Fabric-fürtön az Azure DevTest Labs-környezet létrehozása](create-environment-service-fabric-cluster.md)
- [Környezet csatlakoztatása a labor virtuális hálózathoz az Azure DevTest Labs szolgáltatásban](connect-environment-lab-virtual-network.md)
- [Környezetek integrálása az Azure DevOps CI/CD-folyamatok](integrate-environments-devops-pipeline.md)
 





