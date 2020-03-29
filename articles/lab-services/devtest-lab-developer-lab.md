---
title: Az Azure DevTest Labs használata fejlesztőknek | Microsoft dokumentumok
description: Ismerje meg az Azure DevTest Labs azon funkcióit, amelyek a fejlesztői követelmények nek való megfelelnek, valamint a labor beállításához követendő részletes lépésekről.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: ae44696f62a085bf1e798f0915e2bd1e27c3a78f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760147"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Az Azure DevTest Labs használata fejlesztőknek
Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a fejlesztői gépeket a fejlesztők számára. Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A fejlesztők gyorsan kiépíthetik fejlesztői gépeiket igény szerint.
- A fejlesztők szükség esetén könnyedén testreszabhatják a fejlesztési gépeket.
- A rendszergazdák a következők biztosításával szabályozhatják a költségeket:
  - A fejlesztők nem tudnak több virtuális gépet beszerezni, mint amennyi a fejlesztéshez szükséges.
  - A virtuális gépek leállnak, ha nincsenek használatban. 

![A DevTest Labs használata a képzéshez](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebben a cikkben megismerheti a különböző Azure DevTest Labs-funkciókat, amelyek a fejlesztői követelmények nek való megfelelnek, és a labor beállításához követendő részletes lépésekről.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Fejlesztői környezetek megvalósítása az Azure DevTest Labs alkalmazással
1. **A labor létrehozása** 
   
    Labs a kiindulási pont az Azure DevTest Labs. Miután létrehozott egy tesztkörnyezetet, olyan feladatokat hajthat végre, mint például felhasználók (fejlesztők) hozzáadása a tesztkörnyezethez, a költségek szabályozására vonatkozó házirendek beállítása, a gyorsan létrehozható virtuálisgép-lemezképek definiálása és egyebek.  
   
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
   | [Azure Marketplace-rendszerképek konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan listázhatja az Azure Marketplace-lemezképeket, és csak a fejlesztők számára kívánt képeket teheti elérhetővé.|
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni lemezképet a szükséges szoftver előzetes telepítésével, hogy a fejlesztők gyorsan létrehozhathassanak egy virtuális gépet az egyéni lemezkép használatával.|
   | [További információ a képgyárról](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Nézzen meg egy videót, amely leírja a képgyár beállítását és használatát.|

3. **Újrafelhasználható sablonok létrehozása fejlesztői gépekhez** 
   
    Az Azure DevTest Labs képlete a virtuális gép létrehozásához használt alapértelmezett tulajdonságértékek listája. Képletet hozhat létre a laborban egy lemezkép, a virtuális gép méretének (a CPU és a RAM kombinációja) és egy virtuális hálózat kiválasztásával. Minden fejlesztő láthatja a képletet a tesztkörnyezetben, és használja a virtuális gép létrehozásához. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [DevTest Labs képletek kezelése virtuális gépek létrehozásához](devtest-lab-manage-formulas.md) |Megtudhatja, hogyan hozhat létre képletet egy lemezkép, a virtuális gép méretének (a CPU és a RAM kombinációja) és a virtuális hálózat felvételével.|

4. **Műtermékek létrehozása a virtuális gépek rugalmas testreszabásának engedélyezéséhez**

   A szolgáltatáshibák az alkalmazás üzembe helyezéséhez és konfigurálásához használatosak a virtuális gép kiépítése után. A műtermékek a következők lehetnek:

   - A virtuális gépre telepíteni kívánt eszközök – például az ügynökök, a Hegedűs és a Visual Studio.
   - A virtuális gépen futtatni kívánt műveletek – például egy tármű-klónozás.
   - A tesztelni kívánt alkalmazások.

   Számos összetevő már elérhető a beépített. Létrehozhat saját egyéni összetevőket, ha több testreszabást szeretne az adott igényeknek megfelelően.

   További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md) |Hozzon létre saját egyéni összetevőket a tesztkörnyezetben lévő virtuális gépekhez.|
   | [Git-tárház hozzáadása egyéni összetevők és Azure Resource Manager-sablonok tárolására az Azure DevTest Labs ben való használatra](devtest-lab-add-artifact-repo.md) |Ismerje meg, hogyan tárolhatja az egyéni összetevőket a saját privát Git-tárházban.|

5. **Ellenőrzési költségek**
   
    Az Azure DevTest Labs lehetővé teszi, hogy a laborban egy szabályzatot állítson be, amely megadja a tesztkörnyezetben egy fejlesztő által létrehozható virtuális gépek maximális számát. 
   
    Ha a fejlesztői csapat rendelkezik egy beállított munkaütemezéssel, és le szeretné állítani az összes virtuális gépet a nap egy adott időpontjában, majd a következő napon automatikusan újra szeretné indítani őket, ezt egyszerűen elvégezheti az automatikus leállítás és az automatikus indítási házirendek beállításával a tesztkörnyezetben. 
   
    Végül, ha az alkalmazás fejlesztés befejeződött, törölheti az összes virtuális gépet egyszerre egyetlen PowerShell-parancsfájl futtatásával. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |A környezetkörnyezetházirendek beállításával szabályozhatja a költségeket. |
   | [Az összes tesztkörnyezetbeli virtuális gép törlése PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Törölje az összes laborok egy műveletben, ha a fejlesztés befejeződött.|

1. **Virtuális hálózat hozzáadása virtuális géphez** 
   
    DevTest Labs létrehoz egy új virtuális hálózat (VNET) amikor egy tesztkörnyezet jön létre. Ha konfigurálta a saját virtuális hálózatát – például az ExpressRoute vagy a helyek közötti VPN használatával –, hozzáadhatja ezt a virtuális hálózatot a tesztkörnyezet virtuális hálózati beállításaihoz, hogy a virtuális gépek létrehozásakor elérhető legyen.

    Emellett van egy Azure Active Directory-tartományillesztő összetevő érhető el, amely csatlakozik a virtuális gép egy tartományhoz, amikor a virtuális gép létrehozása történik. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális hálózat konfigurálása az Azure DevTest Labs ben](devtest-lab-configure-vnet.md) |Ismerje meg, hogyan konfigurálhat virtuális hálózatot az Azure DevTest Labs az Azure Portalhasználatával.|

6. **A labor megosztása az egyes fejlesztőkkel**
   
    A laborok közvetlenül elérhetők a fejlesztőkkel megosztott hivatkozás segítségével. Még Csak nem is kell, hogy egy Azure-fiók, mindaddig, amíg van egy [Microsoft-fiók.](devtest-lab-faq.md#what-is-a-microsoft-account) A fejlesztők nem láthatják a más fejlesztők által létrehozott virtuális gépeket.  
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Fejlesztő hozzáadása egy tesztkörnyezethez az Azure DevTest Labsben](devtest-lab-add-devtest-user.md) |Az Azure Portal segítségével fejlesztőket vehet fel a laborba.|
   | [Fejlesztők hozzáadása a laborhoz PowerShell-parancsfájl használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |A PowerShell segítségével automatizálhatja a fejlesztők hozzáadása a laborban. |
   | [Hivatkozás beszerezni a labort](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan férhetnek hozzá a fejlesztők közvetlenül egy tesztkörnyezethez egy hivatkozáson keresztül.|

7. **A laborok létrehozásának automatizálása több csapat számára** 
   
    A laborok létrehozását , beleértve az egyéni beállításokat is, automatizálhatja egy Erőforrás-kezelő sablon létrehozásával, és azonos laborok újbóli és újra létrehozásához. 
   
    További információ az alábbi táblázatban található hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása Erőforrás-kezelő sablon nal](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Laborokat hozhat létre az Azure DevTest Labs ben az Erőforrás-kezelő sablonjainak használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

