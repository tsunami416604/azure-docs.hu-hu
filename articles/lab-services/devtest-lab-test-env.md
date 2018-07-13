---
title: Azure DevTest Labs használatát, a virtuális gépek és PaaS-tesztelési környezetek |} A Microsoft Docs
description: Ismerje meg, hogyan használható az Azure DevTest Labs szolgáltatásban virtuális gép és a PaaS-tesztelési környezet forgatókönyvek.
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
ms.openlocfilehash: 58ab6d502ec5397604c562aedffddb9f48cbb699
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697398"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Használja az Azure DevTest Labs virtuális gépek és PaaS-tesztelési környezetek

Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a gépeket a tesztelők számára. 

Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A tesztelők gyorsan tesztelhetik az alkalmazás legújabb verzióját Windows- és Linux-környezetek újrahasznosítható sablonokkal és összetevőkkel történő kiépítésével.
- A tesztelők vertikálisan felskálázhatják a terhelésteszteket több tesztügynök kiépítésével.
- A rendszergazdák biztosításával, hogy szabályozhatja költségeit:
  - Tesztelőknek szükségük több virtuális gépet nem lehet lekérdezni.
  - Virtuális gépek leállnak, le, amikor nincs használatban.

![DevTest Labs használata képzéshez](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebben a cikkben megismerkedhet különböző Azure DevTest Labs-funkciókat, amelyekkel tesztelő követelményeknek és a részletes lépéseket hajtsa végre a tesztkörnyezet beállítása.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Az Azure DevTest Labs végrehajtási tesztelési környezetek
1. **A tesztkörnyezet létrehozása** 
   
    Laborok szerepelnek az Azure DevTest Labs szolgáltatásban. Labor létrehozása, ha feladatok végrehajtására (tesztelőknek) felhasználók bevonását a labor létrehozása, például szabályozhatja a költségeket, hozhat létre gyorsan Virtuálisgép-rendszerképek definiálása és egyéb házirendek beállítása.  
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Labor létrehozása az Azure DevTest Labs szolgáltatásban](devtest-lab-create-lab.md) |Útmutató az Azure DevTest Labs labor létrehozása az Azure Portalon. |
2. **Virtuális gépek létrehozása és előre elkészített marketplace egyéni rendszerképek segítségével percek alatt** 
   
    Válassza ki a képek számos előre elkészített rendszerképek az Azure piactéren, és elérhetővé teheti őket a tesztkörnyezetben. Ha a beépített rendszerképek nem felelnek meg igényeinek, létrehozhat egy egyéni rendszerkép lab VM egy előre elkészített rendszerkép használatával Azure piactéren, és minden szoftver telepítése, és menti a virtuális gép a labor létrehozása az egyéni rendszerkép létrehozása.

    Ha egyéni lemezképeket fogja használni, fontolja meg egy lemezkép-előállító létrehozásához és a lemezképek terjesztése. Egy rendszerkép-előállító konfigurációs a kódot, megoldás, amely rendszeresen épít, és automatikusan elosztja a konfigurált rendszerképeit is. Ez menti a rendszer manuálisan konfigurálnia a virtuális gép létrehozása az alap operációs rendszerrel után szükséges időt.
  
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Az Azure piactér képeinek konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan zajlik engedélyezett az Azure Marketplace-rendszerképek, így csak a tesztelőknek használni kívánt képeket kiválasztható.|
   | [Egyéni rendszerkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni rendszerkép előre telepíti a szoftvert, hogy a tesztelők gyorsan létre tud hozni egy virtuális Gépet az egyéni rendszerkép használatával van szüksége.|
   | [Kép factory ismertetése](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Tekintse meg, amely ismerteti, hogyan lehet egy lemezkép-előállítót, és állítsa be a videót.|

3. **Teszt gépek újrafelhasználható sablonok létrehozása** 
   
    Az Azure DevTest Labs szolgáltatásban képletet az virtuális gép létrehozásához használt alapértelmezett tulajdonságértékeket listája. A lab-ben egy képlet kiválasztásával a képet, a virtuális gép méretét (kombinációjával Processzor és memória) és egy virtuális hálózatot hozhat létre. Minden egyes tesztelő is tekintse meg a képlet a tesztkörnyezetben, és ezzel hozzon létre egy virtuális Gépet. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális gépek létrehozásához a DevTest Labs képletek kezelése](devtest-lab-manage-formulas.md) |Ismerje meg, hogyan hozhat létre egy képlet szerint vesz fel a képet, a virtuális gép mérete (Processzor és memória kombinációjával) és a egy virtuális hálózatot.|

3. **Több virtuális gépre kiterjedő tesztelési környezeteket hozhat létre** 
   
    Az Azure Resource Manager-sablonok segítségével meghatározhatja az infrastruktúráját és konfigurációját az Azure-megoldás, és ismételten több teszt virtuális gépek konzisztens állapotban.

    Azure PaaS-erőforrások kiépítése egy Resource Manager-sablon egy környezetben, és költségek nyomon jelenik meg. Virtuális gép automatikus leállítása azonban nem vonatkozik a PaaS-erőforrásokhoz.

    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Több virtuális gépes környezet és PaaS-erőforrás létrehozása Azure Resource Manager-sablonokkal](devtest-lab-create-environment-from-arm.md) |Ismerje meg, hogyan telepíthet több virtuális gép konzisztens állapotban a a tesztkörnyezetben.|

4. **Rugalmas virtuális gépek testreszabásának engedélyezése összetevők létrehozása**

   Összetevők segítségével telepítheti és konfigurálhatja az alkalmazás egy virtuális gép kiépítése után. Az összetevők lehetnek:

   - Telepítse a virtuális gép – például ügynökök, Fiddler, és a Visual Studio a kívánt eszközöket.
   - – Például a tárház klónozása az a virtuális gépen futtatni kívánt műveletek.
   - Tesztelni kívánt alkalmazások.

   Számos összetevők már eleve rendelkezésre álló-a-beépített. De ha szeretne további testreszabási igényeinek, saját egyéni összetevőket hozhat létre.

   Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md) |A virtuális gépek a saját egyéni összetevők létrehozása a lab-ben.|
   | [Egyéni összetevők és használata az Azure Resource Manager-sablonokkal tárolására az Azure DevTest Labs szolgáltatásban Git-adattár hozzáadása](devtest-lab-add-artifact-repo.md) |Ismerje meg, hogyan tárolhatja az egyéni összetevők a saját privát Git-tárház.|

5. **Költségek korlátozására**
   
    Az Azure DevTest Labs lehetővé teszi egy szabályzat beállítása a tesztkörnyezetben, adja meg, amely a laborkörnyezetben található tesztelő hozható létre virtuális gépek maximális számát. 
   
    Ha a teszt csoport rendelkezik munkahelyi ütemezés, és szeretné leállítani a virtuális gépek a nap egy adott időpontban, majd automatikusan indítsa újra őket a következő napon, egyszerűen elvégezhető, amely a laborban automatikus leállítási és automatikus indítási házirendek beállításával. 
   
    Végül alkalmazásfejlesztés befejeződése után törölheti a virtuális gépek egyszerre egy egyetlen PowerShell-parancsprogram futtatásával. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |Költségek szabályozása szabályzatok beállítása a tesztkörnyezetben. |
   | [A labor virtuális gépeken a PowerShell-szkripttel törlése](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ha a tesztelés, törölje a egyetlen művelettel Labs szolgáltatásban.|

1. **Virtuális hálózat hozzáadása egy laborhoz** 
   
    DevTest Labs hoz létre egy új virtuális hálózatot (VNET), labor létrehozásakor. Ha konfigurálta a saját virtuális hálózat – például úgy, hogy az ExpressRoute- vagy site-to-site VPN használatával – adhat hozzá a virtuális hálózat a labor virtuális hálózati beállításait, hogy legyen elérhető virtuális gépek létrehozásakor.

    Emellett van egy Azure Active Directory tartományhoz való csatlakozás összetevő érhető el, amely a virtuális gép csatlakozik egy tartományhoz, a virtuális gép létrehozásakor. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-vnet.md) |Útmutató: virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban az Azure portal használatával.|

6. **A labor megosztása minden tesztelő**
   
    Laborok közvetlenül elérhetők a tesztelők megoszt egy hivatkozással. Nem is kell rendelkeznie egy Azure-fiókra, amennyiben rendelkeznek egy [Microsoft-fiók](devtest-lab-faq.md#what-is-a-microsoft-account). Tesztelőknek más tesztelőknek által létrehozott virtuális gépek nem látható.  
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Tesztelő hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet](devtest-lab-add-devtest-user.md) |Az Azure portal használatával tesztelőknek hozzáadása a tesztkörnyezethez.|
   | [A labor létrehozása a PowerShell-szkripttel tesztelőknek hozzáadása](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |A laborhoz hozzáadásának tesztelőknek automatizálása a PowerShell használatával. |
   | [A labor létrehozása hivatkozás beszerzése](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan tesztelőknek közvetlenül hozzáférhet a labor hivatkozáson keresztül.|

7. **További csapatok labor létrehozásának automatizálása** 
   
    Labor létrehozása, egyéni beállításokat, beleértve egy Resource Manager-sablon létrehozásával, és újra és újra létre azonos labs segítségével automatizálható. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Resource Manager-sablonnal labor létrehozása](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tesztkörnyezetek létrehozása az Azure DevTest Labs szolgáltatásban létrehozott Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

