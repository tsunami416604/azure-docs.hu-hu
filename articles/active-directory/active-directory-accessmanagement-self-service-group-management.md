<properties
    pageTitle="Az Azure Active Directory beállítása önkiszolgáló alkalmazáshozzáférés-kezeléshez | Microsoft Azure"
    description="Az önkiszolgáló csoportkezelési szolgáltatással a felhasználók biztonsági vagy Office 365-csoportokat hozhatnak létre és kezelhetnek az Azure Active Directoryban, valamint biztonsági és Office 365-csoporttagságot igényelhetnek."
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="stevenpo"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/26/2016"
    ms.author="curtand"/>

# Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre

Az önkiszolgáló csoportkezelési szolgáltatással a felhasználók biztonsági vagy Office 365-csoportokat hozhatnak létre és kezelhetnek az Azure Active Directoryban (Azure AD), valamint biztonsági és Office 365-csoporttagságot igényelhetnek, amelyet a csoport tulajdonosa elfogadhat vagy elutasíthat. Az önkiszolgáló csoportkezelési szolgáltatások használatával a csoporttagság napi szintű felügyelete olyan személyeknek adható ki, akik tisztában vannak az adott tagság üzleti környezetével. Az önkiszolgáló csoportkezelési szolgáltatások kizárólag biztonsági és Office 365-csoportok esetében érhetőek el, levelezési címmel rendelkező biztonsági csoportok vagy terjesztési listák esetében nem.

Az önkiszolgáló csoportkezelési szolgáltatás jelenleg két alapvető alkalmazási helyzetet tartalmaz: a delegált és az önkiszolgáló csoportkezelést.

- **Delegált csoportkezelés** – Jellemző példa rá egy olyan rendszergazda, aki a vállalata által használt SaaS-alkalmazás hozzáférés-kezelését végzi. E hozzáférési jogosultságok kezelése nehézkessé válik, így a rendszergazda új csoport létrehozására kéri fel a vállalat tulajdonosát. A rendszergazda ezután alkalmazás-hozzáférési jogosultságot rendel a vállalat tulajdonosa által az imént létrehozott új csoporthoz, és az alkalmazáshoz hozzáféréssel rendelkező összes felhasználót ebbe a csoportba helyezi át. A vállalat tulajdonosa ezt követően további felhasználókat vehet fel a csoportba, akik néhány pillanat múlva automatikusan hozzáférést kapnak az alkalmazáshoz. A vállalat tulajdonosának nem kell a rendszergazdára várnia, maga intézheti a felhasználók hozzáférés-kezelését. A rendszergazda megteheti ugyanezt egy másik üzleti csoporthoz tartozó ügyintézővel is, így mind a vállalat tulajdonosa, mind az ügyintéző képes lesz a felhasználói hozzáférésének kezelésére anélkül, hogy a másik felhasználóival kapcsolatba kerülne. A rendszergazda továbbra is megtekintheti az alkalmazáshoz hozzáféréssel rendelkező összes felhasználó listáját, és blokkolhatja is a hozzáférést, ha szükséges.

- **Önkiszolgáló csoportkezelés** – Jellemző példa rá két olyan felhasználó, akik egyaránt rendelkeznek egymástól függetlenül üzembe helyezett SharePoint Online-webhelyekkel, és szeretnének egymás csapatának hozzáférést adni a saját webhelyükhöz. Ennek megvalósítása érdekében létrehozhatnak egy csoportot az Azure AD-ben, majd a SharePoint Online felületén mindketten kiválaszthatják ezt a csoportot, így biztosítva hozzáférést a webhelyeikhez. Ha valaki hozzáférést igényel, azt a hozzáférési panelen igényelheti, és ha kérését jóváhagyják, automatikusan hozzáférést kap mindkét SharePoint Online-webhelyhez. Ezt követően egyikük dönthet úgy, hogy a webhelyéhez hozzáféréssel rendelkező összes felhasználó számára egy adott SaaS-alkalmazáshoz is hozzáférést ad. Felkéri SaaS-alkalmazásának rendszergazdáját, hogy adjon alkalmazás-hozzáférési jogosultságot a webhelyhez. Ettől kezdve az általa elfogadott kérések mindkét SharePoint Online-webhelyhez és az SaaS-alkalmazáshoz is hozzáférést biztosítanak.

## Csoport elérhetővé tétele önkiszolgáló végfelhasználói tevékenységhez

A [klasszikus Azure portál](https://manage.windowsazure.com) **Konfigurálás** lapján a **Delegated group management** (Delegált csoportkezelés) beállítása legyen Enabled (Engedélyezve), majd a **Users can create security groups** (Felhasználók létrehozhatnak biztonsági csoportokat) vagy a **Users can create Office groups** (Felhasználók létrehozhatnak Office-csoportokat) beállítást is engedélyezze.

Ha a **Users can create security groups** (Felhasználók létrehozhatnak biztonsági csoportokat) beállítás engedélyezve van, a címtárban szereplő összes felhasználó létrehozhat új biztonsági csoportot, és tagokat vehet fel ezekbe a csoportokba. Ezek az új csoportok szintén valamennyi felhasználó számára láthatóvá válnak a hozzáférési panelen, ha pedig az adott csoport házirend-beállítása ezt lehetővé teszi, más felhasználók is kérhetik felvételüket e csoportokba. Ha a **Users can create security groups** (Felhasználók létrehozhatnak biztonsági csoportokat) beállítás le van tiltva, a felhasználók nem hozhatnak létre csoportokat, és nem módosíthatják azokat a meglévő csoportokat, amelyeknek a tulajdonosaként szerepelnek, ugyanakkor továbbra is kezelhetik az ilyen csoportok tagságát, és elfogadhatják más felhasználók csoportfelvételi kéréseit.

A **Users who can use self-service for security groups** (Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók) beállítás használatával még részletesebben vezérelheti a felhasználók önkiszolgáló csoportkezelési képességeinek hozzáférését. Ha a **Users can create groups** (Felhasználók létrehozhatnak csoportokat) beállítás engedélyezve van, a címtárban szereplő összes felhasználó létrehozhat új csoportot, és tagokat vehet fel ezekbe a csoportokba. Ha a **Users who can use self-service for security group** (Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók) beállítás értékét bizonyos felhasználókra állítja be, a csoportkezelést bizonyos felhasználói csoportokra korlátozza. Ha ez a kapcsoló bizonyos felhasználók értékre van állítva, egy SSGMSecurityGroupsUsers nevű csoport jön létre a címtárban, és ezt követően kizárólag az Ön által e csoportba felvett felhasználók hozhatnak létre új biztonsági csoportokat, illetve vehetnek fel csoporttagokat a címtáron belül. Ha a **Users who can use self-service for security group** (Önkiszolgáló biztonságicsoport-kezelésre jogosult felhasználók) minden felhasználóra van beállítva, a címtár összes felhasználója számára lehetővé válik az új csoportok létrehozása.

Az (alapértelmezés szerint „SSGMSecurityGroupsUsers” értékre állított) **Group that can use self-service for security groups** (Önkiszolgáló biztonságicsoport-kezelésre jogosult csoport) mező használatával egyéni nevet rendelhet ahhoz a csoporthoz, amely az önkiszolgálásra és új címtárcsoportok létrehozására jogosult felhasználók mindegyikét tartalmazni fogja.

## További információ

E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Managing access to resources with Azure Active Directory groups (Erőforráshozzáférés-kezelés Azure Active Directory-csoportokkal)](active-directory-manage-groups.md)

* [Article Index for Application Management in Azure Active Directory (Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke)](active-directory-apps-index.md)

* [Mi az az Azure Active Directory?](active-directory-whatis.md)

* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)



<!--HONumber=Jun16_HO2-->


