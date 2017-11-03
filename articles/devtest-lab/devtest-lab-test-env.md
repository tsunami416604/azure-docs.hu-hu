---
title: "Azure DevTest Labs használja, a virtuális gép és a PaaS tesztelési környezetben |} Microsoft Docs"
description: "Útmutató: Azure DevTest Labs szolgáltatásban virtuális gép és a PaaS tesztelési környezetben helyzetekben használhatja."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: tarcher
ms.openlocfilehash: 6926fe1be44c078482dd5073788e36d7ae77efef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>A virtuális gép és a PaaS használata Azure DevTest Labs szolgáltatásban tesztelési környezetben

Azure DevTest Labs segítségével számos főbb forgatókönyvek megvalósításához, de egy elsődleges forgatókönyv magában foglalja a DevTest Labs segítségével gazdagépeken tesztelők webhelyről. 

Ebben a forgatókönyvben a DevTest Labs alábbi előnyöket nyújtja:

- Tesztelők webhelyről által gyors kiépítése az újrafelhasználható sablonokkal és az összetevők Windows és Linux környezetben tesztelheti az alkalmazás legújabb verzióját.
- A betöltési tesztelése a kiépítés több teszt ügynök költenie tesztelők webhelyről.
- A rendszergazdák szabályozhatják a költségek biztosításával, hogy:
  - Tesztelők webhelyről van szükségük, mint további virtuális gépek nem olvasható be.
  - Virtuális gépek leállnak le, ha nincsenek használatban.

![DevTest Labs használatát képzés](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebből a cikkből megismerheti segítségével nemcsak tester követelmények és a részletes lépéseket hajtsa végre a beállíthat egy tesztkörnyezetet különböző Azure DevTest Labs szolgáltatásairól.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Az Azure DevTest Labs végrehajtási tesztkörülmények között
1. **A labor létrehozása** 
   
    Labs szerepelnek a Azure DevTest Labs szolgáltatásban. Labor létrehozása után feladatokat végezheti el például a felhasználók (tesztelők webhelyről) hozzáadása a labor szabályozására a költségek, gyorsan, hozhat létre virtuális gép lemezképek meghatározása és egyéb házirendek beállítása.  
   
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
   | [Azure piactéren elérhető rendszerkép konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan zajlik engedélyezett Azure piactéren elérhető rendszerkép, így kijelölésnél érhetők el a lemezképek csak kívánja a tesztelők webhelyről.|
   | [Egyéni lemezkép létrehozása](devtest-lab-create-template.md) |Létrehozhat egyéni rendszerképeket a szoftvert, hogy a tesztelők webhelyről gyorsan létre tud hozni egy virtuális Gépet, az egyéni lemezkép használatával kell előre telepítésével.|
   | [Kép gyári megismerése](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Bemutató videó ismerteti, hogyan állítson be, és egy kép gyári használja.|

3. **A teszt gépek újrafelhasználható sablonok létrehozása** 
   
    Az Azure DevTest Labs képlet egy virtuális gép létrehozásához használt alapértelmezett tulajdonság értékek listáját. Képlet létrehozhatja a laborban lemezkép, a Virtuálisgép-méretet (kombinációja Processzor és memória szempontjából) és egy virtuális hálózatot válassza háttérszínnek. Minden egyes tester tekintse meg a képlet a laborkörnyezetben, és segítségével hozzon létre egy virtuális Gépet. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális gépek létrehozása a DevTest Labs képletek kezelése](devtest-lab-manage-formulas.md) |Ismerje meg, hogyan is létrehozhat egy képletet fel egy lemezképet, a Virtuálisgép-méretet (Processzor és memória szempontjából kombinációja) és a virtuális hálózat.|

3. **Hozzon létre a virtuális Gépre kiterjedő tesztkörülmények között** 
   
    Azure Resource Manager-sablonok segítségével határozza meg az infrastruktúra és az Azure-megoldás konfigurációját, és több teszt virtuális gépek konzisztens állapotban ismételten telepítheti.

    Azure PaaS-erőforrások kiépítése a Resource Manager-sablon a környezetben, és költségkövetést jelennek meg. Virtuális gép automatikus leállítási azonban nem vonatkozik a PaaS erőforrásokhoz.

    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md) |Ismerje meg, hogyan telepíthet több virtuális gépek konzisztens állapotban a tesztelési környezetben.|

4. **Ahhoz, hogy rugalmas VM testreszabási összetevők létrehozása**

   Összetevők segítségével telepítheti és konfigurálhatja az alkalmazás, egy virtuális gép kiépítése után. Az összetevők lehetnek:

   - A virtuális Gépen – például ügynökök, a Fiddler és a Visual Studio telepíteni kívánt eszközök.
   - A virtuális Gépen – például egy tárház klónozása futtatni kívánt műveletek.
   - Tesztelni kívánt alkalmazások.

   Sok az összetevők még elérhető, a-kész. De ha szeretne további testreszabási igény szerinti, saját egyéni összetevők hozhat létre.

   Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs szolgáltatásban virtuális Géphez](devtest-lab-artifact-author.md) |A virtuális gépek a saját egyéni összetevők létrehozása a tesztkörnyezetben.|
   | [Egyéni összetevők és az Azure Resource Manager sablonokban használható Azure DevTest Labs szolgáltatásban tárolni egy Git-tárház hozzáadása](devtest-lab-add-artifact-repo.md) |Útmutató az egyéni összetevők tárolása a saját privát Git-tárház.|

5. **Költségek szabályozása**
   
    Az Azure DevTest Labs lehetővé teszi, hogy meg kell adnia egy házirendet a laborban a laborban a tesztelő létrehozott virtuális gépek maximális számának megadásához. 
   
    Ha a teszt csoport tartozik egy munkahelyi ütemezést, és a virtuális gépek leállítása a nap adott időpontban, és automatikusan indítsa újra őket a következő napon, könnyen elvégezhető, amely a laborkörnyezetben automatikus leállítás be- és az automatikus indítási házirendek beállításával. 
   
    Végül alkalmazásfejlesztés befejeződése után törölheti a virtuális gépek egyszerre egy PowerShell-parancsfájl futtatásával. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |A labor házirendek beállításával kapcsolatos költségek szabályozását. |
   | [Törölje a labor virtuális gépeken, egy PowerShell-parancsfájl használatával](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Törli az egyetlen művelettel Labs szolgáltatásban, ha a tesztelés.|

1. **A virtuális hálózati hozzáadása egy laborhoz** 
   
    DevTest Labs hoz létre egy új virtuális hálózatot (VNET), ha a labor létrehozása. Ha konfigurálta a saját virtuális Hálózatot – például használatával expressroute-on vagy a telephelyek közötti VPN – adhat hozzá a VNETET a labor virtuális hálózati beállításait úgy, hogy az elérhető virtuális gépek létrehozásakor.

    Emellett nincs Azure Active Directory tartományi csatlakozási összetevő érhető el, amelyhez csatlakozik egy virtuális Gépet egy tartományhoz, a virtuális gép létrehozásakor. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A Azure DevTest Labs szolgáltatásban virtuális hálózat konfigurálása](devtest-lab-configure-vnet.md) |Útmutató: virtuális hálózat konfigurálása a Azure DevTest Labs szolgáltatásban az Azure portál használatával.|

6. **A labor megosztása minden tester**
   
    Labs közvetlenül elérhetők a tesztelők webhelyről közösen-kapcsolaton keresztül. Még nincs Azure-fiókot, hogy mindaddig, amíg azok rendelkeznek egy [Microsoft-fiók](devtest-lab-faq.md#what-is-a-microsoft-account). Tesztelők webhelyről más tesztelők webhelyről által létrehozott virtuális gépek nem látható.  
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A tesztelő hozzáadása egy tesztkörnyezetet a Azure DevTest Labs szolgáltatásban](devtest-lab-add-devtest-user.md) |Az Azure-portál használatával adja hozzá a laborban tesztelők webhelyről.|
   | [A PowerShell parancsfájl használatával labor tesztelők webhelyről hozzáadása](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |PowerShell használatával automatizálhatja a laborkörnyezetben tesztelők hozzáadását webhelyről. |
   | [Szerezzen be egy hivatkozást a laborkörnyezetben](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan tesztelők webhelyről közvetlenül hozzáférhetnek a labor hivatkozáson keresztül.|

7. **Labor létrehozása a további csapatokra automatizálása** 
   
    Labor létrehozása, egyéni beállításokat, beleértve a Resource Manager-sablonok létrehozásával, és újra és újra létre azonos labs segítségével automatizálható. 
   
    Az alábbi táblázatban szereplő hivatkozásokra kattintva, ahol további:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A Resource Manager sablonnal labor létrehozása](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Hozzon létre labs Azure DevTest Labs Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

