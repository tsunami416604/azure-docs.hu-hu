---
title: Platform-szolgáltatásként (PaaS) szolgáltatások használata az Azure DevTest Labs-ben
description: Ismerje meg, hogyan használhatja a Platform-as-a-Service (Pass) szolgáltatásokat az Azure DevTest Labs-ben.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169192"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>Platform-szolgáltatásként (PaaS) szolgáltatások használata az Azure DevTest Labs-ben
A PaaS a környezetek szolgáltatáson keresztül támogatja a DevTest Labs. A DevTest Labs környezeteit előre konfigurált Azure Resource Manager-sablonok támogatják egy Git-tárházban. A környezetek PaaS- és IaaS-erőforrásokat is tartalmazhatnak. Lehetővé teszik olyan összetett rendszerek létrehozását, amelyek azure-erőforrásokat, például virtuális gépeket, adatbázisokat, virtuális hálózatokat és webalkalmazásokat is tartalmazhatnak, amelyek testre szabottak az együttműködésre. Ezek a sablonok lehetővé teszik a környezetek konzisztens telepítését és jobb kezelését a forráskód-ellenőrzés használatával. 

A megfelelően beállított rendszer a következő eseteket teszi lehetővé: 

- A fejlesztők, hogy a független és több környezetben
- Tesztelés különböző konfigurációkon aszinkron módon
- Integráció az átmeneti és éles folyamatokba sablonmódosítások nélkül
- Miután a fejlesztői gépek és a környezetek ugyanabban a laborban megkönnyíti a felügyeleti és költségjelentés.  

A DevTest Labs erőforrás-szolgáltató erőforrásokat hoz létre a labor felhasználó nevében, így nem kell további engedélyeket adni a tesztkörnyezet-felhasználó nak a PaaS-erőforrások használatának engedélyezéséhez. Az alábbi képen egy Service Fabric-fürt környezetként a laborban látható.

![A Service Fabric-fürt környezetként](./media/create-environment-service-fabric-cluster/cluster-created.png)

A környezetek beállításával kapcsolatos további információkért [lásd: Többvirtuális gépes környezetek és PaaS-erőforrások létrehozása az Azure Resource Manager-sablonokkal című témakörben.](devtest-lab-create-environment-from-arm.md) A DevTest Labs rendelkezik az Azure Resource Manager-sablonok nyilvános tárházával, amelysegítségével környezeteket hozhat létre anélkül, hogy egyedül kellnie egy külső GitHub-forráshoz. A nyilvános környezetekkel kapcsolatos további információkért olvassa el a [Nyilvános környezetek konfigurálása és használata az Azure DevTest Labs alkalmazásban című témakört.](devtest-lab-configure-use-public-environments.md)

A nagy szervezetekben a fejlesztői csapatok általában olyan környezeteket biztosítanak, mint például a testreszabott/elkülönített tesztelési környezetek. Előfordulhatnak olyan környezetek, amelyeket egy részlegen vagy részlegen belül minden csapatnak használnia kell. Előfordulhat, hogy az informatikai csoport szeretné biztosítani a környezetüket, amelyet a szervezet összes csapata használhat.  

## <a name="customizations"></a>Testreszabások

#### <a name="sandbox"></a>Tesztkörnyezet 
A labor tulajdonosa testre szabhatja a tesztkörnyezeteket a felhasználó szerepkörének **olvasóról** **közreműködőre** történő módosításához az erőforráscsoporton belül. Ez a funkció a **Labor beállítások** lap a konfiguráció és a **labor házirendek.** Ez a szerepkör-változás lehetővé teszi a felhasználó számára, hogy erőforrásokat adjon hozzá vagy távolítson el az adott környezetben. Ha tovább szeretné korlátozni a hozzáférést, használja az Azure-szabályzatokat. Ez a funkció lehetővé teszi az erőforrások vagy a konfiguráció testreszabását az előfizetési szintű hozzáférés nélkül.

#### <a name="custom-tokens"></a>Egyéni tokenek
Van néhány egyéni tesztkörnyezet információ, amely kívül esik az erőforráscsoport, és a sablon által elérhető környezetekre jellemző. Íme néhány közülük: 

- Laborhálózat azonosítása
- Hely
- Az erőforrás-kezelői sablonok at tároló tárfiók. 
 
#### <a name="lab-virtual-network"></a>Labor virtuális hálózat
A [csatlakozási környezetek a labor virtuális hálózati](connect-environment-lab-virtual-network.md) cikk ismerteti, hogyan módosíthatja `$(LabSubnetId)` a Resource Manager sablont a jogkivonat használata. Környezet létrehozásakor a `$(LabSubnetId)` jogkivonatot az első alhálózati jel váltja fel, ahol a virtuális gép **létrehozási beállításában való használat** értéke **igaz.** Lehetővé teszi környezetünk számára a korábban létrehozott hálózatok használatát. Ha ugyanazokat az Erőforrás-kezelő sablonokat szeretné használni a tesztelési `$(LabSubnetId)` környezetekben, mint az előkészítés és az éles környezet, használja alapértelmezett értékként az Erőforrás-kezelő sablonparaméterében. 

#### <a name="environment-storage-account"></a>Környezeti tárfiók
A DevTest Labs támogatja a [beágyazott Erőforrás-kezelő sablonok](../azure-resource-manager/templates/linked-templates.md)használatát. A [[Telepítési beágyazott Azure Resource Manager sablonok tesztelése környezetek](deploy-nested-template-environments.md) cikk ismerteti, hogyan kell használni, `_artifactsLocation` és `_artifactsLocationSasToken` jogkivonatok létrehozása URI egy Resource Manager sablon ugyanabban a mappában, vagy egy beágyazott mappában a fő sablon. A két jogkivonatról az Azure Resource Manager [– Gyakorlati tanácsok című](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)témakör telepítési **összetevők** című szakaszában talál további információt.

## <a name="user-experience"></a>Felhasználó felület

## <a name="developer"></a>Fejlesztői
A fejlesztők ugyanazt a munkafolyamatot használják a virtuális gép létrehozásához egy adott környezet létrehozásához. Kiválasztják a környezetet a géplemezképpel szemben, és beírják a sablon által igényelt szükséges információkat. Minden fejlesztő, amelynek környezete lehetővé teszi a módosítások telepítését és a továbbfejlesztett belső hurok hibakeresést. A környezet bármikor létrehozható a legújabb sablon használatával.  Ez a funkció lehetővé teszi a környezetek megsemmisítését és újbóli létrehozását, hogy csökkentse az állásidőt a rendszer manuális létrehozásától vagy a hibatesztelésből való helyreállítástól.  

### <a name="testing"></a>Tesztelés
DevTest Labs környezetek lehetővé teszik a független tesztelése adott kód és konfigurációk aszinkron módon. Az általános gyakorlat az, hogy a környezet et az egyes lekéréses kérelem kódjával állítsa be, és indítsa el az automatikus tesztelést. Az automatikus tesztelés befejezését követően bármilyen manuális tesztelés végrehajtható az adott környezetben. Ez a folyamat általában a CI/CD-folyamat részeként történik. 

## <a name="management-experience"></a>Vezetői élmény

### <a name="cost-tracking"></a>Költségkövetés
A költségkövetési funkció a teljes költségtrend részeként a különböző környezetekben lévő Azure-erőforrásokat is tartalmazza. Az erőforrások általi költség nem bontja ki a környezeten belüli különböző erőforrásokat, hanem egyetlen költségként jeleníti meg a környezetet.

### <a name="security"></a>Biztonság
A DevTest Labs megfelelően konfigurált Azure-előfizetés [csak a laboron keresztül korlátozhatja az Azure-erőforrásokhoz való hozzáférést.](devtest-lab-add-devtest-user.md) A környezetek segítségével a tesztkörnyezet tulajdonosa engedélyezheti a felhasználóknak, hogy a PaaS-erőforrások jóváhagyott konfigurációkkal való eléréséhez anélkül, hogy más Azure-erőforrásokhoz való hozzáférést engedélyezne. Abban a forgatókönyvben, ahol a tesztkörnyezet-felhasználók testre szabhatja a környezeteket, a tesztkörnyezet tulajdonosa engedélyezheti a közreműködői hozzáférést. A közreműködői hozzáférés lehetővé teszi, hogy a labor-felhasználó csak a felügyelt erőforráscsoporton belül adja hozzá vagy távolítsa el az Azure-erőforrást. Ez lehetővé teszi a könnyebb nyomon követés és kezelés, szemben lehetővé teszi a felhasználó közreműködői hozzáférést az előfizetéshez.

### <a name="automation"></a>Automation
Az automatizálás kulcsfontosságú eleme egy nagyszabású, hatékony ökoszisztémának. Automatizálás szükséges kezelésére vagy nyomon követésére több környezetben előfizetések és laborok között.

### <a name="cicd-pipeline"></a>CI/CD-folyamat
A DevTest Labs PaaS-szolgáltatásai segíthetnek a CI/CD-folyamat fejlesztésében azáltal, hogy koncentrált központi telepítéseket, ahol a hozzáférést a labor szabályozza.

## <a name="next-steps"></a>További lépések
A környezetekkel kapcsolatos részleteket az alábbi cikkekben találja: 

- 
- [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md)
- [Nyilvános környezetek konfigurálása és használata az Azure DevTest Labsben](devtest-lab-configure-use-public-environments.md)
- [Környezet létrehozása önálló Service Fabric-fürttel az Azure DevTest Labs ben](create-environment-service-fabric-cluster.md)
- [Környezet csatlakoztatása a labor virtuális hálózatához az Azure DevTest Labs ben](connect-environment-lab-virtual-network.md)
- [Környezetek integrálása az Azure DevOps CI/CD-folyamatokba](integrate-environments-devops-pipeline.md)
 





