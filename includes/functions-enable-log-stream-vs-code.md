---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/25/2019
ms.author: glenga
ms.openlocfilehash: 437b4ab62cc8c4903af88ca2f9632e89b953c798
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "68669669"
---
Az Azure-beli Function alkalmazáshoz tartozó folyamatos átviteli naplók bekapcsolása:

1. Válassza az F1 billentyűt a parancs paletta megnyitásához, majd keresse meg és futtassa a parancsot **Azure functions: Start streaming logs**.

1. Válassza ki a Function alkalmazást az Azure-ban, majd válassza az **Igen** lehetőséget az alkalmazás naplózásának engedélyezéséhez a Function alkalmazásban.

1. Aktiválja funkcióit az Azure-ban. Figyelje meg, hogy a naplózási adatokat a rendszer a Visual Studio Code kimenet ablakában jeleníti meg.

1. Ha elkészült, ne felejtse el futtatni a (z) **Azure functions parancsot: a streaming-naplók leállítása** a Function alkalmazás naplózásának letiltásához.