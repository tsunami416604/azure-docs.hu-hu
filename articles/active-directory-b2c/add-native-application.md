---
title: Natív ügyfélalkalmazás hozzáadása – Azure Active Directory B2C | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá natív ügyfélalkalmazás a Active Directory B2C bérlőhöz.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6fc953d5c6109fbc6eacbd946ecd112db4639fa5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064596"
---
# <a name="add-a-native-client-application-to-your-azure-active-directory-b2c-tenant"></a>Natív ügyfélalkalmazás hozzáadása a Azure Active Directory B2C bérlőhöz

A natív ügyfél-erőforrásokat regisztrálni kell a bérlőn, mielőtt az alkalmazás kommunikálni tudjon a Azure Active Directory B2Cokkal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.
2. Adja meg az alkalmazás nevét. Például: *nativeapp1*.
3. A **Web App/web API**belefoglalásához válassza a **nem**lehetőséget.
4. A **natív ügyfél**belefoglalása beállításnál válassza az **Igen**lehetőséget.
5. Az **átirányítási URI**esetében adjon meg egy érvényes átirányítási URI-t egy egyéni sémával. Az átirányítási URI kiválasztásakor két fontos szempontot kell figyelembe venni:

    - **Egyedi** – az átirányítási URI sémájának minden alkalmazás esetében egyedinek kell lennie. A példában `com.onmicrosoft.contoso.appname://redirect/path` `com.onmicrosoft.contoso.appname` a séma. Ezt a mintát kell követni. Ha két alkalmazás ugyanazt a sémát használja, a felhasználó választhat egy alkalmazást. Ha a felhasználó helytelenül választ, a bejelentkezés sikertelen lesz.
    - **Befejezés** – az átirányítási URI-nak rendelkeznie kell sémával és elérési úttal. Az elérési útnak legalább egy perjelet tartalmaznia kell a tartomány után. Például `//contoso/` működik és `//contoso` sikertelen. Győződjön meg arról, hogy az átirányítási URI nem tartalmaz speciális karaktereket, például aláhúzást.

6. Kattintson a **Create** (Létrehozás) gombra.
7. A Tulajdonságok lapon jegyezze fel a natív ügyfélalkalmazás konfigurálásakor használni kívánt alkalmazás AZONOSÍTÓját.
