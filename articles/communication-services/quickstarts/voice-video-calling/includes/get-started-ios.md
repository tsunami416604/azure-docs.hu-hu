---
title: Rövid útmutató – hívás hozzáadása iOS-alkalmazáshoz az Azure Communication Services használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az iOS-hez készült ügyféloldali kódtárat hívó Azure kommunikációs szolgáltatásokat.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: bb0af58c9abc4fad701b1d0927f4c13e1fdcca49
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91377391"
---
Ebből a rövid útmutatóból megtudhatja, hogyan indíthat el hívást az iOS-hez készült ügyféloldali kódtárat hívó Azure kommunikációs szolgáltatással.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételekre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Az Azure kommunikációs szolgáltatás [felhasználói hozzáférési jogkivonata](../../access-tokens.md) .

## <a name="setting-up"></a>Beállítás

### <a name="creating-the-xcode-project"></a>A Xcode projekt létrehozása

A Xcode-ben hozzon létre egy új iOS-projektet, és válassza ki az **Egynézetes alkalmazás** sablonját. Ez az oktatóanyag a [SwiftUI keretrendszert](https://developer.apple.com/xcode/swiftui/)használja, ezért a **nyelvet** a **Swift** értékre kell beállítani, és a **felhasználói felületet** kell **SwiftUI**. Ebben a rövid útmutatóban nem fog teszteket létrehozni. Nyugodtan szüntesse meg a **tesztek belefoglalását**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Képernyőfelvétel: az új projekt ablak a Xcode belül.":::

### <a name="install-the-package"></a>A csomag telepítése

Adja hozzá az ügyféloldali függvénytárat hívó Azure kommunikációs szolgáltatásokat és annak függőségeit (AzureCore. Framework és AzureCommunication. Framework) a projekthez.

> [!NOTE]
> A AzureCommunicationCalling SDK kiadásával egy bash-szkriptet talál `BuildAzurePackages.sh` . A futtatáskor a parancsfájl `sh ./BuildAzurePackages.sh` Megadja a létrehozott keretrendszer csomagjainak elérési útját, amelyet a következő lépésben kell importálni a minta alkalmazásban. Vegye figyelembe, hogy a Xcode parancssori eszközöket kell beállítania, ha még nem tette meg a parancsfájl futtatása előtt: indítsa el a Xcode, majd válassza a "Preferences-> Locations" lehetőséget. Válassza ki a Xcode verzióját a parancssori eszközökhöz. **A BuildAzurePackages.sh parancsfájl csak a Xcode 11,5-es vagy újabb verzióval működik**

1. [Töltse le](https://github.com/Azure/Communication/releases) az iOS rendszerhez készült ügyféloldali kódtárat hívó Azure kommunikációs szolgáltatásokat.
2. A Xcode-ben kattintson a projektfájl elemre, majd válassza ki a Build célt a Project Settings Editor megnyitásához.
3. Az **általános** lapon görgessen a **keretrendszerek, tárak és beágyazott tartalom** szakaszhoz, és kattintson a **"+"** ikonra.
4. A párbeszédpanel bal alsó részén a legördülő menüben válassza a **fájlok hozzáadása**lehetőséget, navigáljon a kibontott ügyféloldali függvénytár-csomag **AzureCommunicationCalling. Framework** könyvtárába.
    1. Ismételje meg az utolsó lépést a **AzureCore. Framework** és a **AzureCommunication. Framework**hozzáadásához.
5. Nyissa meg a Project Settings Editor **Build Settings (létrehozási beállítások** ) lapját, és görgessen a **keresési útvonalak** szakaszhoz. Adjon hozzá egy új **Framework keresési útvonalak** bejegyzést a **AzureCommunicationCalling. Framework**-t tartalmazó könyvtárhoz.
    1. Vegyen fel egy másik Framework keresési útvonal bejegyzést, amely a függőségeket tartalmazó mappára mutat.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Képernyőfelvétel: az új projekt ablak a Xcode belül.":::

### <a name="request-access-to-the-microphone"></a>Hozzáférés kérése a mikrofonhoz

Az eszköz mikrofonjának eléréséhez frissítenie kell az alkalmazás információs tulajdonságainak listáját `NSMicrophoneUsageDescription` . A társított értéket adja meg, `string` amely szerepelni fog a párbeszédpanelen, amelyet a rendszer a felhasználótól való hozzáférés kéréséhez használ.

Kattintson a jobb gombbal a `Info.plist` projekt fájának bejegyzésére, és válassza a **Megnyitás**  >  **forrásként kód**lehetőséget. Adja hozzá a következő sorokat a legfelső szintű `<dict>` szakaszhoz, majd mentse a fájlt.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Nyissa meg a projekt **ContentView. Swift** fájlját, és adjon hozzá egy `import` deklarációt a fájl elejéhez az importáláshoz `AzureCommunicationCalling library` . Emellett az importáláshoz `AVFoundation` szükség van a kódban a hangengedélyre vonatkozó kérelemre.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Cserélje le a struct megvalósítását `ContentView` néhány olyan egyszerű felhasználói felületi vezérlőre, amely lehetővé teszi a felhasználók számára a hívások kezdeményezését és befejezését. Ebben a rövid útmutatóban üzleti logikát fogunk csatolni ezen vezérlőkhöz.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: ACSCallClient?
    @State var callAgent: ACSCallAgent?
    @State var call: ACSCall?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek az Azure kommunikációs szolgáltatások ügyfél-függvénytárának főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | A CallClient a hívó ügyféloldali függvénytár fő belépési pontja.|
| ACSCallAgent | A CallAgent a hívások indításához és kezeléséhez használatos. |
| CommunicationUserCredential | A rendszer a CommunicationUserCredential használja jogkivonat-hitelesítő adatként a CallAgent létrehozásához.| 
| CommunicationIdentifier | A CommunicationIdentifier a felhasználó identitásának jelölésére szolgál, amely a következők egyike lehet: CommunicationUser/telefonszám/CallingApplication. |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy olyan példány inicializálása `CallAgent` egy felhasználói hozzáférési jogkivonattal, amely lehetővé teszi a hívások kezdeményezését és fogadását. Adja hozzá az alábbi kódot a `onAppear` visszahíváshoz a **ContentView. Swift**:

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = ACSCallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Az `<USER ACCESS TOKEN>` erőforráshoz érvényes felhasználói hozzáférési tokent kell cserélnie. Ha még nem áll rendelkezésre jogkivonat, tekintse meg a [felhasználói hozzáférési jogkivonat](../../access-tokens.md) dokumentációját.

## <a name="start-a-call"></a>Hívás indítása

A `startCall` metódus beállítása a *hívás indítása* gomb kiválasztásakor végrehajtandó művelet. Frissítse a megvalósítást a hívás indításához a következővel `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: ACSStartCallOptions())
        }
    }
}
```

A-ben a tulajdonságok segítségével `ACSStartCallOptions` megadhatja a hívás kezdeti beállításait (azaz lehetővé teszi a hívás indítását a mikrofon némításával).

## <a name="end-a-call"></a>Hívás befejezése

Az `endCall` aktuális hívás befejezésére szolgáló metódus implementálása a *befejezési hívás* gombra koppintva.

```swift
func endCall()
{    
    self.call!.hangup(ACSHangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>A kód futtatása

Az alkalmazást az iOS-szimulátorban a **termék**  >  **futtatása** vagy a (&#8984;-R) billentyűparancs használatával hozhatja létre és futtathatja.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Képernyőfelvétel: az új projekt ablak a Xcode belül.":::

A kimenő VOIP-hívást úgy teheti meg, hogy egy felhasználói azonosítót biztosít a szövegmezőben, és a **hívás indítása** gombra koppint. A Calling `8:echo123` egy echo-robottal csatlakozik, ez nagyszerű megoldás az első lépésekhez és a hangeszközök ellenőrzéséhez. 

> [!NOTE]
> Amikor először végez hívást, a rendszer kérni fogja a mikrofon elérését. Éles alkalmazásban az API-t kell használnia az `AVAudioSession` [engedély állapotának ellenőrzése](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) és az alkalmazás működésének zökkenőmentes frissítése érdekében, ha az engedély nem lett megadva.

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure/Communication/tree/master/samples/Add%20Voice%20Calling/iOS/Swift)
