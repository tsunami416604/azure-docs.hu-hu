---
title: "A közzétett Hibakeresés az Azure felhőalapú szolgáltatás a Visual Studio és az IntelliTrace |} Microsoft Docs"
description: "Ismerje meg, egy felhőalapú szolgáltatás, a Visual Studio és az IntelliTrace hibakeresése"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/21/2017
ms.author: mikejo
ms.openlocfilehash: d33a8338b37f7479196449238388c7dbf391bbb6
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="debugging-a-published-azure-cloud-service-with-visual-studio-and-intellitrace"></a>A közzétett Azure-felhőszolgáltatásban a Visual Studio és az IntelliTrace-hibakeresés
Intellitrace bejelentkezhet a szerepkör példánya nagy mennyiségű hibakeresési adatok az Azure-ban futtatott. Ha a probléma okát van szüksége, az IntelliTrace-naplók segítségével végighaladhat a kódot a Visual Studio eszközből, mintha csak az Azure-ban futna. Gyakorlatilag IntelliTrace kulccsal rekordok programkód és környezeti adatok az Azure alkalmazás az Azure-ban felhőszolgáltatásként fut, és beállíthatja, a Visual Studio eszközből a rögzített adatokat. 

Ha rendelkezik telepített Visual Studio Enterprise IntelliTrace és az Azure-alkalmazásokban célok .NET-keretrendszer 4 vagy újabb verzió használható. IntelliTrace az Azure-szerepkörök adatokat gyűjt. Ezeket a szerepköröket mindig a 64 bites operációs rendszereket futtató virtuális gépekhez.

Alternatív megoldásként használható [távoli hibakeresés](http://go.microsoft.com/fwlink/p/?LinkId=623041) közvetlenül egy felhőszolgáltatás, amely az Azure-ban futó csatolni.

> [!IMPORTANT]
> IntelliTrace csak hibakeresési forgatókönyvekhez készült, és nem használható éles környezet.
> 

## <a name="configure-an-azure-application-for-intellitrace"></a>Az Azure-alkalmazások konfigurálása az IntelliTrace
Az Azure-alkalmazások IntelliTrace engedélyezéséhez hozzon létre, és tegye közzé az alkalmazást a Visual Studio Azure projektből. Az Azure-ba való közzététele előtt IntelliTrace kell konfigurálása az Azure-alkalmazásában. Ha az alkalmazás közzétételére IntelliTrace beállítása nélkül, akkor a projekt közzé kell. További információkért lásd: [közzététele egy Azure cloud services projekteket a Visual Studio használatával](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Ha készen áll az Azure alkalmazás közzétételéhez, győződjön meg arról, hogy a project build célkitűzések vannak-e beállítva **Debug**.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projekt, és válassza ki a helyi menüből **közzététel**.
   
1. Az a **Azure-alkalmazás közzététele** párbeszédpanel, válassza ki az Azure-előfizetés, válassza ki **következő**.

1. Az a **beállítások** lapon jelölje be a **speciális beállítások** fülre.

1. Kapcsolja be a **engedélyezése IntelliTrace** beállítás gyűjtése az IntelliTrace-naplók az alkalmazáshoz, ha a felhőben van közzétéve.
   
1. Az IntelliTrace-alapkonfiguráció testreszabásához jelölje be **beállítások** melletti **engedélyezése IntelliTrace**.

    ![IntelliTrace beállítások hivatkozására](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/intellitrace-settings-link.png)
   
1. Az a **IntelliTrace beállítások** párbeszédpanelen, a napló, e hívás kapcsolatos információk összegyűjtéséhez, mely modulok és a folyamatok gyűjtéséhez naplóit, és mekkora lemezterület lefoglalása a felvétel számára események megadhat. Intellitrace alkalmazással kapcsolatban további információkért lásd: [intellitrace-hibakeresés](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![IntelliTrace-beállítások](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Az IntelliTrace-naplót, az IntelliTrace-beállításokat (az alapértelmezett mérete 250 MB) a megadott maximális méret körkörös naplófájl. IntelliTrace-naplókat a rendszer egy fájlba, a fájlrendszer, a virtuális gép gyűjti. A naplók igénylésekor pillanatkép hajtja végre, ezen a ponton idő, és letölti a helyi számítógépre.

Miután az Azure felhőszolgáltatást közzé lett téve az Azure-ba, azt is meghatározhatja, ha a IntelliTrace a Azure csomópontján engedélyezve van-e **Server Explorer**, a következő ábrán látható módon:

![Server Explorer - IntelliTrace engedélyezve](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="download-intellitrace-logs-for-a-role-instance"></a>Töltse le az IntelliTrace-naplókat a szerepkörpéldányhoz
Visual Studio használatával, letöltheti a IntelliTrace-naplókat a szerepkör példánya a következő lépések végrehajtásával:

1. A **Server Explorer**, bontsa ki a **Felhőszolgáltatások** csomópont, és keresse meg a szerepkörpéldányt, amelynek a naplói, töltse le kívánja. 

1. Kattintson a jobb gombbal a szerepkörpéldányt, és a s helyi menüben válassza a **IntelliTrace-naplók megtekintése**. 

    ![IntelliTrace-naplók menüpont megjelenítése](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/view-intellitrace-logs.png)

1. Az IntelliTrace-naplók letölti a fájl a könyvtárban található a helyi számítógépen. Minden alkalommal, amikor kérnie az IntelliTrace-naplók, egy új pillanatkép jön létre. A naplók letöltése folyamatban van, amíg a Visual Studio megjeleníti a műveletnek az előrehaladását a **Azure tevékenységnapló** ablak. A következő ábrán látható, a részletek megtekintéséhez művelet sortételt bővítheti.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Továbbra is az IntelliTrace-naplók letöltése a Visual Studio működne. Ha a napló letöltése befejeződött, a Visual Studio nyílik meg.

> [!NOTE]
> Az IntelliTrace-naplókat is tartalmazhatnak, kivételek fellépése, amelyek a keretrendszer hoz létre, és ezt követően kezeli. Belső keretrendszer kódot állít elő, ezeket a kivételeket, így biztonságosan figyelmen kívül hagyhatja őket egy szerepkör indítása normál részeként.
> 
> 

## <a name="next-steps"></a>Következő lépések
- [Azure felhőszolgáltatások hibakeresési lehetőségek](vs-azure-tools-debugging-cloud-services-overview.md)
- [Egy Visual Studio használatával Azure-felhőszolgáltatásban közzététele](vs-azure-tools-publishing-a-cloud-service.md)