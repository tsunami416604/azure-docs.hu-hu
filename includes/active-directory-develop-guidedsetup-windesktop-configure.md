---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: b26b88d0e089217fa9915bdbdcb8f913731bcc67
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988193"
---
## <a name="register-your-application"></a>Alkalmazás regisztrálása

Az alkalmazás két módon regisztrálhatja.

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód

Az alkalmazás gyorsan regisztrálhatja az alábbiak szerint:
1. Lépjen a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Válassza ki **alkalmazás hozzáadása**.

3. Az **Alkalmazás neve** mezőben nevezze el az alkalmazását.

4. Ügyeljen arra, hogy a **interaktív telepítés** kiválasztott, és adja meg a jelölőnégyzet nincs **létrehozás**.

5. Kövesse az utasításokat az Alkalmazásazonosító beszerzése, és illessze be a kódot.

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális módban

Az alkalmazása regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához tegye a következőket:
1. Ha már az alkalmazás még nem regisztrált, lépjen a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app).

2. Válassza ki **alkalmazás hozzáadása**.

3. Az **Alkalmazás neve** mezőben nevezze el az alkalmazását.

4. Ügyeljen arra, hogy az **Irányított beállítás** jelölőnégyzet ne legyen bejelölve, majd válassza a **Létrehozás** lehetőséget.

5. Válassza a **Platform hozzáadása**, a **Natív alkalmazás**, majd a **Mentés** lehetőséget.

6. Az a **Alkalmazásazonosító** mezőbe másolja a GUID Azonosítót.

7. Lépjen a Visual Studióban nyissa meg a *App.xaml.cs* fájlt, és cserélje `your_client_id_here` az imént regisztrált és másolt alkalmazás azonosítójával.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
