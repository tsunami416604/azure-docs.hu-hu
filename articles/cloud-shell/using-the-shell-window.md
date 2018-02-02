---
title: "Azure Cloud Shell ablakában |} Microsoft Docs"
description: "Hogyan használható az Azure felhőalapú rendszerhéj ablakát áttekintése."
services: azure
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: 43da2bf5b66ff7db03a6fb5c2e1ceaebe322bcbb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="using-the-azure-cloud-shell-window"></a>Az Azure felhőalapú rendszerhéj ablakát használatával

Ez a dokumentum ismerteti, hogyan a felhő rendszerhéj ablakát.

## <a name="swap-between-bash-and-powershell-environments"></a>A felcserélendő Bash és PowerShell különböző környezetek között
![](media/using-the-shell-window/env-selector.png)

A felhő rendszerhéj eszköztár a környezet választó segítségével felcserélni a Bash és PowerShell különböző környezetek között.

## <a name="restart-cloud-shell"></a>Cloud Shell újraindítása
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Felhő rendszerhéj újraindítása alaphelyzetbe állítja a gép állapotát, és semmilyen fájl nem őrződik meg a Azure fájlmegosztás el fog veszni.

* Kattintson az újraindítás ikonra a felhő rendszerhéj eszköztárban alaphelyzetbe állítani a gép állapotát.

## <a name="minimize--maximize-cloud-shell-window"></a>Kis méret & felhő rendszerhéj ablakának teljes méretűre állítása
![](media/using-the-shell-window/minmax.png)
* Kattintson a kis méret ikonra a felső sarkában az ablak elrejtéséhez. Kattintson a felhő rendszerhéj ismét az ikonra kattintva felfedése.
* Kattintson a teljes méret ikonra beállítása ablakban maximális magasságát. Ablak visszaállítása eredeti méretének, kattintson a visszaállítás.

## <a name="concurrent-sessions"></a>Egyidejű munkamenetek
Felhő rendszerhéj lehetővé teszi több egyidejű munkamenetek különböző böngészőlapokon távfelügyeletét minden munkamenet nem létezik-e külön Bash folyamatban.
Ha most kilép egy munkamenet, ügyeljen arra, hogy minden munkamenet ablak lépni, minden folyamat függetlenül fut, de ugyanazon a számítógépen futnak.

## <a name="copy-and-paste"></a>Másolás és beillesztés
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Felhő rendszerhéj ablakát átméretezése
* Kattintással és húzással a felső szegélyéhez, hogy az eszköztár felfelé vagy lefelé méretezze át a felhő rendszerhéj ablakát.

## <a name="scrolling-text-display"></a>A görgethető szöveg megjelenítése
* Görgessen az egérrel és touchpad terminál szöveg áthelyezése.

## <a name="changing-the-text-size"></a>A betűméret megváltoztatása
![](media/using-the-shell-window/text-size.png)
* A beállítások ikonra a felső bal oldali ablak, kattintson a "Text" beállítás rámutat, majd válassza ki a kívánt szöveget méretet. A kiválasztott maradnak munkamenetei között.

## <a name="exit-command"></a>Kilépés paranccsal
Futó `exit` megszakítja az aktív munkamenetet. Alapértelmezés szerint ez a viselkedés beavatkozás nélkül 20 perc után következik be.

## <a name="next-steps"></a>További lépések

[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md)
[PowerShell használatával a felhő rendszerhéj gyors üzembe helyezés](quickstart-powershell.md)