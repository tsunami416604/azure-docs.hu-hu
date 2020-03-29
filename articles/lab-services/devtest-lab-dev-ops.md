---
title: Az Azure DevTest Labs és a DevOps integrációja | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure DevTest Labs tesztkörnyezeteit folyamatos integrációs (CI)/ folyamatos kézbesítési (CD) folyamatokon belül vállalati környezetben.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078923"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Az Azure DevTest Labs és az Azure DevOps integrációja
DevOps egy szoftverfejlesztési módszertan, amely integrálja a szoftverfejlesztés (Dev) műveletek (Ops) a rendszer. Ez a rendszer az üzleti céloknak megfelelő en új funkciókat, frissítéseket és javításokat biztosíthat. Ez a módszertan magában foglal mindent a célokon, használati mintákon és az ügyfelek visszajelzésein alapuló új funkciók kialakításától kezdve; problémák esetén a rendszer javításához, helyreállításához és megerősítéséhez. Ennek a módszernek egy könnyen azonosítható összetevője a folyamatos integrációs (CI)/ folyamatos szállítási (CD) csővezeték. A CI/CD-folyamat a rendszer létrehozásához szükséges, a létrehozáshoz, a teszteléshez és a központi telepítéshez szükséges lépések sorozatán keresztül veszi fel a véglegesítéshez szükséges adatokat, kódokat és erőforrásokat. Ez a cikk a laborok vállalati környezetben belüli folyamaton belüli hatékony használatának különböző módjaira összpontosít. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>A laborok használatának előnyei a DevOps-munkafolyamatban 

### <a name="focused-access"></a>Célzott hozzáférés 
Egy tesztkörnyezet összetevőként való használata lehetővé teszi, hogy egy adott ökoszisztéma egy korlátozott csoporthoz társítson. Általában egy csoport vagy csoport, amely egy közös területen vagy egy adott szolgáltatáson dolgozik, egy tesztkörnyezetet rendel hozzá.   

### <a name="infrastructure-replication-in-the-cloud"></a>Infrastruktúra replikációja a felhőben 
A fejlesztő gyorsan beállíthat egy fejlesztői ökoszisztémát, amely tartalmaz egy fejlesztői keretet forráskóddal és eszközökkel. A fejlesztő is létrehozhat egy olyan környezetet, amely közel azonos az éles konfigurációval. Ez segít a gyorsabb belső hurok fejlődését. 

### <a name="pre-production"></a>Éles üzem előtti 
Miután egy labor a CI/CD-folyamat megkönnyíti, hogy több különböző üzem előtti környezetben vagy gépek fut nak egy időben az aszinkron tesztelés. Különböző támogatási infrastruktúrák, például a buildügynökök egy tesztkörnyezetben telepíthetők és kezelhetők. 

## <a name="devops-with-devtest-labs"></a>DevOps a DevTest Labs segítségével 

### <a name="development--operation"></a>Fejlesztés / Üzemeltetés 
A labornak egy olyan csapatra kell összpontosítania, amely egy jellemzőterületen dolgozik. Ez a közös fókusz lehetővé teszi a területspecifikus erőforrások, például eszközök, parancsfájlok vagy Erőforrás-kezelő sablonok megosztását. Ez lehetővé teszi a gyorsabb változásokat, miközben korlátozza a negatív hatások at egy kisebb csoport. Ezek a megosztott erőforrások lehetővé teszik a fejlesztő számára, hogy fejlesztői virtuális gépeket hozzon létre az összes szükséges kóddal, eszközzel és konfigurációval. Ezek dinamikusan vagy olyan rendszerrel hozhatók létre, amely alapképeket hoz létre a testreszabásokkal. Nem csak a fejlesztő hozhat létre virtuális gépeket, hanem devtest labs-környezeteket is létrehozhatnak a szükséges sablonok alapján a megfelelő Azure-erőforrások létrehozásához a laborban. Bármilyen változtatásvagy romboló munka a laborkörnyezet ellen elvégezhető anélkül, hogy bárki másra hatással lenne. Vegye figyelembe azt a forgatókönyvet, amelyben a termék az ügyfél gépére telepített önálló rendszer. Ebben a forgatókönyvben a DevTest Labs továbbfejlesztett virtuális gép létrehozása, amely magában foglalja a további szoftverek telepítése összetevők használatával és az ügyfél konfigurációk előtti a kód gyorsabb belső hurok tesztelése. 
  
## <a name="cicd-pipeline"></a>CI/CD-folyamat 
A CI/CD-folyamat a DevOps egyik kritikus összetevője, amely a fejlesztő lekéréses kérelméből áthelyezi a kódot, integrálja a meglévő kódba, és telepíti azt az éles ökoszisztémába. Nem kell minden erőforrásnak laboron belül lennie. Például egy Jenkins-állomás lehet beállítani a laboron kívül, mint egy állandó erőforrás. Íme néhány konkrét példa a laborok integrálására a folyamatba. 

### <a name="build"></a>Felépítés 
A buildfolyamat egy összetevő-csomag létrehozására összpontosít, amelyet együtt kell tesztelni, hogy átadják a kiadási folyamatnak. Labs része lehet a build folyamat, mint a build ügynökök és egyéb támogatási erőforrások helye. Az infrastruktúra dinamikus kiépítésének képessége nagyobb ellenőrzést tesz lehetővé. Azzal a képességgel, hogy több környezetben egy tesztkörnyezetben, minden build futtatható aszinkron, miközben a build-azonosító a környezeti adatok részeként, hogy egyedileg azonosítsa az erőforrásokat az adott build.   

Build ügynökök, a labor azon képességét, hogy korlátozza a hozzáférést növeli a biztonságot, és csökkenti annak lehetőségét, véletlen korrupció.  

### <a name="test"></a>Test 
A DevTest Labs lehetővé teszi, hogy a CI/CD-folyamat automatizálja az automatikus és manuális teszteléshez használható Azure Resource (virtuális gépek, környezetek) létrehozását. A virtuális gépek olyan összetevők vagy képletek használatával jönnének létre, amelyek a létrehozási folyamatból származó információkat használják a teszteléshez szükséges különböző egyéni konfigurációk létrehozásához.   

### <a name="release"></a>Kiadás 
DevTest Labs gyakran használják ellenőrzésre a kiadási szakaszban, mielőtt a kód telepítése. Hasonló a Build szakaszban végzett teszteléshez. Éles erőforrások at nem kell telepíteni a DevTest Labs. 

### <a name="customization"></a>Testreszabás 
Az Azure DevOps-ban vannak olyan meglévő feladatok, amelyek lehetővé teszik a virtuális gépek és környezetek kezelését adott laborokban. Míg az Azure DevOps-szolgáltatások a CI/CD-folyamat kezelésének egyik módja, integrálhatja a labort bármely olyan rendszerbe, amely támogatja a REST API-k hívását, a PowerShell-parancsfájlok végrehajtását vagy az Azure CLI használatát. 

Míg egyes CI/CD-folyamatkezelők már rendelkeznek nyílt forráskódú beépülő modulokkal, amelyek képesek az Azure-on és a DevTest Labs-en belüli erőforrások kezelésére. Előfordulhat, hogy néhány egyéni parancsfájlt kell használnia a folyamat speciális igényeinek megfelelően.  Ezt szem előtt tartva, egy feladat végrehajtásakor egy egyszerű szolgáltatás a megfelelő szerepkört használja a labor eléréséhez. A szolgáltatásnév általában szüksége van a közreműködői szerepkör-hozzáférés a laborhoz. További információ: [Az Azure DevTests Labs integrálása az Azure DevOps folyamatos integrációs és kézbesítési folyamatába](devtest-lab-integrate-ci-cd-vsts.md)című témakörben talál. 
 