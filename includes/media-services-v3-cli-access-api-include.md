---
title: fájl belefoglalása
description: fájl belefoglalása
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 04/13/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: acb9bdf294dd66005df203f957c155540b658698
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
## <a name="access-the-media-services-api"></a>Hozzáférés a Media Services API-hoz

Az Azure Media Services API-khoz való csatlakozáshoz használja az Azure AD szolgáltatásnév hitelesítését. Az alábbi parancs létrehoz egy Azure AD-alkalmazást, majd egy szolgáltatásnevet csatol a fiókhoz. A .NET-alkalmazás konfigurálásához a visszaadott értékeket fogja használni, ahogy az alábbi lépésben szerepel.

A szkript futtatása előtt kicserélheti az `amsaccount` és `amsResourceGroup` értékeket az erőforrások létrehozásakor választott nevekkel. `amsaccount`: a szolgáltatásnév csatolásához használni kívánt Azure Media Services-fiók neve. <br/>A következő parancs az `xml` beállítást használja Ez egy olyan XML-fájlt ad vissza, amelyet beilleszthet az app.config fájlba. Ha kihagyja az `xml` beállítást, a válasz `json` formátumban jelenik meg..

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```

Ez a parancs a következőhöz hasonló választ ad:

```xml
<add key="Region" value="West US 2" />
<add key="ResourceGroup" value="amsResourceGroup" />
<add key="AadEndpoint" value="https://login.microsoftonline.com" />
<add key="AccountName" value="amsaccount" />
<add key="SubscriptionId" value="111111111-0000-2222-3333-55555555555" />
<add key="ArmAadAudience" value="https://management.core.windows.net/" />
<add key="AadTenantId" value="2222222222-0000-2222-3333-6666666666666" />
<add key="AadSecret" value="33333333-0000-2222-3333-55555555555" />
<add key="AadClientId" value="44444444-0000-2222-3333-55555555555" />
<add key="ArmEndpoint" value="https://management.azure.com/" />
```

### <a name="configure-the-sample-app"></a>A mintaalkalmazás konfigurálása

Az alkalmazás futtatásához és a Media Services API-k eléréséhez a megfelelő hozzáférési értékeket kell megadnia az App.config fájlban. 

1. Nyissa meg a Visual Studiót.
2. Tallózással keresse meg az Ön által klónozott megoldást.
3. A Solution Explorer (Megoldáskezelő) ablakában bontsa ki az *EncodeAndStreamFiles* projektet.
4. Állítsa be ezt a projektet kezdő projektként.
5. Nyissa meg az App.config fájlt.
6. Cserélje le az appSettings-értékeket az előző lépésben beszerzett értékekre.

 ```xml
 <add key="Region" value="value" />
 <add key="ResourceGroup" value="value" />
 <add key="AadEndpoint" value="value" />
 <add key="AccountName" value="value" />
 <add key="SubscriptionId" value="value" />
 <add key="ArmAadAudience" value="value" />
 <add key="AadTenantId" value="value" />
 <add key="AadSecret" value="value" />
 <add key="AadClientId" value="value" />
 <add key="ArmEndpoint" value="value" />
 ```    
 
7. Nyomja le a Ctrl+Shift+B billentyűkombinációt a megoldás felépítéséhez.
