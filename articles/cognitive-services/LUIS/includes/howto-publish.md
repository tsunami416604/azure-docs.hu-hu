---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: 47913f86edcf83825254775705e38310f3c2c6b4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325937"
---
Ahhoz, hogy LUIS-előrejelzéseket kaphasson egy csevegőrobotban vagy más ügyfélalkalmazásban, közzé kell tennie az alkalmazást a végponton. 

1. A jobb felső navigációs menüben válassza a **Publish** (Közzététel) lehetőséget.

    ![A LUIS közzététel a végponton gombja a jobb felső menüben](../media/howto-publish/publish-button.png)

2. Válassza ki a **Production** (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

    ![LUIS közzététel a végponton](../media/howto-publish/publish-app-popup.png)

3. Publishing is complete when you see the status bar at the top of the page confirming success.

    ![LUIS közzététel a végponton](../media/howto-publish/publish-endpoint-success.png)

4. Select the **Access your endpoint URLs** link in the notification to go to the **Azure Resources** page. The endpoint URLs are listed as the **Example Query**.