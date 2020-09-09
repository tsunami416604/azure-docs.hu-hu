---
title: A nagyvállalati szerződéses Azure Portal adminisztrációja
description: Ez a cikk a rendszergazdák Azure EA Portalon elvégzendő gyakori feladatait ismerteti.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 27073cb7cbd78183e519290c676c2499700b0cca
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442547"
---
# <a name="azure-ea-portal-administration"></a>A nagyvállalati szerződéses Azure Portal adminisztrációja

Ez a cikk a rendszergazdák Azure EA Portalon (https://ea.azure.com) ) elvégzendő gyakori feladatait ismerteti. Az Azure EA Portal egy online felügyeleti portál, amely segítséget nyújt az ügyfeleknek az Azure EA-szolgáltatások költségének kezelésében. Az Azure EA Portallal kapcsolatos bevezető információkért lásd: [Ismerkedés az Azure EA Portallal](ea-portal-get-started.md).

## <a name="associate-an-account-to-a-department"></a>Fiókok részlegekhez történő hozzárendelésére

A vállalati rendszergazdák a részlegekhez társíthatják a regisztrációban meglévő fiókokat.

### <a name="to-associate-an-account-to-a-department"></a>Fiókok részlegekhez történő hozzárendelése

1. Jelentkezzen be az Azure EA Portalra vállalati rendszergazdaként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Részleg** lehetőséget.
1. Vigye az egérmutatót a fiókot tartalmazó sor fölé, és válassza a jobbra található ceruza ikont.
1. Válassza ki a részleget a legördülő menüből.
1. Kattintson a **Mentés** gombra.

## <a name="department-spending-quotas"></a>Részleg költségkvótái

Az EA-ügyfelek megadhatják vagy módosíthatják a részlegek költségkvótáit a regisztráción belül. A költségkvóta összege az aktuális előrefizetési időszakra állítható be. Az aktuális előrefizetési időszak végén a rendszer meghosszabbítja a meglévő költségkvótát a következő előrefizetési időszakra, kivéve, ha az értékek frissülnek.

A részlegszintű rendszergazda megtekintheti a költségkvótát, de csak a vállalati rendszergazda frissítheti a kvóta összegét. A vállalati rendszergazda és a részlegszintű rendszergazda értesítést kap a kvóta 50, 75, 90 és 100 százalékának elérésekor.

### <a name="enterprise-administrator-to-set-the-quota"></a>A vállalati rendszergazda a következőképp állíthatja be a kvótát:

 1. Nyissa meg az Azure EA Portalt.
 1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
 1. Válassza a **Részleg** lapot.
 1. Válassza ki a részleget.
 1. Válassza a ceruza szimbólumot a Részleg adatai részben, vagy válassza a **+ Részleg hozzáadása** lehetőséget, ha egy új részleggel együtt szeretne költségkvótát hozzáadni.
 1. A Részleg adatai területen adja meg a költségkvóta összegét a regisztráció pénznemében a Költségkvóta $ mezőben (0-nál nagyobb értéket kell megadni).
    - Jelenleg a Részleg neve és a Költséghely is szerkeszthető.
 1. Kattintson a **Mentés** gombra.

A részleg költségkvótája mostantól megjelenik a Részleg fül részlegek listáját tartalmazó nézetében. A jelenlegi előrefizetési időszak végén az Azure EA Portal fenntartja a költségkvótákat a következő előrefizetési időszakra.

A részlegkvóta összege független az aktuális Azure-előrefizetéstől, és a kvóta összege és a riasztások csak belső használatra szolgálnak. A részleg költségkvótái csak tájékoztató jellegűek, és nem kényszerítenek a költségkeretek betartására.

### <a name="department-administrator-to-view-the-quota"></a>A részlegszintű rendszergazda így tekintheti meg a kvótát:

1. Nyissa meg az Azure EA Portalt.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Részleg** lapot, és tekintse meg a részlegek listáját tartalmazó nézetet a költségkvótákkal.

Ha Ön közvetett ügyfél, a költségfunkciókat a csatornapartnerének kell engedélyeznie.

## <a name="enterprise-user-roles"></a>Vállalati felhasználók szerepkörei

Az Azure EA Portal segítséget nyújt az Azure EA-költségek és -használat felügyeletéhez. Az Azure EA Portalon három fő szerepkör van:

- EA-rendszergazda
- Részlegszintű rendszergazda
- Fióktulajdonos

Minden szerepkör különböző szintű hozzáféréssel és jogosultsággal rendelkezik.

A felhasználói szerepkörökkel kapcsolatos további információkért lásd: [Vállalati felhasználók szerepkörei](https://docs.microsoft.com/azure/billing/billing-ea-portal-get-started#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Azure EA-fiók hozzáadása

Az Azure EA-fiók az Azure EA Portal szervezeti egysége. Ez a fiók az előfizetések felügyeletére, valamint jelentéskészítésre használatos. Az Azure-szolgáltatások eléréséhez és használatához létre kell hoznia vagy hozatnia egy fiókot.

Az Azure-fiókokkal kapcsolatos további információkért lásd a [fiók hozzáadását](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-get-started#add-an-account) ismertető szakaszt.

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test ajánlat

Az Azure vállalati rendszergazdájaként engedélyezheti a szervezeten belüli fióktulajdonosok számára, hogy előfizetéseket hozzanak létre az EA Dev/Test ajánlat alapján. Ezt a fióktulajdonos Dev/Test jelölőnégyzetének bejelölésével teheti meg az Azure EA Portalon.

Ha bejelölte a Dev/Test jelölőnégyzetet, tudassa a fióktulajdonossal, hogy be tudja állítani a Dev/Test-előfizetőkből álló csapata számára szükséges EA Dev/Test-előfizetéseket.

Ez lehetővé teszi, hogy az aktív Visual Studio-előfizetők speciális fejlesztési és tesztelési díjszabás mellett futtassanak fejlesztési és tesztelési számítási feladatokat az Azure-ban. Hozzáférést biztosít a Dev/Test-képek teljes galériájához, beleértve a Windows 8.1 és a Windows 10 rendszert.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Az Enterprise Dev/Test ajánlat beállítása:

1. Jelentkezzen be vállalati rendszergazdaként.
1. Válassza a **Kezelés** elemet a bal oldali navigációs sávon.
1. Válassza a **Fiók** fület.
1. Válassza annak a fióknak a sorát, amelyhez engedélyezni szeretné a Dev/Test-hozzáférést.
1. Válassza a sortól jobbra található ceruza szimbólumot.
1. Jelölje be a Dev/Test jelölőnégyzetet.
1. Kattintson a **Mentés** gombra.

Ha egy felhasználót fióktulajdonosként adnak hozzá az Azure EA Portalon keresztül, akkor a fióktulajdonoshoz társított Azure-előfizetések, amelyek a használatalapú Dev/Test ajánlaton vagy a Visual Studio-előfizetőknek szóló havi kreditajánlatokon alapulnak, az EA Dev/Test ajánlatra lesznek konvertálva. A fióktulajdonoshoz társított egyéb ajánlattípusokon (például használatalapú ajánlattípusokon) alapuló előfizetések Microsoft Azure Enterprise-ajánlatokra lesznek konvertálva.

A Dev/Test ajánlat jelenleg nem érhető el az Azure Government-ügyfelek számára.

## <a name="delete-subscription"></a>Előfizetés törlése

Egy olyan előfizetés törlése, amely esetében Ön a fióktulajdonos:

1. Jelentkezzen be az Azure Portalra a fiókjához tartozó hitelesítő adatokkal.
1. A központi menüben válassza az **Előfizetések** elemet.
1. Az előfizetések lapján, az oldal bal felső sarkában válassza ki azt az előfizetést, amelyet le szeretne mondani, és válassza az **Előfizetés megszüntetése** lehetőséget a megszüntetési lap megnyitásához.
1. Írja be az előfizetés nevét, válassza ki a megszüntetés okát, majd válassza az **Előfizetés megszüntetése** lehetőséget.

Csak a fiókadminisztrátorok szüntethetik meg az előfizetéseket.

További információ: [Mi történik az előfizetés lemondása után?](cancel-azure-subscription.md#what-happens-after-i-cancel-my-subscription)

## <a name="delete-an-account"></a>Fiók eltávolítása

A fiókeltávolítást csak aktív előfizetésekkel nem rendelkező aktív fiókok esetében lehet elvégezni.

1. Az Enterprise Portalon válassza a **Kezelés** lehetőséget a bal oldali navigációs sávon.
1. Válassza a **Fiók** fület.
1. A fiókok táblázatából válassza ki a törölni kívánt fiókot.
1. Válassza a fiók sorának jobb oldalán található X szimbólumot.
1. Ha a fiók nem rendelkezik aktív előfizetéssel, válassza a fiók sora alatti **Igen** lehetőséget a fiók eltávolításának megerősítéséhez.

## <a name="update-notification-settings"></a>Frissítési értesítés beállításai

A rendszer automatikusan regisztrálja a vállalati rendszergazdákat, hogy megkapják a regisztrációjukkal kapcsolatos használati értesítéseket. A vállalati rendszergazdák módosíthatják az értesítések időközét, vagy teljesen ki is kapcsolhatják őket.

Az értesítési kapcsolattartók az Azure EA Portal **Értesítési kapcsolattartó** területén jelennek meg. Az értesítési kapcsolattartók kezelésével gondoskodhat arról, hogy a szervezet megfelelő tagjai kapják meg az Azure EA-értesítéseket.

Az aktuális értesítési beállítások megtekintése:

1. Az Azure EA Portalon lépjen a **Kezelés** > **Értesítési kapcsolattartó** elemre.
2. E-mail-cím – A vállalati rendszergazda Microsoft-fiókjához, illetve munkahelyi vagy iskolai fiókjához társított e-mail-cím, amelyre a rendszer az értesítéseket küldi.
3. Számlázatlan egyenlegről szóló értesítés gyakorisága – Az egyes vállalati rendszergazdáknak küldött értesítésekhez beállított gyakoriság.

Kapcsolattartó hozzáadása:

1. Válassza a **+Kapcsolattartó hozzáadása** lehetőséget.
2. Írja be az e-mail-címet, majd erősítse meg.
3. Kattintson a **Mentés** gombra.

Az új értesítési kapcsolattartó megjelenik az **Értesítendő fél** területen. Az értesítés gyakoriságának módosításához válassza ki az értesítendő felet, majd válassza a kiválasztott sor jobb oldalán található ceruza szimbólumot. Állítsa be a gyakorisághoz a **naponta**, **hetente**, **havonta** vagy a **nincs** értéket.

Figyelmen kívül hagyhatja a _közelgő fedezeti időszak záró dátumát_, valamint _letilthatja és megszüntetheti a közelgő dátummal_ kapcsolatos életciklus-értesítéseket. Az életciklus-értesítések letiltásával figyelmen kívül hagyja a fedezeti időszakról és a szerződés záró dátumáról szóló értesítéseket.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-ajánlat

Az Azure Sponsorship-ajánlat korlátozott számú szponzorált Microsoft Azure-fiókot takar. A Microsoft által kiválasztott, korlátozott számú ügyfél az e-mailben kapott meghívóval csatlakozhat hozzá. Ha Ön jogosult a Microsoft Azure Sponsorship-ajánlatára, akkor kapni fog egy e-mailes meghívót a fiókazonosítójára.

További információért hozzon létre egy [támogatási kérést a szponzorálás aktiválásához](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Váltás munkahelyi vagy iskolai fiókkal történő hitelesítésre

Az Azure Enterprise-felhasználók Microsoft-fiókról (MSA vagy Live ID), az Azure-ban Active Directoryt használó munkahelyi vagy iskolai fiókkal történő hitelesítési típusra válthatnak.

A kezdéshez:

1. Adja hozzá a munkahelyi vagy iskolai fiókot az Azure EA Portalhoz a szükséges szerepkörrel/szerepkörökkel.
1. Ha hibaüzenet jelenik meg, előfordulhat, hogy a fiók nem érvényes az Active Directoryban.  Az Azure egyszerű felhasználónevet (UPN) használ, amely nem mindig egyezik meg az e-mail-címmel.
1. Az Azure EA Portalon történő hitelesítéshez a munkahelyi vagy iskolai fiókot használhatja.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Microsoft-fiókokból származó előfizetések átváltása munkahelyi vagy iskolai fiókokra:

1. Jelentkezzen be a felügyeleti portálra azzal a Microsoft-fiókkal, amely az előfizetések tulajdonosa.
1. Az új fiókra való váltáshoz végezze el a fiók tulajdonjogának átruházását.
1. A Microsoft-fiók most már nem rendelkezik aktív előfizetésekkel, és törölhető.
1. A törölt fiókok korábbi számlázási okokból inaktív állapotban megtekinthetőek maradnak a portálon.  Kiszűrheti őket a nézetből, ha bejelöli a kizárólag aktív fiókok megjelenítésére vonatkozó jelölőnégyzetet.

## <a name="account-subscription-ownership-faq"></a>Gyakori kérdések a fiókhoz tartozó előfizetés tulajdonjogával kapcsolatban

Ez a dokumentum a fiókhoz tartozó előfizetés tulajdonjogával kapcsolatos gyakori kérdésekre ad választ.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hány Azure-fióktulajdonos lehet előfizetésenként?

Előfizetésenként csak egy fióktulajdonos engedélyezett.  További szerepkörök az [Azure Portal](https://portal.azure.com) bal felső sarkában található előfizetési fülön adhatók hozzá a szerepköralapú hozzáférés vagy a Hozzáférés-vezérlés (IAM) használatával.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Szerepelhet egy Azure-fiók tulajdonosa több részlegen is?

Nem, egy fióktulajdonos csak egyetlen részleghez társítható. A szabályzat segít biztosítani a részleg költségeinek és kiadásainak pontos monitorozását és felosztását az Azure EA Portalon, az EA-regisztrációhoz igazodva.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Szerepelhet egy Azure-fiók tulajdonosa biztonsági csoportként?

Nem, az előfizetés tulajdonosának egyedi Microsoft-fiók- (MSA-) vagy Azure Active Directory- (AAD-) hitelesítéssel kell rendelkeznie. A szervezeten belüli utódláshoz érdemes lehet általános fiókokat létrehozni és az AAD használatával kezelni az előfizetéshez való hozzáférést.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Egy egyedi felhasználó lehet több előfizetés tulajdonosa?

Egy Azure-fiók tulajdonosa korlátlan számú előfizetést hozhat létre és kezelhet.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hogyan lehet hozzáférni és megtekinteni a szervezet összes előfizetését?

Jelenleg ezt szabályzat alapján kell végezni, vagyis minden létrehozott előfizetés esetében a fiókot hozzá kell adni egy előfizetési szerepkörhöz szerepköralapú hozzáférés használatával.

### <a name="where-do-i-go-to-create-a-subscription"></a>Hol hozhatok létre előfizetést?

Nagyvállalati Azure-előfizetés létrehozása előtt a fiókját az EA-regisztráció rendszergazdájának hozzá kell adnia a fióktulajdonosi szerepkörhöz az Azure EA Portalon. Ezután be kell jelentkeznie az Azure EA Portalra, hogy megkapja a jogosultságát az EA-ajánlat típusú előfizetések létrehozásához. Javasoljuk, hogy az első EA-előfizetését az EA Portal előfizetési lapján található „+ Előfizetés hozzáadása” hivatkozással hozza létre.  Miután azonban a fiókja rendelkezik a megfelelő jogosultsággal, könnyebb lehet a portal.azure.com webhelyen, az oldal bal felső sarkában található előfizetési fülön létrehozni az előfizetéseket, ahol egyetlen lépésben létrehozhatja és átnevezheti az adott előfizetést.

### <a name="who-can-create-a-subscription"></a>Ki hozhat létre előfizetést?

Ha nagyvállalati Azure-előfizetést szeretne létrehozni, akkor fióktulajdonosi szerepkörrel kell rendelkeznie az [EA Portalon](https://ea.azure.com).

## <a name="next-steps"></a>További lépések

- Olvassa el, hogyan takaríthat meg pénzt a [virtuális gépek lefoglalásával](ea-portal-vm-reservations.md).
- Ha segítségre van szüksége az Azure EA Portallal kapcsolatos hibák elhárításához, olvassa el a következő részt: [A nagyvállalati szerződéses Azure Portal elérésével kapcsolatos hibák elhárítása](ea-portal-troubleshoot.md).
