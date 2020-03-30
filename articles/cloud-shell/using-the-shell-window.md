---
title: Az Azure Cloud Shell ablakának használata | Microsoft dokumentumok
description: Az Azure Cloud Shell ablak használatának áttekintése.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70860324"
---
# <a name="using-the-azure-cloud-shell-window"></a>Az Azure Cloud Shell ablakának használata

Ez a dokumentum bemutatja, hogyan használhatja a Cloud Shell ablakot.

## <a name="swap-between-bash-and-powershell-environments"></a>Bash- és PowerShell-környezetek közötti felváltás

Használja a környezet választó a Cloud Shell eszköztár bash és powershell-környezetek közötti felcseréléshez.  
![Környezet kiválasztása](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>A Felhőrendszerhéj újraindítása
Kattintson az újraindítás ikonra a Cloud Shell eszköztáron a számítógép állapotának visszaállításához.  
![A Felhőrendszerhéj újraindítása](media/using-the-shell-window/restart.png)
> [!WARNING]
> A Cloud Shell újraindítása alaphelyzetbe állítja a számítógép állapotát, és az Azure-fájlmegosztás által nem megőrzött fájlok elvesznek.

## <a name="change-the-text-size"></a>A szöveg méretének módosítása
Kattintson az ablak bal felső részén található beállítások ikonra, majd mutasson a "Szövegméret" gombra, és válassza ki a kívánt szövegméretet. A választás a munkamenetek között megmarad.
![Szöveg mérete](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>A betűtípus módosítása
Kattintson az ablak bal felső részén található beállítások ikonra, majd mutasson a "Betűtípus" lehetőségre, és válassza ki a kívánt betűtípust.  A választás a munkamenetek között megmarad.
![Betűtípus](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Fájlok fel- és letöltése
Kattintson a fájlok feltöltése/letöltésikonjára az ablak bal felső részén, majd válassza a feltöltés vagy a letöltés lehetőséget.  
![Fájlok feltöltése/letöltése](media/using-the-shell-window/uploaddownload.png)
* Fájlok feltöltéséhez az előugró ablakban keresse meg a fájlt a helyi számítógépen, jelölje ki a kívánt fájlt, és kattintson a "Megnyitás" gombra.  A fájl feltöltődik `/home/user` a könyvtárba.
* A fájl letöltéséhez írja be a teljesen minősített fájl elérési útját az előugró ablakba (azaz alapvetően egy elérési utat a `/home/user` könyvtár alatt, amely alapértelmezés szerint megjelenik), és válassza a "Letöltés" gombot.  
> [!NOTE] 
> A fájlok és a fájlelérési utak a Cloud Shellben a kis- és nagybetűket megkülönböztetők. Ellenőrizze a burkolatot a fájl elérési útján.

## <a name="open-another-cloud-shell-window"></a>Másik Cloud Shell-ablak megnyitása
A Cloud Shell több egyidejű munkamenetet tesz lehetővé a böngészőlapokon keresztül, lehetővé téve, hogy minden munkamenet külön folyamatként létezzen.
Ha kilép egy munkamenetből, mindenképpen lépjen ki az egyes munkamenet-ablakokból, mivel az egyes folyamatok egymástól függetlenül futnak, bár ugyanazon a gépen futnak.  
Kattintson az ablak bal felső részén található új munkamenet megnyitása ikonra. Egy új lap nyílik meg, amelyen egy másik munkamenet csatlakozik a meglévő tárolóhoz.
![Új munkamenet megnyitása](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell-szerkesztő
* Tekintse meg [az Azure Cloud Shell szerkesztőjének használata](using-cloud-shell-editor.md) lapot.

## <a name="web-preview"></a>Webes előnézet
Kattintson az ablak bal felső részén található webes előnézeti ikonra, válassza a "Beállítás" lehetőséget, és adja meg a megnyitni kívánt portot.  Válassza a "Port megnyitása" lehetőséget a port megnyitásához, vagy a "Megnyitás és tallózás" lehetőséget a port megnyitásához és a port új lapon való megtekintéséhez.  
![Webes előnézet](media/using-the-shell-window/preview.png)  
<br>
![Port konfigurálása](media/using-the-shell-window/preview-configure.png)  
Kattintson az ablak bal felső részén található webes előnézetikonra, és válassza az "Előnézeti port ..." lehetőséget. egy megnyitott port előnézetének megtekintéséhez egy új lapon. Kattintson az ablak bal felső részén található webes előnézetikonra, és válassza a "Port bezárása ..." lehetőséget. a nyitott port bezárásához.  
![Port előnézete/bezárása](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>A felhőhéj-ablak & maximalizálása
Az ablak jobb felső részén található kis méretikonra kattintva elrejtheti azt. A felfedéshez kattintson ismét a Cloud Shell ikonra.
Kattintson a teljes méret ikonra az ablak maximális magasságának beállításához. Az ablak korábbi méretének visszaállításához kattintson a Visszaállítás gombra.  
![Az ablak kis méretre állítása vagy teljes méretre állítása](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Másolás és beillesztés
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Felhőhéj-ablak átméretezése
Kattintson az eszköztár felső szélére, és húzza lefelé a Felhőhéj ablak átméretezéséhez.

## <a name="scrolling-text-display"></a>Szöveg megjelenítése
Görgessen az egérrel vagy az érintőpárnával a terminál szövegének mozgatásához.

## <a name="exit-command"></a>Kilépés parancs
A `exit` futás megszakítja az aktív munkamenetet. Ez a viselkedés alapértelmezés szerint 20 perc után, interakció nélkül fordul elő.

## <a name="next-steps"></a>További lépések

[Bash a Cloud Shell rövid útmutató](quickstart.md) <br>
[PowerShell a Cloud Shell rövid útmutatójában](quickstart-powershell.md)
