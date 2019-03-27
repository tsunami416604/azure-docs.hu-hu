---
title: Apple Push Notification szolgáltatás konfigurálása az Azure Notification Hubs |} A Microsoft Docs
description: Ismerje meg, hogyan konfigurálja az Azure notification hub Apple Push Notification Service (APNS) beállításokkal.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9a9db9f05895569b050e56cdeec1ee2ee25af0df
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488385"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Az Apple Push Notification Service (APNS) beállítások egy értesítési központ konfigurálása az Azure Portalon
Ez a cikk bemutatja, hogyan az Azure notification hub Apple Push Notification Service (APNS) beállításainak konfigurálása az Azure portal használatával. 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központ, hozzon létre egyet. További információkért lásd: [Azure notification hub létrehozása az Azure Portalon](create-notification-hub-portal.md). 

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification szolgáltatás konfigurálása

Az alábbi eljárás biztosít egy értesítési központ Apple Push Notification Service (APNS) beállítások konfigurálásának lépései:

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Apple (APNS)** a bal oldali menüben.

1. A **hitelesítési mód**, ezek közül bármelyikre **tanúsítvány** vagy **Token**.

   a. Ha **tanúsítvány**:
   * A fájl ikonra, és válassza a *.p12* feltölteni kívánt fájlt.
   * Adja meg a jelszót.
   * Válassza a **Védőfal** módot. Vagy a leküldéses értesítések küldéséhez felhasználók, akik megvásárolták az alkalmazást az áruházból, válassza ki a **éles** mód.

     ![Képernyőkép az APNS-tanúsítvány konfigurálása az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Ha **Token**:

   * Adja meg a **Kulcsazonosító**, **Alkalmazásköteg-azonosító**, **Csapatazonosító**, és **Token**.
   * Válassza a **Védőfal** módot. Vagy a leküldéses értesítések küldéséhez felhasználók, akik megvásárolták az alkalmazást az áruházból, válassza ki a **éles** mód.

     ![Képernyőkép az APN szolgáltatás jogkivonat-konfiguráció az Azure Portalon](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>További lépések
A részletes oktatóanyag értesítések leküldése az iOS-eszközök számára lásd a következő cikket: [Leküldéses értesítések küldése iOS-eszközök az APNS és a Notification Hubs használatával](notification-hubs-ios-apple-push-notification-apns-get-started.md)
