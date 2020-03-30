---
title: Drónképek lekérése
description: Ez a cikk ismerteti, hogyan kaphat drón képeket a partnerektől.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3e452cd548738e5f211899d3a6a676f883d800ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132050"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Drónképek beszerezni a drónpartnereket

Ez a cikk ismerteti, hogyan hozhat be ortomosaic adatokat a drone imagery partnerek az Azure FarmBeats Datahub. Az ortomozaik egy légi illusztráció vagy kép, amelyet geometriailag korrigálnak és varrnak egy drón által gyűjtött adatokból.

Jelenleg a következő képkészítő partnerek támogatottak.

  ![FarmBeats drone képkészítő partnerek](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Drónképadatok integrálása az Azure FarmBeats segítségével ortomozaik adatokat a farmon végzett drónjáratokról a datahubra. Miután az adatok rendelkezésre állnak, megtekintheti azokat a FarmBeats gyorsítóban. Az adatok adatfúzióhoz, valamint a mesterséges intelligencia és a gépi tanulási modellépítéshez használhatók.

## <a name="before-you-begin"></a>Előkészületek

  - Győződjön meg arról, hogy telepítette az Azure FarmBeats.Make sure that you installed Azure FarmBeats. A FarmBeats telepítéséről az [Azure FarmBeats telepítése című](install-azure-farmbeats.md)témakörben talál további információt.
  - Győződjön meg arról, hogy rendelkezik azzal a farmgal, amelyhez a FarmBeats rendszerben definiálni szeretné a drónképeket.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Drónképek integrációjának engedélyezése a FarmBeats-szel

A FarmBeats alkalmazással való integráció engedélyezéséhez adja meg a következő adatokat az eszközszolgáltatónak:
 - API-végpont
 - Bérlőazonosító
 - Ügyfél-azonosító
 - Titkos ügyfélkulcs

Kövesse az alábbi lépéseket.

1. Töltse le ezt a [szkriptet](https://aka.ms/farmbeatspartnerscript), és bontsa ki a helyi meghajtóra. Két fájl van a zip fájlban.
2. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/), és nyissa meg az Azure Cloud Shellt. Ez a beállítás a portál jobb felső sarkában lévő eszköztáron érhető el.

    ![Nyissa meg az Azure Cloud Shellt a portál jobb felső sávján](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Győződjön meg arról, hogy a környezet **PowerShell**re van állítva.

    ![PowerShell-beállítás](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Töltse fel a Cloud Shell-példány 1.

    ![Fájlok feltöltése](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Nyissa meg azt a könyvtárat, ahová a fájlokat feltöltötték. Alapértelmezés szerint a rendszer a felhasználónév alatt tölti fel őket a kezdőkönyvtárba.
6. Futtassa a következő parancsfájlt:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. Kövesse a képernyőn megjelenő utasításokat az API-végpont, a bérlőazonosító, az ügyfélazonosító, az ügyféltitkos kulcs és az EventHub-kapcsolati karakterlánc értékeinek rögzítéséhez.

    Miután megadta a szükséges hitelesítő adatokat a partner drón szoftverrendszerébe, importálhatja az összes farmot a FarmBeats rendszerből. Ezután használhatja a farm adatait a repülési útvonal tervezéséhez és a drone képgyűjteményéhez.

    Miután a drónszolgáltatók szoftvere feldolgozta a nyers képeket, a drónszoftver-rendszer feltölti a varrott ortomozaikot és más feldolgozott képeket a datahubra.

## <a name="view-drone-imagery"></a>Drónképek megtekintése

Miután az adatokat elküldte a FarmBeats datahub, lekérdezheti a Scene Store segítségével FarmBeats Datahub API-k.

Másik lehetőségként megtekintheti a legújabb drone képet a **Farm részletei** oldalon. A kép megtekintéséhez kövesse a lépéseket.

1. Válassza ki azt a farmot, ahhoz a farmhoz, ahamelybe a képeket feltöltötte. Megjelenik **a Farm** részletei lap.
2. Görgessen le a legújabb **Precíziós térképek** szakaszhoz.
3. Tekintse meg a képet a **Drone Képek** részben.

    ![Drone Képek szakasz](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Drónképek letöltése

Amikor kiválasztja a Drone Imagery szakaszt, megnyílik egy előugró ablak, amely a drón ortomozaik nagy felbontású képét jeleníti meg.

![Nagy felbontású ortomozaik](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Az összes dróntérkép megtekintése

A drónszolgáltató által feltöltött fájlok és képek a **Térképek** szakaszban jelennek meg. Jelölje ki a **Térképek** szakaszt, szűrjön **farm**szerint, és válassza ki a megtekinteni és letölteni kívánt fájlokat.

  ![Térképek szakasz](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használhatja a FarmBeats Datahub [API-kat](rest-api-in-azure-farmbeats.md) a drone-képek lefelvételéhez.
