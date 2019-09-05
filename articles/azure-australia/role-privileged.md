---
title: Szerepköralapú Access Control és Privileged Identity Management az Azure Ausztráliában
description: Útmutató a szerepköralapú Access Control és Privileged Identity Management megvalósításához az ausztráliai régiókban, hogy megfeleljenek az ausztráliai kormányzati szabályzatok, rendeletek és jogszabályok követelményeinek.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: a9451b5dcd572eee27809cf66df889f06da960ed
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571496"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Szerepköralapú Access Control (RBAC) és Privileged Identity Management (PIM)

A rendszergazdai jogosultságok kezelése kritikus lépés a biztonság biztosításához az informatikai környezetekben. A rendszergazdai jogosultságok a legkevésbé magas szintű biztonsággal való korlátozása a [ASCS ISM](https://acsc.gov.au/infosec/ism/index.htm) követelménye, és a biztonsági javaslatok a [ASCs Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) listájának részét képezi.

A Microsoft olyan vezérlőket biztosít, amelyekkel igény szerint, a Microsoft Azureon belül pedig elég egyszerűen hozzáférhet. Ezeknek az ellenőrzéseknek a megértése elengedhetetlen a felhőben működő hatékony biztonsági helyzetekhez. Ez az útmutató áttekintést nyújt magukról a vezérlőkről, valamint ismerteti a legfontosabb tervezési szempontokat a megvalósításuk során.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A szerepköralapú Access Control a Microsoft Azureon belüli összes erőforráshoz való hozzáférés, valamint a Azure Active Directory (Azure AD) felügyeletének központja. A RBAC több, az Azure-ban elérhető kiegészítő szolgáltatás mellett is megvalósítható. Ez a cikk az Azure Management Groups, Azure Active Directory csoportok és az Azure Privileged Identity Management (PIM) használatával történő hatékony RBAC megvalósítására koncentrál.

A RBAC megvalósítása magas szinten három összetevőt igényel:

![RBAC – áttekintés](media/rbac-overview.png)

* **Rendszerbiztonsági tag**: A rendszerbiztonsági tag a következők egyike lehet: egy felhasználó, egy csoport, [egyszerű szolgáltatásnév vagy [felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). A rendszerbiztonsági tag Azure Active Directory csoportok használatával kell hozzárendelni a jogosultságokat.

* **Szerepkör**-definíciók: A szerepkör-definíciók (más néven szerepkör) az engedélyek gyűjteményei. Ezek az engedélyek határozzák meg azokat a műveleteket, amelyeket a szerepkör-definícióhoz rendelt rendszerbiztonsági tag is elvégez. Ezt a funkciót az Azure-erőforrás szerepkörei és Azure Active Directory rendszergazdai szerepkörök biztosítják. Az Azure beépített szerepkörök (hivatkozás) készletét tartalmazza, amely egyéni szerepkörökkel kiegészíthető.

* **Hatókör**: A hatókör azon Azure-erőforrások készlete, amelyekre a szerepkör-definíció vonatkozik. Az Azure-szerepkörök az Azure Management Groups használatával rendelhetők hozzá az Azure-erőforrásokhoz.

Ez a három összetevő egyesíti a rendszerbiztonsági tag által a szerepkör-definíciókban meghatározott hozzáférést az Azure Management Groups hatókörében lévő összes erőforráshoz, ezt szerepkör-hozzárendelésnek nevezzük. Több szerepkör-definíció is rendelhető egy rendszerbiztonsági tag számára, és több rendszerbiztonsági tag is hozzárendelhető egyetlen hatókörhöz.

### <a name="azure-active-directory-groups"></a>Azure Active Directory csoportok

Ha a jogosultságokat személyekhez vagy csapatokhoz rendeli hozzá, amikor lehetséges, a hozzárendelést egy Azure Active Directory csoporthoz kell társítani, és nem közvetlenül a szóban forgó felhasználóhoz kell rendelni. Ez ugyanaz az ajánlott eljárás, amelyet a helyszíni Active Directory megvalósítások örökölnek. Ahol lehetséges Azure Active Directory csoportokat kell létrehozni csoportonként, kiegészítve a létrehozott Azure-Management Groups logikai struktúrájával.

Hibrid felhőalapú forgatókönyv esetén a helyszíni Windows Server Active Directory biztonsági csoportok szinkronizálhatók a Azure Active Directory példányával. Ha már végrehajtotta a RBAC-t a helyszíni Windows Server Active Directory biztonsági csoportok használatával, akkor ezek a csoportok a szinkronizálás után felhasználhatók az Azure-erőforrások RBAC megvalósítására. Ellenkező esetben a Felhőbeli környezet tiszta lappal is tekinthető, így megtervezheti és megvalósíthatja a Azure Active Directory megvalósítására épülő robusztus jogosultság-kezelési tervet.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azure-erőforrás szerepkörei és Azure Active Directory rendszergazdai szerepkörök

A Microsoft Azure számos beépített szerepkört kínál az [Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) -erőforrásokhoz és a [Azure Active Directory felügyelethez](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Mindkét típusú szerepkör adott részletes hozzáférést biztosít az Azure-erőforrásokhoz vagy az Azure AD-rendszergazdákhoz. Fontos megjegyezni, hogy az Azure-erőforrás-szerepkörök nem használhatók az Azure AD-hez és az Azure AD-szerepkörökhöz való rendszergazdai hozzáférés biztosításához, és nem biztosítanak konkrét hozzáférést az Azure-erőforrásokhoz.

Néhány példa arra, hogy milyen típusú hozzáféréseket lehet hozzárendelni egy Azure-erőforráshoz egy beépített szerepkör használatával:

* Az egyik felhasználó számára engedélyezheti az előfizetésben lévő virtuális gépek, a másik felhasználó számára pedig a virtuális hálózatok kezelését.
* Engedélyezheti egy adatbázis-rendszergazdai csoport számára az előfizetésben lévő SQL-adatbázisok kezelését.
* Engedélyezheti egy felhasználó számára az egy adott erőforráscsoportba tartozó összes erőforrás, például a virtuális gépek, a webhelyek és az alhálózatok kezelését.
* Engedélyezheti egy alkalmazás számára az egy adott erőforráscsoportba tartozó összes erőforrás elérését.

Az Azure AD-felügyelethez hozzárendelhető hozzáférési típusok például a következők:

* Felhasználói jelszavak alaphelyzetbe állításának engedélyezése a helpdesk munkatársai számára
* Külső felhasználók meghívásának engedélyezése az Azure AD-példányba VÁLLALATKÖZI együttműködés céljából
* A rendszergazdai személyzet olvasási hozzáférésének engedélyezése a bejelentkezéshez és a naplózási jelentésekhez
* Lehetővé teszi a munkatársak számára az összes felhasználó és csoport felügyeletét, beleértve a jelszavak alaphelyzetbe állítását is.

Fontos, hogy elvégezze az időt, hogy megértse az engedélyezett műveletek teljes listáját egy beépített szerepkör biztosítja, hogy a jogosulatlan hozzáférés ne legyen engedélyezve. A beépített szerepkörök és az általuk biztosított hozzáférések listája folyamatosan fejlődik, a szerepkörök és a hozzájuk tartozó definíciók teljes listáját a fent hivatkozott dokumentáció áttekintésével vagy a Azure PowerShell parancsmag használatával tekintheti meg:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

vagy az Azure CLI-parancs:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Szükség szerint egyéni Azure-erőforrás-szerepköröket is létrehozhat. Ezek az egyéni szerepkörök a Azure Portal, a PowerShell vagy az Azure CLI segítségével hozhatók létre. Egyéni szerepkörök létrehozásakor elengedhetetlen, hogy a szerepkör célja egyedi legyen, és hogy a függvényt még egy meglévő Azure-erőforrás-szerepkör ne biztosítsa. Ez csökkenti a folyamatos felügyelet bonyolultságát, és csökkenti a szükségtelen jogosultságokat fogadó rendszerbiztonsági tag kockázatát. Erre példa lehet egy egyéni Azure-erőforrás-szerepkör létrehozása, amely a beépített Azure-erőforrás-szerepkörök, a virtuális gépek közreműködői és a virtuális gép rendszergazdai bejelentkezési adatai között található.

Az egyéni szerepkör a meglévő közreműködő szerepkörön alapulhat, amely a következő hozzáférést biztosítja:

| Azure-erőforrás | Hozzáférés szintje |
| --- | --- |
| Virtuális gépek | Kezelhető, de nem érhető el |
| Virtuális géphez Virtual Network csatolva | Nem érhető el |
| Virtuális géphez csatolt tárterület | Nem érhető el |
|

Az egyéni szerepkör megőrizheti ezt az alapszintű hozzáférést, de a kijelölt felhasználók számára a virtuális gépek hálózati konfigurációjának módosításához alapszintű további jogosultságok is megadhatók.

Az Azure-erőforrás szerepkörei az Azure Management Groups használatával is kihasználhatják az erőforrásokhoz való hozzárendelés előnyeit.

### <a name="azure-management-groups"></a>Azure-beli felügyeleti csoportok

Az Azure Management Groups segítségével egy szervezet felügyelheti a szerepkörök hozzárendelését az összes előfizetésben és az erőforrásaikat egy Azure-bérletben. Az Azure Management Groups úgy lett kialakítva, hogy lehetővé tegye a felügyeleti hierarchiák létrehozását, beleértve a szervezeti struktúra hierarchikus feltérképezését az Azure-ban. A szervezeti egységek különálló logikai entitásként való létrehozása lehetővé teszi, hogy az engedélyeket az egyes csapatokra vonatkozó követelmények alapján alkalmazza a szervezeten belül. Az Azure Management Groups a felügyeleti hierarchia legfeljebb hat szintre való definiálására használható.

![Felügyeleti csoportok](media/management-groups.png)

Az Azure Management Groups Azure-előfizetéseken belüli Azure-előfizetésekre vannak leképezve. Ez lehetővé teszi, hogy a szervezet elkülönítse az egyes üzleti egységekhez tartozó Azure-erőforrásokat, és részletesen szabályozza a költséghatékonyságot és a jogosultság-hozzárendelést.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

A Microsoft az Azure Privileged Identity Managementon keresztül igénybe vette az igény szerinti (JIT) és az éppen elég hozzáférés (JEA) szolgáltatását. Ez a szolgáltatás lehetővé teszi a rendszergazdáknak az Azure-erőforrásokhoz való emelt szintű hozzáférés szabályozását, kezelését és figyelését. A PIM lehetővé teszi, hogy a rendszerbiztonsági tag "jogosult" legyen a rendszergazda személyzet szerepköre számára, így a felhasználók a Azure Portal vagy a PowerShell-parancsmagok segítségével kérhetik a szerepkör aktiválását. Alapértelmezés szerint a szerepkör-hozzárendelés 1 és 72 óra közötti időtartamra aktiválható. Ha szükséges, a felhasználó egy bővítményt kérhet a szerepkör-hozzárendeléséhez, és a szerepkör-hozzárendelés végleges beállításának lehetősége is létezik. Szükség esetén a többtényezős hitelesítésre vonatkozó követelmény kényszeríthető, ha a felhasználók a jogosult szerepköreik aktiválását kérik. Ha a szerepkör-aktiválás lejárati ideje lejár, a rendszerbiztonsági tag már nem rendelkezik a szerepkör által biztosított privilegizált hozzáféréssel.

A PIM használata megakadályozza a jogosultság-hozzárendelési problémákat, amelyek olyan környezetekben fordulnak elő, amelyek nem használják az igény szerinti hozzáférést, vagy nem végzik el a jogosultság-hozzárendelés rutinos naplózását. Az egyik gyakori probléma az emelt szintű jogosultságok kiosztása, amely a emelt szintű jogosultságokat igénylő feladat befejezése után hosszú ideig elveszik. Egy másik probléma az emelt szintű jogosultságok elterjedése egy adott környezetben a rendszerbiztonsági tag számára rendelt hozzáférés klónozásával más hasonló rendszerbiztonsági tag konfigurálásakor.

## <a name="key-design-considerations"></a>A legfontosabb tervezési szempontok

Ha a RBAC stratégiát a legalacsonyabb jogosultsági szintű biztonság érvényesítésére kívánja tervezni, a következő biztonsági követelményeket kell figyelembe venni:

* Az emelt szintű hozzáférésre vonatkozó kérelmek ellenőrzése megtörtént
* A rendszergazdai jogosultságok az adott feladatok végrehajtásához szükséges minimális hozzáférésre korlátozódnak
* A rendszergazdai jogosultságok az adott feladatok elvégzéséhez szükséges minimális időtartamra korlátozódnak
* A megadott rendszergazdai jogosultságok rendszeres felülvizsgálata

A RBAC stratégia megtervezésének folyamata az üzleti funkciók részletes áttekintését igényli, hogy megértse a különböző üzleti szerepkörök közötti hozzáférés különbségeit, valamint a emelt szintű jogosultságokat igénylő munka típusát és gyakoriságát. A biztonsági mentési operátorok, a biztonsági rendszergazdák és az auditorok közötti különbség a különböző időpontokban különböző hozzáférési szintekhez szükséges, és a folyamatos ellenőrzés különböző szintjein történik.

## <a name="validate-requests-for-access"></a>Hozzáférési kérelmek ellenőrzése

A emelt szintű jogosultságokat explicit módon jóvá kell hagyni. Ennek támogatásához ki kell dolgozni egy jóváhagyási folyamatot, és a megfelelő személyzet felelős annak ellenőrzéséhez, hogy a további jogosultságok iránti kérések érvényesek-e. Privileged Identity Management több lehetőséget biztosít a szerepkör-hozzárendelés jóváhagyására. A szerepkör-aktiválási kérés konfigurálható úgy, hogy engedélyezze az önjóváhagyást, illetve hogy az összes szerepkör-aktiválási kérést manuálisan áttekintse és jóváhagyja a kijelölt jóváhagyókat. Az aktiválási kérések úgy is konfigurálhatók, hogy további támogatási információkra van szükség az aktiválási kérelemben (például jegyek száma).

### <a name="restrict-privilege-based-on-duties"></a>Jogosultságok korlátozása a vámok alapján

A rendszerbiztonsági tag számára biztosított jogosultság szintjének korlátozása kritikus fontosságú, mivel a jogosultságok több mint kiosztása egy gyakori informatikai biztonsági támadási vektor. A felügyelt erőforrások típusait és a felelős csapatok értékelését úgy kell megállapítani, hogy a napi feladatokhoz szükséges minimális szintű jogosultságok is hozzárendelhetők legyenek. A napi feladatok elvégzéséhez szükséges további jogosultságokat csak az adott feladat végrehajtásához szükséges ideig kell megadni. Ilyen lehet például a "közreműködő" hozzáférés az ügyfél rendszergazdájához, de a "tulajdonos" engedély kérése egy Azure-erőforrás számára egy adott feladathoz, amely ideiglenes, magas szintű hozzáférést igényel.

Ez biztosítja, hogy minden egyes rendszergazda csak a lehető legrövidebb idő alatt emelt szintű hozzáféréssel rendelkezik. Ezen gyakorlatok betartása csökkenti az informatikai infrastruktúrák teljes támadási felületét.

### <a name="regular-evaluation-of-administrative-privilege"></a>A rendszergazdai jogosultságok rendszeres kiértékelése

Rendkívül fontos, hogy a környezetekben lévő rendszerbiztonsági tagokat rendszeresen naplózza a rendszer, hogy a megfelelő szintű jogosultságot jelenleg rendelje hozzá. Microsoft Azure számos módszert biztosít az Azure rendszerbiztonsági tagjaihoz rendelt jogosultságok naplózására és kiértékelésére. Privileged Identity Management lehetővé teszi a rendszergazdák számára, hogy rendszeres időközönként "hozzáférési felülvizsgálatokat" végezzenek a rendszerbiztonsági tag számára biztosított szerepkörökről. A hozzáférési felülvizsgálat elvégezhető az Azure-erőforrások szerepkör-hozzárendelésének és Azure Active Directory rendszergazdai szerepkör-hozzárendelésének naplózására. A hozzáférési felülvizsgálatot a következő tulajdonságokkal lehet konfigurálni:

* **Tekintse át a nevet, és tekintse át a kezdő és a záró dátumot**: Az értékeléseket úgy kell konfigurálni, hogy a kijelölt felhasználók számára elég hosszúak legyenek.

* **Az áttekinthető szerepkör**: Minden hozzáférési felülvizsgálat egyetlen Azure-szerepkörre koncentrál.

* **Kijelölt felülvizsgálók**: Három lehetőség van a felülvizsgálat végrehajtására. A felülvizsgálatot hozzárendelheti valaki másnak a befejezéshez, elvégezheti saját maga is, vagy megtekintheti, hogy mindegyik felhasználó megvizsgálja a saját hozzáférését.

* A **felhasználóknak meg kell adniuk a hozzáférés okát**: A hozzáférési felülvizsgálat elvégzése során a felhasználóknak meg kell adniuk a jogosultsági szint fenntartásának okát.

A függőben lévő hozzáférési felülvizsgálatok előrehaladása bármikor megfigyelhető a Azure Portal irányítópultján keresztül. Az áttekintett szerepkörhöz való hozzáférés változatlan marad, amíg a hozzáférési felülvizsgálat be nem fejeződik. Az összes PIM-felhasználó [](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) hozzárendelését és aktiválását is naplózhatja egy megadott időszakon belül.

## <a name="next-steps"></a>További lépések

Tekintse át a rendszer monitorozásáról szóló cikket az [Azure Ausztráliában](system-monitor.md).
