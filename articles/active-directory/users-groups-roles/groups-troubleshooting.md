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
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: aaded644635ab93cef9323ad38d1d150b15fe743
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449906"
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása
**Konfiguráltam egy szabály a csoport, de a csoport frissítésére nincs tagságok**<br/>Ellenőrizze a szabály a felhasználói attribútumok értékei: vannak-e felhasználók, amelyek megfelelnek a szabályt? Ha mindent rendben talál, engedélyezze a csoport feltöltése egy kis ideig. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Egy szabály konfiguráltam, de mostantól a meglévő csoporttagok, a szabály el lesznek távolítva**<br/>Ez az elvárt működés. A csoport tagjai meglévő el lesznek távolítva, amikor egy szabály engedélyezve van, vagy módosítani. A kiértékelés a szabály által visszaadott felhasználók tagjai a csoportba kerülnek.     

**Nem látom a tagsága instantly hozzáadása vagy egy szabály, miért nem módosítása esetén?**<br/>Dedikált gyűjteménytagság értékelése egy aszinkron háttérfolyamat rendszeres időközönként történik. Mennyi ideig tart határozza meg a címtárban található felhasználók száma és mérete miatt a szabály létrehozta a csoportot. A felhasználók kis számú könyvtárak általában megjelenik a csoporttagsági változások kisebb, mint néhány perc múlva. A felhasználók nagy számú könyvtárak 30 percet is igénybe vehet, vagy hosszabb adatokkal való feltöltéséhez.

### <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](../fundamentals/active-directory-manage-groups.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](../active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](../connect/active-directory-aadconnect.md)
