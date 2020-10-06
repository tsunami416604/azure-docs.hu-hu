---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: nikuklic
ms.openlocfilehash: 7d158b39b3d8297095b65a0d58d38e87dc8ec250
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762612"
---
[!INCLUDE [Emergency Calling Notice](../../../includes/emergency-calling-notice-include.md)]
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- A kommunikációs szolgáltatások erőforrásában beszerzett telefonszám. [telefonszám beszerzése](../../telephony-sms/get-phone-number.md).
- A a `User Access Token` hívási ügyfél engedélyezéséhez. További információ a [beszerzéséről `User Access Token` ](../../access-tokens.md)
- Az [alkalmazáshoz való telefonálás hozzáadásával kapcsolatos első lépések](../getting-started-with-calling.md) elvégzése

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.
- Az alkalmazást az iOS-hez készült ügyféloldali kódtárat hívó Azure kommunikációs szolgáltatások segítségével hozhatja létre és futtathatja:

## <a name="setting-up"></a>Beállítás

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik az Azure kommunikációs szolgáltatások az iOS-hez készült ügyféloldali függvénytárának főbb funkcióit.

| Név                                           | Leírás                                                                                          |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| PhoneNumber | Ez az osztály szükséges a telefonos funkciókhoz használni kívánt telefonszám inicializálásához. |


## <a name="start-a-call-to-phone"></a>Telefonhívás indítása telefonra

Itt adhatja meg a kommunikációs szolgáltatások erőforrásában beszerzett telefonszámot, amelyet a rendszer a hívás elindításához fog használni:
> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 12223334444)

`startCall`A *hívás indítása* gomb kiválasztásakor végrehajtandó eseménykezelő módosítása:

```swift
func startCall() {
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            let startCallOptions = ACSStartCallOptions()
            startCallOptions!.alternateCallerID = PhoneNumber(phoneNumber: "+12223334444")
            self.call = self.callAgent!.call([PhoneNumber(phoneNumber: self.callee)], options: startCallOptions)
            self.callDelegate = CallDelegate(self)
            self.call!.delegate = self.callDelegate
        }
    }
}
```

## <a name="run-the-code"></a>A kód futtatása

Az alkalmazást az iOS-szimulátorban a **termék**  >  **futtatása** vagy a (&#8984;-R) billentyűparancs használatával hozhatja létre és futtathatja.

![A gyors üzembe helyezési alkalmazás végső megjelenésének és működésének első lépései](../media/ios/quick-start-make-call.png)

Telefonhívást hívhat meg úgy, hogy telefonszámot biztosít a hozzáadott szövegmezőben, és a **hívás indítása** gombra kattint.
> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 12223334444)

> [!NOTE]
> Amikor először végez hívást, a rendszer kérni fogja a mikrofon elérését. Éles alkalmazásban az API-t kell használnia az `AVAudioSession` [engedély állapotának ellenőrzése](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) és az alkalmazás működésének zökkenőmentes frissítése érdekében, ha az engedély nem lett megadva.
