---
title: Felhasználói felületdefiníció-elemeinek létrehozása
description: Az Azure Portal felhasználói felületi definícióinak létrehozásához használandó elemek ismertetése.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086708"
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
| név | Igen | Egy elem adott példányára hivatkozó belső azonosító. Az elemnév leggyakoribb használata a `outputs`, ahol a megadott elemek kimeneti értékei a sablon paramétereihez vannak rendelve. Arra is használhatja, hogy egy elem kimeneti értékét egy másik `defaultValue` elemhez kösse. |
| type | Igen | Az elem rendereléséhez használt felhasználói felület vezérlője. A támogatott típusok listáját az Elemek című [témakörben tetszésszerint.](#elements) |
| label | Igen | Az elem megjelenített szövege. Egyes elemtípusok több címkét tartalmaznak, így az érték több karakterláncot tartalmazó objektum is lehet. |
| defaultValue | Nem | Az elem alapértelmezett értéke. Egyes elemtípusok összetett alapértelmezett értékeket támogatnak, így az érték objektum is lehet. |
| Tooltip | Nem | Az elem eszközhegyében megjelenítendő szöveg. A `label`( a) elemhez hasonlóan egyes elemek több eszköztip karakterláncot is támogatnak. A szövegközi hivatkozások a Markdown szintaxissal ágyazhatók be.
| Korlátok | Nem | Egy vagy több tulajdonság, amely az elem érvényesítési viselkedésének testreszabására szolgál. A megkötések támogatott tulajdonságai elemtípusonként változnak. Egyes elemtípusok nem támogatják az érvényesítési viselkedés testreszabását, és így nincsenek kényszertulajdonságuk. |
| Lehetőségek | Nem | További tulajdonságok, amelyek az elem viselkedését testreszabják. A `constraints`(k) hoz hasonlóan a támogatott tulajdonságok elemtípusonként változnak. |
| Látható | Nem | Azt jelzi, hogy az elem megjelenik-e. Ha `true`megjelenik az elem és a megfelelő gyermekelemek. Az alapértelmezett érték `true`. A tulajdonság értékének dinamikus vezérléséhez [használjon logikai függvényeket.](create-uidefinition-functions.md#logical-functions)

## <a name="elements"></a>Elemek

Az egyes elemek dokumentációja tartalmaz egy felhasználói felületi mintát, sémát, az elem viselkedésére vonatkozó megjegyzéseket (általában az érvényesítéssel és a támogatott testreszabással kapcsolatban), valamint a mintakimenetet.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TagsByForrás](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.IdentityVálasztó](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>További lépések

A felhasználói felületdefiníciók létrehozásának bemutatása a [CreateUiDefinition](create-uidefinition-overview.md)első lépései című témakörben látható.
