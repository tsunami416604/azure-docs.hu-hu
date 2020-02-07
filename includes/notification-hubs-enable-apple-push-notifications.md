---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/21/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: b9f434f2d3c06d3db0bfda7c5853cc835ff64035
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047769"
---
## <a name="generate-the-certificate-signing-request-file"></a>A tanúsítvány-aláírási kérelem fájljának előállítása

A Apple Push Notification Service (APNs) tanúsítványokat használ a leküldéses értesítések hitelesítéséhez. Kövesse ezeket az utasításokat az értesítések küldéséhez és fogadásához szükséges leküldéses tanúsítvány létrehozásához. További információért lásd az [Apple Push Notification szolgáltatás](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) hivatalos dokumentációját.

A tanúsítvány-aláírási kérelem (CSR) fájljának létrehozása, amelyet az Apple az aláírt leküldéses tanúsítvány létrehozásához használ.

1. Futtassa a Kulcskarika-elérés eszközt Mac számítógépén. Megnyitható a **segédprogramok** mappából vagy a Kezdőpanel **másik** mappájából is.

1. Válassza a **kulcstartó-hozzáférés**, majd a **tanúsítvány**kibontása elemet, majd válassza **a tanúsítvány kérése a**hitelesítésszolgáltatótól lehetőséget.

    ![Új tanúsítvány kérése a Kulcskarika-eléréssel](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
    
> [!NOTE]
> Az OS X alapértelmezés szerint kiválasztja a kulcstartó-hozzáférési eszközben kiválasztott kategória első tételét. Ez akkor fordulhat elő, ha a **tanúsítványok** szakaszban szerepel, és az **Apple Worldwide fejlesztői kapcsolatainak hitelesítésszolgáltatója** *nem* a lista első eleme. A tanúsítvány igénylése előtt győződjön meg arról, hogy a **tanúsítványok** szakaszban szerepel, majd válassza az **Apple Worldwide fejlesztői kapcsolatok hitelesítésszolgáltató**lehetőséget.


1. Válassza ki a **felhasználói e-mail-címét**, írja be a **köznapi név** értékét, győződjön meg róla, hogy a **lemezre mentve**van, majd válassza a **Folytatás**lehetőséget. Hagyja üresen a **CA e-mail-címét** , mert nincs rá szükség.

    ![Szükséges tanúsítványinformációk](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Adja meg a CSR-fájl nevét a **Mentés másként**lehetőségnél, válassza ki a helyet a **hol**, majd válassza a **Mentés**lehetőséget.

    ![A tanúsítványhoz tartozó fájlnév kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ez a művelet menti a CSR-fájlt a kiválasztott helyen. Az alapértelmezett hely az **asztali**. Jegyezze meg a fájlhoz választott helyet.

Ezután regisztrálja alkalmazását az Apple-szel, engedélyezze a leküldéses értesítéseket, és töltse fel az exportált CSR-t egy leküldéses tanúsítvány létrehozásához.

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

Ha leküldéses értesítéseket szeretne küldeni egy iOS-alkalmazásnak, regisztrálja alkalmazását az Apple-szel, és regisztráljon a leküldéses értesítésekre is.  

1. Ha még nem regisztrálta az alkalmazást, keresse meg az [iOS-es kiépítési portált](https://go.microsoft.com/fwlink/p/?LinkId=272456) az Apple fejlesztői központban. Jelentkezzen be a portálra az Apple ID azonosítójával, és válassza az **azonosítók**lehetőséget. Ezután válassza a **+** lehetőséget egy új alkalmazás regisztrálásához.

    ![Az iOS Provisioning Portal alkalmazásazonosítói oldala](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Az **új azonosító regisztrálása** képernyőn válassza az alkalmazás- **azonosítók** választógombot. Ezután válassza a **Folytatás** elemet.

    ![az iOS-es kiépítési portál regisztrálja az új azonosító lapot](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Frissítse a következő három értéket az új alkalmazáshoz, majd kattintson a **Folytatás**gombra:

   * **Leírás**: adjon meg egy leíró nevet az alkalmazásnak.

   * **Csomag azonosítója**: adja meg az űrlap **szervezeti azonosítójának** egy köteg-azonosítóját. a terméknév az [alkalmazás-terjesztési útmutatóban](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)említett. A *szervezet azonosítójának* és a *terméknév* értékének meg kell egyeznie a Xcode-projekt létrehozásakor használt szervezeti azonosítóval és terméknév-névvel. Az alábbi képernyőképen a **NotificationHubs** érték a szervezet azonosítója, a **GetStarted** pedig a termék neve lesz. Győződjön meg arról, hogy a **csomag azonosítójának** értéke megegyezik a Xcode projekt értékével, hogy a Xcode a megfelelő közzétételi profilt használja.

      ![iOS-es kiépítési portál – alkalmazás-azonosító lap regisztrálása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Leküldéses értesítések**: a **képességek** szakaszban keresse meg a **leküldéses értesítések** lehetőséget.

      ![Regisztrációs űrlap új alkalmazásazonosítókhoz](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Ez a művelet létrehozza az alkalmazás AZONOSÍTÓját, és kéri, hogy erősítse meg az adatokat. Válassza a **Folytatás**lehetőséget, majd válassza a **regisztráció** lehetőséget az új alkalmazás azonosítójának megerősítéséhez.

      ![Új alkalmazás AZONOSÍTÓjának megerősítése](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Miután kiválasztotta a **regisztrációt**, megjelenik az új alkalmazás azonosítója a **tanúsítványok, azonosítók & profilok** lapon.

4. A **tanúsítványok, azonosítók & profilok** lapon az **azonosítók**területen keresse meg az imént létrehozott app ID sort, és válassza ki annak sorát az **alkalmazás-azonosító konfigurációjának szerkesztése** képernyő megjelenítéséhez.

5. Görgessen le a **leküldéses értesítések** lehetőséghez, majd válassza a **Konfigurálás** lehetőséget a tanúsítvány létrehozásához.

    ![Alkalmazásazonosítói oldal szerkesztése](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. Megjelenik az **Apple push Notification szolgáltatás SSL-tanúsítványainak** ablaka. Kattintson a **tanúsítvány létrehozása** gombra a **fejlesztési SSL-tanúsítvány** szakaszban.

    ![Tanúsítvány létrehozása az alkalmazásazonosító gomb](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Megjelenik az **új tanúsítvány létrehozása** képernyő.

    > [!NOTE]
    > Ez az oktatóprogram fejlesztési tanúsítványt használ. Ugyanez a folyamat használatos a termelési tanúsítvány regisztrálásához is. Csak arra ügyeljen, hogy ugyanazt a tanúsítványtípust használja az értesítések küldéséhez.

1. Válassza a **fájl kiválasztása**elemet, keresse meg azt a helyet, AHOVÁ a CSR-fájlt mentette az első feladatból, majd kattintson duplán a tanúsítvány nevére a betöltéshez. Ezután válassza a **Folytatás** elemet.

1. Miután a portál létrehozza a tanúsítványt, kattintson a **Letöltés** gombra. Mentse a tanúsítványt, és jegyezze meg a helyet, ahová a rendszer mentette.

    ![Előállított tanúsítványok letöltési oldala](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    A rendszer letölti a tanúsítványt, és menti a számítógépére a **letöltések** mappában.

    ![A tanúsítványfájl megkeresése a Letöltések mappában](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Alapértelmezés szerint a letöltött fejlesztési tanúsítvány neve **aps_development. cer**.

1. Kattintson duplán a letöltött **aps_development.cer** leküldéses tanúsítványra. Ez a művelet telepíti az új tanúsítványt a kulcsláncba, ahogy az alábbi képen látható:

    ![Új tanúsítvány a Kulcskarika-elérés tanúsítványlistájában](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Bár a tanúsítványban szereplő név eltérő lehet, a név az **Apple Development iOS Leküldéses szolgáltatásaival**előtaggal lesz ellátva.

1. A kulcslánc-hozzáférési oldalon kattintson a jobb egérgombbal az új leküldéses tanúsítványra, melyet a **Certificates** (Tanúsítványok) kategóriában létrehozott. Válassza az **Exportálás**lehetőséget, nevezze el a fájlt, válassza ki a **. P12** formátumot, majd válassza a **Mentés**lehetőséget.

    ![Tanúsítványok exportálása p12 formátumban](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Megadhatja, hogy a tanúsítvány jelszavas védelemmel legyen ellátva, de ez nem kötelező. Kattintson **az OK** gombra, ha meg szeretné kerülni a jelszó létrehozását. Jegyezze fel az exportált .p12 tanúsítvány nevét és helyét. A rendszer a APNs használatával engedélyezi a hitelesítést.

    > [!NOTE]
    > A. P12-fájl neve és helye eltérő lehet a jelen oktatóanyagban található képpel.

## <a name="create-a-provisioning-profile-for-the-app"></a>Üzembe helyezési profil létrehozása az alkalmazáshoz

1. Térjen vissza az [iOS-es kiépítési portálra](https://go.microsoft.com/fwlink/p/?LinkId=272456), válassza a **tanúsítványok, azonosítók & profilok**lehetőséget, válassza a **profilok** elemet a bal oldali menüben, majd válassza a **+** lehetőséget egy új profil létrehozásához. Megjelenik az **új létesítési Profil regisztrálása** képernyő.

1. Válassza az **iOS-alkalmazások fejlesztése** lehetőséget a **fejlesztés** alatt a létesítési profil típusa területen, majd válassza a **Folytatás**lehetőséget.

    ![Üzembehelyezési profillista](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Ezután válassza ki az **alkalmazás-azonosító** legördülő LISTÁBÓL létrehozott azonosítót, és válassza a **Folytatás**lehetőséget.

    ![Az alkalmazásazonosító kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. A **tanúsítványok kiválasztása** ablakban válassza ki a kód aláírásához használt fejlesztési tanúsítványt, és válassza a **Folytatás**lehetőséget. Ez a tanúsítvány nem a létrehozott leküldéses tanúsítvány. Ha az egyik nem létezik, létre kell hoznia. Ha egy tanúsítvány létezik, ugorjon a következő lépésre. Fejlesztési tanúsítvány létrehozása, ha az egyik nem létezik:

    1. Ha **nem érhető el tanúsítvány**, válassza a **tanúsítvány létrehozása**lehetőséget.
    2. A **szoftver** szakaszban válassza az **Apple Development**elemet. Ezután válassza a **Folytatás** elemet.
    3. Az **új tanúsítvány létrehozása** képernyőn válassza a **fájl kiválasztása**lehetőséget.
    4. Keresse meg a korábban létrehozott **tanúsítvány-aláírási kérelem** tanúsítványát, jelölje ki, majd kattintson a **Megnyitás**gombra.
    5. Válassza a **Folytatás** elemet.
    6. Töltse le a fejlesztési tanúsítványt, és jegyezze meg a helyet, ahová a rendszer mentette.

1. Térjen vissza a **tanúsítványok, azonosítók & profilok** lapra, válassza a bal oldali menüben a **profilok** lehetőséget, majd válassza a **+** lehetőséget egy új profil létrehozásához. Megjelenik az **új létesítési Profil regisztrálása** képernyő.

1. A **tanúsítványok kiválasztása** ablakban válassza ki az imént létrehozott fejlesztési tanúsítványt. Ezután válassza a **Folytatás** elemet.

1. Ezután válassza ki a teszteléshez használni kívánt eszközöket, majd válassza a **Folytatás**lehetőséget.

1. Végül válassza ki a profil nevét a **létesítési profil nevében**, majd válassza a **Létrehozás**lehetőséget.

    ![Egy üzembehelyezési profilnév kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Az új létesítési profil létrehozásakor válassza a **Letöltés**lehetőséget. Jegyezze meg a helyet, ahová a rendszer mentette.

1. Keresse meg a létesítési profil helyét, majd kattintson rá duplán a Xcode fejlesztői gépre való telepítéséhez.

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása

Ebben a szakaszban egy értesítési központot hoz létre, és konfigurálja a hitelesítést a APNs a korábban létrehozott. P12 push-tanúsítvány használatával. Ha egy már létrehozott értesítési központot szeretne használni, ugorjon az 5. lépésre.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Az értesítési központ konfigurálása a APNs-adatokkal

1. Az **Értesítési szolgáltatások** területen válassza az **Apple (APNS)** lehetőséget.

1. Válassza a **Tanúsítvány** elemet.

1. Válassza ki a fájl ikont.

1. Válassza ki a korábban exportált. P12 fájlt, majd kattintson a **Megnyitás**gombra.

1. Ha szükséges, a helyes jelszót kell megadnia.

1. Válassza a **Védőfal** módot. Az **Éles** beállítást kizárólag akkor használja, ha olyan felhasználóknak szeretne leküldéses értesítéseket küldeni, akik megvásárolták az alkalmazást az áruházból.

    ![APNs-tanúsítvány konfigurálása az Azure Portalon](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Kattintson a **Mentés** gombra.

Most már konfigurálta az értesítési központot a APNs. Az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez a kapcsolódási sztringek is rendelkezésre állnak.
