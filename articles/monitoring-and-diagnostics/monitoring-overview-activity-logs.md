---
title: Az Azure tevékenységnapló áttekintése |} Microsoft Docs
description: Megtudhatja, mi az Azure tevékenységnapló és hogyan használhatja az Azure-előfizetése belül bekövetkező események megértéséhez.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: johnkem
ms.openlocfilehash: 060f91e4bdd1dd2690a3e1f148f7c5e5e13d13ef
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235664"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Az Azure tevékenységnapló figyelő előfizetés tevékenység

A **Azure tevékenységnapló** van egy előfizetési napló, amely történt az Azure-előfizetés szintű események betekintést nyújt. Ez magában foglalja az Azure Resource Manager működési adatokat a frissítésekre a szolgáltatás állapotával kapcsolatos események adatait számos. A műveletnapló korábban hívták "Naplófájlok" vagy "Működési Logs", a felügyeleti kategória jelentések vezérlő-vezérlősík eseményeket az előfizetések óta. A tevékenység-naplót használó, meghatározhatja a "mi, ki, és mikor" az esetleges írási műveleteket (PUT, POST, Törlés) végzett az erőforrást az előfizetésében. A művelet és az egyéb kapcsolódó tulajdonságainak állapotának értelmezni is lehet. A műveletnapló nem tartalmaz olvasható (GET) vagy a klasszikus használó erőforrások / "RDFE" modell.

![Tevékenység naplók és a más típusú naplók ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

1. ábra: Tevékenységi naplóit vagy más típusú naplók

A műveletnapló eltér az [diagnosztikai naplók](monitoring-overview-of-diagnostic-logs.md). Tevékenységi naplóit biztosítanak kívülről erőforráson műveleteire vonatkozó adatokat (a "vezérlő vezérlősík"). Diagnosztikai naplók az erőforrás által kibocsátott, és adja meg (az "adatok vezérlősík") erőforrás működésével kapcsolatos adatokat.

> [!WARNING]
> Az Azure tevékenységnapló van, amely elsősorban az Azure Resource Manager-tevékenységek. A klasszikus/RDFE modellt használó erőforrások nem követi nyomon. Egyes klasszikus erőforrás állnak a proxy erőforrás-szolgáltató az Azure Resource Manager (például Microsoft.ClassicCompute). Ha Ön a szolgáltatóosztályokkal klasszikus erőforrástípus Azure Resource Manageren keresztül a proxy erőforrás-szolgáltatókat használ, a műveletek a műveletnapló jelennek meg. Ha Ön a szolgáltatóosztályokkal kívül az Azure Resource Manager-proxyk klasszikus erőforrástípus, a műveletek csak tárolja, amely a műveletnaplóban. A műveletnapló tallózható a portál egy külön szakaszban.
>
>

Az Azure portál, CLI, PowerShell-parancsmagok használatával tevékenységnapló kérhetnek le események és Azure figyelő REST API-t.

> [!NOTE]
>  [Az újabb riasztások](monitoring-overview-unified-alerts.md) továbbfejlesztett megoldást nyújt, ha jelentkeznek be riasztási szabályok létrehozását és kezelését a tevékenység.  [További információk](monitoring-activity-log-alerts-new-experience.md).

Tekintse meg a következő videót a műveletnapló bemutatása.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]


## <a name="categories-in-the-activity-log"></a>A műveletnapló kategóriák
A műveletnapló számos modulkategória közül adatokat tartalmazza. Az ezekben a kategóriákban sémákra részletes [ebben a cikkben találhat](monitoring-activity-log-schema.md). Ezek a következők:
* **Felügyeleti** – Ez a kategória tartalmazza az összes rekord létrehozása, update, delete és művelet műveleteket Resource Manageren keresztül. Milyen típusú mutatunk be, ebbe a kategóriába tartozó eseményeket például "virtuális gép létrehozása" és "hálózati biztonsági csoport törlése" minden felhasználó vagy alkalmazás erőforrás-kezelő használatával végrehajtott műveletet egy adott erőforrástípus művelet van modellezve. Ha a művelet típusa írási, a Delete és a művelet, a rekordokat a kezdő és a sikeres vagy sikertelen lesz, hogy a művelet tárolja, amely a felügyeleti kategória. A felügyeleti kategória is a szerepköralapú hozzáférés-vezérlést módosításokat egy előfizetésben.
* **Szolgáltatás állapota** -ebbe a kategóriába történt az Azure-szolgáltatás állapotának események adatait tartalmazza. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa: "USA keleti régiója az SQL Azure tapasztal állásidő." Szolgáltatás állapotával kapcsolatos események térjen öt fajta: beavatkozás szükséges, támogatott helyreállítási, incidens, karbantartási, adatokat vagy biztonsági, és csak jelenik meg, ha egy erőforrást, amely akkor negatív hatással lehet az esemény az előfizetést.
* **Riasztási** -ebbe a kategóriába látható Azure riasztások valamennyi aktiváláshoz. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa: "CPU % myVM már több mint 80 az elmúlt 5 percben." Az Azure rendszereket rendelkezik egy riasztási koncepció--valamiféle szabály megadása, és értesítést kaphat, ha feltételek egyeznek meg, hogy a szabály. Minden alkalommal, amikor egy támogatott Azure riasztástípus "aktiválja," vagy a feltételek értesítést létrehozásához, egy rekordot az aktiválás is leküldött ebbe a kategóriába a műveletnapló.
* **Automatikus skálázás** – Ez a kategória tartalmazza a rekord a a művelethez az automatikus skálázás motor bármely adta meg az előfizetés automatikus skálázás beállításai alapján a kapcsolódó események. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa, hogy "Automatikus skálázás felskálázott művelete nem sikerült." Használja az automatikus skálázás, automatikusan horizontális felskálázás vagy méretezni támogatott erőforrástípus található példányok száma a nap és/vagy terhelés (mérték) adatok az automatikus skálázási beállítás használatával ideje alapján. Ha a feltételek felfelé vagy lefelé méretezési, a kezdő és a sikeres és sikertelen események ebbe a kategóriába tartozó rögzítését.
* **A javaslat** -ebbe a kategóriába Azure Advisor szolgáltatásból való javaslat eseményeket tartalmazza.
* **Biztonsági** – Ez a kategória tartalmazza az Azure Security Center által generált riasztások adatainak. Az ebbe a kategóriába tartozó mutatunk be eseménytípusra példa, hogy "gyanús dupla bővítményfájl végrehajtása."
* **Házirend- és erőforrás állapota** – ezen kategóriák nem tartalmaz eseményeket, a jövőbeli használatra fenntartva.

## <a name="event-schema-per-category"></a>Esemény séma kategóriánként
[Ez a cikk a tevékenységnapló esemény séma kategóriánként megértéséhez tekintse meg.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Mire képes a műveletnapló
Íme néhány a műveletnapló teheti:

![Azure-tevékenységnapló](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Lekérdezés, és megtekintheti a a **Azure-portálon**.
* [Riasztás létrehozása a tevékenységnapló esemény.](monitoring-activity-log-alerts.md)
* [Adatfolyam-úgy, hogy egy **Eseményközpont** ](monitoring-stream-activity-logs-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldások, például a Power bi szempontjából.
* A Power bi használatával elemezze a [ **Power bi tartalomcsomag**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Menti egy **Tárfiók** archív vagy manuális ellenőrzést](monitoring-archive-activity-log.md). A megőrzési ideje (nap) használatával is megadhat a **napló profil**.
* Lekérdezése a PowerShell-parancsmag, a parancssori felületen vagy a REST API-t.

## <a name="query-the-activity-log-in-the-azure-portal"></a>A lekérdezés a műveletnapló az Azure-portálon
Az Azure-portálon belül több helyen tekintheti meg a tevékenységnapló:
* A **tevékenységnapló** , így hozzáférhet a műveletnapló alatt keresve **minden szolgáltatás** a bal oldali navigációs ablaktáblán.
* **A figyelő** a bal oldali navigációs panelen alapértelmezés szerint megjelenik. A műveletnapló Azure figyelő egy szakaszában.
* Minden erőforrás **erőforrás**, például a virtuális gép konfigurációs panelt. A műveletnapló, a legtöbb a erőforrás paneleken részeiből kell, és kattintással automatikusan szűri az eseményeket az adott erőforrás.

Az Azure portálon a tevékenységnapló a mezők szerint szűrheti:
* A TimeSpan - a kezdési és befejezési ideje eseményeket.
* Kategória – az eseményforrásra fent leírt módon.
* -Előfizetés egy vagy több Azure-előfizetés nevét.
* Erőforráscsoport - egy vagy több erőforráscsoportokat előfizetésekben belül.
* Erőforrás (név) – egy adott erőforrás nevét.
* Erőforrástípus - típusú erőforrások, például Microsoft.Compute/virtualmachines.
* A művelet neve – az Azure Resource Manager művelet, például Microsoft.SQL/servers/Write neve.
* Súlyosság - a súlyossági szintet az esemény (tájékoztatás, figyelmeztetés, hiba, kritikus).
* -A "hívó", vagy a művelet felhasználó által kezdeményezett esemény.
* Nyissa meg a keresési - egy megnyitott keresési szövegmezőben, amely megkeresi az adott karakterlánc összes esemény található összes mezőhöz között.

Miután beállított szűrők csoportja, mentheti munkamenetei között legalább egyszer kell végrehajtani ugyanabban a lekérdezésben újra alkalmazza a jövőben ezek szűrőkkel Ha fennállásának lekérdezésként. A lekérdezés is rögzítheti az irányítópulton való rögzítéséhez Azure mindig követésére egy adott esemény.

Kattintson az "Apply" a lekérdezés futtatása, és minden egyező események megjelenítése. Kattintson a minden esetben a listán a jeleníti meg az esemény összegzése, valamint, hogy az esemény teljes nyers JSON.

Még több bekapcsolási, kattintson a **naplófájl-keresési** ikonra, amely a tevékenységnapló adatait jeleníti meg a [napló Analytics tevékenység Naplóelemzési megoldás](../log-analytics/log-analytics-activity.md). A műveletnapló panel lehetővé teszi egy alapszintű szűrő/tallózási élménynek napló, de Naplóelemzési lehetővé teszi forgáspont, azok lekérdezésére vagy az adatok megjelenítése hatékonyabb módon.

## <a name="export-the-activity-log-with-a-log-profile"></a>A műveletnapló napló profillal exportálása
A **napló profil** a tevékenységnapló exportálásának módját szabályozza. Napló profilt használ, konfigurálhatja:

* Ha a műveletnapló kell küldeni (Tárfiók, vagy az Event Hubs)
* Esemény kategóriák (írási, törlés, művelete) kell küldeni. *"Kategória" napló profilok és tevékenységnapló események szerinti nem egyezik. A napló profil "Kategória" jelenti. a művelet típusa (írási, törlés, művelete). Tevékenységnapló esetben a "kategória" tulajdonság jelöli, a forrás- vagy típusú eseményeket (például felügyeleti ServiceHealth, riasztás és több).*
* Mely régiókat (hely) exportálja. Ügyeljen arra, hogy tartalmazzák "globális", vagy a műveletnaplóban sok eseményt sem globális események.
* Mennyi ideig a műveletnapló rendszer meddig őrizze meg a Storage-fiók.
    - Egy nulla napos megőrzési azt jelenti, hogy a naplók végtelen tartanak. Ellenkező esetben a értéke lehet bármely 1 és 2147483647 között eltelt napok számát.
    - Ha a megőrzési házirend-beállításokat, de naplók tárolása a Storage-fiók le van tiltva, (például, ha csak az Event Hubs vagy Naplóelemzési beállítások vannak jelölve), az adatmegőrzési hatástalan.
    - Adatmegőrzési alkalmazott napi,, így napi (UTC) szerint naplókat, amelyik most már a megőrzési túl napjától végén házirend törlődnek. Például ha egy nap adatmegőrzési, mai nap kezdetén a napló, a nap előtt tegnap törlése akkor történik meg. A törlési folyamat kezdődik éjfél UTC, de vegye figyelembe, hogy törli a tárfiókot az naplók akár 24 óráig is eltarthat.

A tárolási fiók vagy esemény hub névtér, amely nem ugyanahhoz az előfizetéshez, mint a naplók kibocsátó is használhatja. A beállítás konfiguráló felhasználónak mindkét előfizetéshez megfelelő RBAC hozzáféréssel kell rendelkeznie.

> [!NOTE]
>  Jelenleg nem archiválhatja adatok tárolási funkciókat biztosító fiókot, amellyel mögött egy védett virtuális hálózatot.

Ezek a beállítások a "Export" lehetőséget a tevékenységnapló panelen a portálon keresztül is konfigurálható. Akkor is konfigurálható programozott módon [a Azure REST API használatával](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-parancsmagokkal vagy a parancssori felület. Előfizetés csak egy naplófájl-profillal rendelkezhet.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Az Azure portál használatával napló profilok konfigurálása
A műveletnapló az Eseményközpontba adatfolyam, vagy az Azure-portálon a "Export" beállítás használatával tárolhatja őket egy Tárfiókot.

1. Navigáljon a **tevékenységnapló** a portál bal oldalán a menü segítségével.

    ![Tevékenységnapló navigáljon a portálon](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Kattintson a **exportálása** gomb a panel tetején.

    ![A portál Exportálás gomb](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. A megjelenő panelen közül választhat:  
  * régiók, amelynek szeretné események exportálása
  * a Storage-fiók, amelyet szeretne menteni események
  * szeretné megőrizni ezeket az eseményeket, megőrzés napok száma. 0 nap beállítását végtelen őrzi meg a naplókat.
  * a Service Bus Namespace, amelyben szeretné létrehozni az adatfolyamként történő ezeket az eseményeket egy Eseményközpontba.

     ![Exportálás tevékenységnapló panel](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Kattintson a **mentése** ezek a beállítások mentéséhez. A rendszer azonnal alkalmazni fogja a beállításokat az előfizetésére.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Az Azure PowerShell-parancsmagok használatával napló profilok konfigurálása

#### <a name="get-existing-log-profile"></a>Meglévő napló profil beolvasása

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Napló profil hozzáadása

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Name (Név) |Igen |A napló profil neve. |
| StorageAccountId |Nem |Erőforrás-azonosító a tárfiók a műveletnapló mentésére. |
| serviceBusRuleId |Nem |Service Bus-Szabályazonosító a Service Bus-névtér szeretné létrehozni az event hubs rendelkezik. Egy karakterlánc, ebben a formátumban: `{service bus resource ID}/authorizationrules/{key name}`. |
| Helyek |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeinek gyűjtése vesszővel tagolt listája. |
| retentionInDays |Igen |Az eseményeket meg kell őrizni, 1 és 2147483647 közötti napok számát. A nulla érték a naplók határozatlan ideig tárolja (végtelen). |
| Kategóriák |Nem |Be kell esemény kategóriák vesszővel tagolt listája. Lehetséges értékek a következők: Olvasás, törlés és művelet. |

#### <a name="remove-a-log-profile"></a>Napló-profil eltávolítása
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli-20"></a>Az Azure CLI 2.0 használatával napló profilok konfigurálása

#### <a name="get-existing-log-profile"></a>Meglévő napló profil beolvasása

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

A `name` tulajdonság a napló profil nevének kell lennie.

#### <a name="add-a-log-profile"></a>Napló profil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Az a figyelő profilok létrehozása a parancssori felület a teljes dokumentációjáért lásd: a [CLI parancsdokumentációja](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Napló-profil eltávolítása

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>További lépések
* [További információ a tevékenységnapló (korábbi nevén naplók)](../azure-resource-manager/resource-group-audit.md)
* [Az Event hubs Azure tevékenységnapló adatfolyam](monitoring-stream-activity-logs-event-hubs.md)
