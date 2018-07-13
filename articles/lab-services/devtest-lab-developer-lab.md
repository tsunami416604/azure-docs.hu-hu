---
title: Használata az Azure DevTest Labs a fejlesztőknek |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure DevTest Labs-fejlesztői forgatókönyveihez.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38633281"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Az Azure DevTest Labs használata fejlesztőknek
Az Azure DevTest Labs használatával több fontos forgatókönyvet megvalósíthat, ez egyik elsődleges forgatókönyvben azonban a DevTest Lab segítségével futtatja a fejlesztői gépeket a fejlesztők számára. Ebben a forgatókönyvben a DevTest Labs az alábbi előnyöket nyújtja:

- A fejlesztők gyorsan helyezhet üzembe igény szerinti fejlesztési gépeik.
- A fejlesztők szükség esetén könnyedén testreszabhatják a fejlesztési gépeket.
- A rendszergazdák biztosításával, hogy szabályozhatja költségeit:
  - A fejlesztők fejlesztéshez szükséges több virtuális gépet nem lehet lekérdezni.
  - Virtuális gépek leállnak, le, amikor nincs használatban. 

![DevTest Labs használata képzéshez](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Ebben a cikkben megismerkedhet az Azure DevTest Labs fejlesztési követelmények teljesítéséhez használható különféle funkciókat és a részletes lépéseket, amelyek a tesztkörnyezet beállítása.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Az Azure DevTest Labs fejlesztési környezetek megvalósítása
1. **A tesztkörnyezet létrehozása** 
   
    Laborok szerepelnek az Azure DevTest Labs szolgáltatásban. Labor létrehozása, ha feladatok végrehajtására (fejlesztőknek) felhasználók bevonását a labor létrehozása, például szabályozhatja a költségeket, hozhat létre gyorsan Virtuálisgép-rendszerképek definiálása és egyéb házirendek beállítása.  
   
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
   | [Az Azure piactér képeinek konfigurálása](devtest-lab-configure-marketplace-images.md) |Megtudhatja, hogyan zajlik engedélyezett az Azure Marketplace-rendszerképek, így a választható csak azt szeretné, hogy a fejlesztők lemezképek.|
   | [Egyéni rendszerkép létrehozása](devtest-lab-create-template.md) |Hozzon létre egy egyéni rendszerkép előre telepíti a szoftvert, hogy a fejlesztők gyorsan létre tud hozni egy virtuális Gépet az egyéni rendszerkép használatával van szüksége.|
   | [Kép factory ismertetése](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Tekintse meg, amely ismerteti, hogyan lehet egy lemezkép-előállítót, és állítsa be a videót.|

3. **Fejlesztői gépek újrafelhasználható sablonok létrehozása** 
   
    Az Azure DevTest Labs szolgáltatásban képletet az virtuális gép létrehozásához használt alapértelmezett tulajdonságértékeket listája. A lab-ben egy képlet kiválasztásával a képet, a virtuális gép méretét (kombinációjával Processzor és memória) és egy virtuális hálózatot hozhat létre. Minden fejlesztő is tekintse meg a képlet a tesztkörnyezetben, és ezzel hozzon létre egy virtuális Gépet. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális gépek létrehozásához a DevTest Labs képletek kezelése](devtest-lab-manage-formulas.md) |Ismerje meg, hogyan hozhat létre egy képlet szerint vesz fel a képet, a virtuális gép mérete (Processzor és memória kombinációjával) és a egy virtuális hálózatot.|

4. **Rugalmas virtuális gépek testreszabásának engedélyezése összetevők létrehozása**

   Összetevők segítségével telepítheti és konfigurálhatja az alkalmazás egy virtuális gép kiépítése után. Az összetevők lehetnek:

   - Telepítse a virtuális gép – például ügynökök, Fiddler, és a Visual Studio a kívánt eszközöket.
   - – Például a tárház klónozása az a virtuális gépen futtatni kívánt műveletek.
   - Tesztelni kívánt alkalmazások.

   Számos összetevők már eleve rendelkezésre álló-a-beépített. A saját egyéni összetevőket hozhat létre, ha azt szeretné, további testreszabási igényeinek.

   Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Egyéni összetevők létrehozása a DevTest Labs virtuális géphez](devtest-lab-artifact-author.md) |A virtuális gépek a saját egyéni összetevők létrehozása a lab-ben.|
   | [Egyéni összetevők és használata az Azure Resource Manager-sablonokkal tárolására az Azure DevTest Labs szolgáltatásban Git-adattár hozzáadása](devtest-lab-add-artifact-repo.md) |Ismerje meg, hogyan tárolhatja az egyéni összetevők a saját privát Git-tárház.|

5. **Költségek korlátozására**
   
    Az Azure DevTest Labs lehetővé teszi egy szabályzat beállítása a tesztkörnyezetben, adja meg a fejlesztők a lab-ben létrehozott virtuális gépek maximális számát. 
   
    Ha a fejlesztői csapat működik rendelkezik ütemezés, és szeretné leállítani a virtuális gépek a nap egy adott időpontban, majd automatikusan indítsa újra őket a következő napon, egyszerűen elvégezhető, amely a laborban automatikus leállítási és automatikus indítási házirendek beállításával. 
   
    Végül alkalmazásfejlesztés befejeződése után törölheti a virtuális gépek egyszerre egy egyetlen PowerShell-parancsprogram futtatásával. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Laborszabályzatok definiálása](devtest-lab-set-lab-policy.md) |Költségek szabályozása szabályzatok beállítása a tesztkörnyezetben. |
   | [A labor virtuális gépeken a PowerShell-szkripttel törlése](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Egyetlen művelettel az összes labs fejlesztési befejezésekor törlése|

1. **Virtuális hálózat hozzáadása egy virtuális géphez** 
   
    DevTest Labs hoz létre egy új virtuális hálózatot (VNET), labor létrehozásakor. Ha konfigurálta a saját virtuális hálózat – például úgy, hogy az ExpressRoute- vagy site-to-site VPN használatával – adhat hozzá a virtuális hálózat a labor virtuális hálózati beállításait, hogy legyen elérhető virtuális gépek létrehozásakor.

    Emellett van egy Azure Active Directory tartományhoz való csatlakozás összetevő érhető el, amelyek csatlakozni fognak egy virtuális Gépet egy tartományhoz a virtuális gép létrehozásakor. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban](devtest-lab-configure-vnet.md) |Útmutató: virtuális hálózat konfigurálása az Azure DevTest Labs szolgáltatásban az Azure portal használatával.|

6. **A labor megosztása minden fejlesztő**
   
    Labs közvetlenül elérhetők a fejlesztők megoszt egy hivatkozással. Nem is kell rendelkeznie egy Azure-fiókra, amennyiben rendelkeznek egy [Microsoft-fiók](devtest-lab-faq.md#what-is-a-microsoft-account). A fejlesztők más fejlesztők által létrehozott virtuális gépek nem látható.  
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [A fejlesztő hozzáadása Azure DevTest Labs szolgáltatásban létrehozott tesztkörnyezet](devtest-lab-add-devtest-user.md) |Az Azure portal használatával a fejlesztők hozzáadása a tesztkörnyezethez.|
   | [A labor létrehozása a PowerShell-parancsfájl használatával a fejlesztők hozzáadása](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Hozzáadásának fejlesztők számára, hogy a labor automatizálása a PowerShell használatával. |
   | [A labor létrehozása hivatkozás beszerzése](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Ismerje meg, hogyan a fejlesztők közvetlenül hozzáférhet a labor hivatkozáson keresztül.|

7. **További csapatok labor létrehozásának automatizálása** 
   
    Labor létrehozása, egyéni beállításokat, beleértve egy Resource Manager-sablon létrehozásával, és újra és újra létre azonos labs segítségével automatizálható. 
   
    Tudjon meg többet az alábbi táblázatban szereplő hivatkozásokra kattintva:
   
   | Tevékenység | Ismertetett témák |
   | --- | --- |
   | [Resource Manager-sablonnal labor létrehozása](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Tesztkörnyezetek létrehozása az Azure DevTest Labs szolgáltatásban létrehozott Resource Manager-sablonok használatával. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

