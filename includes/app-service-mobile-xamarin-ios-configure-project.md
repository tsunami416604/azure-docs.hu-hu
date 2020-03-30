---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179619"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Az iOS-projekt konfigurálása a Xamarin Studio-ban
1. A Xamarin.Studio alkalmazásban nyissa meg az **Info.plist webhelyet,** és frissítse a **csomagazonosítót** az új alkalmazásazonosítóval korábban létrehozott csomagazonosítóval.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Görgessen le a **Háttérmódokhoz**. Jelölje be a Háttérmód engedélyezése és a **Távoli értesítések** **jelölőnégyzetet.**

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Kattintson duplán a projektre a Megoldás panelen a **Projektbeállításai**párbeszédpanel megnyitásához.
4. A **Build csoportban**válassza az **iOS-csomag aláírása**lehetőséget, és válassza ki a projekthez beállított megfelelő identitás- és kiépítési profilt.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Ez biztosítja, hogy a projekt az új profilt használja a kód aláírásához. A hivatalos Xamarin eszközkiépítési dokumentációt lásd: [Xamarin Device Provisioning].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Az iOS-projekt konfigurálása a Visual Studióban
1. A Visual Studio jobb gombbal kattintson a projektre, majd kattintson a **Tulajdonságok parancsra.**
2. A tulajdonságok lapon kattintson az **iOS-alkalmazás** fülre, és frissítse az **azonosítót** a korábban létrehozott azonosítóval.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Az **iOS-csomag aláírása** lapon válassza ki a megfelelő identitás- és kiépítési profilt, amelyet az imént állított be ehhez a projekthez.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Ez biztosítja, hogy a projekt az új profilt használja a kód aláírásához. A hivatalos Xamarin eszközkiépítési dokumentációt lásd: [Xamarin Device Provisioning].
4. Dupla kattintással nyissa meg az Info.plist listát, majd engedélyezze a **RemoteNotifications** szolgáltatást a **Háttérmódok csoportban.**

[Xamarin-eszköz kiépítés]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
