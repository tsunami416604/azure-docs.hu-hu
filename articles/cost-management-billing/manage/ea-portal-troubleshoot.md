---
title: A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása
description: Ez a cikk bemutat néhány gyakori problémát, amelyek az Azure Nagyvállalati Szerződéssel kapcsolatban léphetnek fel az Azure EA Portalon.
author: bandersmsft
ms.author: banders
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 3bf1c10e28ef9e3e4008968c9dbaef576bccdb5f
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411551"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása

Ez a cikk bemutat néhány gyakori problémát, amelyek az Azure Nagyvállalati Szerződéssel kapcsolatban léphetnek fel. Az Azure EA Portal a nagyvállalati szerződéses felhasználók és költségek kezelésére szolgál. Ezekkel a hibákkal az Azure EA Portalhoz való hozzáférés konfigurálása vagy frissítése során találkozhat.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Hibák egy rendszergazda regisztrációhoz való hozzáadásakor

Különböző hitelesítési szintek vannak a vállalati regisztrációkra vonatkozóan. Ha a hitelesítési szintek nem megfelelően vannak alkalmazva, hibák léphetnek fel az Azure EA Portalba való bejelentkezéskor.

Az Azure EA Portalon hozzáférést biztosíthat a különböző hitelesítési szintekkel rendelkező felhasználóknak. A vállalati rendszergazdák frissíthetik a hitelesítési szinteket, hogy megfeleljenek a szervezetük biztonsági követelményeinek.

### <a name="authentication-level-types"></a>Hitelesítési szintek típusai

- Csak Microsoft-fiók – azoknak a szervezeteknek, akik Microsoft-fiókokon keresztül szeretnék használni, létrehozni és felügyelni a felhasználókat.
- Munkahelyi vagy iskolai fiók – olyan szervezetek számára, amelyek felhőbeli összevonással telepítették az Active Directoryt, és az összes fiók egyetlen bérlőn található.
- Munkahelyi vagy iskolai fiókok több bérlőn – olyan szervezetek számára, amelyek felhőbeli összevonással telepítették az Active Directoryt, és a fiókjaik több bérlőn találhatók.
- Vegyes fiók – lehetővé teszi, hogy hozzáadjon Microsoft-fiókkal és/vagy munkahelyi vagy iskolai fiókkal rendelkező felhasználókat.

A regisztrációhoz elsőként hozzáadott munkahelyi vagy iskolai fiók határozza meg az _alapértelmezett_ vagy _elsődleges_ tartományt. Amennyiben egy másik bérlővel rendelkező munkahelyi vagy iskolai fiókot kíván hozzáadni, több-bérlős hitelesítésre kell módosítania a hitelesítési szintet a regisztrációban.

A hitelesítési szint frissítése:

1. Jelentkezzen be az Azure Portalra vállalati rendszergazdaként.
2. Kattintson a **Kezelés** elemre a bal oldali navigációs panelen.
3. Kattintson a **Regisztráció** fülre.
4. A **Regisztráció részletei** alatt válassza a **Hitelesítési szint** elemet.
5. Kattintson a ceruza szimbólumra.
6. Kattintson a **Save** (Mentés) gombra.

![Hitelesítési szinteket bemutató példa ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Minden Microsoft-fióknak rendelkeznie kell egy, a [https://signup.live.com](https://signup.live.com/) címen létrehozott hozzárendelt azonosítóval.

A munkahelyi vagy iskolai fiókok olyan szervezetek számára érhetők el, amelyek felhőbeli összevonással telepítették az Active Directoryt, és amelyek esetében az összes fiók egy bérlőn található. A felhasználók munkahelyi vagy iskolai összevont felhasználói hitelesítéssel adhatók hozzá, ha a szervezet belső Active Directoryja összevont.

Amennyiben a szervezet nem használja az Active Directory összevonási szolgáltatásokat, nem használhatja a munkahelyi vagy iskolai e-mail-címét. Ehelyett regisztráljon vagy hozzon létre egy új e-mail-címet, és regisztrálja Microsoft-fiókként.

## <a name="unable-to-access-the-azure-ea-portal"></a>Nem lehet elérni az Azure EA Portalt

Ha hibaüzenetet kap az Azure EA Portalba történő bejelentkezéskor, végezze el az alábbi hibaelhárítási lépéseket:

- Győződjön meg arról, hogy a helyes Azure EA Portal URL-címet használja, vagyis: https://ea.azure.com.
- Állapítsa meg, hogy az Azure EA Portalhoz munkahelyi, iskolai vagy Microsoft-fiókkal regisztrált.
  - Ha a munkahelyi fiókját használja, adja meg munkahelyi e-mail-címét és jelszavát. A munkahelyi jelszavát a vállalata határozza meg. Felkeresheti a szervezet informatikai részlegét is, hogy megtudja, hogyan állíthatja vissza jelszavát, ha problémái vannak vele.
  - Ha Microsoft-fiókot használ, adja meg a Microsoft-fiókjához tartozó e-mail-címet és jelszót. Ha elfelejtette a Microsoft-fiókja jelszavát, itt állíthatja vissza: [https://account.live.com/password/reset](https://account.live.com/password/reset).
- A bejelentkezéshez privát vagy inkognitó módú böngésző-munkamenetet használjon, hogy megakadályozza a korábbi vagy a jelenlegi munkamenetek cookie-jainak és gyorsítótárazott adatainak mentését. Törölje a böngésző gyorsítótárát, és használjon privát vagy inkognitó módú ablakot a https://ea.azure.com webhely megnyitásához.
- A Microsoft-fiókja használatakor megjelenő _Érvénytelen felhasználó_ hiba azért jelenhet meg, mert több Microsoft-fiókkal rendelkezik. A bejelentkezési kísérletnél megadott e-mail-cím nem az elsődleges e-mail-cím.
Vagy azért is kaphat _Érvénytelen felhasználó_ hibaüzenetet, mert helytelen fióktípust használtak a felhasználónak a regisztrációhoz való hozzáadásakor. Elképzelhető például, hogy munkahelyi vagy iskolai fiókot használtak a Microsoft-fiók helyett. Ez esetben egy vállalati rendszergazda hozzáadhatja a megfelelő fiókot, vagy Ön kapcsolatba léphet a [támogatási szolgálattal](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Ha ellenőrizni szeretné az elsődleges aliast, látogasson el a [https://account.live.com](https://account.live.com) címre. Ezután kattintson a **Saját adatok**, majd a **Microsoft-fiókba való bejelentkezés módja** elemre. Az útmutatást követve ellenőrizze a másodlagos e-mail-címet, és igényeljen egy kódot a bizalmas információk eléréséhez. Adja meg a biztonsági kódot. Válassza a **Beállítás később** lehetőséget, ha nem szeretné beállítani a kétfaktoros hitelesítést.
  - Ezután megjelenik a **Microsoft-fiókba való bejelentkezés módja** oldal, ahol megtekintheti a fiókhoz tartozó meglévő aliasokat. Ellenőrizze, hogy az elsődleges aliast használja-e az Azure EA Portalra történő bejelentkezéshez. Ha nem, beállíthatja az elsődleges aliasaként, vagy használhatja az elsődleges aliasát az Azure EA Portalra való bejelentkezéshez.

## <a name="no-activation-email-received"></a>Nem érkezett aktiválási e-mail

Az Azure EA Portal aktiválási e-mailje a *waep@microsoft.com* címről érkezik. Ha nem kapott aktiválási e-mailt, ellenőrizze a levélszemét mappát. Az üzenet tárgya a következő: _Invitation to View/Manage the Microsoft Azure service_subject_ (Meghívás a Microsoft Azure-szolgáltatás megtekintésére/kezelésére). Minden újonnan hozzáadott nagyvállalati rendszergazda megkapja ezt az e-mailt.

Ha biztos benne, hogy Ön vállalati rendszergazdaként lett beállítva, nem kell megvárnia az aktiválási e-mail megérkezését, hogy bejelentkezhessen az Azure EA Portalra. A https://ea.azure.com címen bejelentkezhet a munkahelyi, iskolai vagy Microsoft-fiókjához tartozó e-mail-címével és jelszavával.

## <a name="azure-ea-activation-faq"></a>Az Azure EA aktiválása – gyakori kérdések

A cikk ezen szakasza az Azure EA aktiválásával kapcsolatos gyakori problémák megoldásait ismerteti.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Új EA-rendszergazdát szeretnék hozzáadni a regisztrációhoz

Az új vállalati rendszergazdát meglévő vállalati rendszergazdák adhatják hozzá. Ha Ön az EA-rendszergazda, jelentkezzen be az EA Portalra, majd kattintson a **Kezelés** > **+ Rendszergazda hozzáadása** gombra a jobb felső sarokban az új EA-rendszergazda hozzáadásához. A felhasználók hozzáadásához győződjön meg arról, hogy ismeri az e-mail-címüket és az előnyben részesített bejelentkezési módszerüket (például munkahelyi/iskolai hitelesítés vagy Microsoft Live ID).

Ha nem Ön az EA-rendszergazda, forduljon EA-rendszergazdáihoz, és kérvényezze, hogy adják hozzá Önt a regisztrációhoz. Miután hozzáadták Önt a regisztrációhoz, aktiválási e-mailt fog kapni.

Ha azonban az EA-rendszergazdák nem tudnak segítséget nyújtani, mi is hozzáadhatjuk Önt a regisztrációhoz a nevükben, ha megadja az alábbi adatokat:
- regisztrációs szám.
- a hozzáadni kívánt e-mail-cím és a hitelesítés típusa (munkahelyi/iskolai/MS).
- e-mailes jóváhagyás az EA-rendszergazdától.

Ha minden szükséges információval rendelkezik, küldjön be egy kérelmet a következő helyen: [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Frissíteni szeretném a regisztráció első EA-rendszergazdáját

Az első EA-rendszergazda a mennyiségilicenc-szolgáltatási központban (VLSC) frissíthető az értesítési kapcsolattartó és az online rendszergazda portálon való frissítésével. Az EA Portal frissítése körülbelül 24 órát vesz igénybe. A frissítést követően az új EA-rendszergazda aktiválási e-mailt fog kapni.

Ha nincs hozzáférése a VLSC portálhoz, vagy ha az elsődleges vállalti rendszergazda már nem tudja kezelni a regisztrációt és nincs hozzáférése az EA Portalhoz, kérvényezze a frissítést a [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) webhelyen az alábbi adatok megadásával:
- Regisztrációs szám
- A hozzáadni kívánt e-mail-cím és a hitelesítés típusa (munkahelyi/iskolai/MS)
- Az elsődleges EA-rendszergazda módosításának oka
- E-mailes jóváhagyás az elsődleges EA-rendszergazdától

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>A jelenlegi EA-rendszergazda már nem dolgozik a cégnél

Egy EA-regisztrációhoz több EA-rendszergazda is tartozhat, így egy másik EA-rendszergazdával is felveheti a kapcsolatot, hogy új EA-rendszergazdát, fióktulajdonost vagy részlegszintű rendszergazdát adjon hozzá. Ha azonban nem biztos abban, hogy ki az EA-rendszergazda, vagy nincs elérhető EA-rendszergazda a regisztrációban, vegye fel velünk a kapcsolatot a következő adatok megadásával:
- Regisztrációs szám
- A hozzáadni kívánt e-mail-cím és a hitelesítés típusa (munkahelyi/iskolai/MS)
- Információ megadása, miszerint a jelenlegi EA-rendszergazda már nem dolgozik a cégnél

Ne feledje, hogy ha a regisztrációhoz más EA-rendszergazdák is tartoznak, velük is fel fogjuk venni a kapcsolatot, hogy kérvényezzük a regisztrációt érintő adminisztratív jellegű változtatások jóváhagyását.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>A regisztráció állapota függőben van. Hogyan aktiválhatom a regisztrációt?

Ha az elsődleges EA-rendszergazda még nem lépett be a regisztrációba, akkor az „Függőben” állapotú lesz. Ha Ön az EA-rendszergazda, jelentkezzen be az Azure EA Portalra. Előfordulhat, hogy a regisztrációs számokat megjelenítő kezdőlapon nem találja a függőben levő regisztrációt. Törölje az EA Portal jobb felső sarkában található „aktív” jelölőnégyzetet. Ezután megjelenik a függőben levő regisztráció. Kattintson a regisztrációra az adatok eléréséhez, majd a regisztráció Kezelés lapjának elérése után az állapot „Függőben” értékről „Aktív” értékre módosul.

### <a name="why-is-my-account-stuck-in-pending-status"></a>Miért maradt a fiók „Függőben” állapotban?

Amikor először hozzáadják az új fióktulajdonosokat (AO) a regisztrációhoz, mindig „Függőben” állapot jelenik meg náluk. Az aktiválási üdvözlő e-mail fogadása után az AO bejelentkezhet a fiókja aktiválásához. A bejelentkezéssel a fiók állapota „Függőben” értékről „Aktív” értékre módosul.

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Hibaüzenet jelent meg az Azure EA Portalra való bejelentkezéskor

Az Azure EA Portalon a bejelentkezéskor megjelenő hibaüzenetnek több oka is lehet. Kövesse az alábbi hibaelhárítási lépéseket:

 1. A következő webhelyen győződjön meg róla, hogy az EA Portalra vonatkozó megfelelő URL-címet használja: [https://ea.azure.com](https://ea.azure.com).
 1. Állapítsa meg, hogy az Azure EA Portalhoz munkahelyi vagy iskolai fiókkal, illetve Microsoft Live ID-vel regisztrált. Ha a munkahelyi fiókját használja, adja meg munkahelyi e-mail-címét és jelszavát. Ha a Microsoft Live ID-t használja, adja meg a Live ID-hez tartozó e-mail-címét és jelszavát. Ha elfelejtette a Microsoft Live ID-hoz tartozó jelszavát, kérje annak visszaállítását a következő webhelyen: [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. Javasoljuk, hogy privát böngészőt használjon a bejelentkezéshez, hogy megakadályozza a korábbi vagy a jelenlegi munkamenetek cookie-jainak és gyorsítótárazott adatainak megtartását. Törölje a gyorsítótárat, és használjon privát vagy inkognitó módú ablakot a [https://ea.azure.com](https://ea.azure.com) webhely megnyitásához.
 1. A Microsoft-fiókja használatakor megjelenő Érvénytelen felhasználó hiba azért jelenhet meg, mert több Microsoft-fiókkal rendelkezik, és nem az az elsődleges alias, amelybe megpróbál bejelentkezni. Az elsődleges alias ellenőrzéséhez látogasson el az account.live.com webhelyre:
    - Nyissa meg a „Saját adatok” > „Bejelentkezési e-mail-cím vagy telefonszám kezelése” oldalt.
    - A képernyőn megjelenő útmutatást követve ellenőrizze a másodlagos e-mail-címet, és igényeljen egy kódot a bizalmas információk eléréséhez.
    - Adja meg a biztonsági kódot.
    - Ha később szeretné beállítani a kétlépéses hitelesítést, válassza a „Beállítás később” lehetőséget.
    - Ezután megjelenik a „Fiók aliasainak kezelése” oldal, ahol megtekintheti a fiókhoz tartozó aliasokat. Ellenőrizze, hogy az elsődleges aliast használja-e az Azure EA Portalra történő bejelentkezéshez. Ha nem, beállíthatja elsődleges aliasaként, vagy használhatja az elsődleges aliasát az EA Portalra való bejelentkezéshez.

Ha a fenti hibaelhárítási lépésekkel sem sikerült megoldani a problémát, küldjön be egy kérelmet a [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) webhelyen a következő információk megadásával:
- A használt böngészők és azok verziója.
- A hibaüzenet képernyőképe.
- A hibát jelző oldal URL-címe.  
- A hiba előfordulásának dátuma, időpontja és időzónája.
- Emellett segíthet, ha egy naplófájlt is beszerez. A következő lépéseket végrehajtva rögzítheti a hálózat nyomon követési adatait:
  1. Nyissa meg az Internet Explorert.
  1. Nyomja meg az F12 billentyűt, amely megnyit egy mezőt az IE alsó részén.
  1. Válassza a **Hálózat** lapot.
  1. Kattintson a **Rögzítés megkezdése** lehetőségre.
  1. Hajtsa végre a hibát okozó műveletet.
  1. A hiba megjelenése után kattintson a **Rögzítés leállítása** gombra.
  1. Mentse a fájlt, majd csatolja az adatokat a támogatási kéréshez.
  1. Győződjön meg arról, hogy a regisztrációja számát és az e-mail-címét is megadja a támogatási kérésben.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Mi a különbség a munkahelyi/iskolai fiók és a Microsoft-fiók között?

**Microsoft-fiók:** Olyan fiókok, amelyek a Live ID-hoz lettek társítva a következő webhelyen: [https://signup.live.com](https://signup.live.com).

**Munkahelyi/iskolai fiók:** Csak olyan szervezetek számára érhető el, amelyek felhőbeli összevonással telepítették az Active Directoryt, és amelyek esetében az összes fiók egyetlen bérlőn található. A felhasználók akkor adhatóak hozzá munkahelyi vagy iskolai hitelesítési módszerrel, ha a szervezet belső Active Directoryja a felhőben van összevonva.

  2016. szeptemberétől a Microsoft nem engedélyezi a munkahelyi vagy iskolai e-mail-címek Microsoft-fiókokként történő regisztrációját. További részletekért tekintse meg a következő anyagokat: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Ha a szervezet nincs összevonva a felhőben, akkor nem fogja tudni használni a munkahelyi vagy iskolai e-mail-címét. Ehelyett regisztráljon vagy hozzon létre egy új e-mail-címet, és regisztrálja Microsoft-fiókként.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Elfelejtettem az Azure EA Portalhoz tartozó jelszavam

Ha elfelejtette a Microsoft Live ID-hoz tartozó jelszavát, kérje annak visszaállítását a következő webhelyen: [https://account.live.com/password/reset](https://account.live.com/password/reset).

Ha elfelejtette a munkahelyi jelszavát, forduljon a vállalat rendszergazdájához.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Érvényes munkahelyi vagy iskolai fiókkal rendelkezem, de nem tudom hozzáadni az EA Portalon

Ha más bérlőhöz tartozó munkahelyi vagy iskolai fiókkal rendelkezik, módosítsa az engedélyezési szintet a regisztráció részletei oldalon „Munkahelyi vagy iskolai fiókok több bérlőn” értékre. Ezután hozzá fogja tudni adni a fiókot.

## <a name="next-steps"></a>További lépések

- Az Azure EA Portal rendszergazdáinak érdemes elolvasniuk [a nagyvállalati szerződéses Azure Portal adminisztrációjáról](ea-portal-administration.md) szóló cikket a gyakori adminisztratív feladatok megismeréséhez.
