---
title: Integráció az Azure DevTest Labs és fejlesztési és üzemeltetési |} A Microsoft Docs
description: Használata labs, az Azure DevTest Labs belül egy folyamatos integrációs (CI) / folyamatos készregyártás (CD) folyamatok vállalati környezetben.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078923"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Az Azure DevTest Labs szolgáltatásban, és az Azure DevOps-integráció
DevOps-szoftverek fejlesztési módszertanába, amely a szoftverfejlesztői (fejlesztői) integrálható az operations (Ops) a rendszer. Ez a rendszer az üzleti célok összhangban szállíthatják le új funkciók, frissítéseket és javításokat. Ez a módszer több magában foglalja a célok, használati mintákat és; ügyfélvisszajelzések alapján új funkciók tervezése kijavítása, helyreállítása és korlátozások a rendszer a hibák bekövetkezésekor. Ez a módszer egy könnyen azonosítható összetevője a folyamatos integrációs (CI) / folyamatos készregyártás (CD) folyamat. A CI/CD-folyamat az adatokat, a kód és az erőforrások létrehozását, tesztelése és üzembe helyezés, a rendszer előállításához tartalmazó lépések sorozatán keresztül egy véglegesítés az vesz igénybe. Ez a cikk a hatékony használatot vállalati környezetben labs egy adott folyamaton belül különböző módokon összpontosít. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>A DevOps-munkafolyamatban labs használatának előnyei 

### <a name="focused-access"></a>Koncentrálhassanak 
Labor használja, mint egy összetevő lehetővé teszi, hogy egy adott ökoszisztéma társítása a felhasználók egy korlátozott csoportjára. Általában egyetlen csapatra vagy csoportra, amely egy közös területén, illetve egy adott szolgáltatás működik kell hozzájuk rendelt tesztkörnyezet.   

### <a name="infrastructure-replication-in-the-cloud"></a>Felhőbeli infrastruktúra-replikáció 
A fejlesztők gyorsan beállíthat egy fejlesztői ökoszisztéma, amely tartalmaz egy fejlesztői mező, a Forráskód és eszközökkel. A fejlesztői is létrehozhat olyan környezetet, amely szinte teljesen megegyezik a termelési konfigurációja. És biztonságosabbá teszi a belső ciklus gyorsabb fejlesztése. 

### <a name="pre-production"></a>Pre-production 
A CI/CD-folyamat labor kellene megkönnyíti több különböző éles üzem előtti környezetek vagy gépek aszinkron tesztelési egy időben futnak. Különböző támogatási infrastruktúrákat, például a build-ügynökök is telepíthető és kezelhető kulcsok tesztkörnyezetben. 

## <a name="devops-with-devtest-labs"></a>A DevTest Labs fejlesztési és üzemeltetési 

### <a name="development--operation"></a>Fejlesztés / művelet 
Labor kell fordítani a egy csapatot, amely egy szolgáltatás területen működik. Ez gyakori fókusz lehetővé teszi a megosztási területspecifikus-erőforrások, például az eszközök, a szkript vagy a Resource Manager-sablonok. Eközben a negatív hatások, hogy egy szűkebb csoportra lehetővé teszi a gyorsabb módosításokat. A közös erőforrások lehetővé teszik, hogy a fejlesztő fejlesztési virtuális gépek létrehozása a szükséges kódot, eszközök és konfigurációs. Ezeket vagy dinamikusan létrehozhatók, vagy a rendszer, amely létrehozza az alaplemezképek a testre szabott értékekkel. Nem csak a fejlesztői virtuális gépeket hozhat létre, de azok is a megfelelő Azure-erőforrások létrehozása a lab-ben a szükséges sablonok alapján DevTest Labs-környezetet hozhat létre. Minden módosítás és a felülíró munkahelyi hajtható végre ellen a laborkörnyezetben bármely más befolyásolása nélkül. Fontolja meg a forgatókönyv, ahol a termék egy rendszer önálló az ügyfél gépekre telepíthető. Ebben a forgatókönyvben a DevTest Labs javult a virtuális gép létrehozása, amely tartalmazza a további szoftverek összetevők használatával, és előre létrehozásához a kódot, gyorsabb belső ciklus tesztelési olyan felhasználói konfigurációk telepítése. 
  
## <a name="cicd-pipeline"></a>CI/CD-folyamat 
A CI/CD-folyamatok az egyik az, hogy a fejlesztő lekéréses kérelem-kódokat, integrálható a meglévő kód, és telepíti az üzemi ökoszisztéma a fejlesztési és üzemeltetési kritikus összetevői. Labor belül minden erőforrás nem szükséges. Például egy Jenkins-gazdagép sikerült állítani labor kívül több állandó erőforrásként. Az alábbiakban néhány konkrét példa a tesztkörnyezetek integrálása a folyamatot. 

### <a name="build"></a>Felépítés 
A létrehozási folyamat összpontosít, amelyek együtt, a kiadási folyamathoz feladatkiosztás tesztelt összetevők csomag létrehozása. Tesztkörnyezetek a buildelési folyamat része lehet a build-ügynökök és egyéb támogatási erőforrások helyeként. Dinamikusan hozhat létre az infrastruktúra teszi lehetővé, hogy nagyobbnak ellenőrzéshez. Több környezet rendelkezik egy tesztkörnyezetben lehetővé teszi minden build is futhat aszinkron módon történik a build-azonosítója használatával a környezeti információk részeként az erőforrásokat, az adott buildre egyedi azonosításához.   

A build-ügynökök a labor létrehozása lehetővé teszi a hozzáférés korlátozása növeli a biztonságot a, és csökkenti a véletlen sérülések.  

### <a name="test"></a>Tesztelés 
DevTest Labs lehetővé teszi, hogy az Azure-erőforrás (a virtuális gépeket, a környezetek), amely automatikus és manuális tesztelési segítségével automatizálhatja a CI/CD-folyamat. A virtuális gépeket kellene összetevők vagy képletek, amelyek a létrehozási folyamat adatait használja a különböző egyéni konfigurációk létrehozásához szükséges tesztelési segítségével kell létrehozni.   

### <a name="release"></a>Kiadás 
DevTest Labs gyakran használják a kiadási szakaszban ellenőrzési a kód üzembe helyezése előtt. Ez hasonlít a Build szakaszban tesztelése. DevTest Labs belül nem telepíthető, éles erőforrásait. 

### <a name="customization"></a>Testreszabás 
Az Azure DevOps nincsenek meglévő feladatokat, amelyek lehetővé teszik a virtuális gépek kezelése és az adott labs környezeteket. Azure DevOps-szolgáltatásokkal, amelyek közül egyik módja a CI/CD-folyamatok kezelése a labor létrehozása, amely támogatja a REST API-kat, PowerShell-szkriptek végrehajtása vagy az Azure CLI használata lehetővé teszi bármely rendszerbe integrálható. 

Míg néhány CI/CD-folyamat vezető meglévő nyílt forráskódú beépülő modulok, amelyekkel az Azure és a DevTest Labs-erőforrások is kezelhetők. Szükség lehet néhány egyéni parancsfájl használata a konkrét igényei szerint a folyamat.  Az adott szem előtt, amikor egy feladat végrehajtása egy egyszerű szolgáltatást a labor eléréséhez szolgál a megfelelő szerepkörrel. Egyszerű szolgáltatás általában a labor létrehozása a közreműködő szerepkört hozzá kell férnie. További információkért lásd: [Azure DevTests laborok integrálása az Azure fejlesztési és üzemeltetési folyamatba folyamatos integrációt és teljesítést](devtest-lab-integrate-ci-cd-vsts.md). 
 