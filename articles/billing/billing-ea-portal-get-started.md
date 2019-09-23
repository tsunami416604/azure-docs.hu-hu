---
title: Ismerkedés az Azure EA Portallal
description: Ez a cikk azt ismerteti, hogyan használják az Azure nagyvállalati szerződéssel rendelkező ügyfelek az Azure EA Portalt.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 1882b283f376a1bb8706132263c83e1a24ec0705
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70900930"
---
# <a name="get-started-with-the-azure-ea-portal"></a>Ismerkedés az Azure EA Portallal

Ez a cikk segítséget nyújt a közvetlen és közvetett Azure nagyvállalati szerződéssel rendelkező ügyfelek számára az [Azure EA Portal](https://ea.azure.com) használatának megkezdéséhez, az alábbi alapvető témakörök ismertetésével:

- Az Azure EA Portal felépítése.
- Az Azure EA Portalon használt szerepkörök.
- Az előfizetések létrehozásának alapjai.
- A költségek elemzése az Azure EA Portalon és az Azure Portalon.

Az alábbi videó az Azure EA Portal előkészítő lépéseit mutatja be:

[Az Azure EA Portal előkészítési videója](https://www.youtube.com/watch?v=OiZ1GdBpo-I)

>[!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="azure-ea-portal-hierarchy"></a>Az Azure Portal hierarchiája

Az Azure Portal hierarchiája a következőkből áll:

**Microsoft Azure EA Portal** – Az Azure EA Portal egy online felügyeleti portál, amely segít az Azure EA-szolgáltatások költségeinek kezelésében. Egy Azure EA-hierarchia létrehozására használhatja, beleértve a részlegeket, a fiókokat és az előfizetéseket is. Arra is használhatja, hogy összeegyeztesse az igénybe vett szolgáltatások költségeit, letöltse a használati jelentéseket és megtekintse az árlistákat. Emellett API-kulcsokat is létrehozhat a regisztrációjához.

**Részlegek** – A részlegek létrehozásával segítheti a költségek logikai csoportokra történő szegmentálását, majd megadhat egy költségkeretet vagy kvótát a részlegszinten.

**Fiókok** – A fiókok az Azure EA Portalon található szervezeti egységek, és az előfizetések kezelésére szolgálnak. A fiókokat jelentéskészítésre is használják.

**Előfizetések** – Az előfizetések az Azure EA Portalon található legkisebb egységek. A szolgáltatásadminisztrátorok által kezelt Azure-szolgáltatások tárolóiként szolgálnak.

Az alábbi ábra egyszerű Azure EA-hierarchiákat mutat be.

![Egyszerű Azure EA-hierarchiák ábrája](./media/billing-ea-portal-get-started/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az Azure-szolgáltatásoknak a regisztrációban történő felügyeletére négy különböző vállalati rendszergazdai felhasználói szerepkör jogosult:

- Vállalati rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos
- Szolgáltatás-rendszergazda

A szerepkörök két különböző Microsoft Azure-portálon végzett feladatok végrehajtásához használhatók. Az Azure EA Portal (https://ea.azure.com) a számlázás és a költségek kezelésére szolgál. Az Azure Portal (https://portal.azure.com) az Azure-szolgáltatások kezelésére szolgál.

A felhasználói szerepkörök felhasználói fiókhoz vannak rendelve. A felhasználók hitelességének ellenőrzéséhez minden felhasználónak érvényes munkahelyi, iskolai vagy Microsoft-fiókkal kell rendelkeznie. Győződjön meg arról, hogy mindegyik fiókhoz olyan e-mail-cím tartozik, amely rendszeresen ellenőrizve van. A fiókértesítéseket az e-mail-címekre küldi a rendszer.

A felhasználók beállításakor több munkahelyi, iskolai vagy Microsoft-fiókot is hozzárendelhet a vállalati rendszergazdai szerepkörhöz. Azonban csak egy munkahelyi, iskolai vagy Microsoft-fiókot rendelhet hozzá a fióktulajdonosi szerepkörhöz. Emellett egyetlen munkahelyi, iskolai vagy Microsoft-fiók rendelkezhet a vállalati rendszergazdai és a fióktulajdonosi szerepkörrel egyszerre.

### <a name="enterprise-administrator"></a>Vállalati rendszergazda

A vállalati rendszergazda rendelkezik a legmagasabb szintű hozzáféréssel. A szerepkörrel rendelkező felhasználók képesek:

- A fiókok és fióktulajdonosok kezelésére
- Más vállalati rendszergazdák kezelésére
- Részlegszintű rendszergazdák kezelésére
- Értesítendő felek kezelésére
- A fiókok használati adatainak megtekintésére
- A fiókok nem számlázott díjainak megtekintésére

Több vállalati rendszergazda is lehet egy vállalati regisztrációban. A vállalati rendszergazdákhoz rendelhet csak olvasási hozzáférést is. Mindegyikük örökli a részlegszintű rendszergazdai szerepkört.

### <a name="department-administrator"></a>Részlegszintű rendszergazda

A szerepkörrel rendelkező felhasználók képesek:

- Részlegek létrehozására és kezelésére
- Új fióktulajdonosok létrehozására
- Az általuk kezelt részlegek használati adatainak megtekintésére
- A szükséges engedélyek birtokában a költségek megtekintésére

Több részlegszintű rendszergazdája is lehet az egyes vállalati regisztrációkban.

A részlegszintű rendszergazdákhoz rendelhet csak olvasási hozzáférést is. Ha csak olvasási hozzáférést szeretne biztosítani, módosítson vagy hozzon létre egy új részlegszintű rendszergazdát, és állítsa a csak olvasási jogosultság beállítást **Igen** értékre.

### <a name="account-owner"></a>Fióktulajdonos

A szerepkörrel rendelkező felhasználók képesek:

- Előfizetések létrehozására és kezelésére
- Szolgáltatásadminisztrátorok kezelésére
- Előfizetések használati adatainak megtekintésére

Mindegyik fiókhoz szükség van egyedi munkahelyi, iskolai vagy Microsoft-fiókra. Az Azure EA Portal rendszergazdai szerepköreivel kapcsolatos további információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md).

### <a name="service-administrator"></a>Szolgáltatás-rendszergazda

A szolgáltatásadminisztrátor jogosult a szolgáltatások kezelésre az Azure Portalon, és a társadminisztrátor szerepkör felhasználókhoz való hozzárendelésére.

## <a name="activate-your-enrollment"></a>Regisztráció aktiválása

A szolgáltatás aktiválásához a kezdeti vállalati rendszergazda megnyitja az Azure EA Portalt ([https://ea.azure.com](https://ea.azure.com)), és bejelentkezik a meghívó e-mail e-mail-címével.

Ha több regisztrációval rendelkezik, válasszon ki egyet az aktiváláshoz. Alapértelmezés szerint csak az aktív regisztrációk jelennek meg. A regisztrációs előzmények megtekintéséhez törölje az **Aktiválás** beállítás jelölőnégyzetét az Azure EA Portal jobb felső sarkában.

A Regisztráció alatt az állapot **Aktív**.

![Aktív regisztrációt bemutató példa](./media/billing-ea-portal-get-started/ea-enrollment-status.png)

Csak meglévő Azure vállalati rendszergazdák hozhatnak létre más vállalati rendszergazdákat.

### <a name="create-another-enterprise-admin"></a>Másik vállalati rendszergazda létrehozása

- Jelentkezzen be az [Azure EA Portalra](https://ea.azure.com), és lépjen a **Kezelés** > **Regisztráció részletei** területre, majd kattintson az **+ Adminisztrátor hozzáadása** elemre az oldal jobb felső sarkában.

Győződjön meg arról, hogy tudja a felhasználó e-mail-címeit és előnyben részesített hitelesítési módszerét, úgymint munkahelyi, iskolai vagy Microsoft-fiók használatával végzett hitelesítés. Ezekre az információkra szükség van a felhasználó hozzáadásához.

Ha nem Ön a vállalati rendszergazda, forduljon egy vállalati rendszergazdához, és kérje meg, hogy adja hozzá Önt egy regisztrációhoz. Miután hozzá lett adva egy regisztrációhoz, egy aktiválási e-mailt fog kapni.

Ha a vállalati rendszergazda nem tud segíteni Önnek, hozzon létre egy [támogatási kérést az Azure EA Portalon](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Adja meg az alábbi információkat:

- Regisztrációs szám
- Hozzáadandó e-mail-cím és hitelesítési típus (munkahelyi, iskolai vagy Microsoft-fiók)
- E-mailes jóváhagyás egy meglévő vállalati rendszergazdától
  - Ha a meglévő vállalati rendszergazda nem érhető el, forduljon a partneréhez vagy a szoftvertanácsadójához, és kérje meg, hogy a VLSC eszközzel keresztül módosítsa a kapcsolattartási adatokat.

A vállalati rendszergazdai szerepkörrel kapcsolatos további információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](billing-understand-ea-roles.md).

## <a name="create-an-azure-ea-department"></a>Azure EA-részleg létrehozása

A vállalati rendszergazdák és a rendszerszintű rendszergazdák a részlegek segítségével rendszerezik a vállalati Azure-szolgáltatásokat és a használati adatokat, és készítenek róluk jelentéseket részleg és költséghely szerint. A vállalati rendszergazdák képesek:

- Részlegek felvételére vagy eltávolítására
- Fiókok részlegekhez történő hozzárendelésére
- Részlegszintű rendszergazdák létrehozására
- Díjak és költségek megtekintésének engedélyezésére a részlegszintű rendszergazdák számára

A részlegszintű rendszergazdák új fiókokat adhatnak a részlegeikhez. Eltávolíthatnak fiókokat a részlegekből, de a regisztrációból nem.

Részleg hozzáadása:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Részleg** fülre, majd a **+ Részleg hozzáadása** elemre, és adja meg a kért adatokat.
3. A részleg neve az egyetlen kötelezően kitöltendő mező. A névnek legalább három karakterből kell állnia.
4. Amikor végzett, kattintson a **Hozzáadás** gombra.

## <a name="add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

A részleg létrehozása után az Azure vállalati rendszergazda részlegszintű rendszergazdákat adhat hozzá, és mindegyiket hozzárendelheti egy részleghez. A részlegszintű rendszergazdák képesek:

- Más részlegszintű rendszergazdák létrehozására
- Olyan részlegtulajdonságok megtekintésére és módosítására, mint a név vagy a költségközpont
- Fiók hozzáadására a részlegeikhez
- Fiókok eltávolítására a részlegeikből
- A részlegeik használati adatainak letöltésére
- A részleg havi használati adatainak és díjainak megtekintésére, ha egy vállalati rendszergazda engedélyt adott nekik <sup>1</sup>

### <a name="to-add-a-department-admin"></a>Részlegszintű rendszergazda hozzáadása

Vállalati rendszergazdaként:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Részleg** fülre, majd az adott részlegre.
3. Kattintson az **+ Adminisztrátor hozzáadása** elemre, és adja meg a kért adatokat.
4. Csak olvasási hozzáféréshez állítsa a **Csak olvasás** beállítást **Igen** értékre, majd kattintson a **Hozzáadás** elemre.

![A részlegszintű rendszergazda hozzáadására szolgáló párbeszédablakot bemutató példa](./media/billing-ea-portal-get-started/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Csak olvasási hozzáférés beállítása

A részlegszintű rendszergazdáknak adhat csak olvasási hozzáférést a. Új részlegszintű rendszergazda létrehozásakor:

- Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre.

Meglévő részlegszintű rendszergazda szerkesztéséhez:

1. Válassza ki a részleget, majd kattintson a ceruza szimbólumra a **Részlegszintű rendszergazda** elem mellett, amelyet szerkeszteni kíván.
2. Állítsa a csak olvasási hozzáférési beállítást **Igen** értékre. Ezután kattintson a **Mentés** gombra.

A vállalati rendszergazdai szerepkörrel rendelkező felhasználók automatikusan megkapják a részlegszintű rendszergazda engedélyeit.

<sup>1</sup> Ha engedélyt kapott a részleg havi használati adatainak és díjainak megtekintésére, de nem látja őket, forduljon a partneréhez.

## <a name="add-an-account"></a>Fiók hozzáadása

A fiók és az előfizetés szerkezete befolyásolja azok felügyeletét és azt, hogy azok hogyan jelennek meg a számlákon és a jelentéseken. Jellemző szervezeti szerkezet például a vállalati részlegek, a funkció szerinti csapatok és a földrajzi helyszínek alapján történő bontás.

Fiók hozzáadásához:

1. Az Azure EA Portalon kattintson a **Kezelés** elemre a bal oldali navigációs területen.
2. Kattintson a **Fiók** fülre, majd a **Fiók** oldalon kattintson a **+Fiók hozzáadása** elemre.
3. Válasszon ki egy részleget, vagy hagyja hozzárendelés nélkül, majd válassza ki a kívánt hitelesítési típust.
4. Adjon meg egy felhasználóbarát nevet a fiók jelentéskor történő beazonosításához.
5. Adja meg a **Fióktulajdonos e-mail-címét** az új fiókhoz történő hozzárendeléshez.
6. Erősítse meg az e-mail-címet, majd kattintson a **Hozzáadás** elemre.

![A fiókok listáját bemutató példa és a + Fiók hozzáadása lehetőség](./media/billing-ea-portal-get-started/create-ea-add-an-account.png)

Hozzáadhat egy másik fiókot a **Másik fiók hozzáadása** elemre, vagy a bal oldali eszköztár jobb alsó sarkában lévő **Hozzáadás** gombra kattintva.

A fióktulajdonjog megerősítéséhez:

1. Jelentkezzen be az Azure EA Portalra.
2. Ellenőrizze a fióktulajdonjogot az állapot megtekintésével. Az állapotnak **Függőben** állapotról **Kezdő és záró dátum** állapotra kell átváltania. A Kezdő és záró dátum a felhasználó első bejelentkezésének és a szerződés végének a dátuma.


## <a name="change-account-owner"></a>Fiók tulajdonosának módosítása

A vállalati rendszergazdák az Azure EA Portal használatával átruházhatják az előfizetői fiókok tulajdonjogát a regisztrációkban. A művelet áthelyezi az összes előfizetést a forrásként megadott felhasználói fiókból egy célként megadott felhasználói fiókba.

Fontos tudnivalók a felhasználói fiókok adatainak átviteléről:

- Az egyik munkahelyi vagy iskolai fiókról másik munkahelyi vagy iskolai fiókra történő átvitel támogatott.
- A Microsoft-fiókról munkahelyi vagy iskolai fiókra történő átvitel támogatott.
- A munkahelyi vagy iskolai fiókról Microsoft-fiókra történő átvitel nem támogatott.
- Az egyik Microsoft-fiókról a másik Microsoft-fiókra történő átvitel támogatott. A célfióknak érvényes Azure Commerce-fióknak kell lennie, hogy érvényes átviteli cél lehessen. Új fiókok esetén új Azure Commerce-fiókot kell létrehoznia az Azure EA Portalra való bejelentkezéskor. Meglévő fiókok esetén először új Azure-előfizetést kell létrehoznia ahhoz, hogy a fiók jogosulttá váljon.
- Az előfizetés-átvitel befejezésekor a Microsoft frissíti a fióktulajdonost.

RBAC-szabályzatok:

- Csak az ugyanazon bérlő két szervezeti azonosítója közötti Azure-előfizetés-átvitelek őrzik meg a meglévő Azure szerepköralapú hozzáférés-vezérlési (RBAC) szabályzatokat, a szolgáltatásadminisztrátori szerepkör-hozzárendeléseket és a társadminisztrátori szerepkör-hozzárendeléseket. Más előfizetés-átvitelek az RBAC-szabályzatok és a szolgáltatásadminisztrátori szerepkör-hozzárendelések elvesztését eredményezhetik. A szabályzatok és a rendszergazdai szerepkörök nem vihetők át különböző címtárakba. A szolgáltatásadminisztrátorokból a célfiók tulajdonosa lesz.
- Ha ugyanazon bérlő két szervezeti azonosítója közötti Azure-előfizetés-átvitelt hajt végre, az RBAC-szabályzatok és a meglévő szolgáltatásadminisztrátori és társadminisztrátori szerepkörök megmaradnak.

A fióktulajdonos módosítása előtt:

1. Tekintse meg a **Fiók** lapot, és határozza meg a forrásfiókot. A forrásfióknak aktívnak kell lennie.
2. Azonosítsa be a célfiókot. Aktívnak kell lennie.

A fiók tulajdonjogának átvitele az összes előfizetésre vonatkozóan:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Fiók** fülre, és vigye a mutatót a fiók fölé.
3. Kattintson a fióktulajdonos módosításának szimbólumára a jobb oldalon. A szimbólum egy személyt ábrázol.
4. Válasszon ki egy jogosult fiókot, majd kattintson a **Következő** gombra.
5. Erősítse meg az átvitelt, majd kattintson a **Küldés** gombra.

![A Fióktulajdonos módosítása szimbólumot bemutató kép](./media/billing-ea-portal-get-started/create-ea-create-sub-transfer-account-ownership-of-sub.png)

A fiók tulajdonjogának átvitele egy előfizetésre vonatkozóan:

1. A bal oldali navigációs területen kattintson a **Kezelés** elemre.
2. Kattintson a **Fiók** fülre, és vigye a mutatót a fiók fölé.
3. Kattintson az előfizetések átvitelének szimbólumára a jobb oldalon. A szimbólum egy lapot ábrázol.
4. Válasszon ki egy jogosult előfizetést, majd kattintson a **Következő** gombra.
5. Erősítse meg az átvitelt, majd kattintson a **Küldés** gombra.

![Az Előfizetések átvitele szimbólumot bemutató kép](./media/billing-ea-portal-get-started/ea-transfer-subscriptions.png)

Az alábbi videó az Azure EA Portal felhasználókezelését mutatja be:

[Videó az Azure EA Portal felhasználókezeléséről](https://www.youtube.com/watch?v=621jVkvmwm8)

>[!VIDEO https://www.youtube.com/embed/621jVkvmwm8]

## <a name="create-a-subscription"></a>Előfizetés létrehozása

A fióktulajdonosok megtekinthetik és kezelhetik az előfizetéseket. Az előfizetésekkel hozzáférést adhat a szervezeti csoportoknak a fejlesztési környezetekhez és a projektekhez. Például a teszteléshez, az éles környezethez, a fejlesztéshez és az előkészítéshez. Amikor különböző előfizetéseket hoz létre az egyes alkalmazáskörnyezetekhez, elősegíti a környezetek védelmét. Mindegyik előfizetéshez különböző szolgáltatásadminisztrátori fiókot rendelhet. Az előfizetéseket korlátlan számú szolgáltatáshoz hozzárendelheti. A fiók tulajdonosa hoz létre előfizetéseket, és rendel hozzá egy szolgáltatásadminisztrátori fiókot a fiókjában lévő összes előfizetéshez.

### <a name="add-a-subscription"></a>Előfizetés hozzáadása

Az alábbiak alapján adhat hozzá előfizetést.

Amikor első alkalommal ad előfizetést a fiókjához, el kell fogadnia a MOSA-szerződést és egy díjkonstrukciót. Bár a Nagyvállalati Szerződéssel rendelkező ügyfelekre ezek nem vonatkoznak, az előfizetés létrehozásához szükségesek. A Microsoft Azure Nagyvállalati Szerződés regisztrációs kiegészítése felülírja a fenti elemeket, így az Ön szerződéses jogviszonya nem módosul. Amikor a rendszer kéri, jelölje be a feltételek elfogadására szolgáló jelölőnégyzetet.

Minden új előfizetés a _Microsoft Azure Enterprise_ alapértelmezett névvel jön létre. Módosíthatja az előfizetés nevét, hogy megkülönböztesse a regisztráció többi előfizetésétől, és azért, hogy a vállalati szintű jelentésekben felismerhető legyen.

Előfizetés hozzáadásához:

1. Az Azure EA Portalon jelentkezzen be a fiókjába.
2. Kattintson a **Rendszergazda** fülre, majd az **Előfizetés** elemre az oldal tetején.
2. Ellenőrizze, hogy a fiók tulajdonosaként jelentkezett-e be.
3. Kattintson az **+Előfizetés hozzáadása**, majd a **Vásárlás** elemre.
  Amikor első alkalommal ad előfizetést a fiókjához, meg kell adnia a kapcsolattartási adatait. További előfizetések hozzáadásakor a rendszer hozzáadja a megadott kapcsolattartási adatait.
4. Kattintson az **Előfizetések** elemre, majd válassza ki a létrehozott előfizetést, és kattintson az **Előfizetés adatainak szerkesztése** elemre.
5. Frissítse az **Előfizetés neve** és a **Szolgáltatásadminisztrátor** adatokat, majd jelölje be a jelölőnégyzetet.
  Az előfizetés neve megjelenik a jelentéseken, és a fejlesztési portálon az előfizetéshez rendelt projekt nevét is ez adja.

Akár 24 órába is telhet, amíg az új előfizetések megjelennek az előfizetések listáján. Az előfizetés létrehozása után elvégezheti az alábbiakat:

- [Az előfizetés adatainak szerkesztése](https://account.azure.com/Subscriptions)
- [Az előfizetési szolgáltatások kezelése](https://portal.azure.com/#home)

## <a name="transfer-pay-as-you-go-subscription-to-ea-subscription"></a>Használatalapú fizetéses előfizetés átvitele EA-előfizetésbe

Használatalapú fizetéssel rendelkező önálló előfizetés EA-előfizetésbe történő átviteléhez új támogatási kérést kell létrehoznia az Azure Portalon. Támogatási kérés létrehozásához kattintson az **+ Új támogatási kérés** elemre a Súgó és támogatás területen.


## <a name="view-usage-summary-and-download-reports"></a>Használati adatok összegzésének megtekintése és jelentések letöltése

A vállalati rendszergazdák az Azure EA Portalon megtekinthetik a használati adataik összegzését, a felhasznált pénzügyi keretet, valamint a többlethasználattal kapcsolatos díjakat. A díjak minden fióknál és előfizetésnél az összegzés szintjén jelennek meg.

Az egyes fiókok részletes használati adatainak megtekintéséhez

Töltse le a használati adatokról szóló jelentést. Kattintson a **Jelentések** elemre, majd a **Használati adatok letöltése** lapra. A jelentések listáján kattintson a **Letöltés** elemre annál a havi jelentésnél, amelyet le szeretne tölteni.

A jelentés nem tartalmazza a vonatkozó adókat. A használat felmerülésének időpontja és a jelentésben való megjelenése között akár nyolc órás késés is lehet.

A használati adatokat összegző jelentések és ábrák megtekintéséhez:

1. Az Azure EA Portal bal oldali navigációs területén kattintson a **Jelentések** elemre, és tekintse meg a **Használati adatok összegzése** lapot.  
  ![](./media/billing-ea-portal-get-started/create-ea-view usage-summary-and-download-reports.png)
2. Válasszon ki egy pénzügyikeret-időtartamot.
3. Váltson az **M** (Havi) és a **C** (Egyéni) módok között az oldal jobb felső részén, hogy egyéni kezdő- és záró dátummal is megtekinthesse a **használati adatok összegzését**.  
  ![](./media/billing-ea-portal-get-started/create-ea-view-usage-summary-and-download-reports-custom-view.png)
4. Jelöljön ki egy időtartamot vagy egy hónapot a grafikonon további részletek megtekintéséhez.
5. A grafikon a havi használatot mutatja a használati adatok, a szolgáltatások túlhasználata, a külön számlázott díjak és a Marketplace-költségek megjelenítésével.
6. A kiválasztott hónapra vonatkozóan a grafikon alatt részlegek, fiókok és előfizetések alapján szűrheti az adatokat.
7. Válthat a díjak **szolgáltatások** és **hierarchia** szerinti megjelenítése között.
8. A részletek megtekintéséhez kibonthatja vagy összecsukhatja az **Azure-szolgáltatások**, a **Külön számlázott díjak** és az **Azure Marketplace** területeket.

Az alábbi videó a használati adatok megtekintésének módját mutatja be:

[Videó az Azure EA Portal használati adatinak megtekintéséről](https://www.youtube.com/watch?v=Cv2IZ9QCn9E)

>[!VIDEO https://www.youtube.com/embed/Cv2IZ9QCn9E]

### <a name="download-csv-reports"></a>CSV-jelentések letöltése

A vállalati rendszergazdák a Havi jelentés letöltése lapot használhatják több jelentés CSV-fájlként való letöltéséhez. Ezek például az alábbi jelentések lehetnek:

- Egyenleg és díjak
- Használati adatok
- A Marketplace díjai
- Árlista

A jelentések letöltéséhez:


1. Az Azure EA Portalon kattintson a **Jelentések** elemre.
2. Kattintson a lap tetején található **Használati adatok letöltése** elemre.
3. Válassza a **Letöltés** elemet a havi jelentés mellett.

A használat felmerülésének időpontja és a jelentésben való megjelenése között akár öt nap késés is lehet.

A CSV-fájlokat a Safari böngészőből az Excelbe letöltő felhasználók formázási hibákat tapasztalhatnak. A hibák elkerülése érdekében szövegszerkesztővel nyissa meg a fájlt.

![A Használati adatok letöltése oldalt bemutató példa](./media/billing-ea-portal-get-started/create-ea-download-csv-reports.png)

Az alábbi videó a használati adatok letöltésének módját mutatja be:

[Videó az Azure EA Portal használati adatinak megtekintéséről](https://www.youtube.com/watch?v=eY797htT1qg)

>[!VIDEO https://www.youtube.com/embed/eY797htT1qg]

## <a name="schedule-an-onboarding-call"></a>Előkészítő hívás ütemezése

Ha egy személyes ügyfél-előkészítési munkamenetet szeretne beütemezni, hozzon létre egy támogatási kérést az [Azure EA Portal támogatási oldalán](https://support.microsoft.com/supportrequestform/e114582c-4e51-af46-10b1-1f0cc141e133). Válassza ki az **Előkészítés** lehetőséget **problémakategóriaként**.

## <a name="next-steps"></a>További lépések
- Az Azure EA Portal rendszergazdáinak érdemes elolvasniuk [a nagyvállalati szerződéses Azure Portal adminisztrációjáról](billing-ea-portal-administration.md) szóló cikket a gyakori adminisztratív feladatok megismeréséhez.
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](billing-ea-portal-troubleshoot.md).
