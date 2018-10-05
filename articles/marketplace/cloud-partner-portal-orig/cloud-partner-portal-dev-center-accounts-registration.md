---
title: A Microsoft Developer-fiók létrehozása |} A Microsoft Docs
description: Követelmények és lépések a Microsoft Developer-fiók létrehozása.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: bdcd1d0cc9f9979d898410180d364716b344d641
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809921"
---
<a name="create-a-microsoft-developer-account"></a>A Microsoft Developer-fiók létrehozása
====================================

Ez a cikk azt ismerteti, hogyan válhat Azure piactér közzétételre egy engedélyezett Microsoft Developer.

## <a name="create-a-microsoft-account"></a>Microsoft-fiók regisztrálása

A közzétételi folyamat indításához kell végezze el a **Microsoft Developer Center** regisztráció. Használja ugyanazt a regisztrált fiókot lévő a **[Cloud Partner Portalon](https://cloudpartner.azure.com/)** a közzétételi folyamat elindításához.

### <a name="general-account-guidelines"></a>A fiók általános irányelvek

Azt javasoljuk, hogy Ön egy Microsoft-fiókkal rendelkeznie az Azure piactér-i ajánlatainak közzétételéhez. Ez a fiók nem lehet adott szolgáltatások vagy ajánlatokat.

A felhasználó nevét a cím a tartományban kell lennie, és az informatikai csapat által vezérelt. Ezzel a fiókkal minden közzétételi kapcsolódó tevékenységeket kell elvégezni.

>[!WARNING]
>Például az "Azure" és "Microsoft" szót a Microsoft-fiók regisztrálása nem támogatottak. Kerülje azokat a szavakat a fiók létrehozása és a regisztrációs folyamat végrehajtásához.

### <a name="company-account-guidelines"></a>Céges fiók irányelvek

Kövesse ezeket az irányelveket, ha egynél több személynek kell a Microsoft-fiókjával, amely a fiók megnyitva a fiókja eléréséhez.

>[!IMPORTANT]
>Ahhoz, hogy több felhasználó hozzáférjen a fejlesztői központ-fiókjához, javasoljuk, egyéni szerepkörök hozzárendelése az Azure Active Directory használatával. Bejelentkezés után az egyes eléréséhez a fióknak az Azure AD hitelesítő adatait. További információkért lásd: [fiókfelhasználók kezelése](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Tartozik, a vállalati e-mail-címmel a Microsoft-fiók létrehozása\'s tartományhoz, de nem egy egyetlen személy. Ha például windowsapps\@fabrikam.com.
-   A Microsoft-fiókot a lehető legkevesebb fejlesztők való hozzáférés korlátozásához.
-   Állítsa be a vállalati e-mailek terjesztési lista, amely tartalmazza az mindenki, aki hozzáféréssel kell rendelkeznie a fejlesztői fiókhoz, és adja hozzá ezt az e-mail címet biztonsági adatait. Ez lehetővé teszi az összes alkalmazott számára a listán megkapni a biztonsági kódokat, ha szükséges, és kezelheti a Microsoft-fiók biztonsági adatait. Egy terjesztési lista beállítása nem megvalósítható, ha az egyéni e-mail fiók tulajdonosának kell lennie a elérhető hozzáférni és megosztani azokat a biztonsági kódot, amikor a rendszer kéri, (például ha az új biztonsági adatok hozzáadni a fiókhoz, vagy ha azt kell érhetők el az új eszköz.)
-   Nincs szükség a bővítmény és key csapat tagjai számára elérhető vállalati telefonszám megadása.
-   Jelentkezzen be a vállalati fejlesztői fiók a megbízható eszközök segítségével a fejlesztők általában rendelkeznek. Az összes fő csapat tagjai a megbízható eszközökhöz való hozzáféréssel kell rendelkeznie. Ez csökkenti a biztonsági kódokat a fiók elérésekor küldendő szükségességét.
-   Ha engedélyezi a hozzáférést a fiók nem megbízható számítógépről kell, a férjenek hozzá az legfeljebb öt fejlesztők számára. Ideális esetben fejlesztőket hozzá kell férnie a fiók, amely azonos földrajzi és a hálózati hely gépekről.
-   Gyakran tekintse át a [vállalati biztonsági adatok](https://account.live.com/proofs/Manage) , hogy az naprakész legyen.

>[!IMPORTANT]
>Fejlesztői fiók nyitása elsősorban megbízható számítógépeket kell elérhető. Ez azért fontos, mert létrehozott hetente fiókonkénti kódok száma korlátozva van. Azt is lehetővé teszi, hogy a legtöbb zökkenőmentes bejelentkezési élmény.
>
>További információkért lásd: [további fejlesztői fiók irányelvek és biztonsági](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>A Microsoft-fiók létrehozása

1.  Nyisson meg egy új Inkognitó Chrome vagy Internet Explorer InPrivate-böngészési munkamenet, győződjön meg arról, hogy Ön nincs bejelentkezve a meglévő fiók.
2.  Regisztrálja az e-mailt (az előző irányelvek segítségével), amely a Microsoft-fiók segítségével [hivatkozás](https://signup.live.com/signup.aspx). Hajtsa végre a következő regisztrációs utasításokat:

    - A fiók, amely a Microsoft-fiók regisztrálásakor meg kell adnia egy érvényes telefonszámot adjon a rendszer arra, hogy egy fiók ellenőrző kódot szöveges üzenetben vagy egy automata fel fogja hívni.
    - A fiók, amely a Microsoft-fiók regisztrálásakor meg kell adnia egy érvényes e-mail-azonosítót a fiók ellenőrzése az automatikus e-mail fogadása.
    - Ellenőrizze az e-mail címet a terjesztési listához küldeni.

    Most már készen áll az új Microsoft-fiók használata a Microsoft Developer Centerben.

## <a name="register-your-account-in-microsoft-developer-center"></a>Regisztrálja a fiókot a Microsoft Developer Centerben

A Microsoft Developer Center egyszer a vállalati adatok regisztrálásához használatos. A regisztráló kell lennie a vállalat érvényes képviselője, és saját identitás ellenőrzése, hogy személyes adatokat kell megadnia. A személy regisztrálása kell használnia a Microsoft-fiók, amelyet a vállalat **és ugyanazt a fiókot kell használni a Cloud Partner portálra.** Ellenőrizze, hogy a vállalat még nem rendelkezik a Microsoft Developer Center fiók előtt hozzon létre egyet. A folyamat során a céges címadatok, banki információt, beszedjük és adózási adatait. Ezek az adatok általában megtalálhatók a pénzügyi vagy üzleti kapcsolattartói adatok között.

>[!IMPORTANT]
>Annak érdekében, hogy az ajánlat létrehozásának és üzembe helyezésének különböző fázisait halad, el kell végeznie az alábbi fejlesztői profil összetevőket.

| Fejlesztői profil     | Vázlat indítása    | Fájlok másolása folyamatban       | Ingyenes közzététele és megoldássablon   | Kereskedelmi közzététele   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Céges regisztráció  | Rendelkeznie kell         | Rendelkeznie kell     | Rendelkeznie kell                             | Rendelkeznie kell             |
| Adózási profil azonosítója        | Optional          | Optional      | Optional                              | Rendelkeznie kell             |
| Fiók létrehozása          | Optional          | Optional      | Optional                              | Rendelkeznie kell             |

>[!NOTE]
>Használata a saját licences (BYOL) csak a támogatott virtuális gépek és a egy ingyenes ajánlat számít.

### <a name="register-your-company-account"></a>A vállalati fiók regisztrálása

1. Nyisson meg egy új Internet Explorer InPrivate vagy Chrome Inkognitó böngészési munkamenet, győződjön meg arról, hogy Ön nincs bejelentkezve a személyes fiókkal.

2. Nyissa meg a [Windows Dev Center](http://dev.windows.com/registration?accountprogram=azure) eladó el. Folytatás előtt olvassa el az alábbi fontos megjegyzést.

   ![A Microsoft-fiók ellenőrzése](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Ellenőrizze, hogy a e-mail azonosító vagy a terjesztési listát (egy terjesztési lista ajánlott függőség eltávolítása egyéni felhasználók számára), a fejlesztői központban regisztrálásához fog használni először egy Microsoft-fiókjával regisztrált. Ha nem, majd regisztrálja ezen hivatkozás használatával. Emellett minden olyan e-mail-azonosítót a Microsoft vállalati tartomány fejlesztői központ-regisztráció nem használható. "

   ![Dev center bejelentkezés](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Futtassa a "Segítse fiókja védelmét" varázslót a személyazonosságát, egy telefonszám vagy e-mail-cím használatával.

4. A regisztrációs fiókadatok, válassza ki a **számla ország/régió** a legördülő listából, és válassza ki a **tovább**.

   ![Válassza ki az országot/régiót](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Értékesítő" országokat: ahhoz, hogy a szolgáltatások az Azure piactéren értékesítheti, a regisztrált entitást kell lennie az egyik a jóváhagyott "értékesítő" országokat jelennek meg a legördülő listában. Ez a korlátozás küldenie jóváhagyásra cége kifizetési és adózási okból is. További információkért tekintse meg a piactér részvételi szabályzatának megfelelően.

5. Válassza ki **vállalati** , a "Fiók típusa", majd **tovább**.

    >[!IMPORTANT]
    >Fióktípus esetében jobb rálátással eldönteni, melyik vizsgán kíván részt venni, hogy a lap fióktípus esetében, a helyek és a díjak a következő képernyőfelvételen látható megtekintése.

    ![Az eladók fiókok típusai](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Adja meg a **közzétevő megjelenített név**. Ez általában a vállalata nevére.

    >[!NOTE]
    >A fejlesztői központban a megadott közzétevő megjelenített név szerepel az ajánlat az Azure Marketplace-en nem jelenik meg. De ezek az információk szükségesek a regisztrációs folyamat befejezéséhez.

7. Adja meg a **kapcsolattartási adatok** fiók ellenőrzése.

    >[!IMPORTANT]
    >Meg kell adnia pontos kapcsolattartási információinak biztosítása, mert azt fogja az ellenőrzési folyamat a vállalat számára a jóvá kell hagyni a fejlesztői központban. "

8. Adja meg a kapcsolattartási adatait a **vállalati jóváhagyó**. A vállalati jóváhagyó az a személy, aki ellenőrizheti, hogy jogosult-fiók létrehozása a fejlesztői központban a szervezet nevében. Miután megadta ezeket az információkat, válassza ki a **tovább** szeretne váltani a **fizetési szakasz**.

    ![Vállalati jóváhagyó azonosítása](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Adja meg a fiókhoz tartozó fizetési adatokat. Ha egy promóciós kódot, amely lefedi a regisztrációs költségét, megadhatja, hogy itt. Ellenkező esetben adja meg a hitelkártya adatai (vagy a támogatott piac PayPal). Válassza ki **tovább** szeretne váltani a végső **felülvizsgálati**.

   ![Fizetési regisztráció](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Tekintse át a fiók adatait, és győződjön meg arról, hogy minden rendben. Olvassa el és fogadja el a feltételeket és kikötéseket, a [a Microsoft Azure Marketplace kiadói szerződésében](http://go.microsoft.com/fwlink/?LinkID=699560). A jelölőnégyzet bejelölésével jelezze elolvasta és elfogadja ezeket a feltételeket.

11. Válassza ki **Befejezés** a regisztráció megerősítéséhez. Egy megerősítő üzenetet küld az e-mail-címre.

12. Ha csak az ingyenes ajánlatok közzététele, válassza ki a [nyissa meg a Cloud Partner portálra](https://cloudpartner.azure.com/) és a skip "Regisztrálni a fiók a cloud partner portálra a" Ebben a cikkben.

### <a name="commercial-offers"></a>Kereskedelmi ajánlatok

Ha kereskedelmi ajánlatokat, például a számlázási modell, óránkénti használatával virtuálisgép-ajánlat közzétételéhez meg kell adnia a adózási és banki információt. Ez, jelentkezzen be a fejlesztői központban lévő fiókjához, és válassza ki **a fiókadatok frissítése**. Kövesse a következő szakaszban a "Hozzáadás banki és adó-információ oldalt".

>[!IMPORTANT]
>Egy kereskedelmi ajánlattal leküldést az éles környezetben anélkül, hogy a fiók létrehozása és adózási adatait nem.

Ha inkább a banki és adózási adatait később, majd továbbléphet a "Regisztrálni a fiókot, a cloud partner portálra a" Ebben a cikkben.

>[!NOTE]
>Azt javasoljuk, hogy a fiók létrehozása és a lehető leghamarabb adózási adatait, mivel némi időre adózási adatok érvényesítéséhez.

### <a name="add-banking-and-tax-information"></a>Adja hozzá a banki és adózási adatait

Beszerzési kereskedelmi ajánlatokat a közzétételhez meg kell hozzáadása küldenie jóváhagyásra cége kifizetési és adózási adatait, és el a fejlesztői központban.

**Banki információk**

1.  Jelentkezzen be a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) Microsoft-fiókjával.
2.  Válassza ki **küldenie jóváhagyásra cége kifizetési fiók** bal oldali menüben lévő alatt **fizetési módszer**, jelölje be **bankszámla** vagy **PayPal**.

    >[!NOTE]
    >Ha kereskedelmi ajánlatokat a piactéren vásárló ügyfeleknek, ez az a fiók ahol küldenie jóváhagyásra cége kifizetési ezeket vásárlásokra fog kapni.
3.  Adja meg a fizetési adatokat, és válassza ki **mentése**.

    >[!IMPORTANT]
    >Kell küldenie jóváhagyásra cége kifizetési fiókját módosítani, ha kövesse az előző lépések az aktuális adatai helyett új információkkal.
    >
    >A küldenie jóváhagyásra cége kifizetési fiók módosítása késleltetheti-e a fizetések fel egy fizetési ciklus szerint. Ez a késleltetés az oka, hogy ellenőriznünk kell a fiók módosítás hasonlóan a küldenie jóváhagyásra cége kifizetési fiók első beállításakor. Meg fogjuk továbbra is lekérése fizetett a teljes összeget után a fiókját ellenőriztük; minden kifizetés miatt a jelenlegi fizetési ciklus a következő parancsot kell felvenni.

4.  Kattintson a **Tovább** gombra.

**Adózási információk**

1.  Jelentkezzen be a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) Microsoft-fiókjával (ha szükséges).
2.  A bal oldali menüben válassza ki a **profil adó**.
3.  Az a **állítsa be a kitöltésére** oldalon:
    - Válassza ki az országot vagy régiót, ahol az állandó helyével rendelkezik.
    - Válassza ki az országot vagy régiót, ahol elsődleges polgárságról kis türelmet.
    - Kattintson a **Tovább** gombra.
4.  Adja meg a adózási adatait, és válassza ki **tovább**.

>[FIGYELMEZTETÉS]! A kereskedelmi ajánlatokat az éles környezetben anélkül, hogy a fiók létrehozása és adózási adatait a Microsoft Developer Center fiókjában lemezképekét fogunk.

### <a name="developer-center-registration-issues"></a>Fejlesztői központ regisztrációjával kapcsolatos problémák

Ha problémába ütközik a fejlesztői központ-regisztráció, a következő lépések segítségével hozzon létre egy támogatási jegyet.

1.  Nyissa meg a [támogatási hivatkozás](https://developer.microsoft.com/windows/support).
2.  A **lépjen velünk kapcsolatba**válassza **beküldhet**.
    ![Hozzon létre egy jegyet](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  A **Problématípus**, válassza ki a "Súgó-fejlesztői központ" és a **kategória**, jelölje be "közzététel és kezeli az alkalmazásokat". Válassza ki **indítsa el az e-mailek**.

    ![probléma azonosítása](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Fog a bejelentkezési oldalon kell megadni. Bármilyen Microsoft-fiók használatával jelentkezzen be. Ha Microsoft-fiók nem rendelkezik majd [hozzon létre egyet](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Adja meg a problémát, majd válassza a részletes információkat **küldés** a jegy küldéséhez.

    ![egy a jegy küldése](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>A fiók regisztrálásához a cloud partner portálra

Használja a [Cloud Partner Portalon](https://cloudpartner.azure.com/) tehet közzé és felügyelhet a esetében.

1.  Nyisson meg egy új Inkognitó Chrome vagy Internet Explorer InPrivate-böngészési munkamenet, győződjön meg arról, hogy Ön nincs bejelentkezve, egy személyes fiók.
2.  Lépjen a [Cloud Partner Portalon](https://cloudpartner.azure.com/).
3.  Ha Ön új felhasználó, és bejelentkezik a [Cloud Partner Portalon](https://cloudpartner.azure.com/) először, majd be kell jelentkeznie a fejlesztői központban lévő fiókjához regisztrált azonos e-mail-azonosító használatával. Ez biztosítja, hogy a fejlesztői központban lévő fiókjához, és a cloud partner portal fiók kapcsolódik egymáshoz.

Más tagjaival a vállalat az alkalmazáson dolgozó később is hozzáadhat. Exportálhatja a közreműködők vagy a cloud partner portálra a tulajdonosok a következő rész lépéseit követve.

Ha hozzáadott egy közreműködői vagy tulajdonosa, a cloud partner portal portál van, majd bejelentkezhet az Ön fiókja.

>[!TIP]
>A részvételi szabályzatának ismerteti az Azure webhelyén.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Tulajdonos vagy a cloud partner portálra a közreműködők felhasználók kezelése

[Cloud partner portálra a felhasználók felügyeletének lépései](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>További lépések

Most, hogy a fiók létrejön, és regisztrálva, elkezdheti az Azure marketplace közzétételi folyamat.
