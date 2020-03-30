---
title: Az Apple leküldéses értesítési szolgáltatásának konfigurálása az Azure Értesítési központokban | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhat egy Azure-értesítési központot az Apple leküldéses értesítési szolgáltatás (APNS) beállításaival.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127519"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Az Apple leküldéses értesítési szolgáltatás beállításainak konfigurálása egy értesítési központhoz az Azure Portalon

Ez a cikk bemutatja, hogyan konfigurálhatja az Apple Leküldéses értesítési szolgáltatás (APNS) beállításait egy Azure értesítési központ hoz az Azure Portalhasználatával. 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre értesítési központot, hozzon létre egyet most. További információ: [Azure-értesítési központ létrehozása az Azure Portalon.](create-notification-hub-portal.md) 

## <a name="configure-apple-push-notification-service"></a>Az Apple leküldéses értesítési szolgáltatásának konfigurálása

Az alábbi eljárás az Apple Push Notification Service (APNS) értesítési központ beállításainak konfigurálása lépéseit tartalmazza:

1. Az Azure Portalon az **Értesítési központ** lapon válassza az **Apple (APNS)** lehetőséget a bal oldali menüben.

1. **Hitelesítési módban**válassza a **Tanúsítvány** vagy **a Token**lehetőséget.

   a. Ha a Tanúsítvány lehetőséget **választja:**
   * Jelölje ki a fájl ikonját, majd a feltöltendő *.p12* fájlt.
   * Adja meg a jelszót.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni azoknak a felhasználóknak, akik az áruházból vásárolták az alkalmazást, válassza a **Termelési** módot.

     ![Képernyőkép egy APNS-tanúsítvány konfigurációjáról az Azure Portalon](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Ha a **Token (Token) lehetőséget választja:**

   * Adja meg a **kulcsazonosító,** **a bundle-azonosító,** a **csoportazonosító**és a **token**értékét.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni azoknak a felhasználóknak, akik az áruházból vásárolták az alkalmazást, válassza a **Termelési** módot.

     ![Képernyőkép egy APNS-tokenkonfigurációról az Azure Portalon](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>További lépések
Az értesítések iOS-eszközökre történő leküldése lépésenkénti útmutatóját a következő cikkben találja: [Értesítések leküldése iOS-eszközökre értesítési központok és APNS használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md)
