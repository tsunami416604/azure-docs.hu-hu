---
title: Microsoft Developer-fiók létrehozása | Azure Piactér
description: A Microsoft Developer-fiók létrehozásának követelményei és lépései.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280371"
---
<a name="create-a-microsoft-developer-account"></a>Microsoft Developer-fiók létrehozása
====================================

Ez a cikk azt ismerteti, hogyan válhat jóváhagyott Microsoft Developer for Azure Marketplace közzétételi.

## <a name="create-a-microsoft-account"></a>Microsoft-fiók létrehozása

A közzétételi folyamat elindításához be kell fejeznie a **Microsoft Developer Center** regisztrációját. A közzétételi folyamat elindításához ugyanazt a regisztrált fiókot fogja használni a **[Cloud Partner Portalon.](https://cloudpartner.azure.com/)**

### <a name="general-account-guidelines"></a>Általános fiókirányelvek

Azt javasoljuk, hogy csak egy Microsoft-fiókkal rendelkezik az Azure Marketplace-ajánlatokhoz. Ez a fiók nem lehet kifejezetten a szolgáltatásokra vagy ajánlatokra jellemző.

A felhasználónevet alkotó címnek a tartományban kell lennie, és azt az informatikai csapatnak kell vezérelnie. A közzététellel kapcsolatos összes tevékenységet ezen a fiókon keresztül kell elvégezni.

>[!WARNING]
>Az olyan szavak, mint az "Azure" és a "Microsoft" nem támogatottak a Microsoft-fiók regisztrációja során. Kerülje a szavak használatát a fiók létrehozásának és a regisztrációs folyamatnak a befejezéséhez.

### <a name="company-account-guidelines"></a>Vállalati számla irányelvei

Kövesse az alábbi irányelveket, ha egynél több személynek kell hozzáférnie a fiókhoz a fiókot megnyitott Microsoft-fiókkal való bejelentkezéssel.

>[!IMPORTANT]
>Ahhoz, hogy több felhasználó is hozzáférhessen a fejlesztői központ-fiókjához, azt javasoljuk, hogy az Azure Active Directory használatával rendeljen szerepköröket az egyes felhasználókhoz. A fiók hoz való hozzáférés az egyéni Azure AD hitelesítő adatokkal való bejelentkezéssel. További információt a Fiókfelhasználók kezelése című [témakörben talál.](https://docs.microsoft.com/windows/uwp/publish/manage-account-users)

-   Hozza létre Microsoft-fiókját a vállalat\'tartományához tartozó e-mail-cím használatával, de nem egyetlen személyével. Például a\@windowsapps fabrikam.com.
-   Korlátozza a hozzáférést ehhez a Microsoft-fiókhoz a lehető legkevesebb fejlesztőre.
-   Állítson be egy vállalati e-mail terjesztési listát, amely tartalmazza a fejlesztői fiók eléréséhez szükséges összes vállalatot, és adja hozzá ezt az e-mail címet a biztonsági adatokhoz. Ez lehetővé teszi, hogy a listán szereplő összes alkalmazott szükség esetén megkapja a biztonsági kódokat, és kezelje a Microsoft-fiók biztonsági adatait. Ha a terjesztési lista beállítása nem lehetséges, az egyes e-mail fiók tulajdonosának elérhetőnek kell lennie a biztonsági kód eléréséhez és megosztásához, amikor a rendszer kéri (például amikor új biztonsági adatokat ad hozzá a fiókhoz, vagy amikor új eszközről kell elérni.)
-   Adjon meg egy olyan vállalati telefonszámot, amelyhez nincs szükség bővítményre, és amely a csapat kulcstagjai számára elérhető.
-   Általánosságban elmondható, hogy a fejlesztők megbízható eszközökkel jelentkezhetnek be a vállalat fejlesztői fiókjába. A csapat minden kulcsfontosságú tagjának hozzáféréssel kell rendelkeznie ezekhez a megbízható eszközökhöz. Ez csökkenti a fiók elérésekor küldendő biztonsági kódok szükségességét.
-   Ha nem megbízható számítógépről kell engedélyeznie a hozzáférést a fiókhoz, legfeljebb öt fejlesztőre korlátozza a hozzáférést. Ideális esetben ezek a fejlesztők az azonos földrajzi és hálózati helyet használó gépekről kell hozzáférniük a fiókhoz.
-   Gyakran tekintse át [a vállalat biztonsági adatait,](https://account.live.com/proofs/Manage) és győződjön meg arról, hogy az aktuális.

>[!IMPORTANT]
>A fejlesztői fiókot elsősorban megbízható számítógépekről kell elérni. Ez azért fontos, mert a fiókonként és hetente generált kódok száma korlátozva van. Azt is lehetővé teszi a legzökkenőmentesebb bejelentkezési élményt.
>
>További információt a [fejlesztői fiókra vonatkozó további irányelvek és biztonság](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)című témakörben talál.

### <a name="to-create-a-microsoft-account"></a>Microsoft-fiók létrehozása

1.  Nyisson meg egy új Chrome inkognitó- vagy Internet Explorer InPrivate-böngészési munkamenetet, hogy biztosan ne jelentkezett be egy meglévő fiókba.
2.  Regisztrálja az e-mailt (az előző irányelvek szerint) Microsoft-fiókként ezen a [hivatkozáson](https://signup.live.com/signup.aspx)keresztül. Töltse ki a következő regisztrációs utasításokat:

    - A fiók Microsoft-fiókként történő regisztrálásakor meg kell adnia egy érvényes telefonszámot ahhoz, hogy a rendszer szöveges üzenetként vagy automatikus hívásként elküldhesse a fiókellenőrző kódot.
    - A fiók Microsoft-fiókként történő regisztrálásakor meg kell adnia egy érvényes e-mail-azonosítót a fiók ellenőrzéséhez szükséges automatikus e-mail-cím fogadásához.
    - Ellenőrizze a dl-nek küldött e-mail címet.

    Most már készen áll az új Microsoft-fiók használatára a Microsoft Fejlesztői központban.

## <a name="register-your-account-in-microsoft-developer-center"></a>Fiók regisztrálása a Microsoft Fejlesztői központban

A Microsoft Developer Center segítségével egyszer regisztrálhatja a vállalati adatokat. A regisztrálónak a vállalat érvényes képviselőjének kell lennie, és személyes adataikat meg kell adnia személyazonosságának igazolása érdekében. A regisztráló személynek a vállalat számára megosztott Microsoft-fiókot kell használnia, **és ugyanazt a fiókot kell használnia a Cloud Partner Portalban.** Mielőtt megpróbálna létrehozni, ellenőrizze, hogy vállalata még nem rendelkezik-e Microsoft Developer Center-fiókkal. A folyamat során összegyűjtjük a vállalat címadatait, a bankszámlaadatokat és az adózási adatokat. Ezek általában pénzügyi vagy üzleti kapcsolatokból szerezhetők be.

>[!IMPORTANT]
>Az ajánlat létrehozásának és üzembe helyezésének különböző fázisaiban a következő fejlesztői profil-összetevőket kell végrehajtania.

| Fejlesztői profil     | A vázlat indítása    | Előkészítés       | Ingyenes közzététel és megoldássablon   | Kereskedelmi közzététel   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Cégbejegyzés  | Kell, hogy         | Kell, hogy     | Kell, hogy                             | Kell, hogy             |
| Adóprofil azonosítója        | Optional          | Optional      | Optional                              | Kell, hogy             |
| Bankszámla          | Optional          | Optional      | Optional                              | Kell, hogy             |

>[!NOTE]
>Bring Your Own License (BYOL) csak a virtuális gépek, és tekinthető ingyenes ajánlat.

### <a name="register-your-company-account"></a>Vállalati fiók regisztrálása

1. Nyisson meg egy új Internet Explorer InPrivate- vagy Chrome inkognitóböngészési munkamenetet, hogy biztosan ne jelentkezett be személyes fiókba.

2. A [Windows fejlesztői központban](https://dev.windows.com/registration?accountprogram=azure) regisztrálhatja magát eladóként. Kérjük, a folytatás előtt olvassa el az alábbi fontos megjegyzést.

   ![A Microsoft-fiók ellenőrzése](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Győződjön meg arról, hogy az e-mail-azonosító vagy terjesztési lista (a terjesztési lista ajánlott a függőség eltávolítása az egyénektől), amelyet használni fog a fejlesztői központban való regisztrációhoz, először regisztrált Microsoft-fiókként. Ha nem, akkor kérjük, regisztráljon ezzel a linkkel. Emellett a Microsoft vállalati tartománya alatt lévő e-mail-azonosítók nem használhatók fejlesztési központ-regisztrációhoz."

   ![Fejlesztői központ bejelentkezés](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Futtassa a "Segítség a fiók védelméhez" varázslót, hogy telefonszám vagy e-mail-cím használatával igazolja személyazonosságát.

4. A Regisztrációs fiók adatai listában válassza ki **a fiók országát/régióját** a legördülő listából, majd válassza a **Tovább**gombot.

   ![Ország/régió kiválasztása](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Értékesítő" országok/régiók: Ahhoz, hogy az Azure Marketplace-en értékesíthesse szolgáltatásait, a regisztrált entitásnak a legördülő listában szereplő jóváhagyott "értékesítő" országok/régiók egyikéből kell származnia. Ez a korlátozás a kifizetési és adózási okokból. További információt a Piactér részvételi szabályzatai ban talál.

5. Válassza **a Vállalat** lehetőséget a "Fióktípus", majd a **Tovább**lehetőséget.

    >[!IMPORTANT]
    >A fióktípusok jobb megértéséhez és annak eldöntéséhez, hogy melyik típus a legmegfelelőbb az Ön számára, tekintse meg a következő képernyőfelvételen látható fióktípusokat, helyeket és díjakat.

    ![Számlatípusok az eladókszámára](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Írja be a **Publisher megjelenítendő nevét**. Ez általában a vállalat neve.

    >[!NOTE]
    >A fejlesztői központban megadott közzétevői megjelenítendő név nem jelenik meg az Azure Piactéren az ajánlat listázása után. De ez az információ szükséges a regisztrációs folyamat befejezéséhez.

7. Adja meg a fiók ellenőrzéséhez szükséges **kapcsolattartási adatokat.**

    >[!IMPORTANT]
    >Pontos kapcsolattartási adatokat kell megadnia, mert azellenőrzéssorán azellenőrzés során felhasználjuk azÖn vállalatát, hogy jóváhagyják a Fejlesztői központban."

8. Adja meg a **vállalat jóváhagyójának kapcsolattartási**adatait. A vállalat jóváhagyója az a személy, aki ellenőrizheti, hogy ön jogosult-e fiókot létrehozni a fejlesztői központban a szervezet nevében. Miután megadta ezt az információt, válassza a **Tovább** gombot a **Fizetés szakaszra**lépéshez.

    ![A vállalat jóváhagyójának azonosítása](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Adja meg a fiók fizetési adatait. Ha van egy promóciós kódot, amely fedezi a regisztrációs költségek, megadhatja, hogy itt. Ellenkező esetben adja meg hitelkártya adatait (vagy a PayPal-t a támogatott piacokon). Válassza a **Tovább** gombot a végső ellenőrzésre való **továbblépéshez.**

   ![Fizetés regisztrálása](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Tekintse át a fiókadatait, és ellenőrizze, hogy minden rendben van-e. Olvassa el és fogadja el a [Microsoft Azure Piactér kiadói szerződésének](https://go.microsoft.com/fwlink/?LinkID=699560)feltételeit. Jelölje be a jelölőnégyzetet, jelezve, hogy elolvasta és elfogadta ezeket a feltételeket.

11. A regisztráció megerősítéséhez válassza a **Befejezés** lehetőséget. A rendszer megerősítő üzenetet küld az ön e-mail címére.

12. Ha csak ingyenes ajánlatok közzétételét tervezi, válassza [az Ugrás a Felhőpartner-portálra](https://cloudpartner.azure.com/) lehetőséget, és ugorjon a "Fiók regisztrálása a felhőpartneri portálon" című cikkben.

### <a name="commercial-offers"></a>Kereskedelmi ajánlatok

Ha azt tervezi, hogy kereskedelmi ajánlatok, például egy virtuális gép ajánlat egy óránkénti számlázási modell használatával, meg kell adnia az adózási és banki információkat. Ehhez jelentkezzen be a Fejlesztői központ fiókjába, és válassza **a Fiókadatok frissítése**lehetőséget. Kövesse a következő, "Banki és adózási információk hozzáadása" című szakasz utasításait.

>[!IMPORTANT]
>A kereskedelmi ajánlatot nem tudja a termelésre tolni anélkül, hogy bankszámla- és adózási információkat nyújtana.

Ha később szeretné frissíteni a banki és adózási adatait, akkor ugorjon a "Fiók regisztrálása a felhőpartneri portálon" lehetőségre ebben a cikkben.

>[!NOTE]
>Javasoljuk, hogy a lehető leghamarabb adja meg a bankszámlával és az adózási adatokkal kapcsolatos információkat, mert időbe telik az adózási adatok ellenőrzése.

### <a name="add-banking-and-tax-information"></a>Banki és adózási adatok hozzáadása

A kereskedelmi ajánlatok közzétételéhez meg kell adnia a kifizetési és adózási információkat, és be kell küldenie azokat érvényesítésre a Fejlesztői központban.

**Banki adatok biztosítása**

1.  Jelentkezzen be a [Microsoft Fejlesztői központba](https://dev.windows.com/registration?accountprogram=azure) Microsoft-fiókjával.
2.  Válassza a **Kifizetési számla lehetőséget** a bal oldali **menüBen,** a Fizetési mód kiválasztása csoportban válassza **a Bankszámla** vagy a **PayPal lehetőséget.**

    >[!NOTE]
    >Ha olyan kereskedelmi ajánlatai vannak, amelyeket az ügyfelek vásárolnak a Marketplace-en, akkor ez az a fiók, ahol megkapja a kifizetéseket ezekért a vásárlásokért.
3.  Adja meg a fizetési adatokat, majd válassza a **Mentés gombot.**

    >[!IMPORTANT]
    >Ha frissítenie vagy módosítania kell kifizetési számláját, kövesse az előző lépéseket az aktuális információk nak az új információkkal való lecseréléséhez.
    >
    >A kifizetési számla módosítása akár egy fizetési ciklussal is késleltetheti a kifizetéseket. Ez a késedelem azért fordul elő, mert ellenőriznünk kell a fiók változását, ugyanúgy, mint amikor először beállította dedikálta a kifizetési számlát. A fiók ellenőrzése után is megkapja a teljes összeg kifizetését; az aktuális fizetési ciklusra esedékes kifizetések hozzáadásra kerülnek a következő fizetési ciklushoz.

4.  Válassza a **Tovább lehetőséget.**

**Adózási információk biztosítása**

1.  Jelentkezzen be a [Microsoft Fejlesztői központba](https://dev.windows.com/registration?accountprogram=azure) microsoftos fiókjával (ha szükséges).
2.  A bal oldali menüben válassza az **Adóprofil lehetőséget.**
3.  Az **Adóűrlap beállítása** lapon:
    - Válassza ki azt az országot vagy régiót, ahol állandó lakhellyel rendelkezik.
    - Válassza ki azt az országot vagy régiót, ahol elsődleges állampolgársággal rendelkezik.
    - Válassza a **Tovább lehetőséget.**
4.  Adja meg az adózási adatait, majd válassza a **Tovább gombot.**

>[!WARNING]
>A kereskedelmi ajánlatokat nem tudja éles környezetbe vinni anélkül, hogy a Microsoft Developer Center-fiókjában megadná a bankszámlával és az adózással kapcsolatos információkat.

### <a name="developer-center-registration-issues"></a>Fejlesztői központ regisztrációs problémái

Ha problémái vannak a Fejlesztői központ regisztrációjával, az alábbi lépésekkel nyisson meg egy támogatási jegyet.

1.  Nyissa meg a [támogatási linket](https://developer.microsoft.com/windows/support).
2.  A **Kapcsolatfelvétel**csoportban válassza **az Incidens küldése**lehetőséget.
    ![Jegy megnyitása](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  A **Probléma típusnál**válassza a "Súgó a fejlesztői központtal" lehetőséget, a **Kategória**mezőben pedig válassza az "Alkalmazások közzététele és kezelése" lehetőséget. Válassza **a Levelezés indítása**lehetőséget.

    ![a probléma típusának azonosítása](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Kapsz egy bejelentkezési oldalt. A bejelentkezéshez bármely Microsoft-fiókkal jelentkezhet be. Ha nem rendelkezik Microsoft-fiókkal, akkor [hozzon létre egyet.\](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)

5.  Adjon meg részletes információkat a problémáról, és válassza a **Küldés** lehetőséget a jegy elküldéséhez.

    ![küldjön be egy jegyet](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Regisztrálja fiókját a felhőpartneri portálon

A [Felhőpartneri portál](https://cloudpartner.azure.com/) segítségével közzéteheti és kezelheti ajánlatait.

1.  Nyisson meg egy új Chrome inkognitó- vagy Internet Explorer InPrivate-böngészési munkamenetet, hogy biztosan ne jelentkezett be személyes fiókba.
2.  Nyissa meg a [Cloud Partner Portal -ot.](https://cloudpartner.azure.com/)
3.  Ha Ön új felhasználó, és először jelentkezik be a [Cloud Partner Portalra,](https://cloudpartner.azure.com/) akkor ugyanazzal az e-mail-azonosítóval kell bejelentkeznie, mint a Fejlesztői központ-fiókjában regisztrált e-mail azonosítóval. Ez biztosítja, hogy a Fejlesztői központ-fiók és a felhőpartner-portálfiók egymáshoz van kapcsolva.

Később hozzáadhatja a vállalat többi tagját, akik az alkalmazáson dolgoznak. Közreműködőként vagy tulajdonosként a felhőpartneri portálon a következő szakaszlépéseit követve.

Ha hozzá van adva közreműködőként/tulajdonosként a felhőpartner-portálportálon, akkor a saját fiókjával is bejelentkezhet.

>[!TIP]
>A részvételi szabályzatok leírása az Azure-webhelyen található.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Felhasználók kezelése tulajdonosként vagy közreműködőként a felhőpartneri portálon

[A felhasználók kezelésének lépései a felhőpartneri portálon](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>További lépések

Most, hogy a fiók létrejött és regisztrálva van, elindíthatja az Azure piactér közzétételi folyamatát.
