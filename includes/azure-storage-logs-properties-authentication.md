---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711263"
---
| Tulajdonság | Leírás |
|:--- |:---|
|**identitás/típus** | A kérelem elvégzéséhez használt hitelesítés típusa. Például: `OAuth` ,, `SAS Key` `Account Key` , vagy `Anonymous` |
|**identitás/tokenHash**|Ez a mező csak belső használatra van fenntartva. |
|**engedélyezés/művelet** | A kérelemhez rendelt művelet. |
|**engedélyezés/roleAssignmentId** | A szerepkör-hozzárendelés azonosítója. Például: `4e2521b7-13be-4363-aeda-111111111111`.|
|**engedélyezés/roleDefinitionId** | A szerepkör-definíció azonosítója. Például: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**rendszerbiztonsági tag/azonosító** | A rendszerbiztonsági tag azonosítója. Például: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**rendszerbiztonsági tag/típus** | A rendszerbiztonsági tag típusa. Például: `ServicePrincipal`. |
|**kérelmező/appID** | A kérelmezőként használt Open Authorization-(OAuth-) alkalmazás azonosítója. <br> Például: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**kérelmező/célközönség** | A kérelem OAuth célközönsége. Például: `https://storage.azure.com`. |
|**kérelmező/objectId** | A kérelmező OAuth-objektumának azonosítója. Kerberos-hitelesítés esetén a a Kerberos által hitelesített felhasználó objektumazonosítót jelöli. Például: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**kérelmező/tenantId** | Az identitás OAuth-bérlő azonosítója. Például: `72f988bf-86f1-41af-91ab-222222222222`.|
|**kérelmező/tokenIssuer** | Az OAuth jogkivonat kiállítója. Például: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**kérelmező/UPN** | A kérelmező egyszerű felhasználóneve (UPN). Például: `someone@contoso.com`. |
|**kérelmező/Felhasználónév** | Ez a mező csak belső használatra van fenntartva.|