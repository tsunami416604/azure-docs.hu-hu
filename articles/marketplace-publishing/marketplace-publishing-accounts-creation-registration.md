---
title: Létrehozás és a kiadói fiók regisztrációja |} A Microsoft Docs
description: Útmutató a Microsoft Developer-fiók létrehozásához, a jóváhagyást követően értékesítés különböző típusok az Azure Marketplace-en kínálnak.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
ms.openlocfilehash: 24dc50a6c78769877bdc07b9521a9d2e33837937
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579615"
---
# <a name="create-a-microsoft-developer-account"></a>A Microsoft Developer-fiók létrehozása
Ez a cikk végigvezeti a szükséges fiók létrehozása és a egy engedélyezett Microsoft Developer válnak az Azure Marketplace-en való regisztrációs folyamatot.

## <a name="1-create-a-microsoft-account"></a>1. Microsoft-fiók regisztrálása
A közzétételi folyamat indításához szüksége lesz egy Microsoft-fiók létrehozásához. Ez a fiók használható mindkét regisztrálása a **Microsoft Developer Center** és **Azure közzétételi portálján**. Az Azure piactér-i ajánlatainak közzétételéhez egyetlen Microsoft-fiókkal kell rendelkeznie. Nem lehet adott szolgáltatások vagy ajánlatokat.

A felhasználó nevét a cím a tartományban kell lennie, és az informatikai csapat által vezérelt. Ezzel a fiókkal minden közzétételi kapcsolódó tevékenységeket kell elvégezni.

> [!WARNING]
> Szavakat, például **"Azure"** és **"Microsoft"** nem támogatja a Microsoft-fiókot regisztrálni. Kerülje azokat a szavakat a fiók létrehozása és a regisztrációs folyamat végrehajtásához.
>
>

### <a name="guidelines-for-company-accounts"></a>Útmutató a vállalati fiókok
Céges fiók létrehozásakor kövesse ezeket az irányelveket, ha egynél több személynek kell a Microsoft-fiókjával, amely a fiók megnyitva a fiókja eléréséhez.

> [!Important]
> Fontos, hogy lehetővé teszi több felhasználó hozzáférjen a fejlesztői központ-fiókjához, javasoljuk, hogy az Azure Active Directory használata egyéni felhasználók számára, ki férhet hozzá a fiók bejelentkezés után az egyéni szerepkörök hozzárendelése az Azure AD hitelesítő adatait. További információ: [fiókfelhasználók kezelése](https://msdn.microsoft.com/windows/uwp/publish/manage-account-users).

* Az e-mail-cím, amelyhez tartozik, a vállalati tartományhoz, de egyetlen személy, nem Microsoft-fiók létrehozása – például windowsapps@fabrikam.com.
* A Microsoft-fiókot a lehető legkevesebb fejlesztők való hozzáférés korlátozásához.
* Állítsa be a vállalati e-mailek terjesztési lista, amely tartalmazza az mindenki, aki hozzáféréssel kell rendelkeznie a fejlesztői fiókhoz, és adja hozzá ezt az e-mail címet biztonsági adatait. Ez lehetővé teszi az összes alkalmazott számára a listán megkapni a biztonsági kódokat, ha szükséges, és kezelheti a Microsoft-fiók biztonsági adatait. Ha a terjesztési lista beállítása nem megvalósítható, az egyéni e-mail fiók tulajdonosának kell hozzáférni és megosztani azokat a biztonsági kódot, amikor a rendszer kéri, (például amikor az új biztonsági adatok hozzáadni a fiókhoz, vagy ha azt kell érhetők el az új eszköz) számára elérhetővé válnak.
* Adjon hozzá egy munkahelyi telefonszám, amely nem igényel bővítmény és key csapat tagjai számára elérhető.
* Jelentkezzen be a vállalati fejlesztői fiók a megbízható eszközök segítségével a fejlesztők általában rendelkeznek. Az összes fő csapat tagjai a megbízható eszközökhöz való hozzáféréssel kell rendelkeznie. Ez csökkenti a biztonsági kódokat a fiók elérésekor küldendő szükségességét.
* Ha engedélyezi a hozzáférést a fiók nem megbízható számítógépről kell, a férjenek hozzá az legfeljebb öt fejlesztők számára. Ideális esetben fejlesztőket hozzá kell férnie a fiók, amely azonos földrajzi és a hálózati hely gépekről.
* Gyakran tekintse át a vállalat biztonsági adatait, [ https://account.live.com/proofs/Manage ](https://account.live.com/proofs/Manage) , győződjön meg arról, hogy minden jelenlegi.

Fejlesztői fiók nyitása elsősorban megbízható számítógépeket kell elérhető. Ez azért fontos, mert létrehozott hetente fiókonkénti kódok száma korlátozva van. Azt is lehetővé teszi, hogy a legtöbb zökkenőmentes bejelentkezési élmény.

További információt a további fejlesztői fiók irányelvek és a biztonsággal, [Itt](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="instructions"></a>Utasítások
1. Nyisson meg egy új Inkognitó Chrome vagy Internet Explorer InPrivate-böngészési munkamenet, győződjön meg arról, hogy Ön nincs bejelentkezve a meglévő fiók.
2. Regisztrálja az e-mailben (a fenti irányelvek száma pl. windowsapp@fabrikam.com) hivatkozás használatával egy Microsoft-fiókként [ https://signup.live.com/signup.aspx ](https://signup.live.com/signup.aspx). Kövesse az alábbi utasításokat.

   1. Fiókját egy Microsoft-fiók a regisztrálás során meg kell adnia egy érvényes telefonszámot adjon a rendszer arra, hogy egy fiók ellenőrző kódot szöveges üzenetben vagy egy automata fel fogja hívni.
   2. Fiókját egy Microsoft-fiók a regisztrálás során meg kell adnia egy érvényes e-mail-azonosítót a fiók ellenőrzése az automatikus e-mail fogadása.
3. Ellenőrizze az e-mail címet a terjesztési listához küldeni.
4. Most már készen áll az új Microsoft-fiók használata a Microsoft Developer Centerben.

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2. Regisztrálja a fiókot a Microsoft Developer Centerben
A Microsoft Developer Center egyszer a vállalati adatok regisztrálásához használatos. A regisztráló kell lennie a vállalat érvényes képviselője, és saját identitás ellenőrzése, hogy személyes adatokat kell megadnia. A személy regisztrálása kell használnia a Microsoft-fiók, amelyet a vállalat **, és ugyanazt a fiókot kell használni az Azure közzétételi portálján.** Ellenőrizze, hogy a vállalat még nem rendelkezik a Microsoft Developer Center fiók előtt hozzon létre egyet. A folyamat során a céges címadatok, banki információt, beszedjük és adózási adatait. Ezek az adatok általában megtalálhatók a pénzügyi vagy üzleti kapcsolattartói adatok között.

> [!IMPORTANT]
> Annak érdekében, hogy az ajánlat létrehozásának és üzembe helyezésének különböző fázisait halad, el kell végeznie az alábbi fejlesztői profil összetevőket.
>
>

| Fejlesztői profil | Vázlat indítása | Fájlok másolása folyamatban | Ingyenes közzététele és megoldássablon | Kereskedelmi közzététele |
| --- | --- | --- | --- | --- |
| Céges regisztráció |Rendelkeznie kell |Rendelkeznie kell |Rendelkeznie kell |Rendelkeznie kell |
| Adózási profil azonosítója |Optional |Optional |Optional |Rendelkeznie kell |
| Fiók létrehozása |Optional |Optional |Optional |Rendelkeznie kell |

> [!NOTE]
> A saját licences (BYOL) használata csak a virtuális gépek esetében támogatott, és számít egy **ingyenes** ajánlat.
>
>

### <a name="register-your-company-account"></a>A vállalati fiók regisztrálása
1. Nyisson meg egy új Internet Explorer InPrivate vagy Chrome Inkognitó böngészési munkamenet, győződjön meg arról, hogy Ön nincs bejelentkezve a személyes fiókkal.
2. Lépjen a [ http://dev.windows.com/registration?accountprogram=azure ](http://dev.windows.com/registration?accountprogram=azure) el egy értékesítő a fejlesztői központban. Folytatás előtt olvassa el az alábbi fontos megjegyzést.

   > [!IMPORTANT]
   > Ellenőrizze, hogy a e-mail azonosító vagy a terjesztési listát (egy terjesztési lista ajánlott függőség eltávolítása egyéni felhasználók számára), a fejlesztői központban regisztrálásához fog használni először egy Microsoft-fiókjával regisztrált. Ha nem, majd regisztrálja ez [hivatkozás](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1). Ezenkívül **bármely e-mail-azonosító alapján a Microsoft vállalati tartomány azaz @microsoft.com nem használható** fejlesztői központ-regisztráció.
   >
   >

    ![rajz][img-signin]
3. Fejezze be a "Segítse fiókja védelmét" varázsló, amely ellenőrzi, hogy Ön személyazonosságát telefonszámát vagy e-mail címét.

    ![rajz][img-verify]
4. A "Regisztráció – fiókadatok" területen válassza ki a **számla ország/régió** a legördülő menüből.

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"Értékesítő" országokat:** annak érdekében, hogy a szolgáltatások az Azure piactéren értékesítheti, a regisztrált entitást kell lennie a jóváhagyott "értékesítő" országokat fenti közül. Ez a korlátozás küldenie jóváhagyásra cége kifizetési és adózási okból is. A Microsoft aktívan kíváncsi, bontsa ki a közeljövőben országok listája, úgyhogy érdemes figyelnie. További információkért lásd: a [piactér részvételi szabályzatának](http://go.microsoft.com/fwlink/?LinkID=526833).
   >
   >
5. Válassza ki a "fiók típusa" **vállalati** és kattintson a **tovább** gombra.

   > [!IMPORTANT]
   > Jobb megértése a fióktípusokat és a legjobban, választhat, tekintse meg az oldal [fiók díjak, típusok és helyek](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. Adja meg a **közzétevő megjelenített név**, általában a cég nevét.

   > [!TIP]
   > A fejlesztői központban a megadott közzétevő megjelenített név nem jelenik meg az Azure piactéren az ajánlat kerül a listában szereplő után. Azonban ez a regisztrációs folyamat befejezéséhez ki kell tölteni.
   >
   >
7. Adja meg a **kapcsolattartási adatok** fiók ellenőrzése.

   > [!IMPORTANT]
   > Pontos kapcsolattartási információinak biztosítása meg kell adni, mert azt fogja az ellenőrzési folyamat a vállalat számára a jóvá kell hagyni a fejlesztői központban.
   >
   >
8. Adja meg a kapcsolattartási adatait a **vállalati jóváhagyó**. Vállalati jóváhagyó az a személy, aki ellenőrizheti, hogy jogosult-fiók létrehozása a fejlesztői központban a szervezet nevében. Kattintson a **tovább** szeretne váltani a **"Fizetési szakasz"** végeztével.

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. Adja meg a fizetési információk kell fizetnem a fiókjához. Ha egy promóciós kódot, amely lefedi a regisztrációs költségét, megadhatja, hogy itt. Ellenkező esetben adja meg a hitelkártya adatai (vagy a támogatott piac PayPal). Ha elkészült, kattintson a **tovább** a áthelyezése a **"Képernyőn tekintse át"**.

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. Tekintse át a fiók adatait, és győződjön meg arról, hogy minden rendben. Ezután olvassa el és fogadja el a feltételeket és kikötéseket, a [a Microsoft Azure Marketplace kiadói szerződésében](http://go.microsoft.com/fwlink/?LinkID=699560). A jelölőnégyzet bejelölésével jelezze elolvasta és elfogadja ezeket a feltételeket.
11. Kattintson a **Befejezés** a regisztráció megerősítéséhez. Az e-mail-címét egy megerősítő üzenetet küldünk.
12. Ha azt tervezi, csak az ingyenes ajánlatokhoz közzétételére, kattintson a **Ugrás az Azure piactér közzétételi portálján** , és továbbléphet a 3. szakasz ebben a dokumentumban [regisztrálni a fiókot a közzétételi portálon](#3-register-your-account-in-the-publishing-portal).

Ha azt tervezi, közzétételéhez kereskedelmi kínál (pl. virtuális gépek, amelyek óránkénti számlázási modell), kattintson a **a fiókadatok frissítése** ahol ki kell töltenie a adózási és banki információt a fejlesztői központban lévő fiókjához.

Szeretné frissíteni a később a adózási és banki információt, akkor továbbléphet a következő szakasszal, azaz szakasz 3 ebben a dokumentumban [regisztrálni a fiókot a közzétételi portálon](#3-register-your-account-in-the-publishing-portal), és térjen vissza később az Azure közzétételi található hivatkozások használatával Portál.

> [!IMPORTANT]
> Kereskedelmi ajánlatok esetén, nem éles környezetben az ajánlatok lemezképekét adózási és banki információk megadása nélkül.
>
>

Ha szeretné frissíteni a később a adózási és banki információt, megnyithatja a szakasz 3, [regisztrálni a fiókot a közzétételi portálon](#3-register-your-account-in-the-publishing-portal), és térjen vissza később az Azure közzétételi portálján található hivatkozások segítségével.

### <a name="add-tax-and-banking-information"></a>Adózási és banki információt hozzáadása
 Ha közzé szeretné tenni a vásárlás kereskedelmi ajánlatokat, akkor is kell hozzáadása küldenie jóváhagyásra cége kifizetési és adózási adatait, és el a fejlesztői központban. Ha csak az ingyenes ajánlatokhoz közzéteendő (vagy BYOL kínál), majd nem hozzá kell ezeket az információkat. Később is hozzáadhat, de csak az adózási adatok érvényesítéséhez bizonyos idő elteltével. Ha ismeri a kereskedelmi beszerzési ajánlatok tartalmazni fogja, azt javasoljuk, hogy hozzáadja a lehető legrövidebb időn belül.

**Banki adatok**

1. Jelentkezzen be a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) Microsoft-fiókjával.
2. Kattintson a **küldenie jóváhagyásra cége kifizetési fiók** bal oldali menüben lévő alatt **válasszon fizetési módot** kattintson **bankszámla** vagy **PayPal**.

   > [!IMPORTANT]
   > Ha kereskedelmi ajánlatokat a piactéren vásárló ügyfeleknek, ez az a fiók ahol küldenie jóváhagyásra cége kifizetési ezeket vásárlásokra fog kapni.
   >
   >
3. Adja meg a fizetési adatokat, és kattintson a **mentése** amikor elégedett.

   > [!IMPORTANT]
   > Ha frissíteni vagy küldenie jóváhagyásra cége kifizetési fiókját módosítani kell, ugyanezekkel a lépésekkel felett, és cserélje le a jelenlegi adatait az új adatok. A küldenie jóváhagyásra cége kifizetési fiók módosítása késleltetheti-e a fizetések fel egy fizetési ciklus szerint. Ez a késleltetés az oka, hogy ellenőriznünk kell a fiók módosítás hasonlóan a küldenie jóváhagyásra cége kifizetési fiók első beállításakor. Meg fogjuk továbbra is lekérése fizetett a teljes összeget után a fiókját ellenőriztük; minden kifizetés miatt a jelenlegi fizetési ciklus a következő parancsot kell felvenni.
   >
   >
4. Kattintson a **Tovább** gombra.

**Adózási adatait**

1. Jelentkezzen be a [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) Microsoft-fiókjával (ha szükséges).
2. Kattintson a **profil adó** a bal oldali menüben.
3. Az a **állítsa be a kitöltésére** lapon válassza ki az országot vagy régiót, ahol az állandó helyével rendelkezik, és válassza ki az országot vagy régiót, ahol elsődleges polgárságról kis türelmet. Kattintson a **Tovább** gombra.
4. Adja meg a adózási adatait, és kattintson **tovább**.

> [!WARNING]
> Nem kell az éles környezetben, az adózási és banki információt, a Microsoft Developer Center-fiókban nélkül kínál a kereskedelmi leküldést.
>
>

Ha problémákba ütközik a fejlesztői központ-regisztráció, kérjük jelentkezzen egy támogatási jegyet az alábbi

1. Nyissa meg a támogatási hivatkozás [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. A **lépjen velünk kapcsolatba** területén kattintson a gombra **beküldhet** (ahogyan az alábbi képernyőképen látható)

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. Válassza a "Súgó-fejlesztői központ" lehetőséget **Problématípus** és a "közzététel és kezeli az alkalmazásokat", **kategória**. Miután a "Start e-mail" gombra kattintva.

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. A bejelentkezési oldal biztosítjuk. Használja bármely Microsoft-fiók jelentkezzen be. Ha nem rendelkezik Microsoft-fiók, majd hozzon létre egyet a [hivatkozás](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
5. Adja meg az adatokat a probléma és subit a jegy kattintva a **küldés** gombra.

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3. A fiók regisztrálásához a közzétételi portálon
A [közzétételi portál](http://publish.windowsazure.com) tehet közzé és felügyelhet a esetében használatos.

1. Nyisson meg egy új Inkognitó Chrome vagy Internet Explorer InPrivate-böngészési munkamenet, győződjön meg arról, hogy Ön nincs bejelentkezve, egy személyes fiók.
2. Nyissa meg a következőt: [http://publish.windowsazure.com](http://publish.windowsazure.com).
3. Ha Ön új felhasználó, és jelentkezzen be a közzétételi portál első alkalommal, majd kell bejelentkeznie, amellyel a fejlesztői központban lévő fiókjához regisztrált e-mail azonosítója. Ezzel a módszerrel a fejlesztői központban lévő fiókjához és a közzétételi portál fiók fogja össze egymással. Később hozzáadhatja más tagjaival a vállalat számára az alkalmazás dolgozik, ahogy az a közzétételi társadminisztrátorként portál, az alábbi lépéseket követve.

Ha lesz hozzáadva, társadminisztrátorként a közzétételi portálon, majd bejelentkezhet a társadminisztrátori fiókot.

> [!TIP]
> A részvételi szabályzatának leírása a [Azure-webhelyen](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4. Lépések hozzáadása társadminisztrátorként a közzétételi portálon
**Feltételezve, hogy a rendszergazdaként** az alábbi lépések a hozzáadása társadminisztrátorként.

> [!NOTE]
> **Az új felhasználók** hozzáadása társadminisztrátorként való a közzététel előtt portálon győződjön meg arról, hogy legalább egy alkalmazás létrehozott a közzétételi portálon. Erre azért szükség, a **KÖZZÉTEVŐK** lap jelenik meg, csak után legalább egy alkalmazás létrehozása a közzétételi portál.
>
>

1. Győződjön meg arról, hogy a társadminisztrátori e-mail-azonosító egy Microsoft account(MSA). Ha nem, regisztrálja, ez egy MSA [hivatkozás](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Győződjön meg arról, hogy nincs-e legalább egy alkalmazást a rendszergazda fiókban előtt adjon hozzá társadminisztrátorként.
3. Miután végzett a fenti lépéseket, jelentkezzen be a közzétételi portál a társadminisztrátori e-mail-azonosítót, és a kijelentkezési.
4. Most már a közzététel bejelentkezési portált a rendszergazda e-mail azonosítója.
5. Keresse meg a kiadók -> Válassza ki a fiók -> rendszergazdák hozzáadása a társadminisztrátor (az alábbi képernyőképen) ->

   ![rajz](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5. A közzététel a társadminisztrátorként törlésére vonatkozó lépéseket portál
**Feltételezve, hogy a rendszergazdaként** az alábbi lépések a törlése társadminisztrátorként.

1. Jelentkezzen be a közzétételi portál, a rendszergazda e-mail azonosítóval.
2. Navigáljon a **közzétevők** -> Válassza ki a fiók -> **rendszergazdák** -> **Társrendszergazdák**.
3. Kattintson a **X** mellett a társadminisztrátori azt szeretné, hogy tot törlése (az alábbi képernyőképen) gombra.

    ![rajz](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>További lépések
Most, hogy a fiók létrejön, és regisztrálva, győződjön meg arról teljesítéséhez, illetve igazíthatja az ajánlat közzététele áttekintésével nem technikai jellegű Előfeltételek [nem technikai jellegű Előfeltételek](marketplace-publishing-pre-requisites.md).

## <a name="see-also"></a>Lásd még
* [Első lépések: az ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md
