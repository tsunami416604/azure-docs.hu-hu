---
title: A csoportok – Azure Active Directory dinamikus csoporttagság problémák javítása |} A Microsoft Docs
description: Hibaelhárítási tippek az Azure AD dinamikus csoporttagság.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1fef19c555b9d2e52d4734a8f7bc5e39183e684
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56169309"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása

**Konfiguráltam egy szabály a csoport, de a csoport frissítésére nincs tagságok**<br/>Ellenőrizze a szabály a felhasználói attribútumok értékei: vannak-e felhasználók, amelyek megfelelnek a szabályt? Ha mindent rendben talál, engedélyezze a csoport feltöltése egy kis ideig. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Egy szabály konfiguráltam, de mostantól a meglévő csoporttagok, a szabály el lesznek távolítva**<br/>Ez az elvárt működés. A csoport tagjai meglévő el lesznek távolítva, amikor egy szabály engedélyezve van, vagy módosítani. A kiértékelés a szabály által visszaadott felhasználók tagjai a csoportba kerülnek.

**Nem látom a tagsága instantly hozzáadása vagy egy szabály, miért nem módosítása esetén?**<br/>Dedikált gyűjteménytagság értékelése egy aszinkron háttérfolyamat rendszeres időközönként történik. Mennyi ideig tart határozza meg a címtárban található felhasználók száma és mérete miatt a szabály létrehozta a csoportot. A felhasználók kis számú könyvtárak általában megjelenik a csoporttagsági változások kisebb, mint néhány perc múlva. A felhasználók nagy számú könyvtárak 30 percet is igénybe vehet, vagy hosszabb adatokkal való feltöltéséhez.

**Egy szabály feldolgozási hiba jelent meg.**<br/>A következő táblázat felsorolja a dinamikus tagsági szabály előforduló gyakori hibák és a rendszernaplóban őket.

| Hiba történt a szabály elemző | Hiba használat | Javított használat |
| --- | --- | --- |
| Hiba: Az attribútum nem támogatott. |(user.invalidProperty -eq "Value") |(user.department - eq "value")<br/><br/>Ellenőrizze, hogy a rendszer az attribútum a [támogatott tulajdonságok listája](groups-dynamic-membership.md#supported-properties). |
| Hiba: Operátor nem támogatott az attribútum. |(user.accountEnabled – igaz tartalmaz) |(user.accountEnabled -eq true)<br/><br/>Az operátor nem támogatott a tulajdonság típusa (ebben a példában-tartalmaz nem használható írja be a logikai érték). Használja a megfelelő operátorok a tulajdonság típusát. |
| Hiba: Sémafordítási hiba történt a lekérdezés. | 1. (user.department - eq "Értékesítés") (user.department - eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. Hiányzó operátor. Használja a - és vagy - vagy két predikátumok csatlakoztatása<br>(user.department - eq "Értékesítés") – vagy (user.department - eq "Marketing")<br>2. Hiba – a használt reguláris kifejezést az felel meg<br>(user.userPrincipalName -match ".*@domain.ext")<br>vagy másik lehetőségként: (user.userPrincipalName-egyezik "@domain.ext$") |

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazáskezelés az Azure Active Directory használatával](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../hybrid/whatis-hybrid-identity.md)
