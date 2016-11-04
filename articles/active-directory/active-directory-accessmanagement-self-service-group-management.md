---
title: Az Azure Active Directory beállítása önkiszolgáló alkalmazáshozzáférés-kezeléshez | Microsoft Docs
description: Az önkiszolgáló csoportkezelési szolgáltatással a felhasználók biztonsági vagy Office 365-csoportokat hozhatnak létre és kezelhetnek az Azure Active Directoryban, valamint biztonsági és Office 365-csoporttagságot igényelhetnek.
services: active-directory
documentationcenter: ''
author: curtand
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2016
ms.author: curtand

---
# Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre
Az önkiszolgáló csoportkezelési szolgáltatással a felhasználók biztonsági vagy Office 365-csoportokat hozhatnak létre és kezelhetnek az Azure Active Directoryban (Azure AD). A felhasználók ezenkívül biztonsági és Office 365-csoporttagságot igényelhetnek, amelyet ezután a csoport tulajdonosa elfogadhat vagy elutasíthat. Ezáltal a csoporttagság napi szintű felügyelete olyan személyeknek adható ki, akik tisztában vannak az adott tagság üzleti környezetével. Az önkiszolgáló csoportkezelési szolgáltatások kizárólag biztonsági és Office 365-csoportok esetében érhetőek el, levelezési címmel rendelkező biztonsági csoportok vagy terjesztési listák esetében nem.

Az önkiszolgáló csoportkezelési szolgáltatás jelenleg két alapvető alkalmazási helyzetet tartalmaz: a delegált és az önkiszolgáló csoportkezelést.

* **Delegált csoportkezelés** 
   Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférés-kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda hozzáférést ad az új csoportnak az alkalmazáshoz, és hozzáadja a csoporthoz az összes olyan személyt, aki már hozzáféréssel rendelkezik az alkalmazáshoz. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia a felhasználók hozzáférésének kezeléséhez. Ha a rendszergazda ugyanezt az engedélyt biztosítja egy vezetőnek egy eltérő üzleti csoportban, akkor a személy a saját felhasználóinak a hozzáférését is kezelheti. Sem a vállalat tulajdonosa, sem pedig a vezető nem tekintheti meg vagy kezelheti egymás felhasználóit. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.
* **Önkiszolgáló csoportkezelés**
   – Jellemző példa rá két olyan felhasználó, akik egyaránt rendelkeznek egymástól függetlenül üzembe helyezett SharePoint Online-webhelyekkel. Ezek a felhasználók szeretnének egymás csapatának hozzáférést adni a saját webhelyükhöz. Ennek megvalósítása érdekében létrehozhatnak egy csoportot az Azure AD-ben, majd a SharePoint Online felületén mindketten kiválaszthatják ezt a csoportot, így biztosítva hozzáférést a webhelyeikhez. Ha valaki hozzáférést igényel, azt a hozzáférési panelen igényelheti, és ha kérését jóváhagyják, automatikusan hozzáférést kap mindkét SharePoint Online-webhelyhez. Ezt követően egyikük dönthet úgy, hogy a webhelyhez hozzáféréssel rendelkező összes felhasználó számára egy adott SaaS-alkalmazáshoz is hozzáférést ad. A SaaS-alkalmazás rendszergazdája adhat hozzáférési jogot a SharePoint Online webhelyhez tartozó alkalmazáshoz. Ettől kezdve az elfogadott kérések mindkét SharePoint Online-webhelyhez és az SaaS-alkalmazáshoz is hozzáférést biztosítanak.

## Csoport elérhetővé tétele önkiszolgáló végfelhasználói tevékenységhez
1. A [klasszikus Azure-portálon](https://manage.windowsazure.com) nyissa meg az Azure AD-címtárat.
2. A **Configure** (Konfigurálás) lapon állítsa a **Delegated group management** (Delegált csoportkezelés) beállítást Enabled (Engedélyezve) állapotúra.
3. Állítsa a **Users can create security groups** (A felhasználók létrehozhatnak biztonsági csoportokat) vagy **Users can create Office groups** (A felhasználók létrehozhatnak Office-csoportokat) beállítást Enabled (Engedélyezve) állapotúra.

Ha a **Users can create security groups** (Felhasználók létrehozhatnak biztonsági csoportokat) beállítás engedélyezve van, a címtárban szereplő összes felhasználó létrehozhat új biztonsági csoportot, és tagokat vehet fel ezekbe a csoportokba. Ezek az új csoportok szintén valamennyi felhasználó számára láthatóvá válnak a hozzáférési panelen. Ha pedig a csoport házirend-beállítása ezt lehetővé teszi, más felhasználók is kérhetik a felvételüket a csoportokba. Ha a **Users can create security groups** (A felhasználók létrehozhatnak biztonsági csoportokat) beállítás le van tiltva, a felhasználók nem hozhatnak létre csoportokat, és nem módosíthatják azokat a meglévő csoportokat, amelyeknek a tulajdonosai. Ugyanakkor továbbra is kezelhetik az ilyen csoportok tagságát, és elfogadhatják más felhasználók csoportfelvételi kéréseit.

A **Users who can use self-service for security groups** (Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók) beállítás használatával még részletesebben vezérelheti a felhasználók önkiszolgáló csoportkezelési hozzáférését. Ha a **Users can create groups** (Felhasználók létrehozhatnak csoportokat) beállítás engedélyezve van, a címtárban szereplő összes felhasználó létrehozhat új csoportot, és tagokat vehet fel ezekbe a csoportokba. Ha a **Users who can use self-service for security group** (Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók) beállítás értékét bizonyos felhasználókra állítja be, a csoportkezelést bizonyos felhasználói csoportokra korlátozza. Ha ez a kapcsoló bizonyos felhasználókra van állítva, hozzá kell adnia a felhasználókat az SSGMSecurityGroupsUsers csoporthoz, mielőtt új csoportokat hozhatnának létre és felhasználókat adhatnának hozzájuk. Ha a **Users who can use self-service for security group** (Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók) minden felhasználóra van beállítva, a címtár összes felhasználója számára lehetővé válik az új csoportok létrehozása.

A **Group that can use self-service for security groups** (Önkiszolgáló biztonságicsoport-kezelésre jogosult csoport) mező használatával egyéni nevet rendelhet ahhoz a csoporthoz, amely tagjai önkiszolgálásra jogosultak.

## További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Managing access to resources with Azure Active Directory groups (Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal)](active-directory-manage-groups.md)
* [Azure Active Directory-parancsmagok csoportbeállítások konfigurálásához](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Article Index for Application Management in Azure Active Directory (Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke)](active-directory-apps-index.md)
* [Mi az az Azure Active Directory?](active-directory-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

<!--HONumber=Sep16_HO4-->


