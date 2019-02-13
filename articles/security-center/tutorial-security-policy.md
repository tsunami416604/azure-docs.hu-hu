---
title: Biztonsági szabályzatok használata |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan működik az Azure Security Centerben a biztonsági házirendeknek.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/4/2019
ms.author: rkarlin
ms.openlocfilehash: 90c508fb5ad3caf961747f2ac10bae42dd1f4c35
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114960"
---
# <a name="working-with-security-policies"></a>Biztonsági szabályzatok használata

Ez a cikk bemutatja, miként vannak konfigurálva a biztonsági szabályzatok és a Security Center ezeket is megtekintheti. Az Azure Security Center automatikusan hozzárendeli a [beépített biztonsági házirendek](security-center-policy-definitions.md) minden egyes előfizetés, amely regisztrálva van. Konfigurálhatja azokat a [Azure Policy](../azure-policy/azure-policy-introduction.md), amely lehetővé teszi szabályzatok beállítását a felügyeleti csoportokhoz, és több előfizetéshez.

PowerShell-lel házirendek beállításával kapcsolatos utasításokért lásd: [a rövid útmutató: Az Azure RM PowerShell modullal a nem megfelelő erőforrások azonosítására a szabályzat-hozzárendelés létrehozása](../azure-policy/assign-policy-definition-ps.md).

>[!NOTE]
> A Security Center az Azure Policy segítségével az integráció elindult. Meglévő ügyfelek automatikusan átkerülnek az új beépített kezdeményezésére az Azure Policy, a Security Center az előző biztonsági házirendek helyett. Ez a változás nem érinti az erőforrások vagy az új kezdeményezés az Azure Policy-jelenléte kivételével környezettel.

## <a name="what-are-security-policies"></a>Mik azok a biztonsági szabályzatok?
A biztonsági szabályzat határozza meg a számítási feladatokhoz tartozó kívánt konfigurációkat, és segít biztosítani a vállalati vagy hatósági követelményeknek való megfelelést. Az Azure Policy az Azure-előfizetésekre vonatkozó szabályzatokat határozhat meg, és testre szabni azokat a számítási feladatok típusának vagy az adatok érzékenységéhez. A szabályozott adatokat, például személyazonosításra alkalmas adatokat használó alkalmazások például szükség lehet egy magasabb biztonsági szintet, mint a többi munkaterhelését. Házirend beállítása a felügyeleti csoportok vagy előfizetések között, állítsa őket a [Azure Policy](../azure-policy/azure-policy-introduction.md).

A biztonsági szabályzatokat a biztonsági javaslatokkal kap az Azure Security Centerben. Segít azonosítani a potenciális biztonsági réseket és elhárítani a fenyegetéseket az azoknak való megfelelés figyelése Juthat az Önnek legmegfelelőbb lehetőség meghatározásával kapcsolatos további információkért lásd a [beépített biztonsági házirendek](security-center-policy-definitions.md).

Ha engedélyezi a Security Center, a Security Center beépített biztonsági házirend, egy beépített kezdeményezés a kategóriában a Security Center Azure Policy is megjelenik. A beépített kezdeményezés automatikusan regisztrálva a Security Center előfizetéseken (ingyenes vagy Standard szinten). A beépített kezdeményezésére csak naplózási házirendeket tartalmaz. 


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
 - **Lefedettség** – tarifacsomag kiválasztása, ingyenes vagy standard szintű, a felügyeleti csoport, az előfizetés vagy a munkaterület számítógépen futó azonosítja.  A Security Center tarifacsomagjaival kapcsolatos további információért lásd a [díjszabást](security-center-pricing.md).
 - **Beállítások** – előfizetések még a hivatkozás **beállításainak szerkesztése**. Kiválasztásával **beállításainak szerkesztése** frissítése lehetővé teszi a [a Security Center beállítások](security-center-policies-overview.md) minden egyes előfizetés vagy a felügyeleti csoport számára.
 - **Biztonságos pontszám** – a [biztonságos pontszám](security-center-secure-score.md) mérhető, mennyire vannak biztonságban a számítási feladatok biztonsági állapotáról, és segít rangsorolhatja javítására vonatkozó javaslatokat.

2. Válassza ki az előfizetést vagy felügyeleti csoportot, amelynek meg szeretné tekinteni házirendeket.

  - A **biztonsági házirend** képernyőn megjelenik a szabályzatok kap. a kiválasztott előfizetés vagy a felügyeleti csoport által végrehajtott műveletet.
  - A képernyő felső részén használatához nyissa meg az egyes házirendek megadott hivatkozások **hozzárendelés** , amely alkalmazza az előfizetést vagy felügyeleti csoportot. Használhatja a hivatkozások a hozzárendelés eléréséhez és szerkesztéséhez, vagy tiltsa le a szabályzatot. Például ha egy adott házirend-hozzárendelést hatékonyan tagadja meg a az endpoint protection című segítségével a hivatkozás a szabályzat eléréséhez és szerkesztéséhez vagy letiltásához.
  - A házirendek listájában láthatja a szabályzat hatékony alkalmazása az előfizetés vagy a felügyeleti csoport. Ez azt jelenti, hogy a beállítások az egyes házirendek vonatoznak a hatókörre kell figyelembe venni, és biztosítunk, milyen műveletet a szabályzat által összesített eredményét. Például ha egy hozzárendelési a szabályzat le van tiltva, de egy másik, AuditIfNotExist van beállítva, majd a halmozott hatása érvényes AuditIfNotExist. A több aktív hatás mindig elsőbbséget élvez.
  - A szabályzatok pontban hatással lehetnek: Hozzáfűzés, naplózás, AuditIfNotExists, nem engedélyezi, DeployIfNotExists, le van tiltva. Milyen hatások érvényesek. További információkért lásd: [ismertetése házirend hatások](../governance/policy/concepts/effects.md).

   ![a házirend képernyő](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Házirendek hozzárendelésekor a megtekintése, láthatja, hogy több hozzárendeléseket, és láthatja, hogyan minden hozzárendelés önállóan van konfigurálva.

## <a name="edit-security-policies"></a>Biztonsági szabályzatok szerkesztése
Az Azure-előfizetések és a felügyeleti csoportok az egyes szerkesztheti az alapértelmezett biztonsági szabályzatban [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Biztonsági szabályzat módosításához az előfizetésben vagy az azt tartalmazó felügyeleti csoportban tulajdonos, közreműködő vagy biztonsági rendszergazda szerepkörrel kell rendelkeznie.

Egy biztonsági szabályzatot az Azure Policy szerkesztésére vonatkozó utasításokért lásd: és [létrehozása és a megfelelőség kikényszerítése céljából házirendek kezelése](../governance/policy/tutorials/create-and-manage.md).

Biztonsági szabályzatok az Azure Policy portálján keresztül REST API vagy a Windows PowerShell használatával szerkesztheti. Az alábbi példa a REST API-val szerkesztésére vonatkozó utasításokat tartalmaz.

### <a name="configure-a-security-policy-using-the-rest-api"></a>A REST API-val biztonsági szabályzat konfigurálása

A natív integrációt olyan Azure-szabályzat részeként az Azure Security Center lehetővé teszi, hogy használja ki az Azure Policy REST API-t hozhat létre szabályzat-hozzárendelések. Az alábbi utasításokat követve lépésről lépésre szabályzat-hozzárendelések létrehozását, valamint a meglévő hozzárendelések testre szabhatja. 

Az Azure Policy alapfogalmaival: 

- A **szabályzatdefiníció** szabály 

- Egy **kezdeményezés** szabályzatdefiníciók (szabályok) gyűjteménye 

- Egy **hozzárendelés** egy alkalmazás egy szabályzatot vagy kezdeményezést, amelyek egy adott hatókörhöz (felügyeleti csoport, előfizetési, stb.) 

A Security Center rendelkezik egy beépített kezdeményezés, amely tartalmazza az összes annak biztonsági házirendjei. Annak érdekében, hogy a Security Center-szabályzatok az Azure-erőforrások felmérése, a hozzárendelés a felügyeleti csoport, vagy az értékelni kívánt előfizetést kell létrehoznia.  

A beépített kezdeményezés rendelkezik minden alapértelmezés szerint engedélyezve van a Security Center-szabályzat. Dönthet úgy, hogy letiltja a beépített kezdeményezés az egyes szabályzatok, például alkalmazhatja minden kivételével a Security Center-szabályzat **webalkalmazási tűzfal**, a házirend érvénybe paraméter értékének módosításával , **Letiltott**. 

### <a name="api-examples"></a>API-példák

A következő példákban cserélje le ezeket a változókat:

- **{hatókör}**  adja meg a felügyeleti csoport nevét, vagy az előfizetés lépnek életbe a szabályzatot.
- **{policyAssignmentName}**  adja meg a [a megfelelő szabályzat-hozzárendelés neve](#policy-names).
- **a(z) {name}**  adja meg a nevét, vagy a rendszergazda a házirend-módosítások jóváhagyó neve.

Ez a példa bemutatja, hogyan rendelje hozzá a beépített a Security Center-kezdeményezéshez egy előfizetést vagy a felügyeleti csoport
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

Ez a példa bemutatja, hogyan rendelje hozzá a beépített a Security Center-kezdeményezéshez egy adott előfizetés le van tiltva a következő szabályzatokkal: 

- Rendszerfrissítések ("systemUpdatesMonitoringEffect") 

- Biztonsági konfigurációk ("systemConfigurationsMonitoringEffect") 

- Az Endpoint protection ("endpointProtectionMonitoringEffect") 

 
      PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

      Request Body (JSON) 

      { 

        "properties":{ 

      "displayName":"Enable Monitoring in Azure Security Center", 

      "metadata":{ 

      "assignedBy":"{Name}" 

      }, 

      "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

      "parameters":{ 

      "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 

      "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 

      "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 

      }, 

       } 

      } 

Ez a példa bemutatja, hogyan-hozzárendelés eltávolítása:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


### Nevek házirend-referencia <a name="policy-names"></a>

|Házirend neve a Security Centerben|Házirend neve jelenik meg az Azure Policy |A házirend érvénybe paraméter neve|
|----|----|----|
|SQL-titkosítás |Az Azure Security Centerben a titkosítatlan SQL database monitorozása |sqlEncryptionMonitoringEffect| 
|SQL Auditing (SQL-naplózás) |Az Azure Security Center nem naplózott SQL database monitorozása |sqlAuditingMonitoringEffect|
|System updates (Rendszerfrissítések) |Az Azure Security Centerben a hiányzó rendszerfrissítések figyelése |systemUpdatesMonitoringEffect|
|Storage-titkosítás |Tárfiókok hiányzó blobtitkosításának naplózása |storageEncryptionMonitoringEffect|
|JIT hálózati hozzáférés |Az Azure Security Centerben a lehetséges hálózati csak az idő szerinti (JIT) hozzáférés figyelése |jitNetworkAccessMonitoringEffect |
|Adaptív alkalmazásvezérlők |Alkalmazások az Azure Security Center engedélyezésének figyelése |adaptiveApplicationControlsMonitoringEffect|
|Network security groups (Hálózati biztonsági csoportok) |Az Azure Security Center megengedő hálózati hozzáférés figyelése |networkSecurityGroupsMonitoringEffect| 
|Biztonsági konfigurációk |Az Azure Security Center az operációs rendszer biztonsági réseinek figyelése |systemConfigurationsMonitoringEffect| 
|Endpoint protection (Végpontok védelme) |Az Azure Security Centerben a végpontok hiányzó védelmének monitorozása |endpointProtectionMonitoringEffect |
|Disk encryption (Lemeztitkosítás) |Az Azure Security Center titkosítatlan Virtuálisgép-lemezek monitorozása |diskEncryptionMonitoringEffect|
|Sebezhetőségi felmérés |Virtuális gépek biztonsági réseinek figyelése az Azure Security Centerben |vulnerabilityAssessmentMonitoringEffect|
|Web application firewall (Webalkalmazási tűzfal) |Az Azure Security Center nem védett webalkalmazás figyelése |webApplicationFirewallMonitoringEffect |
|Next generation firewall (Új generációs tűzfal) |Az Azure Security Center nem védett hálózati végpontok figyelése| |


### <a name="who-can-edit-security-policies"></a>Ki szerkeszthet biztonsági szabályzatok?
A Security Center szerepköralapú hozzáférés-vezérlés (RBAC), amely biztosít beépített szerepkörök, felhasználók, csoportok és Azure-szolgáltatások rendelhető használ. A Security Center megnyitásakor látnak csak ők is hozzáférhetnek az erőforrásokhoz kapcsolódó információkat. Ami azt jelenti, hogy felhasználók tulajdonosi, közreműködői vagy olvasói szerepköre sincs hozzárendelve a erőforrás tartozik előfizetés vagy az erőforrás-csoport. Ezen szerepkörök mellett két speciális Security Center-szerepkör van:

- Biztonsági olvasó: Jogosultságok megtekintése a Security Centernek, amely tartalmazza a javaslatok, riasztások, a házirend és egészségügyi, rendelkezik, de azok nem végezhet módosításokat.
- Biztonsági rendszergazda: Biztonsági olvasó nézetben megegyező jogokkal rendelkezik, és azok is a biztonsági házirend módosítása és javaslatok és riasztások bezárása.



## <a name="next-steps"></a>További lépések
Ebben a cikkben megismerkedhetett az Azure Policy biztonsági szabályzatok szerkesztése. A Security Centerrel kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md): Ismerje meg, hogyan tervezhet és az Azure Security Centerrel kapcsolatos tervezési szempontokat.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md): Az Azure-erőforrások állapotának figyelését ismertető útmutató.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [Partneri megoldások monitorozása az Azure Security Center](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Összes bérlőre vonatkozó információk megjelenítése az Azure Security Center](security-center-management-groups.md): Ismerje meg, hogyan állítható be a felügyeleti csoportok az Azure Security Center.
* [Az Azure Security Center – gyakori kérdések](security-center-faq.md): Választ találhat a szolgáltatás használatával kapcsolatos gyakori kérdésekre.
* [Az Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

További információk az Azure Policyról: [Mi az az Azure Policy?](../azure-policy/azure-policy-introduction.md)
