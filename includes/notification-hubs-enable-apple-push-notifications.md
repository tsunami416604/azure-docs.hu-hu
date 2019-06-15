---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c664e73b39ad48a860661cfd9141ee74df203f3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116650"
---
## <a name="generate-the-certificate-signing-request-file"></a>A tanúsítvány-aláírási kérelem fájljának létrehozása

Az Apple Push Notification szolgáltatás (APNs) tanúsítványokat használ a leküldéses értesítések hitelesítéséhez. Kövesse ezeket az utasításokat az értesítések küldéséhez és fogadásához szükséges leküldéses tanúsítvány létrehozásához. További információért lásd az [Apple Push Notification szolgáltatás](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) hivatalos dokumentációját.

Hozzon létre a tanúsítvány-aláírási kérelem (CSR) fájlt, amelyet az Apple létrehoz egy aláírt leküldéses tanúsítványt használ.

1. Futtassa a Kulcskarika-elérés eszközt Mac számítógépén. A Megnyitás a **segédprogramok** mappa vagy a **más** a Kezdőpanel mappájába.

1. Válassza ki **Keychain Access**, bontsa ki a **Tanúsítványasszisztens**, majd válassza ki **tanúsítvány kérése hitelesítésszolgáltatótól**.

    ![Új tanúsítvány kérése a Kulcskarika-eléréssel](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Válassza ki a **felhasználó E-mail címének**, adja meg a **köznapi név** értékét, győződjön meg arról, hogy megadja **mentve lemezre**, majd válassza ki **Folytatás**. Hagyja **CA E-mail-cím** üres, nem szükséges.

    ![Szükséges tanúsítványinformációk](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Adjon meg egy nevet a CSR-fájlt a **Mentés másként**, válassza ki azt a helyet a **ahol**, majd válassza ki **mentése**.

    ![Válassza ki a tanúsítvány nevét](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ez a művelet menti a CSR-fájlt a kiválasztott helyen. Az alapértelmezett hely a **asztali**. Jegyezze meg a fájlhoz választott helyet.

Ezután az alkalmazás regisztrálása az Apple, engedélyezze a leküldéses értesítéseket, és hozzon létre egy leküldéses tanúsítványt az exportált CSR-fájl feltöltése.

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

Leküldéses értesítések küldése iOS-alkalmazások, az alkalmazás regisztrálása az Apple, és regisztrálni leküldéses értesítésekre is.  

1. Ha már az alkalmazás még nem regisztrált, keresse meg a [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) , az Apple Developer Center központban. Ezt követően jelentkezzen be az Apple ID, válassza ki **azonosítók**válassza **Alkalmazásazonosítók**, majd válassza a **+** új alkalmazás regisztrálásához.

    ![Az iOS Provisioning Portal alkalmazásazonosítói oldala](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. A következő három érték az új alkalmazás frissítése, és válassza ki **Folytatás**:

   * **Név**: Adjon meg egy leíró nevet az alkalmazáshoz tartozó a **neve** párbeszédpanel a **App ID leírás** szakaszban.

   * **Csomagazonosítója**: Az a **Explicit App ID** területén adjon meg egy **Bundle Identifier** az űrlap `<Organization Identifier>.<Product Name>` említetteknek megfelelően az [alkalmazás telepítési útmutató](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). A *Organization Identifier* és *Terméknév* értékeknek egyezniük kell a szervezet azonosítója és a termék nevét használhatja az Xcode-projekt létrehozásakor. Az alábbi képernyőképen a *NotificationHubs* érték egy szervezet azonosítóként van használatban, és a *GetStarted* érték szolgál a termék nevét. Győződjön meg arról, hogy a **Bundle Identifier** értéke megegyezik az Xcode-projektben, hogy az xcode-ban a helyes közzétételi profilt fogja használni.

   * **Leküldéses értesítések**: Ellenőrizze a **leküldéses értesítések** beállítást a **App Services** szakaszban.

     ![Regisztrációs űrlap új alkalmazásazonosítókhoz](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Ez a művelet létrehoz az Alkalmazásazonosító és a kérelmek, hogy az információk megerősítésére. Válassza ki **regisztrálása** ellenőrizze az új alkalmazás azonosítóját.

     Kiválasztása után **regisztrálása**, megjelenik a **regisztráció befejezéséhez** képernyőn a következő képen látható módon. Válassza a **Done** (Kész) lehetőséget.

     ![Alkalmazásazonosító regisztrációja a jogosultságok megjelenítésével](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. A fejlesztői központban alatt **Alkalmazásazonosítók**, keresse meg a létrehozott alkalmazás Azonosítóját, és válassza ki a sort.

    ![Alkalmazásazonosítói lista](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Válassza ki az Alkalmazásazonosító az alkalmazás részleteinek megjelenítéséhez, és válassza ki a **szerkesztése** gombra a lap alján.

    ![Alkalmazásazonosítói oldal szerkesztése](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Görgessen lefelé a képernyőn, majd válassza az alsó a **tanúsítvány létrehozása** gomb alatt a **Development Push SSL-tanúsítvány** szakaszban.

    ![Tanúsítvány létrehozása az alkalmazásazonosító gomb](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Ekkor megjelenik a **iOS tanúsítvány hozzáadása** Segéd.

    > [!NOTE]
    > Ez az oktatóprogram fejlesztési tanúsítványt használ. Ugyanez a folyamat használatos a termelési tanúsítvány regisztrálásához is. Csak arra ügyeljen, hogy ugyanazt a tanúsítványtípust használja az értesítések küldéséhez.

1. Válassza ki **fájl kiválasztása**, keresse meg a helyet, ahová mentette a CSR-fájlt, az első feladatra, és válassza **Generate**.

    ![Előállított tanúsítványok CSR-feltöltési oldala](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Miután a portál létrehozza a tanúsítványt, válassza ki a **letöltése** gombra, majd válassza ki **kész**.

    ![Előállított tanúsítványok letöltési oldala](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    A tanúsítvány letöltése és mentése a számítógépére, a **letölti** mappát.

    ![A tanúsítványfájl megkeresése a Letöltések mappában](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Alapértelmezés szerint a letöltött fejlesztési tanúsítványfájl neve **aps_development.cer**.

1. Válassza ki a letöltött leküldéses tanúsítványt **aps_development.cer**.

    Ez a művelet telepíti az új tanúsítványt a kulcsláncba, ahogy az alábbi képen látható:

    ![Új tanúsítvány a Kulcskarika-elérés tanúsítványlistájában](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > A tanúsítványban található név lehet különböző, bár a neve a következő előtaggal **Apple fejlesztési iOS leküldéses szolgáltatások**.

1. A kulcslánc-hozzáférési oldalon kattintson a jobb egérgombbal az új leküldéses tanúsítványra, melyet a **Certificates** (Tanúsítványok) kategóriában létrehozott. Válassza ki **exportálása**, a fájl neve, válassza ki a **.p12** formázása, és válassza ki **mentése**.

    ![Tanúsítványok exportálása p12 formátumban](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Jegyezze fel az exportált .p12 tanúsítvány nevét és helyét. Ezek az APNs-hitelesítés engedélyezéséhez szolgálnak.

    > [!NOTE]
    > Ebben az oktatóanyagban létrehoz egy fájlt **QuickStart.p12**. Az Ön fájljának neve és helye eltérhet ettől.

## <a name="create-a-provisioning-profile-for-the-app"></a>Üzembe helyezési profil létrehozása az alkalmazáshoz

1. Az a [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456), jelölje be **kiépítési profilok**, jelölje be **összes**, majd válassza ki **+** hozhat létre egy új profilt. Megjelenik a **hozzáadása IOS-es kiépítési profilt** varázsló.

    ![Üzembehelyezési profillista](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Válassza ki **iOS App Development** alatt **fejlesztési** kiépítési profil típusa, és válassza ki **Folytatás**.

1. Ezután válassza ki a létrehozott alkalmazás azonosítója a **Alkalmazásazonosító** legördülő listára, és válassza **Folytatás**.

    ![Az alkalmazásazonosító kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Az a **válassza ki a tanúsítványok** ablakban válassza ki a kódaláíráshoz használt szokásos fejlesztési tanúsítványt, és válassza ki **Folytatás**. Ez a tanúsítvány nem a létrehozott leküldéses tanúsítvány.

    ![A tanúsítvány kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Ezután válassza ki a teszteléshez használni kívánt eszközöket, és válassza ki **Folytatás**.

    ![Az eszközök kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Végül adjon meg egy nevet a profilnak a **profilnév**, és válassza ki **Generate**.

    ![Egy üzembehelyezési profilnév kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Az új létesítési profil létrehozását követően válassza ki, töltse le és telepítse az Xcode-fejlesztői gépére. Ezután válassza a **Done** (Kész) elemet.

    ![Az üzembehelyezési profil letöltése](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása

Ebben a szakaszban egy értesítési központ létrehozása és a hitelesítés beállítása az apns-sel a korábban létrehozott .p12 leküldéses tanúsítvány használatával. Ha szeretne egy már létrehozott értesítési központot használja, kihagyhatja az 5. lépés.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Az APNs-adatait az értesítési központ konfigurálása

1. Az **Értesítési szolgáltatások** területen válassza az **Apple (APNS)** lehetőséget.

1. Válassza a **Tanúsítvány** elemet.

1. A fájl ikonra.

1. Válassza ki a korábban exportált .p12 fájlt.

1. Adja meg a helyes jelszót.

1. Válassza a **Védőfal** módot. Az **Éles** beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik megvásárolták az alkalmazást az áruházból.

    ![APNs-tanúsítvány konfigurálása az Azure Portalon](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

Most már konfigurálta az értesítési központot az apns-sel. Akkor is a kapcsolati karakterláncokkal az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez.
