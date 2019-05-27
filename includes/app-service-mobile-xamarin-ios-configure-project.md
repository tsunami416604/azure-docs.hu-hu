---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140167"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Az iOS-projekt konfigurálása a Xamarin Studióban
1. Xamarin.Studio, nyissa meg **Info.plist**, és frissítse a **Bundle Identifier** az új app ID azonosítójával korábban létrehozott alkalmazásköteg-azonosító

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Görgessen le a **háttérbeli üzemmódok**. Válassza ki a **engedélyezése előtt Háttérmódok** mezőbe, és a **távoli értesítések** mezőbe.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Kattintson duplán a megoldás panelen nyissa meg a projekt **Projektbeállítások**.
4. Alatt **hozhat létre**, válassza a **iOS alkalmazáscsomag aláírási**, és válassza ki a megfelelő identitás és a kiépítés portprofilkészletet, csupán be ehhez a projekthez.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Ez biztosítja, hogy a projektet az új profil használ a kód aláírása. A hivatalos Xamarin eszközregisztrációs dokumentációja, lásd: [Xamarin Device Provisioning].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Az iOS-projekt konfigurálása a Visual Studióban
1. A Visual Studióban kattintson a jobb gombbal a projektre, és kattintson **tulajdonságok**.
2. A Tulajdonságok oldalát, kattintson a **iOS-alkalmazás** lapra, és frissítse a **azonosító** a korábban létrehozott azonosítóval.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Az a **iOS alkalmazáscsomag aláírási** lapra, válassza ki a megfelelő identitás, létesítési profilt, egyszerűen állítsa be a projekthez.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Ez biztosítja, hogy a projektet az új profil használ a kód aláírása. A hivatalos Xamarin eszközregisztrációs dokumentációja, lásd: [Xamarin Device Provisioning].
4. Kattintson duplán a megnyitásához, és engedélyez Info.plist **RemoteNotifications** alatt **háttérbeli üzemmódok**.

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
