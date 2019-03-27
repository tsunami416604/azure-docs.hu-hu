---
title: A Microsoft leküldéses értesítési szolgáltatás konfigurálása az Azure Notification Hubs |} A Microsoft Docs
description: Ismerje meg az Azure notification hub Microsoft Push Notification Service-beállítások konfigurálása.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 1c76b44438e6527439d0a370c92f4120424b8da5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58488357"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-for-a-notification-hub-in-the-azure-portal"></a>Egy értesítési központ a Microsoft leküldéses értesítési szolgáltatásának (MPNS) beállításainak konfigurálása az Azure Portalon
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure notification hub beállításai a Microsoft leküldéses értesítési szolgáltatásának (MPNS) az Azure portal használatával. 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem hozott létre egy értesítési központ, hozzon létre egyet. További információkért lásd: [Azure notification hub létrehozása az Azure Portalon](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>A Microsoft leküldéses értesítéseket kezelő szolgáltatása (MPNS) konfigurálása

Az alábbi eljárás biztosít egy értesítési központ a Microsoft leküldéses értesítési szolgáltatásának (MPNS) beállítások konfigurálásának lépései: 

1. Az Azure Portalon az a **értesítési központ** lapon jelölje be **Windows Phone (MPNS)** a bal oldali menüben.
1. Vagy nem hitelesített vagy hitelesített leküldéses értesítések engedélyezése:

   a. Nem hitelesített leküldéses értesítések engedélyezéséhez jelölje be **nem hitelesített leküldés engedélyezése** > **mentése**.

      ![Képernyőkép a nem hitelesített leküldéses értesítések engedélyezése](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Hitelesített leküldéses értesítések engedélyezése:
      * Válassza az eszköztár **tanúsítvány feltöltése**.
      * A fájl ikonra, és válassza ki a tanúsítványfájl.
      * Adja meg a tanúsítványhoz tartozó jelszót.
      * Kattintson az **OK** gombra.
      * Az a **Windows Phone (MPNS)** lapon jelölje be **mentése**.

## <a name="next-steps"></a>További lépések
A részletes oktatóanyag az értesítések leküldése a Windows Phone-eszközök Azure Notification Hubs és a Microsoft leküldéses értesítési szolgáltatásának (MPNS) használatával, lásd: [leküldéses értesítések Windows Phone-alkalmazások értesítési használatával Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

