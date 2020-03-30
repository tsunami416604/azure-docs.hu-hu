---
title: A Microsoft leküldéses értesítési szolgáltatáskonfigurálása az Azure Értesítési központokban | Microsoft dokumentumok
description: Megtudhatja, hogyan konfigurálhatja a Microsoft Leküldéses értesítési szolgáltatás beállításait egy Azure-értesítési központhoz.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127329"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>A Microsoft Leküldéses értesítési szolgáltatás (MPNS) beállításainak konfigurálása az Azure Portalon

Ez a cikk bemutatja, hogyan konfigurálhatja a Microsoft Leküldéses értesítési szolgáltatás (MPNS) beállításait egy Azure értesítési központ az Azure Portal használatával. 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre értesítési központot, hozzon létre egyet most. További információ: [Azure-értesítési központ létrehozása az Azure Portalon.](create-notification-hub-portal.md) 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Microsoft leküldéses értesítési szolgáltatás (MPNS) konfigurálása

Az alábbi eljárás a Microsoft Leküldéses értesítési szolgáltatás (MPNS) értesítési központ beállításainak konfigurálásának lépéseit tartalmazza: 

1. Az Azure Portalon az **Értesítési központ** lapon válassza a **Windows Phone (MPNS)** elemet a bal oldali menüben.
1. Nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. A nem hitelesített leküldéses értesítések engedélyezéséhez jelölje be **a Nem hitelesített leküldéses** > **mentés engedélyezése jelölőnégyzetet.**

      ![Képernyőkép, amely bemutatja, hogyan lehet engedélyezni a nem hitelesített leküldéses értesítéseket](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Az eszköztáron válassza a **Tanúsítvány feltöltése**lehetőséget.
      * Jelölje ki a fájl ikonját, majd a tanúsítványfájlt.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Válassza **az OK gombot.**
      * A **Windows Phone (MPNS)** lapon válassza a **Mentés lehetőséget.**

## <a name="next-steps"></a>További lépések
Az Értesítések Szolgáltatás központ és a Microsoft Leküldéses értesítési szolgáltatás (MPNS) használatával a Windows Phone-eszközökre történő értesítések leküldéses útmutatóját a [Leküldéses értesítések a Windows Phone-alkalmazásokba című](notification-hubs-windows-mobile-push-notifications-mpns.md)témakörben találja.

