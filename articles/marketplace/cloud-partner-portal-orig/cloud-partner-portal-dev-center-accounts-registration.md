---
title: Microsoft Developer-fiók létrehozása | Azure piactér
description: A Microsoft fejlesztői fiók létrehozásához szükséges követelmények és lépések.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 522caa53123ad5ccb8076839d2dfa7dc1236b022
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818429"
---
<a name="create-a-microsoft-developer-account"></a>Microsoft fejlesztői fiók létrehozása
====================================

Ez a cikk azt ismerteti, hogy miként lehet jóváhagyni az Azure Marketplace-közzététel jóváhagyott Microsoft Developer szolgáltatását.

## <a name="create-a-microsoft-account"></a>Microsoft-fiók létrehozása

A közzétételi folyamat elindításához el kell végeznie a **Microsoft fejlesztői központ** regisztrációját. Ugyanazt a regisztrált fiókot fogja használni a **[Cloud Partner Portal](https://cloudpartner.azure.com/)** a közzétételi folyamat elindításához.

### <a name="general-account-guidelines"></a>Általános fiókra vonatkozó irányelvek

Javasoljuk, hogy az Azure Marketplace-ajánlatokhoz csak egy Microsoft-fiók rendelkezzen. Ez a fiók nem lehet konkrét a szolgáltatásokra vagy ajánlatokra.

A felhasználónevet képező címnek a tartományon kell lennie, és az informatikai csapatnak kell ellenőriznie. Az összes közzétételi kapcsolódó tevékenység ezen a fiókon keresztül valósítható meg.

>[!WARNING]
>Az "Azure" és a "Microsoft" szavak nem támogatottak Microsoft-fiók regisztrációhoz. Ne használja ezeket a szavakat a fiók létrehozási és regisztrációs folyamatának elvégzéséhez.

### <a name="company-account-guidelines"></a>A vállalati fiókra vonatkozó irányelvek

Kövesse az alábbi irányelveket, ha több személynek is hozzá kell férnie a fiókhoz a fiókot megnyitó Microsoft-fiók való bejelentkezéssel.

>[!IMPORTANT]
>Ahhoz, hogy több felhasználó hozzáférhessen a Fejlesztői központ fiókjához, javasoljuk, hogy az Azure Active Directory használatával rendeljen hozzá szerepköröket az egyes felhasználókhoz. Az egyes Azure AD-beli hitelesítő adataikkal való bejelentkezéssel hozzáférhetnek a fiókhoz. További információ: [fiókok felhasználóinak kezelése](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Hozza létre a Microsoft-fiók a vállalati\'s tartományhoz tartozó e-mail-címmel, de egyetlen személynek sem. Például windowsapps\@fabrikam.com.
-   Korlátozza a hozzáférést ehhez a Microsoft-fiók a lehető legkisebb számú fejlesztőhöz.
-   Állítson be egy vállalati e-mail-terjesztési listát, amely tartalmazza mindazokat, akik hozzáférnek a fejlesztői fiókhoz, és hozzá kell adni ezt az e-mail-címet a biztonsági adataihoz. Ez lehetővé teszi, hogy a listán szereplő összes alkalmazott biztonsági kódokat kapjon, amikor szükséges, és felügyelje Microsoft-fiók biztonsági adatait. Ha a terjesztési lista beállítása nem lehetséges, akkor az egyéni e-mail-fiók tulajdonosának elérhetőnek kell lennie az eléréséhez és a biztonsági kód megosztásához, amikor a rendszer kéri (például ha új biztonsági adatokat ad hozzá a fiókhoz, vagy ha egy új eszközről kell elérnie.)
-   Adjon hozzá egy olyan vállalati telefonszámot, amelyhez nincs szükség bővítményre, és elérhető a legfontosabb csapattagok számára.
-   Általánosságban elmondható, hogy a fejlesztők megbízható eszközöket használnak a vállalat fejlesztői fiókjába való bejelentkezéshez. Az összes kulcsfontosságú csapat tagjának hozzáféréssel kell rendelkeznie ezekhez a megbízható eszközökhöz. Ez csökkenti a fiókhoz való hozzáféréskor a biztonsági kódok elküldéseinak szükségességét.
-   Ha nem megbízható SZÁMÍTÓGÉPRŐL szeretné engedélyezni a fiók elérését, korlátozza a hozzáférést legfeljebb öt fejlesztőhöz. Ideális esetben ezeknek a fejlesztőknek el kell érniük a fiókot azokról a gépekről, amelyek ugyanazt a földrajzi és hálózati helyet használják.
-   Gyakran tekintse át a [vállalat biztonsági adatait](https://account.live.com/proofs/Manage) , és győződjön meg róla, hogy az aktuális.

>[!IMPORTANT]
>A fejlesztői fiókjának elsődlegesen megbízható számítógépekről kell elérhetőnek lennie. Ez azért fontos, mert a felhasználónként generált kódok száma hetente van korlátozva. Emellett a legzökkenőmentesebb bejelentkezési élményt is lehetővé teszi.
>
>További információ: [további fejlesztői fiókokra vonatkozó irányelvek és biztonság](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Microsoft-fiók létrehozása

1.  Nyisson meg egy új Chrome inkognitóban vagy Internet Explorer InPrivate-böngészési munkamenetet, és győződjön meg arról, hogy nincs bejelentkezve egy meglévő fiókba.
2.  A [hivatkozás](https://signup.live.com/signup.aspx)használatával regisztrálja az e-mailt (az előző irányelvek használatával) Microsoft-fiók. Hajtsa végre a következő regisztrációs utasításokat:

    - A fiók Microsoft-fiókként való regisztrálásakor meg kell adnia egy érvényes telefonszámot a rendszer számára, hogy szöveges üzenetként vagy automatikus hívásként küldjön Önnek egy fiókot ellenőrző kódot.
    - A fiók Microsoft-fiókként való regisztrálásakor érvényes e-mail-azonosítót kell megadnia a fiók-ellenőrzés automatikus e-mail-címének fogadásához.
    - Ellenőrizze a DL számára küldött e-mail-címet.

    Most már készen áll az új Microsoft-fiók használatára a Microsoft fejlesztői központban.

## <a name="register-your-account-in-microsoft-developer-center"></a>Fiók regisztrálása a Microsoft fejlesztői központban

A Microsoft fejlesztői központ a vállalati adatok egyszeri regisztrálására szolgál. A regisztrálónak a vállalat érvényes képviselőjének kell lennie, és meg kell adnia személyes adatait az identitásuk érvényesítéséhez. A regisztrálni kívánt személynek a vállalatnál megosztott Microsoft-fiók kell használnia, és ugyanazt a fiókot kell használnia **a Cloud Partner Portalban.** Győződjön meg arról, hogy a vállalat még nem rendelkezik Microsoft Developer Center-fiókkal, mielőtt megpróbál létrehozni egyet. A folyamat során a rendszer begyűjti a vállalati címadatok adatait, a bankszámla adatait és az adózási adatokat. Ezek az adatok általában megtalálhatók a pénzügyi vagy üzleti kapcsolattartói adatok között.

>[!IMPORTANT]
>A következő fejlesztői profilok összetevőit kell végrehajtania ahhoz, hogy az ajánlat létrehozásának és üzembe helyezésének különböző fázisain haladjon át.

| Fejlesztői profil     | Piszkozat elindítása    | Előkészítés       | Ingyenes és megoldási sablon közzététele   | Kereskedelmi közzététel   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Vállalati regisztráció  | Rendelkeznie kell         | Rendelkeznie kell     | Rendelkeznie kell                             | Rendelkeznie kell             |
| Adózási profil azonosítója        | Optional          | Optional      | Optional                              | Rendelkeznie kell             |
| Bankszámla          | Optional          | Optional      | Optional                              | Rendelkeznie kell             |

>[!NOTE]
>A saját licenc használata (BYOL) csak a virtuális gépek esetében támogatott, és ingyenes ajánlatnak minősül.

### <a name="register-your-company-account"></a>Vállalati fiók regisztrálása

1. Nyisson meg egy új Internet Explorer InPrivate-vagy Chrome inkognitóban-böngészési munkamenetet, és győződjön meg arról, hogy nincs bejelentkezve személyes fiókba.

2. Nyissa meg a [Windows fejlesztői központot](https://dev.windows.com/registration?accountprogram=azure) , és regisztrálja magát eladóként. A folytatás előtt olvassa el a következő fontos megjegyzést.

   ![Microsoft-fiók ellenőrzése](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Győződjön meg arról, hogy az e-mail-azonosító vagy a terjesztési lista (az egyes személyektől származó függőségek eltávolítására javasolt terjesztési lista), amelyet a rendszer a fejlesztői központban való regisztráláshoz használ, először Microsoft-fiókként lesz regisztrálva. Ha nem, akkor regisztrálja ezt a hivatkozást. Emellett a Microsoft vállalati tartományában található e-mail-azonosítók nem használhatók a Fejlesztői központ regisztrálásához.

   ![Fejlesztői központ bejelentkezés](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Futtassa a "Segítség a fiók védelméhez" varázslót, hogy igazolja személyazonosságát telefonszám vagy e-mail-cím használatával.

4. A regisztrációs fiók adatai területen válassza ki a **fiók országát/régióját** a legördülő listából, majd kattintson a **tovább**gombra.

   ![Ország/régió kiválasztása](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Viszonteladó" országok/régiók: a szolgáltatások Azure Marketplace-en való értékesítéséhez a regisztrált entitásnak a legördülő listában látható egyik jóváhagyott "értékesítő" országból/régióból kell származnia. Ez a korlátozás kifizetési és adózási okokból szükséges. További információ: Marketplace részvételi szabályzatok.

5. Válassza a **vállalat** lehetőséget a fiók típusaként, majd kattintson a **tovább**gombra.

    >[!IMPORTANT]
    >A fióktípus jobb megismeréséhez és annak eldöntéséhez, hogy melyik típus a legmegfelelőbb az Ön számára, tekintse meg az oldal fiókjának típusát, helyét és díját a következő képernyőfelvételen.

    ![Az értékesítők fiókjának típusai](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Adja meg a **közzétevő megjelenítendő nevét**. Ez általában a vállalat neve.

    >[!NOTE]
    >Az ajánlat listázása után a fejlesztői központban megadott közzétevő megjelenítendő neve nem jelenik meg az Azure piactéren. Ezek az információk azonban a regisztrációs folyamat befejezéséhez szükségesek.

7. Adja meg a fiók ellenőrzéséhez tartozó **kapcsolattartási adatokat** .

    >[!IMPORTANT]
    >Pontos kapcsolattartási adatokat kell megadnia, mivel azt az ellenőrzési folyamat során fogjuk használni a fejlesztői központban.

8. Adja meg a **vállalati jóváhagyó**kapcsolattartási adatait. A vállalati jóváhagyó az a személy, aki ellenőrizheti, hogy jogosult-e fiók létrehozására a fejlesztői központban a szervezet nevében. Miután megadta ezeket az adatokat, kattintson a **tovább** gombra a **fizetés szakaszra**való áttéréshez.

    ![A vállalat jóváhagyójának azonosítása](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Adja meg a fiókjához tartozó fizetési adatokat. Ha olyan promóciós kóddal rendelkezik, amely fedezi a regisztráció költségeit, Itt megadhatja a következőt:. Egyéb esetben adja meg a bankkártya adatait (vagy a PayPal-t a támogatott piacokon). A **Next (tovább** ) gombra kattintva lépjen be a végső **felülvizsgálatba**.

   ![Fizetési regisztráció](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Tekintse át a fiókadatok adatait, és győződjön meg róla, hogy minden helyes. Olvassa el és fogadja el a [Microsoft Azure Marketplace kiadói szerződés](https://go.microsoft.com/fwlink/?LinkID=699560)feltételeit. Jelölje be a jelölőnégyzetet, és jelezze, hogy elolvasta és elfogadta a jelen feltételeket.

11. A regisztráció megerősítéséhez válassza a **Befejezés** lehetőséget. A rendszer megerősítő üzenetet küld az e-mail-címére.

12. Ha úgy tervezi, hogy csak az ingyenes ajánlatokat teszi közzé, válassza [az Ugrás a Cloud Partner Portal](https://cloudpartner.azure.com/) lehetőséget, és ugorjon a "fiók regisztrálása a Felhőbeli partner portálon" lehetőségre ebben a cikkben.

### <a name="commercial-offers"></a>Kereskedelmi ajánlatok

Ha olyan kereskedelmi ajánlatokat kíván közzétenni, mint például a virtuálisgép-ajánlat egy óradíjas számlázási modell használatával, meg kell adnia az adó-és banki adatokat. Ehhez jelentkezzen be a Fejlesztői központ fiókjába, és válassza **a fiók adatainak frissítése**lehetőséget. Kövesse a következő, "banki és adóügyi információk hozzáadása" című szakaszban található utasításokat.

>[!IMPORTANT]
>Nem fog tudni leküldeni egy kereskedelmi ajánlatot az éles környezetbe bankszámla-és adózási adatok megadása nélkül.

Ha később szeretné frissíteni a banki és adózási információkat, a jelen cikk "a fiók regisztrálása a felhőalapú partner portálon" lehetőségre kattintva léphet tovább.

>[!NOTE]
>Javasoljuk, hogy a lehető legrövidebb időn belül adja meg a bankszámla-és adózási adatokat, mert időt vesz igénybe az adózási adatok érvényesítése.

### <a name="add-banking-and-tax-information"></a>Banki és adózási információk hozzáadása

Ha kereskedelmi ajánlatokat szeretne közzétenni a vásárláshoz, hozzá kell adnia a kifizetési és adózási adatokat, és el kell küldenie az ellenőrzéshez a fejlesztői központban.

**Banki adatok megadása**

1.  Jelentkezzen be a [Microsoft fejlesztői központjába](https://dev.windows.com/registration?accountprogram=azure) a Microsoft-fiók.
2.  Válassza a bal oldali menüben a **kifizetési fiók** kiválasztásalehetőséget, és válassza a **Bankszámla** vagy a **PayPal**lehetőséget.

    >[!NOTE]
    >Ha olyan kereskedelmi ajánlatokkal rendelkezik, amelyeket az ügyfelek vásárolnak a piactéren, akkor ez az a fiók, amelyben a vásárlások után kap kifizetést.
3.  Adja meg a fizetési adatokat, majd kattintson a **Mentés**gombra.

    >[!IMPORTANT]
    >Ha frissíteni vagy módosítania kell a kifizetési fiókját, az előző lépésekkel cserélje le az aktuális információt az új adatokra.
    >
    >A kifizetési fiók módosítása akár egy fizetési ciklusban is késleltetheti a befizetését. Ez a késleltetés azért fordul elő, mert ellenőrizni kell a fiók változását, ugyanúgy, ahogy a kifizetési fiók első beállításakor. A fiók ellenőrzése után továbbra is a teljes összegért kell fizetnie. a rendszer a jelenlegi fizetési ciklus miatt esedékes összes kifizetést hozzáadja a következőhöz.

4.  Kattintson a **Tovább** gombra.

**Adózási adatok megadása**

1.  Jelentkezzen be a [Microsoft fejlesztői központba](https://dev.windows.com/registration?accountprogram=azure) a Microsoft-fiók (ha szükséges).
2.  A bal oldali menüben válassza az **adózási profil**lehetőséget.
3.  Az **adózási űrlap beállítása** oldalon:
    - Válassza ki azt az országot vagy régiót, ahol állandó tartózkodási helye van.
    - Válassza ki azt az országot vagy régiót, ahol az elsődleges állampolgárságot tartja.
    - Kattintson a **Tovább** gombra.
4.  Adja meg az adó adatait, majd kattintson a **tovább**gombra.

>[!WARNING]
>Nem lehet leküldeni a kereskedelmi ajánlatait éles környezetbe anélkül, hogy bankszámla-és adózási adatokat kellene benyújtania a Microsoft Developer Center-fiókjában.

### <a name="developer-center-registration-issues"></a>Fejlesztői központ regisztrációs problémái

Ha problémák merülnek fel a Fejlesztői központ regisztrálásával kapcsolatban, kövesse az alábbi lépéseket egy támogatási jegy megnyitásához.

1.  Nyissa meg a [támogatási hivatkozást](https://developer.microsoft.com/windows/support).
2.  A **kapcsolatfelvételi**területen válassza **az incidens küldése**lehetőséget.
    ![jegy megnyitása](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  A **probléma típusa**beállításnál válassza a "Súgó a fejlesztői központhoz" lehetőséget, és a **Kategória**beállításnál válassza a "alkalmazások közzététele és kezelése" lehetőséget. Válassza az **E-mail indítása**lehetőséget.

    ![a probléma típusának azonosítása](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Ekkor megjelenik a bejelentkezési oldal. A bejelentkezéshez használjon bármilyen Microsoft-fiók. Ha nem rendelkezik Microsoft-fiók, [hozzon létre egyet](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Adja meg a probléma részletes adatait, és válassza a **Küldés** lehetőséget a jegy elküldéséhez.

    ![jegy elküldése](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Fiók regisztrálása a felhőalapú partner portálon

Az ajánlat (ok) közzétételéhez és kezeléséhez használja a [Cloud Partner Portal](https://cloudpartner.azure.com/) .

1.  Nyisson meg egy új Chrome inkognitóban vagy Internet Explorer InPrivate-böngészési munkamenetet, és győződjön meg arról, hogy nincs bejelentkezve személyes fiókba.
2.  Lépjen [Cloud Partner Portal](https://cloudpartner.azure.com/).
3.  Ha Ön új felhasználó, és első alkalommal jelentkezik be a [Cloud Partner Portalba](https://cloudpartner.azure.com/) , akkor a Fejlesztői központ fiókjához regisztrált e-mail-azonosítóval kell bejelentkeznie. Ez biztosítja, hogy a Fejlesztői központ fiókja és a felhőalapú partner portál-fiók egymáshoz legyen társítva.

Később felveheti az alkalmazáson dolgozó vállalat többi tagját is. A következő szakaszban ismertetett lépések végrehajtásával a Felhőbeli partner portálon közreműködőként vagy tulajdonosként használhatja őket.

Ha a Cloud Partner portál portálon közreműködőként vagy tulajdonosként van hozzáadva, akkor a saját fiókjával is bejelentkezhet.

>[!TIP]
>A részvételi szabályzatok ismertetését az Azure webhelyén találja.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Felhasználók kezelése tulajdonosként vagy közreműködőként a felhőalapú partner portálon

[A felhőalapú partner portál felhasználóinak kezeléséhez szükséges lépések](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>További lépések

Most, hogy létrehozta és regisztrálta a fiókját, elindíthatja az Azure Marketplace közzétételi folyamatát.
