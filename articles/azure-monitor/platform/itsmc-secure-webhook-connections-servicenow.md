---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitor – konfiguráció a ServiceNow
description: Ez a cikk bemutatja, hogyan csatlakoztathatók a ITSM-termékek/szolgáltatások a ServiceNow-mel a biztonságos exportálással Azure Monitorban.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841193"
---
# <a name="connect-servicenow-to-azure-monitor"></a>A ServiceNow Azure Monitorhoz való kapcsolódása

A következő szakaszokban részletesen ismertetjük a ServiceNow-termék összekapcsolását és az Azure-beli biztonságos exportálást.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy teljesítette a következő előfeltételeket:

* Az Azure AD regisztrálva van.
* A ServiceNow Event Management-ITOM támogatott verziója (Orlando vagy újabb verzió).

## <a name="configure-the-servicenow-connection"></a>A ServiceNow-kapcsolatok konfigurálása

1. Használja a https://(példány neve). Service-Now. com/API/sn_em_connector/em/inbound_event? Source = azuremonitor a biztonságos exportálás definíciójának URI-JÁT.

2. Kövesse az utasításokat a verziónak megfelelően:
   * [Párizs](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)