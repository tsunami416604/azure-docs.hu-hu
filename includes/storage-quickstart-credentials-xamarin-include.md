---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006236"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

Ha a minta alkalmazás az Azure Storage-ba irányuló kérést tesz elérhetővé, akkor azt engedélyezni kell. A kérések engedélyezéséhez adja hozzá a Storage-fiók hitelesítő adatait az alkalmazáshoz kapcsolódási karakterláncként. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

A kapcsolódási karakterlánc másolását követően állítsa azt a *MainPage.XAML.cs* -fájlban lévő Class Level változóra. Nyissa meg a *MainPaage.XAML.cs* , `storageConnectionString` és keresse meg a változót. Cserélje `<yourconnectionstring>` le a-t a tényleges kapcsolatok karakterláncára.

A kód a következő:

```csharp
string storageConnectionString = "<yourconnectionstring>";
```