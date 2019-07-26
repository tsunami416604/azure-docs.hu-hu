---
title: Erőforrás-szolgáltatói műveletek Azure Resource Manager | Microsoft Docs
description: A Microsoft Azure Resource Manager erőforrás-szolgáltatók számára elérhető műveletek listája
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ce4765a512b81d13f735a05ad4fba5408284a607
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501408"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Erőforrás-szolgáltatói műveletek Azure Resource Manager

Ez a cikk felsorolja az egyes Azure Resource Manager erőforrás-szolgáltatók számára elérhető műveleteket. Ezek a műveletek [Egyéni szerepkörökben](custom-roles.md) használhatók, amelyek részletes [szerepköralapú hozzáférés-vezérlést (RBAC)](overview.md) biztosítanak az Azure-beli erőforrásokhoz. A műveleti karakterláncok formátuma a következő:`{Company}.{ProviderName}/{resourceType}/{action}`

Az erőforrás-szolgáltatói műveletek folyamatosan fejlődnek. A legújabb műveletek beszerzéséhez használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) vagy [az az Provider Operation List műveletet](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.AAD/domainServices/delete | Tartományi szolgáltatás törlése |
> | Action | Microsoft.AAD/domainServices/oucontainer/delete | Ou-tároló törlése |
> | Action | Microsoft.AAD/domainServices/oucontainer/read | Ou-tárolók olvasása |
> | Action | Microsoft.AAD/domainServices/oucontainer/write | Ou-tároló írása |
> | Action | Microsoft.AAD/domainServices/read | Tartományi szolgáltatások olvasása |
> | Action | Microsoft.AAD/domainServices/replicaSets/delete | Fürt helyének törlése |
> | Action | Microsoft.AAD/domainServices/replicaSets/read | Fürt helyének olvasása |
> | Action | Microsoft.AAD/domainServices/replicaSets/write | Fürt helyének írása |
> | Action | Microsoft.AAD/domainServices/write | Tartományi szolgáltatás írása |
> | Action | Microsoft.AAD/locations/operationresults/read |  |
> | Action | Microsoft.AAD/Operations/read |  |
> | Action | Microsoft.AAD/register/action | Tartományi szolgáltatás regisztrálása |
> | Action | Microsoft.AAD/unregister/action | Tartományi szolgáltatás regisztrációjának törlése |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | microsoft.aadiam/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Action | microsoft.aadiam/diagnosticsettings/read | Diagnosztikai beállítások olvasása |
> | Action | Microsoft. aadiam/diagnosticsettings/Write | Diagnosztikai beállítások írása |
> | Action | Microsoft. aadiam/diagnosticsettingscategories/READ | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Addons/operations/read | A támogatott RP-műveletek beolvasása. |
> | Action | Microsoft.Addons/register/action | A megadott előfizetés regisztrálása a Microsoft. addons szolgáltatásban |
> | Action | Microsoft.Addons/supportProviders/listsupportplaninfo/action | A megadott előfizetéshez tartozó aktuális támogatási csomag információit sorolja fel. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Eltávolítja a megadott Canonical-támogatási csomagot. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/read | A megadott Canonical támogatási csomag állapotának beolvasása. |
> | Action | Microsoft.Addons/supportProviders/supportPlanTypes/write | Hozzáadja a megadott Canonical-támogatási csomag típusát. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ADHybridHealthService/addsservices/action | Hozzon létre egy új erdőt a bérlő számára. |
> | Action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | A megadott szolgáltatásnév összes kiszolgálójának beolvasása. |
> | Action | Microsoft.ADHybridHealthService/addsservices/alerts/read | Riasztások részleteinek beolvasása az erdőhöz, például a alertid, a riasztás dátuma, a riasztás legutóbbi észlelése, a riasztás leírása, a legutóbbi frissítés, a riasztási szint, a riasztási állapot, a riasztások hibaelhárítási hivatkozásai |
> | Action | Microsoft.ADHybridHealthService/addsservices/configuration/read | Beolvassa az erdő szolgáltatás-konfigurációját. Példa: erdő neve, működési szint, tartománynév-kiosztási főkiszolgáló FSMO-szerepkör, séma-főkiszolgáló FSMO-szerepköre stb. |
> | Action | Microsoft.ADHybridHealthService/addsservices/delete | Töröl egy szolgáltatást, és az állapottal együtt a kiszolgálókat is. |
> | Action | Microsoft. ADHybridHealthService/addsservices/dimensions/READ | Lekéri az erdő tartományait és helyeit. Példa: állapotadatok, aktív riasztások, megoldott riasztások, tulajdonságok, például a tartomány működési szintje, erdő, infrastruktúra-főkiszolgáló, elsődleges tartományvezérlő, RID-főkiszolgáló stb.  |
> | Action | Microsoft. ADHybridHealthService/addsservices/features/UserPreference/READ | Beolvassa az erdő felhasználói beállításait.<br>Példa – MetricCounterName, például ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>A felhasználói felületi diagramok beállításai stb. |
> | Action | Microsoft. ADHybridHealthService/addsservices/forestsummary/READ | Beolvassa az erdő összefoglalását az adott erdőhöz, például az erdő nevét, az erdő alá tartozó tartományok számát, a helyek és a helyek részleteit stb. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Egy adott szolgáltatáshoz tartozó támogatott metrikák listájának beolvasása.<br>Ilyenek például az extranetes fiókzárolási szolgáltatások, a sikertelen kérelmek teljes száma, a függő jogkivonat-kérelmek (proxy), a jogkivonat-kérelmek előállított/másodperc stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ szálak összesen etc a ADDomainService.<br>Futtassa a profil késését, a TCP-kapcsolatok létrehozását, az adatellenőrzési ügynök saját bájtjait, a statisztikát az Azure AD-be a ADSync szolgáltatáshoz. |
> | Action | Microsoft. ADHybridHealthService/addsservices/metrikák/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: Extranetes fiókzárolás, a sikertelen kérelmek teljes száma, a függőben lévő jogkivonat-kérések (proxy), a jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Action | Microsoft. ADHybridHealthService/addsservices/premiumcheck/READ | Ez az API beolvassa a prémium bérlő összes bekészített ADDomainServices listáját. |
> | Action | Microsoft. ADHybridHealthService/addsservices/READ | A megadott szolgáltatásnév szolgáltatás részleteinek beolvasása. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Lekérdezi a megadott szolgáltatásnév összes kiszolgálójának replikálási részleteit. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Lekéri a tartományvezérlők számát és a replikálási hibákat, ha vannak ilyenek. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Beolvassa a tartományvezérlők teljes listáját, valamint az adott erdő replikációs részleteit. |
> | Action | Microsoft. ADHybridHealthService/addsservices/Servicemembers/művelet | Adja hozzá a kiszolgálói példányt a szolgáltatáshoz. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | A ADDomainService kiszolgáló regisztrációja során ezt az API-t hívja meg az új kiszolgálók bevezetéséhez szükséges hitelesítő adatok beszerzéséhez. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Egy adott szolgáltatás és bérlő kiszolgálójának törlése. |
> | Action | Microsoft. ADHybridHealthService/addsservices/Write | Létrehozza vagy frissíti a bérlő ADDomainService-példányát. |
> | Action | Microsoft.ADHybridHealthService/configuration/action | A bérlő konfigurációjának frissítése. |
> | Action | Microsoft.ADHybridHealthService/configuration/read | A bérlő konfigurációjának beolvasása. |
> | Action | Microsoft.ADHybridHealthService/configuration/write | Létrehoz egy bérlői konfigurációt. |
> | Action | Microsoft.ADHybridHealthService/logs/contents/read | A blobban tárolt ügynök-telepítési és regisztrációs naplók tartalmának beolvasása. |
> | Action | Microsoft.ADHybridHealthService/logs/read | Beolvassa az ügynök telepítési és regisztrációs naplóit a bérlő számára. |
> | Action | Microsoft.ADHybridHealthService/operations/read | Lekéri a rendszer által támogatott műveletek listáját. |
> | Action | Microsoft.ADHybridHealthService/register/action | Regisztrálja a ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltatót, és lehetővé teszi a ADHybrid Állapotfigyelő szolgáltatás erőforrás létrehozását. |
> | Action | Microsoft. ADHybridHealthService/jelentések/availabledeployments/READ | Lekéri az elérhető régiók listáját, amelyet a DevOps az ügyfél-incidensek támogatásához használ. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassword/read | Lekérdezi a helytelen jelszó-kísérletek listáját Active Directory összevonási szolgáltatás összes felhasználója számára. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Beolvassa a blob SAS URI-t, amely tartalmazza az állapotot, és az újonnan várólistán lévő jelentési feladatának végleges eredményét az adott bérlő számára napi Felhasználónév/jelszó/IP-cím/felhasználóazonosító gyakorisága alapján. |
> | Action | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Beolvassa a DevOps által beleegyezett bérlők listáját. Jellemzően ügyfélszolgálati támogatáshoz használatos. |
> | Action | Microsoft.ADHybridHealthService/reports/isdevops/read | Egy érték beolvasása, amely azt jelzi, hogy a bérlő DevOps-e, vagy sem. |
> | Action | Microsoft. ADHybridHealthService/jelentések/selectdevopstenant/READ | A kiválasztott fejlesztői Ops-bérlőhöz tartozó userid (ObjectId) frissítése. |
> | Action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Beolvassa az adott bérlő kiválasztott központi telepítését. |
> | Action | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | A bérlői azonosító beolvasása után a bérlői tároló helye lesz. |
> | Action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Lekéri a földrajzi helyet, amelyről az adatok hozzáférnek. |
> | Action | Microsoft.ADHybridHealthService/services/action | Egy szolgáltatási példány frissítése a bérlőben. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | A szolgáltatáshoz tartozó riasztások beolvasása. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | A szolgáltatáshoz tartozó riasztások beolvasása. |
> | Action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | A szolgáltatás neve alapján ellenőrizhető, hogy egy szolgáltatásnak van-e minden szükséges funkciója a szolgáltatás használatához. |
> | Action | Microsoft.ADHybridHealthService/services/delete | Töröl egy szolgáltatási példányt a bérlőben. |
> | Action | Microsoft. ADHybridHealthService/Services/exporterrors/READ | Lekéri egy adott szinkronizálási szolgáltatás exportálási hibáit. |
> | Action | Microsoft.ADHybridHealthService/services/exportstatus/read | Egy adott szolgáltatás exportálási állapotának beolvasása. |
> | Action | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Riasztási visszajelzés küldése egy adott szolgáltatásról és kiszolgálóról. |
> | Action | Microsoft. ADHybridHealthService/Services/metricmetadata/READ | Egy adott szolgáltatáshoz tartozó támogatott metrikák listájának beolvasása.<br>Ilyenek például az extranetes fiókzárolási szolgáltatások, a sikertelen kérelmek teljes száma, a függő jogkivonat-kérelmek (proxy), a jogkivonat-kérelmek előállított/másodperc stb.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ szálak összesen etc a ADDomainService.<br>Futtassa a profil késését, a TCP-kapcsolatok létrehozását, az adatellenőrzési ügynök saját bájtjait, a statisztikát az Azure AD-be a ADSync szolgáltatáshoz. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/mérőszámok/csoportok/átlag/olvasás | Adott szolgáltatás esetében ez az API egy adott szolgáltatás metrikáinak átlagát kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: Extranetes fiókzárolás, a sikertelen kérelmek teljes száma, a függőben lévő jogkivonat-kérések (proxy), a jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/mérőszámok/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: Extranetes fiókzárolás, a sikertelen kérelmek teljes száma, a függőben lévő jogkivonat-kérések (proxy), a jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Adott szolgáltatás esetén ez az API egy adott szolgáltatás metrikáinak összesített nézetét kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: Extranetes fiókzárolás, a sikertelen kérelmek teljes száma, a függőben lévő jogkivonat-kérések (proxy), a jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Szolgáltatás figyelési konfigurációjának hozzáadása vagy frissítése. |
> | Action | Microsoft. ADHybridHealthService/Services/monitoringconfigurations/READ | Lekéri egy adott szolgáltatás figyelési konfigurációit. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/monitoringconfigurations/írás | Szolgáltatás figyelési konfigurációinak hozzáadása vagy frissítése. |
> | Action | Microsoft. ADHybridHealthService/Services/premiumcheck/READ | Ez az API beolvassa a prémium szintű bérlőhöz tartozó összes beépített szolgáltatás listáját. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/olvasás | A bérlőben lévő szolgáltatási példányok beolvasása. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/jelentések/blobUris/olvasás | Minden kockázatos IP-jelentés URI-azonosítójának beolvasása az elmúlt 7 napban. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/jelentések/részletek/olvasás | Az elmúlt 7 nap helytelen jelszavas hibával rendelkező leggyakoribb 50-felhasználók jelentésének beolvasása |
> | Action | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Kockázatos IP-jelentést generál, és visszaadja a rá mutató URI-t. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/művelet | Létrehoz egy kiszolgálói példányt a szolgáltatásban. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/riasztások/olvasás | Egy kiszolgáló értesítéseinek beolvasása. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | A kiszolgáló regisztrálása során a rendszer ezt az API-t hívja meg az új kiszolgálók bevezetéséhez szükséges hitelesítő adatok beszerzéséhez. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/datafreshness/READ | Az adott kiszolgáló esetében ez az API lekéri a kiszolgálók által feltöltött adattípusok listáját, valamint az egyes feltöltések legkésőbbi idejét. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/delete | Töröl egy kiszolgálói példányt a szolgáltatásban. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/exportstatus/READ | Beolvassa a szinkronizálási exportálási hiba részleteit egy adott szinkronizálási szolgáltatáshoz. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/metrikák/csoportok/olvasás | Ez az API a metrikák adatait kapja meg.<br>Ez az API például a következőhöz kapcsolódó információk beszerzésére használható: Extranetes fiókzárolás, a sikertelen kérelmek teljes száma, a függőben lévő jogkivonat-kérések (proxy), a jogkivonat-kérelmek előállított/másodperc stb. a ADFederation szolgáltatáshoz.<br>NTLM-hitelesítés/mp, LDAP sikeres kötések/mp, LDAP-kötési idő, LDAP aktív szálak, Kerberos-hitelesítés/mp, ATQ-szálak összesen etc a ADDomain szolgáltatáshoz.<br>Futtassa a profil késését, a TCP-kapcsolatok létrejöttét, az adatellenőrzési ügynök saját bájtjait, az Azure AD for Sync szolgáltatáshoz való exportálási statisztikát. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/Servicemembers/mérőszámok/olvasás | Lekérdezi az összekötők listáját, és futtatja a profil nevét az adott szolgáltatás és szolgáltatás tagja számára. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/READ | A szolgáltatásban található kiszolgálópéldány beolvasása. |
> | Action | Microsoft. ADHybridHealthService/Services/Servicemembers/serviceconfiguration/READ | Egy adott bérlő szolgáltatás-konfigurációjának beolvasása. |
> | Action | Microsoft. ADHybridHealthService/Services/tenantwhitelisting/READ | Egy adott bérlő szolgáltatás-engedélyezési állapotának beolvasása. |
> | Action | Microsoft. ADHybridHealthService/szolgáltatások/írás | Létrehoz egy szolgáltatási példányt a bérlőben. |
> | Action | Microsoft.ADHybridHealthService/unregister/action | A ADHybrid Állapotfigyelő szolgáltatás erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Advisor/configurations/read | Konfigurációk beolvasása |
> | Action | Microsoft. Advisor/konfigurációk/írás | Konfiguráció létrehozása/frissítése |
> | Action | Microsoft.Advisor/generateRecommendations/action | Javaslatok generálása |
> | Action | Microsoft.Advisor/generateRecommendations/read | Javaslatok állapotának előállítása |
> | Action | Microsoft.Advisor/metadata/read | Metaadatok beolvasása |
> | Action | Microsoft.Advisor/operations/read | Lekéri a Microsoft Advisor műveleteit |
> | Action | Microsoft. Advisor/javaslatok/elérhető/művelet | Új javaslat érhető el a Microsoft Advisor szolgáltatásban |
> | Action | Microsoft. Advisor/javaslatok/olvasás | Olvasási javaslatok |
> | Action | Microsoft.Advisor/recommendations/suppressions/delete | Törlés mellőzése |
> | Action | Microsoft.Advisor/recommendations/suppressions/read | Letiltások beolvasása |
> | Action | Microsoft.Advisor/recommendations/suppressions/write | Eltávolítások létrehozása/frissítése |
> | Action | Microsoft.Advisor/register/action | Regisztrálja a Microsoft Advisor előfizetését |
> | Action | Microsoft.Advisor/suppressions/delete | Törlés mellőzése |
> | Action | Microsoft.Advisor/suppressions/read | Letiltások beolvasása |
> | Action | Microsoft. Advisor/Mellőzés/írás | Eltávolítások létrehozása/frissítése |
> | Action | Microsoft.Advisor/unregister/action | A Microsoft Advisor-előfizetés regisztrációjának törlése |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.AlertsManagement/actionRules/delete | Művelet szabályának törlése egy adott előfizetésben. |
> | Action | Microsoft.AlertsManagement/actionRules/read | A bemeneti szűrőkhöz tartozó összes művelet szabályának beolvasása. |
> | Action | Microsoft.AlertsManagement/actionRules/write | Műveleti szabály létrehozása vagy frissítése egy adott előfizetésben |
> | Action | Microsoft.AlertsManagement/alerts/changestate/action | A riasztás állapotának módosítása |
> | Action | Microsoft. AlertsManagement/riasztások/diagnosztika/olvasás | A riasztáshoz tartozó összes diagnosztika beolvasása |
> | Action | Microsoft.AlertsManagement/alerts/history/read | A riasztás előzményeinek beolvasása |
> | Action | Microsoft.AlertsManagement/alerts/read | A bemeneti szűrők összes riasztásának beolvasása. |
> | Action | Microsoft.AlertsManagement/alertsList/read | A bemeneti szűrők összes riasztásának beolvasása az előfizetések között |
> | Action | Microsoft. AlertsManagement/alertsMetaData/READ | Riasztások beolvasása a bemeneti paraméterhez. |
> | Action | Microsoft.AlertsManagement/alertsSummary/read | Riasztások összefoglalásának beolvasása |
> | Action | Microsoft.AlertsManagement/alertsSummaryList/read | Riasztások összegzésének lekérése az előfizetések között |
> | Action | Microsoft.AlertsManagement/Operations/read | A megadott műveletek beolvasása |
> | Action | Microsoft.AlertsManagement/register/action | Regisztrálja az előfizetést a Microsoft riasztások kezeléséhez |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Intelligens detektor riasztási szabályának törlése egy adott előfizetésben |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Az intelligens detektor riasztási szabályainak beolvasása a bemeneti szűrőkhöz |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Intelligens detektor riasztási szabályának létrehozása vagy frissítése egy adott előfizetésben |
> | Action | Microsoft.AlertsManagement/smartGroups/changestate/action | Az intelligens csoport állapotának módosítása |
> | Action | Microsoft.AlertsManagement/smartGroups/history/read | Az intelligens csoport előzményeinek beolvasása |
> | Action | Microsoft.AlertsManagement/smartGroups/read | A bemeneti szűrők összes intelligens csoportjának beolvasása |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Ellenőrzi, hogy a megadott Analysis Server neve érvényes-e, és nincs-e használatban. |
> | Action | Microsoft.AnalysisServices/locations/operationresults/read | A megadott műveleti eredmény információinak beolvasása. |
> | Action | Microsoft.AnalysisServices/locations/operationstatuses/read | Lekéri a megadott műveleti állapot adatait. |
> | Action | Microsoft.AnalysisServices/operations/read | A műveletek információinak lekérése |
> | Action | Microsoft.AnalysisServices/register/action | Regisztrálja Analysis Services erőforrás-szolgáltatót. |
> | Action | Microsoft.AnalysisServices/servers/delete | Törli a Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | A kiszolgálóhoz társított átjáró állapotának listázása. |
> | Action | Microsoft.AnalysisServices/servers/read | A megadott Analysis Server információinak beolvasása. |
> | Action | Microsoft.AnalysisServices/servers/resume/action | A Analysis Server folytatása. |
> | Action | Microsoft.AnalysisServices/servers/skus/read | A kiszolgáló rendelkezésre álló SKU-adatainak beolvasása |
> | Action | Microsoft.AnalysisServices/servers/suspend/action | Felfüggeszti a Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/write | Létrehozza vagy frissíti a megadott Analysis Server. |
> | Action | Microsoft.AnalysisServices/skus/read | A SKU-ra vonatkozó információk beolvasása |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ApiManagement/checkNameAvailability/read | Ellenőrzi, hogy elérhető-e a megadott szolgáltatás neve |
> | Action | Microsoft.ApiManagement/operations/read | A Microsoft. ApiManagement erőforráshoz elérhető összes API-művelet olvasása |
> | Action | Microsoft.ApiManagement/register/action | Előfizetés regisztrálása a Microsoft. ApiManagement erőforrás-szolgáltatónál |
> | Action | Microsoft.ApiManagement/reports/read | Az időszakok, a földrajzi régió, a fejlesztők, a termékek, az API-k, a műveletek, az előfizetés és a byRequest által összesített jelentések beolvasása. |
> | Action | Microsoft.ApiManagement/service/apis/delete | Törli az API Management Service-példány megadott API-ját. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/delete | A megadott diagnosztika törlése egy API-ból. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/read | Egy API összes diagnosztikát listázza. vagy beolvassa az azonosító alapján megadott API-diagnosztika részleteit. |
> | Action | Microsoft.ApiManagement/service/apis/diagnostics/write | Létrehoz egy új diagnosztikát egy API-hoz, vagy frissíti a meglévőt. vagy frissíti a diagnosztika részleteit az azonosító alapján megadott API-hoz. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/delete | A megadott Megjegyzés törlése a hibából. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/read | Felsorolja a megadott API-hoz tartozó probléma összes mellékletét. vagy beolvassa az azonosítóval megadott API-hoz tartozó melléklettel kapcsolatos adatokat. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/write | Új mellékletet hoz létre a probléma egy API-ban vagy egy meglévő frissítése során. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/delete | A megadott Megjegyzés törlése a hibából. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/read | Felsorolja a megadott API-hoz kapcsolódó probléma összes megjegyzését. vagy beolvassa az azonosító alapján megadott API-hoz tartozó probléma megjegyzésének részleteit. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/write | Új Megjegyzés létrehozása a probléma számára egy API-ban vagy egy meglévő frissítése. |
> | Action | Microsoft.ApiManagement/service/apis/issues/delete | Törli a megadott problémát egy API-ból. |
> | Action | Microsoft.ApiManagement/service/apis/issues/read | Felsorolja a megadott API-hoz kapcsolódó összes problémát. vagy beolvassa a probléma részleteit az azonosító által meghatározott API-hoz. |
> | Action | Microsoft.ApiManagement/service/apis/issues/write | Új probléma létrehozása egy API-hoz, vagy egy meglévő frissítése. vagy frissít egy meglévő problémát egy API-hoz. |
> | Action | Microsoft.ApiManagement/service/apis/operations/delete | Törli a megadott műveletet az API-ban. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/delete | A házirend-konfiguráció törlése az API-művelettel. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/read | A házirend-konfiguráció listájának beolvasása az API-művelet szintjén. vagy szerezze be a házirend-konfigurációt az API-művelet szintjén. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policies/write | Létrehozza vagy frissíti az API-művelet szintjének házirend-konfigurációját. |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/delete | A házirend-konfiguráció törlése a műveleti szinten |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/read | Házirend-konfiguráció beolvasása műveleti szinten |
> | Action | Microsoft.ApiManagement/service/apis/operations/policy/write | Házirend-konfiguráció létrehozása műveleti szinten |
> | Action | Microsoft.ApiManagement/service/apis/operations/read | Felsorolja a megadott API-hoz tartozó műveletek gyűjteményét. vagy lekéri az azonosító alapján megadott API-művelet részleteit. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/delete | Válassza le a címkét a műveletből. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/read | Felsorolja a művelethez társított összes címkét. vagy a művelethez társított címke beolvasása. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/write | Címke társítása a művelethez. |
> | Action | Microsoft.ApiManagement/service/apis/operations/write | Létrehoz egy új műveletet az API-ban, vagy frissíti a meglévőt. vagy frissíti a művelet részleteit az azonosítójában megadott API-ban. |
> | Action | Microsoft.ApiManagement/service/apis/operationsByTags/read | A címkékkel társított műveletek gyűjteményét listázza. |
> | Action | Microsoft.ApiManagement/service/apis/policies/delete | Törli a házirend-konfigurációt az API-ban. |
> | Action | Microsoft.ApiManagement/service/apis/policies/read | A szabályzat konfigurációjának beolvasása az API szintjén. vagy szerezze be a szabályzat konfigurációját az API szintjén. |
> | Action | Microsoft.ApiManagement/service/apis/policies/write | Létrehozza vagy frissíti az API házirend-konfigurációját. |
> | Action | Microsoft.ApiManagement/service/apis/policy/delete | A szabályzat konfigurációjának törlése API-szinten |
> | Action | Microsoft.ApiManagement/service/apis/policy/read | A szabályzat konfigurációjának beolvasása API-szinten |
> | Action | Microsoft.ApiManagement/service/apis/policy/write | Házirend-konfiguráció létrehozása API-szinten |
> | Action | Microsoft.ApiManagement/service/apis/products/read | Felsorolja az összes olyan terméket, amely az API részét képezi. |
> | Action | Microsoft.ApiManagement/service/apis/read | Felsorolja az API Management Service-példány összes API-jait. vagy lekéri az azonosító alapján megadott API részleteit. |
> | Action | Microsoft.ApiManagement/service/apis/releases/delete | Eltávolítja az API összes kiadását, vagy törli a megadott kiadást az API-ban. |
> | Action | Microsoft.ApiManagement/service/apis/releases/read | Az API összes kiadását listázza.<br>Egy API-kiadás jön létre, amikor egy API-változatot aktuálisan használ.<br>A kiadásokat a rendszer az előző változatokra történő visszaállításra is felhasználja.<br>Az eredmények lapozva lesznek, és a $top és a $skip paraméterek is korlátozva lesznek.<br>vagy egy API-kiadás részleteit adja vissza. |
> | Action | Microsoft.ApiManagement/service/apis/releases/write | Új kiadás létrehozása az API-hoz. vagy frissíti az azonosító alapján megadott API kiadásának részleteit. |
> | Action | Microsoft.ApiManagement/service/apis/revisions/delete | Egy API összes változatának eltávolítása |
> | Action | Microsoft.ApiManagement/service/apis/revisions/read | Felsorolja az API összes változatát. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/delete | Törli a séma konfigurációját az API-ban. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/read | A séma konfigurációjának beolvasása az API szintjén. vagy a séma konfigurációjának beolvasása az API szintjén. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/write | Létrehozza vagy frissíti az API sémájának konfigurációját. |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Az API-címke leírásának törlése. |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Az API hatókörében lévő összes címke leírása. A hencegés hasonló modell van definiálva az API-szinten, de a címke hozzárendelhető a műveletekhez, vagy lekérheti a címke leírását az API hatókörében lévő tagDescription. |
> | Action | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Címke leírásának létrehozása/frissítése az API hatókörében. |
> | Action | Microsoft.ApiManagement/service/apis/tags/delete | Válassza le a címkét az API-ból. |
> | Action | Microsoft.ApiManagement/service/apis/tags/read | Az API-hoz társított összes címke listája. vagy az API-hoz társított címke beolvasása. |
> | Action | Microsoft.ApiManagement/service/apis/tags/write | Címke társítása az API-hoz. |
> | Action | Microsoft.ApiManagement/service/apis/write | Új vagy frissíti a API Management Service-példány meglévő API-ját. vagy frissíti a API Management Service-példány megadott API-ját. |
> | Action | Microsoft.ApiManagement/service/apisByTags/read | A címkékhez társított API-k gyűjteményét listázza. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/delete | Az adott API-verzió megadott készletének törlése. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/read | Az API-verziók készleteit sorolja fel a megadott szolgáltatási példányban. vagy lekéri az azonosító alapján megadott API-verzió részleteit. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Verzió entitások listájának beolvasása |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/write | Egy API-verzió készletének létrehozása vagy frissítése. vagy frissíti az azonosító alapján megadott API-VersionSet adatait. |
> | Action | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Frissíti a Virtual Network futó Microsoft. ApiManagement-erőforrásokat a frissített hálózati beállítások kiválasztásához. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/delete | Az adott engedélyezési kiszolgáló példányának törlése. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/read | A szolgáltatási példányon belül meghatározott engedélyezési kiszolgálók gyűjteményét listázza. vagy lekéri az azonosító alapján megadott engedélyezési kiszolgáló adatait. |
> | Action | Microsoft.ApiManagement/service/authorizationServers/write | Új engedélyezési kiszolgáló létrehozása vagy egy meglévő engedélyezési kiszolgáló frissítése. vagy frissíti az azonosító alapján megadott engedélyezési kiszolgáló adatait. |
> | Action | Microsoft.ApiManagement/service/backends/delete | A megadott háttér törlése. |
> | Action | Microsoft.ApiManagement/service/backends/read | Felsorolja a megadott szolgáltatási példányban található háttérrendszer-gyűjteményeket. vagy lekéri az azonosító alapján megadott háttér részleteit. |
> | Action | Microsoft.ApiManagement/service/backends/reconnect/action | Értesíti a APIM proxyt, hogy a megadott időkorlát után hozzon létre egy új kapcsolódást a háttérrendszer számára. Ha nincs időkorlát megadva, a rendszer 2 perces időkorlátot használ. |
> | Action | Microsoft.ApiManagement/service/backends/write | Egy háttérrendszer létrehozása vagy frissítése. vagy frissít egy meglévő hátteret. |
> | Action | Microsoft.ApiManagement/service/backup/action | API Management szolgáltatás biztonsági mentése a megadott tárolóba egy felhasználó által megadott Storage-fiókban |
> | Action | Microsoft.ApiManagement/service/caches/delete | Adott gyorsítótár törlése. |
> | Action | Microsoft.ApiManagement/service/caches/read | Felsorolja a megadott szolgáltatási példány összes külső gyorsítótárának gyűjteményét. vagy lekéri az azonosító alapján megadott gyorsítótár részleteit. |
> | Action | Microsoft.ApiManagement/service/caches/write | Létrehoz vagy frissít egy külső gyorsítótárat az API Management-példányban való használatra. vagy frissíti az azonosító alapján megadott gyorsítótár részleteit. |
> | Action | Microsoft.ApiManagement/service/certificates/delete | Adott tanúsítvány törlése. |
> | Action | Microsoft.ApiManagement/service/certificates/read | Felsorolja a megadott szolgáltatási példányban található összes tanúsítvány gyűjteményét. vagy lekéri az azonosító alapján megadott tanúsítvány részleteit. |
> | Action | Microsoft.ApiManagement/service/certificates/write | Létrehozza vagy frissíti a háttérrel való hitelesítéshez használt tanúsítványt. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Eltávolítja a megadott tartalmi elemeket. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/read | A tartalmi elemek listáját vagy a tartalom elem részleteit adja vissza. |
> | Action | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Új tartalmi elemek létrehozása vagy a megadott tartalmi elemek frissítése |
> | Action | Microsoft.ApiManagement/service/contentTypes/read | A tartalomtípusok listáját adja vissza. |
> | Action | Microsoft.ApiManagement/service/delete | API Management szolgáltatás példányának törlése |
> | Action | Microsoft.ApiManagement/service/diagnostics/delete | Törli a megadott diagnosztikát. |
> | Action | Microsoft.ApiManagement/service/diagnostics/read | Felsorolja az API Management Service-példány összes diagnosztikát. vagy lekéri az azonosító alapján megadott diagnosztika részleteit. |
> | Action | Microsoft.ApiManagement/service/diagnostics/write | Létrehoz egy új diagnosztikát, vagy frissíti a meglévőt. vagy frissíti az azonosító alapján megadott diagnosztika részleteit. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/művelet | Az átjáró konfigurációjának beolvasása. vagy frissíti az átjáró szívverését. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/törlés | Adott átjáró törlése. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/kulcsok/művelet | Az átjáró kulcsainak beolvasása. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/olvasás | A Service instance szolgáltatásban regisztrált átjárók gyűjteményét listázza. vagy beolvassa az azonosító által megadott átjáró részleteit. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/regeneratePrimaryKey/művelet | Újragenerálta az elsődleges átjáró kulcsát a invalidationg létrehozott jogkivonatokkal. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/regenerateSecondaryKey/művelet | Újragenerálja a másodlagos átjáró kulcsát a vele létrehozott jogkivonatok invalidationg. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/jogkivonat/művelet | Az átjáróhoz tartozó megosztott hozzáférés engedélyezési jogkivonatának beolvasása. |
> | Action | Microsoft. ApiManagement/szolgáltatás/átjárók/írás | Létrehoz vagy frissít egy átjárót az API Management-példányban való használatra. vagy frissíti az azonosító alapján megadott átjáró részleteit. |
> | Action | Microsoft.ApiManagement/service/getssotoken/action | Beolvassa az SSO-tokent, amely bejelentkezhet API Management szolgáltatásbeli örökölt portálra rendszergazdaként |
> | Action | Microsoft.ApiManagement/service/groups/delete | Törli a API Management szolgáltatási példány adott csoportját. |
> | Action | Microsoft.ApiManagement/service/groups/read | A szolgáltatási példányon belül definiált csoportok gyűjteményét listázza. vagy lekéri az azonosító alapján megadott csoport részleteit. |
> | Action | Microsoft.ApiManagement/service/groups/users/delete | Meglévő felhasználó eltávolítása meglévő csoportból. |
> | Action | Microsoft.ApiManagement/service/groups/users/read | A csoporthoz társított felhasználói entitások gyűjteményét listázza. |
> | Action | Microsoft.ApiManagement/service/groups/users/write | Meglévő felhasználó hozzáadása meglévő csoporthoz |
> | Action | Microsoft.ApiManagement/service/groups/write | Létrehoz vagy frissít egy csoportot. vagy frissíti az azonosító alapján megadott csoport részleteit. |
> | Action | Microsoft.ApiManagement/service/identityProviders/delete | Törli a megadott identitás-szolgáltatói konfigurációt. |
> | Action | Microsoft.ApiManagement/service/identityProviders/read | Felsorolja a megadott szolgáltatási példányban konfigurált identitás-szolgáltató gyűjteményét. vagy beolvassa a megadott szolgáltatási példányban konfigurált identitás-szolgáltató konfigurációs részleteit. |
> | Action | Microsoft.ApiManagement/service/identityProviders/write | Létrehozza vagy frissíti a IdentityProvider-konfigurációt. vagy frissít egy meglévő IdentityProvider-konfigurációt. |
> | Action | Microsoft.ApiManagement/service/issues/read | Felsorolja a megadott szolgáltatási példányban felmerülő problémák gyűjteményét. vagy API Management probléma részleteinek beolvasása |
> | Action | Microsoft.ApiManagement/service/locations/networkstatus/read | Lekéri azon erőforrások hálózati hozzáférési állapotát, amelyeken a szolgáltatás a helytől függ. |
> | Action | Microsoft.ApiManagement/service/loggers/delete | A megadott naplózó törlése. |
> | Action | Microsoft.ApiManagement/service/loggers/read | A megadott szolgáltatási példányban lévő naplózók gyűjteményét listázza. vagy beolvassa az azonosító alapján megadott naplózó részleteit. |
> | Action | Microsoft.ApiManagement/service/loggers/write | Egy naplózó létrehozása vagy frissítése. vagy frissít egy meglévő naplózó. |
> | Action | Microsoft.ApiManagement/service/managedeployments/action | SKU/egységek módosítása, API Management szolgáltatás regionális központi telepítésének hozzáadása/eltávolítása |
> | Action | Microsoft.ApiManagement/service/networkstatus/read | Lekéri azon erőforrások hálózati hozzáférési állapotát, amelyeken a szolgáltatás függ. |
> | Action | Microsoft.ApiManagement/service/notifications/action | Értesítés küldése egy megadott felhasználónak |
> | Action | Microsoft.ApiManagement/service/notifications/read | A szolgáltatási példányon belül definiált tulajdonságok gyűjteményét listázza. vagy beolvassa az azonosító által megadott értesítés részleteit. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Eltávolítja az e-mailt az értesítési listából. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Beolvassa az értesítésre előfizetett értesítő címzett e-mailek listáját. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Hozzáadja az e-mail-címet az értesítés címzettjeinek listájához. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Eltávolítja a API Management felhasználót az értesítési listából. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Beolvassa az értesítésre előfizetett értesítési címzett felhasználó listáját. |
> | Action | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Hozzáadja a API Management felhasználót az értesítés címzettjeinek listájához. |
> | Action | Microsoft.ApiManagement/service/notifications/write | API Management közzétevő értesítésének létrehozása vagy frissítése. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/delete | Törli a API Management szolgáltatás példányának adott OpenID Connect-szolgáltatóját. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/read | Az összes OpenId Connect-szolgáltató listája. vagy lekéri az adott OpenID Connect-szolgáltatót. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/write | Létrehozza vagy frissíti az OpenID Connect-szolgáltatót. vagy frissíti az adott OpenID Connect-szolgáltatót. |
> | Action | Microsoft.ApiManagement/service/operationresults/read | A hosszú ideig futó művelet aktuális állapotának beolvasása |
> | Action | Microsoft.ApiManagement/service/policies/delete | Törli az API Management szolgáltatás globális házirend-konfigurációját. |
> | Action | Microsoft.ApiManagement/service/policies/read | Az API Management szolgáltatás összes globális házirend-definícióját listázza. vagy szerezze be az API Management szolgáltatás globális szabályzatának definícióját. |
> | Action | Microsoft.ApiManagement/service/policies/write | Létrehozza vagy frissíti az API Management szolgáltatás globális házirend-konfigurációját. |
> | Action | Microsoft.ApiManagement/service/policy/delete | A házirend-konfiguráció törlése a bérlői szinten |
> | Action | Microsoft.ApiManagement/service/policy/read | Házirend-konfiguráció beolvasása a bérlői szinten |
> | Action | Microsoft.ApiManagement/service/policy/write | Házirend-konfiguráció létrehozása a bérlői szinten |
> | Action | Microsoft.ApiManagement/service/policySnippets/read | Felsorolja az összes házirend-kódrészletet. |
> | Action | Microsoft.ApiManagement/service/portalsettings/read | A portál bejelentkezési beállításainak beszerzése, illetve a portálra vonatkozó regisztrációs beállítások beszerzése, illetve a portál delegálási beállításainak beolvasása. |
> | Action | Microsoft.ApiManagement/service/portalsettings/write | A bejelentkezési beállítások frissítése. vagy a bejelentkezési beállítások létrehozása vagy frissítése. vagy frissítse a regisztrációs beállításokat, vagy frissítse a regisztrációs beállításokat, vagy frissítse a delegálási beállításokat. vagy a delegálási beállítások létrehozása vagy frissítése. |
> | Action | Microsoft.ApiManagement/service/products/apis/delete | A megadott termékből törli a megadott API-t. |
> | Action | Microsoft.ApiManagement/service/products/apis/read | A termékhez társított API-k gyűjteményét listázza. |
> | Action | Microsoft.ApiManagement/service/products/apis/write | Egy API-t ad hozzá a megadott termékhez. |
> | Action | Microsoft.ApiManagement/service/products/delete | Termék törlése. |
> | Action | Microsoft.ApiManagement/service/products/groups/delete | Törli a megadott csoport és termék közötti társítást. |
> | Action | Microsoft.ApiManagement/service/products/groups/read | Felsorolja a megadott termékhez társított fejlesztői csoportok gyűjteményét. |
> | Action | Microsoft.ApiManagement/service/products/groups/write | Hozzáadja a megadott fejlesztői csoport társítását a megadott termékhez. |
> | Action | Microsoft.ApiManagement/service/products/policies/delete | Törli a termék házirend-konfigurációját. |
> | Action | Microsoft.ApiManagement/service/products/policies/read | A szabályzat konfigurációjának beolvasása a termék szintjén. vagy szerezze be a szabályzat konfigurációját a termék szintjén. |
> | Action | Microsoft.ApiManagement/service/products/policies/write | Létrehozza vagy frissíti a termékhez tartozó házirend-konfigurációt. |
> | Action | Microsoft.ApiManagement/service/products/policy/delete | A házirend-konfiguráció törlése a termék szintjén |
> | Action | Microsoft.ApiManagement/service/products/policy/read | A szabályzat konfigurációjának beolvasása a termék szintjén |
> | Action | Microsoft.ApiManagement/service/products/policy/write | Házirend-konfiguráció létrehozása a termék szintjén |
> | Action | Microsoft.ApiManagement/service/products/read | Felsorolja a megadott szolgáltatási példányban található termékek gyűjteményét. vagy lekéri az azonosító alapján megadott termék részleteit. |
> | Action | Microsoft.ApiManagement/service/products/subscriptions/read | Felsorolja a megadott termékre vonatkozó előfizetések gyűjteményét. |
> | Action | Microsoft.ApiManagement/service/products/tags/delete | Válassza le a címkét a termékből. |
> | Action | Microsoft.ApiManagement/service/products/tags/read | Felsorolja a termékhez társított összes címkét. vagy a termékhez társított címke beolvasása. |
> | Action | Microsoft.ApiManagement/service/products/tags/write | Címke kiosztása a termékhez. |
> | Action | Microsoft.ApiManagement/service/products/write | Létrehoz vagy frissít egy terméket. vagy frissítse a meglévő termék részleteit. |
> | Action | Microsoft.ApiManagement/service/productsByTags/read | A címkékhez társított termékek gyűjteményét listázza. |
> | Action | Microsoft.ApiManagement/service/properties/delete | A API Management szolgáltatás példányának adott tulajdonságát törli. |
> | Action | Microsoft.ApiManagement/service/properties/read | A szolgáltatási példányon belül definiált tulajdonságok gyűjteményét listázza. vagy lekéri az azonosító alapján megadott tulajdonság részleteit. |
> | Action | Microsoft.ApiManagement/service/properties/write | Létrehoz vagy frissít egy tulajdonságot. vagy frissíti az adott tulajdonságot. |
> | Action | Microsoft.ApiManagement/service/quotas/periods/read | Kvóta számláló értékének lekérése az időszakhoz |
> | Action | Microsoft.ApiManagement/service/quotas/periods/write | Kvóta számlálójának jelenlegi értékének beállítása |
> | Action | Microsoft.ApiManagement/service/quotas/read | Kvóta értékének beolvasása |
> | Action | Microsoft.ApiManagement/service/quotas/write | Kvóta számlálójának jelenlegi értékének beállítása |
> | Action | Microsoft.ApiManagement/service/read | API Management szolgáltatási példány metaadatainak olvasása |
> | Action | Microsoft.ApiManagement/service/regions/read | Felsorolja az összes olyan Azure-régiót, amelyben a szolgáltatás létezik. |
> | Action | Microsoft.ApiManagement/service/reports/read | Jelentések beolvasása időszakok szerint összesítve, vagy a jelentés beolvasása földrajzi régió szerint összesítve vagy a fejlesztők által összesített jelentés beolvasása.<br>vagy a jelentések összesítése termékek alapján.<br>vagy beolvashatja az API-k által összesített jelentést, vagy beolvashatja az Operations által összesített jelentést, vagy az előfizetés alapján összesítheti a jelentést.<br>vagy kérések beolvasása az adatjelentésekről |
> | Action | Microsoft.ApiManagement/service/restore/action | API Management szolgáltatás visszaállítása a megadott tárolóból egy felhasználó által megadott Storage-fiókban |
> | Action | Microsoft.ApiManagement/service/subscriptions/delete | Törli a megadott előfizetést. |
> | Action | Microsoft.ApiManagement/service/subscriptions/read | Az API Management Service-példány összes előfizetését listázza. vagy lekéri a megadott előfizetési entitást. |
> | Action | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Újragenerálta a API Management szolgáltatási példány meglévő előfizetésének elsődleges kulcsát. |
> | Action | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Újragenerálja a API Management szolgáltatási példány meglévő előfizetésének másodlagos kulcsát. |
> | Action | Microsoft.ApiManagement/service/subscriptions/write | Létrehozza vagy frissíti a megadott felhasználó előfizetését a megadott termékre. vagy frissíti az azonosító alapján megadott előfizetés részleteit. |
> | Action | Microsoft.ApiManagement/service/tagResources/read | A címkékhez társított erőforrások gyűjteményét listázza. |
> | Action | Microsoft.ApiManagement/service/tags/delete | Törli a API Management szolgáltatási példány adott címkéjét. |
> | Action | Microsoft.ApiManagement/service/tags/read | A szolgáltatási példányon belül definiált címkék gyűjteményét listázza. vagy lekéri az azonosító alapján megadott címke részleteit. |
> | Action | Microsoft.ApiManagement/service/tags/write | Létrehoz egy címkét. vagy frissíti az azonosító alapján megadott címke részleteit. |
> | Action | Microsoft.ApiManagement/service/templates/delete | Alapértelmezett API Management e-mail sablon alaphelyzetbe állítása |
> | Action | Microsoft.ApiManagement/service/templates/read | Az összes e-mail-sablon beolvasása vagy API Management e-mail sablon adatainak beolvasása |
> | Action | Microsoft.ApiManagement/service/templates/write | API Management e-mail-sablon létrehozása vagy frissítése API Management e-mail-sablon frissítése |
> | Action | Microsoft.ApiManagement/service/tenant/delete | A bérlő házirend-konfigurációjának eltávolítása |
> | Action | Microsoft.ApiManagement/service/tenant/deploy/action | Egy központi telepítési feladat futtatásával alkalmazza a megadott git-ág módosításait az adatbázisban található konfigurációra. |
> | Action | Microsoft.ApiManagement/service/tenant/operationResults/read | A művelet eredményeinek beolvasása vagy egy adott művelet eredményének beolvasása |
> | Action | Microsoft.ApiManagement/service/tenant/read | Az API Management szolgáltatás globális szabályzat-definíciójának beolvasása. vagy a bérlői hozzáférési információk részleteinek beolvasása |
> | Action | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Elsődleges elérési kulcs előállítása |
> | Action | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Másodlagos elérési kulcs újragenerálása |
> | Action | Microsoft.ApiManagement/service/tenant/save/action | Elkészíti a commit és a konfiguráció pillanatképét a tárházban lévő megadott ágra |
> | Action | Microsoft.ApiManagement/service/tenant/syncState/read | Az utolsó git-szinkronizálás állapotának beolvasása |
> | Action | Microsoft.ApiManagement/service/tenant/validate/action | Ellenőrzi a megadott git-ág változásait. |
> | Action | Microsoft.ApiManagement/service/tenant/write | Házirend-konfiguráció beállítása a bérlőhöz vagy a bérlői hozzáférési információk frissítése – részletek |
> | Action | Microsoft.ApiManagement/service/updatecertificate/action | API Management szolgáltatás SSL-tanúsítványának feltöltése |
> | Action | Microsoft.ApiManagement/service/updatehostname/action | API Management szolgáltatás egyéni tartománynevének beállítása, frissítése vagy eltávolítása |
> | Action | Microsoft.ApiManagement/service/users/action | Új felhasználó regisztrálása |
> | Action | Microsoft.ApiManagement/service/users/confirmations/send/action | Visszaigazolás küldése |
> | Action | Microsoft.ApiManagement/service/users/delete | Adott felhasználó törlése. |
> | Action | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Egy hitelesítési jogkivonatot tartalmazó átirányítási URL-címet kér le egy adott felhasználó a fejlesztői portálon való aláírásához. |
> | Action | Microsoft.ApiManagement/service/users/groups/read | Felsorolja az összes felhasználói csoportot. |
> | Action | Microsoft.ApiManagement/service/users/identities/read | Az összes felhasználói identitás listája. |
> | Action | Microsoft.ApiManagement/service/users/keys/read | Felhasználóhoz tartozó kulcsok beolvasása |
> | Action | Microsoft.ApiManagement/service/users/read | Felsorolja a megadott szolgáltatási példányban regisztrált felhasználók gyűjteményét. vagy lekéri a felhasználó azonosítójában megadott adatokat. |
> | Action | Microsoft.ApiManagement/service/users/subscriptions/read | Felsorolja a megadott felhasználó előfizetéseit. |
> | Action | Microsoft.ApiManagement/service/users/token/action | A felhasználó megosztott hozzáférési engedélyezési jogkivonatának beolvasása. |
> | Action | Microsoft.ApiManagement/service/users/write | Létrehoz vagy frissít egy felhasználót. vagy frissíti az azonosító alapján megadott felhasználó részleteit. |
> | Action | Microsoft.ApiManagement/service/write | API Management szolgáltatás új példányának létrehozása |
> | Action | Microsoft.ApiManagement/unregister/action | A Microsoft. ApiManagement erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának megszüntetése |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Authorization/classicAdministrators/delete | Az előfizetés rendszergazdájának eltávolítása. |
> | Action | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Az előfizetésben beállított rendszergazda műveleti állapotainak beolvasása. |
> | Action | Microsoft.Authorization/classicAdministrators/read | Az előfizetés rendszergazdáinak beolvasása. |
> | Action | Microsoft.Authorization/classicAdministrators/write | Az előfizetés rendszergazdájának beállítása vagy módosítása. |
> | Action | Microsoft. Authorization/denyAssignments/delete | Elutasítási hozzárendelés törlése a megadott hatókörben. |
> | Action | Microsoft. Authorization/denyAssignments/olvasás | Elutasítási hozzárendelés adatainak beolvasása. |
> | Action | Microsoft. Authorization/denyAssignments/írás | Elutasítási hozzárendelés létrehozása a megadott hatókörben. |
> | Action | Microsoft. Authorization/elevateAccess/művelet | Felhasználói hozzáférés rendszergazdai szerepkörének megadása a hívónak a bérlői hatókörben |
> | Action | Microsoft.Authorization/locks/delete | A megadott hatókör zárolásainak törlése. |
> | Action | Microsoft.Authorization/locks/read | A megadott hatókör zárolásainak beolvasása. |
> | Action | Microsoft. Authorization/zárolás/írás | A megadott hatókör zárolásainak beállítása. |
> | Action | Microsoft.Authorization/operations/read | A műveletek listájának beolvasása |
> | Action | Microsoft. Authorization/engedélyek/olvasás | A hívó adott hatókörben érvényes engedélyeinek listázása. |
> | Action | Microsoft.Authorization/policyAssignments/delete | Szabályzat-hozzárendelés törlése a megadott hatókörben. |
> | Action | Microsoft. Authorization/policyAssignments/olvasás | Szabályzat-hozzárendelés adatainak lekérése. |
> | Action | Microsoft. Authorization/policyAssignments/írás | Szabályzat-hozzárendelés létrehozása a megadott hatókörben. |
> | Action | Microsoft.Authorization/policyDefinitions/delete | Szabályzat-definíció törlése. |
> | Action | Microsoft.Authorization/policyDefinitions/read | Szabályzat-definíció adatainak lekérése. |
> | Action | Microsoft.Authorization/policyDefinitions/write | Egyéni szabályzat-definíció létrehozása. |
> | Action | Microsoft.Authorization/policySetDefinitions/delete | Szabályzatkészlet-definíció törlése. |
> | Action | Microsoft.Authorization/policySetDefinitions/read | Egy szabályzatkészlet-definíció adatainak beolvasása. |
> | Action | Microsoft.Authorization/policySetDefinitions/write | Egyéni szabályzatkészlet-definíció létrehozása. |
> | Action | Microsoft.Authorization/providerOperations/read | Az összes, szerepkör-definíciókban használható erőforrás-szolgáltató műveleteinek beolvasása. |
> | Action | Microsoft.Authorization/roleAssignments/delete | Szerepkör-hozzárendelés törlése a megadott hatókörből. |
> | Action | Microsoft.Authorization/roleAssignments/read | Információk beolvasása a szerepkör-hozzárendelésről. |
> | Action | Microsoft.Authorization/roleAssignments/write | Szerepkör-hozzárendelés létrehozása a megadott hatókörben. |
> | Action | Microsoft.Authorization/roleDefinitions/delete | A megadott egyéni szerepkör-definíció törlése. |
> | Action | Microsoft.Authorization/roleDefinitions/read | Szerepkör-definíció adatainak beolvasása. |
> | Action | Microsoft. Authorization/roleDefinitions/írás | Egy egyéni szerepkör-definíció létrehozása vagy módosítása a megadott engedélyekkel és hozzárendelhető hatókörökkel. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Azure Automation DSC regisztrációs adatainak beolvasása |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Azure Automation DSC-kulcsok újragenerálása iránti kérés írása |
> | Action | Microsoft.Automation/automationAccounts/certificates/delete | Azure Automation tanúsítvány törlése |
> | Action | Microsoft.Automation/automationAccounts/certificates/getCount/action | A tanúsítványok számának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/certificates/read | Azure Automation-tanúsítvány beolvasása |
> | Action | Microsoft.Automation/automationAccounts/certificates/write | Létrehoz vagy frissít egy Azure Automation tanúsítvány-eszközt |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC fordításának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/read | Egy Azure Automation DSC fordításának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC fordításának írása |
> | Action | Microsoft.Automation/automationAccounts/compilationjobs/write | Egy Azure Automation DSC fordításának írása |
> | Action | Microsoft.Automation/automationAccounts/configurations/content/read | A konfigurációs adathordozó tartalmának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/configurations/delete | Azure Automation DSC tartalmának törlése |
> | Action | Microsoft.Automation/automationAccounts/configurations/getCount/action | Beolvas egy Azure Automation DSC tartalmának számát |
> | Action | Microsoft.Automation/automationAccounts/configurations/read | Azure Automation DSC tartalmának beolvasása |
> | Action | Microsoft. Automation/automationAccounts/konfigurációk/írás | Egy Azure Automation DSC tartalmának írása |
> | Action | Microsoft.Automation/automationAccounts/connections/delete | Azure Automation-összekötő törlése |
> | Action | Microsoft.Automation/automationAccounts/connections/getCount/action | A kapcsolatok számának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/connections/read | Azure Automation a kapcsolatok adategységének beolvasása |
> | Action | Microsoft.Automation/automationAccounts/connections/write | Létrehoz vagy frissít egy Azure Automation-összekötő eszközt |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/delete | Egy Azure Automation típusú kapcsolattípus-eszköz törlése |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/read | Azure Automation kapcsolattípus-eszköz beolvasása |
> | Action | Microsoft.Automation/automationAccounts/connectionTypes/write | Létrehoz egy Azure Automation kapcsolattípus-eszközt |
> | Action | Microsoft.Automation/automationAccounts/credentials/delete | Azure Automation hitelesítőadat-eszköz törlése |
> | Action | Microsoft.Automation/automationAccounts/credentials/getCount/action | A hitelesítő adatok számának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/credentials/read | Azure Automation hitelesítőadat-eszköz beolvasása |
> | Action | Microsoft.Automation/automationAccounts/credentials/write | Egy Azure Automation hitelesítőadat-eszköz létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/delete | Azure Automation fiók törlése |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Hibrid Runbook-feldolgozó erőforrások törlése |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Hibrid Runbook Worker-erőforrások beolvasása |
> | Action | Microsoft.Automation/automationAccounts/jobs/output/read | A feladatok kimenetének beolvasása |
> | Action | Microsoft.Automation/automationAccounts/jobs/read | Azure Automation feladatok beolvasása |
> | Action | Microsoft.Automation/automationAccounts/jobs/resume/action | Egy Azure Automation feladatot folytat |
> | Action | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Lekéri a Azure Automation runbook tartalmát a feladatok végrehajtásának időpontjában. |
> | Action | Microsoft.Automation/automationAccounts/jobs/stop/action | Leállítja egy Azure Automation feladatot |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Beolvas egy Azure Automation-feladatok streamjét |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Beolvas egy Azure Automation-feladatok streamjét |
> | Action | Microsoft.Automation/automationAccounts/jobs/suspend/action | Azure Automation feladatok felfüggesztése |
> | Action | Microsoft.Automation/automationAccounts/jobs/write | Létrehoz egy Azure Automation feladatot |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/delete | Azure Automation feladatütemezés törlése |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/read | Azure Automation feladatütemezés beolvasása |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/write | Létrehoz egy Azure Automation feladatok ütemtervét |
> | Action | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Az Automation-fiókhoz kapcsolódó munkaterület beolvasása |
> | Action | Microsoft.Automation/automationAccounts/listKeys/action | Az Automation-fiók kulcsainak beolvasása |
> | Action | Microsoft.Automation/automationAccounts/modules/activities/read | Lekéri Azure Automation tevékenységeket |
> | Action | Microsoft.Automation/automationAccounts/modules/delete | Azure Automation PowerShell-modul törlése |
> | Action | Microsoft.Automation/automationAccounts/modules/getCount/action | Az Automation-fiókban lévő PowerShell-modulok számának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/modules/read | Azure Automation PowerShell-modul beolvasása |
> | Action | Microsoft.Automation/automationAccounts/modules/write | Egy Azure Automation PowerShell-modul létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Azure Automation DSC csomópont-konfigurációjának törlése |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Beolvas egy Azure Automation DSC csomópont-konfigurációs tartalmát |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Egy Azure Automation DSC csomópont-konfigurációjának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Egy Azure Automation DSC csomópont-konfigurációjának írása |
> | Action | Microsoft.Automation/automationAccounts/nodecounts/read | A megadott típushoz tartozó csomópontok számának összefoglalásának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/nodes/delete | Azure Automation DSC-csomópontok törlése |
> | Action | Microsoft.Automation/automationAccounts/nodes/read | Azure Automation DSC-csomópontok beolvasása |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Azure Automation DSC-jelentés tartalmának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/nodes/reports/read | Azure Automation DSC-jelentések beolvasása |
> | Action | Microsoft.Automation/automationAccounts/nodes/write | Azure Automation DSC-csomópontok létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Azure Automation TypeFields beolvasása |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/delete | Azure Automation Python 2 csomag törlése |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/read | Azure Automation Python 2 csomag beolvasása |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/write | Egy Azure Automation Python 2 csomag létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/delete | Azure Automation Python 3 csomag törlése |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/read | Azure Automation Python 3 csomag beolvasása |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/write | Egy Azure Automation Python 3 csomag létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/read | Azure Automation fiók beolvasása |
> | Action | Microsoft.Automation/automationAccounts/runbooks/content/read | Egy Azure Automation runbook tartalmának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/runbooks/delete | Azure Automation runbook törlése |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Egy Azure Automation runbook-Piszkozat tartalmának létrehozása |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Beolvassa Azure Automation runbook-Piszkozat műveleti eredményeit |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/read | Azure Automation runbook-tervezet beolvasása |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Beolvas egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Egy Azure Automation runbook-Piszkozat tesztelési feladatot folytat |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Leállít egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Egy Azure Automation runbook-Piszkozat tesztelési feladata felfüggesztése |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Létrehoz egy Azure Automation runbook-Piszkozat tesztelési feladatot |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Módosítások visszavonása egy Azure Automation runbook-piszkozatra |
> | Action | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Lekéri Azure Automation runbookok számát |
> | Action | Microsoft.Automation/automationAccounts/runbooks/publish/action | Azure Automation runbook-Piszkozat közzététele |
> | Action | Microsoft.Automation/automationAccounts/runbooks/read | Azure Automation runbook beolvasása |
> | Action | Microsoft.Automation/automationAccounts/runbooks/write | Létrehoz vagy frissít egy Azure Automation runbook |
> | Action | Microsoft.Automation/automationAccounts/schedules/delete | Azure Automation ütemezett eszköz törlése |
> | Action | Microsoft.Automation/automationAccounts/schedules/getCount/action | Beolvassa Azure Automation ütemtervek számát |
> | Action | Microsoft.Automation/automationAccounts/schedules/read | Azure Automation ütemezett eszköz beolvasása |
> | Action | Microsoft.Automation/automationAccounts/schedules/write | Egy Azure Automation Schedule-eszköz létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Azure Automation szoftverfrissítés konfigurációjának törlése |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Azure Automation szoftverfrissítés konfigurációjának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Azure Automation szoftverfrissítési konfiguráció létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/statistics/read | Azure Automation statisztikájának beolvasása |
> | Action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Azure Automation frissítési üzembe helyezési gép beszerzése |
> | Action | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Beolvas egy Azure Automation Update Management-javítási feladatot |
> | Action | Microsoft.Automation/automationAccounts/usages/read | Lekéri Azure Automation használatát |
> | Action | Microsoft.Automation/automationAccounts/variables/delete | Azure Automation változó eszköz törlése |
> | Action | Microsoft.Automation/automationAccounts/variables/read | Egy Azure Automation változó eszköz beolvasása |
> | Action | Microsoft. Automation/automationAccounts/változók/írás | Egy Azure Automation változó eszköz létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/watchers/delete | Azure Automation Watcher-feladatok törlése |
> | Action | Microsoft.Automation/automationAccounts/watchers/read | Beolvas egy Azure Automation figyelő feladatot |
> | Action | Microsoft.Automation/automationAccounts/watchers/start/action | Azure Automation Watcher-feladatok indítása |
> | Action | Microsoft.Automation/automationAccounts/watchers/stop/action | Azure Automation Watcher-feladatok leállítása |
> | Action | Microsoft.Automation/automationAccounts/watchers/streams/read | Beolvas egy Azure Automation megfigyelői feladatok streamjét |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Azure Automation figyelőkkel kapcsolatos feladatok törlése |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Azure Automation figyelőkkel kapcsolatos feladatok beolvasása |
> | Action | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Azure Automation figyelőkkel kapcsolatos feladatok létrehozása |
> | Action | Microsoft.Automation/automationAccounts/watchers/write | Létrehoz egy Azure Automation figyelő feladatot |
> | Action | Microsoft.Automation/automationAccounts/webhooks/action | URI generálása egy Azure Automation webhookhoz |
> | Action | Microsoft.Automation/automationAccounts/webhooks/delete | Azure Automation webhook törlése  |
> | Action | Microsoft.Automation/automationAccounts/webhooks/read | Egy Azure Automation webhook beolvasása |
> | Action | Microsoft.Automation/automationAccounts/webhooks/write | Egy Azure Automation webhook létrehozása vagy frissítése |
> | Action | Microsoft.Automation/automationAccounts/write | Egy Azure Automation fiók létrehozása vagy frissítése |
> | Action | Microsoft.Automation/operations/read | A Azure Automation erőforrásaihoz elérhető műveletek beolvasása |
> | Action | Microsoft.Automation/register/action | Az előfizetés regisztrálása Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/delete | B2C-címtárerőforrás törlése |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/read | B2C-címtárerőforrás megtekintése |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/write | B2C-címtár erőforrásának létrehozása vagy frissítése |
> | Action | Microsoft.AzureActiveDirectory/b2ctenants/read | Az összes olyan B2C-bérlő listázása, amelynek a felhasználó tagja |
> | Action | Microsoft.AzureActiveDirectory/operations/read | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz választható összes API-művelet olvasása |
> | Action | Microsoft.AzureActiveDirectory/register/action | A Microsoft.AzureActiveDirectory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.AzureStack/Operations/read | Erőforrás-szolgáltatói művelet tulajdonságainak beolvasása |
> | Action | Microsoft.AzureStack/register/action | Előfizetés regisztrálása a Microsoft. AzureStack erőforrás-szolgáltatónál |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Azure Stack ügyfél-előfizetés törlése |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/read | Lekéri egy Azure Stack ügyfél-előfizetés tulajdonságait |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/write | Létrehoz vagy frissít egy Azure Stack ügyfél-előfizetést |
> | Action | Microsoft.AzureStack/registrations/delete | Azure Stack Regisztráció törlése |
> | Action | Microsoft.AzureStack/registrations/getActivationKey/action | A legújabb Azure Stack aktiválási kulcs beolvasása |
> | Action | Microsoft.AzureStack/registrations/products/listDetails/action | Azure Stack Marketplace-termék részletes adatainak beolvasása |
> | Action | Microsoft.AzureStack/registrations/products/read | Egy Azure Stack Marketplace-termék tulajdonságainak beolvasása |
> | Action | Microsoft.AzureStack/registrations/read | Egy Azure Stack regisztráció tulajdonságainak beolvasása |
> | Action | Microsoft.AzureStack/registrations/write | Létrehoz vagy frissít egy Azure Stack regisztrációt |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Batch/batchAccounts/applications/delete | Egy alkalmazás törlése |
> | Action | Microsoft. batch/batchAccounts/alkalmazások/olvasás | Alkalmazások felsorolása vagy egy alkalmazás tulajdonságainak beolvasása |
> | Action | Microsoft. batch/batchAccounts/alkalmazások/verziók/aktiválás/művelet | Alkalmazás-csomag aktiválása |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/delete | Alkalmazáscsomag törlése |
> | Action | Microsoft. batch/batchAccounts/alkalmazások/verziók/olvasás | Egy alkalmazáscsomag tulajdonságainak beolvasása |
> | Action | Microsoft. batch/batchAccounts/alkalmazások/verziók/írás | Új alkalmazáscsomag létrehozása vagy meglévő alkalmazáscsomag frissítése |
> | Action | Microsoft. batch/batchAccounts/alkalmazások/írás | Új alkalmazás létrehozása vagy meglévő alkalmazás frissítése |
> | Action | Microsoft. batch/batchAccounts/certificateOperationResults/olvasás | Egy hosszú ideig futó tanúsítvány-művelet eredményének beolvasása batch-fiókon |
> | Action | Microsoft. batch/batchAccounts/Certificates/cancelDelete/Action | Egy batch-fiókban lévő tanúsítvány sikertelen törlésének megszakítása |
> | Action | Microsoft. batch/batchAccounts/tanúsítványok/törlés | Tanúsítvány törlése batch-fiókból |
> | Action | Microsoft. batch/batchAccounts/tanúsítványok/olvasás | A Batch-fiókban lévő tanúsítványok listája vagy a tanúsítvány tulajdonságainak beolvasása |
> | Action | Microsoft. batch/batchAccounts/tanúsítványok/írás | Új tanúsítvány létrehozása batch-fiókon vagy meglévő tanúsítvány frissítése |
> | Action | Microsoft. batch/batchAccounts/delete | Batch-fiók törlése |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/törlés | Feladatot töröl egy batch-fiókból |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/olvasás | A Batch-fiókban lévő feladatok felsorolása vagy a feladat tulajdonságainak beolvasása |
> | DataAction | Microsoft. batch/batchAccounts/feladatok/írás | Új feladatot hoz létre egy batch-fiókon, vagy frissít egy meglévő feladatot. |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/delete | Ütemezett feladatütemezés törlése batch-fiókból |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/olvasás | Egy batch-fiókban lévő feladatok ütemezett listáját vagy egy feladatütemezés tulajdonságainak beolvasása |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/írás | Új feladatütemezés létrehozása batch-fiókon vagy meglévő feladatütemezés frissítése |
> | Action | Microsoft. batch/batchAccounts/listkeys műveletének beolvasása/művelet | Egy batch-fiók hozzáférési kulcsainak listázása |
> | Action | Microsoft. batch/batchAccounts/operationResults/olvasás | Egy hosszú ideig futó batch-fiók eredményének beolvasása |
> | Action | Microsoft. batch/batchAccounts/poolOperationResults/olvasás | Egy hosszú ideig futó készlet műveletének eredményét jeleníti meg egy batch-fiókban |
> | Action | Microsoft. batch/batchAccounts/készletek/törlés | Készlet törlése batch-fiókból |
> | Action | Microsoft. batch/batchAccounts/készletek/disableAutoscale/művelet | Letiltja a Batch-fiókok automatikus méretezését |
> | Action | Microsoft. batch/batchAccounts/készletek/olvasás | A Batch-fiókban lévő készletek felsorolása vagy a készlet tulajdonságainak beolvasása |
> | Action | Microsoft. batch/batchAccounts/készletek/stopResize/művelet | Egy folyamatban lévő átméretezési művelet leállítása egy batch-fiók készletén |
> | Action | Microsoft. batch/batchAccounts/készletek/írás | Létrehoz egy új készletet egy batch-fiókon, vagy frissít egy meglévő készletet. |
> | Action | Microsoft. batch/batchAccounts/olvasás | Batch-fiókokat listáz, vagy egy batch-fiók tulajdonságait kéri le. |
> | Action | Microsoft. batch/batchAccounts/regeneratekeys/művelet | Egy batch-fiók hozzáférési kulcsainak újragenerálása |
> | Action | Microsoft. batch/batchAccounts/syncAutoStorageKeys/művelet | A Batch-fiókhoz konfigurált automatikus Storage-fiókhoz tartozó hozzáférési kulcsok szinkronizálása |
> | Action | Microsoft. batch/batchAccounts/írás | Új batch-fiók létrehozása vagy meglévő batch-fiók frissítése |
> | Action | Microsoft. batch/Locations/accountOperationResults/READ | Egy hosszú ideig futó batch-fiók eredményének beolvasása |
> | Action | Microsoft. batch/Locations/checkNameAvailability/Action | Ellenőrzi, hogy a fiók neve érvényes-e, és nincs-e használatban. |
> | Action | Microsoft. batch/helyszínek/kvóták/olvasás | A megadott előfizetéshez tartozó Batch-kvóták beolvasása a megadott Azure-régióban |
> | Action | Microsoft. batch/Operations/READ | A Microsoft. batch erőforrás-szolgáltatón elérhető műveletek listája |
> | Action | Microsoft. batch/regisztráció/művelet | Regisztrálja az előfizetést a Batch erőforrás-szolgáltató számára, és lehetővé teszi a Batch-fiókok létrehozását. |
> | Action | Microsoft. batch/regisztráció/művelet | A Batch erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése a Batch-fiókok létrehozásának megakadályozása érdekében |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. Billing/billingAccounts/részlegek/olvasás | A Számlázási fiók hatókörében lévő összes részleg listázása |
> | Action | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | A Számlázási fiók hatóköre alatt lévő összes regisztrációs fiók listázása |
> | Action | Microsoft. számlázás/billingAccounts/olvasás | Azon számlázási fiókok listázása, amelyekhez a felhasználónak hozzáférése van |
> | Action | Microsoft. számlázás/billingPeriods/olvasás | Az elérhető számlázási időszakok felsorolása |
> | Action | Microsoft. Billing/részlegek/olvasás | Azon részlegek listázása, amelyekhez a felhasználó hozzáfér |
> | Action | Microsoft. számlázás/számlák/olvasás | Elérhető számlák listája |
> | Action | Microsoft. számlázás/regisztráció/művelet | Előfizetés regisztrálása a Microsoft. számlázási erőforrás-szolgáltatónál |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.BingMaps/mapApis/Delete | Törlési művelet |
> | Action | Microsoft.BingMaps/mapApis/listSecrets/action | A titkos kulcsok listázása |
> | Action | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Az erőforráshoz tartozó egyszeri bejelentkezési engedélyezési jogkivonat beolvasása |
> | Action | Microsoft.BingMaps/mapApis/Read | Olvasási művelet |
> | Action | Microsoft.BingMaps/mapApis/regenerateKey/action | A kulcs újragenerálása |
> | Action | Microsoft.BingMaps/mapApis/Write | Írási művelet |
> | Action | Microsoft.BingMaps/Operations/read | A művelet leírása. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. Blockchain/blockchainMembers/delete | Egy meglévő Blockchain-tag törlése. |
> | Action | Microsoft. Blockchain/blockchainMembers/listApiKeys/művelet | Lekérdezi vagy felsorolja a meglévő Blockchain-tag API-kulcsait. |
> | Action | Microsoft. Blockchain/blockchainMembers/READ | Lekérdezi vagy felsorolja a meglévő Blockchain-tag (oka) t. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Csatlakozás egy Blockchain-tag tranzakciós csomóponthoz. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Töröl egy meglévő Blockchain-tag tranzakciós csomópontot. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Lekérdezi vagy felsorolja a meglévő Blockchain-tagok tranzakciós csomópontjának API-kulcsait. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Lekérdezi vagy felsorolja a meglévő Blockchain-tag tranzakciós csomópont (oka) t. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Létrehoz vagy frissít egy Blockchain-tag tranzakciós csomópontot. |
> | Action | Microsoft. Blockchain/blockchainMembers/Write | Egy Blockchain-tag létrehozása vagy frissítése. |
> | Action | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Lekéri a Blockchain tagjainak működési eredményeit. |
> | Action | Microsoft. Blockchain/Locations/checkNameAvailability/Action | Ellenőrzi, hogy az erőforrás neve érvényes-e, és nincs-e használatban. |
> | Action | Microsoft. Blockchain/Operations/READ | A Microsoft Blockchain erőforrás-szolgáltató összes műveletének listázása. |
> | Action | Microsoft. Blockchain/regisztráció/művelet | Regisztrálja az előfizetést a Blockchain erőforrás-szolgáltatóhoz. |

## <a name="microsoftblueprint"></a>Microsoft. Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. Blueprint/blueprintAssignments/assignmentOperations/READ | Tetszőleges tervelemek olvasása |
> | Action | Microsoft. Blueprint/blueprintAssignments/delete | Tetszőleges tervelemek törlése |
> | Action | Microsoft. Blueprint/blueprintAssignments/READ | Tetszőleges tervelemek olvasása |
> | Action | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Azure Blueprints-szolgáltatásnév objektumazonosítójának lekérdezése. |
> | Action | Microsoft. Blueprint/blueprintAssignments/Write | Tetszőleges tervelemek létrehozása vagy frissítése |
> | Action | Microsoft. Blueprint/tervezetek/összetevők/törlés | Tetszőleges tervelemek törlése |
> | Action | Microsoft. Blueprint/tervezetek/összetevők/olvasás | Tetszőleges tervelemek olvasása |
> | Action | Microsoft. Blueprint/tervezetek/összetevők/írás | Tetszőleges tervelemek létrehozása vagy frissítése |
> | Action | Microsoft. Blueprint/tervezetek/törlés | Tetszőleges tervek törlése |
> | Action | Microsoft. Blueprint/tervezetek/olvasás | Tetszőleges tervek olvasása |
> | Action | Microsoft. Blueprint/tervezetek/verziók/összetevők/olvasás | Tetszőleges tervelemek olvasása |
> | Action | Microsoft. Blueprint/tervezetek/verziók/törlés | Tetszőleges tervek törlése |
> | Action | Microsoft. Blueprint/tervezetek/verziók/olvasás | Tetszőleges tervek olvasása |
> | Action | Microsoft. Blueprint/tervezetek/verziók/írás | Tetszőleges tervek létrehozása vagy frissítése |
> | Action | Microsoft. Blueprint/tervezetek/írás | Tetszőleges tervek létrehozása vagy frissítése |
> | Action | Microsoft. Blueprint/regisztráció/művelet | Az Azure Blueprints erőforrás-szolgáltatójának regisztrálása |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. BotService/botServices/csatornák/törlés | Bot-szolgáltatás törlése |
> | Action | Microsoft. BotService/botServices/csatornák/olvasás | Robot szolgáltatás beolvasása |
> | Action | Microsoft. BotService/botServices/csatornák/írás | Bot-szolgáltatás írása |
> | Action | Microsoft. BotService/botServices/Connections/delete | Bot-szolgáltatás törlése |
> | Action | Microsoft. BotService/botServices/Connections/READ | Robot szolgáltatás beolvasása |
> | Action | Microsoft. BotService/botServices/Connections/Write | Bot-szolgáltatás írása |
> | Action | Microsoft.BotService/botServices/delete | Bot-szolgáltatás törlése |
> | Action | Microsoft. BotService/botServices/READ | Robot szolgáltatás beolvasása |
> | Action | Microsoft. BotService/botServices/Write | Bot-szolgáltatás írása |
> | Action | Microsoft. BotService/Locations/operationresults/READ | Aszinkron művelet állapotának beolvasása |
> | Action | Microsoft. BotService/Operations/READ | Az összes erőforrástípus műveleteinek beolvasása |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Cache/checknameavailability/action | Annak ellenőrzése, hogy a név rendelkezésre áll-e egy új Redis Cache-hez való használatra |
> | Action | Microsoft.Cache/locations/operationresults/read | Egy olyan hosszú ideje futó művelet eredményének beolvasása, amelynek esetében a Location (Hely) fejléc vissza lett küldve az ügyfélnek |
> | Action | Microsoft.Cache/operations/read | A Microsoft.Cache szolgáltató által támogatott műveletek listázása. |
> | Action | Microsoft.Cache/redis/delete | Teljes Redis gyorsítótár törlése |
> | Action | Microsoft.Cache/redis/export/action | Redis-adatok exportálása előre megadott tárolási blobokba meghatározott formátumban |
> | Action | Microsoft.Cache/redis/firewallRules/delete | Egy Redis Cache IP-tűzfalszabályainak törlése |
> | Action | Microsoft.Cache/redis/firewallRules/read | Egy Redis Cache IP-tűzfalszabályainak beolvasása |
> | Action | Microsoft.Cache/redis/firewallRules/write | Egy Redis Cache IP-tűzfalszabályainak szerkesztése |
> | Action | Microsoft.Cache/redis/forceReboot/action | Egy gyorsítótárpéldány kényszerített újraindítása, mely adatvesztést okozhat. |
> | Action | Microsoft.Cache/redis/import/action | Meghatározott formátumú adatok importálása a Redis szolgáltatásba több blobból |
> | Action | Microsoft.Cache/redis/linkedservers/delete | Egy Redis gyorsítótárhoz csatolt kiszolgáló törlése |
> | Action | Microsoft.Cache/redis/linkedservers/read | Egy Redis gyorsítótárhoz hozzárendelt csatolt kiszolgálók beolvasása. |
> | Action | Microsoft.Cache/redis/linkedservers/write | Csatolt kiszolgáló hozzáadása egy Redis gyorsítótárhoz |
> | Action | Microsoft.Cache/redis/listKeys/action | Redis gyorsítótár elérésikulcs-értékeinek megtekintése a felügyeleti portálon |
> | Action | Microsoft.Cache/redis/listUpgradeNotifications/read | A gyorsítótár bérlője legutóbbi csomagváltási értesítéseinek listázása. |
> | Action | Microsoft.Cache/redis/metricDefinitions/read | Egy Redis gyorsítótárhoz elérhető metrikai meghatározások beolvasása |
> | Action | Microsoft.Cache/redis/patchSchedules/delete | Egy Redis Cache javítási ütemezésének törlése |
> | Action | Microsoft.Cache/redis/patchSchedules/read | Egy Redis Cache javítási ütemezésének beolvasása |
> | Action | Microsoft.Cache/redis/patchSchedules/write | Egy Redis Cache javítási ütemezésének módosítása |
> | Action | Microsoft.Cache/redis/read | Redis gyorsítótár beállításainak és konfigurációjának megtekintése a felügyeleti portálon |
> | Action | Microsoft.Cache/redis/regenerateKey/action | Redis gyorsítótár elérésikulcs-értékeinek módosítása a felügyeleti portálon |
> | Action | Microsoft.Cache/redis/start/action | Gyorsítótárpéldány elindítása. |
> | Action | Microsoft.Cache/redis/stop/action | Gyorsítótárpéldány leállítása. |
> | Action | Microsoft.Cache/redis/write | Redis gyorsítótár beállításainak és konfigurációjának módosítása a felügyeleti portálon |
> | Action | Microsoft.Cache/register/action | A Microsoft.Cache erőforrás-szolgáltató regisztrálása egy előfizetéshez |
> | Action | Microsoft.Cache/unregister/action | A Microsoft.Cache erőforrás-szolgáltató egy előfizetéshez tartozó regisztrációjának törlése |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Capacity/appliedreservations/read | Összes foglalás olvasása |
> | Action | Microsoft.Capacity/calculateexchange/action | Kiszámítja az új vásárlások árfolyamát és árát, és visszaadja a házirend-hibákat. |
> | Action | Microsoft. Capacity/calculateprice/művelet | A foglalás árának kiszámítása |
> | Action | Microsoft.Capacity/catalogs/read | Foglalási katalógus olvasása |
> | Action | Microsoft. Capacity/checkoffers/művelet | Előfizetési ajánlatok keresése |
> | Action | Microsoft.Capacity/checkscopes/action | Előfizetés keresése |
> | Action | Microsoft. Capacity/commercialreservationorders/olvasás | Bármely Bérlőben létrehozott foglalási megrendelések beolvasása |
> | Action | Microsoft.Capacity/exchange/action | Bármilyen foglalás cseréje |
> | Action | Microsoft.Capacity/operations/read | Bármilyen művelet beolvasása |
> | Action | Microsoft.Capacity/register/action | Regisztrálja a kapacitás erőforrás-szolgáltatót, és lehetővé teszi a kapacitás-erőforrások létrehozását. |
> | Action | Microsoft.Capacity/reservationorders/action | Bármely foglalás frissítése |
> | Action | Microsoft.Capacity/reservationorders/availablescopes/action | Bármely elérhető hatókör megkeresése |
> | Action | Microsoft.Capacity/reservationorders/calculaterefund/action | Kiszámítja a visszatérítés összegét és az új vásárlás árát, és visszaadja a házirend-hibákat. |
> | Action | Microsoft.Capacity/reservationorders/delete | Foglalás törlése |
> | Action | Microsoft.Capacity/reservationorders/merge/action | Bármilyen foglalás egyesítése |
> | Action | Microsoft.Capacity/reservationorders/read | Összes foglalás olvasása |
> | Action | Microsoft.Capacity/reservationorders/reservations/action | Bármely foglalás frissítése |
> | Action | Microsoft.Capacity/reservationorders/reservations/delete | Foglalás törlése |
> | Action | Microsoft.Capacity/reservationorders/reservations/read | Összes foglalás olvasása |
> | Action | Microsoft.Capacity/reservationorders/reservations/revisions/read | Összes foglalás olvasása |
> | Action | Microsoft.Capacity/reservationorders/reservations/write | Foglalás létrehozása |
> | Action | Microsoft.Capacity/reservationorders/return/action | Bármilyen foglalás visszaküldése |
> | Action | Microsoft.Capacity/reservationorders/split/action | Foglalás felosztása |
> | Action | Microsoft.Capacity/reservationorders/swap/action | Bármilyen foglalás cseréje |
> | Action | Microsoft.Capacity/reservationorders/write | Foglalás létrehozása |
> | Action | Microsoft.Capacity/tenants/register/action | Bármely bérlő regisztrálása |
> | Action | Microsoft.Capacity/unregister/action | Bérlő regisztrációjának törlése |
> | Action | Microsoft.Capacity/validatereservationorder/action | Bármely foglalás ellenőrzése |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. CDN/CheckNameAvailability/művelet |  |
> | Action | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/edgenodes/delete |  |
> | Action | Microsoft.Cdn/edgenodes/read |  |
> | Action | Microsoft. CDN/edgenodes/írás |  |
> | Action | Microsoft.Cdn/operationresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Action | Microsoft.Cdn/operationresults/read |  |
> | Action | Microsoft.Cdn/operationresults/write |  |
> | Action | Microsoft.Cdn/operations/read |  |
> | Action | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/read |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/write |  |
> | Action | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft. CDN/profilok/olvasás |  |
> | Action | Microsoft. CDN/profilok/írás |  |
> | Action | Microsoft.Cdn/register/action | Az előfizetés regisztrálása a CDN erőforrás-szolgáltatónál, és a CDN-profilok létrehozásának engedélyezése. |
> | Action | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Meglévő tanúsítvány törlése |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | A tanúsítványok listájának beolvasása |
> | Action | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Új tanúsítvány hozzáadása vagy meglévő frissítése |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Delete | Meglévő AppServiceCertificate törlése |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Read | A Tanúsítványrendelések listájának beolvasása |
> | Action | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Meglévő tanúsítványrendelési újrakibocsátása |
> | Action | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Meglévő tanúsítványrendelési megújítása |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Tanúsítvány e-mail újraküldése |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Kérelem e-mail-címének újraküldése egy másik e-mail-címre |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Hely zárolásának beolvasása egy kiállított App Service-tanúsítvány |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | A tanúsítványkérelmek listájának beolvasása |
> | Action | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Tanúsítvány e-mail-előzményeinek beolvasása |
> | Action | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Tartomány tulajdonjogának ellenőrzése |
> | Action | Microsoft.CertificateRegistration/certificateOrders/Write | Új Tanúsítványrendelési hozzáadása vagy meglévő frissítése |
> | Action | Microsoft.CertificateRegistration/operations/Read | Az App Service-tanúsítvány regisztrációjának összes műveletének listázása |
> | Action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Egyszerű szolgáltatásnév kiépítése a Service app számára |
> | Action | Microsoft.CertificateRegistration/register/action | Regisztrálja az előfizetéshez tartozó Microsoft-tanúsítványok erőforrás-szolgáltatóját |
> | Action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Tanúsítvány-megvásárlási objektum ellenőrzése az elküldése nélkül |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ClassicCompute/capabilities/read | Képességek megjelenítése |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Az adott tartománynév elérhetőségének ellenőrzése. |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Egy adott tartománynév elérhetőségének beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/active/write | Az aktív tartománynév beállítása. |
> | Action | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Az erőforrás rendelkezésre állási csoportjának megjelenítése. |
> | Action | Microsoft.ClassicCompute/domainNames/capabilities/read | A tartománynév-képességek megjelenítése |
> | Action | Microsoft.ClassicCompute/domainNames/delete | Erőforrások tartománynevének eltávolítása. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Üzembe helyezési pontok megjelenítése. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Szerepkör lekérése a tartománynév üzembe helyezési pontján |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Szerepkörpéldány beolvasása egy szerepkörhöz a tartománynév üzembe helyezési pontján |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Az üzembe helyezési pont állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Az üzembe helyezési pont állapotának hozzáadása. |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Frissítési tartomány lekérésére a tartománynév üzembe helyezési pontjához |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Frissítési tartomány frissítése a tartománynév üzembe helyezési pontján |
> | Action | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Telepítés létrehozása vagy frissítése. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/delete | A tartománynév-kiterjesztések eltávolítása. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/read | A tartománynév-kiterjesztéseket adja vissza. |
> | Action | Microsoft.ClassicCompute/domainNames/extensions/write | Tartománynév-kiterjesztések hozzáadása. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Új belső terheléselosztás eltávolítása. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | A tartománynevek belső terheléselosztói műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | A belső terheléselosztók beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Új belső terheléselosztás létrehozása. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | A tartománynevek elosztott terhelésű végpontcsoportjai műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Az elosztott terhelésű végpontcsoportok beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Az elosztott terhelésű végpontcsoport hozzáadása. |
> | Action | Microsoft.ClassicCompute/domainNames/operationstatuses/read | A tartománynév műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/operationStatuses/read | A tartománynév-kiterjesztések műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/read | Erőforrások tartománynevének visszaadása. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Az alkalmazott szolgáltatási tanúsítványok törlése. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | A tartománynevek szolgáltatási tanúsítványai műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Az alkalmazott szolgáltatási tanúsítványok visszaadása. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Alkalmazott szolgáltatási tanúsítványok hozzáadása vagy módosítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Üzembe helyezési pont migrálásának megszakítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Üzembe helyezési pont migrálásának véglegesítése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/delete | A megadott üzembe helyezési pont törlése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | A tartománynevek üzembe helyezési pontjai műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Üzembe helyezési pont migrálásának előkészítése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/read | Üzembe helyezési pontok megjelenítése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Eltávolítja az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepkörei kiterjesztéshivatkozásai műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozását adja vissza. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Az üzembe helyezési ponti szerepkör kiterjesztéshivatkozásának hozzáadása vagy módosítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | A tartománynév szerepköre metrikadefiníciójának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | A tartománynév szerepkörmetrikájának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | A tartománynevek üzembe helyezési ponti szerepköre műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/read | Üzembe helyezési pont szerepkörének beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | A tartománynév üzembe helyezési ponti szerepköre szerepkörpéldánya távoli asztali kapcsolatfájljának letöltése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | A tartománynevek üzembe helyezési ponti szerepköre szerepkörpéldánya műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Szerepkörpéldány beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | A szerepkörpéldány újraépítése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Szerepkörpéldány rendszerképének alaphelyzetbe állítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | A szerepkörpéldányok újraindítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Az üzembe helyezési pont szerepköre termékváltozatának beolvasása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/write | Az üzembe helyezési pont szerepkörének hozzáadása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/start/action | Üzembe helyezési pont indítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Az üzembe helyezési pont állapotának leállítottra állítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Az üzembe helyezési pont állapotának elindítottra állítása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/stop/action | Üzembe helyezési pont felfüggesztése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Verzióváltási tartomány feldolgozása. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Üzembe helyezési pont migrálásának ellenőrzése. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/write | Telepítés létrehozása vagy frissítése. |
> | Action | Microsoft.ClassicCompute/domainNames/swap/action | Előkészítési pont cseréje üzemelési pontra. |
> | Action | Microsoft.ClassicCompute/domainNames/write | Erőforrások tartománynevének hozzáadása vagy módosítása. |
> | Action | Microsoft.ClassicCompute/moveSubscriptionResources/action | Minden hagyományos erőforrás áthelyezése egy másik előfizetésbe. |
> | Action | Microsoft.ClassicCompute/operatingSystemFamilies/read | Ez a művelet a Microsoft Azure-ban elérhető vendég operációsrendszer-családokat, valamint az egyes családokon belül elérhető operációsrendszer-verziókat listázza. |
> | Action | Microsoft.ClassicCompute/operatingSystems/read | Ez a művelet a Microsoft Azure-ban jelenleg elérhető vendég operációsrendszer-verziókat listázza. |
> | Action | Microsoft.ClassicCompute/operations/read | A műveletek listájának beolvasása. |
> | Action | Microsoft.ClassicCompute/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Action | Microsoft.ClassicCompute/register/action | Regisztrálás a Classic Compute szolgáltatónál |
> | Action | Microsoft.ClassicCompute/resourceTypes/skus/read | Lekérdezi a támogatott erőforrástípusok termékváltozat-listáját. |
> | Action | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Annak ellenőrzése, hogy áthelyezhető-e az előfizetés hagyományos áthelyezéssel. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | A virtuális géphez társított hálózati biztonsági csoport törlése. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | A virtuális géphez társított hálózati biztonsági csoport lekérése. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | A virtuális géphez társított hálózati biztonsági csoport hozzáadása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Lehetséges aszinkron műveletek beolvasása |
> | Action | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Adatlemez csatlakoztatása a virtuális géphez. |
> | Action | Microsoft.ClassicCompute/virtualMachines/capture/action | Virtuális gép rögzítése. |
> | Action | Microsoft.ClassicCompute/virtualMachines/delete | Virtuális gépek eltávolítása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Adatlemez leválasztása a virtuális gépről. |
> | Action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Virtuális gép diagnosztikai beállításainak beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/disks/read | Adatlemezek listájának lekérése |
> | Action | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Virtuális gép RDP-fájljának letöltése. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | A virtuálisgép-bővítmények műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/read | Virtuálisgép-bővítmény beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/write | Virtuálisgép-bővítmény hozzáadása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | A virtuális gép metrikadefiníciójának beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/metrics/read | Metrika beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | A hálózati interfészhez társított hálózati biztonsági csoport törlése. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális gépek társított hálózati biztonsági csoportjai műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | A hálózati interfészhez társított hálózati biztonsági csoport lekérése. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | A hálózati interfészhez társított hálózati biztonsági csoport hozzáadása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | A virtuális gépek műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | A művelet elvégzi a virtuális gép karbantartását. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Diagnosztikai beállítások beolvasása. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | A metrikai meghatározások beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/read | Virtuális gépek listájának beolvasása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/redeploy/action | A virtuális gép ismételt üzembe helyezése. |
> | Action | Microsoft.ClassicCompute/virtualMachines/restart/action | Virtuális gépek újraindítása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Virtuális gép rendszerének leállítása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/start/action | Virtuális gép elindítása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/stop/action | Virtuális gép leállítása. |
> | Action | Microsoft.ClassicCompute/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/operationstatuses/READ | Egy Express Route-keresztkapcsolat műveleti állapotának beolvasása. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/törlés | Express Route-keresztkapcsolati társviszony törlése. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/operationstatuses/olvasás | Egy Express Route-keresztkapcsolati társviszony műveleti állapotának beolvasása. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/olvasás | Express Route-keresztkapcsolati társviszony beolvasása. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/társak/írás | Express Route-keresztkapcsolati társviszony hozzáadása. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/READ | Az Express Route-keresztkapcsolatok beolvasása. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/Write | Express Route-keresztkapcsolatok hozzáadása. |
> | Action | Microsoft. ClassicNetwork/gatewaySupportedDevices/READ | Támogatott eszközök listájának beolvasása. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/delete | A hálózati biztonsági csoport törlése. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | A hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Action | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A hálózati biztonsági csoport diagnosztikai beállításainak beolvasása |
> | Action | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A hálózati biztonsági csoportok diagnosztikai beállításainak létrehozása vagy frissítése, ezt a műveletet az információk erőforrás-szolgáltatója egészíti ki. |
> | Action | Microsoft. ClassicNetwork/networksecuritygroups/Providers/Microsoft. bepillantást/logDefinitions/READ | A hálózati biztonsági csoport lekérése eseményeinek beolvasása |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/read | A hálózati biztonsági csoport lekérése. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | A biztonsági szabály törlése. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | A hálózati biztonsági csoport biztonsági szabályai műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | A biztonsági szabály beolvasása. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Egy biztonsági szabály hozzáadása vagy frissítése. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/write | Új hálózati biztonsági csoport hozzáadása. |
> | Action | Microsoft. ClassicNetwork/Operations/READ | A klasszikus hálózati műveletek beolvasása. |
> | Action | Microsoft. ClassicNetwork/kvóták/olvasás | Az előfizetés kvótájának beolvasása. |
> | Action | Microsoft. ClassicNetwork/regisztráció/művelet | Regisztrálás a Classic Network szolgáltatónál |
> | Action | Microsoft. ClassicNetwork/reservedIps/delete | Fenntartott IP-cím törlése. |
> | Action | Microsoft. ClassicNetwork/reservedIps/JOIN/Action | Csatlakozás egy fenntartott IP-címhez |
> | Action | Microsoft.ClassicNetwork/reservedIps/link/action | Egy fenntartott IP-cím hivatkozása |
> | Action | Microsoft. ClassicNetwork/reservedIps/operationStatuses/READ | A fenntartott IP-címek műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicNetwork/reservedIps/read | A fenntartott IP-címek beolvasása |
> | Action | Microsoft. ClassicNetwork/reservedIps/Write | Új fenntartott IP-cím felvétele |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/művelet | Egy virtuális hálózat migrálásának megszakítása |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/képességek/olvasás | Képességek megjelenítése |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Az adott IP-cím virtuális hálózaton belüli elérhetőségét ellenőrzi. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/művelet | Egy virtuális hálózat migrálásának véglegesítése |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/delete | A virtuális hálózat törlése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/delete | Az ügyféltanúsítvány visszavonásának megszüntetése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/READ | Visszavont ügyféltanúsítványok beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRevokedCertificates/Write | Ügyféltanúsítvány visszavonása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/delete | A virtuális hálózati átjáró ügyféltanúsítványának törlése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/Letöltés/művelet | Tanúsítvány letöltése ujjlenyomat útján. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateways/clientRootCertificates/listPackage/művelet | A virtuális hálózati átjáró tanúsítványcsomagjának listázása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/READ | Ügyfél főtanúsítványainak megkeresése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/clientRootCertificates/Write | Ügyfél új főtanúsítványának feltöltése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/csatlakozás/művelet | Webhelyek közötti átjárókapcsolat létesítése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/kapcsolat bontása/művelet | Webhelyek közötti átjárókapcsolat leválasztása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/olvasás | Kapcsolatok listájának beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/kapcsolatok/teszt/művelet | Webhelyek közötti átjárókapcsolat tesztelése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/törlés | Virtuális hálózati átjáró törlése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/downloadDeviceConfigurationScript/művelet | Eszközkonfigurációs parancsfájl letöltése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/downloadDiagnostics/művelet | Átjáródiagnosztika letöltése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/listCircuitServiceKey/művelet | Kör szolgáltatáskulcsának beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/listPackage/művelet | A virtuális hálózati átjárócsomag listázása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/operationStatuses/READ | A virtuális hálózati átjárók műveleti állapotának beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/csomagok/olvasás | A virtuális hálózati átjárócsomag beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/olvasás | Virtuális hálózati átjárók beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/startDiagnostics/művelet | Elindítja a virtuális hálózati átjáró diagnosztikai szolgáltatását. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/stopDiagnostics/művelet | Leállítja a virtuális hálózati átjáró diagnosztikai szolgáltatását. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/átjárók/írás | Virtuális hálózati átjáró hozzáadása. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/join/action | Csatlakozás a virtuális hálózathoz. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | A virtuális hálózatok műveleti állapotának beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/társ/művelet | Társviszony-létesítés két virtuális hálózat között. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/prepareMigration/művelet | Egy virtuális hálózat migrálásának előkészítése |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/read | Virtuális hálózat beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | A távoli virtuális hálózati társviszony proxyjának törlése. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/READ | A távoli virtuális hálózati társviszony proxyjának beolvasása |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Write | A távoli virtuális hálózati társviszony proxyjának hozzáadása vagy frissítése. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Az alhálózathoz társított hálózati biztonsági csoport törlése. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | A virtuális hálózati alhálózat társított hálózati biztonsági csoport műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Az alhálózathoz társított hálózati biztonsági csoport lekérése. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Az alhálózathoz társított hálózati biztonsági csoport hozzáadása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/művelet | Egy virtuális hálózat migrálásának ellenőrzése |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/virtualNetworkPeerings/READ | A virtuális hálózati társviszony beolvasása. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Write | Új virtuális hálózat hozzáadása. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ClassicStorage/capabilities/read | Képességek megjelenítése |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | A tárfiók elérhetőségének ellenőrzése. |
> | Action | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Egy tárfiók elérhetőségének beolvasása. |
> | Action | Microsoft.ClassicStorage/disks/read | A tárfióklemez beolvasása. |
> | Action | Microsoft.ClassicStorage/images/operationstatuses/read | Lemezkép műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicStorage/images/read | A lemezkép visszaadása. |
> | Action | Microsoft.ClassicStorage/operations/read | Klasszikus tárolási műveletek beolvasása |
> | Action | Microsoft.ClassicStorage/osImages/read | A operációs rendszerkép beolvasása. |
> | Action | Microsoft.ClassicStorage/osPlatformImages/read | Az operációs rendszer platformlemezképének beolvasása. |
> | Action | Microsoft.ClassicStorage/publicImages/read | A nyilvános virtuális gép lemezképének beolvasása. |
> | Action | Microsoft.ClassicStorage/quotas/read | Az előfizetés kvótájának beolvasása. |
> | Action | Microsoft.ClassicStorage/register/action | Regisztrálás egy hagyományos tárolóba. |
> | Action | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Tárfiók migrálásának megszakítása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/commitMigration/művelet | Tárfiók migrálásának végrehajtása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/delete | Tárfiók törlése. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/delete | A megadott tárfióklemez törlése. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/read | A tárfióklemez beolvasása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/write | Új tárfióklemez felvétele. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/delete | Egy adott Storage-fiók rendszerképének törlése. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | A tárfióklemezkép műveleti állapotát adja vissza. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/read | A Storage-fiók rendszerképét adja vissza. Elavult. A "Microsoft. ClassicStorage/storageAccounts/lemezképet" használata |
> | Action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Tárfiókok elérési kulcsainak listázása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | A megadott tárfiók operációsrendszer-lemezképének törlése. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/read | A tárfiók operációsrendszer-lemezképének visszaadása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/osImages/Write | Egy adott tárfiók operációsrendszer-lemezképének hozzáadása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Tárfiók migrálásának előkészítése. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Diagnosztikai beállítások beolvasása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Providers/Microsoft. bepillantást/metricDefinitions/READ | A metrikai meghatározások beolvasása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/read | Tárfiók visszaadása az adott fiókhoz. |
> | Action | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Tárfiók meglévő elérési kulcsainak újragenerálása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/READ | Diagnosztikai beállítások beolvasása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Services/diagnosticSettings/Write | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Services/metricDefinitions/READ | A metrikai meghatározások beolvasása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Services/mérőszámok/olvasás | Metrika beolvasása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Services/READ | Elérhető szolgáltatások beolvasása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Tárfiók migrálásának ellenőrzése. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | A megadott virtuálisgép-lemezkép törlése. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Egy adott virtuálisgép-lemezkép műveleti állapotának beolvasása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | A virtuálisgép-lemezkép beolvasása. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | A megadott virtuálisgép-lemezkép hozzáadása. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Write | Új tárfiók hozzáadása. |
> | Action | Microsoft.ClassicStorage/vmImages/read | A virtuálisgép-lemezképek listázása. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | A művelet a képi tartalom alapján kibontja a vizualizációs funkciók gazdag készletét.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | A művelet a rendszerkép legfontosabb területe körüli határoló mezőt adja vissza. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Ezzel a művelettel egy, a teljes mondattal ellátott, emberi olvasási nyelvezettel rendelkező képet generál.<br> A leírás a művelet által is visszaadott tartalmi címkék gyűjteményén alapul.<br>Minden képhez több Leírás is létrehozható.<br> A leírásokat a megbízhatósági Pontszámuk alapján rendezi a rendszer.<br>Az összes Leírás angol nyelven van. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | A művelet végrehajtja az objektumok észlelését a megadott képen.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | A művelet a felhasználó által megadott szélességet és magasságot tartalmazó miniatűr képet hoz létre.<br> Alapértelmezés szerint a szolgáltatás elemzi a képet, azonosítja a kamat régióját (ROI), és a ROI alapján létrehoz egy intelligens levágási koordinátákat.<br> Az intelligens vágás segít, ha olyan oldalarányt ad meg, amely eltér a bemeneti képtől |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | A művelet egy adott rendszerképen belül felismeri a tartalmat egy tartományszintű modell alkalmazásával.<br> A Computer Vision API által támogatott tartomány-specifikus modellek listája a/models GET kérelem használatával kérhető le.<br> Az API jelenleg a következő tartományszintű modelleket biztosítja: hírességek, tereptárgyak. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Ez a művelet visszaadja a Computer Vision API által támogatott tartomány-specifikus modellek listáját.  Az API jelenleg a következő tartományszintű modelleket támogatja: Celebrity felismerő, Landmark felismerő. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Az optikai karakterfelismerés (OCR) észleli a képek szövegét, és Kinyeri a felismert karaktereket egy gépi használatú adatfolyamba.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Ezt a felületet használhatja egy szövegfelismerés művelet eredményének lekéréséhez. Ha a szövegfelismerés felületet használja, a válasz egy "Operation-Location" nevű mezőt tartalmaz. A "művelet helye" mező tartalmazza azt az URL-címet, amelyet a Get szövegfelismerés művelet eredménye művelethez kell használnia. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | A művelet a megadott rendszerkép tartalmához kapcsolódó szavak vagy címkék listáját hozza létre.<br>A Computer Vision API a képeken található objektumok, élőlények, díszletek vagy műveletek alapján lehet címkéket visszaadni.<br>A kategóriáktól eltérően a címkék nem a hierarchikus besorolási rendszer szerint vannak rendszerezve, hanem a képtartalomnak.<br>A címkék tartalmazhatnak olyan mutatókat, amelyek a kétértelműség elkerülését vagy kontextust biztosítanak, például a "cselló" címkét a "hangszer" mutató kísérheti.<br>Az összes címke angol nyelven van. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Ez a csatoló a szöveg felismerésére szolgáló művelet eredményének beolvasására szolgál. A csatoló URL-címét a szövegfelismerés felületről visszaadott <b>"Operation-Location"</b> mezőből kell beolvasni. |
> | Action | Microsoft.CognitiveServices/accounts/delete | API-fiókok törlése |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/észlelés/művelet | Azonosíthatja az emberi arcokat egy képen, az arc négyszögeit és opcionálisan faceIds, tereptárgyak és attribútumokkal. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Megadott Arcfelismerés törlése. A rendszer a Face (arc) listán szereplő kapcsolódó Face-képeket is törli. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Arc-listából származó arc törlése a megadott faceListId és persisitedFaceId. A kapcsolódó arckép is törlődik. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Egy arc hozzáadása egy megadott arc listához, amely legfeljebb 1 000 arc lehet. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Egy Arcfelismerés faceListId, neve, userData és arcok beolvasása a Face listában. Listázza a faceListId, a nevet és a userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Hozzon létre egy üres Arcfelismerés a felhasználó által megadott faceListId, névvel és opcionális userData. Akár 64-es Arcfelismerés is engedélyezett az Arcfelismerés információinak frissítése, beleértve a nevet és a userData. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/findsimilars/művelet | A lekérdezés arca faceId a hasonló megjelenésű arcok kereséséhez egy faceId tömbből, egy Arcfelismerés vagy egy nagy Arcfelismerés. faceId |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/csoport/művelet | A jelölt arcok a Face hasonlóság alapján csoportosíthatók csoportokba. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/azonosítás/művelet | egy-a-többhöz azonosítással megkeresheti az adott lekérdezés személyének legközelebbi egyezését egy személy vagy egy nagy személy csoportból. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Megadott nagyméretű Arcfelismerés törlése. A rendszer a nagy számú arc listán szereplő kapcsolódó arcokat is törli. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | A megadott largeFaceListId és persisitedFaceId használatával törölheti a Faces elemet egy nagyméretű Arcfelismerés alapján. A kapcsolódó arckép is törlődik. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Megőrzött arc beolvasása nagy largeFaceListId és persistedFaceId Egy megadott nagyméretű persistedFaceId és userData arcok listázása. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Egy arc hozzáadása egy megadott nagy Arcfelismerés-listához, amely legfeljebb 1 000 000 arc lehet. Egy adott arc userData mezőjének frissítése egy nagy Arcfelismerés-listán a persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Nagy számú Arcfelismerés largeFaceListId, neve, userData beolvasása. Jelenítse meg a largeFaceListId, a név és a userData információit. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Nagyméretű Arcfelismerés betanítási feladat elküldése. A képzés elengedhetetlen lépés, hogy csak a betanított nagyméretű arcok használhatók. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | A nagyméretű Arcfelismerés betanítási állapotának ellenőrzéséhez, vagy még folyamatban van. A LargeFaceList-képzés aszinkron művelet |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Hozzon létre egy üres nagyméretű arc listát a felhasználó által megadott largeFaceListId, névvel és opcionális userData. Nagy méretű Arcfelismerés információinak frissítése, beleértve a nevet és a userData. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/delete | Meglévő nagyvállalati csoport törlése a megadott personGroupId. A rendszer törli a nagy csoportba tartozó megőrzött adatmennyiséget. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/Action | Hozzon létre egy új személyt egy adott nagy csoportba. Ha hozzá szeretne adni egy arcot ehhez a személyhez, hívja meg a következőt |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/delete | Meglévő személy törlése egy nagyméretű személy csoportból. A rendszer törli az összes tárolt adat-és Face-képet a személy bejegyzésében. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Egy nagyméretű személy csoportból származó személyből származó arc törlése. A rendszer a Face bejegyzéshez kapcsolódó arcfelismerés és-képet is törli. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/persistedfaces/READ | Személy arca adatainak beolvasása. A megőrzött személy arcát a largePersonGroupId, a számú personid és a persistedFaceId határozza meg. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/persistedfaces/Write | Arcfelismerés vagy-ellenőrzés céljából adjon hozzá egy képet egy személyhez egy nagy méretű személy csoportba. Annak érdekében, hogy a felhasználó frissítse a megőrzött személy userData mezőjét. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/READ | Beolvashat egy személy nevét és userData, valamint a regisztrált személy arcképét jelképező megőrzött faceIds. Az összes személy információinak listázása a megadott nagyméretű személy csoportban, beleértve a számú personid, a nevet, a userData és a persistedFaceIds. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/largepersongroups/Persons/Write | Egy személy nevének vagy userData frissítése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/READ | Egy nagyméretű személy csoport információinak beolvasása, beleértve a nevét és a userData is. Ez az API a nagyméretű személy csoport adatainak listáját adja vissza az összes meglévő nagyvállalati csoport largePesonGroupId, nevével és userData. |
> | DataAction | Microsoft. CognitiveServices/accounts/Face/largepersongroups/Train/Action | Nagyméretű csoportos betanítási feladat elküldése. A képzés egy kulcsfontosságú lépés, amelyet csak a betanított nagyméretű személyek használhatnak. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/képzés/olvasás | A nagyméretű személy csoportos betanítási állapotának ellenőrzéséhez, vagy még folyamatban van. A LargePersonGroup-képzés aszinkron művelet |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/largepersongroups/írás | Hozzon létre egy új, a felhasználó által megadott largePersonGroupId, névvel és opcionális userData rendelkező nagyméretű személy csoportot. Meglévő nagyvállalati csoport nevének és userData frissítése. A tulajdonságok változatlanok maradnak, ha nincsenek a kérelem törzsében. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Meglévő személy csoport törlése a megadott personGroupId. A rendszer törli az adott személy csoportba tartozó megőrzött adatkészleteket. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/Action | Hozzon létre egy új személyt egy adott személy csoportban. Ha hozzá szeretne adni egy arcot ehhez a személyhez, hívja meg a következőt |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/delete | Meglévő személy törlése egy személy csoportból. A rendszer törli az összes tárolt adat-és Face-képet a személy bejegyzésében. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Arc törlése egy személy csoportból. A rendszer a Face bejegyzéshez kapcsolódó arcfelismerés és-képet is törli. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/persistedfaces/READ | Személy arca adatainak beolvasása. A megőrzött személy arcát a personGroupId, a számú personid és a persistedFaceId határozza meg. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/persistedfaces/Write | Arcfelismerés vagy-ellenőrzés céljából vegyen fel egy képet egy személy csoportba. Ha több frissítés képét szeretné kezelni egy személy megőrzött arca userData mezőjében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/READ | Beolvashat egy személy nevét és userData, valamint a regisztrált személy arcképét jelképező megőrzött faceIds. A megadott személy csoport összes adatának listázása, beleértve a regisztrált számú personid, a nevet, a userData és a persistedFaceIds. |
> | DataAction | Microsoft. CognitiveServices/fiókok/Face/persongroups/Persons/Write | Egy személy nevének vagy userData frissítése. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/persongroups/READ | A személy csoport nevének és userData beolvasása. A személyes adatok ezen personGroup való beolvasásához használja a pesonGroupId, a Name és a userData listát. |
> | DataAction | Microsoft. CognitiveServices/accounts/Face/persongroups/Train/Action | Személy csoportos betanítási feladat elküldése. A képzés olyan kulcsfontosságú lépés, amelyet csak a képzett személy csoport használhat. |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/persongroups/képzés/olvasás | Annak ellenőrzéséhez, hogy a személy csoportos betanítási állapota befejezett vagy még folyamatban van-e. A PersonGroup-képzés egy aszinkron művelet, amely aktiválva van |
> | DataAction | Microsoft. CognitiveServices/fiókok/arc/persongroups/írás | Hozzon létre egy új személy csoportot a megadott personGroupId, névvel és felhasználó által megadott userData. Egy meglévő személy csoport nevének és userData frissítése. A tulajdonságok változatlanok maradnak, ha nincsenek a kérelem törzsében. |
> | DataAction | Microsoft. CognitiveServices/fiókok/szembenézés/ellenőrzés/művelet | Győződjön meg arról, hogy két arc ugyanahhoz a személyhez tartozik-e, vagy az, hogy egy arc személyhez tartozik-e. |
> | Action | Microsoft.CognitiveServices/accounts/listKeys/action | Kulcsok listázása |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Lekéri a megadott lekérdezés közzétett végpontjának előrejelzését. |
> | Action | Microsoft.CognitiveServices/accounts/read | API-fiókok beolvasása. |
> | Action | Microsoft.CognitiveServices/accounts/regenerateKey/action | Kulcs újragenerálása |
> | Action | Microsoft.CognitiveServices/accounts/skus/read | Az egy adott meglévő erőforráshoz rendelkezésre álló termékváltozatok beolvasása. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | Az API egy adott dokumentum ismert entitások és általános névvel ellátott\"entitások \"(\"személy \"\",\" hely, szervezet stb.) listáját adja vissza egy adott dokumentumban. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | Az API a bemeneti szövegben található fő beszédtémákat jelző karakterláncokat jeleníti meg. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | Az API a felderített nyelvet és az adott 0 és 1 közötti numerikus pontszámot jeleníti meg. Az 1-hez közeli pontszámok 100%-os bizonyossággal jelzik az azonosított nyelv valódiságát. Összesen 120 nyelv támogatott. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | Az API 0 és 1 közötti numerikus pontszámokat jelenít meg.<br>Az 1. ponthoz közeledő pontszámok pozitív véleményt jeleznek, míg a 0 érték a negatív hangulatot jelzi.<br>A 0,5-es pontszám jelzi az érzelmek hiányát (például<br>egy factoid-utasítás). |
> | Action | Microsoft. CognitiveServices/fiókok/használati adatok/olvasás | Egy meglévő erőforrás kvótahasználatának beolvasása. |
> | Action | Microsoft.CognitiveServices/accounts/write | API-fiókok írása. |
> | Action | Microsoft.CognitiveServices/checkDomainAvailability/action | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Action | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Beolvassa az elérhető SKU-t egy előfizetéshez. |
> | Action | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Értesítés a Microsoft. Network VirtualNetworks vagy alhálózatok törléséről. |
> | Action | Microsoft.CognitiveServices/Operations/read | Az összes elérhető művelet listázása |
> | Action | Microsoft.CognitiveServices/register/action | Cognitive Services-előfizetés regisztrálása |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Commerce/RateCard/read | Az adott előfizetéshez tartozó ajánlati adatokat, erőforrás/fogyasztásmérő metaadatait és díjszabását adja vissza. |
> | Action | Microsoft.Commerce/UsageAggregates/read | Lekéri Microsoft Azure előfizetéssel való felhasználását. Az eredmény az összesített használati adatokat, az előfizetést és az erőforrással kapcsolatos információkat tartalmaz egy adott időtartományon belül. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Compute/availabilitySets/delete | Törli a rendelkezésre állási csoportot |
> | Action | Microsoft.Compute/availabilitySets/read | Egy adott rendelkezésre állási csoport tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/availabilitySets/vmSizes/read | A virtuális gépek a rendelkezésre állási csoportban való létrehozásához vagy frissítéséhez használható méretek listázása |
> | Action | Microsoft.Compute/availabilitySets/write | Létrehoz egy új vagy frissít egy meglévő rendelkezésre állási csoportot |
> | Action | Microsoft.Compute/disks/beginGetAccess/action | Lemez SAS URI-jának beolvasása blobhozzáféréshez |
> | Action | Microsoft.Compute/disks/delete | A lemez törlése |
> | Action | Microsoft.Compute/disks/endGetAccess/action | A lemez SAS URI-jának visszavonása |
> | Action | Microsoft.Compute/disks/read | A lemez tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/disks/write | Új lemez létrehozása vagy meglévő lemez frissítése |
> | Action | Microsoft.Compute/galleries/delete | A katalógus törlése |
> | Action | Microsoft.Compute/galleries/images/delete | A katalógus-lemezkép törlése |
> | Action | Microsoft.Compute/galleries/images/read | Katalógus-lemezkép tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/galleries/images/versions/delete | A katalóguslemezkép-verzió törlése |
> | Action | Microsoft. számítás/galériák/lemezképek/verziók/olvasás | Katalóguslemezkép-verzió tulajdonságainak beolvasása |
> | Action | Microsoft. számítás/galériák/lemezképek/verziók/írás | Új katalóguslemezkép-verzió létrehozása vagy meglévő frissítése |
> | Action | Microsoft.Compute/galleries/images/write | Új katalógus-lemezkép létrehozása vagy meglévő frissítése |
> | Action | Microsoft.Compute/galleries/read | Katalógus tulajdonságainak beolvasása |
> | Action | Microsoft. számítás/galériák/írás | Új katalógus létrehozása vagy meglévő frissítése |
> | Action | Microsoft.Compute/hostGroups/delete | A gazda csoport törlése |
> | Action | Microsoft.Compute/hostGroups/read | Egy gazda csoport tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/hostGroups/write | Létrehoz egy új számítógépcsoportot, vagy frissít egy meglévőt. |
> | Action | Microsoft.Compute/hosts/delete | A gazdagép törlése |
> | Action | Microsoft.Compute/hosts/read | Gazdagép tulajdonságainak lekérése |
> | Action | Microsoft.Compute/hosts/write | Új gazdagép létrehozása vagy meglévő gazdagép frissítése |
> | Action | Microsoft.Compute/images/delete | A lemezkép törlése |
> | Action | Microsoft.Compute/images/read | A lemezkép tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/images/write | Új lemezkép létrehozása vagy meglévő frissítése |
> | Action | Microsoft.Compute/locations/capsOperations/read | Aszinkron Caps művelet állapotának beolvasása |
> | Action | Microsoft.Compute/locations/diskOperations/read | Aszinkron lemez művelet állapotának beolvasása |
> | Action | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Naplók létrehozása a kérelmek összesített számának időintervallum szerinti megjelenítéséhez a szabályozási diagnosztika támogatása érdekében. |
> | Action | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Naplók létrehozása a szabályozott kérelmek erőforrásnév, műveletnév vagy az alkalmazott szabályozási szabályzat szerint csoportosított aggregátumainak megjelenítéséhez. |
> | Action | Microsoft.Compute/locations/operations/read | Egy aszinkron művelet állapotát olvassa be |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Platformlemezképet tartalmazó ajánlat tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Platformlemezképet tartalmazó termékváltozat tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Platformlemezkép-verzió tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/read | VM-bővítmény-típus tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | VM-bővítmény-verzió tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/locations/publishers/read | Gyártó tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/locations/runCommands/read | A helyen elérhető futtatási parancsok listázása |
> | Action | Microsoft.Compute/locations/usages/read | Az előfizetés adott helyen lévő számítási erőforrásaihoz tartozó szolgáltatások korlátait és az aktuális felhasználási mennyiségeket olvassa be |
> | Action | Microsoft.Compute/locations/vmSizes/read | Listázza az adott helyen elérhető virtuálisgép-méreteket |
> | Action | Microsoft.Compute/operations/read | A Microsoft.Compute erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Action | Microsoft.Compute/proximityPlacementGroups/delete | A közelségi elhelyezési csoport törlése |
> | Action | Microsoft.Compute/proximityPlacementGroups/read | A közelségi elhelyezési csoport tulajdonságainak lekérése |
> | Action | Microsoft.Compute/proximityPlacementGroups/write | Új közelségi elhelyezési csoport létrehozása vagy meglévő frissítése |
> | Action | Microsoft.Compute/register/action | Az előfizetés regisztrálása a Microsoft.Compute erőforrás-szolgáltatónál |
> | Action | Microsoft.Compute/restorePointCollections/delete | A visszaállításipont-gyűjtemény és az abban található visszaállítási pontok törlése |
> | Action | Microsoft.Compute/restorePointCollections/read | Egy visszaállításipont-gyűjtemény tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/delete | A visszaállítási pont törlése |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/read | Egy visszaállítási pont tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Egy visszaállítási pont tulajdonságainak beolvasása a blob SAS URI azonosítóival |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/write | Új visszaállítási pont létrehozása |
> | Action | Microsoft.Compute/restorePointCollections/write | Egy új visszaállításipont-gyűjtemény létrehozása, vagy egy meglévő módosítása |
> | Action | Microsoft.Compute/sharedVMImages/delete | A megosztott VM-lemezkép törlése |
> | Action | Microsoft.Compute/sharedVMImages/read | Megosztott VM-lemezkép tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/sharedVMImages/versions/delete | A megosztott VM-lemezkép-verzió törlése |
> | Action | Microsoft.Compute/sharedVMImages/versions/read | Megosztott VM-lemezkép-verzió tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/sharedVMImages/versions/replicate/action | Megosztott VM-lemezkép-verzió replikálása a célrégiókba |
> | Action | Microsoft.Compute/sharedVMImages/versions/write | Hozzon létre egy új megosztott VM-lemezkép-verziót, vagy frissítsen egy meglévőt |
> | Action | Microsoft.Compute/sharedVMImages/write | Új megosztott VM-lemezkép létrehozása vagy egy meglévő frissítése |
> | Action | Microsoft.Compute/skus/read | Az előfizetéssel elérhető Microsoft Compute-termékváltozatok listájának beolvasása |
> | Action | Microsoft.Compute/snapshots/beginGetAccess/action | A blob-hozzáféréshez tartozó pillanatkép SAS URI-azonosítójának beolvasása |
> | Action | Microsoft.Compute/snapshots/delete | A pillanatkép törlése |
> | Action | Microsoft.Compute/snapshots/endGetAccess/action | A pillanatkép SAS URI-azonosítójának visszavonása |
> | Action | Microsoft.Compute/snapshots/read | Pillanatkép tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/snapshots/write | Új pillanatkép létrehozása vagy meglévő pillanatkép frissítése |
> | Action | Microsoft.Compute/unregister/action | Előfizetés regisztrációjának megszüntetése a Microsoft.Compute erőforrás-szolgáltatónál |
> | Action | Microsoft.Compute/virtualMachines/capture/action | Rögzíti a virtuális gépet úgy, hogy másolatot készít a virtuális merevlemezekről és létrehoz egy sablont, mellyel hasonló virtuális gépek hozhatók létre |
> | Action | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | A virtuális gép blobalapú lemezeinek konvertálása felügyelt lemezekké |
> | Action | Microsoft.Compute/virtualMachines/deallocate/action | Kikapcsolja a virtuális gépet és felszabadítja a számítási erőforrásokat |
> | Action | Microsoft.Compute/virtualMachines/delete | Törli a virtuális gépet |
> | Action | Microsoft.Compute/virtualMachines/extensions/delete | Törli a virtuálisgép-bővítményt |
> | Action | Microsoft.Compute/virtualMachines/extensions/read | Egy virtuálisgép-bővítmény tulajdonságait olvassa be |
> | Action | Microsoft.Compute/virtualMachines/extensions/write | Létrehoz egy új vagy frissít egy meglévő virtuálisgép-bővítményt |
> | Action | Microsoft.Compute/virtualMachines/generalize/action | Általánosítottra állítja a virtuális gép állapotát és előkészíti a virtuális gépet a rögzítéshez |
> | Action | Microsoft.Compute/virtualMachines/instanceView/read | A virtuális gép és az erőforrásai részletes futási állapotát olvassa be |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Bejelentkezés egy virtuális gépre normál felhasználóként |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Bejelentkezés egy virtuális gépre a Windows rendszergazdai vagy a Linux gyökérszintű felhasználójának jogosultságaival |
> | Action | Microsoft.Compute/virtualMachines/performMaintenance/action | Karbantartási művelet végrehajtása a virtuális gépen. |
> | Action | Microsoft.Compute/virtualMachines/powerOff/action | Kikapcsolja a virtuális gépet. Vegye figyelembe, hogy a virtuális gép számlázása továbbra is megtörténik. |
> | Action | Microsoft.Compute/virtualMachines/read | Egy virtuális gép tulajdonságait olvassa be |
> | Action | Microsoft.Compute/virtualMachines/redeploy/action | A virtuális gép újbóli üzembe helyezése |
> | Action | Microsoft.Compute/virtualMachines/reimage/action | Különbséglemezt használó virtuális gép rendszerképét állítja alaphelyzetbe. |
> | Action | Microsoft.Compute/virtualMachines/restart/action | Újraindítja a virtuális gépet |
> | Action | Microsoft.Compute/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása a virtuális gépen |
> | Action | Microsoft.Compute/virtualMachines/start/action | Elindítja a virtuális gépet |
> | Action | Microsoft.Compute/virtualMachines/vmSizes/read | Listázza az elérhető méreteket, melyekre a virtuális gép frissíthető |
> | Action | Microsoft.Compute/virtualMachines/write | Létrehoz egy új virtuális gépet vagy frissít egy meglévő virtuális gépet |
> | Action | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | A virtuálisgép-méretezési csoport példányaihoz hozzárendelt számítási erőforrások kikapcsolása és felszabadítása  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete | A virtuálisgép-méretezési csoport törlése |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete/action | A virtuálisgép-méretezési csoport példányainak törlése |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | A virtuálisgép-méretezési csoport bővítményének törlése |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Egy virtuálisgép-méretezési csoport bővítménye tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Egy virtuálisgép-méretezési csoport egy új bővítményének létrehozása, vagy egy meglévő frissítése |
> | Action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Service Fabric-beli virtuálisgép-méretezési csoport platformfrissítési tartományainak manuális bejárása egy elakadt függőben lévő frissítés befejezéséhez |
> | Action | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | A virtuálisgép-méretezési csoport példánynézetének beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | A példányok manuális frissítése a virtuálisgép-méretezési csoport legújabb modelljére |
> | Action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Egy virtuálisgép-méretezési csoport összes hálózati adaptere tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Működés közbeni frissítés indítása a virtuálisgép-méretezési csoport összes példányának a legújabb elérhető platformlemezkép operációsrendszer-verziójára való frissítéséhez. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Virtuálisgép-méretezési csoport gépein végrehajtott operációsrendszer-frissítések előzményeinek beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | A virtuálisgép-méretezési csoport példányai tervezett karbantartásának végrehajtása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | A virtuálisgép-méretezési csoport példányainak kikapcsolása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Egy virtuálisgép-méretezési csoport összes nyilvános IP-címe tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/read | Virtuálisgép-méretezési csoport tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | A virtuálisgép-méretezési csoport példányainak ismételt üzembe helyezése |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Ezzel a művelettel alaphelyzetbe állíthatja a virtuálisgép-méretezési csoport példányainak rendszerképét |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Egy virtuálisgép-méretezési csoport példányai összes lemeze (operációsrendszer-lemeze és adatlemeze) rendszerképének alaphelyzetbe állítása  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/restart/action | Virtuálisgép-méretezési csoport példányainak újraindítása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Ezzel a művelettel megszakítja a virtuálisgép-méretezési csoport működés közbeni frissítését |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Virtuálisgép-méretezési csoport legutóbbi működés közbeni frissítési állapotának beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/scale/action | Annak ellenőrzése, hogy van-e olyan meglévő virtuálisgép-méretezési csoport, mely horizontálisan le- vagy felskálázható a megadott példányszámra |
> | Action | Microsoft.Compute/virtualMachineScaleSets/skus/read | Egy meglévő virtuálisgép-méretezési csoport érvényes termékváltozatainak listázása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/start/action | Virtuálisgép-méretezési csoport példányainak elindítása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Kikapcsol egy virtuális gépet egy virtuálisgép-méretezési csoportban, és felszabadítja az ahhoz tartozó számítási erőforrásokat. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Töröl egy virtuális gépet egy virtuálisgép-méretezési csoportban. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének példányait tartalmazó nézetet adja meg. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | A virtuálisgép-méretezési csoport használatával létrehozott nyilvános IP-cím tulajdonságainak beolvasása. A virtuálisgép-méretezési csoport legfeljebb egy nyilvános IP-címet képes létrehozni ipconfiguration (magánhálózati IP) |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | A virtuálisgép-méretezési csoport használatával létrehozott hálózati adapter egy vagy több IP-konfigurációjának tulajdonságainak beolvasása. Az IP-konfigurációk privát IP-címeket jelentenek |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Egy virtuálisgép-méretezési csoportban létrehozott virtuális gép egy vagy minden hálózati adaptere tulajdonságainak beolvasása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány tervezett karbantartásának végrehajtása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Kikapcsol egy virtuálisgép-példányt egy virtuálisgép-méretezési csoportban. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Egy virtuálisgép-méretezési csoport egy virtuális gépének tulajdonságait adja meg |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Virtuálisgép-méretezési csoportban lévő virtuálisgép-példány ismételt üzembe helyezése |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | A művelet alaphelyzetbe állítja a virtuálisgép-méretezési csoportban lévő virtuálisgép-példány rendszerképét. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példány összes lemeze (operációsrendszer-lemeze és adatlemeze) rendszerképének alaphelyzetbe állítása. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Újraindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Egy előre definiált parancsfájl végrehajtása egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányon. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Elindít egy virtuálisgép-méretezési csoportban lévő virtuálisgép-példányt. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Egy virtuálisgép-méretezési csoportban lévő virtuális gép tulajdonságainak frissítése |
> | Action | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | A virtuálisgép-méretezési csoportba tartozó virtuális gépek létrehozásához vagy frissítéséhez elérhető méretek listázása |
> | Action | Microsoft.Compute/virtualMachineScaleSets/write | Új virtuálisgép-méretezési csoport létrehozása vagy egy meglévő frissítése |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Consumption/balances/read | Egy felügyeleti csoport számlázási időszakának kihasználtsági összegzésének listázása. |
> | Action | Microsoft.Consumption/budgets/delete | A költségvetést egy előfizetéssel vagy egy felügyeleti csoporttal törölheti. |
> | Action | Microsoft.Consumption/budgets/read | Egy előfizetés vagy egy felügyeleti csoport költségvetésének listázása. |
> | Action | Microsoft.Consumption/budgets/write | Előfizetéssel vagy egy felügyeleti csoporttal hozza létre és frissíti a költségvetést. |
> | Action | Microsoft. fogyasztás/díjak/olvasás | Díjak listázása |
> | Action | Microsoft.Consumption/credits/read | Kreditek listázása |
> | Action | Microsoft. felhasználás/események/olvasás | Események listázása |
> | Action | Microsoft. fogyasztás/előrejelzés/olvasás | Előrejelzések listázása |
> | Action | Microsoft.Consumption/lots/read | Tételek listázása |
> | Action | Microsoft. fogyasztás/piactér/olvasás | A piactér erőforrás-használati adatainak listázása az EA és a webdirect-előfizetések hatóköréhez. |
> | Action | Microsoft.Consumption/operationresults/read | Operationresults listázása |
> | Action | Microsoft.Consumption/operations/read | A Microsoft. reakciós erőforrás-szolgáltató által támogatott összes művelet listázása. |
> | Action | Microsoft.Consumption/operationstatus/read | Operationstatus listázása |
> | Action | Microsoft.Consumption/pricesheets/read | Egy előfizetés vagy egy felügyeleti csoport Pricesheets-adatbázisának listázása. |
> | Action | Microsoft.Consumption/register/action | Regisztrálás a használati RP-be |
> | Action | Microsoft.Consumption/reservationDetails/read | A fenntartott példányok kihasználtsági adatait foglalási sorrend vagy felügyeleti csoportok szerint sorolja fel. A részletek száma napi szintenként történik. |
> | Action | Microsoft.Consumption/reservationRecommendations/read | A fenntartott példányokra vonatkozó egyszeri vagy megosztott javaslatok listázása egy előfizetéshez. |
> | Action | Microsoft.Consumption/reservationSummaries/read | A fenntartott példányok kihasználtsági összegzésének listázása foglalási sorrend vagy felügyeleti csoportok szerint. Az összegző adatokat havi vagy napi szinten kell megválasztani. |
> | Action | Microsoft.Consumption/reservationTransactions/read | A fenntartott példányok tranzakciós előzményeinek listázása felügyeleti csoportok szerint. |
> | Action | Microsoft.Consumption/tags/read | Az EA és az előfizetések címkéit listázhatja. |
> | Action | Microsoft. fogyasztás/bérlők/olvasás | Bérlők listázása |
> | Action | Microsoft.Consumption/tenants/register/action | Regisztrálja a Microsoft.-alkalmazás hatókörén belüli műveletet. |
> | Action | Microsoft.Consumption/terms/read | Egy előfizetés vagy egy felügyeleti csoport feltételeit sorolja fel. |
> | Action | Microsoft.Consumption/usageDetails/read | Az EA-és webdirect-előfizetések hatókörének használati adatainak listázása. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Végrehajtás egy adott tárolóba. |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Egy adott tároló naplóinak beolvasása. |
> | Action | Microsoft.ContainerInstance/containerGroups/delete | Egy adott tárolócsoport törlése. |
> | Action | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A tárolócsoport diagnosztikai beállításának beolvasása. |
> | Action | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A tárolócsoport diagnosztikai beállításának létrehozása vagy frissítése. |
> | Action | Microsoft. ContainerInstance/containerGroups/Providers/Microsoft. bepillantást/metricDefinitions/READ | A tárolócsoport elérhető metrikáinak beolvasása. |
> | Action | Microsoft.ContainerInstance/containerGroups/read | Minden tároló csoport beolvasása. |
> | Action | Microsoft.ContainerInstance/containerGroups/restart/action | Egy adott tárolócsoport újraindítása. |
> | Action | Microsoft.ContainerInstance/containerGroups/start/action | Egy adott tárolócsoport elindítása. |
> | Action | Microsoft.ContainerInstance/containerGroups/stop/action | Leállítja egy adott tároló csoportot. A számítási erőforrások fel lesznek foglalva, és a számlázás leáll. |
> | Action | Microsoft.ContainerInstance/containerGroups/write | Egy adott tárolócsoport létrehozása vagy frissítése. |
> | Action | Microsoft.ContainerInstance/locations/cachedImages/read | Az előfizetés egy adott régióban gyorsítótárazott lemezképeinek beolvasása. |
> | Action | Microsoft.ContainerInstance/locations/capabilities/read | Egy adott régió képességeinek beolvasása. |
> | Action | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.ContainerInstance értesítése arról, hogy a virtuális hálózat vagy alhálózat törlése folyamatban van. |
> | Action | Microsoft.ContainerInstance/locations/usages/read | Egy adott régió használati adatainak beolvasása. |
> | Action | Microsoft.ContainerInstance/register/action | A tárolópéldány erőforrás-szolgáltatójára való előfizetés regisztrálása és a tárolócsoportok létrehozásának engedélyezése. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ContainerRegistry/checkNameAvailability/read | Ellenőrzi, hogy a tároló beállításjegyzékének neve használható-e. |
> | Action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Értesíti a Microsoft. ContainerRegistry, hogy a virtuális hálózat vagy az alhálózat törölve lett |
> | Action | Microsoft.ContainerRegistry/locations/operationResults/read | Aszinkron művelet eredményének beolvasása |
> | Action | Microsoft.ContainerRegistry/operations/read | Az összes rendelkezésre álló Azure Container Registry REST API művelet listája |
> | Action | Microsoft.ContainerRegistry/register/action | Regisztrálja az előfizetést a tároló-beállításjegyzék erőforrás-szolgáltatója számára, és lehetővé teszi a tároló-nyilvántartók létrehozását. |
> | Action | Microsoft.ContainerRegistry/registries/artifacts/delete | Összetevő törlése egy tároló-beállításjegyzékben. |
> | Action | Microsoft.ContainerRegistry/registries/builds/cancel/action | Egy meglévő Build megszakítása. |
> | Action | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Lekéri a Build naplók letöltésére szolgáló hivatkozást. |
> | Action | Microsoft.ContainerRegistry/registries/builds/read | Lekéri a megadott Build vagy listázza a megadott tároló beállításjegyzékének összes buildjét. |
> | Action | Microsoft.ContainerRegistry/registries/builds/write | A megadott paraméterekkel frissíti egy tároló-beállításjegyzék buildjét. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/delete | Kiépítési feladatot töröl egy tároló-beállításjegyzékből. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | A felépítési feladat forrás vezérlőelemének tulajdonságait sorolja fel. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/read | Lekéri a megadott felépítési feladat tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes felépítési feladatot. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Kiépített lépést töröl egy felépítési feladatból. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Felsorolja a fordítási argumentumokat a létrehozási lépésekhez, beleértve a titkos argumentumokat is. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Lekéri a megadott Build lépés tulajdonságait, vagy felsorolja a megadott Build feladathoz tartozó összes összeállítási lépést. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Létrehoz vagy frissít egy Build-lépést a megadott paraméterekkel rendelkező Build feladathoz. |
> | Action | Microsoft.ContainerRegistry/registries/buildTasks/write | Létrehoz vagy frissít egy Build feladatot egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Action | Microsoft.ContainerRegistry/registries/delete | Töröl egy tároló-beállításjegyzéket. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Egy Event Grid-szűrőt töröl egy tároló-beállításjegyzékből. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Lekéri a megadott Event Grid-szűrő tulajdonságait, vagy felsorolja az összes Event Grid-szűrőt a megadott tároló-beállításjegyzékhez. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Létrehoz vagy frissít egy Event Grid-szűrőt egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Action | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Lekéri a felhasználó feltöltési helyét, hogy fel tudja tölteni a forrást. |
> | Action | Microsoft.ContainerRegistry/registries/importImage/action | Rendszerkép importálása a tároló-beállításjegyzékbe a megadott paraméterekkel. |
> | Action | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Forrás feltöltési URL-címének beolvasása egy tároló-beállításjegyzékhez. |
> | Action | Microsoft.ContainerRegistry/registries/listCredentials/action | Felsorolja a megadott tároló-beállításjegyzék bejelentkezési hitelesítő adatait. |
> | Action | Microsoft.ContainerRegistry/registries/listPolicies/read | Felsorolja a megadott tároló-beállításjegyzék szabályzatait |
> | Action | Microsoft.ContainerRegistry/registries/listUsages/read | Felsorolja a megadott tároló-beállításjegyzék kvótájának használatát. |
> | Action | Microsoft.ContainerRegistry/registries/metadata/read | Beolvassa egy tároló-beállításjegyzék egy adott tárházának metaadatait |
> | Action | Microsoft.ContainerRegistry/registries/metadata/write | Frissíti egy tároló-beállításjegyzék tárházának metaadatait |
> | Action | Microsoft.ContainerRegistry/registries/operationStatuses/read | Beállításjegyzék aszinkron műveleti állapotának beolvasása |
> | Action | Microsoft.ContainerRegistry/registries/pull/read | Lemezképek lekérése vagy beolvasása egy tároló-beállításjegyzékből. |
> | Action | Microsoft.ContainerRegistry/registries/push/write | Képek leküldése vagy írása egy tároló-beállításjegyzékbe. |
> | Action | Microsoft.ContainerRegistry/registries/quarantineRead/read | Karanténba helyezett lemezképek lekérése vagy beolvasása a Container registryből |
> | Action | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Karanténba helyezett lemezképek karantén állapotának írása/módosítása |
> | Action | Microsoft.ContainerRegistry/registries/queueBuild/action | Létrehoz egy új buildet a kérés paraméterei alapján, és hozzáadja azt a Build-várólistához. |
> | Action | Microsoft.ContainerRegistry/registries/read | Lekéri a megadott tároló-beállításjegyzék tulajdonságait, vagy felsorolja a megadott erőforráscsoport vagy előfizetés alatt lévő összes tároló-bejegyzést. |
> | Action | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Újra létrehozza a megadott tároló-beállításjegyzékhez tartozó bejelentkezési hitelesítő adatok egyikét. |
> | Action | Microsoft.ContainerRegistry/registries/replications/delete | Törli a replikálást egy tároló-beállításjegyzékből. |
> | Action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Replikációs aszinkron műveleti állapot beolvasása |
> | Action | Microsoft. ContainerRegistry/nyilvántartások/replikálás/olvasás | Lekéri a megadott replikálás tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzék összes replikációját. |
> | Action | Microsoft. ContainerRegistry/nyilvántartások/replikálás/írás | Létrehoz vagy frissít egy tároló-beállításjegyzék replikálását a megadott paraméterekkel. |
> | Action | Microsoft.ContainerRegistry/registries/runs/cancel/action | Meglévő Futtatás megszakítása. |
> | Action | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Lekéri a log SAS URL-címét a futtatáshoz. |
> | Action | Microsoft.ContainerRegistry/registries/runs/read | Lekéri egy futtatási tulajdonságot egy tároló beállításjegyzékében vagy a lista futtatásakor. |
> | Action | Microsoft.ContainerRegistry/registries/runs/write | Frissíti a futtatást. |
> | Action | Microsoft.ContainerRegistry/registries/scheduleRun/action | Futtasson egy futtatást egy tároló-beállításjegyzékben. |
> | Action | Microsoft.ContainerRegistry/registries/sign/write | A tartalom megbízhatóságának leküldése/lekérése egy tároló-beállításjegyzékben. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/delete | Töröl egy feladatot egy tároló-beállításjegyzékhez. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | A tároló-beállításjegyzékhez tartozó feladat összes adatának listázása. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/read | Feladat beolvasása egy tároló-beállításjegyzékhez vagy az összes feladat listázása. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/write | Létrehoz vagy frissít egy feladatot egy tároló-beállításjegyzékhez. |
> | Action | Microsoft.ContainerRegistry/registries/updatePolicies/write | Frissíti a megadott tároló-beállításjegyzék szabályzatait |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/delete | Egy webhookot töröl egy tároló-beállításjegyzékből. |
> | Action | Microsoft. ContainerRegistry/nyilvántartások/webhookok/getCallbackConfig/művelet | Beolvassa a webhookhoz tartozó szolgáltatás-URI és egyéni fejlécek konfigurációját. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Felsorolja a megadott webhook legutóbbi eseményeit. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Webhook aszinkron műveleti állapotának beolvasása |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Elindít egy ping eseményt a webhookba. |
> | Action | Microsoft. ContainerRegistry/nyilvántartó/webhook/olvasás | Lekéri a megadott webhook tulajdonságait, vagy felsorolja a megadott tároló-beállításjegyzékhez tartozó összes webhookot. |
> | Action | Microsoft. ContainerRegistry/nyilvántartó/webhook/írás | Létrehoz vagy frissít egy webhookot egy tároló-beállításjegyzékhez a megadott paraméterekkel. |
> | Action | Microsoft. ContainerRegistry/nyilvántartások/írás | Létrehoz vagy frissít egy tároló-beállításjegyzéket a megadott paraméterekkel. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ContainerService/containerServices/delete | Egy tárolószolgáltatás törlése |
> | Action | Microsoft.ContainerService/containerServices/read | Egy tárolószolgáltatás beolvasása |
> | Action | Microsoft.ContainerService/containerServices/write | Új tárolószolgáltatás létrehozása vagy meglévő frissítése |
> | Action | Microsoft.ContainerService/locations/operationresults/read | Aszinkron művelet eredményállapotának beolvasása |
> | Action | Microsoft.ContainerService/locations/operations/read | Egy aszinkron művelet állapotát olvassa be |
> | Action | Microsoft.ContainerService/locations/orchestrators/read | A támogatott vezénylők listázása |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Egy felügyelt fürt hozzáférési profiljának beolvasása szerepkörnév alapján a hitelesítő adatok listázásával |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/read | Egy felügyelt fürt hozzáférési profiljának beolvasása szerepkörnév alapján |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/delete | Ügynök-készlet törlése |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/read | Ügynök készletének beolvasása |
> | Action | Microsoft.ContainerService/managedClusters/agentPools/write | Új ügynök készletének létrehozása vagy meglévő frissítése |
> | Action | Microsoft.ContainerService/managedClusters/delete | Egy felügyelt fürt törlése |
> | Action | Microsoft. Tárolószolgáltatás/managedClusters/érzékelők/olvasás | Felügyelt fürt Detektorának beolvasása |
> | Action | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Felügyelt fürt fürtadminisztrátori hitelesítő adatainak listázása |
> | Action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Felügyelt fürt fürtfelhasználói hitelesítő adatainak listázása |
> | Action | Microsoft.ContainerService/managedClusters/read | Egy felügyelt fürt beolvasása |
> | Action | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Felügyelt fürt HRE-profiljának alaphelyzetbe állítása |
> | Action | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Felügyelt fürt egyszerű szolgáltatásnév-profiljának alaphelyzetbe állítása |
> | Action | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | A fürt frissítési profiljának beolvasása |
> | Action | Microsoft.ContainerService/managedClusters/write | Új felügyelt fürt létrehozása vagy meglévő frissítése |
> | Action | Microsoft.ContainerService/openShiftClusters/delete | Nyitott SHIFT-fürt törlése |
> | Action | Microsoft.ContainerService/openShiftClusters/read | Nyitott SHIFT-fürt beszerzése |
> | Action | Microsoft.ContainerService/openShiftClusters/write | Új nyitott SHIFT-fürtöt hoz létre, vagy frissít egy meglévőt |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/delete | Nyitott eltolású felügyelt fürt törlése |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/read | Nyitott eltolású felügyelt fürt beszerzése |
> | Action | Microsoft.ContainerService/openShiftManagedClusters/write | Létrehoz egy új, nyitott eltolású felügyelt fürtöt, vagy frissít egy meglévőt |
> | Action | Microsoft.ContainerService/operations/read | A Microsoft.ContainerService erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Action | Microsoft.ContainerService/register/action | Előfizetés regisztrálása a Microsoft.ContainerService erőforrás-szolgáltatónál |
> | Action | Microsoft.ContainerService/unregister/action | Microsoft.ContainerService erőforrás-szolgáltatói előfizetés regisztrációjának törlése |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ContentModerator/applications/delete | Törlési művelet |
> | Action | Microsoft.ContentModerator/applications/listSecrets/action | Titkos kulcsok listázása |
> | Action | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Egyszeri bejelentkezési tokenek olvasása |
> | Action | Microsoft.ContentModerator/applications/read | Olvasási művelet |
> | Action | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Action | Microsoft.ContentModerator/applications/write | Írási művelet |
> | Action | Microsoft.ContentModerator/listCommunicationPreference/action | Kommunikációs beállítások listázása |
> | Action | Microsoft.ContentModerator/operations/read | olvasási műveletek |
> | Action | Microsoft.ContentModerator/updateCommunicationPreference/action | Kommunikációs beállítások frissítése |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.CostManagement/cloudConnectors/delete | A megadott cloudConnector törlése. |
> | Action | Microsoft.CostManagement/cloudConnectors/read | A hitelesített felhasználó cloudConnectors listázása. |
> | Action | Microsoft.CostManagement/cloudConnectors/write | A megadott cloudConnector létrehozása vagy frissítése. |
> | Action | Microsoft.CostManagement/dimensions/read | A hatókör összes támogatott dimenziójának listázása. |
> | Action | Microsoft.CostManagement/exports/action | Futtassa a megadott exportálást. |
> | Action | Microsoft.CostManagement/exports/delete | A megadott exportálás törlése. |
> | Action | Microsoft.CostManagement/exports/read | Az export hatókör szerinti listázása. |
> | Action | Microsoft.CostManagement/exports/run/action | Exportálás futtatása. |
> | Action | Microsoft.CostManagement/exports/write | A megadott exportálás létrehozása vagy frissítése. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | A hitelesített felhasználó externalBillingAccount belüli externalSubscriptions listázása. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/read | A hitelesített felhasználó externalBillingAccounts listázása. |
> | Action | Microsoft.CostManagement/externalSubscriptions/read | A hitelesített felhasználó externalSubscriptions listázása. |
> | Action | Microsoft.CostManagement/externalSubscriptions/write | A externalSubscription társított felügyeleti csoportjának frissítése |
> | Action | Microsoft.CostManagement/query/action | Használati adatok lekérdezése hatókör alapján. |
> | Action | Microsoft.CostManagement/query/read | Használati adatok lekérdezése hatókör alapján. |
> | Action | Microsoft.CostManagement/register/action | Regisztrálja a Microsoft. CostManagement hatókörére vonatkozó műveletet egy előfizetés alapján. |
> | Action | Microsoft.CostManagement/reports/action | A használati adatok hatókör szerint ütemezhetnek jelentéseket. |
> | Action | Microsoft.CostManagement/reports/read | A használati adatok hatókör szerint ütemezhetnek jelentéseket. |
> | Action | Microsoft.CostManagement/tenants/register/action | A Microsoft. CostManagement hatókörére vonatkozó művelet regisztrálása bérlő által. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/action | Azure-Customer Insights Adobe-metaadatok létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/read | Az Azure Customer Insights Adobe-metaadatok beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Az Azure Customer Insights megosztott hozzáférési aláírási szabályzatának törlése |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Az Azure Customer Insights megosztott hozzáférési aláírási szabályzatának beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Az Azure Customer Insights közös hozzáférésű aláírási házirend elsődleges kulcsának újbóli előállítása |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Azure Customer Insights közös hozzáférésű aláírási házirend másodlagos kulcsának újragenerálása |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Azure-Customer Insights közös hozzáférésű aláírási szabályzat létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure Customer Insights-összekötő aktiválása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/activate/action | Bármely Azure Customer Insights-összekötő aktiválása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/delete | Bármely Azure Customer Insights-összekötő törlése |
> | Action | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Bármely Azure Customer Insights Connector futtatókörnyezeti állapotának beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Az Azure Customer Insights-összekötők leképezésének aktiválása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Az Azure Customer Insights-összekötők leképezésének törlése |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Az Azure-Customer Insights összekötő-hozzárendelési művelet eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Az Azure Customer Insights-összekötők leképezésének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Azure Customer Insights-összekötők leképezésének létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/connectors/operations/read | Az Azure Customer Insights-összekötő összes műveletének eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/read | Bármely Azure Customer Insights-összekötő beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Azure Customer Insights-összekötő hitelesítési adatok lettek létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/connectors/update/action | Bármely Azure Customer Insights-összekötő frissítése |
> | Action | Microsoft.CustomerInsights/hubs/connectors/write | Azure Customer Insights-összekötő létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/action | Azure Customer Insights CRM-metaadatok létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/crmmetadata/read | Bármely Azure Customer Insights CRM-metaadatok beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/delete | Bármely Azure Customer Insights hub törlése |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/delete | Az Azure Customer Insights GDPR törlése |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/read | Bármely Azure Customer Insights GDPR beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/gdpr/write | Azure-Customer Insights GDPR létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Azure Customer Insights hub számlázási kreditek beszerzése |
> | Action | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Azure Customer Insights hub számlázási előzményeinek beszerzése |
> | Action | Microsoft.CustomerInsights/hubs/images/delete | Bármely Azure Customer Insights-rendszerkép törlése |
> | Action | Microsoft.CustomerInsights/hubs/images/read | Bármely Azure Customer Insights-rendszerkép beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/images/write | Azure Customer Insights-rendszerkép létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/interactions/delete | Az Azure Customer Insights interakcióinak törlése |
> | Action | Microsoft.CustomerInsights/hubs/interactions/operations/read | Az Azure Customer Insights interakciós művelet eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/interactions/read | Az Azure Customer Insights interakciójának beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Kapcsolati hivatkozások javaslata bármely Azure-Customer Insights interakcióhoz |
> | Action | Microsoft.CustomerInsights/hubs/interactions/write | Azure-Customer Insights interakció létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/kpi/delete | Az Azure Customer Insights fő teljesítménymutatóinak törlése |
> | Action | Microsoft.CustomerInsights/hubs/kpi/operations/read | Az Azure Customer Insights fő teljesítménymutatói műveleti eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/kpi/read | Az Azure Customer Insights fő teljesítménymutatójának beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Az Azure Customer Insights fő teljesítménymutatóinak újrafeldolgozása |
> | Action | Microsoft.CustomerInsights/hubs/kpi/write | Az Azure Customer Insights fő teljesítménymutatójának létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/links/delete | Bármely Azure Customer Insights-hivatkozás törlése |
> | Action | Microsoft.CustomerInsights/hubs/links/operations/read | Az Azure Customer Insights-hivatkozások művelet eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/links/read | Az Azure Customer Insights hivatkozásainak beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/links/write | Azure-ügyfelek hivatkozásainak létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/action | Azure Customer Insights MSE-metaadatok létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/msemetadata/read | Az Azure Customer Insights MSE metaadatainak beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/operationresults/read | Az Azure Customer Insights hub műveleti eredményeinek beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/predictions/delete | Az Azure Customer Insights-előrejelzések törlése |
> | Action | Microsoft.CustomerInsights/hubs/predictions/operations/read | Az Azure Customer Insights-előrejelzések működési eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/predictions/read | Az Azure Customer Insights-előrejelzések beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/predictions/write | Azure-ügyfelekhez kapcsolódó előrejelzések létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Az Azure Customer Insights prediktív egyeztetési szabályzatok törlése |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Az Azure Customer Insights prediktív egyeztetési szabályzatok műveleti eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Az Azure Customer Insights prediktív egyeztetési szabályzatok beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Azure Customer Insights prediktív egyeztetési szabályzatok létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/profiles/delete | Bármely Azure Customer Insights-profil törlése |
> | Action | Microsoft.CustomerInsights/hubs/profiles/operations/read | Az Azure Customer Insights-profil működési eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/profiles/read | Azure Customer Insights-profil beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/profiles/write | Bármilyen Azure Customer Insights-profil írása |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | A rendelkezésre álló naplók beolvasása az erőforráshoz |
> | Action | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Az erőforráshoz elérhető metrikák beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/read | Bármely Azure Customer Insights hub beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Az Azure Customer Insights kapcsolati hivatkozásainak törlése |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Az Azure Customer Insights kapcsolati kapcsolatainak beolvasása művelet eredménye |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Bármely Azure Customer Insights kapcsolati hivatkozás beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Bármely Azure Customer Insights kapcsolati hivatkozás létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/relationships/delete | Azure-Customer Insights kapcsolatok törlése |
> | Action | Microsoft.CustomerInsights/hubs/relationships/operations/read | Az Azure Customer Insights-kapcsolatok művelet eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/relationships/read | Az Azure Customer Insights kapcsolatainak beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/relationships/write | Azure-Customer Insights kapcsolatok létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Bármely Azure Customer Insights RBAC-hozzárendelés törlése |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Bármely Azure Customer Insights RBAC-hozzárendelési művelet eredményének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/read | Bármely Azure Customer Insights RBAC-hozzárendelés beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/roleAssignments/write | Azure Customer Insights RBAC-hozzárendelés létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/roles/read | Az Azure Customer Insights RBAC szerepköreinek beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Azure Customer Insights SalesForce-metaadatok létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Az Azure Customer Insights SalesForce-metaadatok beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/segments/delete | Azure Customer Insights-szegmensek törlése |
> | Action | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Az Azure Customer Insight dinamikus szegmensek kezelése |
> | Action | Microsoft.CustomerInsights/hubs/segments/read | Azure Customer Insights-szegmensek beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/segments/static/action | Bármilyen Azure Customer Insight statikus szegmens kezelése |
> | Action | Microsoft.CustomerInsights/hubs/segments/write | Azure Customer Insights-szegmensek létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Az Azure Customer Insights SqlConnectionStrings törlése |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Bármely Azure Customer Insights SqlConnectionStrings beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Azure-Customer Insights SqlConnectionStrings létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Javaslat típusú séma előállítása mintaadatok alapján |
> | Action | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Az Azure Customer Insights hub beállításainak kezelése |
> | Action | Microsoft.CustomerInsights/hubs/views/delete | Bármely Azure Customer Insights-alkalmazás nézetének törlése |
> | Action | Microsoft.CustomerInsights/hubs/views/read | Az Azure Customer Insights alkalmazás nézetének beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/views/write | Azure Customer Insights-alkalmazások nézetének létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/hubs/widgettypes/read | Az Azure Customer Insights app widget-típusok beolvasása |
> | Action | Microsoft.CustomerInsights/hubs/write | Azure Customer Insights hub létrehozása vagy frissítése |
> | Action | Microsoft.CustomerInsights/operations/read | Azure Customer Insights API-metaadatok olvasása |
> | Action | Microsoft.CustomerInsights/register/action | Regisztrálja az előfizetést a Customer Insights erőforrás-szolgáltatóhoz, és lehetővé teszi Customer Insights erőforrások létrehozását |
> | Action | Microsoft.CustomerInsights/unregister/action | A Customer Insights erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Lehetőség a visszaküldési begyűjtések időpontjának lefoglalására. |
> | Action | Microsoft.DataBox/jobs/cancel/action | Egy folyamatban lévő megrendelés törlése. |
> | Action | Microsoft.DataBox/jobs/delete | Megrendelések törlése |
> | Action | Microsoft.DataBox/jobs/listCredentials/action | A megrendeléssel kapcsolatos titkosítatlan hitelesítő adatok listázása. |
> | Action | Microsoft.DataBox/jobs/read | Megrendelések listázása vagy beolvasása |
> | Action | Microsoft.DataBox/jobs/write | Megrendelések létrehozása vagy frissítése |
> | Action | Microsoft.DataBox/locations/availableSkus/action | Ez a metódus a rendelkezésre álló termékváltozatok listáját adja vissza. |
> | Action | Microsoft. DataBox/Locations/availableSkus/READ | Az elérhető SKU-ket listázhatja vagy beolvashatja |
> | Action | Microsoft.DataBox/locations/operationResults/read | A művelet eredményeinek listázása vagy beolvasása |
> | Action | Microsoft.DataBox/locations/validateAddress/action | A szállítási cím ellenőrzése és másodlagos címek biztosítása, ha vannak ilyenek. |
> | Action | Microsoft. DataBox/Locations/validateInputs/Action |  |
> | Action | Microsoft.DataBox/register/action | Microsoft.Databox szolgáltató regisztrálása |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | A riasztások listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | A riasztások listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | A sávszélesség-ütemtervek törlése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | A sávszélesség-ütemtervek listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | A sávszélesség-ütemtervek listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | A sávszélesség-ütemtervek létrehozása vagy frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | A Data Box Edge-eszközök törlése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | A frissítések letöltése az eszközre |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Az erőforrás részletes adatainak beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | A frissítések telepítése az eszközön |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | A feladatok listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Az eszköz hálózati beállításainak listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | A művelet állapotának listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Törli a rendeléseket |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | A megrendelések listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | A megrendelések listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Létrehozza vagy frissíti a rendeléseket |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | A Data Box Edge eszközök listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | A Data Box Edge eszközök listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | A Data Box Edge eszközök listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | A szerepkörök törlése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | A szerepkörök listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | A szerepkörök listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Szerepkörök létrehozása vagy frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Frissítések keresése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Biztonsági beállítások frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | A megosztások törlése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | A megosztások listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | A megosztások listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | A megosztás metaadatainak felhőbeli adatokkal való frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | A megosztások létrehozása vagy frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | A Storage-fiók hitelesítő adatainak törlése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | A Storage-fiók hitelesítő adatainak létrehozása vagy frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Az eseményindítók törlése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Az eseményindítók listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Az eseményindítók listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Az eseményindítók létrehozása vagy frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Felsorolja vagy lekéri a frissítés összegzését |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | A tanúsítvány feltöltése az eszköz regisztrálása céljából |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Törli a felhasználók megosztása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | A művelet eredményének megjelenítése vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | A megosztási felhasználók listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | A megosztási felhasználók listája vagy beolvasása |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | A felhasználók megosztásának létrehozása vagy frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | A Data Box Edge-eszközök létrehozása vagy frissítése |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | A Data Box Edge-eszközök létrehozása vagy frissítése |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Databricks/register/action | Regisztrálás a Databricksben. |
> | Action | Microsoft.Databricks/workspaces/delete | Databricks-munkaterület eltávolítása. |
> | Action | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A rendelkezésre álló diagnosztikai beállításait adja meg a Databricks munkaterület |
> | Action | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítások hozzáadása vagy módosítása. |
> | Action | Microsoft. Databricks/munkaterületek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | A Databricks-munkaterület elérhető naplódefinícióinak beolvasása |
> | Action | Microsoft.Databricks/workspaces/read | Databricks-munkaterületek listájának beolvasása. |
> | Action | Microsoft. Databricks/munkaterületek/írás | Databricks-munkaterület létrehozása. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DataCatalog/catalogs/delete | Data Catalog erőforrás-szolgáltatóhoz tartozó katalógusok erőforrásának törlése. |
> | Action | Microsoft.DataCatalog/catalogs/read | Data Catalog erőforrás-szolgáltatóhoz tartozó katalógusok erőforrásának olvasása. |
> | Action | Microsoft.DataCatalog/catalogs/write | Data Catalog erőforrás-szolgáltatónál írja be a katalógusok erőforrását. |
> | Action | Microsoft.DataCatalog/checkNameAvailability/read | A Data Catalog erőforrás-szolgáltatóhoz tartozó katalógus nevének rendelkezésre állásának keresése. |
> | Action | Microsoft.DataCatalog/datacatalogs/delete | Data Catalog erőforrás-szolgáltatóhoz tartozó DataCatalog-erőforrás törlése. |
> | Action | Microsoft.DataCatalog/datacatalogs/read | Data Catalog erőforrás-szolgáltató DataCatalog-erőforrásának olvasása. |
> | Action | Microsoft.DataCatalog/datacatalogs/write | DataCatalog-erőforrás írása Data Catalog erőforrás-szolgáltatóhoz. |
> | Action | Microsoft.DataCatalog/operations/read | A Data Catalog erőforrás-szolgáltató összes elérhető műveletének beolvasása. |
> | Action | Microsoft.DataCatalog/register/action | Az előfizetés regisztrálása Data Catalog erőforrás-szolgáltatóhoz |
> | Action | Microsoft.DataCatalog/unregister/action | Data Catalog erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Ellenőrzi, hogy a Data Factory neve elérhető-e a használatra. |
> | Action | Microsoft.DataFactory/datafactories/activitywindows/read | A Data Factory a megadott paraméterekkel olvassa be a tevékenységek ablakait. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Beolvassa a tevékenység ablakait a folyamat tevékenységéhez a megadott paraméterekkel. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | A megadott paraméterekkel olvassa be a folyamathoz tartozó Windows-tevékenységeket. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/delete | Törli a folyamatokat. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Szünetelteti a folyamatokat. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/read | Bármely folyamat beolvasása. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Egy folyamat folytatása. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/update/action | Frissíti a folyamatokat. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/write | Egy folyamat létrehozása vagy frissítése. |
> | Action | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | A megadott paraméterekkel beolvassa az adatkészlethez tartozó tevékenységek ablakait. |
> | Action | Microsoft.DataFactory/datafactories/datasets/delete | Töröl minden adatkészletet. |
> | Action | Microsoft.DataFactory/datafactories/datasets/read | Bármilyen adatkészlet beolvasása. |
> | Action | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Az adott adathalmazhoz tartozó adatszelet futtatásának beolvasása a megadott kezdési időponttal. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/read | Az adatszeletek lekérése az adott időszakban. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/write | Az adatszelet állapotának frissítése |
> | Action | Microsoft.DataFactory/datafactories/datasets/write | Létrehoz vagy frissít egy adatkészletet. |
> | Action | Microsoft.DataFactory/datafactories/delete | Törli a Data Factory. |
> | Action | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Az átjáróhoz tartozó kapcsolatok adatainak beolvasása. |
> | Action | Microsoft.DataFactory/datafactories/gateways/delete | Bármely átjáró törlése. |
> | Action | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Felsorolja az átjárók hitelesítési kulcsait. |
> | Action | Microsoft.DataFactory/datafactories/gateways/read | Bármely átjáró beolvasása. |
> | Action | Microsoft. DataFactory/datafactories/átjárók/regenerateauthkey/művelet | Újragenerálja a hitelesítési kulcsokat bármely átjáró számára. |
> | Action | Microsoft.DataFactory/datafactories/gateways/write | Létrehoz vagy frissít egy átjárót. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/delete | Törli a társított szolgáltatásokat. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/read | A társított szolgáltatás beolvasása. |
> | Action | Microsoft.DataFactory/datafactories/linkedServices/write | Létrehozza vagy frissíti a társított szolgáltatásokat. |
> | Action | Microsoft.DataFactory/datafactories/read | A Data Factory beolvasása. |
> | Action | Microsoft.DataFactory/datafactories/runs/loginfo/read | Egy SAS URI-t olvas be a naplókat tartalmazó blob-tárolóba. |
> | Action | Microsoft.DataFactory/datafactories/tables/delete | Töröl minden adatkészletet. |
> | Action | Microsoft.DataFactory/datafactories/tables/read | Bármilyen adatkészlet beolvasása. |
> | Action | Microsoft. DataFactory/datafactories/Tables/Write | Létrehoz vagy frissít egy adatkészletet. |
> | Action | Microsoft.DataFactory/datafactories/write | Létrehozza vagy frissíti a Data Factory. |
> | Action | Microsoft.DataFactory/factories/cancelpipelinerun/action | Megszakítja a futtatási azonosító által megadott folyamat futtatását. |
> | Action | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Létrehoz egy adatfolyam-hibakeresési munkamenetet. |
> | Action | Microsoft.DataFactory/factories/dataflows/delete | Törli az adatfolyamatot. |
> | Action | Microsoft.DataFactory/factories/dataflows/read | Beolvassa az adatfolyamot. |
> | Action | Microsoft.DataFactory/factories/dataflows/write | Adatfolyam létrehozása vagy frissítése |
> | Action | Microsoft.DataFactory/factories/datasets/delete | Töröl minden adatkészletet. |
> | Action | Microsoft.DataFactory/factories/datasets/read | Bármilyen adatkészlet beolvasása. |
> | Action | Microsoft.DataFactory/factories/datasets/write | Létrehoz vagy frissít egy adatkészletet. |
> | Action | Microsoft.DataFactory/factories/delete | Data Factory törlése. |
> | Action | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Egy adatfolyam-hibakeresési munkamenet törlése. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Az ADF Adatsík szolgáltatás elérésének beolvasása. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Olvasási hozzáférés az ADF Adatsík szolgáltatáshoz. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/action | A kitettség-vezérlési funkció értékének beolvasása az adott helyen. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/read | Beolvassa az expozíció-vezérlés funkció értékét az adott helyen. |
> | Action | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Lekéri a GitHub hozzáférési tokenjét. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/delete | Töröl minden Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | A Integration Runtime a kapcsolatok adatainak beolvasása. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | A megadott Integration Runtime SSIS Integration Runtime metaadatok beolvasása. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Integration Runtime állapot beolvasása. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Csatolt Integration Runtime-hivatkozás létrehozása a megadott megosztott Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Felsorolja az egyes Integration Runtime hitelesítő kulcsait. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Beolvassa az egyes Integration Runtime figyelési szolgáltatásait. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | A megadott Integration Runtime csomópontjának törlése. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | A Integration Runtime megadott csomópontjának IP-címét adja vissza. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | A megadott Integration Runtime csomópontjának beolvasása. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Frissíti a saját üzemeltetésű Integration Runtime csomópontot. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/read | Bármilyen Integration Runtime beolvasása. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | A megadott Integration Runtime SSIS frissítése Integration Runtime metaadatokat. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Újragenerálja a megadott Integration Runtime hitelesítési kulcsait. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Eltávolítja a megadott Integration Runtime csatolt Integration Runtime hivatkozásait. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/start/action | A Integration Runtime elindítása. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Leállítja a Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Szinkronizálja a megadott Integration Runtime hitelesítő adatait. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | A megadott Integration Runtime frissítése. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/write | Bármilyen Integration Runtime létrehozása vagy frissítése. |
> | Action | Microsoft.DataFactory/factories/linkedServices/delete | A társított szolgáltatás törlése. |
> | Action | Microsoft.DataFactory/factories/linkedServices/read | A társított szolgáltatás beolvasása. |
> | Action | Microsoft.DataFactory/factories/linkedServices/write | Társított szolgáltatás létrehozása vagy frissítése |
> | Action | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Beolvassa a megadott folyamat futtatási AZONOSÍTÓjának tevékenységeit. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Megszakítja a futtatási azonosító által megadott folyamat futtatását. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | A megadott folyamat futtatási AZONOSÍTÓjának lekérdezése a tevékenység futásakor. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Beolvassa a lekérdezési tevékenység futásának eredményét a megadott folyamat futtatási AZONOSÍTÓJÁHOZ. |
> | Action | Microsoft.DataFactory/factories/pipelineruns/read | A folyamat futtatásának beolvasása. |
> | Action | Microsoft.DataFactory/factories/pipelines/createrun/action | Létrehoz egy futtatást a folyamathoz. |
> | Action | Microsoft.DataFactory/factories/pipelines/delete | A folyamat törlése. |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Lekéri a tevékenységek futtatásának folyamatát. |
> | Action | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | A folyamat futtatásának beolvasása. |
> | Action | Microsoft.DataFactory/factories/pipelines/read | Folyamatban van a folyamat beolvasása. |
> | Action | Microsoft.DataFactory/factories/pipelines/write | Folyamat létrehozása vagy frissítése |
> | Action | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Lekérdezi a hibakeresési folyamat futtatását. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/action | Lekérdezi a folyamat futtatását. |
> | Action | Microsoft.DataFactory/factories/querypipelineruns/read | Beolvassa a lekérdezési folyamat futtatásának eredményét. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/action | Az trigger futtatását kérdezi le. |
> | Action | Microsoft.DataFactory/factories/querytriggerruns/read | Beolvassa az trigger futtatásának eredményét. |
> | Action | Microsoft.DataFactory/factories/read | Data Factory olvasása. |
> | Action | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Elindít egy adatfolyam-hibakeresési munkamenetet. |
> | Action | Microsoft.DataFactory/factories/triggerruns/read | A trigger futtatásának beolvasása. |
> | Action | Microsoft.DataFactory/factories/triggers/delete | Töröl minden triggert. |
> | Action | Microsoft.DataFactory/factories/triggers/read | Bármely trigger beolvasása. |
> | Action | Microsoft.DataFactory/factories/triggers/start/action | Elindítja az összes triggert. |
> | Action | Microsoft.DataFactory/factories/triggers/stop/action | Leállítja az összes triggert. |
> | Action | Microsoft.DataFactory/factories/triggers/triggerruns/read | A trigger futtatásának beolvasása. |
> | Action | Microsoft.DataFactory/factories/triggers/write | Minden trigger létrehozása vagy frissítése. |
> | Action | Microsoft. DataFactory/gyárak/írás | Data Factory létrehozása vagy frissítése |
> | Action | Microsoft.DataFactory/locations/configureFactoryRepo/action | A gyári tárházat konfigurálja. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/action | A kitettség-vezérlési funkció értékének beolvasása az adott helyen. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/read | Beolvassa az expozíció-vezérlés funkció értékét az adott helyen. |
> | Action | Microsoft.DataFactory/operations/read | A Microsoft Data Factory-szolgáltató összes műveletének beolvasása. |
> | Action | Microsoft.DataFactory/register/action | Regisztrálja az előfizetést a Data Factory erőforrás-szolgáltatóhoz. |
> | Action | Microsoft.DataFactory/unregister/action | A Data Factory erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Számítási szabályzat törlése. |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | A számítási szabályzattal kapcsolatos információk beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Számítási szabályzat létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Data Lake Store-fiók DataLakeAnalytics-fiókból való leválasztása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókjával kapcsolatos információk beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Data Lake Store-fiókját. |
> | Action | Microsoft.DataLakeAnalytics/accounts/delete | DataLakeAnalytics-fiók törlése. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Tűzfalszabály adatainak beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Tűzfalszabály létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | DataLakeAnalytics-fiók művelete eredményének beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/read | Egy meglévő DataLakeAnalytics-fiók adatainak beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Egy DataLakeAnalytics-fiók csatolt Storage-fiókjának tároló-jogkivonatait listázza. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | DataLakeAnalytics-fiókhoz tartozó társított Storage-fiók tárolóinak beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Egy DataLakeAnalytics-fiókhoz tartozó Storage-fiók leválasztása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | DataLakeAnalytics-fiókhoz tartozó társított Storage-fiók adatainak beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Hozzon létre vagy frissítsen egy DataLakeAnalytics-fiók csatolt Storage-fiókját. |
> | Action | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Engedélyek megadása a más felhasználók által küldött feladatok megszakításához. |
> | Action | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | SystemMaxAnalyticsUnits átvitele DataLakeAnalytics-fiókok között. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Virtuális hálózati szabály törlése. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Egy virtuális hálózati szabály adatainak beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Virtuális hálózati szabály létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeAnalytics/accounts/write | DataLakeAnalytics-fiók létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeAnalytics/locations/capability/read | A DataLakeAnalytics használatával kapcsolatos előfizetések funkcióinak beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | DataLakeAnalytics-fióknév rendelkezésre állásának keresése. |
> | Action | Microsoft.DataLakeAnalytics/locations/operationResults/read | DataLakeAnalytics-fiók művelete eredményének beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/locations/usages/read | Az előfizetéshez tartozó kvóta-használati információk beolvasása a DataLakeAnalytics használatával kapcsolatban. |
> | Action | Microsoft.DataLakeAnalytics/operations/read | A DataLakeAnalytics elérhető műveleteinek beolvasása. |
> | Action | Microsoft.DataLakeAnalytics/register/action | Regisztrálja az előfizetést a DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeStore/accounts/delete | Data Lake Store-fiók törlése. |
> | Action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | A kulcstartó engedélyezése egy Data Lake Store-fiókhoz. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | EventGrid-szűrő törlése. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | EventGrid-szűrő beolvasása. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | EventGrid-szűrő létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/delete | Tűzfalszabály törlése. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/read | Tűzfalszabály adatainak beolvasása. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/write | Tűzfalszabály létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeStore/accounts/operationResults/read | Data Lake Store-fiók művelete eredményének beolvasása. |
> | Action | Microsoft.DataLakeStore/accounts/read | Egy meglévő Data Lake Store-fiók adatainak beolvasása. |
> | Action | Microsoft.DataLakeStore/accounts/Superuser/action | Adja meg a rendszeradminisztrátort Data Lake Store ha a Microsoft. Authorization/roleAssignments/Write. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Megbízható identitás szolgáltatójának törlése. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Információ kérése egy megbízható identitás-szolgáltatóról. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Hozzon létre vagy frissítsen egy megbízható identitás-szolgáltatót. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Virtuális hálózati szabály törlése. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Egy virtuális hálózati szabály adatainak beolvasása. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Virtuális hálózati szabály létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeStore/accounts/write | Data Lake Store-fiók létrehozása vagy frissítése. |
> | Action | Microsoft.DataLakeStore/locations/capability/read | A Data Lake Store használatával kapcsolatos előfizetések funkcióinak beolvasása. |
> | Action | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Data Lake Store-fióknév rendelkezésre állásának keresése. |
> | Action | Microsoft.DataLakeStore/locations/operationResults/read | Data Lake Store-fiók művelete eredményének beolvasása. |
> | Action | Microsoft.DataLakeStore/locations/usages/read | Az előfizetéshez tartozó kvóta-használati információk beolvasása a Data Lake Store használatával kapcsolatban. |
> | Action | Microsoft.DataLakeStore/operations/read | A Data Lake Store elérhető műveleteinek beolvasása. |
> | Action | Microsoft.DataLakeStore/register/action | Regisztrálja az előfizetést a Data Lake Store. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DataMigration/locations/operationResults/read | 202-es (Elfogadva) válaszhoz kapcsolódó sokáig futó művelet állapotának lekérése |
> | Action | Microsoft.DataMigration/locations/operationStatuses/read | 202-es (Elfogadva) válaszhoz kapcsolódó sokáig futó művelet állapotának lekérése |
> | Action | Microsoft.DataMigration/register/action | Az előfizetés regisztrálása az Azure Database Migration Service szolgáltatójával |
> | Action | Microsoft.DataMigration/services/addWorker/action | DMS-feldolgozó hozzáadása a szolgáltatás elérhető feldolgozóihoz |
> | Action | Microsoft. DataMigration/szolgáltatások/checkStatus/művelet | A szolgáltatás telepítésének és futásának ellenőrzése |
> | Action | Microsoft.DataMigration/services/configureWorker/action | DMS-feldolgozó konfigurálása a szolgáltatás elérhető feldolgozóihoz |
> | Action | Microsoft.DataMigration/services/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Action | Microsoft.DataMigration/services/projects/accessArtifacts/action | A projektösszetevőkre vonatkozó GET vagy PUT műveletek elvégzéséhez használható URL létrehozása |
> | Action | Microsoft.DataMigration/services/projects/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Action | Microsoft.DataMigration/services/projects/files/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Action | Microsoft.DataMigration/services/projects/files/read | Olvasható információk erőforrásokról |
> | Action | Microsoft.DataMigration/services/projects/files/read/action | Szerezzen be egy URL-címet, amellyel olvashatja vagy a fájl tartalmát |
> | Action | Microsoft.DataMigration/services/projects/files/readWrite/action | Szerezzen be egy URL-címet, amellyel olvashatja vagy írhatja a fájl tartalmát |
> | Action | Microsoft.DataMigration/services/projects/files/write | Vezérlők és tulajdonságaik létrehozása és frissítése |
> | Action | Microsoft.DataMigration/services/projects/read | Olvasható információk erőforrásokról |
> | Action | Microsoft.DataMigration/services/projects/tasks/cancel/action | A feladat megszakítása, ha jelenleg fut |
> | Action | Microsoft.DataMigration/services/projects/tasks/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Action | Microsoft.DataMigration/services/projects/tasks/read | Olvasható információk erőforrásokról |
> | Action | Microsoft.DataMigration/services/projects/tasks/write | Feladatok futtatása Azure Database Migration Service-feladatok |
> | Action | Microsoft.DataMigration/services/projects/write | Feladatok futtatása Azure Database Migration Service-feladatok |
> | Action | Microsoft.DataMigration/services/read | Olvasható információk erőforrásokról |
> | Action | Microsoft.DataMigration/services/removeWorker/action | Eltávolít egy DMS-feldolgozót a szolgáltatás elérhető feldolgozói közül |
> | Action | Microsoft.DataMigration/services/serviceTasks/cancel/action | A feladat megszakítása, ha jelenleg fut |
> | Action | Microsoft.DataMigration/services/serviceTasks/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Action | Microsoft. DataMigration/Services/serviceTasks/READ | Olvasható információk erőforrásokról |
> | Action | Microsoft. DataMigration/szolgáltatások/serviceTasks/írás | Feladatok futtatása Azure Database Migration Service-feladatok |
> | Action | Microsoft.DataMigration/services/slots/delete | Töröl egy erőforrást és annak összes gyermekét |
> | Action | Microsoft. DataMigration/szolgáltatások/bővítőhely/olvasás | Olvasható információk erőforrásokról |
> | Action | Microsoft. DataMigration/szolgáltatások/bővítőhely/írás | Vezérlők és tulajdonságaik létrehozása és frissítése |
> | Action | Microsoft. DataMigration/szolgáltatások/indítás/művelet | A DMS szolgáltatás elindítása az áttelepítések feldolgozásának ismételt engedélyezéséhez |
> | Action | Microsoft.DataMigration/services/stop/action | A DMS-szolgáltatás leállítása a költségek csökkentése érdekében |
> | Action | Microsoft.DataMigration/services/updateAgentConfig/action | Frissíti a DMS-ügynök konfigurációját a megadott értékekkel. |
> | Action | Microsoft. DataMigration/szolgáltatások/írás | Vezérlők és tulajdonságaik létrehozása és frissítése |
> | Action | Microsoft.DataMigration/skus/read | DMS-erőforrások által támogatott SKU-k listájának lekérése. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. DBforMariaDB/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Action | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | ResourceGroup-alapú MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | MariaDB-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforMariaDB/locations/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Action | Microsoft.DBforMariaDB/operations/read | A MariaDB-műveletek listájának visszaadása. |
> | Action | Microsoft.DBforMariaDB/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Action | Microsoft.DBforMariaDB/register/action | MariaDB erőforrás-szolgáltató regisztrálása |
> | Action | Microsoft.DBforMariaDB/servers/administrators/delete | Törli a MariaDB-kiszolgáló egy meglévő rendszergazdáját. |
> | Action | Microsoft.DBforMariaDB/servers/administrators/read | Lekéri a MariaDB-kiszolgáló rendszergazdáinak listáját. |
> | Action | Microsoft. DBforMariaDB/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a MariaDB-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Action | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Új javaslat műveleti munkamenet létrehozása |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | A tanácsadók listájának visszaadása |
> | Action | Microsoft.DBforMariaDB/servers/advisors/read | Advisor visszaküldése |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Action | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Javasolt művelet visszaadása |
> | Action | Microsoft. DBforMariaDB/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Action | Microsoft. DBforMariaDB/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Action | Microsoft.DBforMariaDB/servers/databases/delete | Töröl egy meglévő MariaDB-adatbázist. |
> | Action | Microsoft.DBforMariaDB/servers/databases/read | Visszaadja a MariaDB-adatbázisok listáját, vagy lekéri a megadott adatbázis tulajdonságait. |
> | Action | Microsoft.DBforMariaDB/servers/databases/write | Létrehoz egy MariaDB-adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Action | Microsoft.DBforMariaDB/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/read | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Action | Microsoft.DBforMariaDB/servers/logFiles/read | A MariaDB-naplófájlok listájának visszaadása. |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | A MariaDB-kiszolgálók számára elérhető naplók beolvasása |
> | Action | Microsoft. DBforMariaDB/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Egy lekérdezés szövegének visszaadása |
> | Action | Microsoft.DBforMariaDB/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Action | Microsoft.DBforMariaDB/servers/recoverableServers/read | A helyreállítható MariaDB-kiszolgáló adatainak visszaadása |
> | Action | Microsoft.DBforMariaDB/servers/replicas/read | MariaDB-kiszolgáló olvasási replikáinak beolvasása |
> | Action | Microsoft.DBforMariaDB/servers/restart/action | Egy adott kiszolgáló újraindítása. |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Action | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Action | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Action | Microsoft. DBforMariaDB/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Action | Microsoft.DBforMariaDB/servers/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Példányra vonatkozó várakozási idő statisztikája |
> | Action | Microsoft.DBforMariaDB/servers/waitStatistics/read | Várakozási statisztika visszaküldése |
> | Action | Microsoft.DBforMariaDB/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. DBforMySQL/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Action | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | ResourceGroup-alapú MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | A MySQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforMySQL/locations/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Action | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Action | Microsoft.DBforMySQL/operations/read | A MySQL-műveletek listájának visszaadása. |
> | Action | Microsoft.DBforMySQL/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Action | Microsoft.DBforMySQL/register/action | MySQL erőforrás-szolgáltató regisztrálása |
> | Action | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/törlés | A MySQL-kiszolgáló meglévő rendszergazdájának törlése. |
> | Action | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/olvasás | Lekéri a MySQL-kiszolgáló rendszergazdáinak listáját. |
> | Action | Microsoft. DBforMySQL/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a MySQL-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Action | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Új javaslat műveleti munkamenet létrehozása |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | A tanácsadók listájának visszaadása |
> | Action | Microsoft.DBforMySQL/servers/advisors/read | Advisor visszaküldése |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | A javasolt műveletek listájának visszaadása |
> | Action | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Javasolt művelet visszaadása |
> | Action | Microsoft. DBforMySQL/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Action | Microsoft. DBforMySQL/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Action | Microsoft.DBforMySQL/servers/databases/delete | Töröl egy meglévő MySQL-adatbázist. |
> | Action | Microsoft.DBforMySQL/servers/databases/read | A MySQL-adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Action | Microsoft.DBforMySQL/servers/databases/write | Egy MySQL-adatbázist hoz létre a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Action | Microsoft.DBforMySQL/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Action | Microsoft. DBforMySQL/Servers/firewallRules/READ | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Action | Microsoft.DBforMySQL/servers/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Action | Microsoft. DBforMySQL/kiszolgálók/naplófájlok/olvasás | A PostgreSQL-naplófájlok listájának visszaadása. |
> | Action | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Action | Microsoft. DBforMySQL/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A MySQL-kiszolgálókhoz elérhető naplók beolvasása |
> | Action | Microsoft. DBforMySQL/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Action | Microsoft.DBforMySQL/servers/queryTexts/action | Egy lekérdezés szövegének visszaadása |
> | Action | Microsoft.DBforMySQL/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Action | Microsoft. DBforMySQL/Servers/recoverableServers/READ | A helyreállítható MySQL-kiszolgáló adatainak visszaadása |
> | Action | Microsoft.DBforMySQL/servers/replicas/read | MySQL-kiszolgáló olvasási replikáinak beolvasása |
> | Action | Microsoft.DBforMySQL/servers/restart/action | Egy adott kiszolgáló újraindítása. |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Action | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Action | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Action | Microsoft. DBforMySQL/Servers/topQueryStatistics/READ | Lekérdezési statisztika visszaadása |
> | Action | Microsoft.DBforMySQL/servers/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Action | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Példányra vonatkozó várakozási idő statisztikája |
> | Action | Microsoft. DBforMySQL/Servers/waitStatistics/READ | Várakozási statisztika visszaküldése |
> | Action | Microsoft.DBforMySQL/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. DBforPostgreSQL/checkNameAvailability/művelet | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Action | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | ResourceGroup-alapú PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | A PostgreSQL-kiszolgáló műveleti eredményeinek visszaadása |
> | Action | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionAzureAsyncOperation/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionOperationResults/READ | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyAzureAsyncOperation/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Action | Microsoft. DBforPostgreSQL/Locations/privateEndpointConnectionProxyOperationResults/READ | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | A kiszolgáló fenyegetés-észlelési műveletének eredményét visszaadó lista. |
> | Action | Microsoft.DBforPostgreSQL/operations/read | A PostgreSQL-műveletek listájának visszaadása. |
> | Action | Microsoft.DBforPostgreSQL/performanceTiers/read | A rendelkezésre álló teljesítményi rétegek listáját adja vissza. |
> | Action | Microsoft.DBforPostgreSQL/register/action | A PostgreSQL erőforrás-szolgáltató regisztrálása |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/törlés | A PostgreSQL-kiszolgáló meglévő rendszergazdájának törlése. |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/olvasás | Lekéri a PostgreSQL-kiszolgáló rendszergazdáinak listáját. |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/rendszergazdák/írás | Létrehozza vagy frissíti a PostgreSQL-kiszolgáló rendszergazdáját a megadott paraméterekkel. |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/tanácsadók/olvasás | A tanácsadók listájának visszaadása |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/tanácsadók/recommendedActions/READ | A javasolt műveletek listájának visszaadása |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Javaslatok készítése |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/delete | Töröl egy meglévő PostgreSQL-adatbázist. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/read | A PostgreSQL-adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/write | Létrehoz egy PostgreSQL-adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait. |
> | Action | Microsoft.DBforPostgreSQL/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/naplófájlok/olvasás | A PostgreSQL-naplófájlok listájának visszaadása. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/READ | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/validate/Action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnectionProxies/Write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/READ | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Action | Microsoft. DBforPostgreSQL/Servers/privateEndpointConnections/Write | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Action | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/logDefinitions/READ | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Action | Microsoft. DBforPostgreSQL/Servers/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Egy lekérdezés szövegének visszaadása |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/read | A lekérdezések listájához tartozó szövegek visszaküldése |
> | Action | Microsoft. DBforPostgreSQL/kiszolgálók/olvasás | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Action | Microsoft. DBforPostgreSQL/Servers/recoverableServers/READ | A helyreállítható PostgreSQL-kiszolgáló adatainak visszaadása |
> | Action | Microsoft.DBforPostgreSQL/servers/replicas/read | PostgreSQL-kiszolgáló olvasási replikáinak beolvasása |
> | Action | Microsoft.DBforPostgreSQL/servers/restart/action | Egy adott kiszolgáló újraindítása. |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Egy adott kiszolgálón konfigurált kiszolgálói veszélyforrások észlelési házirendjének részleteinek beolvasása |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Action | Microsoft. DBforPostgreSQL/Servers/topQueryStatistics/READ | A lekérdezési statisztikák listájának visszaadása a leggyakoribb lekérdezésekhez. |
> | Action | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Action | Microsoft. DBforPostgreSQL/Servers/waitStatistics/READ | Példányra vonatkozó várakozási idő statisztikája |
> | Action | Microsoft.DBforPostgreSQL/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |
> | Action | Microsoft. DBforPostgreSQL/serversv2/konfigurációk/olvasás | Visszaadja egy kiszolgáló konfigurációinak listáját, vagy lekéri a megadott konfiguráció tulajdonságait. |
> | Action | Microsoft. DBforPostgreSQL/serversv2/konfigurációk/írás | A megadott konfiguráció értékének frissítése |
> | Action | Microsoft.DBforPostgreSQL/serversv2/delete | Töröl egy meglévő kiszolgálót. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Töröl egy meglévő tűzfalszabály-szabályt. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Visszaadja egy kiszolgáló tűzfalszabályok listáját, vagy lekéri a megadott tűzfalszabály tulajdonságait. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Tűzfalszabály létrehozása a megadott paraméterekkel vagy egy meglévő szabály frissítése. |
> | Action | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforráshoz tartozó nem független beállítás beolvasása |
> | Action | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/logDefinitions/READ | A postgres-kiszolgálók számára elérhető naplók beolvasása |
> | Action | Microsoft. DBforPostgreSQL/serversv2/Providers/Microsoft. bepillantást/metricDefinitions/READ | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Action | Microsoft. DBforPostgreSQL/serversv2/READ | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | A megadott kiszolgálóhoz tartozó konfigurációk frissítése |
> | Action | Microsoft.DBforPostgreSQL/serversv2/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Devices/checkNameAvailability/Action | Ellenőrizze, hogy elérhető-e a IotHub neve |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Ellenőrizze, hogy elérhető-e a kiépítési szolgáltatás neve |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Tanúsítvány törlése |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | A tanúsítvány beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Tanúsítvány erőforrásának ellenőrzése |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Az IotHub-bérlő erőforrásának törlése |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | EventHub-fogyasztói csoport törlése |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | EventHub-fogyasztói csoport (ok) beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | EventHub-fogyasztói csoport létrehozása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Eszközök exportálása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | A IotHub bérlői statisztika erőforrásának beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Eszközök importálása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | A IotHub-bérlő kulcsának beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | A megadott IotHub tartozó feladatok (k) beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Lekéri a IotHub bérlői kulcsait |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Kvóta-metrikák beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Read | Az IotHub-bérlő erőforrásának beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Üzenet tesztelése az összes meglévő útvonalon |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Üzenet tesztelése egy megadott vizsgálati útvonalon |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Egy IotHub tartozó összes útválasztási végpont állapotának beolvasása |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Write | Az IotHub-bérlői erőforrás létrehozása vagy frissítése |
> | Action | Microsoft.Devices/ElasticPools/metricDefinitions/read | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Action | Microsoft.Devices/iotHubs/certificates/Delete | Tanúsítvány törlése |
> | Action | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Action | Microsoft.Devices/iotHubs/certificates/Read | A tanúsítvány beolvasása |
> | Action | Microsoft.Devices/iotHubs/certificates/verify/Action | Tanúsítvány erőforrásának ellenőrzése |
> | Action | Microsoft.Devices/iotHubs/certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Action | Microsoft.Devices/iotHubs/Delete | IotHub-erőforrás törlése |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.Devices/IotHubs/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Törli a Event Grid szűrőt |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Read | A Event Grid szűrő beolvasása |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Write | Új vagy meglévő Event Grid-szűrő létrehozása vagy frissítése |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | EventHub-fogyasztói csoport törlése |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | EventHub-fogyasztói csoport (ok) beolvasása |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | EventHub-fogyasztói csoport létrehozása |
> | Action | Microsoft.Devices/iotHubs/exportDevices/Action | Eszközök exportálása |
> | Action | Microsoft.Devices/iotHubs/importDevices/Action | Eszközök importálása |
> | Action | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | A megadott név IotHub-kulcsának beolvasása |
> | Action | Microsoft.Devices/iotHubs/iotHubStats/Read | IotHub statisztikájának beolvasása |
> | Action | Microsoft.Devices/iotHubs/jobs/Read | A megadott IotHub tartozó feladatok (k) beolvasása |
> | Action | Microsoft.Devices/iotHubs/listkeys/Action | Az összes IotHub kulcs lekérése |
> | Action | Microsoft.Devices/IotHubs/logDefinitions/read | A IotHub szolgáltatás elérhető naplózási definícióinak beolvasása |
> | Action | Microsoft.Devices/IotHubs/metricDefinitions/read | A IotHub szolgáltatás elérhető metrikáinak beolvasása |
> | Action | Microsoft.Devices/iotHubs/operationresults/Read | Művelet eredményének beolvasása (elavult API) |
> | Action | Microsoft.Devices/iotHubs/quotaMetrics/Read | Kvóta-metrikák beolvasása |
> | Action | Microsoft.Devices/iotHubs/Read | A IotHub-erőforrás (ok) beolvasása |
> | Action | Microsoft.Devices/iotHubs/routing/$testall/Action | Üzenet tesztelése az összes meglévő útvonalon |
> | Action | Microsoft.Devices/iotHubs/routing/$testnew/Action | Üzenet tesztelése egy megadott vizsgálati útvonalon |
> | Action | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Egy IotHub tartozó összes útválasztási végpont állapotának beolvasása |
> | Action | Microsoft.Devices/iotHubs/skus/Read | Érvényes IotHub-SKU beolvasása |
> | Action | Microsoft.Devices/iotHubs/Write | IotHub-erőforrás létrehozása vagy frissítése |
> | Action | Microsoft.Devices/locations/operationresults/Read | Hely alapú művelet eredményének beolvasása |
> | Action | Microsoft.Devices/operationresults/Read | Művelet eredményének beolvasása |
> | Action | Microsoft.Devices/operations/Read | Az összes ResourceProvider-művelet beolvasása |
> | Action | Microsoft.Devices/provisioningServices/certificates/Delete | Tanúsítvány törlése |
> | Action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Ellenőrző kód előállítása |
> | Action | Microsoft.Devices/provisioningServices/certificates/Read | A tanúsítvány beolvasása |
> | Action | Microsoft.Devices/provisioningServices/certificates/verify/Action | Tanúsítvány erőforrásának ellenőrzése |
> | Action | Microsoft.Devices/provisioningServices/certificates/Write | Tanúsítvány létrehozása vagy frissítése |
> | Action | Microsoft.Devices/provisioningServices/Delete | IotDps-erőforrás törlése |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Kulcsnév IotDps kulcsainak beolvasása |
> | Action | Microsoft.Devices/provisioningServices/listkeys/Action | Az összes IotDps kulcs lekérése |
> | Action | Microsoft.Devices/provisioningServices/logDefinitions/read | A kiépítési szolgáltatáshoz elérhető naplózási definíciók beolvasása |
> | Action | Microsoft.Devices/provisioningServices/metricDefinitions/read | A kiépítési szolgáltatáshoz elérhető metrikák beolvasása |
> | Action | Microsoft.Devices/provisioningServices/operationresults/Read | DPS-művelet eredményének beolvasása |
> | Action | Microsoft.Devices/provisioningServices/Read | IotDps-erőforrás beolvasása |
> | Action | Microsoft.Devices/provisioningServices/skus/Read | Érvényes IotDps-SKU beolvasása |
> | Action | Microsoft.Devices/provisioningServices/Write | IotDps-erőforrás létrehozása |
> | Action | Microsoft.Devices/register/action | Regisztrálja az előfizetést a IotHub erőforrás-szolgáltatóhoz, és lehetővé teszi a IotHub-erőforrások létrehozását |
> | Action | Microsoft.Devices/register/action | Regisztrálja az előfizetést a IotHub erőforrás-szolgáltatóhoz, és lehetővé teszi a IotHub-erőforrások létrehozását |
> | Action | Microsoft.Devices/usages/Read | A szolgáltató előfizetés-használati adatainak beolvasása. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DevSpaces/controllers/delete | Az Azure dev Spaces Controller és a adatsík Services törlése |
> | Action | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Az Azure dev Spaces vezérlő infrastruktúrájának kapcsolati adatainak listázása |
> | Action | Microsoft.DevSpaces/controllers/read | Az Azure dev Spaces-vezérlő tulajdonságainak olvasása |
> | Action | Microsoft.DevSpaces/controllers/write | Azure dev Spaces-vezérlő tulajdonságainak létrehozása vagy frissítése |
> | Action | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Tároló-gazdagép meglévő vezérlő-leképezésének megkeresése |
> | Action | Microsoft.DevSpaces/locations/operationresults/read | Aszinkron művelet állapotának olvasása |
> | Action | Microsoft.DevSpaces/register/action | A Microsoft dev Spaces erőforrás-szolgáltató regisztrálása előfizetéssel |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DevTestLab/labCenters/delete | Tesztkörnyezet-központok törlése. |
> | Action | Microsoft.DevTestLab/labCenters/read | Laboratóriumi központok olvasása. |
> | Action | Microsoft.DevTestLab/labCenters/write | Lab-központok hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Azure Resource Manager-sablonok beolvasása. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Létrehoz egy ARM-sablont az adott összetevőhöz, feltölti a szükséges fájlokat egy Storage-fiókba, és érvényesíti a generált összetevőt. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Összetevők olvasása. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/delete | Az összetevő-források törlése. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/read | Az összetevő-források olvasása. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/write | Összetevő-források hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Véletlenszerű, igényelhető virtuális gép igénylése a laborban. |
> | Action | Microsoft.DevTestLab/labs/costs/read | Olvasási költségek. |
> | Action | Microsoft.DevTestLab/labs/costs/write | Költségek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/CreateEnvironment/action | Virtuális gépek létrehozása tesztkörnyezetben. |
> | Action | Microsoft.DevTestLab/labs/customImages/delete | Egyéni lemezképek törlése. |
> | Action | Microsoft.DevTestLab/labs/customImages/read | Egyéni lemezképek olvasása. |
> | Action | Microsoft.DevTestLab/labs/customImages/write | Egyéni lemezképek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/delete | Labs törlése. |
> | Action | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Ellenőrizze, hogy az aktuális felhasználó rendelkezik-e érvényes profillal a laborban. |
> | Action | Microsoft.DevTestLab/labs/ExportResourceUsage/action | A tesztkörnyezet erőforrás-használatának exportálása egy Storage-fiókba |
> | Action | Microsoft.DevTestLab/labs/formulas/delete | Képletek törlése. |
> | Action | Microsoft.DevTestLab/labs/formulas/read | Képletek olvasása. |
> | Action | Microsoft.DevTestLab/labs/formulas/write | Képletek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/galleryImages/read | Katalógus képeinek olvasása. |
> | Action | Microsoft.DevTestLab/labs/GenerateUploadUri/action | URI létrehozása egyéni lemezképek egy laborba való feltöltéséhez. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/delete | Az üresjárati leállítások törlése. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/read | Inaktív leállítások olvasása. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/write | Üresjárati leállítások hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Virtuális gép importálása egy másik laborba. |
> | Action | Microsoft.DevTestLab/labs/ListVhds/action | Az egyéni lemezképek létrehozásához elérhető lemezképek listázása. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/delete | Notificationchannels törlése. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Értesítés küldése a megadott csatornának. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/read | Notificationchannels olvasása. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/write | Notificationchannels hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Kiértékeli a tesztkörnyezet házirendjét. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/delete | Szabályzatok törlése. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/read | Szabályzatok beolvasása. |
> | Action | Microsoft.DevTestLab/labs/policySets/policies/write | Házirendek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/policySets/read | Házirend-készletek olvasása. |
> | Action | Microsoft.DevTestLab/labs/read | A laborok beolvasása. |
> | Action | Microsoft.DevTestLab/labs/schedules/delete | Ütemtervek törlése. |
> | Action | Microsoft.DevTestLab/labs/schedules/Execute/action | Ütemezett végrehajtás. |
> | Action | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Az összes vonatkozó ütemterv listája |
> | Action | Microsoft.DevTestLab/labs/schedules/read | Ütemtervek olvasása. |
> | Action | Microsoft.DevTestLab/labs/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/delete | Szolgáltatásbeli futók törlése. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/read | Olvasási szolgáltatás futói |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/write | Szolgáltatásbeli futók hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/delete | Megosztott galériákat törölhet. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/read | Megosztott galériák olvasása. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Megosztott lemezképek törlése. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Megosztott lemezképek olvasása. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Megosztott lemezképek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/write | Megosztott galériákat adhat hozzá vagy módosíthat. |
> | Action | Microsoft.DevTestLab/labs/users/delete | Felhasználói profilok törlése. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Attach/action | Csatlakoztassa és hozza létre a lemez bérletét a virtuális géphez. |
> | Action | Microsoft.DevTestLab/labs/users/disks/delete | Lemezek törlése. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Detach/action | Válassza le és szüntesse meg a virtuális géphez csatolt lemez bérletét. |
> | Action | Microsoft.DevTestLab/labs/users/disks/read | Lemezek olvasása. |
> | Action | Microsoft.DevTestLab/labs/users/disks/write | Lemezek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/users/environments/delete | Környezetek törlése. |
> | Action | Microsoft.DevTestLab/labs/users/environments/read | Környezetek olvasása. |
> | Action | Microsoft.DevTestLab/labs/users/environments/write | Környezetek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/users/read | Felhasználói profilok olvasása. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/delete | Törölje a titkos kulcsokat. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/read | A titkok beolvasása. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/write | Titkos kódok hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Törölje a Service Fabrics szolgáltatást. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | A Service Fabric beolvasása. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Ütemtervek törlése. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Ütemezett végrehajtás. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Ütemtervek olvasása. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Indítsa el a Service fabricet. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Service Fabric leállítása |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Service fabricek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/users/write | Felhasználói profilok hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Új vagy meglévő adatlemez csatolása a virtuális géphez. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Összetevők alkalmazása a virtuális gépre. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Meglévő virtuális gép tulajdonjogának átvétele |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/delete | Virtuális gépek törlése. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Válassza le a megadott lemezt a virtuális gépről. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Egy olyan karakterlánc beolvasása, amely a virtuális gép RDP-fájljának tartalmát jelöli |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Felsorolja a megfelelő indítási/leállítási ütemterveket, ha vannak ilyenek. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/read | Virtuális gépek olvasása. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Virtuális gép újbóli üzembe helyezése |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Virtuális gép átméretezése. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Indítsa újra a virtuális gépet. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Ütemtervek törlése. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Ütemezett végrehajtás. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Ütemtervek olvasása. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Indítsa el a virtuális gépet. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Virtuális gép leállítása |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Átviszi a virtuális géphez csatolt összes adatlemezt az aktuális felhasználó tulajdonában. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Meglévő virtuális gép tulajdonjogának felszabadítása |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/write | Virtuális gépek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/delete | Virtuális hálózatok törlése. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/read | Virtuális hálózatok olvasása. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/write | Virtuális hálózatok hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/vmPools/delete | Virtuálisgép-készletek törlése. |
> | Action | Microsoft.DevTestLab/labs/vmPools/read | Virtuális gépek készletének olvasása. |
> | Action | Microsoft.DevTestLab/labs/vmPools/write | Virtuálisgép-készletek hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/labs/write | Labs hozzáadása vagy módosítása. |
> | Action | Microsoft.DevTestLab/locations/operations/read | Olvasási műveletek. |
> | Action | Microsoft.DevTestLab/register/action | Az előfizetés regisztrálása |
> | Action | Microsoft.DevTestLab/schedules/delete | Ütemtervek törlése. |
> | Action | Microsoft.DevTestLab/schedules/Execute/action | Ütemezett végrehajtás. |
> | Action | Microsoft.DevTestLab/schedules/read | Ütemtervek olvasása. |
> | Action | Microsoft.DevTestLab/schedules/Retarget/action | Frissíti az ütemterv cél erőforrás-azonosítóját. |
> | Action | Microsoft.DevTestLab/schedules/write | Ütemtervek hozzáadása vagy módosítása. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DocumentDB/databaseAccountNames/read | A név rendelkezésre állását ellenőrzi. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Gyűjtemény törlése. Csak API-típusokra alkalmazható: "mongodb". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "mongodb". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Beolvashat egy gyűjteményt, vagy listázhatja az összes gyűjteményt. Csak API-típusokra alkalmazható: "mongodb". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | A gyűjtemény teljesítményének beolvasása. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Egy gyűjtemény átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "mongodb". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Gyűjtemény létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "mongodb". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Tároló törlése. Csak API-típusokra alkalmazható: "SQL". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Olvassa el a tárolót, vagy sorolja fel az összes tárolót. Csak API-típusokra alkalmazható: "SQL". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | A tároló átviteli sebességének beolvasása. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Tároló átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "SQL". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Tároló létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "SQL". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Adatbázis törlése. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Gráf törlése. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Egy gráf beolvasása vagy az összes gráf listázása. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Diagram átviteli sebességének beolvasása. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Egy gráf átviteli sebességének frissítése. Csak az "gremlin'" API-típusokra alkalmazható. Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Gráf létrehozása vagy frissítése. Csak az "gremlin'" API-típusokra alkalmazható. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Adatbázis beolvasása vagy az összes adatbázis listázása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Olvassa el az adatbázis átviteli sebességét. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Adatbázis átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Hozzon létre egy adatbázist. Csak API-típusokra alkalmazható: "SQL", "mongodb", "gremlin'". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Szóköz törlése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | A szóköz beolvasása vagy az összes szóköz felsorolása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Lemezterület-átviteli sebesség beolvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Lemezterület-átviteli sebesség frissítése Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Tábla törlése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Az összes tábla beolvasása vagy listázása. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Olvassa el a tábla átviteli sebességét. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Egy tábla átviteli sebességének frissítése. Csak az API-típusokra vonatkozik: "Cassandra". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Tábla létrehozása vagy frissítése. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Hozzon létre egy lemezterületet. Csak az API-típusokra vonatkozik: "Cassandra". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Tábla törlése. Csak API-típusokra alkalmazható: "Table". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "Table". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Az összes tábla beolvasása vagy listázása. Csak API-típusokra alkalmazható: "Table". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Az aszinkron művelet állapotának olvasása. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Olvassa el a tábla átviteli sebességét. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Egy tábla átviteli sebességének frissítése. Csak API-típusokra alkalmazható: "Table". Csak a következő beállítási típusok esetében alkalmazható: "átviteli sebesség". |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Tábla létrehozása vagy frissítése. Csak API-típusokra alkalmazható: "Table". |
> | Action | Microsoft.DocumentDB/databaseAccounts/backup/action | A biztonsági mentés konfigurálására vonatkozó kérelem küldése |
> | Action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Adatbázis-fiók erőforráscsoportának módosítása |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Beolvassa a gyűjtemény metrikájának definícióit. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | A gyűjtemény metrikáinak beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Adatbázis-fiók partíciós kulcs szintű metrikáinak olvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Adatbázis-fiók partíciós szintjének metrikáinak olvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Adatbázis-fiók partícióinak beolvasása gyűjteményben |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Adatbázis-fiók partíciós szintjének használatának elolvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Beolvassa a gyűjtemény használati adatait. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Az adatbázis metrika-definícióinak beolvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Az adatbázis-metrikák beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Az adatbázis-használat beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/delete | Törli az adatbázis-fiókokat. |
> | Action | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Egy adatbázis-fiók régiói feladatátvételi prioritásainak módosítása. Ez a manuális feladatátvételi művelet végrehajtásához használatos. |
> | Action | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/művelet | Az adatbázis-fiók biztonsági mentési szabályzatának beolvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Adatbázis-fiókhoz tartozó kapcsolatok karakterláncának beolvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Adatbázis-fiók kulcsainak listázása |
> | Action | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Az adatbázis-fiók metrikáinak definícióinak beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/metrics/read | Az adatbázis-fiók metrikáinak beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline egy adatbázis-fiók régiója.  |
> | Action | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Egy adatbázis-fiókhoz tartozó régió online. |
> | Action | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Az aszinkron művelet olvasási állapota |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Olvasási késési metrikák |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/read | Replikációs késések százalékos értékének olvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Adott forrás-és célobjektum késési metrikáinak olvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Adott célcsoport késési metrikáinak olvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Az adatbázis-fiókhoz tartozó privát végponti kapcsolatok proxyjának törlése |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Az adatbázis-fiók privát végpont-ügyfélkapcsolati proxyjának beolvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Az adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok proxyjának ellenőrzése |
> | Action | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Az adatbázis-fiókhoz tartozó magánhálózati végponti kapcsolatok proxyjának létrehozása vagy frissítése |
> | Action | Microsoft.DocumentDB/databaseAccounts/read | Egy adatbázis-fiók beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Az adatbázis-fiók írásvédett kulcsának beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Adatbázis-fiók kulcsainak elforgatása |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | A regionális gyűjtemény metrikáinak beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Regionális adatbázis-fiók partíciós kulcs szintű metrikáinak olvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | A regionális adatbázis-fiók partíciós szintjén lévő metrikák olvasása |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | A regionális adatbázis-fiók partícióinak beolvasása egy gyűjteményben |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | A régió és az adatbázis-fiók metrikáinak beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/restore/action | Visszaállítási kérelem elküldése |
> | Action | Microsoft.DocumentDB/databaseAccounts/usages/read | Az adatbázis-fiók használatának beolvasása. |
> | Action | Microsoft.DocumentDB/databaseAccounts/write | Adatbázis-fiókok frissítése. |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Értesíti a Microsoft. DocumentDB, hogy a VirtualNetwork vagy az alhálózat törölve lett |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | DeleteVirtualNetworkOrSubnets aszinkron művelet olvasási állapota |
> | Action | Microsoft.DocumentDB/locations/operationsStatus/read | Aszinkron műveletek állapotának olvasása |
> | Action | Microsoft.DocumentDB/operationResults/read | Az aszinkron művelet olvasási állapota |
> | Action | Microsoft.DocumentDB/operations/read | A Microsoft DocumentDB elérhető olvasási műveletek  |
> | Action | Microsoft.DocumentDB/register/action |  Regisztrálja a Microsoft DocumentDB erőforrás-szolgáltatót az előfizetéshez |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.DomainRegistration/checkDomainAvailability/Action | Ellenőrizze, hogy van-e elérhető tartomány a vásárláshoz |
> | Action | Microsoft.DomainRegistration/domains/Delete | Meglévő tartomány törlése. |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Tulajdonos azonosítójának törlése |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Tulajdonosi azonosítók listázása |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Tulajdonos azonosítójának beolvasása |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Azonosító létrehozása vagy frissítése |
> | Action | Microsoft.DomainRegistration/domains/operationresults/Read | Tartományi művelet beolvasása |
> | Action | Microsoft.DomainRegistration/domains/Read | Tartományok listájának beolvasása |
> | Action | Microsoft.DomainRegistration/domains/Read | Get domain |
> | Action | Microsoft.DomainRegistration/domains/renew/Action | Meglévő tartomány megújítása. |
> | Action | Microsoft.DomainRegistration/domains/Write | Új tartomány hozzáadása vagy meglévő frissítése |
> | Action | Microsoft.DomainRegistration/generateSsoRequest/Action | Kérelem létrehozása a tartomány-ellenőrzési központba való bejelentkezéshez. |
> | Action | Microsoft.DomainRegistration/listDomainRecommendations/Action | A listához tartozó javaslatok lekérdezése kulcsszavak alapján |
> | Action | Microsoft.DomainRegistration/operations/Read | Az App Service-tartomány regisztrációjának összes műveletének listázása |
> | Action | Microsoft.DomainRegistration/register/action | Regisztrálja a Microsoft Domains erőforrás-szolgáltatót az előfizetéshez |
> | Action | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Szerződés listázása művelet |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | TopLevel-tartományok beolvasása |
> | Action | Microsoft.DomainRegistration/topLevelDomains/Read | TopLevel-tartomány beolvasása |
> | Action | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Tartományi vásárlási objektum ellenőrzése a küldés nélkül |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.EventGrid/domains/delete | Tartomány törlése |
> | Action | Microsoft.EventGrid/domains/listKeys/action | Tartomány kulcsainak listázása |
> | Action | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | A tartományok elérhető metrikáinak beolvasása |
> | Action | Microsoft.EventGrid/domains/read | Tartomány beolvasása |
> | Action | Microsoft.EventGrid/domains/regenerateKey/action | Tartomány kulcsának újralétrehozása |
> | Action | Microsoft.EventGrid/domains/topics/read | Tartományi témakör beolvasása |
> | Action | Microsoft.EventGrid/domains/write | Tartomány létrehozása vagy frissítése |
> | Action | Microsoft.EventGrid/eventSubscriptions/delete | EventSubscription törlése |
> | Action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Az esemény-előfizetés teljes URL-címének beolvasása |
> | Action | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Az esemény-előfizetések diagnosztikai beállításának beolvasása |
> | Action | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az esemény-előfizetések diagnosztikai beállításának létrehozása vagy frissítése |
> | Action | Microsoft. EventGrid/eventSubscriptions/Providers/Microsoft. bepillantást/metricDefinitions/READ | A eventSubscriptions elérhető metrikáinak beolvasása |
> | Action | Microsoft.EventGrid/eventSubscriptions/read | EventSubscription beolvasása |
> | Action | Microsoft. EventGrid/eventSubscriptions/Write | EventSubscription létrehozása vagy frissítése |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | A témakörök diagnosztikai beállításának beolvasása |
> | Action | Microsoft. EventGrid/extensionTopics/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | A témakörökhöz elérhető metrikák beolvasása |
> | Action | Microsoft.EventGrid/extensionTopics/read | ExtensionTopic beolvasása. |
> | Action | Microsoft.EventGrid/locations/eventSubscriptions/read | Regionális esemény-előfizetések listázása |
> | Action | Microsoft.EventGrid/locations/operationResults/read | Regionális művelet eredményének beolvasása |
> | Action | Microsoft.EventGrid/locations/operationsStatus/read | Regionális művelet állapotának beolvasása |
> | Action | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Regionális esemény-előfizetések listázása topictype szerint |
> | Action | Microsoft.EventGrid/operationResults/read | Művelet eredményének beolvasása |
> | Action | Microsoft.EventGrid/operations/read | EventGrid-műveletek listázása. |
> | Action | Microsoft.EventGrid/operationsStatus/read | Művelet állapotának beolvasása |
> | Action | Microsoft.EventGrid/register/action | Regisztrálja az előfizetést a EventGrid erőforrás-szolgáltatóhoz. |
> | Action | Microsoft.EventGrid/topics/delete | Témakör törlése |
> | Action | Microsoft.EventGrid/topics/listKeys/action | Témakör kulcsainak listázása |
> | Action | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | A témakörök diagnosztikai beállításának beolvasása |
> | Action | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | A témakörök diagnosztikai beállításának létrehozása vagy frissítése |
> | Action | Microsoft. EventGrid/témakörök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A témakörökhöz elérhető metrikák beolvasása |
> | Action | Microsoft.EventGrid/topics/read | Témakör beolvasása |
> | Action | Microsoft.EventGrid/topics/regenerateKey/action | Témakör kulcsának újralétrehozása |
> | Action | Microsoft. EventGrid/témakörök/írás | Témakör létrehozása vagy frissítése |
> | Action | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Globális esemény-előfizetések listázása a témakör típusa szerint |
> | Action | Microsoft.EventGrid/topictypes/eventtypes/read | Topictype által támogatott eventtypes olvasása |
> | Action | Microsoft.EventGrid/topictypes/read | Topictype beolvasása |
> | Action | Microsoft.EventGrid/unregister/action | A EventGrid erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.EventHub/availableClusterRegions/read | Olvasási művelet az elérhető előre kiépített fürtök Azure-régió általi listázásához. |
> | Action | Microsoft.EventHub/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Action | Microsoft.EventHub/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Action | Microsoft.EventHub/clusters/delete | Töröl egy meglévő fürterőforrás-erőforrást. |
> | Action | Microsoft.EventHub/clusters/namespaces/read | A fürtben található névterekhez tartozó névtér-azonosítók listázása. |
> | Action | Microsoft.EventHub/clusters/operationresults/read | Aszinkron fürt művelet állapotának beolvasása. |
> | Action | Microsoft. EventHub/fürtök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A fürt metrikáinak erőforrásaihoz tartozó leírások listájának beolvasása |
> | Action | Microsoft.EventHub/clusters/read | A fürterőforrás leírásának beolvasása |
> | Action | Microsoft.EventHub/clusters/write | Létrehoz vagy módosít egy meglévő fürterőforrás-erőforrást. |
> | Action | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | A megadott virtuális hálózat EventHub erőforrás-szolgáltatójában található virtuális hálózati szabályok törlése |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/action | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/write | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Action | Microsoft.EventHub/namespaces/Delete | Egy névtérerőforrás törlése |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Action | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Action | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/művelet | A EventHub frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | EventHub-engedélyezési szabályok törlésének művelete |
> | Action | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A EventHub tartozó kapcsolódási karakterlánc beolvasása |
> | Action | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/READ |  A EventHub-engedélyezési szabályok listájának beolvasása |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok/Write | Hozzon létre EventHub engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | ConsumerGroup-erőforrás törlésére szolgáló művelet |
> | Action | Microsoft. EventHub/névterek/eventHubs/consumergroups/READ | ConsumerGroup-erőforrás leírásának beolvasása |
> | Action | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | ConsumerGroup tulajdonságainak létrehozása vagy frissítése. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/Delete | EventHub-erőforrás törlésére szolgáló művelet |
> | Action | Microsoft.EventHub/namespaces/eventhubs/read | EventHub-erőforrás leírásának beolvasása |
> | Action | Microsoft.EventHub/namespaces/eventhubs/write | EventHub tulajdonságainak létrehozása vagy frissítése. |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/delete | IP-szűrő erőforrás törlése |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/read | IP-szűrő erőforrás beolvasása |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/write | IP-szűrő erőforrás létrehozása |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Üzenetek fogadása |
> | DataAction | Microsoft. EventHub/névterek/üzenetek/küldés/művelet | Üzenetek küldése |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/read | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/write | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft. EventHub/névterek/networkruleset/delete | Virtuális hálózati szabály erőforrás törlése |
> | Action | Microsoft. EventHub/névterek/networkruleset/olvasás | Gets NetworkRuleSet Resource |
> | Action | Microsoft. EventHub/névterek/networkruleset/írás | Virtuális hálózati szabály erőforrás létrehozása |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/delete | Virtuális hálózati szabály erőforrás törlése |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/write | Virtuális hálózati szabály erőforrás létrehozása |
> | Action | Microsoft.EventHub/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Action | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Action | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Action | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Action | Microsoft. EventHub/névterek/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Action | Microsoft.EventHub/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Action | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Virtuális hálózati szabály erőforrás törlése |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Virtuális hálózati szabály erőforrás lekérése |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Virtuális hálózati szabály erőforrás létrehozása |
> | Action | Microsoft. EventHub/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Action | Microsoft.EventHub/operations/read | Műveletek lekérése |
> | Action | Microsoft.EventHub/register/action | Az EventHub erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása és az EventHub-erőforrások létrehozásának engedélyezése |
> | Action | Microsoft.EventHub/sku/read | SKU-erőforrások leírásának beolvasása |
> | Action | Microsoft.EventHub/sku/regions/read | SkuRegions-erőforrás leírásának beolvasása |
> | Action | Microsoft.EventHub/unregister/action | Az EventHub erőforrás-szolgáltató regisztrálása |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Features/features/read | Beolvassa az előfizetéshez tartozó szolgáltatásokat. |
> | Action | Microsoft. features/Operations/READ | A műveletek listájának beolvasása. |
> | Action | Microsoft. features/Providers/features/READ | Beolvassa az előfizetés szolgáltatásait az adott erőforrás-szolgáltatón. |
> | Action | Microsoft. features/Providers/funkciók/regisztráció/művelet | Regisztrálja az előfizetéshez tartozó szolgáltatást az adott erőforrás-szolgáltatón. |
> | Action | Microsoft.Features/providers/features/unregister/action | Az előfizetéshez tartozó szolgáltatás regisztrációjának törlése az adott erőforrás-szolgáltatón. |
> | Action | Microsoft.Features/register/action | Az előfizetésben elérhető funkció regisztrálása. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Vendég-konfiguráció hozzárendelésének törlése. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Vendég-konfiguráció hozzárendelésének beolvasása. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | A vendég-konfiguráció hozzárendelési jelentésének beolvasása. |
> | Action | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Új vendég-konfiguráció hozzárendelésének létrehozása. |
> | Action | Microsoft.GuestConfiguration/register/action | Regisztrálja az előfizetést a Microsoft. GuestConfiguration erőforrás-szolgáltatóhoz. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.HDInsight/clusters/applications/delete | HDInsight-fürt alkalmazásának törlése |
> | Action | Microsoft. HDInsight/fürtök/alkalmazások/olvasás | HDInsight-fürt alkalmazásának beolvasása |
> | Action | Microsoft. HDInsight/fürtök/alkalmazások/írás | Alkalmazás létrehozása vagy frissítése a HDInsight-fürthöz |
> | Action | Microsoft. HDInsight/fürtök/changerdpsetting/művelet | HDInsight-fürt RDP-beállításainak módosítása |
> | Action | Microsoft. HDInsight/fürtök/konfigurációk/művelet | HDInsight-fürt konfigurációjának frissítése |
> | Action | Microsoft. HDInsight/fürtök/konfigurációk/művelet | HDInsight-fürt konfigurációjának beolvasása |
> | Action | Microsoft. HDInsight/fürtök/konfigurációk/olvasás | HDInsight-fürt konfigurációjának beolvasása |
> | Action | Microsoft.HDInsight/clusters/delete | HDInsight-fürt törlése |
> | Action | Microsoft.HDInsight/clusters/getGatewaySettings/action | Átjáró beállításainak beolvasása a HDInsight-fürthöz |
> | Action | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Az erőforrás-HDInsight fürt diagnosztikai beállításának beolvasása |
> | Action | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás-HDInsight fürt diagnosztikai beállításának létrehozása vagy frissítése |
> | Action | Microsoft. HDInsight/fürtök/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | A HDInsight-fürthöz elérhető metrikák beolvasása |
> | Action | Microsoft. HDInsight/fürtök/olvasás | A HDInsight-fürt részleteinek beolvasása |
> | Action | Microsoft. HDInsight/fürtök/szerepkörök/átméretezés/művelet | HDInsight-fürt átméretezése |
> | Action | Microsoft.HDInsight/clusters/updateGatewaySettings/action | HDInsight-fürthöz tartozó átjáró beállításainak frissítése |
> | Action | Microsoft. HDInsight/fürtök/írás | HDInsight-fürt létrehozása vagy frissítése |
> | Action | Microsoft. HDInsight/Locations/képességek/olvasás | Előfizetési lehetőségek beszerzése |
> | Action | Microsoft. HDInsight/Locations/checkNameAvailability/READ | Név foglaltságának ellenőrzése |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. ImportExport/feladatok/törlés | Töröl egy meglévő feladatot. |
> | Action | Microsoft. ImportExport/Jobs/listBitLockerKeys/művelet | A megadott feladatokhoz tartozó BitLocker-kulcsok beolvasása. |
> | Action | Microsoft. ImportExport/feladatok/olvasás | Lekéri a megadott feladat tulajdonságait, vagy visszaadja a feladatok listáját. |
> | Action | Microsoft. ImportExport/feladatok/írás | Létrehoz egy feladatot a megadott paraméterekkel, vagy frissíti a megadott feladatokhoz tartozó tulajdonságokat vagy címkéket. |
> | Action | Microsoft. ImportExport/Locations/READ | Lekéri a megadott hely tulajdonságait, vagy visszaadja a helyek listáját. |
> | Action | Microsoft. ImportExport/regisztráció/művelet | Regisztrálja az előfizetést az importálási/exportálási erőforrás-szolgáltató számára, és lehetővé teszi az importálási/exportálási feladatok létrehozását. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Insights/ActionGroups/Delete | Műveletcsoport törlése |
> | Action | Microsoft. bepillantások/ActionGroups/olvasás | Műveletcsoport olvasása |
> | Action | Microsoft.Insights/ActionGroups/Write | Műveletcsoport létrehozása vagy frissítése |
> | Action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Tevékenységnapló-riasztás aktiválva |
> | Action | Microsoft.Insights/ActivityLogAlerts/Delete | Tevékenységnapló-alapú riasztás törlése |
> | Action | Microsoft. bepillantások/ActivityLogAlerts/olvasás | Tevékenységnapló-alapú riasztás olvasása |
> | Action | Microsoft.Insights/ActivityLogAlerts/Write | Tevékenységnapló-alapú riasztás létrehozása vagy frissítése |
> | Action | Microsoft.Insights/AlertRules/Activated/Action | Klasszikus metrikaalapú riasztás aktiválva |
> | Action | Microsoft.Insights/AlertRules/Delete | Klasszikus metrikaalapú riasztás törlése |
> | Action | Microsoft.Insights/AlertRules/Incidents/Read | Klasszikus metrikaalapú riasztási eset olvasása |
> | Action | Microsoft.Insights/AlertRules/Read | Klasszikus metrikaalapú riasztás olvasása |
> | Action | Microsoft.Insights/AlertRules/Resolved/Action | Klasszikus metrikaalapú riasztás megoldva |
> | Action | Microsoft.Insights/AlertRules/Throttled/Action | Klasszikus metrikaalapú riasztási szabály elindítva |
> | Action | Microsoft.Insights/AlertRules/Write | Klasszikus metrikaalapú riasztás létrehozása vagy frissítése |
> | Action | Microsoft.Insights/AutoscaleSettings/Delete | Automatikus méretezési beállítás törlése |
> | Action | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Erőforrás diagnosztikai beállításának olvasása |
> | Action | Microsoft. bepillantást/AutoscaleSettings/Providers/Microsoft. bepillantást/diagnosticSettings/Write | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Action | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/logDefinitions/READ | A naplódefiníciók olvasása |
> | Action | Microsoft. inviews/AutoscaleSettings/Providers/Microsoft. bepillantást/MetricDefinitions/READ | A metrikadefiníciók olvasása |
> | Action | Microsoft.Insights/AutoscaleSettings/Read | Automatikus méretezési beállítás olvasása |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Automatikus vertikális leskálázás inicializálva |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Automatikus vertikális leskálázás befejezve |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Automatikus vertikális felskálázás inicializálva |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Automatikus vertikális felskálázás befejezve |
> | Action | Microsoft.Insights/AutoscaleSettings/Write | Automatikus méretezési beállítás létrehozása vagy frissítése |
> | Action | Microsoft. bepillantások/alapkonfiguráció/olvasás | Metrika alapkonfigurációjának olvasása (előzetes verzió) |
> | Action | Microsoft.Insights/CalculateBaseline/Read | Metrikaértékek alapkonfigurációjának kiszámítása (előzetes verzió) |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Delete | Egy Application Insights-elemzési elem törlése |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Read | Egy Application Insights-elemzési elem olvasása |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Write | Egy Application Insights-elemzési elem írása |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Action | Az Application Insights-elemzési táblájának művelete |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Delete | Egy Application Insights-elemzési tábla sémájának törlése |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Read | Egy Application Insights-elemzési tábla sémájának olvasása |
> | Action | Microsoft.Insights/Components/AnalyticsTables/Write | Egy Application Insights-elemzési tábla sémájának írása |
> | Action | Microsoft.Insights/Components/Annotations/Delete | Egy Application Insights-jegyzet törlése |
> | Action | Microsoft.Insights/Components/Annotations/Read | Egy Application Insights-jegyzet olvasása |
> | Action | Microsoft.Insights/Components/Annotations/Write | Egy Application Insights-jegyzet írása |
> | Action | Microsoft.Insights/Components/Api/Read | Application Insights-összetevőadatok API-jának olvasása |
> | Action | Microsoft.Insights/Components/ApiKeys/Action | Egy Application Insights-beli API-kulcs létrehozása |
> | Action | Microsoft.Insights/Components/ApiKeys/Delete | Egy Application Insights-beli API-kulcs törlése |
> | Action | Microsoft.Insights/Components/ApiKeys/Read | Egy Application Insights-beli API-kulcs olvasása |
> | Action | Microsoft.Insights/Components/BillingPlanForComponent/Read | Egy Application Insights-összetevő számlázási csomagjának olvasása |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Application Insights-összetevő aktuális számlázási funkcióinak olvasása |
> | Action | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Application Insights-összetevő aktuális számlázási funkcióinak írása |
> | Action | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Az Application Insights egy alapértelmezett ALM-integrációja konfigurációjának olvasása |
> | Action | Microsoft.Insights/Components/Delete | Application Insights-összetevő konfigurációjának törlése |
> | Action | Microsoft.Insights/Components/Events/Read | Naplók beolvasása az Application Insightsból OData-lekérdezési formátumban |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Action | Az Application Insights exportálási beállításainak művelete |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Delete | Az Application Insights exportálási beállításainak törlése |
> | Action | Microsoft. bepillantások/összetevők/ExportConfiguration/olvasás | Az Application Insights exportálási beállításainak olvasása |
> | Action | Microsoft.Insights/Components/ExportConfiguration/Write | Az Application Insights exportálási beállításainak írása |
> | Action | Microsoft.Insights/Components/ExtendQueries/Read | Application Insights-összetevő bővített lekérdezési eredményeinek olvasása |
> | Action | Microsoft.Insights/Components/Favorites/Delete | Egy Application Insights-kedvenc törlése |
> | Action | Microsoft.Insights/Components/Favorites/Read | Egy Application Insights-kedvenc olvasása |
> | Action | Microsoft.Insights/Components/Favorites/Write | Egy Application Insights-kedvenc írása |
> | Action | Microsoft.Insights/Components/FeatureCapabilities/Read | Application Insights-összetevő funkciói lehetőségeinek olvasása |
> | Action | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Application Insights-összetevő elérhető számlázási funkcióinak olvasása |
> | Action | Microsoft. bepillantások/összetevők/GetToken/olvasás | Egy Application Insights-összetevő jogkivonatának olvasása |
> | Action | Microsoft.Insights/Components/MetricDefinitions/Read | Application Insights-összetevő metrikadefinícióinak olvasása |
> | Action | Microsoft. bepillantások/összetevők/mérőszámok/olvasás | Application Insights-összetevő metrikáinak olvasása |
> | Action | Microsoft.Insights/Components/Move/Action | Application Insights-összetevő áthelyezése másik erőforráscsoportba vagy előfizetésbe |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Egy személyes Application Insights-elemzési elem törlése |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Read | Egy személyes Application Insights-elemzési elem olvasása |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Write | Egy személyes Application Insights-elemzési elem írása |
> | Action | Microsoft. bepillantások/összetevők/MyFavorites/olvasás | Egy személyes Application Insights-kedvenc olvasása |
> | Action | Microsoft.Insights/Components/Operations/Read | Az Application Insightsban hosszú ideig futó műveletek állapotának beolvasása |
> | Action | Microsoft.Insights/Components/PricingPlans/Read | Egy Application Insights-összetevő tarifacsomagjának olvasása |
> | Action | Microsoft.Insights/Components/PricingPlans/Write | Egy Application Insights-összetevő tarifacsomagjának írása |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Az Application Insights Proaktív problémaészlelés konfigurációjának olvasása |
> | Action | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Az Application Insights Proaktív problémaészlelés konfigurációjának írása |
> | Action | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Action | Microsoft.Insights/Components/Purge/Action | Adatok végleges törlése az Application Insightsból |
> | Action | Microsoft.Insights/Components/Query/Read | Lekérdezések futtatása Application Insights-naplókon |
> | Action | Microsoft.Insights/Components/QuotaStatus/Read | Application Insights-összetevő kvótaállapotának olvasása |
> | Action | Microsoft.Insights/Components/Read | Application Insights-összetevő konfigurációjának olvasása |
> | Action | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Application Insights-webtesztek helyének olvasása |
> | Action | Microsoft. bepillantások/összetevők/webtesztek/olvasás | Webteszt-konfiguráció olvasása |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Delete | Az Application Insights egy ALM integrációja konfigurációjának törlése |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Read | Az Application Insights egy ALM-integrációja konfigurációjának olvasása |
> | Action | Microsoft.Insights/Components/WorkItemConfigs/Write | Az Application Insights egy ALM-integrációja konfigurációjának írása |
> | Action | Microsoft.Insights/Components/Write | Application Insights-összetevő konfigurációjának írása |
> | Action | Microsoft.Insights/DiagnosticSettings/Delete | Erőforrás diagnosztikai beállításának törlése |
> | Action | Microsoft. bepillantások/DiagnosticSettings/olvasás | Erőforrás diagnosztikai beállításának olvasása |
> | Action | Microsoft. bepillantások/DiagnosticSettings/írás | Erőforrás diagnosztikai beállításának létrehozása vagy frissítése |
> | Action | Microsoft.Insights/EventCategories/Read | Tevékenységnaplóbeli eseménykategóriák olvasása |
> | Action | Microsoft.Insights/eventtypes/digestevents/Read | A felügyeleti eseménytípus kivonatának olvasása |
> | Action | Microsoft.Insights/eventtypes/values/Read | Tevékenységnaplóbeli események olvasása |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Hálózati forgalomfolyam-napló diagnosztikai beállításának törlése |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Hálózati forgalomfolyam-napló diagnosztikai beállításának olvasása |
> | Action | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Hálózati forgalomfolyam-napló diagnosztikai beállításának létrehozása vagy frissítése |
> | Action | Microsoft.Insights/ListMigrationDate/Action | Előfizetés migrálási dátumának visszaolvasása |
> | Action | Microsoft.Insights/ListMigrationDate/Read | Előfizetés migrálási dátumának visszaolvasása |
> | Action | Microsoft.Insights/LogDefinitions/Read | A naplódefiníciók olvasása |
> | Action | Microsoft.Insights/LogProfiles/Delete | Tevékenységnapló naplóprofiljának törlése |
> | Action | Microsoft.Insights/LogProfiles/Read | Tevékenységnapló naplóprofiljának olvasása |
> | Action | Microsoft.Insights/LogProfiles/Write | Tevékenységnapló naplóprofiljának létrehozása vagy frissítése |
> | Action | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Adatok olvasása az ADAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/ADReplicationResult/Read | Adatok olvasása az ADReplicationResult táblából |
> | Action | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Adatok olvasása az ADSecurityAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/Alert/Read | Adatok olvasása az Alert táblából |
> | Action | Microsoft.Insights/Logs/AlertHistory/Read | Adatok olvasása az AlertHistory táblából |
> | Action | Microsoft.Insights/Logs/ApplicationInsights/Read | Adatok olvasása az ApplicationInsights táblából |
> | Action | Microsoft.Insights/Logs/AzureActivity/Read | Adatok olvasása az AzureActivity táblából |
> | Action | Microsoft.Insights/Logs/AzureMetrics/Read | Adatok olvasása az AzureMetrics táblából |
> | Action | Microsoft.Insights/Logs/BoundPort/Read | Adatok olvasása a BoundPort táblából |
> | Action | Microsoft.Insights/Logs/CommonSecurityLog/Read | Adatok olvasása a CommonSecurityLog táblából |
> | Action | Microsoft. bepillantások/naplók/ComputerGroup/olvasás | Adatok olvasása a ComputerGroup táblából |
> | Action | Microsoft. bepillantások/naplók/konfigurációváltozás/olvasás | Adatok olvasása a ConfigurationChange táblából |
> | Action | Microsoft. bepillantások/naplók/ConfigurationData/olvasás | Adatok olvasása a ConfigurationData táblából |
> | Action | Microsoft. bepillantások/naplók/ContainerImageInventory/olvasás | Adatok olvasása a ContainerImageInventory táblából |
> | Action | Microsoft. bepillantások/naplók/ContainerInventory/olvasás | Adatok olvasása a ContainerInventory táblából |
> | Action | Microsoft.Insights/Logs/ContainerLog/Read | Adatok olvasása a ContainerLog táblából |
> | Action | Microsoft.Insights/Logs/ContainerServiceLog/Read | Adatok olvasása a ContainerServiceLog táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceAppCrash/olvasás | Adatok olvasása a DeviceAppCrash táblából |
> | Action | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Adatok olvasása a DeviceAppLaunch táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceCalendar/olvasás | Adatok olvasása a DeviceCalendar táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceCleanup/olvasás | Adatok olvasása a DeviceCleanup táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceConnectSession/olvasás | Adatok olvasása a DeviceConnectSession táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceEtw/olvasás | Adatok olvasása a DeviceEtw táblából |
> | Action | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Adatok olvasása a DeviceHardwareHealth táblából |
> | Action | Microsoft.Insights/Logs/DeviceHealth/Read | Adatok olvasása a DeviceHealth táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceHeartbeat/olvasás | Adatok olvasása a DeviceHeartbeat táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceSkypeHeartbeat/olvasás | Adatok olvasása a DeviceSkypeHeartbeat táblából |
> | Action | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Adatok olvasása a DeviceSkypeSignIn táblából |
> | Action | Microsoft. bepillantások/naplók/DeviceSleepState/olvasás | Adatok olvasása a DeviceSleepState táblából |
> | Action | Microsoft. bepillantások/naplók/DHAppFailure/olvasás | Adatok olvasása a DHAppFailure táblából |
> | Action | Microsoft. bepillantások/naplók/DHAppReliability/olvasás | Adatok olvasása a DHAppReliability táblából |
> | Action | Microsoft.Insights/Logs/DHDriverReliability/Read | Adatok olvasása a DHDriverReliability táblából |
> | Action | Microsoft. bepillantások/naplók/DHLogonFailures/olvasás | Adatok olvasása a DHLogonFailures táblából |
> | Action | Microsoft. bepillantások/naplók/DHLogonMetrics/olvasás | Adatok olvasása a DHLogonMetrics táblából |
> | Action | Microsoft.Insights/Logs/DHOSCrashData/Read | Adatok olvasása a DHOSCrashData táblából |
> | Action | Microsoft. bepillantások/naplók/DHOSReliability/olvasás | Adatok olvasása a DHOSReliability táblából |
> | Action | Microsoft. bepillantások/naplók/DHWipAppLearning/olvasás | Adatok olvasása a DHWipAppLearning táblából |
> | Action | Microsoft.Insights/Logs/DnsEvents/Read | Adatok olvasása a DnsEvents táblából |
> | Action | Microsoft. bepillantások/naplók/DnsInventory/olvasás | Adatok olvasása a DnsInventory táblából |
> | Action | Microsoft. bepillantások/naplók/ETWEvent/olvasás | Adatok olvasása az ETWEvent táblából |
> | Action | Microsoft. elemzések/naplók/esemény/olvasás | Adatok olvasása az Event táblából |
> | Action | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Adatok olvasása az ExchangeAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Adatok olvasása az ExchangeOnlineAssessmentRecommendation táblából |
> | Action | Microsoft. bepillantások/naplók/szívverés/olvasás | Adatok olvasása a Heartbeat táblából |
> | Action | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Adatok olvasása az IISAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/InboundConnection/Read | Adatok olvasása az InboundConnection táblából |
> | Action | Microsoft.Insights/Logs/KubeNodeInventory/Read | Adatok olvasása a KubeNodeInventory táblából |
> | Action | Microsoft.Insights/Logs/KubePodInventory/Read | Adatok olvasása a KubePodInventory táblából |
> | Action | Microsoft. bepillantások/naplók/LinuxAuditLog/olvasás | Adatok olvasása a LinuxAuditLog táblából |
> | Action | Microsoft. bepillantások/naplók/MAApplication/olvasás | Adatok olvasása az MAApplication táblából |
> | Action | Microsoft. bepillantások/naplók/MAApplicationHealth/olvasás | Adatok olvasása az MAApplicationHealth táblából |
> | Action | Microsoft. bepillantások/naplók/MAApplicationHealthAlternativeVersions/olvasás | Adatok olvasása az MAApplicationHealthAlternativeVersions táblából |
> | Action | Microsoft. bepillantások/naplók/MAApplicationHealthIssues/olvasás | Adatok olvasása az MAApplicationHealthIssues táblából |
> | Action | Microsoft. bepillantások/naplók/MAApplicationInstance/olvasás | Adatok olvasása az MAApplicationInstance táblából |
> | Action | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Adatok olvasása az MAApplicationInstanceReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MAApplicationReadiness/olvasás | Adatok olvasása az MAApplicationReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MADeploymentPlan/olvasás | Adatok olvasása az MADeploymentPlan táblából |
> | Action | Microsoft. bepillantások/naplók/MADevice/olvasás | Adatok olvasása az MADevice táblából |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Adatok olvasása az MADevicePnPHealth táblából |
> | Action | Microsoft. bepillantások/naplók/MADevicePnPHealthAlternativeVersions/olvasás | Adatok olvasása az MADevicePnPHealthAlternativeVersions táblából |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Adatok olvasása az MADevicePnPHealthIssues táblából |
> | Action | Microsoft. bepillantások/naplók/MADeviceReadiness/olvasás | Adatok olvasása az MADeviceReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MADriverInstanceReadiness/olvasás | Adatok olvasása az MADriverInstanceReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MADriverReadiness/olvasás | Adatok olvasása az MADriverReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeAddin/olvasás | Adatok olvasása az MAOfficeAddin táblából |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Adatok olvasása az MAOfficeAddinHealth táblából |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Adatok olvasása az MAOfficeAddinHealthIssues táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeAddinInstance/olvasás | Adatok olvasása az MAOfficeAddinInstance táblából |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Adatok olvasása az MAOfficeAddinInstanceReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeAddinReadiness/olvasás | Adatok olvasása az MAOfficeAddinReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeApp/olvasás | Adatok olvasása az MAOfficeApp táblából |
> | Action | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Adatok olvasása az MAOfficeAppHealth táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeAppInstance/olvasás | Adatok olvasása az MAOfficeAppInstance táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeAppReadiness/olvasás | Adatok olvasása az MAOfficeAppReadiness táblából |
> | Action | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Adatok olvasása az MAOfficeBuildInfo táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeCurrencyAssessment/olvasás | Adatok olvasása az MAOfficeCurrencyAssessment táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeCurrencyAssessmentDailyCounts/olvasás | Adatok olvasása az MAOfficeCurrencyAssessmentDailyCounts táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeDeploymentStatus/olvasás | Adatok olvasása az MAOfficeDeploymentStatus táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeMacroHealth/olvasás | Adatok olvasása az MAOfficeMacroHealth táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeMacroHealthIssues/olvasás | Adatok olvasása az MAOfficeMacroHealthIssues táblából |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Adatok olvasása az MAOfficeMacroIssueInstanceReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeMacroIssueReadiness/olvasás | Adatok olvasása az MAOfficeMacroIssueReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeMacroSummary/olvasás | Adatok olvasása az MAOfficeMacroSummary táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeSuite/olvasás | Adatok olvasása az MAOfficeSuite táblából |
> | Action | Microsoft. bepillantások/naplók/MAOfficeSuiteInstance/olvasás | Adatok olvasása az MAOfficeSuiteInstance táblából |
> | Action | Microsoft. bepillantások/naplók/MAProposedPilotDevices/olvasás | Adatok olvasása az MAProposedPilotDevices táblából |
> | Action | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Adatok olvasása az MAWindowsBuildInfo táblából |
> | Action | Microsoft. bepillantások/naplók/MAWindowsCurrencyAssessment/olvasás | Adatok olvasása az MAWindowsCurrencyAssessment táblából |
> | Action | Microsoft. bepillantások/naplók/MAWindowsCurrencyAssessmentDailyCounts/olvasás | Adatok olvasása az MAWindowsCurrencyAssessmentDailyCounts táblából |
> | Action | Microsoft. bepillantások/naplók/MAWindowsDeploymentStatus/olvasás | Adatok olvasása az MAWindowsDeploymentStatus táblából |
> | Action | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Adatok olvasása az MAWindowsSysReqInstanceReadiness táblából |
> | Action | Microsoft. bepillantások/naplók/NetworkMonitoring/olvasás | Adatok olvasása a NetworkMonitoring táblából |
> | Action | Microsoft.Insights/Logs/OfficeActivity/Read | Adatok olvasása az OfficeActivity táblából |
> | Action | Microsoft. elemzések/naplók/művelet/olvasás | Adatok olvasása az Operation táblából |
> | Action | Microsoft.Insights/Logs/OutboundConnection/Read | Adatok olvasása az OutboundConnection táblából |
> | Action | Microsoft. elemzések/naplók/Perf/olvasás | Adatok olvasása a Perf táblából |
> | Action | Microsoft. bepillantások/naplók/ProtectionStatus/olvasás | Adatok olvasása a ProtectionStatus táblából |
> | Action | Microsoft. elemzések/naplók/olvasás | Adatok olvasása az összes naplóból |
> | Action | Microsoft. bepillantások/naplók/ReservedAzureCommonFields/olvasás | Adatok olvasása a ReservedAzureCommonFields táblából |
> | Action | Microsoft. bepillantások/naplók/ReservedCommonFields/olvasás | Adatok olvasása a ReservedCommonFields táblából |
> | Action | Microsoft. bepillantások/naplók/SCCMAssessmentRecommendation/olvasás | Adatok olvasása az SCCMAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Adatok olvasása az SCOMAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/SecurityAlert/Read | Adatok olvasása a SecurityAlert táblából |
> | Action | Microsoft.Insights/Logs/SecurityBaseline/Read | Adatok olvasása a SecurityBaseline táblából |
> | Action | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Adatok olvasása a SecurityBaselineSummary táblából |
> | Action | Microsoft.Insights/Logs/SecurityDetection/Read | Adatok olvasása a SecurityDetection táblából |
> | Action | Microsoft. bepillantások/naplók/SecurityEvent/olvasás | Adatok olvasása a SecurityEvent táblából |
> | Action | Microsoft. bepillantások/naplók/ServiceFabricOperationalEvent/olvasás | Adatok olvasása a ServiceFabricOperationalEvent táblából |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Adatok olvasása a ServiceFabricReliableActorEvent táblából |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Adatok olvasása a ServiceFabricReliableServiceEvent táblából |
> | Action | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Adatok olvasása az SfBAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Adatok olvasása az SfBOnlineAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Adatok olvasása a SharePointOnlineAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Adatok olvasása az SPAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Adatok olvasása az SQLAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Adatok olvasása az SQLQueryPerformance táblából |
> | Action | Microsoft.Insights/Logs/Syslog/Read | Adatok olvasása a Syslog táblából |
> | Action | Microsoft. bepillantások/naplók/SysmonEvent/olvasás | Adatok olvasása a SysmonEvent táblából |
> | Action | Microsoft.Insights/Logs/UAApp/Read | Adatok olvasása az UAApp táblából |
> | Action | Microsoft. bepillantások/naplók/UAComputer/olvasás | Adatok olvasása az UAComputer táblából |
> | Action | Microsoft. bepillantások/naplók/UAComputerRank/olvasás | Adatok olvasása az UAComputerRank táblából |
> | Action | Microsoft. bepillantások/naplók/UADriver/olvasás | Adatok olvasása az UADriver táblából |
> | Action | Microsoft. bepillantások/naplók/UADriverProblemCodes/olvasás | Adatok olvasása az UADriverProblemCodes táblából |
> | Action | Microsoft. bepillantások/naplók/UAFeedback/olvasás | Adatok olvasása az UAFeedback táblából |
> | Action | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Adatok olvasása az UAHardwareSecurity táblából |
> | Action | Microsoft. bepillantások/naplók/UAIESiteDiscovery/olvasás | Adatok olvasása az UAIESiteDiscovery táblából |
> | Action | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Adatok olvasása az UAOfficeAddIn táblából |
> | Action | Microsoft. bepillantások/naplók/UAProposedActionPlan/olvasás | Adatok olvasása az UAProposedActionPlan táblából |
> | Action | Microsoft. bepillantások/naplók/UASysReqIssue/olvasás | Adatok olvasása az UASysReqIssue táblából |
> | Action | Microsoft. bepillantások/naplók/UAUpgradedComputer/olvasás | Adatok olvasása az UAUpgradedComputer táblából |
> | Action | Microsoft.Insights/Logs/Update/Read | Adatok olvasása az Update táblából |
> | Action | Microsoft.Insights/Logs/UpdateRunProgress/Read | Adatok olvasása az UpdateRunProgress táblából |
> | Action | Microsoft.Insights/Logs/UpdateSummary/Read | Adatok olvasása az UpdateSummary táblából |
> | Action | Microsoft. elemzések/naplók/használat/olvasás | Adatok olvasása a Usage táblából |
> | Action | Microsoft.Insights/Logs/W3CIISLog/Read | Adatok olvasása a W3CIISLog táblából |
> | Action | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Adatok olvasása a WaaSDeploymentStatus táblából |
> | Action | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Adatok olvasása a WaaSInsiderStatus táblából |
> | Action | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Adatok olvasása a WaaSUpdateStatus táblából |
> | Action | Microsoft.Insights/Logs/WDAVStatus/Read | Adatok olvasása a WDAVStatus táblából |
> | Action | Microsoft.Insights/Logs/WDAVThreat/Read | Adatok olvasása a WDAVThreat táblából |
> | Action | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Adatok olvasása a WindowsClientAssessmentRecommendation táblából |
> | Action | Microsoft. bepillantások/naplók/WindowsFirewall/olvasás | Adatok olvasása a WindowsFirewall táblából |
> | Action | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Adatok olvasása a WindowsServerAssessmentRecommendation táblából |
> | Action | Microsoft.Insights/Logs/WireData/Read | Adatok olvasása a WireData táblából |
> | Action | Microsoft. bepillantások/naplók/WUDOAggregatedStatus/olvasás | Adatok olvasása a WUDOAggregatedStatus táblából |
> | Action | Microsoft. bepillantások/naplók/WUDOStatus/olvasás | Adatok olvasása a WUDOStatus táblából |
> | Action | Microsoft.Insights/MetricAlerts/Delete | Metrikaalapú riasztás törlése |
> | Action | Microsoft.Insights/MetricAlerts/Read | Metrikaalapú riasztás olvasása |
> | Action | Microsoft. bepillantások/MetricAlerts/állapot/olvasás | Metrikaalapú riasztás állapotának olvasása |
> | Action | Microsoft.Insights/MetricAlerts/Write | Metrikaalapú riasztás létrehozása vagy frissítése |
> | Action | Microsoft.Insights/MetricBaselines/Read | Metrika alapkonfigurációinak olvasása |
> | Action | Microsoft. inviews/MetricDefinitions/Microsoft. bepillantások/olvasás | A metrikadefiníciók olvasása |
> | Action | Microsoft. inviews/MetricDefinitions/Providers/Microsoft. bepillantások/olvasás | A metrikadefiníciók olvasása |
> | Action | Microsoft.Insights/MetricDefinitions/Read | A metrikadefiníciók olvasása |
> | Action | Microsoft. bepillantások/Metricnamespaces/olvasás | Metrikus névterek olvasása |
> | Action | Microsoft.Insights/Metrics/Action | Metrikaművelet |
> | Action | Microsoft. inviews/mérőszámok/Microsoft. bepillantások/olvasás | Metrikák olvasása |
> | Action | Microsoft. inviews/metrika/szolgáltatók/mérőszámok/olvasás | Metrikák olvasása |
> | Action | Microsoft.Insights/Metrics/Read | Metrikák olvasása |
> | DataAction | Microsoft.Insights/Metrics/Write | Metrikák írása |
> | Action | Microsoft.Insights/MigrateToNewpricingModel/Action | Előfizetés migrálása új díjszabási modellbe |
> | Action | Microsoft. bepillantások/műveletek/olvasás | Olvasási műveletek |
> | Action | Microsoft.Insights/Register/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Action | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Előfizetés visszaállítása a régi díjszabási modellre |
> | Action | Microsoft.Insights/ScheduledQueryRules/Delete | Ütemezett lekérdezés szabály törlése |
> | Action | Microsoft.Insights/ScheduledQueryRules/Read | Egy ütemezett lekérdezési szabály olvasása |
> | Action | Microsoft.Insights/ScheduledQueryRules/Write | Ütemezett lekérdezési szabály írása |
> | Action | Microsoft.Insights/Tenants/Register/Action | A Microsoft Insights-szolgáltató inicializálása |
> | Action | Microsoft.Insights/Unregister/Action | A Microsoft Insights-szolgáltató regisztrálása |
> | Action | Microsoft. bepillantások/webtesztek/törlés | Webteszt-konfiguráció törlése |
> | Action | Microsoft. bepillantások/webtesztek/GetToken/olvasás | Egy webteszt jogkivonatának olvasása |
> | Action | Microsoft. bepillantások/webtesztek/MetricDefinitions/olvasás | Egy webteszt metrikadefinícióinak olvasása |
> | Action | Microsoft. bepillantások/webtesztek/mérőszámok/olvasás | Egy webteszt metrikáinak olvasása |
> | Action | Microsoft. bepillantások/webtesztek/olvasás | Webteszt-konfiguráció olvasása |
> | Action | Microsoft. bepillantások/webtesztek/írás | Webteszt-konfiguráció írása |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Intune/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Action | Microsoft.Intune/diagnosticsettings/read | Diagnosztikai beállítások olvasása |
> | Action | Microsoft. Intune/diagnosticsettings/írás | Diagnosztikai beállítások írása |
> | Action | Microsoft. Intune/diagnosticsettingscategories/READ | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.IoTCentral/appTemplates/action | Az Azure IoT Central összes elérhető alkalmazási sablonjának beolvasása |
> | Action | Microsoft.IoTCentral/checkNameAvailability/action | Ellenőrzi, hogy elérhető-e IoT Central alkalmazás neve |
> | Action | Microsoft.IoTCentral/checkSubdomainAvailability/action | Ellenőrzi, hogy elérhető-e IoT Central alkalmazás altartománya |
> | Action | Microsoft.IoTCentral/IoTApps/delete | IoT Central-alkalmazások törlése |
> | Action | Microsoft.IoTCentral/IoTApps/read | Egyetlen IoT Central alkalmazást kap |
> | Action | Microsoft.IoTCentral/IoTApps/write | IoT Central-alkalmazások létrehozása vagy frissítése |
> | Action | Microsoft.IoTCentral/operations/read | Az összes elérhető művelet beolvasása IoT Central alkalmazásokban |
> | Action | Microsoft.IoTCentral/register/action | Az Azure IoT Central erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.IoTSpaces/Graph/delete | Törli a Microsoft. IoTSpaces Graph-erőforrást |
> | Action | Microsoft.IoTSpaces/Graph/read | A Microsoft. IoTSpaces Graph-erőforrás (ok) beolvasása |
> | Action | Microsoft. IoTSpaces/gráf/írás | Microsoft. IoTSpaces Graph-erőforrás létrehozása |
> | Action | Microsoft. IoTSpaces/regisztráció/művelet | A Microsoft. IoTSpaces Graph erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása az erőforrások létrehozásának engedélyezéséhez |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.KeyVault/checkNameAvailability/read | Annak ellenőrzése, hogy érvényes-e a kulcstartónév, és nincs-e használatban |
> | Action | Microsoft.KeyVault/deletedVaults/read | Helyreállíthatóan törölt kulcstartók tulajdonságainak megtekintése |
> | Action | Microsoft.KeyVault/hsmPools/delete | HSM-készlet törlése |
> | Action | Microsoft.KeyVault/hsmPools/joinVault/action | Kulcstartó HSM-készlethez való csatlakoztatása |
> | Action | Microsoft.KeyVault/hsmPools/read | A HSM-készlet tulajdonságainak megtekintése |
> | Action | Microsoft.KeyVault/hsmPools/write | Új HSM-készlet létrehozása vagy egy meglévő HSM-készlet tulajdonságainak frissítése |
> | Action | Microsoft.KeyVault/locations/deletedVaults/purge/action | Helyreállíthatóan törölt kulcstartó végleges törlése |
> | Action | Microsoft.KeyVault/locations/deletedVaults/read | Helyreállíthatóan törölt kulcstartó tulajdonságainak megtekintése |
> | Action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.KeyVault értesítése arról, hogy virtuális hálózat vagy alhálózat törlése folyamatban van |
> | Action | Microsoft.KeyVault/locations/operationResults/read | Hosszú futtatású művelet eredményének ellenőrzése |
> | Action | Microsoft.KeyVault/operations/read | A Microsoft.KeyVault erőforrás-szolgáltató elérhető műveleteinek listázása |
> | Action | Microsoft.KeyVault/register/action | Az előfizetés regisztrálása |
> | Action | Microsoft.KeyVault/unregister/action | Előfizetés regisztrációjának törlése |
> | Action | Microsoft.KeyVault/vaults/accessPolicies/write | Egyesítéssel vagy cserével frissíthet egy meglévő hozzáférési szabályzatot, vagy létrehozhat egy új hozzáférési szabályzatot a tárolóban. |
> | Action | Microsoft.KeyVault/vaults/delete | A kulcstartó törlése |
> | Action | Microsoft. kulcstartó/tárolók/üzembe helyezés/művelet | A kulcstartóban található titkos kódokhoz való hozzáférés engedélyezése Azure-erőforrások üzembe helyezésekor |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/delete | Értesíti a Microsoft.KeyVault névteret a Key Vault új EventGrid-előfizetésének törléséről |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/read | Értesíti a Microsoft.KeyVault névteret a Key Vault új EventGrid-előfizetésének megtekintéséről |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/write | Értesíti a Microsoft.KeyVault névteret a Key Vault új EventGrid-előfizetésének létrehozásáról |
> | Action | Microsoft.KeyVault/vaults/read | A kulcstartó tulajdonságainak megtekintése |
> | Action | Microsoft. kulcstartó/tárolók/titkok/olvasás | Megtekintheti egy titkos kulcs tulajdonságait, de nem az értékét. |
> | Action | Microsoft.KeyVault/vaults/secrets/write | Hozzon létre egy új titkot, vagy frissítse egy meglévő titok értékét. |
> | Action | Microsoft.KeyVault/vaults/write | Új kulcstartó létrehozása, vagy meglévő kulcstartó tulajdonságainak módosítása |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. Kusto/fürtök/aktiválás/művelet | Elindítja a fürtöt. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Egy csatolt adatbázis-konfigurációs erőforrás törlése. |
> | Action | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Egy csatolt adatbázis-konfigurációs erőforrás beolvasása. |
> | Action | Microsoft. Kusto/fürtök/AttachedDatabaseConfigurations/írás | Egy csatolt adatbázis-konfigurációs erőforrást ír. |
> | Action | Microsoft.Kusto/Clusters/CheckNameAvailability/action | A fürt nevének rendelkezésre állását ellenőrzi. |
> | Action | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Adatbázis-rendszerbiztonsági tag hozzáadására szolgál. |
> | Action | Microsoft. Kusto/fürtök/adatbázisok/CheckNameAvailability/művelet | Egy adott típushoz tartozó név rendelkezésre állásának ellenőrzése. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Törli az adatkapcsolatok erőforrását. |
> | Action | Microsoft. Kusto/fürtök/adatbázisok/DataConnections/olvasás | Az adatkapcsolatok erőforrás beolvasása. |
> | Action | Microsoft. Kusto/fürtök/adatbázisok/DataConnections/írás | Adatkapcsolati erőforrást ír. |
> | Action | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Az adatbázis-adatkapcsolatok ellenőrzése. |
> | Action | Microsoft.Kusto/Clusters/Databases/delete | Egy adatbázis-erőforrás törlése. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Az Event hub-kapcsolatok erőforrásának törlése. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Az Event hub-kapcsolatok erőforrásának beolvasása. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Az Event hub-kapcsolatok erőforrásának írása. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Ellenőrzi az adatbázis-esemény hub-kapcsolatát. |
> | Action | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Az adatbázis-rendszerbiztonsági tag felsorolása. |
> | Action | Microsoft. Kusto/fürtök/adatbázisok/olvasás | Egy adatbázis-erőforrás beolvasása. |
> | Action | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Az adatbázis-rendszerbiztonsági tag eltávolítása. |
> | Action | Microsoft. Kusto/fürtök/adatbázisok/írás | Egy adatbázis-erőforrást ír. |
> | Action | Microsoft. Kusto/fürtök/inaktiválás/művelet | Leállítja a fürtöt. |
> | Action | Microsoft.Kusto/Clusters/delete | Egy fürterőforrás törlése. |
> | Action | Microsoft. Kusto/fürtök/olvasás | Egy fürterőforrás beolvasása. |
> | Action | Microsoft. Kusto/fürtök/SKU/READ | Egy fürt SKU-erőforrásának beolvasása. |
> | Action | Microsoft. Kusto/fürtök/indítás/művelet | Elindítja a fürtöt. |
> | Action | Microsoft.Kusto/Clusters/Stop/action | Leállítja a fürtöt. |
> | Action | Microsoft. Kusto/fürtök/írás | Fürt erőforrásának írása. |
> | Action | Microsoft.Kusto/Locations/CheckNameAvailability/action | Az erőforrás nevének rendelkezésre állását ellenőrzi. |
> | Action | Microsoft.Kusto/locations/operationresults/read | Olvasási műveletek erőforrásai |
> | Action | Microsoft. Kusto/Operations/READ | Olvasási műveletek erőforrásai |
> | Action | Microsoft.Kusto/Register/action | Regisztrálja az előfizetést a Kusto erőforrás-szolgáltatón. |
> | Action | Microsoft. Kusto/SKU/READ | SKU-erőforrás beolvasása. |
> | Action | Microsoft.Kusto/Unregister/action | Az előfizetés regisztrációjának törlése a Kusto erőforrás-szolgáltatóra. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.LabServices/labAccounts/CreateLab/action | Tesztkörnyezet létrehozása labor-fiókban. |
> | Action | Microsoft.LabServices/labAccounts/delete | Tesztkörnyezet-fiókok törlése. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/delete | Katalógus lemezképének törlése. |
> | Action | Microsoft. LabServices/labAccounts/galleryImages/READ | Katalógus képeinek olvasása. |
> | Action | Microsoft. LabServices/labAccounts/galleryImages/Write | Katalógusbeli rendszerképek hozzáadása vagy módosítása. |
> | Action | Microsoft. LabServices/labAccounts/GetRegionalAvailability/művelet | Regionális elérhetőségi információk beolvasása a labor-fiókban konfigurált egyes méretek kategóriához |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/delete | Idleshutdowns törlése. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/READ | Idleshutdowns olvasása. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Write | Idleshutdowns hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Felhasználók hozzáadása laborhoz |
> | Action | Microsoft.LabServices/labAccounts/labs/delete | Labs törlése. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | A környezeti beállításokban egy felhasználó véletlenszerű környezetét állítja be |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Környezeti beállítás törlése. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | A környezet igénylése és a felhasználóhoz rendelése |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Környezetek törlése. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Környezetek olvasása. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | A felhasználó jelszavának alaphelyzetbe állítása egy adott környezetben |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | A környezeten belüli összes erőforrás indításával elindítja a környezetet. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Egy környezet leállítása a környezetben lévő összes erőforrás leállításával |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Környezetek hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | A környezet beállításához szükséges erőforrások kiosztása/kiosztása a tesztkörnyezet/környezet beállítása aktuális állapotán alapul. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Környezeti beállítások olvasása. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Visszaállítja a jelszót a sablon virtuális gépén. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | A sablon aktuális képét menti a megosztott galériába a labor-fiókban |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Ütemtervek törlése. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Ütemtervek olvasása. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Ütemtervek hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | A sablonon belüli összes erőforrás indításával elindítja a sablont. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Leállít egy sablont a sablonban lévő összes erőforrás leállításával. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Környezeti beállítás hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/labAccounts/labs/read | A laborok beolvasása. |
> | Action | Microsoft.LabServices/labAccounts/labs/Register/action | Regisztráljon a felügyelt laborba. |
> | Action | Microsoft.LabServices/labAccounts/labs/SendEmail/action | E-mail-cím küldése a laborhoz tartozó regisztrációs hivatkozással |
> | Action | Microsoft.LabServices/labAccounts/labs/users/delete | Felhasználók törlése. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/read | Felhasználók beolvasása. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/write | Felhasználók hozzáadása vagy módosítása. |
> | Action | Microsoft. LabServices/labAccounts/Labs/írás | Labs hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/labAccounts/read | Laboratóriumi fiókok olvasása. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Sharedgalleries törlése. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/read | Sharedgalleries olvasása. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/write | Sharedgalleries hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/delete | Sharedimages törlése. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/read | Sharedimages olvasása. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/write | Sharedimages hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/labAccounts/write | Lab-fiókok hozzáadása vagy módosítása. |
> | Action | Microsoft.LabServices/locations/operations/read | Olvasási műveletek. |
> | Action | Microsoft. LabServices/regisztráció/művelet | Az előfizetés regisztrálása |
> | Action | Microsoft.LabServices/users/GetEnvironment/action | A virtuális gép adatainak beolvasása |
> | Action | Microsoft.LabServices/users/GetOperationBatchStatus/action | Kötegelt művelet állapotának beolvasása |
> | Action | Microsoft.LabServices/users/GetOperationStatus/action | A hosszú ideig futó művelet állapotának beolvasása |
> | Action | Microsoft.LabServices/users/GetPersonalPreferences/action | Felhasználó személyes beállításainak beolvasása |
> | Action | Microsoft.LabServices/users/ListAllEnvironments/action | A felhasználó összes környezetének listázása |
> | Action | Microsoft.LabServices/users/ListEnvironments/action | A felhasználó környezetének listázása |
> | Action | Microsoft. LabServices/Users/ListLabs/Action | A felhasználóhoz tartozó Labs listázása. |
> | Action | Microsoft. LabServices/felhasználók/regisztráció/művelet | Felhasználó regisztrálása felügyelt laborban |
> | Action | Microsoft.LabServices/users/ResetPassword/action | A felhasználó jelszavának alaphelyzetbe állítása egy adott környezetben |
> | Action | Microsoft.LabServices/users/StartEnvironment/action | A környezeten belüli összes erőforrás indításával elindítja a környezetet. |
> | Action | Microsoft. LabServices/Users/StopEnvironment/Action | Egy környezet leállítása a környezetben lévő összes erőforrás leállításával |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Logic/integrationAccounts/agreements/delete | Egy integrációs fiókban lévő megállapodás törlése. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Egy integrációs fiókban lévő megállapodástartalom visszahívási URL-címének beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/read | Egy integrációs fiókban lévő megállapodás beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/write | A megállapodás létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/delete | Egy integrációs fiókban lévő szerelvény törlése. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Egy integrációs fiókban lévő szerelvénytartalom visszahívási URL-címének beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/read | Egy integrációs fiókban lévő szerelvény beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/write | A szerelvény létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Egy integrációs fiókban lévő kötegkonfiguráció törlése. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | A kötegkonfiguráció létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/delete | Egy integrációs fiókban lévő tanúsítvány törlése. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/read | Egy integrációs fiókban lévő tanúsítvány beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/write | A tanúsítvány létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/delete | Egy integrációs fiók törlése. |
> | Action | Microsoft.Logic/integrationAccounts/join/action | Az integrációs fiók csatlakoztatása. |
> | Action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Egy integrációs fiók visszahívási URL-címének beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Egy kulcstartóban lévő kulcsok beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Egy integrációs fiókban bekövetkező követési események naplózása. |
> | Action | Microsoft.Logic/integrationAccounts/maps/delete | Egy integrációs fiókban lévő leképezés törlése. |
> | Action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Egy integrációs fiókban lévő hozzárendelési tartalom visszahívási URL-címének beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/maps/read | Egy integrációs fiókban lévő hozzárendelés beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/maps/write | A hozzárendelés létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/partners/delete | Egy integrációs fiókban lévő partner törlése. |
> | Action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Egy integrációs fiókban lévő partnertartalom visszahívási URL-címének beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/partners/read | Beolvassa a partnert az integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/partners/write | A partner létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Az integrációs fiók naplódefinícióinak olvasása. |
> | Action | Microsoft.Logic/integrationAccounts/read | Egy integrációs fiók beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/delete | Egy integrációs fiókban lévő séma törlése. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Egy integrációs fiókban lévő sématartalom visszahívási URL-címének beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/read | Egy integrációs fiókban lévő séma beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/write | A séma létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/delete | Egy integrációs fiókban lévő munkamenet törlése. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/read | Egy integrációs fiókban lévő kötegkonfiguráció beolvasása. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/write | A munkamenet létrehozása vagy frissítése egy integrációs fiókban. |
> | Action | Microsoft.Logic/integrationAccounts/write | Egy integrációs fiók létrehozása vagy frissítése. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/delete | Törli az integrációs szolgáltatási környezetet. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/join/action | Csatlakozik az integrációs szolgáltatási környezethez. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Az integrációs szolgáltatási környezet felügyelt API-műveletének beolvasása. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Az integrációs szolgáltatási környezet felügyelt API-jának beolvasása. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Az integrációs szolgáltatási környezet műveleti állapotainak beolvasása. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Az integrációs szolgáltatási környezet metrikadefinícióinak beolvasása. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/read | Beolvassa az integrációs szolgáltatási környezetet. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/write | Az integrációs szolgáltatási környezet létrehozása vagy frissítése. |
> | Action | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | A munkafolyamat javasolt műveletcsoportjainak beolvasása. |
> | Action | Microsoft. Logic/Locations/munkafolyamatok/érvényesítés/művelet | Érvényesíti a munkafolyamatot. |
> | Action | Microsoft.Logic/operations/read | Egy művelet beolvasása. |
> | Action | Microsoft.Logic/register/action | A Microsoft.Logic erőforrás-szolgáltató regisztrálása egy adott előfizetéshez. |
> | Action | Microsoft.Logic/workflows/accessKeys/delete | Törli a hívóbetűt. |
> | Action | Microsoft. Logic/munkafolyamatok/accessKeys/lista/művelet | Listázza a hívóbetű titkos kulcsait. |
> | Action | Microsoft. Logic/munkafolyamatok/accessKeys/olvasás | Beolvassa a hívóbetűt. |
> | Action | Microsoft. Logic/munkafolyamatok/accessKeys/újragenerált/művelet | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Action | Microsoft. Logic/munkafolyamatok/accessKeys/írás | Létrehozza vagy frissíti a hívóbetűt. |
> | Action | Microsoft.Logic/workflows/delete | Törli a munkafolyamatot. |
> | Action | Microsoft.Logic/workflows/disable/action | Letiltja a munkafolyamatot. |
> | Action | Microsoft.Logic/workflows/enable/action | Engedélyezi a munkafolyamatot. |
> | Action | Microsoft.Logic/workflows/listCallbackUrl/action | Egy munkafolyamat visszahívási URL-címének beolvasása. |
> | Action | Microsoft.Logic/workflows/listSwagger/action | A munkafolyamat Swagger-definícióinak beolvasása. |
> | Action | Microsoft. Logic/munkafolyamatok/áthelyezés/művelet | A munkafolyamatot a meglévő előfizetés-azonosítójáról, erőforráscsoportjából és/vagy nevéről egy másik előfizetés-azonosítóra, erőforráscsoportba és/vagy névre helyezi át. |
> | Action | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/diagnosticSettings/READ | Beolvassa a munkafolyamat diagnosztikai beállításait. |
> | Action | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantások/diagnosticSettings/írás | Létrehozza vagy frissíti a munkafolyamat diagnosztikai beállításait. |
> | Action | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/logDefinitions/READ | Beolvassa a munkafolyamat naplómeghatározásait. |
> | Action | Microsoft. Logic/munkafolyamatok/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Beolvassa a munkafolyamat metrikai meghatározásait. |
> | Action | Microsoft. Logic/munkafolyamatok/olvasás | Beolvassa a munkafolyamatot. |
> | Action | Microsoft.Logic/workflows/regenerateAccessKey/action | Újra létrehozza a hívóbetű titkos kulcsait. |
> | Action | Microsoft.Logic/workflows/run/action | Elindítja egy munkafolyamat futtatását. |
> | Action | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | A munkafolyamat-futtatási műveleti kifejezés követési adatainak beolvasása. |
> | Action | Microsoft.Logic/workflows/runs/actions/read | Beolvassa a munkafolyamat-futtatási műveletet. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Egy munkafolyamat-futtatási művelet ismétlődési kifejezéskövetési adatainak beolvasása. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/read | Egy munkafolyamat-futtatási művelet ismétlődésének beolvasása. |
> | Action | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | A munkafolyamat-futtatási ismétlődéskérelem-előzmények olvasása |
> | Action | Microsoft.Logic/workflows/runs/actions/requestHistories/read | A munkafolyamat-futtatási műveletkérelem-előzmények olvasása |
> | Action | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Egy munkafolyamat-futtatási művelet hatóköre ismétlődésének beolvasása. |
> | Action | Microsoft.Logic/workflows/runs/cancel/action | Megszakítja egy munkafolyamat futtatását. |
> | Action | Microsoft.Logic/workflows/runs/delete | Egy adott munkafolyamat-futtatási művelet törlése. |
> | Action | Microsoft.Logic/workflows/runs/operations/read | Beolvassa a munkafolyamat-futtatás műveleti állapotát. |
> | Action | Microsoft.Logic/workflows/runs/read | Beolvassa a munkafolyamat-futtatást. |
> | Action | Microsoft.Logic/workflows/suspend/action | Egy munkafolyamat felfüggesztése. |
> | Action | Microsoft. Logic/munkafolyamatok/triggerek/előzmények/olvasás | Beolvassa a triggerelőzményeket. |
> | Action | Microsoft. Logic/munkafolyamatok/triggerek/előzmények/újraküldése/művelet | A munkafolyamat-trigger újraküldése. |
> | Action | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Action | Microsoft. Logic/munkafolyamatok/triggerek/olvasás | Beolvassa a triggert. |
> | Action | Microsoft. Logic/munkafolyamatok/eseményindítók/alaphelyzetbe állítás/művelet | A trigger alaphelyzetbe állítása. |
> | Action | Microsoft. Logic/munkafolyamatok/eseményindítók/Futtatás/művelet | Végrehajtja a triggert. |
> | Action | Microsoft.Logic/workflows/triggers/setState/action | A trigger állapotának beállítása. |
> | Action | Microsoft.Logic/workflows/validate/action | Érvényesíti a munkafolyamatot. |
> | Action | Microsoft. Logic/munkafolyamatok/verziók/olvasás | Beolvassa a munkafolyamat verzióját. |
> | Action | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Egy trigger visszahívási URL-címének beolvasása. |
> | Action | Microsoft. Logic/munkafolyamatok/írás | Létrehozza vagy frissíti a munkafolyamatot. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Bármely Machine Learning kötelezettségvállalási terv társításának áthelyezése |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Bármely Machine Learning kötelezettségvállalási terv társításának beolvasása |
> | Action | Microsoft.MachineLearning/commitmentPlans/delete | Machine Learning kötelezettségvállalási terv törlése |
> | Action | Microsoft. MachineLearning/commitmentPlans/JOIN/Action | Csatlakozás bármely Machine Learning kötelezettségvállalási tervhez |
> | Action | Microsoft.MachineLearning/commitmentPlans/read | Machine Learning kötelezettségvállalási terv beolvasása |
> | Action | Microsoft. MachineLearning/commitmentPlans/Write | Machine Learning kötelezettségvállalási terv létrehozása vagy frissítése |
> | Action | Microsoft.MachineLearning/locations/operationresults/read | Machine Learning művelet eredményének beolvasása |
> | Action | Microsoft.MachineLearning/locations/operationsstatus/read | Folyamatban lévő Machine Learning művelet állapotának beolvasása |
> | Action | Microsoft.MachineLearning/operations/read | Machine Learning műveletek beolvasása |
> | Action | Microsoft. MachineLearning/regisztráció/művelet | Regisztrálja az előfizetést a Machine learning webszolgáltatás erőforrás-szolgáltatója számára, és lehetővé teszi a webszolgáltatások létrehozását. |
> | Action | Microsoft. MachineLearning/SKU/READ | Machine Learning kötelezettségvállalási terv beszerzése |
> | Action | Microsoft.MachineLearning/webServices/action | Regionális webszolgáltatás tulajdonságainak létrehozása a támogatott régiók számára |
> | Action | Microsoft.MachineLearning/webServices/delete | Machine Learning webszolgáltatás törlése |
> | Action | Microsoft.MachineLearning/webServices/listkeys/read | Kulcsok beolvasása egy Machine Learning webszolgáltatáshoz |
> | Action | Microsoft.MachineLearning/webServices/read | Bármely Machine Learning webszolgáltatás beolvasása |
> | Action | Microsoft. MachineLearning/webszolgáltatások/írás | Machine Learning webszolgáltatás létrehozása vagy frissítése |
> | Action | Microsoft. MachineLearning/munkaterületek/törlés | Tetszőleges Machine Learning-munkaterület törlése |
> | Action | Microsoft. MachineLearning/munkaterületek/listworkspacekeys/művelet | Machine Learning-munkaterület kulcsainak listázása |
> | Action | Microsoft. MachineLearning/munkaterületek/olvasás | Bármilyen Machine Learning-munkaterület beolvasása |
> | Action | Microsoft. MachineLearning/munkaterületek/resyncstoragekeys/művelet | Machine Learning-munkaterület konfigurált Storage-fiók kulcsainak újraszinkronizálása |
> | Action | Microsoft. MachineLearning/munkaterületek/írás | Tetszőleges Machine Learning-munkaterület létrehozása vagy frissítése |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Egy adott számítási művelet állapotának beolvasása |
> | Action | Microsoft. MachineLearningServices/Locations/használati adatok/olvasás | Használati jelentés az előfizetéshez tartozó pénzmosás-számítási erőforrásokhoz |
> | Action | Microsoft.MachineLearningServices/locations/vmsizes/read | Támogatott virtuális gépek méretének beolvasása |
> | Action | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Egy adott munkaterület-művelet állapotának beolvasása |
> | Action | Microsoft.MachineLearningServices/register/action | Regisztrálja az előfizetést a Machine Learning Services erőforrás-szolgáltatóhoz |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/delete | Machine Learning Services munkaterület (ek) ben lévő számítási erőforrások törlése |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Machine Learning Services munkaterület számítási erőforrásaihoz tartozó titkok listázása |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Számítási erőforrás csomópontjainak listázása Machine Learning Services munkaterületen |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/read | Beolvassa a Machine Learning Services munkaterület (ek) számítási erőforrásait |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/write | A számítási erőforrások létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | Action | Microsoft.MachineLearningServices/workspaces/delete | Törli a Machine Learning Services munkaterületet (ka) t |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Kísérletek létrehozása vagy frissítése Machine Learning Services munkaterületen |
> | Action | Microsoft.MachineLearningServices/workspaces/listKeys/action | Machine Learning Services munkaterület titkainak listázása |
> | Action | Microsoft.MachineLearningServices/workspaces/read | A Machine Learning Services munkaterület (ok) beolvasása |
> | Action | Microsoft.MachineLearningServices/workspaces/write | Machine Learning Services munkaterület (ok) létrehozása vagy frissítése |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. ManagedIdentity/identitások/olvasás | Meglévő rendszerhez rendelt identitás beolvasása |
> | Action | Microsoft.ManagedIdentity/register/action | Regisztrálja az előfizetést a felügyelt identitás erőforrás-szolgáltatója számára |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | Meglévő felhasználóhoz rendelt identitás erőforráshoz való hozzárendelésének RBAC művelete |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Meglévő felhasználó által hozzárendelt identitás törlése |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/read | Meglévő felhasználóhoz rendelt identitás beolvasása |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/write | Új felhasználóhoz rendelt identitás létrehozása vagy egy meglévő felhasználóhoz rendelt identitáshoz társított címkék frissítése |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/READ | A Managed Services-beli regisztrációs definíciók listájának beolvasása. |
> | Action | Microsoft. ManagedServices/operationStatuses/READ | Az erőforrás műveleti állapotának beolvasása. |
> | Action | Microsoft. ManagedServices/regisztráció/művelet | Regisztráljon a felügyelt szolgáltatásokra. |
> | Action | Microsoft. ManagedServices/registrationAssignments/delete | Managed Services-beli regisztrációs hozzárendelés eltávolítása. |
> | Action | Microsoft. ManagedServices/registrationAssignments/READ | A Managed Services-beli regisztrációs hozzárendelések listájának beolvasása. |
> | Action | Microsoft. ManagedServices/registrationAssignments/Write | Managed Services-beli regisztrációs hozzárendelés hozzáadása vagy módosítása. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/delete | Managed Services-beli regisztrációs definíció eltávolítása. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/READ | A Managed Services-beli regisztrációs definíciók listájának beolvasása. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/Write | Managed Services-beli regisztrációs definíció hozzáadása vagy módosítása. |
> | Action | Microsoft. ManagedServices/regisztráció/művelet | A felügyelt szolgáltatások regisztrációjának törlése. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Management/checkNameAvailability/action | Ellenőrzi, hogy a felügyeleti csoport megadott neve érvényes-e és egyedi-e. |
> | Action | Microsoft. Management/getEntities/művelet | A hitelesített felhasználó összes entitásának (Management Groups, előfizetések stb.) listázása. |
> | Action | Microsoft.Management/managementGroups/delete | Felügyeleti csoport törlése. |
> | Action | Microsoft.Management/managementGroups/read | Felügyeleti csoportok listázása a hitelesített felhasználó számára. |
> | Action | Microsoft.Management/managementGroups/subscriptions/delete | Az előfizetés társítása a felügyeleti csoportból. |
> | Action | Microsoft. Management/managementGroups/előfizetések/írás | Meglévő előfizetés társítása a felügyeleti csoporttal. |
> | Action | Microsoft. Management/managementGroups/írás | Hozzon létre vagy frissítsen egy felügyeleti csoportot. |
> | Action | Microsoft.Management/register/action | A megadott előfizetés regisztrálása a Microsoft. Management szolgáltatásban |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | DataAction | Microsoft. Maps/fiókok/adatgyűjtés/olvasás | Olvasási hozzáférést biztosít a Maps-fiókhoz. |
> | Action | Microsoft. Maps/fiókok/törlés | Maps-fiók törlése. |
> | Action | Microsoft.Maps/accounts/eventGridFilters/delete | Event Grid szűrő törlése |
> | Action | Microsoft. Maps/accounts/eventGridFilters/READ | Event Grid szűrő beszerzése |
> | Action | Microsoft. Maps/fiókok/eventGridFilters/írás | Event Grid szűrő létrehozása vagy frissítése |
> | Action | Microsoft. Maps/accounts/Listkeys műveletének beolvasása/Action | Maps-fiók kulcsainak listázása |
> | Action | Microsoft. Maps/fiókok/olvasás | Maps-fiók beszerzése. |
> | Action | Microsoft. Maps/accounts/regenerateKey/Action | Új Maps-fiók elsődleges vagy másodlagos kulcsának létrehozása |
> | Action | Microsoft. Maps/fiókok/írás | Maps-fiók létrehozása vagy frissítése. |
> | Action | Microsoft. Maps/Operations/READ | Szolgáltatói műveletek beolvasása |
> | Action | Microsoft.Maps/register/action | A szolgáltató regisztrálása |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Szerződés beolvasása. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Aláírt szerződés elfogadása. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Kép importálása a végfelhasználó ACR-jébe. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Konfiguráció beolvasása. |
> | Action | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Konfiguráció mentése. |
> | Action | Microsoft.Marketplace/register/action | A Microsoft.Marketplace erőforrás-szolgáltató regisztrálása az előfizetésben. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/delete | A TÖRLÉSi művelet egy klasszikus fejlesztői szolgáltatási erőforráson van. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Lekéri a klasszikus fejlesztői szolgáltatások erőforrás-kezelési kulcsait. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Lekéri egy klasszikus fejlesztői szolgáltatás egyszeri bejelentkezési URL-címét. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/read | A GET művelet egy klasszikus fejlesztői szolgáltatáson keresztül történik. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Létrehoz egy klasszikus fejlesztői szolgáltatások erőforrás-kezelési kulcsait. |
> | Action | Microsoft.MarketplaceApps/Operations/read | Olvassa el az összes erőforrástípus műveleteit. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/megszakítás/művelet | Egy szerződés megszakítása egy adott piactér-elemmel kapcsolatban |
> | Action | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/olvasás | Szerződés visszaadása egy adott piactér-elemmel kapcsolatban |
> | Action | Microsoft. MarketplaceOrdering/szerződések/ajánlatok/csomagok/aláírás/művelet | Szerződés aláírása egy adott piactér-elemmel kapcsolatban |
> | Action | Microsoft. MarketplaceOrdering/szerződések/olvasás | Az adott előfizetéshez tartozó összes szerződés visszaküldése |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Szerződés beszerzése egy adott Piactéri virtuálisgép-elemmel kapcsolatban |
> | Action | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Egy adott Piactéri virtuálisgép-elemmel kapcsolatos szerződés aláírása vagy megszakítása |
> | Action | Microsoft. MarketplaceOrdering/Operations/READ | Az API összes lehetséges műveletének listázása |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. Media/checknameavailability/művelet | Ellenőrzi, hogy elérhető-e Media Services fiók neve |
> | Action | Microsoft. Media/Locations/checkNameAvailability/Action | Ellenőrzi, hogy elérhető-e Media Services fiók neve |
> | Action | Microsoft. Media/Mediaservices/accountfilters/delete | Bármely fiók szűrő törlése |
> | Action | Microsoft. Media/Mediaservices/accountfilters/READ | Bármely fiók szűrő beolvasása |
> | Action | Microsoft. Media/Mediaservices/accountfilters/Write | Bármely fiók szűrő létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/assets/assetfilters/delete | Bármely eszköz-szűrő törlése |
> | Action | Microsoft. Media/Mediaservices/assets/assetfilters/READ | Bármely eszköz szűrő beolvasása |
> | Action | Microsoft. Media/Mediaservices/assets/assetfilters/Write | Bármely eszköz-szűrő létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/assets/delete | Bármely eszköz törlése |
> | Action | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Eszköz titkosítási kulcsának beolvasása |
> | Action | Microsoft.Media/mediaservices/assets/listContainerSas/action | Asset Container SAS URL-címek listázása |
> | Action | Microsoft. Media/Mediaservices/assets/listStreamingLocators/Action | Eszköz streaming-lokátorok listázása |
> | Action | Microsoft. Media/Mediaservices/assets/READ | Bármilyen eszköz beolvasása |
> | Action | Microsoft. Media/Mediaservices/assets/Write | Bármely eszköz létrehozása vagy frissítése |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | A tartalmi kulcsokra vonatkozó szabályzat törlése |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Szabályzat tulajdonságainak beolvasása titkokkal |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/read | A tartalmi kulcsokra vonatkozó szabályzat beolvasása |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/write | A tartalmi kulcsokra vonatkozó szabályzatok létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/delete | Bármely Media Services fiók törlése |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/delete | Bármely Event Grid szűrő törlése |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/read | Bármilyen Event Grid szűrő olvasása |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/write | Tetszőleges Event Grid szűrő létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/liveEventOperations/READ | Bármely élő esemény műveletének beolvasása |
> | Action | Microsoft.Media/mediaservices/liveEvents/delete | Bármely élő esemény törlése |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Minden élő kimenet törlése |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Bármilyen élő kimenet olvasása |
> | Action | Microsoft. Media/Mediaservices/liveEvents/liveOutputs/Write | Bármilyen élő kimenet létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/liveEvents/READ | Bármely élő esemény beolvasása |
> | Action | Microsoft.Media/mediaservices/liveEvents/reset/action | Minden élő esemény műveletének alaphelyzetbe állítása |
> | Action | Microsoft.Media/mediaservices/liveEvents/start/action | Minden élő esemény művelet elindítása |
> | Action | Microsoft.Media/mediaservices/liveEvents/stop/action | Az élő események működésének leállítása |
> | Action | Microsoft. Media/Mediaservices/liveEvents/Write | Élő esemény létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/liveOutputOperations/READ | Bármilyen élő kimeneti művelet beolvasása |
> | Action | Microsoft. Media/Mediaservices/READ | Bármely Media Services fiók beolvasása |
> | Action | Microsoft. Media/Mediaservices/streamingEndpointOperations/READ | Bármely streaming Endpoint művelet olvasása |
> | Action | Microsoft. Media/Mediaservices/streamingEndpoints/delete | Bármely streaming végpont törlése |
> | Action | Microsoft. Media/Mediaservices/streamingEndpoints/READ | Bármely folyamatos átviteli végpont olvasása |
> | Action | Microsoft. Media/Mediaservices/streamingEndpoints/Scale/Action | A streaming Endpoint művelet méretezése |
> | Action | Microsoft. Media/Mediaservices/streamingEndpoints/Start/művelet | Bármely streaming Endpoint művelet elindítása |
> | Action | Microsoft. Media/Mediaservices/streamingEndpoints/leállítás/művelet | A streaming Endpoint művelet leállítása |
> | Action | Microsoft. Media/Mediaservices/streamingEndpoints/Write | Adatfolyam-végpont létrehozása vagy frissítése |
> | Action | Microsoft.Media/mediaservices/streamingLocators/delete | Adatfolyam-kereső törlése |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Tartalmi kulcsok listázása |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Útvonalak listázása |
> | Action | Microsoft. Media/Mediaservices/streamingLocators/READ | Bármely adatfolyam-kereső olvasása |
> | Action | Microsoft. Media/Mediaservices/streamingLocators/Write | Adatfolyam-kereső létrehozása vagy frissítése |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/delete | Bármilyen folyamatos átviteli szabályzat törlése |
> | Action | Microsoft.Media/mediaservices/streamingPolicies/read | Bármely folyamatos átviteli szabályzat beolvasása |
> | Action | Microsoft. Media/Mediaservices/streamingPolicies/Write | Adatfolyam-szabályzat létrehozása vagy frissítése |
> | Action | Microsoft.Media/mediaservices/syncStorageKeys/action | Csatolt Azure Storage-fiókhoz tartozó tárolási kulcsok szinkronizálása |
> | Action | Microsoft.Media/mediaservices/transforms/delete | Bármely átalakító törlése |
> | Action | Microsoft. Media/Mediaservices/átalakítások/feladatok/cancelJob/művelet | Feladat visszavonása |
> | Action | Microsoft.Media/mediaservices/transforms/jobs/delete | Bármilyen feladatot törölhet |
> | Action | Microsoft. Media/Mediaservices/átalakítások/feladatok/olvasás | Bármilyen feladattípus beolvasása |
> | Action | Microsoft. Media/Mediaservices/átalakítások/feladatok/írás | Tetszőleges feladatok létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/átalakítás/olvasás | Bármely átalakítás olvasása |
> | Action | Microsoft. Media/Mediaservices/átalakítás/írás | Tetszőleges átalakítás létrehozása vagy frissítése |
> | Action | Microsoft. Media/Mediaservices/Write | Bármely Media Services fiók létrehozása vagy frissítése |
> | Action | Microsoft. Media/Operations/READ | Elérhető műveletek beolvasása |
> | Action | Microsoft. Media/regisztráció/művelet | Regisztrálja az előfizetést a Media Services erőforrás-szolgáltatóhoz, és lehetővé teszi Media Services fiókok létrehozását |
> | Action | Microsoft.Media/unregister/action | A Media Services erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. Migrálás/assessmentprojects/assessmentOptions/READ | Az adott helyen elérhető értékelési beállítások beolvasása |
> | Action | Microsoft. Migrálás/assessmentprojects/értékelések/olvasás | Projekt értékeléseinek listázása |
> | Action | Microsoft. Migrálás/assessmentprojects/delete | Az értékelési projekt törlése |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/assessedmachines/olvasás | Értékelt gép tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/törlés | Értékelés törlése |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/downloadurl/művelet | Egy értékelési jelentés URL-címének letöltése |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/olvasás | Értékelés tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/értékelések/írás | Új értékelés létrehozása vagy meglévő frissítése |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/törlés | Egy csoport törlése |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/olvasás | Csoport tulajdonságainak beolvasása |
> | Action | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Frissítési csoport gépek hozzáadásával vagy eltávolításával |
> | Action | Microsoft. Migrálás/assessmentprojects/csoportok/írás | Új csoport létrehozása vagy egy meglévő frissítése |
> | Action | Microsoft. Migrálás/assessmentprojects/hypervcollectors/delete | A HyperV-gyűjtő törlése |
> | Action | Microsoft. Migrálás/assessmentprojects/hypervcollectors/READ | A HyperV-gyűjtő tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/assessmentprojects/hypervcollectors/írás | Új HyperV-gyűjtő létrehozása vagy egy meglévő HyperV-gyűjtő frissítése |
> | Action | Microsoft. Migrálás/assessmentprojects/gépek/olvasás | Gép tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/assessmentprojects/olvasás | Az értékelési projekt tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/delete | A VMware-gyűjtő törlése |
> | Action | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/READ | A VMware Collector tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/assessmentprojects/vmwarecollectors/írás | Létrehoz egy új VMware-gyűjtőt, vagy frissít egy meglévő VMware-gyűjtőt. |
> | Action | Microsoft. Migrálás/assessmentprojects/írás | Új értékelési projekt létrehozása vagy egy meglévő felmérési projekt frissítése |
> | Action | Microsoft. Migrálás/Locations/assessmentOptions/READ | Az adott helyen elérhető értékelési beállítások beolvasása |
> | Action | Microsoft. Migrálás/Locations/checknameavailability/Action | Ellenőrzi, hogy az adott előfizetéshez tartozó erőforrás neve elérhető-e a megadott helyen |
> | Action | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Egy adatbázis-példány tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/migrateprojects/adatbázisok/olvasás | Egy adatbázis tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/migrateprojects/delete | Áttelepítő projekt törlése |
> | Action | Microsoft. Migrálás/migrateprojects/gépek/olvasás | Gép tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/migrateprojects/MigrateEvents/delete | Egy áttelepítési eseményt töröl |
> | Action | Microsoft. Migrálás/migrateprojects/MigrateEvents/READ | Beolvassa az áttelepítés eseményeinek tulajdonságait. |
> | Action | Microsoft. Migrálás/migrateprojects/olvasás | Az áttelepíteni kívánt projekt tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/migrateprojects/registerTool/művelet | Eszköz regisztrálása Migrálás projektbe |
> | Action | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | A Project-megoldási adatáttelepítés törlése |
> | Action | Microsoft.Migrate/migrateprojects/solutions/Delete | Egy Áttelepítő projekt-megoldás törlése |
> | Action | Microsoft. Migrálás/migrateprojects/Solutions/getconfig/művelet | Beolvassa az áttelepítésiprojekt-megoldás konfigurációját |
> | Action | Microsoft. Migrálás/migrateprojects/megoldások/olvasás | Beolvassa a projekt áttelepíthető megoldásának tulajdonságait |
> | Action | Microsoft. Migrálás/migrateprojects/megoldások/írás | Új projekt-Áttelepítő megoldás létrehozása vagy egy meglévő Áttelepítő projekt frissítése |
> | Action | Microsoft. Migrálás/migrateprojects/írás | Új Migrálás projekt létrehozása vagy meglévő Migrálás projekt frissítése |
> | Action | Microsoft. Migrálás/műveletek/olvasás | A Microsoft.Migrate erőforrás-szolgáltatónál elérhető műveletek listázása |
> | Action | Microsoft. Migrálás/projektek/értékelések/olvasás | Projekt értékeléseinek listázása |
> | Action | Microsoft. Migrálás/projektek/törlés | A projekt törlése |
> | Action | Microsoft. Migrálás/projektek/csoportok/értékelések/assessedmachines/olvasás | Értékelt gép tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/projektek/csoportok/értékelések/törlés | Értékelés törlése |
> | Action | Microsoft. Migrálás/projektek/csoportok/értékelések/downloadurl/művelet | Egy értékelési jelentés URL-címének letöltése |
> | Action | Microsoft. Migrálás/projektek/csoportok/értékelések/olvasás | Értékelés tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/projektek/csoportok/értékelések/írás | Új értékelés létrehozása vagy meglévő frissítése |
> | Action | Microsoft.Migrate/projects/groups/delete | Egy csoport törlése |
> | Action | Microsoft. Migrálás/projektek/csoportok/olvasás | Csoport tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/projektek/csoportok/írás | Új csoport létrehozása vagy egy meglévő frissítése |
> | Action | Microsoft. Migrálás/projektek/kulcsok/művelet | A projekt megosztott kulcsainak beolvasása |
> | Action | Microsoft. Migrálás/projektek/gépek/olvasás | Gép tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/projektek/olvasás | Projekt tulajdonságainak beolvasása |
> | Action | Microsoft. Migrálás/projektek/írás | Új projekt létrehozása vagy egy meglévő frissítése |
> | Action | Microsoft. Migrálás/regisztráció/művelet | Az előfizetés regisztrálása a Microsoft.Migrate erőforrás-szolgáltatónál |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.MixedReality/register/action | Regisztrálja az előfizetést a vegyes valóság erőforrás-szolgáltatóhoz. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Térbeli horgonyok létrehozása |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Térbeli horgonyok törlése |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/READ | Közeli térbeli horgonyok felderítése |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Térbeli horgonyok tulajdonságainak beolvasása |
> | Action | Microsoft. MixedReality/SpatialAnchorsAccounts/Providers/Microsoft. bepillantást/diagnosticSettings/READ | A Microsoft. MixedReality/SpatialAnchorsAccounts diagnosztikai beállításainak beolvasása |
> | Action | Microsoft. MixedReality/SpatialAnchorsAccounts/Providers/Microsoft. bepillantások/diagnosticSettings/írás | A Microsoft. MixedReality/SpatialAnchorsAccounts diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft. MixedReality/SpatialAnchorsAccounts/Providers/Microsoft. bepillantást/metricDefinitions/READ | A Microsoft. MixedReality/SpatialAnchorsAccounts elérhető metrikáinak beolvasása |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Térbeli horgonyok keresése |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Diagnosztikai adatainak beküldése az Azure térbeli horgonyok szolgáltatás minőségének javítása érdekében |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Térbeli horgonyok tulajdonságainak frissítése |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.NetApp/locations/operationresults/read | Egy műveleti eredmény erőforrásának beolvasása. |
> | Action | Microsoft.NetApp/locations/read | Egy rendelkezésre állási ellenőrzési erőforrás beolvasása. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Egy készlet-erőforrás törlése. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/read | Egy készlet erőforrásának beolvasása. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Kötet-erőforrás törlése. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | A csatlakoztatási cél erőforrásának beolvasása. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Egy mennyiségi erőforrás beolvasása. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Egy pillanatkép-erőforrás törlése. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Pillanatkép-erőforrás beolvasása. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Egy pillanatkép-erőforrást ír. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Mennyiségi erőforrást ír. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/write | Egy készlet erőforrást ír. |
> | Action | Microsoft.NetApp/netAppAccounts/delete | Egy fiók erőforrásának törlése. |
> | Action | Microsoft.NetApp/netAppAccounts/read | Egy fiók erőforrásának beolvasása. |
> | Action | Microsoft.NetApp/netAppAccounts/write | Egy fiók erőforrásának írása. |
> | Action | Microsoft.NetApp/Operations/read | Művelet-erőforrások beolvasása. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Application Gateway elérhető kérelmek fejlécének beolvasása |
> | Action | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Application Gateway elérhető válasz fejlécének beolvasása |
> | Action | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Application Gateway elérhető kiszolgálói változók beolvasása |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway SSL-re előre definiált házirend |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway elérhető SSL-beállítások |
> | Action | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Beolvas Application Gateway elérhető WAF-szabálykészlet |
> | Action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Egy Application Gateway háttérbeli címkészlet illesztése. Nem Riasztásos. |
> | Action | Microsoft.Network/applicationGateways/backendhealth/action | Egy Application Gateway-háttér állapotának beolvasása |
> | Action | Microsoft.Network/applicationGateways/delete | Application Gateway törlése |
> | Action | Microsoft.Network/applicationGateways/read | Egy Application Gateway beolvasása |
> | Action | Microsoft.Network/applicationGateways/start/action | Egy Application Gateway elindítása |
> | Action | Microsoft.Network/applicationGateways/stop/action | Egy Application Gateway leállítása |
> | Action | Microsoft.Network/applicationGateways/write | Application Gateway létrehozása vagy egy Application Gateway frissítése |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Application Gateway WAF szabályzat törlése |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Application Gateway WAF szabályzat beolvasása |
> | Action | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Létrehoz egy Application Gateway WAF szabályzatot, vagy frissít egy Application Gateway WAF szabályzatot |
> | Action | Microsoft.Network/applicationSecurityGroups/delete | Alkalmazás biztonsági csoportjának törlése |
> | Action | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Összekapcsolja az IP-konfigurációt az alkalmazás biztonsági csoportjaival. Nem riasztásos. |
> | Action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Egy biztonsági szabály csatlakoztatása az alkalmazás biztonsági csoportjaihoz. Nem riasztásos. |
> | Action | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | A új választható applicationsecuritygroup lévő IP-konfigurációk felsorolása |
> | Action | Microsoft.Network/applicationSecurityGroups/read | Egy alkalmazás biztonsági csoportjának AZONOSÍTÓját kapja meg. |
> | Action | Microsoft.Network/applicationSecurityGroups/write | Létrehoz egy alkalmazás biztonsági csoportot, vagy frissít egy meglévő alkalmazás biztonsági csoportot. |
> | Action | Microsoft.Network/azureFirewallFqdnTags/read | Azure Firewall FQDN-címkék beolvasása |
> | Action | Microsoft.Network/azurefirewalls/delete | Azure Firewall törlése |
> | Action | Microsoft. Network/azurefirewalls/READ | Azure Firewall beolvasása |
> | Action | Microsoft. Network/azurefirewalls/Write | Létrehoz vagy frissít egy Azure Firewall |
> | Action | Microsoft.Network/bastionHosts/delete | Megerősített gazdagép törlése |
> | Action | Microsoft.Network/bastionHosts/read | Megerősített gazdagép beolvasása |
> | Action | Microsoft. Network/bastionHosts/Write | Megerősített gazdagép létrehozása vagy frissítése |
> | Action | Microsoft.Network/bgpServiceCommunities/read | BGP szolgáltatás-Közösségek beszerzése |
> | Action | Microsoft.Network/checkFrontDoorNameAvailability/action | Ellenőrzi, hogy elérhető-e a bejárati ajtó neve |
> | Action | Microsoft.Network/checkTrafficManagerNameAvailability/action | Ellenőrzi egy Traffic Manager relatív DNS-név rendelkezésre állását. |
> | Action | Microsoft.Network/connections/delete | Deletes VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/read | Gets VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/connections/revoke/action | Express Route-kapcsolatok állapotának megjelölése visszavont értékként |
> | Action | Microsoft. Network/Connections/sharedkey/Action | Get VirtualNetworkGatewayConnection SharedKey |
> | Action | Microsoft.Network/connections/sharedKey/read | Gets VirtualNetworkGatewayConnection SharedKey |
> | Action | Microsoft.Network/connections/sharedKey/write | Meglévő VirtualNetworkGatewayConnection-SharedKey létrehozása vagy frissítése |
> | Action | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Gets Vpn Device Configuration of VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/Connections/Write | Meglévő VirtualNetworkGatewayConnection létrehozása vagy frissítése |
> | Action | Microsoft.Network/ddosCustomPolicies/delete | A DDoS-re szabott szabályzat törlése |
> | Action | Microsoft.Network/ddosCustomPolicies/read | A DDoS-szabályzatok testreszabott definíciójának beolvasása |
> | Action | Microsoft.Network/ddosCustomPolicies/write | Létrehoz egy DDoS testreszabott szabályzatot, vagy frissít egy meglévő DDoS testreszabott szabályzatot |
> | Action | Microsoft.Network/ddosProtectionPlans/delete | DDoS Protection csomag törlése |
> | Action | Microsoft.Network/ddosProtectionPlans/join/action | Egy DDoS Protection-csomaghoz csatlakozik. Nem riasztásos. |
> | Action | Microsoft.Network/ddosProtectionPlans/read | DDoS Protection csomag beolvasása |
> | Action | Microsoft.Network/ddosProtectionPlans/write | Létrehoz egy DDoS Protection csomagot vagy frissíti a DDoS Protection tervet  |
> | Action | Microsoft.Network/dnsoperationresults/read | DNS-művelet eredményeinek beolvasása |
> | Action | Microsoft.Network/dnsoperationstatuses/read | DNS-művelet állapotának beolvasása  |
> | Action | Microsoft.Network/dnszones/A/delete | Távolítsa el egy adott név rekordját, és írja be a "A" értéket egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/A/read | Adja meg a "A" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/dnszones/A/write | Egy "A" típusú rekordhalmaz létrehozása vagy frissítése egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/dnszones/AAAA/delete | Távolítsa el egy adott név rekordját, és írja be az "AAAA" értéket egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/AAAA/read | Adja meg az "AAAA" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/dnszones/AAAA/write | Hozzon létre vagy frissítsen egy "AAAA" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/dnszones/all/read | DNS-rekordhalmazok beolvasása típusok között |
> | Action | Microsoft.Network/dnszones/CAA/delete | Távolítsa el egy adott név rekordját, és írja be a "CAA" típust egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/CAA/read | Adja meg a "CAA" típusú rekordot JSON formátumban. A rekordazonosító tartalmazza az ÉLETTARTAMot, a címkéket és a ETAG. |
> | Action | Microsoft.Network/dnszones/CAA/write | Hozzon létre vagy frissítsen egy "CAA" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/dnszones/CNAME/delete | Távolítsa el egy adott név rekordját, és írja be a "CNAME" típust egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/CNAME/read | Adja meg a "CNAME" típusú rekordot JSON formátumban. A rekordazonosító tartalmazza az ÉLETTARTAMot, a címkéket és a ETAG. |
> | Action | Microsoft.Network/dnszones/CNAME/write | CNAME típusú rekordhalmaz létrehozása vagy frissítése egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/dnszones/delete | Törölje a DNS-zónát JSON formátumban. A zóna tulajdonságai közé tartoznak a címkék, a ETAG, a numberOfRecordSets és a maxNumberOfRecordSets. |
> | Action | Microsoft.Network/dnszones/MX/delete | Távolítsa el egy adott név rekordját, és írja be az "MX" típust egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/MX/read | Adja meg a "MX" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/dnszones/MX/write | Hozzon létre vagy frissítsen egy MX típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/dnszones/NS/delete | Az NS típusú DNS-rekord törlése |
> | Action | Microsoft.Network/dnszones/NS/read | Gets DNS record set of type NS |
> | Action | Microsoft.Network/dnszones/NS/write | NS típusú DNS-rekord létrehozása vagy frissítése |
> | Action | Microsoft.Network/dnszones/PTR/delete | Távolítsa el egy adott név rekordját, és írja be a "PTR" értéket egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/PTR/read | Adja meg a "PTR" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/dnszones/PTR/write | Hozzon létre vagy frissítsen egy PTR típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/dnszones/read | Kérje le a DNS-zónát JSON formátumban. A zóna tulajdonságai közé tartoznak a címkék, a ETAG, a numberOfRecordSets és a maxNumberOfRecordSets. Vegye figyelembe, hogy a parancs nem kéri le a zónán belül található rekordhalmazokat. |
> | Action | Microsoft.Network/dnszones/recordsets/read | DNS-rekordhalmazok beolvasása típusok között |
> | Action | Microsoft.Network/dnszones/SOA/read | Gets DNS record set of type SOA |
> | Action | Microsoft.Network/dnszones/SOA/write | SOA típusú DNS-rekord létrehozása vagy frissítése |
> | Action | Microsoft.Network/dnszones/SRV/delete | Távolítsa el egy adott név rekordját, és írja be a "SRV" értéket egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/SRV/read | A "SRV" típusú rekordtípusok beolvasása JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/dnszones/SRV/write | SRV típusú rekordtípusok létrehozása vagy frissítése |
> | Action | Microsoft.Network/dnszones/TXT/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be a "TXT" típust egy DNS-zónából. |
> | Action | Microsoft.Network/dnszones/TXT/read | Hozzon létre egy "TXT" típusú rekordot JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/dnszones/TXT/write | Hozzon létre vagy frissítsen egy "TXT" típusú rekordhalmazt egy DNS-zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/dnszones/write | Hozzon létre vagy frissítsen egy DNS-zónát az erőforráscsoporthoz.  A DNS-zóna erőforrásaihoz tartozó címkék frissítésére szolgál. Vegye figyelembe, hogy ez a parancs nem használható rekordhalmazok létrehozásához vagy frissítéséhez a zónán belül. |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/delete | ExpressRouteCircuit-engedély törlése |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/read | ExpressRouteCircuit-hitelesítés beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/write | Meglévő ExpressRouteCircuit-hitelesítés létrehozása vagy frissítése |
> | Action | Microsoft.Network/expressRouteCircuits/delete | ExpressRouteCircuit törlése |
> | Action | Microsoft.Network/expressRouteCircuits/join/action | Egy Express Route-áramkörhöz csatlakozik. Nem riasztásos. |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Beolvas egy ExpressRouteCircuit-társat |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | ExpressRouteCircuit-kapcsolatok törlése |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | ExpressRouteCircuit-kapcsolatok beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Meglévő ExpressRouteCircuit-kapcsolatok erőforrásának létrehozása vagy frissítése |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/delete | ExpressRouteCircuit-társítás törlése |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Peer Express Route Circuit-kapcsolat beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/read | ExpressRouteCircuit-társítás beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | ExpressRouteCircuit-társítási útvonaltábla beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | ExpressRouteCircuit-társítási útvonaltábla összegzésének beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/stats/read | ExpressRouteCircuit-társítási stat beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/write | Meglévő ExpressRouteCircuit-társítás létrehozása vagy frissítése |
> | Action | Microsoft.Network/expressRouteCircuits/read | ExpressRouteCircuit beszerzése |
> | Action | Microsoft.Network/expressRouteCircuits/stats/read | ExpressRouteCircuit-stat beolvasása |
> | Action | Microsoft.Network/expressRouteCircuits/write | Meglévő ExpressRouteCircuit létrehozása vagy frissítése |
> | Action | Microsoft.Network/expressRouteCrossConnections/join/action | Egy expressz útvonalon keresztüli összekötő csatlakoztatása. Nem riasztásos. |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Expressz útvonal közötti kapcsolatok összekapcsolásának ARP-táblájának beolvasása |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Expressz útvonalon keresztüli kapcsolatok társításának törlése |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/read | Expressz útvonal közötti kapcsolatok társításának beolvasása |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Expressz útvonal közötti kapcsolatok összekapcsolási útválasztási táblázatának beolvasása |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Expressz útvonal keresztben való összekapcsolási útválasztási táblázatának összefoglalása |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/write | Létrehoz egy expressz útvonalon keresztüli kapcsolatok összekapcsolását, vagy frissíti a meglévő Express Route-kapcsolatok közötti kapcsolatot |
> | Action | Microsoft.Network/expressRouteCrossConnections/read | Expressz útvonalon keresztüli kapcsolatok lekérdezése |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Express Route-kapcsolatok törlése |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Express Route-kapcsolatok beolvasása |
> | Action | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Express Route-kapcsolatok létrehozása vagy egy meglévő Express Route-kapcsolatok frissítése |
> | Action | Microsoft.Network/expressRouteGateways/join/action | Egy Express Route-átjáróhoz csatlakozik. Nem riasztásos. |
> | Action | Microsoft.Network/expressRouteGateways/read | Express Route-átjáró beolvasása |
> | Action | Microsoft.Network/expressRoutePorts/delete | Deletes ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/join/action | Az Express Route-portok illesztése. Nem riasztásos. |
> | Action | Microsoft.Network/expressRoutePorts/links/read | ExpressRouteLink beolvasása |
> | Action | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/write | ExpressRoutePorts létrehozása vagy frissítése |
> | Action | Microsoft.Network/expressRoutePortsLocations/read | Express Route-portok helyeinek beolvasása |
> | Action | Microsoft.Network/expressRouteServiceProviders/read | Expressz Route Service szolgáltatók beolvasása |
> | Action | Microsoft. Network/firewallPolicies/delete | Tűzfal-házirend törlése |
> | Action | Microsoft. Network/firewallPolicies/csatlakozás/művelet | Csatlakozik egy tűzfal-házirendhez. Nem riasztásos. |
> | Action | Microsoft. Network/firewallPolicies/READ | Tűzfal-házirend beolvasása |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/delete | Egy tűzfalszabály-szabálytípus törlése |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/READ | Tűzfal házirend-szabály csoportjának beolvasása |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/Write | Létrehoz egy tűzfalszabály-szabályt, vagy frissíti egy meglévő tűzfalszabály-szabály csoportját. |
> | Action | Microsoft. Network/firewallPolicies/Write | Létrehoz egy tűzfal-házirendet, vagy frissíti a meglévő tűzfal-házirendet. |
> | Action | Microsoft.Network/frontDoors/backendPools/delete | Háttér-készlet törlése |
> | Action | Microsoft.Network/frontDoors/backendPools/read | Háttér-készlet beolvasása |
> | Action | Microsoft.Network/frontDoors/backendPools/write | Háttér-készlet létrehozása vagy frissítése |
> | Action | Microsoft.Network/frontDoors/delete | Bejárati ajtó törlése |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/delete | Előtér-végpont törlése |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | A HTTPS letiltása egy előtér-végponton |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | HTTPS engedélyezése előtér-végponton |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/read | Előtér-végpont beolvasása |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/write | Előtér-végpont létrehozása vagy frissítése |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/delete | Állapot-mintavételi beállítások törlése |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/read | Állapot mintavételi beállításainak beolvasása |
> | Action | Microsoft. Network/frontDoors/healthProbeSettings/Write | Állapot mintavételi beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Terheléselosztási beállítások létrehozása vagy frissítése |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/read | Terheléselosztási beállítások beolvasása |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/write | Terheléselosztási beállítások létrehozása vagy frissítése |
> | Action | Microsoft. Network/frontDoors/Purge/művelet | Gyorsítótárazott tartalom kiürítése egy bejárati ajtóról |
> | Action | Microsoft. Network/frontDoors/READ | Bejárati ajtó beolvasása |
> | Action | Microsoft.Network/frontDoors/routingRules/delete | Útválasztási szabály törlése |
> | Action | Microsoft.Network/frontDoors/routingRules/read | Útválasztási szabály beolvasása |
> | Action | Microsoft.Network/frontDoors/routingRules/write | Útválasztási szabály létrehozása vagy frissítése |
> | Action | Microsoft.Network/frontDoors/validateCustomDomain/action | Előtérbeli végpont ellenőrzése egy bejárati ajtónál |
> | Action | Microsoft. Network/frontDoors/Write | Bejárati ajtó létrehozása vagy frissítése |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Webalkalmazási tűzfal által felügyelt szabálykészlet beolvasása |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Egy webalkalmazási tűzfal házirend törlése |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Webalkalmazási tűzfal házirend beolvasása |
> | Action | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Webalkalmazási tűzfal házirendjének létrehozása vagy frissítése |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | A terheléselosztó háttérbeli címkészlet csatlakoztatása. Nem Riasztásos. |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/read | Terheléselosztó háttérbeli címkészlet-definíciójának beolvasása |
> | Action | Microsoft.Network/loadBalancers/delete | Terheléselosztó törlése |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Egy Load Balancer előtér-IP-konfigurációhoz csatlakozik. Nem riasztásos. |
> | Action | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Beolvassa a terheléselosztó előtérbeli IP-konfigurációjának definícióját |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | A terheléselosztó bejövő NAT-készletéhez csatlakozik. Nem riasztásos. |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/read | Beolvassa a terheléselosztó bejövő NAT-készletének definícióját |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/delete | A terheléselosztó bejövő NAT-szabályának törlése |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | A terheléselosztó bejövő NAT-szabályához csatlakozik. Nem Riasztásos. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/read | Beolvassa a terheléselosztó bejövő NAT-szabályának definícióját |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/write | Létrehoz egy terheléselosztó bejövő NAT-szabályát, vagy frissíti a meglévő terheléselosztó bejövő NAT-szabályát |
> | Action | Microsoft.Network/loadBalancers/loadBalancingRules/read | Terheléselosztó terheléselosztási szabály definíciójának beolvasása |
> | Action | Microsoft.Network/loadBalancers/networkInterfaces/read | A terheléselosztó alatt lévő összes hálózati adapterre mutató hivatkozásokat kap |
> | Action | Microsoft.Network/loadBalancers/outboundRules/read | A terheléselosztó kimenő szabályának definíciójának beolvasása |
> | Action | Microsoft.Network/loadBalancers/probes/join/action | Lehetővé teszi a terheléselosztó mintavételi funkcióinak használatát. A virtuálisgép-méretezési csoport ezen engedély healthProbe tulajdonsága például hivatkozhat a mintavételre. Nem riasztásos. |
> | Action | Microsoft.Network/loadBalancers/probes/read | Terheléselosztó mintavételének beolvasása |
> | Action | Microsoft.Network/loadBalancers/read | Terheléselosztó definíciójának beolvasása |
> | Action | Microsoft.Network/loadBalancers/virtualMachines/read | A terheléselosztó alatt lévő összes virtuális gépre mutató hivatkozásokat kap |
> | Action | Microsoft.Network/loadBalancers/write | Terheléselosztó létrehozása vagy egy meglévő terheléselosztó frissítése |
> | Action | Microsoft.Network/localnetworkgateways/delete | Deletes LocalNetworkGateway |
> | Action | Microsoft.Network/localnetworkgateways/read | Gets LocalNetworkGateway |
> | Action | Microsoft.Network/localnetworkgateways/write | Meglévő LocalNetworkGateway létrehozása vagy frissítése |
> | Action | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Automatikusan jóváhagyott privát kapcsolati szolgáltatások beolvasása |
> | Action | Microsoft.Network/locations/availableDelegations/read | Elérhető delegálások beolvasása |
> | Action | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Elérhető privát végponti erőforrások beolvasása |
> | Action | Microsoft. Network/Locations/bareMetalTenants/Action | Operációs rendszer nélküli bérlő kiosztása vagy ellenőrzése |
> | Action | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | A gyorsított hálózatkezelés támogatásának ellenőrzése |
> | Action | Microsoft.Network/locations/checkDnsNameAvailability/read | Ellenőrzi, hogy a DNS-címke elérhető-e a megadott helyen |
> | Action | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | A privát hivatkozás szolgáltatás láthatóságának ellenőrzése |
> | Action | Microsoft.Network/locations/operationResults/read | Aszinkron POST-vagy TÖRLÉSi művelet működési eredményének beolvasása |
> | Action | Microsoft.Network/locations/operations/read | Egy aszinkron művelet állapotát jelképező műveleti erőforrás beolvasása |
> | Action | Microsoft.Network/locations/serviceTags/read | Szolgáltatás-címkék beolvasása |
> | Action | Microsoft.Network/locations/supportedVirtualMachineSizes/read | A virtuális gépek támogatott méretének beolvasása |
> | Action | Microsoft. Network/Locations/használat/olvasás | Az erőforrások használati metrikáinak beolvasása |
> | Action | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Lekéri az elérhető Virtual Network Endpoint Services listáját |
> | Action | Microsoft.Network/networkIntentPolicies/delete | Hálózati leképezési házirend törlése |
> | Action | Microsoft.Network/networkIntentPolicies/read | Hálózati leképezési szabályzat lekérése – Leírás |
> | Action | Microsoft.Network/networkIntentPolicies/write | Létrehoz egy hálózati leképezési házirendet, vagy frissíti a meglévő hálózati leképezési házirendet. |
> | Action | Microsoft.Network/networkInterfaces/delete | Hálózati adapter törlése |
> | Action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | A virtuális gép hálózati adapterén konfigurált hálózati biztonsági csoportok beolvasása |
> | Action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | A virtuális gép hálózati adapterén konfigurált útválasztási táblázat beolvasása |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Csatlakozik egy hálózati adapter IP-konfigurációjához. Nem riasztásos. |
> | Action | Microsoft. Network/networkInterfaces/ipconfigurations/READ | Hálózati adapter IP-konfigurációjának meghatározását kéri le.  |
> | Action | Microsoft.Network/networkInterfaces/join/action | Csatlakoztat egy virtuális gépet egy hálózati adapterhez. Nem Riasztásos. |
> | Action | Microsoft.Network/networkInterfaces/loadBalancers/read | Az összes olyan terheléselosztó beolvasása, amelyhez a hálózati adapter tartozik |
> | Action | Microsoft.Network/networkInterfaces/read | Hálózati adapter definíciójának beolvasása.  |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Töröl egy hálózati adaptert, koppintson a konfiguráció elemre. |
> | Action | Microsoft. Network/networkInterfaces/tapConfigurations/READ | Beolvas egy hálózati adaptert a konfiguráció elemre koppintva. |
> | Action | Microsoft. Network/networkInterfaces/tapConfigurations/Write | Hálózati adaptert hoz létre, koppintson a konfiguráció lehetőségre, vagy frissítsen egy meglévő hálózati adaptert a konfiguráció gombra. |
> | Action | Microsoft.Network/networkInterfaces/write | Hálózati adaptert hoz létre, vagy frissít egy meglévő hálózati adaptert.  |
> | Action | Microsoft.Network/networkProfiles/delete | Hálózati profil törlése |
> | Action | Microsoft.Network/networkProfiles/read | Hálózati profil beolvasása |
> | Action | Microsoft.Network/networkProfiles/removeContainers/action | Tárolók eltávolítása |
> | Action | Microsoft.Network/networkProfiles/setContainers/action | Tárolók beállítása |
> | Action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Tároló hálózati adapterek beállítása |
> | Action | Microsoft. Network/networkProfiles/Write | Hálózati profil létrehozása vagy frissítése |
> | Action | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Alapértelmezett biztonsági szabály definíciójának beolvasása |
> | Action | Microsoft.Network/networkSecurityGroups/delete | Hálózati biztonsági csoport törlése |
> | Action | Microsoft.Network/networkSecurityGroups/join/action | Csatlakozik egy hálózati biztonsági csoporthoz. Nem Riasztásos. |
> | Action | Microsoft.Network/networkSecurityGroups/read | Hálózati biztonsági csoport definíciójának beolvasása |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/delete | Biztonsági szabály törlése |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/read | Biztonsági szabály definíciójának beolvasása |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/write | Biztonsági szabály létrehozása vagy meglévő biztonsági szabály frissítése |
> | Action | Microsoft.Network/networkSecurityGroups/write | Hálózati biztonsági csoport létrehozása vagy egy meglévő hálózati biztonsági csoport frissítése |
> | Action | Microsoft.Network/networkWatchers/availableProvidersList/action | Egy adott Azure-régió összes elérhető internetszolgáltatóját adja vissza. |
> | Action | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Egy adott hely és az Azure-régiók közötti relatív késési pontszámot adja vissza az internetszolgáltatók számára. |
> | Action | Microsoft.Network/networkWatchers/configureFlowLog/action | A folyamat naplózását konfigurálja a cél erőforráshoz. |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/delete | Egy figyelő törlése |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/query/action | A megadott végpontok közötti figyelési kapcsolat lekérdezése |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/read | Kapcsolati figyelő részleteinek beolvasása |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/start/action | A megadott végpontok közötti kapcsolat figyelésének megkezdése |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Megadott végpontok közötti figyelési kapcsolat leállítása/szüneteltetése |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/write | Létrehoz egy összekötő-figyelőt |
> | Action | Microsoft.Network/networkWatchers/connectivityCheck/action | Ellenőrzi, hogy lehetséges-e közvetlen TCP-kapcsolatok létesítése egy virtuális gépről egy adott végpontra, beleértve egy másik virtuális gépet vagy egy tetszőleges távoli kiszolgálót. |
> | Action | Microsoft.Network/networkWatchers/delete | Hálózati figyelő törlése |
> | Action | Microsoft.Network/networkWatchers/ipFlowVerify/action | Visszaadja, hogy a csomag engedélyezett-e vagy le van tiltva egy adott célhelyről. |
> | Action | Microsoft.Network/networkWatchers/lenses/delete | Egy lencsét töröl |
> | Action | Microsoft.Network/networkWatchers/lenses/query/action | Egy megadott végponton figyelt hálózati forgalom lekérdezése |
> | Action | Microsoft.Network/networkWatchers/lenses/read | A lencse részleteinek beolvasása |
> | Action | Microsoft.Network/networkWatchers/lenses/start/action | A hálózati forgalom figyelésének megkezdése egy megadott végponton |
> | Action | Microsoft.Network/networkWatchers/lenses/stop/action | A hálózati forgalom figyelésének leállítása/szüneteltetése egy megadott végponton |
> | Action | Microsoft.Network/networkWatchers/lenses/write | Létrehoz egy lencsét |
> | Action | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | A hálózati konfiguráció diagnosztika. |
> | Action | Microsoft.Network/networkWatchers/nextHop/action | Egy adott cél és cél IP-cím esetében adja vissza a következő ugrás típusát és a következő remény IP-címét. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/delete | Csomagok rögzítésének törlése |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Információk beolvasása a csomag rögzítési erőforrásának tulajdonságairól és állapotáról. |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/read | A csomag rögzítési definíciójának beolvasása |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Állítsa le a futó csomag rögzítési munkamenetét. |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/Write | Létrehoz egy csomagot. |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/delete | PingMesh törlése |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/read | PingMesh részleteinek beolvasása |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/start/action | PingMesh elindítása a megadott virtuális gépek között |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/stop/action | A megadott virtuális gépek közötti PingMesh leállítása |
> | Action | Microsoft.Network/networkWatchers/pingMeshes/write | Létrehoz egy PingMesh |
> | Action | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Kötegelt lekérdezés figyelési kapcsolata a megadott végpontok között |
> | Action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Lekérdezi a folyamat naplózási állapotát egy erőforráson. |
> | Action | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Lekérdezi a korábban futtatott vagy éppen futó hibaelhárítási művelet hibaelhárítási eredményét. |
> | Action | Microsoft.Network/networkWatchers/read | A Network Watcher definíciójának beolvasása |
> | Action | Microsoft.Network/networkWatchers/securityGroupView/action | Megtekintheti a virtuális gépen alkalmazott konfigurált és érvényes hálózati biztonsági csoportra vonatkozó szabályokat. |
> | Action | Microsoft.Network/networkWatchers/topology/action | Lekérdezi az erőforrások hálózati szintű nézetét és azok kapcsolatait egy erőforráscsoporthoz. |
> | Action | Microsoft.Network/networkWatchers/troubleshoot/action | Hibaelhárítást indít egy hálózati erőforráson az Azure-ban. |
> | Action | Microsoft.Network/networkWatchers/write | Hálózati figyelő létrehozása vagy meglévő hálózati figyelő frissítése |
> | Action | Microsoft. Network/Operations/READ | Elérhető műveletek beolvasása |
> | Action | Microsoft.Network/p2sVpnGateways/delete | Töröl egy P2SVpnGateway. |
> | Action | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | VPN-profil előállítása a P2SVpnGateway számára |
> | Action | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | P2S VPN-kapcsolati állapotának beolvasása a P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/read | P2SVpnGateway beolvasása. |
> | Action | Microsoft.Network/p2sVpnGateways/write | Egy P2SVpnGateway helyez el. |
> | Action | Microsoft.Network/privateDnsOperationResults/read | Egy saját DNS művelet eredményének beolvasása |
> | Action | Microsoft.Network/privateDnsOperationStatuses/read | Egy saját DNS művelet állapotának beolvasása |
> | Action | Microsoft.Network/privateDnsZones/A/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be az "A" értéket egy saját DNS zónából. |
> | Action | Microsoft.Network/privateDnsZones/A/read | Az "A" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/privateDnsZones/A/write | Hozzon létre vagy frissítsen egy "A" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/delete | Távolítsa el egy adott név rekordját, és írja be az "AAAA" értéket egy saját DNS zónából. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/read | Az "AAAA" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/write | Hozzon létre vagy frissítsen egy "AAAA" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft. Network/privateDnsZones/mind/olvasás | saját DNS-rekordhalmaz beolvasása típusok között |
> | Action | Microsoft.Network/privateDnsZones/CNAME/delete | Távolítsa el egy adott név rekordját, és írja be a "CNAME" típust egy saját DNS zónából. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/read | A "CNAME" típusú rekordhalmaz beolvasása egy saját DNS zónában, JSON formátumban megadva. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/write | CNAME típusú rekordhalmaz létrehozása vagy frissítése egy saját DNS zónán belül. |
> | Action | Microsoft.Network/privateDnsZones/delete | Saját DNS zóna törlése. |
> | Action | Microsoft.Network/privateDnsZones/MX/delete | Remove the record set of a given name and type ‘MX’ from a Private DNS zone. |
> | Action | Microsoft.Network/privateDnsZones/MX/read | Az "MX" típusú rekordhalmaz beolvasása egy saját DNS zónába, JSON formátumban megadva. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/privateDnsZones/MX/write | Hozzon létre vagy frissítsen egy MX típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/privateDnsZones/PTR/delete | Távolítsa el egy adott név rekordját, és írja be a "PTR" értéket egy saját DNS zónából. |
> | Action | Microsoft. Network/privateDnsZones/PTR/READ | A "PTR" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/privateDnsZones/PTR/write | Hozzon létre vagy frissítsen egy PTR típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/privateDnsZones/read | A saját DNS zóna tulajdonságainak beolvasása JSON formátumban. Vegye figyelembe, hogy a parancs nem kéri le azokat a virtuális hálózatokat, amelyekhez a saját DNS zóna csatolva van, vagy a zónán belül található rekordhalmazok. |
> | Action | Microsoft.Network/privateDnsZones/recordsets/read | saját DNS-rekordhalmaz beolvasása típusok között |
> | Action | Microsoft.Network/privateDnsZones/SOA/read | A "SOA" típusú rekordhalmaz beszerzése egy saját DNS zónában, JSON formátumban megadva. |
> | Action | Microsoft.Network/privateDnsZones/SOA/write | Egy "SOA" típusú rekordhalmaz frissítése egy saját DNS zónán belül. |
> | Action | Microsoft.Network/privateDnsZones/SRV/delete | Távolítsa el az adott név és a "SRV" típusú rekordot egy saját DNS zónából. |
> | Action | Microsoft.Network/privateDnsZones/SRV/read | A "SRV" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/privateDnsZones/SRV/write | Hozzon létre vagy frissítsen egy "SRV" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/privateDnsZones/TXT/delete | Távolítsa el egy adott név bejegyzéstípusát, és írja be a "TXT" típust egy saját DNS zónából. |
> | Action | Microsoft.Network/privateDnsZones/TXT/read | A "TXT" típusú rekordhalmaz beolvasása egy saját DNS zónában JSON formátumban. A rekordhalmaz a rekordok listáját, valamint az ÉLETTARTAMot, a címkéket és a ETAG tartalmazza. |
> | Action | Microsoft.Network/privateDnsZones/TXT/write | Hozzon létre vagy frissítsen egy "TXT" típusú rekordhalmazt egy saját DNS zónán belül. A megadott rekordok lecserélik az aktuális rekordokat a rekordhalmazban. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Saját DNS zóna virtuális hálózatra mutató hivatkozásának törlése. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | A saját DNS zóna hivatkozásának beolvasása a virtuális hálózat tulajdonságaihoz JSON formátumban. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Saját DNS zóna virtuális hálózatra mutató hivatkozásának létrehozása vagy frissítése. |
> | Action | Microsoft.Network/privateDnsZones/write | Hozzon létre vagy frissítsen egy saját DNS zónát az erőforráscsoporthoz. Vegye figyelembe, hogy ez a parancs nem használható virtuális hálózati kapcsolatok vagy rekordhalmazok zónán belüli létrehozására vagy frissítésére. |
> | Action | Microsoft.Network/privateEndpoints/delete | Egy privát végponti erőforrás törlése. |
> | Action | Microsoft.Network/privateEndpoints/read | Egy privát végpont erőforrásának beolvasása. |
> | Action | Microsoft.Network/privateEndpoints/write | Létrehoz egy új privát végpontot, vagy frissít egy meglévő privát végpontot. |
> | Action | Microsoft.Network/privateLinkServices/delete | Egy privát link Service-erőforrás törlése. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Privát végponti kapcsolatok törlése. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Egy privát végponti kapcsolat definíciójának beolvasása. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Létrehoz egy új magánhálózati végponti kapcsolatokat, vagy frissíti a meglévő magánhálózati végponti kapcsolatokat. |
> | Action | Microsoft.Network/privateLinkServices/read | Egy privát kapcsolati szolgáltatás erőforrásának beolvasása. |
> | Action | Microsoft. Network/privateLinkServices/Write | Létrehoz egy új privát kapcsolati szolgáltatást, vagy frissíti a meglévő Private link Service-t. |
> | Action | Microsoft.Network/publicIPAddresses/delete | Töröl egy nyilvános IP-címet. |
> | Action | Microsoft.Network/publicIPAddresses/join/action | Csatlakozik egy nyilvános IP-címhez. Nem Riasztásos. |
> | Action | Microsoft.Network/publicIPAddresses/read | Egy nyilvános IP-cím definíciójának beolvasása. |
> | Action | Microsoft.Network/publicIPAddresses/write | Létrehoz egy nyilvános IP-címet, vagy frissíti a meglévő nyilvános IP-címet.  |
> | Action | Microsoft.Network/publicIPPrefixes/delete | Nyilvános IP-előtag törlése |
> | Action | Microsoft.Network/publicIPPrefixes/join/action | Egy PublicIPPrefix illesztése. Nem riasztásos. |
> | Action | Microsoft.Network/publicIPPrefixes/read | Nyilvános IP-előtag definíciójának beolvasása |
> | Action | Microsoft.Network/publicIPPrefixes/write | Létrehoz egy nyilvános IP-előtagot, vagy frissíti a meglévő nyilvános IP-előtagot. |
> | Action | Microsoft. Network/regisztráció/művelet | Az előfizetés regisztrálása |
> | Action | Microsoft.Network/routeFilters/delete | Egy útvonal-szűrő definíciójának törlése |
> | Action | Microsoft.Network/routeFilters/join/action | Egy útvonal-szűrőhöz csatlakozik. Nem Riasztásos. |
> | Action | Microsoft.Network/routeFilters/read | Egy útvonal-szűrő definíciójának beolvasása |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/delete | Egy útvonal-szűrő szabály definíciójának törlése |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/read | Egy útvonal-szűrő szabály definíciójának beolvasása |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/write | Egy útvonal-szűrő szabály létrehozása vagy egy meglévő útvonal-szűrési szabály frissítése |
> | Action | Microsoft. Network/routeFilters/Write | Útvonal-szűrő létrehozása vagy meglévő útvonal-szűrő frissítése |
> | Action | Microsoft.Network/routeTables/delete | Útválasztási táblázat definíciójának törlése |
> | Action | Microsoft. Network/routeTables/csatlakozás/művelet | Egy útválasztási táblázathoz csatlakozik. Nem Riasztásos. |
> | Action | Microsoft. Network/routeTables/READ | Útválasztási táblázat definíciójának beolvasása |
> | Action | Microsoft.Network/routeTables/routes/delete | Útvonal-definíció törlése |
> | Action | Microsoft. Network/routeTables/Routes/READ | Útvonal-definíció beolvasása |
> | Action | Microsoft. Network/routeTables/Routes/Write | Útvonal létrehozása vagy meglévő útvonal frissítése |
> | Action | Microsoft. Network/routeTables/Write | Útválasztási táblázat létrehozása vagy meglévő útválasztási táblázat frissítése |
> | Action | Microsoft.Network/securegateways/delete | Biztonságos átjáró törlése |
> | Action | Microsoft. Network/securegateways/READ | Biztonságos átjáró beszerzése |
> | Action | Microsoft. Network/securegateways/Write | Biztonságos átjáró létrehozása vagy frissítése |
> | Action | Microsoft.Network/serviceEndpointPolicies/delete | Szolgáltatás-végponti házirend törlése |
> | Action | Microsoft.Network/serviceEndpointPolicies/join/action | Csatlakozik egy szolgáltatás-végponti házirendhez. Nem riasztásos. |
> | Action | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Összekapcsolja az alhálózatot a szolgáltatás végponti házirendjeivel. Nem riasztásos. |
> | Action | Microsoft.Network/serviceEndpointPolicies/read | Szolgáltatás-végponti szabályzat leírásának beolvasása |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Szolgáltatás-végponti házirend definíciójának törlése |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Lekérdezi a szolgáltatás végponti házirendjének definíciójának leírását |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Létrehoz egy szolgáltatás-végpont házirend-definícióját, vagy frissíti egy meglévő szolgáltatási végpont házirend-definícióját. |
> | Action | Microsoft.Network/serviceEndpointPolicies/write | Létrehoz egy szolgáltatás-végponti házirendet, vagy frissíti a meglévő szolgáltatási végponti házirendet |
> | Action | Microsoft.Network/trafficManagerGeographicHierarchies/read | Lekéri a földrajzi forgalom útválasztási módszerével használható régiókat tartalmazó Traffic Manager földrajzi hierarchiát |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Töröl egy Azure-végpontot egy meglévő Traffic Manager-profilból. Traffic Manager leállítja az útválasztási forgalmat a törölt Azure-végpontra. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Beolvas egy Traffic Manager profilhoz tartozó Azure-végpontot, beleértve az adott Azure-végpont összes tulajdonságát. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Adjon hozzá egy új Azure-végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő Azure-végpont tulajdonságait az adott Traffic Manager-profilban. |
> | Action | Microsoft.Network/trafficManagerProfiles/delete | Törölje a Traffic Manager profilt. A Traffic Manager profiljához társított összes beállítás elvész, és a profil már nem használható a forgalom irányítására. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Külső végpont törlése egy meglévő Traffic Manager-profilból. Traffic Manager megszakítja a törölt külső végpont felé irányuló útválasztási forgalmat. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Beolvas egy Traffic Manager profilhoz tartozó külső végpontot, amely tartalmazza az adott külső végpont összes tulajdonságát. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Adjon hozzá egy új külső végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő külső végpont tulajdonságait az adott Traffic Manager profilban. |
> | Action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Lekérdezi a megadott Traffic Manager-profilhoz tartozó Traffic Manager Heat térképet, amely tartalmazza a lekérdezések számát és a késési időt a hely és a forrás IP-címe alapján. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Beágyazott végpont törlése egy meglévő Traffic Manager-profilból. Traffic Manager leállítja a törölt beágyazott végpontra irányuló útválasztási forgalmat. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Beolvas egy Traffic Manager profilhoz tartozó beágyazott végpontot, amely tartalmazza az adott beágyazott végpont összes tulajdonságát. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Adjon hozzá egy új beágyazott végpontot egy meglévő Traffic Manager profilhoz, vagy frissítse a meglévő beágyazott végpont tulajdonságait az adott Traffic Manager-profilban. |
> | Action | Microsoft.Network/trafficManagerProfiles/read | Traffic Manager profil konfigurációjának beolvasása. Ide tartozik a DNS-beállítások, a forgalmi útválasztási beállítások, a végpont-figyelési beállítások, valamint a Traffic Manager profil által irányított végpontok listája. |
> | Action | Microsoft.Network/trafficManagerProfiles/write | Hozzon létre egy Traffic Manager profilt, vagy módosítsa egy meglévő Traffic Manager-profil konfigurációját.<br>Ide tartozik a profilok engedélyezése vagy letiltása, valamint a DNS-beállítások, a forgalmi útválasztási beállítások vagy a végpont-figyelési beállítások módosítása.<br>A Traffic Manager profil által irányított végpontok hozzáadhatók, eltávolíthatók, engedélyezhetők vagy letilthatók. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Törli a valós idejű felhasználói metrikák gyűjteményéhez használt előfizetési szintű kulcsot. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/read | Lekérdezi a valós idejű felhasználói metrikák gyűjteményéhez használt előfizetési szintű kulcsot. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/write | Létrehoz egy új előfizetési szintű kulcsot, amelyet a rendszer a valós idejű felhasználói metrikák gyűjteményéhez használ. |
> | Action | Microsoft.Network/unregister/action | Előfizetés regisztrációjának törlése |
> | Action | Microsoft.Network/virtualHubs/delete | Virtuális központ törlése |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Deletes a HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | HubVirtualNetworkConnection létrehozása vagy frissítése |
> | Action | Microsoft.Network/virtualHubs/read | Virtuális központ beszerzése |
> | Action | Microsoft. Network/virtualHubs/Write | Virtuális központ létrehozása vagy frissítése |
> | Action | Microsoft. Network/virtualnetworkgateways/Connections/READ | Get VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/virtualNetworkGateways/delete | VirtualNetworkGateway törlése |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Ügyfele-csomag előállítása a virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | VpnProfile-csomag előállítása a VirtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/getadvertisedroutes/Action | Beolvassa a virtualNetworkGateway meghirdetett útvonalakat |
> | Action | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | VirtualNetworkGateway BGP-társ állapotának beolvasása |
> | Action | Microsoft. Network/virtualnetworkgateways/getlearnedroutes/Action | Virtualnetworkgateway megtanult útvonalak beolvasása |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | VPN-ügyfél kapcsolati állapotának beolvasása a VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | A VirtualNetworkGateway P2S-ügyfél ügyfele IPSec-paramétereinek beolvasása. |
> | Action | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Egy előre generált VPN Client profil csomag URL-címének beolvasása |
> | Action | Microsoft.Network/virtualNetworkGateways/read | VirtualNetworkGateway beolvasása |
> | Action | Microsoft. Network/virtualnetworkgateways/reset/Action | VirtualNetworkGateway alaphelyzetbe állítása |
> | Action | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | A VirtualNetworkGateway P2S-ügyfél ügyfele-alapú megosztott kulcsának alaphelyzetbe állítása. |
> | Action | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Ügyfele IPSec-paraméterek beállítása a VirtualNetworkGateway P2S-ügyfél számára. |
> | Action | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | A támogatott VPN-eszközök listája |
> | Action | Microsoft.Network/virtualNetworkGateways/write | VirtualNetworkGateway létrehozása vagy frissítése |
> | Action | Microsoft. Network/virtualNetworks/BastionHosts/Action | Beolvas egy Virtual Network. |
> | Action | Microsoft. Network/virtualNetworks/bastionHosts/default/művelet | Beolvas egy Virtual Network. |
> | Action | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Ellenőrizze, hogy az IP-cím elérhető-e a megadott virtuális hálózaton |
> | Action | Microsoft.Network/virtualNetworks/delete | Virtuális hálózat törlése |
> | Action | Microsoft. Network/virtualNetworks/csatlakozás/művelet | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Action | Microsoft. Network/virtualNetworks/peer/Action | Virtuális hálózat társítása egy másik virtuális hálózattal |
> | Action | Microsoft.Network/virtualNetworks/read | A virtuális hálózat definíciójának beolvasása |
> | Action | Microsoft.Network/virtualNetworks/subnets/delete | Virtuális hálózati alhálózat törlése |
> | Action | Microsoft.Network/virtualNetworks/subnets/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Összekapcsolja az erőforrást, például a Storage-fiókot vagy az SQL Database-t egy alhálózathoz. Nem riasztásos. |
> | Action | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Alhálózat előkészítése a szükséges hálózati házirendek alkalmazásával |
> | Action | Microsoft.Network/virtualNetworks/subnets/read | Virtuális hálózati alhálózat definíciójának beolvasása |
> | Action | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | A virtuális hálózati alhálózat összes virtuális gépre mutató hivatkozás beolvasása |
> | Action | Microsoft.Network/virtualNetworks/subnets/write | Létrehoz egy virtuális hálózati alhálózatot, vagy frissít egy meglévő virtuális hálózati alhálózatot |
> | Action | Microsoft. Network/virtualNetworks/használat/olvasás | A virtuális hálózat minden alhálózatához tartozó IP-használat beszerzése |
> | Action | Microsoft.Network/virtualNetworks/virtualMachines/read | A virtuális hálózatban lévő összes virtuális gépre mutató hivatkozásokat kap |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Virtuális hálózati társak törlése |
> | Action | Microsoft. Network/virtualNetworks/virtualNetworkPeerings/READ | Virtuális hálózat társítási definíciójának beolvasása |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Létrehoz egy virtuális hálózatot, vagy frissíti egy meglévő virtuális hálózati társat |
> | Action | Microsoft.Network/virtualNetworks/write | Virtuális hálózat létrehozása vagy meglévő virtuális hálózat frissítése |
> | Action | Microsoft.Network/virtualNetworkTaps/delete | Törlés Virtual Network koppintson |
> | Action | Microsoft.Network/virtualNetworkTaps/join/action | Csatlakozik egy virtuális hálózathoz. Nem Riasztásos. |
> | Action | Microsoft.Network/virtualNetworkTaps/read | Virtual Network koppintás beolvasása |
> | Action | Microsoft.Network/virtualNetworkTaps/write | Virtual Network létrehozása vagy frissítése koppintson |
> | Action | Microsoft.Network/virtualWans/delete | Virtuális WAN törlése |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Virtuális WAN-P2SVpnServerConfiguration törlése |
> | Action | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Virtuális WAN-P2SVpnServerConfiguration beolvasása |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Virtuális WAN-P2SVpnServerConfiguration létrehozása vagy meglévő virtuális WAN-P2SVpnServerConfiguration frissítése |
> | Action | Microsoft. Network/virtualWans/READ | Virtuális WAN beszerzése |
> | Action | Microsoft. Network/virtualwans/supportedSecurityProviders/READ | A VirtualWan biztonsági szolgáltatók támogatottak. |
> | Action | Microsoft.Network/virtualWans/virtualHubs/read | Minden virtuális WAN-ra hivatkozó virtuális hubok beolvasása. |
> | Action | Microsoft.Network/virtualwans/vpnconfiguration/action | VPN-konfiguráció beolvasása |
> | Action | Microsoft.Network/virtualWans/vpnSites/read | A virtuális WAN-ra hivatkozó összes VPN-hely beolvasása. |
> | Action | Microsoft. Network/virtualWans/Write | Virtuális WAN létrehozása vagy frissítése |
> | Action | Microsoft.Network/vpnGateways/delete | Töröl egy átjáróban. |
> | Action | microsoft.network/vpngateways/listvpnconnectionshealth/action | A kapcsolati állapot beolvasása a átjáróban lévő kapcsolatok teljes vagy egy részhalmazára |
> | Action | Microsoft. Network/vpnGateways/READ | Átjáróban beolvasása. |
> | Action | microsoft.network/vpngateways/reset/action | Átjáróban alaphelyzetbe állítása |
> | Action | microsoft.network/vpnGateways/vpnConnections/delete | Töröl egy VpnConnection. |
> | Action | microsoft.network/vpnGateways/vpnConnections/read | VpnConnection beolvasása. |
> | Action | microsoft.network/vpnGateways/vpnConnections/write | Egy VpnConnection helyez el. |
> | Action | Microsoft.Network/vpnGateways/write | Egy átjáróban helyez el. |
> | Action | Microsoft.Network/vpnsites/delete | Törli a VPN-hely erőforrását. |
> | Action | Microsoft.Network/vpnsites/read | Egy VPN-hely erőforrásának beolvasása. |
> | Action | Microsoft.Network/vpnsites/write | Egy VPN-hely erőforrásának létrehozása vagy frissítése. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Adott névtér-erőforrásnév elérhetőségének ellenőrzése a Notification Hub szolgáltatásban. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Action | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Action | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/olvasás | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Egy névtér-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Action | Microsoft. NotificationHubs/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Action | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Adott értesítésiközpont-név elérhetőségének ellenőrzése egy névtérben. |
> | Action | Microsoft.NotificationHubs/Namespaces/Delete | Egy névtérerőforrás törlése |
> | Action | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/művelet | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Értesítésiközpont-engedélyezési szabályok törlése |
> | Action | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | Az értesítési központhoz tartozó kapcsolati sztring beolvasása |
> | Action | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/READ | Az értesítésiközpont-engedélyezési szabályok listájának beolvasása |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Egy értesítésiközpont-engedélyezési szabály elsődleges vagy másodlagos kulcsának újragenerálása; meg kell adni az újragenerálni kívánt kulcsot |
> | Action | Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok/Write | Hozzon létre értesítési központ engedélyezési szabályait, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Leküldéses tesztértesítés küldése. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Értesítésiközpont-erőforrás törlése |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Az összes értesítési központ PNS hitelesítő adatainak beolvasása. Ide tartozik a WNS, a MPNS, a APNS, a GCM és a Baidu hitelesítő adatai. |
> | Action | Microsoft. NotificationHubs/névterek/NotificationHubs/olvasás | Az értesítésiközpont-erőforrások leírásai listájának beolvasása |
> | Action | Microsoft. NotificationHubs/névterek/NotificationHubs/írás | Hozzon létre egy értesítési központot, és frissítse a tulajdonságait. A tulajdonságai főleg PNS hitelesítő adatokkal rendelkeznek. Engedélyezési szabályok és TTL |
> | Action | Microsoft. NotificationHubs/névterek/olvasás | A névtérerőforrások leírásai listájának beolvasása |
> | Action | Microsoft. NotificationHubs/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Action | Microsoft.NotificationHubs/operationResults/read | A Notification Hubs-szolgáltató által végrehajtott művelet eredményeinek visszaadása |
> | Action | Microsoft.NotificationHubs/operations/read | A Notification Hubs-szolgáltató által támogatott műveletek listájának visszaadása |
> | Action | Microsoft.NotificationHubs/register/action | Regisztrálja az előfizetést a NotificationHubs erőforrás-szolgáltatóhoz, és lehetővé teszi a névterek és NotificationHubs létrehozását |
> | Action | Microsoft.NotificationHubs/unregister/action | A NotificationHubs erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának megszüntetése, és a névterek és NotificationHubs létrehozásának engedélyezése |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. OffAzure/HyperVSites/fürtök/olvasás | Egy Hyper-V fürt tulajdonságainak beolvasása |
> | Action | Microsoft. OffAzure/HyperVSites/fürtök/írás | A Hyper-V fürt létrehozása vagy frissítése |
> | Action | Microsoft.OffAzure/HyperVSites/delete | A Hyper-V hely törlése |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | Egy Hyper-V-gazdagép tulajdonságainak beolvasása |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | A Hyper-V-gazdagép létrehozása vagy frissítése |
> | Action | Microsoft. OffAzure/HyperVSites/feladatok/olvasás | A Hyper-V-feladatok tulajdonságainak beolvasása |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/READ | A Hyper-V gépek tulajdonságainak beolvasása |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/Start/Action | Hyper-V gépek elindítása |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/leállítás/művelet | A Hyper-V gépek leállítása |
> | Action | Microsoft. OffAzure/HyperVSites/operationsstatus/READ | Egy Hyper-V műveleti állapot tulajdonságainak beolvasása |
> | Action | Microsoft. OffAzure/HyperVSites/READ | Egy Hyper-V-hely tulajdonságainak beolvasása |
> | Action | Microsoft. OffAzure/HyperVSites/frissítés/művelet | A Hyper-V-helyen lévő objektumok frissítése |
> | Action | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hyper-V futtató fiókok tulajdonságainak beolvasása |
> | Action | Microsoft. OffAzure/HyperVSites/használat/olvasás | Egy Hyper-V webhely használatának beolvasása |
> | Action | Microsoft. OffAzure/HyperVSites/Write | A Hyper-V-hely létrehozása vagy frissítése |
> | Action | Microsoft. OffAzure/Operations/READ | Beolvassa a feltett műveleteket |
> | Action | Microsoft.OffAzure/register/action | Előfizetés regisztrálása a Microsoft. OffAzure erőforrás-szolgáltatónál |
> | Action | Microsoft.OffAzure/VMwareSites/delete | A VMware-hely törlése |
> | Action | Microsoft.OffAzure/VMwareSites/jobs/read | VMware-feladatok tulajdonságainak beolvasása |
> | Action | Microsoft.OffAzure/VMwareSites/machines/read | VMware-gépek tulajdonságainak beolvasása |
> | Action | Microsoft.OffAzure/VMwareSites/machines/start/action | VMware-gépek elindítása |
> | Action | Microsoft.OffAzure/VMwareSites/machines/stop/action | Leállítja a VMware-gépeket |
> | Action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Egy VMware-műveleti állapot tulajdonságainak beolvasása |
> | Action | Microsoft.OffAzure/VMwareSites/read | Egy VMware-hely tulajdonságainak beolvasása |
> | Action | Microsoft.OffAzure/VMwareSites/refresh/action | Egy VMware-helyen található objektumok frissítése |
> | Action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | VMware futtató fiókok tulajdonságainak beolvasása |
> | Action | Microsoft.OffAzure/VMwareSites/usage/read | VMware-webhely használatának beolvasása |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | Egy VMware-vCenter tulajdonságainak beolvasása |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | A VMware-vCenter létrehozása vagy frissítése |
> | Action | Microsoft.OffAzure/VMwareSites/write | A VMware-hely létrehozása vagy frissítése |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.OperationalInsights/linkTargets/read | Felsorolja azokat a meglévő fiókokat, amelyek nincsenek Azure-előfizetéshez társítva. Ha ezt az Azure-előfizetést egy munkaterülethez szeretné kapcsolni, használja a művelet által visszaadott ügyfél-azonosítót a munkaterület létrehozása művelet ügyfél-azonosító tulajdonságában. |
> | Action | microsoft.operationalinsights/operations/read | Felsorolja az összes rendelkezésre álló OperationalInsights REST API-műveletet. |
> | Action | microsoft.operationalinsights/register/action | Rergisters az előfizetést. |
> | Action | Microsoft.OperationalInsights/register/action | Előfizetés regisztrálása erőforrás-szolgáltatóhoz. |
> | Action | microsoft.operationalinsights/unregister/action | Az előfizetés regisztrációjának törlése. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/action | Keresés az új motor használatával. |
> | Action | Microsoft. OperationalInsights/munkaterületek/elemzés/lekérdezés/séma/olvasás | Keresési séma v2-es verziójának beolvasása. |
> | Action | Microsoft. OperationalInsights/munkaterületek/API/lekérdezés/művelet | Keresés az új motor használatával. |
> | Action | Microsoft. OperationalInsights/munkaterületek/API/lekérdezés/séma/olvasás | Keresési séma v2-es verziójának beolvasása. |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Konfigurációs hatókör törlése |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Konfiguráció hatókörének beolvasása |
> | Action | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Konfigurációs hatókör beállítása |
> | Action | Microsoft.OperationalInsights/workspaces/datasources/delete | Munkaterületen lévő adatforrások törlése. |
> | Action | Microsoft. OperationalInsights/munkaterületek/adatforrások/olvasás | Munkaterületen lévő adatforrások beolvasása. |
> | Action | Microsoft. OperationalInsights/munkaterületek/adatforrások/írás | Adatforrások létrehozása/frissítése egy munkaterület alatt. |
> | Action | Microsoft. OperationalInsights/munkaterületek/törlés | Töröl egy munkaterületet. Ha a munkaterületet egy meglévő munkaterülethez csatolták a létrehozáskor, akkor az ahhoz csatolt munkaterületet nem törli a rendszer. |
> | Action | Microsoft. OperationalInsights/munkaterületek/átjárók/törlés | Eltávolítja a munkaterülethez konfigurált átjárót. |
> | Action | Microsoft. OperationalInsights/munkaterületek/generateregistrationcertificate/művelet | Létrehoz egy regisztrációs tanúsítványt a munkaterülethez. Ez a tanúsítvány a Microsoft System Center Operation Manager és a munkaterület összekapcsolására szolgál. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Letiltja az adott munkaterülethez tartozó intelligencia-csomagokat. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Egy adott munkaterülethez tartozó intelligencia-csomag engedélyezése. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Felsorolja az adott munkaterület számára látható összes intelligenciát, valamint azt is, hogy a csomag engedélyezve van-e vagy le van-e tiltva az adott munkaterületen. |
> | Action | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Társított szolgáltatások törlése az adott munkaterületen. |
> | Action | Microsoft. OperationalInsights/munkaterületek/linkedServices/olvasás | Társított szolgáltatások beolvasása az adott munkaterületen. |
> | Action | Microsoft. OperationalInsights/munkaterületek/linkedServices/írás | Társított szolgáltatások létrehozása/frissítése az adott munkaterületen. |
> | Action | Microsoft. OperationalInsights/munkaterületek/Listkeys műveletének beolvasása/művelet | Lekéri a munkaterület listájának kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Action | Microsoft. OperationalInsights/munkaterületek/Listkeys műveletének beolvasása/olvasás | Lekéri a munkaterület listájának kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Action | Microsoft. OperationalInsights/munkaterületek/managementGroups/olvasás | Beolvassa a munkaterülethez kapcsolódó System Center Operations Manager felügyeleti csoportok nevét és metaadatait. |
> | Action | Microsoft. OperationalInsights/munkaterületek/metricDefinitions/olvasás | Metrika-definíciók beolvasása a munkaterület területen |
> | Action | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | A felhasználó értesítési beállításainak törlése a munkaterületen. |
> | Action | Microsoft. OperationalInsights/munkaterületek/notificationSettings/olvasás | A felhasználó értesítési beállításainak beolvasása a munkaterülethez. |
> | Action | Microsoft. OperationalInsights/munkaterületek/notificationSettings/írás | Adja meg a felhasználó értesítési beállításait a munkaterülethez. |
> | Action | Microsoft. operationalinsights/munkaterületek/műveletek/olvasás | Egy OperationalInsights-munkaterület művelet állapotának beolvasása. |
> | Action | Microsoft. OperationalInsights/munkaterületek/kiürítés/művelet | Megadott adatok törlése a munkaterületről |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesAccountLogon/olvasás | Adatok beolvasása a AADDomainServicesAccountLogon táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Adatok beolvasása a AADDomainServicesAccountManagement táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Adatok beolvasása a AADDomainServicesDirectoryServiceAccess táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesLogonLogoff/olvasás | Adatok beolvasása a AADDomainServicesLogonLogoff táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AADDomainServicesPolicyChange/olvasás | Adatok beolvasása a AADDomainServicesPolicyChange táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Adatok beolvasása a AADDomainServicesPrivilegeUse táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Adatok beolvasása a AADDomainServicesSystemSecurity táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Adatok olvasása az ADAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Adatok beolvasása a ADFActivityRun táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Adatok beolvasása a ADFPipelineRun táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Adatok beolvasása a ADFTriggerRun táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Adatok olvasása az ADReplicationResult táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Adatok olvasása az ADSecurityAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/Alert/read | Adatok olvasása az Alert táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Adatok olvasása az AlertHistory táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Adatok beolvasása a AppCenterError táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Adatok olvasása az ApplicationInsights táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AuditLogs/olvasás | Adatok beolvasása a AuditLogs táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/AutoscaleEvaluationsLog/olvasás | Adatok beolvasása a AutoscaleEvaluationsLog táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Adatok beolvasása a AutoscaleScaleActionsLog táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Adatok beolvasása a AWSCloudTrail táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Adatok olvasása az AzureActivity táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Adatok beolvasása a AzureAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Adatok olvasása az AzureMetrics táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BlockchainApplicationLog/olvasás | Adatok beolvasása a BlockchainApplicationLog táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/BlockchainProxyLog/olvasás | Adatok beolvasása a BlockchainProxyLog táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Adatok olvasása a BoundPort táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Adatok olvasása a CommonSecurityLog táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Adatok olvasása a ComputerGroup táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/konfigurációváltozás/olvasás | Adatok olvasása a ConfigurationChange táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Adatok olvasása a ConfigurationData táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Adatok olvasása a ContainerImageInventory táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Adatok olvasása a ContainerInventory táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Adatok olvasása a ContainerLog táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Adatok beolvasása a ContainerNodeInventory táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Adatok olvasása a ContainerServiceLog táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksAccounts/olvasás | Adatok beolvasása a DatabricksAccounts táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksClusters/olvasás | Adatok beolvasása a DatabricksClusters táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksDBFS/olvasás | Adatok beolvasása a DatabricksDBFS táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksJobs/olvasás | Adatok beolvasása a DatabricksJobs táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksNotebook/olvasás | Adatok beolvasása a DatabricksNotebook táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksSecrets/olvasás | Adatok beolvasása a DatabricksSecrets táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Adatok beolvasása a DatabricksSQLPermissions táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksSSH/olvasás | Adatok beolvasása a DatabricksSSH táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksTables/olvasás | Adatok beolvasása a DatabricksTables táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DatabricksWorkspace/olvasás | Adatok beolvasása a DatabricksWorkspace táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Adatok olvasása a DeviceAppCrash táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Adatok olvasása a DeviceAppLaunch táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceCalendar/olvasás | Adatok olvasása a DeviceCalendar táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceCleanup/olvasás | Adatok olvasása a DeviceCleanup táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Adatok olvasása a DeviceConnectSession táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceEtw/olvasás | Adatok olvasása a DeviceEtw táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Adatok olvasása a DeviceHardwareHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Adatok olvasása a DeviceHealth táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceHeartbeat/olvasás | Adatok olvasása a DeviceHeartbeat táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceSkypeHeartbeat/olvasás | Adatok olvasása a DeviceSkypeHeartbeat táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Adatok olvasása a DeviceSkypeSignIn táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DeviceSleepState/olvasás | Adatok olvasása a DeviceSleepState táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHAppFailure/olvasás | Adatok olvasása a DHAppFailure táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Adatok olvasása a DHAppReliability táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHDriverReliability/olvasás | Adatok olvasása a DHDriverReliability táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHLogonFailures/olvasás | Adatok olvasása a DHLogonFailures táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHLogonMetrics/olvasás | Adatok olvasása a DHLogonMetrics táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHOSCrashData/olvasás | Adatok olvasása a DHOSCrashData táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHOSReliability/olvasás | Adatok olvasása a DHOSReliability táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DHWipAppLearning/olvasás | Adatok olvasása a DHWipAppLearning táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DnsEvents/olvasás | Adatok olvasása a DnsEvents táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/DnsInventory/olvasás | Adatok olvasása a DnsInventory táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ETWEvent/olvasás | Adatok olvasása az ETWEvent táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/esemény/olvasás | Adatok olvasása az Event táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Adatok olvasása az ExchangeAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Adatok olvasása az ExchangeOnlineAssessmentRecommendation táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/szívverés/olvasás | Adatok olvasása a Heartbeat táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/HuntingBookmark/olvasás | Adatok beolvasása a HuntingBookmark táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Adatok olvasása az IISAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Adatok olvasása az InboundConnection táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/InsightsMetrics/olvasás | Adatok beolvasása a InsightsMetrics táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Adatok beolvasása a IntuneAuditLogs táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Adatok beolvasása a IntuneOperationalLogs táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeEvents/olvasás | Adatok beolvasása a KubeEvents táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeNodeInventory/olvasás | Adatok olvasása a KubeNodeInventory táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubePodInventory/olvasás | Adatok olvasása a KubePodInventory táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/KubeServices/olvasás | Adatok beolvasása a KubeServices táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/LinuxAuditLog/olvasás | Adatok olvasása a LinuxAuditLog táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplication/olvasás | Adatok olvasása az MAApplication táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Adatok olvasása az MAApplicationHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Adatok olvasása az MAApplicationHealthAlternativeVersions táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Adatok olvasása az MAApplicationHealthIssues táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Adatok olvasása az MAApplicationInstance táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Adatok olvasása az MAApplicationInstanceReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAApplicationReadiness/olvasás | Adatok olvasása az MAApplicationReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeploymentPlan/olvasás | Adatok olvasása az MADeploymentPlan táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADevice/olvasás | Adatok olvasása az MADevice táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeviceNotEnrolled/olvasás | Adatok beolvasása a MADeviceNotEnrolled táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Adatok beolvasása a MADeviceNRT táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Adatok olvasása az MADevicePnPHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Adatok olvasása az MADevicePnPHealthAlternativeVersions táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Adatok olvasása az MADevicePnPHealthIssues táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADeviceReadiness/olvasás | Adatok olvasása az MADeviceReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADriverInstanceReadiness/olvasás | Adatok olvasása az MADriverInstanceReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MADriverReadiness/olvasás | Adatok olvasása az MADriverReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeAddin/olvasás | Adatok olvasása az MAOfficeAddin táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Adatok beolvasása a MAOfficeAddinEntityHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Adatok olvasása az MAOfficeAddinHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Adatok beolvasása a MAOfficeAddinHealthEventNRT táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Adatok olvasása az MAOfficeAddinHealthIssues táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Adatok olvasása az MAOfficeAddinInstance táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Adatok olvasása az MAOfficeAddinInstanceReadiness táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Adatok olvasása az MAOfficeAddinReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeApp/olvasás | Adatok olvasása az MAOfficeApp táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Adatok beolvasása a MAOfficeAppCrashesNRT táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Adatok olvasása az MAOfficeAppHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Adatok olvasása az MAOfficeAppInstance táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Adatok beolvasása a MAOfficeAppInstanceHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Adatok olvasása az MAOfficeAppReadiness táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Adatok beolvasása a MAOfficeAppSessionsNRT táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Adatok olvasása az MAOfficeBuildInfo táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeCurrencyAssessment/olvasás | Adatok olvasása az MAOfficeCurrencyAssessment táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeCurrencyAssessmentDailyCounts/olvasás | Adatok olvasása az MAOfficeCurrencyAssessmentDailyCounts táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Adatok olvasása az MAOfficeDeploymentStatus táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Adatok beolvasása a MAOfficeDeploymentStatusNRT táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Adatok beolvasása a MAOfficeMacroErrorNRT táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroGlobalHealth/olvasás | Adatok beolvasása a MAOfficeMacroGlobalHealth táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroHealth/olvasás | Adatok olvasása az MAOfficeMacroHealth táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Adatok olvasása az MAOfficeMacroHealthIssues táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroIssueInstanceReadiness/olvasás | Adatok olvasása az MAOfficeMacroIssueInstanceReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroIssueReadiness/olvasás | Adatok olvasása az MAOfficeMacroIssueReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeMacroSummary/olvasás | Adatok olvasása az MAOfficeMacroSummary táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAOfficeSuite/olvasás | Adatok olvasása az MAOfficeSuite táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Adatok olvasása az MAOfficeSuiteInstance táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAProposedPilotDevices/olvasás | Adatok olvasása az MAProposedPilotDevices táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Adatok olvasása az MAWindowsBuildInfo táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsCurrencyAssessment/olvasás | Adatok olvasása az MAWindowsCurrencyAssessment táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MAWindowsCurrencyAssessmentDailyCounts/olvasás | Adatok olvasása az MAWindowsCurrencyAssessmentDailyCounts táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Adatok olvasása az MAWindowsDeploymentStatus táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Adatok beolvasása a MAWindowsDeploymentStatusNRT táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Adatok olvasása az MAWindowsSysReqInstanceReadiness táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftWebApplicationLog/olvasás | Adatok beolvasása a MicrosoftWebApplicationLog táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftWebFunctionExecutionLogs/olvasás | Adatok beolvasása a MicrosoftWebFunctionExecutionLogs táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Adatok beolvasása a MicrosoftWebStdOutStdErrLog táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/MicrosoftWebW3CLog/olvasás | Adatok beolvasása a MicrosoftWebW3CLog táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/NetworkMonitoring/olvasás | Adatok olvasása a NetworkMonitoring táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/OfficeActivity/olvasás | Adatok olvasása az OfficeActivity táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/művelet/olvasás | Adatok olvasása az Operation táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/OutboundConnection/olvasás | Adatok olvasása az OutboundConnection táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/Perf/olvasás | Adatok olvasása a Perf táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Adatok olvasása a ProtectionStatus táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/olvasás | Lekérdezések futtatása a munkaterület összes adatán |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Adatok olvasása a ReservedAzureCommonFields táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Adatok olvasása a ReservedCommonFields táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Adatok olvasása az SCCMAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Adatok olvasása az SCOMAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Adatok olvasása a SecurityAlert táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Adatok olvasása a SecurityBaseline táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Adatok olvasása a SecurityBaselineSummary táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Adatok olvasása a SecurityDetection táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Adatok olvasása a SecurityEvent táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Adatok beolvasása a SecurityIoTRawEvent táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Adatok beolvasása a SecurityRecommendation táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/ServiceFabricOperationalEvent/olvasás | Adatok olvasása a ServiceFabricOperationalEvent táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Adatok olvasása a ServiceFabricReliableActorEvent táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Adatok olvasása a ServiceFabricReliableServiceEvent táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Adatok olvasása az SfBAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Adatok olvasása az SfBOnlineAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Adatok olvasása a SharePointOnlineAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Adatok beolvasása a SigninLogs táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Adatok olvasása az SPAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Adatok olvasása az SQLAssessmentRecommendation táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Adatok olvasása az SQLQueryPerformance táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Adatok beolvasása a SqlThreatProtectionLoginAudits táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Adatok beolvasása a SqlVulnerabilityAssessmentResult táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/syslog/olvasás | Adatok olvasása a Syslog táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/SysmonEvent/olvasás | Adatok olvasása a SysmonEvent táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Adatok olvasása bármelyik egyéni naplóból |
> | Action | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Adatok beolvasása a ThreatIntelligenceIndicator táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAApp/olvasás | Adatok olvasása az UAApp táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAComputer/olvasás | Adatok olvasása az UAComputer táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAComputerRank/olvasás | Adatok olvasása az UAComputerRank táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UADriver/olvasás | Adatok olvasása az UADriver táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UADriverProblemCodes/olvasás | Adatok olvasása az UADriverProblemCodes táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAFeedback/olvasás | Adatok olvasása az UAFeedback táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAHardwareSecurity/olvasás | Adatok olvasása az UAHardwareSecurity táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAIESiteDiscovery/olvasás | Adatok olvasása az UAIESiteDiscovery táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Adatok olvasása az UAOfficeAddIn táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAProposedActionPlan/olvasás | Adatok olvasása az UAProposedActionPlan táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UASysReqIssue/olvasás | Adatok olvasása az UASysReqIssue táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UAUpgradedComputer/olvasás | Adatok olvasása az UAUpgradedComputer táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/frissítés/olvasás | Adatok olvasása az Update táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/UpdateRunProgress/olvasás | Adatok olvasása az UpdateRunProgress táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/updateSummary típusú/olvasás | Adatok olvasása az UpdateSummary táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/használat/olvasás | Adatok olvasása a Usage táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMBoundPort/olvasás | Adatok beolvasása a VMBoundPort táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/VMConnection/olvasás | Adatok beolvasása a VMConnection táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Adatok olvasása a W3CIISLog táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Adatok olvasása a WaaSDeploymentStatus táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Adatok olvasása a WaaSInsiderStatus táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Adatok olvasása a WaaSUpdateStatus táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Adatok olvasása a WDAVStatus táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WDAVThreat/olvasás | Adatok olvasása a WDAVThreat táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Adatok olvasása a WindowsClientAssessmentRecommendation táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsEvent/olvasás | Adatok beolvasása a WindowsEvent táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WindowsFirewall/olvasás | Adatok olvasása a WindowsFirewall táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Adatok olvasása a WindowsServerAssessmentRecommendation táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WireData/olvasás | Adatok olvasása a WireData táblából |
> | Action | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Adatok beolvasása a WorkloadMonitoringPerf táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WUDOAggregatedStatus/olvasás | Adatok olvasása a WUDOAggregatedStatus táblából |
> | Action | Microsoft. OperationalInsights/munkaterületek/lekérdezés/WUDOStatus/olvasás | Adatok olvasása a WUDOStatus táblából |
> | Action | Microsoft.OperationalInsights/workspaces/read | Meglévő munkaterület beolvasása |
> | Action | Microsoft. OperationalInsights/munkaterületek/regeneratesharedkey/művelet | A megadott munkaterület megosztott kulcsának újralétrehozása |
> | Action | microsoft.operationalinsights/workspaces/rules/read | Az összes riasztási szabály beolvasása. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Töröl egy mentett keresési lekérdezést |
> | Action | Microsoft. OperationalInsights/munkaterületek/savedSearches/olvasás | Mentett keresési lekérdezés beolvasása |
> | Action | Microsoft. operationalinsights/munkaterületek/savedsearches/eredmények/olvasás | Mentett keresések eredményeinek beolvasása. Elavult |
> | Action | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/törlés | Ütemezett keresési műveletek törlése. |
> | Action | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/olvasás | Ütemezett keresési műveletek beolvasása. |
> | Action | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/műveletek/írás | Ütemezett keresési műveletek létrehozása vagy frissítése. |
> | Action | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/törlés | Ütemezett keresések törlése. |
> | Action | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemtervek/olvasás | Ütemezett keresések beolvasása. |
> | Action | Microsoft. operationalinsights/munkaterületek/savedsearches/ütemterv/írás | Ütemezett keresések létrehozása vagy frissítése. |
> | Action | Microsoft. OperationalInsights/munkaterületek/savedSearches/írás | Mentett keresési lekérdezés létrehozása |
> | Action | Microsoft. OperationalInsights/munkaterületek/séma/olvasás | Lekéri a munkaterület keresési sémáját.  A keresési séma tartalmazza a kitett mezőket és azok típusait. |
> | Action | Microsoft.OperationalInsights/workspaces/search/action | Keresési lekérdezés végrehajtása |
> | Action | Microsoft. operationalinsights/munkaterületek/keresés/olvasás | Keresési eredmények beolvasása. Elavult. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Lekéri a munkaterület megosztott kulcsait. Ezek a kulcsok a Microsoft Operational Insights-ügynökök munkaterülethez való összekapcsolására szolgálnak. |
> | Action | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Törli a tárolási konfigurációt. Ezzel leállítja a Microsoft Operational Insights az adatoknak a Storage-fiókból való beolvasását. |
> | Action | Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/olvasás | Egy tárolási konfiguráció beolvasása. |
> | Action | Microsoft. OperationalInsights/munkaterületek/storageinsightconfigs/írás | Új tárolási konfiguráció létrehozása. Ezek a konfigurációk egy meglévő Storage-fiókban lévő hely adatainak lekérésére szolgálnak. |
> | Action | Microsoft. OperationalInsights/munkaterületek/upgradetranslationfailures/olvasás | A keresés a munkaterületre vonatkozó verziófrissítési sikertelen fordítási naplójának beolvasása |
> | Action | Microsoft. OperationalInsights/munkaterületek/használati adatok/olvasás | Lekéri egy munkaterület használati adatait, beleértve a munkaterület által olvasott adatok mennyiségét. |
> | Action | Microsoft.OperationalInsights/workspaces/write | Létrehoz egy új munkaterületet vagy egy meglévő munkaterületre mutató hivatkozásokat úgy, hogy megadja az ügyfél azonosítóját a meglévő munkaterületen. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.OperationsManagement/managementAssociations/delete | Meglévő felügyeleti társítás törlése |
> | Action | Microsoft.OperationsManagement/managementAssociations/read | Meglévő felügyeleti társítás beolvasása |
> | Action | Microsoft.OperationsManagement/managementAssociations/write | Új felügyeleti társítás létrehozása |
> | Action | Microsoft.OperationsManagement/managementConfigurations/delete | Meglévő felügyeleti konfiguráció törlése |
> | Action | Microsoft.OperationsManagement/managementConfigurations/read | Meglévő felügyeleti konfiguráció beolvasása |
> | Action | Microsoft.OperationsManagement/managementConfigurations/write | Új felügyeleti konfiguráció létrehozása |
> | Action | Microsoft.OperationsManagement/register/action | Előfizetés regisztrálása erőforrás-szolgáltatóhoz. |
> | Action | Microsoft.OperationsManagement/solutions/delete | Meglévő OMS-megoldás törlése |
> | Action | Microsoft.OperationsManagement/solutions/read | OMS-megoldás beolvasása |
> | Action | Microsoft.OperationsManagement/solutions/write | Új OMS-megoldás létrehozása |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.PolicyInsights/asyncOperationResults/read | Lekérdezi az aszinkron művelet eredményét. |
> | Action | Microsoft.PolicyInsights/operations/read | A Microsoft. PolicyInsights névtérben támogatott műveletek beolvasása |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/action | A szabályzateseményekkel kapcsolatos adatok lekérdezése. |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/read | A szabályzateseményekkel kapcsolatos adatok lekérdezése. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/action | A szabályzat állapotaival kapcsolatos adatok lekérdezése. |
> | Action | Microsoft. PolicyInsights/policyStates/queryResults/READ | A szabályzat állapotaival kapcsolatos adatok lekérdezése. |
> | Action | Microsoft. PolicyInsights/policyStates/összefoglalás/művelet | A szabályzat legutóbbi állapotait tartalmazó összegzés lekérdezése. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/read | A szabályzat legutóbbi állapotait tartalmazó összegzés lekérdezése. |
> | Action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Elindít egy új megfelelőségi kiértékelést a kiválasztott hatókörre. |
> | Action | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | A DeployIfNotExists szabályzatokhoz szükséges erőforrásokkal kapcsolatos adatok lekérdezése. |
> | Action | Microsoft.PolicyInsights/register/action | Regisztrálja a Microsoft Policy bepillantást biztosító erőforrás-szolgáltatót, és engedélyezi a műveleteket. |
> | Action | Microsoft. PolicyInsights/szervizelés/megszakítás/művelet | A folyamatban lévő Microsoft-szabályzatok szervizelésének megszakítása. |
> | Action | Microsoft.PolicyInsights/remediations/delete | Szabályzatszervizelések törlése. |
> | Action | Microsoft.PolicyInsights/remediations/listDeployments/read | Egy szabályzatszervizeléshez szükséges telepítések listázása. |
> | Action | Microsoft. PolicyInsights/szervizelés/olvasás | Szabályzatszervizelések beolvasása. |
> | Action | Microsoft. PolicyInsights/szervizelés/írás | Microsoft Policy szervizelések létrehozása vagy frissítése. |
> | Action | Microsoft.PolicyInsights/unregister/action | A Microsoft Policy-erőforrás-szolgáltató regisztrációjának törlése. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. Portal/konzolok/törlés | A Cloud Shell-példány eltávolítása. |
> | Action | Microsoft. Portal/konzolok/olvasás | A Cloud Shell-példány beolvasása. |
> | Action | Microsoft. Portal/konzolok/írás | Egy Cloud Shell-példány létrehozása vagy frissítése. |
> | Action | Microsoft. Portal/irányítópultok/törlés | Az irányítópult eltávolítása az előfizetésből. |
> | Action | Microsoft. Portal/irányítópultok/olvasás | Az előfizetéshez tartozó irányítópultok beolvasása. |
> | Action | Microsoft. Portal/irányítópultok/írás | Irányítópult felvétele vagy átalakítása előfizetéssé. |
> | Action | Microsoft.Portal/register/action | Regisztrálás portálon |
> | Action | Microsoft.Portal/usersettings/delete | A Cloud Shell felhasználói beállításainak eltávolítása. |
> | Action | Microsoft.Portal/usersettings/read | A Cloud Shell felhasználói beállításainak beolvasása. |
> | Action | Microsoft. Portal/usersettings/írás | Egy Cloud Shell-felhasználói beállítás létrehozása vagy frissítése. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.PowerBIDedicated/capacities/delete | Törli a Power BI dedikált kapacitást. |
> | Action | Microsoft.PowerBIDedicated/capacities/read | A megadott Power BI dedikált kapacitás információinak beolvasása. |
> | Action | Microsoft.PowerBIDedicated/capacities/resume/action | Folytatja a kapacitást. |
> | Action | Microsoft.PowerBIDedicated/capacities/skus/read | Rendelkezésre álló SKU-információk beolvasása a kapacitáshoz |
> | Action | Microsoft.PowerBIDedicated/capacities/suspend/action | Felfüggeszti a kapacitást. |
> | Action | Microsoft.PowerBIDedicated/capacities/write | Létrehozza vagy frissíti a megadott Power BI dedikált kapacitást. |
> | Action | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Ellenőrzi, hogy a megadott Power BI dedikált kapacitás neve érvényes-e, és nincs-e használatban. |
> | Action | Microsoft.PowerBIDedicated/locations/operationresults/read | A megadott műveleti eredmény információinak beolvasása. |
> | Action | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Lekéri a megadott műveleti állapot adatait. |
> | Action | Microsoft.PowerBIDedicated/operations/read | A műveletek információinak lekérése |
> | Action | Microsoft.PowerBIDedicated/register/action | Regisztrálja Power BI dedikált erőforrás-szolgáltatót. |
> | Action | Microsoft.PowerBIDedicated/skus/read | A SKU-ra vonatkozó információk beolvasása |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.RecoveryServices/locations/allocatedStamp/read | A Lefoglalt bélyegző beolvasása egy belső művelet, melyet a szolgáltatás használ |
> | Action | Microsoft.RecoveryServices/locations/allocateStamp/action | A AllocateStamp a szolgáltatás által használt belső művelet |
> | Action | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItem/write | Védett elemek biztonsági másolatának létrehozása |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Action | microsoft.recoveryservices/Locations/backupStatus/action | Recovery Services-tárolók biztonsági mentési állapotának keresése |
> | Action | microsoft.recoveryservices/Locations/backupValidateFeatures/action | Funkciók ellenőrzése |
> | Action | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Ellenőrizze, hogy az erőforrás neve rendelkezésre állása egy API-e, hogy elérhető-e az erőforrás neve |
> | Action | Microsoft.RecoveryServices/locations/operationStatus/read | Egy adott művelet műveleti állapotának beolvasása |
> | Action | Microsoft. Recoveryservices szolgáltatónál/Operations/READ | A művelet egy erőforrás-szolgáltató műveleteinek listáját adja vissza. |
> | Action | Microsoft.RecoveryServices/register/action | Az adott erőforrás-szolgáltatóhoz tartozó előfizetés regisztrálása |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vault/backupconfig/READ | Recovery Services-tároló konfigurációjának visszaadása. |
> | Action | microsoft.recoveryservices/Vaults/backupconfig/write | Recovery Services tár konfigurációjának frissítése. |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vault/backupEngines/READ | A tárolóban regisztrált biztonsági másolatokat kezelő kiszolgálókat adja vissza. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | Biztonsági másolat védelmi leképezésének törlése |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | Biztonsági másolat védelmi szándékának beolvasása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | Biztonsági másolat védelmi leképezésének létrehozása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | A művelet állapotának visszaadása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | Az összes védhető tároló beolvasása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | A regisztrált tároló törlése |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | A tárolóban lévő számítási feladatok lekérdezése |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | Tároló összes elemének beolvasása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | Beolvassa a védelmi tárolón végrehajtott művelet eredményét. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Biztonsági másolat készítése egy védett elemről. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Védett elemek törlése |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Beolvassa a védett elemeken végrehajtott művelet eredményét. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | A védett elemeken végrehajtott művelet állapotát adja vissza. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | A védett tétel objektumának részleteit adja vissza. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Azonnali elemek helyreállításának kiépítése védett elemek esetén |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Védett elemek helyreállítási pontjainak beolvasása. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Védett elemek helyreállítási pontjainak visszaállítása. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | A védett elemek azonnali elemek helyreállításának visszavonása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Védett elemek biztonsági másolatának létrehozása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | Az összes regisztrált tároló visszaadása |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | Létrehoz egy regisztrált tárolót |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | Frissíti a tárolók listáját |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vaults/backupJobs/megszakítás/művelet | Feladat megszakítása |
> | Action | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | A feladatművelet eredményét adja vissza. |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vault/backupJobs/READ | Az összes feladatütemezés visszaadása |
> | Action | microsoft.recoveryservices/Vaults/backupJobsExport/action | Feladatok exportálása |
> | Action | microsoft.recoveryservices/Vaults/backupOperationResults/read | A Recovery Services-tárolóval kapcsolatos biztonsági mentés eredményét adja vissza. |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vault/backupOperations/READ | Recovery Services-tároló biztonsági mentési műveletének állapotát adja vissza. |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/delete | Védelmi szabályzat törlése |
> | Action | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | A szabályzatművelet eredményeinek beolvasása. |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vaults/backupPolicies/Operations/READ | Házirend-művelet állapotának beolvasása. |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vault/backupPolicies/READ | Az összes védelmi szabályzat visszaadása |
> | Action | Microsoft. recoveryservices szolgáltatónál/tárolók/backupPolicies/írás | Védelmi szabályzat létrehozása |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vault/backupProtectableItems/READ | A védhető elemek listáját adja vissza. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectedItems/read | A védett elemek listájának beolvasása. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | Az előfizetéshez tartozó összes tárolót adja vissza. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | Az összes biztonsági mentési védelmi leképezés listázása |
> | Action | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Recovery Services-tároló biztonsági PIN-kódjának beolvasása. |
> | Action | Microsoft. recoveryservices szolgáltatónál/Vault/backupstorageconfig/READ | Recovery Services tároló tárolási konfigurációját adja vissza. |
> | Action | microsoft.recoveryservices/Vaults/backupstorageconfig/write | Frissíti Recovery Services tár tárolási konfigurációját. |
> | Action | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | A védett elemek és a védett kiszolgálók összefoglalásait adja vissza egy Recovery Services számára. |
> | Action | microsoft.recoveryservices/Vaults/backupValidateOperation/action | A művelet érvényesítése védett elemen |
> | Action | Microsoft.RecoveryServices/Vaults/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Action | Microsoft.RecoveryServices/Vaults/delete | A tár törlése művelet törli a "Vault" típusú megadott Azure-erőforrást. |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Lekéri a Recovery Services-tároló riasztásait. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | A riasztás feloldása. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | A Recovery Services-tár értesítési konfigurációjának beolvasása. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | E-mail-értesítések konfigurálása a Recovery Services-tárolóhoz. |
> | Action | Microsoft.RecoveryServices/Vaults/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | A tároló regisztrációjának törlése művelettel törölheti a tároló regisztrációját. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | A művelet eredményeinek beolvasása művelettel az aszinkron módon elküldött művelet műveleti állapota és eredménye olvasható be. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | A tárolók beolvasása művelettel beolvashatók az adott erőforráshoz regisztrált tárolók. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | A szolgáltatás-tároló regisztrálása művelettel regisztrálhat egy tárolót a helyreállítási szolgáltatással. |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Riasztási beállítások beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Riasztási beállítások létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationEvents/read | Események beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Konzisztencia-ellenőrzést futtat a hálón |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Bármilyen háló törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | A Process Server lemezképének üzembe helyezése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Háló migrálása a HRE-be |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Bármilyen háló beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Átjáró újrahozzárendelése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Háló eltávolítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Háló tanúsítványának megújítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Bármely logikai hálózat beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Bármilyen hálózat beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Hálózati hozzárendelések törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Hálózati leképezések létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Védhető elemek felderítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Védelmi tárolók beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Védelmi tároló eltávolítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Az áttelepítési elemek törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Elemek átmigrálása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Áttelepítési helyreállítási pontok olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Bármely áttelepítési elem olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Áttelepítés tesztelése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Karbantartás áttelepíthetők |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Áttelepítési elemek létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Bármely védhető elem olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Lemezek hozzáadása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Helyreállítási pont alkalmazása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Védett elemek törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Feladatátvétel véglegesítve |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Tervezett feladatátvétel |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Védett elemek beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Replikációs helyreállítási pontok olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Védett elemek eltávolítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Lemezek eltávolítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Replikáció javítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Védett elemek ismételt védelme |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Visszajelzés elküldése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Bármely cél számítási méret olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Feladatátvételi teszt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Feladatátvételi teszt karbantartása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Feladatátvétel |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mobilitási szolgáltatás frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Védett elemek létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | A védelmi tárolók összes leképezésének törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | A védelmi tárolók megfeleltetésének beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Védelmi tároló megfeleltetésének eltávolítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Védelmi tárolók hozzárendeléseinek létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Védelmi tároló váltása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Védelmi tárolók létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Recovery Services szolgáltatók törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Recovery Services szolgáltatók beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Szolgáltató frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Recovery Services-szolgáltató eltávolítása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Recovery Services szolgáltatók létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | A tárolási besorolások beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | A tárolási besorolási leképezések törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | A tárolási besorolási leképezések beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Tárolási besorolási leképezések létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Tetszőleges vCenter törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Bármilyen vCenter beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Tetszőleges vCenter létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Bármilyen háló létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Feladat visszavonása |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/read | Bármilyen feladat beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Újraindítási feladatok |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Feladatok folytatása |
> | Action | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Bármely áttelepítési elem olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Hálózati leképezések olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Bármilyen hálózat beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Bármely szabályzat törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Bármilyen szabályzat beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Szabályzatok létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Védett elemek beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | A védelmi tárolók megfeleltetésének beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Védelmi tárolók beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Helyreállítási tervek törlése |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Feladatátvételi véglegesítés helyreállítási terve |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Tervezett feladatátvétel helyreállítási terve |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Helyreállítási tervek olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Helyreállítási terv ismételt védetté |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Feladatátvételi teszt helyreállítási terve |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Feladatátvételi teszt karbantartásának helyreállítási terve |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Feladatátvétel helyreállítási terve |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Helyreállítási tervek létrehozása vagy frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Recovery Services szolgáltatók beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | A tárolási besorolási leképezések beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | A tárolási besorolások beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Bármilyen beolvasás  |
> | Action | Microsoft.RecoveryServices/vaults/replicationUsages/read | A tár replikációs használatának beolvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | A tár replikációs állapotának olvasása |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Tár állapotának frissítése |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Bármilyen beolvasás  |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Tetszőlegesek létrehozása vagy frissítése  |
> | Action | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Bármilyen vCenter beolvasása |
> | Action | Microsoft. recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Egy adott Recovery Services-tároló használati adatait adja vissza. |
> | Action | Microsoft. Recoveryservices szolgáltatónál/tárolók/használati adatok/olvasás | Bármely tár használatának olvasása |
> | Action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | A tároló-jogkivonat művelettel lekérheti a tár szintű háttérbeli műveletek tároló-tokenjét. |
> | Action | Microsoft.RecoveryServices/Vaults/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Relay/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Action | Microsoft.Relay/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Action | Microsoft. Relay/névterek/engedélyezési szabályok/művelet | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft. Relay/névterek/engedélyezési szabályok/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Action | Microsoft. Relay/névtér/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Action | Microsoft. Relay/névtér/engedélyezési szabályok/olvasás | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Action | Microsoft. Relay/névtér/engedélyezési szabályok/regenerateKeys/művelet | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft. Relay/névterek/engedélyezési szabályok/írás | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Action | Microsoft.Relay/namespaces/Delete | Egy névtérerőforrás törlése |
> | Action | Microsoft. Relay/névtér/disasterRecoveryConfigs/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Action | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Action | Microsoft. Relay/névtér/HybridConnections/engedélyezési szabályok/művelet | A HybridConnection frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Action | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/delete | HybridConnection-engedélyezési szabályok törlésének művelete |
> | Action | Microsoft. Relay/névtér/HybridConnections/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A HybridConnection tartozó kapcsolódási karakterlánc beolvasása |
> | Action | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/READ |  A HybridConnection-engedélyezési szabályok listájának beolvasása |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok/írás | Hozzon létre HybridConnection engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/Delete | HybridConnection-erőforrás törlésére szolgáló művelet |
> | Action | Microsoft.Relay/namespaces/HybridConnections/read | HybridConnection-erőforrás leírásának beolvasása |
> | Action | Microsoft.Relay/namespaces/HybridConnections/write | HybridConnection tulajdonságainak létrehozása vagy frissítése. |
> | Action | Microsoft. Relay/névtér/messagingPlan/olvasás | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft. Relay/névterek/messagingPlan/írás | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft. Relay/névtér/operationresults/olvasás | Névtérművelet állapotának beolvasása |
> | Action | Microsoft. Relay/névterek/szolgáltatók/Microsoft. bepillantást/metricDefinitions/READ | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Action | Microsoft.Relay/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Action | Microsoft.Relay/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Action | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/művelet | A WcfRelay frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Action | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/delete | WcfRelay-engedélyezési szabályok törlésének művelete |
> | Action | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/listkeys műveletének beolvasása/művelet | A WcfRelay tartozó kapcsolódási karakterlánc beolvasása |
> | Action | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/READ |  A WcfRelay-engedélyezési szabályok listájának beolvasása |
> | Action | Microsoft. Relay/névtér/WcfRelays/engedélyezési szabályok/regeneratekeys/művelet | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok/írás | Hozzon létre WcfRelay engedélyezési szabályokat, és frissítse annak tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Action | Microsoft. Relay/névterek/WcfRelays/delete | WcfRelay-erőforrás törlésére szolgáló művelet |
> | Action | Microsoft. Relay/névtér/WcfRelays/olvasás | WcfRelay-erőforrás leírásának beolvasása |
> | Action | Microsoft. Relay/névterek/WcfRelays/írás | WcfRelay tulajdonságainak létrehozása vagy frissítése. |
> | Action | Microsoft. Relay/névterek/írás | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Action | Microsoft.Relay/operations/read | Műveletek lekérése |
> | Action | Microsoft.Relay/register/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |
> | Action | Microsoft.Relay/unregister/action | A Relay erőforrás-szolgáltató előfizetésének regisztrálása és a Relay-erőforrások létrehozásának engedélyezése |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Beolvassa a megadott erőforrás rendelkezésre állási állapotát |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/read | Beolvassa a megadott hatókörben lévő összes erőforrás rendelkezésre állási állapotát |
> | Action | Microsoft.ResourceHealth/events/read | Az adott előfizetés Service Health-eseményeinek beolvasása |
> | Action | Microsoft.Resourcehealth/healthevent/action | A megadott erőforrás állapotának változását jelöli |
> | Action | Microsoft.Resourcehealth/healthevent/Activated/action | A megadott erőforrás állapotának változását jelöli |
> | Action | Microsoft.Resourcehealth/healthevent/InProgress/action | A megadott erőforrás állapotának változását jelöli |
> | Action | Microsoft.Resourcehealth/healthevent/Pending/action | A megadott erőforrás állapotának változását jelöli |
> | Action | Microsoft. Resourcehealth/healthevent/megoldott/művelet | A megadott erőforrás állapotának változását jelöli |
> | Action | Microsoft.Resourcehealth/healthevent/Updated/action | A megadott erőforrás állapotának változását jelöli |
> | Action | Microsoft.ResourceHealth/impactedResources/read | Az adott előfizetés érintett erőforrásainak beolvasása |
> | Action | Microsoft.ResourceHealth/metadata/read | Metaadatok beolvasása |
> | Action | Microsoft.ResourceHealth/Operations/read | A Microsoft ResourceHealth szolgáltatáshoz rendelkezésre álló műveletek lekérése |
> | Action | Microsoft.ResourceHealth/register/action | A Microsoft Resource Health-előfizetés regisztrálása |
> | Action | Microsoft.ResourceHealth/unregister/action | Megszünteti a Microsoft ResourceHealth előfizetését |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Resources/checkPolicyCompliance/action | Annak ellenőrzése, hogy egy adott erőforrás megfelelőségi állapota megfelel-e az erőforrás-szabályzatoknak. |
> | Action | Microsoft.Resources/checkResourceName/action | Erőforrásnév érvényességének ellenőrzése. |
> | Action | Microsoft. Resources/központi telepítések/megszakítás/művelet | Megszakítja az üzembe helyezést. |
> | Action | Microsoft.Resources/deployments/delete | Törli az üzemelő példányt. |
> | Action | Microsoft.Resources/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Action | Microsoft.Resources/deployments/read | Beolvassa vagy listázza az üzemelő példányokat. |
> | Action | Microsoft.Resources/deployments/validate/action | Érvényesíti az üzemelő példányt. |
> | Action | Microsoft.Resources/deployments/whatIf/action | A sablontelepítéssel járó módosítások előrejelzése. |
> | Action | Microsoft. Resources/üzembe helyezés/írás | Létrehozza vagy frissíti az üzemelő példányt. |
> | Action | Microsoft.Resources/links/delete | Törli az erőforrás-hivatkozást. |
> | Action | Microsoft.Resources/links/read | Beolvassa vagy listázza az erőforrás-hivatkozásokat. |
> | Action | Microsoft. Resources/Links/Write | Létrehozza vagy frissíti az erőforrás-hivatkozást. |
> | Action | Microsoft. Resources/Marketplace/vásárlás/művelet | Erőforrás vásárlása a Piactéren. |
> | Action | Microsoft. Resources/Providers/READ | A szolgáltatók listájának lekérése. |
> | Action | Microsoft.Resources/resources/read | Az erőforrások listájának beolvasása szűrők alapján. |
> | Action | Microsoft. Resources/Subscriptions/Locations/READ | Beolvassa a támogatott helyek listáját. |
> | Action | Microsoft.Resources/subscriptions/operationresults/read | Az előfizetési művelet eredményeinek lekérése. |
> | Action | Microsoft. Resources/előfizetések/szolgáltatók/olvasás | Beolvassa vagy listázza az erőforrás-szolgáltatókat. |
> | Action | Microsoft. Resources/előfizetések/olvasás | Beolvassa az előfizetések listáját. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/delete | Törli az erőforráscsoportot és az ahhoz tartozó összes erőforrást. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Beolvassa vagy listázza az üzembe helyezési műveleteket. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Az üzembe helyezési műveletek állapotának beolvasása vagy listázása. |
> | Action | Microsoft. Resources/előfizetések/resourcegroups/üzembe helyezés/olvasás | Beolvassa vagy listázza az üzemelő példányokat. |
> | Action | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Létrehozza vagy frissíti az üzemelő példányt. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action | Erőforrásokat helyez át az egyik erőforráscsoportból egy másikba. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/read | Beolvassa vagy listázza az erőforráscsoportokat. |
> | Action | Microsoft. Resources/Subscriptions/resourcegroups/Resources/READ | Az erőforráscsoporthoz tartozó erőforrások lekérése. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Erőforrások erőforráscsoportok közötti áthelyezésének ellenőrzése. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/write | Létrehozza vagy frissíti az erőforráscsoportot. |
> | Action | Microsoft. Resources/előfizetések/erőforrások/olvasás | Beolvassa az előfizetéshez tartozó erőforrásokat. |
> | Action | Microsoft.Resources/subscriptions/tagNames/delete | Törli az előfizetéscímkét. |
> | Action | Microsoft.Resources/subscriptions/tagNames/read | Beolvassa vagy listázza az előfizetéscímkéket. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Törli az előfizetéscímke értékét. |
> | Action | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Beolvassa vagy listázza az előfizetéscímkék értékeit. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/tagValues/Write | Hozzáadja az előfizetéscímke értékét. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/Write | Előfizetéscímke hozzáadása. |
> | Action | Microsoft.Resources/tags/delete | Eltávolítja az erőforrás összes címkéjét. |
> | Action | Microsoft.Resources/tags/read | Lekéri az erőforrás összes címkéjét. |
> | Action | Microsoft. Resources/Tags/Write | Frissíti egy erőforrás címkéit a meglévő címkék új címkékkel való lecserélésével vagy összevonásával, vagy a meglévő címkék eltávolításával. |
> | Action | Microsoft.Resources/tenants/read | Beolvassa a bérlők listáját. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Scheduler/jobcollections/delete | A feladatgyűjtemény törlése. |
> | Action | Microsoft.Scheduler/jobcollections/disable/action | A feladatgyűjtemény letiltása. |
> | Action | Microsoft.Scheduler/jobcollections/enable/action | A feladatgyűjtemény engedélyezése. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/delete | A feladat törlése. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Logic App-definíció előállítása egy Scheduler-feladat alapján. |
> | Action | Microsoft. Scheduler/feladatgyűjtemények/feladatok/jobhistories/olvasás | A feladatelőzmények beolvasása. |
> | Action | Microsoft. Scheduler/feladatgyűjtemények/feladatok/olvasás | A feladat beolvasása. |
> | Action | Microsoft.Scheduler/jobcollections/jobs/run/action | A feladat futtatása. |
> | Action | Microsoft. Scheduler/feladatgyűjtemények/feladatok/írás | Feladatok létrehozása és frissítése |
> | Action | Microsoft. Scheduler/feladatgyűjtemények/olvasás | Feladatgyűjtemény beolvasása |
> | Action | Microsoft. Scheduler/feladatgyűjtemények/írás | Feladatgyűjtemények létrehozása és frissítése |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Search/checkNameAvailability/action | A szolgáltatás nevének rendelkezésre állását ellenőrzi. |
> | Action | Microsoft.Search/operations/read | A Microsoft. Search szolgáltató összes elérhető műveletét listázza. |
> | Action | Microsoft.Search/register/action | Regisztrálja az előfizetést a keresési erőforrás-szolgáltatónál, és lehetővé teszi a keresési szolgáltatások létrehozását. |
> | Action | Microsoft.Search/searchServices/createQueryKey/action | Létrehozza a lekérdezési kulcsot. |
> | Action | Microsoft.Search/searchServices/delete | A keresési szolgáltatás törlése. |
> | Action | Microsoft.Search/searchServices/deleteQueryKey/delete | Törli a lekérdezési kulcsot. |
> | Action | Microsoft.Search/searchServices/listAdminKeys/action | A rendszergazdai kulcsok beolvasása. |
> | Action | Microsoft.Search/searchServices/listQueryKeys/read | Az adott Azure Search szolgáltatáshoz tartozó lekérdezési API-kulcsok listáját adja vissza. |
> | Action | Microsoft.Search/searchServices/read | A keresési szolgáltatás beolvasása. |
> | Action | Microsoft.Search/searchServices/regenerateAdminKey/action | Újra létrehozza a felügyeleti kulcsot. |
> | Action | Microsoft.Search/searchServices/start/action | Elindítja a keresési szolgáltatást. |
> | Action | Microsoft.Search/searchServices/stop/action | Leállítja a keresési szolgáltatást. |
> | Action | Microsoft. Search/searchServices/Write | A keresési szolgáltatás létrehozása vagy frissítése. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | A megadott forgalmi korlátozási szabályok betartatása a megadott hálózati biztonsági csoport (ok) hoz tartozó egyező biztonsági szabályok létrehozásával |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/read | Az Azure által védett erőforrások adaptív hálózattal kapcsolatos javaslatainak beolvasása |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/read | Az erőforrás összetett veszélyforrások elleni védelmi beállításainak beolvasása |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/write | Az erőforrás összetett veszélyforrások elleni védelmi beállításainak frissítése |
> | Action | Microsoft.Security/alerts/read | Az összes elérhető biztonsági riasztás beolvasása |
> | Action | Microsoft.Security/applicationWhitelistings/read | Az alkalmazás engedélyezési listájának beolvasása |
> | Action | Microsoft.Security/applicationWhitelistings/write | Új alkalmazás-engedélyezési lista létrehozása vagy meglévő frissítése |
> | Action | Microsoft.Security/complianceResults/read | Az erőforrás megfelelőségi eredményeinek beolvasása |
> | Action | Microsoft.Security/informationProtectionPolicies/read | Az erőforráshoz tartozó Information Protection-szabályzatok beolvasása |
> | Action | Microsoft.Security/informationProtectionPolicies/write | Az erőforráshoz tartozó Information Protection-szabályzatok frissítése |
> | Action | Microsoft.Security/locations/alerts/activate/action | Biztonsági riasztás aktiválása |
> | Action | Microsoft.Security/locations/alerts/dismiss/action | Biztonsági riasztás bezárása |
> | Action | Microsoft.Security/locations/alerts/read | Az összes elérhető biztonsági riasztás beolvasása |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Az igény szerinti hálózati hozzáférési házirend törlése |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Igény szerinti hálózati hozzáférési szabályzat kezdeményezése |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Az igény szerinti hálózati hozzáférési szabályzatok beolvasása |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Egy új, igény szerinti hálózati hozzáférési házirend létrehozása vagy egy meglévő frissítése |
> | Action | Microsoft.Security/locations/read | A biztonsági adattárolási hely beolvasása |
> | Action | Microsoft.Security/locations/tasks/activate/action | Biztonsági javaslat aktiválása |
> | Action | Microsoft.Security/locations/tasks/dismiss/action | Biztonsági javaslat bezárása |
> | Action | Microsoft.Security/locations/tasks/read | Az összes elérhető biztonsági javaslat beolvasása |
> | Action | Microsoft.Security/locations/tasks/resolve/action | Biztonsági javaslat feloldása |
> | Action | Microsoft.Security/locations/tasks/start/action | Biztonsági javaslat elindítása |
> | Action | Microsoft.Security/policies/read | A biztonsági szabályzat beolvasása |
> | Action | Microsoft.Security/policies/write | A biztonsági szabályzat frissítése |
> | Action | Microsoft.Security/pricings/delete | A hatókör díjszabási beállításainak törlése |
> | Action | Microsoft.Security/pricings/read | A hatókör díjszabási beállításainak beolvasása |
> | Action | Microsoft.Security/pricings/write | A hatókör díjszabási beállításainak frissítése |
> | Action | Microsoft.Security/register/action | Az előfizetés regisztrálása Azure Security Center |
> | Action | Microsoft.Security/securityContacts/delete | A biztonsági kapcsolattartó törlése |
> | Action | Microsoft.Security/securityContacts/read | A biztonsági kapcsolattartó beolvasása |
> | Action | Microsoft.Security/securityContacts/write | A biztonsági kapcsolattartó frissítése |
> | Action | Microsoft.Security/securitySolutions/delete | Biztonsági megoldás törlése |
> | Action | Microsoft.Security/securitySolutions/read | A biztonsági megoldások beolvasása |
> | Action | Microsoft.Security/securitySolutions/write | Új biztonsági megoldás létrehozása vagy egy meglévő frissítése |
> | Action | Microsoft.Security/securitySolutionsReferenceData/read | A biztonsági megoldások hivatkozási adatkérésének beolvasása |
> | Action | Microsoft. Security/securityStatuses/READ | Az Azure-erőforrások biztonsági állapotának beolvasása |
> | Action | Microsoft.Security/securityStatusesSummaries/read | A hatókörre vonatkozó biztonsági állapotok összegzésének beolvasása |
> | Action | Microsoft.Security/settings/read | A hatókör beállításainak beolvasása |
> | Action | Microsoft.Security/settings/write | A hatókör beállításainak frissítése |
> | Action | Microsoft. Security/feladatok/olvasás | Az összes elérhető biztonsági javaslat beolvasása |
> | Action | Microsoft.Security/unregister/action | Az előfizetés regisztrációjának törlése Azure Security Center |
> | Action | Microsoft.Security/webApplicationFirewalls/delete | Webalkalmazási tűzfal törlése |
> | Action | Microsoft.Security/webApplicationFirewalls/read | A webalkalmazási tűzfalak beolvasása |
> | Action | Microsoft.Security/webApplicationFirewalls/write | Új webalkalmazási tűzfal létrehozása vagy egy meglévő frissítése |
> | Action | Microsoft.Security/workspaceSettings/connect/action | Munkaterület-beállítások újrakapcsolási beállításainak módosítása |
> | Action | Microsoft.Security/workspaceSettings/delete | A munkaterület beállításainak törlése |
> | Action | Microsoft.Security/workspaceSettings/read | A munkaterület beállításainak beolvasása |
> | Action | Microsoft.Security/workspaceSettings/write | A munkaterület beállításainak frissítése |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/delete | Diagnosztikai beállítások törlése |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/read | Diagnosztikai beállítások olvasása |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/write | Diagnosztikai beállítások írása |
> | Action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Diagnosztikai beállítások kategóriáinak olvasása |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ServiceBus/checkNameAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. |
> | Action | Microsoft.ServiceBus/checkNamespaceAvailability/action | A névtér adott előfizetésben való elérhetőségének ellenőrzése. Ez az API elavult. használja helyette a CheckNameAvailability. |
> | Action | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | A megadott virtuális hálózat ServiceBus erőforrás-szolgáltatójában található virtuális hálózati szabályok törlése |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/action | Frissíti a névtér-engedélyezési szabályt. Ez az API elavult. Ehelyett használjon egy PUT hívást a névtér-engedélyezési szabály frissítéséhez. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Névtér-engedélyezési szabály törlése. Az alapértelmezett névtér-engedélyezési szabály nem törölhető.  |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | A névtérhez tartozó kapcsolati sztring beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/read | A névtér-engedélyezési szabályok leírásai listájának beolvasása. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/write | Hozzon létre egy névtér szintű engedélyezési szabályokat, és frissítse a tulajdonságait. Az engedélyezési szabályok hozzáférési jogosultságokat, az elsődleges és a másodlagos kulcsokat is frissítheti. |
> | Action | Microsoft.ServiceBus/namespaces/Delete | Egy névtérerőforrás törlése |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | A vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok kulcsainak beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Vészhelyreállítás elsődleges névterére vonatkozó engedélyezési szabályok beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | A vészhelyreállítás letiltása és a módosítások elsődleges névtérből másodlagos névtérbe történő replikálásának leállítása. |
> | Action | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | A névtéralias adott előfizetésben való elérhetőségének ellenőrzése. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | A névtérhez társított vész-helyreállítási konfiguráció törlése. Ez a művelet csak az elsődleges névtér használatával hívható meg. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | GEO DR feladatátvételének meghívása és a névtéralias beállítása úgy, hogy a másodlagos névtérre mutasson. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | A névtérhez hozzárendelt vész-helyreállítási konfiguráció beolvasása. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | A névtérhez hozzárendelt vész-helyreállítási konfiguráció létrehozása vagy frissítése. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | A névtérhez társított Event Grid-szűrő törlése. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | A névtérhez társított Event Grid-szűrő beolvasása. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | A névtérhez társított Event Grid-szűrő létrehozása vagy frissítése. |
> | Action | Microsoft.ServiceBus/namespaces/eventhubs/read | EventHub-erőforrás leírásának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | IP-szűrő erőforrás törlése |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/read | IP-szűrő erőforrás beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/write | IP-szűrő erőforrás létrehozása |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | Üzenetek tallózása |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | Üzenetek késleltetése |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Üzenetek fogadása |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Shedule üzenetek |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Üzenetek küldése |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | Munkamenet állapotának beállítása |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/read | Lekéri egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/write | Frissíti egy névtér üzenetkezelési tervét.<br>Ez az API elavult.<br>A MessagingPlan-erőforráson keresztül elérhető tulajdonságok a (szülő) névtér-erőforrásba kerülnek a későbbi API-verziókban.<br>Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. |
> | Action | Microsoft.ServiceBus/namespaces/migrate/action | Névtérmigrálási művelet |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Az áttelepítési konfiguráció törlése. |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Az áttelepítési konfiguráció beolvasása, amely megadja az áttelepítés és a függőben lévő replikálási műveletek állapotát |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | A standard szintű névtérből prémium szintűbe való áttelepítés visszaállítása |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | A standard szintű névtérhez hozzárendelt DNS társítása a prémium szintű névtérhez, mely művelet elvégzi az áttelepítést, és leállítja az erőforrások a standard szintű névtérből a prémium szintűbe való szinkronizálását |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Az áttelepítési konfiguráció létrehozása vagy frissítése. Ezzel elindítja a standard és a prémium szintű névtér erőforrásainak szinkronizálását. |
> | Action | Microsoft. ServiceBus/névterek/networkruleset/delete | Virtuális hálózati szabály erőforrás törlése |
> | Action | Microsoft. ServiceBus/névterek/networkruleset/olvasás | Gets NetworkRuleSet Resource |
> | Action | Microsoft. ServiceBus/névterek/networkruleset/írás | Virtuális hálózati szabály erőforrás létrehozása |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Virtuális hálózati szabály erőforrás törlése |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/write | Virtuális hálózati szabály erőforrás létrehozása |
> | Action | Microsoft.ServiceBus/namespaces/operationresults/read | Névtérművelet állapotának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Névtér diagnosztikai beállításainak erőforrás-leírásának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Névtér-naplók erőforrás-leírások listájának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Névtér-metrikák erőforrás-leírásának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | A várólista frissítési művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Várólista-engedélyezési szabályok törlésének művelete |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | A várólistához tartozó kapcsolódási karakterlánc lekérése |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  A várólista-engedélyezési szabályok listájának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Várólista-engedélyezési szabályok létrehozása és a tulajdonságainak frissítése. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Action | Microsoft.ServiceBus/namespaces/queues/Delete | Várólista-erőforrás törlésének művelete |
> | Action | Microsoft.ServiceBus/namespaces/queues/read | Várólista-erőforrás leírásának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/queues/write | Várólista tulajdonságainak létrehozása vagy frissítése |
> | Action | Microsoft.ServiceBus/namespaces/read | A névtérerőforrások leírásai listájának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | ACS-névtér eltávolítása |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | A témakör frissítésének művelete. Ez a művelet a 2017-04-01-es API-verzióban nem támogatott. Engedélyezési szabályok. Használjon egy PUT hívást az engedélyezési szabály frissítéséhez. |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | A témakör engedélyezési szabályainak törlésére szolgáló művelet |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | A kapcsolódási karakterlánc lekérése a témakörhöz |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  A témakör engedélyezési szabályainak lekérése |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Az erőforrás elsődleges vagy másodlagos kulcsának újragenerálása |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Hozzon létre a témakör engedélyezési szabályait, és frissítse a tulajdonságait. Az engedélyezési szabályokhoz tartozó hozzáférési jogosultságok frissíthetők. |
> | Action | Microsoft.ServiceBus/namespaces/topics/Delete | Témakör-erőforrás törlésére szolgáló művelet |
> | Action | Microsoft.ServiceBus/namespaces/topics/read | Témakör-erőforrás leírások listájának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | TopicSubscription-erőforrás törlésére szolgáló művelet |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | TopicSubscription-erőforrás leírásának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Szabály-erőforrás törlésére szolgáló művelet |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Szabály erőforrás-leírások listájának beolvasása |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Szabály tulajdonságainak létrehozása vagy frissítése. |
> | Action | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | TopicSubscription tulajdonságainak létrehozása vagy frissítése. |
> | Action | Microsoft.ServiceBus/namespaces/topics/write | Témakör tulajdonságainak létrehozása vagy frissítése. |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Virtuális hálózati szabály erőforrás törlése |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Virtuális hálózati szabály erőforrás lekérése |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Virtuális hálózati szabály erőforrás létrehozása |
> | Action | Microsoft.ServiceBus/namespaces/write | Hozzon létre egy névtér-erőforrást, és frissítse a tulajdonságait. A névtér címkéi és kapacitása a frissíthető tulajdonságok. |
> | Action | Microsoft.ServiceBus/operations/read | Műveletek lekérése |
> | Action | Microsoft.ServiceBus/register/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |
> | Action | Microsoft.ServiceBus/sku/read | SKU-erőforrások leírásának beolvasása |
> | Action | Microsoft.ServiceBus/sku/regions/read | SkuRegions-erőforrás leírásának beolvasása |
> | Action | Microsoft.ServiceBus/unregister/action | Regisztrálja az előfizetést a ServiceBus erőforrás-szolgáltatóra, és lehetővé teszi a ServiceBus-erőforrások létrehozását |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.ServiceFabric/clusters/applications/delete | Tetszőleges alkalmazás törlése |
> | Action | Microsoft.ServiceFabric/clusters/applications/read | Tetszőleges alkalmazás beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/delete | Tetszőleges szolgáltatás törlése |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Tetszőleges partíció beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Tetszőleges replika beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/read | Tetszőleges szolgáltatás beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Bármely szolgáltatásállapot beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/write | Tetszőleges szolgáltatás létrehozása vagy frissítése |
> | Action | Microsoft.ServiceFabric/clusters/applications/write | Tetszőleges alkalmazás létrehozása vagy frissítése |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Tetszőleges alkalmazástípus törlése |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/read | Tetszőleges alkalmazástípus beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Tetszőleges alkalmazástípus-verzió törlése |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Tetszőleges alkalmazástípus-verzió beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Tetszőleges alkalmazástípus-verzió létrehozása vagy frissítése |
> | Action | Microsoft.ServiceFabric/clusters/applicationTypes/write | Tetszőleges alkalmazástípus létrehozása vagy frissítése |
> | Action | Microsoft.ServiceFabric/clusters/delete | Tetszőleges fürt törlése |
> | Action | Microsoft.ServiceFabric/clusters/nodes/read | Tetszőleges csomópont beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/read | Tetszőleges fürt beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/statuses/read | Bármely fürtállapot beolvasása |
> | Action | Microsoft.ServiceFabric/clusters/write | Tetszőleges fürt létrehozása vagy frissítése |
> | Action | Microsoft.ServiceFabric/locations/clusterVersions/read | Minden fürtverzió beolvasása |
> | Action | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Tetszőleges fürtverzió beolvasása egy adott környezetben |
> | Action | Microsoft.ServiceFabric/locations/operationresults/read | Tetszőleges műveleti eredmények beolvasása |
> | Action | Microsoft.ServiceFabric/locations/operations/read | Tetszőleges műveletek beolvasása hely szerint |
> | Action | Microsoft.ServiceFabric/operations/read | Tetszőleges használható műveletek beolvasása |
> | Action | Microsoft.ServiceFabric/register/action | Tetszőleges művelet regisztrálása |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.SignalRService/locations/checknameavailability/action | Annak ellenőrzése, hogy a név elérhető-e az új szignáló szolgáltatással való használatra |
> | Action | Microsoft.SignalRService/locations/operationresults/signalr/read | Aszinkron művelet állapotának lekérdezése |
> | Action | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Aszinkron művelet állapotának lekérdezése |
> | Action | Microsoft.SignalRService/locations/usages/read | Az Azure Signaler szolgáltatás kvóta-használatának beolvasása |
> | Action | Microsoft.SignalRService/operationresults/read | Aszinkron művelet állapotának lekérdezése |
> | Action | Microsoft.SignalRService/operationstatus/read | Aszinkron művelet állapotának lekérdezése |
> | Action | Microsoft.SignalRService/register/action | Regisztrálja a Microsoft. SignalRService erőforrás-szolgáltatót egy előfizetéssel |
> | Action | Microsoft.SignalRService/SignalR/delete | A teljes jelző szolgáltatás törlése |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Event Grid-szűrő törlése a jelzőből. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/read | A megadott Event Grid-szűrő tulajdonságainak beolvasása vagy a megadott jelzőhöz tartozó összes Event Grid-szűrő felsorolása. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/write | A megadott paraméterekkel rendelkező jelzőhöz tartozó Event Grid-szűrő létrehozása vagy frissítése. |
> | Action | Microsoft.SignalRService/SignalR/listkeys/action | A Signaler-hozzáférési kulcsok értékének megtekintése a felügyeleti portálon vagy az API-n keresztül |
> | Action | Microsoft.SignalRService/SignalR/read | A jelző beállításainak és konfigurációjának megtekintése a felügyeleti portálon vagy az API-n keresztül |
> | Action | Microsoft.SignalRService/SignalR/regeneratekey/action | A Signaler-hozzáférési kulcsok értékének módosítása a felügyeleti portálon vagy az API-n keresztül |
> | Action | Microsoft.SignalRService/SignalR/restart/action | Az Azure Signaler szolgáltatás újraindítása a felügyeleti portálon vagy API-n keresztül. Bizonyos állásidő lesz. |
> | Action | Microsoft.SignalRService/SignalR/write | A jelző beállításainak és konfigurációjának módosítása a felügyeleti portálon vagy az API-n keresztül |
> | Action | Microsoft.SignalRService/unregister/action | A Microsoft. SignalRService erőforrás-szolgáltató regisztrációjának törlése előfizetéssel |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Alkalmazásdefiníció alkalmazás-összetevőinek listázása. |
> | Action | Microsoft.Solutions/applicationDefinitions/delete | Alkalmazásdefiníció eltávolítása. |
> | Action | Microsoft.Solutions/applicationDefinitions/read | Az alkalmazásdefiníciók listájának beolvasása. |
> | Action | Microsoft.Solutions/applicationDefinitions/write | Alkalmazásdefiníció hozzáadása vagy módosítása. |
> | Action | Microsoft.Solutions/applications/applicationArtifacts/read | Alkalmazás-összetevők listázása. |
> | Action | Microsoft.Solutions/applications/delete | Alkalmazás eltávolítása. |
> | Action | Microsoft.Solutions/applications/read | Az alkalmazások listájának beolvasása. |
> | Action | Microsoft.Solutions/applications/refreshPermissions/action | Alkalmazásengedély(ek) frissítése. |
> | Action | Microsoft.Solutions/applications/updateAccess/action | Az alkalmazás-hozzáférés frissítése. |
> | Action | Microsoft.Solutions/applications/write | Alkalmazás létrehozása. |
> | Action | Microsoft.Solutions/jitRequests/delete | JIT-kérelem eltávolítása |
> | Action | Microsoft.Solutions/jitRequests/read | JIT-kérelem-lista beolvasása. |
> | Action | Microsoft.Solutions/jitRequests/write | JIT-kérelem létrehozása |
> | Action | Microsoft.Solutions/locations/operationStatuses/read | Az erőforrás műveleti állapotának beolvasása. |
> | Action | Microsoft.Solutions/register/action | Regisztrálás megoldásokra. |
> | Action | Microsoft.Solutions/unregister/action | A megoldásokra való regisztráció törlése. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Sql/checkNameAvailability/action | Győződjön meg arról, hogy az adott kiszolgálónév elérhető-e a világ egy adott előfizetéshez való kiépítés céljából. |
> | Action | Microsoft.Sql/instancePools/delete | Egy példány készletének törlése |
> | Action | Microsoft.Sql/instancePools/read | Egy példány készletének beolvasása |
> | Action | Microsoft.Sql/instancePools/usages/read | Példány-készlet használati adatainak beolvasása |
> | Action | Microsoft.Sql/instancePools/write | Egy példány-készlet létrehozása vagy frissítése |
> | Action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | A kiszolgálói blob naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Action | Microsoft.Sql/locations/capabilities/read | Az előfizetéshez tartozó képességek beolvasása egy adott helyen |
> | Action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Action | Microsoft.Sql/locations/databaseOperationResults/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Action | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Folyamatban lévő műveletek beolvasása a törölt kiszolgálón |
> | Action | Microsoft.Sql/locations/deletedServerOperationResults/read | Folyamatban lévő műveletek beolvasása a törölt kiszolgálón |
> | Action | Microsoft.Sql/locations/deletedServers/read | Visszaadja a törölt kiszolgálók listáját, vagy lekéri a megadott törölt kiszolgáló tulajdonságait. |
> | Action | Microsoft.Sql/locations/deletedServers/recover/action | Törölt kiszolgáló helyreállítása |
> | Action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Egy rugalmas készlet aszinkron műveletéhez tartozó Azure aszinkron művelet beolvasása |
> | Action | Microsoft.Sql/locations/elasticPoolOperationResults/read | Egy rugalmas készlet műveletének eredményét kapja meg. |
> | Action | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási titkosítási védőn |
> | Action | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási titkosítási védőn |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | A kiterjesztett kiszolgáló blob-naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Action | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Egy tűzfalszabály-művelet állapotának beolvasása. |
> | Action | Microsoft.Sql/locations/firewallRulesOperationResults/read | Egy tűzfalszabály-művelet állapotának beolvasása. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/delete | Töröl egy meglévő példány-feladatátvételi csoportot. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Tervezett feladatátvételt hajt végre egy meglévő példány-feladatátvételi csoportban. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kényszerített feladatátvételt hajt végre egy meglévő példány-feladatátvételi csoportban. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/read | A példányok feladatátvételi csoportjainak listáját adja vissza, vagy lekéri a megadott példány-feladatátvételi csoport tulajdonságait. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/write | Létrehoz egy példány-feladatátvételi csoportot a megadott paraméterekkel, vagy frissíti a megadott példány-feladatátvételi csoport tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Egy példány-készlet művelet állapotának beolvasása |
> | Action | Microsoft.Sql/locations/instancePoolOperationResults/read | Egy példány-készlet művelet eredményének beolvasása |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Egy adott interfész-végpont Azure aszinkron műveletének részleteit adja vissza. |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | A megadott csatoló-végponti profil műveletének részleteit adja vissza. |
> | Action | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Lekéri egy ügynök műveletének állapotát. |
> | Action | Microsoft.Sql/locations/jobAgentOperationResults/read | Lekérdezi egy ügynök műveletének eredményét. |
> | Action | Microsoft.Sql/locations/longTermRetentionBackups/read | Felsorolja a hely összes kiszolgálójának összes adatbázisának hosszú távú adatmegőrzési biztonsági másolatait. |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Felsorolja a kiszolgáló összes adatbázisának hosszú távú adatmegőrzési biztonsági másolatait. |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Hosszú távú adatmegőrzési biztonsági mentés törlése |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Egy adatbázis hosszú távú adatmegőrzési biztonsági másolatait sorolja fel |
> | Action | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Felügyelt adatbázis-visszaállítási művelet befejezése |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Folyamatban lévő műveleteket végez a transzparens adattitkosítás felügyelt példányának titkosítási oltalmazóján |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Folyamatban lévő műveleteket végez a transzparens adattitkosítás felügyelt példányának titkosítási oltalmazóján |
> | Action | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítás felügyelt példányainak kulcsaihoz |
> | Action | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítás felügyelt példányainak kulcsaihoz |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása a felügyelt adatbázis transzparens adattitkosításával |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Folyamatban lévő műveletek beolvasása a felügyelt adatbázis transzparens adattitkosításával |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Egy privát végponti kapcsolatok műveletének eredményét kapja meg |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | A magánhálózati végponti kapcsolatok proxyjának eredményének beolvasása |
> | Action | Microsoft.Sql/locations/read | Egy adott előfizetés elérhető helyeinek beolvasása |
> | Action | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Action | Microsoft.Sql/locations/serverKeyOperationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítási kiszolgáló kulcsain |
> | Action | Microsoft.Sql/locations/syncAgentOperationResults/read | A Szinkronizáló ügynök erőforrás-műveletének eredményének beolvasása |
> | Action | Microsoft.Sql/locations/syncDatabaseIds/read | Egy adott régió és előfizetés szinkronizálási adatbázis-azonosítóinak beolvasása |
> | Action | Microsoft.Sql/locations/syncGroupOperationResults/read | A szinkronizálási csoport erőforrása művelet eredményének beolvasása |
> | Action | Microsoft.Sql/locations/syncMemberOperationResults/read | A szinkronizálási tag erőforrása művelet eredményének beolvasása |
> | Action | Microsoft.Sql/locations/usages/read | Az előfizetéshez tartozó használati metrikák gyűjteményének beolvasása egy helyen |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | A megadott virtuális hálózati szabályok Azure aszinkron műveletének részleteit adja vissza.  |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | A virtuális hálózati szabályok megadott műveletének részleteit adja vissza.  |
> | Action | Microsoft.Sql/managedInstances/administrators/delete | A felügyelt példány meglévő rendszergazdája törölve. |
> | Action | Microsoft.Sql/managedInstances/administrators/read | Lekéri a felügyelt példányok rendszergazdáinak listáját. |
> | Action | Microsoft.Sql/managedInstances/administrators/write | A felügyelt példányok rendszergazdájának létrehozása vagy frissítése a megadott paraméterekkel. |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Egy felügyelt adatbázis rövid távú adatmegőrzési szabályzatának beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Egy felügyelt adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | A Batch Update érzékenységi címkéi |
> | Action | Microsoft.Sql/managedInstances/databases/delete | Meglévő felügyelt adatbázis törlése |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Lekéri a felügyelt példány adatbázisaihoz elérhető naplókat |
> | Action | Microsoft.Sql/managedInstances/databases/read | Meglévő felügyelt adatbázis beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/read | Felügyelt adatbázis-séma beszerzése. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Felügyelt adatbázis oszlopának beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Egy adott oszlop érzékenységi címkéjének törlése |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Felügyelt adatbázistábla beszerzése |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Az adott kiszolgálóhoz konfigurált felügyelt adatbázis-veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Az adatbázis-veszélyforrások észlelési házirendjének módosítása egy adott felügyelt adatbázis esetében |
> | Action | Microsoft.Sql/managedInstances/databases/securityEvents/read | A felügyelt adatbázis biztonsági eseményeinek beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Az adatbázis transzparens adattitkosítás adatainak beolvasása egy adott felügyelt adatbázisban |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Az adatbázis transzparens adattitkosítás módosítása egy adott felügyelt adatbázisra vonatkozóan |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Egy adott adatbázis sebezhetőségi felmérésének eltávolítása |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy givendatabase |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Az adott adatbázis sebezhetőségi felmérési szabályának alaptervének eltávolítása |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének beolvasása |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének módosítása |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Meglévő vizsgálat eredményének konvertálása emberi olvasási formátumba. Ha már létezik, semmi nem történik |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Visszaadja az adatbázis sebezhetőségi felmérési rekordjainak listáját, vagy beolvassa a megadott vizsgálati AZONOSÍTÓhoz tartozó vizsgálati rekordot. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Action | Microsoft.Sql/managedInstances/databases/write | Létrehoz egy új adatbázist, vagy frissít egy meglévő adatbázist. |
> | Action | Microsoft.Sql/managedInstances/delete | Egy meglévő felügyelt példány törlése. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/read | A kiszolgáló titkosítási védelmi adatainak listáját adja vissza, vagy lekéri a megadott kiszolgálói titkosítási védő tulajdonságait. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/write | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Action | Microsoft.Sql/managedInstances/keys/delete | Egy meglévő Azure SQL felügyelt példány kulcsának törlése. |
> | Action | Microsoft.Sql/managedInstances/keys/read | Visszaadja a felügyelt példány kulcsainak listáját, vagy lekéri a megadott felügyelt példány kulcsának tulajdonságait. |
> | Action | Microsoft.Sql/managedInstances/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott felügyelt példány kulcsának tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/managedInstances/metricDefinitions/read | Felügyelt példány metrika-definícióinak beolvasása |
> | Action | Microsoft.Sql/managedInstances/metrics/read | Felügyelt példány metrikáinak beolvasása |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft. SQL/managedInstances/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | A felügyelt példányok elérhető naplófájljainak beolvasása |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | A felügyelt példányok számára elérhető metrikák visszatérési típusai |
> | Action | Microsoft.Sql/managedInstances/read | Visszaadja a felügyelt példányok listáját, vagy lekéri a megadott felügyelt példány tulajdonságait. |
> | Action | Microsoft.Sql/managedInstances/recoverableDatabases/read | A helyreállítható felügyelt adatbázisok listáját adja vissza. |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Rövid távú adatmegőrzési szabályzat beolvasása egy eldobott felügyelt adatbázishoz |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Egy eldobott felügyelt adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Visszaállítható felügyelt adatbázisok listájának beolvasása. |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Egy adott kiszolgálóhoz konfigurált felügyelt kiszolgáló veszélyforrás-észlelési szabályzatok listájának beolvasása |
> | Action | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Felügyelt kiszolgáló fenyegetés-észlelési szabályzatának módosítása egy adott felügyelt kiszolgálóhoz |
> | Action | Microsoft.Sql/managedInstances/tdeCertificates/action | TDE-tanúsítvány létrehozása/frissítése |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Egy adott felügyelt példány sebezhetőségi felmérésének eltávolítása |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy adott felügyelt példányon |
> | Action | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Egy adott felügyelt példány sebezhetőségi felmérésének módosítása |
> | Action | Microsoft.Sql/managedInstances/write | Létrehoz egy felügyelt példányt a megadott paraméterekkel, vagy frissíti a megadott felügyelt példány tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/operations/read | Elérhető REST-műveletek beolvasása |
> | Action | Microsoft. SQL/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
> | Action | Microsoft.Sql/register/action | Regisztrálja a Microsoft SQL Database erőforrás-szolgáltató előfizetését, és engedélyezi a Microsoft SQL-adatbázisok létrehozását. |
> | Action | Microsoft.Sql/servers/administratorOperationResults/read | Folyamatban lévő műveletek beolvasása a kiszolgálói rendszergazdáknál |
> | Action | Microsoft.Sql/servers/administrators/delete | Kiszolgáló-rendszergazda törlése |
> | Action | Microsoft.Sql/servers/administrators/read | Kiszolgáló-rendszergazda adatainak beolvasása |
> | Action | Microsoft.Sql/servers/administrators/write | Kiszolgáló-rendszergazda létrehozása vagy frissítése |
> | Action | Microsoft.Sql/servers/advisors/read | A kiszolgálóhoz elérhető tanácsadók listáját adja vissza. |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/read | A kiszolgáló megadott tanácsadójának javasolt műveleteinek listáját adja vissza. |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/write | A javasolt művelet alkalmazása a kiszolgálón |
> | Action | Microsoft.Sql/servers/advisors/write | A tanácsadók kiszolgálói szinten történő automatikus végrehajtási állapotának frissítése. |
> | Action | Microsoft.Sql/servers/auditingPolicies/read | Az adott kiszolgálón konfigurált alapértelmezett kiszolgálóoldali naplózási szabályzat részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/auditingPolicies/write | Az alapértelmezett kiszolgálói tábla naplózásának módosítása egy adott kiszolgálón |
> | Action | Microsoft.Sql/servers/auditingSettings/operationResults/read | A kiszolgálói blob naplózási házirendjének beállítása művelet eredményének beolvasása |
> | Action | Microsoft.Sql/servers/auditingSettings/read | Egy adott kiszolgálón konfigurált kiszolgálói blob naplózási szabályzat részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/auditingSettings/write | A kiszolgáló blob-naplózásának módosítása egy adott kiszolgálón |
> | Action | Microsoft.Sql/servers/automaticTuning/read | A kiszolgáló Automatikus hangolási beállításainak visszaadása |
> | Action | Microsoft.Sql/servers/automaticTuning/write | Frissíti a kiszolgáló Automatikus hangolási beállításait, és visszaadja a frissített beállításokat |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Töröl egy meglévő biztonsági mentési archiváló tárat. |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Ezzel a művelettel egy biztonsági mentési hosszú távú adatmegőrzési tár olvasható be. Információt ad vissza a kiszolgálón regisztrált tárolóról |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Ezzel a művelettel lehet regisztrálni egy biztonsági mentési hosszú távú adatmegőrzési tárolót egy kiszolgálóra |
> | Action | Microsoft.Sql/servers/communicationLinks/delete | Egy meglévő kiszolgálói kommunikációs hivatkozás törlése. |
> | Action | Microsoft.Sql/servers/communicationLinks/read | Egy megadott kiszolgáló kommunikációs hivatkozásainak listáját adja vissza. |
> | Action | Microsoft.Sql/servers/communicationLinks/write | Kiszolgáló-kommunikációs hivatkozás létrehozása vagy frissítése. |
> | Action | Microsoft.Sql/servers/connectionPolicies/read | Egy adott kiszolgáló kiszolgálói csatlakoztatási házirendjeinek a listáját adja vissza. |
> | Action | Microsoft.Sql/servers/connectionPolicies/write | Kiszolgálói kapcsolatok házirendjének létrehozása vagy frissítése. |
> | Action | Microsoft.Sql/servers/databases/advisors/read | Az adatbázishoz elérhető tanácsadók listájának beolvasása |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Az adatbázis megadott tanácsadójának javasolt műveleteinek listáját adja vissza. |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | A javasolt művelet alkalmazása az adatbázison |
> | Action | Microsoft.Sql/servers/databases/advisors/write | Egy Advisor automatikus végrehajtási állapotának frissítése az adatbázis szintjén. |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/read | Az adott adatbázisban konfigurált táblázatos naplózási szabályzat részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/write | A tábla naplózási házirendjének módosítása egy adott adatbázisra vonatkozóan |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/read | Egy adott adatbázisban konfigurált blob-naplózási szabályzat részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/write | Egy adott adatbázis blob-naplózási házirendjének módosítása |
> | Action | Microsoft.Sql/servers/databases/auditRecords/read | Az adatbázis blob-naplózási rekordjainak beolvasása |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/read | Egy adatbázis Automatikus hangolási beállításainak visszaadása |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/write | Frissíti az adatbázis Automatikus hangolási beállításait, és visszaadja a frissített beállításokat |
> | Action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Egy adott adatbázis biztonsági mentési archiválási házirendjeinek a listáját adja vissza. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Adatbázis biztonsági mentési archiválási szabályzatának létrehozása vagy frissítése. |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Egy adatbázis rövid távú adatmegőrzési szabályzatának beolvasása |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Egy adatbázis rövid távú adatmegőrzési szabályzatának frissítése |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/read | Egy adott adatbázisban konfigurált kapcsolati szabályzat részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/write | Egy adott adatbázishoz tartozó kapcsolatbiztonsági szabályzat módosítása |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | A Batch Update érzékenységi címkéi |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Az adatbázis-adatmaszkolási szabályzatok listájának visszaadása. |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Az adatmaszkolási házirend szabályának törlése egy adott adatbázisra vonatkozóan |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Az adott adatbázisban konfigurált adatmaszkolási házirend-szabály részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Az adatmaszkolási házirend szabályának módosítása egy adott adatbázisra vonatkozóan |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Egy adott adatbázis adatmaszkolási házirendjének módosítása |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Az adatraktár-lekérdezés elosztott lekérdezési lépésének információit adja vissza a kiválasztott lépés-AZONOSÍTÓhoz |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Az adatraktár-terjesztési lekérdezési adatokat adja vissza a kiválasztott lekérdezési AZONOSÍTÓhoz. |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Lekéri egy SQL Data Warehouse példány felhasználói tevékenységét, amely tartalmazza a futó és a felfüggesztett lekérdezéseket |
> | Action | Microsoft.Sql/servers/databases/delete | Töröl egy meglévő adatbázist. |
> | Action | Microsoft.Sql/servers/databases/export/action | Azure SQL Database exportálása |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Az adott adatbázisban konfigurált kiterjesztett blob-naplózási szabályzat részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Egy adott adatbázis kiterjesztett blob-naplózási szabályzatának módosítása |
> | Action | Microsoft.Sql/servers/databases/extensions/read | Az adatbázishoz tartozó bővítmények gyűjteményét kéri le. |
> | Action | Microsoft.Sql/servers/databases/extensions/write | Egy adott adatbázis kiterjesztésének módosítása |
> | Action | Microsoft.Sql/servers/databases/failover/action | Ügyfél által kezdeményezett adatbázis-feladatátvétel. |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Egy adott adatbázishoz tartozó földrajzi biztonsági mentési szabályzatok beolvasása |
> | Action | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Adatbázis-geobackup szabályzat létrehozása vagy frissítése |
> | Action | Microsoft.Sql/servers/databases/importExportOperationResults/read | Folyamatban lévő importálási/exportálási műveletek beolvasása |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | A kiválasztott adatbázishoz elérhető karbantartási időszakok listájának beolvasása. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/read | A kiválasztott adatbázis karbantartási Windows-beállításainak beolvasása. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/write | Beállítja a karbantartási Windows-beállításokat a kiválasztott adatbázishoz. |
> | Action | Microsoft.Sql/servers/databases/metricDefinitions/read | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Action | Microsoft.Sql/servers/databases/metrics/read | Adatbázisok visszaküldési metrikái |
> | Action | Microsoft.Sql/servers/databases/move/action | Meglévő adatbázis nevének módosítása |
> | Action | Microsoft.Sql/servers/databases/operationResults/read | Egy adatbázis-művelet állapotának beolvasása. |
> | Action | Microsoft.Sql/servers/databases/operations/cancel/action | Megszakítja Azure SQL Database folyamatban lévő aszinkron műveletet, amely még nem fejeződött be. |
> | Action | Microsoft.Sql/servers/databases/operations/read | Az adatbázison végrehajtott műveletek listájának visszaadása |
> | Action | Microsoft.Sql/servers/databases/pause/action | Az Azure SQL Datawarehouse-adatbázis szüneteltetése |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | A rendelkezésre álló naplók beolvasása az adatbázisokhoz |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Az adatbázisokhoz elérhető metrikák visszatérési típusai |
> | Action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | A megadott paramétereknek megfelelő lekérdezési szövegek gyűjteményét adja vissza. |
> | Action | Microsoft.Sql/servers/databases/queryStore/read | Az adatbázishoz tartozó Query Store-beállítások aktuális értékeit adja vissza. |
> | Action | Microsoft.Sql/servers/databases/queryStore/write | Frissíti az adatbázis lekérdezés-tárolójának beállításait |
> | Action | Microsoft.Sql/servers/databases/read | Az adatbázisok listájának visszaadása vagy a megadott adatbázis tulajdonságainak beolvasása. |
> | Action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/delete | A replikálási kapcsolat kényszerített leállítása és lehetséges adatvesztése |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Feladatátvétel az elsődlegesről az összes módosítás szinkronizálása után, hogy az adatbázis az elsődleges replikációs relationship\u0027s legyen, és hogy a távoli elsődleges legyen a másodlagos |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Azonnali feladatátvétel a lehetséges adatvesztéssel, az adatbázis replikálása az elsődleges replikálási relationship\u0027s, és a távoli elsődleges folyamat másodlagosra állítása |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/read | Visszaadja a replikációs hivatkozások listáját, vagy lekéri a megadott replikációs hivatkozások tulajdonságait. |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | A replikációs kapcsolat kényszerített leállítása vagy a partnerrel való szinkronizálás után |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Replikációs mód frissítése a szinkron vagy aszinkron üzemmódra való hivatkozáshoz |
> | Action | Microsoft.Sql/servers/databases/restorePoints/action | Új visszaállítási pont létrehozása |
> | Action | Microsoft.Sql/servers/databases/restorePoints/delete | Törli az adatbázis visszaállítási pontját. |
> | Action | Microsoft.Sql/servers/databases/restorePoints/read | Az adatbázis visszaállítási pontjait adja vissza. |
> | Action | Microsoft.Sql/servers/databases/resume/action | Az Azure SQL Datawarehouse-adatbázis folytatása |
> | Action | Microsoft.Sql/servers/databases/schemas/read | Adatbázis-séma beszerzése. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Adatbázis oszlopának beolvasása. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Egy adott oszlop érzékenységi címkéjének törlése |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Az adott oszlopra vonatkozó érzékenységi javaslatok letiltása |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Érzékenységi javaslatok engedélyezése egy adott oszlopban |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Egy adott oszlop érzékenységi címkéjének beolvasása |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Egy adott oszlop érzékenységi címkéjének létrehozása vagy frissítése |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/read | Adatbázis táblázatának beolvasása. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Tárgymutató-javaslatok listájának beolvasása egy adatbázison |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Tárgymutató-javaslat alkalmazása |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Az adott kiszolgálóhoz konfigurált adatbázis-veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Action | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Az adatbázis-veszélyforrások észlelési házirendjének módosítása egy adott adatbázisra vonatkozóan |
> | Action | Microsoft.Sql/servers/databases/securityMetrics/read | Adatbázis-biztonsági mérőszámok gyűjteményének beolvasása |
> | Action | Microsoft.Sql/servers/databases/sensitivityLabels/read | Egy adott adatbázis érzékenységi címkéjének listázása |
> | Action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | A teljesítmény növelése vagy a költséghatékonyság csökkentése érdekében a lekérdezés végrehajtási statisztikái alapján fel-vagy leskálázásával kapcsolatos javaslat visszaküldése |
> | Action | Microsoft.Sql/servers/databases/skus/read | Egy adatbázishoz elérhető SKU-gyűjtemény beolvasása |
> | Action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Szinkronizálási csoport szinkronizálásának megszakítása |
> | Action | Microsoft.Sql/servers/databases/syncGroups/delete | Töröl egy meglévő szinkronizálási csoportot. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | A Szinkronizáló központ adatbázis-sémáinak listájának visszaadása |
> | Action | Microsoft.Sql/servers/databases/syncGroups/logs/read | A szinkronizálási csoport naplóiból álló lista visszaküldése |
> | Action | Microsoft.Sql/servers/databases/syncGroups/read | Visszaadja a szinkronizálási csoportok listáját, vagy lekéri a megadott szinkronizálási csoport tulajdonságait. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | A Szinkronizáló központ adatbázis-sémájának frissítése |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | A szinkronizálási központ sémájának frissítési művelete eredményének beolvasása |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Töröl egy meglévő szinkronizálási tagot. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Visszaadja a szinkronizálási tagok listáját, vagy lekéri a megadott szinkronizálási tag tulajdonságait. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Szinkronizálási tag sémájának frissítése |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | A szinkronizálási tag séma-frissítési műveletének eredményének beolvasása |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | A szinkronizálási tag adatbázis-sémáinak listájának visszaadása |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Egy szinkronizálási tagot hoz létre a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási tag tulajdonságait. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Szinkronizálási csoport szinkronizálásának elindítása |
> | Action | Microsoft.Sql/servers/databases/syncGroups/write | Egy szinkronizálási csoportot hoz létre a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási csoport tulajdonságait. |
> | Action | Microsoft.Sql/servers/databases/topQueries/queryText/action | A kiválasztott lekérdezési azonosító Transact-SQL-szövegét adja vissza. |
> | Action | Microsoft.Sql/servers/databases/topQueries/read | Összesített futtatókörnyezeti statisztikákat ad vissza a kijelölt lekérdezéshez a kiválasztott időszakban |
> | Action | Microsoft.Sql/servers/databases/topQueries/statistics/read | Összesített futtatókörnyezeti statisztikákat ad vissza a kijelölt lekérdezéshez a kiválasztott időszakban |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Folyamatban lévő műveletek beolvasása transzparens adattitkosítással |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Egy adott adatbázis transzparens adattitkosítási biztonsági funkciójának állapotának és részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Transzparens adattitkosítási állapot módosítása |
> | Action | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Az Azure SQL Datawarehouse-adatbázis frissítése |
> | Action | Microsoft.Sql/servers/databases/usages/read | A Azure SQL Database használati adatok beolvasása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Egy adott adatbázis sebezhetőségi felmérésének eltávolítása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy givendatabase |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Az adott adatbázis sebezhetőségi felmérési szabályának alaptervének eltávolítása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének beolvasása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Egy adott adatbázis sebezhetőségi felmérési szabályának alaptervének módosítása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Meglévő vizsgálat eredményének konvertálása emberi olvasási formátumba. Ha már létezik, semmi nem történik |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Visszaadja az adatbázis sebezhetőségi felmérési rekordjainak listáját, vagy beolvassa a megadott vizsgálati AZONOSÍTÓhoz tartozó vizsgálati rekordot. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Sebezhetőség-felmérési adatbázis vizsgálatának végrehajtása. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Az adatbázis sebezhetőség-felmérési vizsgálatának végrehajtási műveletének eredményének beolvasása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Egy adott adatbázison konfigurált sebezhetőségi felmérés részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Egy adott adatbázis sebezhetőségi felmérésének módosítása |
> | Action | Microsoft.Sql/servers/databases/write | Létrehoz egy adatbázist a megadott paraméterekkel, vagy frissíti a megadott adatbázis tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/servers/delete | Töröl egy meglévő kiszolgálót. |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Egy meglévő vész-helyreállítási konfiguráció törlése egy adott kiszolgálóhoz |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | DisasterRecoveryConfiguration feladatátvétele |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | DisasterRecoveryConfiguration-feladatátvétel kényszerítése |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | A kiszolgálót tartalmazó vész-helyreállítási konfigurációk gyűjteményének beolvasása |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | A kiszolgáló vész-helyreállítási konfigurációjának módosítása |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/read | A kiszolgálóhoz már létrehozott rugalmas készletre vonatkozó becslések listáját adja vissza. |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/write | Új rugalmas készletre vonatkozó becslést hoz létre a megadott adatbázisok listájához |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/read | A rugalmas készlethez elérhető tanácsadók listájának beolvasása |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | A rugalmas készlethez megadott tanácsadó által javasolt műveletek listáját adja vissza. |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | A javasolt művelet alkalmazása a rugalmas készleten |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/write | Az Advisor automatikus végrehajtási állapotának frissítése rugalmas készlet szintjén. |
> | Action | Microsoft.Sql/servers/elasticPools/databases/read | A rugalmas készlethez tartozó adatbázisok listájának beolvasása |
> | Action | Microsoft.Sql/servers/elasticPools/delete | Meglévő rugalmas készlet törlése |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Tevékenységek és részletek beolvasása egy adott rugalmas adatbázis-készleten |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Tevékenységek és részletek beolvasása egy adott adatbázisra, amely a rugalmas adatbázis-készlet részét képezi. |
> | Action | Microsoft.Sql/servers/elasticPools/failover/action | Az ügyfél által kezdeményezett rugalmas készlet feladatátvétele. |
> | Action | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | A rugalmas adatbázis-készletek számára elérhető metrikák visszatérési típusai |
> | Action | Microsoft.Sql/servers/elasticPools/metrics/read | Rugalmas adatbázis-készletek visszaküldési metrikái |
> | Action | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Megszakítja az Azure SQL rugalmas készlet megszakítását, amely még nem fejeződött be aszinkron művelet. |
> | Action | Microsoft.Sql/servers/elasticPools/operations/read | A rugalmas készleten végrehajtott műveletek listájának visszaadása |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Az erőforrás diagnosztikai beállításainak beolvasása |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Az erőforrás diagnosztikai beállításainak létrehozása vagy frissítése |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | A rugalmas adatbázis-készletek számára elérhető metrikák visszatérési típusai |
> | Action | Microsoft.Sql/servers/elasticPools/read | Az adott kiszolgálón található rugalmas készlet adatainak beolvasása |
> | Action | Microsoft.Sql/servers/elasticPools/skus/read | Egy rugalmas készlethez rendelkezésre álló SKU-gyűjtemény beolvasása |
> | Action | Microsoft.Sql/servers/elasticPools/write | Meglévő rugalmas készlet új vagy módosított tulajdonságainak létrehozása |
> | Action | Microsoft.Sql/servers/encryptionProtector/read | A kiszolgáló titkosítási védelmi adatainak listáját adja vissza, vagy lekéri a megadott kiszolgálói titkosítási védő tulajdonságait. |
> | Action | Microsoft.Sql/servers/encryptionProtector/write | A megadott kiszolgálói titkosítási fólia tulajdonságainak frissítése. |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/read | Az adott kiszolgálón konfigurált kiterjesztett kiszolgálói blob-naplózási szabályzat részleteinek beolvasása |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/write | A kiterjesztett kiszolgáló blob-naplózásának módosítása egy adott kiszolgálón |
> | Action | Microsoft.Sql/servers/failoverGroups/delete | Töröl egy meglévő feladatátvételi csoportot. |
> | Action | Microsoft.Sql/servers/failoverGroups/failover/action | Tervezett feladatátvételt hajt végre egy meglévő feladatátvételi csoportban. |
> | Action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Kényszerített feladatátvételt hajt végre egy meglévő feladatátvételi csoportban. |
> | Action | Microsoft.Sql/servers/failoverGroups/read | A feladatátvételi csoportok listáját adja vissza, vagy lekéri a megadott feladatátvételi csoport tulajdonságait. |
> | Action | Microsoft.Sql/servers/failoverGroups/write | Létrehoz egy feladatátvételi csoportot a megadott paraméterekkel, vagy frissíti a megadott feladatátvételi csoport tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/servers/firewallRules/delete | Töröl egy meglévő kiszolgálói tűzfalszabály-szabályt. |
> | Action | Microsoft.Sql/servers/firewallRules/read | Visszaadja a kiszolgálói tűzfalszabályok listáját, vagy lekéri a megadott kiszolgálói tűzfalszabály tulajdonságait. |
> | Action | Microsoft.Sql/servers/firewallRules/write | Létrehoz egy kiszolgálói tűzfalszabály a megadott paraméterekkel, frissíti a megadott szabály tulajdonságait, vagy felülírja az összes meglévő szabályt az új kiszolgálói tűzfalszabályok (ok) használatával. |
> | Action | Microsoft.Sql/servers/import/action | Új adatbázis létrehozása a kiszolgálón és a séma és az adatok központi telepítése DacPac-csomagból |
> | Action | Microsoft.Sql/servers/importExportOperationResults/read | Folyamatban lévő importálási/exportálási műveletek beolvasása |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | A megadott csatoló-végponti profil törlése |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/read | A megadott csatoló-végpont profiljának tulajdonságait adja vissza. |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Létrehoz egy illesztőfelület-végponti profilt a megadott paraméterekkel, vagy frissíti a megadott csatoló-végpont tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/servers/jobAgents/delete | Az Azure SQL DB-feladatok ügynökének törlése |
> | Action | Microsoft.Sql/servers/jobAgents/read | Azure SQL Database-feladatok ügynökének beolvasása |
> | Action | Microsoft.Sql/servers/jobAgents/write | Létrehoz vagy frissít egy Azure SQL DB-feladatot kezelő ügynököt |
> | Action | Microsoft.Sql/servers/keys/delete | Töröl egy meglévő kiszolgálói kulcsot. |
> | Action | Microsoft.Sql/servers/keys/read | Visszaadja a kiszolgálói kulcsok listáját, vagy lekéri a megadott kiszolgálói kulcs tulajdonságait. |
> | Action | Microsoft.Sql/servers/keys/write | Létrehoz egy kulcsot a megadott paraméterekkel, vagy frissíti a megadott kiszolgálói kulcs tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/servers/operationResults/read | Folyamatban lévő kiszolgálói műveletek beolvasása |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Meglévő privát végponti kapcsolatkezelő törlése |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | A privát végponti kapcsolatok proxyinak listáját adja vissza, vagy beolvassa a megadott privát végpont-összekötő tulajdonságait. |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Egy privát végponti kapcsolatok ellenőrzése az NRP oldalról |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Létrehoz egy privát végponti ügyfélkapcsolati proxyt a megadott paraméterekkel, vagy frissíti a megadott magánhálózati végponthoz tartozó tulajdonságokat vagy címkéket. |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/delete | Meglévő magánhálózati végponti kapcsolatok törlése |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/read | A privát végponti kapcsolatok listáját adja vissza, vagy lekéri a megadott privát végponti kapcsolat tulajdonságait. |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/write | Meglévő magánhálózati végponti kapcsolatok jóváhagyása vagy elutasítása |
> | Action | Microsoft. SQL/kiszolgálók/privateEndpointConnectionsApproval/művelet | Meghatározza, hogy a felhasználó jóváhagyhat-e privát végponti kapcsolatokat |
> | Action | Microsoft.Sql/servers/privateLinkResources/read | A kapcsolódó SQL Serverhez tartozó magánhálózati kapcsolati erőforrások beolvasása |
> | Action | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | A kiszolgálók számára elérhető metrikák visszatérési típusai |
> | Action | Microsoft.Sql/servers/read | A kiszolgálók listájának visszaadása vagy a megadott kiszolgáló tulajdonságainak beolvasása. |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Az ajánlott rugalmas adatbázis-készletek metrikáinak beolvasása egy adott kiszolgálóhoz |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/read | A rugalmas adatbázis-készletek javaslatának beolvasása, amely csökkenti a költségeket, vagy javítja a teljesítményt a korábbi erőforrások kihasználtsága alapján |
> | Action | Microsoft.Sql/servers/recoverableDatabases/read | Ez a művelet az élő adatbázis vész-helyreállítására szolgál az adatbázis utolsó ismert jó biztonsági mentési pontra való visszaállításához. Az utolsó jó biztonsági mentésre vonatkozó adatokat ad vissza, de az doesn\u0027t valójában visszaállítja az adatbázist. |
> | Action | Microsoft.Sql/servers/replicationLinks/read | Visszaadja a replikációs hivatkozások listáját, vagy lekéri a megadott replikációs hivatkozások tulajdonságait. |
> | Action | Microsoft.Sql/servers/restorableDroppedDatabases/read | Azon adatbázisok listájának lekérése, amelyek a megőrzési házirendben még nem találhatók meg az adott kiszolgálón. |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | A kiszolgálói veszélyforrások észlelése házirend írási műveletének eredményeinek beolvasása |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/read | Egy adott kiszolgálóhoz konfigurált kiszolgálói veszélyforrások észlelési házirendjeinek listájának beolvasása |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/write | A kiszolgálói veszélyforrások észlelési házirendjének módosítása egy adott kiszolgálóhoz |
> | Action | Microsoft.Sql/servers/serviceObjectives/read | Az adott kiszolgálón elérhető szolgáltatási szintű célkitűzések (más néven teljesítményszint) listájának beolvasása |
> | Action | Microsoft.Sql/servers/syncAgents/delete | Töröl egy meglévő szinkronizálási ügynököt. |
> | Action | Microsoft.Sql/servers/syncAgents/generateKey/action | Szinkronizálási ügynök regisztrációs kulcsának előállítása |
> | Action | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | A szinkronizálási ügynökhöz csatolt adatbázisok listájának visszaadása |
> | Action | Microsoft.Sql/servers/syncAgents/read | Visszaadja a Szinkronizáló ügynökök listáját, vagy lekéri a megadott szinkronizálási ügynök tulajdonságait. |
> | Action | Microsoft.Sql/servers/syncAgents/write | Létrehoz egy szinkronizáló ügynököt a megadott paraméterekkel, vagy frissíti a megadott szinkronizálási ügynök tulajdonságait. |
> | Action | Microsoft.Sql/servers/tdeCertificates/action | TDE-tanúsítvány létrehozása/frissítése |
> | Action | Microsoft.Sql/servers/usages/read | A kiszolgáló DTU-kvótájának és aktuális DTU-consuption visszaadása a kiszolgálón található összes adatbázis alapján |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/delete | Meglévő Virtual Network-szabály törlése |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/read | Visszaadja a virtuális hálózati szabályok listáját, vagy lekéri a megadott virtuális hálózati szabály tulajdonságait. |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/write | Létrehoz egy virtuális hálózati szabályt a megadott paraméterekkel, vagy frissíti a megadott virtuális hálózati szabály tulajdonságait vagy címkéit. |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Egy adott kiszolgáló sebezhetőségi felmérésének eltávolítása |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/read | A sebezhetőségi felmérési szabályzatok beolvasása egy adott kiszolgálón |
> | Action | Microsoft.Sql/servers/vulnerabilityAssessments/write | Egy adott kiszolgáló sebezhetőségi felmérésének módosítása |
> | Action | Microsoft.Sql/servers/write | Létrehoz egy kiszolgálót a megadott paraméterekkel, vagy frissíti a megadott kiszolgálóhoz tartozó tulajdonságokat vagy címkéket. |
> | Action | Microsoft.Sql/unregister/action | A Microsoft SQL Database erőforrás-szolgáltatóhoz tartozó előfizetés regisztrációjának törlése és a Microsoft SQL-adatbázisok létrehozásának engedélyezése. |
> | Action | Microsoft.Sql/virtualClusters/delete | Töröl egy meglévő virtuális fürtöt. |
> | Action | Microsoft.Sql/virtualClusters/read | Visszaadja a virtuális fürtök listáját, vagy lekéri a megadott virtuális fürt tulajdonságait. |
> | Action | Microsoft.Sql/virtualClusters/write | Frissíti a virtuális fürtök címkéit. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Storage/checknameavailability/read | Ellenőrzi, hogy érvényes-e a fióknév, és nincs-e használatban. |
> | Action | Microsoft.Storage/locations/checknameavailability/read | Ellenőrzi, hogy érvényes-e a fióknév, és nincs-e használatban. |
> | Action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | A Microsoft.Storage értesítése arról, hogy virtuális hálózat vagy alhálózat törlése van folyamatban |
> | Action | Microsoft.Storage/locations/usages/read | A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza |
> | Action | Microsoft.Storage/operations/read | Az aszinkrón műveletek állapotának lekérdezése. |
> | Action | Microsoft.Storage/register/action | A tárolásierőforrás-szolgáltató előfizetésének regisztrálása és a tárfiókkészítés engedélyezése. |
> | Action | Microsoft.Storage/skus/read | A Microsoft.Storage által támogatott termékváltozatok listázása. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | A blobtartalom hozzáadásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Egy blob törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Egy automatikus pillanatkép törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | A fiók alatti blobok (nagy bináris objektumok) listáját adja vissza az egyező címkék szűrővel |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Blobot vagy blobok listáját adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | A blobcímkék olvasásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | A blobcímkék írásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Blob írásának eredményét adja vissza |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Blobtároló visszatartásának törlése |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | A tároló törlésének eredményét adja vissza |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Blobtároló módosíthatatlansági szabályzatának törlése |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Blobtároló bővítésére vonatkozó módosíthatatlansági szabályzat |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Blobtároló zárolására vonatkozó módosíthatatlansági szabályzat |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Blobtároló beolvasására vonatkozó módosíthatatlansági szabályzat |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Blobtárolón végrehajtott put műveletre vonatkozó módosíthatatlansági szabályzat |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | A blobtárolóbérlet megszerzésére irányuló művelet eredményének visszaadása |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Tároló visszaadása |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | A tárolók listájának visszaadása |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Blobtároló visszatartásának beállítása |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Blobtároló-javítás eredményének visszaadása |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | A blob-tároló Put eredményét adja vissza. |
> | Action | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Felhasználói delegálási kulcsot ad vissza a Blob service szolgáltatáshoz |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read | A Blob szolgáltatás tulajdonságait vagy a statisztikát adja vissza |
> | Action | Microsoft.Storage/storageAccounts/blobServices/write | A blobszolgáltatás tulajdonságain végrehajtott Put művelet eredményének visszaadása |
> | Action | Microsoft.Storage/storageAccounts/delete | Meglévő tárfiók törlése. |
> | Action | Microsoft. Storage/storageAccounts/feladatátvétel/művelet | Az ügyfél rendelkezésre állási problémák esetén vezérelheti a feladatátvételt |
> | Action | Microsoft.Storage/storageAccounts/fileServices/read | Fájlszolgáltatás tulajdonságainak lekérdezése |
> | Action | Microsoft.Storage/storageAccounts/listAccountSas/action | A fiók SAS-jogkivonatát adja vissza a megadott tárfiókhoz. |
> | Action | Microsoft.Storage/storageAccounts/listkeys/action | A megadott tárfiók hozzáférési kulcsainak lekérése. |
> | Action | Microsoft.Storage/storageAccounts/listServiceSas/action | A szolgáltatás SAS-jogkivonatát adja vissza a meghatározott tárfiókhoz. |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/delete | Tárfiók kezelési szabályzatainak törlése |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/read | A tárfiók kezelési szabályzatainak beolvasása |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/write | Tárfiók kezelési szabályzatainak eltárolása |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Privát végponti kapcsolatok Proxyinak törlése |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Magánhálózati végponti kapcsolatok Proxyinak elhelyezése |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Privát végponti kapcsolatok törlése |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Privát végponti kapcsolatok beszerzése |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Magánhálózati végponti kapcsolatok elhelyezése |
> | Action | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/művelet | Privát végponti kapcsolatok jóváhagyása |
> | Action | Microsoft.Storage/storageAccounts/privateLinkResources/read | StorageAccount-groupids beolvasása |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Sor törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Üzenet hozzáadásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Üzenet törlésének eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Üzenet feldolgozásának eredményét adja vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Egy üzenetet ad vissza |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Üzenet írásának eredményét adja vissza |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/read | A sort vagy a sorok listáját adja vissza. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/write | A sor írásának eredményét adja vissza |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Queue szolgáltatás tulajdonságainak lekérdezése |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | A Queue szolgáltatás tulajdonságait vagy a statisztikát adja vissza. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/write | A Queue szolgáltatás tulajdonságai beállításának eredményét adja vissza |
> | Action | Microsoft.Storage/storageAccounts/read | A tárfiókok listájának vagy a megadott tárfiók tulajdonságainak lekérése. |
> | Action | Microsoft.Storage/storageAccounts/regeneratekey/action | A megadott tárfiók hozzáférési kulcsainak újragenerálása. |
> | Action | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | BLOB-tartományok visszaállítása a megadott idő állapotára |
> | Action | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | A megadott tárfiók összes felhasználódelegálási kulcsának visszavonása. |
> | Action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Tárfiók diagnosztikai beállításainak létrehozása vagy frissítése. |
> | Action | Microsoft.Storage/storageAccounts/tableServices/read | Table service tulajdonságainak lekérdezése |
> | Action | Microsoft.Storage/storageAccounts/write | Tárfiók létrehozása a megadott paraméterekkel, a tulajdonságok vagy címkék frissítése, vagy egyéni tartomány felvétele a megadott tárfiókhoz. |
> | Action | Microsoft.Storage/usages/read | A megadott előfizetésben szereplő erőforrások korlátját és jelenlegi kihasználtsági értéket adja vissza |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | microsoft.storagesync/locations/checkNameAvailability/action | Ellenőrzi, hogy a Storage Sync szolgáltatás neve érvényes-e, és nincs-e használatban. |
> | Action | Microsoft. storagesync/helyszínek/munkafolyamatok/műveletek/olvasás | Egy aszinkron művelet állapotát olvassa be |
> | Action | Microsoft. storagesync/Operations/READ | A támogatott műveletek listájának beolvasása |
> | Action | Microsoft. storagesync/regisztráció/művelet | Regisztrálja az előfizetést a Storage Sync Provider számára |
> | Action | microsoft.storagesync/storageSyncServices/delete | A Storage Sync szolgáltatásainak törlése |
> | Action | Microsoft. storagesync/storageSyncServices/Providers/Microsoft. bepillantást/metricDefinitions/READ | A Storage Sync Services elérhető metrikáinak beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/read | A Storage Sync szolgáltatásainak beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/delete | Bármely regisztrált kiszolgáló törlése |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | A regisztrált kiszolgáló elérhető metrikáinak beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/read | Bármely regisztrált kiszolgáló beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/write | Bármely regisztrált kiszolgáló létrehozása vagy frissítése |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Bármilyen Felhőbeli végpont törlése |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Aszinkron biztonsági mentési/visszaállítási művelet állapotának beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | A művelet meghívása a biztonsági mentés után |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | A művelet meghívása a visszaállítás után |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | A művelet meghívása a biztonsági mentés előtt |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | A művelet meghívása a visszaállítás előtt |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Felhőbeli végpontok beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Szívverés visszaállítása |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Felhőalapú végpontok létrehozása vagy frissítése |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/delete | Szinkronizált csoportok törlése |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/Providers/Microsoft. bepillantást/metricDefinitions/READ | A szinkronizálási csoportok elérhető metrikáinak beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/read | Bármely szinkronizálási csoport olvasása |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Bármely kiszolgálói végpont törlése |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Providers/Microsoft. bepillantást/metricDefinitions/READ | A kiszolgálói végpontok elérhető metrikáinak beolvasása |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Bármely kiszolgálói végpont olvasása |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | A művelet meghívása a fájlok egy kiszolgálóra való felidézéséhez |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Bármely kiszolgálói végpont létrehozása vagy frissítése |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/write | Szinkronizált csoportok létrehozása vagy frissítése |
> | Action | Microsoft. storagesync/storageSyncServices/munkafolyamatok/operationresults/olvasás | Egy aszinkron művelet állapotát olvassa be |
> | Action | Microsoft. storagesync/storageSyncServices/munkafolyamatok/műveletek/olvasás | Egy aszinkron művelet állapotát olvassa be |
> | Action | Microsoft. storagesync/storageSyncServices/munkafolyamatok/olvasás | Munkafolyamatok olvasása |
> | Action | Microsoft. storagesync/storageSyncServices/Write | Tárolási szinkronizálási szolgáltatások létrehozása vagy frissítése |
> | Action | Microsoft. storagesync/regisztráció/művelet | A Storage Sync Provider előfizetés regisztrációjának törlése |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/delete | Törli a Access Control rekordokat. |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/read | A Access Control rekordok listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/write | A Access Control rekordok létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/alerts/read | A riasztások listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/backups/read | A biztonságimásolat-készlet listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/delete | Meglévő sávszélesség-beállítások törlése (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/read | A sávszélesség-beállítások listázása (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/write | Új vagy frissített sávszélesség-beállításokat hoz létre (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/certificates/write | Tanúsítványok létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/Managers/certificates/write | Az erőforrás-tanúsítvány frissítése művelet frissíti az erőforrás/tároló hitelesítő adatait tartalmazó tanúsítványt. |
> | Action | Microsoft.StorSimple/managers/clearAlerts/action | Törölje az Eszközkezelőhöz társított összes riasztást. |
> | Action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | A Cloud Appliance által támogatott konfigurációk listázása |
> | Action | Microsoft.StorSimple/managers/configureDevice/action | Eszköz konfigurálása |
> | Action | Microsoft.StorSimple/managers/delete | Az eszközkezelők törlése |
> | Action | Microsoft.StorSimple/Managers/delete | A tár törlése művelet törli a "Vault" típusú megadott Azure-erőforrást. |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/read | A riasztási beállítások listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/write | Riasztási beállítások létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | A szolgáltatás titkosítási kulcsának átváltásának engedélyezése az eszközökön |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Készítsen manuális biztonsági mentést a szabályzat által védett összes kötetre vonatkozó igény szerinti biztonsági mentés létrehozásához. |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Meglévő biztonsági mentési házirendek törlése (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/read | A biztonsági mentési házirendek listázása (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Meglévő ütemtervek törlése |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Az ütemtervek listázása |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Új vagy frissítési ütemtervet hoz létre |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/write | Új vagy frissítési biztonsági mentési szabályzatok létrehozása (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/devices/backups/delete | A biztonságimásolat-készlet törlése |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Megosztás vagy kötet klónozása egy biztonsági mentési elem használatával. |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/backups/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft. StorSimple/vezetők/eszközök/biztonsági másolatok/olvasás | A biztonságimásolat-készlet listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/backups/restore/action | Állítsa vissza az összes kötetet egy biztonságimásolat-készletből. |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | A biztonsági mentési ütemterv csoportok törlése |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft. StorSimple/vezetők/eszközök/backupScheduleGroups/olvasás | A biztonsági mentési ütemterv csoportok listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | A biztonsági mentési ütemterv csoportok létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/delete | A CHAP-beállítások törlése |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/read | A CHAP-beállítások listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/write | A CHAP-beállítások létrehozása vagy frissítése |
> | Action | Microsoft. StorSimple/vezetők/eszközök/inaktiválás/művelet | Inaktiválja az eszközt. |
> | Action | Microsoft.StorSimple/managers/devices/delete | Az eszközök törlése |
> | Action | Microsoft.StorSimple/managers/devices/disks/read | A lemezek listája vagy beolvasása |
> | Action | Microsoft. StorSimple/vezetők/eszközök/Letöltés/művelet | Egy eszköz frissítéseinek letöltése. |
> | Action | Microsoft. StorSimple/vezetők/eszközök/feladatátvétel/művelet | Az eszköz feladatátvétele. |
> | Action | Microsoft.StorSimple/managers/devices/failover/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft. StorSimple/vezetők/eszközök/failoverTargets/olvasás | Felsorolja vagy lekéri az eszközök feladatátvételi céljait |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Készítsen biztonsági másolatot a fájlkiszolgálón. |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/delete | A fájlkiszolgálók törlése |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | A metrikák listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/read | A fájlkiszolgálók listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | A megosztások törlése |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | A metrikák listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/read | A megosztások listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Megosztások létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/write | Fájlkiszolgálók létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Hardver-összetevő csoportok vezérlő energiagazdálkodási állapotának módosítása |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft. StorSimple/vezetők/eszközök/hardwareComponentGroups/olvasás | A hardver-összetevő csoportjai listázása |
> | Action | Microsoft. StorSimple/vezetők/eszközök/telepítés/művelet | Frissítések telepítése az eszközön. |
> | Action | Microsoft.StorSimple/managers/devices/installUpdates/action | Telepíti a frissítéseket az eszközökön (csak 8000 sorozat esetén). |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Készítsen biztonsági másolatot egy iSCSI-kiszolgálóról. |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Az iSCSI-kiszolgálók törlése |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | A lemezek törlése |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | A metrikák listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | A lemezek listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Lemezek létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | A metrikák listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/read | Az iSCSI-kiszolgálók listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/write | ISCSI-kiszolgálók létrehozása vagy frissítése |
> | Action | Microsoft. StorSimple/vezetők/eszközök/feladatok/megszakítás/művelet | Futó feladat megszakítása |
> | Action | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft. StorSimple/vezetők/eszközök/feladatok/olvasás | A feladatok listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Egy meglévő eszközhöz tartozó feladatátvevő készletek listázása (csak 8000 sorozat esetén). |
> | Action | Microsoft. StorSimple/vezetők/eszközök/listFailoverTargets/művelet | Az eszközök feladatátvételi céljainak listázása (csak 8000 sorozat esetén). |
> | Action | Microsoft.StorSimple/managers/devices/metrics/read | A metrikák listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Action | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigration/Action | Ellenőrzi a sikeres áttelepítést, és véglegesíti azt. |
> | Action | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/confirmMigrationStatus/READ | Az áttelepítés megerősítési állapotának listázása |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Az áttelepítés megerősítési állapotának beolvasása. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Az áttelepítési becslési feladatokhoz tartozó állapot beolvasása. |
> | Action | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/fetchMigrationStatus/Action | Az áttelepítés állapotának beolvasása. |
> | Action | Microsoft. StorSimple/vezetők/eszközök/migrationSourceConfigurations/importálás/művelet | Forrás-konfigurációk importálása az áttelepítéshez |
> | Action | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/migrationEstimate/READ | Az áttelepítési becslés listázása |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Az áttelepítés állapotának listázása |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigration/Action | Áttelepítés indítása a forrás-konfigurációk használatával |
> | Action | Microsoft. StorSimple/Managers/Devices/migrationSourceConfigurations/startMigrationEstimate/Action | Az áttelepítési folyamat időtartamának becsléséhez indítsa el a feladatot. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/read | A hálózati beállítások listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/write | Új vagy frissített hálózati beállításokat hoz létre |
> | Action | Microsoft.StorSimple/managers/devices/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Az Eszközkezelő nyilvános titkosítási kulcsának listázása |
> | Action | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Egy meglévő eszköz támogatási csomagjának közzététele. A StorSimple-támogatási csomag egy könnyen használható mechanizmus, amely az összes releváns naplót gyűjti, hogy segítséget nyújtson Microsoft ügyfélszolgálatanek a StorSimple-eszközök hibáinak elhárításában. |
> | Action | Microsoft.StorSimple/managers/devices/read | Az eszközök listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Frissítések keresése az eszközön. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/read | A biztonsági beállítások listázása |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Egy eszköz távfelügyeleti tanúsítványának szinkronizálása. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Frissítse a biztonsági beállításokat. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/write | Új vagy frissített biztonsági beállítások létrehozása |
> | Action | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Tesztüzenet küldése e-mailben a konfigurált e-mail-címzetteknek. |
> | Action | Microsoft.StorSimple/managers/devices/shares/read | A megosztások listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/read | Felsorolja vagy lekéri az időbeállításokat |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/write | Új vagy frissítési idő beállításainak létrehozása |
> | Action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/devices/updateSummary/read | Felsorolja vagy lekéri a frissítés összegzését |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Meglévő mennyiségi tárolók törlése (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | A metrikák listázása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | A metrikák definícióinak listázása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/read | A mennyiségi tárolók listázása (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Meglévő kötetek törlése |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | A metrikák listázása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | A metrikák definícióinak listázása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | A művelet eredményeinek listázása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Kötetek listázása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Új vagy frissített kötetek létrehozása |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/write | Létrehoz egy új vagy frissített kötet-tárolót (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/devices/volumes/read | Kötetek listázása |
> | Action | Microsoft.StorSimple/managers/devices/write | Eszközök létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/encryptionSettings/read | A titkosítási beállítások listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/extendedInformation/delete | A kiterjesztett tár adatainak törlése |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Action | Microsoft.StorSimple/managers/extendedInformation/read | A kiterjesztett tár adatainak listája vagy beolvasása |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Action | Microsoft.StorSimple/managers/extendedInformation/write | A kiterjesztett tár adatainak létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | A Részletes adatok beolvasása művelet a ?vault? típusú Azure-erőforrásokra vonatkozó bővített objektumadatokat olvassa be |
> | Action | Microsoft.StorSimple/managers/features/read | A funkciók listázása |
> | Action | Microsoft.StorSimple/managers/fileservers/read | A fájlkiszolgálók listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/getEncryptionKey/action | Az Eszközkezelő titkosítási kulcsának beolvasása. |
> | Action | Microsoft.StorSimple/managers/iscsiservers/read | Az iSCSI-kiszolgálók listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/jobs/read | A feladatok listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/listActivationKey/action | A StorSimple Eszközkezelő aktiválási kulcsának beolvasása. |
> | Action | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Egy StorSimple Eszközkezelő nyilvános titkosítási kulcsainak listázása. |
> | Action | Microsoft.StorSimple/managers/metrics/read | A metrikák listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/metricsDefinitions/read | A metrikák definícióinak listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Klasszikus áttelepítése a Jászolok Resource Managerbe |
> | Action | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Áttelepítési forrás konfigurációinak listázása (csak 8000 sorozat) |
> | Action | Microsoft.StorSimple/managers/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Hozzon létre egy új felhőalapú készüléket. |
> | Action | Microsoft.StorSimple/managers/read | Az eszközkezelők listája vagy beolvasása |
> | Action | Microsoft.StorSimple/Managers/read | A tár beolvasása művelet lekérdezi a Vault típusú Azure-erőforrást jelképező objektumot. |
> | Action | Microsoft.StorSimple/managers/regenerateActivationKey/action | Egy meglévő StorSimple Eszközkezelő aktiválási kulcsának újragenerálta. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | A Storage-fiók hitelesítő adatainak törlése |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/read | A Storage-fiók hitelesítő adatainak listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/write | A Storage-fiók hitelesítő adatainak létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/storageDomains/delete | Törli a tárolási tartományokat |
> | Action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | A művelet eredményeinek megjelenítése vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/storageDomains/read | A tárolási tartományok listája vagy beolvasása |
> | Action | Microsoft.StorSimple/managers/storageDomains/write | Tárolási tartományok létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/managers/write | Eszközkezelők létrehozása vagy frissítése |
> | Action | Microsoft.StorSimple/Managers/write | A tárlétrehozási művelettel vault típusú Azure-erőforrás hozható létre |
> | Action | Microsoft.StorSimple/operations/read | A műveletek listája vagy beolvasása |
> | Action | Microsoft.StorSimple/register/action | Szolgáltató regisztrálása a Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.StreamAnalytics/locations/quotas/Read | Stream Analytics előfizetés kvótájának beolvasása |
> | Action | Microsoft.StreamAnalytics/operations/Read | Stream Analytics műveletek beolvasása |
> | Action | Microsoft.StreamAnalytics/Register/action | Előfizetés regisztrálása Stream Analytics erőforrás-szolgáltatóval |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Delete | Stream Analytics-feladatok törlése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Stream Analytics feladat funkciójának törlése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | A Stream Analytics Job függvény olvasási műveletének eredményei |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Stream Analytics feladat funkciójának beolvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Stream Analytics feladat funkciójának alapértelmezett definíciójának beolvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics feladat funkciójának tesztelése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Írási Stream Analytics feladat függvény |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Stream Analytics-feladathoz tartozó bevitel törlése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Stream Analytics-feladathoz való bevitel olvasási műveletének eredményei |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Stream Analytics-feladatba való bevitel beolvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Példa Stream Analytics feladatba való bevitelre |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Stream Analytics-feladathoz való bevitel tesztelése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Írási Stream Analytics feladatok bevitele |
> | Action | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Metrikus definíciók olvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Stream Analytics feladatokhoz tartozó olvasási művelet eredményei |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Stream Analytics feladatok kimenetének törlése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Stream Analytics feladatok kimenetének olvasási műveletének eredményei |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Stream Analytics a feladatok kimenetének olvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Stream Analytics feladatok kimenetének tesztelése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Stream Analytics a feladatok kimenetének írása |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantást/diagnosticSettings/READ | Diagnosztikai beállítások beolvasása. |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantások/diagnosticSettings/írás | Diagnosztikai beállítás írása |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Providers/Microsoft. bepillantást/logDefinitions/READ | Lekéri a streamingjobs elérhető naplóit |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | A streamingjobs elérhető metrikáinak beolvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Read | Stream Analytics-feladatok beolvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Start/action | Stream Analytics feladatok indítása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stream Analytics feladatok leállítása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Stream Analytics feladatok átalakításának törlése |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Stream Analytics feladatok átalakításának olvasása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Stream Analytics feladatok átalakításának írása |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Write | Stream Analyticsi feladatok írása |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. előfizetés/megszakítás/művelet | Az előfizetés megszakítása |
> | Action | Microsoft.Subscription/CreateSubscription/action | Azure-előfizetés létrehozása |
> | Action | Microsoft.Subscription/register/action | Előfizetés regisztrálása a Microsoft. előfizetés erőforrás-szolgáltatónál |
> | Action | Microsoft. előfizetés/Átnevezés/művelet | Átnevezi az előfizetést |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/read | Azure-előfizetések definíciójának beszerzése egy felügyeleti csoporton belül. |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/write | Azure-előfizetés definíciójának létrehozása |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.Support/register/action | Regisztrálás a támogatási erőforrás-szolgáltatóra |
> | Action | Microsoft.Support/supportTickets/read | Egy támogatási jegy adatainak beolvasása (beleértve az állapotot, a súlyosságot, a kapcsolattartási adatokat és a kommunikációs elemeket), vagy az előfizetésekhez tartozó támogatási jegyek listájának beolvasása. |
> | Action | Microsoft.Support/supportTickets/write | Támogatási jegy létrehozása vagy frissítése. Támogatási jegyet technikai, számlázási, kvótákkal kapcsolatos vagy előfizetés-kezelési problémákkal kapcsolatban hozhat létre. A meglévő támogatási jegyeknek frissítheti a súlyosságát, kapcsolattartási adatait és kommunikációit. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Törli a hozzáférési házirendet. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hozzáférési szabályzat tulajdonságainak beolvasása. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Új hozzáférési szabályzatot hoz létre egy környezethez, vagy frissít egy meglévő hozzáférési szabályzatot. |
> | Action | Microsoft.TimeSeriesInsights/environments/delete | Törli a környezetet. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Az eseményforrás törlése. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/read | Egy eseményforrás tulajdonságainak beolvasása. |
> | Action | Microsoft. TimeSeriesInsights/Environments/eventsources/Write | Új eseményforrás létrehozása egy környezethez, vagy egy meglévő eseményforrás frissítése. |
> | Action | Microsoft.TimeSeriesInsights/environments/read | Egy környezet tulajdonságainak beolvasása. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Törli a hivatkozási adatkészletet. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Egy hivatkozási adathalmaz tulajdonságainak beolvasása. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Új hivatkozási adatkészletet hoz létre egy adott környezethez, vagy frissít egy meglévő hivatkozási adatkészletet. |
> | Action | Microsoft.TimeSeriesInsights/environments/status/read | A környezet állapotának lekérése, a hozzá tartozó műveletek állapota, például bejövő adatok. |
> | Action | Microsoft.TimeSeriesInsights/environments/write | Új környezet létrehozása vagy egy meglévő környezet frissítése. |
> | Action | Microsoft.TimeSeriesInsights/register/action | Regisztrálja az előfizetést a Time Series Insights erőforrás-szolgáltatóhoz, és lehetővé teszi Time Series Insights környezetek létrehozását. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft.VisualStudio/Account/Delete | Fiók törlése |
> | Action | Microsoft.VisualStudio/Account/Extension/Read | Fiók/bővítmény olvasása |
> | Action | Microsoft.VisualStudio/Account/Project/Read | Fiók/projekt olvasása |
> | Action | Microsoft.VisualStudio/Account/Project/Write | Fiók/projekt beállítása |
> | Action | Microsoft.VisualStudio/Account/Read | Fiók beolvasása |
> | Action | Microsoft.VisualStudio/Account/Write | Fiók beállítása |
> | Action | Microsoft.VisualStudio/Extension/Delete | Bővítmény törlése |
> | Action | Microsoft.VisualStudio/Extension/Read | Olvasási bővítmény |
> | Action | Microsoft.VisualStudio/Extension/Write | Bővítmény beállítása |
> | Action | Microsoft.VisualStudio/Project/Delete | Projekt törlése |
> | Action | Microsoft.VisualStudio/Project/Read | Projekt beolvasása |
> | Action | Microsoft.VisualStudio/Project/Write | Projekt beállítása |
> | Action | Microsoft.VisualStudio/Register/Action | Azure-előfizetés regisztrálása a Microsoft. VisualStudio-szolgáltatóval |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | microsoft.web/apimanagementaccounts/apiacls/read | Az API Management-fiókok Apiacls beolvasása. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Az API Management-fiókok API-Apiacls törlése. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/read | Az API Management-fiókok API-Apiacls beolvasása. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/write | Az API Management-fiókok API-Apiacls frissítése. |
> | Action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Az API Management-fiókok API-Connectionacls beolvasása. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Erősítse meg a jóváhagyás kód API Management-fiókok API-kapcsolatait. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Az API Management-fiókok API-kapcsolatainak törlése Connectionacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | API Management-fiókok API-kapcsolatok Connectionacls beolvasása. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Az API Management-fiókok API-kapcsolatainak Connectionacls frissítése. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/delete | API Management-fiókok API-kapcsolatainak törlése. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Az API Management-fiókok API-kapcsolataihoz tartozó engedélyezési hivatkozások beolvasása. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Kapcsolati kulcsok API Management-fiókok API-kapcsolatainak listázása. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | A Secrets API Management-fiókok API-kapcsolatainak listázása. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/read | API Management-fiókok API-kapcsolatainak beolvasása. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/write | API Management-fiókok API-kapcsolatainak frissítése. |
> | Action | microsoft.web/apimanagementaccounts/apis/delete | Az API Management-fiókok API-k törlése. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Az API Management-fiókok API-k honosított definícióinak törlése. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Az API Management-fiókok API-k honosított definícióinak beolvasása. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Az API Management-fiókok API-k honosított definícióinak frissítése. |
> | Action | microsoft.web/apimanagementaccounts/apis/read | API Management-fiókok API-k beszerzése. |
> | Action | microsoft.web/apimanagementaccounts/apis/write | Frissítse az API Management accounts API-kat. |
> | Action | microsoft.web/apimanagementaccounts/connectionacls/read | Az API Management-fiókok Connectionacls beolvasása. |
> | Action | Microsoft. Web/availablestacks/READ | Elérhető stackek beolvasása. |
> | Action | Microsoft. Web/tanúsítványok/törlés | Meglévő tanúsítvány törlése. |
> | Action | Microsoft. Web/tanúsítványok/olvasás | A tanúsítványok listájának beolvasása. |
> | Action | Microsoft. Web/tanúsítványok/írás | Adjon hozzá egy új tanúsítványt, vagy frissítsen egy meglévőt. |
> | Action | Microsoft. Web/checknameavailability/READ | Ellenőrizze, hogy elérhető-e az erőforrás neve. |
> | Action | Microsoft. Web/classicmobileservices/READ | Klasszikus Mobile Services beolvasása. |
> | Action | Microsoft. Web/connectionGateways/delete | Egy összekötő átjáró törlése. |
> | Action | Microsoft. Web/connectionGateways/csatlakozás/művelet | Kapcsolódási átjáróhoz csatlakozik. |
> | Action | Microsoft. Web/connectionGateways/ListStatus/művelet | Egy összekötő átjáró állapotának felsorolása. |
> | Action | Microsoft. Web/connectionGateways/Move/Action | Egy összekötő átjáró mozgatása. |
> | Action | Microsoft. Web/connectionGateways/READ | A kapcsolatok átjáróinak listájának beolvasása. |
> | Action | Microsoft. Web/connectionGateways/írás | Létrehoz vagy frissít egy összekötő átjárót. |
> | Action | microsoft.web/connections/confirmconsentcode/action | Erősítse meg a kapcsolatok jóváhagyásának kódját. |
> | Action | Microsoft. Web/Connections/delete | Töröl egy-kapcsolatokat. |
> | Action | Microsoft. Web/kapcsolatok/csatlakozás/művelet | Csatlakozik a csatlakozáshoz. |
> | Action | microsoft.web/connections/listconsentlinks/action | A kapcsolatok csatlakozási kapcsolatainak listázása. |
> | Action | Microsoft. Web/Connections/Move/Action | Egy kapcsolatok áthelyezése. |
> | Action | Microsoft. Web/Connections/READ | A kapcsolatok listájának beolvasása. |
> | Action | Microsoft. Web/Connections/Write | Létrehozza vagy frissíti a kapcsolatokat. |
> | Action | Microsoft.Web/customApis/Delete | Töröl egy egyéni API-t. |
> | Action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Az API-definíció kibontása egy WSDL-ből. |
> | Action | Microsoft.Web/customApis/Join/Action | Csatlakozik egy egyéni API-hoz. |
> | Action | Microsoft.Web/customApis/listWsdlInterfaces/Action | Az egyéni API WSDL-felületeit listázza. |
> | Action | Microsoft.Web/customApis/Move/Action | Áthelyez egy egyéni API-t. |
> | Action | Microsoft.Web/customApis/Read | Az egyéni API-k listájának beolvasása. |
> | Action | Microsoft.Web/customApis/Write | Létrehoz vagy frissít egy egyéni API-t. |
> | Action | Microsoft.Web/deletedSites/Read | Törölt webalkalmazás tulajdonságainak beolvasása |
> | Action | Microsoft. Web/deploymentlocations/READ | Üzembe helyezési helyszínek beolvasása. |
> | Action | Microsoft. Web/geoRegions/READ | A földrajzi régiók listájának beolvasása. |
> | Action | microsoft.web/hostingenvironments/capacities/read | Üzemeltetési környezetek kapacitásainak beolvasása. |
> | Action | Microsoft.Web/hostingEnvironments/Delete | App Service Environment törlése |
> | Action | microsoft.web/hostingenvironments/detectors/read | Üzemeltetési környezetek érzékelők beszerzése. |
> | Action | Microsoft. Web/hostingenvironments/diagnosztika/olvasás | Üzemeltetési környezetek diagnosztika beszerzése. |
> | Action | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Az összes bejövő függőség hálózati végpontjának beolvasása. |
> | Action | Microsoft. Web/hostingEnvironments/csatlakozás/művelet | Egy App Service Environment illesztése |
> | Action | microsoft.web/hostingenvironments/metricdefinitions/read | Üzemeltetési környezetek metrikai definícióinak beolvasása. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Üzemeltetési környezetek többszerepköres készletek metrikai definícióinak beolvasása. |
> | Action | microsoft.web/hostingenvironments/multirolepools/metrics/read | Üzemeltetési környezetek többszerepköres készletek metrikáinak beolvasása. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Előtér-készlet tulajdonságainak beolvasása egy App Service Environment |
> | Action | microsoft.web/hostingenvironments/multirolepools/skus/read | Üzemeltetési környezetek többszerepköres készletek SKU-i beszerzése. |
> | Action | microsoft.web/hostingenvironments/multirolepools/usages/read | Üzemeltetési környezetek többszerepköres készletek használatának beolvasása. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Új előtér-készlet létrehozása App Service Environment vagy meglévő frissítése |
> | Action | microsoft.web/hostingenvironments/operations/read | Üzemeltetési környezetek műveleteinek beolvasása. |
> | Action | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Az összes kimenő függőség hálózati végpontjának beolvasása. |
> | Action | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Privát végponti kapcsolatok jóváhagyása |
> | Action | Microsoft. Web/hostingEnvironments/READ | App Service Environment tulajdonságainak beolvasása |
> | Action | Microsoft.Web/hostingEnvironments/reboot/Action | App Service Environment összes számítógépének újraindítása |
> | Action | microsoft.web/hostingenvironments/resume/action | Üzemeltetési környezetek folytatása. |
> | Action | microsoft.web/hostingenvironments/serverfarms/read | Üzemeltetési környezetek App Service csomagok beszerzése. |
> | Action | microsoft.web/hostingenvironments/sites/read | Üzemeltetési környezetek Web Apps. |
> | Action | microsoft.web/hostingenvironments/suspend/action | Üzemeltetési környezetek felfüggesztése. |
> | Action | microsoft.web/hostingenvironments/usages/read | Üzemeltetési környezetek használatának beolvasása. |
> | Action | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Üzemeltetési környezetek Workerpools metrika-definíciók. |
> | Action | microsoft.web/hostingenvironments/workerpools/metrics/read | Az üzemeltetési környezetek Workerpools metrikák beszerzése. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Read | Munkavégző készlet tulajdonságainak beolvasása egy App Service Environment |
> | Action | microsoft.web/hostingenvironments/workerpools/skus/read | Üzemeltetési környezetek beszerzése Workerpools SKU-ban. |
> | Action | microsoft.web/hostingenvironments/workerpools/usages/read | Üzemeltetési környezetek Workerpools-használatok beszerzése. |
> | Action | Microsoft.Web/hostingEnvironments/workerPools/Write | Új munkavégző készlet létrehozása egy App Service Environment vagy meglévő frissítése |
> | Action | Microsoft. Web/hostingEnvironments/írás | Hozzon létre egy új App Service Environment vagy frissítsen egy meglévőt |
> | Action | Microsoft. Web/ishostingenvironmentnameavailable/READ | Szerezze be, hogy elérhető-e az üzemeltetési környezet neve. |
> | Action | Microsoft. Web/ishostnameavailable/READ | Ellenőrizze, hogy az állomásnév elérhető-e. |
> | Action | Microsoft. Web/isusernameavailable/READ | Ellenőrizze, hogy elérhető-e a Felhasználónév. |
> | Action | Microsoft.Web/listSitesAssignedToHostName/Read | Az állomásnévhez rendelt helyek nevének beolvasása. |
> | Action | microsoft.web/locations/apioperations/read | Locations API-műveletek beolvasása. |
> | Action | Microsoft. Web/Locations/connectiongatewayinstallations/READ | Telephelyek közötti kapcsolatok átjárójának beolvasása. |
> | Action | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Vnet vagy alhálózati törlési értesítés a helyszínekről. |
> | Action | microsoft.web/locations/extractapidefinitionfromwsdl/action | API-definíció kinyerése a WSDL-ből a helyekhez. |
> | Action | microsoft.web/locations/listwsdlinterfaces/action | A helyükhöz tartozó WSDL-felületek listázása. |
> | Action | microsoft.web/locations/managedapis/apioperations/read | Telephelyek által felügyelt API-műveletek beolvasása. |
> | Action | Microsoft. Web/Locations/król/JOIN/Action | Egy felügyelt API-hoz csatlakozik. |
> | Action | microsoft.web/locations/managedapis/read | Webhelyek által felügyelt API-k beolvasása. |
> | Action | microsoft.web/operations/read | Műveletek beolvasása. |
> | Action | microsoft.web/publishingusers/read | Felhasználók közzétételének beolvasása. |
> | Action | microsoft.web/publishingusers/write | A közzétételi felhasználók frissítése. |
> | Action | Microsoft. Web/javaslatok/olvasás | Az előfizetésekre vonatkozó javaslatok listájának beolvasása. |
> | Action | microsoft.web/register/action | Regisztrálja a Microsoft. web erőforrás-szolgáltatót az előfizetéshez. |
> | Action | microsoft.web/resourcehealthmetadata/read | Resource Health metaadatok beolvasása. |
> | Action | microsoft.web/serverfarms/capabilities/read | App Service csomagok funkcióinak beolvasása. |
> | Action | Microsoft.Web/serverfarms/Delete | Meglévő App Service csomag törlése |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/delete | App Service-csomagok törlése az első féltől származó alkalmazások beállításaival. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/read | App Service-csomagok beszerzése az első féltől származó alkalmazások beállításait. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/write | App Service-csomagok frissítése az első féltől származó alkalmazások beállításait. |
> | Action | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relay/delete | Törölje App Service a hibrid kapcsolatok névtereit továbbító csomagok. |
> | Action | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relay/READ | App Service csomagok hibrid kapcsolatok névterei továbbítók beolvasása. |
> | Action | Microsoft. Web/kiszolgálófarmok/hybridconnectionnamespaces/Relays/Sites/READ | App Service csomagok hibrid kapcsolatok névtereit Web Apps. |
> | Action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | App Service csomagok hibrid csatlakoztatási csomagokra vonatkozó korlátainak beolvasása. |
> | Action | Microsoft. Web/kiszolgálófarmok/hybridconnectionrelays/READ | App Service csomagok hibrid kapcsolatok továbbításának beolvasása. |
> | Action | Microsoft. Web/kiszolgálófarmok/metricdefinitions/READ | App Service csomagok metrika-definícióinak beolvasása. |
> | Action | microsoft.web/serverfarms/metrics/read | App Service csomagok metrikáinak beolvasása. |
> | Action | Microsoft. Web/kiszolgálófarmok/operationresults/READ | App Service csomagok műveleti eredményeinek beolvasása. |
> | Action | Microsoft. Web/kiszolgálófarmok/READ | App Service csomag tulajdonságainak beolvasása |
> | Action | Microsoft.Web/serverfarms/restartSites/Action | App Service-csomag összes Web Apps újraindítása |
> | Action | microsoft.web/serverfarms/sites/read | App Service csomagok beszerzése Web Apps. |
> | Action | microsoft.web/serverfarms/skus/read | App Service csomagok beolvasása. |
> | Action | Microsoft. Web/kiszolgálófarmok/használat/olvasás | App Service csomagok használatának beolvasása. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | App Service csomagok frissítése Virtual Network kapcsolatok átjárók. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/read | App Service csomagok beszerzése Virtual Network kapcsolatokhoz. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | App Service csomagok törlése Virtual Network kapcsolatok útvonala. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | App Service csomagok beszerzése Virtual Network kapcsolatok útvonala. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | App Service csomagok frissítése Virtual Network kapcsolatok útvonala. |
> | Action | Microsoft. Web/kiszolgálófarmok/Workers/újraindítás/művelet | App Service-csomagok újraindítása. |
> | Action | Microsoft. Web/kiszolgálófarmok/írás | Új App Service terv létrehozása vagy egy meglévő frissítése |
> | Action | microsoft.web/sites/analyzecustomhostname/read | Egyéni állomásnév elemzése. |
> | Action | Microsoft.Web/sites/applySlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alkalmazása a cél tárolóhelyről az aktuális webalkalmazásra |
> | Action | Microsoft.Web/sites/backup/Action | Új webalkalmazás biztonsági másolatának létrehozása |
> | Action | microsoft.web/sites/backup/read | Web Apps biztonsági másolat beolvasása. |
> | Action | microsoft.web/sites/backup/write | Frissítse Web Apps biztonsági mentést. |
> | Action | microsoft.web/sites/backups/action | Felfedi egy meglévő alkalmazás biztonsági mentését, amely az Azure Storage-beli blobból állítható vissza. |
> | Action | microsoft.web/sites/backups/delete | Delete Web Apps Backups. |
> | Action | microsoft.web/sites/backups/list/action | Web Apps biztonsági mentések listázása. |
> | Action | Microsoft. Web/Sites/Backups/READ | Egy webalkalmazás biztonsági mentésének tulajdonságainak beolvasása |
> | Action | microsoft.web/sites/backups/restore/action | Web Apps biztonsági mentések visszaállítása. |
> | Action | Microsoft. Web/Sites/Backups/Write | Web Apps biztonsági mentések frissítése. |
> | Action | microsoft.web/sites/config/delete | Delete Web Apps Config. |
> | Action | Microsoft.Web/sites/config/list/Action | A webalkalmazás biztonsági szempontból bizalmas beállításainak, például a hitelesítő adatok közzétételének, az Alkalmazásbeállítások és a kapcsolati karakterláncok listázása |
> | Action | Microsoft.Web/sites/config/Read | Webalkalmazás konfigurációs beállításainak beolvasása |
> | Action | microsoft.web/sites/config/snapshots/read | Web Apps konfigurációs Pillanatképek beolvasása. |
> | Action | Microsoft.Web/sites/config/Write | A webalkalmazás konfigurációs beállításainak frissítése |
> | Action | microsoft.web/sites/containerlogs/action | A webalkalmazás tömörített tárolóinak naplóinak beolvasása. |
> | Action | microsoft.web/sites/containerlogs/download/action | Web Apps tároló naplófájljainak letöltése. |
> | Action | microsoft.web/sites/continuouswebjobs/delete | Delete Web Apps Continuous Web Jobs. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/READ | Web Apps folyamatos webes feladatok beolvasása. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/Start/művelet | Indítsa el Web Apps folyamatos webes feladatokat. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/leállítás/művelet | A folyamatos webes feladatok Web Apps leállítása. |
> | Action | Microsoft.Web/sites/Delete | Meglévő webalkalmazás törlése |
> | Action | microsoft.web/sites/deployments/delete | Web Apps üzemelő példányok törlése. |
> | Action | Microsoft. Web/Sites/üzembe helyezés/napló/olvasás | Web Apps központi telepítések naplójának beolvasása. |
> | Action | microsoft.web/sites/deployments/read | Web Apps üzemelő példányok beolvasása. |
> | Action | microsoft.web/sites/deployments/write | Web Apps központi telepítések frissítése. |
> | Action | microsoft.web/sites/detectors/read | Web Apps érzékelők beolvasása. |
> | Action | microsoft.web/sites/diagnostics/analyses/execute/Action | Web Apps diagnosztikai elemzés futtatása. |
> | Action | Microsoft. Web/Sites/Diagnostics/elemzés/olvasás | Web Apps diagnosztika elemzésének beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/aspnetcore/READ | Web Apps diagnosztika beszerzése ASP.NET Core alkalmazáshoz. |
> | Action | Microsoft. Web/Sites/Diagnostics/autoheal/READ | Szerezze be Web Apps diagnosztika autohealt. |
> | Action | Microsoft. Web/Sites/diagnosztika/üzembe helyezés/olvasás | Web Apps diagnosztika központi telepítésének beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/központi telepítések/olvasás | Web Apps diagnosztika központi telepítésének beolvasása. |
> | Action | microsoft.web/sites/diagnostics/detectors/execute/Action | Web Apps diagnosztikai detektor futtatása. |
> | Action | Microsoft. Web/Sites/Diagnostics/érzékelők/olvasás | Web Apps diagnosztikai detektor beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/failedrequestsperuri/READ | Web Apps diagnosztika sikertelen kérelmének beolvasása URI-n. |
> | Action | Microsoft. Web/Sites/Diagnostics/frebanalysis/READ | Web Apps diagnosztikai sikertelen kérelmek ESEMÉNYTÁROLÁSI elemzésének beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/loganalyzer/READ | Web Apps diagnosztikai napló Analizátorjának beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/READ | Web Apps diagnosztikai kategóriák beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/runtimeavailability/READ | Web Apps diagnosztikai futtatókörnyezet rendelkezésre állásának beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/servicehealth/READ | Web Apps diagnosztikai Service Health beolvasása. |
> | Action | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Web Apps diagnosztikai hely CPU-elemzésének beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/sitecrashes/READ | Web Apps diagnosztikai hely összeomlásának beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/sitelatency/READ | Web Apps diagnosztikai hely késésének beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/sitememoryanalysis/READ | Web Apps diagnosztikai hely memória-elemzésének beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/siterestartsettingupdate/READ | Web Apps diagnosztikai hely újraindítási beállításának beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/siterestartuserinitiated/READ | Web Apps diagnosztikai hely újraindítását kezdeményező felhasználó beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/siteswap/READ | Web Apps diagnosztikai hely felcserélése. |
> | Action | Microsoft. Web/Sites/Diagnostics/ThreadCount/READ | Web Apps diagnosztikai szálak számának beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/workeravailability/READ | Web Apps diagnosztikai Workeravailability beolvasása. |
> | Action | Microsoft. Web/Sites/Diagnostics/workerprocessrecycle/READ | Web Apps diagnosztika munkavégző folyamatának újrahasznosítása. |
> | Action | microsoft.web/sites/domainownershipidentifiers/read | Web Apps tartomány tulajdonjog-azonosítóinak beolvasása. |
> | Action | microsoft.web/sites/domainownershipidentifiers/write | Web Apps tartomány tulajdonjog-azonosítóinak frissítése. |
> | Action | microsoft.web/sites/extensions/delete | Web Apps hely bővítményeinek törlése. |
> | Action | Microsoft. Web/Sites/Extensions/READ | Web Apps hely bővítményeinek beolvasása. |
> | Action | Microsoft. Web/Sites/Extensions/Write | Web Apps hely bővítményeinek frissítése. |
> | Action | microsoft.web/sites/functions/action | Functions Web Apps. |
> | Action | microsoft.web/sites/functions/delete | Web Apps függvények törlése. |
> | Action | microsoft.web/sites/functions/listsecrets/action | A Secrets Web Apps függvények listázása. |
> | Action | Microsoft. Web/Sites/functions/MasterKey/READ | Web Apps függvények MasterKey beolvasása. |
> | Action | Microsoft. Web/Sites/functions/READ | Web Apps függvények beolvasása. |
> | Action | Microsoft. Web/Sites/functions/token/READ | Web Apps functions token beolvasása. |
> | Action | Microsoft. Web/Sites/functions/Write | Web Apps függvények frissítése. |
> | Action | microsoft.web/sites/hostnamebindings/delete | Delete Web Apps Hostname Bindings. |
> | Action | microsoft.web/sites/hostnamebindings/read | Get Web Apps Hostname Bindings. |
> | Action | microsoft.web/sites/hostnamebindings/write | Update Web Apps Hostname Bindings. |
> | Action | microsoft.web/sites/hostruntime/functions/keys/read | Get Web Apps Hostruntime Functions Keys. |
> | Action | Microsoft.Web/sites/hostruntime/host/_master/read | függvényalkalmazás fő kulcsának beolvasása rendszergazdai műveletekhez |
> | Action | Microsoft.Web/sites/hostruntime/host/action | Hajtsa végre függvényalkalmazás futtatókörnyezet műveleteit, például a Szinkronizáló eseményindítókat, a függvények hozzáadását, a függvények meghívását, a függvények törlését |
> | Action | microsoft.web/sites/hostruntime/host/read | Get Web Apps Hostruntime Host. |
> | Action | microsoft.web/sites/hybridconnection/delete | Web Apps hibrid kapcsolatok törlése. |
> | Action | Microsoft. Web/Sites/hybridconnection/READ | Web Apps hibrid kapcsolatok beszerzése. |
> | Action | Microsoft. Web/Sites/hybridconnection/Write | Web Apps hibrid kapcsolatok frissítése. |
> | Action | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Törölje Web Apps hibrid kapcsolatok névtereit. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/Relays/listkeys műveletének beolvasása/Action | Kulcsok listázása Web Apps hibrid kapcsolatok névterei továbbítók. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/Relays/READ | Web Apps hibrid kapcsolatok névterei továbbításának beolvasása. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/Relay/Write | Frissítse Web Apps hibrid kapcsolatok névtereit. |
> | Action | Microsoft. Web/Sites/hybridconnectionrelays/READ | Web Apps hibrid kapcsolatok továbbításának beolvasása. |
> | Action | microsoft.web/sites/instances/deployments/delete | Web Apps példányok telepítésének törlése. |
> | Action | microsoft.web/sites/instances/deployments/read | Web Apps példányok üzembe helyezésének beolvasása. |
> | Action | Microsoft. Web/Sites/instances/Extensions/log/READ | Web Apps példányok bővítmények naplójának beolvasása. |
> | Action | microsoft.web/sites/instances/extensions/processes/read | Web Apps példányok bővítmények folyamatainak beolvasása. |
> | Action | microsoft.web/sites/instances/extensions/read | Web Apps példányok bővítményeinek beolvasása. |
> | Action | microsoft.web/sites/instances/processes/delete | Web Apps példányok folyamatainak törlése. |
> | Action | microsoft.web/sites/instances/processes/modules/read | Web Apps példányok beolvasása modulokat dolgoz fel. |
> | Action | microsoft.web/sites/instances/processes/read | Web Apps példányok folyamatainak beolvasása. |
> | Action | microsoft.web/sites/instances/processes/threads/read | Web Apps példányok beolvasása szálakat dolgoz fel. |
> | Action | microsoft.web/sites/instances/read | Web Apps példányok beolvasása. |
> | Action | microsoft.web/sites/listsyncfunctiontriggerstatus/action | A szinkronizálási függvény trigger állapotának Web Apps. |
> | Action | Microsoft. Web/Sites/metricdefinitions/READ | Web Apps metrikák definícióinak beolvasása. |
> | Action | microsoft.web/sites/metrics/read | Web Apps mérőszámok beolvasása. |
> | Action | Microsoft. Web/Sites/metricsdefinitions/READ | Web Apps mérőszámok definícióinak beolvasása. |
> | Action | microsoft.web/sites/migratemysql/action | Telepítse át a MySql Web Apps. |
> | Action | microsoft.web/sites/migratemysql/read | A MySql Web Apps migrálása. |
> | Action | microsoft.web/sites/networktrace/action | Hálózati nyomkövetési Web Apps. |
> | Action | Microsoft. Web/Sites/networktraces/operationresults/READ | Web Apps hálózati nyomkövetési művelet eredményeinek beolvasása. |
> | Action | microsoft.web/sites/newpassword/action | Newpassword Web Apps. |
> | Action | Microsoft. Web/Sites/operationresults/READ | Web Apps művelet eredményeinek beolvasása. |
> | Action | Microsoft. Web/Sites/Operations/READ | Web Apps műveletek beolvasása. |
> | Action | Microsoft. Web/Sites/perfcounters/READ | Web Apps teljesítményszámlálók beolvasása. |
> | Action | microsoft.web/sites/premieraddons/delete | Web Apps Premier bővítmények törlése. |
> | Action | Microsoft. Web/Sites/premieraddons/READ | Web Apps Premier addons beszerzése. |
> | Action | Microsoft. Web/Sites/premieraddons/Write | Web Apps Premier addons frissítése. |
> | Action | microsoft.web/sites/privateaccess/read | A webhelyhez hozzáférő, a webhely elérését engedélyező és engedélyezett virtuális hálózatok körének beolvasása. |
> | Action | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/Action | Privát végponti kapcsolatok jóváhagyása |
> | Action | microsoft.web/sites/processes/modules/read | Web Apps folyamatok moduljainak beolvasása. |
> | Action | microsoft.web/sites/processes/read | Web Apps folyamatok beolvasása. |
> | Action | Microsoft. Web/Sites/processs/Threads/READ | Web Apps folyamatok szálának beolvasása. |
> | Action | microsoft.web/sites/publiccertificates/delete | Web Apps nyilvános tanúsítványok törlése. |
> | Action | microsoft.web/sites/publiccertificates/read | Web Apps nyilvános tanúsítványok beszerzése. |
> | Action | microsoft.web/sites/publiccertificates/write | Web Apps nyilvános tanúsítványok frissítése. |
> | Action | Microsoft.Web/sites/publish/Action | Webalkalmazás közzététele |
> | Action | Microsoft.Web/sites/publishxml/Action | Webes alkalmazás közzétételi profiljának XML-kódjának beolvasása |
> | Action | microsoft.web/sites/publishxml/read | Web Apps közzétételi XML-fájl beolvasása. |
> | Action | Microsoft. Web/Sites/READ | Webalkalmazás tulajdonságainak beolvasása |
> | Action | Microsoft. Web/Sites/recommendationhistory/READ | Web Apps javaslat előzményeinek beolvasása. |
> | Action | microsoft.web/sites/recommendations/disable/action | Web Apps javaslatok letiltása. |
> | Action | Microsoft. Web/Sites/javaslatok/olvasás | A webalkalmazáshoz tartozó javaslatok listájának beolvasása. |
> | Action | microsoft.web/sites/recover/action | Recover Web Apps. |
> | Action | Microsoft.Web/sites/resetSlotConfig/Action | Webalkalmazás-konfiguráció alaphelyzetbe állítása |
> | Action | microsoft.web/sites/resourcehealthmetadata/read | Web Apps Resource Health metaadatok beolvasása. |
> | Action | Microsoft.Web/sites/restart/Action | Webalkalmazás újraindítása |
> | Action | microsoft.web/sites/restore/read | Web Apps visszaállítás beolvasása. |
> | Action | microsoft.web/sites/restore/write | Web Apps visszaállítása. |
> | Action | microsoft.web/sites/restorefrombackupblob/action | A webalkalmazás visszaállítása a biztonsági mentési Blobból. |
> | Action | Microsoft. Web/Sites/restorefromdeletedapp/Action | Web Apps visszaállítása a törölt alkalmazásból. |
> | Action | microsoft.web/sites/restoresnapshot/action | Web Apps Pillanatképek visszaállítása. |
> | Action | microsoft.web/sites/siteextensions/delete | Web Apps hely bővítményeinek törlése. |
> | Action | Microsoft. Web/Sites/siteextensions/READ | Web Apps hely bővítményeinek beolvasása. |
> | Action | Microsoft. Web/Sites/siteextensions/Write | Web Apps hely bővítményeinek frissítése. |
> | Action | microsoft.web/sites/slots/analyzecustomhostname/read | Web Apps tárolóhelyek elemzése az egyéni állomásnév elemzéséhez. |
> | Action | Microsoft.Web/sites/slots/applySlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alkalmazása a cél tárolóhelyről az aktuális bővítőhelyre. |
> | Action | Microsoft.Web/sites/slots/backup/Action | Hozzon létre új webalkalmazás-tárolóhely biztonsági mentést. |
> | Action | microsoft.web/sites/slots/backup/read | Web Apps tárolóhelyek biztonsági másolatának beolvasása. |
> | Action | microsoft.web/sites/slots/backup/write | Web Apps tárolóhelyek biztonsági mentésének frissítése. |
> | Action | Microsoft. Web/Sites/Slots/Backups/művelet | Fedezze fel Web Apps bővítőhelyek biztonsági másolatait. |
> | Action | microsoft.web/sites/slots/backups/delete | Web Apps tárolóhelyek biztonsági mentésének törlése. |
> | Action | microsoft.web/sites/slots/backups/list/action | Web Apps tárolóhelyek biztonsági másolatainak listázása. |
> | Action | Microsoft. Web/Sites/Slots/Backups/READ | Webalkalmazási tárolóhelyek biztonsági mentésének tulajdonságainak beolvasása |
> | Action | microsoft.web/sites/slots/backups/restore/action | Web Apps tárolóhelyek biztonsági másolatának visszaállítása. |
> | Action | microsoft.web/sites/slots/config/delete | Web Apps tárolóhelyek konfigurációjának törlése. |
> | Action | Microsoft.Web/sites/slots/config/list/Action | A webalkalmazási tárolóhely biztonsági kényes beállításainak listázása, például a hitelesítő adatok közzététele, az Alkalmazásbeállítások és a kapcsolati karakterláncok |
> | Action | Microsoft.Web/sites/slots/config/Read | Webalkalmazási tárolóhely konfigurációs beállításainak beolvasása |
> | Action | Microsoft.Web/sites/slots/config/Write | A webalkalmazási tárolóhely konfigurációs beállításainak frissítése |
> | Action | microsoft.web/sites/slots/containerlogs/action | A webalkalmazások tárolóhelyének tömörített tároló-naplófájljainak beolvasása. |
> | Action | microsoft.web/sites/slots/containerlogs/download/action | Töltse le Web Apps tárolóhelyek tárolójának naplóit. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/delete | Web Apps tárolóhelyek folyamatos webes feladatának törlése. |
> | Action | Microsoft. Web/Sites/Slots/continuouswebjobs/READ | Web Apps tárolóhelyek folyamatos webes feladatainak beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/continuouswebjobs/Start/művelet | Indítsa el Web Apps bővítőhelyek folyamatos webes feladatait. |
> | Action | Microsoft. Web/Sites/Slots/continuouswebjobs/leállítás/művelet | Állítsa le Web Apps résidők folyamatos webes feladatait. |
> | Action | Microsoft.Web/sites/slots/Delete | Meglévő webalkalmazási tárolóhely törlése |
> | Action | microsoft.web/sites/slots/deployments/delete | Web Apps tárolóhelyek telepítésének törlése. |
> | Action | Microsoft. Web/Sites/bővítőhely/üzembe helyezés/napló/olvasás | Web Apps tárolóhelyek üzembe helyezési naplójának beolvasása. |
> | Action | microsoft.web/sites/slots/deployments/read | Web Apps tárolóhelyek központi telepítésének beolvasása. |
> | Action | microsoft.web/sites/slots/deployments/write | Web Apps bővítőhelyek telepítésének frissítése. |
> | Action | microsoft.web/sites/slots/detectors/read | Web Apps tárolóhelyek észlelésének beolvasása. |
> | Action | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Futtassa Web Apps bővítőhely diagnosztikai elemzését. |
> | Action | Microsoft. Web/Sites/bővítőhely/diagnosztika/elemzés/olvasás | Web Apps tárolóhelyek diagnosztikai elemzésének beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/aspnetcore/READ | ASP.NET Core alkalmazáshoz Web Apps bővítőhely diagnosztika beszerzése. |
> | Action | Microsoft. Web/Sites/bővítőhely/diagnosztika/autoheal/olvasás | Szerezze be a Web Apps bővítőhely diagnosztika autoheal szolgáltatását. |
> | Action | Microsoft. Web/Sites/bővítőhely/diagnosztika/üzembe helyezés/olvasás | Szerezze be Web Apps tárolóhelyek diagnosztika üzembe helyezését. |
> | Action | Microsoft. Web/Sites/bővítőhely/diagnosztika/központi telepítés/olvasás | Web Apps tárolóhelyek diagnosztikai telepítésének beolvasása. |
> | Action | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Web Apps bővítőhely diagnosztikai detektor futtatása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/érzékelők/olvasás | Web Apps tárolóhelyek diagnosztikai Detektorának beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/frebanalysis/READ | Web Apps bővítőhelyek diagnosztikai sikertelen kérelmek ESEMÉNYTÁROLÁSI elemzésének beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/loganalyzer/READ | Web Apps tárolóhelyek diagnosztikai naplójának beolvasása. |
> | Action | Microsoft. Web/Sites/bővítőhely/diagnosztika/olvasás | Web Apps tárolóhelyek diagnosztika beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/runtimeavailability/READ | Szerezze be Web Apps tárolóhelyek diagnosztika-futtatókörnyezetének rendelkezésre állását. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/servicehealth/READ | Szerezze be Web Apps bővítőhelyek diagnosztikai Service Healthét. |
> | Action | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének CPU-elemzését. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/sitecrashes/READ | A Web Apps tárolóhelyek diagnosztikai helyének összeomlása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/sitelatency/READ | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének késését. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/sitememoryanalysis/READ | Szerezze be Web Apps tárolóhelyek diagnosztikai helyének memóriájának elemzését. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/siterestartsettingupdate/READ | Web Apps tárolóhelyek diagnosztikai helyének újraindítása a frissítés beállításának beolvasása. |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Web Apps tárolóhelyek diagnosztikai helyének újraindítási felhasználójának beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/siteswap/READ | Web Apps tárolóhelyek diagnosztikai helyének felcserélése. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/ThreadCount/READ | Web Apps bővítőhelyek diagnosztikai szálak számának beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/workeravailability/READ | Web Apps bővítőhely diagnosztikai Workeravailability beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Diagnostics/workerprocessrecycle/READ | Szerezze be Web Apps bővítőhely diagnosztika munkavégző folyamatának újrahasznosítását. |
> | Action | microsoft.web/sites/slots/domainownershipidentifiers/read | Web Apps bővítőhelyek tartományi tulajdonosi azonosítóinak beolvasása. |
> | Action | microsoft.web/sites/slots/functions/listsecrets/action | A Secrets Web Apps a tárolóhelyek funkcióinak listázása. |
> | Action | Microsoft. Web/Sites/Slots/functions/READ | Web Apps tárolóhelyek funkcióinak beolvasása. |
> | Action | microsoft.web/sites/slots/hostnamebindings/delete | Delete Web Apps Slots Hostname Bindings. |
> | Action | microsoft.web/sites/slots/hostnamebindings/read | Get Web Apps Slots Hostname Bindings. |
> | Action | Microsoft. Web/Sites/Slots/hostnamebindings/Write | Update Web Apps Slots Hostname Bindings. |
> | Action | microsoft.web/sites/slots/hybridconnection/delete | Web Apps bővítőhelyek hibrid kapcsolatainak törlése. |
> | Action | Microsoft. Web/Sites/Slots/hybridconnection/READ | Web Apps bővítőhelyek hibrid kapcsolatainak beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/hybridconnection/Write | Web Apps bővítőhelyek hibrid kapcsolatainak frissítése. |
> | Action | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Törölje Web Apps bővítőhely hibrid kapcsolatok névtereit. |
> | Action | Microsoft. Web/Sites/Slots/hybridconnectionnamespaces/Relay/Write | Frissítse Web Apps bővítőhely hibrid kapcsolatok névtereit. |
> | Action | Microsoft. Web/Sites/Slots/hybridconnectionrelays/READ | Web Apps bővítőhelyek hibrid kapcsolatainak beolvasása. |
> | Action | microsoft.web/sites/slots/instances/deployments/read | Web Apps bővítőhely példányok üzembe helyezésének beolvasása. |
> | Action | microsoft.web/sites/slots/instances/processes/delete | Web Apps tárolóhely-példányok folyamatainak törlése. |
> | Action | microsoft.web/sites/slots/instances/processes/read | Web Apps résidő-példányok folyamatainak beolvasása. |
> | Action | microsoft.web/sites/slots/instances/read | Web Apps tárolóhelyek példányainak beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/metricdefinitions/READ | Web Apps bővítőhelyek metrikai definícióinak beolvasása. |
> | Action | Microsoft. Web/Sites/slot/metrika/olvasás | Web Apps bővítőhely metrikáinak beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/migratemysql/READ | Web Apps bővítőhelyek áttelepíthetők a MySql-re. |
> | Action | microsoft.web/sites/slots/networktrace/action | Hálózati nyomkövetési Web Apps tárolóhelyek. |
> | Action | Microsoft. Web/Sites/Slots/networktraces/operationresults/READ | Web Apps tárolóhelyek hálózati nyomkövetési műveletének eredményeinek beolvasása. |
> | Action | microsoft.web/sites/slots/newpassword/action | ÚjJelszó Web Apps tárolóhelyek. |
> | Action | Microsoft. Web/Sites/Slots/operationresults/READ | Web Apps bővítőhelyek műveleti eredményeinek beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/Operations/READ | Web Apps tárolóhelyek műveleteinek beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/perfcounters/READ | Web Apps bővítőhelyek teljesítményszámlálók beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/phplogging/READ | Web Apps bővítőhelyek Phplogging beolvasása. |
> | Action | microsoft.web/sites/slots/premieraddons/delete | Web Apps bővítőhely Premier addons törlése. |
> | Action | microsoft.web/sites/slots/premieraddons/read | Web Apps Slots Premier addons beszerzése. |
> | Action | Microsoft. Web/Sites/Slots/premieraddons/Write | Web Apps Slots Premier addons frissítése. |
> | Action | microsoft.web/sites/slots/processes/read | Web Apps tárolóhelyek folyamatainak beolvasása. |
> | Action | microsoft.web/sites/slots/publiccertificates/delete | Web Apps bővítőhely nyilvános tanúsítványainak törlése. |
> | Action | microsoft.web/sites/slots/publiccertificates/read | Web Apps bővítőhely nyilvános tanúsítványainak beolvasása. |
> | Action | microsoft.web/sites/slots/publiccertificates/write | Web Apps bővítőhely nyilvános tanúsítványainak létrehozása vagy frissítése. |
> | Action | Microsoft.Web/sites/slots/publish/Action | Webalkalmazási tárolóhely közzététele |
> | Action | Microsoft.Web/sites/slots/publishxml/Action | Közzétételi profil XML-kódjának beolvasása a webalkalmazási tárolóhelyhez |
> | Action | Microsoft. Web/Sites/Slots/READ | Webalkalmazás-telepítési tárolóhely tulajdonságainak beolvasása |
> | Action | microsoft.web/sites/slots/recover/action | Web Apps tárolóhelyek helyreállítása. |
> | Action | Microsoft.Web/sites/slots/resetSlotConfig/Action | Webalkalmazás-tárolóhely konfigurációjának alaphelyzetbe állítása |
> | Action | microsoft.web/sites/slots/resourcehealthmetadata/read | Web Apps tárolóhelyek Resource Health metaadatok beolvasása. |
> | Action | Microsoft.Web/sites/slots/restart/Action | Webalkalmazás-tárolóhely újraindítása |
> | Action | Microsoft. Web/Sites/slot/Restore/READ | Web Apps tárolóhelyek visszaállításának beolvasása. |
> | Action | microsoft.web/sites/slots/restore/write | Web Apps tárolóhelyek visszaállítása. |
> | Action | microsoft.web/sites/slots/restorefrombackupblob/action | Web Apps tárolóhely visszaállítása a biztonsági mentési Blobból. |
> | Action | Microsoft. Web/Sites/Slots/restorefromdeletedapp/Action | Webalkalmazás-tárolóhelyek visszaállítása a törölt alkalmazásból. |
> | Action | microsoft.web/sites/slots/restoresnapshot/action | Web Apps tárolóhelyek pillanatképének visszaállítása. |
> | Action | microsoft.web/sites/slots/siteextensions/delete | Web Apps bővítőhelyek helyének bővítményeinek törlése. |
> | Action | Microsoft. Web/Sites/Slots/siteextensions/READ | Web Apps bővítőhelyek helyének bővítményeinek beolvasása. |
> | Action | Microsoft. Web/Sites/Slots/siteextensions/Write | Frissítse Web Apps bővítőhelyek helyének bővítményeit. |
> | Action | Microsoft. Web/Sites/Slots/slotsdiffs/Action | A webalkalmazás és a bővítőhelyek közötti beállítások közötti különbségek beolvasása |
> | Action | Microsoft.Web/sites/slots/slotsswap/Action | Webalkalmazás-telepítési bővítőhelyek cseréje |
> | Action | microsoft.web/sites/slots/snapshots/read | Web Apps tárolóhelyek pillanatképének beolvasása. |
> | Action | Microsoft.Web/sites/slots/sourcecontrols/Delete | Webalkalmazás-tárolóhely forrás-ellenőrzési konfigurációs beállításainak törlése |
> | Action | Microsoft. Web/Sites/Slots/sourcecontrols/READ | A webalkalmazási tárolóhely forrás-ellenőrzési konfigurációs beállításainak beolvasása |
> | Action | Microsoft. Web/Sites/Slots/sourcecontrols/Write | A webalkalmazási tárolóhely forrás-ellenőrzési konfigurációs beállításainak frissítése |
> | Action | Microsoft.Web/sites/slots/start/Action | Webalkalmazás-tárolóhely elindítása |
> | Action | Microsoft.Web/sites/slots/stop/Action | Webalkalmazási tárolóhely leállítása |
> | Action | microsoft.web/sites/slots/sync/action | Web Apps tárolóhelyek szinkronizálása. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/delete | A webjobs által aktivált Web Apps tárolóhelyek törlése. |
> | Action | Microsoft. Web/Sites/Slots/triggeredwebjobs/READ | Beolvashatja Web Apps bővítőhelyek által aktivált webjobs-feladatokat. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/run/action | Futtassa Web Apps bővítőhelyen indított webjobs-feladatokat. |
> | Action | Microsoft. Web/Sites/Slots/használati adatok/olvasás | Web Apps tárolóhelyek használatának beolvasása. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/delete | Web Apps bővítőhelyek Virtual Network kapcsolatok törlése. |
> | Action | Microsoft. Web/Sites/slot/virtualnetworkconnections/Gateway/Write | Frissítse Web Apps tárolóhelyek Virtual Network kapcsolatok átjáróit. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/read | Web Apps bővítőhelyek Virtual Network kapcsolatok beszerzése. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/write | Web Apps bővítőhelyek Virtual Network kapcsolatok frissítése. |
> | Action | Microsoft. Web/Sites/Slots/webjobs/olvasás | Web Apps slot webjobs beszerzése. |
> | Action | Microsoft. Web/Sites/Slots/Write | Hozzon létre egy új webalkalmazási tárolóhelyet vagy frissítsen egy meglévőt |
> | Action | Microsoft. Web/Sites/slotsdiffs/Action | A webalkalmazás és a bővítőhelyek közötti beállítások közötti különbségek beolvasása |
> | Action | Microsoft.Web/sites/slotsswap/Action | Webalkalmazás-telepítési bővítőhelyek cseréje |
> | Action | microsoft.web/sites/snapshots/read | Web Apps Pillanatképek beolvasása. |
> | Action | Microsoft.Web/sites/sourcecontrols/Delete | Webalkalmazás forrás-ellenőrzési konfigurációs beállításainak törlése |
> | Action | Microsoft. Web/Sites/sourcecontrols/READ | A webalkalmazás forrás-ellenőrzési konfigurációs beállításainak beolvasása |
> | Action | Microsoft. Web/Sites/sourcecontrols/Write | A webalkalmazás forrás-ellenőrzési konfigurációs beállításainak frissítése |
> | Action | Microsoft.Web/sites/start/Action | Webalkalmazás indítása |
> | Action | Microsoft.Web/sites/stop/Action | Webalkalmazás leállítása |
> | Action | microsoft.web/sites/sync/action | Sync Web Apps. |
> | Action | Microsoft. Web/Sites/syncfunctiontriggers/Action | Web Apps szinkronizálási függvényének eseményindítói. |
> | Action | microsoft.web/sites/triggeredwebjobs/delete | Web Apps indított webjobs-feladatok törlése. |
> | Action | Microsoft. Web/Sites/triggeredwebjobs/előzmények/olvasás | Web Apps indított webjobs-előzmények beolvasása. |
> | Action | Microsoft. Web/Sites/triggeredwebjobs/READ | Web Apps indított webjobs-feladatok beolvasása. |
> | Action | microsoft.web/sites/triggeredwebjobs/run/action | Web Apps indított webjobs-feladatok futtatása. |
> | Action | Microsoft. Web/Sites/használat/olvasás | Web Apps használati adatok beolvasása. |
> | Action | microsoft.web/sites/virtualnetworkconnections/delete | Web Apps Virtual Network kapcsolatok törlése. |
> | Action | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/READ | Web Apps Virtual Network kapcsolatok átjáróinak beolvasása. |
> | Action | Microsoft. Web/Sites/virtualnetworkconnections/Gateway/Write | Frissítse Web Apps Virtual Network Connections átjárókat. |
> | Action | microsoft.web/sites/virtualnetworkconnections/read | Web Apps Virtual Network kapcsolatok beolvasása. |
> | Action | microsoft.web/sites/virtualnetworkconnections/write | Web Apps Virtual Network kapcsolatok frissítése. |
> | Action | Microsoft. Web/Sites/webjobs/olvasás | Web Apps webjobs-feladatok beolvasása. |
> | Action | Microsoft. Web/Sites/Write | Hozzon létre egy új webalkalmazást, vagy frissítsen egy meglévőt |
> | Action | microsoft.web/skus/read | SKU-i beolvasása. |
> | Action | Microsoft. Web/sourcecontrols/READ | Forrás vezérlők beolvasása. |
> | Action | Microsoft. Web/sourcecontrols/írás | Frissítse a forrás vezérlőelemeket. |
> | Action | microsoft.web/unregister/action | A Microsoft. web erőforrás-szolgáltató regisztrációjának törlése az előfizetéshez. |
> | Action | microsoft.web/validate/action | Érvényesít. |
> | Action | microsoft.web/verifyhostingenvironmentvnet/action | Az üzemeltetési környezet vnet ellenőrzése. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Művelet típusa | Művelet | Leírás |
> | --- | --- | --- |
> | Action | Microsoft. WorkloadMonitor/összetevők/olvasás | Az erőforrás összetevőinek beolvasása |
> | Action | Microsoft.WorkloadMonitor/componentsSummary/read | Az összetevők összegzésének beolvasása |
> | Action | Microsoft. WorkloadMonitor/monitorInstances/READ | Az erőforráshoz tartozó figyelők példányainak beolvasása |
> | Action | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | A figyelő példányok összegzésének beolvasása |
> | Action | Microsoft. WorkloadMonitor/figyelők/olvasás | Az erőforráshoz tartozó figyelők beolvasása |
> | Action | Microsoft. WorkloadMonitor/figyelők/írás | Az erőforráshoz tartozó figyelő konfigurálása |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/read | Értesítési beállítások beolvasása az erőforráshoz |
> | Action | Microsoft. WorkloadMonitor/notificationSettings/Write | Az erőforráshoz tartozó értesítési beállítások konfigurálása |
> | Action | Microsoft. WorkloadMonitor/Operations/READ | A támogatott műveletek beolvasása |

## <a name="next-steps"></a>További lépések

- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
