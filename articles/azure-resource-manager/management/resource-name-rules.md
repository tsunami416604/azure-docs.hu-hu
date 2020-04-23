---
title: Erőforrás-elnevezési korlátozások
description: Az Azure-erőforrások elnevezésére vonatkozó szabályokat és korlátozásokat jeleníti meg.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: fab1ab2bb779b3826c852e49da7970030d34594d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086385"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Az Azure-erőforrásokra vonatkozó elnevezési szabályok és korlátozások

Ez a cikk összegzi az Azure-erőforrások elnevezési szabályait és korlátozásait. Az erőforrások elnevezésével kapcsolatos javaslatokaz [Ajánlott elnevezési és címkézési konvenciók](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)című témakörben vannak.

Az erőforrásnevek nem különböznek a kis- és nagybetűktől, kivéve, ha az érvényes karakterek oszlopban kifejezetten fel van jelölve.

A következő táblázatokban az alfanumerikus kifejezés a következőkre utal:

* **a-tól** **z-ig** (kisbetűk)
* **A-tól** **Z-ig** (nagybetűk)
* **0-tól** **9-ig** (számok)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | erőforráscsoport | 3-63 | Kisbetűk és számok.<br><br>Kezdje kisbetűvel. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | szolgáltatás | Globális | 1-50 | Alfanumerikus.<br><br>Kezdjük a levéllel. |
> | szolgáltatás / apis | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / kérdések | api-t | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / kérdések / mellékletek | Kérdés | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / kérdések / hozzászólások | Kérdés | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / műveletek | api-t | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / műveletek / címkék | Művelet | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / kiadások | api-t | 1–80 | Alfanumerikus jelek, aláhúzások és kötőjelek.<br><br>A kezdés és a befejezés alfanumerikus vagy aláhúzásjellel. |
> | szolgáltatás / apis / sémák | api-t | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / tagDescriptions | api-t | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / címkék | api-t | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / api-version-készletek | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / authorizationServers | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / háttérrendszerek | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / tanúsítványok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / diagnosztika | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / csoportok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / csoportok / felhasználók | group | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / identityProviders | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / naplózók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / értesítések | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / értesítések / címzettEmailek | értesítés | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / openidConnectProviders | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / házirendek | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / termékek | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / termékek / apis | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / termékek / csoportok | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / termékek / címkék | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / tulajdonságok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / előfizetések | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / címkék | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / sablonok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / felhasználók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | configurationStores (konfigurációRaktárak) | erőforráscsoport | 5-50 | Alfanumerikus jelek, aláhúzások és kötőjelek. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Zárak | a megbízás hatálya | 1–90 | Alfanumerikus, pont, aláhúzás, kötőjel és zárójel.<br><br>Nem lehet pontban végződni. |
> | házirend-hozzárendelések | a megbízás hatálya | 1-128 megjelenítendő név<br><br>1-260 erőforrás neve | A megjelenítendő név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve `%` nem szerepelhet, és nem végződhet pontokkal vagy térközrel. |
> | politikai definíciók | a fogalommeghatározás hatálya | 1-128 megjelenítendő név<br><br>1-260 erőforrás neve | A megjelenítendő név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve `%` nem szerepelhet, és nem végződhet pontokkal vagy térközrel. |
> | policySetDefinitions (policySetDefinitions) | a fogalommeghatározás hatálya | 1-128 megjelenítendő név<br><br>1-260 erőforrás neve | A megjelenítendő név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve `%` nem szerepelhet, és nem végződhet pontokkal vagy térközrel.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | automationAccounts | erőforráscsoport | 6-50 | Alfanumerikus és kötőjelek.<br><br>Kezdje betűvel, és végződjön alfanumerikussal. |
> | automationAccounts / tanúsítványok | automatizálási fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem lehet térrel végződni.  |
> | automationAccounts / kapcsolatok | automatizálási fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem lehet térrel végződni. |
> | automationAccounts / hitelesítő adatok | automatizálási fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem lehet térrel végződni. |
> | automationAccounts / runbookok | automatizálási fiók | 1–63 | Alfanumerikus jelek, aláhúzások és kötőjelek.<br><br>Kezdjük a levéllel.  |
> | automationAccounts / ütemezések | automatizálási fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem lehet térrel végződni. |
> | automationAccounts / változók | automatizálási fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem lehet térrel végződni. |
> | automationAccounts / figyelők | automatizálási fiók | 1–63 |  Alfanumerikus jelek, aláhúzások és kötőjelek.<br><br>Kezdjük a levéllel. |
> | automationAccounts / webhooks | automatizálási fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem lehet térrel végződni. |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | batchAccounts | Régió | 3–24 | Kisbetűk és számok. |
> | batchAccounts / alkalmazások | kötegszámla | 1-64 | Alfanumerikus jelek, aláhúzások és kötőjelek. |
> | batchAccounts / tanúsítványok | kötegszámla | 5-45 | Alfanumerikus jelek, aláhúzások és kötőjelek. |
> | batchAccounts / készletek | kötegszámla | 1-64 | Alfanumerikus jelek, aláhúzások és kötőjelek. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | blockchainMembers | Globális | 2-20 | Kisbetűk és számok.<br><br>Kezdje kisbetűvel. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | botServices (botServices) | Globális | 2–64 |  Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. |
> | botServices / csatornák | bot szolgáltatás | 2–64 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. |
> | botServices / Kapcsolatok | bot szolgáltatás | 2–64 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. |
> | enterpriseChannels | erőforráscsoport | 2–64 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Redis | Globális | 1–63 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. Egymást követő kötőjelek nem engedélyezettek. |
> | Redis / firewallRules | Redis | 1-256 | Alfanumerikus |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Profilok | erőforráscsoport | 1-260 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |
> | profilok / végpontok | Globális | 1-50 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |

## <a name="microsoftcertificateregistration"></a>Microsoft.Tanúsítványregisztráció

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | certificateOrders (tanúsítványRendelések) | erőforráscsoport | 3-30 | Alfanumerikus. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | erőforráscsoport | 2–64 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | availabilitySets | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvégződésű befejezés. |
> | diskEncryptionSets | erőforráscsoport | 1–80 | Alfanumerikus és aláhúzás. |
> | Lemezek | erőforráscsoport | 1–80 | Alfanumerikus és aláhúzás. |
> | Galériák | erőforráscsoport | 1–80 | Alfanumerikus és pont.<br><br>Kezdje és fejezze be alfanumerikus. |
> | galériák / alkalmazások | katalógus | 1–80 | Alfanumerikus, kötőjelek és pont.<br><br>Kezdje és fejezze be alfanumerikus. |
> | galériák / alkalmazások/verziók | Alkalmazás | 32 bites egész szám | Számok és időszakok. |
> | galériák / képek | katalógus | 1–80 | Alfanumerikus, kötőjelek és pont.<br><br>Kezdje és fejezze be alfanumerikus. |
> | galériák / képek / verziók | image | 32 bites egész szám | Számok és időszakok. |
> | images | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvégződésű befejezés. |
> | pillanatképek | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvégződésű befejezés. |
> | virtualMachines | erőforráscsoport | 1-15 (Windows)<br>1-64 (Linux)<br><br>Lásd az alábbi megjegyzést. | Nem használható:<br> `\/""[]:|<>+=;,?*@&`<br><br>Nem lehet hangsúlyozni. Nem végződhet pontvagy kötőjel. |
> | virtualMachineScaleSets | erőforráscsoport | 1-15 (Windows)<br>1-64 (Linux)<br><br>Lásd az alábbi megjegyzést. | Nem használható:<br> `\/""[]:|<>+=;,?*@&`<br><br>Nem lehet hangsúlyozni. Nem végződhet pontvagy kötőjel. |

> [!NOTE]
> Az Azure virtuális gépek két különböző névvel rendelkeznek: az erőforrás neve és az állomásnév. Amikor virtuális gépet hoz létre a portálon, mindkét névhez ugyanaz t használja a függvény. Az előző táblában szereplő korlátozások az állomásnévre vonatkoznak. A tényleges erőforrásnév legfeljebb 64 karakterből állhat.

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | containerGroups (tárolócsoportok) | erőforráscsoport | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. Szomszédos kötőjelek használata nem megengedett. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Nyilvántartások | Globális | 5-50 | Alfanumerikus. |
> | nyilvántartások / buildTasks | registry | 5-50 | Alfanumerikus. |
> | nyilvántartások / buildTasks/steps | build feladat | 5-50 | Alfanumerikus. |
> | nyilvántartások / replikációk | registry | 5-50 | Alfanumerikus. |
> | nyilvántartások / hatókörTérképek | registry | 5-50 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | nyilvántartások / feladatok | registry | 5-50 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | nyilvántartások / tokenek | registry | 5-50 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | nyilvántartások / webhookok | registry | 5-50 | Alfanumerikus. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | managedClusters (kezelt fürtök) | erőforráscsoport | 1–63 | Alfanumerikus jelek, aláhúzások és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |
> | openShiftManagedClusters | erőforráscsoport | 1-30 | Alfanumerikus. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Csomópontok | erőforráscsoport | 1-64 | Alfanumerikus.<br><br>Kezdjük a levéllel.  |
> | hubok / authorizationPolicies | Hub | 1-50 | Alfanumerikus, aláhúzásjelek és pont.<br><br>Kezdje és fejezze be alfanumerikus. |
> | elosztók / csatlakozók | Hub | 1-128 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | elosztók / összekötők/leképezések | összekötő | 1-128 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / interakciók | Hub | 1-128 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | elosztók / kpi | Hub | 1-512 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / linkek | Hub | 1-512 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / előrejelzések | Hub | 1-512 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / profilok | Hub | 1-128 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / kapcsolatokLinkek | Hub | 1-512 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / kapcsolatok | Hub | 1-512 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / roleAssignments | Hub | 1-128 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |
> | hubok / nézetek | Hub | 1-512 | Alfanumerikus és aláhúzás.<br><br>Kezdjük a levéllel. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Egyesületek | erőforráscsoport | 1-180 | Nem használható:<br>`%&\\?/`<br><br>Nem lehet pontokkal vagy szóköznel végződni. |
> | erőforrás-szolgáltatók | erőforráscsoport | 3-64 | Nem használható:<br>`%&\\?/`<br><br>Nem lehet pontokkal vagy szóköznel végződni. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Munkahelyek | erőforráscsoport | 3–24 | Alfanumerikus, kötőjelek, aláhúzások és pont. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | munkaterületek | erőforráscsoport | 3-30 | Alfanumerikus jelek, aláhúzásjelek és kötőjelek |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Gyárak | Globális | 3-63 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |
> | gyárak / adatfolyamok | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdjük alfanumerikus. |
> | gyárak / adatkészletek | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdjük alfanumerikus. |
> | gyárak / integrációRuntimes | Gyár | 3-63 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |
> | gyárak / kapcsolt szolgáltatások | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdjük alfanumerikus. |
> | gyárak / csővezetékek | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdjük alfanumerikus. |
> | gyárak / triggerek | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdjük alfanumerikus. |
> | gyárak / triggerek / rerunTriggers | Ravaszt | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdjük alfanumerikus. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | Globális | 3–24 | Kisbetűk és számok. |
> | számlák / computePolicies | account | 3-60 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | fiókok / dataLakeStoreAccounts | account | 3–24 | Kisbetűk és számok. |
> | számlák / firewallRules | account | 3-50 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | fiókok / storageAccounts | account | 3-60 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | Globális | 3–24 | Kisbetűk és számok. |
> | számlák / firewallRules | account | 3-50 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | számlák / virtualNetworkRules | account | 3-50 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | services | erőforráscsoport | 2-62 | Alfanumerikus, kötőjelek, pont és aláhúzás.<br><br>Kezdjük alfanumerikus. |
> | szolgáltatások / projektek | szolgáltatás | 2-57 | Alfanumerikus, kötőjelek, pont és aláhúzás.<br><br>Kezdjük alfanumerikus. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | Globális | 3-63 | Kisbetűk, kötőjelek és számok.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. |
> | szerverek / adatbázisok | Szerverek | 1–63 | Alfanumerikus és kötőjelek. |
> | szerverek / firewallRules | Szerverek | 1-128 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | szerverek / virtualNetworkRules | Szerverek | 1-128 | Alfanumerikus és kötőjelek. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | Globális | 3-63 | Kisbetűk, kötőjelek és számok.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. |
> | szerverek / adatbázisok | Szerverek | 1–63 | Alfanumerikus és kötőjelek. |
> | szerverek / firewallRules | Szerverek | 1-128 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | szerverek / virtualNetworkRules | Szerverek | 1-128 | Alfanumerikus és kötőjelek. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | Globális | 3-63 | Kisbetűk, kötőjelek és számok.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. |
> | szerverek / adatbázisok | Szerverek | 1–63 | Alfanumerikus és kötőjelek. |
> | szerverek / firewallRules | Szerverek | 1-128 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | szerverek / virtualNetworkRules | Szerverek | 1-128 | Alfanumerikus és kötőjelek. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | IotHubok | Globális | 3-50 | Alfanumerikus és kötőjelek.<br><br>Nem végződhet kötőjellel. |
> | IotHubs / tanúsítványok | IoT-központ | 1-64 | Alfanumerikus, kötőjelek, pont és aláhúzás. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumerikus, kötőjelek, pont és aláhúzás. |
> | provisioningServices (szolgáltatások kiépítése) | erőforráscsoport | 3-64 | Alfanumerikus és kötőjelek.<br><br>Alfanumerikus végződéssel végződik. |
> | provisioningServices / tanúsítványok | provisioningServices (szolgáltatások kiépítése) | 1-64 | Alfanumerikus, kötőjelek, pont és aláhúzás. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Labs | erőforráscsoport | 1-50 | Alfanumerikus jelek, aláhúzások és kötőjelek. |
> | laborok / customimages | Labor | 1–80 | Alfanumerikus, aláhúzás, kötőjel és zárójel. |
> | laborok / képletek | Labor | 1–80 | Alfanumerikus, aláhúzás, kötőjel és zárójel. |
> | laborok / virtualmachines | Labor | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. Nem lehet minden szám. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | adatbázis-fiókok | Globális | 3-31 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel vagy számmal. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Tartományok | erőforráscsoport | 3-50 | Alfanumerikus és kötőjelek. |
> | domainek / témák | domain | 3-50 | Alfanumerikus és kötőjelek. |
> | eventElőfizetések | erőforráscsoport | 3-64 | Alfanumerikus és kötőjelek. |
> | Témák | erőforráscsoport | 3-50 | Alfanumerikus és kötőjelek. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | erőforráscsoport | 6-50 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. Levéllel vagy számmal végződik. |
> | Névterek | Globális | 6-50 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. Levéllel vagy számmal végződik. |
> | névterek / AuthorizationRules | névtér | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje betűvel vagy számmal. |
> | névterek / disasterRecoveryConfigs | névtér | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje betűvel vagy számmal. |
> | névterek / eventhubs | névtér | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje betűvel vagy számmal. |
> | névterek / eventhubs / authorizationRules | eseményközpont | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje betűvel vagy számmal. |
> | névterek / eventhubs / fogyasztói csoportok | eseményközpont | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje betűvel vagy számmal. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | Globális | 3-59 | Alfanumerikus és kötőjelek<br><br>Kezdje betűvel vagy számmal. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Munkahelyek | erőforráscsoport | 2–64 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Összetevők | erőforráscsoport | 1-260 | Nem használható:<br>`%&\?/` <br><br>Nem lehet szóköznel vagy ponttal végződni.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | IoTApps | Globális | 2-63 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel vagy számmal. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Boltívek | Globális | 3–24 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. A vége betűvel vagy számjegyjellel. Egymást követő kötőjeleket nem tartalmazhat. |
> | boltívek / titkok | Tároló | 1-127 | Alfanumerikus és kötőjelek. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | Globális | 4-22 | Kisbetűk és számok.<br><br>Kezdjük a levéllel. |
> | /clusters / adatbázisok | cluster | 1-260 | Alfanumerikus jelek, kötőjelek, szóközök és pont. |
> | /clusters / adatbázisok / dataConnections | adatbázis | 1-40 | Alfanumerikus jelek, kötőjelek, szóközök és pont. |
> | /clusters / adatbázisok / eventhubconnections | adatbázis | 1-40 | Alfanumerikus jelek, kötőjelek, szóközök és pont. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | integrációS fiókok | erőforráscsoport | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationAccounts / szerelvények | integrációs fiók | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationAccounts / batchConfigurations | integrációs fiók | 1-20 | Alfanumerikus. |
> | integrationAccounts / tanúsítványok | integrációs fiók | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationAccounts / térképek | integrációs fiók | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationAccounts / partnerek | integrációs fiók | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationAccounts / rosettanetprocessconfigurations | integrációs fiók | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationAccounts / sémák | integrációs fiók | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationAccounts / munkamenetek | integrációs fiók | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |
> | integrationServiceEnvironments környezetek | erőforráscsoport | 1–80 | Alfanumerikus, kötőjelek, pont és aláhúzás. |
> | integrationServiceEnvironments / managedApis | integrációs szolgáltatási környezet | 1–80 | Alfanumerikus, kötőjelek, pont és aláhúzás. |
> | Munkafolyamatok | erőforráscsoport | 1–80 | Alfanumerikus, kötőjel, aláhúzás, pont és zárójel. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kötelezettségvállalásTervek | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet térrel. |
> | webServices | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet térrel. |
> | munkaterületek | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet térrel. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | munkaterületek | erőforráscsoport | 3-33 | Alfanumerikus és kötőjelek. |
> | munkaterületek / számítások | munkaterület | 2-16 | Alfanumerikus és kötőjelek. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | userAssignedIdentities | erőforráscsoport | 3-128 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek<br><br>Kezdje betűvel vagy számmal. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | erőforráscsoport | 1-98 (erőforráscsoport nevéhez és fióknevéhez) | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | médiaszolgáltatások | erőforráscsoport | 3–24 | Kisbetűk és számok. |
> | mediaservices / liveEvents | Médiaszolgáltatás | 1-32 | Alfanumerikus és kötőjelek.<br><br>Kezdjük alfanumerikus. |
> | mediaservices / liveEvents / liveOutputs | Élő esemény | 1-256 | Alfanumerikus és kötőjelek.<br><br>Kezdjük alfanumerikus. |
> | mediaservices / streamingEndpoints | Médiaszolgáltatás | 1-24 | Alfanumerikus és kötőjelek.<br><br>Kezdjük alfanumerikus. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | alkalmazásátjárók | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | alkalmazásSecurityGroups | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | azureTűzfalak | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvégződésű befejezés. |
> | bástyaHázigazdák | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | Kapcsolatok | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | dnsZones (dnsZones) | erőforráscsoport | 1-63 karakter<br><br>2–34 címke<br><br>Minden címke egy ponttól elválasztott karakterkészlet. Contoso.com **például** 2 címkével rendelkezik. | Minden címke tartalmazhat alfanumerikus, aláhúzásjeleket és kötőjeleket.<br><br>Minden címkét egy pont választ el egymástól. |
> | expressRouteCircuits | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | firewallPolicies (tűzfalházirendek) | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | firewallPolicies / ruleGroups | tűzfal házirendje | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | elülső ajtók | Globális | 5-64 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |
> | terheléselosztók | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | loadBalancers / inboundNatRules | terheléselosztó | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | localNetworkGateways | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | hálózati interfészek | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | networkSecurityGroups (hálózatibiztonsági csoportok) | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | networkSecurityGroups / securityRules | hálózati biztonsági csoport | 1–80 |  Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | networkWatchers | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | privátDnsZones | erőforráscsoport | 1-63 karakter<br><br>2–34 címke<br><br>Minden címke egy ponttól elválasztott karakterkészlet. Contoso.com **például** 2 címkével rendelkezik. | Minden címke tartalmazhat alfanumerikus, aláhúzásjeleket és kötőjeleket.<br><br>Minden címkét egy pont választ el egymástól. |
> | privateDnsZones / virtualNetworkLinks | privát DNS-zóna | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | nyilvánosIP-címek | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | nyilvános IPPrefixes | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | routeFilters | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | routeFilters / routeFilterRules | útvonalszűrő | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | routeTables (útvonaltáblák) | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | routeTables / útvonalak | útvonaltábla | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | serviceEndpointPolicies | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | trafficmanagerprofiles (trafficmanagerprofiles) | Globális | 1–63 | Alfanumerikus, kötőjelek és pont.<br><br>Kezdje és fejezze be alfanumerikus. |
> | virtualNetworkGateways | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | virtualNetworks | erőforráscsoport | 2–64 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | virtuális hálózatok / alhálózatok | virtuális hálózat | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | virtualNetworks / virtualNetworkPeerings | virtuális hálózat | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | virtualWans | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | vpnGateways | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | vpnGateways / vpnConnections | VPN-átjáró | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |
> | vpnOldalak | erőforráscsoport | 1–80 | Alfanumerikus, aláhúzás, pont és kötőjel.<br><br>Kezdjük alfanumerikus. Alfanumerikus vagy aláhúzásvége. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Névterek | Globális | 6-50 | Alfanumerikus és kötőjelek<br><br>Kezdje és fejezze be alfanumerikus. |
> | névterek / AuthorizationRules | névtér | 1-256 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje az alfanumerikus értéket. |
> | névterek / értesítésHubs | névtér | 1-260 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje az alfanumerikus értéket. |
> | névterek / értesítésHubs / AuthorizationRules | értesítési központ | 1-256 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje az alfanumerikus értéket. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | erőforráscsoport | 4-63 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |
> | munkaterületek | erőforráscsoport | 4-63 | Alfanumerikus és kötőjelek.<br><br>Kezdje és fejezze be alfanumerikus. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | megoldások | munkaterület | N/A | A Microsoft által készített megoldások esetében a névnek a következő mintában kell lennie:<br>`SolutionType(WorkspaceName)`<br><br>A harmadik felek által készített megoldások esetében a névnek a mintában kell lennie:<br>`SolutionType[WorkspaceName]`<br><br>Egy érvényes név például a következő:<br>`AntiMalware(contoso-IT)`<br><br>A megoldás típusa a kis- és nagybetűk megkülönböztetése. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | irányítópultok | erőforráscsoport | 3-160 | Alfanumerikus és kötőjelek.<br><br>Korlátozott karakterek használatához adjon hozzá egy **rejtett cím** nevű címcímet a használni kívánt irányítópult nevével. A portál megjeleníti ezt a nevet az irányítópult megjelenítésekor. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | workspaceGyűjtemények | régió | 3-63 | Alfanumerikus és kötőjelek.<br><br>Nem lehet kötőjellel kezdeni. Nem használható egymást követő kötőjelek. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Kapacitások | régió | 3-63 | Kisbetűk vagy számok<br><br>Kezdje kisbetűvel. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Boltívek | erőforráscsoport | 2-50 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. |
> | trezorok / backupPolicies | tár | 3-150 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. Nem végződhet kötőjellel. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Névterek | Globális | 6-50 | Alfanumerikus és kötőjelek.<br><br>Kezdje betűvel. Levéllel vagy számmal végződik. |
> | névterek / AuthorizationRules | névtér | 1-50 |  Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be alfanumerikus. |
> | névterek / Hibridkapcsolatok | névtér | 1-260 | Alfanumerikus, pont, kötőjel, aláhúzás és vágás.<br><br>Kezdje és fejezze be alfanumerikus. |
> | névterek / HybridConnections/authorizationRules | hibrid kapcsolat | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be alfanumerikus. |
> | névterek / WcfRelays | névtér | 1-260 | Alfanumerikus, pont, kötőjel, aláhúzás és vágás.<br><br>Kezdje és fejezze be alfanumerikus. |
> | névterek / WcfRelays / authorizationRules | Wcf relé | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be alfanumerikus. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Telepítések | erőforráscsoport | 1-64 | Alfanumerikus, aláhúzás, zárójel, kötőjel és pont. |
> | erőforráscsoportok | előfizetést | 1–90 | A [regex dokumentációjának](/rest/api/resources/resourcegroups/createorupdate)megfelelő alfanumerikus karakterek, aláhúzásjelek, zárójelek, kötőjelek, periódusok és unicode karakterek.<br><br>Nem végződhet pontokkal. |
> | címkeNevek | Erőforrás | 1-512 | Nem használható:<br>`<>%&\?/` |
> | tagNames / tagValues | címke neve | 1-256 | Minden karakter. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Névterek | Globális | 6-50 | Alfanumerikus és kötőjelek.<br><br>Kezdje betűvel. Levéllel vagy számmal végződik.<br><br>További információt a Névtér létrehozása című [témakörben talál.](/rest/api/servicebus/create-namespace) |
> | névterek / AuthorizationRules | névtér | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be az alphnumerinikus. |
> | névterek / disasterRecoveryConfigs | Globális | 6-50 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. Alfanumerikus végződéssel végződik. |
> | névterek / áttelepítésKonfigurációk | névtér |  | Mindig **$default**kell lennie. |
> | névterek / várólisták | névtér | 1-260 | Alfanumerikus, pont, kötőjel, aláhúzás és vágás.<br><br>Kezdje és fejezze be alfanumerikus. |
> | névterek / várólisták / authorizationRules | üzenetsor | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be az alphnumerinikus. |
> | névterek / témakörök | névtér | 1-260 | Alfanumerikus, pont, kötőjel, aláhúzás és vágás.<br><br>Kezdje és fejezze be alfanumerikus. |
> | névterek / témakörök / authorizationRules | témakör | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be az alphnumerinikus. |
> | névterek / témák / előfizetések | témakör | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be az alphnumerinikus. |
> | névterek / témák / előfizetések / szabályok | előfizetést | 1-50 | Alfanumerikus, pont, kötőjel és aláhúzás.<br><br>Kezdje és fejezze be az alphnumerinikus. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | régió | 4-23 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel. A vége kisbetűvel vagy számmal végződik. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | jelző | Globális | 3-63 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. Levéllel vagy számmal végződik.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | managedInstances (példányok) | Globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. |
> | Szerverek | Globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. |
> | szerverek / rendszergazdák | kiszolgáló |  | Kell `ActiveDirectory`. |
> | szerverek / adatbázisok | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:\/?`<br><br>Nem lehet pontokkal vagy szóköznel végződni. |
> | szerverek / adatbázisok / syncGroups | adatbázis | 1-150 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | szerverek / elasticPools | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:\/?`<br><br>Nem lehet pontokkal vagy szóköznel végződni. |
> | kiszolgálók / feladatátvételi csoportok | Globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. |
> | szerverek / firewallRules | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:;\/?`<br><br>Nem végződhet pontokkal. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | storageAccounts | Globális | 3–24 | Kisbetűk és számok. |
> | storageAccounts / blobServices | tárfiók |  | Kell `default`. |
> | storageAccounts / blobServices / tárolók | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel vagy számmal. Nem használható egymást követő kötőjelek. |
> | storageAccounts / fileServices | tárfiók |  | Kell `default`. |
> | storageAccounts / fileServices / megosztások | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. Nem használható egymást követő kötőjelek. |
> | storageAccounts / managementPolicies | tárfiók |  | Kell `default`. |
> | blob | tároló | 1–1024 | Url-karakterek, kis- és nagybetűk megkülönböztetése |
> | üzenetsor | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. Nem használható egymást követő kötőjelek. |
> | tábla | tárfiók | 3-63 | Alfanumerikus.<br><br>Kezdjük a levéllel. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | storageSyncServices | erőforráscsoport | 1-260 | Alfanumerikus, szóközök, pont, kötőjel és aláhúzás.<br><br>Nem lehet pontokkal vagy szóköznel végződni. |
> | storageSyncServices / syncGroups | tárolószinkronizálási szolgáltatás | 1-260 | Alfanumerikus, szóközök, pont, kötőjel és aláhúzás.<br><br>Nem lehet pontokkal vagy szóköznel végződni. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Vezetők | erőforráscsoport | 2-50 | Alfanumerikus és kötőjelek.<br><br>Kezdjük a levéllel. Alfanumerikus végződéssel végződik. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | streamingjobs | erőforráscsoport | 3-63 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | streamingjobs / funkciók | streamelési feladat | 3-63 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | streamingjobs / bemenetek | streamelési feladat | 3-63 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | streamingjobs / kimenetek | streamelési feladat | 3-63 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |
> | streamingjobs / átalakítások | streamelési feladat | 3-63 | Alfanumerikus jelek, kötőjelek és aláhúzásjelek. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Környezetben | erőforráscsoport | 1–90 | Nem használható:<br>`'<>%&:\?/#` |
> | környezetek / accessPolicies | environment | 1–90 | Nem használható:<br> `'<>%&:\?/#` |
> | környezetek / eventSources | environment | 1–90 | Nem használható:<br>`'<>%&:\?/#` |
> | környezetek / referenceDataSets | environment | 3-63 | Alfanumerikus |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kiszolgálófarmok | erőforráscsoport | 1-40 | Alfanumerikus és kötőjelek. |
> | Helyek | Globális | 2-60 | Alfanumerikus és kötőjeleket tartalmaz.<br><br>Nem lehet kötőjellel kezdeni vagy végződni. |
> | oldalak / bővítőhely | Oldalon | 2-59 | Alfanumerikus és kötőjelek. |

## <a name="next-steps"></a>További lépések

Az erőforrások elnevezésével kapcsolatos javaslatokért olvassa [el a Kész: Ajánlott elnevezési és címkézési konvenciók című témakört.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)
