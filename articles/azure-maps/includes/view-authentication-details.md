---
title: Azure Maps hitelesítési adatainak megtekintése
description: A Azure Portal használatával megtekintheti Azure Maps hitelesítési adatait.
author: philmea
ms.author: philmea
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 9146844a6b83f78ad99ef7cd1aec4b028daf3ff6
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988582"
---
A Azure Portalban megtekintheti a Azure Maps fiók hitelesítésének részleteit. A fiókjában a **Beállítások** menüben válassza a **hitelesítés**lehetőséget.

![Hitelesítés részletei](../media/how-to-manage-authentication/how-to-view-auth.png)

Azure Maps fiók létrehozása után a Azure Maps `x-ms-client-id` érték szerepel az Azure Portal-hitelesítés részletei lapon. Ez az érték azt a fiókot jelöli, amelyet a rendszer a REST API kérelmekhez fog használni. Ezt az értéket az alkalmazás-konfigurációban kell tárolni, és a HTTP-kérések megkezdése előtt be kell olvasni az Azure AD-hitelesítés Azure Maps használatával történő használatakor.
