---
title: Azure Create UI definition Element | Microsoft Docs
description: A Azure Portal felhasználói felületi definícióinak összeállításakor használandó elemeket ismerteti.
author: tfitzmac
ms.service: managed-applications
ms.topic: reference
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 9f952b8301f1d85d81fcc63e5d46dc57b1fb1106
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932005"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition elemek

Ez a cikk a CreateUiDefinition összes támogatott elemének sémáját és tulajdonságait ismerteti. 

## <a name="schema"></a>Séma

A legtöbb elem sémája a következő:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Tulajdonság | Kötelező | Leírás |
| -------- | -------- | ----------- |
| név | Igen | Egy belső azonosító, amely egy elem adott példányára hivatkozik. Az elemnév leggyakoribb használata `outputs`, ahol a megadott elemek kimeneti értékei a sablon paramétereinek megfelelően vannak leképezve. Azt is megteheti, hogy egy elem kimeneti értékét egy másik elem `defaultValue`éhez köti. |
| type | Igen | Az elemhez tartozó FELHASZNÁLÓIFELÜLET-vezérlő. A támogatott típusok listáját lásd: [elemek](#elements). |
| label | Igen | Az elem megjelenített szövege Egyes elemek több címkét tartalmaznak, így az érték lehet egy több sztringet tartalmazó objektum. |
| defaultValue | Nem | Az elem alapértelmezett értéke. Egyes elemtípus-típusok összetett alapértelmezett értékeket támogatnak, így az érték lehet egy objektum. |
| toolTip | Nem | Az elem elemleírásában megjelenítendő szöveg A `label`hoz hasonlóan egyes elemek több elemleírás-karakterláncot is támogatnak. A beágyazott hivatkozások a Markdown szintaxis használatával ágyazhatók be.
| korlátok | Nem | Egy vagy több olyan tulajdonság, amely az elem érvényesítési viselkedésének testreszabására szolgál. A megkötések támogatott tulajdonságai elem típusa szerint változnak. Egyes elemtípus-típusok nem támogatják az érvényesítési viselkedés testreszabását, így nincsenek megkötések tulajdonsága. |
| beállítások | Nem | További tulajdonságok, amelyek testre szabják az elem viselkedését. A `constraints`hoz hasonlóan a támogatott tulajdonságok az elemtípus szerint változnak. |
| Látható | Nem | Azt jelzi, hogy megjelenik-e az elem. Ha `true`, az elem és a megfelelő alárendelt elemek jelennek meg. Az alapértelmezett érték `true`. A tulajdonság értékének dinamikus vezérléséhez használja a [logikai függvényeket](create-uidefinition-functions.md#logical-functions) .

## <a name="elements"></a>Elemek

Az egyes elemek dokumentációja tartalmaz egy felhasználói felületi mintát, sémát, a elem viselkedésére vonatkozó megjegyzéseket (általában az érvényesítéssel és a támogatott testreszabással kapcsolatban) és a minta kimenetével.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. bezárása](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Következő lépések

A felhasználói felületi definíciók létrehozásával kapcsolatban lásd: Bevezetés [a CreateUiDefinition](create-uidefinition-overview.md)használatába.
