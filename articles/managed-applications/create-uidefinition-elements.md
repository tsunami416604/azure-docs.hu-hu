---
title: "Az Azure által felügyelt alkalmazás, hozzon létre felhasználói felület definition funkciók |} Microsoft Docs"
description: "Az Azure által felügyelt alkalmazások felhasználói felületi definíciók konstrukciója során használandó funkcióit ismerteti"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 23e407bf93bc51116ca45339bffcb801d69290f0
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition elemek
Ez a cikk ismerteti a séma- és egy CreateUiDefinition minden támogatott elemei tulajdonságait. Ezek az elemek használata során [Azure által felügyelt alkalmazások létrehozására](publish-service-catalog-app.md). A sémát a legtöbb elemek a következőképpen történik:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "foobar",
  "toolTip": "Keep calm and visit the [Azure Portal](portal.azure.com).",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Tulajdonság | Szükséges | Leírás |
| -------- | -------- | ----------- |
| név | Igen | Belső azonosító való hivatkozáshoz egy elem előfordulását. A leggyakoribb használatát az elem neve van `outputs`, ahol a kimeneti megadott elemek képezi le, a sablon paramétereit. A kimeneti értéket egy elem kötése is használhatja a `defaultValue` másik elem. |
| type | Igen | A felhasználói felületének vezérlői elem megjelenítéséhez. A támogatott típusainak listáját lásd: [elemek](#elements). |
| Címke | Igen | Az elem szövegének. Néhány elemtípus több címke tartalmaz, ezért az érték lehet egy több karakterláncokat tartalmazó objektum. |
| DefaultValue érték | Nem | Az elem alapértelmezett értéke. Néhány elemtípus támogatja összetett alapértelmezett értékének megadását, az érték lehet egy objektumot. |
| Elemleírás | Nem | A elem az elemleírásban megjelenítendő szöveget. Hasonló `label`, bizonyos elemek támogatja a több eszköz tipp karakterláncokat. Beágyazott hivatkozásokat tartalmaz a Markdown-szintaxis használatával lehet beágyazott.
| Megkötések | Nem | Egy vagy több tulajdonságát, amely segítségével testre szabhatja az elem ellenőrzési viselkedését. Korlátozások a támogatott tulajdonságok elemtípus változhat. Néhány elemtípus nem támogatják az ellenőrzési viselkedését testreszabása, és így vannak megkötések tulajdonságot. |
| beállítások | Nem | Az elem működtető további tulajdonságok. Hasonló `constraints`, a támogatott tulajdonságok elemtípus változhat. |
| Látható | Nem | Azt jelzi, hogy megjelenik-e az elem. Ha `true`, az elem és a megfelelő gyermekelemek jelennek meg. Az alapértelmezett érték `true`. Használjon [logikai funkciók](create-uidefinition-functions.md#logical-functions) dinamikusan vezérléséhez Ez a tulajdonság értékét.

## <a name="elements"></a>Elemek

A dokumentáció egyes elemei a felhasználói felület mintát tartalmaz, séma, megjegyzések a (általában vonatkozó érvényesítése és a támogatott Testreszabás) elemet, és a minta kimenet működését.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Következő lépések
* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](overview.md).
* A bevezetést UI-definíciók létrehozásáról lásd: [Ismerkedés a CreateUiDefinition](create-uidefinition-overview.md).
