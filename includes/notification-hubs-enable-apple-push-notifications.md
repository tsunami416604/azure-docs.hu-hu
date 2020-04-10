---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: a9e8574ea2d7222871c7f065383e6c0c62057dd3
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "81007831"
---
## <a name="generate-the-certificate-signing-request-file"></a>A tanúsítványaláíró kérelemfájl létrehozása

Az Apple leküldéses értesítési szolgáltatása (APN) tanúsítványokat használ a leküldéses értesítések hitelesítéséhez. Kövesse ezeket az utasításokat az értesítések küldéséhez és fogadásához szükséges leküldéses tanúsítvány létrehozásához. További információért lásd az [Apple Push Notification szolgáltatás](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) hivatalos dokumentációját.

Hozza létre a tanúsítványaláíró kérelem (CSR) fájlt, amelyet az Apple az aláírt leküldéses tanúsítvány létrehozásához használ.

1. Futtassa a Kulcskarika-elérés eszközt Mac számítógépén. Megnyitható a **Segédprogramok** mappából vagy a Launchpad **Egyéb** mappájából.

1. Válassza a **Kulcskarika-elérés**lehetőséget, a **Tanúsítványsegéd**csomópontot, majd a **Tanúsítvány kérése a hitelesítésszolgáltatótól**lehetőséget.

    ![Új tanúsítvány kérése a Kulcskarika-eléréssel](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Alapértelmezés szerint a Kulcskarika-hozzáférés a lista első elemét jelöli ki. Ez akkor lehet probléma, ha a **Tanúsítványok** kategóriában van, és az **Apple Worldwide Developer Relations Certification Authority** nem az első elem a listán. Az CSR (Tanúsítványaláírási kérelem) létrehozása előtt győződjön meg arról, hogy rendelkezik nem kulcsos elemsel, vagy az **Apple Worldwide Developer Relations certification Authority (Apple Worldwide Developer Relations Certification Authority)** kulcsa van kiválasztva.

1. Válassza ki a **felhasználói e-mail címét,** adja meg a **Köznapi név** értékét, győződjön meg arról, hogy a **Mentett lemezre**értéket adja meg, majd válassza a **Folytatás**lehetőséget. Hagyja üresen **a hitelesítésköteles e-mail címet,** mert nincs rá szükség.

    ![Szükséges tanúsítványinformációk](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Írja be az CSR-fájl nevét a **Mentés másként**területen, válassza ki a helyet a **Hely területen,** majd kattintson a **Mentés gombra.**

    ![A tanúsítvány fájlnevének kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Ez a művelet menti az CSR-fájlt a kijelölt helyre. Az alapértelmezett hely az **Asztal**. Jegyezze meg a fájlhoz választott helyet.

Ezután regisztrálja az alkalmazást az Apple-nél, engedélyezze a leküldéses értesítéseket, és töltse fel az exportált CSR-t leküldéses tanúsítvány létrehozásához.

## <a name="register-your-app-for-push-notifications"></a>Alkalmazás regisztrálása leküldéses értesítésekhez

Ha leküldéses értesítéseket szeretne küldeni egy iOS-alkalmazásnak, regisztrálja az alkalmazást az Apple-nél, és regisztráljon leküldéses értesítésekre is.  

1. Ha még nem regisztrálta az alkalmazást, keresse meg az [iOS-kiépítési portált](https://go.microsoft.com/fwlink/p/?LinkId=272456) az Apple Developer Centerben. Jelentkezzen be a portálra az Apple ID azonosítójával, és válassza **az Azonosítók**lehetőséget. Ezután **+** válassza ki az új alkalmazás regisztrálásához.

    ![Az iOS Provisioning Portal alkalmazásazonosítói oldala](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Az **Új azonosító regisztrálása** képernyőn válassza az **Alkalmazásazonosítók választógombot.** Ezután válassza a **Folytatás** elemet.

    ![Az iOS-kiépítési portál új azonosítólap regisztrálása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Frissítse az új alkalmazás következő három értékét, majd válassza a **Folytatás**lehetőséget:

   * **Leírás**: Írja be az alkalmazás leíró nevét.

   * **Csomagazonosító**: Adja meg az űrlap **csomagazonosítóját.** [App Distribution Guide](https://help.apple.com/xcode/mac/current/#/dev91fe7130a) A *szervezeti azonosító* és a *terméknév* értékeknek meg kell egyezniük az Xcode projekt létrehozásakor használt szervezeti azonosítóval és terméknévvel. A következő képernyőképen a **NotificationHubs-érték** szervezeti azonosítóként, a **GetStarted** érték pedig a terméknévként használatos. Győződjön meg arról, hogy a **csomagazonosító** értéke megegyezik az Xcode projekt értékével, hogy az Xcode a megfelelő közzétételi profilt használja.

      ![Az iOS-kiépítési portál regisztrálja az alkalmazásazonosítóját tartalmazó lapot](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Leküldéses értesítések:** Ellenőrizze a **Leküldéses értesítések** lehetőséget a **Képességek** szakaszban.

      ![Regisztrációs űrlap új alkalmazásazonosítókhoz](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Ez a művelet létrehozza az alkalmazásazonosítót, és kéri, hogy erősítse meg az adatokat. Válassza **a Folytatás**lehetőséget, majd az új alkalmazásazonosító megerősítéséhez válassza a **Regisztráció** lehetőséget.

      ![Új alkalmazásazonosító megerősítése](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Miután a Regisztráció lehetőséget **választja,** az új alkalmazásazonosító t a **Tanúsítványok, azonosítók & profilok** lapon sorként látja.

4. A **Tanúsítványok, azonosítók & profilok** lap **Azonosítók**csoportában keresse meg az imént létrehozott alkalmazásazonosító-sort, és jelölje ki annak sorát az **Alkalmazásazonosító konfigurációjának szerkesztése** képernyő megjelenítéséhez.

## <a name="creating-a-certificate-for-notification-hubs"></a>Tanúsítvány létrehozása az értesítési központokhoz
Az értesítési központ **APNS-sel**való működéséhez tanúsítvány szükséges. Ez kétféleképpen történhet:

1. Hozzon létre egy **.p12-t,** amely közvetlenül az Értesítési központba tölthető fel.  
2. Hozzon létre egy **.p8-at,** amely a [tokenalapú hitelesítéshez](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification) használható (*az újabb megközelítés*).

Az újabb megközelítés számos előnnyel jár (a tanúsítványok használatával összehasonlítva) az [APNS tokenalapú (HTTP/2) hitelesítésében](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification)dokumentált módon. Mindkét megközelítésre azonban mindkét megközelítésre vonatkozóan lépéseket tettek. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>1. LEHETŐSÉG: .p12 leküldéses tanúsítvány létrehozása, amely közvetlenül az Értesítési központba tölthető fel

1. Görgessen le a **leküldéses értesítések** jelölőnégyzethez, majd válassza a **Konfigurálás** lehetőséget a tanúsítvány létrehozásához.

    ![Alkalmazásazonosítói oldal szerkesztése](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Megjelenik **az Apple Push Notification service SSL Certificates** ablaka. Válassza a **Tanúsítvány létrehozása** gombot a **Fejlesztési SSL-tanúsítvány** csoportban.

    ![Tanúsítvány létrehozása az alkalmazásazonosító gomb](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Megjelenik **az Új tanúsítvány létrehozása** képernyő.

    > [!NOTE]
    > Ez az oktatóprogram fejlesztési tanúsítványt használ. Ugyanez a folyamat használatos a termelési tanúsítvány regisztrálásához is. Csak arra ügyeljen, hogy ugyanazt a tanúsítványtípust használja az értesítések küldéséhez.

3. Válassza **a Fájl kiválasztása**lehetőséget, keresse meg azt a helyet, ahová az először történt csr fájlt mentette, majd dupla kattintással töltse be a tanúsítvány nevét. Ezután válassza a **Folytatás** elemet.

4. Miután a portál létrehozza a tanúsítványt, válassza a **Letöltés** gombot. Mentse a tanúsítványt, és jegyezze meg azt a helyet, ahhoz, ahhoz mentve.

    ![Előállított tanúsítványok letöltési oldala](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    A tanúsítvány letöltése és mentése a számítógépre a **Letöltések** mappába történik.

    ![A tanúsítványfájl megkeresése a Letöltések mappában](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Alapértelmezés szerint a letöltött fejlesztési tanúsítvány neve **aps_development.cer**.

5. Kattintson duplán a letöltött **aps_development.cer** leküldéses tanúsítványra. Ez a művelet telepíti az új tanúsítványt a kulcsláncba, ahogy az alábbi képen látható:

    ![Új tanúsítvány a Kulcskarika-elérés tanúsítványlistájában](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Bár a tanúsítványban lévő név eltérő lehet, a név előtaggal lesz rögzítve az **Apple Development iOS leküldéses szolgáltatásaival.**

6. A kulcslánc-hozzáférési oldalon kattintson a jobb egérgombbal az új leküldéses tanúsítványra, melyet a **Certificates** (Tanúsítványok) kategóriában létrehozott. Válassza **az Exportálás**lehetőséget, nevezze el a fájlt, jelölje ki a **.p12** formátumot, majd kattintson a **Mentés gombra.**

    ![Tanúsítványok exportálása p12 formátumban](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Választhat, hogy a tanúsítványt jelszóval védi, de ez nem kötelező. Kattintson **az OK gombra,** ha meg szeretné kerülni a jelszó létrehozását. Jegyezze fel az exportált .p12 tanúsítvány nevét és helyét. Az APN-ekkel való hitelesítés engedélyezésére szolgálnak.

    > [!NOTE]
    > A .p12 fájlneve és helye eltérhet az oktatóanyagban láthatótól.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>2. LEHETŐSÉG: .p8 tanúsítvány létrehozása, amely tokenalapú hitelesítéshez használható

1. Jegyezze fel a következő részleteket:

    - **Alkalmazásazonosító előtag** (ez egy **csapatazonosító)**
    - **Csomagazonosító**
    
2. A **Tanúsítványok, azonosítók & profilok**alkalmazásban kattintson a **Kulcsok**gombra.

   > [!NOTE]
   > Ha már van konfigurálva az **APNS-hez**konfigurált kulcs, akkor újra használhatja a .p8 tanúsítványt, amelyet közvetlenül a létrehozása után töltött le. Ha igen, figyelmen **3** kívül hagyhatja a **3-5.**

3. Új **+** kulcs létrehozásához kattintson a gombra (vagy a **Billentyű létrehozása** gombra).
4. Adja meg a megfelelő **kulcsnév** értéket, majd ellenőrizze az **Apple Leküldéses értesítések szolgáltatás (APN)** lehetőséget, majd kattintson a **Folytatás**gombra, majd a következő képernyőn a **Regisztráció** gombra.
5. Kattintson a **Letöltés gombra,** majd helyezze át a **.p8** fájlt *(AuthKey_* előtaggal) egy biztonságos helyi könyvtárba, majd kattintson a **Kész**gombra.

   > [!NOTE] 
   > Ügyeljen arra, hogy a .p8 fájlt biztonságos helyen tartsa (és mentse a biztonsági mentést). A kulcs letöltése után nem tölthető le újra, mert a kiszolgáló példánya eltávolításra kerül.
  
6. A **Kulcsok**oldalon kattintson az imént létrehozott kulcsra (vagy egy meglévő kulcsra, ha azt választotta helyette).
7. Jegyezze fel a **kulcsazonosító** értékét.
8. Nyissa meg a .p8 tanúsítványt egy megfelelő alkalmazással, például a [**Visual Studio Code-ban,**](https://code.visualstudio.com) majd jegyezze fel a kulcsértéket. Ez az érték a **-----BEGIN PRIVATE KEY-----** és **-----END PRIVATE KEY----- között.**

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Ez az értesítési **központ**konfigurálásához használt **tokenérték.** 

A lépések végén a következő információkkal kell rendelkeznie az [értesítési központ konfigurálása az APN-adatokkal című](#configure-your-notification-hub-with-apns-information)részben:

- **Csapatazonosító** (lásd az 1. lépést)
- **Csomagazonosító** (lásd az 1. lépést)
- **Kulcsazonosító** (lásd a 7. lépést)
- **Token érték,** azaz a .p8 kulcs értéke (lásd a 8. lépést)

## <a name="create-a-provisioning-profile-for-the-app"></a>Üzembe helyezési profil létrehozása az alkalmazáshoz

1. Térjen vissza az [iOS-kiépítési portálra,](https://go.microsoft.com/fwlink/p/?LinkId=272456)válassza **a Tanúsítványok, azonosítók & profilok**lehetőséget, válassza a bal oldali menü Profilok **parancsát,** majd válasszon **+** új profil létrehozásához. Megjelenik **egy új kiépítési profil regisztrálása** képernyő.

1. Válassza az **iOS-alkalmazásfejlesztés** lehetőséget a **Fejlesztés** csoportban létesítési profiltípusként, majd kattintson a **Folytatás**lehetőségre.

    ![Üzembehelyezési profillista](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Ezután jelölje ki a létrehozott alkalmazásazonosítót az **Alkalmazásazonosító** legördülő listából, és válassza a **Folytatás**gombot.

    ![Az alkalmazásazonosító kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. A **Tanúsítványok kiválasztása** ablakban jelölje ki a kódaláíráshoz használt fejlesztési tanúsítványt, és válassza a **Folytatás**gombot. Ez a tanúsítvány nem a létrehozott leküldéses tanúsítvány. Ha az egyik nem létezik, létre kell hoznia. Ha létezik tanúsítvány, ugorjon a következő lépésre. Fejlesztési tanúsítvány létrehozása, ha nem létezik:

    1. Ha a **Nincs elérhető tanúsítvány**látható, válassza a Tanúsítvány létrehozása **lehetőséget.**
    2. A **Szoftver** csoportban válassza az **Apple Development**lehetőséget. Ezután válassza a **Folytatás** elemet.
    3. Az **Új tanúsítvány létrehozása** képernyőn válassza a **Fájl kiválasztása lehetőséget.**
    4. Tallózással keresse meg a korábban létrehozott **tanúsítványaláíró** tanúsítványt, jelölje ki, és válassza a **Megnyitás**gombot.
    5. Válassza a **Folytatás** elemet.
    6. Töltse le a fejlesztési tanúsítványt, és jegyezze meg azt a helyet, ahamelya menti.

1. Térjen vissza a **Tanúsítványok, azonosítók & profilok** lapra, válassza **+** a bal oldali menü Profilok **parancsát,** majd új profil létrehozásához válassza a lehetőséget. Megjelenik **egy új kiépítési profil regisztrálása** képernyő.

1. A **Tanúsítványok kiválasztása** ablakban válassza ki az imént létrehozott fejlesztői tanúsítványt. Ezután válassza a **Folytatás** elemet.

1. Ezután jelölje ki a teszteléshez használni kívánt eszközöket, és válassza a **Folytatás**gombot.

1. Végül válasszon nevet a profilnak a **Kiépítési profilneve területen,** és válassza **a Létrehozás lehetőséget.**

    ![Egy üzembehelyezési profilnév kiválasztása](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Az új létesítési profil létrehozásakor válassza a **Letöltés lehetőséget.** Ne feledje, hogy a rendszer elmenti azt a helyet.

1. Tallózással keresse meg a létesítési profil helyét, majd kattintson rá duplán az Xcode fejlesztői gépre való telepítéséhez.

## <a name="create-a-notification-hub"></a>Értesítési központ létrehozása

Ebben a szakaszban létrehoz egy értesítési központot, és konfigurálja a hitelesítést az APN-ekkel a .p12 leküldéses tanúsítvány vagy a token alapú hitelesítés használatával. Ha már létrehozott értesítési központot szeretne használni, ugorjon az 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Az értesítési központ konfigurálása APN-adatokkal

Az **Értesítési szolgáltatások csoportban**válassza az **Apple (APNS)** lehetőséget, majd kövesse a megfelelő lépéseket a korábban az [Értesítési központokhoz tanúsítvány létrehozása](#creating-a-certificate-for-notification-hubs) szakaszban kiválasztott módszer alapján.  

> [!NOTE]
> Csak akkor használja az Production for Application mode **(Éles** **alkalmazáshoz) módot,** ha leküldéses értesítéseket szeretne küldeni azoknak a felhasználóknak, akik az áruházból vásárolták az alkalmazást.

### <a name="option-1-using-a-p12-push-certificate"></a>1. LEHETŐSÉG: .p12 leküldéses tanúsítvány használata

1. Válassza a **Tanúsítvány** elemet.

1. Válassza a fájl ikont.

1. Jelölje ki a korábban exportált .p12 fájlt, majd válassza **a Megnyitás**gombot.

1. Ha szükséges, adja meg a helyes jelszót.

1. Válassza a **Védőfal** módot.

    ![APNs-tanúsítvány konfigurálása az Azure Portalon](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Kattintson a **Mentés** gombra.

### <a name="option-2-using-token-based-authentication"></a>2. LEHETŐSÉG: Token alapú hitelesítés használata

1. Válassza a **Token lehetőséget.**
1. Adja meg a korábban beszerzett alábbi értékeket:

    - **Kulcsazonosító**
    - **Csomagazonosító**
    - **Csapatazonosító**
    - **Token** 

1. **Homokozó** kiválasztása
1. Kattintson a **Mentés** gombra. 

Most konfigurálta az értesítési központot az APN-ekkel. Az alkalmazás regisztrálásához és leküldéses értesítések küldéséhez is rendelkezik kapcsolati karakterláncokkal.
