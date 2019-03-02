---
title: Az Azure Cloud Shell ablak használata |} A Microsoft Docs
description: Hogyan használható az Azure Cloud Shell-ablakról áttekintése.
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
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: a02642540e6eb39f35b9cc0d38d187a7afa36b7a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243448"
---
# <a name="using-the-azure-cloud-shell-window"></a>Az Azure Cloud Shell ablak használatával

Ez a dokumentum ismerteti, hogyan használhatja a Cloud Shell ablakába.

## <a name="swap-between-bash-and-powershell-environments"></a>Váltás a Bash és a PowerShell-környezetek között
![](media/using-the-shell-window/env-selector.png)

A környezet választómezőt használja a Cloud Shell eszköztárában a Bash és a PowerShell környezetek közötti váltás.

## <a name="restart-cloud-shell"></a>Cloud Shell újraindítása
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> A Cloud Shell újraindítása visszaállítja a gép állapotát, és nem megőrzött fájlok az Azure által fájlmegosztás el fog veszni.

* Kattintson a Cloud Shell eszköztárában alaphelyzetbe állítani a gép állapota az újraindítás ikonra.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizálja a & maximalizálása érdekében a Cloud Shell-ablakról
![](media/using-the-shell-window/minmax.png)
* Kattintson a felül található kis méret ikonra kattintva rejtheti el őket az ablak, jobb. Kattintson a Cloud Shell ismét az ikonra kattintva felfedése.
* Kattintson az ablak maximális Oszlopmagasság állítsa teljes méret ikonra. Ablak előző mérete visszaállításához kattintson a visszaállítás.

## <a name="concurrent-sessions"></a>Egyidejű munkamenetek
A cloud Shell lehetővé teszi, hogy több egyidejű munkamenetek különböző böngészőlapokon azáltal, hogy létezik egy külön folyamatként Bash minden egyes munkamenetnél.
Ha egy munkamenetben kilép, ügyeljen arra, lépjen ki az egyes munkameneti ablakban, minden egyes folyamat függetlenül fut, de ugyanazon a számítógépen futnak.

## <a name="copy-and-paste"></a>Másolás és beillesztés
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Méretezze át a Cloud Shell-ablakról
* Kattintással és húzással az eszköztár felső széle felfelé vagy lefelé átméretezése a Cloud Shell ablakába.

## <a name="scrolling-text-display"></a>Görgethető szöveges megjelenítése
* Görgessen az egérrel és touchpad áthelyezése a Terminálszolgáltatások szöveget.

## <a name="changing-the-text-size"></a>A szöveg méretének módosítása
![](media/using-the-shell-window/text-size.png)
* Kattintson az ablak bal felső a beállítások ikonra, majd mutasson a "Szöveg mérete" lehetőséget, és válassza ki a kívánt szöveg méretét. A kijelölt maradnak a munkamenetek között.

## <a name="exit-command"></a>Kilépés paranccsal
Futó `exit` leállítja az aktív munkamenetét. Ez a viselkedés beavatkozás nélkül 20 perc után alapértelmezés szerint történik.

## <a name="next-steps"></a>További lépések

[A bash Cloud Shell rövid](quickstart.md)
[PowerShell Cloud Shell rövid](quickstart-powershell.md)