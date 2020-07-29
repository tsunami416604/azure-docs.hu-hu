---
author: alexeystrakh
ms.author: alstrakh
ms.date: 06/11/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: c8271cc4de558ec5c97f41d6a070f71a9fc49cd2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060488"
---
### <a name="create-the-react-native-solution"></a>A reakciós natív megoldás létrehozása

1. A alkalmazásban `Terminal` frissítse a környezeti eszközöket, hogy a natív reagálás a következő parancsokkal működjön:

    ```bash
    # install node
    brew install node
    # or update
    brew update node
    # install watchman
    brew install watchman
    # or update
    brew upgrade watchman
    # install cocoapods
    sudo gem install cocoapods
    ```

1. A alkalmazásban `Terminal` futtassa a következő parancsot, ha a `React Native` CLI telepítve van az eltávolításához. A (z) használatával `npx` automatikusan elérhetővé válik a legújabb, NATÍV CLI-verzió:

    ```bash
    npm uninstall -g react-native-cli
    ```

    > [!NOTE]
    > A reakciós Native beépített parancssori felülettel rendelkezik. A parancssori felület egy adott verziójának globális telepítése és kezelése helyett azt javasoljuk, hogy a (z) használatával az aktuális verziót `npx` , amely a Node.js. A `npx react-native <command>` -ben a parancssori felület aktuális stabil verziója lesz letöltve és végrehajtva a parancs futtatásakor.

1. Navigáljon a projektek mappájához, ahol létre szeretné hozni az új alkalmazást. A (z) paraméter megadásával használja az írógéppel alapuló sablont `--template` :

    ```bash
    # init new project with npx
    npx react-native init PushDemo --template react-native-template-typescript
    ```

1. Futtassa a Metro Servert, amely létrehozza a JavaScript-csomagokat, és figyeli a kódok frissítését a csomagok valós idejű frissítéséhez:

    ```bash
    cd PushDemo
    npx react-native start
    ```

1. Futtassa az iOS-alkalmazást a telepítés ellenőrzéséhez. A következő parancs végrehajtása előtt győződjön meg arról, hogy iOS-szimulátort indított el vagy csatlakoztatott egy iOS-eszközt:

    ```bash
    npx react-native run-ios
    ```

1. Futtassa az Android-alkalmazást a telepítés ellenőrzéséhez. Néhány további lépésre van szükség egy Android-emulátor vagy-eszköz konfigurálásához, hogy hozzáférhessen a natív Metro-kiszolgáló Reagálásához. Az alábbi parancsok az Androidhoz készült kezdeti JavaScript-köteget hozzák forgalomba, és az eszközök mappába helyezik azokat.

    ```bash
    # create assets folder for the bundle
    mkdir android/app/scr/main/assets
    # build the bundle
    npx react-native bundle --platform android --dev true --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res
    # enable ability for sim to access the localhost
    adb reverse tcp:8081 tcp:8081
    ```

    A szkript előre telepítve lesz az alkalmazás kezdeti verziójával. A üzembe helyezést követően konfigurálja az emulátort vagy az eszközt a Metro-kiszolgáló eléréséhez a kiszolgáló IP-címének megadásával. Futtassa az alábbi parancsot az Android-alkalmazás létrehozásához és futtatásához:

    ```bash
    npx react-native run-android
    ```

    Egyszer az alkalmazásban, a találat `CMD+M` (emulátor) vagy az eszköz megrázásával töltse fel a fejlesztői beállításokat, navigáljon a (z) `Settings`  >  `Change Bundle Location` elemre, és adja meg a Metro kiszolgáló IP-címét az alapértelmezett porttal: `<metro-server-ip-address>:8081` .

1. A fájlban végezze el az `App.tsx` oldal elrendezésének módosítását, mentse, és a módosítást automatikusan az iOS-és Android-alkalmazásokban is tükrözze.

    > [!NOTE]
    > A részletes fejlesztői környezet telepítési útmutatója a [hivatalos dokumentációban](https://reactnative.dev/docs/environment-setup) érhető el

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Ehhez a mintához a következő három csomag szükséges:

1. [Natív leküldéses értesítések reagálása iOS](https://www.npmjs.com/package/@react-native-community/push-notification-ios)  -  rendszeren [Projekt GitHub](https://github.com/react-native-community/push-notification-ios)

    Ez a csomag akkor jött létre, amikor a PushNotificationIOS kibontották a natív reagálási alapból. A csomag natív módon valósítja meg a leküldéses értesítéseket az iOS-hez, és a natív felületet biztosít a hozzáféréshez. Futtassa a következő parancsot a csomag telepítéséhez:

    ```bash
    yarn add @react-native-community/push-notification-ios
    ```

1. [Natív leküldéses értesítések reakciója platformfüggetlen](https://www.npmjs.com/package/react-native-push-notification)

    Ez a csomag helyi és távoli értesítéseket valósít meg az iOS és az Android platformon platformfüggetlen módon. Futtassa a következő parancsot a csomag telepítéséhez:

    ```bash
    yarn add react-native-push-notification
    ```

1. [Eszköz információs csomagja](https://www.npmjs.com/package/react-native-device-info) A csomag információt nyújt az eszközről futtatókörnyezetben. Ezzel meghatározhatja az eszköz azonosítóját, amely a leküldéses értesítések regisztrálására szolgál. Futtassa a következő parancsot a csomag telepítéséhez:

    ```bash
    yarn add react-native-device-info
    ```

### <a name="implement-the-cross-platform-components"></a>A platformok közötti összetevők implementálása

1. Létrehozás és megvalósítás `DemoNotificationHandler` :

    ```typescript
    import PushNotification from 'react-native-push-notification';

    class DemoNotificationHandler {
      private _onRegister: any;
      private _onNotification: any;

      onNotification(notification: any) {
        console.log('NotificationHandler:', notification);

        if (typeof this._onNotification === 'function') {
          this._onNotification(notification);
        }
      }

      onRegister(token: any) {
        console.log('NotificationHandler:', token);

        if (typeof this._onRegister === 'function') {
          this._onRegister(token);
        }
      }

      attachTokenReceived(handler: any) {
        this._onRegister = handler;
      }

      attachNotificationReceived(handler: any) {
        this._onNotification = handler;
      }
    }

    const handler = new DemoNotificationHandler();

    PushNotification.configure({
      onRegister: handler.onRegister.bind(handler),
      onNotification: handler.onNotification.bind(handler),
      permissions: {
        alert: true,
        badge: true,
        sound: true,
      },
      popInitialNotification: true,
      requestPermissions: true,
    });

    export default handler;
    ```

1. Létrehozás és megvalósítás `DemoNotificationService` :

    ```typescript
    import PushNotification from 'react-native-push-notification';
    import DemoNotificationHandler from './DemoNotificationHandler';

    export default class DemoNotificationService {
      constructor(onTokenReceived: any, onNotificationReceived: any) {
        DemoNotificationHandler.attachTokenReceived(onTokenReceived);
        DemoNotificationHandler.attachNotificationReceived(onNotificationReceived);
        PushNotification.getApplicationIconBadgeNumber(function(number: number) {
          if(number > 0) {
            PushNotification.setApplicationIconBadgeNumber(0);
          }
        });
      }

      checkPermissions(cbk: any) {
        return PushNotification.checkPermissions(cbk);
      }

      requestPermissions() {
        return PushNotification.requestPermissions();
      }

      cancelNotifications() {
        PushNotification.cancelLocalNotifications();
      }

      cancelAll() {
        PushNotification.cancelAllLocalNotifications();
      }

      abandonPermissions() {
        PushNotification.abandonPermissions();
      }
    }
    ```

1. Létrehozás és megvalósítás `DemoNotificationRegistrationService` :

    ```typescript
    export default class DemoNotificationService {
        constructor(
            readonly apiUrl: string,
            readonly apiKey: string) {
        }

    async registerAsync(request: any): Promise<Response> {
            const method = 'PUT';
            const registerApiUrl = `${this.apiUrl}/notifications/installations`;
            const result = await fetch(registerApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                },
                body: JSON.stringify(request)
            });

            this.validateResponse(registerApiUrl, method, request, result);
            return result;
        }

        async deregisterAsync(deviceId: string): Promise<Response> {
            const method = 'DELETE';
            const deregisterApiUrl = `${this.apiUrl}/notifications/installations/${deviceId}`;
            const result = await fetch(deregisterApiUrl, {
                method: method,
                headers: {
                    Accept: 'application/json',
                    'Content-Type': 'application/json',
                    'apiKey': this.apiKey
                }
            });

            this.validateResponse(deregisterApiUrl, method, null, result);
            return result;
        }

        private validateResponse(requestUrl: string, method: string, requestPayload: any, response: Response) {
            console.log(`Request: ${method} ${requestUrl} => ${JSON.stringify(requestPayload)}\nResponse: ${response.status}`);
            if (!response || response.status != 200) {
                throw `HTTP error ${response.status}: ${response.statusText}`;
            }
        }
    }
    ```

1. Konfigurálja az alkalmazást. Nyissa meg `package.json` és adja hozzá a következő parancsfájl-definíciót:

    ```json
    "configure": "cp .app.config.tsx src/config/AppConfig.tsx"
    ```

    Ezután hajtsa végre ezt a parancsfájlt, amely az alapértelmezett konfigurációt másolja a `config` mappába.

    ```bash
    yarn configure
    ```

    Utolsó lépésként frissítse az előző lépésben másolt konfigurációs fájlt az API-hozzáférési információkkal. Megadása `apiKey` és `apiUrl` Paraméterek:

    ```typescript
    module.exports = {
        appName: "PushDemo",
        env: "production",
        apiUrl: "https://<azure-push-notifications-api-url>/api/",
        apiKey: "<api-auth-key>",
    };
    ```

### <a name="implement-the-cross-platform-ui"></a>A platformfüggetlen felhasználói felület implementálása

1. Lapelrendezés megadása

    ```typescript
    <View style={styles.container}>
      {this.state.isBusy &&
        <ActivityIndicator></ActivityIndicator>
      }
      <View style={styles.button}>
        <Button title="Register" onPress={this.onRegisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
      <View style={styles.button}>
        <Button title="Deregister" onPress={this.onDeregisterButtonPress.bind(this)} disabled={this.state.isBusy} />
      </View>
    </View>
    ```

1. Stílusok alkalmazása

    ```css
    const styles = StyleSheet.create({
      container: {
        flex: 1,
        alignItems: "center",
        justifyContent: 'flex-end',
        margin: 50,
      },
      button: {
        margin: 5,
        width: "100%",
      }
    });
    ```

1. Az oldal összetevő inicializálása

    ```typescript
      state: IState;
      notificationService: DemoNotificationService;
      notificationRegistrationService: DemoNotificationRegistrationService;
      deviceId: string;

      constructor(props: any) {
        super(props);
        this.deviceId = DeviceInfo.getUniqueId();
        this.state = {
          status: "Push notifications registration status is unknown",
          registeredOS: "",
          registeredToken: "",
          isRegistered: false,
          isBusy: false,
        };

        this.notificationService = new DemoNotificationService(
          this.onTokenReceived.bind(this),
          this.onNotificationReceived.bind(this),
        );

        this.notificationRegistrationService = new DemoNotificationRegistrationService(
          Config.apiUrl,
          Config.apiKey,
        );
      }
    ```

1. Definiálás gomb kattintson a kezelők elemre.

    ```typescript
      async onRegisterButtonPress() {
        if (!this.state.registeredToken || !this.state.registeredOS) {
          Alert.alert("The push notifications token wasn't received.");
          return;
        }

        let status: string = "Registering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          const pnPlatform = this.state.registeredOS == "ios" ? "apns" : "fcm";
          const pnToken = this.state.registeredToken;
          const request = {
            installationId: this.deviceId,
            platform: pnPlatform,
            pushChannel: pnToken,
            tags: []
          };
          const response = await this.notificationRegistrationService.registerAsync(request);
          status = `Registered for ${this.state.registeredOS} push notifications`;
          isRegistered = true;
        } catch (e) {
          status = `Registration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }

      async onDeregisterButtonPress() {
        if (!this.notificationService)
          return;

        let status: string = "Deregistering...";
        let isRegistered = this.state.isRegistered;
        try {
          this.setState({ isBusy: true, status });
          await this.notificationRegistrationService.deregisterAsync(this.deviceId);
          status = "Deregistered from push notifications";
          isRegistered = false;
        } catch (e) {
          status = `Deregistration failed: ${e}`;
        }
        finally {
          this.setState({ isBusy: false, status, isRegistered });
        }
      }
    ```

1. A kapott jogkivonat-regisztrációk és leküldéses értesítések kezelése

    ```typescript
      onTokenReceived(token: any) {
        console.log(`Received a notification token on ${token.os}`);
        this.setState({ registeredToken: token.token, registeredOS: token.os, status: `The push notifications token has been received.` });

        if (this.state.isRegistered && this.state.registeredToken && this.state.registeredOS) {
          this.onRegisterButtonPress();
        }
      }

      onNotificationReceived(notification: any) {
        console.log(`Received a push notification on ${this.state.registeredOS}`);
        this.setState({ status: `Received a push notification...` });

        if (notification.data.message) {
          Alert.alert(AppConfig.appName, `${notification.data.action} action received`);
        }
      }
    };
    ```