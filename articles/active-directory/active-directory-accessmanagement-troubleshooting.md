---
title: A csoportok dinamikus tagság hibaelhárítása |} Microsoft Docs
description: Dinamikus csoporttagság csoportok tippek az Azure ad-ben.
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
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 6d8d04273e9f29b2634c8b77b0268f3c7b77b1e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása
**Egy szabály beállítása a csoport, de nincs tagságát módosul, a csoport**<br/>Ellenőrizze a szabály a felhasználói attribútumok értékeit: vannak-e, amely megfelel a szabálynak felhasználók? Ha mindent megfelelőnek tűnik, várja meg a csoport feltöltése egy kis idő. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Egy szabály beállítása, de most a szabály meglévő tagjai törlődnek**<br/>Ez az elvárt működés. A csoport tagjai meglévő törlődnek, amikor a szabály engedélyezve van-e módosítani. A szabály értékelése által visszaadott felhasználók tagjai a csoportba kerülnek.     

**Nem szerepel az tagsága instantly hozzáadása vagy szabály, miért nem módosítása esetén?**<br/>Dedikált gyűjteménytagság értékelése egy aszinkron háttér folyamat rendszeres időközönként történik. Mennyi ideig tart határozza meg a könyvtárban lévő felhasználók számát és méretét a csoport, a szabály eredményeként jött létre. Felhasználók kis mennyiségű mappák általában, megjelenik a csoporttagsági változások legfeljebb néhány perc múlva. A felhasználók sok könyvtárak 30 percet is igénybe vehet, vagy hosszabb adatokkal való feltöltéséhez.

### <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
