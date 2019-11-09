---
title: A drone-képek beolvasása
description: Útmutató a drone-képek a partnerektől való lekéréséhez
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6ee4391369744f966ce273697e5ba9e7f0590edd
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890994"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Drone-képek beszerzése a drone-partnerektől

Ez a cikk azt ismerteti, hogyan lehet a orthomosaic-adatokból bevezetni a drone-Rendszerképbeli partnereiből az Azure FarmBeats-adatközpontba. Az orthomosaic egy olyan légi illusztráció/kép, amely geometriailag korrigálva van és össze van fűzve a drone által gyűjtött adatokkal.

Jelenleg a következő képkeresési partnerek támogatottak.

  ![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

A drone-képek Azure FarmBeats-mel való integrálása segít a orthomosaic adatoknak az adatközpontba való bevezetésében a farmon folytatott drone-repülőjáratokon. Miután az elérhetővé válik, megtekintheti azt a FarmBeats-gyorsító alkalmazásban, és felhasználható adatfúzióra és mesterséges intelligencia/ML-modell létrehozására is.

## <a name="before-you-begin"></a>Előkészületek

  - Győződjön meg arról, hogy telepítette az Azure FarmBeats-t. A központi telepítéshez látogasson el a [FarmBeats telepítése](prepare-for-deployment.md)című oldalra.
  - Győződjön meg arról, hogy rendelkezik a FarmBeats-rendszeren definiált farmtal (amelyhez a drone-képeket szeretné használni).

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>A FarmBeats-vel való drone-képek integrációjának engedélyezése   

A következő információkat kell megadnia az eszköz szolgáltatójának az FarmBeats való integráció engedélyezéséhez:  
 - API-végpont  
 - Bérlőazonosító  
 - Ügyfél-azonosító  
 - Ügyfél titka  

Ehhez a következő lépések szükségesek:

1. Töltse le ezt a [szkriptet](https://aka.ms/farmbeatspartnerscript) , és bontsa ki a helyi meghajtón. Ebben a ZIP-fájlban két fájl található.  
2. Jelentkezzen be [Azure Portal](https://portal.azure.com/) és nyissa meg Cloud shell (ez a lehetőség a portál jobb felső sarkában érhető el).   

    ![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Győződjön meg arról, hogy a környezet **PowerShell** -re van beállítva

    ![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Töltse fel a letöltött két fájlt (a fenti 1. lépésből) a Cloud Shell.  

    ![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Nyissa meg azt a könyvtárat, ahová a fájlokat feltöltötte. (Alapértelmezés szerint a rendszer feltölti a > a felhasználónevet a kezdőkönyvtár könyvtárába.)  
6. Futtassa a következő parancsfájlt:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. A képernyőn megjelenő utasításokat követve rögzítheti az API-végpontok, a bérlői azonosító, az ügyfél-azonosító, az ügyfél titkos kulcsa és a EventHub kapcsolódási karakterlánc értékét

    Miután megtörtént a szükséges hitelesítő adatok beírása a partner drone-szoftveres rendszerébe, importálhatja az összes farmot a FarmBeats rendszerből, és a farm részleteit használva elvégezheti a repülési útvonalak tervezését és a drone képgyűjteményt.

    Ha a nyers lemezképeket a drone-szolgáltatók szoftvere dolgozza fel, a drone szoftverrendszer feltölti a varrattal ellátható orthomosaic és más feldolgozott képeket az adatközpontba.

## <a name="view-drone-imagery"></a>A drone-képek megtekintése

Az FarmBeats adatközpontba való elküldése után a FarmBeats adatközpont API-jai segítségével le kell tudnia kérdezni a jelenet áruházat.

Azt is megteheti, hogy megtekintheti a legújabb drone-rendszerképet a **Farm részletek** lapján. A megtekintéséhez kövesse az alábbi lépéseket:  

1. Válassza ki azt a farmot, amelyhez a képeket feltöltötte. Megjelenik a **Farm** adatai lap.
2. Görgessen le a legújabb **precíziós térképek** szakaszhoz.
3. Meg kell tudnia tekinteni a képet a drone- **képek** szakaszban.

    ![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>A drone-képek letöltése

Amikor kiválasztja a drone-képek szakaszt, megnyílik egy előugró ablak, amely a drone-orthomosaic nagy felbontású képét jeleníti meg.

![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Az összes drone-Térkép megtekintése

A drone-szolgáltató által feltöltött fájlok és rendszerképek megjelennek a Maps szakaszban. Válassza ki a **Maps** szakaszt, majd a szűrés **Farm** alapján lehetőséget, és válassza ki a megfelelő fájlokat a megtekintéshez és a letöltéshez:

  ![A Project Farm veri](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan használhatja a FarmBeats adatközponti [API-kat](references-for-farmbeats.md#rest-api) a drone-képek beszerzéséhez.
