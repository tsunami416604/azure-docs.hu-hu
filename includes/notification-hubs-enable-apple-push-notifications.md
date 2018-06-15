---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/11/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 08ff4b2190b26471d7b1ac1850ce89f889b8c256
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33814730"
---
## <a name="generate-the-certificate-signing-request-file"></a>A tanúsítvány-aláírási kérelem fájljának létrehozása
Az Apple Push Notification szolgáltatás (APNS) tanúsítványokat használ a leküldéses értesítések hitelesítéséhez. Kövesse ezeket az utasításokat az értesítések küldéséhez és fogadásához szükséges leküldéses tanúsítvány létrehozásához. További információért lásd az [Apple Push Notification szolgáltatás](http://go.microsoft.com/fwlink/p/?LinkId=272584) hivatalos dokumentációját.

Hozza létre a tanúsítvány-aláírási kérelem (CSR) fájlját, mellyel az Apple létrehoz egy aláírt leküldéses tanúsítványt.

1. Futtassa a Kulcskarika-elérés eszközt Mac számítógépén. Ez a **Segédprogramok** vagy az **Egyéb** mappából nyitható meg a Launchpadről.
2. Kattintson a **Kulcskarika-elérés** menüpontra, bontsa ki a **Tanúsítványasszisztens** almenü, majd kattintson a **Tanúsítvány igénylése egy tanúsítványkiadó központból...** lehetőségre.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Töltse ki a **Felhasználói e-mail-cím** és az **Általános név** mezőket, ügyeljen arra, hogy a **Mentve lemezre** lehetőség legyen kiválasztva, majd kattintson a **Folytatás** gombra. Hagyja üresen a **CA e-mail-cím** mezőt, ennek kitöltése nem kötelező.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. Adjon meg egy nevet a tanúsítvány-aláírási kérelem (CSR) fájljához a **Mentés mint** mezőben, válassza ki a helyet a **Hely** mezőben, majd kattintson a **Mentés** gombra.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      Ez a művelet menti a CSR-fájlt a megadott helyre; az alapértelmezett hely az Íróasztal. Jegyezze meg a fájlhoz választott helyet.

Ezután regisztrálni fogja az alkalmazását az Apple rendszerében, engedélyezi a leküldéses értesítéseket, és feltölti ezt az exportált CSR-t a leküldéses tanúsítvány létrehozásához.

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez
Ahhoz, hogy leküldéses értesítéseket küldhessen az iOS-alkalmazásoknak, először regisztrálnia kell az alkalmazását az Apple rendszerében, és regisztrálnia kell a leküldéses értesítésekhez is.  

1. Ha már regisztrálta az alkalmazását, lépjen az <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> (iOS üzembe helyezési portál) felületére az Apple Developer Center központban, jelentkezzen be Apple-azonosítójával, kattintson az **Identifiers** (Azonosítók), majd az **App IDs** (Alkalmazásazonosítók) lehetőségre, végül kattintson a **+** jelre az új alkalmazás regisztrálásához.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)
      
2. Frissítse a következő három mezőt az új alkalmazásában, és kattintson a **Continue** (Folytatás) gombra:
   
   * **Name** (Név): Adjon meg egy leíró nevet az alkalmazáshoz a **Name** (Név) mezőben az **App ID Description** (Alkalmazásazonosító leírása) szakaszban.
   * **Bundle Identifier** (Csomagazonosító): Az **Explicit App ID** (Explicit alkalmazásazonosító) szakasz alatt adjon meg egy csomagazonosítót (**Bundle Identifier**) abban a formában, `<Organization Identifier>.<Product Name>` ahogy azt az [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8) (Alkalmazásterjesztési útmutató) írja. A használt céges azonosítónak (*Organization Identifier*) és terméknévnek (*Product Name*) meg kell egyeznie azzal a céges azonosítóval és terméknévvel, amelyeket az XCode-projekt létrehozásakor használ. Az alábbi képernyőképen a *NotificationHubs* a használt céges azonosító, a terméknév pedig a *GetStarted*. Ha ügyel arra, hogy ez az érték megegyezzen azzal, amelyet az XCode-projektben használ, a helyes közzétételi profilt fogja tudni használni az XCode környezetben. 
   * **Push Notifications** (Leküldéses értesítések): Jelölje be a **Push Notifications** (Leküldéses értesítések) beállítást az **App Services** (Alkalmazásszolgáltatások) szakaszban.
     
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
     
      Ez a művelet létrehozza az alkalmazásazonosítót, és a rendszer megkéri Önt az információk megerősítésére. Kattintson a **Register** (Regisztráció) gombra az új alkalmazásazonosító megerősítéséhez.
     
      Miután a **Register** (Regisztráció) gombra kattintott, megjelenik az alábbi képen is látható **Registration complete** (Regisztráció kész) képernyő. Kattintson a **Done** (Kész) gombra.
      
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


1. Keresse meg az Ön által létrehozott alkalmazásazonosítót a fejlesztői központban az alkalmazásazonosítók alatt, és kattintson az azt tartalmazó sorra.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)
   
      Ha az alkalmazásazonosítóra kattint, megjelennek az alkalmazás részletei. Kattintson az alul található **Edit** (Szerkesztés) gombra.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)
      
2. Görgessen a képernyő aljára, és kattintson a **Create Certificate...** (Tanúsítvány létrehozása...) gombra a **Development Push SSL Certificate** (Leküldéses fejlesztési SSL-tanúsítvány) szakasz alatt.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
      Ekkor megjelenik az „Add iOS Certificate” (iOS tanúsítvány hozzáadása) segédprogram.
   
   > [!NOTE]
   > Ez az oktatóprogram fejlesztési tanúsítványt használ. Ugyanez a folyamat használatos a termelési tanúsítvány regisztrálásához is. Csak arra ügyeljen, hogy ugyanazt a tanúsítványtípust használja az értesítések küldéséhez.
   > 
   > 
3. Kattintson a **Choose File** (Fájl kiválasztása) lehetőségre, keresse meg a helyet, ahová mentette az első feladatban létrehozott CSR-fájlt, majd kattintson a **Generate** (Létrehozás) gombra.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
4. Miután a portál létrehozza a tanúsítványt, kattintson a **Download** (Letöltés) gombra, majd a **Done** (Kész) gombra.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
      Ez a művelet letölti a tanúsítványt, és menti azt a számítógépére, a Letöltések mappába.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > Alapértelmezés szerint a letöltött fejlesztési tanúsítványfájl neve **aps_development.cer**.
   > 
   > 
5. Kattintson duplán a letöltött **aps_development.cer** leküldéses tanúsítványra.
   
      Ez a művelet telepíti az új tanúsítványt a kulcsláncba, ahogy az alábbi képen látható:
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > A tanúsítványban található név lehet különböző, de rendelkezni fog az **Apple Development iOS Push Services** (Apple fejlesztési iOS leküldéses szolgáltatások) előtagjával.   
6. A kulcslánc-hozzáférési oldalon kattintson a jobb egérgombbal az új leküldéses tanúsítványra, melyet a **Certificates** (Tanúsítványok) kategóriában létrehozott. Kattintson az **Export** (Exportálás) elemre, válassza a **.p12** formátumot, majd kattintson a **Save** (Mentés) gombra.
   
    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)
   
    Jegyezze fel az exportált .p12 tanúsítvány nevét és helyét. Ezek szükségesek lesznek az APNS-sel való hitelesítéshez.
   
   > [!NOTE]
   > Ez az oktatóprogram egy QuickStart.p12 fájlt hoz létre. Az Ön fájljának neve és helye eltérhet ettől.
   
## <a name="create-a-provisioning-profile-for-the-app"></a>Üzembe helyezési profil létrehozása az alkalmazáshoz
1. Térjen vissza az <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a> felületére, válassza a **Provisioning Profiles** (Üzembe helyezési profilok), majd az **All** (Összes) lehetőséget, és kattintson a **+** gombra az új profil létrehozásához. Ekkor megjelenik az **Add iOS Provisiong Profile** (iOS üzembe helyezési profil hozzáadása) varázsló
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. Az üzembe helyezési profil típusaként válassza az **iOS App Development** (iOS-alkalmazásfejlesztés) lehetőséget a **Development** (Fejlesztés) alatt, majd kattintson a **Continue** (Folytatás) gombra. 
3. Ezután válassza ki az Ön által létrehozott azonosítót az **App ID** (Alkalmazásazonosító) legördülő listából, és kattintson a **Continue** (Folytatás) gombra
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. A **Select certificates** (Tanúsítványok kiválasztása) képernyőn válassza ki a kódaláíráshoz használt szokásos fejlesztési tanúsítványt, majd kattintson a **Continue** (Folytatás) gombra. Ez a tanúsítvány nem az Ön által létrehozott leküldéses tanúsítvány.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
5. Ezután válassza ki a teszteléshez használt eszközöket a **Devices** (Eszközök) mezőben, és kattintson a **Continue** (Folytatás) gombra
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. Végül adjon meg egy nevet a profilnak a **Profile Name** (Profilnév) mezőben, és kattintson a **Generate** (Létrehozás) gombra.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
7. Az új üzembe helyezési profil létrejötte után kattintson rá a letöltéshez, és telepítse az Xcode-fejlesztői gépére. Ezután kattintson a **Done** (Kész) gombra.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
