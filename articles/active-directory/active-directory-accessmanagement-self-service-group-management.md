---
title: "Alkalmazások önkiszolgáló hozzáférés-kezelésének beállítása az Azure Active Directoryban | Microsoft Docs"
description: "Biztonsági vagy Office 365-csoportok létrehozása és kezelése az Azure Active Directoryban, valamint biztonsági és Office 365-csoporttagság igénylése"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 904d5c70-c34a-46c4-a9a7-d1efecf4821c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 7a7370eb076ba8602a58a260a14bb863c55bc803
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-azure-active-directory-for-self-service-group-management"></a>Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre
A felhasználók saját biztonsági vagy Office 365-csoportokat hozhatnak létre és kezelhetnek az Azure Active Directoryban (Azure AD-ben). A felhasználók ezenkívül biztonsági és Office 365-csoporttagságot igényelhetnek, amelyet ezután a csoport tulajdonosa elfogadhat vagy elutasíthat. A csoporttagság napi szintű felügyelete olyan személyeknek adható ki, akik tisztában vannak az adott tagság üzleti környezetével. Az önkiszolgáló csoportkezelési szolgáltatások kizárólag biztonsági és Office 365-csoportok esetében érhetőek el, levelezési címmel rendelkező biztonsági csoportok vagy terjesztési listák esetében nem.

Az önkiszolgáló csoportkezelési szolgáltatás jelenleg két alapvető alkalmazási helyzetet szolgál ki: a delegált és az önkiszolgáló csoportkezelést.

* **Delegált csoportkezelés** – Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférésének kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda hozzáférést ad az új csoportnak az alkalmazáshoz, és hozzáadja a csoporthoz az összes olyan személyt, aki már hozzáféréssel rendelkezik az alkalmazáshoz. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia a felhasználók hozzáférésének kezeléséhez. Ha a rendszergazda ugyanezt az engedélyt biztosítja egy vezetőnek egy eltérő üzleti csoportban, akkor a személy a saját felhasználóinak a hozzáférését is kezelheti. Sem a vállalat tulajdonosa, sem pedig a vezető nem tekintheti meg vagy kezelheti egymás felhasználóit. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.
* **Önkiszolgáló csoportkezelés** – Jellemző példa rá két olyan felhasználó, akik egyaránt rendelkeznek egymástól függetlenül üzembe helyezett SharePoint Online-webhelyekkel. Ezek a felhasználók szeretnének egymás csapatának hozzáférést adni a saját webhelyükhöz. Ennek megvalósítása érdekében létrehozhatnak egy csoportot az Azure AD-ben, majd a SharePoint Online felületén mindketten kiválaszthatják ezt a csoportot, így biztosítva hozzáférést a webhelyeikhez. Ha valaki hozzáférést igényel, azt a hozzáférési panelen igényelheti, és ha kérését jóváhagyják, automatikusan hozzáférést kap mindkét SharePoint Online-webhelyhez. Ezt követően egyikük dönthet úgy, hogy a webhelyhez hozzáféréssel rendelkező összes felhasználó számára egy adott SaaS-alkalmazáshoz is hozzáférést ad. A SaaS-alkalmazás rendszergazdája adhat hozzáférési jogot a SharePoint Online webhelyhez tartozó alkalmazáshoz. Ettől kezdve az elfogadott kérések mindkét SharePoint Online-webhelyhez és az SaaS-alkalmazáshoz is hozzáférést biztosítanak.

## <a name="make-a-group-available-for-user-self-service"></a>Csoport elérhetővé tétele önkiszolgáló felhasználói tevékenységhez
1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Felhasználók és csoportok** lehetőséget, majd válassza a **Csoportbeállítások** elemet.
3. Állítsa az **Önkiszolgáló csoportkezelés engedélyezve** elemet **Igen** értékűre.
4. Állítsa **A felhasználók létrehozhatnak biztonsági csoportokat** vagy **A felhasználók létrehozhatnak Office 365-csoportokat** elemet **Igen** értékűre.
  * Ha ezek a beállítások engedélyezve vannak, a címtárban lévő összes felhasználó létrehozhat új biztonsági csoportokat, és tagokat vehet fel ezekbe a csoportokba. Ezek az új csoportok szintén valamennyi felhasználó számára láthatóvá válnak a hozzáférési panelen. Ha pedig a csoport házirend-beállítása ezt lehetővé teszi, más felhasználók is kérhetik a felvételüket a csoportokba. 
  * Ha ezek a beállítások le vannak tiltva, a felhasználók nem hozhatnak létre csoportokat, és nem módosíthatják azokat a meglévő csoportokat, amelyeknek a tulajdonosai. Ugyanakkor továbbra is kezelhetik az ilyen csoportok tagságát, és elfogadhatják más felhasználók csoportfelvételi kéréseit.

A **Biztonsági csoportok kezelésére jogosult felhasználók** és az **Office 365-csoportok kezelésére jogosult felhasználók** beállítás használatával még részletesebben vezérelheti a felhasználók önkiszolgáló csoportkezelési hozzáférését. Ha **A felhasználók létrehozhatnak csoportokat** beállítás engedélyezve van, a bérlőben lévő összes felhasználó létrehozhat új csoportokat, és tagokat vehet fel ezekbe a csoportokba. Ha ezeket **Néhány** értékre állítja be, a csoportkezelést egyes felhasználói csoportokra korlátozza. Ha ez a kapcsoló **Some** értékre (bizonyos felhasználókra) van állítva, hozzá kell adnia a felhasználókat az SSGMSecurityGroupsUsers csoporthoz, mielőtt új csoportokat hozhatnának létre és felhasználókat adhatnának hozzájuk. Ha az **Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók** és az **Office 365 csoportok kezelésére jogosult felhasználók** **Minden felhasználó** értékre van beállítva, a bérlő összes felhasználója számára lehetővé válik új csoportok létrehozása.

A **Biztonsági csoportok kezelésére jogosult csoport** és az **Office 365 csoportok kezelésére jogosult csoport** használatával megadhat egyetlen csoportot, amelynek tagjai használhatják az önkiszolgáló funkciót.

## <a name="next-steps"></a>Következő lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
