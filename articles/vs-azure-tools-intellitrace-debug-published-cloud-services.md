---
title: Hibakeresés a közzétett egy Azure felhőszolgáltatást a Visual Studio és az IntelliTrace |} A Microsoft Docs
description: Ismerje meg, a Visual Studio és az IntelliTrace cloud service hibakeresése
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.custom: vs-azure
ms.workload: azure-vs
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: df361d1721e3a437294a21129acb0ad8c2093630
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969425"
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>A Visual Studio és az IntelliTrace közzétett Azure felhőszolgáltatás hibakeresést
IntelliTrace, a széles körű hibakeresési információkat egy szerepkörpéldány számára az Azure-ban való futáskor jelentkezhet meg. Ha a probléma okának feltárásához van szüksége, az IntelliTrace-naplók segítségével végighaladhat a kód a Visual Studióból, ha azt az Azure-ban futó. Érvényben az IntelliTrace kulccsal rekordok programkód és környezeti adatok az Azure-alkalmazásokat az Azure-ban felhőszolgáltatásként fut, és beállíthatja, a Visual Studióból a rögzített adatokat. 

IntelliTrace, ha van telepítve a Visual Studio Enterprise és az Azure-alkalmazás célok .NET-keretrendszer 4 vagy újabb verzió használható. IntelliTrace gyűjt adatokat az Azure-szerepkörök számára. Ezek a szerepkörök minden esetben a 64 bites operációs rendszereket futtató virtuális gépekhez.

Alternatív megoldásként használható [távoli hibakeresés](http://go.microsoft.com/fwlink/p/?LinkId=623041) közvetlenül egy felhőszolgáltatás, amely az Azure-ban fut-e csatolni.

> [!IMPORTANT]
> IntelliTrace csak hibakeresési célokra szolgál, és nem használható éles környezet.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Azure-alkalmazások konfigurálása az IntelliTrace
IntelliTrace engedélyezése az Azure-alkalmazások, hozzon létre, és közzé az alkalmazást a Visual Studióhoz kapcsolódó Azure-projektből. IntelliTrace kell konfigurálnia az Azure-alkalmazásokat, az Azure-ba való közzététel előtt. Ha az alkalmazás nem az IntelliTrace konfigurálása, kell tegye közzé ismét a projektet. További információkért lásd: [közzététele az Azure cloud services-projektek, a Visual Studio használatával](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Készen áll az Azure-alkalmazás üzembe helyezése, győződjön meg arról, hogy a vannak beállítva a project build célok **Debug**.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projekt, és a helyi menüből válassza ki a **közzététel**.
   
1. Az a **Azure-alkalmazások közzététele** párbeszédpanel, válassza ki az Azure-előfizetést, és válassza ki **tovább**.

1. Az a **beállítások** lapon válassza ki a **speciális beállítások** fülre.

1. Kapcsolja be a **IntelliTrace engedélyezése** beállítás gyűjtése az IntelliTrace-naplók az alkalmazáshoz, ha azt közzé van téve a felhőben.
   
1. Az IntelliTrace-alapkonfiguráció testreszabásához válassza **beállítások** melletti **IntelliTrace engedélyezése**.

    ![Beállítások hivatkozására az IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. Az a **nastavení IntelliTrace** párbeszédpanelen megadhatja napló, hívás információkat gyűjtsön-e, melyik modulokat és a folyamatok gyűjthet naplókat, és mekkora lemezterület lefoglalása megkapni a felvételre mutató eseményeket. Intellitrace alkalmazással kapcsolatban további információkért lásd: [intellitrace-hibakeresés](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![Nastavení IntelliTrace](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Az IntelliTrace-naplót (az alapértelmezett mérete 250 MB) az IntelliTrace-beállításaiban megadott maximális méretét. kör alakú naplófájlt. IntelliTrace-naplók összegyűjtése a fájlrendszer, a virtuális gép egy fájlba. A naplók kér, amikor egy pillanatkép ezen a ponton időben végrehajtott és a helyi számítógépre letöltött.

Miután az Azure-felhőszolgáltatásban közzétette az Azure-ba, megadhatja, hogy ha IntelliTrace engedélyezve van az Azure csomópontjáról **Server Explorer**, ahogy az alábbi képen látható:

![Server Explorer – IntelliTrace engedélyezve](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Egy szerepkörpéldány az IntelliTrace-naplók letöltése
A Visual Studio használatával töltheti le egy szerepkörpéldány az IntelliTrace-naplók az alábbi lépéseket:

1. A **Server Explorer**, bontsa ki a **Cloud Services** csomópontot, és keresse meg a szerepkörpéldány, amelynek a naplói letöltéséhez. 

1. Kattintson a jobb gombbal a szerepkörpéldány, és a s helyi menüből válassza ki a **IntelliTrace-naplók megtekintése**. 

    ![IntelliTrace-naplók menüpont megjelenítése](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Az IntelliTrace-naplókat a rendszer letölti a helyi számítógépen mappában levő fájlt. Minden alkalommal, amikor kérnie az IntelliTrace-naplók, új pillanatkép jön létre. Amíg a naplók letöltése a Visual Studio megjeleníti a a műveletnek az előrehaladását a **Azure-tevékenységnapló** ablak. Az alábbi ábrán látható, kibonthatja a sortételt a művelet további részletek megtekintéséhez.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Továbbra is az IntelliTrace-naplók letöltése a Visual Studióban működik. Ha a napló letöltése befejeződött, megnyílik a Visual Studióban.

> [!NOTE]
> Az IntelliTrace-naplók tartalmazhat kivételeket, amelyek a keretrendszer állít elő, és ezt követően végzi. Belső keretrendszer kódot állít elő, az ilyen kivételek részeként egy normál indul el egy szerepkör, így, nyugodtan figyelmen kívül hagyhatja őket.
> 
> 

## <a name="next-steps"></a>További lépések
- [Az Azure cloud services hibakeresési beállításai](vs-azure-tools-debugging-cloud-services-overview.md)
- [Közzététel az Azure cloud Services, a Visual Studio használatával](vs-azure-tools-publishing-a-cloud-service.md)