---
title: A Microsoft leküldéses értesítési szolgáltatásának konfigurálása az Azure Notification Hubsban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a Microsoft leküldéses értesítési szolgáltatás beállításait egy Azure Notification hub számára.
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
ms.openlocfilehash: 0d7bf5410e05bb74a215fb7a95c704673b764b93
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212477"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>A Microsoft leküldéses értesítési szolgáltatás (MPNS) beállításainak konfigurálása egy értesítési Központ számára a Azure Portal
Ez a cikk bemutatja, hogyan konfigurálhatja a Microsoft leküldéses értesítési szolgáltatás (MPNS) beállításait egy Azure Notification hub számára a Azure Portal használatával. 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központot, hozzon létre egyet most. További információ: [Azure Notification hub létrehozása a Azure Portalban](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>A Microsoft push Notification szolgáltatás (MPNS) konfigurálása

Az alábbi eljárás lépéseit követve konfigurálhatja az értesítési központ Microsoft leküldéses értesítési szolgáltatásának (MPNS) beállításait: 

1. A Azure Portal az **értesítési központ** lapon válassza a **Windows Phone-telefon (MPNS)** elemet a bal oldali menüben.
1. Nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. A nem hitelesített leküldéses értesítések engedélyezéséhez válassza a nem **hitelesített leküldéses** > **Mentés**engedélyezése lehetőséget.

      ![A nem hitelesített leküldéses értesítések engedélyezését bemutató képernyőkép](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Az eszköztáron válassza a **tanúsítvány feltöltése**lehetőséget.
      * Válassza ki a fájl ikont, majd válassza ki a tanúsítványfájl.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Kattintson az **OK** gombra.
      * A **Windows Phone-telefon (MPNS)** lapon válassza a **Mentés**lehetőséget.

## <a name="next-steps"></a>További lépések
Az Azure Notification Hubs és a Microsoft leküldéses értesítési szolgáltatás (MPNS) használatával Windows Phone-telefon eszközökre irányuló értesítések leküldésével kapcsolatos részletes utasításokért tekintse meg a [leküldéses értesítéseket Windows Phone-telefon alkalmazásoknak Notification Hubs használatával](notification-hubs-windows-mobile-push-notifications-mpns.md)című témakört.

