---
title: A csoportok dinamikus tagság hibaelhárítása |} A Microsoft Docs
description: Hibaelhárítási tippek az Azure AD dinamikus csoporttagság.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: c616618df0ef3a7b97a8d90977559889022db232
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44718474"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása

**Konfiguráltam egy szabály a csoport, de a csoport frissítésére nincs tagságok**<br/>Ellenőrizze a szabály a felhasználói attribútumok értékei: vannak-e felhasználók, amelyek megfelelnek a szabályt? Ha mindent rendben talál, engedélyezze a csoport feltöltése egy kis ideig. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Egy szabály konfiguráltam, de mostantól a meglévő csoporttagok, a szabály el lesznek távolítva**<br/>Ez az elvárt működés. A csoport tagjai meglévő el lesznek távolítva, amikor egy szabály engedélyezve van, vagy módosítani. A kiértékelés a szabály által visszaadott felhasználók tagjai a csoportba kerülnek.

**Nem látom a tagsága instantly hozzáadása vagy egy szabály, miért nem módosítása esetén?**<br/>Dedikált gyűjteménytagság értékelése egy aszinkron háttérfolyamat rendszeres időközönként történik. Mennyi ideig tart határozza meg a címtárban található felhasználók száma és mérete miatt a szabály létrehozta a csoportot. A felhasználók kis számú könyvtárak általában megjelenik a csoporttagsági változások kisebb, mint néhány perc múlva. A felhasználók nagy számú könyvtárak 30 percet is igénybe vehet, vagy hosszabb adatokkal való feltöltéséhez.

**Egy szabály feldolgozási hiba jelent meg.**<br/>A következő táblázat felsorolja a dinamikus tagsági szabály előforduló gyakori hibák és a rendszernaplóban őket.

| Hiba történt a szabály elemző | Hiba használat | Javított használat |
| --- | --- | --- |
| Chyba: Attribútum nem támogatott. |(user.invalidProperty - eq "Value") |(user.department - eq "value")<br/><br/>Ellenőrizze, hogy a rendszer az attribútum a [támogatott tulajdonságok listája](groups-dynamic-membership.md#supported-properties). |
| Chyba: Operátor nem támogatott az attribútum. |(user.accountEnabled – igaz tartalmaz) |(user.accountEnabled - eq true)<br/><br/>Az operátor nem támogatott a tulajdonság típusa (ebben a példában-tartalmaz nem használható írja be a logikai érték). Használja a megfelelő operátorok a tulajdonság típusát. |
| Chyba: Lekérdezésfordítási hiba. | 1. (user.department - eq "Értékesítés") (user.department - eq "Marketing")<br>2. (user.userPrincipalName-egyezik "*@domain.ext") | 1. Hiányzó operátor. Használja a - és vagy - vagy két predikátumok csatlakoztatása<br>(user.department - eq "Értékesítés") – vagy (user.department - eq "Marketing")<br>2. Hiba – a használt reguláris kifejezést az felel meg<br>(user.userPrincipalName-egyezik ". *@domain.ext")<br>vagy másik lehetőségként: (user.userPrincipalName-egyezik "@domain.ext$") |

## <a name="next-steps"></a>További lépések

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Alkalmazások kezelése az Azure Active Directoryban](../manage-apps/what-is-application-management.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../connect/active-directory-aadconnect.md)
