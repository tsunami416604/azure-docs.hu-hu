---
title: Egyéni összetevők létrehozása a DevTest Labs virtuális gépéhez | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhatja meg saját műtermékek et az Azure DevTest Labs használatával.
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66399206"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Egyéni összetevők létrehozása a DevTest Labs virtuális gépéhez

Tekintse meg az alábbi videót a cikkben ismertetett lépések áttekintéséhez:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Áttekintés
Az összetevők használatával üzembe *helyezheti* és beállíthatja az alkalmazást, miután üzembe helyezte a virtuális gép kiépítése. A műtermék egy műtermék-definíciós fájlból és más parancsfájlokból áll, amelyek egy Git-tárházban lévő mappában tárolódnak. A műtermék-definíciós fájlok JSON-ból és kifejezésekből állnak, amelyek segítségével megadhatja, hogy mit szeretne telepíteni a virtuális gépre. Megadhatja például egy műtermék nevét, egy futtatandó parancsot és a parancs futtatásakor elérhető paramétereket. A műtermék-definíciós fájlon belül név szerint hivatkozhat más parancsfájlokra is.

## <a name="artifact-definition-file-format"></a>Műtermék-definíciós fájlformátum
A következő példa a definíciós fájl alapvető struktúráját kiteket tartalmazó szakaszokat mutatja be:

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
| $schema |Nem |A JSON-sémafájl helye. A JSON-sémafájl segítségével tesztelheti a definíciós fájl érvényességét. |
| cím |Igen |A laborban megjelenített műtermék neve. |
| leírás |Igen |A laborban megjelenített műtermék leírása. |
| ikonUri |Nem |A laborban megjelenő ikon URI-ja. |
| targetOsType típus |Igen |Annak a virtuális gépnek az operációs rendszere, ahol a műtermék telepítve van. A támogatott lehetőségek a Windows és a Linux. |
| paraméterek |Nem |A műtermék-telepítési parancs számítógépen való futtatásakor megadott értékek. Ez segít a műtermék testreszabásához. |
| runCommand parancs |Igen |A virtuális számítógépen végrehajtott műtermék-telepítési parancs. |

### <a name="artifact-parameters"></a>Műtermék-paraméterek
A definíciós fájl paraméterek szakaszában adja meg, hogy a felhasználó milyen értékeket adhat meg a műtermék telepítésekor. Ezeket az értékeket a műtermék-telepítési parancsban hivatkozhatja.

Paraméterek definiálásához használja a következő struktúrát:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Elem neve | Kötelező? | Leírás |
| --- | --- | --- |
| type |Igen |A paraméterérték típusa. Az engedélyezett típusokat az alábbi listában láthatja. |
| displayName |Igen |A tesztkörnyezetben egy felhasználó számára megjelenített paraméter neve. |
| leírás |Igen |A laborban megjelenő paraméter leírása. |

Az engedélyezett típusok a következők:

* karakterlánc (bármely érvényes JSON-karakterlánc)
* int (bármely érvényes JSON egész szám)
* bool (bármely érvényes JSON logikai)
* tömb (bármely érvényes JSON tömb)

## <a name="secrets-as-secure-strings"></a>Titkok biztonságos karakterláncként
Titkos kulcsok deklarálása biztonságos karakterláncként. Az **artifactfile.json** fájl szakaszában `parameters` található biztonságos karakterlánc-paraméter deklarálásának szintaxisa:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

A műtermék-telepítési parancs hoz a PowerShell-parancsfájl, amely a ConvertTo-SecureString paranccsal létrehozott biztonságos karakterláncot veszi igénybe. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

A teljes példa artifactfile.json és a artifact.ps1 (PowerShell-parancsfájl), tekintse meg [ezt a mintát a GitHubon.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)

Egy másik fontos megjegyzés, hogy ne naplózza a titkos kulcsokat a konzolra, mivel a kimenet a felhasználói hibakereséshez rögzítésre kerül. 

## <a name="artifact-expressions-and-functions"></a>Műtermék-kifejezések és -függvények
A műtermék-telepítési parancs létrehozásához kifejezések és függvények használhatók.
A kifejezések et szögletes zárójelek ([ és ]) zárják be, és a rendszer a műtermék telepítésekor értékeli ki. A kifejezések bárhol megjelenhetnek a JSON-karakterlánc értékében. A kifejezések mindig egy másik JSON-értéket adnak vissza. Ha olyan konstans karakterláncot kell használnia, amely szögletes zárójelgel ([) kezdődik, akkor két zárójelet kell használnia ([[).
Általában függvényekkel rendelkező kifejezéseket használ egy érték létrehozásához. Csakúgy, mint a JavaScript, függvény hívások formázott **functionName(arg1, arg2, arg3)**.

Az alábbi lista a gyakori funkciókat tartalmazza:

* **parameters(parameterName)**: A műtermék-parancs futtatásakor megadott paraméterértéket adja eredményül.
* **concat(arg1, arg2, arg3,..... )**: Több karakterlánc-értéket egyesít. Ez a függvény több érvet is felhozhat.

A következő példa bemutatja, hogyan használhatók kifejezések és függvények az értékek létrehozásához:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Egyéni összetevő létrehozása

1. Telepítsen egy JSON-szerkesztőt. Szüksége van egy JSON-szerkesztőre, hogy műtermék-definíciós fájlokkal dolgozik. A [Windows,](https://code.visualstudio.com/)Linux és OS X rendszerekhez elérhető Visual Studio Code használatát javasoljuk.
2. Szerezzen be egy minta artifactfile.json definíciós fájlt. Tekintse meg a DevTest Labs csapata által a [GitHub-tárházban létrehozott összetevőket.](https://github.com/Azure/azure-devtestlab) Hoztunk létre egy gazdag könyvtár leletek, amely segít létrehozni a saját leletek. Töltsön le egy műtermék-definíciós fájlt, és módosítsa azt a saját összetevők létrehozásához.
3. Használja ki az IntelliSense-t. Az IntelliSense segítségével megtekintheti azokat az érvényes elemeket, amelyek segítségével műtermék-definíciós fájlt hozhat létre. Az elemek értékeinek különböző beállításaiis láthatók. Például, ha szerkeszti a **targetOsType** elem, IntelliSense megmutatja két lehetőség közül választhat, a Windows vagy linux.
4. A műtárgyat a [DevTest Labs nyilvános Git-tárházban](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) vagy [a saját Git-tárházban](devtest-lab-add-artifact-repo.md)tárolja. A nyilvános tárházban megtekintheti a mások által megosztott összetevőket, amelyeket közvetlenül használhat, vagy igény szerint testreszabhatja őket.
   
   1. Hozzon létre egy külön könyvtárat minden egyes műtermékhez. A könyvtárnévnek meg kell egyeznie a műtermék nevével.
   2. Tárolja a műtermék-definíciós fájlt (artifactfile.json) a létrehozott könyvtárban.
   3. Tárolja a műtermék-telepítési parancsból hivatkozott parancsfájlokat.
      
      Íme egy példa arra, hogyan nézhet ki egy műtermékmappa:
      
      ![Példa a műtermék mappára](./media/devtest-lab-artifact-author/git-repo.png)
5. Ha saját tárháza segítségével tárolja a műtermékeket, adja hozzá a tárházat a laborhoz a cikkben található utasítások szerint: [Git-tárház hozzáadása összetevőkhez és sablonokhoz.](devtest-lab-add-artifact-repo.md)

## <a name="related-articles"></a>Kapcsolódó cikkek
* [A műtermék-hibák diagnosztizálása a DevTest Labs ben](devtest-lab-troubleshoot-artifact-failure.md)
* [Virtuális gép csatlakoztatása meglévő Active Directory-tartományhoz a DevTest Labs Erőforrás-kezelő sablonjának használatával](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [adhat hozzá git-műtermék-tárházat egy tesztkörnyezethez.](devtest-lab-add-artifact-repo.md)
