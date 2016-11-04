---
title: Új felhasználók hozzáadása az Azure Active Directoryhoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan vehet fel új felhasználókat, vagy hogyan módosíthatja a felhasználói adatokat az Azure Active Directoryban.
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
ms.date: 09/22/2016
ms.author: curtand

---
# Új felhasználók vagy Microsoft-fiókkal rendelkező felhasználók hozzáadása az Azure Active Directoryhoz
Felhasználók hozzáadásával feltöltheti a címtárat adatokkal. Ez a cikk azt ismerteti, hogyan vehet fel új felhasználókat a szervezetben, és hogyan vehet fel Microsoft-fiókkal rendelkező felhasználókat. Az Azure Active Directoryban a más címtárakban lévő felhasználók felvételéről vagy a partnervállalatokban lévő felhasználók felvételéről további információért lásd: [Más címtárakban vagy partnervállalatokban lévő felhasználók felvétele az Azure Active Directoryba](active-directory-create-users-external.md). A hozzáadott felhasználók alapértelmezés szerint nem rendelkeznek rendszergazdai engedélyekkel, de bármikor hozzájuk rendelhet szerepköröket.

## Felhasználó hozzáadása
1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza az **Active Directory** lehetőséget, majd válassza ki a szervezete címtárának nevét.
3. Válassza a **Felhasználók** lapot, majd a parancssávon válassza a **Felhasználó hozzáadása** parancsot.
4. A **Felhasználó bemutatása** lap **Felhasználó típusa** területén válassza a következők egyikét:
   
   * **Új felhasználó a szervezetben** – egy új felhasználói fiókot ad a címtárhoz.
   * **Felhasználó meglévő Microsoft-fiókkal** – egy meglévő végfelhasználói Microsoft-fiókot ad a címtárhoz (például egy Outlook-fiókot)
5. A **Felhasználó típusától** függően írjon be egy felhasználónevet (új felhasználóhoz) vagy e-mail-címet (Microsoft-fiókkal rendelkező felhasználóhoz).
6. A felhasználói **Profil** oldalon adjon meg egy kereszt- és egy vezetéknevet, egy rövid nevet és egy felhasználói szerepkört a **Szerepkörök** listából. A felhasználói és rendszergazdai szerepkörökről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure AD-ben](active-directory-assign-admin-roles.md). Adja meg, hogy **engedélyezi-e a többtényezős hitelesítést** a felhasználó számára.
7. Az **Ideiglenes jelszó beszerzése** oldalon válassza a **Létrehozás** lehetőséget.

> [!IMPORTANT]
> Ha a szervezete több tartományt használ, a következő problémákról kell tudnia a felhasználói fiók hozzáadásakor:
> 
> * Ha azonos egyszerű felhasználónévvel (UPN) szeretne felhasználói fiókokat felvenni több tartományba, **először** adja például a geoffgrisso@contoso.onmicrosoft.com címet vegye fel, **majd ezt követően a** geoffgrisso@contoso.com címet.
> * **Ne** adja hozzá a geoffgrisso@contoso.com címet a geoffgrisso@contoso.onmicrosoft.com hozzáadása előtt. A sorrend betartása fontos, és nehézkes lehet visszavonni a változásokat.
> 
> 

## Felhasználói adatok módosítása
Bármely felhasználói attribútumot módosíthat, kivéve az objektumazonosítót.

1. Nyissa meg a címtárat.
2. Válassza a **Felhasználók** lapot, majd válassza ki a módosítani kívánt felhasználó megjelenítendő nevét.
3. Végezze el a módosításokat, majd kattintson a **Mentés** parancsra.

Ha az épp módosított felhasználó szinkronizálva van a helyszíni Active Directory szolgáltatással, nem módosíthatja a felhasználó adatait ezzel az eljárással. A felhasználó módosításához használja a helyszíni Active Directory-felügyeleti eszközöket.

## Vendégfelhasználók kezelése és korlátozásai
A vendégfiókok másik címtárakban lévő olyan felhasználók, akik meg lettek hívva a címtárába a SharePoint-dokumentumok, alkalmazások vagy más Azure-erőforrások elérése céljából. A címtárban lévő vendégfiókok mögöttes UserType attribútuma „Vendég” beállítású. A normál felhasználók (különösen a címtár tagjai) UserType attribútuma „Tag”.

A vendégek korlátozott jogosultságokkal rendelkeznek a címtárban. Ezek a jogosultságok korlátozzák a vendégek azon képességét, hogy a címtár más felhasználóinak adatait felderítsék. De a vendégfelhasználók továbbra is kommunikálhatnak azon erőforrásokkal társított felhasználókkal és csoportokkal, amelyeken dolgoznak. A vendégfelhasználók a következőkre képesek:

* Azon Azure-előfizetéssel társított felhasználók és csoportok megtekintése, amelyhez hozzá vannak rendelve.
* Azon csoportok tagjainak megtekintése, amelyekhez tartoznak.
* A címtárban lévő többi felhasználó megkeresése, ha ismerik a felhasználó teljes e-mail-címét.
* A megkeresett felhasználók attribútumainak korlátozott megtekintése – ez a megjelenítendő névre, az e-mail-címre, az egyszerű felhasználónévre (UPN-re) és a miniatűr fényképre van korlátozva.
* A címtárban lévő ellenőrzött tartományok listájának lekérése.
* Hozzájárulás megadása az alkalmazásoknak, ugyanazon hozzáférést adva nekik, mint a címtár tagjainak.

## A vendégfelhasználók hozzáférési házirendjeinek beállítása
A címtár **Konfigurálás** lapja tartalmazza a vendégfelhasználók hozzáférés-vezérlésének lehetőségeit. Ezek a lehetőségek csak a klasszikus Azure portálon módosíthatók a címtár globális rendszergazdája által. Jelenleg erre a célra nem létezik PowerShell- vagy API-módszer.

A klasszikus Azure portálon a **Konfigurálás** lap megnyitásához válassza az **Active Directory** lehetőséget, majd jelölje ki a címtár nevét.

![Konfigurálás lap az Azure Active Directoryban][1]

Ezután szerkesztheti a vendégfelhasználók hozzáférés-vezérlésének lehetőségeit.

![hozzáférés-vezérlési lehetőségek a vendégfelhasználók számára][2]

## A következő lépések
* [Más címtárakban vagy partnervállalatokban lévő felhasználók felvétele az Azure Active Directoryba](active-directory-create-users-external.md)
* [Az Azure AD felügyelete](active-directory-administer.md)
* [Jelszavak kezelése az Azure AD-ben](active-directory-manage-passwords.md)
* [Csoportok kezelése az Azure AD-ben](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png



<!--HONumber=Sep16_HO4-->


