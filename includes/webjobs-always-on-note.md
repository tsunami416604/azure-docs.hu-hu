---
title: fájl belefoglalása
description: fájl belefoglalása
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68424155"
---
> [!NOTE]
> Egy webalkalmazás 20 perc inaktivitás után idővel elévülhet. Csak a tényleges webalkalmazásnak küldött kérelmek alaphelyzetbe állítják az időzítőt. Az alkalmazás konfigurációjának megtekintése az Azure Portalon, vagy`https://<app_name>.scm.azurewebsites.net`a speciális eszközök webhelyére ( ) nem állítja alaphelyzetbe az időzítőt. Ha az alkalmazás folyamatos vagy ütemezett (időzítő eseményindító) WebJobs-ot futtat, engedélyezze **az Always On alkalmazást,** hogy a WebJobs megbízhatóan fusson. Ez a funkció csak az alapszintű, standard és prémium [szintű tarifacsomagokban](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)érhető el.
