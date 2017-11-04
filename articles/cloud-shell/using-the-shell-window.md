---
title: "Az Azure felhőalapú rendszerhéj (előzetes verzió) ablakban |} Microsoft Docs"
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
ms.date: 10/16/2017
ms.author: juluk
ms.openlocfilehash: 59cd03f6e04e9b096fe740e8a7140e602c0baa58
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2017
---
# <a name="using-the-azure-cloud-shell-window"></a>Az Azure felhőalapú rendszerhéj ablakát használatával

Ez a dokumentum ismerteti, hogyan a felhő rendszerhéj ablakát.

## <a name="swap-between-bash-and-powershell-environments"></a>A felcserélendő Bash és PowerShell különböző környezetek között
![](media/using-the-shell-window/env-selector.png)

A felhő rendszerhéj eszköztár a környezet választó segítségével felcserélni a Bash és PowerShell különböző környezetek között.

## <a name="restart-cloud-shell"></a>Indítsa újra a felhő rendszerhéj
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Felhő rendszerhéj újraindítása alaphelyzetbe állítja a gép állapotát, és semmilyen fájl nem őrződik meg a fájl megosztási el fog veszni.

* Kattintson az újraindítás ikonra a felhő rendszerhéj eszköztárban alaphelyzetbe állítani a gép állapotát.

## <a name="minimize--maximize-cloud-shell-window"></a>Kis méret & felhő rendszerhéj ablakának teljes méretűre állítása
![](media/using-the-shell-window/minmax.png)
* Kattintson a kis méret ikonra a felső sarkában az ablak elrejtéséhez. Kattintson a felhő rendszerhéj ismét az ikonra kattintva felfedése.
* Kattintson a teljes méret ikonra beállítása ablakban maximális magasságát. Ablak visszaállítása eredeti méretének, kattintson a visszaállítás.

## <a name="concurrent-sessions"></a>Egyidejű munkamenetek
Felhő rendszerhéj lehetővé teszi több egyidejű munkamenetek különböző böngészőlapokon távfelügyeletét minden munkamenet nem létezik-e külön Bash folyamatban.
Ha most kilép egy munkamenet, ügyeljen arra, hogy minden munkamenet ablak lépni, minden folyamat függetlenül fut, de ugyanazon a számítógépen futnak.

## <a name="copy-and-paste"></a>Másolás és beillesztés
[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Felhő rendszerhéj ablakát átméretezése
* Kattintással és húzással a felső szegélyéhez, hogy az eszköztár felfelé vagy lefelé méretezze át a felhő rendszerhéj ablakát.

## <a name="scrolling-text-display"></a>A görgethető szöveg megjelenítése
* Görgessen az egérrel és touchpad terminál szöveg áthelyezése.

## <a name="changing-the-text-size"></a>A betűméret megváltoztatása
![](media/using-the-shell-window/text-size.png)
* A beállítások ikonra a felső bal oldali ablak, kattintson a "Text" beállítás rámutat, majd válassza ki a kívánt szöveget méretet. A kiválasztott maradnak munkamenetei között.

## <a name="exit-command"></a>Kilépés paranccsal
Futó `exit` megszakítja az aktív munkamenetet. Alapértelmezés szerint ez a viselkedés beavatkozás nélkül 20 perc után következik be.

## <a name="next-steps"></a>Következő lépések

[A felhő rendszerhéj gyors üzembe helyezés bash](quickstart.md)
[PowerShell használatával a felhő rendszerhéj gyors üzembe helyezés](quickstart-powershell.md)