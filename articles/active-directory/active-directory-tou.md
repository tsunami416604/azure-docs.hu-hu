---
title: "Azure Active Directory – Használati feltételek| Microsoft Docs"
description: "Az Azure AD Használati feltételek funkción keresztül Ön és vállalata felhasználási feltételeket biztosíthat az Azure AD-szolgáltatások felhasználóinak."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: d55872ef-7e45-4de5-a9a0-3298e3de3565
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: a29f1e7b39b7f35073aa5aa6c6bd964ffaa6ffd0
ms.openlocfilehash: b6318b419a0ea87fd1fb56656b1161909876f338
ms.contentlocale: hu-hu
ms.lasthandoff: 09/21/2017

---

# <a name="azure-active-directory-terms-of-use-feature-preview"></a>Azure Active Directory – Használati feltételek funkció (Előzetes verzió)
Az Azure AD Használati feltételek funkcióján keresztül a vállalatok egyszerűen láthatják el a végfelhasználókat a szükséges információkkal.  Így gondoskodni lehet róla, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat.

Az Azure AD – Használati feltételek pdf formátumot használ a tartalmak megjelenítéséhez.   Ez bármilyen tartalmat jelenthet, akár az életben lévő szerződéseket is, így begyűjtheti a végfelhasználói megállapodásokat a felhasználók bejelentkezésekor.  A használati feltételeket alkalmazásokhoz vagy felhasználói csoportokhoz is használhatja, illetve akkor is, ha különböző célokra többféle használati feltételre van szüksége.

A jelen dokumentum többi része az Azure AD – Használati feltételekkel kapcsolatos kezdeti lépéseket ismerteti.  

## <a name="why-use-azure-ad-terms-of-use"></a>Miért érdemes használni az Azure AD – Használati feltételeket?
Nem mindig könnyű rávenni az alkalmazottakat vagy a vendégfelhasználókat arra, hogy a hozzáférés előtt elfogadják a használni feltételeket? Segítségre van szüksége annak nyomon követéséhez, hogy ki fogadta el a vállalat használati feltételeit és ki nem?  Az Azure AD Használati feltételek funkcióján keresztül a vállalatok egyszerűen láthatják el a végfelhasználókat a szükséges információkkal.  Így gondoskodni lehet róla, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat.

Az Azure AD – Használati feltételek az alábbiakhoz használhatók:
-   A szervezet minden tagjára vonatkozó általános használati feltételek.
-   Specifikus használati feltételek a felhasználó jellemzői alapján (például orvosok kontra nővérek, belföldi kontra nemzetközi alkalmazottak [dinamikus csoportjai](https://azure.microsoft.com/updates/azure-active-directory-dynamic-membership-for-groups)).
-   Specifikus használati feltételek a kulcsfontosságú üzleti alkalmazások (pl. Salesforce) hozzáférése alapján.


## <a name="prerequisites"></a>Előfeltételek
A következő lépéseket követve konfigurálja az Azure AD – Használati feltételeket:

1. Jelentkezzen be az Azure AD-be azon címtár globális, biztonsági vagy feltételes hozzáférési rendszergazdájaként, amelyhez be szeretné állítani az Azure AD – Használati feltételeket.
2. Győződjön meg arról, hogy a címtár prémium szintű Azure AD P1, P2, EMS E3 vagy EMS E5 előfizetéssel rendelkezik.  Ha nincs ilyen előfizetése, akkor [szerezze be a prémium szintű Azure AD-t](active-directory-get-started-premium.md) vagy [indítson egy próbaverziót](https://azure.microsoft.com/trial/get-started-active-directory/).
3. Az Azure AD – Használati feltételek irányítópultját a következő címen tekintheti meg: [https://aka.ms/catou](https://aka.ms/catou)



## <a name="add-company-terms-of-use"></a>Vállalati használati feltételek hozzáadása
A használati feltételek véglegesítése után a hozzáadáshoz kövesse a következő eljárást.

### <a name="to-add-terms-of-use"></a>Használati feltételek hozzáadása
1. Lépjen a [https://aka.ms/catou](https://aka.ms/catou) címen található irányítópultra
2. Kattintson az Add (Hozzáadás) parancsra.</br>
![Használati feltételek hozzáadása](media/active-directory-tou/tou2.png)
3. Adja meg a használati feltételek **nevét**.
4. Adja meg a **megjelenítendő nevet**.  A felhasználók ezt a fejlécet látják bejelentkezéskor.
5. A **Tallózás** lehetőséggel keresse meg a véglegesített használati feltételeket tartalmazó pdf fájlt, és válassza ki.  Ajánlott betűméret: 24.
6. A feltöltött használati feltételeket sablon vagy egyéni feltételes hozzáférési szabályzat segítségével **Kényszerítheti ki**.  Az egyéni feltételes hozzáférési szabályzat részletes használati feltételek alkalmazását teszi lehetővé, amelyeket akár egy adott felhőalkalmazásra vagy felhasználói csoportra is leszűkíthet.  További információkért lásd a [feltételes hozzáférés konfigurálását bemutató részt](active-directory-conditional-access-best-practices.md)
7. Kattintson a **Create** (Létrehozás) gombra.
8. Ha egyéni feltételes hozzáférési sablont választott, akkor egy új képernyő jelenik meg, amelyen testre szabható a feltételes hozzáférési szabályzat.
7. Ezután már az új használati feltételek jelennek meg.</br>

![Használati feltételek hozzáadása](media/active-directory-tou/tou3.png)

## <a name="delete-terms-of-use"></a>Használati feltételek törlése
A korábbi használati feltételeket az alábbi eljárással törölheti vagy távolíthatja el:

### <a name="to-delete-terms-of-use"></a>Használati feltételek törlése
1. Lépjen a [https://aka.ms/catou](https://aka.ms/catou) címen található irányítópultra
2. Válassza ki az eltávolítani kívánt használati feltételeket.
3. Kattintson a **Törlés** gombra.
4. Az új használati feltételek eltűnnek.


## <a name="audit-terms-of-use"></a>Használati feltételek naplózása
Az Azure AD – Használati feltételek egy egyszerűen használható naplózási funkcióval is rendelkezik, amellyel megtekintheti, hogy ki és mikor fogadta el a használati feltételeket.  A naplózás megkezdéséhez kövesse az alábbi eljárást:

### <a name="to-audit-terms-of-use"></a>Használati feltételek naplózása
1. Lépjen a [https://aka.ms/catou](https://aka.ms/catou) címen található irányítópultra
2. Kattintson az Audit Event (Esemény naplózása) lehetőségre.</br>
![Esemény naplózása](media/active-directory-tou/tou8.png)
3.  Az Azure AD naplókat megjelenítő képernyőjén a rendelkezésre álló legördülő menük segítségével szűrheti az információkat, amennyiben adott naplózási információkra kíváncsi.
![Esemény naplózása](media/active-directory-tou/tou9.png)
4.  Az információkat helyi felhasználás céljából egy .csv fájlba is mentheti.

## <a name="what-users-see"></a>Mit látnak a felhasználók?
Az érintett felhasználók a használati feltételek létrehozása és kényszerítése után a következő  képernyőket fogják látni, amikor bejelentkeznek.
-   Az ajánlott eljárás a 24-es betűméret alkalmazása a PDF-ekben.
![Esemény naplózása](media/active-directory-tou/tou10.png)
-   A képernyő mobileszközökön így jelenik meg</br></br>
![Esemény naplózása](media/active-directory-tou/tou11.png)

## <a name="additional-information"></a>További információ
Az alábbi információkkal érdemes tisztában lenni, mivel segítenek a használati feltételek alkalmazásában.


Az érintett felhasználóknak újra be kell jelentkezniük, hogy megfelelhessenek az új szabályzatnak, ha:
 - egy feltételes hozzáférési szabályzat van engedélyezve a használati feltételekhez
 - létrehoztak újabb használati feltételeket

Ennek az az oka, hogy a feltételes hozzáférési szabályzatok azonnal hatályba lépnek. Ebben az esetben a rendszergazdai felületen „szomorú felhők” vagy „Azure AD-tokenekkel kapcsolatos problémák” jelennek meg. A rendszergazdának újra be kell jelentkeznie, hogy megfelelhessen az új szabályzatnak.





## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Hogyan ellenőrizhetem, hogy egy felhasználó elfogadta-e a használati feltételeket (és ha igen, mikor)?**</br>
V: Ha a felhasználó elfogadja a használati feltételeket, az bekerül a naplóba. Az Azure AD-naplókban keresést is végezhet a kívánt találatok megjelenítéséhez.  

**K: Ha megváltoznak a használati feltételek, azokat a felhasználóknak újra el kell fogadniuk?**</br>
V: Igen, egy rendszergazda megváltoztathatja a használati feltételeket, amelyeket aztán mindenkinek újból el kell fogadnia.

**K: A használati feltételek egyszerre több nyelvet is támogathatnak?**</br>
V: Nem, jelenleg nem szerepelhet többféle nyelv ugyanazon használati feltételek között.  Azonban a hatókört csoportokra is korlátozhatja (például a Franciaországban érvényes használati feltételek különbözhetnek az Egyesült Királyságra vonatkozóktól). 

**K: Mikor lépnek életbe a használati feltételek?**</br>
V: A használati feltételek a bejelentkezéskor lépnek életbe.

**K: Mely alkalmazásokhoz használhatom a használati feltételeket?**</br>
V: Feltételes hozzáférési szabályzatot a modern hitelesítést használó vállalati alkalmazásokhoz hozhat létre.  Bővebb információ: [vállalati alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-view-azure-portal).

**K: Több használati feltételt is hozzáadhatok egy adott felhasználóhoz vagy alkalmazáshoz?**</br>
V: Igen, ha több feltételes hozzáférési szabályzatot is létrehoz az adott csoportokhoz vagy alkalmazásokhoz. Ha egy felhasználó több használati feltétel hatókörébe is beletartozik, külön-külön kell elfogadnia őket.
 
**K: Mi történik akkor, ha egy felhasználó nem fogadja el a használati feltételeket?**</br>
V: Ebben az esetben a felhasználón nem kaphat hozzáférést az alkalmazáshoz. A hozzáféréshez a felhasználónak újra be kell jelentkeznie, és el kell fogadnia a használati feltételeket.
