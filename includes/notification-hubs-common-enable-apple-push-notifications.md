---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081436"
---
### <a name="register-your-ios-app-for-push-notifications"></a>IOS-alkalmazás regisztrálása leküldéses értesítésekhez

Ha leküldéses értesítéseket szeretne küldeni egy iOS-alkalmazásnak, regisztrálja alkalmazását az Apple-szel, és regisztráljon a leküldéses értesítésekre is.  

1. Ha még nem regisztrálta az alkalmazást, keresse meg az [iOS-es kiépítési portált](https://go.microsoft.com/fwlink/p/?LinkId=272456) az Apple fejlesztői központban. Jelentkezzen be a portálra az Apple ID azonosítójával, keresse meg a **tanúsítványok, azonosítók & profilok**elemet, majd válassza az **azonosítók**lehetőséget. Kattintson ide **+** egy új alkalmazás regisztrálásához.

    ![Az iOS Provisioning Portal alkalmazásazonosítói oldala](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Az **új azonosító regisztrálása** képernyőn válassza az alkalmazás- **azonosítók** választógombot. Ezután válassza a **Folytatás** elemet.

    ![az iOS-es kiépítési portál regisztrálja az új azonosító lapot](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Frissítse a következő három értéket az új alkalmazáshoz, majd kattintson a **Folytatás**gombra:

   * **Leírás**: adjon meg egy leíró nevet az alkalmazásnak.

   * **Csomag azonosítója**: adja meg a **com. <organization_identifier>. <Product_name>** az [alkalmazás-terjesztési útmutatóban](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)említettek szerint. A következő képernyőképen az `mobcat` érték a szervezet azonosítója, a **PushDemo** pedig a termék neve lesz.

      ![iOS-es kiépítési portál – alkalmazás-azonosító lap regisztrálása](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Leküldéses értesítések**: a **képességek** szakaszban keresse meg a **leküldéses értesítések** lehetőséget.

      ![Regisztrációs űrlap új alkalmazásazonosítókhoz](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Ez a művelet létrehozza az alkalmazás AZONOSÍTÓját, és kéri, hogy erősítse meg az adatokat. Válassza a **Folytatás**lehetőséget, majd válassza a **regisztráció** lehetőséget az új alkalmazás azonosítójának megerősítéséhez.

      ![Új alkalmazás AZONOSÍTÓjának megerősítése](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Miután kiválasztotta a **regisztrációt**, megjelenik az új alkalmazás azonosítója a **tanúsítványok, azonosítók & profilok** lapon.

1. A **tanúsítványok, azonosítók & profilok** lapon az **azonosítók**területen keresse meg a létrehozott alkalmazás-azonosító sort. Ezután válassza ki annak sorát, és jelenítse meg az **alkalmazás-azonosító konfigurációjának szerkesztése** képernyőt.

#### <a name="creating-a-certificate-for-notification-hubs"></a>Tanúsítvány létrehozása Notification Hubshoz

Egy tanúsítványra van szükség ahhoz, hogy az értesítési központ működjön az **Apple Push Notification Services (APNS)** szolgáltatással, és kétféleképpen lehet megadni:

1. [P12 push-tanúsítvány létrehozása, amely közvetlenül az értesítési](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) központba tölthető fel (*az eredeti módszer*)

1. [Jogkivonat-alapú hitelesítéshez használható P8-tanúsítvány létrehozása](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*az újabb és javasolt megközelítés*)

Az újabb megközelítés számos előnnyel jár, mint a [jogkivonat-alapú (http/2) hitelesítés a APNS esetében](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Kevesebb lépésre van szükség, de az adott forgatókönyvek esetében is meg van hatalmazva. A lépéseket azonban mindkét megközelítéshez meg kell adni, mivel a jelen oktatóanyagban is működni fog.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>1. lehetőség: P12 push-tanúsítvány létrehozása, amely közvetlenül az értesítési központba tölthető fel

1. Futtassa a Kulcskarika-elérés eszközt Mac számítógépén. Megnyitható a **segédprogramok** mappából vagy a Kezdőpanel **másik** mappájából is.

1. Válassza a **kulcstartó-hozzáférés**, majd a **tanúsítvány**kibontása elemet, majd válassza **a tanúsítvány kérése a**hitelesítésszolgáltatótól lehetőséget.

    ![Új tanúsítvány kérése a Kulcskarika-eléréssel](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Alapértelmezés szerint a kulcstartó-hozzáférés kiválasztja a lista első elemét. Ez akkor lehet probléma, ha a **tanúsítványok** kategória és az **Apple Worldwide fejlesztői kapcsolatok hitelesítésszolgáltatója** nem a lista első eleme. Győződjön meg arról, hogy rendelkezik egy nem kulcsfontosságú elemmel, vagy az **Apple Worldwide fejlesztői kapcsolatok hitelesítésszolgáltató** kulcs van kiválasztva a CSR (tanúsítvány-aláírási kérelem) létrehozása előtt.

1. Válassza ki a **felhasználói e-mail-címét**, írja be a **köznapi név** értékét, győződjön meg róla, hogy a **lemezre mentve**van, majd válassza a **Folytatás**lehetőséget. Hagyja üresen a **CA e-mail-címét** , mert nincs rá szükség.

    ![Várt tanúsítvány adatai](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Adja meg a tanúsítvány- **aláírási kérelem (CSR) fájljának** nevét a **Save as (Mentés másként**) mezőben, válassza ki a helyet a **hol**, majd válassza a **Mentés**lehetőséget.

    ![A tanúsítványhoz tartozó fájlnév kiválasztása](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ez a művelet menti a **CSR-fájlt** a kiválasztott helyen. Az alapértelmezett hely az **asztali**. Jegyezze meg a fájlhoz választott helyet.

1. Az iOS-es [kiépítési portál](https://go.microsoft.com/fwlink/p/?LinkId=272456) **tanúsítványok, azonosítók & profilok** lapján lépjen vissza a **leküldéses értesítések** lehetőségre, majd válassza a **Konfigurálás** lehetőséget a tanúsítvány létrehozásához.

    ![Alkalmazásazonosítói oldal szerkesztése](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Megjelenik az **Apple push Notification szolgáltatás TLS/SSL-tanúsítványai** ablak. Kattintson a **tanúsítvány létrehozása** gombra a **fejlesztés TLS/SSL-tanúsítvány** szakaszban.

    ![Tanúsítvány létrehozása az alkalmazásazonosító gomb](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Megjelenik az **új tanúsítvány létrehozása** képernyő.

    > [!NOTE]
    > Ez az oktatóprogram fejlesztési tanúsítványt használ. Ugyanez a folyamat használatos a termelési tanúsítvány regisztrálásához is. Csak arra ügyeljen, hogy ugyanazt a tanúsítványtípust használja az értesítések küldéséhez.

1. Válassza a **fájl kiválasztása**elemet, keresse meg azt a helyet, ahová a **CSR-fájlt**mentette, majd kattintson duplán a tanúsítvány nevére a betöltéshez. Ezután válassza a **Folytatás** elemet.

1. Miután a portál létrehozza a tanúsítványt, kattintson a **Letöltés** gombra. Mentse a tanúsítványt, és jegyezze meg a helyet, ahová a rendszer mentette.

    ![Előállított tanúsítványok letöltési oldala](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    A rendszer letölti a tanúsítványt, és menti a számítógépére a **letöltések** mappában.

    ![A tanúsítványfájl megkeresése a Letöltések mappában](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Alapértelmezés szerint a letöltött fejlesztési tanúsítvány neve **aps_development. cer**.

1. Kattintson duplán a letöltött **aps_development.cer** leküldéses tanúsítványra. Ez a művelet telepíti az új tanúsítványt a kulcsláncba, ahogy az alábbi képen látható:

    ![Új tanúsítvány a Kulcskarika-elérés tanúsítványlistájában](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Bár a tanúsítványban szereplő név eltérő lehet, a név az **Apple Development iOS Leküldéses szolgáltatásaival** előtaggal lesz ellátva, és hozzá kell rendelnie a megfelelő köteg-azonosítót.

1. A **kulcstartó-hozzáférés területen**  +  **kattintson** az új leküldéses tanúsítványra, amelyet a **tanúsítványok** kategóriában hozott létre. Válassza az **Exportálás**lehetőséget, nevezze el a fájlt, válassza ki a **P12** formátumot, majd válassza a **Mentés**lehetőséget.

    ![Tanúsítványok exportálása p12 formátumban](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Megadhatja, hogy a tanúsítvány jelszavas védelemmel legyen ellátva, de a jelszó megadása nem kötelező. Kattintson **az OK** gombra, ha meg szeretné kerülni a jelszó létrehozását. Jegyezze fel az exportált P12-tanúsítvány fájlnevét és helyét. Ezek a APNs-alapú hitelesítés engedélyezésére szolgálnak.

    > [!NOTE]
    > A P12-fájl neve és helye eltérő lehet a jelen oktatóanyagban található képpel.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>2. lehetőség: jogkivonat-alapú hitelesítéshez használható P8-tanúsítvány létrehozása

1. Jegyezze fel az alábbi adatokat:

    * **Alkalmazás azonosítójának előtagja** (**csoport azonosítója**)
    * **Csomagazonosító**

1. Vissza a **tanúsítványok, azonosítók & profilok**területen kattintson a **kulcsok**elemre.

   > [!NOTE]
   > Ha már van konfigurált kulcs a **APNS**-hez, akkor újra használhatja azt a P8-tanúsítványt, amelyet a létrehozása után közvetlenül letöltött. Ha igen, figyelmen kívül hagyhatja a **3** – **5**. lépést.

1. Az **+** új kulcs létrehozásához kattintson a gombra (vagy a **kulcs létrehozása** gombra).
1. Adjon meg egy megfelelő **Kulcsnév** értéket, majd jelölje be az **Apple push Notifications szolgáltatás (APNS)** beállítást, majd kattintson a **Continue (folytatás**) gombra, majd a következő képernyőn **regisztráljon** .
1. Kattintson a **Download (Letöltés** ) gombra, majd helyezze át a **P8** -fájlt ( *AuthKey_*) a biztonságos helyi könyvtárba, majd kattintson a **kész**gombra.

   > [!NOTE]
   > Ügyeljen arra, hogy a P8-fájlt biztonságos helyen tárolja (és mentse a biztonsági mentést). A kulcs letöltése után nem tölthető le újra, mert a rendszer eltávolítja a kiszolgálói másolatot.
  
1. A **kulcsok**területen kattintson a létrehozott kulcsra (vagy egy meglévő kulcsra, ha inkább a használatát választotta).
1. Jegyezze fel a **kulcs azonosítójának** értékét.
1. Nyissa meg a P8-tanúsítványt az Ön által választott megfelelő alkalmazásban, például a [**Visual Studio Code**](https://code.visualstudio.com)-ban. Jegyezze fel a kulcs értékét ( **-----BEGIN Private key-----** és **-----END titkos kulcs-----**).

    -----A TITKOS KULCS MEGKEZDÉSE-----  
    <key_value>  
    -----VÉGE A TITKOS KULCSNAK-----

    > [!NOTE]
    > Ez az a **jogkivonat-érték** , amelyet később a rendszer az **értesítési központ**konfigurálásához fog használni.

A lépések végén a következő információkat kell használnia az [értesítési központ APNS-információkkal történő konfigurálásához](#configure-your-notification-hub-with-apns-information).

* **Csoport azonosítója** (lásd: 1. lépés)
* **Köteg azonosítója** (lásd: 1. lépés)
* **Kulcs azonosítója** (lásd: 7. lépés)
* **Jogkivonat értéke** (a 8. lépésben megszerzett P8-kulcs értéke)

### <a name="create-a-provisioning-profile-for-the-app"></a>Üzembe helyezési profil létrehozása az alkalmazáshoz

1. Térjen vissza az [iOS-es kiépítési portálra](https://go.microsoft.com/fwlink/p/?LinkId=272456), válassza a **tanúsítványok, azonosítók & profilok**lehetőséget, válassza a **profilok** elemet a bal oldali menüben, majd válassza **+** az új profil létrehozása lehetőséget. Megjelenik az **új létesítési Profil regisztrálása** képernyő.

1. Válassza az **iOS-alkalmazások fejlesztése** lehetőséget a **fejlesztés** alatt a létesítési profil típusa területen, majd válassza a **Folytatás**lehetőséget.

    ![Üzembehelyezési profillista](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Ezután válassza ki az **alkalmazás-azonosító** legördülő LISTÁBÓL létrehozott azonosítót, és válassza a **Folytatás**lehetőséget.

    ![Az alkalmazásazonosító kiválasztása](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. A **tanúsítványok kiválasztása** ablakban válassza ki a kód aláírásához használt fejlesztési tanúsítványt, és válassza a **Folytatás**lehetőséget.

    > [!NOTE]
    > Ez a tanúsítvány nem az [előző lépésben](#creating-a-certificate-for-notification-hubs)létrehozott leküldéses tanúsítvány. Ez a fejlesztési tanúsítvány. Ha az egyik nem létezik, létre kell hoznia, mert ez az oktatóanyag [előfeltétele](#prerequisites) . A fejlesztői tanúsítványok az [Apple fejlesztői portálon](https://developer.apple.com), a [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) -on vagy a [Visual Studióban](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/)is létrehozhatók.

1. Térjen vissza a **tanúsítványok, azonosítók & profilok** lapra, válassza a bal oldali menüben a **profilok** lehetőséget, majd válassza **+** az új profil létrehozása lehetőséget. Megjelenik az **új létesítési Profil regisztrálása** képernyő.

1. A **tanúsítványok kiválasztása** ablakban válassza ki a létrehozott fejlesztési tanúsítványt. Ezután válassza a **Folytatás** elemet.

1. Ezután válassza ki a teszteléshez használni kívánt eszközöket, majd válassza a **Folytatás**lehetőséget.

1. Végül válassza ki a profil nevét a **létesítési profil nevében**, majd válassza a **Létrehozás**lehetőséget.

    ![Egy üzembehelyezési profilnév kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Az új létesítési profil létrehozásakor válassza a **Letöltés**lehetőséget. Jegyezze meg a helyet, ahová a rendszer mentette.

1. Tallózással keresse meg a létesítési profil helyét, majd kattintson rá duplán a telepítéshez a fejlesztői gépen.
