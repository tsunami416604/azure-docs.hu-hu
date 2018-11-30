---
title: Az Azure Security Center biztonsági szabályzatainak beállítani, az Azure-szabályzatok, és a Security Centerben megtekinthető |} A Microsoft Docs
description: Ebből a dokumentumból megismerheti, hogy a szabályzatok beállítása az Azure Policy, vagy az Azure Security Center ezeket is megtekintheti.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334669"
---
# <a name="view-security-policies"></a>Biztonsági szabályzatok megtekintése

Ez a cikk bemutatja, miként vannak konfigurálva a biztonsági szabályzatok és a Security Center ezeket is megtekintheti. Az Azure Security Center automatikusan hozzárendeli a [beépített biztonsági házirendek](security-center-policy-definitions.md) minden egyes előfizetés, amely regisztrálva van. Konfigurálhatja azokat a [Azure Policy](../azure-policy/azure-policy-introduction.md), amely lehetővé teszi szabályzatok beállítását a felügyeleti csoportokhoz, és több előfizetéshez.

PowerShell-lel házirendek beállításával kapcsolatos utasításokért lásd: [a rövid útmutató: az Azure RM PowerShell modullal a nem megfelelő erőforrások azonosítására a szabályzat-hozzárendelés létrehozása](../azure-policy/assign-policy-definition-ps.md).



## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. Az Azure Policy az Azure-előfizetésekre vonatkozó szabályzatokat határozhat meg, és testre szabni azokat a számítási feladatok típusának vagy az adatok érzékenységéhez. A szabályozott adatokat, például személyazonosításra alkalmas adatokat használó alkalmazások például szükség lehet egy magasabb biztonsági szintet, mint a többi munkaterhelését. Házirend beállítása a felügyeleti csoportok vagy előfizetések között, állítsa őket a [Azure Policy](../azure-policy/azure-policy-introduction.md).



A biztonsági szabályzatokat a biztonsági javaslatokkal kap az Azure Security Centerben. Segít azonosítani a potenciális biztonsági réseket és elhárítani a fenyegetéseket az azoknak való megfelelés figyelése Juthat az Önnek legmegfelelőbb lehetőség meghatározásával kapcsolatos további információkért lásd a [beépített biztonsági házirendek](security-center-policy-definitions.md).

### <a name="management-groups"></a>Felügyeleti csoportok
Ha a vállalatnak sok előfizetése van, jól jöhet egy módszer, hogy hatékonyan kezelje az előfizetésekhez való hozzáférést, a szabályzatokat és a megfelelőséget. Az Azure Management Groups előfizetések fölötti hatókörszintet biztosít. Az előfizetéseket „felügyeleti csoportok” nevű tárolókba rendezheti, és az irányítási szabályzatokat alkalmazhatja a felügyeleti csoportokra. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott szabályzatokat. Minden címtárhoz tartozik egy legfelső szintű, „gyökér” felügyeleti csoportnak nevezett felügyeleti csoport. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. Ez a gyökérszintű felügyeleti csoport lehetővé teszi globális szabályzatok és RBAC-hozzárendelések címtárszintű alkalmazását. Felügyeleti csoportok használata az Azure Security Center beállításához, kövesse a [összes bérlőre vonatkozó információk megjelenítése az Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Fontos, hogy átlássa a felügyeleti csoportok és előfizetések hierarchiáját. További információt a felügyeleti csoportokról, a gyökérszintű felügyeletről és a felügyeleti csoportok hozzáféréséről [az erőforrások az Azure Felügyeleti csoportok segítségével való rendszerezését](../governance/management-groups/index.md#root-management-group-for-each-directory) ismertető részben talál.
>

## <a name="how-security-policies-work"></a>A biztonsági szabályzatok működése
A Security Center automatikusan létrehoz egy alapértelmezett biztonsági szabályzatot minden egyes Azure-előfizetéséhez. Szerkesztheti a szabályzatokat az Azure Policy, tegye a következőket:
- Új szabályzatdefiníciók létrehozása.
- Szabályzatok hozzárendelése felügyeleti csoportokhoz és előfizetésekhez, amelyek jelölhetnek egy teljes vállalatot, vagy egy adott üzleti egységet a vállalaton belül.
- Szabályzatoknak való megfelelés figyelése.

Az Azure Policyvel kapcsolatos további információkért olvassa el [a megfelelőség szabályzatok létrehozásával és kezelésével történő kikényszerítésével](../azure-policy/create-manage-policy.md) foglalkozó témakört.

Egy Azure-szabályzat az alábbi összetevőkből áll:

- A **házirend** szabály.
- Egy **kezdeményezés** házirendek gyűjteménye.
- Egy **hozzárendelés** az alkalmazás egy szabályzatot vagy kezdeményezést, amelyek egy adott hatókörhöz (felügyeleti csoport, előfizetéshez vagy erőforráscsoportot).

Egy erőforrást a rendszer azon szabályzatok szerint értékel ki, amelyek hozzá vannak rendelve, majd az erőforrás kap egy megfelelőségi arányt azon szabályzatok alapján, amelyeknek megfelel.

## <a name="who-can-edit-security-policies"></a>Ki szerkeszthet biztonsági szabályzatok?
A Security Center szerepköralapú hozzáférés-vezérlés (RBAC), amely biztosít beépített szerepkörök, felhasználók, csoportok és Azure-szolgáltatások rendelhető használ. A Security Center megnyitásakor látnak csak ők is hozzáférhetnek az erőforrásokhoz kapcsolódó információkat. Ami azt jelenti, hogy felhasználók tulajdonosi, közreműködői vagy olvasói szerepköre sincs hozzárendelve a erőforrás tartozik előfizetés vagy az erőforrás-csoport. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- Biztonsági olvasó: rendelkezik megtekintése a Security Centernek, amely tartalmazza a javaslatok, riasztások, a házirend és egészségügyi, rights, de azok nem végezhet módosításokat.
- Biztonsági rendszergazda: biztonsági olvasó nézetben megegyező jogokkal rendelkezik, és azok is a biztonsági házirend módosítása és javaslatok és riasztások bezárása.

## <a name="edit-security-policies"></a>Biztonsági szabályzatok szerkesztése
Az Azure-előfizetések és a felügyeleti csoportok az egyes szerkesztheti az alapértelmezett biztonsági szabályzatban [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Biztonsági szabályzat módosításához az előfizetésben vagy az azt tartalmazó felügyeleti csoportban tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie.

Egy biztonsági szabályzatot az Azure Policy szerkesztésére vonatkozó utasításokért lásd: és [létrehozása és a megfelelőség kikényszerítése céljából házirendek kezelése](../governance/policy/tutorials/create-and-manage.md).

## <a name="view-security-policies"></a>Biztonsági szabályzatok megtekintése

Biztonsági szabályzatok megtekintése a Security Centerben:

1. Az a **a Security Center** irányítópulton válassza **biztonsági házirend**.

    ![A Szabályzatkezelés panel](./media/security-center-policies/security-center-policy-mgt.png)

  Az a **házirendkezelés** képernyőn láthatja, hogy a felügyeleti csoportokhoz, előfizetések és munkaterületek, valamint a felügyeleticsoport-struktúrát kialakítani számát.

  > [!NOTE]
  > - A Security Center irányítópultján valószínűleg-előfizetések nagyobb számú **előfizetési lefedettség** , mint az előfizetések alatt látható **házirendkezelés**. Az Előfizetési lefedettség alatt a Standard, Ingyenes és „nem lefedett” előfizetések is megjelennek. A "nem kezelt" előfizetések nincs engedélyezve a Security Center, és nem jelennek meg a **házirendkezelés**.
  >

  A táblázatban lévő oszlopok a következők:

 - **Szabályzati kezdeményezés hozzárendelése** – Security Center [beépített szabályzatok](security-center-policy-definitions.md) és a egy előfizetést vagy felügyeleti csoporthoz hozzárendelt kezdeményezések.
 - **Megfelelőségi** – általános megfelelőségi pontszám egy felügyeleti csoporthoz, az előfizetés vagy a munkaterület számára. Az érték a hozzárendelések súlyozott átlaga. A súlyozott átlag figyelembe veszi egy hozzárendelés szabályzatainak számát és azon erőforrások számát, amelyre a hozzárendelés érvényes.

 Ha például az előfizetéshez két VM tartozik, és hozzá van rendelve egy kezdeményezés öt szabályzattal, akkor 10 értékelése lesz az előfizetésében. Ha az egyik VM két szabályzatnak nem felel meg, akkor az előfizetés teljes megfelelőségi értéke 80% lesz.

 - **Lefedettség** – tarifacsomag kiválasztása, ingyenes vagy standard szintű, a felügyeleti csoport, az előfizetés vagy a munkaterület számítógépen futó azonosítja.  A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
 - **Beállítások** – előfizetések még a hivatkozás **beállításainak szerkesztése**. Kiválasztásával **beállításainak szerkesztése** frissítése lehetővé teszi a [a Security Center beállítások](security-center-policies-overview.md) minden egyes előfizetés vagy a felügyeleti csoport számára.

2. Válassza ki az előfizetést vagy felügyeleti csoportot, amelynek meg szeretné tekinteni házirendeket.

  - A **biztonsági házirend** képernyőn megjelenik a szabályzatok kap. a kiválasztott előfizetés vagy a felügyeleti csoport által végrehajtott műveletet.
  - A képernyő felső részén használatához nyissa meg az egyes házirendek megadott hivatkozások **hozzárendelés** , amely alkalmazza az előfizetést vagy felügyeleti csoportot. Használhatja a hivatkozások a hozzárendelés eléréséhez és szerkesztéséhez, vagy tiltsa le a szabályzatot. Például ha egy adott házirend-hozzárendelést hatékonyan tagadja meg a az endpoint protection című segítségével a hivatkozás a szabályzat eléréséhez és szerkesztéséhez vagy letiltásához.
  - A házirendek listájában láthatja a szabályzat hatékony alkalmazása az előfizetés vagy a felügyeleti csoport. Ez azt jelenti, hogy a beállítások az egyes házirendek vonatoznak a hatókörre kell figyelembe venni, és biztosítunk, milyen műveletet a szabályzat által összesített eredményét. Például ha egy hozzárendelési a szabályzat le van tiltva, de egy másik, AuditIfNotExist van beállítva, majd a halmozott hatása érvényes AuditIfNotExist. A több aktív hatás mindig elsőbbséget élvez.
  - A szabályzatok pontban hatással lehetnek: hozzáfűzése, naplózás, AuditIfNotExists, Deny a DeployIfNotExists, le van tiltva. Milyen hatások érvényesek. További információkért lásd: [ismertetése házirend hatások](../governance/policy/concepts/effects.md).

   ![a házirend képernyő](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Házirendek hozzárendelésekor a megtekintése, láthatja, hogy több hozzárendeléseket, és láthatja, hogyan minden hozzárendelés önállóan van konfigurálva.

## <a name="next-steps"></a>További lépések
Ebből a cikkből megismerhette a Security Center biztonsági szabályzatainak konfigurálását. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Az Azure Security Centerhez tartozó tervezési szempontokat ismertető és az azokat figyelembe vevő tervezési folyamatokban segítő útmutató, amely megkönnyíti az Azure Security Center használatát.
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md): Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Bérlőszintű láthatóság az Azure Security Centerben](security-center-management-groups.md): Megismerheti a felügyeleti csoportok beállításának módját az Azure Security Centerben.
* [Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

További információk az Azure Policyról: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
