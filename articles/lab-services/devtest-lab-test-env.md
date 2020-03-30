---
title: Az Azure DevTest Labs használata virtuális gép- és PaaS-tesztkörnyezetekben | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja az Azure DevTest Labs virtuális gép és PaaS tesztkörnyezet forgatókönyvek.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c6b458091a8e5e22cca55d401e89e5e13bcf9de9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60623178"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Az Azure DevTest Labs használata virtuális gép- és PaaS-tesztkörnyezetekben

Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a gépeket a tesztelők számára. 

Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A tesztelők gyorsan tesztelhetik az alkalmazás legújabb verzióját Windows- és Linux-környezetek újrahasznosítható sablonokkal és összetevőkkel történő kiépítésével.
- A tesztelők vertikálisan felskálázhatják a terhelésteszteket több tesztügynök kiépítésével.
- A rendszergazdák a következők biztosításával szabályozhatják a költségeket:
  - A tesztelők nem kaphatnak több virtuális gépet, mint amire szükségük van.
  - A virtuális gépek leállnak, ha nincsenek használatban.

![A DevTest Labs használata a képzéshez](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebben a cikkben megismerheti a tesztelők követelményeinek kielégítésére használt különböző Azure DevTest Labs-funkciókat, valamint a tesztkörnyezet beállításához követendő részletes lépéseket.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Tesztkörnyezetek megvalósítása az Azure DevTest Labs segítségével
1. **A labor létrehozása** 
   
    Labs a kiindulási pont az Azure DevTest Labs. Miután létrehozott egy tesztkörnyezetet, olyan feladatokat hajthat végre, mint például felhasználók (tesztelők) hozzáadása a tesztkörnyezethez, a költségek szabályozására vonatkozó házirendek beállítása, a gyorsan létrehozható virtuálisgép-lemezképek definiálása és egyebek.  
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása az Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Ismerje meg, hogyan hozhat létre labort az Azure DevTest Labs az Azure Portalon. |
2. **Virtuális gépek létrehozása percek alatt kész piactéri lemezképek és egyéni lemezképek használatával** 
   
    Az Azure Marketplace-en a képek széles választékából választhat kész képeket, és elérhetővé teheti őket a laborban. Ha a kész lemezképek nem felelnek meg a követelményeknek, létrehozhat egy egyéni lemezképet egy tesztkörnyezetvirtuális gép létrehozásával egy kész lemezkép használatával az Azure Marketplace-en, az összes szükséges szoftver telepítése, és a virtuális gép mentése egyéni lemezképként a laborban.

    Ha egyéni képeket fog használni, fontolja meg egy képgyár használatát a képek létrehozásához és terjesztéséhez. A lemezképgyár egy kódkénti konfigurációs megoldás, amely rendszeresen létrehozza és automatikusan terjeszti a konfigurált lemezképeket. Ezzel időt takarít meg a rendszer manuális konfigurálásához, miután az alap operációs rendszerrel létrejött egy virtuális gép.
  
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Azure Marketplace-rendszerképek konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan listázhatja az Azure Marketplace-lemezképeket, és csak a tesztelők számára kívánt képeket teheti elérhetővé.|
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni lemezképet a szükséges szoftver előzetes telepítésével, hogy a tesztelők gyorsan létrehozhathassanak egy virtuális gépet az egyéni lemezkép használatával.|
   | [További információ a képgyárról](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Nézzen meg egy videót, amely leírja a képgyár beállítását és használatát.|

3. **Újrafelhasználható sablonok létrehozása tesztgépekhez** 
   
    Az Azure DevTest Labs képlete a virtuális gép létrehozásához használt alapértelmezett tulajdonságértékek listája. Képletet hozhat létre a laborban egy lemezkép, a virtuális gép méretének (a CPU és a RAM kombinációja) és egy virtuális hálózat kiválasztásával. Minden tesztelő láthatja a képletet a laborban, és használja egy virtuális gép létrehozásához. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [DevTest Labs képletek kezelése virtuális gépek létrehozásához](devtest-lab-manage-formulas.md) |Megtudhatja, hogyan hozhat létre képletet egy lemezkép, a virtuális gép méretének (a CPU és a RAM kombinációja) és a virtuális hálózat felvételével.|

3. **Többvirtuális gépes tesztkörnyezetek létrehozása** 
   
    Az Azure Resource Manager-sablonok segítségével meghatározhatja az Azure-megoldás infrastruktúráját és konfigurációját, és ismételten több tesztvirtuális gépet helyezhet üzembe konzisztens állapotban.

    Az Azure PaaS-erőforrások egy Erőforrás-kezelő sablonból kiépíthetők egy környezetben, és megjelenhetnek a költségkövetésben. A virtuális gép automatikus leállítása azonban nem vonatkozik a PaaS-erőforrásokra.

    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md) |Ismerje meg, hogyan telepíthet több virtuális gépet konzisztens állapotban a tesztkörnyezethez.|

4. **Műtermékek létrehozása a virtuális gépek rugalmas testreszabásának engedélyezéséhez**

   A szolgáltatáshibák az alkalmazás üzembe helyezéséhez és konfigurálásához használatosak a virtuális gép kiépítése után. A műtermékek a következők lehetnek:

   - A virtuális gépre telepíteni kívánt eszközök – például az ügynökök, a Hegedűs és a Visual Studio.
   - A virtuális gépen futtatni kívánt műveletek – például egy tármű-klónozás.
   - A tesztelni kívánt alkalmazások.

   Számos összetevő már elérhető a beépített. De ha azt szeretné, hogy több testreszabási az ön igényeinek, létrehozhat ja saját egyéni összetevők.

   További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md) |Hozzon létre saját egyéni összetevőket a tesztkörnyezetben lévő virtuális gépekhez.|
   | [Git-tárház hozzáadása egyéni összetevők és Azure Resource Manager-sablonok tárolására az Azure DevTest Labs ben való használatra](devtest-lab-add-artifact-repo.md) |Ismerje meg, hogyan tárolhatja az egyéni összetevőket a saját privát Git-tárházban.|

5. **Ellenőrzési költségek**
   
    Az Azure DevTest Labs lehetővé teszi, hogy a laborban egy szabályzatot állítson be, amely megadja a tesztelő által a laborban létrehozható virtuális gépek maximális számát. 
   
    Ha a tesztcsapat rendelkezik egy beállított munkaütemezéssel, és le szeretné állítani az összes virtuális gépet a nap egy adott időpontjában, majd a következő napon automatikusan újra szeretné indítani őket, ezt egyszerűen elvégezheti az automatikus leállítás és az automatikus indítási házirendek beállításával a tesztkörnyezetben. 
   
    Végül, ha az alkalmazás fejlesztés befejeződött, törölheti az összes virtuális gépet egyszerre egyetlen PowerShell-parancsfájl futtatásával. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |A környezetkörnyezetházirendek beállításával szabályozhatja a költségeket. |
   | [Az összes tesztkörnyezetbeli virtuális gép törlése PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |A tesztelés befejezésekor törölje az összes művelet összes laborjait.|

1. **Virtuális hálózat hozzáadása laborhoz** 
   
    DevTest Labs létrehoz egy új virtuális hálózat (VNET) amikor egy tesztkörnyezet jön létre. Ha konfigurálta a saját virtuális hálózatát – például az ExpressRoute vagy a helyek közötti VPN használatával –, hozzáadhatja ezt a virtuális hálózatot a tesztkörnyezet virtuális hálózati beállításaihoz, hogy a virtuális gépek létrehozásakor elérhető legyen.

    Emellett van egy Azure Active Directory-tartományillesztő összetevő érhető el, amely csatlakozik a virtuális gép egy tartományhoz, amikor a virtuális gép létrehozása történik. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális hálózat konfigurálása az Azure DevTest Labs ben](devtest-lab-configure-vnet.md) |Ismerje meg, hogyan konfigurálhat virtuális hálózatot az Azure DevTest Labs az Azure Portalhasználatával.|

6. **Ossza meg a labort minden tesztelővel**
   
    A laborok közvetlenül elérhetők a tesztelőkkel megosztott hivatkozás segítségével. Még Csak nem is kell, hogy egy Azure-fiók, mindaddig, amíg van egy [Microsoft-fiók.](devtest-lab-faq.md#what-is-a-microsoft-account) A tesztelők nem látják a más tesztelők által létrehozott virtuális gépeket.  
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Tesztelő hozzáadása egy tesztkörnyezethez az Azure DevTest Labsben](devtest-lab-add-devtest-user.md) |Az Azure Portal segítségével tesztelőket adhat hozzá a laborhoz.|
   | [Tesztelők hozzáadása a laborhoz PowerShell-parancsfájl használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |A PowerShell segítségével automatizálhatja a tesztelők hozzáadása a laborban. |
   | [Hivatkozás beszerezni a labort](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogy a tesztelők hogyan férhetnek hozzá közvetlenül a laborhoz egy hivatkozáson keresztül.|

7. **A laborok létrehozásának automatizálása több csapat számára** 
   
    A laborok létrehozását , beleértve az egyéni beállításokat is, automatizálhatja egy Erőforrás-kezelő sablon létrehozásával, és azonos laborok újbóli és újra létrehozásához. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása Erőforrás-kezelő sablon nal](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Laborokat hozhat létre az Azure DevTest Labs ben az Erőforrás-kezelő sablonjainak használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

