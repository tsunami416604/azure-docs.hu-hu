---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010498"
---
A streamnaplók bekapcsolása az Azure-ban a függvényalkalmazáshoz:

1. Az F1 billentyűparanccsal nyissa meg a parancskatalógust, és keresse meg, majd futtassa az **Azure Functions: Start Streaming Logs** parancsot.

1. Válassza ki a függvényalkalmazást az Azure-ban, majd válassza az **Igen** lehetőséget, hogy engedélyezze az alkalmazásnaplózást a függvényalkalmazás számára.

1. Aktiválja a függvényeket az Azure-ban. Ekkor a Visual Studio Code Output (Kimenet) ablakában naplózási adatok jelennek meg.

1. Ha végzett, ne feledje futtatni az **Azure Functions: Stop Streaming Logs** parancsot a függvényalkalmazás naplózásának leállításához.