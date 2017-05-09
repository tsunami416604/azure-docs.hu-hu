---
title: "Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directoryhoz? | Microsoft Docs"
description: "Bejelentkezés a Microsoft Azure-ba és a kapcsolódó problémák, például az Azure-előfizetések és az Azure Active Directory közötti kapcsolat."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2017
ms.author: curtand
ms.translationtype: Human Translation
ms.sourcegitcommit: 2d428e0e3aaf8fd4a2138648411da644ccd308f6
ms.openlocfilehash: 4eee1b3f30c9e47983af5c1dfa60fe0deb8c2cc8
ms.contentlocale: hu-hu
ms.lasthandoff: 02/08/2017


---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>How Azure subscriptions are associated with Active Directory? (Hogyan kapcsolódnak az Azure-előfizetések az Azure Active Directory-hoz?)
Ez a cikk a Microsoft Azure-ba történő bejelentkezésről és a kapcsolódó problémákról, például az Azure-előfizetések és az Azure Active Directory (Azure AD) közötti kapcsolatról tartalmaz információt.

## <a name="accounts-that-you-can-use-to-sign-in"></a>Fiókok, amelyekkel bejelentkezhet
Kezdjük azokkal a fiókokkal, amelyekkel bejelentkezhet. Két típus létezik: Microsoft-fiók (korábban Microsoft Live ID) és munkahelyi vagy iskolai fiók, amely egy, az Azure AD-ben tárolt fiók.

| Microsoft-fiók | Azure AD-fiók |
| --- | --- |
| A Microsoft által futtatott végfelhasználói identitásrendszer |A Microsoft által futtatott üzleti identitásrendszer |
| A végfelhasználóra irányuló szolgáltatások, például a Hotmail és az MSN hitelesítése |A vállalakozásokra irányuló szolgáltatások, például az Office 365 hitelesítése |
| A felhasználók saját Microsoft-fiókokat hoznak létre, például amikor egy e-mail-fiókot regisztrálnak |A vállalatok és a szervezetek a saját munkahelyi vagy iskolai fiókjaikat hozzák létre és kezelik |
| Az identitások a Microsoft-fiókrendszerben lesznek létrehozva és tárolva |Az identitások az Azure-ral vagy más szolgáltatással (például az Office 365 keretein belül) jönnek létre, és a szervezethez rendelt Azure AD-példányban vannak tárolva |

Bár az Azure eredetileg csak a Microsoft-fiókok felhasználóinak hozzáférését engedélyezte, mostantól engedélyezi *mindkét* rendszer felhasználóinak hozzáférését. Ez úgy valósul meg, hogy az összes Azure-tulajdonság megbízik az Azure AD hitelesítésében, így az Azure AD hitelesíti a szervezeti felhasználókat, valamint a rendszer összevonási kapcsolatot hoz létre, ahol az Azure AD megbízik a Microsoft-fiókok végfelhasználói identitásrendszerében a felhasználók hitelesítéséhez. Ennek eredményeképpen az Azure AD képes a „vendég” Microsoft-fiókok és a „natív” Azure AD-fiókok hitelesítésére is.

Itt például egy Microsoft-fiókkal rendelkező felhasználó bejelentkezik a klasszikus Azure portálra.

> [!NOTE]
> A klasszikus Azure portálra történő bejelentkezéshez a msmith@hotmail.com fióknak Azure-előfizetésre van szüksége. A fióknak a szolgáltatás rendszergazdájának vagy az előfizetés társadminisztrátorának kell lennie.
>
>

![][1]

Mivel ez a Hotmail-cím végfelhasználói fiók, a bejelentkezést a Microsoft-fiók végfelhasználói identitásrendszere hitelesíti. Az Azure AD identitásrendszere megbízik a Microsoft-fiókrendszer által végzett hitelesítésben, és egy tokent ad ki az Azure-szolgáltatások eléréséhez.

## <a name="how-an-azure-subscription-is-related-to-azure-ad"></a>Az Azure-előfizetések és az Azure AD kapcsolata
Minden Azure-előfizetés bizalmi kapcsolattal rendelkezik egy Azure AD-példányhoz. Ez azt jelenti, hogy megbízik ebben a címtárban a felhasználók, szolgáltatások és eszközök hitelesítéséhez. Több előfizetés is megbízhat ugyanabban a címtárban, de egy előfizetés csak egy címtárban bízhat meg. A Beállítások lapon láthatja, hogy melyik címtárban bízik meg az előfizetése. [Szerkesztheti az előfizetés beállításait,](active-directory-understanding-resource-access.md) hogy módosítsa, mely címtárban bízik meg.

Ez az előfizetés és a címtár közötti bizalmi kapcsolat nem olyan, mint ami az előfizetés és az Azure összes többi erőforrása (webhelyek, adatbázisok stb.) között áll fenn, amelyek inkább az előfizetések gyermekerőforrásainak számítanak. Ha egy előfizetés lejár, akkor az előfizetéssel társított ilyen egyéb erőforrások hozzáférése is lejár. De a címtár az Azure-ban elérhető marad, és más előfizetéseket társíthat ezzel a címtárral, és folytathatja a címtár felhasználóinak kezelését.

Ugyanígy az előfizetésben látható Azure AD-bővítmény sem úgy működik, mint a klasszikus Azure portál egyéb bővítményei. A klasszikus Azure portál egyéb bővítményei az Azure-előfizetésre korlátozódnak. Az Azure AD-bővítményben láthatók nem változnak az előfizetés alapján – itt csak a bejelentkezett felhasználó alapján jelennek meg a címtárakat.

Minden felhasználó egyetlen saját címtárral rendelkezik, amely hitelesíti őt, de más címtárakban lehet vendég is. Az Azure AD-bővítményben minden címtárat lát, amelynek a felhasználói fiókja a tagja. Nem jelennek meg azok a címtárak, amelyeknek nem tagja a fiókja. A címtárak tokeneket adhatnak ki a munkahelyi vagy iskolai fiókokhoz az Azure AD-ben vagy a Microsoft-fiókok felhasználóinak (mert az Azure AD össze van vonva a Microsoft-fiókrendszerrel).

Ez a diagram Michael Smith előfizetését jeleníti meg, miután regisztrált a Contoso egy munkahelyi fiókjával.

![][2]

## <a name="how-to-manage-a-subscription-and-a-directory"></a>Előfizetés vagy címtár kezelése
Az Azure-előfizetések rendszergazdai szerepkörei kezelik az Azure-előfizetéshez társított erőforrásokat. Ezek a szerepkörök és az előfizetés kezelésének ajánlott eljárásai a [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles.md) cím szakaszban találhatók.

Alapértelmezés szerint a Szolgáltatás-rendszergazda szerepkörhöz van rendelve a regisztráláskor. Ha másoknak is ugyanezzel az előfizetéssel kell bejelentkezniük és elérniük a szolgáltatásokat, társadminisztrátorként felveheti őket. A Szolgáltatás-rendszergazda és a társadminisztrátorok Microsoft-fiókok vagy munkahelyi és iskolai fiókok lehetnek azon címtárból, amellyel az Azure-előfizetés társítva van.

Az Azure AD különböző rendszergazdai szerepkörökkel rendelkezik a címtárral és az identitással kapcsolatos funkciók kezeléséhez. Egy címtár globális rendszergazdája például felhasználókat és csoportokat adhat a címtárhoz, vagy a felhasználók többtényezős hitelesítését követelheti meg. A címtárat létrehozó felhasználókhoz a globális rendszergazdai szerepkör van rendelve, és ők rendszergazdai szerepköröket rendelhetnek más felhasználókhoz.

Az előfizetés rendszergazdáihoz hasonlóan az Azure AD rendszergazdai szerepkörei Microsoft-fiókok vagy munkahelyi és iskolai fiókok lehetnek. Az Azure AD rendszergazdai szerepköreit más szolgáltatások is használják, például az Office 365 és a Microsoft Intune. További információ: [Rendszergazdai szerepkörök hozzárendelése](active-directory-assign-admin-roles.md)

De itt az a legfontosabb, hogy az Azure-előfizetések rendszergazdái és az Azure AD-címtár rendszergazdái két különböző fogalmi kört alkotnak. Az Azure-előfizetés rendszergazdái kezelhetik az erőforrásokat az Azure-ban, és megtekinthetik az Active Directory-bővítményt a klasszikus Azure portálon (mert a klasszikus Azure portál egy Azure-erőforrás). A címtár rendszergazdái kezelhetik a címtár tulajdonságait.

Egy személy mindkét szerepkörrel rendelkezhet, de erre nincs szükség. Egy felhasználóhoz a címtár globális rendszergazdai szerepköre hozzárendelhető, de nem kell hozzárendelni az Azure-előfizetés szolgáltatás-rendszergazdai vagy társadminisztrátori szerepkörét. A felhasználó nem jelentkezhet be a klasszikus Azure portálra, ha nem az előfizetés rendszergazdája. De a felhasználó végezhet rendszergazdai feladatokat a címtáron más eszközökkel, például az Azure AD PowerShell vagy az Office 365 felügyeleti központ használatával.

## <a name="why-cant-i-manage-the-directory-with-my-current-user-account"></a>Miért nem tudok kezelni egy címtárat a jelenlegi felhasználói fiókommal?
Néha egy felhasználó megpróbálhat munkahelyi vagy iskolai fiókkal bejelentkezni a klasszikus Azure portálra, mielőtt Azure-előfizetésre regisztrálna. Ebben az esetben a felhasználó üzenetet kap arról, hogy nincs előfizetése ehhez a fiókhoz. Az üzenet tartalmaz egy hivatkozást egy ingyenes próbaelőfizetés igényléséhez.

Az ingyenes próbaverzióra történő regisztráció után a felhasználó láthatja a szervezet címtárát a klasszikus Azure portálon, de nem tudja kezelni azt (vagyis nem tud hozzáadni felhasználókat, vagy szerkeszteni a meglévő felhasználói tulajdonságokat), mert a felhasználó nem a címtár globális rendszergazdája. Az előfizetés lehetővé teszi, hogy a felhasználó használja a klasszikus Azure portált, és megtekinthesse az Azure Active Directory-bővítményt, de a globális rendszergazda további engedélyeire van szükség a címtár kezeléséhez.

## <a name="using-your-work-or-school-account-to-manage-an-azure-subscription-that-was-created-by-using-a-microsoft-account"></a>A munkahelyi vagy iskolai fiók használata Microsoft-fiókkal létrehozott Azure-előfizetés kezeléséhez
Ajánlott eljárásként [szervezetként kell regisztrálnia az Azure-előfizetésre](sign-up-organization.md), és munkahelyi vagy iskolai fiókkal kell kezelnie az erőforrásokat az Azure-ban. A munkahelyi vagy iskolai fiókok használata ajánlott, mert ezek központilag kezelhetők a fiókokat kiadó szervezet által, több funkciójuk van, mint a Microsoft-fiókoknak, és az Azure AD közvetlenül hitelesíti ezeket. Ugyanez a fiók más, az üzleteknek és szervezeteknek nyújtott Microsoft Online-szolgáltatások hozzáférését is biztosítja, például az Office 365- vagy a Microsoft Intune-hozzáférését. Ha már létezik egy, az ilyen más tulajdonságokkal használt fiókja, valószínűleg ugyanezt a fiókot szeretné használni az Azure-ral. Ilyen esetben az ilyen tulajdonságok mögöttes Active Directory-példányával is rendelkezik, amelyet az Azure-előfizetéshez szeretne használni a bizalmi kapcsolathoz.

Ezenkívül a munkahelyi vagy iskolai fiókok több módon is kezelhetők, mint a Microsoft-fiókok. Egy rendszergazda például alaphelyzetbe állíthatja a munkahelyi vagy iskolai fiókok jelszavát, vagy többtényezős hitelesítést követelhet meg ezekhez.

Néhány esetben lehet, hogy azt szeretné, hogy a szervezete egy felhasználója kezelhesse az egyik Azure-előfizetéssel társított erőforrásokat egy végfelhasználói Microsoft-fiók esetében. Ha át szeretne állni arra, hogy különböző fiókok kezeljenek előfizetéseket vagy címtárakat, a [Manage the directory for your Office 365 subscription in Azure](#manage-the-directory-for-your-office-365-subscription-in-azure) (Office 365-előfizetéshez tartozó címtár kezelése az Azure-ban) című fejezetet érdemes elolvasnia.

## <a name="signing-in-when-you-used-your-work-email-for-your-microsoft-account"></a>Bejelentkezés, ha a munkahelyi fiókját használta a Microsoft-fiókhoz
Ha a múltban valamikor létrehozott egy végfelhasználói Microsoft-fiókot a munkahelyi e-mail-címét használva felhasználói azonosítóként, megjelenhet egy oldal, amely felkéri, hogy válasszon a Microsoft Azure-fiókrendszer és a Microsoft-fiókrendszer közül.

![][3]

Ugyanazzal a névvel van egy-egy felhasználói fiókja az Azure AD-ben és a végfelhasználói Microsoft-fiókrendszerben. Ki kell választania a használni kívánt Azure-előfizetéssel társított fiókot. Ha hibaüzenetet kap, amely szerint nem létezik előfizetés ehhez a felhasználóhoz, valószínűleg rossz lehetőséget választott. Jelentkezzen ki, és próbálkozzon újra. További információ a bejelentkezést meggátló hibákról: [A „Nem találtunk a fiókjával társított előfizetéseket” hibák elhárítása](https://social.msdn.microsoft.com/Forums/en-US/f952f398-f700-41a1-8729-be49599dd7e2/troubleshooting-we-were-unable-to-find-any-subscriptions-associated-with-your-account-errors-in?forum=windowsazuremanagement).

## <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Manage the directory for your Office 365 subscription in Azure (Office 365-előfizetéshez tartozó címtár kezelése az Azure-ban)
Tegyük fel, hogy regisztrált az Office 365-ba, mielőtt az Azure-ba regisztrált volna. Most a klasszikus Azure portálon szeretné kezelni az Office 365-előfizetés címtárát. Ezt kétféleképpen teheti meg, attól függően, hogy regisztrált-e az Azure-ba.

### <a name="i-do-not-have-a-subscription-for-azure"></a>Nincs előfizetésem az Azure-hoz
Ebben az esetben csak [regisztráljon az Azure-ba](sign-up-organization.md) ugyanazzal a munkahelyi vagy iskolai fiókkal, amelyet az Office 365-höz használ. Az Office 365-fiók kapcsolódó információit a rendszer előre kitölti az Azure regisztrációs űrlapján. A fiókjához az előfizetés Szolgáltatás-rendszergazdai szerepköre lesz rendelve.  

### <a name="i-do-have-a-subscription-for-azure-using-my-microsoft-account"></a>Van előfizetésem az Azure-hoz a Microsoft-fiókomon keresztül
Ha munkahelyi vagy iskolai fiókkal regisztrált az Office 365-be, majd Microsoft-fiókkal regisztrált az Azure-ba, akkor két címtára van: egy a munkahelyhez vagy az iskolához, és egy alapértelmezett címtár, amely az Azure-ba történő regisztráció során jött létre.

Ha mindkét címtárat a klasszikus Azure portálon szeretné kezelni, végezze el az alábbi lépéseket.

> [!NOTE]
> Ezek a lépések csak akkor végezhetők el, ha egy felhasználó Microsoft-fiókkal van bejelentkezve. Ha a felhasználó munkahelyi vagy iskolai fiókkal van bejelentkezve, akkor a **Meglévő címtár használata** lehetőség nem érhető el, mert a munkahelyi vagy iskolai fiókok csak a saját címtárukkal hitelesíthetők (vagyis azzal a címtárral, ahol a munkahelyi vagy iskolai fiók tárolva van, és amelynek a munkahely vagy az iskola a tulajdonosa).
>
>

1. Jelentkezzen be a klasszikus Azure portálra a Microsoft-fiókjával.
2. Kattintson az **Új** > **Alkalmazásszolgáltatások** > **Active Directory** > **Címtár** > **Egyéni létrehozása** lehetőségre.
3. Kattintson a **Meglévő címtár használata** elemre, és jelölje be a **Készen állok a kijelentkezésre** jelölőnégyzetet, majd kattintson a pipára a művelet elvégzéséhez.
4. Jelentkezzen be a klasszikus Azure portálra egy olyan fiókkal, amely globális rendszergazdai jogosultságokkal rendelkezik a munkahelyi vagy iskolai címtárhoz.
5. A **Használhatom a Contoso címtárát az Azure-ral?** kérdésnél kattintson a **folytatás** gombra.
6. Kattintson az **Azonnali kijelentkezés** parancsra.
7. Jelentkezzen be ismét a klasszikus Azure portálra a Microsoft-fiókjával. Mindkét címtár megjelenik az Active Directory-bővítményben.

## <a name="next-steps"></a>Következő lépések
* Az Azure-előfizetések rendszergazdáinak módosításáról további információ: [Azure-rendszergazdai szerepkörök felvétele vagy módosítása](../billing/billing-add-change-azure-subscription-administrator.md)
* Az erőforrások hozzáférésének Microsoft Azure-ban történő kezeléséről további információért lásd: [Az erőforrások hozzáférésének megismerése az Azure-ban](active-directory-understanding-resource-access.md)
* A szerepkörök Azure AD-ben történő hozzárendeléséről további információért lásd: [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles.md).
* [Regisztráció az Azure-ba szervezetként](sign-up-organization.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

