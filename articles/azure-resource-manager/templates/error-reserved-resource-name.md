---
title: Fenntartott erőforrásnév-hibák
description: Ez a témakör azt ismerteti, hogy miként hárítható el a hibák, amikor fenntartott szót tartalmazó erőforrásnevet ad meg.
ms.topic: troubleshooting
ms.date: 11/08/2017
ms.openlocfilehash: e76f4bf9bfee7de6e7523d69acf1388d2dd80e93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75477628"
---
# <a name="resolve-reserved-resource-name-errors"></a>Fenntartott erőforrásnév-hibák elhárítása

Ez a cikk azt a hibát ismerteti, amellyel egy olyan erőforrás telepítésekor találkozhat, amely a nevében egy fenntartott szót tartalmaz.

## <a name="symptom"></a>Hibajelenség

Nyilvános végponton keresztül elérhető erőforrás telepítésekor a következő hibaüzenet jelenhet meg:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Ok

A nyilvános végponttal ellátott erőforrások nem használhatnak fenntartott szavakat vagy védjegyeket a névben.

A következő szavak vannak fenntartva:

* HOZZÁFÉRÉS
* Azure
* Bing
* Bizspark
* Biztalk
* CORTANA (CORTANA)
* Directx
* Dotnet
* Dynamics
* EXCEL
* Exchange
* Élvonalban
* Groove
* HOLOLENS
* HIPERV
* Kinect
* Lync
* MSDN
* O365
* Office
* OFFICE365
* ONEDRIVE
* Onenote
* Outlook
* Powerpoint
* Sharepoint
* Skype
* Visio
* VISUALSTUDIO (VIZUÁLIS STÚDIÓ)

A következő szavak nem használhatók teljes szóként vagy karakterláncrészként a névben:

* Logika
* Microsoft
* Windows
* Xbox

## <a name="solution"></a>Megoldás

Adjon meg egy olyan nevet, amely nem használja a fenntartott szavak egyikét sem.