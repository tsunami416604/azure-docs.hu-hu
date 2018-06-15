---
title: Azure fenntartott erőforrás utat |} Microsoft Docs
description: Javítsa a hibákat, erőforrás nevét, amely tartalmaz egy fenntartott szó nyújtásakor ismerteti.
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
ms.openlocfilehash: b91a53d17d64afb0a56f745505f10e8cabbc22cc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357061"
---
# <a name="resolve-reserved-resource-name-errors"></a>Hárítsa el a hibákat a lefoglalt erőforrás neve

Ez a cikk ismerteti az egy erőforrást, a név egy fenntartott szó tartalmazó telepítése során előforduló hiba.

## <a name="symptom"></a>Jelenség

Egy erőforrást egy nyilvános végpontot keresztül elérhető való telepítésekor a következő hibaüzenet jelenhet meg:

```
Code=ReservedResourceName;
Message=The resource name <resource-name> or a part of the name is a trademarked or reserved word.
```

## <a name="cause"></a>Ok

Erőforrásokat, amelyek egy nyilvános végpontot a név nem használható célra fenntartott szavakat vagy védjegye.

A következő szavak számára vannak fenntartva:

* HOZZÁFÉRÉS
* AZURE
* BING
* BIZSPARK
* BIZTALK
* A CORTANA
* DIRECTX
* DOTNET
* DYNAMICS
* EXCEL
* EXCHANGE
* FOREFRONT
* GROOVE
* HOLOLENSRE
* HYPER-V
* KINECT
* LYNC
* MSDN
* O365
* OFFICE
* OFFICE365
* ONEDRIVE VÁLLALATI VERZIÓ
* ONENOTE-BAN
* OUTLOOK
* POWERPOINT
* SHAREPOINT
* SKYPE
* A VISIO
* VISUALSTUDIO

A következő szavak szavakat vagy a substring nevében nem használható:

* BEJELENTKEZÉS
* MICROSOFT
* WINDOWS
* XBOX

## <a name="solution"></a>Megoldás

Adjon meg egy nevet, amely nem használ egy fenntartott szó.