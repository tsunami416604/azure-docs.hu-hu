---
title: A DevTest Labs szolgáltatásban virtuális gép egyéni összetevők létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre saját összetevők az Azure DevTest Labs használatával.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 05abc61da7af02c56dacd632175d6fbfa64cb9e1
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098561"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>A DevTest Labs szolgáltatásban virtuális gép egyéni összetevők létrehozása

Ebben a cikkben leírt lépések áttekintését a következő videó megtekintése:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Áttekintés
Használhat *összetevők* üzembe helyezéséhez és a egy virtuális gép létrehozása után az alkalmazás beállítása. Egy összetevő egy összetevő-definíciós fájlt, és más parancsfájl a Git-tárház mappájában tárolt fájlok áll. Összetevő-definíciós fájlokat, a JSON és kifejezések, amelyek segítségével adja meg a kívánt virtuális Gépre telepíthető áll. Definiálhat például egy összetevő, a futtatni kívánt parancs és a parancs futtatásakor elérhető paramétereket nevét. Az összetevő-definíciós fájl belül más parancsfájlok név alapján hivatkozhat.

## <a name="artifact-definition-file-format"></a>Összetevő csomagdefiníciósfájl-formátumról
Az alábbi példa bemutatja az alapvető szerkezete egy csomagdefiníciós fájl alkotó szakaszokban:

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
| cím |Igen |A lab-ben jelenik meg az összetevő neve. |
| leírás |Igen |A lehívandó összetevő jelenik meg a labor leírása. |
| iconUri |Nem |URI-ját az ikon jelenik meg a tesztkörnyezetben. |
| targetOsType |Igen |Az operációs rendszer a virtuális gép, amelyen telepítve van-e az összetevő. Támogatott kapcsolók a következők: Windows és Linux rendszereken. |
| paraméterek |Nem |Az összetevő telepítési parancs egy gépen való futtatásakor biztosított értékeket. Ennek segítségével testre szabhatja az összetevő. |
| runCommand |Igen |Összetevő telepítése a virtuális gépen végrehajtott parancs. |

### <a name="artifact-parameters"></a>Összetevő paraméterei
A csomagdefiníciós fájl paraméterei területen adja meg, mely értékeket a felhasználó megadhatja egy összetevő telepítésekor. Ezeket az értékeket a összetevő telepítési parancshoz hivatkozhatunk.

Paraméterek megadásához használja az alábbi struktúrával:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Elem neve | Kötelező? | Leírás |
| --- | --- | --- |
| type |Igen |A paraméter értékének típusa. Tekintse meg az alábbi lista az engedélyezett típusok esetében. |
| displayName |Igen |A lab-ben a felhasználó számára megjelenő paraméter neve. | |
| leírás |Igen |A lab-ben megjelenő paraméter leírása. |

Engedélyezett típusok a következők:

* karakterlánc (bármilyen érvényes JSON-karakterlánc)
* Int (bármilyen érvényes JSON egész szám)
* logikai (bármilyen érvényes JSON logikai)
* tömb (bármilyen érvényes JSON-tömböt)

## <a name="artifact-expressions-and-functions"></a>Összetevő-kifejezések és függvények
Használhat kifejezéseket, és a függvények létrehozására, az összetevő telepítési parancs.
Kifejezések parancsfájlblokkjában találhatók a zárójelek közé ([és]), és az összetevő telepítésekor a rendszer értékeli. Kifejezések bárhol megjelenhet egy JSON-karakterlánc értéken. Kifejezések mindig egy másik JSON-értéket ad vissza. Ha szeretné használni a szögletes zárójel ([]) kezdetű konstans sztring, két zárójelek közé ([[) kell használnia.
Általában használatával kifejezések függvényekkel hozhat létre egy értéket. Ugyanúgy, mint a JavaScript, függvényhívások formázott **functionName (arg1, arg2, arg3)**.

Az alábbi lista tartalmazza a közös funkciók:

* **parameters(parameterName)**: Az összetevő parancs futtatásakor megadott paraméter értéket ad vissza.
* **Concat (arg1, arg2, arg3,...)** : Több karakterlánc-értékek egyesíti. Ez a függvény argumentumainak különböző is igénybe vehet.

Az alábbi példa bemutatja, hogyan hozhat létre egy érték kifejezések és függvények használatával:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Egyéni összetevő létrehozása

1. Telepítse a JSON-szerkesztővel. Szüksége lesz egy JSON-szerkesztő használata összetevő definíciós fájlokat. Azt javasoljuk, [Visual Studio Code](https://code.visualstudio.com/), amely Windows, Linux- és OS X érhető el.
2. Egy mintául szolgáló artifactfile.json csomagdefiníciós fájl beolvasása. Tekintse meg a DevTest Labs csapat által létrehozott összetevők a [GitHub-adattár](https://github.com/Azure/azure-devtestlab). Az összetevők, amelyek segítségével egyéni összetevők létrehozása könyvtár gazdag hoztunk létre. Töltse le egy összetevő-definíciós fájlt, és hajtsa végre a módosításokat a saját összetevőket hozhat létre ahhoz.
3. Ellenőrizze, hogy az IntelliSense használatát. Az IntelliSense segítségével megtekintheti az érvényes elemeket, amelyek segítségével hozhat létre egy összetevő-definíciós fájlt. A különböző lehetőségek értékek elem is látható. Például, ha szerkeszti a **targetOsType** elem, az IntelliSense bemutatja, két választási lehetőség, Windows vagy Linux.
4. Az összetevő Store a [DevTest Labs nyilvános Git-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) vagy [saját Git-tárház](devtest-lab-add-artifact-repo.md). A nyilvános tárházban megtekintheti, hogy közvetlenül használni, vagy testre is szabhatja őket, hogy illeszkedjen az igényeihez mások által megosztott összetevők.
   
   1. Hozzon létre minden egyes összetevő külön könyvtárat. A könyvtár neve ugyanaz, mint a szerelvényösszetevő nevének kell lennie.
   2. Az összetevő-definíciós fájl (artifactfile.json) Store hozott létre a címtárban.
   3. A parancsfájlok, az összetevő telepítési parancs által hivatkozott Store.
      
      Íme egy példa, hogyan kell egy összetevő mappa kinéznie:
      
      ![Összetevő mappa példa](./media/devtest-lab-artifact-author/git-repo.png)
5. Ha a saját tárház összetevők tárolására használ, hozzáadása a tárház a labor létrehozása a cikkben található utasításokat követve: [Adjon hozzá egy Git-tárház összetevők és sablonok](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A DevTest Labs szolgáltatásban az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)
* [Meglévő Active Directory-tartomány egy Resource Manager-sablon használatával a DevTest Labs szolgáltatásban létrehozott virtuális gépek csatlakoztatása](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [egy Git-tárház összetevő hozzáadása egy laborhoz](devtest-lab-add-artifact-repo.md).
