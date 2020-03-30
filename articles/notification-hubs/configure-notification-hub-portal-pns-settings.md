---
title: Leküldéses értesítések beállítása az Azure Értesítési központokban | Microsoft dokumentumok
description: Megtudhatja, hogyan állíthatja be az Azure Értesítési központokat az Azure Portalon a platformértesítési rendszer (PNS) beállításainak használatával.
services: notification-hubs
author: sethmanheim
manager: femila
editor: dbradish-microsoft
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 02/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 78afb124ee1d1ab9b212197fb7a7140f88de9940
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349513"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Rövid útmutató: Leküldéses értesítések beállítása értesítési központban

Az Azure Notification Hubs egy leküldéses motort biztosít, amely könnyen használható, és amely horizontális felskálázódik. Az Értesítési központok segítségével értesítéseket küldhet bármely platformra (iOS, Android, Windows, Baidu) és bármely háttérrendszerről (felhőből vagy helyszíni rendszerről). További információ: [Mi az Azure Értesítési központ?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban az Értesítési központok platformértesítési rendszer (PNS) beállításait fogja használni a leküldéses értesítések több platformon történő beállításához. A rövid útmutató bemutatja az Azure Portalon elkövetési lépéseket.  [A Google Firebase Cloud Messaging](/azure/notification-hubs/configure-notification-hub-portal-pns-settings?tabs=azure-cli#google-firebase-cloud-messaging-fcm) utasításokat tartalmaz az Azure CLI használatára vonatkozóan.

Ha még nem hozott létre értesítési központot, hozzon létre egyet most. További információ: [Azure-értesítési központ létrehozása az Azure Portalon](create-notification-hub-portal.md) vagy [Egy Azure értesítési központ létrehozása az Azure CLI használatával](create-notification-hub-azure-cli.md)

## <a name="apple-push-notification-service"></a>Apple Push Notification szolgáltatás

Az Apple leküldéses értesítési szolgáltatás (APNS) beállítása:

1. Az Azure Portalon az **Értesítési központ** lapon válassza az **Apple (APNS)** lehetőséget a bal oldali menüből.

1. **Hitelesítési módban**válassza a **Tanúsítvány** vagy **a Token**lehetőséget.

   a. Ha a Tanúsítvány lehetőséget **választja:**
   * Jelölje ki a fájl ikonját, majd a feltöltendő *.p12* fájlt.
   * Adja meg a jelszót.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni azoknak a felhasználóknak, akik az áruházból vásárolták az alkalmazást, válassza a **Termelési** módot.

     ![Képernyőkép egy APNS-tanúsítvány konfigurációjáról az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Ha a **Token (Token) lehetőséget választja:**

   * Adja meg a **kulcsazonosító,** **a bundle-azonosító,** a **csoportazonosító**és a **token**értékét.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni azoknak a felhasználóknak, akik az áruházból vásárolták az alkalmazást, válassza a **Termelési** módot.

     ![Képernyőkép egy APNS-tokenkonfigurációról az Azure Portalon](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

További információ: [Leküldéses értesítések iOS-be az Azure Értesítési központ használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md)című témakörben talál.

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase felhőalapú üzenetküldés (FCM)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Leküldéses értesítések beállítása a Google FCM-hez:

1. Az Azure Portalon az **Értesítési központ** lapon válassza a **Google (GCM/FCM)** lehetőséget a bal oldali menüből.
2. Illessze be a korábban mentett Google FCM-projekt **API-kulcsát.**
3. Kattintson a **Mentés** gombra.

   ![Képernyőkép, amely bemutatja, hogyan kell beállítani az értesítési központokat a Google FCM-hez](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

A lépések végrehajtásával egy riasztás jelzi, hogy az értesítési központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, rendelkeznie kell a következőkkel:

* Az [Azure CLI](/cli/azure/install-azure-cli) 2.0.67-es vagy újabb verziója.

* Az Azure [CLI-bővítmény az értesítési központokhoz.](/cli/azure/ext/notification-hub/notification-hub)
* A Google Firebase Cloud Messaging (FCM) projekt **API-kulcsa.**

### <a name="set-up-push-notifications-for-google-fcm"></a>Leküldéses értesítések beállítása a Google FCM-hez

1. Az [az notification-hub credential gcm update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) paranccsal adja hozzá a Google API-kulcsot az értesítési központhoz.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Az Android-alkalmazásnak szüksége van egy kapcsolati karakterláncra az értesítési központhoz való csatlakozáshoz.  Az [értesítési központ engedélyezési szabálylista](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) parancs segítségével sorolja fel a rendelkezésre álló hozzáférési házirendeket.  Használja az [az notification-hub engedélyezési szabály listakulcsok](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) parancs a hozzáférési házirend kapcsolati karakterláncok.  Adja meg az **elsődlegesConnectionString** vagy `--query` **a másodlagosConnectionString paramétert** az elsődleges kapcsolati karakterlánc közvetlen leválasztásához.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Az [az notification-hub tesztküldés](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) parancs használatával tesztelje az üzenetek androidos alkalmazásba történő küldését.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Az [az notification-hub hitelesítő paranccsal](/cli/azure/ext/notification-hub/notification-hub/credential) azure CLI-hivatkozásokat más platformokra is beszerezhet.

---

Az értesítések Android-alkalmazásokba történő leküldéséről a [Leküldéses értesítések küldése a Firebase használatával androidos eszközökre című](notification-hubs-android-push-notification-google-fcm-get-started.md)témakörben talál további információt.

## <a name="windows-push-notification-service"></a>Windows leküldéses értesítési szolgáltatás

A Windows leküldéses értesítési szolgáltatás (WNS) beállítása:

1. Az Azure Portalon az **Értesítési központ** lapon válassza a **Windows (WNS)** lehetőséget a bal oldali menüben.
2. Adja meg a **csomag biztonsági azonosítójának** és **biztonsági kulcsának értékeit.**
3. Kattintson a **Mentés** gombra.

   ![Képernyőkép a Csomag biztonsági azonosítója és a biztonsági kulcs mezőiről](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

További információt az [Értesítések küldése UWP-alkalmazásoknak az Azure Értesítési központ használatával című témakörben talál.](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft leküldéses értesítési szolgáltatás Windows Phone-telefonhoz

A Microsoft Leküldéses értesítési szolgáltatás (MPNS) beállítása Windows Phone-telefonhoz:

1. Az Azure Portalon az **Értesítési központ** lapon válassza a **Windows Phone (MPNS)** lehetőséget a bal oldali menüben.
1. Nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. A nem hitelesített leküldéses értesítések engedélyezéséhez jelölje be **a Nem hitelesített leküldéses** > **mentés engedélyezése jelölőnégyzetet.**

      ![Képernyőkép, amely bemutatja, hogyan lehet engedélyezni a nem hitelesített leküldéses értesítéseket](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Az eszköztáron válassza a **Tanúsítvány feltöltése**lehetőséget.
      * Jelölje ki a fájl ikonját, majd a tanúsítványfájlt.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Válassza **az OK gombot.**
      * A **Windows Phone (MPNS)** lapon válassza a **Mentés lehetőséget.**

További információt a [Leküldéses értesítések A Windows Phone-alkalmazásokba az Értesítési központok használatával című témakörben](notification-hubs-windows-mobile-push-notifications-mpns.md)talál.

## <a name="baidu-android-china"></a>Baidu (Android Kína)

A Baidu leküldéses értesítéseinek beállítása:

1. Az Azure Portalon az **Értesítési központ** lapon válassza a **Baidu (Android China)** lehetőséget a bal oldali menüből.
2. Adja meg a Baidu konzolról a Baidu felhőleküldéses projektben beszerzett **Api-kulcsot.**
3. Adja meg a Baidu konzolról a Baidu felhőleküldéses projektben beszerzett **titkos kulcsot.**
4. Kattintson a **Mentés** gombra.

    ![Képernyőkép az értesítési központokról, amelyek a Baidu (Android China) konfigurációt jelenítik meg a leküldéses értesítésekhez](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

A lépések végrehajtásával egy riasztás jelzi, hogy az értesítési központ frissítése sikeresen megtörtént. A **Mentés** gomb le van tiltva.

További információ: [Az Értesítési központok használatának megkezdése a Baidu használatával](notification-hubs-baidu-china-android-notifications-get-started.md)című témakörben található.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtudhatja, hogyan konfigurálhatja a platformértesítési rendszer beállításait egy értesítési központhoz az Azure Portalon.

Ha többet szeretne megtudni arról, hogyan kell értesítéseket leadni különböző platformokra, olvassa el az alábbi útmutatókat:

-[Értesítések küldése iOS-eszközökre az Értesítési központok és az APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
-[leküldéses értesítések androidos eszközökre történő használatával az Értesítési központok és](notification-hubs-android-push-notification-google-fcm-get-started.md)
-a Google FCM[leküldéses értesítések használatával egy Windows-eszközön](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
-futó UWP-alkalmazáshoz[Leküldéses értesítéseket egy Windows Phone 8 alkalmazásba AZ MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
-[push értesítések használatával az Értesítési központok és a Baidu felhőleküldéses értesítések használatával](notification-hubs-baidu-china-android-notifications-get-started.md)
