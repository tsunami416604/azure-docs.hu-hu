---
title: Apple Push Notification Service konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat egy Azure Notification hub-t Apple Push Notification Service (APNS) beállításokkal.
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
ms.openlocfilehash: 116af5192236045ec50409f65a9687ffec5223b5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406100"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Az értesítési központ Apple Push Notification Service (APNS) beállításainak konfigurálása a Azure Portal
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Notification hub Apple Push Notification Service (APNS) beállításait a Azure Portal használatával. 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service konfigurálása

Az alábbi eljárás az értesítési központ Apple Push Notification Service (APNS) beállításainak konfigurálásához nyújt útmutatást:

1. A Azure Portal az **értesítési központ** lapon válassza az **Apple (APNS)** lehetőséget a bal oldali menüben.

1. A **hitelesítési mód**beállításnál válassza a **tanúsítvány** vagy a **token**lehetőséget.

   a. Ha a **tanúsítvány**lehetőséget választja:
   * Válassza ki a fájl ikont, majd válassza ki a feltölteni kívánt *. P12* -fájlt.
   * Adjon meg egy jelszót.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-tanúsítvány konfigurációjának Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Ha a **jogkivonat**lehetőséget választja:

   * Adja meg a **kulcs azonosítójának**, **a köteg azonosítójának**, a **csoport azonosítójának**és a **tokennek**az értékeit.
   * Válassza a **Védőfal** módot. Ha leküldéses értesítéseket szeretne küldeni az alkalmazást az áruházból megvásárolt felhasználóknak, válassza a **termelési** mód lehetőséget.

     ![Képernyőkép a APNS-jogkivonat konfigurációjának Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Következő lépések
Az értesítések iOS-eszközökre történő leküldésével kapcsolatos részletes utasításokért tekintse meg a következő cikket: [leküldéses értesítések iOS-eszközökre Notification Hubs és APNS használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md)
