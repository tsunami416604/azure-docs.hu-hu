---
title: Fenntartott Azure-erőforrás neve hibák |} A Microsoft Docs
description: Ismerteti, hogyan lehet javítsa ki a hibákat, ha egy erőforrás-nevet, amely tartalmaz egy fenntartott szó.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 922389b7c6c1bb7ad1d9b8f6ec35ccc1c5656723
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390851"
---
# <a name="resolve-reserved-resource-name-errors"></a>Fenntartott erőforrás neve ki a hibákat

Ez a cikk ismerteti a hibát tapasztal egy erőforrást a név egy fenntartott szó tartalmazó telepítésekor.

## <a name="symptom"></a>Jelenség

Egy erőforrás, amely elérhető egy nyilvános végpontot üzembe helyezésekor, a következő hiba jelenhet meg:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Ok

Egy nyilvános végponttal rendelkező erőforrások a név nem használható célra fenntartott szavakat vagy védjegyei.

A következő szavak vannak fenntartva:

* HOZZÁFÉRÉS
* AZ AZURE
* BING
* BIZSPARK
* BIZTALK
* CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* AZ EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENS
* HYPERV
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE
* ONENOTE-BAN
* OUTLOOK
* A POWERPOINT
* A SHAREPOINT
* SKYPE
* VISIO
* VISUALSTUDIO

A következő szöveg teljes szavankénti vagy a név egy karakterláncrészletet nem használható:

* LOGIN
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Megoldás

Adjon meg egy nevet, amely nem használ egy fenntartott szó.