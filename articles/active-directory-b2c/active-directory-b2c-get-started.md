---
title: 'Azure Active Directory B2C előzetes verzió: Azure Active Directory B2C-bérlő létrehozása| Microsoft Docs'
description: 'Témakör: Azure Active Directory B2C-bérlő létrehozása'
services: active-directory-b2c
documentationcenter: ''
author: swkrish
manager: msmbaldwin
editor: bryanla

ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2016
ms.author: swkrish

---
# Azure Active Directory B2C előzetes verzió: Azure AD B2C bérlő létrehozása
A Microsoft Azure Active Directory (Azure AD) B2C használatának megkezdéséhez kövesse a cikkben ismertetett három lépést.

[!INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1. lépés: Regisztráljon az Azure-előfizetésre
Ha már rendelkezik Azure-előfizetéssel, ugorja át ezt a lépést. Ha nem rendelkezik Azure-előfizetéssel, regisztráljon [Azure-előfizetésre](../active-directory/sign-up-organization.md) az Azure AD B2C eléréséhez.

> [!NOTE]
> Jelenleg ingyenesen használhatja az Azure AD B2C előzetes verzióját, de a használat bérlőnként 50 000 felhasználóra van korlátozva. A [klasszikus Azure portálhoz](http://manage.windowsazure.com/) való hozzáféréshez Azure-előfizetés szükséges.
> 
> 

## 2. lépés: Azure AD B2C bérlő létrehozása
A következő lépések alapján hozzon létre egy új Azure AD B2C bérlőt. Jelenleg a B2C funkciók nem kapcsolhatók be a meglévő címtárakban, ha vannak ilyenek.

1. Jelentkezzen be a [klasszikus Azure-portálra](https://manage.windowsazure.com/) előfizetés-rendszergazdaként. Ez ugyanaz a munkahelyi, iskolai vagy Microsoft-fiók, amelyet az Azure portálra való regisztráláshoz használt.
2. Kattintson a **New** (Új) > **App Services** (Alkalmazásszolgáltatások) > **Active Directory** > **Directory** (Címtár) > **Custom Create** (Egyéni létrehozás) lehetőségre.
   
    ![Képernyőkép bérlő létrehozásának elkezdéséről](./media/active-directory-b2c-get-started/new-directory.png)
3. Válassza ki a **Nevet**, **Tartománynevet** és **Országot vagy Régiót** a bérlőhöz.
4. Jelölje be a **This is a B2C directory** (Ez egy B2C-címtár) lehetőséget.
5. Pipálja ki a jelölőnégyzetet a művelet végrehajtásához.
   
    ![Képernyőkép a B2C-címtár létrehozásához szükséges jelölőnégyzetről](./media/active-directory-b2c-get-started/create-b2c-directory.png)
6. A bérlő létrehozása megtörtént, és a bérlő meg fog jelenni az Active Directory bővítményben. A bérlőhöz globális rendszergazda is tartozik. Szükség szerint más globális rendszergazdákat is hozzáadhat.

## 3. lépés: Lépjen az Azure portál B2C funkciók panelére
1. Lépjen a bal oldalon található navigációs sávban az Active Directory bővítményre.
2. A létrehozott bérlő a **Directory** (Címtár) lapon található: kattintson rá.
3. Kattintson a **Konfigurálása** fülre.
4. Kattintson a **B2C beállítások kezelése** hivatkozásra a **B2C felügyelet** szakaszban.
   
    ![Képernyőkép címtár konfigurálásáról B2C számára](./media/active-directory-b2c-get-started/b2c-directory-configure-tab.png)
5. Az Azure-portálon a B2C funkciók panelje új böngészőlapon vagy ablakban nyílik meg.
   
   > [!IMPORTANT]
   > Akár 2-3 percet is igénybe vehet, míg az újonnan létrehozott bérlő az Azure portálon elérhető lesz. Elháríthatja ezt a hibát, ha egy kis idő elteltével újra végrehajtja ezeket a lépéseket. Ha a hiba elhárítása nem sikerült, forduljon a támogatási szolgálathoz.
   > 
   > 
6. A panelt a könnyű elérhetőség érdekében rögzítse a kezdőpulton. (A rögzítéshez szükséges eszköz pirossal ki van emelve a funkciók panel jobb felső sarkában.)
   
    ![Képernyőkép a B2C funkciók panelről](./media/active-directory-b2c-get-started/b2c-features-blade.png)
   
   > [!NOTE]
   > A [klasszikus Azure portálon](https://manage.windowsazure.com/) kezelheti a felhasználókat és csoportokat, az önkiszolgáló jelszó-visszaállítás konfigurálását és a bérlői vállalati védjegyadataira vonatkozó funkciókat.
   > 
   > 

## Következő lépések
Az [Azure Active Directory B2C előzetes verzió: az alkalmazás regisztrálása](active-directory-b2c-app-registration.md) részből megtudhatja, hogyan lehet egy alkalmazást regisztrálni Azure AD B2C esetén, és hogyan lehet Első lépések alkalmazást készíteni.

<!--HONumber=Jun16_HO2-->


