---
title: Az Azure tevékenységnapló áttekintése
description: Ismerje meg, mi az az Azure-tevékenységnapló, és hogyan használhatja azt az Azure-előfizetésen belül bekövetkező események megértéséhez.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 2fc09ccdf68605e444ed4b196162df6205557272
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002103"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Az Azure-tevékenységnapló-előfizetési tevékenység figyelése

A **Azure-tevékenységnapló** van egy előfizetési napló, amely az Azure-ban bekövetkezett események előfizetés-szintű betekintést nyújt. Ez magában foglalja az adatok az Azure Resource Manager frissítéseket a Service Health-események operatív adatok széles. A tevékenységnapló nevezték "Naplófájlok" vagy "Műveleti naplók," a felügyeleti kategória jelentések vezérlősík események az előfizetésekre vonatkozó óta. A tevékenységnapló használatával megadhatja, hogy a "mit, ki, és mikor" írási műveletek (PUT, POST, DELETE) tett erőforrásokra az előfizetésben. A művelet és az egyéb releváns tulajdonságok állapotát is ismernie is. A tevékenységnapló olvasási (GET) műveleteket, illetve a klasszikus erőforrások műveletei nem tartalmaz / "RDFE" modellt.

![Tevékenység-naplók és egyéb típusú naplók ](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)

1. ábra: Tevékenység-naplók és egyéb típusú naplók

A tevékenységnapló eltér [diagnosztikai naplók](diagnostic-logs-overview.md). A Tevékenységnaplók adatait a műveletek egy erőforráson kívülről (a "vezérlősík"). Diagnosztikai naplók az erőforrás által kibocsátott vannak, és adja meg a műveletet az erőforrás (az "adatsík") kapcsolatos információkat.

> [!WARNING]
> Az Azure-tevékenységnapló elsősorban az Azure Resource Manager-tevékenységek szól. A klasszikus/RDFE modellt használó erőforrások nem követi nyomon. Néhány klasszikus erőforrástípusok rendelkezik egy proxykiszolgáló erőforrás-szolgáltató az Azure Resource Manager (például Microsoft.ClassicCompute). Klasszikus erőforrástípust Azure Resource Manageren keresztül ezek proxy erőforrás-szolgáltatók használata kezelheti, ha a műveletek a tevékenységnaplóban jelennek meg. Egy klasszikus erőforrástípus kívül az Azure Resource Manager-proxyk dolgozhat, ha a műveletek csak a műveleti napló elszámolni. A műveleti napló egy külön szakaszban a portál tallózható.
>
>

A Tevékenységnaplót, az Azure portal, a parancssori felület, a PowerShell-parancsmagok használatával lehet lekérdezni események és az Azure Monitor REST API-t.

> [!NOTE]
> [Az újabb riasztások](../../azure-monitor/platform/alerts-overview.md) ajánlat hatékonyabb, ha jelentkeznek be riasztási szabályok létrehozása és kezelése a tevékenység.  [További információk](../../azure-monitor/platform/alerts-activity-log.md).


## <a name="categories-in-the-activity-log"></a>Kategóriák a tevékenységnaplóban
A tevékenységnapló megjelenítése és adatokat. További részletek a sémák szerializálása e kategóriák közül a [ebben a cikkben](../../azure-monitor/platform/activity-log-schema.md). Ezek a következők:
* **Felügyeleti** – ezt a kategóriát tartalmazza az összes rekordot létrehozni, frissítési, törlési és műveleti műveleteket hajtja végre a Resource Manageren keresztül. Milyen típusú itt jelennének meg ebbe a kategóriába tartozó eseményeket például a "virtuális gép létrehozása" és "hálózati biztonsági csoport törlése" minden felhasználó vagy alkalmazás használatával a Resource Manager által végrehajtott műveletek az egyes erőforrástípusok műveletként van modellezve. Ha a művelet típusa, Write, Delete vagy műveletet, a rekordokat a kezdő és a sikeres vagy sikertelen a művelet rögzítve lesznek a felügyeleti kategória. A felügyeleti kategória is módosítania kellene a szerepköralapú hozzáférés-vezérlés az előfizetéshez.
* **A Service Health** – Ez a kategória tartalmazza a service health az Azure-ban bekövetkezett események rekord. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "SQL Azure, az USA keleti régiójában üzemen kívül van." A Szolgáltatásállapot-események az öt fajta származnak: Beavatkozás szükséges, helyreállítási eszközkapcsolat, incidens, karbantartási, adatokat vagy biztonsági, és csak jelenik meg, ha egy erőforráshoz lenne hatással lehet az esemény-előfizetés.
* **A Resource Health** – Ez a kategória tartalmazza azt a rekordot a az Azure-erőforrások előfordult resource health események. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "Virtuális gép állapot nem érhető el értékre módosult." Közelmúltbeli állapotesemények hozhat létre négy egészségügyi állapotok egyike: Elérhető, nem érhető el, csökkentett teljesítményű vagy ismeretlen. Emellett a resource health-események csoportosíthatók, hogy a Platform vagy a felhasználó által kezdeményezett.
* **Riasztási** – Ez a kategória összes aktiválás az Azure-riasztások rekordja látható. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "százalékos processzorhasználatról a myVM már több mint 80-as az elmúlt 5 percben." Azure rendszerek különböző rendelkezik egy riasztási elv – valamilyen szabály meghatározása, és értesítést kaphat, ha a feltétel egyezik az szabályokat. Minden alkalommal, amikor egy támogatott Azure riasztástípus "aktiválja,", vagy értesítést hozhat létre, hogy a feltételek teljesülnek, egy rekord, az aktiválás szintén leküldéssel ebbe a kategóriába, a tevékenységnapló.
* **Automatikus skálázási** – Ez a kategória tartalmazza a rekord a meghatározott az előfizetés automatikus skálázási beállítások alapján automatikus skálázási motor a művelethez kapcsolódó események. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "Automatikus vertikális felskálázási művelet sikertelen volt." Használja az automatikus méretezés, akkor lehet automatikusan horizontálisan felskálázhatja vagy leskálázhatja a támogatott erőforrástípus a példányok számának napi és/vagy terhelési () metrikaadatok egy automatikus skálázási beállítás használatával ideje alapján. Ha a feltételek teljesülnek felfelé vagy lefelé méretezés, a kezdő és a sikeres és sikertelen események ebbe a kategóriába vannak rögzítve.
* **Az ajánlás** – Ez a kategória javaslattételi események az Azure Advisor tartalmaz.
* **Biztonsági** – Ez a kategória tartalmazza a rekord minden olyan Azure Security Center által generált riasztások. Itt jelennének meg ebbe a kategóriába tartozó esemény típusa például a "a program kettős kiterjesztésű gyanús fájlt futtatott."
* **A házirend** – Ez a kategória az Azure Policy által végrehajtott összes érvénybe művelet művelet rekordokat tartalmaz. Milyen típusú itt jelennének meg ebbe a kategóriába tartozó eseményeket közé a naplózási és a Megtagadás. Minden szabályzat által elvégzett művelet egy erőforrás-művelet van modellezve.

## <a name="event-schema-per-category"></a>Kategória szerinti eseménysémája
[Ebből a cikkből megismerheti a tevékenységnapló eseménysémája kategória szerinti megtekintéséhez.](../../azure-monitor/platform/activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Mire képes az Activity Log a
Az alábbiakban néhány, a tevékenységnapló megteheti:

![Azure-tevékenységnapló](./media/activity-logs-overview/Activity_Log_Overview_v3.png)


* Lekérdezni és megtekinteni azt a a **az Azure portal**.
* [Egy tevékenységnapló eseményéhez, hozzon létre egy riasztást.](../../azure-monitor/platform/activity-log-alerts.md)
* [Stream, hogy egy **Eseményközpont** ](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldással, például a Power bi támogatunk.
* A Power bi használatával elemezhetők a [ **Power bi-tartalomcsomag**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Mentse azt egy **Tárfiók** archív vagy manuális ellenőrzést](../../azure-monitor/platform/archive-activity-log.md). A megőrzési ideje (nap) használatával is megadhat a **Naplóprofil**.
* PowerShell-parancsmagot, a CLI vagy a REST API-n keresztül lekérdezéseket futtathat rajta.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Lekérdezés a tevékenységnapló az Azure Portalon
Az Azure Portalon a tevékenységnapló több helyen is megtekintheti:
* A **tevékenységnapló** , így hozzáférhet a tevékenységnaplóban a Keresés **minden szolgáltatás** a bal oldali navigációs ablaktáblán.
* **A figyelő** a bal oldali navigációs ablaktáblán alapértelmezés szerint megjelenik. A tevékenységnapló Azure Monitor egy szakaszában.
* A legtöbb **erőforrások**, például a virtuális gép konfigurációs panel. A tevékenységnaplóban a legtöbb erőforráspanelek egy szakaszt, és kattintással automatikusan szűri az eseményeket csak az adott erőforráshoz kapcsolódó.

Az Azure Portalon a Tevékenységnaplót, ezek a mezők szerint szűrheti:
* Időtartomány - események kezdő és befejező időpontját.
* Kategória – az esemény kategóriáját fent leírtak szerint.
* Előfizetés – egy vagy több Azure-előfizetés nevét.
* Erőforráscsoport – egy vagy több erőforrás csoportosítja ezen előfizetések keretében.
* Erőforrás (név) – egy adott erőforrás nevét.
* Erőforrástípus - erőforrás, például Microsoft.Compute/virtualmachines típusát.
* Název operace – egy Azure Resource Manager-műveletet, például Microsoft.SQL/servers/Write nevére.
* Súlyosság – az a súlyossági szintet az esemény (tájékoztatás, figyelmeztetés, hiba, kritikus).
* Esemény kezdeményezője – a "hívó" vagy a műveletet végrehajtó felhasználó.
* Keresés megnyitása – Ez az egy megnyitott szövegkeresési mező, amelyek között az összes esemény található összes mezőhöz adott karakterláncot keres.

Miután beállított szűrők csoportja, mindig kövesse figyelemmel az adott események az Azure-irányítópulton való lekérdezés is rögzíthet.

Még hatékonyabbá elemre a **naplók** ikont, mely megjeleníti a tevékenységnapló adatait a [összegyűjtheti és elemezheti a tevékenységeket tartalmazó naplók megoldás](../../azure-monitor/platform/collect-activity-logs.md). A tevékenységnapló panel kínál egy alapszintű szűrő/tallózási élménynek a naplókat, de az Azure Monitor naplók funkció lehetővé teszi, hogy forgáspont, lekérdezése és hatékonyabb módon jelenítheti meg az adatokat.

## <a name="export-the-activity-log-with-a-log-profile"></a>A napló profillal tevékenységnapló exportálása
A **Naplóprofil** a tevékenységnapló exportálása hogyan szabályozza. Napló profilt használ, konfigurálhatja:

* Ha a tevékenységnapló kell küldeni (Storage-fiók vagy az Event Hubs)
* Melyik eseménykategóriák (írás, törlés, a művelet) küldendő adattípusokat. *A "category" Naplóprofilok és tevékenységnapló eseményei jelentése nem egyezik. A napló-profilban a "Category" a művelet típusa (írás, törlés, a művelet) jelöli. Egy tevékenységnapló-esemény a "category" tulajdonság jelöli a forrás- vagy típusú eseményeket (például felügyeleti ServiceHealth, riasztást és több).*
* Mely régiókban (helyeken) exportálja. Ügyeljen arra, hogy tartalmaznak "globális", mivel a tevékenységnaplóban sok eseményt globális események.
* Mennyi ideig a tevékenységnapló fenn kell tartani a Storage-fiókban.
    - Egy nulla napnyi adatmegőrzéshez azt jelenti, hogy naplókat tartják örökre. Ellenkező esetben az érték lehet minden olyan 1 és 2147483647 között eltelt napok számát.
    - Ha a megőrzési házirend-beállításokat, de a naplók tárolása a Storage-fiók le van tiltva, (például, ha csak az Event Hubs és a Log Analytics lehetőség be van jelölve), az adatmegőrzési szabályzatok nem befolyásolják.
    - Adatmegőrzési házirendek, az alkalmazott napi, hogy naponta (UTC), naplók, amely mostantól a megőrzési ideje meghaladja a nap végén törli a házirendet. Például ha egy nap adatmegőrzési, ma a nap kezdetén az a napja előtt tegnap naplóinak törlődnének. A törlési folyamat kezdődik UTC szerint éjfélig, de vegye figyelembe, hogy a naplók a tárfiókból a törlendő akár 24 órát is igénybe vehet.

Egy tároló vagy egy eseményközpontba eseményközpont-névtér, amely nem ugyanabban az előfizetésben, mint a naplókat kibocsátó is használhatja. A beállítást konfiguráló felhasználónak rendelkeznie kell a megfelelő RBAC-hozzáférés mindkét előfizetéshez.

> [!NOTE]
>  Jelenleg nem archiválhatja egy tárfiókot, amelyet mögött egy biztonságos virtuális hálózaton található adatokat.

> [!WARNING]
> A naplóadatok JSON sorok 2018. november 1-től változik a tárfiókban formátumát. [Ebben a cikkben olvashat ennek hatásairól, valamint arról, hogy hogyan frissítheti eszközeit az új formátum kezeléséhez.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

Ezek a beállítások az "Export" lehetőséget a tevékenységnapló panel a portálon keresztül konfigurálható. Akkor is konfigurálható programozott módon [az Azure Monitor REST API használatával](https://msdn.microsoft.com/library/azure/dn931927.aspx), PowerShell-parancsmagok vagy a parancssori felület. Egy előfizetéshez csak egy naplóprofil rendelkezhet.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Az Azure portal használatával naplóprofilok konfigurálása
A tevékenységnapló streamelése az Eseményközpontba, vagy a "Exportálás az Event Hubs" lehetőség az Azure Portal használatával egy tárfiókban tárolja őket.

1. Navigáljon a **tevékenységnapló** menüjének használatával, a portál bal oldalán.

    ![Tevékenységnapló navigáljon a portálon](./media/activity-logs-overview/activity-logs-portal-navigate-v2.png)
2. Kattintson a **exportálás eseményközpontba** gombra a panel tetején.

    ![A portál Exportálás gomb](./media/activity-logs-overview/activity-logs-portal-export-v2.png)
3. A megjelenő panelen választhatja:  
  * régiók, amelynek szeretné exportálni az események
  * a Storage-fiókot, amelyhez szeretné menteni az események
  * nap meg szeretné őrizni a storage-ban ezek az események száma. 0 napra beállítását örökre megőrzi a naplókat.
  * a Service Bus Namespace, amelyben szeretné létrehozni a streamelési ezeket az eseményeket egy Eseményközpontba.

     ![Tevékenységnapló panel exportálása](./media/activity-logs-overview/activity-logs-portal-export-blade.png)
4. Kattintson a **mentése** ezek a beállítások mentéséhez. A rendszer azonnal alkalmazni fogja a beállításokat az előfizetésére.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Az Azure PowerShell-parancsmagok használatával naplóprofilok konfigurálása

#### <a name="get-existing-log-profile"></a>Meglévő log profil beolvasása

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Napló profil hozzáadása

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Tulajdonság | Szükséges | Leírás |
| --- | --- | --- |
| Name (Név) |Igen |A napló-profil neve. |
| StorageAccountId |Nem |Erőforrás-azonosító, amelyhez a tevékenységnapló menteni a tárfiók. |
| serviceBusRuleId |Nem |Service Bus Szabályazonosító a Service Bus-névtér szeretné, hogy a létrehozott event hubs. Egy karakterlánc, a következő formátumban: `{service bus resource ID}/authorizationrules/{key name}`. |
| Hely |Igen |Régiók, amelynek szeretné tevékenységnapló eseményeket gyűjtő vesszővel tagolt listája. |
| RetentionInDays |Igen |Mely eseményeket meg kell őrizni, 1 és 2147483647 között eltelt napok száma. A nulla érték határozatlan ideig tárolja a naplók (végtelen). |
| Kategória |Nem |Eseménykategóriák kell gyűjteni, vesszővel tagolt listája. Lehetséges értékek: írási, törlési és művelet. |

#### <a name="remove-a-log-profile"></a>Napló profil eltávolítása
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Az Azure CLI-vel naplóprofilok konfigurálása

#### <a name="get-existing-log-profile"></a>Meglévő log profil beolvasása

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

A `name` tulajdonságot kell lennie a napló-profil nevét.

#### <a name="add-a-log-profile"></a>Napló profil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

A figyelő profilok létrehozása a CLI-vel teljes dokumentációjáért lásd: a [CLI parancsdokumentációja](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Napló profil eltávolítása

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>További lépések
* [További információ a tevékenységnapló (korábbi nevén Auditnaplók)](../../azure-resource-manager/resource-group-audit.md)
* [Az Event hubs Azure tevékenységnapló Stream](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)

