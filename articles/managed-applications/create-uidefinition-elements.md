---
title: Az Azure felhasználói felület jelentésdefiníciós elem létrehozása |} A Microsoft Docs
description: Felhasználóifelület-definíciók az Azure portal kapcsolatot létesítő használandó-elemeket ismerteti.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 895b5cfb121347f9400d1aa315fef05d9088c55c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366471"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition elemek
Ez a cikk ismerteti a séma és a egy CreateUiDefinition minden támogatott elemei tulajdonságait. 

## <a name="schema"></a>Séma

A legtöbb elemét sémája a következő:

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

| Tulajdonság | Szükséges | Leírás |
| -------- | -------- | ----------- |
| név | Igen | Egy elem előfordulását hivatkozni belső azonosítója. Az elem nevét, a leggyakoribb használata `outputs`, ahol a kimeneti értékeket a megadott elemek vannak leképezve a sablon paramétereit. Kívánt elem értékét kimeneti kötést is használhatja a `defaultValue` másik elem. |
| type | Igen | Az elem megjelenítése a felhasználói felületi vezérlőnek. Támogatott típusainak listáját lásd: [elemek](#elements). |
| label | Igen | Az elem megjelenítendő szöveg. Néhány elemtípus tartalmazza a több címke, ezért az érték lehet több karakterláncokat tartalmazó objektumot. |
| DefaultValue érték | Nem | Az alapértelmezett érték az elem. Néhány elem típusát támogatja a összetett alapértelmezett értékeket, ezért az érték lehet egy objektumot. |
| Elemleírás | Nem | Az elem az elemleírás megjeleníteni kívánt szöveg. Hasonló `label`, bizonyos elemek támogatja a több eszközre tipp karakterlánc. Beágyazott hivatkozások Markdown szintaxissal lehet beágyazni.
| Korlátozások | Nem | Egy vagy több tulajdonságot, amely segítségével testre szabhatja az elem ellenőrzési viselkedését. Typ prvku megkötések támogatott tulajdonságai eltérők. Néhány elem típusát támogatja az ellenőrzési viselkedése testre szabhatja, és így vannak nincsenek megkötések tulajdonság. |
| beállítások | Nem | Testre szabható a az elem viselkedése további tulajdonságokat. Hasonló `constraints`, a támogatott tulajdonságok elem típusa szerint változó. |
| Látható | Nem | Azt jelzi, hogy az elem megjelenik-e. Ha `true`, az elem és a megfelelő gyermekelemek jelennek meg. Az alapértelmezett érték `true`. Használat [logikai függvények](create-uidefinition-functions.md#logical-functions) dinamikusan szabályozhatja e tulajdonság értéke.

## <a name="elements"></a>Elemek

A dokumentáció minden eleme egy felhasználói felületi mintát tartalmaz, sémát és a Megjegyzések a (általában vonatkozó érvényesítési és támogatott testreszabása) elemet, és a kimeneti viselkedését.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>További lépések
Felhasználóifelület-definíciók létrehozása bevezetésért lásd: [CreateUiDefinition használatának első lépései](create-uidefinition-overview.md).
