---
title: "Egyéni összetevők a DevTest Labs szolgáltatásban virtuális gép létrehozása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhatnak létre a saját Azure DevTest Labs használni kívánt összetevőket."
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: tarcher
ms.openlocfilehash: 679819618452d65847c6163569e04945ba8a414d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Egyéni összetevők a DevTest Labs szolgáltatásban virtuális gép létrehozása

Ebben a cikkben ismertetett lépések áttekintését a következő videó megtekintése:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Áttekintés
Használhat *összetevők* használatával telepítse és állítsa be az alkalmazás a virtuális gép kiépítése után. Összetevő egy összetevő csomagdefiníciós fájlt, és más parancsfájl egy Git-tárházban mappában tárolt fájlok áll. Összetevő definíciós fájlok tartalmazzák a JSON, és adja meg szeretné telepíteni a virtuális gép használó kifejezéseket. Például megadhatja összetevő, szeretne futtatni egy parancsot és paramétereket, érhetők el, ha a parancs nevét. Más parancsfájlok belül az összetevő-definíciós fájlt nevű hivatkozhat.

## <a name="artifact-definition-file-format"></a>Összetevő csomagdefiníciós fájlformátumról
A következő példa bemutatja az alapvető szerkezete egy csomagdefiníciós fájl szakaszait:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Elem neve | Kötelező? | Leírás |
| --- | --- | --- |
| $schema |Nem |A JSON-fájl helyét. A JSON-fájl segítségével ellenőrizheti a csomagdefiníciós fájl érvényességét. |
| Cím |Igen |A labor megjelenő összetevő nevét. |
| leírás |Igen |A labor megjelenő összetevő leírása. |
| iconUri |Nem |Ikon, a laborban URI Azonosítóját. |
| targetOsType |Igen |Operációs rendszer a virtuális gép, amelyen telepítve van-e az összetevő. Támogatott beállítások a Windows és Linux esetén. |
| paraméterek |Nem |Az összetevő telepítési parancs futtatásakor a gépen biztosított értékeket. Ennek segítségével testre szabhatja a összetevő. |
| ParancsFuttatása |Igen |Összetevő telepítése egy virtuális gépen végrehajtott parancs. |

### <a name="artifact-parameters"></a>Összetevő-paraméterek
A csomagdefiníciós fájl paraméterek szakaszban adja meg, mely értékei a felhasználó megadhatja összetevő telepítésekor. Ezek az értékek összetevő telepítési parancs hivatkozhat.

Paraméterek megadásához használja az alábbi szerkezettel:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Elem neve | Kötelező? | Leírás |
| --- | --- | --- |
| type |Igen |A paraméter értékének típusa. A következő listája az engedélyezett típusok. |
| displayName |Igen |A paraméter, a laborban a felhasználó számára megjelenített neve. | |
| leírás |Igen |A paraméter, a laborban megjelenített leírása. |

Engedélyezett típusok a következők:

* karakterlánc (bármilyen érvényes JSON)
* Int (bármilyen érvényes JSON egész szám lehet)
* logikai érték (bármilyen érvényes JSON logikai)
* a tömb (bármilyen érvényes JSON-tömb)

## <a name="artifact-expressions-and-functions"></a>Összetevő kifejezések és funkciók
Kifejezéseket és funkciók összeállításához összetevő telepítési parancs.
Kifejezések parancsfájlblokkjában találhatók szögletes zárójelbe ([és]), és a kiértékelésük, ha az összetevő telepítve van. Kifejezések bárhol megjelenhet egy JSON-karakterlánc. Kifejezések mindig egy másik JSON-értéket ad vissza. Ha szeretné használni a literál karakterlánc, amely egy szögletes zárójel ([]) kezdődik, két zárójelek közé ([[) kell használnia.
Általában akkor használják kifejezések függvényekkel érték létrehozásához. Ahogy a JavaScript, függvényhívások-ként formázott **functionName(arg1,arg2,arg3)**.

A következő lista mutatja azokat a közös funkciók:

* **parameters(parameterName)**: az összetevő parancs futtatásakor megadott paraméter értéket ad vissza.
* **Concat (arg1, arg2, arg3,...)** : Több karakterlánc-értékek egyesíti. Ez a funkció széles argumentumainak is igénybe vehet.

A következő példa bemutatja, hogyan értéket összeállításához kifejezések és a funkciók használata:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Hozzon létre egy egyéni összetevő

1. JSON-szerkesztő telepítése. A JSON-szerkesztő összetevő definíciós fájlok van szüksége. Azt javasoljuk, [Visual Studio Code](https://code.visualstudio.com/), elérhető a Windows, Linux vagy OS X.
2. Egy minta artifactfile.json csomagdefiníciós fájl beolvasása. Tekintse meg az összetevők hozta létre a DevTest Labs felelős csapat bevonása a [GitHub-tárházban](https://github.com/Azure/azure-devtestlab). Létrehoztunk egy gazdag könyvtár összetevők, amelyek segítségével a saját összetevők létrehozása. Töltse le egy összetevő csomagdefiníciós fájlt, és módosítja a saját összetevők létrehozásához.
3. Ellenőrizze, hogy az IntelliSense használja. IntelliSense segítségével érvényes elemek, amelyekkel egy összetevő csomagdefiníciós fájlt létrehozni. Az elem értékek a különböző lehetőségek is látható. Például, ha szerkeszti a **targetOsType** elem, az IntelliSense bemutatja, két választási lehetőség, a Windows vagy Linux.
4. Az összetevő tárolni egy [Git-tárház](devtest-lab-add-artifact-repo.md).
   
   1. Hozzon létre egy külön könyvtárat minden összetevő. A könyvtár neve legyen ugyanaz, mint az összetevő neve.
   2. Az összetevő-definíciós fájlt (artifactfile.json) tárolni a létrehozott könyvtárba.
   3. A parancsprogramok, az összetevő telepítési parancs hivatkozott tárolja.
      
      Itt látható egy példa hogyan nézhet ki egy összetevő mappára:
      
      ![Összetevő mappa – példa](./media/devtest-lab-artifact-author/git-repo.png)
5. Vegyen fel az összetevők tárházat a laborkörnyezetben. Lásd: [vegyen fel egy Git-tárházat összetevők és sablonok](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A DevTest Labs szolgáltatásban összetevő hibák diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)
* [Meglévő Active Directory-tartományban a DevTest Labs szolgáltatásban a Resource Manager-sablon használatával egy virtuális gép csatlakoztatása](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [egy Git összetevőtárban hozzáadása egy laborhoz](devtest-lab-add-artifact-repo.md).

