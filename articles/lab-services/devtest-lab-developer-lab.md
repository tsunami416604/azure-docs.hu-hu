---
title: Használja az Azure DevTest Labs szolgáltatásban a fejlesztők számára |} Microsoft Docs
description: 'Útmutató: Azure DevTest Labs fejlesztői helyzetekben használhatja.'
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 96432abe619ea23c1a06735567d00660e5430550
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-azure-devtest-labs-for-developers"></a>Használja az Azure DevTest Labs szolgáltatásban a fejlesztők számára
Azure DevTest Labs szolgáltatásban több kulcs forgatókönyv végrehajtásához használható, de egy elsődleges forgatókönyv magában foglalja a DevTest Labs segítségével fejlesztési gazdagépeken a fejlesztők számára. Ebben a forgatókönyvben a DevTest Labs alábbi előnyöket nyújtja:

- A fejlesztők gyorsan építhető ki az igény szerinti fejlesztési gépeik.
- A fejlesztők könnyen teste szabhatja a fejlesztési gépeikhez, amikor erre szükség van.
- A rendszergazdák szabályozhatják a költségek biztosításával, hogy:
  - A fejlesztők további virtuális gépek fejlesztési van szükségük, mint nem olvasható be.
  - Virtuális gépek leállnak le, ha nincsenek használatban. 

![DevTest Labs használatát képzés](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebből a cikkből megismerheti fejlesztői követelmények teljesítéséhez használható különböző Azure DevTest Labs-szolgáltatások és a részletes lépéseket, amelyek követésével egy tesztlabor beállításához.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Azure DevTest Labs végrehajtási fejlesztői környezetek
1. **A labor létrehozása** 
   
    Labs szerepelnek a Azure DevTest Labs szolgáltatásban. Labor létrehozása után feladatokat végezheti el például a felhasználók (fejlesztők) hozzáadása a labor szabályozására a költségek, gyorsan, hozhat létre virtuális gép lemezképek meghatározása és egyéb házirendek beállítása.  
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása a Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Útmutató a labor létrehozása a Azure DevTest Labs szolgáltatásban az Azure portálon. |
2. **Hozzon létre a virtuális gépek beépített piactéren elérhető rendszerkép és az egyéni lemezképek használatával percek alatt** 
   
    Válasszon az előre elkészített képek széles képek az Azure piactéren, és elérhetővé tétele a tesztkörnyezetben. Ha az előre elkészített lemezképeket nem megfelelnek az elvárásainak, hozzon létre egy virtuális gépet egy előre elkészített lemezkép az Azure piactérről, telepíteni a szoftvert, amelyekre szüksége van, és a virtuális gép mentése az, amikor egyéni lemezképként, amikor egyéni kép is létrehozhat.

    Ha egyéni lemezképek fog használni, érdemes lehet egy kép gyári létrehozásához, és a lemezképek terjesztése. Egy kép gyári egy olyan konfigurációs, kód megoldás, rendszeresen készítésére és a beállított képek automatikusan továbbítja. Ez menti a rendszer az alap operációs rendszer virtuális gép létrehozása után kézzel konfigurálásához szükséges időt.
  
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Azure piactéren elérhető rendszerkép konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan zajlik engedélyezett Azure piactéren elérhető rendszerkép, így kijelölésnél érhetők el a lemezképek csak kívánja a fejlesztők számára.|
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Létrehozhat egyéni rendszerképeket a szoftvert, hogy a fejlesztők gyorsan létre tud hozni egy virtuális Gépet, az egyéni lemezkép használatával kell előre telepítésével.|
   | [Kép gyári megismerése](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Bemutató videó ismerteti, hogyan állítson be, és egy kép gyári használja.|

3. **A fejlesztői gépek újrafelhasználható sablonok létrehozása** 
   
    Az Azure DevTest Labs képlet egy virtuális gép létrehozásához használt alapértelmezett tulajdonság értékek listáját. Képlet létrehozhatja a laborban lemezkép, a Virtuálisgép-méretet (kombinációja Processzor és memória szempontjából) és egy virtuális hálózatot válassza háttérszínnek. Minden egyes fejlesztői tekintse meg a képlet a laborkörnyezetben, és segítségével hozzon létre egy virtuális Gépet. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális gépek létrehozása a DevTest Labs képletek kezelése](devtest-lab-manage-formulas.md) |Ismerje meg, hogyan is létrehozhat egy képletet fel egy lemezképet, a Virtuálisgép-méretet (Processzor és memória szempontjából kombinációja) és a virtuális hálózat.|

4. **Ahhoz, hogy rugalmas VM testreszabási összetevők létrehozása**

   Összetevők segítségével telepítheti és konfigurálhatja az alkalmazás, egy virtuális gép kiépítése után. Az összetevők lehetnek:

   - A virtuális Gépen – például ügynökök, a Fiddler és a Visual Studio telepíteni kívánt eszközök.
   - A virtuális Gépen – például egy tárház klónozása futtatni kívánt műveletek.
   - Tesztelni kívánt alkalmazások.

   Sok az összetevők még elérhető, a-kész. A saját egyéni összetevők hozhat létre, ha azt szeretné, további testreszabási saját igényeinek.

   Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs szolgáltatásban virtuális Géphez](devtest-lab-artifact-author.md) |A virtuális gépek a saját egyéni összetevők létrehozása a tesztkörnyezetben.|
   | [Egyéni összetevők és az Azure Resource Manager sablonokban használható Azure DevTest Labs szolgáltatásban tárolni egy Git-tárház hozzáadása](devtest-lab-add-artifact-repo.md) |Útmutató az egyéni összetevők tárolása a saját privát Git-tárház.|

5. **Költségek szabályozása**
   
    Az Azure DevTest Labs lehetővé teszi, hogy meg kell adnia egy házirendet a laborban a, amikor a fejlesztők által létrehozott virtuális gépek maximális száma. 
   
    Ha a fejlesztői csapat tartozik egy munkahelyi ütemezést, és a virtuális gépek leállítása a nap adott időpontban, és automatikusan indítsa újra őket a következő napon, könnyen elvégezhető, amely a laborkörnyezetben automatikus leállítás be- és az automatikus indítási házirendek beállításával. 
   
    Végül alkalmazásfejlesztés befejeződése után törölheti a virtuális gépek egyszerre egy PowerShell-parancsfájl futtatásával. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |A labor házirendek beállításával kapcsolatos költségek szabályozását. |
   | [Törölje a labor virtuális gépeken, egy PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Egyetlen művelettel összes labs törölni fejlesztési befejeződött.|

1. **Virtuális hálózat hozzáadása a virtuális gépek** 
   
    DevTest Labs hoz létre egy új virtuális hálózatot (VNET), ha a labor létrehozása. Ha konfigurálta a saját virtuális Hálózatot – például használatával expressroute-on vagy a telephelyek közötti VPN – adhat hozzá a VNETET a labor virtuális hálózati beállításait úgy, hogy az elérhető virtuális gépek létrehozásakor.

    Emellett nincs Azure Active Directory tartományi csatlakozási összetevő érhető el, akkor csatlakozik egy virtuális Gépet egy tartományhoz a virtuális gép létrehozásakor. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A Azure DevTest Labs szolgáltatásban virtuális hálózat konfigurálása](devtest-lab-configure-vnet.md) |Útmutató: virtuális hálózat konfigurálása a Azure DevTest Labs szolgáltatásban az Azure portál használatával.|

6. **A labor megosztása minden fejlesztői**
   
    Labs közvetlenül elérhető a fejlesztők a megosztott kapcsolaton keresztül. Még nincs Azure-fiókot, hogy mindaddig, amíg azok rendelkeznek egy [Microsoft-fiók](devtest-lab-faq.md#what-is-a-microsoft-account). A fejlesztők más fejlesztők által létrehozott virtuális gépek nem látható.  
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Egy fejlesztő a Azure DevTest Labs szolgáltatásban labor hozzáadása](devtest-lab-add-devtest-user.md) |Az Azure-portál használatával adja hozzá a fejlesztők a laborkörnyezetben.|
   | [Adja hozzá a fejlesztők a labor egy PowerShell-parancsfájl használatával](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |PowerShell használatával automatizálhatja a labor hozzáadása fejlesztők. |
   | [Szerezzen be egy hivatkozást a laborkörnyezetben](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan fejlesztők közvetlenül hozzáférhetnek a labor hivatkozáson keresztül.|

7. **Labor létrehozása a további csapatokra automatizálása** 
   
    Labor létrehozása, egyéni beállításokat, beleértve a Resource Manager-sablonok létrehozásával, és újra és újra létre azonos labs segítségével automatizálható. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A Resource Manager sablonnal labor létrehozása](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Hozzon létre labs Azure DevTest Labs Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

