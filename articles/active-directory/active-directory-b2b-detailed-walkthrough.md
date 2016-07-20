<properties
   pageTitle="Az Azure Active Directory B2B együttműködés előzetes verziójának részletes bemutatója | Microsoft Azure"
   description="Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok támogatása érdekében lehetővé teszi, hogy az üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokat"
   services="active-directory"
   documentationCenter=""
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="identity"
   ms.date="05/09/2016"
   ms.author="viviali"/>

# Azure AD B2B együttműködés előzetes verziója: részletes bemutató

Ez a bemutató az Azure AD B2B együttműködés használatának módját ismerteti. A Contoso informatikai rendszergazdájaként három partnervállalat alkalmazottaival szeretnénk megosztani alkalmazásokat. Egyik partnervállalatnál sem feltétel az Azure AD megléte.

- Alice, a Simple Partner szervezet munkatársa
- Bob, a Medium Partner szervezet munkatársa, és bizonyos alkalmazásokhoz kell hozzáférést kapnia
- Carol, a Complex Partner szervezet munkatársa, és bizonyos alkalmazásokhoz kell hozzáférést kapnia, valamint tagsági jogviszonyra van szüksége a Contoso csoportjaiban

Miután meghívókat küldtünk a partnerfelhasználóknak, konfigurálhatjuk őket az Azure AD-ben, hogy hozzáférést nyújtsunk az alkalmazásokhoz és a csoporttagsághoz az Azure portálon keresztül. Először vegyük fel Alice felhasználóját.

## Alice hozzáadása a Contoso címtárához
1. Hozzon létre egy .csv fájlt az itt látható fejlécekkel, feltöltve Alice **Email**, **DisplayName** és **InviteContactUsUrl** mezőjét. A **DisplayName** a meghívón megjelenő név, valamint a Contoso Azure AD-címtárban megjelenő név. Alice az **InviteContactUsUrl** címen keresztül léphet kapcsolatba a Contoso vállalattal. A következő példában az InviteContactUsUrl a Contoso LinkedIn-profilját határozza meg. Fontos, hogy a .csv fájl első sorában lévő címkéket pontosan [A CSV fájlformátum referenciájában](active-directory-b2b-references-csv-file-format.md) látható módon írja be.  
![Alice CSV-példafájlja](./media/active-directory-b2b-detailed-walkthrough/AliceCSV.png)

2. Az Azure portálon adjon egy felhasználót a Contoso címtárához (Active Directory > Contoso > Felhasználók > Felhasználó hozzáadása). A „Felhasználó típusa” legördülő listában válassza a „Partnervállalatok felhasználói” elemet. Töltse fel a .csv fájlt. A feltöltés előtt győződjön meg róla, hogy a .csv fájl be van zárva.  
![CSV-fájl feltöltése Alice-hoz](./media/active-directory-b2b-detailed-walkthrough/AliceUpload.png)

3. Alice most Külső felhasználóként szerepel a Contoso Azure AD-címtárban.  
![Alice szerepel az Azure AD listájában](./media/active-directory-b2b-detailed-walkthrough/AliceInAD.png)

4. Alice a következő e-mailt kapja.  
![Alice meghívót tartalmazó e-mailje](./media/active-directory-b2b-detailed-walkthrough/AliceEmail.png)

5. Alice a hivatkozásra kattint, és a rendszer felkéri, hogy fogadja el a meghívást, és jelentkezzen be a munkahelyi hitelesítő adataival. Ha Alice nem szerepel az Azure AD-címtárban, a rendszer felkéri a regisztrálásra.  
![Regisztráció Alice meghívása után](./media/active-directory-b2b-detailed-walkthrough/AliceSignUp.png)

6. Alice az Alkalmazás-hozzáférési panelre lesz átirányítva, amely üres, amíg hozzáférést nem kap az alkalmazásokhoz.  
![Alice hozzáférési panelje](./media/active-directory-b2b-detailed-walkthrough/AliceAccessPanel.png)

Ez az eljárás a B2B együttműködés legegyszerűbb formáját teszi lehetővé. A Contoso Azure AD-címtárban lévő felhasználóként Alice-nak hozzáférés adható az alkalmazásokhoz és csoportokhoz az Azure portálon keresztül. Most adjuk hozzá Bobot, akinek a Moodle és a Salesforce alkalmazásokat kell elérnie.

## Bob hozzáadása a Contoso címtárához és hozzáférés biztosítása az alkalmazásokhoz
1. A Windows PowerShellt használja a telepített Azure AD-modullal a Moodle és a Salesforce alkalmazásazonosítóinak megkereséséhez. Az azonosítók a következő parancsmaggal olvashatók be: `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` Ez lekéri a Contoso címtárában elérhető összes alkalmazás és azok AppPrincialId azonosítójának listáját.  
![Bob azonosítóinak beolvasása](./media/active-directory-b2b-detailed-walkthrough/BobPowerShell.png)

2. Hozzon létre egy .csv fájlt Bob Email és DisplayName, **InviteAppID**, **InviteAppResources** és InviteContactUsUrl információival. Töltse fel az **InviteAppResources** mezőt a PowerShellben talált Moodle és Salesforce AppPrincipalId azonosítókkal, vesszővel elválasztva őket. Töltse fel az **InviteAppId** mezőt ugyanezen Moodle AppPrincipalId azonosítóval, hogy a Moodle emblémáját felhasználja az e-mailben és a bejelentkezési oldalakon.  
![Bob CSV-példafájlja](./media/active-directory-b2b-detailed-walkthrough/BobCSV.png)

3. Töltse fel a .csv fájlt az Azure portálon keresztül, ahogyan Alice esetén is tette. Bob most egy külső felhasználó a Contoso Azure AD-címtárban.

4. Bob a következő e-mailt kapja.  
![Bob meghívót tartalmazó e-mailje](./media/active-directory-b2b-detailed-walkthrough/BobEmail.png)

5. Bob a hivatkozásra kattint, és a rendszer felkéri, hogy fogadja el a meghívást. Miután bejelentkezett, a Hozzáférési panelre lesz átirányítva, és már használhatja is a Moodle és a Salesforce alkalmazást.  
![Bob hozzáférési panelje](./media/active-directory-b2b-detailed-walkthrough/BobAccessPanel.png)

Ezután Carolt vesszük fel, akinek elérést kell adni az alkalmazásokhoz és tagsági jogviszonyra van szüksége a Contoso címtár csoportjaiban.

## Carol hozzáadása a Contoso címtárához és hozzáférés biztosítása az alkalmazásokhoz, valamint a csoporttagság biztosítása

1. A Windows PowerShellt használja a telepített Azure AD-modullal a Contoso címtárán belüli alkalmazásazonosítók és csoportazonosítók megkereséséhez.
 - Az AppPrincipalId azonosítót a `Get-MsolServicePrincipal | fl DisplayName, AppPrincipalId` parancsmaggal olvassa be, ahogyan Bob esetében
 - A `Get-MsolGroup | fl DisplayName, ObjectId` parancsmaggal olvassa be a csoportok ObjectId azonosítóját. Ez lekéri a Contoso címtárában lévő összes csoport és azok ObjectId azonosítójának listáját. A csoportazonosítók objektumazonosítóként is beolvashatók az Azure portálon lévő csoport Tulajdonságok lapjáról.  
![Carol azonosítóinak és csoportjainak beolvasása](./media/active-directory-b2b-detailed-walkthrough/CarolPowerShell.png)

2. Hozzon létre egy .csv fájlt, és töltse fel Carol Email, DisplayName, InviteAppID, InviteAppResources, **InviteGroupResources** és InviteContactUsUrl információival. Az **InviteGroupResources** mezőt a MyGroup1 és az Externals csoportok ObjectId azonosítói töltik fel, vesszővel elválasztva.  
![Carol CSV-példafájlja](./media/active-directory-b2b-detailed-walkthrough/CarolCSV.png)

3. Töltse fel a .csv fájlt az Azure portálon keresztül.

4. Carol a Contoso címtárában szereplő felhasználó, és a MyGroup1 és az Externals csoport tagja is, ahogyan az az Azure portálon is látható.  
![Carol szerepel az Azure AD egy csoportjában](./media/active-directory-b2b-detailed-walkthrough/CarolGroup.png)

5. Carol kap egy e-mailt, amely tartalmazza a meghívó elfogadásának hivatkozását. Miután bejelentkezik, az Alkalmazás-hozzáférési panelre lesz átirányítva, hogy hozzáféréssel rendelkezzen a Moodle és a Salesforce alkalmazáshoz.  

Ennyit kell tudni a felhasználók partnervállalatokból történő hozzáadásáról az Azure AD B2B együttműködés használata során. Ez a bemutató ismertette, hogyan adhatja hozzá az Alice, Bob és Carol nevű felhasználókat a Contoso címtárához három különálló .csv fájl használatával. Ez a folyamat megkönnyíthető, ha a különálló .csv fájlokat egyetlen fájlban egyesíti.  
![Alice, Bob és Carol CSV-példafájlja](./media/active-directory-b2b-detailed-walkthrough/CombinedCSV.png)

## Kapcsolódó cikkek
Ismerje meg az Azure AD B2B együttműködés további cikkeit:

- [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Működés](active-directory-b2b-how-it-works.md)
- [A CSV fájlformátum referenciája](active-directory-b2b-references-csv-file-format.md)
- [Külső felhasználói token formátuma](active-directory-b2b-references-external-user-token-format.md)
- [Külső felhasználói objektum attribútumainak módosítása](active-directory-b2b-references-external-user-object-attribute-changes.md)
- [Az aktuális előzetes verzió korlátozásai](active-directory-b2b-current-preview-limitations.md)
- [Article Index for Application Management in Azure Active Directory (Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke)](active-directory-apps-index.md)



<!--HONumber=Jun16_HO2-->


