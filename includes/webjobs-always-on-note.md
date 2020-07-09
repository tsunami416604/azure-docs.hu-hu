---
title: fájlbefoglalás
description: fájlbefoglalás
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 02/19/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 82e5221daefaecb687ad9feb79305e546d4ec17e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "68424155"
---
> [!NOTE]
> A webalkalmazások 20 perc inaktivitás után időtúllépést okozhatnak. Csak a tényleges webalkalmazásnak küldött kérések állítják vissza az időzítőt. Ha megtekinti az alkalmazás konfigurációját a Azure Portalban, vagy kéréseket tesz a speciális eszközök helyének ( `https://<app_name>.scm.azurewebsites.net` ), ne állítsa alaphelyzetbe az időzítőt. Ha az alkalmazás folyamatos vagy ütemezett (időzítő trigger) webjobs-feladatokat futtat, engedélyezze a **mindig be** lehetőséget a webjobs megbízható működésének biztosításához. Ez a funkció csak az alapszintű, a standard és a prémium [szintű díjszabásban](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)érhető el.
