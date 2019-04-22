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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699219"
---
# <a name="using-the-azure-cloud-shell-window"></a>Az Azure Cloud Shell ablak használatával

Ez a dokumentum ismerteti, hogyan használhatja a Cloud Shell ablakába.

## <a name="swap-between-bash-and-powershell-environments"></a>Váltás a Bash és a PowerShell-környezetek között

A környezet választómezőt használja a Cloud Shell eszköztárában a Bash és a PowerShell környezetek közötti váltás.  
![Környezet kiválasztása](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Cloud Shell újraindítása
Kattintson a Cloud Shell eszköztárában alaphelyzetbe állítani a gép állapota az újraindítás ikonra.  
![Cloud Shell újraindítása](media/using-the-shell-window/restart.png)
> [!WARNING]
> A Cloud Shell újraindítása visszaállítja a gép állapotát, és nem megőrzött fájlok az Azure által fájlmegosztás el fog veszni.

## <a name="change-the-text-size"></a>A betűméret megváltoztatása
Kattintson az ablak bal felső a beállítások ikonra, majd mutasson a "Szöveg mérete" lehetőséget, és válassza ki a kívánt szöveg méretét. A kijelölt maradnak a munkamenetek között.
![Szöveg mérete](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>A betűtípus módosítása
Kattintson az ablak bal felső a beállítások ikonra, majd mutasson a "Betűtípus" beállítás, és jelölje ki a kívánt betűtípust.  A kijelölt maradnak a munkamenetek között.
![Betűtípus](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Fájlok fel- és letöltése
Kattintson az ablak bal a feltöltési/letöltési fájlok ikonra a felső ezután válassza ki, fel- vagy letöltést.  
![Fájlok fel-és letöltése](media/using-the-shell-window/uploaddownload.png)
* Fájlok feltöltése használatával előugró keresse meg a fájlt a helyi számítógépen, válassza ki a kívánt fájlt, majd kattintson a "Nyitott" gombra.  A fájl feltölti a `/home/user` könyvtár.
* Fájlok letöltéséhez, a fájl teljes elérési útját írja be az előugró ablakban, és válassza a "Letöltés" gombra.  
> [!NOTE] 
> Fájlok és elérési utat és nagybetűk a Cloud Shellben. Gondosan ellenőrizze a kis-és a fájl elérési úton.

## <a name="open-another-cloud-shell-window"></a>A Cloud Shell egy másik ablak megnyitása
A cloud Shell lehetővé teszi, hogy több egyidejű munkamenetek különböző böngészőlapokon azáltal, hogy a jelen vannak egy külön folyamat minden egyes munkamenetnél.
Ha egy munkamenetben kilép, ügyeljen arra, lépjen ki az egyes munkameneti ablakban, minden egyes folyamat függetlenül fut, de ugyanazon a számítógépen futnak.  
Kattintson az ablak bal az új munkamenet megnyitása ikonra a felső. Az a meglévő tárolóhoz csatlakoztatott egy másik munkamenet egy új lapon nyílik meg.
![Új munkamenet megnyitása](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell-szerkesztő
* Tekintse meg a [az Azure Cloud Shell-szerkesztő használatával](using-cloud-shell-editor.md) lapot.

## <a name="web-preview"></a>Webes előnézet
A webes előnézet ikonra a felső kattintson az ablak bal, válassza ki a "Konfigurálás", adja meg a kívánt port megnyitásához.  Válassza ki bármelyik "nyílt port" csak nyissa meg a portot, vagy a "nyissa meg, és keresse meg" nyissa meg a portot, és a egy új lapon port megtekintéséhez.  
![Webes előnézet](media/using-the-shell-window/preview.png)  
<br>
![Port konfigurálása](media/using-the-shell-window/preview-configure.png)  
Kattintson az ablak bal felső a webes előnézet ikon kiválasztása a "Port megtekintése..." az előzetes verzióra port megnyitása új lapon. Kattintson az ablak bal felső a webes előnézet ikon kiválasztása a "Port bezárása..." gombra kattintva zárja be a port megnyitása.  
![Előnézet/Bezárás port](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizálja a & maximalizálása érdekében a Cloud Shell-ablakról
Kattintson a felül található kis méret ikonra kattintva rejtheti el őket az ablak, jobb. Kattintson a Cloud Shell ismét az ikonra kattintva felfedése.
Kattintson az ablak maximális Oszlopmagasság állítsa teljes méret ikonra. Ablak előző mérete visszaállításához kattintson a visszaállítás.  
![Kis méretre vagy a teljes méret](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Másolás és beillesztés
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Méretezze át a Cloud Shell-ablakról
Kattintással és húzással az eszköztár felső széle felfelé vagy lefelé átméretezése a Cloud Shell ablakába.

## <a name="scrolling-text-display"></a>Görgethető szöveges megjelenítése
Görgessen az egérrel és touchpad áthelyezése a Terminálszolgáltatások szöveget.

## <a name="exit-command"></a>Kilépés paranccsal
Futó `exit` leállítja az aktív munkamenetét. Ez a viselkedés beavatkozás nélkül 20 perc után alapértelmezés szerint történik.

## <a name="next-steps"></a>További lépések

[A bash Cloud Shell rövid](quickstart.md) <br>
[A PowerShell Cloud Shell rövid](quickstart-powershell.md)