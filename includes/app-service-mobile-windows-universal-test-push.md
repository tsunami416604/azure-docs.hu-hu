---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 70e0d766fe3c9669912ce9f13f729fb1c757a26f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140504"
---
1. Kattintson a jobb gombbal a Windows Store-projektet, kattintson a **Set as StartUp Project**, majd nyomja le az F5 billentyűt, a Windows Store-alkalmazás futtatásához.

    Az alkalmazás indítása után az eszköz regisztrálva van a leküldéses értesítésekhez.
2. Állítsa le a Windows Store alkalmazást, és ismételje meg az előző lépést a Windows Phone Store alkalmazás.

    Mindkét eszköz ezen a ponton a leküldéses értesítések fogadásához van regisztrálva.

3. Futtassa újra a Windows Store alkalmazást, és írja be a szöveget **Insert a TodoItem**, és kattintson a **mentése**.

    Vegye figyelembe, hogy a Beszúrás után a Windows Store és a Windows Phone-alkalmazások leküldéses értesítés küldése WNS-ből. Az értesítés jelenik meg a Windows Phone akkor is, ha az alkalmazás nem fut.

    ![](./media/app-service-mobile-windows-universal-test-push/mobile-quickstart-push5-wp8.png)
