---
title: Felhőszolgáltatás hibakeresést az Azure lehetőségeit |} A Microsoft Docs
description: Hibakeresés az Azure cloud services
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: mikejo
ms.openlocfilehash: e4ca0f710257530a05acdec2b240ae2870d4fa80
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969306"
---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Ismerje meg, az Azure cloud service hibakeresése a különböző módjai
Ez a cikk az Azure cloud service hibakeresése a különböző módjait mutató hivatkozásokat tartalmaz. 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Hibakeresés az Azure-felhőszolgáltatás a Visual Studióban
Időt takaríthat meg, és költséget takaríthat meg az Azure compute emulator egy helyi gépen a felhőszolgáltatás hibakeresést. A szolgáltatás helyi hibakeresés a telepítése előtt, növelheti megbízhatóságát és teljesítményét anélkül számítási időért. Azonban néhány hiba is felléphet, csak akkor, amikor egy felhőalapú szolgáltatás az Azure-ban futtatja. Távoli hibakeresés, amikor közzéteszi a szolgáltatást, és ezután csatolja a hibakeresőt a szerepkörpéldányok engedélyezésével csak akkor, amikor egy felhőalapú szolgáltatás az Azure-ban futtatja előforduló hibákat is hibakereséséhez. További információkért lásd: [a helyi számítógépen a cloud service hibakeresése a](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

## <a name="using-intellitrace"></a>Az IntelliTrace 
Visual Studio Enterprise írni a szerepkörök megcélzott .NET-keretrendszer 4.5 használ, az IntelliTrace a időpontban központi telepítését az Azure-felhőszolgáltatás a Visual Studióból is engedélyezheti. IntelliTrace biztosít egy naplót, hogy az alkalmazás hibakeresését, mintha az Azure-ban futó, használhatja a Visual Studio használatával. További információkért lásd: [IntelliTrace és a Visual Studio egy közzétett felhőszolgáltatás hibakeresést](http://go.microsoft.com/fwlink/p/?LinkId=623016).

## <a name="remote-debugging"></a>Távoli hibakeresés 
Engedélyezheti a távoli hibakeresése a cloud Services szolgáltatások telepítésekor meg kell adnia a felhőszolgáltatás a Visual Studióból időpontjában. Ha a központi telepítés távoli hibakeresés engedélyezését választja, távoli hibakeresési szolgáltatások telepítve vannak az egyes szerepkörpéldány futtató virtuális gépeket. Ezen szolgáltatások – például `msvsmon.exe` – nincs hatással a teljesítményre vagy felesleges költségeket eredményez. További információkért lásd: [az Azure cloud service hibakeresése a](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).

## <a name="next-steps"></a>További lépések
- [Egy Azure-felhőszolgáltatás vagy virtuális Gépen, a Visual Studio hibakereső](./vs-azure-tools-debug-cloud-services-virtual-machines.md) – ismerje meg részletesen ismerteti az Azure cloud services hibakereséséhez.