---
title: "A csoportok dinamikus tagság hibaelhárítása |} Microsoft Docs"
description: "Dinamikus csoporttagság csoportok tippek az Azure ad-ben."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 89bb04b6-a379-49c2-8465-fe386641816a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: it-pro
ms.openlocfilehash: 0bb4c294cc6a4e1c9c2f1ad405c539854b6bcf5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="troubleshooting-dynamic-memberships-for-groups"></a>Dinamikus csoporttagságok hibaelhárítása
**Egy szabály beállítása a csoport, de nincs tagságát módosul, a csoport**<br/>Ellenőrizze a szabály a felhasználói attribútumok értékeit: vannak-e, amely megfelel a szabálynak felhasználók? Ha mindent megfelelőnek tűnik, várja meg a csoport feltöltése egy kis idő. Az első feltöltésnél és szabálymódosítás után a bérlő méretétől függően ez akár 24 órát is jelenthet.

**Egy szabály beállítása, de most a szabály meglévő tagjai törlődnek**<br/>Ez az elvárt működés. A csoport tagjai meglévő törlődnek, amikor a szabály engedélyezve van-e módosítani. A szabály értékelése által visszaadott felhasználók tagjai a csoportba kerülnek.     

**Nem szerepel az tagsága instantly hozzáadása vagy szabály, miért nem módosítása esetén?**<br/>Dedikált gyűjteménytagság értékelése egy aszinkron háttér folyamat rendszeres időközönként történik. Mennyi ideig tart határozza meg a könyvtárban lévő felhasználók számát és méretét a csoport, a szabály eredményeként jött létre. Felhasználók kis mennyiségű mappák általában, megjelenik a csoporttagsági változások legfeljebb néhány perc múlva. A felhasználók sok könyvtárak 30 percet is igénybe vehet, vagy hosszabb adatokkal való feltöltéséhez.

### <a name="next-steps"></a>Következő lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
