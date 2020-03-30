---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e2d258f327ade2149adc92bdb7fb4bcc87cdd58c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179160"
---
#### <a name="to-install-the-storsimple-adapter-for-sharepoint"></a>A SharePoint-alapú StorSimple adapter telepítése
1. Másolja a telepítőt a SharePoint központi felügyeleti webalkalmazás futtatására is konfigurált webes előtér-kiszolgálóra. 
2. Rendszergazdai jogosultságokkal rendelkező fiókkal jelentkezzen be a WFE-kiszolgálóra.
3. Kattintson duplán a telepítőre. Elindul a SharePoint telepítővarázslójához használható StorSimple adapter. A telepítés megkezdéséhez kattintson a **Tovább** gombra.
   
    ![StorSimple adapter beállításának kezdőlapja](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup1-include.png)
4. A SharePoint beállítási konfigurációs lapján válasszon ki egy telepítési helyet, írja be a StorSimple-eszköz DATA 0 hálózati illesztőjének IP-címét, majd kattintson a **Tovább**gombra. 
   
    ![StorSimple adapter beállításának konfigurációs lapja](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup2-include.png) 
5. A beállítás visszaigazolása lapon kattintson a **Telepítés gombra.**
   
    ![StorSimple adapter beállításának visszaigazolási lapja](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Confirm_Setup-include.png) 
6. A Telepítő varázsló bezárásához kattintson a **Befejezés** gombra.
   
    ![A StorSimple adapter beállításának beállítása befejeződött](./media/storsimple-install-sharepoint-adapter/HCS_SSASP_Setup_finish-include.png) 
7. Nyissa meg a SharePoint központi felügyelet lapját. Meg kell jelennie egy StorSimple konfigurációs csoport, amely tartalmazza a StorSimple adapter SharePoint-kapcsolatok.
8. Folytassa a következő lépéssel: [Az RBS konfigurálása](#configure-rbs).

