---
title: fájl belefoglalása
description: fájl belefoglalása
services: commercial marketplace
documentationcenter: partner-center-commercial-marketplace
author: qianw211
manager: evansma
editor: ''
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2019
ms.author: v-qiwe
ms.custom: include file
ms.openlocfilehash: 059a2691c8e7905295a65daf262338ece65247d1
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812644"
---
Ha a partner centeren keresztül tesz közzé ajánlatot a piactéren, csatlakoznia kell az ajánlathoz az Ügyfélkapcsolat-kezelési (CRM) rendszerhez, hogy az ügyfél elérhetőségi adatait azonnal megkapja az ügyfelek érdeklődésének kinyilvánítása után. üzembe helyezi a terméket.

1. **Válassza ki a kívánt érdeklődő célhelyet, ahová az ügyfél-érdeklődőket küldeni szeretné**. A következő CRM-rendszerek támogatottak:

    * [Dynamics 365](../commercial-marketplace-lead-management-instructions-dynamics.md) ügyfél-engagement
    * [Marketo](../commercial-marketplace-lead-management-instructions-marketo.md)
    * [Salesforce](../commercial-marketplace-lead-management-instructions-salesforce.md)

    Ha a fenti listában nincs kifejezetten támogatva a CRM rendszer, a következő beállításokkal tárolhatja az ügyfél-érdeklődő adatait, majd exportálhatja vagy importálhatja ezeket az adatait a CRM-rendszerbe.

    * [Azure-tábla](../commercial-marketplace-lead-management-instructions-azure-table.md)
    * [Https-végpont](../commercial-marketplace-lead-management-instructions-https.md)

2. Olvassa el a fent hivatkozott dokumentációt a kiválasztott vezető célhelyre, hogy megtudja, hogyan állíthatja be a vezető célhelyet a Piactéri ajánlatból érkező érdeklődők fogadására. 
3. Kapcsolódjon az ajánlathoz a vezető célhoz, miközben közzéteszi az ajánlatot a piactéren a partner Centerben. Ehhez a fenti dokumentációban talál további információt.
4. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van beállítva. Ha megfelelően konfigurálta az érdeklődői célhelyet, és a partner Centerben közzétette az ajánlatát, a rendszer érvényesíti a kapcsolatot, és elküld egy tesztelési érdeklődőt. Ha az ajánlat megtekintése előtt megtekinti az ajánlatot, akkor tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja meg saját maga is beszerezni az ajánlatot. 
5. Győződjön meg arról, hogy a vezető célhely kapcsolata naprakész marad, hogy ne veszítse el az érdeklődőket, ügyeljen arra, hogy frissítse ezeket a kapcsolatokat, amikor valami módosult a végén.
